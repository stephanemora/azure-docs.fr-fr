---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67176758"
---
### <a name="access-tokens"></a>Jetons d’accès

Les jetons d’accès sont une méthode plus robuste pour vous authentifier à Azure Spatial Anchors. Plus particulièrement lorsque vous préparez votre application à un déploiement de production. En résumé, cette approche consiste à configurer un service back-end auquel votre application cliente peut s’authentifier en toute sécurité. Vos interfaces de service back-end avec AAD lors de l’exécution et le service de jeton de sécurité Azure Spatial Anchors demande un jeton d’accès. Ce jeton est ensuite remis à l’application cliente et utilisé dans le Kit de développement logiciel pour s’authentifier auprès Azure Spatial Anchors.
