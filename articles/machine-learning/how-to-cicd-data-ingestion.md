---
title: DevOps pour un pipeline d’ingestion des données
titleSuffix: Azure Machine Learning
description: Découvrez comment appliquer des pratiques DevOps à une implémentation de pipeline d’ingestion des données utilisée pour préparer des données destinées à la formation d’un modèle.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247178"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps pour un pipeline d’ingestion des données

Dans la plupart des scénarios, une solution d’ingestion des données est une composition de scripts, d’appels de service et d’un pipeline qui orchestre toutes les activités. Dans cet article, vous apprendrez à appliquer les pratiques DevOps au cycle de vie de développement d’un pipeline commun d’ingestion des données. Le pipeline prépare les données pour la formation du modèle Machine Learning.

## <a name="the-solution"></a>La solution

Examinez le workflow d’ingestion des données suivant :

![data-ingestion-pipeline](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

Dans cette approche, les données d’apprentissage sont stockées dans un stockage de blobs Azure. Un pipeline Azure Data Factory extrait les données d’un conteneur de blobs d’entrée, les transforme et enregistre les données dans le conteneur de blobs de sortie. Ce conteneur sert de [stockage de données](concept-data.md) pour l’Azure Machine Learning service. Une fois les données préparées, le pipeline Data Factory appelle un pipeline de Machine Learning de formation pour effectuer l’apprentissage d’un modèle. Dans cet exemple, la transformation des données est effectuée par un notebook Python exécuté sur un cluster Azure Databricks. 

## <a name="what-we-are-building"></a>Ce que nous créons

Comme pour toute solution logicielle, il existe une équipe (par exemple, des ingénieurs de données) qui l’utilise. 

![cicd-data-ingestion](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Les membres de cette équipe collaborent et partagent les mêmes ressources Azure, telles qu’Azure Data Factory, Azure Databricks, un compte de stockage Azure, etc. La collection de ces ressources est un environnement de développement. Les ingénieurs de données contribuent à la même base de code source. Le processus d’intégration continue assemble le code, le vérifie avec les tests de qualité du code et des tests unitaires et produit des artefacts tels que le code testé et les modèles Azure Resource Manager. Le processus de déploiement continu déploie les artefacts dans les environnements en aval. Cet article explique comment automatiser les processus d’intégration continue et de déploiement continu grâce à [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Gestion du contrôle de code source

Les membres de l’équipe travaillent de façon légèrement différente pour collaborer sur le code source du notebook Python et le code source Azure Data Factory. Toutefois, dans les deux cas, le code est stocké dans un référentiel de contrôle de code source (par exemple, Azure DevOps, GitHub, GitLab) et la collaboration est normalement basée sur un modèle de branchement (par exemple, [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)).

### <a name="python-notebook-source-code"></a>Code source du notebook Python

Les ingénieurs de données utilisent le code source du notebook Python localement dans un IDE (par exemple, [Visual Studio Code](https://code.visualstudio.com)) ou directement dans l’espace de travail Databricks. Cette deuxième approche permet de déboguer le code dans l’environnement de développement. Dans tous les cas, le code sera fusionné dans le référentiel en suivant une stratégie de branchement. Il est vivement recommandé de stocker le code dans des fichiers `.py` plutôt qu’au format de notebook Jupyter `.ipynb`. Cela améliore la lisibilité du code et active les vérifications automatiques de la qualité du code dans le processus d’intégration continue.

### <a name="azure-data-factory-source-code"></a>Code source Azure Data Factory

Le code source des pipelines Azure Data Factory est une collection de fichiers JSON générés par un espace de travail. Normalement, les ingénieurs de données travaillent avec un concepteur visuel dans l’espace de travail Azure Data Factory plutôt qu’avec les fichiers de code source directement. Configurez l’espace de travail avec un référentiel de contrôle de code source tel qu’il est décrit dans la [documentation Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration). Grâce à cette configuration en place, les ingénieurs de données peuvent collaborer sur le code source en suivant un workflow de branchement privilégié.    

## <a name="continuous-integration-ci"></a>Intégration continue (CI)

L’objectif ultime du processus d’intégration continue est de rassembler le travail de l’équipe à partir du code source et de le préparer pour le déploiement dans les environnements en aval. Comme pour la gestion du code source, ce processus est différent pour les notebooks Python et les pipelines Azure Data Factory. 

### <a name="python-notebook-ci"></a>CI de notebooks Python

Le processus CI des notebooks Python récupère le code de la branche de collaboration (par exemple, ***maître*** ou ***développement***) et effectue les activités suivantes :
* Linting de code
* Test des unités
* Enregistrement du code en tant qu’artefact

L’extrait de code suivant illustre l’implémentation de ces étapes dans un pipeline ***YAML*** Azure DevOps :

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

Le pipeline utilise ***flake8*** pour effectuer le linting du code Python. Il exécute les tests unitaires définis dans le code source et publie les résultats du linting et des tests afin qu’ils soient disponibles dans l’écran d’exécution du pipeline Azure :

![linting-unit-tests](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Si le linting et les tests unitaires sont réussis, le pipeline copie le code source dans le référentiel d’artefacts afin d’être utilisé lors des étapes de déploiement ultérieures.

### <a name="azure-data-factory-ci"></a>CI Azure Data Factory

Le processus d’intégration continue pour un pipeline Azure Data Factory est un goulot d’étranglement dans l’ensemble du scénario CI/CD pour un pipeline d’ingestion des données. Il n’existe aucune intégration ***continue***. Un artefact déployable pour Azure Data Factory est une collection de modèles Azure Resource Manager. La seule façon de produire ces modèles consiste à cliquer sur le bouton ***Publier*** dans l’espace de travail Azure Data Factory. Il n’y a pas d’automatisation ici.
Les ingénieurs des données fusionnent le code source de leurs branches de fonctionnalités dans la branche de collaboration, par exemple ***maître*** ou ***développement***. Ensuite, une personne disposant des autorisations accordées clique sur le bouton ***Publier*** pour générer des modèles Azure Resource Manager à partir du code source de la branche collaboration. Lorsque l’utilisateur clique sur le bouton, l’espace de travail valide les pipelines (pensez-y comme au linting et aux tests unitaires), génère des modèles Azure Resource Manager (pensez-y comme à la création) et enregistre les modèles générés dans une branche technique ***adf_publish*** dans le même référentiel de code (pensez-y comme à la publication d’artefacts). Cette branche est créée automatiquement par l’espace de travail Azure Data Factory. Ce processus est décrit en détail dans la [documentation Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment).

Il est important de s’assurer que les modèles Azure Resource Manager générés sont indépendants de l’environnement. Cela signifie que toutes les valeurs qui peuvent différer d’un environnement à l’autre sont paramétrées. La solution Azure Data Factory est suffisamment intelligente pour exposer la plupart de ces valeurs en tant que paramètres. Par exemple, dans le modèle suivant, les propriétés de connexion à un espace de travail Azure Machine Learning sont exposées en tant que paramètres :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Toutefois, vous souhaiterez peut-être exposer vos propriétés personnalisées qui ne sont pas gérées par défaut par l’espace de travail Azure Data Factory. Dans le scénario de cet article, un pipeline Azure Data Factory appelle un notebook Python qui traite les données. Le notebook accepte un paramètre avec le nom d’un fichier de données d’entrée.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Ce nom est différent pour les environnements ***Dev***, ***QA***, ***UAT*** et ***PROD***. Dans un pipeline complexe avec plusieurs activités, il peut y avoir plusieurs propriétés personnalisées. Il est conseillé de collecter toutes ces valeurs dans un même emplacement et de les définir comme des ***variables*** de pipeline :

![adf-variables](media/how-to-cicd-data-ingestion/adf-variables.png)

Les activités du pipeline peuvent faire référence aux variables de pipeline tout en les utilisant réellement :

![adf-notebook-parameters](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

L’espace de travail Azure Data Factory ***n’exposent pas*** les variables de pipeline en tant que paramètres par défaut des modèles Azure Resource Manager. L’espace de travail utilise le [modèle de paramétrage par défaut](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) dictant quelles propriétés de pipeline doivent être exposées comme paramètres de modèle Azure Resource Manager. Pour ajouter des variables de pipeline à la liste, mettez à jour la section « Microsoft.DataFactory/factories/pipelines » du [modèle de paramétrage par défaut](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) avec l’extrait de code suivant et placez le fichier JSON qui en résulte à la racine du dossier source :

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Cela obligera l’espace de travail Azure Data Factory à ajouter les variables à la liste de paramètres quand l’utilisateur clique sur le bouton ***Publier*** :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

Les valeurs du fichier JSON sont des valeurs par défaut configurées dans la définition du pipeline. Elles doivent être remplacées par les valeurs de l’environnement cible lors du déploiement du modèle Azure Resource Manager.

## <a name="continuous-delivery-cd"></a>Déploiement continu (CD)

Le processus de déploiement continu prend les artefacts et les déploie dans le premier environnement cible. Cela permet de s’assurer que la solution fonctionne en exécutant des tests. En cas de réussite, il continue dans l’environnement suivant. Le pipeline Azure de CD est constitué de plusieurs index représentant les environnements. Chaque index contient des [déploiements](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) et des [tâches](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) qui effectuent les étapes suivantes :
* Déployer un notebook Python dans un espace de travail Azure Databricks
* Déployer un pipeline de Azure Data Factory 
* Exécuter le pipeline
* Vérifier le résultat de l’ingestion des données

Les index du pipeline peuvent être configurés avec des [approbations](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) et des [portes](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) qui offrent un contrôle supplémentaire sur la façon dont le processus de déploiement évolue dans la chaîne d’environnements.

### <a name="deploy-a-python-notebook"></a>Déployer un notebook Python

L’extrait de code suivant définit un [déploiement](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) de pipeline Azure qui copie un notebook Python sur un cluster Databricks :

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

Les artefacts produits par l’intégration continue sont automatiquement copiés vers l’agent de déploiement et sont disponibles dans le dossier ***$(Pipeline.Workspace)***. Dans ce cas, la tâche de déploiement fait référence à l’artefact ***di-notebooks*** contenant le notebook Python. Ce [déploiement](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) utilise l’[extension Databricks Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) pour copier les fichiers du notebook dans l’espace de travail Databricks.
L’index ***Deploy_to_QA*** contient une référence au groupe de variables ***devops-ds-qa-vg*** défini dans le projet Azure DevOps. Les étapes de cet index font référence aux variables de ce groupe de variables, par exemple $(DATABRICKS_URL), $(DATABRICKS_TOKEN). L’idée est que l’index suivant (par exemple, ***Deploy_to_UAT***) fonctionne avec les mêmes noms de variables que ceux définis dans son propre groupe de variables dans l’étendue UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Déployer un pipeline de Azure Data Factory

Un artefact déployable pour Azure Data Factory est un modèle Azure Resource Manager. Par conséquent, il sera déployé avec la tâche ***Déploiement du groupe de ressources Azure***, comme illustré dans l’extrait de code suivant :

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
La valeur du paramètre de nom de fichier de données provient de la variable $(DATA_FILE_NAME) définie dans un groupe de variables de l’index QA. De même, tous les paramètres définis dans ***ARMTemplateForFactory.json*** peuvent être substitués. S’ils ne le sont pas, les valeurs par défaut sont utilisées.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Exécuter le pipeline et vérifier le résultat de l’ingestion des données

L’étape suivante consiste à s’assurer que la solution déployée fonctionne. La définition de travail suivante exploite un pipeline Azure Data Factory avec un [script PowerShell](https://github.com/microsoft/DataOps/tree/master/adf/utils) et exécute un notebook Python sur un cluster Azure Databricks. Le notebook vérifie si les données ont été correctement ingérées et valide le fichier de données de résultats avec le nom $(bin_FILE_NAME).

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

La dernière tâche du travail vérifie le résultat de l’exécution du notebook. Si elle retourne une erreur, elle définit l’état de l’exécution du pipeline sur Échec.

## <a name="putting-pieces-together"></a>Assemblage des éléments

Le résultat de cet article est un pipeline Azure CI/CD qui comprend les index suivants :
* CI
* Déployer dans QA
    * Déployer dans Databricks + déployer dans ADF
    * Test d’intégration

Il contient un nombre d’index ***Déployer*** égal au nombre d’environnements cibles. Chaque index ***Déployer*** contient deux [déploiements](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) qui s’exécutent en parallèle et un [travail](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) qui s’exécute après les déploiements pour tester la solution sur l’environnement.

Un exemple d’implémentation du pipeline est assemblé dans l’extrait de code ***YAML*** suivant :

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Étapes suivantes

* [Contrôle de code source dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control)
* [Intégration et déploiement continus dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps pour Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
