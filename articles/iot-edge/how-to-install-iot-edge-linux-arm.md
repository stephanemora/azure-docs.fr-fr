---
title: Installer Azure IoT Edge sur Linux ARM32 | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur Linux sur des appareils ARM32, comme un Raspberry PI
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kgremban
ms.openlocfilehash: ad7e34110b0c6d047eb7454b7fac9f8c10df8be2
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316097"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Installer le runtime Azure IoT Edge sur Linux (ARM32v7/armhf)

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud. 

Pour en savoir plus sur le fonctionnement du runtime IoT Edge et les composants inclus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article répertorie les étapes pour installer le runtime Azure IoT Edge sur un appareil Linux ARM32v7/armhf IoT Edge. Par exemple, ces étapes sont adaptées aux appareils Raspberry Pi. Pour obtenir la liste des systèmes d’exploitation ARM32, consultez [prise en charge Azure IoT Edge](support.md#operating-systems). 

>[!NOTE]
>Chaque package des référentiels de logiciels Linux est soumis aux termes du contrat de licence qu’il contient (/usr/share/doc/*nom_package*). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

## <a name="install-the-container-runtime"></a>Installer le runtime de conteneur

Azure IoT Edge s’appuie sur un runtime de conteneur [compatible avec OCI](https://www.opencontainers.org/). Pour les scénarios en production, nous vous recommandons vivement d’utiliser le moteur [basé sur Moby](https://mobyproject.org/) fourni ci-dessous. C’est le seul moteur de conteneur officiellement pris en charge avec Azure IoT Edge. Les images conteneur Docker CE/EE sont compatibles avec le runtime Moby.

Les commandes suivantes installent le moteur Moby et l’interface de ligne de commande (CLI). L’interface CLI est utile pour le développement, mais facultative pour les déploiements de production.

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

Le **démon de sécurité IoT Edge** fournit et gère les normes de sécurité sur l’appareil IoT Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge. 


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

Le démon peut être configuré à l’aide du fichier de configuration situé à l’emplacement `/etc/iotedge/config.yaml`. Le fichier est protégé en écriture par défaut, vous devrez donc peut-être des autorisations élevées pour le modifier.

Un appareil IoT Edge unique peut être approvisionné manuellement à l’aide d’une chaîne de connexion d’appareil fournie par IoT Hub. Vous pouvez également utiliser le service Device Provisioning pour approvisionner automatiquement des appareils. Ce service s’avère particulièrement utile lorsque vous devez approvisionner de nombreux appareils. Choisissez le script d’installation approprié selon votre choix en matière d’approvisionnement. 

### <a name="option-1-manual-provisioning"></a>Option 1 : Provisionnement manuel

Pour configurer manuellement un appareil, vous devez lui fournir avec un [chaîne de connexion d’appareil](how-to-register-device-portal.md) que vous pouvez créer en inscrivant un nouvel appareil IoT Edge dans votre IoT hub.

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

Pour provisionner automatiquement un appareil, [configurez le Service Device Provisioning et récupérez votre ID d’inscription d’appareil](how-to-auto-provision-simulated-device-linux.md). Le provisionnement automatique fonctionne uniquement avec des appareils qui possèdent une puce de Module de plateforme sécurisée (TPM). Par exemple, les appareils Raspberry Pi ne sont pas équipés d’une puce TPM par défaut. 

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

Si vous avez utilisé la procédure de **configuration manuelle** dans la section précédente, le runtime IoT Edge doit être correctement provisionné et en cours d’exécution sur votre appareil. Ou, si vous avez utilisé le **configuration automatique** étapes, vous devez suivre quelques étapes supplémentaires afin que le runtime puisse inscrire votre appareil avec votre IoT hub à votre place. Pour connaître les étapes suivantes, consultez [créer et approvisionner un appareil TPM IoT Edge simulé sur une machine virtuelle Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

Sur les appareils aux ressources limitées, il est vivement recommandé d’affecter la valeur *false* à la variable d’environnement *OptimizeForPerformance*, conformément aux instructions figurant dans le [guide de résolution des problèmes](troubleshoot.md#stability-issues-on-resource-constrained-devices).

Si votre réseau comporte un serveur proxy, effectuez les étapes décrites dans [Configurer votre appareil IoT Edge pour communiquer via un serveur proxy](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Désinstaller IoT Edge

Si vous souhaitez supprimer l’installation d’IoT Edge de votre appareil Linux, utilisez les commandes suivantes à partir de la ligne de commande. 

Supprimez le runtime IoT Edge. 

```bash
sudo apt-get remove --purge iotedge
```

Lorsque le runtime IoT Edge est supprimé, les conteneurs qu’il a créés sont arrêtés, mais existent toujours sur votre appareil. Affichez tous les conteneurs pour voir ceux qui restent. 

```bash
sudo docker ps -a
```

Supprimez les conteneurs de votre appareil, dont les deux conteneurs de runtime. 

```bash
sudo docker rm -f <container name>
```

Enfin, supprimez le runtime du conteneur de votre appareil. 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous rencontrez des problèmes avec le runtime IoT Edge installation correctement, reportez-vous à la [dépannage](troubleshoot.md#stability-issues-on-resource-constrained-devices) page.

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).
