---
title: Compétences conditionnel recherche cognitive (recherche Azure) | Microsoft Docs
description: Compétences conditionnel qui permet le filtrage, la création de valeurs par défaut et la fusion de valeurs.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028424"
---
#   <a name="conditional-skill"></a>Compétences conditionnel

Le **compétence conditionnel** permet une variété de scénarios qui requièrent une opération booléenne pour déterminer les données qui doivent être assignées à une sortie. Ces scénarios incluent : filtrage, affectez une valeur par défaut et fusion de données basé sur une condition.

Le pseudo-code suivant explique ce que réalise la compétence conditionnel :

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Cette compétence n’est pas liée à une API Cognitive Services et son utilisation ne vous est pas facturée. Toutefois, vous devez toujours [attacher une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md) pour remplacer l’option de ressource **Gratuit** qui vous limite à un petit nombre d’enrichissements quotidiens par jour.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Champs évaluées

Cette opération est spéciale, car ses entrées sont des champs évaluées.

Les valeurs valides d’une expression sont les suivantes :

-   Chemins d’accès de l’annotation (chemins d’accès dans les expressions doivent être délimitées par « $("and") ») <br/>
    Exemples :
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Littéraux (chaînes, des chiffres, la valeur true, false, null) <br/>
    Exemples :
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
       "= null"                 // null value
    ```

-  Expressions qui utilisent un opérateur de comparaison (==, ! =, > =, >, < =, <) <br/>
    Exemples :
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Expressions qui utilisent des opérateurs booléens (& &, ||, !, ^) <br/>
    Exemples :
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Expressions qui utilisent un opérateur numérique (+, -, \*, /, %) <br/>
    Exemples : 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

En raison de l’évaluation est pris en charge, la qualification conditionnel peut être utilisée pour les scénarios de transformation mineures. Voir exemple [définition des compétences 4](#transformation-examples) pour obtenir un exemple.

## <a name="skill-inputs"></a>Entrées de la compétence
Les entrées respectent la casse.

| Entrées      | Description |
|-------------|-------------|
| condition   | Cette entrée est un [évaluée champ](#evaluated-fields) qui représente la condition à évaluer. Cette condition doit correspondre à une valeur booléenne (true ou false).   <br/>  Exemples : <br/> « = true » <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Cette entrée est un [évaluée champ](#evaluated-fields). La valeur à retourner si la condition est évaluée à true. Les chaînes constantes doivent être retournées dans ' ' guillemets. <br/>Exemples de valeurs : <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> « = $(/documents/entités/\*) » <br/> |
| whenFalse   | Cette entrée est un [évaluée champ](#evaluated-fields). La valeur à retourner si la condition est évaluée à false.  <br/>Exemples de valeurs : <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> « = $(/documents/entités/\*) » <br/>

## <a name="skill-outputs"></a>Sorties de la compétence
Il existe une seule sortie appelée « sortie ». Elle retournera la valeur de whenFalse si la condition est false, ou whenTrue si la condition est vraie.

## <a name="examples"></a>Exemples

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>Exemple de définition de compétence 1 : Filtrage des documents afin d’afficher uniquement les documents « Français »

La sortie suivante renvoie un tableau de phrases (« / documents/frenchSentences ») si la langue du document est le français. Si la langue n’est pas français, cette valeur est définie sur null.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Si « / documents/frenchSentences » est utilisé comme le *contexte* d’une autre compétence, cette compétence s’exécutera uniquement si « / documents/frenchSentences » n’est pas définie sur null


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>Exemple de définition de compétence 2 : Définition d’une valeur par défaut lorsqu’il n’existe pas.

La sortie suivante créera une annotation (« / documents/languageWithDefault ») qui est définie sur la langue du document, ou « es » si la langue n’est pas définie.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>Exemple de définition de compétence 3 : Fusion de valeurs à partir de deux champs différents dans un seul champ

Dans cet exemple, des phrases ont un *frenchSentiment* propriété. Chaque fois que le *frenchSentiment* propriété est null, nous souhaitons utiliser la *englishSentiment* valeur. Nous attribuons la sortie à un membre appelé simplement *sentiment* (« / documenter/sentiment / * / sentiment »).

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-examples"></a>Exemples de transformation
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>Exemple de définition de compétence 4 : Effectuer des transformations de données sur un seul champ

Dans cet exemple, nous recevons un sentiment comprise entre 0 et 1, et nous aimerions transformer afin qu’elle soit comprise entre -1 et 1. Il s’agit d’une transformation mathématique petit que nous pourrions faire à l’aide de la compétence conditionnel.

Dans cet exemple, nous utilisons jamais l’aspect conditionnelle de la compétence que la condition est toujours true. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```


## <a name="special-considerations"></a>Considérations spéciales
Notez que certains paramètres sont évaluées, donc vous devez être particulièrement prudent suivant le modèle documenté. Expressions doivent commencer par un equals signe « = » et de chemins d’accès doivent être délimité par des « $("and") ». Veillez à placer vos chaînes de guillemets' ' comme afin d’aider à l’évaluateur de faire la distinction entre des chaînes et des chemins d’accès réels et des opérateurs. En outre, assurez-vous d’ajouter un espace blanc autour des opérateurs (par exemple un * dans un chemin d’accès a une signification différente que l’opérateur de multiplication).


## <a name="next-steps"></a>Étapes suivantes

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
