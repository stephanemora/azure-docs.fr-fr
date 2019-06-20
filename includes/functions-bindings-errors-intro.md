---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c1784111cd2fc2c93b67510f310b9e513cf2b86e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176858"
---
Les [déclencheurs et les liaisons](../articles/azure-functions/functions-triggers-bindings.md) Azure Functions communiquent avec divers services Azure. Lors de l’intégration avec ces services, vous pouvez des erreurs qui proviennent des API des services Azure sous-jacents peuvent être déclenchées. Des erreurs peuvent également se produire lorsque vous tentez de communiquer avec d’autres services à partir de votre code de fonction à l’aide des bibliothèques REST ou clientes. Pour éviter la perte de données et garantir un comportement correct de vos fonctions, il est important de gérer les erreurs provenant de chaque source.

Les déclencheurs suivants prennent en charge les nouvelles tentatives intégrées :

* [stockage d’objets blob Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Stockage File d’attente Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (file d’attente/rubrique)](../articles/azure-functions/functions-bindings-service-bus.md)

Par défaut, ces déclencheurs sont retentés jusqu’à cinq fois. Après la cinquième tentative, ces déclencheurs écrivent un message à une [file d’attente de messages incohérents](../articles/azure-functions/functions-bindings-storage-queue.md#trigger---poison-messages) spéciale.

Pour les autres déclencheurs Fonctions, il n’y a pas de nouvelle tentative intégrée quand des erreurs se produisent au moment de l’exécution d’une fonction. Pour éviter la perte d’informations de déclencheur en cas d’erreur dans votre fonction, nous vous recommandons d’utiliser des blocs try-catch dans votre code de fonction pour détecter les erreurs. Lorsqu’une erreur se produit, écrivez les informations transmises par le déclencheur dans la fonction dans une « file d’attente de messages incohérents » spéciale. Cette approche est le même que celle utilisée par le [déclencheur du stockage Blob](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---poison-blobs).

De cette façon, vous pouvez capturer les événements déclencheurs qui pourraient être perdus en raison d’erreurs et les retenter ultérieurement à l’aide d’une autre fonction pour traiter les messages à partir de la file d’attente de messages incohérent en utilisant les informations stockées.  
