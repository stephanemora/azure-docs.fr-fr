---
title: Compétence cognitive Reconnaissance d’entités (V3)
titleSuffix: Azure Cognitive Search
description: Extrayez différents types d’entités d’un texte à l’aide de l’Analyse de texte V3 dans un pipeline d’enrichissement dans la Recherche cognitive Azure.
manager: jennmar
author: ayokande
ms.author: aakande
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/19/2021
ms.openlocfilehash: 9edb5a194f79d7b3fff1caf1672a656a85bd4766
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111528742"
---
#    <a name="entity-recognition-cognitive-skill-v3"></a>Compétence cognitive Reconnaissance d’entités (V3)

> [!IMPORTANT] 
> Cette compétence est actuellement en préversion publique. Les fonctionnalités en préversion sont fournies sans contrat de niveau de service et ne sont pas recommandées pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Il n’y a actuellement pas de prise en charge du portail ou du SDK .NET.

La compétence **Reconnaissance d’entités** extrait les entités de différents types du texte. Ces entités sont classées dans 14 catégories distinctes, allant des personnes et des organisations aux URL et numéros de téléphone. Cette compétence utilise les modèles Machine Learning fournis par [Analyse de texte](../cognitive-services/text-analytics/overview.md) dans Cognitive Services.

> [!NOTE]
> Si vous élargissez le champ en augmentant la fréquence des traitements, en ajoutant des documents supplémentaires ou en ajoutant plusieurs algorithmes d’IA, vous devez [attacher une ressource Cognitive Services facturable](cognitive-search-attach-cognitive-services.md). Des frais s’appliquent durant l’appel des API dans Cognitive Services ainsi que pour l’extraction d’images dans le cadre de la phase de craquage de document de la Recherche cognitive Azure. L’extraction de texte à partir des documents est gratuite.
>
> L'exécution des compétences intégrées est facturée au prix actuel du [paiement à l'utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Les prix appliqués pour l’extraction d’images sont présentés sur la [page de tarification du service Recherche cognitive Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.V3.EntityRecognitionSkill

## <a name="data-limits"></a>Limites de données
La taille maximale d’un enregistrement doit être de 50 000 caractères telle que mesurée par [`String.Length`](/dotnet/api/system.string.length). Si vous devez subdiviser vos données avant de les envoyer à la compétence EntityRecognition, utilisez la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Paramètres de la compétence

Les paramètres respectent la casse et sont tous facultatifs.

| Nom du paramètre     | Description |
|--------------------|-------------|
| `categories`    | Tableau des catégories à extraire.  Types de catégorie possibles : `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"DateTime"`, `"URL"`, `"Email"`, `"PersonType"`, `"Event"`, `"Product"`, `"Skill"`, `"Address"`, `"Phone Number"`, `"IP Address"`. Si aucune catégorie n’est précisée, tous les types sont retournés.|
| `defaultLanguageCode` |    Code de langue du texte d’entrée. Si le code de langue par défaut n’est pas spécifié, l’anglais (en) est utilisé comme code de langue par défaut. <br/> Voir la [Liste complète des langues prises en charge](../cognitive-services/text-analytics/language-support.md). Les catégories d’entités ne sont pas toutes prises en charge pour toutes les langues. Consultez la remarque ci-dessous.|
| `minimumPrecision` | Valeur comprise entre 0 et 1 Si le score de confiance (dans la sortie `namedEntities`) est inférieur à cette valeur, l’entité n’est pas retournée. La valeur par défaut est 0. |
| `modelVersion` | (Facultatif) Version du modèle à utiliser lors de l’appel du service Analyse de texte. Si rien n’est spécifié, la dernière version disponible est utilisée par défaut. Nous vous recommandons de ne pas spécifier cette valeur, sauf nécessité absolue. Pour plus d’informations, consultez [Contrôle de version de modèle dans l’API Analyse de texte](../cognitive-services/text-analytics/concepts/model-versioning.md).|


## <a name="skill-inputs"></a>Entrées de la compétence

| Nom d’entrée      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | Chaîne indiquant la langue des enregistrements. Si ce paramètre n’est pas spécifié, le code de langue par défaut est utilisé pour l’analyse des enregistrements. <br/>Voir la [Liste complète des langues prises en charge](../cognitive-services/text-analytics/language-support.md). |
| `text`          | Texte à analyser.          |

## <a name="skill-outputs"></a>Sorties de la compétence

> [!NOTE]
>toutes les catégories d’entités ne sont pas prises en charge pour toutes les langues. Pour connaître les catégories prises en charge pour la langue souhaitée, reportez-vous aux [Catégories d’entités prises en charge dans le API Analyse de texte v3](../cognitive-services/text-analytics/named-entity-types.md).

| Nom de sortie      | Description                   |
|---------------|-------------------------------|
| `persons`       | Tableau de chaînes représentant chacune le nom d’une personne. |
| `locations`  | Tableau de chaînes représentant chacune un lieu. |
| `organizations`  | Tableau de chaînes représentant chacune une organisation. |
| `quantities`  | Tableau de chaînes représentant chacune une quantité. |
| `dateTimes`  | Tableau de chaînes représentant chacune une valeur DateTime (telle qu’elle apparaît dans le texte). |
| `urls` | Tableau de chaînes représentant chacune une URL. |
| `emails` | Tableau de chaînes représentant chacune un e-mail. |
| `personTypes` | Tableau de chaînes représentant chacune un élément PersonType. |
| `events` | Tableau de chaînes représentant chacune un événement. |
| `products` | Tableau de chaînes représentant chacune un produit. |
| `skills` | Tableau de chaînes représentant chacune une compétence. |
| `addresses` | Tableau de chaînes représentant chacune une adresse. |
| `phoneNumbers` | Tableau de chaînes représentant chacune un numéro de téléphone. |
| `ipAddresses` | Tableau de chaînes représentant chacune une adresse. |
| `namedEntities` | Tableau de types complexes contenant les champs suivants : <ul><li>catégorie</li> <li>subcategory</li> <li>confianceScore (une valeur plus élevée signifie qu’il s’agit d’une entité réelle)</li> <li>length (longueur (nombre de caractères) de cette entité)</li> <li>le décalage (l’emplacement où elle a été trouvée dans le texte) ;</li> <li>text (nom réel de l’entité tel qu’il apparaît dans le texte)</li></ul> |


##    <a name="sample-definition"></a>Exemple de définition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "context": "/document",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en", 
    "minimumPrecision": 0.5, 
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
            "name": "persons", 
            "targetName": "people"
        },
        {
            "name": "emails", 
            "targetName": "emails"
        },
        {
            "name": "namedEntities", 
            "targetName": "namedEntities"
        }
    ]
  }
```
##    <a name="sample-input"></a>Exemple d’entrée

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso Corporation was founded by Jean Martin. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Exemple de sortie

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "people": [ "Jean Martin"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category": "Person",
            "subcategory": null,
            "length": 11,
            "offset": 35,
            "confidenceScore": 0.98,
            "text": "Jean Martin"
          },
          {
            "category": "Email",
            "subcategory": null,
            "length": 19,
            "offset": 71,
            "confidenceScore": 0.8,
            "text": "contact@contoso.com"
          }
        ],
      }
    }
  ]
}
```

Notez que les décalages renvoyés pour les entités dans la sortie de cette qualification le sont directement à partir de l'[API Analyse de texte](../cognitive-services/text-analytics/overview.md), ce qui signifie que si vous les utilisez pour indexer dans la chaîne d'origine, vous devez utiliser la classe [StringInfo](/dotnet/api/system.globalization.stringinfo) dans .NET afin d'extraire le bon contenu.  [Pour plus d’informations, cliquez ici.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>Cas d’avertissement
Si le code de langue du document n’est pas pris en charge, un avertissement est retourné et aucune entité n’est extraite.

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)