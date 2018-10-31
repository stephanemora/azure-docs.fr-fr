---
title: Envoyer des métriques de système d’exploitation invité vers le magasin de métriques d’Azure Monitor à l’aide d’un modèle Resource Manager pour un groupe de machines virtuelles identiques Windows
description: Envoyer des métriques de système d’exploitation invité vers le magasin de métriques d’Azure Monitor à l’aide d’un modèle Resource Manager pour un groupe de machines virtuelles identiques Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 7b600bd699ce7f9e4a6c7cba1a41b6bdece16bf0
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343722"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Envoyer des métriques de système d’exploitation invité vers le magasin de métriques d’Azure Monitor à l’aide d’un modèle Resource Manager pour un groupe de machines virtuelles identiques Windows

L’[extension Windows Azure Diagnostics](azure-diagnostics.md) (WAD) d’Azure Monitor vous permet de collecter des métriques et des journaux à partir du système d’exploitation invité (SE invité) exécuté dans le cadre d’une machine virtuelle, d’un service cloud ou d’un cluster Service Fabric.  L’extension peut envoyer des données de télémétrie vers de nombreux emplacements différents répertoriés dans l’article précédemment lié.  

Cet article décrit le processus pour envoyer les métriques de performance du SE invité d’un groupe de machines virtuelles identiques Windows vers le magasin de données d’Azure Monitor. À partir de WAD version 1.11, vous pouvez écrire des métriques directement dans le magasin de métriques d’Azure Monitor où les métriques standard de la plateforme sont déjà collectées. En les stockant dans cet emplacement, vous avez accès aux mêmes actions que celles disponibles pour les métriques de la plateforme.  Ces actions sont les suivantes : génération d’alertes en temps quasi réel, création de graphiques, routage, accès à partir de l’API REST et bien plus encore.  Avant, l’extension WAD écrivait dans Stockage Azure, mais pas dans le magasin de données d’Azure Monitor.  

Si vous ne connaissez pas les modèles Resource Manager, apprenez-en plus sur les [déploiements de modèle](../azure-resource-manager/resource-group-overview.md), leur structure et leur syntaxe.  

## <a name="pre-requisites"></a>Conditions préalables

- Votre abonnement doit être inscrit auprès de [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Vous devez avoir installé [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1), ou vous pouvez utiliser [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurer Azure Monitor en tant que récepteur de données 
L’extension Azure Diagnostics utilise une fonctionnalité appelée « récepteurs de données » pour acheminer les métriques et les journaux vers différents emplacements.  Les étapes suivantes montrent comment utiliser un modèle Resource Manager et PowerShell pour déployer une machine virtuelle à l’aide du nouveau récepteur de données « Azure Monitor ». 

## <a name="author-resource-manager-template"></a>Créer un modèle Resource Manager 
Pour cet exemple, vous pouvez utiliser un exemple de modèle rendu public. Les modèles de démarrage se trouvent dans https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale  

- **Azuredeploy.json** est un modèle Resource Manager préconfiguré pour le déploiement d’un groupe de machines virtuelles identiques

- **Azuredeploy.parameters.json** est un fichier de paramètres qui stocke des informations telles que le nom d’utilisateur et le mot de passe que vous souhaitez définir pour votre machine virtuelle. Pendant le déploiement, le modèle Resource Manager utilise les paramètres définis dans ce fichier. 

Téléchargez et enregistrez les deux fichiers en local. 

###  <a name="modify-azuredeployparametersjson"></a>Modifier le fichier azuredeploy.parameters.json
Ouvrez le fichier *azuredeploy.parameters.json* 

- Indiquez une référence de machine virtuelle, **vmSKU**, que vous souhaitez déployer (nous recommandons Standard_D2_v3) 
- Spécifiez la version **windowsOSVersion** souhaitée pour votre groupe de machines virtuelles identiques (nous recommandons 2016-Datacenter) 
- Donnez un nom à la ressource de groupe de machines virtuelles identiques à déployer à l’aide d’une propriété **vmssName**. Par exemple, *VMSS-WAD-TEST*.    
- Spécifiez le nombre souhaité de machines virtuelles à exécuter sur le groupe de machines virtuelles identiques à l’aide de la propriété **instanceCount**
- Entrez les valeurs **adminUsername** et **adminPassword** pour le groupe de machines virtuelles identiques. Ces paramètres sont utilisés pour l’accès à distance aux machines virtuelles du groupe identique. Ces paramètres sont utilisés pour l’accès à distance à la machine virtuelle. N’UTILISEZ PAS les paramètres de ce modèle pour éviter un accès non autorisé à votre machine virtuelle. Les bots recherchent sur Internet des noms d’utilisateur et mots de passe dans les référentiels GitHub publics. Avec ces valeurs par défaut, ils sont susceptibles de se transformer en machines virtuelles de test. 


###  <a name="modify-azuredeployjson"></a>Modifier le fichier azuredeploy.json
Ouvrez le fichier *azuredeploy.json* 

Ajoutez une variable pour contenir les informations de compte de stockage dans le modèle Resource Manager. Vous devez toujours fournir un compte de stockage dans le cadre de l’installation de l’extension Diagnostics. Les journaux et/ou les compteurs de performances spécifiés dans le fichier config de diagnostics sont écrits dans le compte de stockage spécifié en plus d’être envoyés vers le magasin de métriques d’Azure Monitor. 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
 ```
 
Recherchez la définition de Groupe de machines virtuelles identiques dans la section des ressources et ajoutez la section « identity » à la configuration. Cela garantit qu’Azure lui assigne une identité système. Cette étape garantit que les machines virtuelles du groupe identique peuvent transmettre leurs propres métriques invité à Azure Monitor.  

```json
  { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
 ```

Dans la ressource du groupe de machines virtuelles identiques, recherchez la section **virtualMachineProfile**. Ajoutez un nouveau profil nommé **extensionsProfile** pour gérer les extensions.  


Dans **extensionProfile**, ajoutez une nouvelle extension au modèle, comme illustré dans la **section VMSS-WAD-extension**.  Cette section correspond à l’extension des identités managées pour ressources Azure qui garantit que les métriques émises sont acceptées par Azure Monitor. Le champ du **nom** peut contenir n’importe quel nom. 

Le code ci-après émanant de l’extension MSI ajoute également l’extension Diagnostics et la configuration en tant que ressource d’extension à la ressource du groupe de machines virtuelles identiques. N’hésitez pas à ajouter/supprimer des compteurs de performances si nécessaire. 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identites for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Ajoutez une propriété dependsOn au compte de stockage pour vous assurer qu’il est créé dans le bon ordre. 
```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
 ```

Créez un compte de stockage s’il n’en existe pas déjà un dans le modèle.  
```json
"resources": [
  // add this code    
  {
     "type": "Microsoft.Storage/storageAccounts",
     "name": "[variables('storageAccountName')]",
     "apiVersion": "2015-05-01-preview",
     "location": "[resourceGroup().location]",
     "properties": {
       "accountType": "Standard_LRS"
      }
  },
  // end added code
  { 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Enregistrez et fermez les deux fichiers. 

## <a name="deploy-the-resource-manager-template"></a>Déployer le modèle Resource Manager 

> [!NOTE]
> Vous devez exécuter la version 1.5 ou supérieure de l’extension Azure Diagnostics ET la valeur de la propriété « autoUpgradeMinorVersion » doit être définie sur *true* dans votre modèle Resource Manager.  Azure charge alors l’extension appropriée lorsqu’il démarre la machine virtuelle. Si ces paramètres ne figurent pas dans votre modèle, modifiez-les et redéployez le modèle. 


Pour déployer le modèle Resource Manager, vous allez utiliser Azure PowerShell.  

1. Lancement de PowerShell 
1. Connectez-vous à Azure à l’aide de `Login-AzureRmAccount`
1. Obtenez la liste de vos abonnements à l’aide de `Get-AzureRmSubscription`
1. Définissez l’abonnement dans lequel vous allez créer/mettre à jour la machine virtuelle 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Créez un nouveau groupe de ressources pour la machine virtuelle déployée, exécutez la commande ci-dessous 

   ```PowerShell
    New-AzureRmResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > N’oubliez pas d’utiliser une région Azure dans laquelle les mesures personnalisées sont activées. Consultez les documents suivants : 
 
1. Exécutez les commandes suivantes pour déployer la machine virtuelle avec le  
   > [!NOTE] 
   > Si vous souhaitez mettre à jour un groupe identique existant, ajoutez simplement *-Mode Incremental* à la fin de la commande suivante. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Lorsque votre déploiement a abouti, vous devez être en mesure de retrouver le groupe de machines virtuelles identiques dans le portail Azure, et il doit transmettre des métriques à Azure Monitor. 

   > [!NOTE] 
   > Vous pouvez rencontrer des erreurs liées à la vmSkuSize sélectionnée. Si cela se produit, revenez à votre fichier azuredeploy.json et mettez à jour la valeur par défaut du paramètre vmSkuSize. Dans ce cas, nous vous recommandons d’essayer « Standard_DS1_v2 »). 


## <a name="chart-your-metrics"></a>Représenter vos métriques graphiquement 

1. Connectez-vous au portail Azure. 

1. Dans le menu de gauche, cliquez sur **Surveiller** 

1. Dans la page Surveiller, cliquez sur **Métriques**. 

   ![Page Métriques](./media/metrics-store-custom-rest-api/metrics.png) 

1. Modifiez la période d’agrégation afin d’indiquer **30 dernières minutes**.  

1. Dans la liste déroulante des ressources, sélectionnez le groupe de machines virtuelles identiques que vous venez de créer.  

1. Dans la liste déroulante des espaces de noms, sélectionnez **azure.vm.windows.guest** 

1. Dans la liste déroulante des métriques, sélectionnez **Memory\%Committed Bytes in Use** (Mémoire\Octets validés utilisés).  

Vous pouvez ensuite également choisir d’utiliser les dimensions de cette métrique afin de la représenter graphiquement pour une machine virtuelle spécifique du groupe identique, ou de tracer chaque machine virtuelle du groupe identique. 



## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [métriques personnalisées](metrics-custom-overview.md).

