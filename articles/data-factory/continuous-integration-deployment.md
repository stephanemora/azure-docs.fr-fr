---
title: Intégration et livraison continues dans Azure Data Factory
description: Découvrez comment utiliser l’intégration et la livraison continues pour déplacer les pipelines Data Factory d’un environnement (développement, test, production) à un autre.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 06c8784c235b157f5799bb727df9784dfaa2f376
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440534"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Intégration et livraison continues (CI/CD) dans Azure Data Factory

## <a name="overview"></a>Vue d’ensemble

L’intégration continue consiste à tester automatiquement et, dès que possible, chaque modification apportée à votre code base. La livraison continue fait suite au test effectué pendant l’intégration continue, et envoie (push) les modifications à un système de production ou de préproduction.

Dans Azure Data Factory, l’intégration et la livraison continues impliquent de déplacer des pipelines Data Factory d’un environnement (développement, test, production) vers un autre. Pour procéder à l’intégration et à la livraison continues, vous pouvez utiliser l’expérience utilisateur de Data Factory avec les modèles Azure Resource Manager. L’expérience utilisateur de Data Factory peut générer un modèle Resource Manager à partir de la liste déroulante du **modèle ARM**. Lorsque vous sélectionnez **Exporter un modèle ARM**, le portail génère le modèle Resource Manager pour la fabrique de données et un fichier de configuration qui inclut toutes vos chaînes de connexion et d’autres paramètres. Vous devez ensuite créer un fichier de configuration pour chaque environnement (développement, test, production). Le principal fichier de modèle Resource Manager reste le même pour tous les environnements.

Pour une présentation de neuf minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>Cycle de vie de l’intégration continue

Vous trouverez ci-dessous une vue d’ensemble de l’intégration continue et du cycle de vie de livraison dans une fabrique de données Azure configurée avec Azure Repos Git. Pour plus d’informations sur la configuration d’un référentiel git, consultez [Contrôle de code source dans Azure Data Factory](source-control.md).

1.  Une fabrique de données de développement est créée et configurée avec Azure Repos Git, où tous les développeurs ont l’autorisation de créer des ressources Data Factory telles que des pipelines et des jeux de données.

1.  À mesure que les développeurs apportent des modifications à leur branche de fonctionnalités, ils déboguent leurs exécutions de pipeline avec leurs modifications les plus récentes. Pour plus d’informations sur le débogage d’une exécution de pipeline, consultez [Développement et débogage itératifs avec Azure Data Factory](iterative-development-debugging.md).

1.  Une fois les développeurs satisfaits de leurs modifications, ils peuvent créer une demande de tirage (pull request) de leur branche de fonctionnalités vers la branche principale ou la branche de collaboration pour que leurs modifications soient examinées par leurs pairs.

1.  Une fois que la demande de tirage (pull request) est approuvée et que les modifications sont fusionnées dans la branche principale, ils peuvent les publier vers la fabrique de développement.

1.  Lorsque l’équipe est prête à déployer les modifications apportées à la fabrique de test, puis à la fabrique de production, elle exporte le modèle Resource Manager à partir de la branche principale.

1.  Le modèle Resource Manager exporté est déployé avec différents fichiers de paramètres vers la fabrique de test et la fabrique de production.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Créer un modèle Resource Manager pour chaque environnement

À partir de la liste déroulante **modèle ARM**, sélectionnez **Exporter un modèle ARM** pour exporter le modèle Resource Manager de votre fabrique de données dans l’environnement de développement.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Dans vos fabriques de données de test et de production, sélectionnez **Importer un modèle ARM**. Cette action ouvre le portail Azure, dans lequel vous pouvez importer le modèle exporté. Sélectionnez **Créer votre propre modèle dans l’éditeur** pour ouvrir l’éditeur de modèle Resource Manager.

![Déploiement personnalisé - Créer votre propre modèle](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

Cliquez sur **Charger le fichier** et sélectionnez le modèle Resource Manager généré.

![Déploiement personnalisé - Modifier le modèle](media/continuous-integration-deployment/custom-deployment-edit-template.png)

Dans le volet Paramètres, entrez les valeurs de configuration telles que les informations d’identification du service lié. Lorsque vous avez terminé, cliquez sur **Acheter** pour déployer le modèle Resource Manager.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Chaînes de connexion

Vous trouverez des informations sur la configuration des chaînes de connexion dans l’article de chaque connecteur. Par exemple, pour Azure SQL Database, consultez [Copier des données depuis/vers Azure SQL Database en utilisant Azure Data Factory](connector-azure-sql-database.md). Pour vérifier une chaîne de connexion, vous pouvez ouvrir le mode Code de la ressource dans l’expérience utilisateur Data Factory. En mode Code, la partie de la chaîne de connexion relative au mot de passe ou à la clé de compte est supprimée. Pour ouvrir le mode Code, sélectionnez l’icône en surbrillance dans la capture d’écran suivante.

![Ouvrez le mode Code pour afficher la chaîne de connexion](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatiser l’intégration continue aux versions d’Azure Pipelines

Ci-dessous, un guide de configuration d’une version Azure Pipelines automatise le déploiement d’une fabrique de données dans plusieurs environnements.

![Diagramme de l’intégration continue à Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Spécifications

-   Un abonnement Azure lié à Team Foundation Server ou Azure Repos utilisant le  [point de terminaison de service Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Une fabrique de données configurée avec l’intégration d’Azure Repos Git.

-   Un coffre  [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) contenant les secrets pour chaque environnement.

### <a name="set-up-an-azure-pipelines-release"></a>Configurer une version d’Azure Pipelines

1.  Dans l' [expérience utilisateur Azure DevOps](https://dev.azure.com/), ouvrez le projet configuré avec votre fabrique de données.

1.  Sur le côté gauche de la page, cliquez sur **Pipelines**, puis sélectionnez **Mise en production**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Sélectionnez **Nouveau pipeline** ou, si vous avez des pipelines existants, **Nouveau**, puis un **Nouveau pipeline de mise en version**.

1.  Sélectionnez le modèle **Tâche vide**.

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Dans le champ **Nom de l’étape**, entrez le nom de votre environnement.

1.  Sélectionnez **Ajouter un artefact** et sélectionnez le même référentiel que celui configuré avec Data Factory. Choisissez `adf_publish` comme branche par défaut avec la dernière version par défaut.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Ajoutez une tâche de déploiement Azure Resource Manager :

    a.  Dans la vue des étapes, cliquez sur le lien **Afficher les tâches de l’étape**.

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Créer une tâche. Recherchez **Déploiement d’Azure Resource Group** et cliquez sur **Ajouter**.

    c.  Dans la tâche Déploiement, choisissez l’abonnement, le groupe de ressources et l’emplacement de la fabrique de données cible et fournissez des informations d’identification si nécessaire.

    d.  Dans la liste déroulante des actions, sélectionnez **Créer ou mettre à jour un groupe de ressources**.

    e.  Sélectionnez **…** dans le champ **Modèle**. Recherchez le modèle Azure Resource Manager créé à l’étape **Importer un modèle ARM** dans [Créer un modèle de gestionnaire de ressource pour chaque environnement](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment). Recherchez ce fichier dans le dossier `<FactoryName>` de la branche `adf_publish`.

    f.  Sélectionnez **…** dans le **champ des paramètres du modèle.** pour choisir le fichier de paramètres. Choisissez le fichier approprié selon que vous avez créé ou non une copie ou que vous utilisez ou non le fichier par défaut *ARMTemplateParametersForFactory.json*.

    g.  Sélectionnez **…** en regard du champ **Remplacer les paramètres du modèle** et indiquez les informations pour la fabrique de données cible. Pour les informations d’identification provenant du coffre de clés, entrez le nom du secret entre guillemets doubles. Par exemple, si le nom du secret est `cred1`, entrez `"$(cred1)"`pour sa valeur.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. Sélectionnez le mode de déploiement **incrémentiel**.

    > [!WARNING]
    > Si vous sélectionnez le mode de déploiement **Complet**, les ressources existantes peuvent être supprimées, y compris les ressources du groupe de ressources cible qui ne sont pas définies dans le modèle Resource Manager.

1.  Enregistrez le pipeline de mise en production.

1. Pour déclencher une mise en publication, cliquez sur **Créer une mise en sortie**

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Obtenez les secrets à partir du coffre Azure Key Vault

Si vous avez des secrets à transmettre dans un modèle Azure Resource Manager, nous vous recommandons d’utiliser Azure Key Vault avec la version Azure Pipelines.

Il existe deux moyens de gérer les secrets :

1.  Ajoutez les secrets au fichier de paramètres. Pour plus d’informations, consultez [Use Azure Key Vault to pass secure parameter value during deployment](../azure-resource-manager/resource-manager-keyvault-parameter.md) (Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée lors du déploiement).

    -   Créez une copie du fichier de paramètres qui sera chargée dans la branche de publication et définissez les valeurs des paramètres que vous souhaitez obtenir à partir du coffre de clés avec le format suivant :

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   Lorsque vous utilisez cette méthode, le secret est automatiquement extrait du coffre de clés.

    -   Le fichier de paramètres doit également être dans la branche de publication.

1.  Ajoutez une [tâche Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) avant le déploiement Azure Resource Manager décrit dans la section précédente :

    -   Sélectionnez l’onglet **Tâches**, créez une tâche, recherchez **Azure Key Vault** et ajoutez-le.

    -   Dans la tâche Key Vault, sélectionnez l’abonnement dans lequel vous avez créé le coffre de clés, fournissez des informations d’identification si nécessaire, puis choisissez le coffre de clés.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Accorder des autorisations à l’agent Azure Pipelines

La tâche Azure Key Vault peut échouer avec une erreur d’accès refusé si les autorisations appropriées ne sont pas présentes. Téléchargez les journaux d’activité de la version, puis recherchez le fichier `.ps1` avec la commande pour accorder des autorisations à l’agent Azure Pipelines. Vous pouvez exécuter la commande directement, ou vous pouvez copier l’ID du principal à partir du fichier et ajouter manuellement la stratégie d’accès dans le portail Azure. **Get** et **List** sont les autorisations minimales requises.

### <a name="update-active-triggers"></a>Mettre à jour les déclencheurs actifs

Le déploiement peut échouer si vous tentez de mettre à jour les déclencheurs actifs. Pour mettre à jour les déclencheurs actifs, vous devez les arrêter manuellement et les démarrer après le déploiement. Vous pouvez le faire via une tâche Azure PowerShell.

1.  Dans l’onglet Tâches de la version, ajoutez une tâche **Azure Powershell**.

1.  Choisissez **Azure Resource Manager** en tant que type de connexion et sélectionnez votre abonnement.

1.  Choisissez **Script inline** comme type de script, puis indiquez votre code. L’exemple suivant arrête les déclencheurs :

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Vous pouvez suivre des étapes similaires (avec la fonction `Start-AzDataFactoryV2Trigger`) pour redémarrer les déclencheurs après le déploiement.

> [!IMPORTANT]
> Dans les scénarios de déploiement et d’intégration continus, le type de runtime d’intégration doit être le même dans tous les environnements. Par exemple, si vous avez un runtime d’intégration *auto-hébergé* dans l’environnement de développement, les autres environnements (test, production) doivent également avoir un runtime d’intégration *auto-hébergé*. De même, si vous partagez des runtimes d’intégration à plusieurs stades, vous devez les configurer comme étant *liés et auto-hébergés* dans tous les environnements (développement, test, production).

#### <a name="sample-prepostdeployment-script"></a>Exemple de script de pré/post-déploiement

Vous trouverez ci-dessous un exemple de script pour arrêter les déclencheurs avant le déploiement et les redémarrer après. Le script inclut également le code pour supprimer les ressources qui ont été retirées. Pour installer la dernière version d’Azure PowerShell, consultez [Installer Azure PowerShell sur Windows avec PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Utiliser des paramètres personnalisés avec le modèle Resource Manager

Si vous êtes en mode GIT, vous pouvez remplacer les propriétés par défaut dans votre modèle Resource Manager pour définir des propriétés paramétrables dans le modèle et des propriétés codées en dur. Vous souhaiterez peut-être remplacer le modèle de paramétrage par défaut dans les scénarios suivants :

* Vous utilisez CI/CD automatisé et souhaitez modifier certaines propriétés pendant le déploiement de Resource Manager, mais les propriétés ne sont pas paramétrables par défaut.
* Votre fabrique est si volumineuse que le modèle Resource Manager par défaut n’est pas valide car il dépasse le nombre maximum autorisé de paramètres (256).

Dans ces conditions, pour remplacer le modèle de paramétrage par défaut, créez un fichier nommé *arm-template-parameters-definition.json* dans le dossier racine du dépôt. Le nom de fichier doit correspondre exactement. Data Factory tente de lire ce fichier à partir de la branche dans laquelle vous vous trouvez actuellement dans le portail Azure Data Factory, et pas uniquement à partir de la branche de collaboration. Vous pouvez créer ou modifier le fichier à partir d’une branche privée, dans laquelle vous pouvez tester vos modifications à l’aide du **modèle Export ARM** dans l’interface utilisateur. Vous pouvez ensuite fusionner le fichier dans la branche de collaboration. Si aucun fichier n’est trouvé, le modèle par défaut est utilisé.


### <a name="syntax-of-a-custom-parameters-file"></a>Syntaxe d’un fichier de paramètres personnalisés

Voici quelques recommandations à suivre lorsque vous créez le fichier de paramètres personnalisés. Le fichier comprend une section pour chaque type d’entité : déclencheur, pipeline, service lié, jeu de données, runtime d’intégration, etc.
* Entrez le chemin d’accès de propriété sous le type d’entité correspondant.
* Lorsque vous définissez un nom de propriété sur « \* », vous indiquez que vous souhaitez paramétrer toutes les propriétés dans celle-ci (uniquement jusqu’au premier niveau, pas de manière récursive). Vous pouvez également fournir toutes les exceptions à ceci.
* Lorsque vous définissez la valeur d’une propriété sous forme de chaîne, vous indiquez que vous souhaitez paramétrer la propriété. Utilisez le format  `<action>:<name>:<stype>`.
   *  `<action>` peut être l’un des caractères suivants :
      * `=` permet de conserver la valeur actuelle en tant que valeur par défaut pour le paramètre.
      * `-` permet de ne pas conserver la valeur par défaut pour le paramètre.
      * `|` est un cas particulier pour les secrets Azure Key Vault pour les chaînes de connexion ou les clés.
   * `<name>` correspond au nom du paramètre. S’il est vide, il prend le nom du Si la valeur commence par un caractère `-`, le nom est abrégé. Par exemple, `AzureStorage1_properties_typeProperties_connectionString` serait abrégé en `AzureStorage1_connectionString`.
   * `<stype>` correspond au type de paramètre. Si  `<stype>`  est vide, le type par défaut est `string`. Valeurs prises en charge : `string`, `bool`, `number`, `object` et `securestring`.
* Lorsque vous spécifiez un tableau spécifique dans le fichier de définition, vous indiquez que la propriété correspondante dans le modèle correspond à un tableau. Data Factory itère dans tous les objets du tableau en utilisant la définition spécifiée dans l’objet de runtime d’intégration du tableau. Le second objet, une chaîne, correspond alors au nom de la propriété et sert de nom au paramètre pour chaque itération.
* Il n’est pas possible d’avoir une définition spécifique à une instance de ressource. Toute définition s’applique à toutes les ressources de ce type.
* Par défaut, toutes les chaînes sécurisées, telles que les secrets Key Vault, et les chaînes sécurisées, telles que les chaînes de connexion, les clés et les jetons, sont paramétrables.
 
### <a name="sample-parameterization-template"></a>Exemple de modèle de paramétrage

Vous trouverez ci-dessous un exemple de ce que à quoi peut ressembler un modèle de paramétrage :

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Vous trouverez ci-dessous une explication de la façon dont le modèle ci-dessus est construit, ventilé par type de ressource.

#### <a name="pipelines"></a>Pipelines
    
* Toute propriété dans le chemin d’accès activities/typeProperties/waitTimeInSeconds est paramétrable. Toute activité dans un pipeline qui a une propriété au niveau du code nommée `waitTimeInSeconds` (par exemple, l’activité `Wait`) est paramétrable en tant que nombre, avec un nom par défaut. Mais elle n’a pas de valeur par défaut dans le modèle Resource Manager. Il s’agit d’une entrée obligatoire lors du déploiement de Resource Manager.
* De même, une propriété appelée `headers` (par exemple, dans une activité `Web`) est paramétrable avec le type `object` (JObject). Elle a une valeur par défaut, la même que dans la fabrique source.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Toutes les propriétés, sous le chemin `typeProperties` sont paramétrables avec des valeurs par défaut respectives. Par exemple, deux propriétés existent sous les propriétés de type **IntegrationRuntimes** : `computeProperties` et `ssisProperties`. Les deux types de propriété sont créés avec leurs valeurs et types (objet) par défaut respectifs.

#### <a name="triggers"></a>Déclencheurs

* Sous `typeProperties`, deux propriétés sont paramétrables. La première est `maxConcurrency`, qui est spécifiée pour avoir une valeur par défaut et est de type `string`. Elle porte le nom de paramètre par défaut `<entityName>_properties_typeProperties_maxConcurrency`.
* La propriété `recurrence` est également paramétrable. Sous celle-ci, toutes les propriétés à ce niveau sont spécifiées pour être paramétrables sous forme de chaînes, avec des valeurs et noms de paramètres par défaut. La propriété `interval` est une exception. Elle est paramétrable en tant que type de nombre, et le nom du paramètre est suivi du suffixe `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. De même, la propriété `freq` est une chaîne et peut être paramétrée en tant que chaîne. La propriété `freq` est toutefois paramétrable sans valeur par défaut. Le nom est abrégé et suivi d’un suffixe. Par exemple : `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Les services liés sont uniques. Étant donné que les services liés et les jeux de données sont de types différents, vous pouvez fournir une personnalisation spécifique au type. Dans cet exemple, tous les services liés de type `AzureDataLakeStore`, un modèle spécifique sera appliqué, et pour tous les autres (via \*) qu’un autre modèle sera appliqué.
* La propriété `connectionString` pourra être paramétrée en tant que valeur `securestring`, qu’elle n’aura de valeur par défaut, et qu’elle aura un nom de paramètre abrégé suivi du suffixe `connectionString`.
* La propriété `secretAccessKey` se trouve être un `AzureKeyVaultSecret` (par exemple, un service lié `AmazonS3`). Elle est paramétrable automatiquement en tant que secret Azure Key Vault et extraite du coffre de clés configuré. Vous pouvez également paramétrer le coffre de clés proprement dit.

#### <a name="datasets"></a>Groupes de données

* Bien que la personnalisation spécifique au type soit disponible pour les jeux de données, la configuration peut être fournie sans avoir explicitement de configuration au niveau \*. Dans l’exemple ci-dessus, toutes les propriétés du jeu de données sous `typeProperties` sont paramétrables.

### <a name="default-parameterization-template"></a>Modèle de paramétrage par défaut

Vous trouverez ci-dessous le modèle actuel. Si vous devez ajouter seulement un paramètre ou quelques paramètres, la modification directe peut être utile, car vous ne perdrez pas la structure de paramétrage existante.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

Vous trouverez ci-dessous un exemple de la façon d’ajouter une valeur unique au modèle de paramétrage par défaut. Nous voulons seulement ajouter un ID de cluster Databricks interactif pour un service lié Databricks au fichier de paramètres. Notez que le fichier ci-dessous est le même que le fichier ci-dessus, sauf que `existingClusterId` est inclus dans le champ propriétés de `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Modèles Resource Manager liés

Si vous avez configuré l’intégration et le déploiement continus (CI/CD) pour vos fabriques de données, vous pouvez vous rapprocher des limites du modèle Azure Resource Manager là mesure que votre fabrique augmente. Un exemple de limite est le nombre maximal de ressources dans un modèle Resource Manager. Pour s’adapter à des fabriques de grande taille, en plus de générer le modèle Resource Manager complet pour une fabrique, Data Factory génère désormais des modèles liés Resource Manager. Avec cette fonctionnalité, la charge utile de fabrique entière est divisée en plusieurs fichiers, afin de ne pas atteindre les limites.

Si vous avez configuré Git, les modèles liés sont générés et enregistrés en même temps que les modèles Resource Manager complets, dans la branche `adf_publish`, sous un nouveau dossier nommé `linkedTemplates`.

![Dossier de modèles Resource Manager liés](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Les modèles Resource Manager liés ont généralement un modèle maître et un ensemble de modèles enfants liés au maître. Le modèle parent est appelé `ArmTemplate_master.json`, et les modèles enfants sont nommés au format `ArmTemplate_0.json`, `ArmTemplate_1.json`, et ainsi de suite. Pour utiliser des modèles liés au lieu du modèle Resource Manager complet, mettez à jour votre tâche CI/CD pour qu’elle pointe vers `ArmTemplate_master.json` plutôt que vers `ArmTemplateForFactory.json` (le modèle Resource Manager complet). Resource Manager exige également que vous chargiez les modèles liés dans un compte de stockage afin qu’ils soient accessibles par Azure pendant le déploiement. Pour plus d’informations, consultez [Deploying Linked ARM Templates with VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/) (Déploiement de modèles ARM liés avec VSTS).

N’oubliez pas d’ajouter les scripts Data Factory dans votre pipeline CI/CD avant et après la tâche de déploiement.

Si vous n’avez pas configuré Git, les modèles liés sont accessibles par le biais du mouvement **Exporter un modèle ARM**.

## <a name="hot-fix-production-branch"></a>Branche de production de correctifs

Si vous déployez une fabrique en production et que vous vous rendez compte qu’il y a un bogue qui doit être corrigé immédiatement, mais que vous ne pouvez pas déployer la branche de collaboration actuelle, vous devrez peut-être déployer un correctif. Cette approche est également connue sous le nom de QFE (Quick-Fix Engineering). 

1.  Dans Azure DevOps, accédez à la version qui a été déployée en production et recherchez la dernière validation qui a été déployée.

2.  À partir du message de validation, récupérez l’ID de validation de la branche de collaboration.

3.  Créez une branche de correctifs à partir de cette validation.

4.  Accédez à l’expérience utilisateur Azure Data Factory et basculez vers cette branche.

5.  À l’aide de l’expérience utilisateur Azure Data Factory, corrigez le bogue. Tester vos modifications.

6.  Une fois le correctif vérifié, cliquez sur **Exporter le modèle ARM** pour accéder au modèle de correctif Resource Manager.

7.  Archivez manuellement ce build dans la branche adf_publish.

8.  Si vous avez configuré votre pipeline de mise en production pour qu’il se déclenche automatiquement en fonction des archivages adf_publish, une nouvelle version démarre automatiquement. Sinon, créez manuellement une file d'attente de versions.

9.  Déployez la version de correctif pour les fabriques de test et de production. Cette version contient la charge utile de production précédente plus la correction effectuée à l’étape 5.

10. Ajoutez les modifications du correctif à la branche de développement pour que le même bogue ne se produise pas dans des versions ultérieures.

## <a name="best-practices-for-cicd"></a>Meilleures pratiques pour CI/CD

Si vous utilisez une intégration Git avec votre fabrique de données, et disposez d’un pipeline CI/CD qui déplace vos modifications du développement vers le test, puis la production, nous vous recommandons les meilleures pratiques suivantes :

-   **Intégration Git**. Vous devez uniquement configurer votre fabrique de données de développement avec une intégration Git. Les modifications au niveau du test et de la production sont déployées par le biais de CI/CD et n’ont pas besoin d’intégration Git.

-   **Script CI/CD Data Factory**. Avant l’étape de déploiement de Resource Manager dans CI/CD, certaines tâches sont requises, telles que l’arrêt/le démarrage des déclencheurs et le nettoyage. Nous vous recommandons d’utiliser des scripts PowerShell avant et après le déploiement. Pour plus d’informations, consultez [Mettre à jour des déclencheurs actifs](#update-active-triggers). 

-   **Runtimes d’intégration et partage**. Les runtimes d’intégration ne changent pas souvent et se ressemblent à toutes les étapes de CI/CD. Par conséquent, Data Factory s’attend à trouver le même nom et le même type de runtimes d’intégration à tous les stades de CI/CD. Si vous envisagez de partager des runtimes d’intégration à toutes les étapes, envisagez d’utiliser une fabrique ternaire qui contiendra uniquement les runtimes d’intégration partagés. Vous pouvez utiliser cette fabrique partagée dans tous vos environnements en tant que type de runtime d’intégration lié.

-   **Key Vault**. Lorsque vous utilisez les services liés basés sur Azure Key Vault, vous pouvez en profiter encore davantage en conservant éventuellement des coffres de clés distincts pour différents environnements. Vous pouvez également configurer des niveaux d’autorisation distincts pour chacun d’eux. Par exemple, vous ne souhaitez peut-être pas que les membres de votre équipe disposent d’autorisations sur les secrets de production. Si vous suivez cette approche, il est recommandé de conserver les mêmes noms de secrets à toutes les étapes. Si vous conservez les mêmes noms, vous ne devez pas modifier vos modèles Resource Manager dans les environnements CI/CD, car la seule chose qui change est le nom du coffre de clés, qui est l’un des paramètres du modèle Resource Manager.

## <a name="unsupported-features"></a>Fonctionnalités non prises en charge

- Par défaut, ADF _n’autorise pas_ les validations de type cherry-picking ou la publication sélective des ressources. Les publications incluent **toutes** les modifications apportées dans la fabrique de données.

    - Les entités de la fabrique de données dépendent les unes des autres ; par exemple, des déclencheurs dépendent de pipelines, des pipelines de jeux de données et d’autres pipelines, etc. La publication sélective d’un sous-ensemble de ressources _peut_ engendrer des comportements et des erreurs inattendus.
    - Dans les rares cas où la publication sélective est requise, vous pouvez envisager un correctif. Pour plus d’informations, consultez [Branche de production de correctifs](#hot-fix-production-branch).

-   Vous ne pouvez pas publier à partir de branches privées.

-   À ce stade, vous ne pouvez pas héberger de projets sur Bitbucket.
