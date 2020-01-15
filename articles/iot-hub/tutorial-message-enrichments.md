---
title: Didacticiel - Utilisation des enrichissements de messages Azure IoT Hub
description: Didacticiel présentant comment utiliser des enrichissements de messages Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: 323730fff4659c87058669016b69808a880994cf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453885"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments"></a>Tutoriel : Utilisation des enrichissements de messages Azure IoT Hub

Les *enrichissements de messages* sont la capacité du IoT Hub à *horodater* des messages avec des informations supplémentaires avant que les messages ne soient envoyés au point de terminaison désigné. Vous pouvez utiliser les enrichissements de messages pour inclure des données permettant de simplifier le traitement en aval. Par exemple, enrichir des messages de télémétrie d’appareil avec une balise de jumeau d’appareil peut réduire la charge de clients en leur évitant d’effectuer des appels d’API doubles pour cette information. Pour plus d’informations, consultez [Vue d’ensemble des enrichissements de messages](iot-hub-message-enrichments-overview.md).

Dans ce didacticiel, vous voyez deux façons de créer et de configurer les ressources nécessaires pour tester les enrichissements de message pour un IoT Hub. Les ressources incluent un compte de stockage avec deux conteneurs de stockage : l’un pour contenir les messages enrichis et l’autre pour contenir les messages d’origine. Un IoT Hub permet également de recevoir les messages et de les acheminer vers le conteneur de stockage approprié, selon qu’ils sont enrichis ou non. 

* La première méthode consiste à utiliser la Azure CLI pour créer les ressources et configurer le routage des messages. Vous définissez ensuite les enrichissements manuellement à l’aide du [Portail Azure](https://portal.azure.com). 

* La deuxième méthode consiste à utiliser un modèle de Azure Resource Manager pour créer les ressources *et* les configurations pour le routage des messages et les enrichissements de messages. 

Une fois que les configurations du routage des messages et des enrichissements du message sont terminées, vous utilisez une application pour envoyer des messages au IoT Hub, qui les achemine ensuite vers les deux conteneurs de stockage. Seuls les messages envoyés au point de terminaison correspondant au conteneur de stockage **enrichi** sont enrichis.

Dans le cadre de ce tutoriel, vous allez effectuer les tâches suivantes :

**Utilisation des enrichissements de messages Azure IoT Hub**
> [!div class="checklist"]
> * Première méthode : enrichissements manuels des messages
>   - Créez des ressources et configurer le routage des messages à l’aide de l’Azure CLI.
>   - Configurez manuellement les enrichissements de message à l’aide du [Portail Azure](https://portal.azure.com).
> * Deuxième méthode : utilisation d’un modèle RM
>   - Créez des ressources, configurez le routage des messages et les enrichissements de messages à l’aide d’un modèle de Azure Resource Manager. 
> * Exécutez une application qui simule l’envoi de messages par un appareil IoT au hub.
> * Afficher les résultats et vérifiez que les enrichissements de messages fonctionnent comme prévu.

## <a name="prerequisites"></a>Conditions préalables requises

* Vous devez avoir un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Installer [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Récupérer le référentiel d’exemples C# IOT

Téléchargez les [exemples C# IOT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) à partir de GitHub et décompressez-les. Ce référentiel contient plusieurs applications, scripts et modèles Resource Manager. Voici ceux que vous devez utiliser dans le cadre de ce tutoriel :

* Pour la méthode manuelle, il existe un script CLI pour créer les ressources. Ce script se trouve dans **/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli**. Ce script crée les ressources et configure le routage des messages. Après l’exécution du script, vous créez manuellement les enrichissements de message à l’aide du[Portail Azure](https://portal.azure.com) puis exécutez l’application DeviceSimulation pour voir les enrichissements opérationnels.

* Pour la méthode automatisée, il existe un modèle Azure Resource Manager. Le modèle se trouve dans **/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json**. Ce modèle crée les ressources, configure le routage des messages et, enfin, configure les enrichissements du message. Après avoir chargé ce modèle, vous exécutez l’application Device Simulation pour voir les enrichissements en action.

* La troisième application que vous utilisez est l’application Device Simulation, que vous utilisez pour envoyer des messages à IoT Hub et tester les enrichissements de message.

## <a name="manual-set-up-and-configuration-using-azure-cli"></a>Configuration manuelle et configuration à l’aide d’Azure CLI

En plus de créer les ressources nécessaires, le script Azure CLI configure les deux itinéraires menant aux points de terminaison et correspondant aux deux conteneurs de stockage distincts. Pour plus d’informations sur la configuration du routage des messages, consultez le [tutoriel sur le routage](tutorial-routing.md). Une fois les ressources configurées, vous utilisez le [portail Azure](https://portal.azure.com) pour configurer les enrichissements de messages pour chaque point de terminaison, puis passez à l’étape de test.

> [!NOTE]
> Tous les messages sont acheminés vers les deux points de terminaison, mais seuls les messages acheminés vers le point de terminaison avec les enrichissements de messages configurés seront enrichis.
>

Vous pouvez utiliser le script ci-dessous ou ouvrir le script dans le dossier /resources du référentiel téléchargé. Voici les étapes que suivra le script :

* Créez un IoT Hub.
* Créez un compte de stockage.
* Création de deux conteneurs dans le compte de stockage : le premier pour les messages enrichis et le second pour les messages non enrichis.
* Configuration du routage pour les deux comptes de stockage distincts.
    * Création d'un point de terminaison pour chaque conteneur de compte de stockage.
    * Création d'un itinéraire menant à chaque point de terminaison de conteneur de compte de stockage.

Plusieurs noms de ressources doivent être globalement uniques, comme le nom du hub IoT et le nom du compte de stockage. Pour faciliter l'exécution du script, une valeur alphanumérique aléatoire appelée *randomValue* est ajoutée à ces noms de ressources. La valeur randomValue est générée une fois au début du script, puis ajoutée aux noms de ressources tout au long du script en fonction des besoins. Si vous ne souhaitez pas que cette valeur soit aléatoire, vous pouvez la définir sur une chaîne vide ou sur une valeur spécifique.

Si vous ne l'avez pas déjà fait, ouvrez une [fenêtre Cloud Shell](https://shell.azure.com) et vérifiez qu’elle est définie sur Bash. Ouvrez le script dans le référentiel décompressé, utilisez Ctrl+A pour le sélectionner, puis Ctrl-C pour le copier. Vous pouvez également copier le script CLI suivant ou l'ouvrir directement dans Cloud Shell. Collez le script dans la fenêtre Cloud Shell en cliquant avec le bouton droit sur la ligne de commande et en sélectionnant **Coller**. Le script est exécuté, une instruction à la fois. Lorsque le script s'arrête, sélectionnez **Entrée** pour vous assurer qu’il exécute la dernière commande. Le bloc de code suivant illustre le script utilisé, avec des commentaires expliquant son utilité.

Voici les ressources créées par le script. **Enriched** indique que la ressource correspond aux messages avec enrichissements. **Original** indique que la ressource correspondant aux messages non enrichis.

| Name | Valeur |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nom du conteneur | ressource d’origine  |
| nom du conteneur | ressource enrichie  |
| nom de l’appareil IoT | Contoso-Test-Device |
| Nom de l’IoT Hub | ContosoTestHubMsgEn |
| Nom du compte de stockage | contosostorage |
| Nom du point de terminaison 1 | ContosoStorageEndpointOriginal |
| Nom du point de terminaison 2 | ContosoStorageEndpointEnriched|
| Nom de l’itinéraire 1 | ContosoStorageRouteOriginal |
| Nom de l’itinéraire 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

À ce stade, les ressources sont toutes paramétrées et le routage des messages est configuré. Vous pouvez afficher la configuration du routage des messages dans le portail et configurer les enrichissements de messages pour les messages transférés vers le conteneur de stockage **enriched**.

### <a name="manually-configure-the-message-enrichments-using-the-azure-portal"></a>Configurez manuellement les enrichissements de message à l’aide du Portail Azure

1. Accédez à votre hub IoT en sélectionnant **Groupes de ressources**, puis sélectionnez le groupe de ressources configuré pour ce tutoriel (**ContosoResources_MsgEn**). Recherchez le hub IoT dans la liste et sélectionnez-le. Sélectionnez **Routage des messages** pour le hub IoT.

   ![Sélectionner le routage des messages](./media/tutorial-message-enrichments/select-iot-hub.png)

   Le volet de routage des messages contient trois onglets -- **Itinéraires**, **Points de terminaison personnalisés**, et **Enrichir les messages**. Vous pouvez parcourir les deux premiers onglets pour afficher la configuration définie par le script. Utilisez le troisième onglet pour ajouter des enrichissements de messages. Nous allons enrichir les messages acheminés vers le point de terminaison correspondant au conteneur de stockage nommé **enriched**. Entrez le nom et la valeur, puis sélectionnez le point de terminaison **ContosoStorageEndpointEnriched** dans la liste déroulante. Voici un exemple de configuration d’un enrichissement ajoutant le nom du hub IoT au message :

   ![Ajouter le premier enrichissement](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Ajoutez ces valeurs à la liste correspondant au point de terminaison ContosoStorageEndpointEnriched.

   | Clé | Valeur | Point de terminaison (liste déroulante) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Si votre appareil n’a pas de jumeau, la valeur que vous entrez ici est estampillée en tant que chaîne pour la valeur des enrichissements de messages. Pour afficher des informations relatives au jumeau d'appareil, accédez à votre hub dans le portail, puis sélectionnez **Appareils IoT**, sélectionnez votre appareil, puis **Jumeau d’appareil** en haut de la page.
   >
   > Vous pouvez modifier les informations relatives au jumeau pour ajouter des balises (emplacement, par exemple) et lui attribuer une valeur spécifique. Pour en savoir plus, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](iot-hub-devguide-device-twins.md)

3. Lorsque vous avez terminé, votre volet doit ressembler à l'image ci-dessous :

   ![Table avec tous les enrichissements ajoutés](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Sélectionnez **Appliquer** pour enregistrer les modifications. Passez à la section [Test des enrichissements de message](#testing-message-enrichments).

## <a name="use-an-rm-template-to-create-and-configure-the-resources-message-routing-and-message-enrichments"></a>Utilisez un modèle RM pour créer et configurer les ressources, le routage des messages et les enrichissements de messages 

1. Connectez-vous au portail Azure. Cliquez sur **+ Créer une ressource**. La zone de recherche s’affiche. Recherchez **Déploiement de modèle**. Dans le volet de résultats, sélectionnez **Template deployment (déployer à l’aide d’un modèle personnalisé)** .

   ![Template deployment dans le Portail Azure](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Sélectionnez **Créer** dans le volet Template deployment. 

1. Dans le volet déploiement personnalisé. Sélectionnez **« Créer votre propre modèle dans l’éditeur »** .

1. Dans le volet Modifier le modèle, sélectionnez **Charger le fichier**. L’Explorateur Windows s’affiche. Localisez le fichier **template_messageenrichments.json** dans le fichier référentiel décompressé dans **/iot-hub/Tutorials/Routing/SimulatedDevice/resources**. 

   ![Sélectionner un modèle à partir d’un ordinateur local](./media/tutorial-message-enrichments/template-select.png)

1. Sélectionnez **Ouvrir** pour charger le fichier de modèle à partir de l’ordinateur local. Il est chargé dans le volet d’édition et s’affiche.

   Ce modèle est configuré pour utiliser un nom de IoT Hub global unique et un nom de compte de stockage en ajoutant une valeur aléatoire à la fin des noms par défaut, afin que vous puissiez utiliser le modèle sans y apporter de modifications. 

   Voici les ressources créées par le chargement du modèle. **Enriched** indique que la ressource correspond aux messages avec enrichissements. **Original** indique que la ressource correspondant aux messages non enrichis. Il s’agit des mêmes valeurs que celles utilisées dans le script Azure CLI.

   | Name | Valeur |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nom du conteneur | ressource d’origine  |
   | nom du conteneur | ressource enrichie  |
   | nom de l’appareil IoT | Contoso-Test-Device |
   | Nom de l’IoT Hub | ContosoTestHubMsgEn |
   | Nom du compte de stockage | contosostorage |
   | Nom du point de terminaison 1 | ContosoStorageEndpointOriginal |
   | Nom du point de terminaison 2 | ContosoStorageEndpointEnriched|
   | Nom de l’itinéraire 1 | ContosoStorageRouteOriginal |
   | Nom de l’itinéraire 2 | ContosoStorageRouteEnriched |

1. Sélectionnez **Enregistrer**et le volet Déploiement personnalisé s’affiche, en affichant tous les paramètres utilisés par le modèle. Le seul champ que vous devez définir est le **groupe de ressources**. Vous pouvez en créer un nouveau ou en sélectionner un dans la liste déroulante.

   Voici la moitié supérieure du volet de déploiement personnalisé. Vous pouvez voir où vous remplissez le groupe de ressources.

   ![Moitié supérieure du volet de déploiement personnalisé](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Voici la moitié inférieure du volet de déploiement personnalisé. Vous pouvez voir le reste des paramètres, ainsi que les conditions générales. 

   ![Moitié inférieure du volet de déploiement personnalisé](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Activez la case à cocher indiquant que vous acceptez les conditions générales, puis sélectionnez **Acheter** pour poursuivre le déploiement du modèle.

1. Attendez que le modèle soit complètement déployé. Vous pouvez sélectionner l’icône représentant une cloche en haut de l’écran pour vérifier la progression. Une fois l’opération terminée, vous pouvez continuer vers [Test des enrichissements de messages](#testing-message-enrichments).

## <a name="testing-message-enrichments"></a>Test des enrichissements de messages

Vous pouvez afficher les enrichissements de message en sélectionnant **Groupes de ressources**, puis sélectionner le groupe de ressources que vous utilisez pour ce didacticiel. Sélectionnez ensuite la IoT Hub dans la liste des ressources, puis accédez à **Messagerie**. Laa configuration du routage des messages et les enrichissements configurés seront affichés.

Maintenant que les enrichissements de messages sont configurés pour le point de terminaison, exécutez l’application Appareil simulé pour envoyer des messages au hub IoT. Le hub a été configuré avec des paramètres assurant les opérations suivantes :

* Les messages acheminés vers le point de terminaison de stockage ContosoStorageEndpointOriginal ne seront pas enrichis et seront stockés dans le conteneur de stockage `original`.

* Les messages acheminés vers le point de terminaison de stockage ContosoStorageEndpointEnriched seront enrichis et stockés dans le conteneur de stockage `enriched`.

L’application Appareil simulé est une des applications du téléchargement décompressé. L’application envoie des messages pour chaque méthode de routage de messages différente du [tutoriel sur le routage](tutorial-routing.md), ce qui inclut Stockage Azure.

Double-cliquez sur le fichier de solution (IoT_SimulatedDevice.sln) pour ouvrir le code dans Visual Studio, puis ouvrez Program.cs. Remplacez le marqueur `{your hub name}` par le nom du hub IoT. Le format du nom d’hôte du hub IoT est **{nom de votre hub}.azure-devices.net**. Pour ce didacticiel, le nom d’hôte du hub est **ContosoTestHubMsgEn.azure-devices.net**. Remplacez ensuite la clé de l'appareil que vous avez enregistré précédemment, lors de l'exécution du script afin de créer les ressources pour le marqueur `{your device key}`.

Si vous ne disposez pas de la clé de l'appareil, vous pouvez la récupérer à partir du portail. Une fois connecté, accédez à **Groupes de ressources**, sélectionnez votre groupe de ressources, puis votre hub IoT. Sous **Appareils IoT**, recherchez votre appareil de test, puis sélectionnez-le. Sélectionnez l’icône de copie en regard de **Clé primaire** pour la copier dans le Presse-papiers.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Exécuter et tester

Exécutez l’application console pendant quelques minutes. Les messages envoyés s'affichent sur l’écran de la console de l’application.

L’application envoie un nouveau message appareil-à-cloud à l’IoT Hub toutes les secondes. Le message contient un objet sérialisé JSON avec l’ID d’appareil, la température, l’humidité et le niveau du message, qui est défini par défaut sur `normal`. Un niveau de `critical` ou `storage` est attribué de façon aléatoire, ce qui permet au message d’être acheminé vers le compte de stockage ou le point de terminaison par défaut. Les messages envoyés au conteneur **enriched** du compte de stockage seront enrichis.

Une fois plusieurs messages de stockage envoyés, affichez les données.

1. Sélectionnez **Groupes de ressources**, puis recherchez votre groupe de ressources (ContosoResourcesMsgEn) et sélectionnez-le.

2. Sélectionnez votre compte de stockage (contosostorage). Sélectionnez ensuite **Explorateur de stockage (préversion)** à partir du volet de sélection de gauche.

   ![Sélectionner l'explorateur de stockage](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Sélectionnez **CONTENEURS D'OBJETS BLOB** pour afficher les deux conteneurs pouvant être utilisés.

   ![Voir les conteneurs dans le compte de stockage](./media/tutorial-message-enrichments/show-blob-containers.png)

Les messages du conteneur nommé **enriched** bénéficieront des enrichissements de messages. Les messages du conteneur nommé **original** seront bruts, sans enrichissements. Explorez un des conteneurs au niveau de détail et ouvrez le fichier de message le plus récent, puis faites-en de même pour l'autre conteneur afin de vérifier qu'aucun enrichissement n'a été ajouté aux messages de ce conteneur.

Lorsque vous examinez les messages enrichis, vous devez voir s'afficher « mon hub IoT » avec le nom du hub, ainsi que l’emplacement et l’ID de client, comme suit :

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Voici un message non enrichi. « my IoT Hub », « devicelocation » et « customerID » ne s’affichent pas ici car ce sont les champs qui seraient ajoutés par les enrichissements, et ce point de terminaison ne présente aucun enrichissement.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez supprimer toutes les ressources que vous avez créées dans ce tutoriel, supprimez le groupe de ressources. Cette opération supprime toutes les ressources contenues dans le groupe. Dans le cas présent, le hub IoT, le compte de stockage et le groupe de ressources sont supprimés.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Utiliser Azure CLI pour nettoyer des ressources

Pour supprimer le groupe de ressources, utilisez la commande [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` a été défini sur **ContosoResourcesMsgEn** au début de ce didacticiel.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré et testé l'ajout d'enrichissements aux messages IoT Hub en procédant comme suit :

**Utilisation des enrichissements de messages Azure IoT Hub**
> [!div class="checklist"]
> * Première méthode
>   * Créez des ressources et configurer le routage des messages à l’aide de l’Azure CLI.
>   * Configurez manuellement les enrichissements de message à l’aide du [Portail Azure](https://portal.azure.com).
> * Deuxième méthode
>   * Créez des ressources, configurez le routage des messages et les enrichissements de messages à l’aide d’un modèle de Azure Resource Manager. 
> * Exécutez une application qui simule l’envoi de messages par un appareil IoT au hub.
> * Afficher les résultats et vérifiez que les enrichissements de messages fonctionnent comme prévu.

Pour plus d’informations sur les enrichissements de messages, consultez [Vue d’ensemble des enrichissements de messages](iot-hub-message-enrichments-overview.md).

Pour plus d’informations sur le routage des messages, consultez les articles suivants :

* [Utiliser le routage des messages IoT Hub pour envoyer des messages appareil-à-cloud à différents points de terminaison](iot-hub-devguide-messages-d2c.md)

* [Tutoriel : Routage d’IoT Hub](tutorial-routing.md)