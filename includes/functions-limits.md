---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 8f30d9fb2fcfe8f55af13d7726aa8458f8733b3f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236017"
---
| Ressource | [Plan de consommation](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plan Premium](../articles/azure-functions/functions-scale.md#premium-plan-public-preview) | [Plan App Service](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Montée en charge | Basée sur les événements | Basée sur les événements | [Manuel/Mise à l’échelle](../articles/app-service/web-sites-scale.md) | 
|Par défaut [durée du délai d’expiration](../articles/azure-functions/functions-scale.md#timeout) (min) |5. | 30 |30<sup>2</sup> |
|Max [durée du délai d’expiration](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | unbounded | unbounded<sup>3</sup> |
| Nombre maximal de connexions sortantes (par instance) | 600 active (total de 1200) | unbounded | unbounded |
| Taille de demande maximale (Mo)<sup>4</sup> | 100 | 100 | 100 |
| Longueur de chaîne de requête maximale<sup>4</sup> | 4096 | 4096 | 4096 |
| Longueur d’URL maximale demande<sup>4</sup> | 8 192 | 8 192 | 8 192 |
| [ACU](../articles/virtual-machines/windows/acu.md) par instance | 100 | 210-840 | 100-840 |
| Mémoire maximale (en Go par instance) | 1.5 | 3.5-14 | 1.75-14 |
| Applications de fonction par plan |100 |100 |unbounded<sup>5</sup> |
| [Plans App Service](../articles/app-service/overview-hosting-plans.md) | 100 par [région](https://azure.microsoft.com/global-infrastructure/regions/) |100 par groupe de ressources |100 par groupe de ressources |
| Stockage<sup>6</sup> |1 Go |250 Go |50-1000 GO |
| Domaines personnalisés par application</a> |500<sup>7</sup> |500 |500 |
| domaines personnalisés [Prise en charge SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Non pris en charge, certificat générique pour *. azurewebsites.net disponible par défaut| unbounded SNI SSL et 1 connexion SSL IP incluses |unbounded SNI SSL et 1 connexion SSL IP incluses | 

<sup>1</sup>pour obtenir des limites spécifiques pour les différentes options de plan App Service, consultez le [limites de plan App Service](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup>par défaut, le délai d’expiration pour le runtime de Functions 1.x dans un plan App Service est illimitée.  
<sup>3</sup>nécessite le plan App Service soient définies pour [Always On](../articles/azure-functions/functions-scale.md#always-on). Payer au standard [taux](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> ces limites sont [définie dans l’hôte](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> le nombre réel d’applications de fonction que vous pouvez héberger dépend de l’activité des applications, la taille des instances de machine et l’utilisation des ressources correspondantes.   
<sup>6</sup>la limite de stockage est la taille totale du contenu dans le stockage temporaire dans toutes les applications dans le même plan App Service. Plan de consommation utilise Azure Files pour le stockage temporaire.  
<sup>7</sup>lorsque votre application de fonction est hébergée dans un [plan de consommation](../articles/azure-functions/functions-scale.md#consumption-plan), seule l’option d’enregistrement CNAME est pris en charge. Pour les applications de fonction dans un [plan Premium](../articles/azure-functions/functions-scale.md#premium-plan-public-preview) ou un [plan App Service](../articles/azure-functions/functions-scale.md#app-service-plan), vous pouvez mapper un domaine personnalisé à l’aide d’un enregistrement CNAME ou un enregistrement A. 
