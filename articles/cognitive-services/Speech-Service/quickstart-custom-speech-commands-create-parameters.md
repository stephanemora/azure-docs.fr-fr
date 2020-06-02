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
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853623"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>Démarrage rapide : Créer une application Commandes personnalisées avec des paramètres (préversion)

Dans l'[article précédent](./quickstart-custom-speech-commands-create-new.md), vous avez créé une application Commandes personnalisées simple sans paramètres.

Dans cet article, vous allez ajouter des paramètres à cette application afin qu'elle puisse gérer l'activation et la désactivation de plusieurs appareils.

## <a name="create-parameters"></a>Créer des paramètres

1. Ouvrez le projet [que vous avez créé précédemment](./quickstart-custom-speech-commands-create-new.md).
1. Nous allons modifier la commande existante pour activer et désactiver plusieurs appareils.
1. Dans la mesure où la commande pourra désormais être activée et désactivée, renommez-la `TurnOnOff`.
   - Dans le volet gauche, sélectionnez la commande `TurnOn`, puis cliquez sur l'icône `...` en regard de `+ New command`, en haut du volet.
   
   - Sélectionnez l'icône `Rename`. Dans la fenêtre contextuelle **Renommer la commande**, remplacez **Nom** par `TurOnOff`. Puis sélectionnez **Enregistrer**.

1. Vous allez ensuite créer un paramètre qui permettra d'indiquer si l'utilisateur souhaite activer ou désactiver l'appareil.
   - Sélectionnez l'icône `+ Add` en haut du volet central. Dans la liste déroulante, sélectionnez **Paramètre**.
   - Le volet le plus à droite contient la section de configuration **Paramètres**.
   - Attribuez une valeur au **Nom**.
   - Cochez la case **Obligatoire**. Dans la fenêtre **Ajouter une réponse pour un paramètre obligatoire**, sélectionnez **Éditeur simple** et, pour la **Première variante**, ajoutez
        ```
        On or Off?
        ```
   - Sélectionnez **Update**.

       > [!div class="mx-imgBorder"]
       > ![Créer une réponse pour un paramètre obligatoire](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - Nous allons ensuite configurer le reste des propriétés du paramètre comme suit et sélectionner `Save` pour enregistrer toutes les configurations dans le paramètre.
       

       | Configuration      | Valeur suggérée     | Description                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Nom               | ActiverDésactiver           | Nom descriptif du paramètre                                                                           |
       | Est global          | non cochée       | Case à cocher indiquant si une valeur de ce paramètre est globalement appliquée à toutes les commandes de l'application|
       | Obligatoire           | cochée         | Case à cocher indiquant si une valeur de ce paramètre est requise avant d'exécuter la commande |
       | Réponse pour le paramètre obligatoire      |Éditeur simple -> activé ou désactivé ?      | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue |
       | Type               | String          | Type du paramètre, par exemple Nombre, Chaîne ou Date/heure ou Zone géographique   |
       | Configuration      | Accepter les valeurs d'entrée prédéfinies du catalogue interne | Pour les chaînes, cela limite les entrées à un ensemble de valeurs possibles |
       | Valeurs d'entrée prédéfinies     | on, off             | Ensemble de valeurs possibles et leurs alias         |
       
        > [!div class="mx-imgBorder"]
        > ![Créer un paramètre](media/custom-speech-commands/create-on-off-parameter.png)

   - Ensuite, sélectionnez à nouveau l'icône `+ Add` pour ajouter un deuxième paramètre représentant le nom des appareils avec la configuration suivante.
   

       | Paramètre            | Valeur suggérée       | Description                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | Nom               | AppareilSujet         | Nom descriptif du paramètre                                                                     |
       | Est global          | non cochée             | Case à cocher indiquant si une valeur de ce paramètre est globalement appliquée à toutes les commandes de l'application |
       | Obligatoire           | cochée               | Case à cocher indiquant si une valeur de ce paramètre est requise avant d'exécuter la commande          |
       | Éditeur simple      | Quel appareil ?    | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue                                       |
       | Type               | String                | Type du paramètre, par exemple Nombre, Chaîne ou Date/heure ou Zone géographique                                                |
       | Configuration      | Accepter les valeurs d'entrée prédéfinies du catalogue interne | Pour les chaînes, une liste de chaînes limite les entrées à un ensemble de valeurs possibles       |
       | Valeurs d'entrée prédéfinies | télé, ventilateur               | Ensemble de valeurs possibles et leurs alias                               |
       | Alias (TV)      | télévision, TV     | Alias facultatifs pour chaque valeur d'entrée prédéfinie possible                                 |

## <a name="add-example-sentences"></a>Ajouter des exemples de phrases

Avec les commandes avec paramètres, il convient d'ajouter des exemples de phrases couvrant toutes les combinaisons possibles. Par exemple :

1. Informations complètes sur les paramètres - `turn {OnOff} the {SubjectDevice}`
1. Informations partielles sur les paramètres - `turn it {OnOff}`
1. Aucune information sur les paramètres - `turn something`

Les exemples de phrases, qui peuvent contenir différents degrés d'informations, permettent à l'application Commandes personnalisées de procéder à des résolutions en une ou plusieurs fois avec des informations partielles.

Sachant cela, modifiez les exemples de phrases pour utiliser les paramètres suggérés ci-dessous.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> Dans l'éditeur d'exemples de phrases, utilisez des accolades pour faire référence à vos paramètres. - `turn {OnOff} the {SubjectDevice}` Utilisez l'onglet de la saisie semi-automatique basée sur les paramètres créés précédemment.

## <a name="add-parameters-to-completion-rules"></a>Ajouter des paramètres aux règles de saisie semi-automatique

Modifiez la règle de saisie semi-automatique que nous avons créée dans [le précédent guide de démarrage rapide](./quickstart-custom-speech-commands-create-new.md).

1. Dans la section **Conditions**, ajoutez une nouvelle condition en sélectionnant **+ Ajouter une condition**.
1. Dans la nouvelle fenêtre contextuelle **Nouvelle condition**, sélectionnez `Required parameters` à partir de la liste déroulante **Type**. Dans la liste de contrôle ci-dessous, cochez `OnOff` et `SubjectDevice`.
1. Cliquez sur **Créer**.
1. Dans la section **Actions**, modifiez l'action existante Envoyer une réponse vocale en pointant l'action et en cliquant sur l'icône de modification. Cette fois, nous allons utiliser les paramètres `OnOff` et `SubjectDevice` que nous venons de créer.

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Faites un essai
1. Sélectionnez l'icône `Train` en haut du volet droit.

1. Une fois l'apprentissage terminé, sélectionnez `Test`.
    - Une nouvelle fenêtre **Tester votre application** apparaît.
    - Essayez quelques interactions.

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
> [Démarrage rapide : Utiliser des commandes personnalisées avec Custom Voice (préversion)](./quickstart-custom-speech-commands-select-custom-voice.md)
