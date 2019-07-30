---
title: Référence du moteur de règles Azure CDN | Microsoft Docs
description: Documentation de référence sur les fonctionnalités et conditions de correspondance du moteur de règles Azure CDN.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593162"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Documentation de référence sur le moteur de règles Azure CDN de Verizon Premium

Cet article fournit les descriptions détaillées des conditions de correspondance et fonctionnalités disponibles pour le [moteur de règles](cdn-verizon-premium-rules-engine.md) Azure Content Delivery Network (CDN).

Le moteur de règles est conçu pour être l’autorité finale sur la façon dont certains types de demandes sont traités par le CDN.

**Utilisations courantes** :

- Remplacer ou définir une stratégie de cache personnalisée.
- Sécuriser ou refuser les demandes de contenu sensible.
- Rediriger les demandes.
- Stocker les données de journal personnalisé.

## <a name="terminology"></a>Terminologie

Une règle est définie à l’aide d’[**expressions conditionnelles**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), de [**conditions de correspondance**](cdn-verizon-premium-rules-engine-reference-match-conditions.md) et de [**fonctionnalités**](cdn-verizon-premium-rules-engine-reference-features.md). Ces éléments sont mis en surbrillance dans l’illustration suivante :

 ![Condition de correspondance CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntaxe

La façon dont les caractères spéciaux sont traités varie en fonction de la façon dont une condition de correspondance ou une fonctionnalité gère les valeurs de texte. Une condition de correspondance ou une fonctionnalité peut interpréter le texte de l’une des manières suivantes :

1. [**Valeurs littérales**](#literal-values)
2. [**Valeurs de caractère générique**](#wildcard-values)
3. [**Expressions régulières**](#regular-expressions)

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

### <a name="regular-expressions"></a>Expressions régulières

Les expressions régulières définissent un modèle qui est recherché dans une valeur de texte. La notation d’une expression régulière définit des significations spécifiques pour une variété de symboles. Le tableau ci-dessous indique comment les caractères spéciaux sont traités par les conditions de correspondance et les fonctionnalités prenant en charge les expressions régulières.

Caractère spécial | Description
------------------|------------
\ | Une barre oblique inverse échappe le caractère qui la suit, ce caractère est alors traité comme une valeur littérale plutôt que d’utiliser sa signification d’expression régulière. Par exemple, la syntaxe suivante échappe un astérisque : `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | An asterisk allows the preceding character to be matched zero or more times.
Space | A space character is typically treated as a literal character.
'value' | Single quotes are treated as literal characters. A set of single quotes does not have special meaning.

## Next steps

- [Rules engine match conditions](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)
- [Azure CDN overview](cdn-overview.mdLa signification d’un symbole de pourcentage dépend de son utilisation. Docs
descr`%{HTTPVariable}`: cette syntaxe identifie une variable HTTP.match`%{HTTPVariable%Pattern}`: cette syntaxe utilise un symbole de pourcentage pour identifier une variable HTTP et comme délimiteur.1/2019`\``: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\`20`).f the available match conditions and features for the Azure Content Delivery Network (CDN) [rules engine](cdn-verizon-premium-rules-engine.md).

The rules engine is designed to be the final authority on how specific types of requests are processed by the CDN.

**Common uses**:

- Override or define a custom cache policy.
- Secure or deny requests for sensitive content.
- Redirect requests.
- Store custom log data.

## Terminology

A rule is defined through the use of [**conditional expressions**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**match conditions**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), and [**features**](cdn-verizon-premium-rules-engine-reference-features.md). These elements are highlighted in the following illustration:

 ![CDN match condition](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## Syntax

The manner in which special characters are treated varies according to how a match condition or feature handles text values. A match condition or feature may interpret text in one of the following ways:

1. [**Literal values**](#literal-values)
2. [**Wildcard values**](#wildcard-values)
3. [**Regular expressions**](#regular-expressions)

### Literal values

Text that is interpreted as a literal value treats all special characters, with the exception of the % symbol, as a part of the value that must be matched. In other words, a literal match condition set to `\'*'\` is only satisfied when that exact value (that is, `\'*'\`) is found.

A percentage symbol is used to indicate URL encoding (for example, `%20`).

### Wildcard values

Text that is interpreted as a wildcard value assigns additional meaning to special characters. The following table describes how the following set of characters is interpreted:

Character | Description
----------|------------
\ | A backslash is used to escape any of the characters specified in this table. A backslash must be specified directly before the special character that should be escaped.<br/>For example, the following syntax escapes an asterisk: `\*`
% | A percentage symbol is used to indicate URL encoding (for example, `%20`).
\* | An asterisk is a wildcard that represents one or more characters.
Space | A space character indicates that a match condition may be satisfied by either of the specified values or patterns.
'value' | A single quote does not have special meaning. However, a set of single quotes is used to indicate that a value should be treated as a literal value. It can be used in the following ways:<br><br/>- It allows a match condition to be satisfied whenever the specified value matches any portion of the comparison value.  For example, `'ma'` would match any of the following strings: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- It allows a special character to be specified as a literal character. For example, you may specify a literal space character by enclosing a space character within a set of single quotes (that is, `' '` or `'sample value'`).<br/>- It allows a blank value to be specified. Specify a blank value by specifying a set of single quotes (that is, '').<br /><br/>**Important:**<br/>- If the specified value does not contain a wildcard, then it is automatically considered a literal value, which means that it is not necessary to specify a set of single quotes.<br/>- If a backslash does not escape another character in this table, it is ignored when it is specified within a set of single quotes.<br/>- Another way to specify a special character as a literal character is to escape it using a backslash (that is, `\`).

### Regular expressions

Regular expressions define a pattern that is searched for within a text value. Regular expression notation defines specific meanings to a variety of symbols. The following table indicates how special characters are treated by match conditions and features that support regular expressions.

Special Character | Description
------------------|------------
\ | A backslash escapes the character the follows it, which causes that character to be treated as a literal value instead of taking on its regular expression meaning. For example, the following syntax escapes an asterisk: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | Un astérisque permet de mettre en correspondance une ou plusieurs fois le caractère qui le précède.
Espace | Un caractère d’espace est généralement traité comme un caractère littéral.
'valeur' | Les guillemets simples sont traités comme des caractères littéraux. Un jeu de guillemets simples n’a pas de signification particulière.

## <a name="next-steps"></a>Étapes suivantes

- [Conditions de correspondance du moteur de règles](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expressions conditionnelles du moteur de règles](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Fonctionnalités du moteur de règles](cdn-verizon-premium-rules-engine-reference-features.md)
- [Remplacement du comportement HTTP à l’aide du moteur de règles](cdn-verizon-premium-rules-engine.md)
- [Vue d’ensemble d’Azure CDN](cdn-overview.md)