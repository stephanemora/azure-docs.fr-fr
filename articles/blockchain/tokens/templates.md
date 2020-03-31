---
title: Modèles Azure Blockchain Tokens
description: Les modèles Azure Blockchain Tokens sont des modèles standardisés et réutilisables qui simplifient la création et le déploiement de jetons basés sur un registre.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79227385"
---
# <a name="azure-blockchain-tokens-templates"></a>Modèles Azure Blockchain Tokens

[!INCLUDE [Preview note](./includes/preview.md)]

Un modèle Azure Blockchain Tokens est un modèle standardisé et réutilisable qui simplifie la création et le déploiement de jetons basés sur un registre. Un modèle se compose d’une formule basée sur la grammaire [Infrastructure de taxonomie des jetons (TTF)](overview.md#token-taxonomy-framework). La grammaire englobe le type de jeton de base et l’ensemble des comportements pour le jeton.  

Par exemple, le modèle de jeton **τϜ{d,m,b,r}** décrit un jeton de base fongible qui est divisible, émissible, épuisable, et doté d’un support de rôle.
  
## <a name="base-token-types"></a>Types de jetons

Lors de la définition et de la création du jeton basé sur le registre pour votre ressource particulière, il est important de prendre en compte le jeton de base à utiliser.

### <a name="fungible"></a>Fongible

Des jetons fongible (τF) ont des valeurs interchangeables entre eux tant qu’ils font partie de la même classe ou de la même série. Un jeton a la même valeur qu’un autre jeton ou une quantité donnée de jetons a la même valeur qu’une autre quantité égale. Par exemple, un dollar est un jeton fongible. Si deux personnes détiennent chacune un billet d’un dollar, elles peuvent échanger ces billets sans conséquence. Ils ont une valeur égale. 

### <a name="non-fungible"></a>Non fongible

Des jetons non fongibles (τN) ne sont pas interchangeables avec d’autres jetons du même type car ils ont généralement des valeurs différentes. Par exemple, un titre de propriété est un jeton non fongible. Des titres de propriété de deux appartements différents dans un même complexe n’ont pas nécessairement la même valeur, en raison de leurs emplacements ou étages respectifs. La valeur perçue n’est pas la même pour les deux jetons de titre de propriété.

### <a name="hybrid"></a>Hybride

Les jetons hybrides sont des jetons qui ont des composants à la fois de jetons fongibles et de jetons non fongibles. Un jeton hybride est un type de jeton de base qui possède une classe de l’autre type de jeton.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Base non fongible hybride avec segments fongibles

Un jeton de base non fongible hybride avec segments fongibles a une base non fongible avec des segments de jeton fongibles.
Par exemple, un ticket de concert est un jeton hybride dont la date et l’heure constituent le jeton de base non fongible. Les tickets des diverses sections de places pour le concert sont les segments avec des jetons fongibles. Les tickets sont interchangeables au sein de leurs sections de places respectives, mais pas entre des sections.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Base fongible hybride avec segments non-fongibles

Un jeton de base fongible hybride avec segments non fongibles a une base fongible avec des segments de jeton non fongibles. Par exemple, un titre adossé à des créances hypothécaires est un jeton hybride dans lequel plusieurs propriétaires constituent la base fongible fractionnée entre de nombreux propriétaires. Le titre est interchangeable. Les emprunts individuels sont les segments non fongibles qui représentent le titre spécifique adossé à des créances hypothécaires.

## <a name="token-behaviors"></a>Comportements de jeton

Le comportement d’un jeton définit les capacités ou restrictions du jeton. Le comportement comprend des propriétés de prise en charge qui font partie de la définition du jeton. Des comportements peuvent être appliqués à tous les types de jetons ou à un seul. Les comportements peuvent être internes ou externes selon leurs effets. Un comportement interne active ou restreint des propriétés sur le jeton lui-même. Un comportement externe active ou restreint l’invocation du comportement à partir d’un acteur externe.

Pour plus d’informations sur les comportements de jetons selon l’ Infrastructure de taxonomie des jetons (TTF) pris en charge par Azure Blockchain Tokens, voir [Composabilité des jetons](composability.md).

## <a name="pre-built-token-templates"></a>Modèles de jeton prédéfinis

Azure Blockchain Tokens fournit quatre modèles de jetons prédéfinis utilisables sans modification. Vous pouvez utiliser ces modèles prédéfinis dans la plupart des cas d’usage pour commencer à créer, à déployer et à gérer vos jetons rapidement.

### <a name="commodity-tokens"></a>Jetons de marchandise

Les jetons de marchandise ont une valeur cohérente et sont transférables. Par exemple, un baril de pétrole ou une unité d’énergie.

**𝜏F{~d,t,m,b,r}** : fongible, entier, transférable, émissible, épuisable et doté d’un support de rôle.

De nombreux scénarios de blockchain nécessitent une transparence et une visibilité de la chaîne logistique ou de plusieurs organisations. Les jetons de marchandises sont basés sur ces cas d’usage courants. Les jetons sont interchangeables et cohérents. Le modèle de jeton de marchandise est flexible et personnalisable avec des métadonnées.

### <a name="qualified-tokens"></a>Jetons qualifiés

Les jetons qualifiés représentent une chose gagné. Ils sont généralement associés à une seule entité et ne peuvent pas être transférés. Par exemple, un diplôme ou un PV d’infraction.

**𝜏N{s,~t}** : non fongible, singleton et non transférable

Différents scénarios d’audit et d’attestation requièrent que la propriété du jeton ne puisse pas être modifiée. Il existe un ensemble de cas d’usage qui nécessitent la fourniture d’un jeton qualifié, que l’association soit correcte ou incorrecte.

### <a name="asset-tokens"></a>Jetons de d’actif

Les jetons d’actifs ont une valeur unique dépendant de l’élément et ne sont pas convertibles en marchandises. Par exemple, un objet de musée ou un titre de propriété.

**𝜏N{s,t}** : non fongible, singleton et transférable

Les jetons d’actif peuvent être confondus avec des jetons de marchandise. La principale différence entre eux est que les jetons d’actif sont uniques par nature et que leur valeur est indépendante de leur type. Par exemple, un objet d’art tel qu’une peinture à huile de la main d’un artiste établi constitue un jeton d’actif. Toutefois, une copie imprimée de la Mona Lisa est considérée comme un jeton de marchandise. De même, un titre de propriété est un jeton d’actif, car sa valeur réside dans les qualités subjectives de la propriété.

### <a name="ticket-tokens"></a>Jetons de ticket

Les jetons de ticket ont une valeur cohérente mais expirent généralement. Par exemple, un billet d’avion.

**𝜏N{m,b,r}** : non fongible, émissible, épuisable et doté d’un support de rôle.

Les jetons de ticket ont généralement une date d’expiration qui les différencie d’un jeton de marchandise ordinaire. Par exemple, un billet d’avion, un ticket de concert ou un ticket d’événement sportif ont tous des options d’attribution de siège et des dates d’utilisation spécifiques. Vous ne pouvez pas facilement échanger des tickets dont les dates et les sièges diffèrent.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin de plus de flexibilité pour votre scénario, découvrez comment créer vos propres modèles de jeton en tirant parti de la [composabilité des jetons](composability.md).
