---
title: Retour des traductions N-Best - API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Renvoyez les n meilleures traductions avec l’API de traduction de texte Microsoft Translator Text.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a0f4e4dd5cea9c3ea7b682e6372d2ffa8726e6ef
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646471"
---
# <a name="how-to-return-n-best-translations"></a>Comment renvoyer les n meilleures traductions

> [!NOTE]
> Cette méthode est déconseillée. Elle n’est pas disponible dans la version 3.0 de l’API de traduction de texte Translator Text.

Les méthodes GetTranslations() et GetTranslationsArray() de l’API Microsoft Translator incluent un indicateur booléen facultatif « IncludeMultipleMTAlternatives ».
La méthode renvoie le nombre d’alternatives maxTranslations maximal où le delta est fourni par la liste des n meilleures traductions du moteur de traduction.

La signature est :

**Syntaxe**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Paramètres**

| Paramètre | Description |
|:---|:---|
| appId | **Requis** Si l’en-tête Autorisation est utilisé, laissez le champ appid vide. Sinon, spécifiez une chaîne contenant « Beare » + « » + un jeton d’accès.|
| texte | **Requis** Chaîne représentant le texte à traduire. La taille du texte ne peut pas dépasser 10 000 caractères.|
| from | **Requis** Chaîne représentant le code de langue du texte à traduire. |
| to | **Requis** Chaîne représentant le code de langue dans laquelle le texte doit être traduit. |
| maxTranslations | **Requis** Un entier représentant le nombre maximal de traductions à renvoyer. |
| options | **Facultatif** Un objet TranslateOptions qui contient les valeurs répertoriées ci-dessous. Tous sont facultatifs. Par défaut, il s’agit des paramètres les plus courants.

* Catégorie : la seule option prise en charge (et donc par défaut) est « général ».
* ContentType : la seule option prise en charge (et donc par défaut) est 	« texte/brut ».
* État : état utilisateur pour aider à mettre en corrélation la requête et la réponse. Le même contenu sera renvoyé dans la réponse.
* IncludeMultipleMTAlternatives : indicateur pour déterminer s’il faut renvoyer plusieurs alternatives du moteur de traduction machine. Par défaut, la valeur est false et n’offre qu’une seule alternative.

## <a name="ratings"></a>Évaluations
Les notes sont appliquées comme suit : la meilleure traduction automatique obtient une note de 5.
Les alternatives de traduction générées automatiquement (les n meilleures traductions) ont la note de 0, et leur degré de correspondance est de 100.

## <a name="number-of-alternatives"></a>Nombre d’alternatives
Le nombre d’alternatives renvoyées peut atteindre la valeur maxTranslations, mais peut être inférieur.

## <a name="language-pairs"></a>Paires de langues
Cette fonctionnalité n’est pas disponible pour les traductions entre le chinois simplifié et le chinois traditionnel, dans les deux sens. Elle est disponible pour toutes les autres paires de langues que Microsoft Translator prend en charge.
