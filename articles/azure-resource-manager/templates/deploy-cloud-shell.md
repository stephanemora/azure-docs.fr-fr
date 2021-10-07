---
title: Déployer des modèles avec le service Cloud Shell
description: Utilisez Azure Resource Manager et Azure Cloud Shell pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Azure Resource Manager (modèle ARM).
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: 0228f4862cbdcda98bc577844bca9dad34feb96f
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535090"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>Déployer des modèles ARM à partir d’Azure Cloud Shell

Vous pouvez utiliser [Azure Cloud Shell](../../cloud-shell/overview.md) pour déployer un modèle Azure Resource Manager (modèle ARM). Vous pouvez déployer un modèle ARM stocké à distance ou sur le compte de stockage local pour le service Cloud Shell.

Vous pouvez effectuer le déploiement sur n’importe quelle étendue. Cet article présente un déploiement sur un groupe de ressources.

## <a name="deploy-remote-template"></a>Déployer un modèle distant

Pour déployer un modèle externe, indiquez précisément l’URI du modèle comme vous le feriez pour tout autre déploiement externe. Le modèle externe peut se trouver dans un dépôt GitHub ou un compte de stockage externe.

1. Ouvrez l’invite du service Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Ouvrir Cloud Shell":::

1. Pour déployer le modèle, exécutez les commandes suivantes :

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Déployer un modèle local

Pour déployer un modèle local, vous devez commencer par charger votre modèle dans le compte de stockage connecté à votre session Cloud Shell.

1. Connectez-vous à [Cloud Shell](https://shell.azure.com).

1. Sélectionnez **PowerShell** ou **Bash**.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-bash-powershell.png" alt-text="Sélectionnez Bash ou PowerShell":::

1. Sélectionnez **Charger/Télécharger des fichiers**, puis **Charger**.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-upload.png" alt-text="Charger un fichier":::

1. Sélectionnez le modèle ARM que vous souhaitez télécharger, puis sélectionnez **Ouvrir**.

1. Pour déployer le modèle, exécutez les commandes suivantes :

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les commandes de déploiement, consultez [Déployer des ressources à l’aide de modèles ARM et l’interface CLI Azure](deploy-cli.md) et [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](deploy-powershell.md).
- Pour afficher un aperçu des modifications avant de déployer un modèle, consultez [Opération de simulation de déploiement de modèle ARM](./deploy-what-if.md).