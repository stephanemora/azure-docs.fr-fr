---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4dd43c5bcc5a0e9a734db4ca9a4b3d7137f85250
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203181"
---
## <a name="create-an-azure-storage-account"></a>Création d'un compte Azure Storage

Functions utilise un compte à usage général dans stockage Azure pour conserver l’état et d’autres informations sur vos fonctions. Créez un compte de stockage à usage général dans le groupe de ressources que vous avez créé à l’aide de la commande [az storage account create](/cli/azure/storage/account#az-storage-account-create).

Dans la commande suivante, indiquez un nom de compte de stockage global unique là où se trouve l’espace réservé `<storage_name>`. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
