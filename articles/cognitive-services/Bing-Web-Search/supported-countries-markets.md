---
title: Pays, régions et langues pris en charge par l’API Recherche Web Bing
description: Découvrez les langues et pays/régions pris en charge par l’API Recherche Web Bing.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/16/2018
ms.author: erhopf
ms.openlocfilehash: 87384a8aeabfe0c4a178344235debcc586cdf3de
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888469"
---
# <a name="countries-regions-and-languages-supported-by-the-bing-web-search-api"></a>Pays, régions et langues pris en charge par l’API Recherche Web Bing

L’API Recherche Web Bing prend en charge plus d’une trentaine de pays ou régions, dont beaucoup possèdent plusieurs langues. En utilisant une requête pour spécifier un pays ou une région, vous pouvez affiner les résultats de la recherche en fonction des centres d’intérêt dans ce pays ou cette région. Il se peut que les résultats incluent des liens vers Bing qui permettent de localiser l’expérience utilisateur Bing en fonction du pays, de la région ou de la langue spécifié.

Vous pouvez spécifier un pays ou une région à l’aide du paramètre de requête `cc`. Lorsqu’un pays ou une région est spécifié, vous devez indiquer un ou plusieurs codes de langue avec l’[`Accept-Language`en-tête](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers). Utilisez le [tableau des marchés](#Markets) pour consulter la liste des langues prises en charge pour chaque marché.

Vous pouvez également indiquer le marché à l’aide du paramètre de requête `mkt` et d’un code issu du tableau **Marchés**. En indiquant un marché, vous renseignez simultanément un pays/une région et une langue prédéfinie. Vous pouvez définir clairement la langue avec le paramètre de requête `setLang`.

## <a name="countriesregions"></a>Pays/régions

|Pays/région|Code|
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

|Pays/région|Langage|Code du marché|
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
