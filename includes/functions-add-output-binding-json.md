---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: b118da6b751bc7a1e29ceef10c91dc5e9e3659c2
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474240"
---
Les attributs de liaison sont définis directement dans le fichier function.json. Selon le type de liaison, des propriétés supplémentaires peuvent être requises. La [configuration de la sortie de la file d’attente](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) décrit les champs requis pour une liaison de file d’attente Stockage Azure. L’extension vous permet d’ajouter facilement des liaisons au fichier function.json. 

Pour créer une liaison, cliquez avec le bouton droit (Ctrl+Clic sur macOS) sur le fichier `function.json` dans votre dossier HttpTrigger, puis choisissez **Ajouter une liaison...** . Suivez les invites pour définir les propriétés de liaison suivantes pour la nouvelle liaison :

| Prompt | Valeur | Description |
| -------- | ----- | ----------- |
| **Sélectionner le sens de la liaison** | `out` | La liaison est une liaison de sortie. |
| **Sélectionner une liaison avec un sens...** | `Azure Queue Storage` | La liaison est une liaison de file d’attente Stockage Azure. |
| **Nom utilisé pour identifier cette liaison dans votre code** | `msg` | Nom qui identifie le paramètre de liaison référencé dans votre code. |
| **File d’attente à laquelle le message sera envoyé** | `outqueue` | Nom de la file d’attente dans laquelle écrit la liaison. Si *queueName* n’existe pas, la liaison le crée à la première utilisation. |
| **Sélectionnez le paramètre dans « local.setting.json »** | `AzureWebJobsStorage` | Nom d’un paramètre d’application qui contient la chaîne de connexion du compte de stockage. Le paramètre `AzureWebJobsStorage` contient la chaîne de connexion du compte de stockage que vous avez créé avec l’application de fonction. |

Une liaison est ajoutée au tableau `bindings` dans votre fichier function.json, qui doit maintenant se présenter comme l’exemple suivant :

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```