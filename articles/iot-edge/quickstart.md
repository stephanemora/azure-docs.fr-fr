---
title: 'Démarrage rapide : Créer un appareil Azure IoT Edge sous Windows | Microsoft Docs'
description: Dans ce démarrage rapide, vous allez apprendre à créer un appareil IoT Edge et à déployer du code préconfiguré à distance à partir du portail Azure.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 71e38059aceb7da63f3545610b9acfe48c5d3150
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663211"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil Windows (préversion)

Essayez Azure IoT Edge dans ce guide de démarrage rapide en déployant du code conteneurisé sur un appareil IoT Edge Linux sur Windows. IoT Edge vous permet de gérer à distance du code sur vos appareils afin que vous puissiez envoyer plus de charges de travail à la périphérie. Pour ce guide de démarrage rapide, nous vous recommandons d’utiliser votre propre appareil pour voir combien il est facile d’utiliser Azure IoT Edge pour Linux sur Windows.

Dans ce guide de démarrage rapide, vous apprenez à :

* Créez un hub IoT.
* Inscrivez un appareil IoT Edge dans votre IoT Hub.
* Installer et démarrer le runtime IoT Edge pour Linux sur Windows sur votre appareil.
* Déployer à distance un module sur un appareil IoT Edge et envoyer des données de télémétrie.

![Diagramme - Démarrage rapide : architecture pour appareil et cloud](./media/quickstart/install-edge-full.png)

Ce guide de démarrage rapide vous guide tout au long de la configuration de votre appareil Azure IoT Edge pour Linux sur Windows. Ensuite, vous déployez un module depuis le portail Azure vers votre appareil. Le module utilisé dans ce guide de démarrage rapide est un capteur simulé qui génère des données de pression, d’humidité et de température. Les autres tutoriels Azure IoT Edge s’appuient sur le travail que vous effectuez ici en déployant des modules supplémentaires qui analysent les données simulées des insights métier.

Si vous n’avez pas d’abonnement Azure actif, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

>[!NOTE]
>IoT Edge pour Linux sur Windows est en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Préparez votre environnement pour l’interface Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Ressources cloud :

* Un groupe de ressources permettant de gérer toutes les ressources que vous utilisez lors de ce démarrage rapide.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Appareil IoT Edge :

* Votre appareil doit être un PC ou un serveur Windows, version 1809 ou ultérieure
* Au moins 4 Go de mémoire, 8 Go de mémoire recommandés
* 10 Go d'espace disque disponible

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Commencez le guide de démarrage rapide en créant un hub IoT avec Azure CLI.

![Diagramme - Créer un IoT Hub dans le cloud](./media/quickstart/create-iot-hub.png)

Le niveau gratuit d'IoT Hub fonctionne pour ce démarrage rapide. Si vous avez utilisé IoT Hub par le passé et que vous avez créé un hub, vous pouvez utiliser ce hub IoT.

Le code suivant crée un hub **F1** gratuit dans le groupe de ressources `IoTEdgeResources`. Remplacez `{hub_name}` par un nom unique pour votre hub IoT. La création d’un hub IoT peut prendre plusieurs minutes.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Si vous obtenez une erreur parce qu’un hub gratuit existe déjà dans votre abonnement, remplacez la référence SKU par **S1**. Si vous recevez une erreur indiquant que le nom du hub IoT n’est pas disponible, cela signifie que quelqu’un d’autre a déjà un hub portant ce nom. Essayez avec un autre nom.

## <a name="register-an-iot-edge-device"></a>Enregistrer un appareil IoT Edge

Inscrivez l’appareil IoT Edge avec votre IoT Hub récemment créé.

![Diagramme - Inscrire un appareil avec une identité IoT Hub](./media/quickstart/register-device.png)

Créez une identité d’appareil pour votre appareil simulé afin qu’il puisse communiquer avec votre IoT Hub. L’identité d’appareil se trouve dans le cloud, et une chaîne unique de connexion d’appareil vous permet d’associer un appareil physique à une identité d’appareil.

Étant donné que le comportement et la gestion des appareils IoT Edge peuvent être différents de ceux des appareils IoT standard, déclarez cette identité en indiquant qu’elle est destinée à un appareil IoT Edge avec l’indicateur `--edge-enabled`.

1. Dans Azure Cloud Shell, entrez la commande suivante pour créer un appareil nommé **myEdgeDevice** dans votre hub.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Si vous recevez une erreur relative aux clés de stratégie iothubowner, vérifiez que votre instance de Cloud Shell exécute la dernière version de l’extension azure-iot.

2. Affichez la chaîne de connexion pour votre appareil, qui lie votre appareil physique à l’aide de son identité dans IoT Hub. Elle contient le nom de votre hub IoT, le nom de votre appareil, puis une clé partagée qui authentifie les connexions entre les deux.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copiez la valeur de la clé `connectionString` à partir de la sortie JSON et enregistrez-la. Cette valeur est la chaîne de connexion de l’appareil. Vous l’utiliserez pour configurer le runtime IoT Edge dans la section suivante.

   ![Récupérer la chaîne de connexion à partir de la sortie CLI](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Installer et démarrer le runtime IoT Edge

Installez IoT Edge pour Linux sur Windows sur votre appareil, et configurez-le avec une chaîne de connexion d’appareil.

![Diagramme - Démarrer le runtime IoT Edge sur l’appareil](./media/quickstart/start-runtime.png)

1. [Téléchargez Windows Admin Center](https://aka.ms/WACDownloadEFLOW).

1. Suivez l’Assistant Installation pour configurer Windows Admin Center sur votre appareil.

1. Une fois dans Windows Admin Center, en haut à droite de l’écran, sélectionnez l’**icône d’engrenage de paramètres**.

1. Dans le menu des paramètres, sous Passerelle, sélectionnez **Extensions**.

1. Accédez à l’onglet **Flux** et sélectionnez **Ajouter**.

1. Entrez https://aka.ms/wac-insiders-feed dans la zone de texte et sélectionnez **Ajouter**.

1. Une fois le flux ajouté, accédez à l’onglet **Extensions disponibles**. La mise à jour de la liste des extensions peut prendre un certain temps.

1. Dans la liste des **Extensions disponibles** sélectionnez **Azure IoT Edge**.

1. **Installez** l’extension.

1. Une fois l’extension installée, accédez à la page principale du tableau de bord en sélectionnant **Windows Admin Center** dans l’angle supérieur gauche de votre écran.

1. Vous verrez la connexion d’hôte local représentant le PC sur lequel vous exécutez Windows Admin Center.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Capture d’écran - Page de démarrage de Windows Admin Center":::

1. Sélectionnez **Ajouter**.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Capture d’écran - Page de démarrage de Windows Admin Center - Bouton Ajouter":::

1. Recherchez la vignette Azure IoT Edge, puis sélectionnez **Créer nouveau**. L’Assistant Installation démarre.

    :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Capture d’écran - Vignette Azure IoT Edge pour Linux sur Windows":::

1. Suivez les étapes de l’Assistant Installation pour accepter le CLUF et choisissez **Suivant**

    :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Capture d’écran - Accueil de l’Assistant":::

1. Choisissez les **Données de diagnostic facultatives** pour fournir des données de diagnostic étendues qui aident Microsoft à superviser et à maintenir la qualité de service, puis cliquez sur **Suivant : Déployer**.

    :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Capture d’écran - Données de diagnostic":::

1. Dans l’écran **Sélectionner un appareil cible**, sélectionnez l’appareil cible souhaité pour vérifier qu’il respecte la configuration minimale requise. Pour ce guide de démarrage rapide, nous installons IoT Edge sur l’appareil local. Vous devez donc choisir la connexion localhost. Après confirmation, choisissez **Suivant** pour continuer.

    :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Capture d’écran - Sélectionner un appareil cible":::

1. Acceptez les paramètres par défaut en choisissant **Suivant**.

1. L’écran de déploiement montre le processus de téléchargement du package, l’installation du package, la configuration de l’hôte et la configuration finale de la machine virtuelle Linux.  Un déploiement réussi se présente comme suit :

    :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Capture d’écran - Assistant - Déploiement réussi":::

1. Cliquez sur **Suivant : Se connecter** pour passer à l’étape finale et provisionner votre appareil Azure IoT Edge avec son ID d’appareil à partir de votre instance IoT Hub.

1. Copiez la chaîne de connexion de votre appareil dans Azure IoT Hub et collez-la dans le champ de chaîne de connexion de l’appareil. Sélectionnez ensuite **Provisionnement avec la méthode sélectionnée**.

    > [!NOTE]
    > Reportez-vous à l’étape 3 de la section précédente, [Inscrire un appareil IoT Edge](#register-an-iot-edge-device), pour récupérer votre chaîne de connexion.

    :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Capture d’écran - Assistant - Provisionnement":::

1. Une fois le provisionnement terminé, sélectionnez **Terminer** pour revenir à l’écran d’accueil de Windows Admin Center. Vous devriez maintenant voir votre appareil listé en tant qu’appareil IoT Edge.

    :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Capture d’écran - Windows Admin Center - Appareil Azure IoT Edge":::

1. Sélectionnez votre appareil Azure IoT Edge pour afficher son tableau de bord. Vous devez constater que les charges de travail de votre jumeau d’appareil dans Azure IoT Hub ont été déployées. La **Liste des modules IoT Edge** doit indiquer qu’un module (**edgeAgent**) est en cours d’exécution, et l’**État de l’agent IoT Edge** doit indiquer **Actif (en cours d’exécution)** .

Votre appareil IoT Edge est maintenant configuré. Il est prêt à exécuter les modules déployés dans le cloud.

## <a name="deploy-a-module"></a>Déployer un module

Gérez votre appareil Azure IoT Edge depuis le cloud pour déployer un module qui transmet des données de télémétrie à IoT Hub.

![Diagramme - Déployer un module du cloud vers un appareil](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Afficher les données générées

Dans ce guide de démarrage rapide, vous avez créé un nouveau périphérique IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour déployer un module IoT Edge afin de l’exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même.

Dans ce cas, le module que vous avez envoyé (push) génère des exemples de données d’environnement que vous pouvez utiliser pour les tests ultérieurement. Le capteur simulé surveille à la fois une machine et l’environnement de la machine. Par exemple, ce capteur peut être installé dans une salle de serveurs, dans une usine ou sur une éolienne. Le message inclut la température et l’humidité ambiantes, la température et la pression de la machine ainsi qu’un horodatage. Les tutoriels IoT Edge se servent des données créées par ce module comme données de test pour l’analytique.

Vérifiez que le module déployé à partir du cloud est en cours d’exécution sur votre appareil IoT Edge en accédant à l’interface de commande dans Windows Admin Center.

1. Connectez-vous à votre nouvel appareil IoT Edge.

   :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Capture d’écran - Connexion à l’appareil":::

2. Dans la page **Vue d’ensemble** figurent la **Liste des modules IoT Edge** et l’**État de l’agent IoT Edge**, où vous pouvez voir les différents modules qui ont été déployés ainsi que l’état de l’appareil.  

3. Sous **Outils** sélectionnez **Interface de commande**. L’interface de commande est un terminal PowerShell qui utilise automatiquement SSH (Secure Shell) pour se connecter à la machine virtuelle Linux de votre appareil Azure IoT Edge sur votre PC Windows.

   :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Capture d’écran - Interface de commande":::

4. Pour vérifier les trois modules sur votre appareil, exécutez la **commande bash** suivante :

   ```bash
   sudo iotedge list
   ```

   :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Capture d’écran - Interface de commande - Liste":::

5. Consultez les messages envoyés du module de capteur de température vers le cloud.

   ```bash
   iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Les commandes IoT Edge respectent la casse quand vous référencez les noms de module.

   :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Capture d’écran - Capteur de température":::

Vous pouvez également regarder les messages arriver sur votre hub IoT avec l’[extension Azure IoT Hub pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez poursuivre les didacticiels IoT Edge, vous pouvez utiliser l’appareil que vous avez inscrit et configuré dans ce démarrage rapide. Sinon, vous pouvez supprimer les ressources Azure que vous avez créées dans cet article pour éviter les frais.

Si vous avez créé votre machine virtuelle et un IoT Hub dans un nouveau groupe de ressources, vous pouvez supprimer ce groupe et toutes les ressources associées. Vérifiez le contenu du groupe de ressources pour être certain que vous ne voulez rien en conserver. Si vous ne voulez pas supprimer tout le groupe, vous pouvez supprimer des ressources individuelles.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible.

Supprimez le groupe **IoTEdgeResources**. La suppression d’un groupe de ressources peut prendre plusieurs minutes.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Vous pouvez vérifier que le groupe de ressources est supprimé en consultant la liste des groupes de ressources.

```azurecli-interactive
az group list
```

### <a name="clean-removal-of-azure-iot-edge-for-linux-on-windows"></a>Suppression propre d’Azure IoT Edge pour Linux sur Windows

Vous pouvez désinstaller Azure IoT Edge pour Linux sur Windows à partir de votre appareil IoT Edge par le biais de l’extension du tableau de bord dans Windows Admin Center.

1. Connectez-vous à la connexion d’appareil Azure IoT Edge pour Linux sur Windows dans Windows Admin Center. L’extension de l’outil de tableau de bord Azure est chargée.
2. Sélectionner **Désinstaller**. Une fois Azure IoT Edge pour Linux sur Windows supprimé, Windows Admin Center accède à la page de démarrage et supprime l’entrée de la connexion de l’appareil Azure IoT Edge de la liste.

Une autre manière de supprimer Azure IoT Edge de votre système Windows consiste à accéder à **Démarrer** > **Paramètres** > **Applications** > **Azure IoT Edge** > **Désinstaller** sur votre appareil IoT Edge. Cette opération supprime Azure IoT Edge de votre appareil IoT Edge, mais conserve la connexion dans Windows Admin Center. Vous pouvez aussi désinstaller Windows Admin Center à partir du menu Paramètres.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un appareil IoT Edge et utilisé l’interface cloud Azure IoT Edge pour déployer du code sur l’appareil. Vous possédez désormais un appareil de test générant des données brutes sur son environnement.

L’étape suivante consiste à configurer votre environnement de développement local afin de pouvoir commencer à créer des modules IoT Edge qui exécutent votre logique métier.

> [!div class="nextstepaction"]
> [Commencer à développer des modules IoT Edge](tutorial-develop-for-linux.md)
