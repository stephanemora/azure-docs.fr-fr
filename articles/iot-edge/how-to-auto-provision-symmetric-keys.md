---
title: Provisionner un appareil à l’aide d’une attestation de clé symétrique - Azure IoT Edge
description: Utiliser l’attestation de clé symétrique pour tester l’approvisionnement automatique des appareils pour Azure IoT Edge avec le service Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 10/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9944308d00c9cfecbd38a6443efb49913148806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535916"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Créer et approvisionner un appareil IoT Edge à l’aide de l’attestation de clé symétrique

Les appareils Azure IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Device Provisioning](../iot-dps/index.yml) tout comme les appareils qui ne sont pas compatibles avec Edge. Si vous ne connaissez pas le processus de provisionnement automatique, révisez les [concepts du provisionnement automatique](../iot-dps/concepts-auto-provisioning.md) avant de poursuivre.

Cet article vous montre comment créer une inscription individuelle au service Device Provisioning à l’aide de l’attestation de clé symétrique sur un appareil IoT Edge, en procédant comme suit :

* Création d’une instance du service IoT Hub Device Provisioning.
* Création d’une inscription individuelle pour l’appareil.
* Installation du runtime IoT Edge et de la connexion à IoT Hub.

L’attestation de clé symétrique est une approche simple pour authentifier un appareil avec une instance du service Device Provisioning. Cette méthode d’attestation représente une expérience « Hello world » pour les développeurs qui découvrent le provisionnement d’appareils ou n’ont pas d’exigences de sécurité strictes. L’attestation d’appareil avec un [Module de plateforme sécurisée (TPM)](../iot-dps/concepts-tpm-attestation.md) ou un [certificat X.509](../iot-dps/concepts-security.md#x509-certificates) est plus sécurisée, et doit être utilisée lorsque les exigences de sécurité sont plus strictes.

## <a name="prerequisites"></a>Conditions préalables requises

* Un IoT Hub actif
* Un appareil physique ou virtuel

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurer le service IoT Hub Device Provisioning

Créez une nouvelle instance du service IoT Hub Device Provisioning dans Azure et liez-la à votre hub IoT. Vous pouvez suivre les instructions dans [Configurer le service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md).

Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Choisir un ID d’inscription unique pour l’appareil

Un ID d’inscription unique doit être défini pour identifier chaque appareil. Vous pouvez utiliser l’adresse MAC, le numéro de série ou toute information unique provenant de l’appareil.

Dans cet exemple, nous utilisons une combinaison d’une adresse MAC et d’un numéro de série, forment la chaîne suivante pour un identifiant d’inscription : `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`.

Créez un ID d’inscription unique pour votre appareil. Les caractères valides sont les caractères alphanumériques minuscules et les tirets (« - »).

## <a name="create-a-dps-enrollment"></a>Créer une inscription au service Device Provisioning

Utilisez l’ID d’inscription de votre appareil pour créer une inscription individuelle au service Device Provisioning.

Lorsque vous créez une inscription auprès du service Device Provisioning, vous avez la possibilité de déclarer un **État initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-monitor.md).

> [!TIP]
> Les inscriptions de groupe sont également possibles lors de l’utilisation de l’attestation de clé symétrique et impliquent les mêmes décisions que les inscriptions individuelles.

1. Dans le [Portail Microsoft Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter une inscription individuelle** et suivez ces étapes pour configurer l’inscription :  

   1. Pour **Mécanisme**, sélectionnez **Clé symétrique**.

   1. Cochez la case **Générer automatiquement les clés**.

   1. Indiquez l’**ID d’inscription** que vous avez créé pour votre appareil.

   1. Fournissez un **ID d’appareil loT Hub** pour votre appareil si vous le souhaitez. Vous pouvez utiliser l’ID d’appareil pour cibler un appareil individuel lors du déploiement de module. Si vous ne fournissez pas un ID d’appareil, l’ID d’inscription est utilisé.

   1. Sélectionnez **Vrai** pour déclarer que cette inscription est un appareil IoT Edge. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

   1. Acceptez la valeur par défaut de la stratégie d’allocation du service Device Provisioning pour déterminer la façon dont **vous souhaitez affecter des appareils aux hubs** ou choisissez une autre valeur spécifique à cette inscription.

   1. Choisissez le **Hub IoT** lié auquel vous voulez connecter votre appareil. Vous pouvez choisir plusieurs hubs : l’appareil sera affecté à l’un d’entre eux en fonction de la stratégie d’allocation sélectionnée.

   1. Choisissez la façon dont **vous souhaitez que les données de l’appareil soient traitées lors du réapprovisionnement** lorsque les appareils demandent la configuration suite à la première fois.

   1. Ajoutez une valeur de balise à l’**État initial du jumeau d’appareil** si vous le souhaitez. Vous pouvez utiliser des balises pour cibler des groupes d’appareils lors du déploiement de module. Par exemple :

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

   1. Vérifiez que **Activer l’entrée** est définie sur **Activer**.

   1. Sélectionnez **Enregistrer**.

Maintenant qu’une inscription existe pour cet appareil, le runtime IoT Edge peut provisionner automatiquement l’appareil lors de l’installation. Veillez à copier la valeur de **Clé primaire** de votre inscription pour l’utiliser lors de l’installation du runtime IoT Edge ou si vous comptez créer des clés d’appareil à utiliser avec une inscription de groupe.

## <a name="derive-a-device-key"></a>Dériver une clé d’appareil

> [!NOTE]
> Cette section n’est obligatoire que si vous utilisez une inscription de groupe.

Chaque appareil utilise sa clé d’appareil dérivée avec votre ID d’inscription unique pour effectuer l’attestation de clé symétrique avec l’inscription lors de l’approvisionnement. Pour générer la clé de l’appareil, utilisez la clé que vous avez copiée à partir de votre inscription au service Device Provisioning pour calculer un [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) de l’ID d’inscription unique pour l’appareil et convertir le résultat au format Base64.

N’incluez pas la clé primaire ou secondaire de votre inscription dans le code de votre appareil.

### <a name="linux-workstations"></a>Stations de travail Linux

Si vous utilisez une station de travail Linux, vous pouvez utiliser openssl pour générer votre clé d’appareil dérivée, comme indiqué dans l’exemple suivant.

Remplacez la valeur **KEY** par la **clé primaire** que vous avez notée précédemment.

Remplacez la valeur de **REG_ID** avec l’ID d’inscription de votre appareil.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Stations de travail Windows

Si vous utilisez une station de travail Windows, utilisez PowerShell pour générer votre clé d’appareil dérivée, comme indiqué dans l’exemple suivant.

Remplacez la valeur **KEY** par la **clé primaire** que vous avez notée précédemment.

Remplacez la valeur de **REG_ID** avec l’ID d’inscription de votre appareil.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Ses composants s’exécutent dans des conteneurs et vous permettent de déployer des conteneurs supplémentaires sur l’appareil, pour que vous puissiez exécuter du code en périphérie.

Vous aurez besoin des informations suivantes lors de l’approvisionnement de votre appareil :

* Valeur **Étendue de l’ID** du service Device Provisioning
* L’**ID d’inscription** de l’appareil que vous avez créé
* La valeur de **Clé primaire** que vous avez copiée lors de l’inscription DPS

> [!TIP]
> Pour les inscriptions de groupe, vous devez avoir la [clé dérivée](#derive-a-device-key) de chaque appareil au lieu de la clé d’inscription DPS.

### <a name="linux-device"></a>Appareil Linux

Suivez les instructions relatives à l’architecture de votre appareil. Veillez à configurer le runtime IoT Edge pour le provisionnement automatique, et non manuel.

[Installer le runtime Azure IoT Edge sur Linux](how-to-install-iot-edge-linux.md)

La section du fichier de configuration pour l’approvisionnement des clés symétriques se présente comme suit :

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "<SCOPE_ID>"
   attestation:
      method: "symmetric_key"
      registration_id: "<REGISTRATION_ID>"
      symmetric_key: "<SYMMETRIC_KEY>"
```

Remplacez les valeurs d’espace réservé pour `<SCOPE_ID>`, `<REGISTRATION_ID>` et `<SYMMETRIC_KEY>` par les données que vous avez collectées précédemment. Assurez-vous que la ligne **approvisionnement :** n’est pas précédée d’une espace et que les éléments imbriqués sont en retrait de deux espaces.

### <a name="windows-device"></a>Appareil Windows

Installez le runtime IoT Edge sur l’appareil pour lequel vous avez généré une clé d’appareil dérivée. Vous allez configurer le runtime IoT Edge pour un provisionnement automatique et non manuel.

Pour plus d'informations sur l'installation d'IoT Edge sous Windows, notamment sur les conditions préalables et les instructions relatives aux tâches telles que la gestion des conteneurs et la mise à jour d'IoT Edge, consultez [Installer le runtime Azure IoT Edge sous Windows](how-to-install-iot-edge-windows.md).

1. Ouvrez une fenêtre PowerShell en mode administrateur. Veillez à utiliser une session AMD64 de PowerShell lors de l'installation d'IoT Edge, plutôt que PowerShell (x86).

1. La commande **Deploy-IoTEdge** vérifie que votre ordinateur Windows a une version prise en charge, active la fonctionnalité des conteneurs, avant de télécharger le runtime moby et le runtime IoT Edge. Par défaut, la commande utilise des conteneurs de Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. À ce stade, les appareils IoT Core peuvent redémarrer automatiquement. D’autres appareils Windows 10 ou Windows Server peuvent vous inviter à redémarrer. Si c’est le cas, redémarrez votre appareil maintenant. Une fois votre appareil prêt, exécutez à nouveau PowerShell en tant qu’administrateur.

1. La commande **Initialize-IoTEdge** configure le runtime IoT Edge sur votre ordinateur. La commande opère par défaut un approvisionnement manuel avec des conteneurs Windows, sauf si vous utilisez l’indicateur `-Dps` pour utiliser un approvisionnement automatique.

   Remplacez les valeurs d’espace réservé pour `{scope_id}`, `{registration_id}` et `{symmetric_key}` par les données que vous avez collectées précédemment.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a démarré correctement, vous pouvez accéder à votre IoT Hub et commencer à déployer des modules IoT Edge sur votre appareil. Utilisez les commandes suivantes sur votre appareil pour vérifier que le runtime a été installé et démarré correctement.

### <a name="linux-device"></a>Appareil Linux

Vérifiez l’état du service IoT Edge.

```cmd/sh
systemctl status iotedge
```

Consultez les journaux d’activité de service.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Répertoriez les modules en cours d’exécution.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Appareil Windows

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

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service Device Provisioning vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-monitor.md) ou [d’Azure CLI](how-to-deploy-monitor-cli.md).
