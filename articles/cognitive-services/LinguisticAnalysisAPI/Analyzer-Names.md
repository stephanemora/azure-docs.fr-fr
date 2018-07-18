---
title: Structure d’attribution de noms d’analyseur dans l’API Analyse linguistique | Microsoft Docs
description: Découvrez comment l’API d’analyse linguistique utilise sa structure d’attribution de noms pour les analyseurs, afin d’offrir flexibilité et précision.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/23/2016
ms.author: lesun
ms.openlocfilehash: 2729b7126e82862660fc8e1a995cc87ae996ea03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367968"
---
# <a name="analyzer-names"></a>Noms d’analyseur

Nous utilisons une structure d’attribution de noms un peu complexe pour les analyseurs, afin d’offrir une certaine flexibilité en matière d’analyseur et une certaine précision pour faciliter la compréhension du nom.
Les noms d’analyseur sont formés de quatre éléments : un ID, un type, une spécification et une implémentation.
Le rôle de chaque composant est défini ci-dessous.

## <a name="id"></a>ID
Tout d’abord, un analyseur a un ID unique ; un GUID.
Ces GUID ne doivent être modifiés que très rarement, mais constituent la seule méthode pour décrire de façon unique un analyseur spécifique.

## <a name="kind"></a>Type
Ensuite, chaque analyseur se voit attribuer un **type**.
Celui-ci définit en termes très larges le type d’analyse renvoyé et doit définir de façon unique la structure de données utilisée pour représenter cette analyse.
Actuellement, trois types distincts existent :
 - [Jetons](Sentences-and-Tokens.md)
 - [Balises PDV](Pos-Tagging.md)
 - [Constituency Tree](constituency-parsing.md) (Arborescence de circonscription)

## <a name="specification"></a>Caractéristique
Toutefois, au sein d’un type donné, différents experts peuvent être en désaccord sur la façon dont un phénomène particulier doit être analysé.
Contrairement aux langages de programmation, il n’existe aucune définition claire et exacte à ce sujet.

Par exemple, imaginez que nous essayons de rechercher les jetons dans la phrase en anglais « He didn’t go ».
Examinez plus précisément la chaîne « didn’t ».
Fractionner la phrase en deux jetons (« did » et « not ») est l’une des interprétations possibles.
L’autre phrase « He did not go » aurait donc le même ensemble de jetons.
Une autre possibilité pourrait être de la fractionner en deux jetons « did » et « n’t ».
Ce dernier jeton ne serait pas considéré comme un mot, mais cette approche conserve plus d’informations sur la chaîne en surface, ce qui peut souvent être utile.
Ou alors, cette contraction pourrait être considérée comme un seul mot.

Quel que soit le choix, une certaine cohérence doit être respectée.
C’est précisément le rôle d’une **spécification** : déterminer à quoi ressemble une représentation correcte.

Les sorties de l’analyseur peuvent être comparées de façon juste aux données qui respectent la même spécification.

## <a name="implementation"></a>Implémentation

Il existe souvent plusieurs modèles qui tentent d’obtenir les mêmes résultats, mais avec différentes caractéristiques de performances.
Un modèle peut être plus rapide mais moins précis, tandis qu’un autre peut présenter un autre compromis.

L’élément **implémentation** du nom d’un analyseur est utilisé pour identifier ce type d’informations de façon que les utilisateurs puissent choisir l’analyseur qui correspond le mieux à leurs besoins.
