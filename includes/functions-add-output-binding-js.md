---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 5e99c6e795729426964ec1f89f5f8c904e330227
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493375"
---
Ajoutez le code qui utilise l’objet de liaison de sortie `msg` sur `context.bindings` pour créer un message de file d’attente. Ajoutez ce code avant l’instruction `context.res`.

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

À ce stade, votre fonction doit se présenter comme suit :

:::code language="javascript" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::