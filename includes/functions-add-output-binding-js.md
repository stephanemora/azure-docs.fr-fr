---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 098aaba0a357c13a059d3c4042a19715c6b5ad42
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "78191027"
---
Ajoutez le code qui utilise l’objet de liaison de sortie `msg` sur `context.bindings` pour créer un message de file d’attente. Ajoutez ce code avant l’instruction `context.res`.

:::code language="javascript" range="7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

À ce stade, votre fonction doit se présenter comme suit :

:::code language="javascript" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::