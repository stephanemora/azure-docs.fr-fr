---
title: Démarrage rapide Azure IoT Edge + Linux | Microsoft Docs
description: Dans ce démarrage rapide, découvrez comment déployer à distance un code prédéfini sur un appareil IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 86bf28249321a705e8855de35121611b05009854
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063491"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil Linux x64

Azure IoT Edge s’empare du pouvoir du cloud et l’offre à vos appareils Internet des Objets. Dans ce démarrage rapide, découvrez comment utiliser l’interface de cloud pour déployer à distance un code prédéfini sur un appareil IoT Edge.

Dans ce guide de démarrage rapide, vous apprenez à :

1. Créez un IoT Hub.
2. Inscrivez un appareil IoT Edge dans votre IoT Hub.
3. Installez et démarrez le runtime IoT Edge sur votre appareil.
4. Déployez à distance un module vers un appareil IoT Edge.

![Architecture du démarrage rapide][2]

Ce démarrage rapide transforme votre machine virtuelle ou ordinateur Linux en appareil IoT Edge. Vous pouvez ensuite déployer un module depuis le portail Azure vers votre appareil. Le module que vous déployez dans ce démarrage rapide est un capteur simulé qui génère des données de pression, d’humidité et de température. Les autres didacticiels Azure IoT Edge s’appuient sur le travail que vous effectuez en déployant des modules qui analysent les données simulées des informations métier. 

Si vous n’avez pas d'abonnement Azure actif, créez un [compte gratuit][lnk-account] avant de commencer.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI vous permet d’effectuer plusieurs étapes de ce démarrage rapide, et Azure IoT dispose d’une extension permettant d’activer des fonctionnalités supplémentaires. 

Ajoutez l’extension Azure IoT à l’instance de Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Commencez le démarrage rapide en créant votre IoT Hub dans le portail Azure.
![Créer un IoT Hub][3]

Le niveau gratuit d'IoT Hub fonctionne pour ce démarrage rapide. Si vous avez utilisé IoT Hub par le passé et que vous avez créé gratuitement un hub, vous pouvez utiliser cet IoT Hub. Chaque abonnement peut avoir uniquement un IoT hub gratuit. 

1. Dans Azure Cloud Shell, créez un groupe de ressources. Le code suivant crée un groupe de ressources appelé **TestResources** dans la région **USA Ouest**. En plaçant toutes les ressources pour les démarrages rapides et les didacticiels dans un groupe, vous pouvez les gérer ensemble. 

   ```azurecli-interactive
   az group create --name TestResources --location westus
   ```

1. Créez un IoT hub dans votre nouveau groupe de ressources. Le code suivant crée un hub gratuit **F1** dans le groupe de ressources **TestResources**. Remplacez *{hub_name}* par un nom unique pour votre IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group TestResources --name {hub_name} --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Enregistrer un appareil IoT Edge

Inscrivez l’appareil IoT Edge avec votre IoT Hub récemment créé.
![Inscrire un appareil][4]

Créez une identité d’appareil pour votre appareil simulé afin qu’il puisse communiquer avec votre hub IoT. Étant donné que les appareils IoT Edge se comportent et peuvent être gérés différemment des appareils IoT standard, vous déclarez qu’il s’agit d’un appareil IoT Edge dès le départ. 

1. Dans Azure cloud shell, entrez la commande suivante pour créer un appareil nommé **myEdgeDevice** dans votre hub.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

1. Récupérez la chaîne de connexion pour votre appareil, qui lie votre appareil physique à l’aide de son identité dans IoT Hub. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. Copiez la chaîne de connexion et enregistrez-la. Vous utiliserez cette valeur pour configurer le runtime IoT Edge dans la section suivante. 


## <a name="install-and-start-the-iot-edge-runtime"></a>Installer et démarrer le runtime IoT Edge

Installez et démarrez le runtime Azure IoT Edge sur votre appareil. 
![Inscrire un appareil][5]

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend trois composants. Le **démon de sécurité IoT Edge** démarre chaque fois qu’un appareil Edge démarre et amorce l’appareil en démarrant l’agent IoT Edge. L’**agent IoT Edge** facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge, y compris le hub IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub. 

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

1. Téléchargez et installez le démon de sécurité IoT Edge. 

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
   iotedge list
   ```

   ![Afficher un module sur votre appareil](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Déployer un module

Gérez votre appareil Azure IoT Edge depuis le cloud pour déployer un module qui transmettra des données de télémétrie à IoT Hub.
![Inscrire un appareil][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Afficher les données générées

Dans ce guide de démarrage rapide, vous avez créé un nouveau périphérique IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour transmettre un module IoT Edge afin de l’exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même. Dans ce cas, le module que vous transmettez crée des données environnementales que vous pouvez utiliser pour les didacticiels. 

Rouvrez l’invite de commandes sur l’ordinateur qui exécute votre appareil simulé. Confirmez que le module déployé à partir du cloud est en cours d’exécution sur votre appareil IoT Edge :

   ```bash
   iotedge list
   ```

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

Lorsque vous n’avez plus besoin des ressources Azure que vous avez créées, vous pouvez utiliser la commande suivante pour supprimer le groupe de ressources créé et toutes les ressources qui y sont associées :

   ```azurecli-interactive
   az group delete --name TestResources
   ```

## <a name="next-steps"></a>Étapes suivantes

Ce démarrage rapide constitue une étape nécessaire pour suivre tous les didacticiels d’IoT Edge. Vous pouvez continuer avec l’un des autres didacticiels pour savoir comment Azure IoT Edge peut vous aider à transformer ces données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Filtrer les données de capteur à l’aide d’une fonction Azure](tutorial-deploy-function.md)



<!-- Images -->
[0]: ./media/quickstart-linux/cloud-shell.png
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
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
