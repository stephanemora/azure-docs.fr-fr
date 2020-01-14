---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: e479f2376668a2fc3824e733996c94cfab04c9ec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467847"
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

Pour plus d’informations sur la manière de récupérer les clés d’accès au compte à l’aide du portail Azure, consultez [Gérer les clés d’accès au compte de stockage](../articles/storage/common/storage-account-keys-manage.md).