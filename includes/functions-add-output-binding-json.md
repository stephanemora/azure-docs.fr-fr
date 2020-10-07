---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "78191022"
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

Une liaison est ajoutée au tableau `bindings` dans votre fichier function.json, qui doit ressembler à ce qui suit :

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::