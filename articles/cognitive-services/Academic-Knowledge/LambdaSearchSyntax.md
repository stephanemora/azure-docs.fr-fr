---
title: Syntaxe de recherche Lambda – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez la syntaxe de recherche Lambda que vous pouvez utiliser dans l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 4d4c540e00794bfdf1df265457798cc13530c828
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61337786"
---
# <a name="lambda-search-syntax"></a>Syntaxe d’une recherche lambda

Chaque chaîne d’une requête de recherche *lambda* décrit un modèle de graphique. Une requête doit se composer au moins d’un nœud de démarrage qui permet d’indiquer le nœud de graphique à partir duquel commencer la traversée. Pour spécifier un nœud de démarrage, appelez la méthode *MAG.StartFrom()*, puis transmettez l’ID d’un ou de plusieurs nœuds ou un objet de requête qui spécifie les contraintes de recherche. La méthode *StartFrom()* possède trois surcharges. Toutes considèrent deux arguments, le second étant facultatif. Le premier argument peut être un entier long, une collection énumérable d’entiers longs ou une chaîne représentant un objet JSON et possédant la même sémantique que la recherche *json* :
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

Le processus d’écriture d’une requête de recherche lambda consiste à passer d’un nœud à un autre. Pour spécifier le type d’arête à parcourir, utilisez *FollowEdge()* et transmettez les types d’arêtes désirés. *FollowEdge()* considère un nombre arbitraire d’arguments de chaîne :
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Si le type d’arête n’a pas d’importance, omettez simplement *FollowEdge()* entre deux nœuds et la requête parcourra toutes les arêtes possible entre ces deux nœuds.

Vous pouvez spécifier les actions de traversée à effectuer sur un nœud via *VisitNode()*. En d’autres termes, vous pouvez déterminer s’il convient de s’arrêter sur un nœud, auquel cas le résultat correspondra au chemin d’accès actuel, ou de continuer à explorer le graphique.  Le type enum *Action* définit deux types d’actions : *Action.Return* et *Action.Continue*. Vous pouvez transmettre ce type de valeur enum directement à *VisitNode()* ou les combiner aux opérations au niveau du bit et à l’opérateur « & » (AND). Lorsque deux actions sont combinées, cela signifie que les deux actions seront effectuées. Remarque : n’utilisez pas les opérations au niveau du bit ou l’opérateur « | » sur les actions (OR). Ce comportement entraîne l’arrêt de la requête sans aucun résultat. Si vous ignorez *VisitNode()* entre deux appels *FollowEdge()*, l’exploration du graphique est effectuée sans condition lorsque la requête atteint un nœud.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

Pour *VisitNode()*, vous pouvez également transmettre une expression lambda de type *Expression\<Func\<INode, Action\>\>*, qui considère *INode* et renvoie une action de traversée :

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*INode* 

*INode* fournit des interfaces d’accès aux données *en lecture seule* et quelques fonctions d’assistance intégrées sur un nœud. 

### <a name="basic-data-access-interfaces"></a>Interfaces d’accès aux données de base

##### <a name="long-cellid"></a>long CellID

ID du nœud en 64 bits. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(string fieldName)

Obtient la valeur de la propriété spécifiée. *T* représente le type souhaité qui doit être associé à l’interprétation du champ. Un essai de forçage de type automatique sera effectué si le type souhaité ne peut pas être converti implicitement à partir du type du champ. Remarque : lorsque la propriété comporte des valeurs multiples, *GetField\<string\>*  entraîne la sérialisation de la liste en chaîne Json ["val1", "val2", ...]. Si la propriété n’existe pas, il évoque une exception et abandonne l’exploration actuelle du graphique.

##### <a name="bool-containsfieldstring-fieldname"></a>bool ContainsField(string fieldName)

Indique si un champ portant un nom donné existe dans le nœud actuel.

##### <a name="string-getstring-fieldname"></a>string get(string fieldName)

Fonctionne comme *GetField\<string\>(fieldName)*. Toutefois, il n’évoque pas d’exception lorsque le champ est introuvable, il renvoie une chaîne vide ("") à la place.

##### <a name="bool-hasstring-fieldname"></a>bool has(string fieldName)

Indique si la propriété donnée existe dans le nœud actuel. Identique à *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>bool has(string fieldName, string value)

Indique si la propriété dispose de la valeur donnée. 

##### <a name="int-countstring-fieldname"></a>int count(string fieldname)

Obtient le nombre de valeurs d’une propriété donnée. Lorsque la propriété n’existe pas, il renvoie 0.

### <a name="built-in-helper-functions"></a>Fonctions d’assistance intégrées

##### <a name="action-returnifbool-condition"></a>Action return_if(bool condition)

Retourne *Action.Return* si la condition est *true*. Si la condition est *false* et que cette expression n’est jointe à aucune autre action par des opérations au niveau du bit et un opérateur (AND), l’exploration du graphique est abandonnée.

##### <a name="action-continueifbool-condition"></a>Action continue_if(bool condition)

Retourne *Action.Continue* si la condition est *true*. Si la condition est *false* et que cette expression n’est jointe à aucune autre action par des opérations au niveau du bit et un opérateur (AND), l’exploration du graphique est abandonnée.

##### <a name="bool-dicedouble-p"></a>bool dice(double p)

Génère un nombre aléatoire supérieur ou égal à 0.0 et inférieur à 1.0. Cette fonction renvoie *true* uniquement si le nombre est inférieur ou égal à *p*.

Par rapport à la recherche *json*, la recherche *lambda* est plus expressive : Les expressions lambda C# peuvent servir directement à spécifier des modèles de requête. Voici deux exemples.

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
