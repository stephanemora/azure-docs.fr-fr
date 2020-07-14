---
title: Créer un espace de travail avec un modèle Azure Resource Manager
titleSuffix: Azure Machine Learning
description: Découvrez la manière d’utiliser un modèle Azure Resource Manager pour créer un espace de travail Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: larryfr
author: Blackmist
ms.date: 05/19/2020
ms.custom: seoapril2019
ms.openlocfilehash: 39c694f4e2afbf5d781a8fde43a7db9c4a255466
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392662"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Utiliser un modèle Azure Resource Manager pour créer un espace de travail pour Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

Vous trouverez dans cet article différentes façons de créer un espace de travail Azure Machine Learning à l’aide de modèles Azure Resource Manager. Un modèle Resource Manager permet de créer des ressources sous la forme d’une seule opération coordonnée. Un modèle est un document JSON qui définit les ressources nécessaires à un déploiement. Il peut également spécifier des paramètres de déploiement. Les paramètres permettent de fournir des valeurs d’entrée lorsque vous utilisez le modèle.

Pour plus d’informations, consultez la page [Déploiement d’une application avec un modèle Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Prérequis

* Un **abonnement Azure**. Si vous n’en avez pas, essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

* Pour utiliser un modèle d’une interface CLI, vous devez avoir [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modèle Resource Manager

Le modèle Resource Manager suivant permet de créer un espace de travail Azure Machine Learning et des ressources Azure associées :

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Ce modèle crée les services Azure suivants :

* Groupe de ressources Azure
* Compte Stockage Azure
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Espace de travail Azure Machine Learning

Le groupe de ressources est le conteneur des services. Les différents services sont requis par l’espace de travail Azure Machine Learning.

L’exemple de modèle possède deux paramètres :

* L’**emplacement**, où le groupe de ressources et les services vont être créés.

    Le modèle utilise l’emplacement que vous sélectionnez pour la plupart des ressources. L’exception est le service Application Insights, qui n’est pas disponible dans tous les emplacements où les autres services le sont. Si vous sélectionnez un emplacement où il n’est pas disponible, le service est créé à l’emplacement USA Centre Sud.

* Le **nom de l’espace de travail**, qui est le nom convivial de l’espace de travail Azure Machine Learning.

    > [!NOTE]
    > Le nom de l’espace de travail n’est pas sensible à la casse.

    Le nom des autres services est généré de façon aléatoire.

> [!TIP]
> Le modèle associé à ce document crée un registre de conteneurs Azure, mais vous pouvez également créer un espace de travail sans créer de registre de conteneurs. Un registre de conteneurs est créé quand vous effectuez une opération qui le nécessite. C’est par exemple ce qui se passe lors de l’entraînement ou du déploiement d’un modèle.
>
> Vous pouvez également référencer un registre de conteneurs ou un compte de stockage existant dans le modèle Azure Resource Manager au lieu d’en créer un. Toutefois, le registre de conteneurs que vous utilisez doit avoir le __compte d’administrateur__ activé. Pour plus d’informations sur l’activation du compte d’administrateur, consultez [Compte d’administrateur](/azure/container-registry/container-registry-authentication#admin-account).

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Pour plus d’informations sur les modèles, voir les articles suivants :

* [Création de modèles Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Déployer une application avec des modèles Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Types de ressources Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Modèle avancé

L’exemple de modèle suivant montre comment créer un espace de travail avec trois paramètres :

* Activer les paramètres de confidentialité élevée pour l’espace de travail
* Activer le chiffrement pour l’espace de travail
* Utiliser une instance Azure Key Vault existante pour récupérer les clés gérées par le client

Pour plus d'informations, consultez [Chiffrement au repos](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]
> Pour pouvoir utiliser ce modèle, l’abonnement doit satisfaire à certaines exigences :
> * L’application __Azure Machine Learning__ doit être un __contributeur__ pour votre abonnement Azure.
> * Vous devez disposer d’un Azure Key Vault contenant une clé de chiffrement.
> * Vous devez disposer dans l’Azure Key Vault d’une stratégie d’accès accordant les accès __get__, __wrap__ et __unwrap__ à l’application __Azure Cosmos DB__.
> * L’Azure Key Vault doit se trouver dans la même région que celle où vous envisagez de créer l’espace de travail Azure Machine Learning.

__Pour ajouter l’application Azure Machine Learning en tant que de contributeur__, utilisez les commandes suivantes :

1. Pour vous authentifier auprès d’Azure à partir de l’interface CLI, utilisez la commande suivante :

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. Pour obtenir l’ID d’objet de l’application Azure Machine Learning, utilisez la commande suivante. La valeur peut être différente pour chacun de vos abonnements Azure :

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    Cette commande retourne l’ID d’objet qui est un GUID.

1. Pour ajouter l’ID d’objet en tant que contributeur à votre abonnement, utilisez la commande suivante. Remplacez `<object-ID>` par le GUID de l’étape précédente. Remplacez `<subscription-ID>` par le nom ou l’ID de votre abonnement Azure :

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

__Pour ajouter une clé à votre Azure Key Vault__, suivez les instructions de la section [Ajouter une clé, un secret ou un certificat au coffre de clés](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) de l’article __Gérer Key Vault avec Azure CLI__.

__Pour ajouter une stratégie d’accès au coffre de clés, utilisez les commandes suivantes__ :

1. Pour récupérer l’ID d’objet de l’application Azure Cosmos DB, utilisez la commande suivante. La valeur peut être différente pour chacun de vos abonnements Azure :

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    Cette commande retourne l’ID d’objet qui est un GUID.

1. Pour définit la stratégie, utilisez la commande suivante. Remplacez `<keyvault-name>` par le nom de l’Azure Key Vault existant. Remplacez `<object-ID>` par le GUID de l’étape précédente :

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

__Pour obtenir les valeurs__ pour `cmk_keyvault` (ID du Key Vault) et les paramètres `resource_cmk_uri` (URI de clé) dont ce modèle a besoin, procédez comme suit :

1. Pour connaître l’ID du Key Vault, utilisez la commande suivante :

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    Cette commande renvoie une valeur semblable `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault`.

1. Pour obtenir la valeur de l’URI de la clé gérée par le client, utilisez la commande suivante :

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    Cette commande renvoie une valeur semblable `https://mykeyvault.vault.azure.net/keys/mykey/{guid}`.

__Exemple de modèle__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> Une fois l'espace de travail créé, vous ne pouvez pas modifier les paramètres des données confidentielles, du chiffrement, de l’ID du coffre de clés ou des identificateurs de clés. Pour modifier ces valeurs, vous devez créer un espace de travail à l’aide des nouvelles valeurs.

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

1. Suivez la procédure indiquée dans [Déployer des ressources à partir d’un modèle personnalisé](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Lorsque vous arrivez à l’écran __Modifier le modèle__, collez dans le modèle à partir de ce document.
1. Sélectionnez __Enregistrer__ pour utiliser le modèle. Fournissez les informations suivantes et acceptez les conditions répertoriées :

   * Abonnement : Sélectionnez l’abonnement Azure à utiliser pour ces ressources.
   * Groupe de ressources : Sélectionnez ou créez un groupe de ressources pour contenir les services.
   * Nom de l’espace de travail : Nom à utiliser pour l’espace de travail Azure Machine Learning qui va être créé. Le nom de l'espace de travail doit contenir entre 3 et 33 caractères. Il ne peut contenir que des caractères alphanumériques et « - ».
   * Localisation : Sélectionnez l’emplacement de création des ressources.

Pour plus d’informations, consultez [Déployer des ressources à partir d’un modèle personnalisé](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

Cet exemple suppose que vous avez enregistré le modèle dans un fichier nommé `azuredeploy.json` dans le répertoire actuel :

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) et [Déployer un modèle Resource Manager privé avec un jeton SAP et Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

Cet exemple suppose que vous avez enregistré le modèle dans un fichier nommé `azuredeploy.json` dans le répertoire actuel :

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../azure-resource-manager/templates/deploy-cli.md) et [Déployer un modèle Resource Manager privé avec un jeton SAP et Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Dépannage

### <a name="resource-provider-errors"></a>Erreurs du fournisseur de ressources

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Stratégie d’accès Azure Key Vault et modèles Azure Resource Manager

Lorsque vous utilisez plusieurs fois un modèle Azure Resource Manager pour créer l’espace de travail et les ressources associées (y compris Azure Key Vault). Vous utilisez, par exemple, le modèle plusieurs fois avec les mêmes paramètres dans le cadre d’un pipeline d’intégration et de déploiement continus.

La plupart des opérations de création de ressources via des modèles sont idempotentes, mais Key Vault efface les stratégies d’accès chaque fois que le modèle est utilisé. L’effacement des stratégies accès empêche l’accès à Key Vault de n’importe quel espace de travail existant qui l’utilise. Par exemple, les fonctions Arrêter/Créer de machine virtuelle Azure Notebooks peuvent échouer.  

Pour éviter ce problème, nous vous recommandons une des approches suivantes :

* Ne déployez pas le modèle plus d’une fois pour les mêmes paramètres. Ou supprimez les ressources existantes avant d’utiliser le modèle pour les recréer.

* Examinez les stratégies d’accès Key Vault, puis utilisez ces stratégies pour définir la propriété `accessPolicies` du modèle. Pour voir les stratégies d’accès, utilisez la commande Azure CLI suivante :

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Pour plus d’informations sur l’utilisation de la section `accessPolicies` du modèle, consultez les [informations de référence sur l’objet AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Vérifiez si la ressource Key Vault existe déjà. Si c’est le cas, ne la recréez pas via le modèle. Par exemple, pour utiliser la ressource Key Vault existante au lieu d’en créer une nouvelle, apportez les modifications suivantes au modèle :

    * **Ajoutez** un paramètre qui accepte l’ID d’une ressource Key Vault existante :

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Supprimez** la section qui crée une ressource Key Vault :

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Supprimez** la ligne `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` dans la section `dependsOn` de l’espace de travail. **Modifiez** également l’entrée `keyVault` dans la section `properties` de l’espace de travail pour référencer le paramètre `keyVaultId` :

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Une fois ces changements effectués, vous pouvez spécifier l’ID de la ressource Key Vault existante quand vous exécutez le modèle. Le modèle réutilisera la ressource Key Vault en affectant son ID à la propriété `keyVault` de l’espace de travail.

    Pour obtenir l’ID de la ressource Key Vault, référencez la sortie de l’exécution du modèle initial ou utilisez Azure CLI. La commande suivante est un exemple d’utilisation d’Azure CLI pour obtenir l’ID de la ressource Key Vault :

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Cette commande retourne une valeur semblable au texte suivant :

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager](../azure-resource-manager/templates/deploy-rest.md).
* [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
