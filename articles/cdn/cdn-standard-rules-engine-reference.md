---
title: Informations de référence sur le moteur de règles standard pour Azure CDN | Microsoft Docs
description: Documentation de référence sur les conditions de correspondance et les actions du moteur de règles standard pour Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/22/2020
ms.author: allensu
ms.openlocfilehash: 6260a4b78197329e020bebaa3bc08db5ad792086
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559301"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Informations de référence sur le moteur de règles standard pour Azure CDN

Dans le [moteur de règles Standard](cdn-standard-rules-engine.md) pour Azure Content Delivery Network (Azure CDN), une règle se compose d’une ou de plusieurs conditions de correspondance et d’une action. Cet article fournit les descriptions détaillées des conditions de correspondance et fonctionnalités disponibles dans le moteur de règles standard pour Azure CDN.

Le moteur de règles est conçu pour être l’autorité finale sur la façon dont certains types de demandes sont traités par Standard Azure CDN.

**Utilisations courantes des règles** :

- Remplacer ou définir une stratégie de cache personnalisée.
- Rediriger les demandes.
- Modifier la requête HTTP et les en-têtes de réponse.

## <a name="terminology"></a>Terminologie

Pour définir une règle dans le moteur de règles, définissez les [conditions de correspondance](cdn-standard-rules-engine-match-conditions.md) et les [actions](cdn-standard-rules-engine-actions.md) :

 ![Structure des règles Azure CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Chaque règle peut avoir jusqu’à dix conditions de correspondance et cinq actions. Chaque point de terminaison Azure CDN peut avoir jusqu’à 25 règles. 

Une valeur par défaut *règle globale* est incluse dans cette limite. La règle globale n’a pas de conditions de correspondance ; les actions définies dans une règle globale se déclenchent toujours.

## <a name="limits-and-pricing"></a>Limites et tarification 

Chaque point de terminaison Azure CDN peut avoir jusqu’à 25 règles. Chaque règle peut avoir jusqu’à dix conditions de correspondance et cinq actions. La tarification du moteur de règles suit les dimensions ci-dessous : 
- Règles : 1 USD par règle par mois 
- Demandes traitées : 0,60 USD par million de demandes
- Les 5 premières règles restent gratuites

## <a name="syntax"></a>Syntaxe

La façon dont les caractères spéciaux sont traités dans une règle varie selon la façon dont les différentes conditions de correspondance et actions gèrent les valeurs de texte. Une condition de correspondance ou une action peut interpréter le texte de l’une des manières suivantes :

- [Valeurs littérales](#literal-values)
- [Valeurs de caractère générique](#wildcard-values)


### <a name="literal-values"></a>Valeurs littérales

Le texte interprété comme une valeur littérale traite tous les caractères spéciaux, *à l’exception du symbole %* , comme une partie de la valeur qui doit être mise en correspondance dans une règle. Par exemple, une condition de correspondance littérale définie sur `'*'` est satisfaite uniquement lorsque la valeur exacte `'*'` est trouvée.

Un symbole de pourcentage est utilisé pour indiquer l’encodage des URL (par exemple, `%20`).

### <a name="wildcard-values"></a>Valeurs de caractère générique

Le texte interprété comme une valeur de caractère générique attribue une signification supplémentaire aux caractères spéciaux. Le tableau suivant décrit la façon dont les caractères spéciaux spécifiques sont interprétés dans le moteur de règles standard :

Caractère | Description
----------|------------
\ | Une barre oblique inverse est utilisée pour échapper les caractères spécifiés dans ce tableau. Une barre oblique inverse doit être spécifiée juste avant le caractère spécial à échapper. Par exemple, la syntaxe suivante échappe un astérisque : `\*`
% | Un symbole de pourcentage est utilisé pour indiquer l’encodage des URL (par exemple, `%20`).
\* | Un astérisque est un caractère générique représentant un ou plusieurs caractères.
espace | Un caractère d’espace indique qu’une condition de correspondance peut être remplie par les valeurs ou les modèles spécifiés.
guillemets simples | Un guillemet simple n’a pas de signification particulière. Toutefois, un jeu de guillemets simples indique qu’une valeur doit être traitée comme une valeur littérale. Les guillemets simples peuvent être utilisés des manières suivantes :<ul><li>Permettre de remplir une condition de correspondance lorsque la valeur spécifiée correspond à une partie de la valeur de comparaison.  Par exemple, `'ma'` peut correspondre à l’une des chaînes suivantes : <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>Permettre de spécifier un caractère spécial en tant que caractère littéral. Par exemple, vous pouvez spécifier un caractère d’espace littéral en plaçant un caractère d’espace dans un jeu de guillemets simples (`' '` ou `'<sample value>'`).</li><li>Permettre de spécifier une valeur vide. Spécifiez une valeur vide en entrant un jeu de guillemets simples ( **''** ).</li></ul>**Important !**<br /><ul><li>Si la valeur spécifiée ne contient pas de caractère générique, elle est automatiquement considérée comme une valeur littérale. Vous n’avez pas besoin de spécifier un jeu de guillemets simples pour une valeur littérale.</li><li>Si une barre oblique inverse n’est pas utiliser pour échapper à un autre caractère dans ce tableau, elle est ignorée lorsqu’elle est placée dans un jeu de guillemets simples.</li><li>Une autre manière de spécifier un caractère spécial en tant que caractère littéral consiste à l’échapper à l’aide d’une barre oblique inverse (`\`).</li></ul>

## <a name="next-steps"></a>Étapes suivantes

- [Conditions de correspondance du moteur de règles Standard](cdn-standard-rules-engine-match-conditions.md)
- [Actions du moteur de règles Standard](cdn-standard-rules-engine-actions.md)
- [Appliquer HTTPS en utilisant le moteur de règles Standard](cdn-standard-rules-engine.md)
- [Vue d’ensemble d’Azure CDN](cdn-overview.md)
