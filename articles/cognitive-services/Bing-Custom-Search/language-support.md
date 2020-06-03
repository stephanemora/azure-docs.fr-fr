---
title: Prise en charge linguistique - API Recherche personnalisée Bing
titleSuffix: Azure Cognitive Services
description: Liste des langues et régions prises en charge par l’API Recherche personnalisée Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 004bd973651d5903db4254a8883be2c8a83d9b38
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310578"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Langues et régions prises en charge par l’API Recherche personnalisée Bing

L’API Recherche personnalisée Bing prend en charge plus d’une trentaine de pays/régions, dont beaucoup possèdent plusieurs langues.

La requête peut indiquer le paramètre [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) (facultatif), qui vous permet de choisir le marché dont proviennent les résultats. Pour obtenir la liste complète des paramètres de requête facultatifs, consultez [Query Parameters](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) (Paramètres de requête).

Vous pouvez spécifier un pays/une région à l’aide du paramètre de requête `cc`. Si vous spécifiez un pays/une région, vous devez également spécifier un ou plusieurs codes de langue à l’aide de l’en-tête `Accept-Language`. Les langues prises en charge varient selon le pays/la région ; elles sont indiquées pour chaque pays/région dans le tableau **Marchés**.

Étant donné que l’en-tête `Accept-Language` et le paramètre de requête `setLang` s’excluent mutuellement, ne spécifiez pas les deux. Pour plus d’informations, consultez [Accept-Language](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

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
|Indonésie|id|
|Italie|IT|
|Japon|JP|
|Corée du Sud|KR|
|Malaisie|MY|
|Mexique|MX|
|Pays-Bas|NL|
|Nouvelle-Zélande|NZ|
|Norvège|Non|
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
|Royaume-Uni|Go|
|États-Unis|US|


## <a name="markets"></a>Marchés

|Pays/région|Langage|Code du marché|
|-------|--------|-----------|
|Argentine|Espagnol|es-AR|
|Australie|Anglais|en-AU|
|Autriche|Allemand|de-AT|
|Belgique|Néerlandais|nl-BE|
|Belgique|Français|fr-BE|
|Brésil|Portugais|pt-br|
|Canada|Anglais|en-CA|
|Canada|Français|fr-CA|
|Chili|Espagnol|es-CL|
|Danemark|Danois|da-DK|
|Finlande|Finnois|fi-FI|
|France|Français|fr-FR|
|Allemagne|Allemand|de-DE|
|Hong Kong (R.A.S.)|Chinois traditionnel|zh-HK|
|Inde|Anglais|en-IN|
|Indonésie|Anglais|en-ID|
|Italie|Italien|it-IT|
|Japon|Japonais|ja-JP|
|Corée du Sud|Coréen|ko-KR|
|Malaisie|Anglais|en-MY|
|Mexique|Espagnol|es-MX|
|Pays-Bas|Néerlandais|nl-NL|
|Nouvelle-Zélande|Anglais|en-NZ|
|Norvège|Norvégien|no-NO|
|Chine|Chinois|zh-CN|
|Pologne|Polonais|pl-PL|
|Portugal|Portugais|pt-PT|
|Philippines|Anglais|en-PH|
|Russie|Russe|ru-RU|
|Arabie Saoudite|Arabe|ar-SA|
|Afrique du Sud|Anglais|en-ZA|
|Espagne|Espagnol|es-ES|
|Suède|Suédois|sv-SE|
|Suisse|Français|fr-CH|
|Suisse|Allemand|de-CH|
|Taïwan|Chinois traditionnel|zh-TW|
|Turquie|Turc|tr-TR|
|Royaume-Uni|Anglais|en-GB|
|États-Unis|Anglais|fr-FR|
|États-Unis|Espagnol|es-US|
