---
title: Présentation de l’API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Utilisez l’API Connaissances universitaires pour interpréter les requêtes utilisateur et récupérer des informations enrichies à partir d’Academic Graph.
services: cognitive-services
author: darrine
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: overview
ms.date: 10/30/2018
ms.author: darrine
ms.openlocfilehash: b15ed5e2b31ed817d3f6558858e2b7285f98a70f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551719"
---
# <a name="academic-knowledge-api"></a>API Connaissances universitaires

Bienvenue dans l’API Connaissances universitaires. Ce service permet d’interpréter les requêtes utilisateur dans le cadre universitaire et d’extraire des informations enrichies depuis Microsoft Academic Graph (MAG). La base de connaissances MAG constitue un graphe d’entités hétérogènes à l’échelle du web, composé d’entités qui modélisent des activités d’érudition : domaine d’étude, auteur, établissement, publication, réunion et événement. 

Les données MAG sont extraites de l’index web de Bing ainsi que d’une base de connaissances interne à Bing. Compte tenu de l’indexation permanente de Bing, cette API contient des informations actualisées issues du web, qui suivent les découvertes et le référencement par Bing. En fonction de ce jeu de données, les API Connaissances universitaires permettent un dialogue interactif, axé sur la connaissance, qui combine de manière fluide la recherche réactive avec des expériences de suggestion proactive, des documents sur des travaux fouillés, des résultats de recherche de graphe et des répartitions d’histogrammes de valeurs d’attribut pour un ensemble de documents et d’entités associées.

Pour plus d’informations sur Microsoft Academic Graph, consultez [https://aka.ms/academicgraph](https://aka.ms/academicgraph).

L’API Connaissances universitaires a été déplacée de Cognitive Services Preview dans Cognitive Services Labs. La nouvelle page d’accueil du projet est : [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). Votre clé API existante continue de fonctionner jusqu’au 24 mai 2018. Après cette date, veuillez générer une nouvelle clé API. Notez que la préversion payante n’est plus disponible après l’expiration de votre clé existante. Si le niveau gratuit de l’API n’est pas suffisant pour vos besoins, contactez notre équipe. 

## <a name="features"></a>Caractéristiques
L’API Connaissances universitaires se compose de quatre points de terminaison REST associés :  
  1. **Interpret**. Interprète une chaîne de requête utilisateur en langage naturel. Retourne des interprétations annotées pour permettre des expériences enrichies de saisie semi-automatique dans les zones de recherche qui anticipent la saisie utilisateur.  
  2. **evaluate**. Évalue une expression de requête et retourne les résultats de l’entité Connaissances universitaires.  
  3. **calchistogram**. Calcule un histogramme de la distribution des valeurs d’attribut pour des entités du secteur universitaire retournées par une expression de requête, comme la distribution de citations par année et auteur.  
  
Utilisées conjointement, ces méthodes API vous permettent de créer une expérience de recherche sémantique riche. En fonction d’une chaîne de requête utilisateur, la méthode **interpret** vous fournit une version annotée de la requête et une expression de requête structurée, tout en terminant éventuellement la requête de l’utilisateur selon la sémantique des données universitaires sous-jacentes. Par exemple, si un utilisateur tape la chaîne *latent s*, la méthode **interpret** peut fournir un ensemble d’interprétations classées, suggérant que l’utilisateur peut être à la recherche du champ d’étude *analyse sémantique latente*, de l’article *analyse de structure latente* ou d’autres expressions d’entités commençant par *latent s*. Ces informations peuvent être utilisées pour guider rapidement l’utilisateur vers les résultats de recherche désirés.

La méthode **evaluate** peut s’utiliser pour récupérer un jeu d’entités de documents correspondants à partir de la base de connaissances universitaire, et la méthode **calchistogram** servir pour calculer la distribution des valeurs d’attribut d’un jeu d’entités de documents qui est utilisable pour filtrer davantage les résultats de recherche.        

## <a name="getting-started"></a>Mise en route 
Consultez les sous-rubriques à gauche pour une documentation détaillée.  Notez que pour améliorer la lisibilité des exemples, les appels d’API REST contiennent des caractères (comme les espaces) qui n’ont pas été codés URL.  Votre code doit appliquer les codages URL appropriés.
