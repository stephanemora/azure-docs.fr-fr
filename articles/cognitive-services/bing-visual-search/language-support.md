---
title: Prise en charge linguistique - API Recherche visuelle Bing
titleSuffix: Azure Cognitive Services
description: Liste des langages naturels, des pays et des régions pris en charge par l’API Recherche visuelle Bing. L’API Recherche visuelle Bing prend en charge plus d’une trentaine de pays/région, dont beaucoup possèdent plusieurs langues.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: edfd3ba88da16ff283096af3c5a4929dd06417e2
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435267"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Langages et régions pris en charge par l’API Recherche visuelle Bing

L’API Recherche visuelle Bing prend en charge plus d’une trentaine de pays/région, dont beaucoup possèdent plusieurs langues. Chaque demande doit inclure le pays/la région et la langue choisis par l’utilisateur. Le fait de connaître le marché de l’utilisateur aide Bing à renvoyer les résultats appropriés. Si vous n’indiquez rien, Bing fait au mieux pour déterminer le pays/la région et la langue de l’utilisateur. Étant donné que les résultats peuvent contenir des liens vers Bing, le fait de connaître le pays/la région et la langue aide à créer une meilleure expérience pour l’utilisateur Bing s’il clique sur des liens Bing.

Pour spécifier le pays/la région et la langue, utilisez pour le paramètre de requête `mkt` (marché) l’un des codes du tableau **Marchés** ci-dessous. Le marché spécifie à la fois un pays/une région et une langue. Si l’utilisateur souhaite afficher du texte dans une langue différente, utilisez pour le paramètre de requête `setLang` le code langue approprié.

De même, vous pouvez spécifier le pays/la région à l’aide du paramètre de requête `cc`. Si vous spécifiez un pays/une région, vous devez également spécifier un ou plusieurs codes de langue à l’aide de l’en-tête HTTP `Accept-Language`. Les langues prises en charge varient selon le pays/la région ; elles sont indiquées pour chaque pays dans le tableau Marchés.



> [!NOTE]
> Les restrictions de marché suivantes s’appliquent :
>
> - Les annotations de reconnaissance d’image sont disponibles en anglais uniquement.
> - Les recettes, achats et statistiques de pages sont disponibles sur le marché américain (en-US) uniquement.


## <a name="countries"></a>Pays

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
