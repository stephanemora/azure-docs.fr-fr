---
title: Simuler Azure IoT Edge sur Windows | Microsoft Docs
description: Installer le runtime Azure IoT Edge sur un appareil simulé dans Windows et déployer votre premier module
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 06/08/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5bde54a65160c58d8bfba2f6c4c3b6a4317e46ed
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38540229"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Démarrage rapide : Déployer votre premier module IoT Edge à partir du portail Azure sur un appareil Windows - préversion

Azure IoT Edge vous permet d’analyser et de traiter les données sur vos appareils, au lieu d’envoyer (push) toutes les données dans le cloud. Les tutoriels IoT Edge décrivent comment déployer différents types de modules, mais vous devez d’abord disposer d’un appareil pour le test. 

Dans ce guide de démarrage rapide, vous apprenez à :

1. Création d’un IoT Hub
2. Enregistrer un appareil IoT Edge
3. Démarrer le runtime IoT Edge
4. Déployer un module

![Plan du didacticiel][2]

Le module que vous déployez dans ce guide de démarrage rapide est un capteur simulé qui génère des données de pression, d’humidité et de température. Les autres tutoriels Azure IoT Edge s’appuient sur le travail que vous effectuez ici en déployant des modules qui analysent les données simulées des informations métier. 

>[!NOTE]
>Le runtime IoT Edge sur Windows est en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si vous n’avez pas d'abonnement Azure actif, créez un [compte gratuit][lnk-account] avant de commencer.

## <a name="prerequisites"></a>Prérequis

Ce guide de démarrage rapide part du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows pour simuler un appareil IoT. Si vous exécutez Windows sur une machine virtuelle, activez la [virtualisation imbriquée][lnk-nested] et allouez au moins 2 Go de mémoire. 

Vérifiez que les prérequis suivants sont remplis sur l’ordinateur que vous utilisez pour un appareil IoT Edge :

1. Assurez-vous d’utiliser une version Windows prise en charge :
   * Windows 10 ou version ultérieure
   * Windows Server 2016 ou version ultérieure
2. Installez [Docker pour Windows][lnk-docker] et assurez-vous qu’il s’exécute correctement.
3. Configurer Docker pour utiliser des [conteneurs Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Commencez le guide de démarrage rapide en créant votre hub IoT dans le portail Azure.
![Créer un IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Enregistrer un appareil IoT Edge

Inscrivez l’appareil IoT Edge avec votre IoT Hub récemment créé.
![Inscrire un appareil][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Installer et démarrer le runtime IoT Edge

Installez et démarrez le runtime Azure IoT Edge sur votre appareil IoT Edge. 
![Inscrire un appareil][5]

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend trois composants. Le **démon de sécurité IoT Edge** démarre chaque fois qu’un appareil Edge démarre et amorce l’appareil en démarrant l’agent IoT Edge. **L’agent IoT Edge** facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge, notamment le hub IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub. 

>[!NOTE]
>Les étapes d’installation de cette section sont manuelles pour l’instant alors qu’un script d’installation est en cours de développement. 

Les instructions de cette section configurent le runtime IoT Edge avec les conteneurs Linux. Si vous souhaitez utiliser des conteneurs Windows, consultez [Installer le runtime Azure IoT Edge sur Windows à utiliser avec des conteneurs Windows](how-to-install-iot-edge-windows-with-windows.md).

### <a name="download-and-install-the-iot-edge-service"></a>Télécharger et installer le service IoT Edge

1. Sur votre appareil IoT Edge, exécutez PowerShell en tant qu’administrateur.

2. Téléchargez le package du service IoT Edge.

  ```powershell
  Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
  Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
  Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
  rmdir C:\ProgramData\iotedge\iotedged-windows
  $env:Path += ";C:\ProgramData\iotedge"
  SETX /M PATH "$env:Path"
  ```

3. Installer le vcruntime.

  ```powershell
  Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
  .\vc_redist.exe /quiet /norestart
  ```

4. Créez et démarrez le service IoT Edge.

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

5. Ajoutez des exceptions de pare-feu pour les ports utilisés par le service IoT Edge.

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

6. Créez un fichier appelé **iotedge.reg** et ouvrez-le dans un éditeur de texte. 

7. Ajoutez le contenu suivant et enregistrez le fichier. 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

8. Accédez à votre fichier dans l’Explorateur de fichiers et double-cliquez dessus pour importer les modifications dans le Registre Windows. 

### <a name="configure-the-iot-edge-runtime"></a>Configurer le runtime IoT Edge 

Configurez le runtime avec la chaîne de connexion de votre appareil IoT Edge que vous avez copiée lors de l’inscription d’un nouvel appareil. Ensuite, configurez le réseau du runtime. 

1. Ouvrez le fichier de configuration IoT Edge, qui se trouve dans `C:\ProgramData\iotedge\config.yaml`. Ce fichier étant protégé, exécutez un éditeur de texte tel que le Bloc-notes en tant qu’administrateur, puis utilisez l’éditeur pour ouvrir le fichier. 

2. Recherchez la section sur le **provisionnement** et remplacez la valeur de **device_connection_string** par la chaîne que vous avez copiée à partir des informations de votre appareil IoT Edge. 

3. Dans votre fenêtre PowerShell d’administrateur, récupérez le nom d’hôte de votre appareil IoT Edge et copiez la sortie. 

   ```powershell
   hostname
   ```

4. Dans le fichier de configuration, recherchez la section sur le **nom d’hôte de l’appareil Edge**. Remplacez la valeur de **hostname** par le nom d’hôte que vous avez copié à partir de PowerShell.

3. Dans votre fenêtre PowerShell d’administrateur, récupérez l’adresse IP de votre appareil IoT Edge. 

   ```powershell
   ipconfig
   ```

4. Copiez la valeur de **IPv4 Address** dans la section **vEthernet (DockerNAT)** de la sortie. 

5. Créez une variable d’environnement appelée **IOTEDGE_HOST**, en remplaçant *\<ip_address\>* par l’adresse IP de votre appareil IoT Edge. 

  ```powershell
  [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
  ```

  Conservez la variable d'environnement sur les redémarrages.

  ```powershell
  SETX /M IOTEDGE_HOST "http://<ip_address>:15580"
  ```

6. Dans le fichier `config.yaml`, recherchez la section sur les **paramètres de connexion**. Remplacez les valeurs de **management_uri** et **workload_uri** par votre adresse IP et les ports que vous avez ouverts dans la section précédente. Remplacez **\<GATEWAY_ADDRESS\>** par votre adresse IP. 

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

7. Recherchez la section sur les **paramètres d’écoute** et ajoutez les mêmes valeurs pour **management_uri** et **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

8. Recherchez la section **Paramètres de runtime de conteneur Moby**  et vérifiez que la valeur de **network** est définie sur `nat`.

9. Enregistrez le fichier de configuration. 

10. Dans PowerShell, redémarrez le service IoT Edge.

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

### <a name="view-the-iot-edge-runtime-status"></a>Afficher l’état du runtime IoT Edge

Vérifiez que le runtime a été correctement installé et configuré.

1. Vérifiez l’état du service IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Si vous avez besoin de résoudre les problèmes du service, récupérez les journaux de ce dernier. 

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
    -FilterHashtable @{ProviderName= "iotedged";
      LogName = "application"; StartTime = [datetime]::Today} |
    select TimeCreated, Message |
    sort-object @{Expression="TimeCreated";Descending=$false}
   ```

3. Affichez tous les modules s’exécutant sur votre appareil IoT Edge. Comme le service vient de démarrer pour la première fois, vous devez uniquement voir le module **edgeAgent** en cours d’exécution. Le module edgeAgent s’exécute par défaut et vous aide à installer et démarrer tous les modules supplémentaires que vous déployez sur votre appareil. 

   ```powershell
   iotedge list
   ```

   ![Afficher un module sur votre appareil](./media/quickstart/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Déployer un module

Gérez votre appareil Azure IoT Edge depuis le cloud pour déployer un module qui transmettra des données de télémétrie à IoT Hub.
![Inscrire un appareil][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Afficher les données générées

Dans ce guide de démarrage rapide, vous avez créé un nouveau périphérique IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour transmettre un module IoT Edge afin de l’exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même. Dans ce cas, le module que vous transmettez crée des données environnementales que vous pouvez utiliser pour les didacticiels. 

Vérifiez que le module déployé à partir du cloud est en cours d’exécution sur votre appareil IoT Edge. 

```powershell
iotedge list
```

   ![Afficher trois modules sur votre appareil](./media/quickstart/iotedge-list-2.png)

Affichez les messages envoyés du module tempSensor vers le cloud. 

```powershell
iotedge logs tempSensor -f
```

  ![Afficher les données dans votre module](./media/quickstart/iotedge-logs.png)

Vous pouvez également afficher les messages reçus par votre hub IoT à l’aide de [l’outil IoT Hub Explorer][lnk-iothub-explorer] ou [l’extension Azure IoT Toolkit pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez utiliser l’appareil simulé que vous avez configuré dans ce guide de démarrage rapide pour tester les tutoriels IoT Edge. Si vous souhaitez que le module tempSensor n’envoie plus de données à votre hub IoT, utilisez la commande suivante pour arrêter le service IoT Edge et supprimer les conteneurs qui ont été créés sur votre appareil. Quand vous souhaitez de nouveau utiliser votre ordinateur comme appareil IoT Edge, pensez à démarrer le service. 

   ```powershell
   Stop-Service iotedge -NoWait
   docker rm -f $(docker ps -aq)
   ```

Quand vous n’avez plus besoin du hub IoT que vous avez créé, vous pouvez utiliser le portail Azure pour supprimer la ressource et tous les appareils associés. Accédez à la page de présentation de votre hub IoT, puis sélectionnez **Supprimer**. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un appareil IoT Edge et utilisé l’interface cloud Azure IoT Edge pour déployer du code sur l’appareil. Vous possédez désormais un appareil de test générant des données brutes sur son environnement. 

Vous êtes prêt à passer à l’un des autres tutoriels pour savoir comment Azure IoT Edge peut vous aider à transformer ces données en informations métier « at the edge » (« en périphérie»).

> [!div class="nextstepaction"]
> [Filtrer les données de capteur à l’aide d’Azure Function](tutorial-deploy-function.md)

<!-- Images -->
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md
[lnk-account]: https://azure.microsoft.com/free
