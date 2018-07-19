---
title: Guide pratique pour installer Azure IoT Edge sur Linux | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 0174aa2288bbb95cc5cfc796446893fde00a8964
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344349"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Installer le runtime Azure IoT Edge sur Linux (x64)

Le runtime Azure IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend trois composants. Le **démon de sécurité IoT Edge** fournit et gère les standards de sécurité sur l’appareil Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant l’agent IoT Edge. **L’agent IoT Edge** facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge, y compris le hub IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub.

Cet article liste les étapes pour installer le runtime Azure IoT Edge sur votre appareil Edge Linux x64 (Intel/AMD).

>[!NOTE]
>Chaque package des référentiels de logiciels Linux est soumis aux termes du contrat de licence qu’il contient (/usr/share/doc/*nom_package*). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Inscrire la clé Microsoft et le flux des référentiels de logiciels

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/

# Perform apt upgrade
sudo apt-get upgrade
```

## <a name="install-the-container-runtime"></a>Installer le runtime de conteneur 

Azure IoT Edge s’appuie sur un runtime de conteneur [compatible avec OCI][lnk-oci] (par exemple, Docker). Si Docker CE/EE est déjà installé sur votre appareil Edge, vous pouvez continuer à l’utiliser à des fins de développement et de test avec Azure IoT Edge. 

Pour les scénarios de production, nous vous recommandons vivement d’utiliser le moteur [basé sur Moby][lnk-moby] fourni ci-dessous. C’est le seul moteur de conteneur officiellement pris en charge avec Azure IoT Edge. Les images conteneur Docker CE/EE sont totalement compatibles avec le runtime Moby.

Mettez à jour apt-get.

```bash
sudo apt-get update
```

Installez le moteur Moby et l’interface de ligne de commande (CLI). L’interface CLI est utile pour le développement, mais facultative pour les déploiements de production.*

```bash
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installer le démon de sécurité Azure IoT Edge

Les commandes ci-dessous installent également la version standard **d’iothsmlib** si elle n’est pas déjà présente.

```bash
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurer le démon de sécurité Azure IoT Edge

Le démon peut être configuré à l’aide du fichier de configuration situé à l’emplacement `/etc/iotedge/config.yaml`. Le fichier étant protégé en écriture par défaut, vous pouvez avoir besoin d’autorisations élevées pour le modifier.

```bash
sudo nano /etc/iotedge/config.yaml
```

L’appareil Edge peut être configuré manuellement à l’aide d’une [chaîne de connexion d’appareil][lnk-dcs] ou [automatiquement par le biais du service Device Provisioning][lnk-dps].

* Pour la configuration manuelle, supprimez les marques de commentaire du mode de provisionnement **manuel**. Mettez à jour la valeur de **device_connection_string** avec la chaîne de connexion à partir de votre appareil IoT Edge.

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

* Pour la configuration automatique, supprimez les marques de commentaire du mode de provisionnement **dps**. Mettez à jour les valeurs de **scope_id** et de **registration_id** avec les valeurs de votre instance de service IoT Hub Device Provisioning et de votre appareil IoT Edge à l’aide du module de plateforme sécurisée. 

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

Après avoir entré les informations de provisionnement dans la configuration, redémarrez le démon :

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si vous avez utilisé la procédure de **configuration manuelle** dans la section précédente, le runtime IoT Edge doit être correctement provisionné et en cours d’exécution sur votre appareil. Si vous avez utilisé la procédure de **configuration automatique**, vous devez suivre quelques étapes supplémentaires pour que le runtime puisse inscrire à votre place votre appareil avec votre hub IoT. Pour connaître les étapes suivantes, consultez [Créer et provisionner un appareil Edge avec TPM simulé sur une machine virtuelle Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Vous pouvez vérifier l’état du démon IoT Edge à l’aide de la commande suivante :

```cmd/sh
systemctl status iotedge
```

Examinez les journaux du démon à l’aide de la commande suivante :

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Enfin, répertoriez les modules en cours d’exécution à l’aide de la commande suivante :

```cmd/sh
sudo iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Si vous ne parvenez pas à installer correctement le runtime Edge, consultez la page de [dépannage][lnk-trouble].

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
