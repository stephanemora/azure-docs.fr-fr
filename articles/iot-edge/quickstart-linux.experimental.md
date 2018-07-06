---
title: Simuler Azure IoT Edge sur Linux | Microsoft Docs
description: Dans ce démarrage rapide, découvrez comment déployer à distance un code prédéfini sur un appareil IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0e0d22b3363b00c81be5091fd12773f9e486c09e
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099183"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil Linux x64

Azure IoT Edge vous permet d’analyser et de traiter les données sur vos appareils, au lieu d’envoyer (push) toutes les données dans le cloud. Les tutoriels IoT Edge décrivent comment déployer différents types de modules, mais vous devez d’abord disposer d’un appareil pour le test. 

Dans ce guide de démarrage rapide, vous apprenez à :

1. Créez un IoT Hub.
2. Inscrivez un appareil IoT Edge dans votre IoT Hub.
3. Démarrez le runtime IoT Edge.
4. Déployez à distance un module vers un appareil IoT Edge.

![Plan du didacticiel][2]

Ce démarrage rapide transforme votre machine virtuelle ou ordinateur Linux en appareil IoT Edge. Vous pouvez ensuite déployer un module depuis le portail Azure vers votre appareil. Le module que vous déployez dans ce démarrage rapide est un capteur simulé qui génère des données de pression, d’humidité et de température. Les autres tutoriels Azure IoT Edge s’appuient sur le travail que vous effectuez en déployant des modules qui analysent les données simulées des informations métier. 

Si vous n’avez pas d'abonnement Azure actif, créez un [compte gratuit][lnk-account] avant de commencer.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Commencez le guide de démarrage rapide en créant votre hub IoT dans le portail Azure.
![Créer un IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Enregistrer un appareil IoT Edge

Inscrivez l’appareil IoT Edge avec votre IoT Hub récemment créé.
![Inscrire un appareil][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]


## <a name="install-and-start-the-iot-edge-runtime"></a>Installer et démarrer le runtime IoT Edge

Installez et démarrez le runtime Azure IoT Edge sur votre appareil. 
![Inscrire un appareil][5]

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend trois composants. Le **démon de sécurité IoT Edge** démarre chaque fois qu’un appareil Edge démarre et amorce l’appareil en démarrant l’agent IoT Edge. **L’agent IoT Edge** facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge, notamment le hub IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub. 

### <a name="register-your-device-to-use-the-software-repository"></a>Inscrire votre appareil pour utiliser le référentiels de logiciels

Les packages dont vous avez besoin pour exécuter le runtime IoT Edge sont gérés dans un référentiel de logiciels. Configurez votre appareil IoT Edge pour accéder à ce référentiel. 

Les étapes décrites dans cette section s’appliquent aux appareils exécutant **Ubuntu 16.04**. Pour accéder au référentiel de logiciels sur d’autres versions de Linux, consultez [Installer le runtime Azure IoT Edge sur Linux (x64)](how-to-install-iot-edge-linux.md) ou [Installer le runtime Azure IoT Edge sur Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

1. Installez la configuration du référentiel sur la machine que vous utilisez en tant qu’appareil IoT Edge.

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. Installez une clé publique pour accéder au référentiel.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Installer un runtime de conteneurs

Le runtime IoT Edge est un ensemble de conteneurs, et la logique que vous déployez sur votre appareil IoT Edge est empaquetée sous forme de conteneurs. Préparez votre appareil pour ces composants en installant un runtime de conteneurs.

Mettez à jour **apt-get**.

   ```bash
   sudo apt-get update
   ```

Installez Moby, un runtime de conteneurs, et ses commandes CLI. 

   ```bash
   sudo apt-get install moby-engine
   sudo apt-get install moby-cli   
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>Installer et configurer le démon de sécurité IoT Edge

Le démon de sécurité s’installe en tant que service système afin que le runtime IoT Edge démarre chaque fois que votre appareil démarre. L’installation inclut également une version de **hsmlib** qui autorise le démon de sécurité à interagir avec la sécurité matérielle de l’appareil. 

1. Télécharger et installer le démon de sécurité IoT Edge. 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. Ouvrez le fichier de configuration IoT Edge. Comme il s’agit d’un fichier protégé, vous devrez peut-être utiliser des privilèges élevés pour y accéder.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. Ajoutez la chaîne de connexion de l’appareil IoT Edge que vous avez copiée lors de l’inscription de votre appareil. Remplacez la valeur de la variable **device_connection_string** que vous avez copiée précédemment dans ce démarrage rapide.

4. Redémarrez le démon de sécurité Edge :

   ```bash
   sudo systemctl restart iotedge
   ```

5. Vérifiez que le démon de sécurité Edge s’exécute en tant que service système :

   ```bash
   sudo systemctl status iotedge
   ```

   ![Voir le démon Edge s’exécuter en tant que service système](./media/quickstart-linux/iotedged-running.png)

   Vous pouvez également voir les journaux du démon de sécurité Edge en exécutant la commande suivante :

   ```bash
   journalctl -u iotedge
   ```

6. Affichez les modules en cours d’exécution sur votre appareil : 

   ```bash
   sudo iotedge list
   ```
Après une fermeture de session et une ouverture de session, *sudo* n’est pas requis pour la commande ci-dessus.

   ![Afficher un module sur votre appareil](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Déployer un module

Gérez votre appareil Azure IoT Edge depuis le cloud pour déployer un module qui transmettra des données de télémétrie à IoT Hub.
![Inscrire un appareil][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Afficher les données générées

Dans ce guide de démarrage rapide, vous avez créé un nouveau périphérique IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour transmettre un module IoT Edge afin de l’exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même. Dans ce cas, le module que vous transmettez crée des données environnementales que vous pouvez utiliser pour les didacticiels. 

Rouvrez l’invite de commandes sur l’ordinateur qui exécute votre appareil simulé. Confirmez que le module déployé à partir du cloud est en cours d’exécution sur votre appareil IoT Edge :

   ```bash
   sudo iotedge list
   ```
Après une fermeture de session et une ouverture de session, *sudo* n’est pas requis pour la commande ci-dessus.

   ![Afficher trois modules sur votre appareil](./media/quickstart-linux/iotedge-list-2.png)

Afficher les messages envoyés à partir du module tempSensor :

  ```bash
   sudo iotedge logs tempSensor -f 
   ```

Après une fermeture de session et une ouverture de session, *sudo* n’est pas requis pour la commande ci-dessus.

![Afficher les données dans votre module](./media/quickstart-linux/iotedge-logs.png)

Le module de capteur de température peut attendre de se connecter à Edge Hub si la dernière ligne que vous voyez dans le journal est `Using transport Mqtt_Tcp_Only`. Essayez d’arrêter le module et de laisser l’agent Edge le redémarrer. Vous pouvez l’arrêter avec la commande `sudo docker stop tempSensor`.

Vous pouvez afficher la télémétrie envoyée par l’appareil à l’aide de l’[outil d’exploration IoT Hub][lnk-iothub-explorer] ou l’[extension Azure IoT Toolkit pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez poursuivre les didacticiels IoT Edge, vous pouvez utiliser l’appareil que vous avez inscrit et configuré dans ce démarrage rapide. Si vous souhaitez supprimer les installations de votre appareil, utilisez les commandes suivantes.  

Supprimez le runtime IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Supprimez les conteneurs qui ont été créés sur votre appareil. 

   ```bash
   sudo docker rm -f $(sudo docker ps -aq)
   ```

Supprimez le runtime de conteneurs.

   ```bash
   sudo apt-get remove --purge moby
   ```

Lorsque vous n’avez plus besoin du hub Azure IoT ou de l’appareil IoT Edge que vous avez créé dans ce démarrage rapide, vous pouvez les supprimer dans le portail Azure. Accédez à la page de présentation de votre hub IoT, puis sélectionnez **Supprimer**. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un appareil IoT Edge et utilisé l’interface cloud Azure IoT Edge pour déployer du code sur l’appareil. Vous possédez désormais un appareil simulé générant des données brutes sur son environnement. 

Ce démarrage rapide constitue une étape nécessaire pour suivre tous les didacticiels d’IoT Edge. Vous pouvez continuer avec l’un des tutoriels pour savoir comment Azure IoT Edge peut vous aider à transformer ces données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Filtrer les données de capteur à l’aide d’une fonction Azure](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
