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
ms.openlocfilehash: 43f82341a3cc9d2163afd35e42864aaa7866b1b2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035169"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Installer le runtime Azure IoT Edge sur Linux (x64)

Le runtime Azure IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend trois composants. Le **démon de sécurité IoT Edge** fournit et gère les standards de sécurité sur l’appareil Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant l’agent IoT Edge. L’**agent IoT Edge** facilite le déploiement et la surveillance des modules sur l’appareil Edge, y compris le hub IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub.

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
```

### <a name="debian-9"></a>Debian 9

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/debian/9/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

## <a name="install-the-container-runtime"></a>Installer le runtime de conteneurs 

Azure IoT Edge s’appuie sur un runtime de conteneur [compatible avec OCI][lnk-oci] (par exemple, Docker). Si Docker CE/EE est déjà installé sur votre appareil Edge, vous pouvez continuer à l’utiliser à des fins de développement et de test avec Azure IoT Edge. 

Pour les scénarios de production, nous vous recommandons vivement d’utiliser le moteur [basé sur Moby][lnk-moby] fourni ci-dessous. C’est le seul moteur de conteneur officiellement pris en charge avec Azure IoT Edge. Les images conteneur Docker CE/EE sont totalement compatibles avec le runtime Moby.

*Les instructions ci-dessous permettent d’installer le moteur moby et l’interface de ligne de commande (CLI). L’interface CLI est utile pour le développement, mais facultative pour les déploiements de production.*

```cmd/sh
sudo apt-get update
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installer le démon de sécurité Azure IoT Edge

Les commandes ci-dessous installent également la version standard **d’iothsmlib** si elle n’est pas déjà présente.

```cmd/sh
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configure le démon de sécurité Azure IoT Edge

Vous pouvez configurer le démon à l’aide du fichier de configuration disponible à l’emplacement `/etc/iotedge/config.yaml`. Vous pouvez configurer l’appareil Edge <!--[automatically via Device Provisioning Service][lnk-dps] or--> manuellement en utilisant une [chaîne de connexion d’appareil][lnk-dcs].

Pour une configuration manuelle, entrez la chaîne de connexion d’appareil dans la section **provisioning** de **config.yaml**.

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*Le fichier étant protégé en écriture par défaut, vous devrez peut-être utiliser `sudo` pour le modifier. Par exemple `sudo nano /etc/iotedge/config.yaml`*

Après avoir entré les informations de provisionnement dans la configuration, redémarrez le démon :

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

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
iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Si vous ne parvenez pas à installer le runtime Edge correctement, consultez la page de [dépannage][lnk-trouble].

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
