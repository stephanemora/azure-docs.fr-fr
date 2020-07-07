---
title: Provisionner automatiquement des appareils Windows avec DPS - Azure IoT Edge | Microsoft Docs
description: Utiliser un appareil simulé sur votre machine Windows pour tester le provisionnement automatique d’appareils pour Azure IoT Edge avec le service Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8278d9f2129ab8b213cf1b561f4b82b56dffc8da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82131033"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Créer et provisionner un appareil IoT Edge simulé avec un TPM virtuel sous Windows

Les appareils Azure IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Device Provisioning](../iot-dps/index.yml) tout comme les appareils qui ne sont pas compatibles avec Edge. Si vous ne connaissez pas le processus de provisionnement automatique, révisez les [concepts du provisionnement automatique](../iot-dps/concepts-auto-provisioning.md) avant de poursuivre.

DPS prend désormais en charge l’attestation de clé symétrique pour les appareils IoT Edge lors d’une inscription individuelle et d’une inscription de groupe. Pour une inscription de groupe, si l'option « Est un appareil IoT Edge » est définie sur true dans l'attestation de clé symétrique, tous les appareils inscrits sous ce groupe d'inscriptions sont marqués en tant qu'appareils IoT Edge.

Cet article explique comment tester le provisionnement automatique sur un appareil IoT Edge simulé en procédant comme suit :

* Création d’une instance du service IoT Hub Device Provisioning.
* Création d’un appareil simulé sur votre machine Windows avec un module de plateforme sécurisée (TPM) simulé pour la sécurité du matériel.
* Création d’une inscription individuelle pour l’appareil.
* Installation du runtime IoT Edge et connexion de l’appareil à IoT Hub.

> [!TIP]
> Cet article explique comment tester le provisionnement automatique à l'aide de l'attestation TPM sur des appareils virtuels, mais il s'applique principalement à l'utilisation de matériel TPM physique.

## <a name="prerequisites"></a>Prérequis

* Une machine de développement Windows. Cet article utilise Windows 10.
* Un hub IoT actif.

> [!NOTE]
> TPM 2.0 est requis lors de l'utilisation de l'attestation TPM avec DPS, et il ne peut être utilisé que pour créer des inscriptions individuelles, et non groupées.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurer le service IoT Hub Device Provisioning

Créez une nouvelle instance du service IoT Hub Device Provisioning dans Azure et liez-la à votre hub IoT. Vous pouvez suivre les instructions dans [Configurer le service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md).

Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.

> [!TIP]
> Si vous utilisez un appareil TPM physique, vous devez déterminer la **Paire de clés de type EK (Endorsement Key)** , propre à chaque puce TPM et fournie par le fabricant de la puce TPM associée. Vous pouvez dériver un **ID d'inscription** unique pour votre appareil TPM, en créant par exemple un code de hachage SHA-256 pour la paire de clés de type EK.
>
> Suivez les instructions de l'article [Gérer les inscriptions d'appareils à l'aide du portail Azure](../iot-dps/how-to-manage-enrollments.md) pour créer votre inscription dans DPS, puis poursuivez avec la section [Installer le runtime IoT Edge](#install-the-iot-edge-runtime) de cet article.

## <a name="simulate-a-tpm-device"></a>Simuler un appareil TPM

Créez un appareil TPM simulé sur votre machine de développement Windows. Récupérez l'**ID d'inscription** et la **Paire de clés de type EK (Endorsement Key)** de votre appareil, et utilisez-les pour créer une entrée d'inscription individuelle dans DPS.

Lorsque vous créez une inscription auprès du service Device Provisioning, vous avez la possibilité de déclarer un **État initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-at-scale.md).

Choisissez le langage du kit SDK que vous souhaitez utiliser pour créer l’appareil simulé et suivez les étapes jusqu’à la création de l’inscription individuelle.

Lorsque vous créez l’inscription individuelle, sélectionnez **True** pour déclarer que l’appareil TPM simulé sur votre machine de développement Windows est un **appareil IoT Edge**.

> [!TIP]
> Dans l'interface de ligne de commande Azure, vous pouvez créer une [inscription](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) ou un [groupe d'inscriptions](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) et utiliser l'indicateur **compatible avec Edge** pour spécifier qu'un appareil, ou un groupe d'appareils, est un appareil IoT Edge.

Guides d’appareils simulés et d’inscriptions individuelles :

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Après avoir créé l’inscription individuelle, enregistrez la valeur de l’**ID d’inscription**. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Ses composants s’exécutent dans des conteneurs et vous permettent de déployer des conteneurs supplémentaires sur l’appareil, pour que vous puissiez exécuter du code en périphérie.

Vous aurez besoin des informations suivantes lors de l’approvisionnement de votre appareil :

* Valeur **Étendue de l’ID** du service Device Provisioning
* L’**ID d’inscription** de l’appareil que vous avez créé

Installez le runtime IoT Edge sur l'appareil qui exécute le TPM simulé. Vous allez configurer le runtime IoT Edge pour un provisionnement automatique et non manuel.

> [!TIP]
> Gardez ouverte la fenêtre dans laquelle s’exécute le simulateur TPM durant l’installation et les tests.

Pour plus d'informations sur l'installation d'IoT Edge sous Windows, notamment sur les conditions préalables et les instructions relatives aux tâches telles que la gestion des conteneurs et la mise à jour d'IoT Edge, consultez [Installer le runtime Azure IoT Edge sous Windows](how-to-install-iot-edge-windows.md).

1. Ouvrez une fenêtre PowerShell en mode administrateur. Veillez à utiliser une session AMD64 de PowerShell lors de l'installation d'IoT Edge, plutôt que PowerShell (x86).

1. La commande **Deploy-IoTEdge** vérifie que votre ordinateur Windows a une version prise en charge, active la fonctionnalité des conteneurs, avant de télécharger le runtime moby et le runtime IoT Edge. Par défaut, la commande utilise des conteneurs de Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. À ce stade, les appareils IoT Core peuvent redémarrer automatiquement. D’autres appareils Windows 10 ou Windows Server peuvent vous inviter à redémarrer. Si c’est le cas, redémarrez votre appareil maintenant. Une fois votre appareil prêt, exécutez à nouveau PowerShell en tant qu’administrateur.

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
