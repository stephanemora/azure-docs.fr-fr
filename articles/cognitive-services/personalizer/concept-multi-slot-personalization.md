---
title: Personnalisation sur plusieurs emplacements
description: Découvrez où et quand utiliser la personnalisation à un seul emplacement et sur plusieurs emplacements avec les API Reward et Rank de Personalizer.
services: cognitive-services
ms.author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 05/24/2021
ms.openlocfilehash: 042bce09afeabb10b20d8dfcb575040ccf5e1c03
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382314"
---
# <a name="multi-slot-personalization-preview"></a>Personnalisation sur plusieurs emplacements (préversion)

La personnalisation sur plusieurs emplacements (en préversion) vous permet de cibler du contenu dans des mises en page web, des carrousels et des listes où plusieurs actions (par exemple, un produit ou un élément de contenu) sont présentées à vos utilisateurs. Grâce aux API multi-emplacements de Personalizer, vous pouvez demander aux modèles IA de Personalizer d’apprendre quels sont les contextes d’utilisation et les produits qui suscitent certains comportements, en tenant compte du placement dans votre interface utilisateur et en en tirant des enseignements. Par exemple, Personalizer peut apprendre que certains produits ou contenus attirent davantage de clics dans une barre latérale ou un pied de page que dans la partie principale d’une page.

Dans cet article, découvrez pourquoi la personnalisation sur plusieurs emplacements améliore les résultats, comment l’activer et quand l’utiliser. Cet article suppose que vous êtes familiarisé avec les API Personalizer comme `Rank` et `Reward`, et que vous avez une compréhension conceptuelle de la façon dont vous les utilisez dans votre application. Si vous n’êtes pas familiarisé avec Personalizer et son fonctionnement, passez en revue les pages suivantes avant de continuer :

* [Qu’est-ce que Personalizer ?](what-is-personalizer.md) 
* [Fonctionnement de Personalizer](how-personalizer-works.md) 

> [!IMPORTANT] 
>  La personnalisation sur plusieurs emplacements est en préversion publique. Les fonctionnalités, les approches et les processus varient en fonction des commentaires des utilisateurs. L’activation de la préversion de la personnalisation sur plusieurs emplacements a pour effet de désactiver les autres fonctionnalités Personalizer dans votre boucle de manière permanente. La personnalisation sur plusieurs emplacements ne peut pas être désactivée une fois qu’elle est activée pour une boucle Personalizer. Lisez ce document et prenez en compte l’impact avant de configurer une boucle Personalizer pour la personnalisation sur plusieurs emplacements.

<!-- 
We encourage you to provide feedback on multi-slot personalization APIs via ...
-->

## <a name="when-to-use-multi-slot-personalization"></a>Quand utiliser la personnalisation sur plusieurs emplacements

Chaque fois que vous affichez des produits et/ou du contenu pour les utilisateurs, vous pouvez afficher plusieurs éléments pour vos clients. Exemple :

* **Mises en page de sites web pour les pages d’accueil** : de nombreuses vignettes et zones de page sont dédiées à la mise en surbrillance du contenu dans des zones, des bannières et les barres latérales de différentes formes et tailles. La personnalisation sur plusieurs emplacements vous apprend en quoi les caractéristiques de cette disposition affectent les choix et actions des clients.
* **Carrousels** : les carrousels de contenu changeant dynamiquement nécessitent quelques éléments qui effectueront une rotation. La personnalisation sur plusieurs emplacements peut apprendre en quoi la durée de séquence et même d’affichage affecte les clics et l’engagement.
* **Produits/contenu connexes et références incorporées** : il est courant d’impliquer les utilisateurs en incorporant des références à du contenu et à des produits supplémentaires dans des bannières, des barres latérales, des vignettes et des zones de pied de page. La personnalisation sur plusieurs emplacements peut vous aider à allouer vos références là où elles sont le plus susceptibles d’être utilisées.
* **Résultats de recherche ou listes** : si votre application a une fonctionnalité de recherche, où vous fournissez des résultats sous forme de listes ou de vignettes, vous pouvez utiliser la personnalisation sur plusieurs emplacements pour choisir les éléments à mettre en évidence en haut en prenant en compte plus de métadonnées que les rankers traditionnels.
* **Canaux et playlists dynamiques** : la personnalisation sur plusieurs emplacements peut vous aider à déterminer une brève séquence pour une liste de vidéos ou de morceaux à lire ensuite dans un canal dynamique.

La personnalisation sur plusieurs emplacements vous permet de déclarer les « emplacements » dans l’interface utilisateur pour lesquels des actions doivent être choisies. Elle vous permet également de fournir plus d’informations sur les emplacements, que Personalizer peut utiliser pour améliorer l’emplacement du produit, par exemple, s’agit-il d’une grande ou d’une petite zone ? Affiche-t-il une légende ou uniquement une fonctionnalité ? S’agit-il d’un pied de page ou d’une barre latérale ?


## <a name="how-to-use-multi-slot-personalization"></a>Utilisation de la personnalisation sur plusieurs emplacements 

1. [Activer la personnalisation sur plusieurs emplacements](#enable-multi-slot-personalization)
1. [Créer un objet JSON pour la demande Rank](#create-json-object-for-a-rank-request)
1. [Appeler l’API Rank définissant les emplacements et les actions de ligne de base](#use-the-response-of-the-rank-api)
1. [Appeler les API Reward](#call-the-reward-api)


### <a name="enable-multi-slot-personalization"></a>Activer la personnalisation sur plusieurs emplacements 

Consultez les [différences entre les personnalisations à un seul emplacement et sur plusieurs emplacements](#differences-between-single-slot-and-multi-slot-personalization) ci-dessous pour comprendre et déterminer si la personnalisation sur plusieurs emplacements vous est utile. *La personnalisation sur plusieurs emplacements est une fonctionnalité en préversion* : nous vous encourageons à créer une boucle Personalizer si vous souhaitez tester les API de personnalisation sur plusieurs emplacements, car son activation est non réversible et aura des effets sur une boucle Personalizer s’exécutant en production.

Une fois que vous avez décidé de convertir une boucle en personnalisation sur plusieurs emplacements, vous devez effectuer les étapes suivantes une seule fois pour cette boucle Personalizer :

[!INCLUDE [Upgrade Personalizer instance to Multi-Slot](./includes/upgrade-personalizer-multi-slot.md)]

### <a name="create-json-object-for-a-rank-request"></a>Créer un objet JSON pour une demande Rank
L’utilisation de la personnalisation sur plusieurs emplacements nécessite une API qui diffère légèrement de l’API de personnalisation à un seul emplacement.

Vous déclarez les emplacements disponibles auxquels attribuer des actions dans chaque demande d’appel Rank, dans l’objet emplacements :

* **Tableau d’emplacements** : vous devez déclarer un tableau d’emplacements. Les emplacements sont *triés* : la position de chaque emplacement dans le tableau est importante. Nous vous recommandons vivement de classer vos définitions d’emplacement en fonction du nombre de récompenses/clics/conversions que chaque emplacement a tendance à obtenir, en commençant par celui qui en obtient le plus. Par exemple, vous pouvez placer une grande zone de type bannière d’un site web en tant qu’emplacement 1, plutôt qu’un petit pied de page.  Tous les autres éléments étant de même niveau, Personalizer attribuera les actions avec davantage de chance d’obtenir des récompenses au plus tôt dans la séquence.
* **ID d’emplacement** : vous devez fournir un ID à chaque emplacement (chaîne unique pour tous les autres emplacements dans cet appel Rank).
* **Caractéristiques de l’emplacement** : vous devez fournir des métadonnées supplémentaires qui le décrivent et le différencie des autres emplacements. Il s’agit des *caractéristiques*. Lorsque vous déterminez les caractéristiques de l’emplacement, vous devez suivre les instructions recommandées pour les caractéristiques de contexte et d’actions (voir [Caractéristiques du contexte et des actions]()).  Les caractéristiques d’emplacement standard permettent d’identifier la taille, la position ou les caractéristiques visuelles d’un élément d’interface utilisateur. Par exemple `position: "top-left"`, `size: "big"`, `animated: "no"`, `sidebar: "true"` ou `sequence: "1"`.
* **Actions de ligne de base** : vous devez spécifier l’ID d’action de ligne de base pour chaque emplacement. Autrement dit, l’ID de l’action qui serait affichée dans cet emplacement si Personalizer n’existait pas. Cela est nécessaire pour former Personalizer en [mode Apprenti]() et pour avoir un chiffre significatif lors des [évaluations hors connexion]().
* **Avoir suffisamment d’actions** : assurez-vous d’appeler Rank avec plus d’actions que d’emplacements, afin que Personalizer puisse attribuer au moins une action à chaque emplacement. Personalizer ne répète pas les recommandations d’action dans les différents emplacements : la réponse de Rank attribue chaque action au plus à un emplacement. 

Si vous ajoutez ou supprimez des emplacements au fil du temps, ajoutez et modifiez leurs caractéristiques ou réorganisez le tableau : Personalizer s’adaptera et poursuivra la formation sur la base des nouvelles informations.

Voici un exemple d’objet `slots` avec quelques exemples de caractéristiques. Bien que la majorité de l’objet `slots` soit stable (puisque les interfaces utilisateur ont tendance à changer lentement), la majeure partie ne changera pas souvent : mais vous devez vous assurer d’attribuer les ID d’action de ligne de base appropriés à chaque appel Rank. 


```json
"slots": [ 
    { 
      "id": "BigHighlight", 
      "features": [ 
            { 
              "size": "Large", 
              "position": "Left-Middle" 
            }
        ],
        "baselineAction": "BlackBoot_4656" 
    }, 

    { 
      "id": "Sidebar1", 
      "features": [ 
            { 
              "size": "Small", 
              "position": "Right-Top" 
            } 
        ],
        "baselineAction": "TrekkingShoe_1122"  
    }  
  ]

```


### <a name="use-the-response-of-the-rank-api"></a>Utiliser la réponse de l’API Rank

Une réponse Rank pour la personnalisation sur plusieurs emplacements reçue de la requête ci-dessus peut se présenter comme suit : 

```json
{ 
  "slots": [ 
        { 
          "id": "BigHighlight", 
          "rewardActionId": "WhiteSneaker_8181" 
        }, 
        { 
          "id": "SideBar1", 
          "rewardActionId": "BlackBoot_4656" 
        } 
    ], 
  "eventId": "123456D0-BFEE-4598-8196-C57383D38E10" 
} 
```

Prenez le rewardActionId de chaque emplacement et utilisez-le pour afficher votre interface utilisateur de manière appropriée.

### <a name="call-the-reward-api"></a>Appeler l’API Reward

Personalizer apprend à choisir les actions qui permettront d’optimiser la récompense obtenue. Votre application observera le comportement de l’utilisateur et calculera un « score de récompense » pour Personalizer en fonction de la réaction observée. Par exemple, si l’utilisateur a cliqué sur l’action dans `"slotId": "SideBar1",`, vous enverrez un « 1 » à Personalizer afin de fournir un renforcement positif pour les choix d’action.

L’API Reward spécifie l’ID d’événement pour la récompense dans l’URL :

```http
https://{endpoint}/personalizer/v1.0/events/{eventId}/reward
``` 

Par exemple, la récompense de l’événement ci-dessus avec l’ID 123456D0-BFEE-4598-8196-C57383D38E10/reward sera envoyée à `https://{endpoint}/personalizer/v1.0/events/123456D0-BFEE-4598-8196-C57383D38E10/reward/reward` :

```json
{ 
  "reward": [ 
    { 
      "slotId": "BigHighlight", 
      "value": 0.2 
    }, 
    { 
      "slotId": "SideBar1", 
     "value": 1.0 
    }, 
  ] 
} 
```

Vous n’avez pas besoin de fournir tous les scores de récompense dans un seul appel de l’API Reward. Vous pouvez appeler l’API Reward plusieurs fois, à chaque fois avec l’ID d’événement et l’ID d’emplacement appropriés. Si aucun score de récompense n’est reçu pour un emplacement dans un événement, Personalizer attribue implicitement la récompense par défaut configurée pour la boucle (en général 0).


## <a name="differences-between-single-slot-and-multi-slot-personalization"></a>Différences entre la personnalisation sur un emplacement unique et sur plusieurs emplacements

Il existe des différences dans la façon dont vous utilisez les API Rank et Reward avec une personnalisation sur un emplacement unique et sur plusieurs emplacements :

|  Description  | Personnalisation sur un emplacement unique   | Personnalisation sur plusieurs emplacements    |  
|---------------|-------------------------------|-------------------------------|
| Éléments de la demande d’appel de l’API Rank | Vous envoyez un objet de contexte et une liste d’actions  | Vous envoyez un contexte, une liste d’actions et une liste ordonnée d’emplacements     | 
| Demande Rank spécifiant une ligne de base | Personalizer prend la première action de la liste d’actions comme action de ligne de base (l’élément que votre application aurait choisi si Personalizer n’existait pas).|Vous devez spécifier l’ID d’action de ligne de base qui aurait été utilisé dans chaque emplacement.|
| Réponse de l’appel de l’API Rank | Votre application met en évidence l’action indiquée dans le champ rewardActionId     | La réponse inclut un rewardActionId différent pour chaque emplacement qui a été spécifié dans la demande. Votre application affiche ces actions rewardActionId dans chaque emplacement. | 
| Appel de l’API Reward | Vous appelez l’API Reward avec un score de récompense, que vous calculez à partir de la manière dont les utilisateurs ont interagi avec rewardActionId pour cet eventId donné. Par exemple, si l’utilisateur clique dessus, vous envoyez une récompense de 1.     | Vous spécifiez la récompense pour chaque emplacement, étant donné la manière dont l’action avec rewardActionId a obtenu le comportement souhaité de l’utilisateur. Ceci peut être envoyé dans un ou plusieurs appels de l’API Reward avec le même ID d’événement.   | 


### <a name="impact-of-enabling-multi-slot-for-a-personalizer-loop"></a>Impact de l’activation de la personnalisation sur plusieurs emplacements pour une boucle Personalizer

En outre, lorsque vous activez la personalisation sur plusieurs emplacements, tenez compte des points suivants :

|  Description  | Personnalisation sur un emplacement unique   | Personnalisation sur plusieurs emplacements    | 
|---------------|-------------------------------|-------------------------------|
| Événements inactifs et activation    | Lors de l’appel de l’API Activate, Personalizer active l’événement, attend un score de récompense ou attribue la récompense configurée par défaut si le temps d’attente de récompense est dépassé. | Personalizer s’active et attend des récompenses pour tous les emplacements spécifiés dans l’ID d’événement. |     
| Mode Apprenti | L’API Rank de Personalizer retourne toujours l’action de ligne de base et forme les modèles internes en imitant l’action de ligne de base.| L’API Rank de Personalizer retourne l’action de ligne de base pour chaque emplacement spécifié dans le champ baselineAction. Personalizer formera les modèles internes en imitant les premiers |
| Vitesse d'apprentissage | Apprend uniquement à partir de l’action mise en surbrillance | Peut apprendre des interactions avec n’importe quel emplacement. Cela signifie généralement qu’il y a plus de comportements utilisateur qui peuvent produire des récompenses, ce qui se traduirait par un apprentissage plus rapide de la part de Personalizer. |  
| Évaluations hors connexion |Compare les performances de Personalizer par rapport aux paramètres de ligne de base et d’apprentissage optimisés, en fonction de l’action qui aurait été choisie.| (Limitation de la préversion) Évalue uniquement les performances du premier emplacement dans le tableau. Pour obtenir des évaluations plus précises, nous vous recommandons de vous assurer que l’emplacement avec la plupart des récompenses est le premier de votre tableau. |
| Optimisation automatique (préversion) | Votre boucle Personalizer peut effectuer régulièrement des évaluations hors connexion en arrière-plan et optimiser les paramètres d’apprentissage sans intervention de l’administrateur | (Limitation de la préversion) L’optimisation automatique est désactivée pour les boucles Personalizer sur lesquelles les API de personnalisation sur plusieurs emplacements sont activées. |

## <a name="next-steps"></a>Étapes suivantes
* [Utilisation de la personnalisation sur plusieurs emplacements](how-to-multi-slot.md)
* [Exemple : notebook Jupyter exécutant une boucle de personnalisation sur plusieurs emplacements](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer/azure-notebook)