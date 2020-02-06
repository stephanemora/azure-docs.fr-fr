---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963651"
---
Pour optimiser les performances, utilisez un compte de stockage différent pour chaque application de fonction. Ceci est particulièrement important lorsque vous avez des fonctions Durable Functions ou des fonctions déclenchées par Event Hub, qui génèrent un volume élevé de transactions de stockage. Lorsque votre logique d’application interagit avec le stockage Azure, soit directement (à l’aide du SDK Stockage), soit via l’une des liaisons de stockage, vous devez utiliser un compte de stockage dédié. Par exemple, si vous avez une fonction déclenchée par Event Hub qui écrit des données dans le stockage d’objets blob, utilisez deux comptes de stockage : un pour l’application de fonction et un autre pour les objets blob stockés par la fonction.