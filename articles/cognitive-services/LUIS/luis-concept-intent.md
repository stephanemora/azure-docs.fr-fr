---
title: Intentions de l’utilisateur
titleSuffix: Language Understanding - Azure Cognitive Services
description: Une intention représente une tâche ou une action que l’utilisateur souhaite effectuer. Il s’agit d’une finalité ou d’un objectif exprimé dans l’énoncé d’un utilisateur.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: f9f0c52c187b36416dbc77f280a8f6148cf1cbbc
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971050"
---
# <a name="concepts-about-intents-in-your-luis-app"></a>Concepts relatifs aux intentions dans votre application LUIS

Une intention représente une tâche ou une action que l’utilisateur souhaite effectuer. Il s’agit d’une finalité ou d’un objectif exprimé dans [l’énoncé](luis-concept-utterance.md) d’un utilisateur.

Définissez un ensemble d’intentions qui correspondent aux actions que les utilisateurs souhaitent effectuer dans votre application. Par exemple, une application de voyage définit plusieurs intentions :

Intentions de l’application de voyage   |   Exemples d’énoncés   | 
------|------|
 RéserverVol     |   « Réserve-moi un vol pour Rio la semaine prochaine » <br/> « Emmène-moi à Rio le 24 » <br/> « J’ai besoin d’un billet d’avion pour Rio de Janeiro dimanche prochain »    |
 Salutations     |   « Salut » <br/>« Bonjour » <br/>« Bonsoir »  |
 BulletinMétéo | « Quel temps fait-il à Boston ? » <br/> « Montre-moi les prévisions pour ce week-end » |
 Aucun         | « Trouve-moi une recette de cookies »<br>« Les Lakers ont-ils gagné ? » |

Toutes les applications s’accompagnent de l’intention prédéfinie « [None](#none-intent-is-fallback-for-app) », qui correspond à l’intention de secours. 

## <a name="prebuilt-domains-provide-intents"></a>Intentions fournies par les domaines prédéfinis
En plus des intentions que vous définissez, vous pouvez utiliser les intentions prédéfinies d’un des domaines prédéfinis. Pour savoir comment personnaliser les intentions d’un domaine prédéfini dans une application, voir [Utiliser les domaines prédéfinis dans les applications LUIS](luis-how-to-use-prebuilt-domains.md).

## <a name="return-all-intents-scores"></a>Retourner le score de toutes les intentions
Vous attribuez une seule intention à un énoncé. Lorsque LUIS reçoit un énoncé sur le point de terminaison, il retourne la meilleure intention pour cet énoncé. Si vous souhaitez connaître le score de toutes les intentions de l’énoncé, vous pouvez ajouter un indicateur `verbose=true` sur la chaîne de requête [d’appel du point de terminaison](https://aka.ms/v1-endpoint-api-docs) de l’API. 

## <a name="intent-compared-to-entity"></a>Différence entre intention et entité
L’intention correspond à l’action que le chatbot doit effectuer pour l’utilisateur ; elle s’appuie sur la totalité de l’énoncé. L’entité représente des mots ou des expressions contenues dans l’énoncé. Un énoncé ne peut avoir qu’une seule meilleure intention, mais il peut comporter de nombreuses entités. 

<a name="how-do-intents-relate-to-entities"></a> Créez une intention lorsque le _but_ de l’utilisateur doit déclencher une action dans votre application cliente, comme un appel à la fonction checkweather(). Ensuite, créez une entité afin de représenter les paramètres requis pour exécuter l’action. 

|Exemple d’intention   | Entité | Entité dans des exemples d’énoncés   | 
|------------------|------------------------------|------------------------------|
| BulletinMétéo | { "type": "location", "entity": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "demain","resolution":"2018-05-23" } | « Quel temps fait-il à `Seattle` `tomorrow` ? » |
| BulletinMétéo | { "type": "date_range", "entity": "ce weekend" } | Montre-moi les prévisions pour `this weekend` | 

## <a name="custom-intents"></a>Intentions personnalisées

Des [énoncés](luis-concept-utterance.md) répondant au même objectif correspondent à une seule intention. Les énoncés d’une intention peuvent utiliser n’importe quelle [entité](luis-concept-entity-types.md) de l’application, car celles-ci ne sont pas propres à une intention en particulier. 

## <a name="prebuilt-domain-intents"></a>Intentions de domaine prédéfini

Les [domaines prédéfinis](luis-how-to-use-prebuilt-domains.md) comportent des intentions avec des énoncés.  

## <a name="none-intent-is-fallback-for-app"></a>Intention None de secours pour l’application
L’intention **None** est une intention de secours fourre-tout. Elle sert à montrer à LUIS des expressions qui ne sont pas importantes dans le domaine d’application (sujet). L’intention **None** doit comporter entre 10 et 20 % du nombre total d’énoncés de l’application. Ne laissez pas l’intention None vide. 

### <a name="none-intent-helps-conversation-direction"></a>Utilité pour orienter la conversation
Lorsqu’un énoncé est annoncé comme ayant l’intention None et retourné au chatbot avec cette prédiction, le bot peut poser des questions supplémentaires ou proposer un menu afin d’orienter l’utilisateur vers des choix valides dans le chatbot. 

### <a name="no-utterances-in-none-intent-skews-predictions"></a>Prédictions faussées en l’absence d’énoncés dans l’intention None
Si vous n’ajoutez pas d’énoncés à l’intention **None**, LUIS force un énoncé extérieur au domaine dans l’une des intentions du domaine. Cela fausse les scores de prédiction en indiquant à LUIS la mauvaise intention pour l’énoncé. 

### <a name="add-utterances-to-the-none-intent"></a>Ajout d’énoncés à l’intention None
L’intention **None** est créée mais laissée vide intentionnellement. Remplissez-la avec des énoncés extérieurs à votre domaine. Un bon énoncé pour **None** est un élément sans rapport avec l’application ou son secteur. Par exemple, une application de voyage ne doit pas utiliser pour **None** des énoncés liés aux déplacements, comme les réservations, la facturation, la restauration, l’hébergement, le transport ou les divertissements à bord. 

Quel type d’énoncés reste-t-il pour l’intention None ? Commencez par une question spécifique à laquelle votre bot ne devra pas répondre, par exemple, « Quel type de dinosaure a les dents bleues ? ». Il s’agit d’une question très précise et très éloignée d’une application de voyage. 

### <a name="none-is-a-required-intent"></a>Intention None obligatoire
L’intention **None** est requise ; elle n’est ni supprimable ni renommable.

## <a name="negative-intentions"></a>Intentions négatives 
Si vous souhaitez identifier les intentions négatives et positives, par exemple, « je **veux** une voiture » et « je **ne veux pas** de voiture », vous pouvez créer deux intentions (une positive et une négative) et ajouter les énoncés correspondant à chacune. Vous pouvez également créer une intention unique et marquer les deux termes, positif et négatif, comme une entité.  

## <a name="intent-balance"></a>Équilibre des intentions
Les intentions du domaine d’application doivent avoir un équilibre entre les énoncés sur chaque objectif. Évitez d’avoir une intention avec 10 énoncés et une autre avec 500 énoncés. Ce n’est pas équilibré. Si vous vous trouvez dans cette situation, vérifiez l’intention comportant 500 énoncés pour voir si la majeure partie d’entre eux peut être réorganisée en un [modèle](luis-concept-patterns.md). 

L’intention **None** n’est pas prise en compte dans la recherche d’équilibre. Cette intention doit contenir 10 % du nombre total d’énoncés de l’application.

## <a name="intent-limits"></a>Limites des intentions
Consultez les [limites](luis-boundaries.md#model-boundaries) pour savoir combien d’intentions vous pouvez ajouter à un modèle. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Dépasser le nombre maximal d’intentions 
Demandez-vous d’abord si votre système utilise trop d’intentions. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Combiner plusieurs intentions en une seule avec des entités 
Des intentions trop proches peuvent être plus difficiles à distinguer pour LUIS. Elles doivent être suffisamment variées pour capturer les principales tâches demandées par l’utilisateur, mais il n’est pas nécessaire de saisir la totalité des chemins de votre code. Par exemple, RéserverVol et ServiceClientVol peuvent être des intentions distinctes dans une application de voyage, mais RéserverVolInternational et RéserverVolIntérieur sont trop proches. Si votre système a besoin de les distinguer, utilisez les entités ou une logique autre que des intentions. 

### <a name="dispatcher-model"></a>Modèle du répartiteur
Découvrez comment combiner des applications LUIS et QnA Maker avec le [modèle de répartition](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Aide pour les applications comportant de nombreuses intentions
S’il ne vous est pas possible de réduire le nombre d’intentions ou de les diviser en plusieurs applications, contactez le support. Si votre abonnement Azure comprend des services de support, contactez le [support technique Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [entités](luis-concept-entity-types.md) (mots importants pertinents pour les intentions).
* Découvrir comment [ajouter et gérer les intentions](luis-how-to-add-intents.md) dans une application LUIS.
* Voir les [meilleures pratiques](luis-concept-best-practices.md) en matière d’intention.