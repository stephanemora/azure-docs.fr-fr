---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424719"
---
Exécutez la commande suivante pour afficher les [journaux de streaming](../articles/azure-functions/functions-run-local.md#enable-streaming-logs) en temps quasi-réel :

```console
func azure functionapp logstream <APP_NAME> 
```

Dans une fenêtre de terminal distincte ou dans le navigateur, appelez à nouveau la fonction distante. Un journal détaillé de l’exécution de la fonction dans Azure est affiché dans le terminal. 