---
title: Didacticiel - à l’aide d’enrichissement de message Azure IoT Hub
description: Didacticiel illustrant l’utilisation d’enrichissement de message pour les messages Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: e4906bf9f2aead69c315ddb7b2e3b10489378d87
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259073"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Didacticiel : À l’aide d’enrichissement de message Azure IoT Hub (version préliminaire)

*Enrichissement de message* est la capacité du IoT Hub pour *tampon* messages avec des informations supplémentaires avant que les messages sont envoyés au point de terminaison désigné. Il est souhaitable d’utiliser d’enrichissement de message pour inclure des données qui peuvent être utilisées pour simplifier le traitement en aval. Par exemple, enrichir les messages de télémétrie d’appareil avec une balise de jumeau d’appareil peut réduire la charge sur les clients à tirer de jumeau d’appareil d’appels d’API pour obtenir ces informations. Pour plus d’informations, consultez le [vue d’ensemble de l’enrichissement de message](iot-hub-message-enrichments-overview.md).

Dans ce didacticiel, vous utilisez l’interface CLI Azure pour configurer les ressources, y compris les deux points de terminaison qui pointent vers deux conteneurs de stockage différent-- **enrichi** et **d’origine**. Puis vous utilisez le [Azure portal](https://portal.azure.com) pour configurer l’enrichissement de message à appliquer uniquement aux messages envoyés au point de terminaison avec le **enrichi** conteneur de stockage. Vous envoyez des messages à IoT Hub, qui sont acheminées vers les deux conteneurs de stockage. Seuls les messages envoyés au point de terminaison pour le **enrichi** conteneur de stockage sera être enrichi.

Voici les tâches que vous allez effectuer pour suivre ce didacticiel :

**À l’aide d’enrichissement de message IoT Hub**
> [!div class="checklist"]
> * À l’aide de l’interface CLI Azure, créez les ressources--un IoT hub, un compte de stockage avec deux points de terminaison et la configuration de routage.
> * Utilisez le portail Azure pour configurer les enrichissements de message.
> * Exécuter une application qui simule un appareil IoT envoyant des messages au concentrateur.
> * Afficher les résultats et vérifiez que l’enrichissement de message fonctionnent comme prévu.

## <a name="prerequisites"></a>Conditions préalables

* Vous devez avoir un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Installer [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Récupérer l’exemple de code

Télécharger [Simulation d’appareil IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) et décompressez-le. Ce référentiel comporte plusieurs applications, y compris celle que vous utiliserez pour envoyer des messages à IoT hub.

Ce téléchargement contient également le script pour créer les ressources utilisées pour tester l’enrichissement de message. Le script se trouve dans /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Pour l’instant, vous pouvez examiner le script et l’utiliser. Vous pouvez également copier le script directement à partir de l’article.

Lorsque vous êtes prêt à commencer à tester, vous utiliserez l’application de Simulation d’appareil à partir de ce téléchargement pour envoyer un message à votre IoT hub.

## <a name="set-up-and-configure-resources"></a>Installer et configurer des ressources

En plus de créer les ressources nécessaires, le script Azure CLI configure également les deux itinéraires pour les points de terminaison qui sont des conteneurs de stockage distinct. Pour plus d’informations sur la configuration du routage, consultez le [didacticiel routage](tutorial-routing.md). Une fois que les ressources sont configurés, vous utilisez le [Azure portal](https://portal.azure.com) configurer enrichissement de message pour chaque point de terminaison, puis passez à l’étape de test.

> [!NOTE]
> Tous les messages sont acheminés vers les deux points de terminaison, mais uniquement les messages acheminés au point de terminaison avec enrichissement de message configurés seront enrichies.
>

Vous pouvez utiliser le script ci-dessous, ou ouvrez le script dans le dossier /ressources du référentiel téléchargé. Voici les étapes que le script s’exécutera :

* Créez un IoT Hub.
* Créez un compte de stockage.
* Créer deux conteneurs du compte de stockage : un pour les messages enrichies et un pour les messages qui ne sont pas enrichies.
* Configurer le routage pour les deux comptes de stockage différent.
    * Créer un point de terminaison pour chaque conteneur de compte de stockage.
    * Créez un itinéraire vers chacun des points de terminaison de conteneur de compte de stockage.

Plusieurs noms de ressources doivent être globalement uniques, comme le nom du hub IoT et le nom du compte de stockage. Pour faciliter l’exécution du script plus facile, les noms de ressource sont ajoutées avec une valeur aléatoire d’alphanumériques appelée *randomValue*. La valeur randomValue est générée une fois au début du script, puis ajoutée aux noms de ressources tout au long du script en fonction des besoins. Si vous ne souhaitez pas que cette valeur soit aléatoire, vous pouvez la définir sur une chaîne vide ou sur une valeur spécifique.

Si vous n’avez pas déjà fait, ouvrez un [fenêtre Cloud Shell pour Bash.](https://shell.azure.com). Ouvrez le script dans le référentiel décompressé, utilisez Ctrl + A pour sélectionner tout cela, puis Ctrl + C pour copier. Alternativement, vous pouvez copier le script CLI suivant ou ouvrez-le directement dans cloud shell. Collez le script dans la fenêtre d’interpréteur de commandes cloud Azure en cliquant sur la ligne de commande et en sélectionnant **coller**. Le script est exécuté une instruction à la fois. Une fois le script s’arrête en cours d’exécution, sélectionnez **entrée** pour vous assurer qu’il exécute la dernière commande. Le bloc de code suivant illustre le script qui est utilisé, avec commentaires expliquant ce qu’il fait.

Voici les ressources créées par le script. **Enrichi** signifie que la ressource est pour les messages ayant : enrichissements de. **D’origine** signifie que la ressource est pour les messages qui ne sont pas enrichies.

| Nom | Valeur |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| Nom du conteneur | original  |
| Nom du conteneur | enrichi  |
| Nom de l’appareil IoT | Appareil de Test de Contoso |
| Nom de l’IoT Hub | ContosoTestHubMsgEn |
| Nom de compte de stockage | contosostorage |
| point de terminaison 1 de nom | ContosoStorageEndpointOriginal |
| point de terminaison 2 de nom | ContosoStorageEndpointEnriched|
| itinéraire Name 1 | ContosoStorageRouteOriginal |
| itinéraire nom 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
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
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

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

# This is the endpoint for container 1, for endpoint messages that are not enriched.
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

# This is the endpoint for container 2, for endpoint messages that are enriched.
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

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

À ce stade, les ressources sont tous paramétrés et le routage est configuré. Vous pouvez afficher la configuration de routage de message dans le portail et configurer l’enrichissement de message pour les messages à la **enrichi** conteneur de stockage.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Afficher le routage et configurer l’enrichissement de message

1. Accédez à votre IoT Hub en sélectionnant **groupes de ressources**, puis sélectionnez le groupe de ressources défini pour ce didacticiel (**ContosoResources_MsgEn**). Recherchez le IoT Hub dans la liste et sélectionnez-le. Sélectionnez *le routage des messages** pour le Iot Hub.

   ![Sélectionnez le routage des messages](./media/tutorial-message-enrichments/select-iot-hub.png)

   Le message volet routage a trois onglets-- **itinéraires**, **les points de terminaison personnalisé**, et **enrichir les messages**. Vous pouvez parcourir les deux premiers onglets pour afficher la configuration définie par le script. Le troisième onglet permet d’ajouter l’enrichissement de message. Nous allons enrichir les messages acheminés au point de terminaison pour le conteneur de stockage nommé **enrichi**. Renseignez le nom et la valeur, puis sélectionnez le point de terminaison **ContosoStorageEndpointEnriched** dans la liste déroulante. Voici un exemple de configuration d’un enrichissement qui ajoute le nom du IoT Hub au message :

   ![Ajouter le premier enrichissement](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Ajouter ces valeurs à la liste pour le point de terminaison ContosoStorageEndpointEnriched.

   | Nom | Valeur | Point de terminaison (liste déroulante) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Emplacement de l’appareil | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Si votre appareil n’a pas un double, la valeur que vous placez ici est marquée en tant que chaîne pour la valeur de l’enrichissement de message. Pour afficher l’appareil de représentations d’informations, accédez à votre hub dans le portail, puis sélectionnez **appareils IoT**, sélectionnez votre appareil, puis **jumeau d’appareil** en haut de la page.
   >
   > Vous pouvez modifier les informations relatives au jumeau pour ajouter des balises (par exemple, emplacement) et affectez-lui une valeur spécifique si vous le souhaitez. Pour en savoir plus, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](iot-hub-devguide-device-twins.md)

3. Lorsque vous avez terminé, votre volet doit ressembler à cette image :

   ![Table avec toutes les enrichissements ajouté](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Sélectionnez **appliquer** pour enregistrer les modifications.

## <a name="send-messages-to-the-iot-hub"></a>Envoyer des messages à IoT Hub

Maintenant que l’enrichissement de message est configurés pour le point de terminaison, exécutez l’application d’appareil simulé pour envoyer des messages à IoT Hub. Le concentrateur a été configuré avec les règles pour accomplir les tâches suivantes :

* Messages acheminés vers le point de terminaison de stockage ContosoStorageEndpointOriginal ne sera pas enrichies et seront stockées dans le conteneur de stockage `original`.

* Messages acheminés vers le point de terminaison de stockage ContosoStorageEndpointEnriched seront enrichies et stockés dans le conteneur de stockage `enriched`.

L’application Simulated Device est l’une des applications dans le téléchargement décompressé. L’application envoie des messages pour chacune des méthodes de routage de message différent dans le [didacticiel routage](tutorial-routing.md); Cela inclut le stockage Azure.

Double-cliquez sur le fichier de solution (IoT_SimulatedDevice.sln) pour ouvrir le code dans Visual Studio, puis ouvrez Program.cs. Remplacez `{your hub name}` par le nom de hub IoT. Le format du nom d’hôte IoT hub est **{nom de votre hub} .azure-Devices.NET**. Pour ce didacticiel, le nom d’hôte hub est **ContosoTestHubMsgEn.azure-Devices.NET**. Ensuite, remplacez `{device key}` avec la clé d’appareil que vous avez enregistré précédemment lorsque vous exécutez le script pour créer les ressources.

Si vous n’avez pas la clé d’appareil, vous pouvez le récupérer à partir du portail. Une fois connecté, accédez à **groupes de ressources**, sélectionnez votre groupe de ressources, puis sélectionnez votre IoT Hub. Regardez sous **appareils IoT** pour votre appareil de test et sélectionnez votre appareil. Sélectionnez l’icône de copie **clé primaire** pour le copier dans le Presse-papiers.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Exécuter et tester

Exécutez l’application console. Patientez quelques minutes. Les messages qui sont envoyés sont affichés sur l’écran de la console de l’application.

L’application envoie un nouveau message appareil-à-cloud à l’IoT Hub toutes les secondes. Le message contient un objet sérialisé JSON avec l’ID d’appareil, la température, l’humidité et le niveau du message, qui est défini par défaut sur `normal`. Il affecte de manière aléatoire un niveau de `critical` ou `storage`, qui provoque le message est routé vers le compte de stockage ou au point de terminaison par défaut. Les messages envoyés à la **enrichi** conteneur dans le compte de stockage sera être enrichie.

Une fois que plusieurs messages de stockage ont été envoyés, afficher les données.

1. Sélectionnez **groupes de ressources**, puis recherchez votre groupe de ressources (ContosoResourcesMsgEn) et sélectionnez-le.

2. Sélectionnez votre compte de stockage (contosostorage). Puis sélectionnez **Explorateur de stockage (version préliminaire)** à partir du volet de sélection sur la gauche.

   ![Sélectionnez l’Explorateur de stockage](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Sélectionnez **conteneurs d’objets BLOB** pour voir les deux conteneurs qui peuvent être utilisés.

   ![Afficher les conteneurs dans le compte de stockage](./media/tutorial-message-enrichments/show-blob-containers.png)

Les messages dans le conteneur appelé **enrichi** ont l’enrichissement de message inclus dans les messages. Les messages dans le conteneur appelé **d’origine** aura des messages bruts avec aucun enrichissement. Explorez un des conteneurs jusqu'à ce que vous obtenez vers le bas et ouvrez le fichier de message le plus récent, puis faites de même pour l’autre conteneur vérifier qu’il n’y a aucun enrichissement ajoutés aux messages dans ce conteneur.

Lorsque vous examinez les messages qui ont été enrichies, vous devez voir « My IoT Hub » avec le nom du hub, ainsi que l’emplacement et l’ID de client, comme suit :

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","device location":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Et Voici un message unenriched. « « Mon IoT Hub », « Emplacement de l’appareil » et « customerID » ne s’affichent pas ici, car ce point de terminaison n’a aucun enrichissement.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez supprimer toutes les ressources que vous avez créé dans ce didacticiel, supprimez le groupe de ressources. Cette opération supprime toutes les ressources contenues dans le groupe. Dans le cas présent, le hub IoT, le compte de stockage et le groupe de ressources sont supprimés.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Utiliser Azure CLI pour nettoyer des ressources

Pour supprimer le groupe de ressources, utilisez la commande [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` a été défini sur **ContosoResources** au début de ce tutoriel.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous configurez et testé Ajout enrichissement de message pour les messages IoT Hub en procédant comme suit :

**À l’aide d’enrichissement de message IoT Hub**
> [!div class="checklist"]
> * À l’aide de l’interface CLI Azure, créez les ressources--un IoT hub, un compte de stockage avec deux enendpoints et la configuration de routage.
> * Utilisez le portail Azure pour configurer les enrichissements de message.
> * Exécuter une application qui simule un appareil IoT envoi du message au hub.
> * Afficher les résultats et vérifiez que l’enrichissement de message fonctionnent comme prévu.

Pour plus d’informations sur l’enrichissement de message, consultez le [vue d’ensemble de l’enrichissement de message](iot-hub-message-enrichments-overview.md).

Pour plus d’informations sur le routage des messages, consultez les articles suivants :

* [Utiliser le routage des messages IoT Hub pour envoyer des messages appareil-à-cloud à différents points de terminaison](iot-hub-devguide-messages-d2c.md)

* [Tutoriel : Routage d’IoT Hub](tutorial-routing.md)