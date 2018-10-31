---
title: Domaines prédéfinis pour Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: LUIS inclut un ensemble de domaines prédéfinis pour ajouter rapidement des scénarios courants d’utilisateur conversationnel.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: b1c7ced4a934ea5d094e0c54a295870986f09933
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651805"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>Ajouter des domaines prédéfinis pour les scénarios d’usage courants 

LUIS inclut un ensemble d’intentions prédéfinies à partir des domaines prédéfinis pour ajouter rapidement des intentions et des énoncés courants. Il s’agit d’un moyen simple et rapide d’ajouter des fonctionnalités à votre application cliente conversationnelle sans avoir à concevoir les modèles pour ces fonctionnalités. 

## <a name="add-a-prebuilt-domain"></a>Ajouter un domaine prédéfini

1. Sur la page **My Apps** (Mes applications), sélectionnez votre application. Vous ouvrez ainsi votre application à la section **Build** (Générer). 

1. Sur la page **Intentions**, sélectionnez **Ajouter des domaines prédéfinis** depuis la barre d’outils en bas à gauche. 

1. Sélectionnez l’intention **Calendrier**, puis sélectionnez le bouton **Ajouter un domaine**.

    ![Ajouter le domaine prédéfini Calendrier](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Sélectionnez **Intentions** dans le volet de navigation gauche pour afficher les intentions Calendrier. Chaque intention de ce domaine comporte le préfixe `Calendar.`. Deux entités et des énoncés pour ce domaine sont ajoutés à l’application : `Calendar.Location` et `Calendar.Subject`. 

## <a name="train-and-publish"></a>Former et publier

1. Après avoir ajouté le domaine, effectuez l’apprentissage de l’application en sélectionnant **Effectuer l’apprentissage** dans la barre d’outils en haut à droite. 

1. Dans la barre d’outils supérieure, sélectionnez **Publier**. Choisissez l’emplacement de publication **Production**. 

1. Lorsque la notification de réussite de couleur verte s’affiche, sélectionnez le lien **Refer to the list of endpoints** (Consulter la liste des points de terminaison) pour visualiser les points de terminaison.

1. Sélectionnez un point de terminaison. Un nouvel onglet de navigateur s’ouvre au niveau de ce point de terminaison. Laissez l’onglet ouvert et poursuivez à la section **Test**.

## <a name="test"></a>Test

Testez la nouvelle intention au niveau du point de terminaison en ajoutant une valeur pour le paramètre **q** : `Schedule a meeting with John Smith in Seattle next week`.

LUIS retourne l’intention correcte et le sujet de réunion :

```JSON
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Référence de domaine prédéfini](./luis-reference-prebuilt-domains.md)
