---
title: Azure Front Door – Réécriture d’URL | Microsoft Docs
description: Cet article explique comment Azure Front Door effectue la réécriture d’URL pour vos itinéraires, si cette option est configurée.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 1e5bd565be7a1cabf08ddf33c65eb12b5294249f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471470"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Réécriture d’URL (chemin de transfert personnalisé)
Azure Front Door prend en charge la **réécriture d’URL**. Vous pouvez ainsi configurer un chemin de transfert personnalisé facultatif à utiliser lors de la construction d’une requête à transférer au backend. Par défaut, si aucun chemin de transfert personnalisé n’est fourni, Front Door copie le chemin d’URL entrant dans l’URL utilisée dans la requête transférée. L’en-tête d’hôte utilisé dans la requête transférée est tel que configuré pour le backend sélectionné. Pour découvrir ce qu’il fait et comment vous pouvez le configurer, consultez [En-tête d’hôte backend](front-door-backend-pool.md#hostheader).

L’un des principaux avantages offerts par la réécriture d’URL à l’aide du chemin de transfert personnalisé est qu’elle copie toute partie du chemin entrant correspondant à un chemin contenant des caractères génériques dans le chemin transféré (ces segments de chemins sont les segments **verts** dans l’exemple ci-dessous) :
</br>
![Réécriture d’URL Azure Front Door ][1]

## <a name="url-rewrite-example"></a>Exemple de réécriture d’URL
Prenez une règle de routage avec les chemins et les hôtes frontend configurés suivants :

| Hôtes      | Chemins       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

La première colonne du tableau ci-dessous présente des exemples de requêtes entrantes et la deuxième colonne montre ce que serait le chemin de l’itinéraire correspondant « le plus spécifique ».  La troisième colonne et les colonnes suivantes de la première ligne du tableau sont des exemples de **chemins de transfert personnalisé** configurés, le reste des lignes dans ces colonnes représentant des exemples de ce que serait le chemin de la requête transférée s’il correspondait à la requête de cette ligne.

Par exemple, la deuxième ligne indique que pour la requête entrante `www.contoso.com/sub`, si le chemin de transfert personnalisé est `/`, le chemin transféré sera `/sub`. Si le chemin de transfert personnalisé est `/fwd/`, le chemin transféré sera `/fwd/sub`. Et ainsi de suite pour les colonnes restantes. Les parties **en gras** des chemins ci-dessous représentent les éléments qui font partie de la mise en correspondance de caractère générique.


| Requête entrante       | Chemin correspondant le plus spécifique | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>Paramètres facultatifs
Vous pouvez également spécifier certains paramètres facultatifs supplémentaires pour tout paramètre de règle de routage donné :

* **Configuration du cache** : si vous désactivez ou ne spécifiez pas ce paramètre, les requêtes qui correspondent à cette règle de routage ne tenteront pas d’utiliser le contenu mis en cache, mais elles extraieront toujours à partir du backend. Apprenez-en davantage sur la [mise en cache avec Front Door](front-door-caching.md).



## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg