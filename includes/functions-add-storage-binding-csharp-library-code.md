---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: be3c746146012195757ab06de0c424dbc8297e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "78190912"
---
Ajoutez le code qui utilise l’objet de liaison de sortie `msg` pour créer un message de file d’attente. Ajoutez ce code avant que la méthode retourne.

:::code language="csharp" range="28-32" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" :::

À ce stade, votre fonction doit se présenter comme suit :

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-36":::
