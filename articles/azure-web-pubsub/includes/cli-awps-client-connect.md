---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: be837e20579a9236cc634e9518c82f17e2273e6b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123454020"
---
Utilisez la commande [az webpubsub client](/cli/azure/webpubsub/client) Azure CLI pour démarrer une connexion client WebSocket au service créé à l’étape précédente, en fournissant les informations suivantes :

- Nom du hub : chaîne de 1 à 127 caractères. Elle doit commencer par des caractères alphabétiques `(a-z, A-Z)` et ne contenir que des caractères alpha-numériques `(0-9, a-z, A-Z)` ou trait de soulignement `(_)`.

**Hub** est un ensemble logique des connexions WebSocket connectées. Vérifiez [À propos des hubs, groupes et connexions](../key-concepts.md) pour obtenir des informations sur les concepts.

  > [!Important]
  > Remplacez &lt;your-unique-resource-name&gt; par le nom de votre ressource Web PubSub créée précédemment.

- Nom de l’hub : **myHub1**.
- Groupe de ressources nommé : **myResourceGroup**.
- ID d’utilisateur : **user1**

```azurecli-interactive
az webpubsub client start --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name "myHub1" --user-id "user1"
```

Vous pouvez voir que la commande a établi une connexion WebSocket au service Web PubSub et que vous avez reçu un message JSON indiquant qu’il est désormais connecté avec succès et qu’il est attribué avec un `connectionId` unique :

```json
{"type":"system","event":"connected","userId":"user1","connectionId":"<your_unique_connection_id>"}
```
