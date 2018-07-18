---
title: Langues et pays pris en charge pour l’API Recherche Web Bing sur Azure | Microsoft Docs
description: Découvrez les langues et pays pris en charge par l’API Recherche Web Bing.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 7b62c4a4feb7144662a8fe4d692f11f1efe5db1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368125"
---
# <a name="bing-web-search-countries-and-languages"></a>Pays et langues de Recherche Web Bing

L’API Recherche Web Bing prend en charge plus d’une trentaine de pays, dont beaucoup d’entre eux ont plus d’une langue. Spécifier un pays avec une requête sert principalement à affiner les résultats de la recherche en fonction des centres d’intérêt dans ce pays. En outre, les résultats peuvent contenir des liens vers Bing, servant à localiser l’expérience utilisateur de Bing en fonction du pays ou de la langue spécifiée.

Vous pouvez spécifier un pays à l’aide du paramètre de requête `cc`. Si vous spécifiez un pays, vous devez également indiquer un ou plusieurs codes de langue à l’aide de l’en-tête HTTP `Accept-Language`. Les langues prises en charge varient selon le pays ; elles sont indiquées pour chaque pays dans le tableau Marchés.

Vous pouvez également indiquer le marché à l’aide du paramètre de requête `mkt` et d’un code issu du tableau **Marchés**. Le fait d’indiquer un marché spécifie simultanément un pays et une langue par défaut. Dans ce cas, le paramètre de requête `setLang` peut être défini sur un code de langue. Généralement, il s’agit de la même langue que celle spécifiée par `mkt`, sauf si l’utilisateur préfère voir Bing dans une autre langue.

## <a name="countries"></a>Pays

|Pays|Code|
|-------|----|
|Argentine|AR|
|Australie|AU|
|Autriche|AT|
|Belgique|BE|
|Brésil|BR|
|Canada|CA|
|Chili|CL|
|Danemark|DK|
|Finlande|FI|
|France|FR|
|Allemagne|DE|
|Hong Kong (R.A.S.)|HK|
|Inde|IN|
|Indonésie|ID|
|Italie|IT|
|Japon|JP|
|Corée du Sud|KR|
|Malaisie|MY|
|Mexique|MX|
|Pays-bas|NL|
|Nouvelle-Zélande|NZ|
|Norvège|NON|
|Chine|CN|
|Pologne|PL|
|Portugal|PT|
|Philippines|PH|
|Russie|RU|
|Arabie Saoudite|SA|
|Afrique du Sud|ZA|
|Espagne|ES|
|Suède|SE|
|Suisse|CH|
|Taïwan|TW|
|Turquie|TR|
|Royaume-Uni|GB|
|États-Unis|FR|


## <a name="markets"></a>Marchés

|Pays|Langage|Code du marché|
|-------|--------|-----------|
|Argentine|Espagnol|es-AR|
|Australie|Français|en-AU|
|Autriche|Allemand|de-AT|
|Belgique|Néerlandais|nl-BE|
|Belgique|Français|fr-BE|
|Brésil|Portugais|pt-br|
|Canada|Français|en-CA|
|Canada|Français|fr-CA|
|Chili|Espagnol|es-CL|
|Danemark|Danois|da-DK|
|Finlande|Finnois|fi-FI|
|France|Français|fr-FR|
|Allemagne|Allemand|de-DE|
|Hong Kong (R.A.S.)|Chinois traditionnel|zh-HK|
|Inde|Français|en-IN|
|Indonésie|Français|en-ID|
|Italie|Italien|it-IT|
|Japon|Japonais|ja-JP|
|Corée du Sud|Coréen|ko-KR|
|Malaisie|Français|en-MY|
|Mexique|Espagnol|es-MX|
|Pays-bas|Néerlandais|nl-NL|
|Nouvelle-Zélande|Français|en-NZ|
|Norvège|Norvégien|no-NO|
|Chine|Chinois|zh-CN|
|Pologne|Polonais|pl-PL|
|Portugal|Portugais|pt-PT|
|Philippines|Français|en-PH|
|Russie|Russe|ru-RU|
|Arabie Saoudite|Arabe|ar-SA|
|Afrique du Sud|Français|en-ZA|
|Espagne|Espagnol|es-ES|
|Suède|Suédois|sv-SE|
|Suisse|Français|fr-CH|
|Suisse|Allemand|de-CH|
|Taïwan|Chinois traditionnel|zh-TW|
|Turquie|Turc|tr-TR|
|Royaume-Uni|Français|en-GB|
|États-Unis|Français|fr-FR|
|États-Unis|Espagnol|es-US|
