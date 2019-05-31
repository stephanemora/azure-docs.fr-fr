---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 35b087d13099b975a1c9c6d2dbd449935f5f0d1d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66249076"
---
```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|routePrefix|api|Préfixe d’itinéraire qui s’applique à tous les itinéraires. Utilisez une chaîne vide pour supprimer le préfixe par défaut. |
|maxOutstandingRequests|200<sup>*</sup>|Nombre maximal de requêtes en attente qui ont lieu à un moment donné. La limite inclut les requêtes qui sont en file d’attente, mais dont l’exécution n’a pas démarré, ainsi que les exécutions en cours. Toutes les requêtes entrantes au-delà de cette limite sont rejetées avec une réponse 429 « Trop occupé ». Ainsi, les appelants peuvent appliquer des stratégies temporelles de nouvelle tentative et vous êtes en mesure de contrôler les latences maximales de requêtes. Ce paramètre contrôle uniquement la mise en file d’attente qui se produit dans le chemin d’accès d’exécution de l’hôte de script. Les autres files d’attente, telles que la file d’attente des requêtes ASP.NET, sont toujours actives et ne sont pas concernées par ce paramètre. <sup>*</sup>La valeur par défaut pour la version 1.x est illimitée (`-1`). La valeur par défaut pour la version 2.x dans un plan de consommation est 200. La valeur par défaut pour la version 2.x dans un plan dédié est illimitée (`-1`).|
|maxConcurrentRequests|100<sup>*</sup>|Nombre maximal de fonctions HTTP exécutées en parallèle. Ce paramètre vous permet de contrôler la concurrence, et ainsi facilite la gestion de l’utilisation des ressources. Par exemple, vous pouvez disposer d’une fonction HTTP qui utilise de nombreuses ressources système (mémoire/processeur/sockets) et qui provoque par conséquent des situations où la concurrence est trop élevée. Vous pouvez également avoir une fonction qui effectue des requête vers un service tiers, et qui émet à ce titre des appels dont le débit doit être limité. Dans ces cas, il peut être pertinent d’appliquer une limitation. <sup>*</sup>La valeur par défaut pour la version 1.x est illimitée (`-1`). La valeur par défaut pour la version 2.x dans un plan de consommation est 100. La valeur par défaut pour la version 2.x dans un plan dédié est illimitée (`-1`).|
|dynamicThrottlesEnabled|true<sup>*</sup>|Lorsqu’il est activé, ce paramètre provoque la vérification périodique des compteurs de performance système, comme les connexions/les threads/les processus/la mémoire/le processeur, etc., par le pipeline de traitement des requêtes. Si l’un de ces compteurs dépasse un seuil supérieur intégré (80 %), les requêtes sont rejetées avec une réponse 429 (trop de demandes) jusqu’à ce qu’un niveau normal soit retrouvé. <sup>*</sup>La valeur par défaut pour la version 1.x est false. La valeur par défaut pour la version 2.x dans un plan de consommation est true. La valeur par défaut pour la version 2.x dans un plan dédié est false.|
