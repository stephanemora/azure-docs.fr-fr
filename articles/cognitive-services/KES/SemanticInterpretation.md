---
title: Interprétation sémantique - API Service d’exploration des connaissances
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’interprétation sémantique dans l’API Service d’exploration des connaissances.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860811"
---
# <a name="semantic-interpretation"></a>Interprétation sémantique

L’interprétation sémantique associe une sortie sémantique à chaque chemin interprété via des règles de grammaire.  Plus particulièrement, le service évalue la séquence d’instructions dans les éléments `tag` traversés par l’interprétation afin d’en calculer la sortie finale.  

Une instruction peut être une assignation d’un littéral ou d’une variable à une autre variable.  Elle peut également assigner la sortie d’une fonction avec 0 ou plusieurs paramètres à une variable.  Chaque paramètre de fonction peut être spécifié à l’aide d’un littéral ou d’une variable.  Si la fonction ne retourne pas de sortie, l’assignation est omise.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Une variable est spécifiée avec un identificateur de nom qui commence par une lettre et ne comprend que des lettres (A-Z), des nombres (0-9), et le tiret bas (\_).  Son type est implicitement déduit par l valeur de sortie de fonction ou littérale qui lui est assignée. 

Voici une liste des types de données actuellement pris en charge :

|Type|Description|Exemples|
|----|----|----|
|Chaîne|Séquence de 0 caractères ou plus|« Hello World ! »<br/>""|
|Bool|Valeur booléenne|true<br/>false|
|Int32|Entier signé 32 bits.  -2.1e9 à 2.1e9|123<br/>-321|
|Int64|Entier signé 64 bits. -9.2e18 et 9.2e18|9876543210|
|Double|Virgule flottante double précision. 1.7e+/-308 (15 chiffres)|123.456789<br/>1.23456789e2|
|Guid|Identificateur global unique|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Requête|Une expression de requête qui spécifie un sous-ensemble d’objets de données dans l’index|All()<br/>And(*q1*, *q2*)|

## <a name="semantic-functions"></a>Fonctions sémantiques

Il y a un ensemble intégré de fonctions sémantiques.  Ces fonctions permettent de construire des requêtes sophistiquées, et fournissent un contrôle dépendant du contexte sur les interprétations grammaticales.

### <a name="and-function"></a>Fonction And

`query = And(query1, query2);`

Renvoie une requête composée de l’intersection de deux requêtes d’entrée.

### <a name="or-function"></a>Fonction Or

`query = Or(query1, query2);`

Renvoie une requête composée de l’union de deux requêtes d’entrée.

### <a name="all-function"></a>Fonction All

`query = All();`

Renvoie une requête qui inclut tous les objets de données.

Dans l’exemple suivant, nous utilisons la fonction All() pour générer de manière itérative une requête basée sur l’intersection d’1 ou plusieurs mots-clés.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Fonction None

`query = None();`

Renvoie une requête qui n’inclut aucun objet de données.

Dans l’exemple suivant, nous utilisons la fonction None() pour générer de manière itérative une requête basée sur l’union d’1 ou plusieurs mots-clés.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Fonction Query

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Renvoie une requête qui n’inclut que les objets dont l’attribut *attrName* correspond à la valeur *value*, selon l’opération spécifiée *op*, qui est par défaut « eq ».  En règle générale, utilisez un élément `attrref` pour créer une requête basée sur la chaîne de requête d’entrée correspondante.  Si une valeur est donnée ou obtenue par d’autres moyens, la fonction Query() peut être utilisée pour créer une requête correspondant à cette valeur.

Dans l’exemple suivant, nous utilisons la fonction Query() pour implémenter la prise en charge de la spécification de publications académiques d’une décennie précise.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Fonction Composite

`query = Composite(innerQuery);`

Renvoie une expression qui encapsule une requête *innerQuery* composée de correspondances sur les sous-attributs d’un attribut composite commun *attr*.  L’encapsulation nécessite l’attribut composite *attr* de n’importe quel objet de données correspondant pour obtenir au moins une valeur qui satisfait individuellement la requête *innerQuery*.  Notez qu’une requête sur les sous-attributs d’un attribut composite doit être encapsulée à l’aide de la fonction Composite() avant d’être combinée avec d’autres requêtes.

Par exemple, la requête suivante renvoie les publications académiques de « harry shum » lorsqu’il travaillait chez « microsoft » :
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

En revanche, la requête suivante renvoie les publications académiques dont l’un des auteurs est « harry shum » et l’une des affiliations est « microsoft » :
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>Fontion GetVariable

`value = GetVariable(name, scope);`

Renvoie la valeur de la variable *name* définie dans la valeur *scope* spécifiée.  La variable *name* est un identificateur qui commence par une lettre et ne comprend que des lettres (A-Z), des nombres (0-9), et le tiret bas (_).  La valeur *scope* peut être définie dans « request » ou « system ».  Remarque : les variables définies dans des étendues différentes sont différentes les unes des autres, dont celles définies via la sortie des fonctions sémantiques.

Les variables d’étendue de requête sont partagées entre toutes les interprétations au sein de la requête d’interprétation actuelle.  Elles peuvent être utilisées pour contrôler la recherche des interprétations plutôt que de grammaire.

Les variables système sont prédéfinies par le service et peuvent être utilisées pour récupérer des statistiques diverses sur l’état actuel du système.  Ci-dessous, l’ensemble des variables système actuellement prises en charge :

|Nom|type|Description|
|----|----|----|
|IsAtEndOfQuery|Bool|true si l’interprétation actuelle correspond à tous les textes de requête d’entrée|
|IsBeyondEndOfQuery|Bool|true si l’interprétation actuelle a suggéré des saisies semi-automatiques au-delà des textes de requête d’entrée|

### <a name="setvariable-function"></a>Fonction SetVariable

`SetVariable(name, value, scope);`

Assigne *value* à la variable *name* dans l’*étendue* spécifiée.  La variable *name* est un identificateur qui commence par une lettre et ne comprend que des lettres (A-Z), des nombres (0-9), et le tiret bas (_).  Actuellement, la seule valeur possible pour *scope* est « request ».  Aucune variable système n’est définissable.

Les variables d’étendue de requête sont partagées entre toutes les interprétations au sein de la requête d’interprétation actuelle.  Elles peuvent être utilisées pour contrôler la recherche des interprétations plutôt que de grammaire.

### <a name="assertequals-function"></a>Fonction AssertEquals

`AssertEquals(value1, value2);`

Si les valeurs *value1* et *value2* sont égales, la fonction réussit et n’a aucun effet secondaire.  Autrement, la fonction échoue et rejette l’interprétation.

### <a name="assertnotequals-function"></a>Fonction AssertNotEquals

`AssertNotEquals(value1, value2);`

Si les valeurs *value1* et *value2* ne sont pas égales, la fonction réussit et n’a aucun effet secondaire.  Autrement, la fonction échoue et rejette l’interprétation.


