---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: 753a3d16792e0dbaffe318cd188ea3f0d40bd5cb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "78191024"
---
Ajoutez du code qui utilise l’applet de commande `Push-OutputBinding` pour écrire du texte dans la file d’attente à l’aide de la liaison de sortie `msg`. Ajoutez ce code avant de définir l’état OK dans l’instruction `if`.

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

À ce stade, votre fonction doit se présenter comme suit :

:::code language="powershell" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::
