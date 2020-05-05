---
title: Déployer des ressources avec Azure CLI et un modèle
description: Utilisez Azure Resource Manager et Azure CLI pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8ee15699a085178add05137be895fe6b660b715b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81685702"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Déployer des ressources à l’aide de modèles ARM et l’interface CLI Azure

Cet article explique comment utiliser Azure CLI avec les modèles Azure Resource Manager (ARM) pour déployer vos ressources dans Azure. Si vous n’avez pas une bonne connaissance des concepts de déploiement et de gestion des solutions Azure, consultez [Vue d’ensemble du déploiement de modèles](overview.md).

Les commandes de déploiement ont changé dans la version 2.2.0 d’Azure CLI. Les exemples de cet article nécessitent la version 2.2.0 ou ultérieure d’Azure CLI.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Si Azure CLI n’est pas installé, vous pouvez utiliser le [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Étendue du déploiement

Vous pouvez cibler votre déploiement au niveau d’un groupe de ressources, d’un abonnement, d’un groupe d’administration ou d’un locataire. Dans la plupart des cas, un déploiement cible un groupe de ressources. Pour appliquer des stratégies et des attributions de rôles dans une plus grande étendue, effectuez des déploiements au niveau du groupe d’administration ou de l’abonnement. Lorsque vous effectuez un déploiement au niveau de l’abonnement, vous pouvez créer un groupe de ressources et y déployer des ressources.

Les commandes à utiliser diffèrent en fonction de l’étendue du déploiement.

Pour un déploiement dans un **groupe de ressources**, utilisez [az deployment group create](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create) :

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

Pour un déploiement dans un **abonnement**, utilisez [az deployment sub create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create) :

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

Pour plus d’informations sur les déploiements au niveau de l’abonnement, consultez [Créer des groupes de ressources et des ressources au niveau de l’abonnement](deploy-to-subscription.md).

Pour un déploiement dans un **groupe de d’administration**, utilisez [az deployment mg create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create) :

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

Pour plus d’informations sur les déploiements au niveau du groupe d’administration, consultez [Créer des ressources au niveau du groupe d’administration](deploy-to-management-group.md).

Pour un déploiement dans un **locataire**, utilisez [az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create) :

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

Pour plus d’informations sur les déploiements au niveau du locataire, consultez [Créer des ressources au niveau du locataire](deploy-to-tenant.md).

Les exemples de cet article illustrent des déploiements dans des groupes de ressources.

## <a name="deploy-local-template"></a>Déployer un modèle local

Au moment de déployer des ressources dans Azure, vous effectuez les opérations suivantes :

1. Connexion à votre compte Azure
2. Créez un groupe de ressources qui sert de conteneur pour les ressources déployées. Le nom du groupe de ressources ne peut contenir que des caractères alphanumériques, des points, des traits de soulignement, des traits d'union et des parenthèses. Il peut comprendre jusqu’à 90 caractères. Il ne peut pas se terminer par un point.
3. Déploiement dans le groupe de ressources du modèle qui définit les ressources à créer

Un modèle peut inclure des paramètres qui permettent de personnaliser le déploiement. Par exemple, vous pouvez indiquer des valeurs qui sont adaptées à un environnement particulier (par exemple, de développement, de test ou de production). L’exemple de modèle définit un paramètre pour la référence (SKU) de compte de stockage.

L’exemple suivant crée un groupe de ressources et déploie un modèle à partir de votre ordinateur local :

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Le déploiement peut prendre plusieurs minutes. Au terme, vous voyez un message qui inclut le résultat :

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Déployer un modèle distant

Au lieu de stocker les modèles Resource Manager sur votre ordinateur local, vous pouvez les stocker dans un emplacement externe. Vous pouvez stocker des modèles dans un dépôt de contrôle de code source (par exemple, GitHub). Vous pouvez aussi les stocker dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation.

Pour déployer un modèle externe, utilisez le paramètre **template-uri**. Pour déployer l’exemple de modèle à partir de GitHub, utilisez l’URI figurant dans l’exemple.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

L’exemple précédent nécessite un URI accessible publiquement pour le modèle, ce qui convient pour la plupart des scénarios, sachant que votre modèle ne doit pas inclure de données sensibles. Si vous avez besoin de spécifier des données sensibles (par exemple, un mot de passe d’administrateur), passez cette valeur en tant que paramètre sécurisé. Toutefois, si vous ne souhaitez pas que votre modèle soit accessible au public, vous pouvez le protéger en le stockant dans un conteneur de stockage privé. Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton de signature d’accès partagé (SAS), consultez [Déployer un modèle privé avec un jeton SAS](secure-template-with-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

Dans Azure Cloud Shell, utilisez les commandes suivantes :

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Paramètres

Pour passer les valeurs de paramètre, vous pouvez utiliser des paramètres inline ou un fichier de paramètres.

### <a name="inline-parameters"></a>Paramètres inline

Pour passer des paramètres inline, indiquez les valeurs dans `parameters`. Par exemple, pour passer une chaîne et un tableau à un modèle dans un interpréteur de commandes Bash, utilisez :

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Si vous utilisez l’interface de ligne de commande Azure avec l’invite de commandes Windows (CMD) ou PowerShell, transmettez le tableau au format : `exampleArray="['value1','value2']"`.

Vous pouvez également récupérer le contenu d’un fichier et fournir ce contenu en tant que paramètre inline.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Obtenir une valeur de paramètre à partir d’un fichier est utile lorsque vous devez fournir des valeurs de configuration. Par exemple, vous pouvez fournir des [valeurs cloud-init pour une machine virtuelle Linux](../../virtual-machines/linux/using-cloud-init.md).

Le format arrayContent.json est :

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Fichiers de paramètres

Au lieu de passer des paramètres en tant que valeurs inline dans votre script, il peut s’avérer plus facile d’utiliser un fichier JSON qui contient les valeurs des paramètres. Le fichier de paramètres doit être un fichier local. Les fichiers de paramètres externes ne sont pas pris en charge avec Azure CLI.

Pour plus d’informations sur le fichier de paramètres, consultez [Créer un fichier de paramètres Resource Manager](parameter-files.md).

Pour transmettre un fichier de paramètres local, utilisez `@` pour spécifier un fichier local nommé storage.parameters.json.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Gérer le format JSON étendu

Pour déployer un modèle avec des commentaires ou à plusieurs chaînes de ligne à l’aide d’Azure CLI version 2.3.0 ou antérieure, vous devez utiliser le commutateur `--handle-extended-json-format`.  Par exemple :

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>Tester le déploiement d’un modèle

Pour tester votre modèle et vos valeurs de paramètres sans réellement déployer toutes les ressources, utilisez [az deployment group validate](/cli/azure/group/deployment).

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Si aucune erreur n’est détectée, la commande retourne des informations sur le déploiement de test. En particulier, notez que **error** a la valeur Null.

```output
{
  "error": null,
  "properties": {
      ...
```

Si une erreur est détectée, la commande retourne un message d’erreur. Par exemple, la transmission d’une valeur incorrecte pour la référence (SKU) du compte de stockage retourne l’erreur suivante :

```output
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Si votre modèle comporte une erreur de syntaxe, la commande retourne une erreur indiquant que le modèle n’a pas pu être analysé. Le message indique le numéro de ligne et la position de l’erreur d’analyse.

```output
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour restaurer un déploiement réussi lorsque vous obtenez une erreur, consultez [Restaurer en cas d’erreur vers un déploiement réussi](rollback-on-error.md).
- Pour spécifier comment gérer les ressources présentes dans le groupe de ressources, mais non définies dans le modèle, consultez [Modes de déploiement Azure Resource Manager](deployment-modes.md).
- Pour comprendre comment définir des paramètres dans votre modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](template-syntax.md).
- Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](common-deployment-errors.md).
- Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](secure-template-with-sas-token.md).
- Pour déployer en toute sécurité votre service sur plusieurs régions, consultez [Azure Deployment Manager](deployment-manager-overview.md).
