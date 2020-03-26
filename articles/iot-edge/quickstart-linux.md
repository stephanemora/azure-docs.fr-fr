---
title: 'Démarrage rapide : Créer un appareil Azure IoT Edge sous Linux | Microsoft Docs'
description: Dans ce démarrage rapide, vous allez apprendre à créer un appareil IoT Edge et à déployer du code préconfiguré à distance à partir du portail Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 52258a8bc287df36158ec143e4aad74c34455ea4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80236085"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil virtuel Linux

Testez Azure IoT Edge dans ce démarrage rapide en déployant du code conteneurisé sur un appareil IoT Edge virtuel. IoT Edge vous permet de gérer à distance du code sur vos appareils afin que vous puissiez envoyer plus de charges de travail à la périphérie. Pour ce démarrage rapide, nous vous recommandons d’utiliser une machine virtuelle Azure pour votre appareil IoT Edge, ce qui vous permet de créer rapidement une machine de test avec tous les composants requis installés, puis de la supprimer une fois que vous avez terminé.

Dans ce guide de démarrage rapide, vous apprenez à :

1. Créez un IoT Hub.
2. Inscrivez un appareil IoT Edge dans votre IoT Hub.
3. Installez et démarrez le runtime IoT Edge sur votre appareil virtuel.
4. Déployez à distance un module vers un appareil IoT Edge.

![Diagramme - Démarrage rapide : architecture pour appareil et cloud](./media/quickstart-linux/install-edge-full.png)

Ce démarrage rapide vous explique comment créer une machine virtuelle Linux configurée pour être un appareil IoT Edge. Vous pouvez ensuite déployer un module depuis le portail Azure vers votre appareil. Le module que vous déployez dans ce guide de démarrage rapide est un capteur simulé qui génère des données de pression, d’humidité et de température. Les autres tutoriels Azure IoT Edge s’appuient sur le travail que vous effectuez ici en déployant des modules qui analysent les données simulées des informations métier.

Si vous n’avez pas d’abonnement Azure actif, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

L’interface Azure CLI vous permet d’effectuer plusieurs étapes de ce démarrage rapide, et Azure IoT dispose d’une extension permettant d’activer des fonctionnalités supplémentaires.

Ajoutez l’extension Azure IoT à l’instance de Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```
   
[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Prérequis

Ressources cloud :

* Un groupe de ressources permettant de gérer toutes les ressources que vous utilisez lors de ce démarrage rapide.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Appareil IoT Edge :

* Un appareil ou une machine virtuelle Linux faisant office de périphérique IoT Edge. Vous devez utiliser la machine virtuelle [Azure IoT Edge sur Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) fournie par Microsoft, qui préinstalle tout ce dont vous avez besoin pour exécuter IoT Edge sur un appareil. Acceptez les conditions d’utilisation et créez cette machine virtuelle à l’aide des commandes suivantes :

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   Quelques minutes peuvent être nécessaires pour créer et démarrer la nouvelle machine virtuelle.

   Quand vous créez une machine virtuelle, prenez note de **publicIpAddress**, qui est fourni dans la sortie de la commande create. Vous utiliserez cette adresse IP publique pour vous connecter à la machine virtuelle plus loin dans le guide de démarrage rapide.

* Si vous préférez exécuter le runtime Azure IoT Edge sur votre propre appareil, suivez les instructions fournies dans [Installer le runtime Azure IoT Edge sur Linux](how-to-install-iot-edge-linux.md).

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Commencez le guide de démarrage rapide en créant un hub IoT avec Azure CLI.

![Diagramme - Créer un IoT Hub dans le cloud](./media/quickstart-linux/create-iot-hub.png)

Le niveau gratuit d'IoT Hub fonctionne pour ce démarrage rapide. Si vous avez utilisé IoT Hub par le passé et que vous avez créé gratuitement un hub, vous pouvez utiliser cet IoT Hub. Chaque abonnement peut avoir uniquement un IoT hub gratuit.

Le code suivant crée un hub gratuit **F1** dans le groupe de ressources **IoTEdgeResources**. Remplacez `{hub_name}` par un nom unique pour votre hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Si vous obtenez une erreur parce qu’un hub gratuit existe déjà dans votre abonnement, remplacez la référence SKU par **S1**. Si vous recevez une erreur indiquant que le nom du hub IoT n’est pas disponible, cela signifie que quelqu’un d’autre a déjà un hub portant ce nom. Essayez avec un autre nom.

## <a name="register-an-iot-edge-device"></a>Enregistrer un appareil IoT Edge

Inscrivez l’appareil IoT Edge avec votre IoT Hub récemment créé.

![Diagramme - Inscrire un appareil avec une identité IoT Hub](./media/quickstart-linux/register-device.png)

Créez une identité d’appareil pour votre appareil IoT Edge afin qu’il puisse communiquer avec votre IoT Hub. L’identité d’appareil se trouve dans le cloud, et une chaîne unique de connexion d’appareil vous permet d’associer un appareil physique à une identité d’appareil.

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

3. Copiez la valeur de la clé `connectionString` à partir de la sortie JSON et enregistrez-la. Cette valeur est la chaîne de connexion de l’appareil. Vous l’utiliserez pour configurer le runtime IoT Edge dans la section suivante.

   ![Récupérer la chaîne de connexion à partir de la sortie CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Configurer votre appareil IoT Edge

Démarrer le runtime Azure IoT Edge sur votre appareil IoT Edge

![Diagramme - Démarrer le runtime sur l’appareil](./media/quickstart-linux/start-runtime.png)

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend trois composants. Le **démon de sécurité IoT Edge** démarre chaque fois qu’un appareil IoT Edge démarre et amorce l’appareil en démarrant l’agent IoT Edge. **L’agent IoT Edge** facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge, notamment le hub IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub.

Pendant la configuration du runtime, vous fournissez une chaîne de connexion d’appareil. Utilisez la chaîne que vous avez récupérée à partir de Azure CLI. Cette chaîne associe votre appareil physique à l’identité d’appareil IoT Edge dans Azure.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Définir la chaîne de connexion sur l’appareil IoT Edge

Si vous utilisez la machine virtuelle Azure IoT Edge sur Ubuntu comme indiqué dans les prérequis, le runtime IoT Edge est déjà installé sur votre appareil. Il vous suffit simplement de configurer votre appareil avec la chaîne de connexion d’appareil que vous avez récupérée dans la section précédente. Vous pouvez procéder à distance sans avoir à vous connecter à la machine virtuelle. Exécutez la commande suivante, en remplaçant `{device_connection_string}` par votre propre chaîne.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Si vous exécutez IoT Edge sur votre ordinateur local ou sur un appareil ARM32 ou ARM64, vous devez installer le runtime IoT Edge et ses prérequis sur votre appareil. Suivez les instructions fournies dans [Installer le runtime Azure IoT Edge sur Linux](how-to-install-iot-edge-linux.md), puis revenez à ce guide de démarrage rapide.

### <a name="view-the-iot-edge-runtime-status"></a>Afficher l’état du runtime IoT Edge

Les autres commandes de ce guide sont exécutées sur votre appareil IoT Edge lui-même, afin que vous puissiez voir ce qui s’y passe concrètement. Si vous utilisez une machine virtuelle, connectez-vous à cet ordinateur dès maintenant en utilisant l’adresse IP publique générée par la commande de création. Vous pouvez également trouver l’adresse IP publique dans la page Vue d’ensemble de la machine virtuelle dans le portail Azure. Utilisez la commande suivante pour vous connecter à votre machine virtuelle. Remplacez `{azureuser}` si vous avez utilisé un nom d’utilisateur autre que celui suggéré dans les prérequis. Remplacez `{publicIpAddress}` par l’adresse de votre machine.

   ```console
   ssh azureuser@{publicIpAddress}
   ```

Vérifiez que le runtime a été correctement installé et configuré sur votre appareil IoT Edge.

>[!TIP]
>Vous avez besoin de privilèges élevés pour exécuter les commandes `iotedge`. Une fois que vous vous déconnectez de votre machine et que vous vous reconnectez pour la première fois après avoir installé le runtime IoT Edge, vos autorisations sont automatiquement mises à jour. Dans l’intervalle, ajoutez `sudo` devant les commandes.

1. Vérifiez que le démon de sécurité IoT Edge s’exécute en tant que service système.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Voir le démon IoT Edge s’exécuter en tant que service système](./media/quickstart-linux/iotedged-running.png)

2. Si vous avez besoin de résoudre les problèmes du service, récupérez les journaux d’activité de ce dernier.

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

Dans ce guide de démarrage rapide, vous avez créé un nouveau périphérique IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour déployer un module IoT Edge afin de l’exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même.

Dans ce cas, le module que vous avez envoyé (push) crée des exemples de données que vous pouvez utiliser pour les tests. Le module du capteur de température simulé génère des données d’environnement que vous pouvez utiliser plus tard pour les tests. Le capteur simulé surveille à la fois une machine et l’environnement de la machine. Par exemple, ce capteur peut être installé dans une salle de serveurs, dans une usine ou sur une éolienne. Le message inclut la température et l’humidité ambiantes, la température et la pression de la machine ainsi qu’un horodatage. Les tutoriels IoT Edge se servent des données créées par ce module comme données de test pour l’analytique.

Rouvrez l’invite de commandes sur votre appareil IoT Edge ou utilisez la connexion SSH à partir d’Azure CLI. Confirmez que le module déployé à partir du cloud est en cours d’exécution sur votre appareil IoT Edge :

   ```bash
   sudo iotedge list
   ```

   ![Afficher trois modules sur votre appareil](./media/quickstart-linux/iotedge-list-2.png)

Consultez les messages envoyés par le module de capteur de température :

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Les commandes IoT Edge respectent la casse quand vous référencez les noms de module.

   ![Afficher les données dans votre module](./media/quickstart-linux/iotedge-logs.png)

Vous pouvez également regarder les messages arriver sur votre hub IoT avec l’[extension Azure IoT Hub pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez poursuivre les didacticiels IoT Edge, vous pouvez utiliser l’appareil que vous avez inscrit et configuré dans ce démarrage rapide. Sinon, vous pouvez supprimer les ressources Azure que vous avez créées dans cet article pour éviter les frais.

Si vous avez créé votre machine virtuelle et un IoT Hub dans un nouveau groupe de ressources, vous pouvez supprimer ce groupe et toutes les ressources associées. Vérifiez le contenu du groupe de ressources pour être certain que vous ne voulez rien en conserver. Si vous ne voulez pas supprimer tout le groupe, vous pouvez supprimer des ressources individuelles.

Supprimez le groupe **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un appareil IoT Edge et utilisé l’interface cloud Azure IoT Edge pour déployer du code sur l’appareil. Vous possédez désormais un appareil de test générant des données brutes sur son environnement.

L’étape suivante consiste à configurer votre environnement de développement local afin de pouvoir commencer à créer des modules IoT Edge qui exécutent votre logique métier.

> [!div class="nextstepaction"]
> [Commencer à développer des modules IoT Edge pour des appareils Linux](tutorial-develop-for-linux.md)
