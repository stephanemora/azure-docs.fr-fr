---
title: Exécuter un package SSIS à l’aide de l’activité SSIS dans Azure Data Factory | Microsoft Docs
description: Cet article décrit comment exécuter un package SQL Server Integration Services (SSIS) à partir d’un pipeline Azure Data Factory à l’aide de l’activité SSIS.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 04/17/2018
ms.author: douglasl
ms.openlocfilehash: 6c8bbe7ef7f74638b978cdad5b59a89fd81d12a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
# <a name="run-an-ssis-package-using-the-ssis-activity-in-azure-data-factory"></a>Exécuter un package SSIS à l’aide de l’activité SSIS dans Azure Data Factory
Cet article décrit comment exécuter un package SSIS à partir d’un pipeline Azure Data Factory à l’aide d’une activité SSIS. 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. L’activité SSIS n’est pas disponible dans la version 1 du service Data Factory, qui est en disponibilité générale. Pour découvrir une autre méthode d’exécuter un package SSIS avec la version 1 du service Data Factory, consultez [Exécuter des packages SSIS à l’aide de l’activité de procédure stockée dans la version 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Prérequis


### <a name="azure-sql-database"></a>Base de données SQL Azure 
La procédure pas à pas dans cet article utilise une base de données Azure SQL qui héberge le catalogue SSIS. Vous pouvez également utiliser Azure SQL Managed Instance (préversion).

## <a name="create-an-azure-ssis-integration-runtime"></a>Créer un runtime d’intégration Azure-SSIS
Créez un runtime d’intégration Azure-SSIS si vous n’en avez pas en suivant les instructions pas à pas fournies dans le [Didacticiel : Déployer des packages SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Interface utilisateur de Data Factory (portail Azure)
Dans cette section, vous utilisez l’interface utilisateur de Data Factory pour créer un pipeline Data Factory avec une activité SSIS qui exécute un package SSIS.

### <a name="create-a-data-factory"></a>Créer une fabrique de données
La première étape consiste à créer une fabrique de données à l’aide du portail Azure. 

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Accédez au [portail Azure](https://portal.azure.com). 
3. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**. 
      
     ![Page de nouvelle fabrique de données](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
     ![Nom indisponible - erreur](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
    Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
4. Sélectionnez **V2 (préversion)** pour la **version**.
5. Sélectionnez **l’emplacement** de la fabrique de données. Seuls les emplacements pris en charge par Data Factory sont affichés dans la liste déroulante. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements.
6. Sélectionnez **Épingler au tableau de bord**.     
7. Cliquez sur **Créer**.
8. Sur le tableau de bord, vous voyez la mosaïque suivante avec l’état : **Déploiement de fabrique de données**. 

    ![mosaïque déploiement de fabrique de données](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
    ![Page d'accueil Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Cliquez sur la vignette **Créer et surveiller** pour lancer l’application d’interface utilisateur (IU) d’Azure Data Factory dans un onglet séparé. 

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Créer un pipeline avec une activité SSIS
Lors de cette étape, vous utilisez l’interface utilisateur de Data Factory pour créer un pipeline. Vous ajoutez une activité SSIS au pipeline et la configurez pour exécuter le package SSIS. 

1. Dans la page de prise en main, cliquez sur **Créer un pipeline** : 

    ![Page de prise en main](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Dans la boîte à outils **Activités**, développez **Général**, puis faites un glisser-déplacer de l’activité **Exécuter le package SSIS** vers la surface du concepteur de pipeline. 

   ![Faire glisser l’activité SSIS vers la surface du concepteur](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Sous l’onglet **Général** des propriétés de l’activité SSIS, fournissez un nom et une description pour l’activité. Définissez les valeurs de délai d’attente et de nouvelle tentative facultatives.

    ![Définissez les propriétés sous l’onglet Général](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Sous l’onglet **Paramètres** des propriétés de l’activité SSIS, sélectionnez le runtime d’intégration Azure-SSIS associé à la base de données `SSISDB` sur laquelle le package est déployé. Fournissez le chemin du package dans la base de données `SSISDB` en utilisant le format `<folder name>/<project name>/<package name>.dtsx`. Si vous le souhaitez, vous pouvez spécifier une exécution 32 bits et un niveau de journalisation prédéfini ou personnalisé et fournir un chemin d’environnement en utilisant le format `<folder name>/<environment name>`.

    ![Définir les propriétés sous l’onglet Paramètres](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Pour valider la configuration du pipeline, cliquez sur **Valider** dans la barre d’outils. Pour fermer le **Rapport de validation de pipeline**, cliquez sur **>>**.

6. Publiez le pipeline dans Data Factory en cliquant sur le bouton **Publier tout**. 

### <a name="run-and-monitor-the-pipeline"></a>Exécuter et surveiller le pipeline
Dans cette section, vous déclenchez une exécution du pipeline puis vous la surveillez. 

1. Pour déclencher une exécution de pipeline, cliquez sur **Déclencher** dans la barre d’outils, puis sur **Déclencher maintenant**. 

    ![Déclencher maintenant](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Dans la fenêtre **Exécution du pipeline**, sélectionnez **Terminer**. 
3. Basculez vers l’onglet **Surveiller** sur la gauche. Vous voyez l’exécution de pipeline et son état, ainsi que d’autres informations (telles que l’heure de début d’exécution). Pour actualiser la vue, cliquez sur **Actualiser**.

    ![Exécutions de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Cliquez sur le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Une seule exécution d’activité est affichée, étant donné que le pipeline n’a qu’une seule activité (l’activité SSIS).

    ![Exécutions d’activités](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

4. Vous pouvez exécuter la **requête** suivante par rapport à la base de données SSISDB dans votre serveur SQL Azure pour vérifier que le package s’est exécuté. 

    ```sql
    select * from catalog.executions
    ```

    ![Vérifier les exécutions de package](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Vous pouvez également créer un déclencheur planifié pour votre pipeline afin que le pipeline s’exécute selon une planification (horaire, quotidienne, et ainsi de suite). Pour obtenir un exemple, consultez [Créer une fabrique de données - Interface utilisateur de Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell
Dans cette section, vous utilisez Azure PowerShell pour créer un pipeline Data Factory avec une activité SSIS qui exécute un package SSIS. 

Installez les modules Azure PowerShell les plus récents en suivant les instructions décrites dans [Comment installer et configurer Azure PowerShell](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Créer une fabrique de données
Vous pouvez utiliser la fabrique de données qui a le runtime d’intégration Azure-SSIS ou créer une fabrique de données distincte. La procédure suivante décrit les étapes permettant de créer une fabrique de données. Vous créez un pipeline avec une activité SSIS dans cette fabrique de données. L’activité SSIS exécute votre package SSIS. 

1. Définissez une variable pour le nom du groupe de ressources que vous utiliserez ultérieurement dans les commandes PowerShell. Copiez le texte de commande suivant dans PowerShell, spécifiez un nom pour le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) entre des guillemets doubles, puis exécutez la commande. Par exemple : `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Affectez une valeur différente à la variable `$ResourceGroupName` et exécutez à nouveau la commande
2. Pour créer le groupe de ressources Azure, exécutez la commande suivante : 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Affectez une valeur différente à la variable `$ResourceGroupName` et exécutez à nouveau la commande. 
3. Définissez une variable pour le nom de la fabrique de données. 

    > [!IMPORTANT]
    >  Mettez à jour le nom de la fabrique de données afin qu’il soit globalement unique. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Pour créer la fabrique de données, exécutez l’applet de commande suivante **Set-AzureRmDataFactoryV2**, à l’aide des propriétés Location et ResourceGroupName à partir de la variable $ResGrp : 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

Notez les points suivants :

* Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez l’erreur suivante, changez le nom, puis réessayez.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pour créer des instances de fabrique de données, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être un membre des rôles **contributeur** ou **propriétaire**, ou un **administrateur** de l’abonnement Azure.
* À l’heure actuelle, Data Factory version 2 vous permet de créer des fabriques de données uniquement dans les régions Est des États-Unis, Est des États-Unis 2, Europe de l’Ouest et Asie du Sud-Est. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Créer un pipeline avec une activité SSIS 
Dans cette étape, vous allez créer un pipeline avec une activité SSIS. L’activité exécute votre package SSIS. 

1. Créez un fichier JSON nommé **RunSSISPackagePipeline.json** dans le dossier **C:\ADF\RunSSISPackage** avec un contenu similaire à l’exemple suivant :

    > [!IMPORTANT]
    > Remplacez les noms d’objets, les descriptions et les chemins, ainsi que les valeurs de propriété et de paramètre, les mots de passe et autres valeurs de variable avant d’enregistrer le fichier. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  Dans Azure PowerShell, accédez au dossier `C:\ADF\RunSSISPackage`.

3. Pour créer le pipeline **RunSSISPackagePipeline**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Voici l'exemple de sortie :

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Créer une exécution du pipeline
Utilisez l’applet de commande **Invoke-AzureRmDataFactoryV2Pipeline** pour exécuter le pipeline. L’applet de commande renvoie l’ID d’exécution du pipeline pour permettre une surveillance ultérieure.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

Exécutez le script PowerShell suivant afin de vérifier continuellement l’état de l’exécution du pipeline jusqu’à la fin de la copie des données. Copiez/collez le script suivant dans la fenêtre PowerShell et appuyez sur ENTRÉE. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Créer un déclencheur
À l’étape précédente, vous avez exécuté le pipeline à la demande. Vous pouvez également créer un déclencheur de planification pour exécuter le pipeline d’après une planification (horaire, quotidienne, etc.).

1. Créez un fichier JSON nommé **MyTrigger.json** dans le dossier **C:\ADF\RunSSISPackage** avec le contenu suivant : 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. Dans **Azure PowerShell**, basculez vers le dossier **C:\ADF\RunSSISPackage**.
3. Exécutez l’applet de commande **Set-AzureRmDataFactoryV2Trigger** pour créer le déclencheur. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Par défaut, le déclencheur est arrêté. Démarrez-le avec l’applet de commande **Start-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Vérifiez que le déclencheur est démarré en exécutant l’applet de commande **Get-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. Exécutez la commande ci-dessous après l’heure suivante. Par exemple, si l’heure actuelle est 15h25 UTC, exécutez la commande à 16h00 UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    Vous pouvez exécuter la requête suivante par rapport à la base de données SSISDB dans votre serveur Azure SQL pour vérifier que le package s’est exécuté. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Étapes suivantes
Vous pouvez également surveiller le pipeline à l’aide du portail Azure. Pour obtenir des instructions pas à pas, consultez [Surveiller le pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
