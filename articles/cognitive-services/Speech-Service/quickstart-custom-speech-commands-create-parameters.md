---
title: 'Démarrage rapide : Créer une commande personnalisée avec Paramètres (préversion) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans cet article, vous allez ajouter des paramètres à une application Commandes personnalisées.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 994ac88f78dfe5a5b0ee6fef3fa97d66d53c911b
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156690"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Démarrage rapide : Créer une commande personnalisée avec paramètres (préversion)

Dans l'[article précédent](./quickstart-custom-speech-commands-create-new.md), nous avons créé un projet Commandes personnalisées pour répondre aux commandes sans paramètres.

Dans cet article, nous allons ajouter des paramètres à cette application afin qu'elle puisse gérer l'activation et la désactivation de plusieurs appareils.

## <a name="create-parameters"></a>Créer des paramètres

1. Ouvrez le projet [que nous avons créé précédemment](./quickstart-custom-speech-commands-create-new.md).
1. Dans la mesure où la commande pourra désormais être activée et désactivée, renommez-la « ActiverDésactiver ».
   - Survolez le nom de la commande et sélectionnez l'icône de modification pour le changer.
1. Créez un nouveau paramètre pour indiquer si l'utilisateur souhaite activer ou désactiver l'appareil.
   - Sélectionnez l'icône `+` en regard de la section Paramètres.

   > [!div class="mx-imgBorder"]
   > ![Créer un paramètre](media/custom-speech-commands/create-on-off-parameter.png)

   | Paramètre            | Valeur suggérée     | Description                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name               | ActiverDésactiver               | Nom descriptif de votre paramètre                                                                     |
   | Est global          | non cochée           | Case à cocher indiquant si une valeur de ce paramètre est globalement appliquée à toutes les commandes du projet |
   | Obligatoire           | cochée             | Case à cocher indiquant si une valeur de ce paramètre est requise avant d'exécuter la commande          |
   | Modèle de réponse  | « - Activé ou désactivé ? »      | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue                                       |
   | Type               | String              | Type du paramètre, par exemple Nombre, Chaîne ou Date/heure                                               |
   | Configuration      | Liste de chaînes         | Pour les chaînes, une liste de chaînes limite les entrées à un ensemble de valeurs possibles                                      |
   | Valeurs de la liste de chaînes | on, off             | Pour un paramètre Liste de chaînes, ensemble des valeurs possibles et leurs synonymes                                |

   - Ensuite, sélectionnez à nouveau l'icône `+` pour ajouter un deuxième paramètre représentant le nom des appareils. Pour cet exemple, un téléviseur et un ventilateur

   | Paramètre            | Valeur suggérée       | Description                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name               | AppareilSujet         | Nom descriptif de votre paramètre                                                                     |
   | Est global          | non cochée             | Case à cocher indiquant si une valeur de ce paramètre est globalement appliquée à toutes les commandes du projet |
   | Obligatoire           | cochée               | Case à cocher indiquant si une valeur de ce paramètre est requise avant d'exécuter la commande          |
   | Modèle de réponse  | « - Quel appareil ? »     | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue                                       |
   | Type               | String                | Type du paramètre, par exemple Nombre, Chaîne ou Date/heure                                               |
   | Configuration      | Liste de chaînes           | Pour les chaînes, une liste de chaînes limite les entrées à un ensemble de valeurs possibles                                      |
   | Valeurs de la liste de chaînes | télé, ventilateur               | Pour un paramètre Liste de chaînes, ensemble des valeurs possibles et leurs synonymes                                |
   | Synonymes (télé)      | télévision, TV     | Synonymes facultatifs pour chacune des valeurs possibles d'un paramètre Liste de chaînes                                      |

## <a name="add-sample-sentences"></a>Ajouter des exemples de phrases

Avec les paramètres, il convient d'ajouter des exemples de phrases couvrant toutes les combinaisons possibles. Par exemple :

1. Informations complètes sur les paramètres - `"turn {OnOff} the {SubjectDevice}"`
1. Informations partielles sur les paramètres - `"turn it {OnOff}"`
1. Aucune information sur les paramètres - `"turn something"`

Les exemples de phrases, qui peuvent contenir plus ou moins d'informations, permettent à l'application Commandes personnalisées de procéder à des résolutions en une ou plusieurs fois avec des informations partielles.

Sachant cela, modifiez les exemples de phrases pour utiliser les paramètres suggérés ci-dessous.

> [!TIP]
> Dans l'éditeur d'exemples de phrases, utilisez des accolades pour faire référence à vos paramètres. - `turn {OnOff} the {SubjectDevice}` Utilisez la saisie semi-automatique via la touche Tab pour faire référence aux paramètres créés précédemment.

> [!div class="mx-imgBorder"]
> ![Exemples de phrases avec paramètres](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Ajouter des paramètres à la règle de saisie semi-automatique

Modifiez la règle de saisie semi-automatique que vous avez créée dans [le précédent guide de démarrage rapide](./quickstart-custom-speech-commands-create-new.md) :

1. Ajoutez une nouvelle condition et sélectionnez le paramètre Obligatoire. Sélectionnez `OnOff` et `SubjectDevice`
1. Modifiez l'action Réponse vocale pour utiliser `OnOff` et `SubjectDevice` :

   ```
   Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Faites un essai

Ouvrez le volet Conversation de test et essayez quelques interactions.

- Entrée : éteindre la télé
- Sortie : OK, éteindre la télé

- Entrée : éteindre la télévision
- Sortie : OK, éteindre la télé

- Entrée : éteindre
- Sortie : Quel appareil ?
- Entrée : la télé
- Sortie : OK, éteindre la télé

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Démarrage rapide : Se connecter à une application Commandes personnalisées avec le kit de développement logiciel (SDK) Speech (préversion)](./quickstart-custom-speech-commands-speech-sdk.md)
