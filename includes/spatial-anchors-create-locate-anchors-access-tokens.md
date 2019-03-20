---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 0e4c0b6d622fe0d8d20692ec6c0c889779e4601a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751745"
---
### <a name="access-tokens"></a>Jetons d'accès

Jetons d’accès sont une méthode plus robuste pour s’authentifier auprès des ancres spatiale d’Azure. Surtout à mesure que vous préparez votre application pour un déploiement de production. Le résumé de cette approche consiste à configurer un service back-end que votre application cliente peut s’authentifier en toute sécurité avec. Vos interfaces de service back-end avec AAD lors de l’exécution et du service Azure Spatial ancres STS pour demander un jeton d’accès. Ce jeton est ensuite remis à l’application cliente et utilisé dans le Kit de développement logiciel pour s’authentifier auprès des ancres spatiale d’Azure.
