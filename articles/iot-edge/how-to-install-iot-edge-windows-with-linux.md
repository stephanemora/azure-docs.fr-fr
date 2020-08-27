---
title: Installer Azure IoT Edge pour Linux sur Windows | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge pour conteneurs Linux sur Windows 10, Windows Server et Windows IoT Standard
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: c3a23e0c2546da55f977d589eb38607994d3902b
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88611787"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Utiliser IoT Edge sur Windows pour exécuter des conteneurs Linux

Testez les modules IoT Edge pour appareils Linux à l’aide d’une machine Windows.

Dans un scénario de production, les appareils Windows doivent exclusivement exécuter des conteneurs Windows. Toutefois, en phase de développement, il est courant d'utiliser un ordinateur Windows pour créer des modules IoT Edge destinés aux appareils Linux. Le runtime IoT Edge pour Windows vous permet d’exécuter des conteneurs Linux à des fins de **test et de développement**.

Cet article répertorie les étapes d’installation du runtime Azure IoT Edge avec les conteneurs Linux sur votre système Windows x64 (AMD/Intel). Pour en savoir plus sur le programme d’installation du runtime IoT Edge, notamment ses paramètres d’installation, consultez [Installer le runtime Azure IoT Edge sur Windows](how-to-install-iot-edge-windows.md).

Pour plus d’informations sur ce qu’inclut la dernière version d’IoT Edge, voir les [publications d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Conditions préalables requises

Cette section a pour but de vous aider à déterminer si votre appareil Windows peut prendre en charge IoT Edge, ainsi qu’à le préparer à un moteur de conteneur avant installation.

### <a name="supported-windows-versions"></a>Versions de Windows prises en charge

Azure IOT Edge avec des conteneurs Linux peut s’exécuter sur n’importe quelle version de Windows qui correspond à la [configuration requise pour Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Si vous souhaitez installer IoT Edge sur une machine virtuelle, activez la virtualisation imbriquée et allouez au moins 2 Go de mémoire. La manière dont vous activez la virtualisation imbriquée dépend de l’hyperviseur que vous utilisez. Pour Hyper-V, la virtualisation imbriquée est activée par défaut sur les machines virtuelles de deuxième génération. Pour VMware, un bouton bascule permet d’activer cette fonctionnalité sur votre machine virtuelle.

### <a name="prepare-the-container-engine"></a>Préparer le moteur de conteneur

Azure IoT Edge s’appuie sur un moteur de conteneur [compatible avec OCI](https://www.opencontainers.org/). En termes de configuration, la principale différence entre l'exécution de conteneurs Windows et Linux sur une machine Windows tient au fait que l'installation d'IoT Edge inclut un runtime de conteneurs Windows, mais que vous devez fournir votre propre runtime pour les conteneurs Linux avant d’installer IoT Edge.

Pour configurer une machine Windows afin de développer et tester des conteneurs pour appareils Linux, vous pouvez utiliser [Docker Desktop](https://www.docker.com/docker-windows) en tant que moteur de conteneur. Vous devez installer Docker et le configurer pour [utiliser des conteneurs Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) avant d’installer IoT Edge.  

Si votre appareil Azure IoT Edge est un ordinateur Windows, vérifiez qu’il présente la [configuration requise](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) pour Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Installer IoT Edge sur un nouvel appareil

>[!NOTE]
>Les packages logiciels Azure IoT Edge sont soumis aux termes de contrat de licence situés dans les packages (dans le répertoire LICENSE). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

Un script PowerShell télécharge et installe le démon de sécurité Azure IoT Edge. Le démon de sécurité démarre ensuite le premier des deux modules de runtime, l’agent IoT Edge qui permet de déployer à distance d’autres modules.

Lorsque vous installez le runtime IoT Edge pour la première fois sur un appareil, vous devez fournir à celui-ci une identité d’un hub IoT. Un appareil IoT Edge unique peut être approvisionné manuellement à l’aide d’une chaîne de connexion d’appareil fournie par votre hub IoT. Vous pouvez également utiliser le service Device Provisioning pour provisionner automatiquement des appareils, ce qui s’avère particulièrement utile lorsque vous devez configurer de nombreux appareils.

Pour en savoir plus sur les différents paramètres et options d'installation, consultez [Installer le runtime Azure IoT Edge sur Windows](how-to-install-iot-edge-windows.md). Une fois Docker Desktop installé et configuré pour les conteneurs Linux, la principale différence d’installation consiste à déclarer Linux avec le paramètre **- ContainerOs**. Par exemple :

1. Si ce n’est pas déjà fait, inscrivez un nouvel appareil IoT Edge et récupérez la chaîne de connexion d’appareil. Copiez cette chaîne de connexion pour l’utiliser plus tard dans cette section. Vous pouvez effectuer cette étape à l’aide des outils suivants :

   * [Azure portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Exécutez PowerShell ISE en tant qu’administrateur.

   >[!NOTE]
   >Utilisez une session AMD64 de PowerShell pour installer IoT Edge, pas PowerShell (x86). Si vous ne savez pas quel type de session vous utilisez, exécutez la commande suivante :
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. La commande **Deploy-IoTEdge** vérifie que votre ordinateur Windows a une version prise en charge, active la fonctionnalité des conteneurs, avant de télécharger le runtime moby (qui n'est pas utilisé pour les conteneurs Linux) et le runtime IoT Edge. Par défaut, la commande utilise des conteneurs Windows et vous devez donc déclarer Linux en tant que le système d’exploitation des conteneurs.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. À ce stade, les appareils IoT Core peuvent redémarrer automatiquement. D’autres appareils Windows 10 ou Windows Server peuvent vous inviter à redémarrer. Si c’est le cas, redémarrez votre appareil maintenant. Une fois votre appareil prêt, exécutez à nouveau PowerShell en tant qu’administrateur.

5. La commande **Initialize-IoTEdge** configure le runtime IoT Edge sur votre ordinateur. Par défaut, la commande est une commande d’approvisionnement manuel avec une chaîne de connexion d'appareil. Déclarez une nouvelle fois Linux en tant que système d’exploitation des conteneurs.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Lorsque vous y êtes invité, renseignez la chaîne de connexion que vous avez récupérée dans l’étape 1. La chaîne de connexion d’appareil associe l’appareil physique à un ID d’appareil dans IoT Hub.

   La chaîne de connexion se présente au format suivant et ne doit pas contenir de guillemets : `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Vérifiez l’état du service IoT Edge :

```powershell
Get-Service iotedge
```

Examinez les journaux du service des 5 dernières minutes :

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Exécutez une vérification automatisée des erreurs de configuration et de mise en réseau les plus courantes :

```powershell
iotedge check
```

Répertoriez les modules en cours d’exécution. Après une nouvelle installation, le seul module que vous devez voir en cours d’exécution est **edgeAgent**. Une fois que vous avez [déployé les modules IoT Edge](how-to-deploy-modules-portal.md) pour la première fois, l’autre module système, **edgeHub**, démarre également sur l’appareil.

```powershell
iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous éprouvez des difficultés à installer correctement IoT Edge, voir la page [Résolution des problèmes](troubleshoot.md).

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).
