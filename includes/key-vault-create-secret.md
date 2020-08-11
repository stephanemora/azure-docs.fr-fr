---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512798"
---
Créons un secret appelé **mySecret** avec la valeur **Success!** . Il peut s’agir d’un mot de passe, d’une chaîne de connexion SQL ou de toute autre information que vous devez sécuriser et garder à disposition pour votre application. 

Pour ajouter un secret à votre nouveau coffre de clés, utilisez la commande Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) :

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```