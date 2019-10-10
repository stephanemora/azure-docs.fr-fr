---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 03ed1b28b4cabc054301e11c2b4d0f9e632abe02
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839076"
---
Vous pouvez utiliser la commande [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) pour afficher les files d’attente de stockage dans votre compte, comme dans l’exemple suivant :

```azurecli-interactive
az storage queue list --output tsv
```

La sortie de cette commande comprend une file d’attente nommée `outqueue`, qui a été créée lors de l’exécution de la fonction.

Ensuite, utilisez la commande [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) pour afficher les messages dans cette file d’attente, comme dans l’exemple suivant :

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

La chaîne retournée doit être la même que le message que vous avez envoyé pour tester la fonction.

> [!NOTE]  
> L’exemple précédent décode la chaîne retournée à partir de base64. En effet, les liaisons Stockage File d’attente écrivent dans Stockage Azure et lisent à partir de ce dernier sous forme de [chaînes base64](../articles/azure-functions/functions-bindings-storage-queue.md#encoding).