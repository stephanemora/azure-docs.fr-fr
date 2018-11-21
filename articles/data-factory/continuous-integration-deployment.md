---
title: Intégration et livraison continues dans Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’intégration et la livraison continues pour déplacer les pipelines Data Factory d’un environnement (développement, test, production) à un autre.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: douglasl
ms.openlocfilehash: 60c715e97f6b1d2046fb4050ae41b27146c0610a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623771"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Intégration et livraison continues (CI/CD) dans Azure Data Factory

L’intégration continue consiste à tester automatiquement et, dès que possible, chaque modification apportée à votre code base. La livraison continue fait suite au test effectué pendant l’intégration continue, et envoie (push) les modifications à un système de production ou de préproduction.

Pour Azure Data Factory, l’intégration et la livraison continues impliquent de déplacer des pipelines Data Factory d’un environnement (développement, test, production) vers un autre. Pour procéder à l’intégration et à la livraison continues, vous pouvez utiliser l’intégration de l’interface utilisateur de Data Factory avec les modèles Azure Resource Manager. L’interface utilisateur de la fabrique de données peut générer un modèle Resource Manager lorsque vous sélectionnez les options du **modèle ARM**. Lorsque vous sélectionnez **Exporter un modèle ARM**, le portail génère le modèle Resource Manager pour la fabrique de données et un fichier de configuration qui inclut toutes vos chaînes de connexion et d’autres paramètres. Vous devez ensuite créer un fichier de configuration pour chaque environnement (développement, test, production). Le principal fichier de modèle Resource Manager reste le même pour tous les environnements.

Pour une présentation de neuf minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

## <a name="create-a-resource-manager-template-for-each-environment"></a>Créer un modèle Resource Manager pour chaque environnement
Sélectionnez **Exporter un modèle ARM** pour exporter le modèle Resource Manager de votre fabrique de données dans l’environnement de développement.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Ensuite, accédez à votre fabrique de données de test et à votre fabrique de données de production, puis sélectionnez **Importer un modèle ARM**.

![](media/continuous-integration-deployment/continuous-integration-image2.png)

Cette action ouvre le portail Azure, dans lequel vous pouvez importer le modèle exporté. Sélectionnez **Créer votre propre modèle dans l’éditeur**, puis **Charger le fichier** et sélectionnez le modèle Resource Manager généré. Entrez les paramètres et la fabrique de données, puis le pipeline entier est importé dans votre environnement de production.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Sélectionnez **Charger le fichier** pour sélectionner le modèle Resource Manager exporté et entrez toutes les valeurs de configuration (par exemple, les services liés).

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**Chaînes de connexion**. Vous pouvez trouver les informations requises pour créer des chaînes de connexion dans les articles à propos des connecteurs individuels. Par exemple, pour Azure SQL Database, consultez [Copier des données depuis/vers Azure SQL Database en utilisant Azure Data Factory](connector-azure-sql-database.md). Pour vérifier la chaîne de connexion appropriée, pour un service lié par exemple, vous pouvez également ouvrir le mode Code pour la ressource dans l’interface utilisateur de Data Factory. Toutefois, en mode Code, la partie de la chaîne de connexion relative au mot de passe ou à la clé de compte est supprimée. Pour ouvrir le mode Code, sélectionnez l’icône en surbrillance dans la capture d’écran suivante.

![Ouvrez le mode Code pour afficher la chaîne de connexion](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>Cycle de vie de l’intégration continue
Voici le cycle de vie complet de l’intégration et de la livraison continues que vous pouvez utiliser après avoir activé l’intégration d’Azure Repos Git dans l’interface utilisateur Data Factory :

1.  Configurez une fabrique de données de développement avec Azure Repos, dans laquelle tous les développeurs peuvent créer des ressources Data Factory, telles que des pipelines, des jeux de données, etc.

1.  Ensuite, les développeurs peuvent modifier des ressources, par exemple des pipelines. À mesure qu’ils apportent leurs modifications, ils peuvent sélectionner **Déboguer** pour voir comment le pipeline s’exécute avec les modifications les plus récentes.

1.  Une fois les développeurs satisfaits de leurs modifications, ils peuvent créer une demande de tirage (pull request) à partir de leur branche vers la branche maître (ou de la branche de collaboration) pour que leurs modifications soient examinées par leurs pairs.

1.  Une fois les modifications apportées dans la branche principale, ils peuvent les publier vers la fabrique de développement en sélectionnant **Publier**.

1.  Lorsque l’équipe est prête à promouvoir les modifications dans la fabrique de test et la fabrique de production, elle peut exporter le modèle Resource Manager à partir de la branche principale ou à partir de n’importe quelle autre branche si la branche principale stocke la fabrique de données de développement dynamique.

1.  Le modèle Resource Manager exporté peut être déployé avec différents fichiers de paramètres vers la fabrique de test et la fabrique de production.

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatiser l’intégration continue aux versions d’Azure Pipelines

Les étapes suivantes de configuration d’une version Azure Pipelines vous permettront d’automatiser le déploiement d’une fabrique de données dans plusieurs environnements.

![Diagramme de l’intégration continue à Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Configuration requise

-   Un abonnement Azure lié à Team Foundation Server ou Azure Repos utilisant le  [*point de terminaison de service Azure Resource Manager*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)

-   Une fabrique de données avec intégration d’Azure Repos Git

-   Un coffre  [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)  contenant les secrets

### <a name="set-up-an-azure-pipelines-release"></a>Configurer une version d’Azure Pipelines

1.  Accédez à votre page Azure Repos dans le même projet que celui configuré avec la fabrique de données.

1.  Cliquez sur le menu supérieur **Azure Pipelines** &gt; **Versions** &gt; **Créer une définition de mise en production**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Sélectionnez le modèle **Processus vide**.

1.  Entrez le nom de votre environnement.

1.  Ajoutez un artefact Git et sélectionnez le même référentiel que celui configuré avec la fabrique de données. Choisissez `adf_publish` comme branche par défaut avec la dernière version par défaut.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Ajoutez une tâche de déploiement Azure Resource Manager :

    a.  Créez une tâche, recherchez **Déploiement d’Azure Resource Group** et ajoutez-le.

    b.  Dans la tâche Déploiement, choisissez l’abonnement, le groupe de ressources et l’emplacement de la fabrique de données cible et fournissez des informations d’identification si nécessaire.

    c.  Sélectionnez l’action **Créer ou mettre à jour un groupe de ressources**.

    d.  Sélectionnez **…** dans le champ **Modèle**. Recherchez le modèle Resource Manager (*ARMTemplateForFactory.json*) qui a été créé par l’action de publication dans le portail. Recherchez ce fichier dans le dossier `<FactoryName>` de la branche `adf_publish`.

    e.  Procédez de la même manière pour le fichier de paramètres. Choisissez le fichier approprié selon que vous avez créé ou non une copie ou que vous utilisez ou non le fichier par défaut *ARMTemplateParametersForFactory.json*.

    f.  Sélectionnez **…** en regard du champ **Remplacer les paramètres du modèle** et indiquez les informations pour la fabrique de données cible. Pour les informations d’identification associées au coffre de clés, utilisez le même nom de secret au format suivant : en supposant que le nom du secret est `cred1`, entrez `"$(cred1)"` (entre guillemets).

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. Sélectionnez le mode de déploiement **incrémentiel**.

    > [!WARNING]
    > Si vous sélectionnez le mode de déploiement **Complet**, les ressources existantes peuvent être supprimées, y compris les ressources du groupe de ressources cible qui ne sont pas définies dans le modèle Resource Manager.

1.  Enregistrez le pipeline de mise en production.

1.  Créez une nouvelle version à partir du pipeline de mise en production.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>Facultatif - Récupérer les secrets à partir d’Azure Key Vault

Si vous avez des secrets à transmettre dans un modèle Azure Resource Manager, nous vous recommandons d’utiliser Azure Key Vault avec la version Azure Pipelines.

Il existe deux moyens de gérer les secrets :

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

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Accorder des autorisations à l’agent Azure Pipelines
La tâche Azure Key Vault peut échouer la première fois avec une erreur Accès refusé. Téléchargez les journaux de la version, puis recherchez le fichier `.ps1` avec la commande pour accorder des autorisations à l’agent Azure Pipelines. Vous pouvez exécuter la commande directement, ou vous pouvez copier l’ID du principal à partir du fichier et ajouter manuellement la stratégie d’accès dans le portail Azure. (*Get* et *List* sont les autorisations minimales requises).

### <a name="update-active-triggers"></a>Mettre à jour les déclencheurs actifs
Le déploiement peut échouer si vous tentez de mettre à jour les déclencheurs actifs. Pour mettre à jour les déclencheurs actifs, vous devez les arrêter manuellement et les démarrer après le déploiement. Vous pouvez ajouter une tâche Azure Powershell à cet effet, comme indiqué dans l’exemple suivant :

1.  Dans l’onglet Tâches de la version, recherchez **Azure Powershell**, puis ajoutez-le.

1.  Choisissez **Azure Resource Manager** en tant que type de connexion et sélectionnez votre abonnement.

1.  Choisissez **Script inline** comme type de script, puis indiquez votre code. L’exemple suivant arrête les déclencheurs :

    ```powershell
    $triggersADF = Get-AzureRmDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Vous pouvez suivre des étapes similaires et utiliser un code similaire (avec la fonction `Start-AzureRmDataFactoryV2Trigger`) pour redémarrer les déclencheurs après le déploiement.

> [!IMPORTANT]
> Dans les scénarios de déploiement et d’intégration continus, le type de runtime d’intégration doit être le même dans tous les environnements. Par exemple, si vous avez un runtime d’intégration *auto-hébergé* dans l’environnement de développement, les autres environnements (test, production) doivent également avoir un runtime d’intégration *auto-hébergé*. De même, si plusieurs étapes partagent un même runtime d’intégration, vous devez configurer ce runtime d’intégration comme étant *lié et auto-hébergé* dans tous les environnements (développement, test, production).

## <a name="sample-deployment-template"></a>Exemple de modèle de déploiement

Voici un exemple de modèle de déploiement que vous pouvez importer dans Azure Pipelines.

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>Exemple de script pour arrêter et redémarrer les déclencheurs et le nettoyage

Voici un exemple de script pour arrêter les déclencheurs avant le déploiement et les redémarrer après. Le script inclut également le code pour supprimer les ressources qui ont été retirées. Pour installer la dernière version d’Azure PowerShell, consultez [Installer Azure PowerShell sur Windows avec PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.9.0).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder="$(env:System.DefaultWorkingDirectory)/Dev/",
    [parameter(Mandatory = $false)] [String] $armTemplate="$rootFolder\arm_template.json",
    [parameter(Mandatory = $false)] [String] $ResourceGroupName="sampleuser-datafactory",
    [parameter(Mandatory = $false)] [String] $DataFactoryName="sampleuserdemo2",
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true

)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzureRmDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and $_.properties.pipelines.Count -gt 0} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {

    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzureRmDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #delte resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzureRmDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzureRmDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzureRmDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzureRmDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzureRmDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzureRmDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    #Start Active triggers - After cleanup efforts (moved code on 10/18/2018)
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Utiliser des paramètres personnalisés avec le modèle Resource Manager

Vous pouvez définir des paramètres personnalisés pour le modèle Resource Manager. Vous devez simplement disposer d’un fichier nommé `arm-template-parameters-definition.json` dans le dossier racine du référentiel. (Le nom du fichier doit correspondre exactement au nom indiqué ici.) Data Factory tente de lire le fichier à partir de la branche dans laquelle vous travaillez actuellement et pas uniquement à partir de la branche de collaboration. Si aucun fichier n’est trouvé, Data Factory utilise les paramètres et valeurs par défaut.

### <a name="syntax-of-a-custom-parameters-file"></a>Syntaxe d’un fichier de paramètres personnalisés

Voici quelques recommandations à suivre lors de la création du fichier de paramètres personnalisés. Pour consulter des exemples de syntaxe, consultez la section [Exemple de fichier de paramètres personnalisés](#sample).

1. Lorsque vous spécifiez un tableau spécifique dans le fichier de définition, vous indiquez que la propriété correspondante dans le modèle correspond à un tableau. Data Factory itère au sein de tous les objets du tableau en utilisant la définition spécifiée dans le premier objet du tableau. Le second objet, une chaîne, correspond alors au nom de la propriété et sert de nom au paramètre pour chaque itération.

    ```json
    ...
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
            }
        }
    },
    ...
    ```

2. Lorsque vous définissez un nom de propriété sur `*`, vous indiquez que vous souhaitez que le modèle utilise toutes les propriétés à ce niveau, exception faite des propriétés explicitement définies.

3. Lorsque vous définissez la valeur d’une propriété sous forme de chaîne, vous indiquez que vous souhaitez paramétrer la propriété. Utilisez le format `<action>:<name>:<stype>`.
    1.  `<action>` peut être l’une des valeurs suivantes : 
        1.  `=` permet de conserver la valeur actuelle en tant que valeur par défaut pour le paramètre.
        2.  `-` permet de ne pas conserver la valeur par défaut pour le paramètre.
        3.  `|` est un cas particulier pour les secrets Azure Key Vault pour une chaîne de connexion.
    2.  `<name>` correspond au nom du paramètre. Si `<name`> est vide, il prend le nom du paramètre. 
    3.  `<stype>` correspond au type du paramètre. Si `<stype>` est vide, le type par défaut correspond à une chaîne.
4.  Si vous entrez un caractère `-` au début d’un nom de paramètre, le nom complet du paramètre Resource Manager est abrégé en `<objectName>_<propertyName>`.
Par exemple, `AzureStorage1_properties_typeProperties_connectionString` est abrégé en `AzureStorage1_connectionString`.


### <a name="sample"></a> Exemple de fichier de paramètres personnalisés

L’exemple suivant indique un exemple de fichier de paramètres. Utilisez cet exemple comme référence pour créer votre propre fichier de paramètres personnalisé. Si le fichier que vous indiquez n’est pas au format JSON adéquat, Data Factory génère un message d’erreur dans la console du navigateur, puis rétablit les paramètres et valeurs par défaut indiqués dans l’interface utilisateur Data Factory.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {},
    "Microsoft.DataFactory/factories/integrationRuntimes": {
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
                    }
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
                    "connectionString": "|:-connectionString:secureString"
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
        }
    }
}
```
