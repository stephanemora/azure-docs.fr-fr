---
title: Syntaxe d’une expression de requête - API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser la syntaxe d’expression de requête dans l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 4056ecba7ac80436952228da9e1b74dc7382448c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448950"
---
# <a name="query-expression-syntax"></a>Syntaxe d’expression de requête

Nous avons vu que la réponse à une requête **interpret** inclut une expression de requête. La grammaire qui a interprété la requête de l’utilisateur a créé une expression de requête pour chaque interprétation. Une expression de requête peut ensuite être utilisée pour émettre une requête **evaluate** afin de récupérer les résultats de la recherche d’entités.

Vous pouvez également construire vos propres expressions de requête et les utiliser dans une requête **evaluate**. Cette opération peut être utile si vous créez votre propre interface utilisateur et souhaitez générer une expression de requête en réponse aux actions de l’utilisateur. Pour ce faire, vous devez connaître la syntaxe des expressions de requête.  

Chaque attribut d’entité pouvant être inclus dans une expression de requête dispose d’un type de données spécifique et d’un ensemble d’opérateurs de requête possibles. L’ensemble des attributs d’entité et des opérateurs pris en charge pour chaque attribut est spécifié sur la page [Attributs d’entité](EntityAttributes.md). Une requête à valeur unique requiert l’attribut afin de prendre en charge l’opération *Equals*. Une requête de préfixe requiert l’attribut pour prendre en charge l’opération *StartsWith*. Une requête de plage numérique requiert l’attribut pour prendre en charge l’opération *IsBetween*.

Certaines des données d’entité sont stockées en tant qu’attributs composites, comme l’indique le point « . » dans le nom de l’attribut. Par exemple, les informations d’auteur et de collaboration sont représentées sous forme d’attribut composite. Elles contiennent 4 composants : AuN, AuId, AfN et AfId. Ces composants sont des éléments de données distincts qui forment une valeur d’attribut d’entité unique.

Remarque : Toutes les expressions de requête doivent être en minuscules et sans caractères spéciaux.

**Attribut de chaîne : valeur unique** (inclut les correspondances aux synonymes)  
Ti='indexation par analyse sémantique latente'  
Composite (AA.AuN='sue dumais')

**Attribut de chaîne : valeur unique exacte** (correspond uniquement à des valeurs canoniques)  
Ti=='indexation par analyse sémantique latente'  
Composite (AA.AuN=='susan t dumais')
     
**Attribut de chaîne : valeur de préfixe**   
Ti='indexation par analyse séman'…  
Composite (AA.AuN='sue du'...)

**Attribut numérique : valeur unique**  
Y=2010
 
**Attribut numérique : valeur de plage**  
Y>2005  
Y>2005  
Y<2010  
Y<=2010  
Y=\[2010, 2012\) (inclut la valeur limite gauche uniquement : 2010, 2011)  
Y=\[2010, 2012\] (inclut les deux valeurs limites : 2010, 2011, 2012)
 
**Attribut numérique : valeur de préfixe**  
Y='19'... (toute valeur numérique qui commence par 19) 
 
**Attribut de date : valeur unique**  
D='2010-02-04'

**Attribut de date : valeur de plage**  
D>'2010-02-03'  
D=['2010-02-03','2010-02-05']

**Requêtes And/Or :**  
And(Y=1985, Ti='systèmes électroniques utilisés')  
Or(Ti='systèmes électroniques utilisés', Ti='principes de tolérance aux pannes et pratique')  
And(Or(Y=1985,Y=2008), Ti='systèmes électroniques utilisés')
 
**Requêtes composites :**  
Pour interroger les composants d’un attribut composite, vous devez placer la partie de l’expression de requête qui fait référence à l’attribut composite dans la fonction Composite(). 

Par exemple, pour rechercher des livres par nom d’auteur, utilisez la requête suivante :
```
Composite(AA.AuN='mike smith')
```
<br>Pour rechercher des livres d’un auteur particulier lorsque l’auteur se trouvait dans un établissement spécifique, utilisez la requête suivante :
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>La fonction Composite() associe les deux parties de l’attribut composite. Cela signifie que nous obtenons uniquement des livres où l’un des auteurs est « Mike Smith » pendant qu’il était à Harvard. 

Pour rechercher des livres d’un auteur particulier en collaboration avec des auteurs d’un établissement spécifique, utilisez la requête suivante :
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>Dans cette version, étant donné que Composite() est appliqué à l’auteur et à la collaboration de façon individuelle avant And(), vous obtenez tous les livres où l’un des auteurs est « Mike Smith » et l’établissement de l’un des auteurs ayant collaboré est « Harvard ». Cet exemple semble similaire au précédent, mais tous deux sont différents.

Pour résumer, prenons l’exemple suivant : Nous disposons d’un attribut composite C doté de deux éléments (A et B). Une entité peut disposer de plusieurs valeurs pour C. Voici nos entités :
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>La requête 
```
Composite(And(C.A=1, C.B=2))
```

<br>met en correspondance uniquement les entités qui disposent d’une valeur pour C, où le composant C.A est 1 et le composant C.B est 2. E1 seulement correspond à cette requête.

La requête 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>met en correspondance les entités qui disposent d’une valeur pour C, où C.A est 1, et d’une valeur pour C, où C.B est 2. E1 et E2 correspondent à cette requête.

Notez ce qui suit :  
- Vous ne pouvez pas référencer une partie d’un attribut composite hors d’une fonction Composite().
- Vous ne pouvez pas référencer des parties de deux attributs composites différents au sein d’une même fonction Composite().
- Vous ne pouvez pas référencer un attribut qui ne fait pas partie d’un attribut composite au sein d’une fonction Composite().
