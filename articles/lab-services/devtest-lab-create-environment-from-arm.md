---
title: Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec les modèles Azure Resource Manager | Microsoft Docs
description: Découvrez comment créer des environnements de plusieurs machines virtuelles et des ressources PaaS dans Azure DevTest Labs à partir d’un modèle Azure Resource Manager
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: spelluru
ms.openlocfilehash: 96e3a24b0c9f9ab21652ffcd1b29deeb512581e5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796931"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec les modèles Azure Resource Manager

Le [portail Azure ](https://go.microsoft.com/fwlink/p/?LinkID=525040) vous permet [d’ajouter facilement une machine virtuelle à la fois à un laboratoire](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). Toutefois, si l’environnement contient plusieurs machines virtuelles, chaque machine virtuelle doit être créée individuellement. Pour des scénarios comme une application web à plusieurs niveaux ou une batterie de serveurs SharePoint, un mécanisme est nécessaire pour permettre la création de plusieurs machines virtuelles en une seule étape. Les modèles Azure Resource Manager, vous permettent désormais de définir l’infrastructure et la configuration de votre solution Azure et de déployer de manière répétée plusieurs machines virtuelles dans un état cohérent. Cette fonctionnalité permet de bénéficier des avantages suivants :

- Les modèles Azure Resource Manager sont chargés directement à partir de votre dépôt de contrôle de code source (GitHub ou Azure DevOps Services Git).
- Suite à la configuration, vos utilisateurs peuvent créer un environnement en choisissant un modèle Azure Resource Manager à partir du portail Azure, comme avec les autres types de [bases de machines virtuelles](./devtest-lab-comparing-vm-base-image-types.md).
- Les ressources PaaS Azure peuvent être approvisionnées dans un environnement à partir d’un modèle Azure Resource Manager, en plus des machines virtuelles IaaS.
- Le coût des environnements peut être suivi dans le laboratoire en plus des machines virtuelles créées par d’autres types de bases.
- Une fois créées, les ressources PaaS s’affichent dans le suivi des coûts. Notez que l’arrêt automatique des machines virtuelles ne s’applique pas aux ressources PaaS.

Découvrez les nombreux [avantages de l’utilisation des modèles Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) pour déployer, mettre à jour et supprimer toutes vos ressources lab en une seule opération.

> [!NOTE]
> Si vous utilisez un modèle Resource Manager comme base pour créer des machines virtuelles de laboratoire, sachez qu’il existe des différences entre la création d’un groupe de machines virtuelles et celle de plusieurs machines virtuelles individuelles. La rubrique [Utiliser un modèle Azure Resource Manager de machine virtuelle](devtest-lab-use-resource-manager-template.md) explique ces différences en détail.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="devtest-labs-public-environments"></a>Environnements publics DevTest Labs
Azure DevTest Labs a un [dépôt public de modèles Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments), que vous pouvez utiliser pour créer des environnements sans devoir vous connecter à une source GitHub externe par vous-même. Ce dépôt inclut des modèles fréquemment utilisés, comme Azure Web Apps, Cluster Service Fabric et l’environnement de batterie de serveurs SharePoint de développement. Cette fonctionnalité est similaire au dépôt public d’artefacts qui est inclus pour chaque lab que vous créez. Le dépôt d’environnements vous permet de vous familiariser rapidement avec des modèles d’environnement préalablement créés avec un nombre minimal de paramètres d’entrée, pour un démarrage en douceur avec les ressources PaaS au sein des labs. Pour plus d’informations, consultez [Configurer et utiliser des environnements publics dans DevTest Labs](devtest-lab-configure-use-public-environments.md).

## <a name="configure-your-own-template-repositories"></a>Configurer vos propres dépôts de modèle
Dans le cadre des bonnes pratiques liées à l’Infrastructure as Code et à la Configuration as Code, des modèles d’environnement doivent être gérés dans le contrôle de code source. Azure DevTest Labs suit cette pratique et charge tous les modèles Azure Resource Manager directement à partir de vos dépôts GitHub ou Azure DevOps Services Git. Par conséquent, les modèles Resource Manager peuvent être utilisés tout le long du cycle de publication, de l’environnement de test jusqu’à l’environnement de production.

Découvrez les modèles créés par l’équipe DevTest Labs dans le [dépôt GitHub public](https://github.com/Azure/azure-devtestlab/tree/master/Environments). Dans ce dépôt public, vous pouvez afficher les modèles partagés par d’autres utilisateurs et vous pouvez les utiliser directement ou les personnaliser en fonction de vos besoins. Après avoir créé votre modèle, stockez-le dans ce dépôt pour le partager avec d’autres utilisateurs. Vous pouvez également configurer votre propre dépôt Git avec des modèles pouvant être utilisés pour configurer des environnements dans le cloud. 

Quelques règles sont à prendre en compte pour organiser vos modèles Azure Resource Manager dans un dépôt :

- Le fichier de modèle principal doit être nommé `azuredeploy.json`. 

    ![Fichiers de modèle Azure Resource Manager clés](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Si vous souhaitez utiliser des valeurs de paramètres définies dans un fichier de paramètres, le fichier de paramètres doit être nommé `azuredeploy.parameters.json`.
- Vous pouvez utiliser les paramètres `_artifactsLocation` et `_artifactsLocationSasToken` pour créer la valeur de l’URI parametersLink et permettre à DevTest Labs de gérer automatiquement les modèles imbriqués. Pour plus d’informations, consultez [How Azure DevTest Labs makes nested Resource Manager template deployments easier for testing environments](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/).
- Les métadonnées peuvent être définies pour spécifier le nom d’affichage et la description du modèle. Ces métadonnées doivent être dans un fichier nommé `metadata.json`. Le fichier de métadonnées d’exemple suivant montre comment spécifier le nom d’affichage et la description : 

    ```json
    { 
        "itemDisplayName": "<your template name>", 
        "description": "<description of the template>" 
    }
    ```

Les étapes suivantes vous guident pour ajouter un dépôt dans votre laboratoire à l’aide du portail Azure. 

1. Connectez-vous au [Portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.   
1. Dans le volet **Vue d’ensemble** du laboratoire, sélectionnez **Configuration et stratégies**.

    ![Configuration et stratégies](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. À partir de la liste de paramètres **Configuration et stratégies**, sélectionnez **Dépôts**. Le volet **Dépôts** liste les dépôts qui ont été ajoutés au laboratoire. Un dépôt nommé `Public Repo` est généré automatiquement pour tous les laboratoires et se connecte au [dépôt GitHub de DevTest Labs](https://github.com/Azure/azure-devtestlab) qui contient plusieurs artefacts de machine virtuelle que vous pouvez utiliser.

    ![Dépôt public](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Sélectionnez **Ajouter +** pour ajouter votre dépôt de modèle Azure Resource Manager.
1. Lorsque le second volet **Dépôts** s’ouvre, entrez les informations nécessaires comme suit :
    - **Nom** : entrez le nom du référentiel utilisé dans le laboratoire.
    - **URL de clonage Git** : entrez l’URL de clonage Git HTTPS provenant de GitHub ou Azure DevOps Services.  
    - **Branche** : entrez le nom de la branche pour accéder à vos définitions de modèle Azure Resource Manager. 
    - **Jeton d’accès personnel** : le jeton d’accès personnel est utilisé pour accéder en toute sécurité à votre dépôt. Pour obtenir votre jeton à partir d’Azure DevOps Services, sélectionnez **&lt;votre_nom> > Mon profil > Sécurité > Public access token (Jeton d’accès public)**. Pour obtenir votre jeton à partir de GitHub, sélectionnez votre avatar, puis sélectionnez **Paramètres > Public access token (Jeton d’accès public)**. 
    - **Chemins d’accès du dossier**  : en utilisant l’un des deux champs d’entrée, entrez le chemin d’accès du dossier qui commence par une barre oblique - / - et est relatif à votre Url de clonage Git de vos définitions d’artefacts (premier champ d’entrée) ou de vos définitions de modèle Azure.   
    
        ![Dépôt public](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. Une fois tous les champs nécessaires renseignés et validés, cliquez sur **Enregistrer**.

La section suivante vous guidera dans la création d’environnements à partir d’un modèle Azure Resource Manager.

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Créer un environnement à partir d’un modèle Resource Manager à l’aide du portail Azure

Une fois qu’un dépôt de modèles Azure Resource Manager a été configuré dans le laboratoire, vos utilisateurs de laboratoire peuvent créer un environnement à l’aide du portail Azure en procédant comme suit :

1. Connectez-vous au [Portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.   
1. Dans le volet du laboratoire, sélectionnez **Ajouter +**.
1. Le volet **Choisir une base** affiche les images de base que vous pouvez utiliser avec les modèles Azure Resource Manager répertoriés en premier. Sélectionnez le modèle Azure Resource Manager souhaité.

    ![Choisir une base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. Dans le volet **Ajouter**, entrez la valeur **Nom de l’environnement**. Le nom de l’environnement est ce qui est affiché aux utilisateurs dans le laboratoire. Les autres champs d’entrée sont définis dans le modèle Azure Resource Manager. Si les valeurs par défaut sont définies dans le modèle ou si le fichier `azuredeploy.parameter.json` est présent, les valeurs par défaut sont affichées dans ces champs d’entrée. Pour les paramètres de type *chaîne sécurisée*, vous pouvez utiliser les secrets stockés dans votre coffre de clés Azure. Pour en savoir plus sur l’enregistrement des secrets dans un coffre de clés et leur utilisation lors de la création des ressources de laboratoire, consultez [Stocker les secrets dans Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

    ![Volet Ajouter](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Il existe plusieurs valeurs de paramètres, même quand elles sont spécifiées, qui sont affichées sous forme de valeurs vides. Par conséquent, si les utilisateurs affectent ces valeurs à des paramètres dans un modèle Azure Resource Manager, DevTest Labs n’affiche pas les valeurs. Au lieu de cela, des champs d’entrée vides sont affichés dans lesquels les utilisateurs de laboratoire doivent entrer une valeur lors de la création de l’environnement.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Sélectionnez **Ajouter** pour créer l’environnement. L’environnement lance immédiatement l’approvisionnement avec l’affichage de l’état dans la liste **Mes machines virtuelles**. Un groupe de ressources est automatiquement créé par le laboratoire pour approvisionner toutes les ressources définies dans le modèle Azure Resource Manager.
1. Une fois que l’environnement est créé, sélectionnez-le dans la liste **Mes machines virtuelles** pour ouvrir le volet du groupe de ressources et parcourir les ressources approvisionnées dans l’environnement.
    
    ![Liste Mes machines virtuelles](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   Vous pouvez également développer l’environnement pour afficher uniquement la liste des machines virtuelles qui sont approvisionnées dans l’environnement.
    
    ![Liste Mes machines virtuelles](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Cliquez sur l’un des environnements pour afficher les actions disponibles comme appliquer des artefacts, attacher des disques de données, modifier le temps d’arrêt automatique, etc.

    ![Actions de l’environnement](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="automate-deployment-of-environments"></a>Automatiser le déploiement des environnements
Azure DevTest Labs offre la possibilité d’utiliser un [modèle Azure Resource Manager Management](../azure-resource-manager/resource-group-authoring-templates.md) pour créer un environnement avec un ensemble de ressources dans le laboratoire. Ces environnements peuvent contenir toutes les ressources Azure qui peuvent être créés à l’aide de modèles Resource Manager. Les environnements de dev/test permettent aux utilisateurs de facilement déployer des infrastructures complexes de manière cohérente dans les limites du laboratoire. Actuellement, l’ajout d’un environnement à un laboratoire à l’aide du portail Azure est possible lors de sa création qu’une seule fois, mais dans un environnement de développement ou un cas de test, où plusieurs créations se produisent, un déploiement automatisé permet une expérience améliorée.

Complète les étapes suivantes de la [configurer vos propres référentiels modèle](#configure-your-own-template-repositories) section avant de continuer : 

1. Créez le modèle Resource Manager qui définit les ressources en cours de création. 
2. Configurer le modèle Resource Manager dans Git un référentiel. 
3. Connectez le référentiel Git au laboratoire. 

### <a name="powershell-script-to-deploy-the-resource-manager-template"></a>Script PowerShell pour déployer le modèle Resource Manager
Enregistrez le script PowerShell dans la section suivante sur votre disque dur (par exemple : deployenv.ps1) et exécutez le script une fois que vous spécifiez des valeurs pour SubscriptionId, ResourceGroupName, LabName, le nom du référentiel, TemplateName (dossier) dans le référentiel Git, EnvironmentName.

```powershell
./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"  
```

#### <a name="sample-script"></a>Exemple de script
Voici l’exemple de script pour créer un environnement dans votre laboratoire. Les commentaires dans le script vous aider à mieux comprendre le script. 

```powershell
#Requires -Module Az.Resources

[CmdletBinding()]

param (
# ID of the Azure Subscription where the lab is created.
[string] [Parameter(Mandatory=$true)] $SubscriptionId,

# Name of the lab (existing) in which to create the environment.
[string] [Parameter(Mandatory=$true)] $LabName,

# Name of the connected repository in the lab. 
[string] [Parameter(Mandatory=$true)] $RepositoryName,

# Name of the template (folder name in the Git repository) based on which the environment will be created.
[string] [Parameter(Mandatory=$true)] $TemplateName,

# Name of the environment to be created in the lab.
[string] [Parameter(Mandatory=$true)] $EnvironmentName,

# The parameters to be passed to the template. Each parameter is prefixed with “-param_”. 
# For example, if the template has a parameter named “TestVMName” with a value of “MyVMName”, the string in $Params will have the form: `-param_TestVMName MyVMName`. 
# This convention allows the script to dynamically handle different templates.
[Parameter(ValueFromRemainingArguments=$true)]
    $Params
)

# Save this script as the deployenv.ps1 file
# Run the script after you specify values for SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (folder) in the Git repo, EnvironmentName
# ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"    

# Comment this statement to completely automate the environment creation.    
# Sign in to Azure. 
Connect-AzAccount

# Select the subscription that has the lab.  
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

# Get information about the user, specifically the user ID, which is used later in the script.  
$UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)
        
# Get information about the lab such as lab location. 
$lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName 
if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." } 
    
# Get information about the repository in the lab. 
$repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
    -ResourceName $LabName `
    -ApiVersion 2016-05-15 `
    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." } 

# Get information about the Resource Manager template based on which the environment will be created. 
$template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
    -ResourceName "$LabName/$($repository.Name)" `
    -ApiVersion 2016-05-15 `
    | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." } 

# Build the template parameters with parameter name and values.     
$parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
$templateParameters = @()

# The custom parameters need to be extracted from $Params and formatted as name/value pairs.
$Params | ForEach-Object {
    if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
        $name = $Matches[1]                
    } elseif ( $name ) {
        $templateParameters += @{ "name" = "$name"; "value" = "$_" }
        $name = $null #reset name variable
    }
}

# Once name/value pairs are isolated, create an object to hold the necessary template properties
$templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; } 

# Now, create or deploy the environment in the lab by using the New-AzResource command. 
New-AzResource -Location $Lab.Location `
    -ResourceGroupName $lab.ResourceGroupName `
    -Properties $templateProperties `
    -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
    -ResourceName "$LabName/$UserId/$EnvironmentName" `
    -ApiVersion '2016-05-15' -Force 
 
Write-Output "Environment $EnvironmentName completed."
```

Vous pouvez également utiliser Azure CLI pour déployer des ressources des modèles Resource Manager. Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli).

> [!NOTE]
> Seul un utilisateur disposant des autorisations de propriétaire de laboratoire peut créer des machines virtuelles à partir d’un modèle Resource Manager avec Azure PowerShell. Si vous souhaitez automatiser la création de machines virtuelles à l’aide d’un modèle Resource Manager et que vous disposez uniquement d’autorisations utilisateur, vous pouvez utiliser la commande [ **az lab vm create** dans l’interface CLI](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).

### <a name="general-limitations"></a>Limitations générales 

Prenez en compte les limitations suivantes lors de l’utilisation d’un modèle Resource Manager dans DevTest Labs :

- Un modèle Resource Manager que vous créez ne peut pas faire référence à des ressources existantes ; il ne peut faire référence qu’à de nouvelles ressources. De plus, si vous disposez d’un modèle Resource Manager que vous déployez généralement en dehors de DevTest Labs et qu’il contient des références à des ressources existantes, il ne peut pas être utilisé dans le laboratoire.

   La seule exception est que vous **pouvez** faire référence à un réseau virtuel existant. 

- Des formules ne peuvent pas être créées à partir de machines virtuelles de laboratoire qui ont été créées à partir d’un modèle Resource Manager. 

- Des images personnalisées ne peuvent pas être créées à partir de machines virtuelles de laboratoire qui ont été créées à partir d’un modèle Resource Manager. 

- La plupart des stratégies ne sont pas évaluées lorsque vous déployez des modèles Resource Manager.

   Par exemple, vous pouvez disposer d’une stratégie de laboratoire qui spécifie qu’un utilisateur peut uniquement créer cinq machines virtuelles. Toutefois, un utilisateur peut déployer un modèle Resource Manager qui crée des dizaines de machines virtuelles. Les stratégies qui ne sont pas évaluées incluent :

   - Nombre de machines virtuelles par utilisateur
   - Nombre de machines virtuelles Premium par utilisateur de laboratoire
   - Nombre de disques Premium par utilisateur de laboratoire


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>Configurer les droits d’accès au groupe de ressources d’environnement pour les utilisateurs de laboratoire

Les utilisateurs de laboratoire peuvent déployer un modèle Resource Manager. Par défaut, ils disposent toutefois de droits d’accès en lecture, ce qui signifie qu’ils ne peuvent pas modifier les ressources dans un groupe de ressources d’environnement. Par exemple, ils ne peuvent pas arrêter ou démarrer leurs ressources.

Suivez ces étapes pour accorder à vos utilisateurs de laboratoire des droits d’accès Contributeur. Par la suite, lorsqu’ils déploieront un modèle Resource Manager, ils seront en mesure de modifier les ressources dans cet environnement. 


1. Dans le volet **Vue d’ensemble** de votre laboratoire, sélectionnez **Configuration et stratégies**.
1. Sélectionnez **Paramètres du lab**.
1. Dans le volet Paramètres du lab, sélectionnez **Contributeur** pour accorder des autorisations d’écriture aux utilisateurs de laboratoire.

    ![Configurer les droits d’accès d’utilisateur de laboratoire](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
* Après la création d’une machine virtuelle, vous pouvez vous connecter à celle-ci en sélectionnant **Connexion** dans le volet de gestion de la machine virtuelle.
* Vous pouvez afficher et gérer les ressources d’un environnement en sélectionnant celui-ci dans la liste **Mes machines virtuelles** de votre laboratoire. 
* Explorez les [modèles Azure Resource Manager de la galerie de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates).
