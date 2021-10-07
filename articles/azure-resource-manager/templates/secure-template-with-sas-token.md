---
title: Déployer un modèle ARM avec un jeton SAS - Gestionnaire de ressource Azure | Microsoft Docs
description: Découvrez comment utiliser l’interface CLI Azure ou Azure PowerShell pour déployer en toute sécurité un modèle ARM privé avec un jeton SAS. Protégez et gérez l’accès à vos modèles.
ms.topic: conceptual
ms.date: 09/17/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli, seo-azure-cli
keywords: modèle privé, modèle de jeton SAS, compte de stockage, sécurité de modèle, modèle ARM Azure, modèle du Gestionnaire de ressource Azure
ms.openlocfilehash: 0e6a680e0344ed5a03715c35e99394aead756501
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584939"
---
# <a name="how-to-deploy-private-arm-template-with-sas-token"></a>Déployer un modèle privé ARM avec un jeton SAS

Lorsque votre modèle Azure Resource Manager (modèle ARM) se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle pour éviter de l’exposer publiquement. Vous accédez à un modèle sécurisé en créant un jeton de signature d’accès partagé (SAS) pour le modèle et en fournissant ce jeton pendant le déploiement. Cet article explique comment utiliser Azure PowerShell ou l’interface CLI Azure pour déployer un modèle avec un jeton SAS.

Vous y trouverez des informations sur la façon de protéger et de gérer l’accès à vos modèles ARM privés avec des instructions sur la façon d’effectuer les opérations suivantes :

* Créer un compte de stockage avec un conteneur sécurisé
* Charger le modèle dans le compte de stockage
* Fournir un jeton SAP au cours du déploiement

> [!IMPORTANT]
> Plutôt que de sécuriser votre modèle avec un jeton SAS, envisagez d’utiliser les [spécifications de modèle](template-specs.md). Les spécifications de modèle vous permettent de partager vos modèles avec d’autres utilisateurs de votre organisation et de gérer l’accès aux modèles par le biais d’Azure RBAC.

## <a name="create-storage-account-with-secured-container"></a>Créer un compte de stockage avec un conteneur sécurisé

Le script suivant crée un compte et un conteneur de stockage avec l’accès public désactivé pour la sécurité du modèle.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-private-template-to-storage-account"></a>Charger un modèle privé dans le compte de stockage

Maintenant, vous pouvez charger votre modèle dans le compte de stockage. Indiquez le chemin d’accès au modèle que vous souhaitez utiliser.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Fournir un jeton SAP au cours du déploiement

Pour déployer un modèle dans un compte de stockage privé, générez un jeton SAP et incluez-le dans l’URI du modèle. Définissez le délai d’expiration de façon à laisser suffisamment de temps pour terminer le déploiement.

> [!IMPORTANT]
> L’objet blob contenant le modèle privé n’est accessible qu’au propriétaire du compte. Toutefois, lorsque vous créez un jeton SAP pour l’objet blob, celui-ci est accessible à toute personne ayant cet URI. Si un autre utilisateur intercepte l’URI, il pourra accéder au modèle. Un jeton SAP est un bon moyen de limiter l’accès à vos modèles, mais vous ne devez pas inclure de données sensibles comme des mots de passe directement dans le modèle.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

L’exemple suivant fonctionne avec l’environnement Bash dans Cloud Shell. D’autres environnements peuvent nécessiter une syntaxe différente pour créer l’heure d’expiration du jeton SAS.

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Pour accéder à un exemple d’utilisation d’un jeton SAP avec des modèles liés, consultez [Utilisation de modèles liés avec Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Étapes suivantes
* Pour une introduction au déploiement de modèles, voir [Déployer des ressources à l’aide de modèles ARM et d’Azure PowerShell](deploy-powershell.md).
* Pour définir des paramètres dans le modèle, consultez [Création de modèles](./syntax.md#parameters).