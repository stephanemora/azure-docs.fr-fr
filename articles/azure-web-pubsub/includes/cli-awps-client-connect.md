---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 1f6356ceaa844433e6da3102b8db0fcbd5a61615
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121782439"
---
Utilisez la commande [az webpubsub client](/cli/azure/webpubsub/client) Azure CLI pour démarrer une connexion client WebSocket au service créé à l’étape précédente, en fournissant les informations suivantes :

- Nom du hub : chaîne de 1 à 127 caractères. Elle doit commencer par des caractères alphabétiques `(a-z, A-Z)` et ne contenir que des caractères alpha-numériques `(0-9, a-z, A-Z)` ou trait de soulignement `(_)`.

**Hub** est un ensemble logique des connexions WebSocket connectées. Vérifiez [À propos des hubs, groupes et connexions](../key-concepts.md) pour obtenir des informations sur les concepts.

  > [!Important]
  > Remplacez &lt;your-unique-resource-name&gt; par le nom de votre ressource Web PubSub créée précédemment.

- Nom de l’hub : **myHub1**.
- Groupe de ressources nommé : **myResourceGroup**.

```azurecli-interactive
az webpubsub client start --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name myHub1
```

Vous pouvez voir que la commande a établi une connexion WebSocket au service Web PubSub et que vous avez reçu un message JSON indiquant qu’il est désormais connecté avec succès et qu’il est attribué avec un `connectionId` unique :

```json
{"type":"system","event":"connected","userId":null,"connectionId":"<your_unique_connection_id>"}
```
