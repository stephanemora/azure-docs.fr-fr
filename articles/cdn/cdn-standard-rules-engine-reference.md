---
title: Référence du moteur de règles standard Azure CDN | Microsoft Docs
description: Documentation de référence sur les fonctionnalités et conditions de correspondance du moteur de règles Azure CDN standard.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615832"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>Référence du moteur de règles Azure CDN à partir du moteur de règles Microsoft

Cet article fournit les descriptions détaillées des conditions de correspondance et fonctionnalités disponibles pour le [moteur de règles standard](cdn-standard-rules-engine.md) Azure Content Delivery Network (CDN).

Le moteur de règles est conçu pour être l’autorité finale sur la façon dont certains types de demandes sont traités par le CDN.

**Utilisations courantes** :

- Remplacer ou définir une stratégie de cache personnalisée.
- Rediriger les demandes.
- Modifier la requête HTTP et les en-têtes de réponse

## <a name="terminology"></a>Terminologie

Une règle est définie à l’aide de [**conditions de correspondance**](cdn-standard-rules-engine-match-conditions.md) et [**d’actions**](cdn-standard-rules-engine-actions.md). Ces éléments sont mis en surbrillance dans l’illustration suivante :

 ![Structure des règles CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Chaque règle peut avoir jusqu’à 4 conditions de correspondance et 3 actions. Il y a un maximum de 5 règles par point de terminaison CDN. En outre, il y a une règle en place par défaut, appelée **Règle globale**. Il s’agit d’une règle sans conditions de correspondance, où les actions définies se déclencheront toujours. Cette règle est incluse dans la limite actuelle de 5 règles.

## <a name="syntax"></a>Syntaxe

La façon dont les caractères spéciaux sont traités varie en fonction de la façon dont une condition de correspondance ou actopm gère les valeurs de texte. Une condition de correspondance ou une fonctionnalité peut interpréter le texte de l’une des manières suivantes :

1. [**Valeurs littérales**](#literal-values)
2. [**Valeurs de caractère générique**](#wildcard-values)


### <a name="literal-values"></a>Valeurs littérales

Le texte interprété comme une valeur littérale traite tous les caractères spéciaux, à l’exception du symbole %, comme une partie de la valeur qui doit être mise en correspondance. En d’autres termes, une condition de correspondance littérale définie sur `\'*'\` n’est remplie que si cette valeur exacte (c’est-à-dire `\'*'\`) est trouvée.

Un symbole de pourcentage est utilisé pour indiquer l’encodage des URL (par exemple, `%20`).

### <a name="wildcard-values"></a>Valeurs de caractère générique

Le texte interprété comme un caractère générique attribue une signification supplémentaire aux caractères spéciaux. Le tableau ci-dessous décrit comment le jeu de caractères suivant est interprété :

Caractère | Description
----------|------------
\ | Une barre oblique inverse est utilisée pour échapper les caractères spécifiés dans ce tableau. Une barre oblique inverse doit être spécifiée juste avant le caractère spécial à échapper.<br/>Par exemple, la syntaxe suivante échappe un astérisque : `\*`
% | Un symbole de pourcentage est utilisé pour indiquer l’encodage des URL (par exemple, `%20`).
\* | Un astérisque est un caractère générique représentant un ou plusieurs caractères.
Espace | Un caractère d’espace indique qu’une condition de correspondance peut être remplie par les valeurs ou les modèles spécifiés.
'valeur' | Un guillemet simple n’a pas de signification particulière. Toutefois, un jeu de guillemets simples est utilisé pour indiquer qu’une valeur doit être traitée comme une valeur littérale. Il peut être utilisé selon les manières suivantes :<br><br/>- Il permet de remplir une condition de correspondance lorsque la valeur spécifiée correspond à une partie de la valeur de comparaison.  Par exemple, `'ma'` peut correspondre à l’une des chaînes suivantes : <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- Il permet de spécifier un caractère spécial en tant que caractère littéral. Par exemple, vous pouvez spécifier un caractère d’espace littéral en plaçant un caractère d’espace dans un jeu de guillemets simples (c’est-à-dire `' '` ou `'sample value'`).<br/>- Il permet de spécifier une valeur vide. Spécifiez une valeur vide en entrant un jeu de guillemets simples (c’est-à-dire '').<br /><br/>**Important :**<br/>- Si la valeur spécifiée ne contient pas de caractère générique, elle est automatiquement considérée comme une valeur littérale, ce qui signifie qu’il n’est pas nécessaire de spécifier un jeu de guillemets simples.<br/>- Si une barre oblique inverse n’échappe pas à un autre caractère dans ce tableau, elle est ignorée si un jeu de guillemets simples est entré.<br/>- Une autre manière de spécifier un caractère spécial en tant que caractère littéral consiste à l’échapper à l’aide d’une barre oblique inverse (c’est-à-dire `\`).

## <a name="next-steps"></a>Étapes suivantes

- [Conditions de correspondance du moteur de règles standard](cdn-standard-rules-engine-match-conditions.md)
- [Actions du moteur de règles standard](cdn-standard-rules-engine-actions.md)
- [Appliquer HTTPS à l’aide du moteur de règles standard](cdn-standard-rules-engine.md)
- [Vue d’ensemble d’Azure CDN](cdn-overview.md)
