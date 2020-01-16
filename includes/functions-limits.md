---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 8946da455b4a395814d4cb5a833932c2e3d56f0a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75658523"
---
| Ressource | [Plan Consommation](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plan Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Plan App Service](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Montée en charge | Basé sur les événements | Basé sur les événements | [Manuel/Mise à l’échelle automatique](../articles/app-service/manage-scale-up.md) | 
| Nombre maximal d’instances | 200 | 100 | 10-20 |
|[Durée du délai d’expiration](../articles/azure-functions/functions-scale.md#timeout) (min) par défaut |5 | 30 |30<sup>2</sup> |
|[Durée du délai d’expiration](../articles/azure-functions/functions-scale.md#timeout) maximum (min) |10 | 60 | illimité<sup>3</sup> |
| Nbre max. de connexions sortantes (par instance) | 600 actives (1 200 au total) | unbounded | unbounded |
| Taille de requête max. (Mo)<sup>4</sup> | 100 | 100 | 100 |
| Longueur de chaîne de requête max.<sup>4</sup> | 4096 | 4096 | 4096 |
| Longueur d’URL de requête max.<sup>4</sup> | 8 192 | 8 192 | 8 192 |
| [ACU](../articles/virtual-machines/windows/acu.md) par instance | 100 | 210-840 | 100-840 |
| Mémoire max. (en Go par instance) | 1.5 | 3,5-14 | 1,75-14 |
| Applications de fonction par plan |100 |100 |illimité<sup>5</sup> |
| [Plans App Service](../articles/app-service/overview-hosting-plans.md) | 100 par [région](https://azure.microsoft.com/global-infrastructure/regions/) |100 par groupe de ressources |100 par groupe de ressources |
| Stockage<sup>6</sup> |1 Go |250 Go |50-1 000 Go |
| Domaines personnalisés par application</a> |500<sup>7</sup> |500 |500 |
| domaines personnalisés [Prise en charge SSL](../articles/app-service/configure-ssl-bindings.md) |connexion SNI SSL illimitée incluse | connexions 1 IP SSL et SNI SSL illimitées incluses |connexions 1 IP SSL et SNI SSL illimitées incluses | 

<sup>1</sup> Pour connaître les limites spécifiques des différentes options du plan App Service, consultez les [limites du plan App Service](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> Par défaut, le délai d’attente du runtime de Functions 1.x dans un plan App Service est illimité.  
<sup>3</sup> Nécessite que le plan App Service soit défini sur [Always On](../articles/azure-functions/functions-scale.md#always-on). Facturation aux [tarifs](https://azure.microsoft.com/pricing/details/app-service/) standard.  
<sup>4</sup> Ces limites sont [définies dans l’hôte](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> Le nombre réel d’applications de fonction qui peuvent être hébergées dépend de l’activité des applications, de la taille des instances de machine et de l’utilisation de ressources correspondante.  
<sup>6</sup> La limite de stockage est la taille totale du contenu dans le stockage temporaire de toutes les applications du même plan App Service. Le plan Consommation utilise Azure Files pour le stockage temporaire.  
<sup>7</sup> Lorsque votre application de fonction est hébergée dans un [Plan Consommation](../articles/azure-functions/functions-scale.md#consumption-plan), seule l’option CNAME est prise en charge. Pour les applications de fonction présentes dans un [plan Premium](../articles/azure-functions/functions-scale.md#premium-plan) ou un [plan App Service](../articles/azure-functions/functions-scale.md#app-service-plan), vous pouvez mapper un domaine personnalisé en utilisant l’un ou l’autre des enregistrements : CNAME ou A.
