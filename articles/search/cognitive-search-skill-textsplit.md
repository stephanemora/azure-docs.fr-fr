---
title: Compétence Fractionnement de texte de la recherche cognitive - Recherche Azure
description: Découpe le texte en segments ou des pages de texte en fonction de leur longueur dans un pipeline d’enrichissement Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 78ed2fab81bfb1562125135c5901a2de395c3843
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023946"
---
#   <a name="text-split-cognitive-skill"></a>Compétence cognitive Fractionnement de texte

La compétence **Fractionnement de texte** découpe le texte en segments. Vous pouvez choisir de décomposer le texte en phrases ou en pages d’une longueur donnée. Cette opération est particulièrement utile si d’autres compétences en aval imposent des critères de longueur maximale du texte. 

> [!NOTE]
> Cette compétence n’est pas liée à une API Cognitive Services et son utilisation ne vous est pas facturée. Toutefois, vous devez toujours [attacher une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md) pour remplacer l’option de ressource **Gratuit** qui vous limite à un petit nombre d’enrichissements quotidiens par jour.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse.

| Nom du paramètre     | Description |
|--------------------|-------------|
| textSplitMode      | « pages » ou « sentences » (phrases) | 
| maximumPageLength | Si textSplitMode est défini sur « pages », il s’agit de la longueur maximale de la page selon `String.Length`. La valeur minimale est 100.  Si textSplitMode est réglé sur « pages », l’algorithme essaie de fractionner le texte en blocs d’une taille maximale de « maximumPageLength ». Dans ce cas, l’algorithme fera de son mieux pour arrêter la phrase sur une limite de phrase, de sorte que la taille du bloc peut être légèrement inférieure à « maximumPageLength ». | 
| defaultLanguageCode   | (Facultatif) L’un des codes de langue suivants : `da, de, en, es, fi, fr, it, ko, pt`. La langue par défaut est l’anglais (en). Quelques points à prendre en compte :<ul><li>Si vous utilisez un format codelangue-codepays, seule la partie codelangue du format est utilisée.</li><li>Si la langue ne figure pas dans la liste précédente, la compétence Fractionnement découpe le texte suivant les limites de caractères.</li><li>Il est utile d’indiquer un code de langue pour éviter de couper un mot en deux dans les langues sans espaces, par exemple, le chinois, le japonais et le coréen.</li></ul>  |


## <a name="skill-inputs"></a>Entrées de la compétence

| Nom du paramètre       | Description      |
|----------------------|------------------|
| text  | Texte à fractionner en sous-chaînes. |
| languageCode  | (Facultatif) Code de langue du document.  |

## <a name="skill-outputs"></a>Sorties de la compétence 

| Nom du paramètre     | Description |
|--------------------|-------------|
| textItems | Tableau des sous-chaînes extraites. |


##  <a name="sample-definition"></a>Exemple de définition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>Exemple d’entrée

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Exemple de sortie

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Cas d’erreur
Si la langue n’est pas prise en charge, un avertissement est généré et le texte est fractionné suivant les limites de caractères.

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
