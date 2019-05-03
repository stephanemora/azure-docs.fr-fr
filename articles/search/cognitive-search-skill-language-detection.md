---
title: Compétence Détection de la langue de la recherche cognitive - Recherche Azure
description: Évalue le texte non structuré et, pour chaque enregistrement, retourne un identificateur de langue avec un score indiquant la puissance de l’analyse dans un pipeline d’enrichissement Recherche Azure.
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
ms.openlocfilehash: a076eee9818f294a8e5c4b10cebbcb9e5a55d80c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021845"
---
#   <a name="language-detection-cognitive-skill"></a>Compétence cognitive Détection de la langue

Le **détection de la langue** compétence détecte la langue du texte d’entrée et signale un code de langue unique pour chaque document soumis dans la demande. Le code de langue est associé à un score indiquant la puissance de l’analyse. Cette compétence utilise les modèles d’apprentissage automatique fournis par [Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) dans Cognitive Services.

Cette fonctionnalité est particulièrement utile lorsqu’il est nécessaire d’indiquer la langue du texte en entrée dans d’autres compétences (par exemple, la [compétence Analyse des sentiments](cognitive-search-skill-sentiment.md) ou la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md)).

Détection de la langue s’appuie sur les bibliothèques de traitement en langage naturel de Bing, ce qui dépasse le nombre de [prise en charge des langues et régions](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) répertoriées pour l’Analytique de texte. La liste exacte des langages n’est pas publiée, mais il inclut tous les langages largement parlée, ainsi que les variantes, dialectes et certains langages régionales et culturelles. Si vous avez du contenu exprimée dans un langage moins fréquemment utilisé, vous pouvez [essayez l’API de détection de langue](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) pour voir si elle retourne un code. La réponse pour les langages qui ne peut pas être détecté est `unknown`.

> [!NOTE]
> Comme vous développez étendue en augmentant la fréquence de traitement, l’ajout de plusieurs documents, ou ajoutez plusieurs algorithmes d’intelligence artificielle, vous devrez [attacher une ressource Cognitive Services facturable](cognitive-search-attach-cognitive-services.md). Des frais sont applicables durant l’appel des API dans Cognitive Services ainsi que pour l’extraction d’images durant la phase d’extraction du contenu des documents du service Recherche Azure. L’extraction de texte à partir des documents est gratuite.
>
> L’exécution de compétences intégrées est facturée existant [Cognitive Services paie-sous-vous accédez prix](https://azure.microsoft.com/pricing/details/cognitive-services/). Image de tarification d’extraction est décrit sur le [page de tarification de Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limites de données
La taille maximale d’un enregistrement est de 50 000 caractères selon `String.Length`. Si vous avez besoin de découper vos données avant de les envoyer à l’Analyseur des sentiments, vous pouvez utiliser la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Entrées de la compétence

Les paramètres respectent la casse.

| Entrées     | Description |
|--------------------|-------------|
| text | Texte à analyser.|

## <a name="skill-outputs"></a>Sorties de la compétence

| Nom de sortie    | Description |
|--------------------|-------------|
| languageCode | Code ISO 6391 de la langue identifiée. Exemple : « en ». |
| languageName | Nom de la langue. Exemple : « anglais ». |
| de votre application | Valeur comprise entre 0 et 1 correspondant à la probabilité que la langue soit correctement identifiée. Le score peut être inférieur à 1 si la phrase comporte plusieurs langues.  |

##  <a name="sample-definition"></a>Exemple de définition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>Exemple de sortie

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Cas d’erreur
Si le texte est exprimé dans une langue non prise en charge, une erreur est générée et aucun identificateur de langue n’est retourné.

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
