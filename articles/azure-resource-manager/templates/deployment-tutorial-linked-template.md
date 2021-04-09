---
title: 'Tutoriel : Déployer un modèle lié'
description: Découvrez comment déployer un modèle lié
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8f2bbd327adca6eef62d5e79f422f61d460ea7a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589281"
---
# <a name="tutorial-deploy-a-linked-template"></a>Tutoriel : Déployer un modèle lié

Dans les [tutoriels précédents](./deployment-tutorial-local-template.md), vous avez appris à déployer un modèle qui est stocké sur votre ordinateur local. Pour déployer des solutions complexes, vous pouvez diviser un modèle en plusieurs modèles, puis les déployer au moyen d’un modèle principal. Dans ce tutoriel, vous allez apprendre à déployer un modèle principal qui contient la référence à un modèle lié. Lorsque le modèle principal est déployé, il déclenche le déploiement du modèle lié. Vous allez également apprendre à stocker et à sécuriser les modèles à l’aide d’un jeton SAS. Comptez environ **12 minutes** pour suivre ce tutoriel.

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le tutoriel précédent, mais ce n’est pas obligatoire.

## <a name="review-template"></a>Vérifier le modèle

Dans les tutoriels précédents, vous avez déployé un modèle qui crée un compte de stockage, un plan App Service et une application web. Le modèle utilisé était :

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Créer un modèle lié

Vous pouvez séparer la ressource de compte de stockage en un modèle lié :

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Le modèle suivant est le modèle principal. L’objet `Microsoft.Resources/deployments` mis en surbrillance montre comment appeler un modèle lié. Le modèle lié ne peut pas être stocké en tant que fichier local ni fichier qui est uniquement disponible sur votre réseau local. Vous pouvez fournir une valeur d’URI du modèle lié qui comprend HTTP ou HTTPS, ou utiliser la propriété _relativePath_ pour déployer un modèle lié distant à un emplacement relatif au modèle parent. L’une des options consiste à placer le modèle principal et le modèle lié dans un compte de stockage.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>Stocker le modèle lié

Le modèle principal et le modèle lié sont stockés dans GitHub :

Le script PowerShell suivant crée un compte de stockage, crée un conteneur et copie les deux modèles à partir d’un dépôt GitHub vers le conteneur. Ces deux modèles sont les suivants :

- Modèle principal : https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- Modèle lié : https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Sélectionnez **Essayer** pour ouvrir Cloud Shell et **Copier** pour copier le script PowerShell, puis cliquez avec le bouton droit sur le volet de l’interpréteur de commandes pour coller le script :

> [!IMPORTANT]
> Ce nom doit comprendre entre 3 et 24 caractères, uniquement des lettres en minuscules et des nombres. Le nom doit être unique. Dans le modèle, le nom du compte de stockage est le nom du projet auquel **store** est ajouté, et le nom du projet doit comprendre entre 3 et 11 caractères. Le nom du projet doit donc respecter les exigences du nom du compte de stockage et comporter moins de 11 caractères.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Déployer un modèle

Pour déployer des modèles dans un compte de stockage, générez un jeton SAS et fournissez-le au paramètre _-QueryString_. Définissez le délai d’expiration de façon à laisser suffisamment de temps pour terminer le déploiement. Les objets blob contenant les modèles ne sont accessibles qu’au propriétaire du compte. Toutefois, lorsque vous créez un jeton SAS pour un objet blob, ce dernier est accessible à toute personne doté de ce jeton SAS. Si un autre utilisateur intercepte l’URI et le jeton SAS, il pourra accéder au modèle. Un jeton SAP est un bon moyen de limiter l’accès à vos modèles, mais vous ne devez pas inclure de données sensibles comme des mots de passe directement dans le modèle.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> Dans le code Azure CLI ci-dessous, le paramètre `date` `-d` n’est pas un argument valide sur macOS. Si vous utilisez macOS, indiquez `-v+2H` pour ajouter 2 heures à l’heure actuelle dans le terminal sur macOS.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
  --verbose
```

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Nettoyez les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à déployer un modèle lié. Dans le tutoriel suivant, vous allez apprendre à créer un pipeline DevOps pour déployer un modèle.

> [!div class="nextstepaction"]
> [Créer un pipeline](./deployment-tutorial-pipeline.md)