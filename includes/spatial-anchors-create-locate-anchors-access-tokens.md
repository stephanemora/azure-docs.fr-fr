---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632453"
---
### <a name="access-tokens"></a>Jetons d'accès

Jetons d’accès sont une méthode plus robuste pour s’authentifier auprès des ancres spatiale d’Azure. Surtout à mesure que vous préparez votre application pour un déploiement de production. Le résumé de cette approche consiste à configurer un service back-end que votre application cliente peut s’authentifier en toute sécurité avec. Vos interfaces de service back-end avec AAD lors de l’exécution et le Azure Spatial ancres Secure Token Service pour demander un jeton d’accès. Ce jeton est ensuite remis à l’application cliente et utilisé dans le Kit de développement logiciel pour s’authentifier auprès des ancres spatiale d’Azure.
