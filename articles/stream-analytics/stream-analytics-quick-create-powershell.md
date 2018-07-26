---
title: Créer un travail Stream Analytics à l’aide d’Azure PowerShell
description: Ce démarrage rapide décrit en détail comment utiliser le module Azure PowerShell pour déployer et exécuter un travail Azure Stream Analytics.
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 18903dfbe187de73a6edb14196fa29e02d35dbca
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185378"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Démarrage rapide : Créer un travail Stream Analytics à l’aide d’Azure PowerShell

Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à l’aide des lignes de commande ou des scripts PowerShell. Ce démarrage rapide décrit en détail comment utiliser le module Azure PowerShell pour déployer et exécuter un travail Azure Stream Analytics. 
 
Cet exemple de travail lit les données de diffusion en continu à partir d’un blob dans le service Stockage Blob Azure. Le fichier de données d’entrée utilisé dans ce démarrage rapide contient des données statiques à des fins d’illustration uniquement. Dans un scénario réel, vous utilisez la diffusion en continu des données d’entrée pour un travail Stream Analytics. Ensuite, le travail transforme les données à l’aide du langage de requête Stream Analytics pour calculer la température moyenne lorsqu’elle dépasse 100°. Enfin, ce travail écrit les événements de sortie résultants dans un autre fichier. 

## <a name="before-you-begin"></a>Avant de commencer

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).  

* Ce démarrage rapide requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour rechercher la version installée sur votre ordinateur local. Si vous avez besoin de procéder à une installation ou à une mise à niveau, consultez l’article [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 


## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Connect-AzureRmAccount`, puis entrez vos informations d’identification Azure dans le navigateur contextuel :

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

Après l’ouverture de session, si vous avez plusieurs abonnements, sélectionnez l’abonnement que vous souhaitez utiliser pour ce démarrage rapide en exécutant les applets de commande suivantes. Assurez-vous de remplacer <your subscription name> par le nom de votre abonnement :  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>Préparer les données d’entrée

Avant de définir le travail Stream Analytics, préparez les données qui seront configurées en tant qu’entrée pour le travail.

1. Téléchargez les [données d’exemple de capteur](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) à partir de GitHub. Cliquez sur le lien avec le bouton droit, puis sélectionnez **Save Link As...** (Enregistrer le lien sous) ou **Enregistrer la cible sous**.

2. Le bloc de code PowerShell ci-après exécute plusieurs commandes pour préparer les données d’entrée requises par le travail. Passez en revue les différentes sections pour comprendre le code. 

   1. Créez un compte de stockage standard à usage général à l’aide de l’applet de commande [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  Cet exemple crée un compte de stockage nommé mystorageaccount avec les options de stockage localement redondant (LRS) et de chiffrement des objets blob (activées par défaut).  

   2. Récupérez le contexte de compte de stockage `$storageAccount.Context` qui définit le compte de stockage à utiliser. Lorsque vous utilisez des comptes de stockage, référencez le contexte au lieu d’entrer les informations d’identification à plusieurs reprises. 

   3. Créez un conteneur de stockage à l’aide de l’applet de commande [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer), puis chargez les [données d’exemple de capteur](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) que vous avez téléchargées précédemment. 

   4. Copiez la clé de stockage générée par le code, puis collez cette clé dans les fichiers JSON pour la création ultérieure de l’entrée et de la sortie du travail de diffusion en continu.

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

Créez un travail Stream Analytics avec l’applet de commande [New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Cette applet de commande prend le nom du travail, le nom du groupe de ressources et la définition du travail comme paramètres. Le nom du travail peut correspondre à n’importe quel nom convivial identifiant votre travail. Ce nom ne peut contenir que des caractères alphanumériques, des traits d’union et des traits de soulignement, et doit comporter entre 3 et 63 caractères. La définition du travail est un fichier JSON qui contient les propriétés nécessaires à la création d’un travail. Sur votre ordinateur local, créez un fichier nommé `JobDefinition.json` et ajoutez-y les données JSON suivantes :

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

Ensuite, exécutez l’applet de commande `New-AzureRmStreamAnalyticsJob`. Veillez à remplacer la valeur de la variable `jobDefinitionFile` par le chemin dans lequel vous avez stocké le fichier JSON de définition du travail. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>Configurer l’entrée du travail

Ajoutez une entrée à votre travail à l’aide de l’applet de commande [New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Cette applet de commande prend le nom du travail, le nom de l’entrée du travail, le nom du groupe de ressources et la définition de l’entrée du travail comme paramètres. La définition de l’entrée du travail est un fichier JSON qui contient les propriétés nécessaires à la configuration de l’entrée du travail. Dans cet exemple, vous allez créer un stockage blob en guise d’entrée. 

Sur votre ordinateur local, créez un fichier nommé `JobInputDefinition.json` et ajoutez-y les données JSON ci-après. Veillez à remplacer la valeur de `accountKey` par la clé d’accès de votre compte de stockage qui correspond à la valeur stockée dans $storageAccountKey. 

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
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
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

Ensuite, exécutez l’applet de commande `New-AzureRmStreamAnalyticsInput` et veillez à remplacer la valeur de la variable `jobDefinitionFile` par le chemin dans lequel vous avez stocké le fichier JSON de définition de l’entrée du travail. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Configurer la sortie du travail

Ajoutez une sortie à votre travail à l’aide de l’applet de commande [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Cette applet de commande prend le nom du travail, le nom de la sortie du travail, le nom du groupe de ressources et la définition de la sortie du travail comme paramètres. La définition de la sortie du travail est un fichier JSON qui contient les propriétés nécessaires à la configuration de la sortie du travail. Cet exemple utilise le stockage blob en guise de sortie. 

Sur votre ordinateur local, créez un fichier nommé `JobOutputDefinition.json` et ajoutez-y les données JSON ci-après. Veillez à remplacer la valeur de `accountKey` par la clé d’accès de votre compte de stockage qui correspond à la valeur stockée dans $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
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

Ensuite, exécutez l’applet de commande `New-AzureRmStreamAnalyticsOutput`. Veillez à remplacer la valeur de la variable `jobOutputDefinitionFile` par le chemin dans lequel vous avez stocké le fichier JSON de définition de la sortie du travail. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Définir la requête de transformation

Ajoutez une transformation à votre travail à l’aide de l’applet de commande [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Cette applet de commande prend le nom du travail, le nom de la transformation du travail, le nom du groupe de ressources et la définition de la transformation du travail comme paramètres. Sur votre ordinateur local, créez un fichier nommé `JobTransformationDefinition.json` et ajoutez-y les données JSON ci-après. Le fichier JSON contient un paramètre de requête qui définit la requête de transformation :

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

Ensuite, exécutez l’applet de commande `New-AzureRmStreamAnalyticsTransformation`. Veillez à remplacer la valeur de la variable `jobTransformationDefinitionFile` par le chemin dans lequel vous avez stocké le fichier JSON de définition de la transformation du travail. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Démarrer le travail Stream Analytics et observer le résultat

Démarrez le travail à l’aide de l’applet de commande [Start-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Cette applet de commande prend le nom du travail, le nom du groupe de ressources, le mode de démarrage de la sortie et l’heure de démarrage comme paramètres. `OutputStartMode` accepte les valeurs de `JobStartTime`, `CustomTime` ou `LastOutputEventTime`. Pour plus d’informations sur les éléments auxquels ces valeurs font référence, consultez la section [Paramètres](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) de la documentation PowerShell. Dans cet exemple, définissez le mode sur `CustomTime` et fournissez une valeur pour l’élément `OutputStartTime`. 

Pour la valeur de temps, sélectionnez `2018-01-01`. Cette date de début est choisie car elle précède l’horodatage de l’événement à partir de l’échantillon de données. Après son exécution, l’applet de commande ci-après renvoie la valeur `True` en guise de sortie si le travail démarre. Dans le conteneur de stockage, un dossier de sortie est créé avec les données transformées. 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, le travail de streaming et toutes les ressources associées. La suppression du travail évite la facturation des unités de streaming consommées par le travail. Si vous envisagez d’utiliser le travail à l’avenir, vous pouvez vous abstenir de le supprimer et vous contenter de l’arrêter à ce stade. Si vous ne pensez pas continuer à utiliser ce travail, supprimez toutes les ressources créées pendant ce guide de démarrage rapide en exécutant l’applet de commande suivante :

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé un travail Stream Analytics simple. Pour savoir comment configurer d’autres sources d’entrée et effectuer une détection en temps réel, passez à l’article suivant :

> [!div class="nextstepaction"]
> [Détection des fraudes en temps réel à l’aide d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
