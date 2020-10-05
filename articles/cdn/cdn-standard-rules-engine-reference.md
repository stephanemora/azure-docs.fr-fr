---
title: Informations de référence sur le moteur de règles standard pour Azure CDN | Microsoft Docs
description: Documentation de référence sur les conditions de correspondance et les actions du moteur de règles standard pour Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: cae0ec1daed8277d1e7e4f3c5cbc701965ac00b8
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661764"
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

Actuellement, nous prenons en charge le caractère générique dans la **condition de correspondance UrlPath** dans le moteur de règles standard. Le caractère \* est un caractère générique qui représente un ou plusieurs caractères. 

## <a name="next-steps"></a>Étapes suivantes

- [Conditions de correspondance du moteur de règles Standard](cdn-standard-rules-engine-match-conditions.md)
- [Actions du moteur de règles Standard](cdn-standard-rules-engine-actions.md)
- [Appliquer HTTPS en utilisant le moteur de règles Standard](cdn-standard-rules-engine.md)
- [Vue d’ensemble d’Azure CDN](cdn-overview.md)
