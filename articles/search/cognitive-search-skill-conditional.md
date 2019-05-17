---
title: Compétences conditionnel recherche cognitive (recherche Azure) | Microsoft Docs
description: La compétence conditionnelle permet le filtrage, la création de valeurs par défaut et la fusion de valeurs.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791515"
---
#   <a name="conditional-skill"></a>Compétences conditionnel

Le *compétence conditionnel* permet des scénarios de recherche Azure qui nécessitent une opération booléenne pour déterminer les données à affecter à une sortie. Ces scénarios incluent le filtrage, affectez une valeur par défaut et fusion de données selon une condition.

Le pseudo-code suivant illustre ce que réalise la compétence conditionnelle :

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Cette compétence n’est pas liée à une API Azure Cognitive Services, et vous n’êtes pas facturé pour son utilisation. Toutefois, vous devez quand même [attacher une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md) pour remplacer l’option de ressource « Gratuit » qui vous limite à un petit nombre d’enrichissement par jour.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Champs évaluées

Cette opération est spéciale, car ses entrées sont des champs évaluées.

Les éléments suivants sont des valeurs valides d’une expression :

-   Chemins d’accès de l’annotation (chemins d’accès dans les expressions doivent être délimitées par « $("and") »)
 <br/>
    Exemples :
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Littéraux (chaînes, des chiffres, la valeur true, false, null) <br/>
    Exemples :
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Expressions qui utilisent des opérateurs de comparaison (==, ! =, > =, >, < =, <) <br/>
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

-   Expressions qui utilisent des opérateurs numériques (+, -, \*, /, %) <br/>
    Exemples : 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Étant donné que la compétence conditionnelle prend en charge la version d’évaluation, vous pouvez l’utiliser dans les scénarios de transformation de mineur. Par exemple, consultez [définition des compétences 4](#transformation-example).

## <a name="skill-inputs"></a>Entrées de la compétence
Les entrées respectent la casse.

| Entrée   | Description  |
|-------------|-------------|
| condition   | Cette entrée est un [évaluée champ](#evaluated-fields) qui représente la condition à évaluer. Cette condition doit correspondre à une valeur booléenne (*true* ou *false*).   <br/>  Exemples : <br/> « = true » <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Cette entrée est un [évaluée champ](#evaluated-fields) qui représente la valeur à retourner si la condition est évaluée sur *true*. Les chaînes constantes doivent être retournées dans les guillemets simples ('et'). <br/>Exemples de valeurs : <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> « = $(/documents/entités/\*) » <br/> |
| whenFalse   | Cette entrée est un [évaluée champ](#evaluated-fields) qui représente la valeur à retourner si la condition est évaluée sur *false*. <br/>Exemples de valeurs : <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> « = $(/documents/entités/\*) » <br/>

## <a name="skill-outputs"></a>Sorties de la compétence
Il existe une seule sortie appelée simplement « sortie ». Elle retourne la valeur *whenFalse* si la condition est false ou *whenTrue* si la condition est vraie.

## <a name="examples"></a>Exemples

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Exemple de définition de compétence 1 : Filtrer les documents pour retourner uniquement les documents Français

La sortie suivante retourne un tableau de phrases (« / documents/frenchSentences ») si la langue du document est le Français. Si la langue n’est pas le Français, la valeur est définie sur *null*.

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
Si « / documents/frenchSentences » est utilisé comme le *contexte* d’une autre compétence, cette compétence s’exécute uniquement si « / documents/frenchSentences » n’est pas définie sur *null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Exemple de définition de compétence 2 : Définir une valeur par défaut pour une valeur qui n’existe pas

La sortie suivante crée une annotation (« / documents/languageWithDefault ») qui est définie sur le langage du document ou à « es » si la langue n’est pas définie.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Exemple de définition de compétence 3 : Fusionner des valeurs à partir de deux champs dans un seul

Dans cet exemple, des phrases ont un *frenchSentiment* propriété. Chaque fois que le *frenchSentiment* propriété est null, nous souhaitons utiliser la *englishSentiment* valeur. Nous attribuons la sortie à un membre qui est appelé *sentiment* (« / documenter/sentiment / * / sentiment »).

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

## <a name="transformation-example"></a>Exemple de transformation
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Exemple de définition de compétence 4 : Transformation des données sur un seul champ

Dans cet exemple, nous recevons un *sentiment* qui est comprise entre 0 et 1. Nous voulons pas transformer pour qu’il soit comprise entre -1 et 1. Nous pouvons utiliser la compétence conditionnelle pour effectuer cette transformation mineure.

Dans cet exemple, nous n’utilisons pas l’aspect conditionnelle de la compétence, car la condition est toujours *true*.

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
Certains paramètres sont évalués, donc vous devez faire attention à suivre le modèle documenté. Expressions doivent commencer par un signe égal. Un chemin d’accès doit être délimité par des « $("and") ». Veillez à placer les chaînes entre guillemets simples. Qui vous aide à l’évaluateur de faire la distinction entre des chaînes et des chemins d’accès réels et des opérateurs. En outre, assurez-vous d’ajouter un espace blanc autour des opérateurs (par exemple, un « * » dans un chemin d’accès signifie que quelque chose de différent multiply).


## <a name="next-steps"></a>Étapes suivantes

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
