---
title: Expressions de requête structurée - API Service d’exploration des connaissances
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser des expressions de requête structurée dans l’API Service d’exploration de la base de connaissances (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a544cdca1ef4be56fcf368a39040f4ee85076a9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815103"
---
# <a name="structured-query-expression"></a>Expression de requête structurée

Une expression de requête structurée spécifie un ensemble d’opérations à évaluer par rapport à l’index de données.  Il se compose d’expressions de requête d’attribut et de fonctions de niveau supérieur.  Utilisez la méthode [*evaluate*](evaluateMethod.md) pour calculer le nombre d’objets correspondant à l’expression.  Voici un exemple du domaine de publications académiques qui retourne les publications créées par Jaime Teevan depuis l’année 2013.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Les expressions de requête structurée peuvent être obtenues à partir de requêtes [*interpret*](interpretMethod.md), où la sortie sémantique de chaque interprétation est une expression de requête structurée qui retourne les objets d’index correspondant à la requête en langage naturel d’entrée.  Sinon, elles peuvent être créées manuellement à l’aide de la syntaxe décrite dans cette section.

## <a name="attribute-query-expression"></a>Expression de requête d’attribut

Une expression de requête d’attribut identifie un ensemble d’objets selon leur correspondance à un attribut spécifique.  Différentes opérations de mise en correspondance sont prises en charge selon le type d’attribut et l’opération indexée spécifiés dans le [schéma](SchemaFormat.md) :

| Type | Opération | Exemples |
|------|-------------|------------|
| String | equals | Title='latent semantic analysis' (canonique + synonymes) |
| String | equals | Author.Name=='susan t dumais' (canonique uniquement)|
| String | starts_with | Title='latent s'... |
| Int32/Int64/Double | equals | Year=2000 |
| Int32/Int64/Double | starts_with | Year='20'... (toute valeur décimale commençant par « 20 ») |
| Int32/Int64/Double | is_between | Year&lt;2000 <br/> Year&lt;=2000 <br/> Year&gt;2000 <br/> Year&gt;=2000 <br/> Year=[2010,2012) *(inclut la valeur limite gauche uniquement : 2010, 2011)* <br/> Year=[2000,2012] *(inclut les deux valeurs limites : 2010, 2011, 2012)* |
| Date | equals | BirthDate='1984-05-14' |
| Date | is_between | BirthDate&lt;='2008/03/14' <br/> PublishDate=['2000-01-01','2009-12-31'] |
| Guid | equals | Id='602DD052-CC47-4B23-A16A-26B52D30C05B' |


Par exemple, l’expression « Title='latent s'... » correspond à toutes les publications académiques dont le titre commence par la chaîne « latent s ».  Afin d’évaluer cette expression, l’attribut Title doit spécifier l’opération « starts_with » dans le schéma utilisé pour créer l’index.

Pour les attributs avec des synonymes associés, une expression de requête peut spécifier les objets dont la valeur canonique correspond à une chaîne donnée à l’aide de l’opérateur « == », ou les objets pour lesquels une des valeurs canoniques/de synonyme correspond à l’aide de l’opérateur « = ».  Les deux nécessitent l’opérateur « equals » pour être spécifiés dans la définition d’attribut.


## <a name="functions"></a>Fonctions

Il existe un ensemble prédéfini de fonctions permettant la construction d’expressions de requête plus élaborées à partir de requêtes d’attribut de base.

### <a name="and-function"></a>Et fonction

`And(expr1, expr2)`

Retourne l’intersection de deux expressions de requête d’entrée.

L’exemple suivant retourne les publications académiques publiées durant l’année 2000 relatives à la récupération des informations :

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Ou fonction

`Or(expr1, expr2)`

Retourne l’union de deux expressions de requête d’entrée.

L’exemple suivant retourne les publications académiques publiées durant l’année 2000 relatives à la récupération des informations ou à la modélisation d’utilisateur :

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Fonction composite

`Composite(expr)`

Retourne une expression qui encapsule une expression interne composée de requêtes sur les sous-attributs d’un attribut composite commun.  L’encapsulation nécessite l’attribut composite de n’importe quel objet de données correspondant pour obtenir au moins une valeur qui satisfait individuellement l’expression interne.  Notez qu’une expression de requête sur les sous-attributs d’un attribut composite doit être encapsulée à l’aide de la fonction Composite() avant d’être combinée avec d’autres expressions de requête.

Par exemple, l’expression suivante retourne les publications académiques de « harry shum » lorsqu’il travaillait chez « microsoft » :

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

En revanche, l’expression suivante retourne les publications académiques dont l’un des auteurs est « harry shum » et l’une des affiliations est « microsoft » :

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
