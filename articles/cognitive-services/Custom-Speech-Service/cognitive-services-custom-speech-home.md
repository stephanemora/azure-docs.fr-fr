---
title: Vue d’ensemble de Custom Speech Service sur Azure | Microsoft Docs
description: Custom Speech Service est un service cloud qui permet aux utilisateurs de personnaliser des modèles vocaux pour la transcription de la reconnaissance vocale en texte.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ms.openlocfilehash: a6139283a555f8f97371c02f9d1f3d53dc6f15d3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368856"
---
# <a name="what-is-custom-speech-service"></a>Qu’est-ce que le service de reconnaissance vocale personnalisé ?

Custom Speech Service est un service cloud qui donne la possibilité aux utilisateurs de personnaliser des modèles vocaux pour la transcription de la reconnaissance vocale en texte.
Pour utiliser Custom Speech Service, reportez-vous au [portail de Custom Speech Service](https://cris.ai).

Custom Speech Service vous permet de créer en les personnalisant des modèles de langage et des modèles acoustiques adaptés à votre application et vos utilisateurs. En chargeant vos données vocales et/ou textuelles spécifiques sur Custom Speech Service, vous pouvez créer des modèles personnalisés qui peuvent être utilisés conjointement avec les modèles vocaux de pointe existants de Microsoft.

Par exemple, si vous ajoutez une interaction vocale sur une application de téléphone mobile, de tablette ou de PC, vous pouvez créer un modèle de langage personnalisé pouvant se combiner au modèle acoustique de Microsoft pour créer un point de terminaison de reconnaissance vocale conçu spécialement pour votre application. Si votre application est conçue pour une utilisation dans un environnement spécifique, ou par remplissage d’un utilisateur particulier, vous pouvez également créer et déployer un modèle acoustique personnalisé auprès de ce service.


## <a name="how-do-speech-recognition-systems-work"></a>Fonctionnement des systèmes de reconnaissance vocale
Les systèmes de reconnaissance vocale sont constitués de plusieurs composants qui fonctionnent ensemble. Deux des composants les plus importants sont le modèle acoustique et le modèle de langage.

Le modèle acoustique est un classifieur qui étiquette de courts fragments audio en un certain nombre de phonèmes, ou unités sonores, dans une langue donnée. Par exemple, le mot « vocal » comprend cinq phonèmes : « v », « o », « k », « a » et « l ». Ces classifications sont effectuées une centaine de fois par seconde.

Le modèle de langage est une distribution de probabilités sur des séquences de mots. Le modèle de langage aide le système à opérer un choix parmi des séquences de mots homophones, en se basant sur la probabilité des séquences de mots elles-mêmes. Par exemple, les groupes de mots « séquence vocale » et « c’est quand ce veau cale » se prononcent de la même manière mais, la première hypothèse étant sensiblement plus probable, le modèle de langage lui attribue un score supérieur.

Les deux modèles acoustique et de langage sont des modèles statistiques tirés des données d’entraînement. Par conséquent, ils fonctionnent de manière optimale lorsque le discours rencontré quand ils sont utilisés dans des applications est semblable aux données observées pendant l’entraînement. Les modèles acoustique et de langage du moteur de reconnaissance vocale de Microsoft ont été entraînés sur une collection impressionnante de voix et de textes ; ils offrent des performances de pointe pour les scénarios d’utilisation les plus courants, tels que l’interaction avec Cortana sur votre smartphone, tablette ou PC, une recherche sur le web conduite par la voix ou la dictée d’un SMS à un ami.

## <a name="why-use-the-custom-speech-service"></a>Pourquoi utiliser Custom Speech Service ?
Figurant parmi les plus performants, le moteur de reconnaissance vocale de Microsoft est destiné aux scénarios décrits ci-dessus. Toutefois, si vous vous attendez à ce que les requêtes vocales adressées à votre application contiennent des éléments de vocabulaire particuliers, tels que des noms de produits ou des mots de jargon technique rarement rencontrés dans un discours ordinaire, vous obtiendrez probablement de meilleures performances en personnalisant le modèle de langage.

Par exemple, imaginons que vous construisez une application pour effectuer des recherches sur MSDN à l’aide de la voix. Il est probable que des termes tels que « orienté objet » ou « espace de noms » apparaissent plus fréquemment que dans des applications vocales ordinaires. La personnalisation du modèle de langage permet au système d’apprendre cela.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon d’utiliser Custom Speech Service, consultez le [portail Custom Speech Service] (https://cris.ai).

* [Prise en main](cognitive-services-custom-speech-get-started.md)
* [FORUM AUX QUESTIONS](cognitive-services-custom-speech-faq.md)
* [Glossaire](cognitive-services-custom-speech-glossary.md)
