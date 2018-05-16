---
title: Créer un travail Stream Analytics à l’aide d’Azure PowerShell
description: Ce démarrage rapide décrit en détail comment utiliser le module Azure PowerShell pour déployer et exécuter un travail Azure Stream Analytics.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 0be8cee9e6c7874282f4e8f43f75fa7f2490c14e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Démarrage rapide : Créer un travail Stream Analytics à l’aide d’Azure PowerShell

Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à l’aide des lignes de commande ou des scripts PowerShell. Ce démarrage rapide décrit en détail comment utiliser le module Azure PowerShell pour déployer et exécuter un travail Azure Stream Analytics.

## <a name="before-you-begin"></a>Avant de commencer

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).  

* Ce démarrage rapide requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour rechercher la version installée sur votre ordinateur local. Si vous avez besoin de procéder à une installation ou à une mise à niveau, consultez l’article [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Le scénario de cet article décrit la lecture de données à partir du stockage Blob, la transformation des données et leur réécriture dans un autre conteneur au sein du même stockage Blob.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Connect-AzureRmAccount` et entrez vos informations d’identification Azure dans le navigateur contextuel. Après l’ouverture de session, si vous avez plusieurs abonnements, sélectionnez l’abonnement que vous souhaitez utiliser pour ce démarrage rapide en exécutant les applets de commande suivantes. Assurez-vous de remplacer <your subscription> par le nom de votre abonnement :  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>Préparer les données d’entrée

Avant de définir le travail Stream Analytics, vous devez préparer les données qui seront configurées en tant qu’entrée pour le travail. Exécutez les opérations suivantes pour préparer les données d’entrée requises pour le travail : 

1. Téléchargez les [données d’exemple de capteur](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) à partir de GitHub.  

2. Créer un compte de stockage standard à usage général avec réplication LRS à l’aide de l’applet de commande [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount)  

3. Obtenez le contexte du compte de stockage qui définit le compte de stockage à utiliser. Lorsque vous utilisez des comptes de stockage, référencez le contexte au lieu d’entrer les informations d’identification à plusieurs reprises. Cet exemple crée un compte de stockage nommé mystorageaccount avec les options de stockage localement redondant (LRS) et de chiffrement des objets blob (activées par défaut).  

4. Ensuite créez un conteneur à l’aide de [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer), définissez les autorisations de l’objet blob pour autoriser l’accès public aux fichiers et téléchargez les [données d’exemple de capteur](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) que vous avez téléchargées précédemment. 

Ces étapes nécessitent l’exécution du script PowerShell suivant :

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

Créez un travail Stream Analytics avec l’applet de commande [New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Cette applet de commande prend le nom du travail, le nom du groupe de ressources et la définition du travail comme paramètres. Le nom du travail peut être n’importe quel nom qui identifie votre travail. Le nom du travail Stream Analytics peut contenir uniquement des caractères alphanumériques, des traits d’union et des traits de soulignement, et doit avoir entre 3 et 63 caractères. Une définition de travail est un fichier JSON qui contient les propriétés nécessaires pour créer un travail. Sur votre ordinateur local, créez un fichier nommé « JobDefinition.json » et ajoutez-y les données JSON suivantes :

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

Ensuite, exécutez l’applet de commande New-AzureRMStreamAnalyticsJob et veillez à remplacer la valeur de la variable jobDefinitionFile par le chemin d’accès où vous avez stocké le fichier JSON de définition du travail. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>Configurer l’entrée du travail

Ajoutez une entrée à votre travail à l’aide de l’applet de commande [New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Cette applet de commande prend le nom du travail, le nom de l’entrée du travail, le nom du groupe de ressources et la définition de l’entrée du travail comme paramètres. La définition de l’entrée de travail est un fichier JSON qui contient les propriétés nécessaires à la configuration de l’entrée du travail. Dans cet exemple, vous allez créer une entrée de type stockage d’objets blob. Sur votre ordinateur local, créez un fichier nommé « JobInputDefinition.json » et ajoutez-y les données JSON suivantes. Veillez à remplacer la valeur de **accountKey** par la clé d’accès de votre compte de stockage qui est la valeur stockée dans la valeur $storageAccountKey. 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Ensuite, exécutez l’applet de commande New-AzureRMStreamAnalyticsInput et veillez à remplacer la valeur de la variable jobDefinitionFile par le chemin d’accès où vous avez stocké le fichier JSON de définition de l’entrée de travail. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Configurer la sortie du travail

Ajoutez une sortie à votre travail à l’aide de l’applet de commande [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Cette applet de commande prend le nom du travail, le nom de la sortie du travail, le nom du groupe de ressources et la définition de la sortie du travail comme paramètres. La définition de la sortie de travail est un fichier JSON qui contient les propriétés nécessaires à la configuration de la sortie du travail. Dans cet exemple, vous allez créer une sortie de type stockage d’objets blob. Sur votre ordinateur local, créez un fichier nommé « JobOutputDefinition.json » et ajoutez-y les données JSON suivantes. Veillez à remplacer la valeur de **accountKey** par la clé d’accès de votre compte de stockage qui est la valeur stockée dans la valeur $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Ensuite, exécutez l’applet de commande New-AzureRMStreamAnalyticsOutput et veillez à remplacer la valeur de la variable jobOutputDefinitionFile par le chemin d’accès où vous avez stocké le fichier JSON de définition de la sortie de travail. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Définir la requête de transformation

Ajoutez une transformation à votre travail à l’aide de l’applet de commande [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Cette applet de commande prend le nom du travail, le nom de la transformation du travail, le nom du groupe de ressources et la définition de la transformation du travail comme paramètres. Sur votre ordinateur local, créez un fichier nommé « JobTransformationDefinition.json » et ajoutez-y les données JSON suivantes. Le fichier JSON contient un paramètre de requête qui définit la requête de transformation :

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

Ensuite, exécutez l’applet de commande New-AzureRMStreamAnalyticsTransformation et veillez à remplacer la valeur de la variable jobTransformationDefinitionFile par le chemin d’accès où vous avez stocké le fichier JSON de définition de la transformation du travail. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Démarrer le travail Stream Analytics et observer le résultat

Démarrez le travail à l’aide de l’applet de commande [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Cette applet de commande prend le nom du travail, le nom du groupe de ressources, le mode de démarrage de la sortie et l’heure de démarrage comme paramètres. OUtpputStartMode accepte deux valeurs JobStartTime, CustomTime ou LastOutputEventTime. Pour savoir à quoi fait référence chacune de ces valeurs, consultez la section [Paramètres](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) dans la documentation PowerShell. Dans cet exemple, vous pouvez spécifier le mode CustomTime et fournir une valeur pour OutputStartTime. 

Pour la valeur de temps, sélectionnez un jour avant la date de téléchargement du fichier dans le stockage Blob, car l’heure à laquelle le fichier a été téléchargé est antérieure à l’heure actuelle. Après avoir exécuté l’applet de commande suivante, elle renvoie la sortie « True » si le travail démarre. Un conteneur nommé « myoutputcontainer » est créé dans le compte de stockage avec les données transformées. 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, le travail de streaming et toutes les ressources associées. La suppression du travail évite la facturation des unités de streaming consommées par le travail. Si vous envisagez d’utiliser le travail à l’avenir, vous pouvez l’arrêter et le redémarrer plus tard lorsque vous en avez besoin. Si vous ne pensez pas continuer à utiliser ce travail, supprimez toutes les ressources créées pendant ce guide de démarrage rapide en exécutant l’applet de commande suivante :

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé un travail Stream Analytics simple. Pour savoir comment configurer d’autres sources d’entrée et effectuer une détection en temps réel, passez à l’article suivant :

> [!div class="nextstepaction"]
> [Détection des fraudes en temps réel à l’aide d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
