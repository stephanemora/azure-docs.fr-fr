---
title: Déployer des ressources avec Azure CLI et des fichiers Bicep
description: Utilisez Azure Resource Manager et Azure CLI pour déployer des ressources sur Azure. Les ressources sont définies dans le fichier Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: aa23bfd9d867b9e0d5d2724a2b1f41b9fbc8e5da
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111954765"
---
# <a name="deploy-resources-with-bicep-and-azure-cli"></a>Déployer des ressources avec Bicep et Azure CLI

Cet article explique comment utiliser Azure CLI avec les fichiers Bicep pour déployer vos ressources dans Azure. Si vous n’avez pas une bonne connaissance des concepts de déploiement et de gestion des solutions Azure, consultez [Vue d’ensemble de Bicep](./overview.md).

Pour déployer des fichiers Bicep, vous devez disposer d’[Azure CLI version 2.20.0 ou ultérieure](/cli/azure/install-azure-cli).

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Si Azure CLI n’est pas installé, vous pouvez utiliser Azure Cloud Shell. Pour plus d’informations, consultez [Déployer des fichiers Bicep à partir d’Azure Cloud Shell](./deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Étendue du déploiement

Vous pouvez cibler votre déploiement au niveau d’un groupe de ressources, d’un abonnement, d’un groupe d’administration ou d’un locataire. Les commandes à utiliser diffèrent en fonction de l’étendue du déploiement.

* Pour un déploiement dans un **groupe de ressources**, utilisez [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) :

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-bicep>
  ```

* Pour un déploiement dans un **abonnement**, utilisez [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create) :

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-bicep>
  ```

  Pour plus d’informations sur les déploiements au niveau de l’abonnement, consultez [Créer des groupes de ressources et des ressources au niveau de l’abonnement](deploy-to-subscription.md).

* Pour un déploiement dans un **groupe de d’administration**, utilisez [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create) :

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-bicep>
  ```

  Pour plus d’informations sur les déploiements au niveau du groupe d’administration, consultez [Créer des ressources au niveau du groupe d’administration](deploy-to-management-group.md).

* Pour un déploiement dans un **locataire**, utilisez [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create) :

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-bicep>
  ```

  Pour plus d’informations sur les déploiements au niveau du locataire, consultez [Créer des ressources au niveau du locataire](deploy-to-tenant.md).

Pour chaque étendue, l’utilisateur qui déploie le fichier Bicep doit disposer des autorisations nécessaires afin de créer des ressources.

## <a name="deploy-local-bicep-file"></a>Déployer un fichier Bicep local

Vous pouvez déployer un fichier Bicep à partir de votre ordinateur local ou d’un ordinateur stocké en externe. Cette section décrit le déploiement d’un fichier Bicep local.

Si vous effectuez un déploiement vers un groupe de ressources qui n’existe pas, vous devez commencer par créer ce dernier. Le nom du groupe de ressources ne peut contenir que des caractères alphanumériques, des points, des traits de soulignement, des traits d'union et des parenthèses. Il peut comprendre jusqu’à 90 caractères. Le nom ne peut pas se terminer par un point.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Pour déployer un fichier Bicep local, utilisez le paramètre `--template-file` dans la commande de déploiement. L’exemple suivant montre également comment définir une valeur de paramètre.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-bicep> \
  --parameters storageAccountType=Standard_GRS
```

Le déploiement peut prendre plusieurs minutes. Au terme, vous voyez un message qui inclut le résultat :

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-bicep-file"></a>Déployer un fichier Bicep distant

Actuellement, Azure CLI ne prend pas en charge le déploiement de fichiers bicep distants. Utilisez [bicep CLI](./install.md#development-environment) pour compiler le fichier Bicep dans un modèle JSON, puis chargez le fichier JSON dans l’emplacement distant.

## <a name="parameters"></a>Paramètres

Pour passer les valeurs de paramètre, vous pouvez utiliser des paramètres inline ou un fichier de paramètres.

### <a name="inline-parameters"></a>Paramètres inline

Pour passer des paramètres inline, indiquez les valeurs dans `parameters`. Par exemple, pour transmettre une chaîne et un tableau à un fichier Bicep dans un interpréteur de commandes Bash, utilisez :

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Si vous utilisez l’interface de ligne de commande Azure avec l’invite de commandes Windows (CMD) ou PowerShell, transmettez le tableau au format : `exampleArray="['value1','value2']"`.

Vous pouvez également récupérer le contenu d’un fichier et fournir ce contenu en tant que paramètre inline.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Obtenir une valeur de paramètre à partir d’un fichier est utile lorsque vous devez fournir des valeurs de configuration. Par exemple, vous pouvez fournir des [valeurs cloud-init pour une machine virtuelle Linux](../../virtual-machines/linux/using-cloud-init.md).

Le format _arrayContent.json_ est :

```json
[
    "value1",
    "value2"
]
```

Pour passer un objet en entrée, par exemple pour définir des balises, utilisez JSON. Par exemple, votre fichier Bicep peut inclure un paramètre comme celui-ci :

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

Dans ce cas, vous pouvez passer une chaîne JSON pour définir le paramètre comme indiqué dans le script bash suivant :

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $bicepFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Utilisez des guillemets doubles autour du JSON que vous voulez passer à l’objet.

### <a name="parameter-files"></a>Fichiers de paramètres

Au lieu de passer des paramètres en tant que valeurs inline dans votre script, il peut s’avérer plus facile d’utiliser un fichier JSON qui contient les valeurs des paramètres. Le fichier de paramètres doit être un fichier local. Les fichiers de paramètres externes ne sont pas pris en charge avec Azure CLI. Le fichier Bicep utilise des fichiers de paramètres JSON.

Pour plus d’informations sur le fichier de paramètres, consultez [Créer un fichier de paramètres Resource Manager](./parameter-files.md).

Pour transmettre un fichier de paramètres local, utilisez `@` pour spécifier un fichier local nommé _storage.parameters.json_.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.bicep \
  --parameters @storage.parameters.json
```

## <a name="preview-changes"></a>Prévisualiser les modifications

Avant de déployer votre fichier Bicep, vous pouvez obtenir un aperçu des changements que le fichier Bicep apportera à votre environnement. Utilisez l’[opération de simulation](./deploy-what-if.md) pour vérifier que le fichier Bicep apporte les changements prévus. Cette opération vérifie aussi que le fichier Bicep est exempt d’erreurs.

## <a name="deploy-template-specs"></a>Déployer des specs de modèle

Actuellement, Azure CLI ne prend pas en charge la création de spécifications de modèle en fournissant des fichiers Bicep. Toutefois, vous pouvez créer un fichier Bicep avec la ressource [Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) pour déployer une spécification de modèle. Voici un [exemple](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep). Vous pouvez également générer votre fichier Bicep dans le JSON d’un modèle ARM à l’aide de l’interface CLI Bicep, puis créer un spec de modèle avec le modèle JSON.

## <a name="deployment-name"></a>Nom du déploiement

Lorsque vous déployez un fichier Bicep, vous pouvez attribuer un nom au déploiement. Ce nom peut vous aider à récupérer le déploiement à partir de l’historique de déploiement. Si vous n’attribuez pas de nom au déploiement, le nom du fichier Bicep sera utilisé. Par exemple, si vous déployez un fichier Bicep nommé `azuredeploy.bicep` sans spécifier de nom pour le déploiement, le déploiement sera nommé `azuredeploy`.

Chaque fois que vous exécutez un déploiement, une entrée est ajoutée à l’historique de déploiement du groupe de ressources avec le nom du déploiement. Si vous exécutez un autre déploiement et que vous lui attribuez le même nom, l’entrée précédente est remplacée par le déploiement actuel. Si vous souhaitez conserver des entrées uniques dans l’historique de déploiement, attribuez un nom unique à chaque déploiement.

Pour créer un nom unique, vous pouvez assigner un numéro aléatoire.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Vous pouvez aussi ajouter une valeur de date.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Si vous exécutez des déploiements simultanés dans le même groupe de ressources avec le même nom de déploiement, seul le dernier déploiement aboutit. Les déploiements de même nom qui n’arrivent pas à terme sont remplacés par le dernier déploiement. Par exemple, si vous exécutez un déploiement nommé `newStorage` qui déploie un compte de stockage nommé `storage1` et que, dans le même temps, vous exécutez un autre déploiement nommé `newStorage` qui déploie un compte de stockage nommé `storage2`, vous ne déployez qu’un seul compte de stockage. Le compte de stockage qui en résulte est nommé `storage2`.

En revanche, si vous exécutez un déploiement nommé `newStorage` qui déploie un compte de stockage nommé `storage1` et que, aussitôt terminé, vous exécutez un autre déploiement nommé `newStorage` qui déploie un compte de stockage nommé `storage2`, vous disposez de deux comptes de stockage : un nommé `storage1` et l’autre nommé `storage2`. Cependant, l’historique de déploiement ne présente qu’une seule entrée.

Quand vous spécifiez un nom unique pour chaque déploiement, vous pouvez les exécuter simultanément sans conflit. Si vous exécutez un déploiement nommé `newStorage1` qui déploie un compte de stockage nommé `storage1` et que, dans le même temps, vous exécutez un autre déploiement nommé `newStorage2` qui déploie un compte de stockage nommé `storage2`, vous disposez de deux comptes de stockage et l’historique de déploiement présente deux entrées.

Pour éviter les conflits lors de déploiements simultanés et faire en sorte que l’historique de déploiement présente des entrées uniques, attribuez un nom unique à chaque déploiement.

## <a name="next-steps"></a>Étapes suivantes

* Pour restaurer un déploiement réussi lorsque vous obtenez une erreur, consultez [Restaurer en cas d’erreur vers un déploiement réussi](../templates/rollback-on-error.md).
* Pour comprendre comment définir des paramètres dans votre modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](../templates/syntax.md).
* Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](../templates/common-deployment-errors.md).