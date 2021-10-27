---
title: Azure Front Door - Supervision de la mise en correspondance d’une règle de routage | Microsoft Docs
description: Cet article vous aide à comprendre comment Azure Front Door fait correspondre la règle de routage à utiliser pour une demande entrante
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 815f7500266c175585fa1b27292e593fc73fd8d7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003777"
---
# <a name="how-requests-are-matched-to-a-routing-rule"></a>Comment les demandes sont mises en correspondance avec une règle de routage

Après avoir établi une connexion et effectué une négociation TLS, l’une des premières choses que fait un environnement Front Door quand une requête lui parvient est de déterminer à quelle règle de routage faire correspondre la requête, puis d’effectuer l’action définie dans la configuration. Le document suivant explique comment une porte d’entrée détermine la configuration d’itinéraire à utiliser pendant le traitement d’une requête HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Structure d’une configuration d’itinéraire de porte d’entrée
La configuration d’une règle de routage de porte d’entrée est constituée de deux parties principales : le « côté gauche » et le « côté droit ». Nous faisons correspondre la demande entrante au côté gauche de l’itinéraire, tandis que le côté droit définit le mode de traitement de la demande.

### <a name="incoming-match-left-hand-side"></a>Mise en correspondance de la demande entrante (côté gauche)
Les propriétés suivantes déterminent si la demande entrante correspond à la règle de routage (ou côté gauche) :

* **Protocoles HTTP** (HTTP/HTTPS)
* **Hôtes** (par exemple www\.foo.com, \*.bar.com)
* **Chemins d’accès** (par exemple, /\*, /users/\*, /file.gif)

Ces propriétés sont développées en interne de telle sorte que chaque combinaison Protocole/Hôte/Chemin d’accès constitue une correspondance potentielle.

### <a name="route-data-right-hand-side"></a>Données d’itinéraire (côté droit)
Le choix du mode de traitement de la demande dépend de l’activation ou non de la mise en cache pour l’itinéraire en question. Par conséquent, si aucune réponse n’est mise en cache pour la demande, celle-ci est transférée au backend approprié dans le pool de backends configuré.

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
Après avoir identifié l’hôte frontend spécifique et filtré les règles de routage possibles, qui se limitent aux itinéraires de cet hôte frontend, la porte d’entrée filtre alors les règles de routage en fonction du chemin de la demande. Nous utilisons une logique comparable à celle des hôtes frontend :

1. Recherchez une règle d’acheminement ayant une correspondance exacte dans le chemin.
2. Si aucune correspondance exacte n’est trouvée dans le chemin, recherchez des règles d’acheminement avec un chemin générique qui correspond.
3. Si aucune règle de routage n’est trouvée avec un chemin correspondant, la demande est rejetée et une réponse HTTP 400 : Demande incorrecte est retournée.

>[!NOTE]
> * Les chemins sans caractère générique sont considérés comme des correspondances parfaites. Même si le chemin se termine par une barre oblique, il est quand même considéré comme tel.
> * Les chemins d’accès du paramètre Modèles à mettre en correspondance ne respectent pas la casse, ce qui signifie que les chemins d’accès ayant une casse différente sont traités comme des doublons. Par exemple, vous avez le même hôte qui utilise le même protocole avec les chemins d’accès `/FOO` et `/foo`. Ces chemins d’accès sont considérés comme des doublons, ce qui n’est pas autorisé dans le paramètre Modèles à mettre en correspondance.
> 

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

Après avoir établi une correspondance avec une seule règle d’acheminement Front Door, choisissez le mode de traitement de la demande. Si Front Door dispose d’une réponse en cache pour la règle d’acheminement correspondante, cette réponse est renvoyée au client. Si Front Door n’a pas de réponse en cache pour la règle d’acheminement correspondante, l’évaluation suivante consiste à déterminer si vous avez configuré [une réécriture d’URL (un chemin de transfert personnalisé)](front-door-url-rewrite.md) pour la règle d’acheminement correspondante. Si aucun chemin de transfert personnalisé n’est défini, la demande est transférée en l’état au serveur principal approprié dans le pool principal configuré. Si un chemin de transfert personnalisé a été défini, le chemin de la demande est mis à jour selon le [chemin de transfert personnalisé](front-door-url-rewrite.md) défini, puis transféré au serveur principal.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).
