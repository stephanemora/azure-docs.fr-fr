---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493941"
---
Visual Studio Code vous permet d’ajouter des liaisons à votre fichier function.json en suivant une série d’invites pratique. 

Pour ajouter une liaison, ouvrez la palette de commandes (F1) et saisissez **Azure Functions : ajouter une liaison…** , choisissez la fonction pour la nouvelle liaison, puis suivez les invites, qui varient en fonction du type de liaison ajouté à la fonction. 

Voici des exemples d’invites pour définir une nouvelle liaison de sortie de stockage :

| Invite | Valeur | Description |
| -------- | ----- | ----------- |
| **Sélectionner le sens de la liaison** | `out` | La liaison est une liaison de sortie. |
| **Sélectionner une liaison avec un sens** | `Azure Queue Storage` | La liaison est une liaison de file d’attente Stockage Azure. |
| **Nom utilisé pour identifier cette liaison dans votre code** | `msg` | Nom qui identifie le paramètre de liaison référencé dans votre code. |
| **File d’attente à laquelle le message sera envoyé** | `outqueue` | Nom de la file d’attente dans laquelle écrit la liaison. Si *queueName* n’existe pas, la liaison le crée à la première utilisation. |
| **Sélectionnez un paramètre dans « local.setting.json »** | `MyStorageConnection` | Nom d’un paramètre d’application qui contient la chaîne de connexion du compte de stockage. Le paramètre `AzureWebJobsStorage` contient la chaîne de connexion du compte de stockage que vous avez créé avec l’application de fonction. |

Vous pouvez également cliquer avec le bouton droit (Ctrl+clic sur macOS) directement sur le fichier **function.json** dans le dossier de votre fonction, sélectionner **Ajouter une liaison** et suivre les mêmes invites.

Dans cet exemple, une liaison est ajoutée au tableau `bindings` dans votre fichier function.json :

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```