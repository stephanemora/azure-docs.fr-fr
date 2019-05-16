---
title: Exécuter un package SSIS avec l’activité Exécuter le Package SSIS - Azure | Microsoft Docs
description: Cet article décrit comment exécuter un package SQL Server Integration Services (SSIS) dans un pipeline Azure Data Factory à l’aide de l’activité Exécuter le Package SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/19/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 7287dc2fccf461cf23c45202336e3d92bc5a40aa
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259701"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Exécuter un package SSIS avec l’activité Exécuter le Package SSIS dans Azure Data Factory
Cet article décrit comment exécuter un package SSIS dans un pipeline Azure Data Factory (ADF) à l’aide de l’activité Exécuter le Package SSIS. 

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Créez un runtime d’intégration Azure-SSIS si vous n’en avez pas encore en suivant les instructions pas à pas fournies dans le [Tutoriel : Déployer des packages SSIS vers Azure](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Exécuter un package dans le portail Azure
Dans cette section, vous utilisez l’interface utilisateur (IU)/app ADF pour créer un pipeline Data Factory avec une activité Execute SSIS Package qui exécute un package SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Créer un pipeline avec une activité Exécuter le Package SSIS
Lors de cette étape, vous utilisez l’IU/app ADF pour créer un pipeline. Vous ajoutez une activité Exécuter le Package SSIS au pipeline et la configurez pour exécuter votre package SSIS. 

1. Dans la page d’accueil/de présentation du fichier de définition d’application sur le portail Azure, cliquez sur la mosaïque **Créer et surveiller** pour lancer l’IU/l’application ADF dans un onglet séparé. 

   ![Page d’accueil Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Sur la page **Prise en main**, cliquez sur **Créer un pipeline** : 

   ![Page de prise en main](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. Dans la boîte à outils **Activités**, développez **Général**, puis glissez-déposez l’activité **Exécuter le package SSIS** vers la surface du concepteur de pipeline. 

   ![Faire glisser une activité Execute SSIS Package jusqu’à l’aire du concepteur](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Sous l’onglet **Général** d’Exécuter le Package SSIS, fournissez un nom et une description pour l’activité. Définissez les valeurs de délai d’attente et de nouvelle tentative facultatives.

   ![Définissez les propriétés sous l’onglet Général](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Sous l’onglet **Paramètres** de l’activité Exécuter le Package SSIS, sélectionnez votre runtime d’intégration Azure-SSIS associé à la base de données SSISDB sur laquelle le package est déployé. Si votre package utilise l’authentification Windows pour accéder à des magasins de données, par exemple, des partages de serveurs SQL/fichiers en local, Azure Files, etc., vérifiez le **l’authentification Windows** case à cocher et entrez le nom de domaine/nom d’utilisateur/mot de passe pour votre package exécution. Si votre package a besoin d’un runtime 32 bits pour fonctionner, cochez la case **Runtime 32 bits**. Concernant **Niveau de journalisation**, sélectionnez un périmètre prédéfini de journalisation pour l’exécution de votre package. Cochez la case **Personnalisé** si vous préférez entrer un nom de journalisation personnalisé. Lorsque votre Azure-SSIS IR est en cours d’exécution et que la case **Entrées manuelles** n’est pas cochée, vous pouvez parcourir et sélectionner vos dossiers/projets/packages/environnements existants dans SSISDB. Cliquez sur le bouton **Actualiser** pour récupérer vos nouveaux dossiers/projets/packages/environnements ajoutés dans SSISDB afin qu’ils soient sélectionnables lors de la navigation. 

   ![Définir les propriétés sous l’onglet Paramètres - Automatique](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Lorsque votre IR Azure-SSIS ne fonctionne pas ou le **entrées manuellement** case à cocher est activée, vous pouvez entrer des chemins d’accès de votre package et l’environnement à partir de SSISDB directement dans les formats suivants : `<folder name>/<project name>/<package name>.dtsx` et `<folder name>/<environment name>`.

   ![Définir les propriétés sous l’onglet Paramètres - Manuel](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

5. Sous l’onglet **Paramètres SSIS** de l’activité Exécuter un package SSIS, alors que votre Azure-SSIS IR s’exécute et que la case **Entrées manuelles** de l’onglet **Paramètres** est décochée, les paramètres SSIS existants dans votre projet ou package sélectionné depuis SSISDB sont affichés pour que vous puissiez leur assigner des valeurs. Vous pouvez également les entrer un par un afin de leur assigner des valeurs manuellement - assurez-vous qu’elles existent et qu’elles sont correctement saisies pour que l’exécution de votre package soit réussie. Vous pouvez ajouter du contenu dynamique à leurs valeurs à l’aide d’expressions, fonctions, les variables système ADF et paramètres de pipeline ADF/variables. Vous pouvez également utiliser les secrets stockés dans votre Azure Key Vault (AKV) en tant que leurs valeurs. Pour ce faire, cliquez sur le **AZURE KEY VAULT** case à cocher en regard du paramètre pertinentes, sélectionner/Modifier votre service AKV lié existant ou créez-en un, puis sélectionnez la nom/version du secret pour votre valeur de paramètre.  Lorsque vous créez/modifiez votre service AKV lié, vous pouvez sélectionner et modifier votre coffre de clés AZURE existant ou créez-en un, mais accordez l’accès d’identité ADF géré à votre coffre de clés AZURE si vous n'avez pas déjà fait. Vous pouvez également entrer vos secrets directement dans le format suivant : `<AKV linked service name>/<secret name>/<secret version>`.

   ![Définir les propriétés sous l’onglet Paramètres SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Sur le **gestionnaires de connexions** onglet pour l’activité exécuter le Package SSIS, lors de l’exécution de votre IR Azure-SSIS et le **entrées manuellement** case à cocher sur **paramètres** onglet est désactivée, les gestionnaires de connexions existant dans votre package/projet sélectionné à partir de SSISDB seront affichera pour vous permettre d’affecter des valeurs à leurs propriétés. Sinon, vous pouvez entrer les une par une pour affecter manuellement des valeurs à leurs propriétés : Veuillez vous assurer qu’elles existent et sont entrés correctement pour l’exécution de votre package réussisse. Vous pouvez ajouter du contenu dynamique à leurs valeurs de propriété à l’aide d’expressions, fonctions, les variables système ADF et paramètres de pipeline ADF/variables. Vous pouvez également utiliser les secrets stockés dans votre Azure Key Vault (AKV) en tant que leurs valeurs de propriété. Pour ce faire, cliquez sur le **AZURE KEY VAULT** case à cocher en regard de la propriété pertinente, sélectionner/Modifier votre service AKV lié existant ou créez-en un, puis sélectionnez la nom/version du secret pour votre valeur de propriété.  Lorsque vous créez/modifiez votre service AKV lié, vous pouvez sélectionner et modifier votre coffre de clés AZURE existant ou créez-en un, mais accordez l’accès d’identité ADF géré à votre coffre de clés AZURE si vous n'avez pas déjà fait. Vous pouvez également entrer vos secrets directement dans le format suivant : `<AKV linked service name>/<secret name>/<secret version>`.

   ![Définir les propriétés sous l’onglet Gestionnaires de connexions](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Sous l’onglet **Substitutions de propriété** pour Exécuter le Package SSIS, vous pouvez entrer les chemins des propriétés existantes dans votre package sélectionné un à un à partir de SSISDB un par un pour leur attribuer des valeurs manuellement - Veuillez vous assurer qu’elles existent et qu’elles sont correctement entrées pour que l’exécution du paquet réussisse, par exemple pour remplacer la valeur de votre variable utilisateur, entrez son chemin d’accès dans le format suivant : `\Package.Variables[User::YourVariableName].Value`. Vous pouvez également ajouter du contenu dynamique à leurs valeurs à l’aide d’expressions, de fonctions, de variables système ADF et de paramètres/variables de pipeline ADF.

   ![Définir les propriétés sous l’onglet Substitutions de propriété](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

8. Pour valider la configuration du pipeline, cliquez sur **Valider** dans la barre d’outils. Pour fermer le **Rapport de validation de pipeline**, cliquez sur **>>**.

9. Publiez le pipeline dans ADF en cliquant sur le bouton **Publier tout**. 

### <a name="run-the-pipeline"></a>Exécuter le pipeline
À cette étape, vous déclenchez une exécution du pipeline. 

1. Pour déclencher une exécution de pipeline, cliquez sur **Déclencher** dans la barre d’outils, puis sur **Déclencher maintenant**. 

   ![Déclencher maintenant](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Dans la fenêtre **Exécution du pipeline**, sélectionnez **Terminer**. 

### <a name="monitor-the-pipeline"></a>Surveiller le pipeline

1. Basculez vers l’onglet **Surveiller** sur la gauche. Vous voyez l’exécution de pipeline et son état, ainsi que d’autres informations (telles que l’heure de début d’exécution). Pour actualiser la vue, cliquez sur **Actualiser**.

   ![Exécutions de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Cliquez sur le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Une seule exécution d’activité est affichée, étant donné que le pipeline n’a qu’une seule activité (l’activité Exécuter le Package SSIS).

   ![Exécutions d’activités](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Vous pouvez exécuter la **requête** suivante par rapport à la base de données SSISDB dans votre serveur SQL Azure pour vérifier que le package s’est exécuté. 

   ```sql
   select * from catalog.executions
   ```

   ![Vérifier les exécutions de package](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Vous pouvez également obtenir l’ID de l’exécution SSISDB à partir de la sortie de l’exécution de l’activité de pipeline, et utiliser l’ID pour consulter des journaux d’activité d’exécution et des messages d’erreur plus complets dans SSMS.

   ![Obtenez l’ID de l'exécution.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planifier le pipeline avec un déclencheur

Vous pouvez également créer un déclencheur planifié pour votre pipeline afin que le pipeline s’exécute selon une planification (horaire, quotidienne, et ainsi de suite). Pour obtenir un exemple, consultez [Créer une fabrique de données - Interface utilisateur de Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Exécuter un package avec PowerShell
Dans cette section, vous utilisez Azure PowerShell pour créer un pipeline ADF avec une activité Execute SSIS Package qui exécute votre package SSIS. 

Installez les modules Azure PowerShell les plus récents en suivant les instructions pas à pas décrites dans [Comment installer et configurer Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Créer un ADF avec Azure-SSIS IR
Vous pouvez soit utiliser un ADF existant déjà fourni par Azure-SSIS IR, soit créer un nouvel ADF avec Azure-SSIS IR en suivant les instructions étape par étape dans le [Tutoriel : Déployer des packages SSIS sur Azure via Powershell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Créer un pipeline avec une activité Exécuter le Package SSIS 
Au cours de cette étape, vous créez un pipeline avec une activité Execute SSIS Package. L’activité exécute votre package SSIS. 

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
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MySecret"
                           }
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
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
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

2. Dans Azure PowerShell, accédez au dossier `C:\ADF\RunSSISPackage`.

3. Pour créer le pipeline **RunSSISPackagePipeline**, exécutez le **Set-AzDataFactoryV2Pipeline** applet de commande.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
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

### <a name="run-the-pipeline"></a>Exécuter le pipeline
Utilisez le **Invoke-AzDataFactoryV2Pipeline** applet de commande pour exécuter le pipeline. L’applet de commande renvoie l’ID d’exécution du pipeline pour permettre une surveillance ultérieure.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Surveiller le pipeline

Exécutez le script PowerShell suivant afin de vérifier continuellement l’état de l’exécution du pipeline jusqu’à la fin de la copie des données. Copiez/collez le script suivant dans la fenêtre PowerShell et appuyez sur ENTRÉE. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
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

Vous pouvez également surveiller le pipeline à l’aide du portail Azure. Pour obtenir des instructions pas à pas, consultez [Surveiller le pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planifier le pipeline avec un déclencheur
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
           }]
       }
   }    
   ```
2. Dans **Azure PowerShell**, basculez vers le dossier **C:\ADF\RunSSISPackage**.
3. Exécutez le **Set-AzDataFactoryV2Trigger** applet de commande, ce qui crée le déclencheur. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Par défaut, le déclencheur est arrêté. Démarrez le déclencheur en exécutant la **Start-AzDataFactoryV2Trigger** applet de commande. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Vérifiez que le déclencheur est démarré en exécutant la **Get-AzDataFactoryV2Trigger** applet de commande. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Exécutez la commande ci-dessous après l’heure suivante. Par exemple, si l’heure actuelle est 15h25 UTC, exécutez la commande à 16h00 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
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
Consultez le billet de blog suivant :
-   [Moderniser et étendre vos flux de travail ETL/ELT avec des activités SSIS dans des pipelines ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
