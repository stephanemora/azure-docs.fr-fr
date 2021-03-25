---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244495"
---
Créons un secret appelé **mySecret** avec la valeur **Success!** . Il peut s’agir d’un mot de passe, d’une chaîne de connexion SQL ou de toute autre information que vous devez sécuriser et garder à disposition pour votre application. 

Pour ajouter un secret à votre nouveau coffre de clés, utilisez la commande Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) :

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```