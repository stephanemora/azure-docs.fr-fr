---
title: Qu’est-ce que le routage Azure Front Door Standard/Premium ?
description: Cet article vous aide à comprendre comment Azure Front Door Standard/Premium détermine quelle règle de routage utiliser pour une demande entrante.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101097799"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>Qu’est-ce que le routage Azure Front Door Standard/Premium (préversion) ?

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Le routage Azure Front Door Standard/Premium définit comment le trafic est géré quand la demande entrante arrive dans l’environnement Azure Front Door. Via les paramètres de routage, une association est définie entre un domaine et un groupe d’origines de back-end. En activant les fonctionnalités avancées comme Modèle à suivre et Ensemble de règles, vous pouvez exercer un contrôle plus précis sur le trafic.

La configuration du routage Front Door Standard/Premium est constituée de deux parties principales : le « côté gauche » et le « côté droit ». Nous faisons correspondre la demande entrante au côté gauche de la route, tandis que le côté droit définit comment nous traitons la demande.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="incoming-match-left-hand-side"></a>Mise en correspondance de la demande entrante (côté gauche)

Les propriétés suivantes déterminent si la demande entrante correspond à la règle de routage (ou côté gauche) :

* **Protocoles HTTP** (HTTP/HTTPS)
* **Hôtes** (par exemple www\.foo.com, \*.bar.com)
* **Chemins d’accès** (par exemple, /\*, /users/\*, /file.gif)

Ces propriétés sont développées en interne de telle sorte que chaque combinaison Protocole/Hôte/Chemin d’accès constitue une correspondance potentielle.

### <a name="route-data-right-hand-side"></a>Données d’itinéraire (côté droit)

Le choix de la façon de traiter la demande dépend de l’activation ou non de la mise en cache pour la route. Si une réponse mise en cache n’est pas disponible, la demande est transférée au back-end approprié.

## <a name="route-matching"></a>Mise en correspondance avec un itinéraire

Cette section porte essentiellement sur la façon d’établir une correspondance avec une règle de routage de porte d’entrée donnée. L’idée de base est que nous établissons toujours en premier lieu une correspondance avec la **correspondance la plus spécifique** en tenant compte uniquement du « côté gauche ».  Nous établissons une correspondance d’abord en fonction du protocole HTTP, puis de l’hôte frontend, puis du chemin d’accès.

### <a name="frontend-host-matching"></a>Mise en correspondance avec des hôtes frontend

Quand il s’agit de mettre en correspondance des hôtes frontend, nous utilisons la logique définie ci-dessous :

1. Recherche d’un routage ayant une correspondance parfaite sur l’hôte.
2. Si aucune correspondance exacte n’est trouvée sur l’hôte frontend, la demande est rejetée et une erreur 400 Demande incorrecte est envoyée.

Pour expliquer plus en détail ce processus, nous allons examiner un exemple de configuration d’itinéraires de porte d’entrée (côté gauche uniquement) :

| Règle de routage | Hôtes frontend | Path |
|-------|--------------------|-------|
| Un | foo.contoso.com | /\* |
| B | foo.contoso.com | /users/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Si les demandes entrantes suivantes étaient envoyées à la porte d’entrée, elles correspondraient aux règles de routage suivantes issues du tableau précédent :

| Hôte frontend entrant | Règle(s) de routage correspondante(s) |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Erreur 400 : Demande incorrecte |
| foo.adventure-works.com | C |
| contoso.com | Erreur 400 : Demande incorrecte |
| www\.adventure-works.com | Erreur 400 : Demande incorrecte |
| www\.northwindtraders.com | Erreur 400 : Demande incorrecte |

### <a name="path-matching"></a>Mise en correspondance avec un chemin

Après cela, Azure Front Door Standard/Premium détermine l’hôte front-end spécifique et filtre les règles de routage possibles pour retenir seulement les routes avec cet hôte front-end. Front Door filtre ensuite les règles de routage en fonction du chemin de la demande. Nous utilisons une logique comparable à celle des hôtes frontend :

1. Recherche d’une règle de routage ayant une correspondance exacte dans le chemin.
2. Si aucune correspondance exacte n’est trouvée dans le chemin, recherche de règles de routage avec un chemin générique qui correspond.
3. Si aucune règle de routage n’est trouvée avec un chemin correspondant, la demande est rejetée et une réponse HTTP 400 : Demande incorrecte est retournée.

>[!NOTE]
> Les chemins sans caractère générique sont considérés comme des correspondances parfaites. Même si le chemin se termine par une barre oblique, il est quand même considéré comme tel.

Pour une explication plus détaillée, examinons d’autres exemples :

| Règle de routage | Hôte frontend    | Path     |
|-------|---------|----------|
| Un     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

Compte tenu de cette configuration, le tableau d’exemples de correspondances obtenu serait le suivant :

| Demandes entrantes    | Itinéraire correspondant |
|---------------------|---------------|
| www\.contoso.com/            | Un             |
| www\.contoso.com/a           | B             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | B             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | B             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> En l’absence de règle de routage pour un hôte frontend parfaitement concordant avec un chemin d’itinéraire fourre-tout (`/*`), il n’existe pas de correspondance avec une règle de routage.
>
> Exemple de configuration :
>
> | Routage | Host             | Path    |
> |-------|------------------|---------|
> | Un     | profile.contoso.com | /api/\* |
>
> Tableau des correspondances :
>
> | Requête entrante       | Itinéraire correspondant |
> |------------------------|---------------|
> | profile.domain.com/other | Aucun. Erreur 400 : Demande incorrecte |

### <a name="routing-decision"></a>Décision de routage

Une fois qu’Azure Front Door Standard/Premium a déterminé une seule règle de routage, il doit ensuite choisir comment traiter la demande. Si Azure Front Door Standard/Premium a une réponse dans le cache pour la règle de routage correspondante, la réponse à la demande est renvoyée au client. Azure Front Door Standard/Premium détermine si vous avez ou non un ensemble de règles pour la règle de routage en correspondance. S’il n’existe pas d’ensemble de règles défini, la demande est transférée telle quelle au pool de backends. Sinon, l’ensemble de règles est exécuté dans l’ordre où elles sont configurées.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer une instance Front Door Standard/Premium](create-front-door-portal.md).
