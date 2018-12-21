---
title: 'Démarrage rapide : Créer un appareil Azure IoT Edge sous Linux | Microsoft Docs'
description: Utilisez ce guide de démarrage rapide pour apprendre à créer un appareil IoT Edge et à déployer du code préconfiguré à distance à partir du portail Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/14/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 1bc7425d1979b2e1a35884c0800117455aebe9b6
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338054"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil Linux x64

Azure IoT Edge s’empare du pouvoir du cloud et l’offre à vos appareils Internet des Objets. Dans ce démarrage rapide, découvrez comment utiliser l’interface de cloud pour déployer à distance un code prédéfini sur un appareil IoT Edge.

Dans ce guide de démarrage rapide, vous apprenez à :

1. Créez un IoT Hub.
2. Inscrivez un appareil IoT Edge dans votre IoT Hub.
3. Installez et démarrez le runtime IoT Edge sur votre appareil.
4. Déployez à distance un module vers un appareil IoT Edge.

![Diagramme - Démarrage rapide : architecture pour appareil et cloud](./media/quickstart-linux/install-edge-full.png)

Ce démarrage rapide transforme votre machine virtuelle ou ordinateur Linux en appareil IoT Edge. Vous pouvez ensuite déployer un module depuis le portail Azure vers votre appareil. Le module que vous déployez dans ce démarrage rapide est un capteur simulé qui génère des données de pression, d’humidité et de température. Les autres tutoriels Azure IoT Edge s’appuient sur le travail que vous effectuez ici en déployant des modules qui analysent les données simulées des informations métier.

Si vous n’avez pas d’abonnement Azure actif, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

L’interface Azure CLI vous permet d’effectuer plusieurs étapes de ce démarrage rapide, et Azure IoT dispose d’une extension permettant d’activer des fonctionnalités supplémentaires. 

Ajoutez l’extension Azure IoT à l’instance de Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Prérequis

Ressources cloud : 

* Un groupe de ressources permettant de gérer toutes les ressources que vous utilisez lors de ce démarrage rapide. 

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Appareil IoT Edge :

* Un appareil ou une machine virtuelle Linux faisant office de périphérique IoT Edge. Nous vous recommandons d’utiliser la machine virtuelle [Azure IoT Edge sur Ubuntu](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) fournie par Microsoft, qui préinstallera le runtime IoT Edge. Créez cette machine virtuelle à l’aide de la commande suivante :

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   Quand vous créez une machine virtuelle, prenez note de **publicIpAddress**, qui est fourni dans la sortie de la commande create. Vous utiliserez cette adresse IP publique pour vous connecter à la machine virtuelle plus loin dans le guide de démarrage rapide.

* Si vous préférez exécuter le runtime Azure IoT Edge sur votre système local, suivez les instructions dans [Installer le runtime Azure IoT Edge sur Linux (x64)](how-to-install-iot-edge-linux.md).

* Si vous souhaitez utiliser un appareil ARM32 comme Raspberry Pi, suivez les instructions dans [Installer le runtime Azure IoT Edge sur Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Commencez le guide de démarrage rapide en créant votre hub IoT avec Azure CLI.

![Diagramme - Créer un hub IoT dans le cloud](./media/quickstart-linux/create-iot-hub.png)

Le niveau gratuit d'IoT Hub fonctionne pour ce démarrage rapide. Si vous avez utilisé IoT Hub par le passé et que vous avez créé gratuitement un hub, vous pouvez utiliser cet IoT Hub. Chaque abonnement peut avoir uniquement un IoT hub gratuit. 

Le code suivant crée un hub gratuit **F1** dans le groupe de ressources **IoTEdgeResources**. Remplacez *{hub_name}* par un nom unique pour votre IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 
   ```

   Si vous obtenez une erreur parce qu’un hub gratuit existe déjà dans votre abonnement, remplacez la référence SKU par **S1**. Si vous recevez une erreur indiquant que le nom du hub IoT n’est pas disponible, cela signifie que quelqu’un d’autre a déjà un hub portant ce nom. Essayez avec un autre nom. 

## <a name="register-an-iot-edge-device"></a>Enregistrer un appareil IoT Edge

Inscrivez l’appareil IoT Edge avec votre IoT Hub récemment créé.
![Diagramme - Inscrire un appareil avec une identité IoT Hub](./media/quickstart-linux/register-device.png)

Créez une identité d’appareil pour votre appareil simulé afin qu’il puisse communiquer avec votre IoT Hub. L’identité d’appareil se trouve dans le cloud, et une chaîne unique de connexion d’appareil vous permet d’associer un appareil physique à une identité d’appareil. 

Étant donné que le comportement et la gestion des appareils IoT Edge peuvent être différents de ceux des appareils IoT standard, déclarez cette identité en indiquant qu’elle est destinée à un appareil IoT Edge avec l’indicateur `--edge-enabled`. 

1. Dans Azure cloud shell, entrez la commande suivante pour créer un appareil nommé **myEdgeDevice** dans votre hub.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Si vous recevez une erreur relative aux clés de stratégie iothubowner, vérifiez que votre shell cloud exécute la dernière version de l’extension azure-cli-iot-ext. 

2. Récupérez la chaîne de connexion pour votre appareil, qui lie votre appareil physique à l’aide de son identité dans IoT Hub. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copiez la chaîne de connexion et enregistrez-la. Vous utiliserez cette valeur pour configurer le runtime IoT Edge dans la section suivante. 

## <a name="connect-the-iot-edge-device-to-iot-hub"></a>Connecter l’appareil IoT Edge à IoT Hub

Installez et démarrez le runtime Azure IoT Edge sur votre appareil IoT Edge. 
![Diagramme - Démarrer le runtime sur l'appareil](./media/quickstart-linux/start-runtime.png)

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend trois composants. Le **démon de sécurité IoT Edge** démarre chaque fois qu’un appareil Edge démarre et amorce l’appareil en démarrant l’agent IoT Edge. **L’agent IoT Edge** facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge, notamment le hub IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub. 

Pendant la configuration du runtime, vous fournissez une chaîne de connexion d’appareil. Utilisez la chaîne que vous avez récupérée à partir de Azure CLI. Cette chaîne associe votre appareil physique à l’identité d’appareil IoT Edge dans Azure. 

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Définir la chaîne de connexion sur l’appareil IoT Edge

* Si vous utilisez Azure IoT Edge sur une machine virtuelle Ubuntu, utilisez la chaîne de connexion copiée précédemment pour configurer à distance votre appareil IoT Edge :

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
   ```

   Pour les étapes restantes, récupérez l’adresse IP publique qui a été générée par la commande de création. Vous pouvez également trouver l’adresse IP publique dans la page Vue d’ensemble de la machine virtuelle dans le portail Azure. Utilisez la commande suivante pour vous connecter à votre machine virtuelle. Remplacez **{publicIpAddress}** par l’adresse de votre machine. 

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

* Si vous exécutez IoT Edge sur votre ordinateur local ou un appareil ARM32, ouvrez le fichier de configuration situé dans /etc/iotedge/config.yaml et mettez à jour la variable **device_connection_string** avec la valeur copiée précédemment, puis redémarrez le démon de sécurité IoT Edge pour appliquer vos modifications :

   ```bash
   sudo systemctl restart iotedge
   ```

>[!TIP]
>Vous avez besoin de privilèges élevés pour exécuter les commandes `iotedge`. Une fois que vous vous déconnectez de votre machine et que vous vous reconnectez pour la première fois après avoir installé le runtime IoT Edge, vos autorisations sont automatiquement mises à jour. Dans l’intervalle, utilisez **sudo** devant les commandes. 

### <a name="view-the-iot-edge-runtime-status"></a>Afficher l’état du runtime IoT Edge

Vérifiez que le runtime a été correctement installé et configuré.

1. Vérifiez que le démon de sécurité Edge s’exécute en tant que service système.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Voir le démon Edge s’exécuter en tant que service système](./media/quickstart-linux/iotedged-running.png)

2. Si vous avez besoin de résoudre les problèmes du service, récupérez les journaux de ce dernier. 

   ```bash
   journalctl -u iotedge
   ```

3. Affichez les modules en cours d’exécution sur votre appareil. 

   ```bash
   sudo iotedge list
   ```

   ![Afficher un module sur votre appareil](./media/quickstart-linux/iotedge-list-1.png)

Votre appareil IoT Edge est maintenant configuré. Il est prêt à exécuter les modules déployés dans le cloud. 

## <a name="deploy-a-module"></a>Déployer un module

Gérez votre appareil Azure IoT Edge depuis le cloud pour déployer un module qui transmettra des données de télémétrie à IoT Hub.
![Diagramme - Déployer un module du cloud vers un appareil](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Afficher les données générées

Dans ce guide de démarrage rapide, vous avez créé un nouveau périphérique IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour transmettre un module IoT Edge afin de l’exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même. Dans ce cas, le module que vous transmettez crée des données environnementales que vous pouvez utiliser pour les didacticiels.

Rouvrez l’invite de commandes sur votre appareil IoT Edge ou utilisez la connexion SSH à partir d’Azure CLI. Confirmez que le module déployé à partir du cloud est en cours d’exécution sur votre appareil IoT Edge :

   ```bash
   sudo iotedge list
   ```

   ![Afficher trois modules sur votre appareil](./media/quickstart-linux/iotedge-list-2.png)

Afficher les messages envoyés à partir du module tempSensor :

   ```bash
   sudo iotedge logs tempSensor -f
   ```

![Afficher les données dans votre module](./media/quickstart-linux/iotedge-logs.png)

Le module de capteur de température peut attendre de se connecter à Edge Hub si la dernière ligne que vous voyez dans le journal est `Using transport Mqtt_Tcp_Only`. Essayez d’arrêter le module et de laisser l’agent Edge le redémarrer. Vous pouvez l’arrêter avec la commande `sudo docker stop tempSensor`.

Vous pouvez également regarder les messages arriver sur votre hub IoT avec l'[extension Azure IoT Hub Toolkit pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anciennement Azure IoT Toolkit). 

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez poursuivre les didacticiels IoT Edge, vous pouvez utiliser l’appareil que vous avez inscrit et configuré dans ce démarrage rapide. Sinon, vous pouvez supprimer les ressources Azure que vous avez créées, ainsi que le runtime IoT Edge de votre appareil.

### <a name="delete-azure-resources"></a>Supprimer les ressources Azure

Si vous avez créé votre machine virtuelle et un IoT Hub dans un nouveau groupe de ressources, vous pouvez supprimer ce groupe et toutes les ressources associées. Vérifiez le contenu du groupe de ressources pour être certain que vous ne voulez rien en conserver. Si vous ne voulez pas supprimer tout le groupe, vous pouvez supprimer des ressources individuelles.

Supprimez le groupe **IoTEdgeResources**.

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```

### <a name="remove-the-iot-edge-runtime"></a>Supprimer le runtime IoT Edge

Si vous souhaitez supprimer les installations de votre appareil, utilisez les commandes suivantes.  

Supprimez le runtime IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Lorsque le runtime IoT Edge est supprimé, les conteneurs qu’il a créés sont arrêtés, mais existent toujours sur votre appareil. Affichez tous les conteneurs.

   ```bash
   sudo docker ps -a
   ```

Supprimez les conteneurs qui ont été créés sur votre appareil par le runtime IoT Edge. Modifiez le nom du conteneur tempSensor si vous l’avez appelé différemment. 

   ```bash
   sudo docker rm -f tempSensor
   sudo docker rm -f edgeHub
   sudo docker rm -f edgeAgent
   ```

Supprimez le runtime de conteneurs.

   ```bash
   sudo apt-get remove --purge moby-cli
   sudo apt-get remove --purge moby-engine
   ```

## <a name="next-steps"></a>Étapes suivantes

Ce démarrage rapide constitue une étape nécessaire pour suivre tous les didacticiels d’IoT Edge. Vous pouvez continuer avec l’un des autres didacticiels pour savoir comment Azure IoT Edge peut vous aider à transformer ces données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Filtrer les données de capteur à l’aide d’Azure Function](tutorial-deploy-function.md)
