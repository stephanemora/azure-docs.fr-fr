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
ms.date: 02/25/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 605f4c639cfc8c0f9732f7347532e1bd7edc055f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341623"
---
#   <a name="language-detection-cognitive-skill"></a>Compétence cognitive Détection de la langue

Le **détection de la langue** compétence détecte la langue du texte d’entrée et signale un code de langue unique pour chaque document soumis dans la demande. Le code de langue est associé à un score indiquant la puissance de l’analyse. Cette compétence utilise les modèles d’apprentissage automatique fournis par [Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) dans Cognitive Services.

Cette fonctionnalité est particulièrement utile lorsqu’il est nécessaire d’indiquer la langue du texte en entrée dans d’autres compétences (par exemple, la [compétence Analyse des sentiments](cognitive-search-skill-sentiment.md) ou la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md)).

Détection de la langue s’appuie sur les bibliothèques de traitement en langage naturel de Bing, ce qui dépasse le nombre de [prise en charge des langues et régions](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) répertoriées pour l’Analytique de texte. La liste exacte des langages n’est pas publiée, mais il inclut tous les langages largement parlée, ainsi que les variantes, dialectes et certains langages régionales et culturelles. Si vous avez du contenu exprimée dans un langage moins fréquemment utilisé, vous pouvez [essayez l’API de détection de langue](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) pour voir si elle retourne un code. La réponse pour les langages qui ne peut pas être détecté est `unknown`.

> [!NOTE]
> Depuis le 21 décembre 2018, vous pouvez [attacher une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md) à un ensemble de compétences Recherche Azure. Cela nous permet de commencer à facturer l’exécution de l’ensemble de compétences. Ce jour-là, nous avons également commencé à facturer l’extraction d’images dans le cadre de notre étape de décodage de documents. L’extraction de texte à partir de documents est toujours offerte sans frais supplémentaires.
>
> L’exécution de [compétences cognitives intégrées](cognitive-search-predefined-skills.md) est facturée au [tarif de paiement à l’utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services), qui est le même que si vous aviez exécuté la tâche directement. L’extraction d’image est une opération facturable de Recherche Azure, actuellement proposée au tarif de la préversion. Pour plus d’informations, consultez la [page des tarifs de Recherche Azure](https://go.microsoft.com/fwlink/?linkid=2042400) ou [Comment la facturation fonctionne](search-sku-tier.md#how-billing-works).

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
