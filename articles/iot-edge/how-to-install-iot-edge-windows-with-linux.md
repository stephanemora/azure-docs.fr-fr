---
title: Installer Azure IoT Edge pour Linux sur Windows | Microsoft Docs
description: Instructions d’installation Azure IoT Edge pour les conteneurs Linux sur Windows 10, Windows Server et Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: b7386cbbe18d7e05c2fbffb96f6214b468956192
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66151699"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Utiliser IoT Edge sur Windows pour exécuter des conteneurs Linux

Testez les modules IoT Edge pour appareils Linux à l’aide d’un ordinateur Windows. 

Dans un scénario de production, les appareils Windows ne devraient exécuter les conteneurs Windows. Toutefois, un scénario courant de développement consiste à utiliser un ordinateur Windows pour générer des modules pour les appareils Linux IoT Edge. Le runtime IoT Edge pour Windows vous permet d’exécuter des conteneurs Linux pour **test et développement** à des fins. 

Cet article répertorie les étapes pour installer le runtime Azure IoT Edge à l’aide de conteneurs Linux sur votre Windows x64 (Intel/AMD) système. Pour en savoir plus sur le programme d’installation runtime IoT Edge, notamment des détails sur tous les paramètres d’installation, consultez [installer le runtime Azure IoT Edge sur Windows](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Conditions préalables

Cette section a pour but de vous aider à déterminer si votre appareil Windows peut prendre en charge IoT Edge, ainsi qu’à le préparer à un moteur de conteneur avant installation. 

### <a name="supported-windows-versions"></a>Versions de Windows prises en charge

Azure IoT Edge avec des conteneurs Linux peut s’exécuter sur les versions suivantes de Windows : 
* Mise à jour anniversaire Windows 10 (build 14393) ou une version ultérieure
* Windows Server 2016 ou version ultérieure

Pour plus d’informations sur qu’inclut la dernière version d’IoT Edge, voir les [publications d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Si vous souhaitez installer IoT Edge sur un ordinateur virtuel, activer la virtualisation imbriquée et allouez au moins 2 Go de mémoire. Comment vous activez la virtualisation imbriquée est différente selon l’hyperviseur de votre utilisation. Pour Hyper-V, les ordinateurs virtuels de génération 2 ont imbriquées virtualisation est activée par défaut. Pour VMWare, il existe un bouton bascule pour activer la fonctionnalité sur votre machine virtuelle. 

### <a name="prepare-the-container-engine"></a>Préparer le moteur de conteneur 

Azure IoT Edge s’appuie sur un moteur de conteneur [compatible avec OCI](https://www.opencontainers.org/). La plus grande différence de configuration entre les conteneurs Windows et Linux en cours d’exécution sur un ordinateur est que l’installation de IoT Edge inclut un runtime de conteneur Windows, mais vous devez fournir votre propre runtime pour les conteneurs Linux avant d’installer IoT Edge de Windows. 

Pour configurer un ordinateur Windows pour développer et tester des conteneurs pour les appareils de Linux, vous pouvez utiliser [Docker Desktop](https://www.docker.com/docker-windows) comme votre moteur de conteneur. Vous devez installer Docker et configurez-le pour qu’il [utilisent des conteneurs Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) avant d’installer IoT Edge.  

Si votre appareil Azure IoT Edge est un ordinateur Windows, vérifiez qu’il présente la [configuration requise](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) pour Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Installer IoT Edge sur un nouvel appareil

>[!NOTE]
>Les packages logiciels Azure IoT Edge sont soumis aux termes de contrat de licence situés dans les packages (dans le répertoire LICENSE). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package implique l’acceptation de ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

Un script PowerShell télécharge et installe le démon de sécurité Azure IoT Edge. Le démon de sécurité démarre ensuite le premier des deux modules de runtime, l’agent IoT Edge qui permet de déployer à distance d’autres modules. 

Lorsque vous installez le runtime IoT Edge pour la première fois sur un appareil, vous devez fournir à celui-ci une identité d’un hub IoT. Un seul appareil IoT Edge peut être configuré manuellement à l’aide d’une chaîne de connexion d’appareil fournie par votre hub IoT. Vous pouvez également utiliser le service Device Provisioning pour provisionner automatiquement des appareils, ce qui s’avère particulièrement utile lorsque vous devez configurer de nombreux appareils. 

Vous trouverez plus d’informations sur les différentes options d’installation et les paramètres dans l’article [installer le runtime Azure IoT Edge sur Windows](how-to-install-iot-edge-windows.md). Une fois que vous avez Desktop Docker installé et configuré pour des conteneurs Linux, la différence d’installation principal déclare Linux avec le **- ContainerOs** paramètre. Exemple : 

1. Si vous n’avez pas déjà fait, enregistrez un nouvel appareil IoT Edge et récupérer la chaîne de connexion de périphérique. Copiez la chaîne de connexion pour une utilisation ultérieure dans cette section. Vous pouvez effectuer cette étape en utilisant les outils suivants :

   * [Portail Azure](how-to-register-device-portal.md)
   * [Interface de ligne de commande Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Exécutez PowerShell ISE en tant qu’administrateur.

   >[!NOTE]
   >Utilisez une session AMD64 de PowerShell pour installer IoT Edge, pas PowerShell (x86). Si vous n’êtes pas sûr du type de session que vous utilisez, exécutez la commande suivante :
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Le **IoTEdge de déployer** commande vérifie que votre machine Windows se trouve sur une version prise en charge et Active la fonctionnalité de conteneurs et télécharge ensuite le runtime moby (qui n’est pas utilisé pour les conteneurs Linux) et le runtime IoT Edge. Les valeurs par défaut de la commande sur des conteneurs Windows, déclare donc Linux en tant que le système d’exploitation de conteneur souhaité. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. À ce stade, les appareils IoT Core peuvent redémarrer automatiquement. Autres appareils Windows 10 ou Windows Server peuvent vous inviter à redémarrer. Dans ce cas, redémarrez votre appareil maintenant. Une fois que votre appareil est prêt, exécutez PowerShell en tant qu’administrateur à nouveau.

5. La commande **Initialize-IoTEdge** configure le runtime IoT Edge sur votre ordinateur. Par défaut, la commande provisionnement manuel avec une chaîne de connexion de périphérique. Déclarer Linux en tant que le système d’exploitation de conteneur souhaité à nouveau. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Lorsque vous y êtes invité, fournissez la chaîne de connexion d’appareil que vous avez récupéré à l’étape 1. La chaîne de connexion d’appareil associe l’appareil physique avec un ID d’appareil dans IoT Hub. 

   La chaîne de connexion du périphérique prend le format suivant et ne doit pas inclure de guillemets : `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Vérifiez l’état du service IoT Edge. Il doit être répertorié comme en cours d’exécution.  

```powershell
Get-Service iotedge
```

Examinez les journaux d’activité du service des 5 dernières minutes. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Répertoriez les modules en cours d’exécution. Après une nouvelle installation, le module uniquement, vous devez voir est en cours d’exécution **edgeAgent**. Après avoir [déployer les modules IoT Edge](how-to-deploy-modules-portal.md), vous verrez d’autres. 

```powershell
iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous éprouvez des difficultés à installer correctement IoT Edge, voir la page [Résolution des problèmes](troubleshoot.md).

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).
