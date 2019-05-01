---
title: Ajouter un référentiel d’artefacts à votre laboratoire dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment ajouter un référentiel d’artefacts à votre laboratoire dans Azure DevTest labs.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: c1e74efa9cf99e8510ea17aedc840ce3b0731c3b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916673"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Ajouter un référentiel d’artefacts à votre laboratoire dans DevTest Labs
DevTest Labs vous permet de spécifier un artefact à ajouter à une machine virtuelle au moment de la création de la machine virtuelle ou une fois que la machine virtuelle est créée. Cet artefact peut être un outil ou une application que vous souhaitez installer sur la machine virtuelle. Artefacts sont définies dans un fichier JSON chargé à partir d’un référentiel GitHub ou VSTS Git. 

Le [référentiel d’artefacts public](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), géré par DevTest Labs, fournit de nombreux outils courants pour Windows et Linux. Un lien vers ce dépôt est automatiquement ajouté à votre laboratoire. Vous pouvez créer votre propre référentiel d’artefacts avec des outils spécifiques qui ne sont pas disponibles dans le référentiel d’artefacts public. Pour en savoir plus sur la création d’artefacts personnalisés, consultez [créer des artefacts personnalisés](devtest-lab-artifact-author.md).

Cet article fournit des informations sur l’ajout de votre référentiel d’artefacts personnalisés en utilisant le portail Azure, les modèles Azure Resource Manager et Azure PowerShell. Vous pouvez automatiser l’ajout d’un référentiel d’artefacts à un laboratoire en écrivant des scripts PowerShell ou CLI. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables
Pour ajouter un dépôt à votre laboratoire, obtenez d’abord des informations essentielles de votre dépôt. Les sections suivantes décrivent comment obtenir les informations requises pour les dépôts qui sont hébergés sur **GitHub** ou **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obtenir l’URL de clonage du dépôt GitHub et le jeton d’accès personnel

1. Accédez à la page d’accueil du dépôt GitHub contenant l’artefact ou les définitions de modèle Resource Manager.
2. Sélectionnez **Cloner ou télécharger**.
3. Pour copier l’URL dans le Presse-papiers, sélectionnez le bouton **URL de clonage HTTPS**. Enregistrez l’URL pour une utilisation ultérieure.
4. Dans le coin supérieur droit de GitHub, Sélectionnez l’image de profil, puis sélectionnez **Paramètres**.
5. Dans le **paramètres personnels** menu de gauche, sélectionnez **les paramètres du développeur**.
6. Sélectionnez **jetons d’accès personnels** sur le menu de gauche.
7. Sélectionnez **Générer un nouveau jeton**.
8. Dans la page **Nouveau jeton d’accès personnel**, sous **Description du jeton**, entrez une description. Accepter les éléments par défaut sous **Sélectionner des étendues**, puis sélectionnez **Générer un jeton**.
9. Enregistrez le jeton généré. Vous l’utiliserez ultérieurement.
10. Fermez GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Obtenir l’URL du clone Azure Repos et le jeton d’accès personnel
1. Accédez à la page d’accueil de votre collection d’équipe (par exemple, https://contoso-web-team.visualstudio.com)), puis sélectionnez votre projet.
2. Sur la page d’accueil du projet, sélectionnez **Code**.
3. Pour afficher l’URL de clonage, sélectionnez dans la page **Code** du projet l’option **Cloner**.
4. Enregistrez l’URL. Vous l’utiliserez ultérieurement.
5. Pour créer un jeton d’accès personnel, dans le menu déroulant du compte d’utilisateur, sélectionnez **Mon profil**.
6. Sur la page d’informations du profil, sélectionnez **Sécurité**.
7. Sous l’onglet **Sécurité**, sélectionnez **Ajouter**.
8. Dans la page **Créer un jeton d’accès personnel** :
   1. Entrez une **Description** pour le jeton.
   2. Dans la liste **Expire dans**, sélectionnez **180 jours**.
   3. Dans la liste **Comptes**, sélectionnez **Tous les comptes accessibles**.
   4. Sélectionnez l’option **Toutes les étendues**.
   5. Sélectionnez **Créer le jeton**.
9. Le nouveau jeton apparaît dans la liste **Jetons d’accès personnels**. Sélectionnez **Copier le jeton**puis enregistrez la valeur du jeton pour utilisation ultérieure.
10. Continuer pour vous connecter votre laboratoire à la section de référentiel.

## <a name="use-azure-portal"></a>Utiliser le portail Azure
Cette section fournit les étapes pour ajouter un référentiel d’artefacts à un laboratoire dans le portail Azure. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste de services.
3. Dans la liste de laboratoires, sélectionnez votre laboratoire. 
4. Sélectionnez **Configuration et stratégies** sur le menu de gauche.
5. Sélectionnez **référentiels** sous **des ressources externes** section dans le menu de gauche.
6. Sélectionnez **+ ajouter** sur la barre d’outils.

    ![Bouton pour ajouter un dépôt](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Sur le **référentiels** , spécifiez les informations suivantes :
   1. **Nom**. Entrez un nom pour le dépôt.
   2. **URL Git Clone**. Entrez l’URL de clonage Git HTTPS que vous avez précédemment copiée à partir de GitHub ou d’Azure DevOps Services.
   3. **Branche**. Pour obtenir vos définitions, entrez la branche.
   4. **Jeton d’accès personnel**. Entrez le jeton d’accès personnel que vous avez obtenu à partir de GitHub ou Azure DevOps Services.
   5. **Chemins de dossiers**. Entrez au moins un chemin de dossier relatif à l’URL du clone contenant votre artefact ou vos définitions de modèle Resource Manager. Quand vous spécifiez un sous-répertoire, veillez à inclure la barre oblique dans le chemin du dossier.

        ![Zone Référentiels](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Sélectionnez **Enregistrer**.

## <a name="use-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager
Modèles Azure Resource Management (Azure Resource Manager) sont des fichiers JSON qui décrivent les ressources Azure que vous souhaitez créer. Pour plus d’informations sur ces modèles, consultez [modèles de création Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Cette section fournit les étapes pour ajouter un référentiel d’artefacts à un laboratoire à l’aide d’un modèle Azure Resource Manager.  Le modèle crée le laboratoire si elle n’existe pas déjà. 

### <a name="template"></a>Modèle
L’exemple de modèle utilisé dans cet article rassemble les informations suivantes par le biais de paramètres. La plupart des paramètres ont des valeurs par défaut intelligentes, mais il existe quelques valeurs doivent être spécifiés. Vous devez spécifier le nom de laboratoire, URI pour le référentiel d’artefacts et le jeton de sécurité pour le référentiel. 

- Nom de laboratoire.
- Nom d’affichage pour le référentiel d’artefacts dans l’interface utilisateur de DevTest Labs (IU). La valeur par défaut est : `Team Repository`.
- URI vers le référentiel (exemple : `https://github.com/<myteam>/<nameofrepo>.git` ou `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Branche du référentiel qui contient les artefacts. La valeur par défaut est : `master`.
- Nom du dossier qui contient les artefacts. La valeur par défaut est : `/Artifacts`.
- Type de référentiel. Valeurs autorisées sont `VsoGit` ou `GitHub`.
- Jeton d’accès pour le référentiel. 

    ```json
    {
    
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Déployer le modèle
Il existe quelques façons de déployer le modèle dans Azure et que la ressource créée, si elle n’existe pas, ou mis à jour, s’il existe. Pour plus d’informations, consultez les articles suivants :

- [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [Déployer des ressources à l’aide de modèles Resource Manager et du Portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)

Poursuivons et apprenez à déployer le modèle dans PowerShell. Applets de commande utilisées pour déployer le modèle sont spécifiques au contexte, donc le locataire actuel et l’abonnement actuel sont utilisées. Utilisez [Set-AzContext](/powershell/module/az.accounts/set-azcontext) avant de déployer le modèle, si nécessaire, pour modifier le contexte.

Tout d’abord, créez un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Si le groupe de ressources que vous souhaitez utiliser déjà existe, ignorez cette étape.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Ensuite, créez un déploiement vers le groupe de ressources à l’aide [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Cette applet de commande applique les modifications de ressources vers Azure. Plusieurs déploiements de ressources peuvent être apportées à n’importe quel groupe de ressources donné. Si vous déployez plusieurs fois au même groupe de ressources, assurez-vous que le nom de chaque déploiement est unique.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Une fois AzResourceGroupDeployment de nouveau exécuté avec succès, la commande affiche des informations importantes telles que l’état d’approvisionnement (doit être réussi) et des sorties pour le modèle.
 
## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell 
Cette section fournit un exemple de script PowerShell qui peut être utilisé pour ajouter un référentiel d’artefacts à un laboratoire. Si vous n’avez pas Azure PowerShell, consultez [comment installer et configurer Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) pour obtenir des instructions détaillées pour l’installer.

### <a name="full-script"></a>Script complet
Voici le script complet, y compris des messages détaillés et les commentaires :

**Nouvelle DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab. 

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>

 
[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',
    
    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,
    
    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"@

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"
 
Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Exécution du script PowerShell
L’exemple suivant montre comment exécuter le script : 

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>parameters
L’exemple de script PowerShell dans cet article prend les paramètres suivants :

| Paramètre | Description | 
| --------- | ----------- | 
| LabName | Le nom de l’atelier. |
| ArtifactRepositoryName | Nom pour le nouveau dépôt d’artefact. Le script crée un nom aléatoire pour le référentiel, s’il n’est pas spécifié. |
| ArtifactRepositoryDisplayName | Nom d’affichage pour le référentiel d’artefacts. Il s’agit du nom qui s’affiche dans le portail Azure (https://portal.azure.com) lors de l’affichage de tous les référentiels d’artefact pour un laboratoire. |
| RepositoryUri | URI vers le référentiel. Exemples : `https://github.com/<myteam>/<nameofrepo>.git` ou `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.| 
| RepositoryBranch | Branche dans les artefacts fichiers se trouvent. La valeur par défaut est « master ». | 
| FolderPath | Dossier sous lequel vous pouvez trouver les artefacts. Valeur par défaut est ' / artefacts |
| PersonalAccessToken | Jeton de sécurité pour accéder au référentiel GitHub ou VSOGit. Consultez la section conditions préalables pour obtenir des instructions obtenir un jeton accès personnel |
| SourceType | Indique si les artefacts est référentiel VSOGit ou GitHub. |

Le référentiel lui-même devez interne nom pour l’identification, qui est différente du nom d’affichage est visible dans le portail Azure. Vous ne voyez pas le nom interne à l’aide du portail Azure, mais vous voyez lorsque vous utilisez l’API REST Azure ou Azure PowerShell. Le script fournit un nom, s’il n’est pas spécifié par l’utilisateur de notre script.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Commandes PowerShell utilisées dans le script

| Commande PowerShell | Notes |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Cette commande est utilisée pour obtenir des informations sur le laboratoire telles que son emplacement. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Il n’existe aucune commande spécifique pour l’ajout des référentiels d’artefact. Le modèle générique [New-AzResource](/powershell/module/az.resources/new-azresource) applet de commande fait le travail. Cette applet de commande a besoin le **ResourceId** ou **ResourceName** et **ResourceType** paire connaître le type de ressource à créer. Cet exemple de script utilise le nom de la ressource et de la paire de type de ressource. <br/><br/>Notez que vous créez la source de dépôt d’artefact dans le même emplacement et sous le même groupe de ressources que le laboratoire.|

Le script ajoute une nouvelle ressource à l’abonnement actuel. Utilisez [Get-AzContext](/powershell/module/az.accounts/get-azcontext) pour afficher ces informations. Utilisez [Set-AzContext](/powershell/module/az.accounts/set-azcontext) pour définir le locataire actuel et l’abonnement.

La meilleure façon de découvrir le nom de la ressource et les informations de type de ressource consiste à utiliser le [Test Drive Azure REST API](https://azure.github.io/projects/apis/) site Web. Découvrez le [DevTest Labs : 2016-05-15](https://aka.ms/dtlrestapis) fournisseur de l’API REST disponibles pour le fournisseur de dev/test. Les utilisateurs de script l’ID de ressource suivante. 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
Le type de ressource est que tout indiqué après 'providers' dans l’URI, à l’exception des éléments répertoriés dans les accolades. Le nom de ressource est tout indiqué dans les accolades. Si plusieurs éléments est attendu pour le nom de ressource, séparez chaque élément avec une barre oblique, comme nous l’avons fait. 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Étapes suivantes
- [Spécifier des artefacts obligatoires pour votre laboratoire dans Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Créer des artefacts personnalisés pour votre machine virtuelle DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnostiquer les échecs d’artefact dans le laboratoire](devtest-lab-troubleshoot-artifact-failure.md)

