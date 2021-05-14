---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/21/2021
ms.author: sngun
ms.openlocfilehash: cebf848f673b9e2651e6598d64aefff0cd371553
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064714"
---
## <a name="capacity-planner-modes"></a>Modes de planification de la capacité


|**Mode**  |**Description**  |
|---------|---------|
|De base|Fournit des estimations de coût et d’unités de requête/seconde rapides et de haut niveau. Ce mode utilise les paramètres Azure Cosmos DB par défaut pour la stratégie d’indexation, la cohérence et d’autres paramètres. <br/><br/>Utilisez le mode De base pour une estimation rapide et de haut niveau lorsque vous évaluez une charge de travail potentielle à exécuter sur Azure Cosmos DB. Pour plus d’informations, consultez Comment [estimer le coût en mode de base](#basic-mode).|
|Avancé|Fournit une estimation plus détaillée des unités de requête par seconde et des coûts, avec la possibilité de paramétrer des paramètres supplémentaires, tels que la stratégie d’indexation, le niveau de cohérence et d’autres paramètres qui affectent le coût et le débit. <br/><br/>Utilisez le mode Avancé lorsque vous estimez les unités de requête par seconde d’un nouveau projet ou que vous souhaitez obtenir une estimation plus détaillée. Pour plus d’informations, consultez Comment [estimer le coût en mode avancé](#advanced-mode).|
