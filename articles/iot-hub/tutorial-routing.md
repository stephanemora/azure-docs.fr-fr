---
title: Configurer le routage des messages avec Azure IoT Hub (.NET) | Microsoft Docs
description: Configurer le routage des messages avec Azure IoT Hub
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: ab354410ba3b0b37ae630a2b68daec63a9051555
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700823"
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>Didacticiel : Configurer le routage des messages avec IoT Hub

Le routage des messages vous permet d’envoyer des données de télémétrie de vos appareils IoT à des points de terminaison compatibles avec Event Hub intégrés ou à des points de terminaison personnalisés comme le stockage d’objets blob, la file d’attente Service Bus, la rubrique Service Bus et Event Hubs. Lors de la configuration du routage des messages, vous pouvez créer des règles d’acheminement pour personnaliser l’itinéraire qui correspond à une certaine règle. Une fois la configuration effectuée, les données entrantes sont automatiquement acheminées vers les points de terminaison par l’IoT Hub. 

Dans ce didacticiel, vous allez apprendre à configurer et utiliser des règles d’acheminement avec IoT Hub. Vous allez acheminer des messages à partir d’un appareil IoT vers un ou plusieurs services, y compris le stockage d’objets blob et une file d’attente Service Bus. Les messages envoyés vers la file d’attente Service Bus seront récupérés par une application logique et envoyés par e-mail. Les messages dont le routage n’a pas été spécifiquement configuré sont envoyés au point de terminaison par défaut et affichés dans une visualisation Power BI.

Dans ce didacticiel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Configurer, à l’aide de PowerShell ou d’Azure CLI, les ressources de base : un IoT Hub, un compte de stockage, une file d’attente Service Bus et un appareil simulé.
> * Configurer les itinéraires et les points de terminaison dans IoT Hub pour le compte de stockage et la file d’attente Service Bus.
> * Créer une application logique qui est déclenchée et qui envoie un e-mail lors de l’ajout d’un message à la file d’attente Service Bus.
> * Télécharger et exécuter une application qui simule l’envoi de messages par un appareil IoT au Hub pour les différentes options de routage.
> * Créer une visualisation Power BI pour les données envoyées au point de terminaison par défaut.
> * Afficher les résultats...
> * ... dans la file d’attente Service Bus et les e-mails.
> * ... dans le compte de stockage.
> * ... dans la visualisation Power BI.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Installez [Visual Studio pour Windows](https://www.visualstudio.com/). 

- Un compte Power BI pour examiner l’analytique du flux de données du point de terminaison par défaut. ([Essayez Power BI gratuitement](https://app.powerbi.com/signupredirect?pbi_source=web)).

- Un compte Office 365 pour envoyer des e-mails de notification. 

Vous avez besoin d’Azure CLI ou d’Azure PowerShell pour effectuer les étapes de configuration de ce didacticiel. 

Pour utiliser Azure CLI, alors que vous pouvez l’installer localement, nous vous recommandons d’utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter des scripts Azure CLI. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte ; vous n’avez donc pas besoin de les installer en local. 

Pour utiliser PowerShell, installez-le en local en suivant les instructions ci-dessous. 

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Cloud Shell peut être ouvert de plusieurs façons :

|  |   |
|-----------------------------------------------|---|
| Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code. | ![Cloud Shell dans cet article](./media/tutorial-routing/cli-try-it.png) |
| Ouvrez Cloud Shell dans votre navigateur. | [![https://shell.azure.com/bash](./media/tutorial-routing/launchcloudshell.png)](https://shell.azure.com) |
| Sélectionnez le bouton **Cloud Shell** du menu situé en haut à droite du [portail Azure](https://portal.azure.com). |    ![Cloud Shell dans le portail](./media/tutorial-routing/cloud-shell-menu.png) |
|  |  |

### <a name="using-azure-cli-locally"></a>Utilisation d’Azure CLI en local

Si vous préférez utiliser Azure CLI en local plutôt que Cloud Shell, vous devez disposer du module Azure CLI version 2.0.30.0 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

### <a name="using-powershell-locally"></a>Utilisation de PowerShell en local

Pour ce didacticiel, le module Azure PowerShell version 5.7 ou ultérieure est nécessaire. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="set-up-resources"></a>Configurer des ressources

Pour ce didacticiel, vous avez besoin d’un IoT Hub, d’un compte de stockage et d’une file d’attente Service Bus. Ces ressources peuvent toutes être créées à l’aide d’Azure CLI ou d’Azure PowerShell. Utilisez le même groupe de ressources et le même emplacement pour toutes les ressources. Puis à la fin, vous pouvez supprimer tous les éléments en une seule fois en supprimant le groupe de ressources.

Les sections suivantes expliquent comment effectuer ces étapes. Suivez les instructions relatives à Azure CLI *ou* à PowerShell.

1. Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md). 

    <!-- When they add the Basic tier, change this to use Basic instead of Standard. -->

2. Créez un IoT Hub dans le niveau S1. Ajoutez un groupe de consommateurs à votre IoT Hub. Le groupe de consommateurs est utilisé par Azure Stream Analytics lors de la récupération des données.

3. Créez un compte de stockage V1 standard avec la réplication Standard_LRS.

4. Créez un espace de noms et une file d’attente Service Bus. 

5. Créez une identité d’appareil pour l’appareil simulé qui envoie des messages à votre Hub. Enregistrez la clé pour la phase de test.

### <a name="azure-cli-instructions"></a>Instructions Azure CLI

La méthode la plus simple pour utiliser ce script est de le copier, puis de le coller dans Cloud Shell. En supposant que vous êtes déjà connecté, il exécutera le script, ligne par ligne. 

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostorage$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "$storageAccountKey"

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off 

# The Service Bus namespace must be globally unique, so add a random number to the end.
sbNameSpace=ContosoSBNamespace$RANDOM
echo "Service Bus namespace = " $sbNameSpace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNameSpace \
    --location $location
    
# The Service Bus queue name must be globally unique, so add a random number to the end.
sbQueueName=ContosoSBQueue$RANDOM
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNameSpace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

### <a name="powershell-instructions"></a>Instructions PowerShell

La méthode la plus simple pour utiliser ce script consiste à ouvrir [PowerShell ISE](/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise.md), à copier le script dans le Presse-papiers, puis à le coller en intégralité dans la fenêtre de script. Vous pouvez ensuite modifier les valeurs des noms de ressource (si vous le souhaitez) et exécuter le script en intégralité. 

```azurepowershell-interactive
# Log into Azure account.
Login-AzureRMAccount

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"
$iotDeviceName = "Contoso-Test-Device"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, so add a random number to the end.
$iotHubName = "ContosoTestHub$(Get-Random)"
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzureRmIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzureRmIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random number to the end.
$storageAccountName = "contosostorage$(Get-Random)"
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzureStorageContainer -Name $containerName `
    -Context $storageContext

# The Service Bus namespace must be globally unique,
#   so add a random number to the end.
$serviceBusNamespace = "ContosoSBNamespace$(Get-Random)"
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzureRmServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random number to the end.
$serviceBusQueueName  = "ContosoSBQueue$(Get-Random)"
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzureRmServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

Ensuite, créez une identité d’appareil et enregistrez sa clé pour une utilisation ultérieure. Cette identité d’appareil est utilisée par l’application de simulation pour envoyer des messages à IoT Hub. Cette fonctionnalité n’est pas disponible dans PowerShell, mais vous pouvez créer l’appareil dans le [portail Azure](https://portal.azure.com).

1. Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous à votre compte Azure.

2. Cliquez sur **Groupes de ressources** et sélectionnez votre groupe de ressources. Ce didacticiel utilise **ContosoResources**.

3. Dans la liste des ressources, cliquez sur votre IoT Hub. Ce didacticiel utilise **ContosoTestHub**. Sélectionnez **Appareils IoT** dans le volet Hub.

4. Cliquez sur **+ Ajouter**. Dans le volet Ajouter un appareil, indiquez l’ID d’appareil. Ce didacticiel utilise **Contoso-Test-Device**. N’indiquez pas de clés et cochez l’option **Générer automatiquement les clés**. Vérifiez que l’option **Connecter l’appareil à IoT Hub** est activée. Cliquez sur **Enregistrer**.

   ![Capture d’écran montrant l’écran d’ajout de l’appareil.](./media/tutorial-routing/add-device.png)

5. Une fois l’opération de création terminée, cliquez sur l’appareil pour voir les clés générées. Cliquez sur l’icône de copie de la clé primaire et enregistrez-la quelque part (dans le bloc-notes par exemple) pour l’utiliser lors de la phase de test dans ce didacticiel.

   ![Capture d’écran présentant les détails de l’appareil, y compris les clés.](./media/tutorial-routing/device-details.png)

## <a name="set-up-message-routing"></a>Configurer le routage de messages

Vous vous apprêtez à acheminer les messages vers différentes ressources en fonction des propriétés jointes au message par l’appareil simulé. Les messages dont le routage n’est pas personnalisé sont envoyés au point de terminaison par défaut (messages/événements). 

|value |Résultat|
|------|------|
|level="storage" |Écrire dans Stockage Azure.|
|level="critical" |Écrire dans une file d’attente Service Bus. Une application logique extrait le message de la file d’attente et utilise Office 365 pour envoyer le message par e-mail.|
|default |Afficher ces données à l’aide de Power BI.|

### <a name="routing-to-a-storage-account"></a>Routage vers un compte de stockage 

Configurez maintenant le routage pour le compte de stockage. Définissez un point de terminaison, puis configurez un itinéraire pour ce point de terminaison. Les messages pour lesquels la propriété **level** est définie sur **storage** sont automatiquement écrits dans un compte de stockage.

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Groupes de ressources**, puis sélectionnez votre groupe de ressources. Ce didacticiel utilise **ContosoResources**. Dans la liste des ressources, cliquez sur l’IoT Hub. Ce didacticiel utilise **ContosoTestHub**. Cliquez sur **Endpoints**. Dans le volet **Points de terminaison**, cliquez sur **+Ajouter**. Entrez les informations suivantes :

   **Nom** : saisissez le nom du point de terminaison. Ce didacticiel utilise **StorageContainer**.
   
   **Type de point de terminaison** : sélectionnez **Conteneur de stockage Azure** dans la liste déroulante.

   Cliquez sur **Pick a container** (Choisir un conteneur) pour afficher la liste des comptes de stockage. Sélectionnez votre compte de stockage. Ce tutoriel utilise **contosostorage**. Sélectionnez ensuite le conteneur. Ce didacticiel utilise **contosoresults**. Cliquez sur **Sélectionner**, ce qui vous redirige vers le volet **Ajouter un point de terminaison**. 
   
   ![Capture d’écran présentant l’ajout d’un point de terminaison.](./media/tutorial-routing/add-endpoint-storage-account.png)
   
   Cliquez sur **OK** pour terminer l’ajout du point de terminaison.
   
2. Cliquez sur **Itinéraires** dans votre IoT Hub. Vous allez créer une règle d’acheminement qui achemine les messages vers le conteneur de stockage que vous venez d’ajouter en tant que point de terminaison. Cliquez sur **+Ajouter** dans la partie supérieure du volet Itinéraires. Renseignez les champs affichés à l’écran. 

   **Nom** : saisissez le nom de votre règle d’acheminement. Ce didacticiel utilise **StorageRule**.

   **Source de données** : sélectionnez **Messages des appareils** dans la liste déroulante.

   **Point de terminaison** : sélectionnez le point de terminaison que vous venez de configurer. Ce didacticiel utilise **StorageContainer**. 
   
   **Chaîne de requête** : entrez `level="storage"` pour la chaîne de requête. 

   ![Capture d’écran montrant la création d’une règle d’acheminement pour le compte de stockage.](./media/tutorial-routing/create-a-new-routing-rule-storage.png)
   
   Cliquez sur **Enregistrer**. Une fois l’opération terminée, vous êtes redirigé vers le volet Itinéraires dans lequel vous pouvez voir la nouvelle règle d’acheminement pour le stockage. Fermez le volet Itinéraires, ce qui vous redirige vers la page Groupe de ressources.

### <a name="routing-to-a-service-bus-queue"></a>Routage vers une file d’attente Service Bus 

Configurez maintenant le routage pour la file d’attente Service Bus. Définissez un point de terminaison, puis configurez un itinéraire pour ce point de terminaison. Les messages pour lesquels la propriété **level** est définie sur **critical** sont écrits dans la file d’attente Service Bus, qui déclenche une application logique qui envoie ensuite un e-mail avec les informations. 

1. Sur la page Groupe de ressources, cliquez sur votre IoT Hub, puis sur **Points de terminaison**. Dans le volet **Points de terminaison**, cliquez sur **+Ajouter**. Entrez les informations ci-après.

   **Nom** : saisissez le nom du point de terminaison. Ce didacticiel utilise **CriticalQueue**. 

   **Type de point de terminaison** : sélectionnez **File d’attente Service Bus** dans la liste déroulante.

   **Espace de noms Service Bus** : sélectionnez l’espace de noms Service Bus pour ce didacticiel dans la liste déroulante. Ce didacticiel utilise **ContosoSBNamespace**.

   **File d’attente Service Bus** : sélectionnez la file d’attente Service Bus dans la liste déroulante. Ce didacticiel utilise **contososbqueue**.

   ![Capture d’écran présentant l’ajout d’un point de terminaison pour la file d’attente Service Bus.](./media/tutorial-routing/add-endpoint-sb-queue.png)

   Cliquez sur **OK** pour enregistrer le point de terminaison. Une fois l’opération terminée, fermez le volet Points de terminaison. 
    
2. Cliquez sur **Itinéraires** dans votre IoT Hub. Vous allez créer une règle d’acheminement qui achemine les messages vers la file d’attente Service Bus que vous venez d’ajouter en tant que point de terminaison. Cliquez sur **+Ajouter** dans la partie supérieure du volet Itinéraires. Renseignez les champs affichés à l’écran. 

   **Nom** : saisissez le nom de votre règle d’acheminement. Ce didacticiel utilise **SBQueueRule**. 

   **Source de données** : sélectionnez **Messages des appareils** dans la liste déroulante.

   **Point de terminaison** : sélectionnez le point de terminaison que vous venez de configurer, **CriticalQueue**.

   **Chaîne de requête** : entrez `level="critical"` pour la chaîne de requête. 

   ![Capture d’écran montrant la création d’une règle d’acheminement pour la file d’attente Service Bus.](./media/tutorial-routing/create-a-new-routing-rule-sbqueue.png)
   
   Cliquez sur **Enregistrer**. Lorsque vous êtes redirigé vers le volet Itinéraires, vous voyez les deux nouvelles règles d’acheminement, comme affiché ici.

   ![Capture d’écran montrant les itinéraires que vous venez de configurer.](./media/tutorial-routing/show-routing-rules-for-hub.png)

   Fermez le volet Itinéraires, ce qui vous redirige vers la page Groupe de ressources.

## <a name="create-a-logic-app"></a>Créer une application logique  

La file d’attente Service Bus doit être utilisée pour recevoir des messages indiqués comme étant critiques. Configurez une application logique pour surveiller la file d’attente Service Bus et envoyer un e-mail lors de l’ajout d’un message à la file d’attente. 

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **+ Créer une ressource**. Tapez **application logique** dans la zone de recherche, puis cliquez sur Entrée. Dans les résultats de la recherche affichés, sélectionnez l’application logique, puis cliquez sur **Créer** pour continuer vers le volet **Créer une application logique**. Renseignez les champs. 

   **Nom** : ce champ indique le nom de l’application logique. Ce didacticiel utilise **ContosoLogicApp**. 

   **Abonnement** : sélectionnez votre abonnement Azure.

   **Groupe de ressources** : cliquez sur **Utiliser l’existant** et sélectionnez votre groupe de ressources. Ce didacticiel utilise **ContosoResources**. 

   **Emplacement** : utilisez votre emplacement. Ce didacticiel utilise **Ouest des États-Unis**. 

   **Log Analytics** : cette option doit être désactivée. 

   ![Capture d’écran montrant l’écran Créer une application logique.](./media/tutorial-routing/create-logic-app.png)

   Cliquez sur **Créer**.

4. Accédez maintenant à l’application logique. La méthode la plus simple pour accéder à l’application logique consiste à cliquer sur **Groupe de ressources**, à sélectionner votre groupe de ressources (ce didacticiel utilise**ContosoResources**), puis à sélectionner l’application logique dans la liste des ressources. La page Concepteur d’applications logiques apparaît (vous devrez peut-être faire défiler vers la droite pour voir la page entière). Sur la page Concepteur d’applications logiques, faites défiler jusqu’à ce que vous voyiez la vignette indiquant **Application logique vide +** et cliquez dessus. 

5. Une liste de connecteurs s’affiche. Sélectionnez **Service Bus**. 

   ![Capture d’écran montrant la liste des connecteurs.](./media/tutorial-routing/logic-app-connectors.png)

6. Une liste de déclencheurs s’affiche. Sélectionnez **Service Bus - Quand une file d’attente reçoit un message (saisie semi-automatique)**. 

   ![Capture d’écran montrant la liste des déclencheurs pour Service Bus.](./media/tutorial-routing/logic-app-triggers.png)

6. Dans l’écran suivant, indiquez le nom de connexion. Ce didacticiel utilise **ContosoConnection**. 

   ![Capture d’écran montrant la configuration de la connexion pour la file d’attente Service Bus.](./media/tutorial-routing/logic-app-define-connection.png)

   Cliquez sur l’espace de noms Service Bus. Ce didacticiel utilise **ContosoSBNamespace**. Lorsque vous sélectionnez l’espace de noms, le portail interroge l’espace de noms Service Bus pour récupérer les clés. Sélectionnez **RootManageSharedAccessKey** et cliquez sur **Créer**. 
   
   ![Capture d’écran montrant la fin de la configuration de la connexion.](./media/tutorial-routing/logic-app-finish-connection.png)

7. Dans l’écran suivant, sélectionnez le nom de la file d’attente (ce didacticiel utilise **contososbqueue**) dans la liste déroulante. Vous pouvez utiliser les valeurs par défaut pour les autres champs. 

   ![Capture d’écran montrant les options de file d’attente.](./media/tutorial-routing/logic-app-queue-options.png)

7. Définissez maintenant l’action pour envoyer un e-mail quand un message est reçu dans la file d’attente. Dans le Concepteur d’applications logiques, cliquez sur **+ Nouvelle étape** pour ajouter une étape, puis cliquez sur **Ajouter une action**. Dans le volet **Choisir une action**, recherchez **Outlook Office 365** et cliquez dessus. Dans l’écran des déclencheurs, sélectionnez **Office 365 Outlook - Envoyer un message électronique**.  

   ![Capture d’écran montrant les options Office 365.](./media/tutorial-routing/logic-app-select-outlook.png)

8. Ensuite, connectez-vous à votre compte Office 365 pour configurer la connexion. Spécifiez les adresses e-mail des destinataires. Indiquez également l’objet et tapez le message que vous souhaitez que le destinataire voit dans le corps de l’e-mail. Pour tester si tout fonctionne bien, indiquez votre propre adresse e-mail dans le champ des destinataires.

   Cliquez sur **Ajouter du contenu dynamique** pour afficher le contenu du message que vous pouvez inclure. Sélectionnez **Contenu** pour que le message soit inclus dans l’e-mail. 

   ![Capture d’écran montrant les options d’e-mail pour l’application logique.](./media/tutorial-routing/logic-app-send-email.png)

9. Cliquez sur **Enregistrer**. Puis, fermez le Concepteur d’applications logiques.

## <a name="set-up-azure-stream-analytics"></a>Configurer Azure Stream Analytics

Pour afficher les données dans une visualisation Power BI, commencez par configurer un travail Stream Analytics pour récupérer les données. N’oubliez pas que seuls les messages pour lesquels la propriété **level** est définie sur **normal** sont envoyés au point de terminaison par défaut et seront récupérés par le travail Stream Analytics pour la visualisation Power BI.

### <a name="create-the-stream-analytics-job"></a>Créer le travail Stream Analytics

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource** > **Internet des objets** > **Tâche Stream Analytics**.

2. Saisissez les informations ci-après concernant le travail.

   **Nom du travail** : nom du travail. Le nom doit être globalement unique. Ce didacticiel utilise **contosoJob**.

   **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre IoT Hub. Ce didacticiel utilise **ContosoResources**. 

   **Emplacement**: utilisez le même emplacement que celui utilisé dans le script de configuration. Ce didacticiel utilise **Ouest des États-Unis**. 

   ![Capture d’écran montrant comment créer le travail Stream Analytics.](./media/tutorial-routing/stream-analytics-create-job.png)

3. Cliquez sur **Créer** pour créer le travail. Pour revenir à la tâche, cliquez sur **Groupes de ressources**. Ce didacticiel utilise **ContosoResources**. Sélectionnez le groupe de ressources, puis cliquez sur le travail Stream Analytics dans la liste des ressources. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Ajouter une entrée au travail Stream Analytics

1. Sous **Topologie du travail**, cliquez sur **Entrées**.

2. Dans le volet **Entrées**, cliquez sur **Ajouter une entrée de flux** et sélectionnez IoT Hub. Dans l’écran qui s’affiche, renseignez les champs suivants :

   **Alias d’entrée** : ce didacticiel utilise **contosoinputs**.

   **Abonnement** : sélectionnez votre abonnement.

   **IoT Hub** : sélectionnez l’IoT Hub. Ce didacticiel utilise **ContosoTestHub**.

   **Point de terminaison** : sélectionnez **Messagerie**. (Si vous sélectionnez Surveillance des opérations, vous obtenez les données de télémétrie sur l’IoT Hub, plutôt que les données que vous envoyez.) 

   **Nom de la stratégie d’accès partagé** : sélectionnez **iothubowner**. Le portail renseigne la clé de la stratégie d’accès partagé pour vous.

   **Groupe de consommateurs** : sélectionnez le groupe de consommateurs que vous avez créé précédemment. Ce didacticiel utilise **contosoconsumers**.
   
   Pour les autres champs, acceptez les valeurs par défaut. 

   ![Capture d’écran montrant comment configurer les entrées du travail Stream Analytics.](./media/tutorial-routing/stream-analytics-job-inputs.png)

5. Cliquez sur **Enregistrer**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Ajouter une sortie au travail Stream Analytics

1. Sous **Topologie du travail**, cliquez sur **Sorties**.

2. Dans le volet **Sorties**, cliquez sur **Ajouter**, puis sélectionnez **Power BI**. Dans l’écran qui s’affiche, renseignez les champs suivants :

   **Alias de sortie** : alias unique de la sortie. Ce didacticiel utilise **contosooutputs**. 

   **Nom du jeu de données** : nom du jeu de données à utiliser dans Power BI. Ce didacticiel utilise **contosodataset**. 

   **Nom de la table** : nom de la table à utiliser dans Power BI. Ce didacticiel utilise **contosotable**.

   Acceptez les valeurs par défaut pour les autres champs.

3. Cliquez sur **Autoriser** et connectez-vous à votre compte Power BI.

   ![Capture d’écran montrant comment configurer les sorties du travail Stream Analytics.](./media/tutorial-routing/stream-analytics-job-outputs.png)

4. Cliquez sur **Enregistrer**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurer la requête du travail Stream Analytics

1. Sous **Topologie du travail**, cliquez sur **Requête**.

2. Remplacez `[YourInputAlias]` par l’alias d’entrée du travail. Ce didacticiel utilise **contosoinputs**.

3. Remplacez `[YourOutputAlias]` par l’alias de sortie du travail. Ce didacticiel utilise **contosooutputs**.

   ![Capture d’écran montrant comment configurer la requête pour le travail Stream Analytics.](./media/tutorial-routing/stream-analytics-job-query.png)

4. Cliquez sur **Enregistrer**.

5. Fermez le volet Requête. Cela vous renvoie à l’affichage des ressources dans le groupe de ressources. Cliquez sur le travail Stream Analytics. Ce tutoriel l’appelle **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Exécuter la tâche Stream Analytics

Dans le travail Stream Analytics, cliquez sur **Démarrer** > **Maintenant** > **Démarrer**. Une fois le travail lancé, l’état correspondant passe de **Arrêté** à **Exécution**.

Pour configurer le rapport Power BI, vous avez besoin de données ; vous allez donc configurer Power BI après la création de l’appareil et l’exécution de l’application de simulation de l’appareil.

## <a name="run-simulated-device-app"></a>Exécuter l’application d’appareil simulé

Plus haut dans la section sur la configuration du script, vous avez configuré un appareil pour simuler l’utilisation d’un appareil IoT. Dans cette section, vous téléchargez une application console .NET qui simule un appareil envoyant des messages appareil-à-cloud à un IoT Hub. Cette application envoie des messages pour chacune des différentes méthodes de routage. 

Téléchargez la solution pour la [simulation d’appareil IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Cela permet de télécharger un référentiel contenant plusieurs applications ; la solution que vous recherchez se trouve à l’emplacement iot-hub/Tutorials/Routing/SimulatedDevice/.

Double-cliquez sur le fichier de solution (SimulatedDevice.sln) pour ouvrir le code dans Visual Studio, puis ouvrez Program.cs. Remplacez `{iot hub hostname}` par le nom d’hôte IoT Hub. Le format du nom d’hôte IoT Hub est **{iot-hub-name}.azure-devices.net**. Pour ce didacticiel, le nom d’hôte IoT Hub est **ContosoTestHub.azure-devices.net**. Ensuite, remplacez `{device key}` par la clé de l’appareil enregistrée précédemment lorsque vous avez configuré l’appareil simulé. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Exécuter et tester 

Exécutez l’application console. Patientez quelques minutes. Vous pouvez voir les messages envoyés sur l’écran de la console de l’application.

L’application envoie un nouveau message appareil-à-cloud à l’IoT Hub toutes les secondes. Le message contient un objet sérialisé JSON avec l’ID d’appareil, la température, l’humidité et le niveau du message, qui est défini par défaut sur `normal`. Un niveau de `critical` ou `storage` est attribué de façon aléatoire, ce qui permet au message d’être acheminé vers le compte de stockage ou la file d’attente Service Bus (qui déclenche votre application logique pour envoyer un e-mail). Les mesures par défaut (`normal`) seront affichées dans le rapport BI que vous configurerez après.

À ce stade, si tout est configuré correctement, vous devez voir les résultats suivants :

1. Vous commencez à recevoir des e-mails sur les messages critiques. 

   ![Capture d’écran montrant les e-mails résultants.](./media/tutorial-routing/results-in-email.png)

   Cela signifie :

   * Que le routage vers la file d’attente Service Bus fonctionne correctement.
   * Que l’application logique qui récupère le message dans la file d’attente Service Bus fonctionne correctement.
   * Que le connecteur de l’application logique pour Outlook fonctionne correctement. 

2. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Groupes de ressources** et sélectionnez votre groupe de ressources. Ce didacticiel utilise **ContosoResources**. Sélectionnez le compte de stockage, cliquez sur **Objets blob**, puis sélectionnez le conteneur. Ce didacticiel utilise **contosoresults**. Vous devez voir un dossier, et vous pouvez explorer les répertoires jusqu’à voir un ou plusieurs fichiers. Ouvrez l’un de ces fichiers ; ils contiennent les entrées acheminées vers le compte de stockage. 

   ![Capture d’écran montrant les fichiers de résultats dans le stockage.](./media/tutorial-routing/results-in-storage.png)

Cela signifie :

   * Que le routage vers le compte de stockage fonctionne correctement.

Maintenant, avec l’application en cours d’exécution, configurez la visualisation Power BI pour voir les messages entrants via le routage par défaut. 

## <a name="set-up-the-power-bi-visualizations"></a>Configurer les visualisations Power BI

1. Connectez-vous à votre compte [Power BI](https://powerbi.microsoft.com/).

2. Accédez **Espace de travail** et sélectionnez l’espace de travail que vous avez défini lors de la création de la sortie du travail Stream Analytics. Ce didacticiel utilise **My Workspace**. 

3. Cliquez sur **Jeux de données**.

   Vous devez voir apparaître le jeu de données répertorié que vous avez indiqué lors de la création de la sortie du travail Stream Analytics. Ce didacticiel utilise **contosodataset**. (5 à 10 minutes peuvent être nécessaires pour que le jeu de données s’affiche la première fois.)

4. Sous **ACTIONS**, cliquez sur la première icône pour créer un rapport.

   ![Capture d’écran montrant l’espace de travail Power BI avec Actions et l’icône de rapport mis en surbrillance.](./media/tutorial-routing/power-bi-actions.png)

5. Créez un graphique en courbes pour afficher la température en temps réel et au fil du temps.

   a. Sur la page de création de rapports, ajoutez un graphique en courbes en cliquant sur l’icône de graphiques en courbes.

   ![Capture d’écran montrant les visualisations et les champs.](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   b. Sur le volet **Champs**, développez la table que vous avez indiquée lors de la création de la sortie du travail Stream Analytics. Ce didacticiel utilise **contosotable**.

   c. Faites glisser **EventEnqueuedUtcTime** vers **Axe** dans le volet **Visualisations**.

   d. Faites glisser **Température** vers **Valeurs**.

   Un graphique en courbes est créé. L’axe des abscisses affiche la date et l’heure du fuseau horaire UTC. Quant à l’axe des ordonnées, il affiche la température fournie par le capteur.

7. Créez un autre graphique en courbes pour afficher l’humidité en temps réel, au fil du temps. Pour configurer le deuxième graphique, suivez la même procédure que ci-dessus et placez **EventEnqueuedUtcTime** sur l’axe des abscisses et **Humidité** sur l’axe des ordonnées.

   ![Capture d’écran montrant le rapport Power BI final avec les deux graphiques.](./media/tutorial-routing/power-bi-report.png)

8. Cliquez sur **Enregistrer** pour enregistrer le rapport.

Vous devez être en mesure de voir les données sur les deux graphiques. Cela signifie :

   * Que le routage vers le point de terminaison par défaut fonctionne correctement.
   * Que le travail Azure Stream Analytics est correctement diffusé en continu.
   * Que la visualisation Power BI est correctement configurée.

Vous pouvez actualiser les graphiques pour afficher les données les plus récentes en cliquant sur le bouton Actualiser en haut de la fenêtre Power BI. 

## <a name="clean-up-resources"></a>Supprimer des ressources 

Si vous souhaitez supprimer toutes les ressources que vous avez créées, supprimez le groupe de ressources. Cette opération supprime toutes les ressources contenues dans le groupe. Dans ce cas, l’IoT Hub, l’espace de noms et la file d’attente Service Bus, l’application logique, le compte de stockage et le groupe de ressources lui-même sont supprimés. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Supprimer des ressources dans la visualisation Power BI

Connectez-vous à votre compte [Power BI](https://powerbi.microsoft.com/). Accédez à votre espace de travail. Ce didacticiel utilise **My Workspace**. Pour supprimer la visualisation Power BI, accédez à Jeux de données, puis cliquez sur l’icône de corbeille pour supprimer le jeu de données. Ce didacticiel utilise **contosodataset**. Lorsque vous supprimez le jeu de données, le rapport est également supprimé.

### <a name="clean-up-resources-using-azure-cli"></a>Supprimer des ressources à l’aide d’Azure CLI

Pour supprimer le groupe de ressources, utilisez la commande [az group delete](https://docs.microsoft.com/en-us/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```
### <a name="clean-up-resources-using-powershell"></a>Supprimer des ressources à l’aide de PowerShell

Pour supprimer le groupe de ressources, utilisez la commande [Remove-AzureRmResourceGroup](https://docs.microsoft.com/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup). $resourceGroup a été défini sur **ContosoIoTRG1** au début de ce didacticiel.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser le routage des messages pour acheminer des messages IoT Hub vers différentes destinations en effectuant les tâches suivantes.  

> [!div class="checklist"]
> * Configurer, à l’aide de PowerShell ou d’Azure CLI, les ressources de base : un IoT Hub, un compte de stockage, une file d’attente Service Bus et un appareil simulé.
> * Configurer les itinéraires et les points de terminaison dans IoT Hub pour le compte de stockage et la file d’attente Service Bus.
> * Créer une application logique qui est déclenchée et qui envoie un e-mail lors de l’ajout d’un message à la file d’attente Service Bus.
> * Télécharger et exécuter une application qui simule l’envoi de messages par un appareil IoT au Hub pour les différentes options de routage.
> * Créer une visualisation Power BI pour les données envoyées au point de terminaison par défaut.
> * Afficher les résultats...
> * ... dans la file d’attente Service Bus et les e-mails.
> * ... dans le compte de stockage.
> * ... dans la visualisation Power BI.

Passez au didacticiel suivant pour découvrir comment gérer l’état d’un appareil IoT. 

> [!div class="nextstepaction"]
[Configurer vos appareils à partir d’un service back-end](tutorial-device-twins.md)

 <!--  [Manage the state of a device](./tutorial-manage-state.md) -->