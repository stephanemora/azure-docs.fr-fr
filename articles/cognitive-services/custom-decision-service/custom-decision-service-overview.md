---
title: Présentation du service Décision personnalisée
titlesuffix: Azure Cognitive Services
description: Cet article fournit une vue d’ensemble du service Décision personnalisée.
services: cognitive-services
author: alekh
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: overview
ms.date: 05/08/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 073b2bf6df21e05481cde043d8ddcfd49822e94f
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704247"
---
# <a name="what-is-custom-decision-service"></a>Présentation du service Décision personnalisée

Dans une application mobile ou web classique, une première page affiche des liens vers différents articles ou d’autres types de contenu. Lors du chargement de la première page, le service Décision personnalisée peut recevoir une requête pour classer les articles figurant sur cette page. Par conséquent, quand un utilisateur choisit un article en cliquant dessus, une deuxième requête peut être envoyée au service Décision personnalisée, qui enregistre le résultat de cette décision de l’utilisateur.

L’utilisation du service Décision personnalisée est simple. Cela nécessite seulement un flux RSS pour votre contenu et quelques lignes de code JavaScript supplémentaires dans votre application.

Le service Décision personnalisée convertit votre contenu en caractéristiques exploitables pour l’apprentissage automatique. Le système utilise ces caractéristiques pour analyser les éléments de votre contenu (texte, images, vidéos et sentiment général). Il utilise aussi plusieurs autres fonctionnalités [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services), comme [Entity Linking](../entitylinking/home.md), [Analyse de texte](../text-analytics/overview.md), [Émotion ](../emotion/home.md) et [Vision par ordinateur](../computer-vision/home.md).

Voici quelques cas d’usage courants du service Décision personnalisée :

* Personnalisation des articles sur un site web d’actualités
* Personnalisation du contenu vidéo sur un portail multimédia
* Optimisation des emplacements des annonces publicitaires ou des pages web vers lesquelles les annonces redirigent
* Classement des éléments recommandés sur un site web d’achat

Le service Décision personnalisée est disponible en *préversion publique gratuite*. Il peut personnaliser une liste d’articles sur un site web ou une application. L’extraction des caractéristiques donne de meilleurs résultats sur du contenu en langue anglaise. Des [fonctionnalités limitées](../text-analytics/overview.md) sont proposées dans les autres langues (allemand, espagnol, français, japonais et portugais, notamment). Cette documentation sera mise à jour au fur et à mesure que de nouvelles fonctionnalités seront disponibles.

Le service Décision personnalisée peut s’utiliser dans des applications qui ne font pas partie du domaine de personnalisation du contenu. Ces applications peuvent être utiles pour avoir une préversion personnalisée. [Contactez-nous](https://azure.microsoft.com/overview/sales-number/) pour en savoir plus.

## <a name="api-usage-modes"></a>Modes d’utilisation des API

Le service Décision personnalisée s’applique aussi bien à des pages web qu’à des applications mobiles. Les API peuvent être appelées à partir d’un navigateur ou d’une application. Leur mode d’utilisation est similaire, mais il y a quelques différences.

## <a name="glossary-of-terms"></a>Glossaire

Plusieurs termes sont souvent employés dans cette documentation :

* **Ensemble d’actions** : ensemble d’éléments de contenu à classer par le service Décision personnalisée. Cet ensemble peut être spécifié comme point de terminaison *RSS* ou *Atom*.
* **Classement** : chaque requête envoyée au service Décision personnalisée spécifie un ou plusieurs ensembles d’actions. Le système répond en extrayant toutes les options de contenu à partir de ces ensembles et les retourne dans l’ordre de classement.
* **Fonction de rappel** : cette fonction, que vous spécifiez, affiche le contenu dans votre IU. Le contenu est trié selon l’ordre de classement retourné par le service Décision personnalisée.
* **Récompense** : mesure de la réaction de l’utilisateur au contenu affiché. Le service Décision personnalisée mesure la réponse de l’utilisateur à l’aide de clics. Les clics sont signalés au système à l’aide de code personnalisé ajouté à votre application.

## <a name="next-steps"></a>Étapes suivantes

* [Inscrivez votre application](custom-decision-service-get-started-register.md) auprès du service Décision personnalisée.
* Apprenez à optimiser une [page web](custom-decision-service-get-started-browser.md) ou une [application de smartphone](custom-decision-service-get-started-app.md).
* Consultez les [informations de référence sur les API](custom-decision-service-api-reference.md) pour en savoir plus sur les fonctionnalités fournies.
