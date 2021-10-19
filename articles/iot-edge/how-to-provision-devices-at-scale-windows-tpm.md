---
title: Approvisionner un appareil avec un TPM virtuel sur Windows – Azure IoT Edge | Microsoft Docs
description: Utiliser un TPM simulé sur un appareil Windows afin de tester le service d’approvisionnement d’appareil Azure pour Azure IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 10/06/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: f130adffa4f5d542082b3ab6271952a867c30cdb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661876"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Créer et approvisionner des appareils IoT Edge à grande échelle avec un TPM sur Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions de bout en bout pour l’approvisionnement automatique d’un appareil IoT Edge Windows à l’aide d’un module de plateforme sécurisée (TPM). Vous pouvez provisionner automatiquement des appareils Azure IoT Edge à l’aide du [service Device Provisioning (DPS) Azure IoT Hub](../iot-dps/index.yml). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la présentation du [provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Cet article décrit deux méthodologies. Sélectionnez votre préférence en fonction de l’architecture de votre solution :

1. Approvisionner automatiquement un appareil Windows avec du matériel TPM physique.
1. Approvisionner automatiquement un appareil Windows exécutant un module de plateforme sécurisée (TPM) simulé. Cette méthodologie est recommandée comme scénario de test uniquement, car un TPM simulé n’offre pas la même sécurité qu’un TPM physique.

Les instructions varient en fonction de votre méthodologie. Vérifiez que vous vous trouvez dans l’onglet approprié.

Voici les tâches à effectuer :

# <a name="physical-tpm"></a>[TPM physique](#tab/physical-tpm)

* Récupérez les informations d’approvisionnement de votre appareil.
* Création d’une inscription individuelle pour l’appareil.
* Installation du runtime IoT Edge et connexion de l’appareil à IoT Hub.

# <a name="simulated-tpm"></a>[TPM simulé](#tab/simulated-tpm)

* Configurez votre TPM simulé et récupérez ses informations de configuration.
* Création d’une inscription individuelle pour l’appareil.
* Installation du runtime IoT Edge et connexion de l’appareil à IoT Hub.

---

## <a name="prerequisites"></a>Prérequis

Les prérequis sont les mêmes pour les solutions de TPM physique et de TPM virtuel.

* Une machine de développement Windows. Cet article utilise Windows 10.
* Un hub IoT actif.
* Une instance du service IoT Hub Device Provisioning dans Azure, liée à votre hub IoT.
  * Si vous ne disposez pas d’une instance du service Device Provisioning, vous pouvez suivre les instructions fournies dans les sections [Créer un service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) et [Lier le hub IoT et votre service Device Provisioning](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) du guide de démarrage rapide du service IoT Hub Device Provisioning.
  * Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.

> [!NOTE]
> TPM 2.0 est requis pour utiliser l’attestation TPM avec DPS.
>
> Il est possible de créer des inscriptions DPS individuelles et non des inscriptions DPS groupées avec un module TPM.

## <a name="set-up-your-tpm"></a>Configurer votre TPM

# <a name="physical-tpm"></a>[TPM physique](#tab/physical-tpm)

Dans cette section, vous allez créer un outil permettant de récupérer **l’ID d’inscription** et la **Paire de clés de type EK (Endorsement key)** de votre module TPM.

1. Suivez les étapes décrites dans [Configurer un environnement de développement Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) pour installer et générer l’Azure IoT device SDK pour C.

1. Exécutez les commandes suivantes dans une session PowerShell avec élévation de privilèges pour créer l’outil Kit de développement logiciel (SDK) qui récupère les informations d’approvisionnement de votre appareil pour votre TPM.

   ```powershell
   cd azure-iot-sdk-c\cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client\tools\tpm_device_provision
   make
   .\tpm_device_provision
   ```

1. La fenêtre Sortie affiche **l’ID d’inscription** et la **Paire de clés de type EK (Endorsement Key)** de l’appareil. Copiez ces valeurs. Vous vous en servirez plus tard, au moment de créer une inscription individuelle pour votre appareil dans le Service DPS.

> [!TIP]
> Si vous ne souhaitez pas utiliser l’outil SDK pour récupérer les informations, vous devez trouver un autre moyen d’obtenir les informations de provisionnement. La **Paire de clés de type EK (Endorsement Key)** , propre à chaque puce TPM, est fournie par le fabricant de la puce TPM associée. Vous pouvez dériver un **ID d'inscription** unique pour votre appareil TPM, en créant par exemple un code de hachage SHA-256 pour la paire de clés de type EK.

Une fois que vous disposez de votre ID d’inscription et de votre paire de clés de type EK, vous pouvez continuer.

# <a name="simulated-tpm"></a>[TPM simulé](#tab/simulated-tpm)

Si vous n’avez pas de module de plateforme sécurisée (TPM) physique disponible et que vous souhaitez tester cette méthode d’approvisionnement, vous pouvez simuler un module de plateforme sécurisée sur votre appareil.

IoT Hub Device Provisioning Service fournit des exemples qui simulent un TPM et retournent pour vous la **paire de clés de type EK (Endorsement Key)** et l’**ID d’inscription**.

1. Choisissez l’un des exemples de la liste suivante, en fonction de votre langage par défaut.
1. Arrêtez de suivre les étapes de l’exemple DPS une fois que le TPM est en cours d’exécution et que vous avez collecté la **paire de clés de type EK (Endorsement Key)** et l’**ID d’inscription**. N’appuyez pas sur *Entrée* pour exécuter l’inscription dans l’exemple d’application.
1. Laissez la fenêtre hébergeant le TPM en cours d’exécution jusqu’à ce que vous ayez terminé le test de ce scénario.
1. Revenez à cet article pour créer une inscription DPS et configurer votre appareil.

Exemples de TPM simulés :

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

---

## <a name="create-a-dps-enrollment"></a>Créer une inscription au service Device Provisioning

Utilisez les informations d’approvisionnement de votre TPM pour créer une inscription individuelle dans le service de provisionnement des appareils.

Lorsque vous créez une inscription auprès du service Device Provisioning, vous avez la possibilité de déclarer un **État initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-at-scale.md).

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions individuelles à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne un appareil IoT Edge.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance du Service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter une inscription individuelle**, puis suivez cette procédure pour configurer l’inscription :

   1. Pour **Mécanisme**, sélectionnez **TPM**.

   1. Spécifiez la **paire de clés de type EK (Endorsement Key)** et l’**ID d’inscription** que vous avez copiés sur votre machine virtuelle ou votre appareil physique.

   1. Fournissez un ID pour votre appareil si vous le souhaitez. Si vous ne fournissez pas un ID d’appareil, l’ID d’inscription est utilisé.

   1. Sélectionnez **True** pour déclarer que votre machine virtuelle ou votre appareil physique est un appareil IoT Edge.

   1. Choisissez le hub IoT lié auquel vous voulez connecter votre appareil, ou sélectionnez **Lier à un nouveau hub IoT**. Vous pouvez choisir plusieurs hubs : l’appareil sera affecté à l’un d’entre eux en fonction de la stratégie d’attribution sélectionnée.

   1. Ajoutez une valeur de balise à l’**État initial du jumeau d’appareil** si vous le souhaitez. Vous pouvez utiliser des balises pour cibler des groupes d’appareils lors du déploiement de module. Pour plus d’informations, consultez [Déploiement de modules IoT Edge à grande échelle](how-to-deploy-at-scale.md).

   1. Sélectionnez **Enregistrer**.

Maintenant qu’une inscription existe pour cet appareil, le runtime IoT Edge peut provisionner automatiquement l’appareil lors de l’installation.

## <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

Dans cette section, vous allez préparer votre machine virtuelle ou votre appareil physique Windows à IoT Edge. Ensuite, vous allez installer IoT Edge.

Vous devez effectuer une étape sur votre appareil pour qu’il soit prêt à installer le runtime IoT Edge. Votre appareil a besoin d’un moteur de conteneur installé.

### <a name="install-iot-edge"></a>Installer IoT Edge

Le démon de sécurité IoT Edge fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, telle qu’une préversion, ou si vous devez installer en mode hors connexion, suivez les étapes d’installation hors connexion ou propres à une version.

1. Exécutez PowerShell ISE en tant qu’administrateur.

   Utilisez une session AMD64 de PowerShell, et non PowerShell (x86). Si vous ne savez pas quel type de session vous utilisez, exécutez la commande suivante :

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Exécutez la commande [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) qui effectue les tâches suivantes :

   * Vérifie que votre ordinateur Windows est sous une version prise en charge.
   * Active la fonctionnalité des conteneurs.
   * Télécharge le moteur Moby et le runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Redémarrez votre appareil si vous y êtes invité.

Quand vous installez IoT Edge sur un appareil, vous pouvez utiliser des paramètres supplémentaires pour modifier le processus, à savoir :

* Diriger le trafic pour qu’il transite par un serveur proxy,
* Pointer le programme d’installation vers un répertoire local pour une installation hors connexion.

Pour plus d’informations sur ces paramètres supplémentaires, consultez [Scripts PowerShell pour IoT Edge sur conteneurs Windows](reference-windows-scripts.md).

## <a name="configure-the-device-with-provisioning-information"></a>Configurer l’appareil avec des informations de provisionnement

Une fois que le runtime est installé sur votre appareil, configurez l’appareil avec les informations qu’il utilise pour se connecter au service Device Provisioning (DPS) et à IoT Hub.

1. Récupérez la valeur **Étendue de l’ID** du service Device Provisioning et la valeur **ID d’inscription** de l’appareil qui ont été collectées dans les sections précédentes.

1. Ouvrez une fenêtre PowerShell en mode administrateur. Veillez à utiliser une session AMD64 de PowerShell lors de l'installation d'IoT Edge, plutôt que PowerShell (x86).

1. La commande **Initialize-IoTEdge** configure le runtime IoT Edge sur votre ordinateur. Par défaut, la commande est une commande d’approvisionnement manuel avec des conteneurs Windows. Utilisez la balise `-Dps` pour utiliser le service de provisionnement des appareils à la place du provisionnement manuel.

   Remplacez les valeurs d'espace réservé de `{scope_id}` et `{registration_id}` par les données que vous avez collectées précédemment.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a démarré correctement, vous pouvez accéder à votre IoT Hub et commencer à déployer des modules IoT Edge sur votre appareil. Utilisez les commandes suivantes sur votre appareil pour vérifier que le runtime a été installé et démarré correctement.  

Vérifiez l’état du service IoT Edge.

```powershell
Get-Service iotedge
```

Examinez les journaux d’activité du service des 5 dernières minutes.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Répertoriez les modules en cours d’exécution.

```powershell
iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service Device Provisioning vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md)
