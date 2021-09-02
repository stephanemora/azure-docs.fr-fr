---
title: Provisionner un appareil à l’aide d’une attestation de clé symétrique - Azure IoT Edge
description: Utiliser l’attestation de clé symétrique pour tester l’approvisionnement automatique des appareils pour Azure IoT Edge avec le service Device Provisioning
author: kgremban
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/21/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d4aa7f1a02d8ab789810f06b38c95e9cfd76d5fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562920"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Créer et approvisionner un appareil IoT Edge à l’aide de l’attestation de clé symétrique

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Les appareils Azure IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Device Provisioning](../iot-dps/index.yml) tout comme les appareils qui ne sont pas compatibles avec Edge. Si vous ne connaissez pas le processus de provisionnement automatique, révisez la présentation du [provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Cet article vous montre comment créer une inscription individuelle ou de groupe au service Device Provisioning à l’aide de l’attestation de clé symétrique sur un appareil IoT Edge, en procédant comme suit :

* Création d’une instance du service IoT Hub Device Provisioning.
* Création d’une inscription individuelle ou de groupe.
* Installation du runtime IoT Edge et de la connexion à IoT Hub.

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>Pour une expérience simplifiée, essayez l[outil de configuration Azure IoT Edge](https://github.com/azure/iot-edge-config). Cet outil en ligne de commande, actuellement disponible en préversion publique, installe IoT Edge sur votre appareil et le provisionne à l’aide du service IoT Hub Device Provisioning et de l’attestation de clé symétrique.
:::moniker-end

L’attestation de clé symétrique est une approche simple pour authentifier un appareil avec une instance du service Device Provisioning. Cette méthode d’attestation représente une expérience « Hello world » pour les développeurs qui découvrent le provisionnement d’appareils ou n’ont pas d’exigences de sécurité strictes. L’attestation d’appareil avec un [Module de plateforme sécurisée (TPM)](../iot-dps/concepts-tpm-attestation.md) ou un [certificat X.509](../iot-dps/concepts-x509-attestation.md) est plus sécurisée, et doit être utilisée lorsque les exigences de sécurité sont plus strictes.

## <a name="prerequisites"></a>Prérequis

* Un IoT Hub actif
* Un appareil physique ou virtuel

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurer le service IoT Hub Device Provisioning

Créez une nouvelle instance du service IoT Hub Device Provisioning dans Azure et liez-la à votre hub IoT. Vous pouvez suivre les instructions dans [Configurer le service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md).

Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.

## <a name="choose-a-unique-device-registration-id"></a>Choisir un ID d’inscription d’appareil unique

Un ID d’inscription unique doit être défini pour identifier chaque appareil. Vous pouvez utiliser l’adresse MAC, le numéro de série ou toute information unique provenant de l’appareil. Dans cet exemple, vous pouvez utiliser une combinaison d’une adresse MAC et d’un numéro de série, forment la chaîne suivante pour un ID d’inscription : `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Les caractères valides sont les caractères alphanumériques minuscules et les tirets (`-`).

## <a name="option-1-create-a-dps-individual-enrollment"></a>Option 1 : Créer une inscription individuelle dans le service IoT Hub Device Provisioning

Créez une inscription individuelle pour provisionner un seul appareil par le biais du service IoT Hub Device Provisioning.

Lorsque vous créez une inscription auprès du service Device Provisioning, vous avez la possibilité de déclarer un **État initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-at-scale.md).

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions individuelles à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne un appareil IoT Edge.

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

## <a name="option-2-create-a-dps-enrollment-group"></a>Option 2 : Créer un groupe d’inscription DPS

Utilisez l’ID d’inscription de votre appareil pour créer une inscription individuelle au service Device Provisioning.

Lorsque vous créez une inscription auprès du service Device Provisioning, vous avez la possibilité de déclarer un **État initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-at-scale.md).

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions individuelles à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne des appareils IoT Edge. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

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

Maintenant que le groupe d’inscriptions existe, le runtime IoT Edge peut provisionner automatiquement des appareils lors de l’installation.

### <a name="derive-a-device-key"></a>Dériver une clé d’appareil

Chaque appareil provisionné dans le cadre d’une inscription de groupe a besoin d’une clé d’appareil dérivée pour effectuer une attestation de clé symétrique avec l’inscription lors du provisionnement.

Pour générer une clé d’appareil, utilisez la clé que vous avez copiée à partir de votre groupe d’inscriptions DPS pour calculer un [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) de l’ID d’inscription unique pour l’appareil et convertir le résultat au format Base64.

N’incluez pas la clé primaire ou secondaire de votre inscription dans le code de votre appareil.

#### <a name="derive-a-key-on-linux"></a>Dériver une clé sur Linux

Sur Linux, vous pouvez utiliser openssl pour générer votre clé d’appareil dérivée, comme indiqué dans l’exemple suivant.

Remplacez la valeur **KEY** par la **clé primaire** que vous avez notée précédemment.

Remplacez la valeur de **REG_ID** avec l’ID d’inscription de votre appareil.

```bash
KEY=PASTE_YOUR_ENROLLMENT_KEY_HERE
REG_ID=PASTE_YOUR_REGISTRATION_ID_HERE

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

#### <a name="derive-a-key-on-windows"></a>Dériver une clé sur Windows

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

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Ses composants s’exécutent dans des conteneurs et vous permettent de déployer des conteneurs supplémentaires sur l’appareil, pour que vous puissiez exécuter du code en périphérie.

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

Suivez les étapes appropriées pour installer Azure IoT Edge en fonction de votre système d’exploitation :

* [Installer IoT Edge pour Linux](how-to-install-iot-edge.md)
* [Installer IoT Edge pour des appareils Linux sur Windows](how-to-install-iot-edge-on-windows.md)
  * Ce scénario est la méthode recommandée pour exécuter IoT Edge sur des appareils Windows.
* [Installer IoT Edge avec des conteneurs Windows](how-to-install-iot-edge-windows-on-windows.md)

Une fois que IoT Edge est installé sur votre appareil, revenez à cet article pour provisionner l’appareil.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Suivez la procédure décrite dans [Installer le runtime Azure IoT Edge](how-to-install-iot-edge.md), puis revenez à cet article pour provisionner l’appareil.

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-the-device-with-provisioning-information"></a>Configuration de l’appareil avec des informations de provisionnement

Une fois que le runtime est installé sur votre appareil, configurez l’appareil avec les informations qu’il utilise pour se connecter au service Device Provisioning (DPS) et à IoT Hub.

Préparez les informations suivantes :

* Valeur **Étendue de l’ID** du service Device Provisioning
* L’**ID d’inscription** de l’appareil que vous avez créé
* Entrez la **clé primaire** d’une inscription spécifique ou une [clé dérivée](#derive-a-device-key) pour les appareils utilisant une inscription de groupe.

# <a name="linux"></a>[Linux](#tab/linux)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Ouvrez le fichier de configuration sur l’appareil IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Recherchez la section des configurations de provisionnement dans le fichier. Décommentez les lignes du provisionnement avec les clés symétriques du service Device Provisioning et assurez-vous que les autres lignes de provisionnement sont commentées.

   La ligne `provisioning:` ne doit pas être précédée d’un espace et les éléments imbriqués doivent être en retrait de deux espaces.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
     attestation:
       method: "symmetric_key"
       registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
       symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Mettez à jour les valeurs de `scope_id`, `registration_id` et `symmetric_key` avec vos informations relatives à l’appareil et à DPS.

1. Si vous le souhaitez, utilisez les lignes `always_reprovision_on_startup` ou `dynamic_reprovisioning` pour configurer le comportement de reprovisionnement de votre appareil. Si un appareil est configuré pour être reprovisionné au démarrage, le reprovisionnement est toujours tenté avec le service DPS dans un premier temps, puis, en cas d’échec, au moyen de la sauvegarde de provisionnement. Si un appareil est configuré pour se reprovisionner dynamiquement lui-même, IoT Edge redémarre et exécute le reprovisionnement si un événement de reprovisionnement est détecté. Pour plus d’informations, consultez [Concepts du reprovisionnement d’appareils IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Redémarrez le runtime IoT Edge afin qu’il récupère toutes les modifications de configuration que vous avez effectuées sur l’appareil.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Créez un fichier de configuration pour votre appareil à partir d’un fichier de modèle fourni dans le cadre de l’installation d’IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Ouvrez le fichier de configuration sur l’appareil IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Recherchez la section **Provisioning** du fichier. Supprimez les marques de commentaire des lignes pour l’approvisionnement par clé symétrique de DPS, et assurez-vous que les autres lignes d’approvisionnement sont commentées.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"

   symmetric_key = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   ```

1. Mettez à jour les valeurs de `id_scope`, `registration_id` et `symmetric_key` avec vos informations relatives à l’appareil et à DPS.

   Le paramètre de clé symétrique peut accepter la valeur d’une clé incluse, d’un URI de fichier ou d’un URI de PKCS#11. Supprimez les marques de commentaire d’une ligne de clé symétrique, en fonction du format que vous utilisez.

   Si vous utilisez des URI PKCS#11, recherchez la section **PKCS#11** dans le fichier config et fournissez des informations sur votre configuration de PKCS#11.

1. Enregistrez et fermez le fichier config.toml.

1. Appliquez les modifications de configuration que vous avez apportées à IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

# <a name="linux-on-windows"></a>[Linux sur Windows](#tab/eflow)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Vous pouvez utiliser PowerShell ou Windows Admin Center pour provisionner votre appareil IoT Edge.

### <a name="powershell"></a>PowerShell

Pour PowerShell, exécutez la commande suivante en mettant à jour les valeurs d’espace réservé avec vos propres valeurs :

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

### <a name="windows-admin-center"></a>Windows Admin Center

Pour Windows Admin Center, procédez comme suit :

1. Dans le volet **Approvisionnement de l’appareil Azure IoT Edge**, sélectionnez **Clés symétriques (DPS)** dans le menu déroulant des méthodes d’approvisionnement.

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à votre instance DPS.

1. Indiquez votre ID d’étendue DPS, l’ID d’inscription de l’appareil et la clé primaire ou la clé dérivée de l’inscription dans les champs Windows Admin Center.

1. Sélectionnez **Approvisionnement avec la méthode sélectionnée**.

   ![Sélectionnez Approvisionnement avec la méthode sélectionnée après avoir renseigné les champs requis pour l’approvisionnement avec clé symétrique](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Une fois l’approvisionnement terminé, sélectionnez **Terminer**. Vous êtes redirigé vers le tableau de bord principal. Un nouvel appareil, dont le type est `IoT Edge Devices`, doit maintenant apparaître dans la liste. Vous pouvez sélectionner l’appareil IoT Edge pour vous y connecter. Une fois sur la page de **vue d’ensemble**, vous pouvez consulter la **liste des modules IoT Edge** et l’**état IoT Edge** de votre appareil.

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Actuellement, il n’existe pas de prise en charge d’IoT Edge version 1.2 s’exécutant sur IoT Edge pour Linux sur Windows.

:::moniker-end
<!-- end 1.2 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

1. Ouvrez une fenêtre PowerShell en mode administrateur. Veillez à utiliser une session AMD64 de PowerShell lors de l'installation d'IoT Edge, plutôt que PowerShell (x86).

1. La commande **Initialize-IoTEdge** configure le runtime IoT Edge sur votre ordinateur. La commande opère par défaut un approvisionnement manuel avec les conteneurs Windows. Vous devez donc ajouter l’indicateur `-DpsSymmetricKey` pour utiliser l’approvisionnement automatique avec l’authentification par clé symétrique.

   Remplacez les valeurs d’espace réservé pour `{scope_id}`, `{registration_id}` et `{symmetric_key}` par les données que vous avez collectées précédemment.

   Ajoutez le paramètre `-ContainerOs Linux` si vous utilisez des conteneurs Linux sur Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Actuellement, il n’existe pas de prise en charge d’IoT Edge version 1.2 s’exécutant sur Windows.

:::moniker-end
<!-- end 1.2 -->

---

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a démarré correctement, vous pouvez accéder à votre IoT Hub et commencer à déployer des modules IoT Edge sur votre appareil.

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

Utilisez les commandes suivantes sur votre appareil pour vérifier que IoT Edge a été installé et démarré correctement.

# <a name="linux"></a>[Linux](#tab/linux)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

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

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Vérifiez l’état du service IoT Edge.

```cmd/sh
sudo iotedge system status
```

Consultez les journaux d’activité de service.

```cmd/sh
sudo iotedge system logs
```

Répertoriez les modules en cours d’exécution.

```cmd/sh
sudo iotedge list
```

:::moniker-end

# <a name="linux-on-windows"></a>[Linux sur Windows](#tab/eflow)

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Connectez-vous à IoT Edge pour Linux sur une machine virtuelle Windows.

```powershell
Connect-EflowVM
```

Vérifiez l’état du service IoT Edge.

```cmd/sh
sudo systemctl status iotedge
```

Consultez les journaux d’activité de service.

```cmd/sh
sudo journalctl -u iotedge --no-pager --no-full
```

Répertoriez les modules en cours d’exécution.

```cmd/sh
sudo iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Actuellement, il n’existe pas de prise en charge d’IoT Edge version 1.2 s’exécutant sur IoT Edge pour Linux sur Windows.

:::moniker-end
<!-- end 1.2 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
:::moniker range="=iotedge-2018-06"

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

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Actuellement, il n’existe pas de prise en charge d’IoT Edge version 1.2 s’exécutant sur Windows.

:::moniker-end
<!-- end 1.2 -->

---

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service Device Provisioning vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).
