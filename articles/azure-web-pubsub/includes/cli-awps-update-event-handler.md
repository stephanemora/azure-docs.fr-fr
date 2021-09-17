---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 156633c04cabd3e97bca4ae6d861b007be8fe84c
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446973"
---
Utilisez la commande Azure CLI [az webpubsub event-handler hub](/cli/azure/webpubsub/event-handler/hub) pour mettre à jour les paramètres du gestionnaire d’événements :

  > [!Important]
  > Remplacez &lt;your-unique-resource-name&gt; par le nom de votre ressource Web PubSub créée précédemment.
  > Remplacez &lt;domain-name&lt; par le nom ngrok imprimé.

```azurecli-interactive
az webpubsub event-handler hub update -n "<your-unique-resource-name>" -g "myResourceGroup" --hub-name myHub1 --template url-template="https://<domain-name>.ngrok.io/eventHandler" user-event-pattern="*" system-event-pattern="connected"
```
