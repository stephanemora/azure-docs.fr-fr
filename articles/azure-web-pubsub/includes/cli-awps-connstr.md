---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 4bfab858862bfb08cfaa2e9dc685b8ade7c0325e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121782443"
---
> [!Important]
> Une chaîne de connexion contient les informations d’autorisation requises pour que votre application accède au service Azure Web PubSub. La clé d’accès à l’intérieur dans la chaîne de connexion est semblable à un mot de passe racine pour votre service. Veillez toujours à protéger vos clés d’accès. Utilisez Azure Key Vault pour gérer et effectuer la rotation de vos clés en toute sécurité. Évitez de distribuer des clés d’accès à d’autres utilisateurs, de les coder en dur ou de les enregistrer en texte brut dans un emplacement accessible à d’autres personnes. Effectuez une rotation de vos clés si vous pensez qu’elles ont pu être compromises.

Utilisez la commande Azure CLI [az webpubsub key](/cli/azure/webpubsub#az_webpubsub_key) pour obtenir la valeur **ConnectionString** du service.

```azurecli-interactive
az webpubsub key show --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --query primaryConnectionString
```
