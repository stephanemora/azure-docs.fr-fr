---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 4ad977dc8cbaa85360092dbfd391a3c3b88f67bb
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747922"
---
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec la commande [az group create](/cli/azure/group). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Créez un compte de stockage à usage général avec la commande [az storage account create](/cli/azure/storage/account). Le compte de stockage à usage général peut être utilisé pour les quatre services : objets blob, fichiers, tables et files d’attente.

N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Spécifier les informations d’identification de compte de stockage

L’interface de ligne de commande Azure a besoin de vos informations d’identification de compte de stockage pour la plupart des commandes de ce didacticiel. Bien que plusieurs options s’offrent à vous, l’une des méthodes les plus simples pour fournir ces informations d’identification consiste à définir les variables d’environnement `AZURE_STORAGE_ACCOUNT` et `AZURE_STORAGE_KEY`.

> [!NOTE]
> Cet article explique comment définir des variables d’environnement à l’aide de Bash. D’autres environnements peuvent nécessiter des modifications de syntaxe.

Tout d’abord, affichez les clés de votre compte de stockage à l’aide de la commande [az storage account keys list](/cli/azure/storage/account/keys). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

Ensuite, définissez les variables d’environnement `AZURE_STORAGE_ACCOUNT` et `AZURE_STORAGE_KEY`. Pour cela, vous pouvez utiliser l’interpréteur de commandes Bash à l’aide de la commande `export`. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Pour plus d’informations sur la manière de récupérer des clés d’accès au compte à l’aide du portail Azure, voir **Clés d’accès** dans [Gérer les paramètres de compte de stockage dans le portail Azure](../articles/storage/common/storage-account-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#access-keys).