---
title: Créer et approvisionner des appareils IoT Edge à l’aide de clés symétriques sur Windows - Azure IoT Edge | Microsoft Docs
description: Utiliser l’attestation de clés symétriques pour tester l’approvisionnement des appareils Windows à grande échelle pour Azure IoT Edge avec le service Device Provisioning
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 2f892af6b876d81d9312e1f93785265e55f960a2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226012"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-windows-using-symmetric-keys"></a>Créer et approvisionner des appareils IoT Edge à grande échelle sur Windows à l’aide de clés symétriques

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions de bout en bout pour l’approvisionnement automatique d’un ou plusieurs appareils IoT Edge Windows à l’aide de clés symétriques. Vous pouvez approvisionner automatiquement des appareils IoT Edge Azure à l’aide du [Service de provisionnement des appareils Azure IoT Hub](../iot-dps/index.yml) (DPS). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la présentation du [provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Voici les tâches à effectuer :

1. Création d’une **inscription individuelle** pour un appareil unique ou d’une **inscription de groupe** pour un ensemble d’appareils
1. Installation du runtime IoT Edge et de la connexion à IoT Hub.

L’attestation de clé symétrique est une approche simple pour authentifier un appareil avec une instance du service Device Provisioning. Cette méthode d’attestation représente une expérience « Hello world » pour les développeurs qui découvrent le provisionnement d’appareils ou n’ont pas d’exigences de sécurité strictes. L’attestation d’appareil avec un [Module de plateforme sécurisée (TPM)](../iot-dps/concepts-tpm-attestation.md) ou un [certificat X.509](../iot-dps/concepts-x509-attestation.md) est plus sécurisée, et doit être utilisée lorsque les exigences de sécurité sont plus strictes. <!-- note links here; they will break -->

## <a name="prerequisites"></a>Prérequis

* Un hub IoT actif.
* Un appareil Windows physique ou virtuel faisant office d’appareil IoT Edge.
  * Vous devrez définir un **ID d’inscription***unique* pour identifier chaque appareil. Vous pouvez utiliser l’adresse MAC, le numéro de série ou toute information unique provenant de l’appareil. Dans cet exemple, vous pouvez utiliser une combinaison d’une adresse MAC et d’un numéro de série, forment la chaîne suivante pour un ID d’inscription : `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Les caractères valides sont les caractères alphanumériques minuscules et les tirets (`-`).
* Une instance du service IoT Hub Device Provisioning dans Azure, liée à votre hub IoT.
  * Si vous ne disposez pas d’une instance du service Device Provisioning, vous pouvez suivre les instructions fournies dans les sections [Créer un service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) et [Lier le hub IoT et votre service Device Provisioning](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) du guide de démarrage rapide du service IoT Hub Device Provisioning.
  * Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.

## <a name="create-a-dps-enrollment"></a>Créer une inscription au service Device Provisioning

Créez une inscription pour provisionner un ou plusieurs appareils via le service Device Provisioning.

Si vous envisagez de provisionner un seul appareil IoT Edge, créez une **inscription individuelle**. Si vous avez besoin de provisionner plusieurs appareils, suivez les étapes de création d’une **inscription de groupe** DPS.

Lorsque vous créez une inscription dans le service Device Provisioning, vous avez la possibilité de déclarer un **état initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-at-scale.md).

Pour plus d’informations sur les inscriptions dans le service Device Provisioning, consultez [Guide pratique pour gérer les inscriptions d’appareils](../iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Créer une inscription individuelle dans le service Device Provisioning

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions individuelles à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne un appareil IoT Edge.

1. Dans le [Portail Microsoft Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter une inscription individuelle** et suivez ces étapes pour configurer l’inscription :  

   1. Pour **Mécanisme**, sélectionnez **Clé symétrique**.

   1. Indiquez un **ID d’inscription** unique pour votre appareil.

   1. Fournissez si vous le souhaitez un **ID d’appareil loT Hub** pour votre appareil. Vous pouvez utiliser l’ID d’appareil pour cibler un appareil individuel lors du déploiement de module. Si vous ne fournissez pas un ID d’appareil, l’ID d’inscription est utilisé.

   1. Sélectionnez **Vrai** pour déclarer que cette inscription est un appareil IoT Edge.

   1. Si vous le souhaitez, ajoutez une valeur de balise à l’**état initial du jumeau d’appareil**. Vous pouvez utiliser des balises pour cibler des groupes d’appareils lors du déploiement de module. Par exemple :

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Sélectionnez **Enregistrer**.

1. Copiez la **clé primaire** de l’inscription spécifique à utiliser lors de l’installation du runtime IoT Edge.

Maintenant qu’une inscription existe pour cet appareil, le runtime IoT Edge peut provisionner automatiquement l’appareil lors de l’installation.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Créer une inscription de groupe dans le service Device Provisioning

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions de groupe à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne des appareils IoT Edge. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

1. Dans le [Portail Microsoft Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter une inscription individuelle** et suivez ces étapes pour configurer l’inscription :  

   1. Indiquez un **nom de groupe**.

   1. Sélectionnez **Clé symétrique** comme type d’attestation.

   1. Sélectionnez **Vrai** pour déclarer que cette inscription est un appareil IoT Edge. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

   1. Si vous le souhaitez, ajoutez une valeur de balise à l’**état initial du jumeau d’appareil**. Vous pouvez utiliser des balises pour cibler des groupes d’appareils lors du déploiement de module. Par exemple :

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Sélectionnez **Enregistrer**.

1. Copiez la valeur de **clé primaire** du groupe d’inscriptions à utiliser lors de la création de clés d’appareil à utiliser avec une inscription de groupe.

Maintenant qu’il existe un groupe d’inscriptions, le runtime IoT Edge peut provisionner automatiquement des appareils lors de l’installation.

#### <a name="derive-a-device-key"></a>Dériver une clé d’appareil

Chaque appareil provisionné dans le cadre d’une inscription de groupe a besoin d’une clé d’appareil dérivée pour effectuer une attestation de clé symétrique avec l’inscription lors du provisionnement.

Pour générer une clé d’appareil, utilisez la clé que vous avez copiée à partir de votre groupe d’inscriptions DPS pour calculer un [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) de l’ID d’inscription unique pour l’appareil et convertir le résultat au format Base64.

> [!IMPORTANT]
> N’incluez pas la clé primaire ou secondaire de votre inscription dans le code de votre appareil.

Sur Windows, vous pouvez utiliser PowerShell pour générer votre clé d’appareil dérivée, comme indiqué dans l’exemple suivant.

Remplacez la valeur **KEY** par la **clé primaire** que vous avez notée précédemment.

Remplacez la valeur de **REG_ID** avec l’ID d’inscription de votre appareil.

```powershell
$KEY='PASTE_YOUR_ENROLLMENT_KEY_HERE'
$REG_ID='PASTE_YOUR_REGISTRATION_ID_HERE'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

Voici un exemple de sortie d’une clé d’appareil dérivée :

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

## <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

Dans cette section, vous allez préparer votre machine virtuelle ou votre appareil physique Windows à IoT Edge. Ensuite, vous allez installer IoT Edge.

Le démon de sécurité IoT Edge fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, telle qu’une version préliminaire, ou si vous devez installer en mode hors connexion, suivez les [étapes d’installation hors connexion ou version spécifique](how-to-provision-single-device-windows-symmetric.md#offline-or-specific-version-installation-optional).

1. Exécutez PowerShell ISE en tant qu’administrateur.

   Utilisez une session AMD64 de PowerShell, et non PowerShell (x86). Si vous ne savez pas quel type de session vous utilisez, exécutez la commande suivante :

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Exécutez la commande [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) qui effectue les tâches suivantes :

   * Vérifie que votre ordinateur Windows est sous une version prise en charge.
   * Active la fonctionnalité des conteneurs.
   * Télécharge le moteur [Moby](https://github.com/moby/moby) et le runtime IoT Edge.

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

Préparez les informations suivantes :

* Valeur **Étendue de l’ID** du service Device Provisioning
* L’**ID d’inscription** de l’appareil que vous avez créé
* Entrez la **clé primaire** d’une inscription spécifique ou une [clé dérivée](#derive-a-device-key) pour les appareils utilisant une inscription de groupe.

1. Ouvrez une fenêtre PowerShell en mode administrateur. Veillez à utiliser une session AMD64 de PowerShell lors de l'installation d'IoT Edge, plutôt que PowerShell (x86).

1. La commande **Initialize-IoTEdge** configure le runtime IoT Edge sur votre ordinateur. La commande opère par défaut un approvisionnement manuel avec les conteneurs Windows. Vous devez donc ajouter l’indicateur `-DpsSymmetricKey` pour utiliser l’approvisionnement automatique avec l’authentification par clé symétrique.

   Remplacez les valeurs d’espace réservé pour `{scope_id}`, `{registration_id}` et `{symmetric_key}` par les données que vous avez collectées précédemment.

   Ajoutez le paramètre `-RegistrationId {registration_id}` si vous souhaitez définir l’ID de l’appareil autrement que par le nom commun du certificat d’identité.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a démarré correctement, vous pouvez accéder à votre IoT Hub et commencer à déployer des modules IoT Edge sur votre appareil.

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

Vous pouvez vérifier que l’inscription de groupe que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription de groupe que vous avez créée. Accédez à l’onglet **Enregistrements d’inscription** pour afficher tous les appareils enregistrés dans ce groupe.

---

Utilisez les commandes suivantes sur votre appareil pour vérifier que IoT Edge a été installé et démarré correctement.

Vérifiez l’état du service IoT Edge.

```powershell
Get-Service iotedge
```

Consultez les journaux d’activité de service.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Répertoriez les modules en cours d’exécution.

```powershell
iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service Device Provisioning vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).
