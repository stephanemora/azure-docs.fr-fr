---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9c3dd4ecede4e8087b2ec9ad470d5ab415ea6e35
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803280"
---
Mettez à jour *HttpExample\\\_\_init\_\_.py* pour correspondre au code suivant, en ajoutant le paramètre `msg` à la définition de fonction et `msg.set(name)` sous l’instruction `if name:`.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Le paramètre `msg` est une instance de la [`azure.functions.Out class`](/python/api/azure-functions/azure.functions.out). Sa méthode `set` écrit un message de type chaîne dans la file d’attente. Dans le cas présent, il s’agit du nom passé à la fonction dans la chaîne de requête de l’URL.
