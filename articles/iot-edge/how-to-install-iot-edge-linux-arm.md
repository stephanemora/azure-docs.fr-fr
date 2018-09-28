---
title: Installer Azure IoT Edge sur Linux ARM32 | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur Linux sur des appareils ARM32, comme un Raspberry PI
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 9007ee95dc25854a55006284ce6fc574fd8e7968
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948517"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Installer le runtime Azure IoT Edge sur Linux (ARM32v7/armhf)

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud. 

Pour en savoir plus sur le fonctionnement du runtime IoT Edge et les composants inclus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article répertorie les étapes pour installer le runtime Azure IoT Edge sur un appareil Edge Linux ARM32v7/armhf. Par exemple, ces étapes sont adaptées aux appareils Raspberry Pi. Consultez la [prise en charge Azure IoT Edge](support.md#operating-systems) pour obtenir la liste des systèmes d’exploitation ARM32 actuellement pris en charge. 

>[!NOTE]
>Chaque package des référentiels de logiciels Linux est soumis aux termes du contrat de licence qu’il contient (/usr/share/doc/*nom_package*). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

## <a name="install-the-container-runtime"></a>Installer le runtime de conteneur

Azure IoT Edge s’appuie sur un runtime de conteneur [compatible avec OCI][lnk-oci]. Pour les scénarios de production, nous vous recommandons vivement d’utiliser le moteur [basé sur Moby][lnk-moby] fourni ci-dessous. C’est le seul moteur de conteneur officiellement pris en charge avec Azure IoT Edge. Les images conteneur Docker CE/EE sont compatibles avec le runtime Moby.

Les commandes ci-dessous permettent d’installer le moteur Moby et l’interface de ligne de commande (CLI). L’interface CLI est utile pour le développement, mais facultative pour les déploiements de production.

```bash

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>Installer le démon de sécurité IoT Edge

Le **démon de sécurité IoT Edge** fournit et gère les standards de sécurité sur l’appareil Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge. 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="connect-your-device-to-an-iot-hub"></a>Connectez votre appareil à un IoT Hub 

Configurez le runtime IoT Edge pour lier votre appareil physique à une identité d’appareil existante dans un IoT Hub Azure. 

Le démon peut être configuré à l’aide du fichier de configuration situé à l’emplacement `/etc/iotedge/config.yaml`. Le fichier étant protégé en écriture par défaut, vous pouvez avoir besoin d’autorisations élevées pour le modifier.

Un appareil IoT Edge unique peut être approvisionné manuellement à l’aide d’une chaîne de connexion d’appareil fournie par IoT Hub. Vous pouvez également utiliser le service Device Provisioning pour approvisionner automatiquement des appareils. Ce service s’avère particulièrement utile lorsque vous devez approvisionner de nombreux appareils. Choisissez le script d’installation approprié selon votre choix en matière d’approvisionnement. 

### <a name="option-1-manual-provisioning"></a>Option 1 : Provisionnement manuel

Pour provisionner manuellement un appareil, vous devez lui fournir une [chaîne de connexion d’appareil][lnk-dcs] que vous pouvez créer en inscrivant un nouvel appareil dans votre IoT Hub.


Ouvrez le fichier de configuration. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Recherchez la section de provisionnement du fichier et supprimez les commentaires du mode de provisionnement **manuel**. Mettez à jour la valeur de **device_connection_string** avec la chaîne de connexion à partir de votre appareil IoT Edge.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

Enregistrez et fermez le fichier. 

   `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations de provisionnement dans le fichier de configuration, redémarrez le démon :

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Option 2 : Provisionnement automatique

Pour provisionner automatiquement un appareil, [configurez le Service Device Provisioning et récupérez votre ID d’inscription d’appareil][lnk-dps]. Le provisionnement automatique fonctionne uniquement avec des appareils qui possèdent une puce de Module de plateforme sécurisée (TPM). Par exemple, les appareils Raspberry Pi ne sont pas équipés d’une puce TPM par défaut. 

Ouvrez le fichier de configuration. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Recherchez la section de provisionnement du fichier et supprimez les commentaires du mode de provisionnement **dps**. Mettez à jour les valeurs de **scope_id** et de **registration_id** avec les valeurs de votre IoT Hub Device Provisioning Service et de votre appareil IoT Edge avec TPM. 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Enregistrez et fermez le fichier. 

   `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations de provisionnement dans le fichier de configuration, redémarrez le démon :

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si vous avez utilisé la procédure de **configuration manuelle** dans la section précédente, le runtime IoT Edge doit être correctement provisionné et en cours d’exécution sur votre appareil. Si vous avez utilisé la procédure de **configuration automatique**, vous devez suivre quelques étapes supplémentaires pour que le runtime puisse inscrire à votre place votre appareil avec votre hub IoT. Pour connaître les étapes suivantes, consultez [Créer et provisionner un appareil Edge avec TPM simulé sur une machine virtuelle Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Vous pouvez vérifier l’état du démon IoT Edge à l’aide de la commande suivante :

```bash
systemctl status iotedge
```

Examinez les journaux du démon à l’aide de la commande suivante :

```bash
journalctl -u iotedge --no-pager --no-full
```

Enfin, répertoriez les modules en cours d’exécution à l’aide de la commande suivante :

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Conseils et suggestions

Vous avez besoin de privilèges élevés pour exécuter les commandes `iotedge`. Après l’installation du runtime, déconnectez-vous de votre machine et reconnectez-vous pour mettre à jour vos autorisations automatiquement. Dans l’intervalle, utilisez **sudo** devant les commandes `iotedge`.

Sur des appareils avec contraintes de ressources, il est vivement recommandé d’affecter la valeur *false* à la variable d’environnement *OptimizeForPerformance* conformément aux instructions figurant dans le [guide de résolution des problèmes][lnk-trouble].

Si votre réseau comporte un serveur proxy, effectuez les étapes décrites dans [Configurer votre appareil IoT Edge pour communiquer via un serveur proxy](how-to-configure-proxy-support.md).

## <a name="next-steps"></a>Étapes suivantes

Si vous ne parvenez pas à installer correctement le runtime Edge, consultez la page de [dépannage][lnk-trouble].

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-trouble]: https://docs.microsoft.com/azure/iot-edge/troubleshoot#stability-issues-on-resource-constrained-devices
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
