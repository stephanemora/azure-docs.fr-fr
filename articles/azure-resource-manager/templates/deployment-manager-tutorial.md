---
title: Utiliser Azure Deployment Manager pour déployer des modèles
description: Découvrez comment utiliser les modèles Resource Manager avec Azure Deployment Manager pour déployer des ressources Azure.
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 95d5067eccff5c847588834061db8454f75e55d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627581"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Tutoriel : Utiliser Azure Deployment Manager avec des modèles Resource Manager (préversion publique)

Découvrez comment utiliser [Azure Deployment Manager](./deployment-manager-overview.md) pour déployer vos applications dans plusieurs régions. Si vous préférez une approche plus rapide, le [guide de démarrage rapide d’Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart) crée les configurations requises dans votre abonnement et personnalise les artefacts pour déployer une application dans plusieurs régions. Le guide démarrage rapide effectue les mêmes tâches que dans ce didacticiel.

Pour utiliser Deployment Manager, vous devez créer deux modèles :

* **Un modèle de topologie** : décrit les ressources Azure constituant vos applications et l’emplacement où les déployer.
* **Un modèle de déploiement** : décrit les étapes à suivre lors du déploiement de vos applications.

> [!IMPORTANT]
> Si votre abonnement est marqué pour les Canaries pour les tests de nouvelles fonctionnalités Azure, vous pouvez utiliser Azure Deployment Manager uniquement en vue du déploiement dans la région des Canaries.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Présentation du scénario
> * Télécharger les fichiers du didacticiel
> * Préparer les artefacts
> * Créer l’identité managée définie par l’utilisateur
> * Créer le modèle de topologie de service
> * Créer le modèle de lancement
> * Déployer les modèles
> * Vérifier le déploiement
> * Déployer la version la plus récente
> * Nettoyer les ressources

Ressources supplémentaires :

* [Informations de référence sur l’API Azure Deployment Manager](/rest/api/deploymentmanager/).
* [Tutoriel : Utiliser le contrôle d’intégrité dans Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Une certaine expérience du développement de [modèles Azure Resource Manager](overview.md).
* Azure PowerShell. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](/powershell/azure/get-started-azureps).
* Cmdlets Deployment Manager. Pour installer ces cmdlets en version préliminaire, vous avez besoin de la dernière version de PowerShellGet. Pour obtenir la toute dernière version, consultez [Installation de PowerShellGet](/powershell/scripting/gallery/installing-psget). Fermez la fenêtre PowerShell après l’installation de PowerShellGet. Ouvrez une nouvelle fenêtre PowerShell avec des privilèges élevés, puis exécutez la commande suivante :

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>Présentation du scénario

Le modèle de topologie décrit les ressources Azure constituant votre service et l’emplacement où les déployer. La définition de topologie de service présente la hiérarchie suivante :

* Topologie de service
  * Services
    * Unités de service

Le diagramme suivant illustre la topologie de réseau utilisée dans ce didacticiel :

![Diagramme de scénario du didacticiel Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Deux services sont alloués dans les régions USA Ouest et USA Est. Chaque service a deux unités de service : une application web front-end et un compte de stockage back-end. Les définitions d’unité de service contiennent des liens vers les fichiers de modèle et de paramètres qui créent les applications web et les comptes de stockage.

## <a name="download-the-tutorial-files"></a>Télécharger les fichiers du didacticiel

1. Téléchargez [les modèles et artefacts](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) utilisés dans ce didacticiel.
1. Décompressez les fichiers à l’emplacement désiré sur votre ordinateur.

Dans le dossier racine, vous verrez deux dossiers :

* _ADMTemplates_ : contient les modèles Deployment Manager, qui incluent :
  * _CreateADMServiceTopology.json_
  * _CreateADMServiceTopology.Parameters.json_
  * _CreateADMRollout.json_
  * _CreateADMRollout.Parameters.json_
* _ArtifactStore_ : contient les artefacts du modèle et les artefacts binaires. Consultez [Préparer les artefacts](#prepare-the-artifacts).

Il existe deux ensembles de modèles. L’un comprend les modèles Deployment Manager utilisés pour déployer la topologie de service et le lancement. L’autre est appelé à partir des unités de service pour créer des services web et des comptes de stockage.

## <a name="prepare-the-artifacts"></a>Préparer les artefacts

Le dossier ArtifactStore téléchargé contient deux dossiers :

![Diagramme source de l’artefact du didacticiel Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* Le dossier _Modèles_ : contient les artefacts du modèle. Les dossiers _1.0.0.0_ et _1.0.0.1_ représentent les deux versions des artefacts binaires. Chaque version comprend un dossier pour chaque service : _ServiceEUS_ (service USA Est) et _ServiceWUS_ (service USA Ouest). Chaque service dispose d’un ensemble fichier de modèles + fichier de paramètres pour la création d’un compte de stockage et d’un autre ensemble pour la création d’une application web. Le modèle d’application web appelle un package compressé qui contient les fichiers d’application web. Le fichier compressé est un artefact binaire stocké dans le dossier de fichiers binaires.
* Le dossier _Fichiers binaires_ : contient les artefacts binaires. Les dossiers _1.0.0.0_ et _1.0.0.1_ représentent les deux versions des artefacts binaires. Dans chaque version, il existe un fichier compressé pour la création de l’application web dans la région USA Ouest et un autre pour la création de l’application web dans la région USA Est.

Les deux versions (1.0.0.0 et 1.0.0.1) servent au [déploiement de révision](#deploy-the-revision). Même si les artefacts du modèle et les artefacts binaires présentent deux versions, seuls les artefacts binaires diffèrent entre les deux versions. Dans la pratique, les artefacts binaires sont mis à jour plus fréquemment que les artefacts de modèle.

1. Ouvrez _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json_ dans un éditeur de texte. Ce modèle de base permet de créer un compte de stockage.
1. Ouvrez _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json_.

    ![Didacticiel Azure Deployment Manager Créer un modèle d’application web](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Le modèle appelle un package de déploiement, qui contient les fichiers de l’application web. Dans ce tutoriel, le package compressé contient uniquement un fichier _index.html_.
1. Ouvrez _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json_.

    ![Didacticiel Azure Deployment Manager Créer un modèle d’application web paramètres containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    La valeur de `deployPackageUri` est le chemin au package de déploiement. Le paramètre contient une variable `$containerRoot`. La valeur de `$containerRoot` est fournie dans le [modèle de lancement](#create-the-rollout-template) en concaténant l’emplacement de la SAS source de l’artefact, l’artefact racine et `deployPackageUri`.
1. Ouvrez _\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html_.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    Le code HTML indique les informations sur la région et la version. Le fichier binaire dans le dossier _1.0.0.1_ indique _Version 1.0.0.1_. Après avoir déployé le service, vous pouvez consulter ces pages.
1. Consultez les autres fichiers d’artefact. Ils vous aideront à mieux comprendre le scénario.

Les artefacts du modèle sont utilisés par le modèle de topologie de service, et les artefacts binaires sont utilisés par le modèle de lancement. Le modèle de topologie et le modèle de lancement définissent une ressource Azure de source d’artefact, utilisée pour diriger Resource Manager vers le modèle et les artefacts des fichiers binaires utilisés lors du déploiement. Pour simplifier ce didacticiel, un seul compte de stockage est utilisé pour stocker les artefacts du modèle et les artefacts binaires. Les deux sources d’artefact pointent vers le même compte de stockage.

Exécutez le script PowerShell suivant pour créer un groupe de ressources, créer un conteneur de stockage, créer un conteneur d’objets blob, charger les fichiers téléchargés, puis créer un jeton SAS.

> [!IMPORTANT]
> `projectName` dans le script PowerShell sert à générer des noms pour les services Azure qui sont déployés dans ce tutoriel. Différents services Azure ont des exigences différentes quant aux noms. Pour garantir la réussite du déploiement, choisissez un nom comportant moins de 12 caractères avec uniquement des lettres minuscules et des chiffres.
> Enregistrez une copie du nom du projet. Vous utilisez le même `projectName` dans tout le tutoriel.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

Effectuez une copie de l’URL avec le jeton SAS. Cette URL est nécessaire pour remplir un champ dans les deux fichiers de paramètres : le fichier de paramètres de topologie et le fichier de paramètres de lancement.

Ouvrez le conteneur à partir du portail Azure et vérifiez que les dossiers des _fichiers binaires_ et des _modèles_ ainsi que les fichiers sont chargés.

## <a name="create-the-user-assigned-managed-identity"></a>Créer l’identité managée affectée par l'utilisateur

Plus loin dans ce didacticiel, vous déploierez un lancement. Une identité managée affectée à l’utilisateur est nécessaire pour effectuer les actions de déploiement (par exemple, déployer les applications web et le compte de stockage). Cette identité doit avoir accès à l’abonnement Azure sur lequel vous déployez le service et disposer des autorisations suffisantes pour procéder au déploiement complet de l’artefact.

Vous devez créer une identité managée affectée à l’utilisateur et configurer le contrôle d’accès pour votre abonnement.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Créez une [identité managée affectée à l’utilisateur](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
1. Dans le portail, sélectionnez **Abonnements** dans le menu de gauche, puis sélectionnez votre abonnement.
1. Sélectionnez **Contrôle d’accès (IAM)** , puis **Ajouter une attribution de rôle**.
1. Entrez ou sélectionnez les valeurs suivantes :

    ![Didacticiel Azure Deployment Manager Contrôle d’accès d’identité managée affectée à l’utilisateur](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Rôle** : accordez une autorisation suffisante pour pouvoir procéder au déploiement d’artefact (applications web et comptes de stockage). Pour ce didacticiel, sélectionnez **Contributeur**. En pratique, vous souhaitez restreindre les autorisations au minimum.
    * **Attribuer l’accès à** : sélectionnez **Identité managée affectée par l’utilisateur**.
    * Choisissez l’identité managée affectée à l’utilisateur que vous avez créée précédemment dans ce didacticiel.
1. Sélectionnez **Enregistrer**.

## <a name="create-the-service-topology-template"></a>Créer le modèle de topologie de service

Ouvrez _\ADMTemplates\CreateADMServiceTopology.json_.

### <a name="the-parameters"></a>Paramètres

Le modèle contient les paramètres suivants :

* `projectName` : ce nom est utilisé pour créer les noms des ressources Deployment Manager. Par exemple, avec **demo**, le nom de la topologie de service est **demo** ServiceTopology. Les noms de ressources sont définis dans la section `variables` du modèle.
* `azureResourcelocation` : pour simplifier ce tutoriel, toutes les ressources partagent cet emplacement, sauf spécification contraire.
* `artifactSourceSASLocation` : URI SAS vers le conteneur d’objets blob où le modèle d’unité de service et les fichiers de paramètres sont stockés pour le déploiement. Consultez [Préparer les artefacts](#prepare-the-artifacts).
* `templateArtifactRoot` : chemin décalé du conteneur d’objets blob où les modèles et les paramètres sont stockés. La valeur par défaut est _templates/1.0.0.0_. Ne modifiez pas cette valeur, sauf si vous souhaitez modifier la structure de dossiers décrite dans [Préparer les artefacts](#prepare-the-artifacts). Ce didacticiel utilise des chemins d’accès relatifs. Le chemin d’accès complet est construit en concaténant `artifactSourceSASLocation`, `templateArtifactRoot`, et `templateArtifactSourceRelativePath` (ou `parametersArtifactSourceRelativePath`).
* `targetSubscriptionID` : ID d’abonnement sur lequel les ressources Deployment Manager vont être déployées et facturées. Dans ce didacticiel, utilisez votre ID d’abonnement.

### <a name="the-variables"></a>Variables

La section Variables définit les noms des ressources, les emplacements Azure pour les deux services : `ServiceWUS` et `ServiceEUS` et les chemins d’artefact :

![Variables de modèle de topologie du didacticiel Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Comparer les chemins d’accès à l’artefact avec la structure de dossiers que vous avez chargée vers le compte de stockage. Notez que les chemins d'accès des artefacts sont relatifs. Le chemin d’accès complet est construit en concaténant `artifactSourceSASLocation`, `templateArtifactRoot`, et `templateArtifactSourceRelativePath` (ou `parametersArtifactSourceRelativePath`).

### <a name="the-resources"></a>Ressources

Au niveau racine, deux ressources sont définies : une *source d’artefact*, et une *topologie de service*.

Voici la définition de la source d’artefact :

![Source d’artefact de ressources de modèle de topologie de didacticiel Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

La capture d’écran suivante montre uniquement certaines parties de la topologie de service, des services et des définitions des unités de service :

![Topologie de service des ressources de modèle de topologie de didacticiel Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* `artifactSourceId` : permet d’associer la ressource de source d’artefact à la ressource de topologie de service.
* `dependsOn` : toutes les ressources de topologie de service dépendent de la ressource de source d’artefact.
* `artifacts` : pointe vers les artefacts du modèle. Utilise les chemins d’accès relatifs. Le chemin complet est construit en concaténant `artifactSourceSASLocation` (défini dans la source d’artefact), `artifactRoot` (défini dans la source d’artefact) et `templateArtifactSourceRelativePath` (ou `parametersArtifactSourceRelativePath`).

### <a name="topology-parameters-file"></a>Fichier de paramètres de topologie

Vous créez un fichier de paramètres utilisé avec le modèle de topologie.

1. Ouvrez _\ADMTemplates\CreateADMServiceTopology.Parameters.json_ dans Visual Studio Code ou n’importe quel éditeur de texte.
1. Entrez les valeurs des paramètres :

    * `projectName` : entrez une chaîne de 4 à 5 caractères. Ce nom est utilisé pour créer des noms de ressources Azure uniques.
    * `azureResourceLocation` : si vous ne connaissez pas bien les régions Azure, utilisez **centralus** dans ce tutoriel.
    * `artifactSourceSASLocation` : saisissez l’URI SAS vers le dossier racine (le conteneur d’objets blob) où le modèle d’unité de service et les fichiers de paramètres sont stockés pour le déploiement.  Consultez [Préparer les artefacts](#prepare-the-artifacts).
    * `templateArtifactRoot` : sauf si vous modifiez la structure de dossiers des artefacts, utilisez _templates/1.0.0.0_ pour ce tutoriel.

> [!IMPORTANT]
> Le modèle de topologie et le modèle de lancement partagent certains paramètres. Ces paramètres doivent avoir les mêmes valeurs. Il s’agit de `projectName`, d’`azureResourceLocation` et d’`artifactSourceSASLocation` (les deux sources d’artefact partagent le même compte de stockage dans ce tutoriel).

## <a name="create-the-rollout-template"></a>Créer le modèle de lancement

Ouvrez _\ADMTemplates\CreateADMRollout.json_.

### <a name="the-parameters"></a>Paramètres

Le modèle contient les paramètres suivants :

![Paramètres de modèle de lancement de didacticiel Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* `projectName` : ce nom est utilisé pour créer les noms des ressources Deployment Manager. Par exemple, avec **demo**, le nom de déploiement est **demo** Rollout. Les noms sont définis dans la section `variables` du modèle.
* `azureResourcelocation` : pour simplifier ce tutoriel, toutes les ressources Deployment Manager partagent cet emplacement, sauf spécification contraire.
* `artifactSourceSASLocation` : URI SAS vers le dossier racine (le conteneur d’objets blob) où le modèle d’unité de service et les fichiers de paramètres sont stockés pour le déploiement. Consultez [Préparer les artefacts](#prepare-the-artifacts).
* `binaryArtifactRoot` : La valeur par défaut est _binaries/1.0.0.0_. Ne modifiez pas cette valeur, sauf si vous souhaitez modifier la structure de dossiers décrite dans [Préparer les artefacts](#prepare-the-artifacts). Ce didacticiel utilise des chemins d’accès relatifs. Le chemin complet est construit en concaténant `artifactSourceSASLocation`, `binaryArtifactRoot` et le `deployPackageUri` spécifié dans _CreateWebApplicationParameters.json_. Consultez [Préparer les artefacts](#prepare-the-artifacts).
* `managedIdentityID` : l’identité managée affectée à l’utilisateur qui effectue les actions de déploiement. Voir [Créer l’identité managée affectée à l'utilisateur](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Variables

La section `variables` définit les noms des ressources. Assurez-vous que le nom de topologie de service, les noms de service et les noms d’unité de service correspondent aux noms définis dans le [modèle de topologie](#create-the-service-topology-template).

![Variables de modèle de lancement de didacticiel Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Ressources

Au niveau racine, trois ressources sont définies : une source d’artefact, une étape, et un lancement.

La définition de source d’artefact est identique à celle définie dans le modèle de topologie. Voir [Créer le modèle de topologie de service](#create-the-service-topology-template) pour plus d’informations.

La capture d’écran suivante montre la définition de l’étape `wait` :

![Attente/étape Ressources de modèle de lancement de didacticiel Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

La durée utilise la [norme ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (lettres majuscules requises) est un exemple d’attente de 1 minute.

La capture d’écran suivante montre uniquement certaines parties de la définition du lancement :

![Lancement de ressources de modèle de lancement de didacticiel Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* `dependsOn` : la ressource de lancement dépend de la ressource de source d’artefact et de n’importe quelle étape définie.
* `artifactSourceId` : permet d’associer la ressource de source d’artefact à la ressource de lancement.
* `targetServiceTopologyId` : permet d’associer la ressource de topologie de service à la ressource de lancement.
* `deploymentTargetId` : ID de ressource de l’unité de service de la ressource de topologie de service.
* `preDeploymentSteps` et `postDeploymentSteps` : contient les étapes de lancement. Dans le modèle, une étape `wait` est appelée.
* `dependsOnStepGroups` : configure les dépendances entre les groupes d’étapes.

### <a name="rollout-parameters-file"></a>Fichier de paramètres du lancement

Vous créez un fichier de paramètres à utiliser avec le modèle de lancement.

1. Ouvrez _\ADMTemplates\CreateADMRollout.Parameters.json_ dans Visual Studio Code ou n’importe quel éditeur de texte.
1. Entrez les valeurs des paramètres :

    * `projectName` : entrez une chaîne de 4 à 5 caractères. Ce nom est utilisé pour créer des noms de ressources Azure uniques.
    * `azureResourceLocation` : Spécifiez un emplacement Azure.
    * `artifactSourceSASLocation` : saisissez l’URI SAS vers le dossier racine (le conteneur d’objets blob) où le modèle d’unité de service et les fichiers de paramètres sont stockés pour le déploiement.  Consultez [Préparer les artefacts](#prepare-the-artifacts).
    * `binaryArtifactRoot` : sauf si vous modifiez la structure de dossiers des artefacts, utilisez _binaries/1.0.0.0_ pour ce tutoriel.
    * `managedIdentityID` : entrez l’identité managée affectée par l’utilisateur. Voir [Créer l’identité managée affectée à l'utilisateur](#create-the-user-assigned-managed-identity). La syntaxe est :

        ```json
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Le modèle de topologie et le modèle de lancement partagent certains paramètres. Ces paramètres doivent avoir les mêmes valeurs. Il s’agit de `projectName`, d’`azureResourceLocation` et d’`artifactSourceSASLocation` (les deux sources d’artefact partagent le même compte de stockage dans ce tutoriel).

## <a name="deploy-the-templates"></a>Déployer les modèles

Azure PowerShell peut être utilisé pour déployer les modèles.

1. Exécutez le script pour déployer la topologie de service.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    Si vous exécutez ce script à partir d’une autre session PowerShell que celle dans laquelle vous avez exécuté le script [Préparer les artefacts](#prepare-the-artifacts), vous devez d’abord remplir à nouveau les variables, ce qui inclut `$resourceGroupName` et `$filePath`.

    > [!NOTE]
    > `New-AzResourceGroupDeployment` est un appel asynchrone. Le message de réussite signifie uniquement que le déploiement a pu commencer. Pour vérifier le déploiement, consultez les étapes 2 et 4 de cette procédure.

1. Vérifiez que la topologie de service et les ressources soulignées ont été créées avec succès à l’aide du portail Azure :

    ![Ressources de topologie de service déployées de didacticiel Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    L’option **Afficher les types masqués** doit être sélectionnée pour voir les ressources.

1. <a id="deploy-the-rollout-template"></a>Déployez le modèle de lancement :

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

1. Vérifiez la progression du lancement à l’aide du script PowerShell suivant :

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Les cmdlets PowerShell de Deployment Manager doivent être installées avant de pouvoir exécuter ce cmdlet. Consultez les [Conditions préalables](#prerequisites). Le paramètre `-Verbose` peut être utilisé pour afficher l’intégralité de la sortie.

    L’exemple suivant affiche l’état d’exécution :

    ```Output
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Une fois le lancement déployé, vous devez voir deux groupes de ressources supplémentaires créés, un pour chaque service.

## <a name="verify-the-deployment"></a>Vérifier le déploiement

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Accédez aux applications web nouvellement créées sous les nouveaux groupes de ressources créés par le déploiement du lancement.
1. Ouvrez l’application web dans un navigateur web. Vérifiez l’emplacement et la version sur le fichier _index.html_.

## <a name="deploy-the-revision"></a>Déployer la révision

Lorsque vous avez une nouvelle version (1.0.0.1) pour l’application web, vous pouvez utiliser la procédure suivante pour la redéployer.

1. Ouvrez _CreateADMRollout.Parameters.json_.
1. Mettez à jour `binaryArtifactRoot` à _binaries/1.0.0.1_.
1. Redéployez le lancement, comme indiqué dans [Déployer les modèles](#deploy-the-rollout-template).
1. Vérifiez le déploiement, comme indiqué dans [Vérifier le déploiement](#verify-the-deployment). La page web doit afficher la version 1.0.0.1.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
1. Utilisez le champ **Filter by name** (Filtrer par nom) afin de réduire les groupes de ressources créés dans ce didacticiel.

    * **&lt;projectName>rg** : contient les ressources Deployment Manager.
    * **&lt;projectName>ServiceWUSrg** : contient les ressources définies par ServiceWUS.
    * **&lt;projectName>ServiceEUSrg** : contient les ressources définies par ServiceEUS.
    * Le groupe de ressources pour l’identité managée définie par l’utilisateur.
1. Sélectionnez le nom du groupe de ressources.
1. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.
1. Répétez les deux dernières étapes pour supprimer d’autres groupes de ressources créés lors de ce didacticiel.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser Azure Deployment Manager. Pour intégrer la supervision de l’intégrité à Azure Deployment Manager, consultez [Tutoriel : Utiliser le contrôle d’intégrité dans Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
