---
title: Guide pratique pour installer Azure IoT Edge sur Linux | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur Linux sur ARM32
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: ad70fcc6b9779cb33772a3fce2fb11b4cec804ee
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062596"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Installer le runtime Azure IoT Edge sur Linux (ARM32v7/armhf)

Le runtime Azure IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend trois composants. Le **démon de sécurité IoT Edge** fournit et gère les standards de sécurité sur l’appareil Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant l’agent IoT Edge. **L’agent IoT Edge** facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge, y compris le hub IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub.

Cet article répertorie les étapes pour installer le runtime Azure IoT Edge sur un appareil Edge Linux ARM32v7/armhf (par exemple, Raspberry Pi).

>[!NOTE]
>Chaque package dans les référentiels de logiciels Linux est soumis aux termes du contrat de licence qu’il renferme (/usr/share/doc/*nom_package*). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package implique l’acceptation de ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

## <a name="install-the-container-runtime"></a>Installer le runtime de conteneur

Azure IoT Edge s’appuie sur un runtime de conteneur [compatible avec OCI][lnk-oci] (par exemple, Docker). Si Docker CE/EE est déjà installé sur votre appareil Edge, vous pouvez continuer à l’utiliser à des fins de développement et de test avec Azure IoT Edge. 

Pour les scénarios de production, nous vous recommandons vivement d’utiliser le moteur [basé sur Moby][lnk-moby] fourni ci-dessous. C’est le seul moteur de conteneur officiellement pris en charge avec Azure IoT Edge. Les images conteneur Docker CE/EE sont totalement compatibles avec le runtime Moby.

Les commandes ci-dessous permettent d’installer le moteur moby et l’interface de ligne de commande (CLI). L’interface CLI est utile pour le développement, mais facultative pour les déploiements de production.

```cmd/sh

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

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurer le démon de sécurité Azure IoT Edge

Vous pouvez configurer le démon à l’aide du fichier de configuration disponible à l’emplacement `/etc/iotedge/config.yaml`. Vous pouvez configurer l’appareil Edge <!--[automatically via Device Provisioning Service][lnk-dps] or--> manuellement en utilisant une [chaîne de connexion d’appareil][lnk-dcs].

Pour une configuration manuelle, entrez la chaîne de connexion d’appareil dans la section **provisioning** de **config.yaml**.

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*Le fichier étant protégé en écriture par défaut, vous devrez peut-être utiliser `sudo` pour le modifier. Par exemple, `sudo nano /etc/iotedge/config.yaml`*

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

Si vous ne parvenez pas à installer correctement le runtime Edge, consultez la page de [dépannage][lnk-trouble].

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md