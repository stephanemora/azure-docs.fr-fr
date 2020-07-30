---
title: 'Procédure : Ajouter des commandes simples à une application Commandes personnalisées - service Speech'
titleSuffix: Azure Cognitive Services
description: Dans cet article, vous apprendrez à ajouter des paramètres à des commandes personnalisées
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 9363f400754a38d4cc6efd29ac48d7a0476de66f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524299"
---
# <a name="add-parameters-to-commands"></a>Ajouter des paramètres aux commandes

Dans cet article, vous apprendrez à ajouter des paramètres à des commandes personnalisées. Les paramètres constituent des informations requises par les commandes pour effectuer une tâche. Dans des scénarios complexes, les paramètres peuvent également servir à définir des conditions qui déclenchent des actions personnalisées.

## <a name="prerequisites"></a>Prérequis

> [!div class="checklist"]
> * [Guide pratique pour Créer une application avec des commandes simples](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>Configurer les paramètres d’une commande TurnOn

Modifiez la commande **TurnOn** existante pour activer et désactiver plusieurs appareils.

1. Dans la mesure où la commande pourra désormais gérer un scénario d’activation et de désactivation, renommez-la **TurnOnOff**.
   1. Dans le volet gauche, sélectionnez la commande **TurnOn**, puis cliquez sur le bouton points de suspension (...) en regard de **Nouvelle commande** en haut du volet.
   
   1. Sélectionnez **Renommer**. Dans la fenêtre **Renommer la commande**, remplacez **Nom** par **TurnOnOff**.

1. Vous allez ensuite ajouter à cette commande un paramètre permettant d'indiquer si l'utilisateur souhaite activer ou désactiver l'appareil.
   1. Sélectionnez **Ajouter** en haut du volet central. Dans la liste déroulante, sélectionnez **Paramètre**.
   1. Dans le volet droit, dans la section **Paramètres**, ajoutez une valeur dans la zone **Nom** : **OnOff**, dans ce cas.
   1. Sélectionnez **Obligatoire**. Dans la fenêtre **Ajouter une réponse pour un paramètre obligatoire**, sélectionnez **Éditeur simple**. Dans la **première variante**, ajoutez
        ```
        On or Off?
        ```
   1. Sélectionnez **Update**.

       > [!div class="mx-imgBorder"]
       > ![Créer une réponse pour un paramètre obligatoire](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Nous configurons maintenant les propriétés des paramètres. Pour plus d’explications sur toutes les propriétés de configuration d’une commande, accédez aux [références](./custom-commands-references.md). Configurez le reste des propriétés du paramètre comme suit :
      

       | Configuration      | Valeur suggérée     | Description                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Nom               | `OnOff`           | Nom descriptif du paramètre                                                                           |
       | Est global          | non cochée       | Case à cocher indiquant si une valeur de ce paramètre est globalement appliquée à toutes les commandes de l'application|
       | Obligatoire           | cochée         | Case à cocher indiquant si une valeur de ce paramètre est requise avant d'exécuter la commande |
       | Réponse pour le paramètre obligatoire      |Éditeur simple > `On or Off?`      | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue |
       | Type               | String          | Type du paramètre, par exemple Nombre, Chaîne ou Date/heure ou Zone géographique   |
       | Configuration      | Accepter les valeurs d'entrée prédéfinies du catalogue interne | Pour les chaînes, cela limite les entrées à un ensemble de valeurs possibles |
       | Valeurs d'entrée prédéfinies     | `on`, `off`           | Ensemble de valeurs possibles et leurs alias         |
       
        
   1. Pour ajouter des valeurs d’entrée prédéfinies, sélectionnez **Ajouter une entrée prédéfinie** et dans fenêtre **Nouvel élément**, tapez un **Nom** comme indiqué dans le tableau ci-dessus. Dans ce cas, nous n’utilisons pas d’alias. Vous pouvez donc laisser le champ vide. 

    > [!div class="mx-imgBorder"]

    > ![Créer un paramètre](media/custom-commands/create-on-off-parameter.png)

   1. Sélectionnez **Enregistrer** pour enregistrer toutes les configurations du paramètre.
 
 ### <a name="add-subjectdevice-parameter"></a>Ajouter un paramètre SubjectDevice 

   1. Sélectionnez ensuite à nouveau **Ajouter** pour ajouter un second paramètre représentant le nom des appareils qui peuvent être contrôlés avec cette commande. Utilisez la configuration suivante.
   

       | Paramètre            | Valeur suggérée       |
       | ------------------ | --------------------- |
       | Nom               | `SubjectDevice`         |
       | Est global          | non cochée             |
       | Obligatoire           | cochée               |
       | Réponse pour le paramètre obligatoire     | Éditeur simple > `Which device do you want to control?`    | 
       | Type               | String                |          |
       | Configuration      | Accepter les valeurs d'entrée prédéfinies du catalogue interne | 
       | Valeurs d'entrée prédéfinies | `tv`, `fan`               |
       | Alias (`tv`)      | `television`, `telly`     |

   1. Sélectionnez **Enregistrer**.

### <a name="modify-example-sentences"></a>Modifier des exemples de phrases

Pour les commandes avec paramètres, il convient d'ajouter des exemples de phrases couvrant toutes les combinaisons possibles. Par exemple :

* Informations complètes sur les paramètres - `turn {OnOff} the {SubjectDevice}`
* Informations partielles sur les paramètres - `turn it {OnOff}`
* Aucune information sur les paramètres - `turn something`

Les exemples de phrases, qui peuvent contenir différents degrés d'informations, permettent à l'application Commandes personnalisées de procéder à des résolutions en une ou plusieurs fois avec des informations partielles.

Sachant cela, modifiez les exemples de phrases pour utiliser les paramètres suggérés ci-dessous :

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Sélectionnez **Enregistrer**.

> [!TIP]
> Dans l'éditeur d'exemples de phrases, utilisez des accolades pour faire référence à vos paramètres. - `turn {OnOff} the {SubjectDevice}` Utilisez l'onglet de la saisie semi-automatique basée sur les paramètres créés précédemment.

### <a name="modify-completion-rules-to-include-parameters"></a>Modifier les règles d’exécution pour inclure des paramètres

Modifiez la règle d’exécution **ConfirmationResponse** existante.

1. Dans la section **Conditions**, sélectionnez **Ajouter une condition**.
1. Dans la fenêtre **Nouvelle condition**, dans la liste **Type**, sélectionnez **Paramètres requis**. Dans la liste de contrôle ci-dessous, cochez **OnOff** et **SubjectDevice**.
1. Sélectionnez **Create** (Créer).
1. Dans la section **Actions**, modifiez l’action existante **Envoyer une réponse vocale** en pointant l’action et en sélectionnant le bouton de modification. Cette fois-ci, utilisez les nouveaux paramètres **OnOff** et **SubjectDevice**

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Sélectionnez **Enregistrer**.

### <a name="try-it-out"></a>Faites un essai
1. Sélectionnez l'icône **Former** en haut du volet droit.

1. Une fois l'apprentissage terminé, sélectionnez **Tester**. Une fenêtre **Tester votre application** apparaît.
 Essayez quelques interactions.

    - Entrée : éteindre la télé
    - Sortie : OK, éteindre la télé
    - Entrée : éteindre la télévision
    - Sortie : OK, éteindre la télé
    - Entrée : éteindre
    - Sortie : Quel appareil voulez-vous contrôler ?
    - Entrée : la télé
    - Sortie : OK, éteindre la télé

## <a name="configure-parameters-for-settemperature-command"></a>Configurer les paramètres de la commande SetTemperature

Modifiez la commande **SetTemperature** pour lui permettre de régler la température comme indiqué par l’utilisateur.

Ajouter un nouveau paramètre **Temperature** avec la configuration suivante

| Configuration      | Valeur suggérée     |
| ------------------ | ----------------|
| Nom               | `Temperature`           |
| Obligatoire           | cochée         |
| Réponse pour le paramètre obligatoire      | Éditeur simple > `What temperature would you like?`
| Type               | Number          |


Remplacez les exemples d’énoncés suivants par les valeurs suivantes.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Modifiez les règles d’exécution existantes conformément à la configuration suivante.

| Configuration      | Valeur suggérée     |
| ------------------ | ----------------|
| Conditions         | Paramètre obligatoire > Température           |
| Actions           | Envoyer une réponse vocale > `Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>Faites un essai

**Effectuez l’apprentissage** et **testez** les modifications en effectuant quelques interactions.

- Entrée : Définir la température
- Sortie : Quelle température aimeriez-vous ?
- Entrée : 50 degrés
- Sortie : Ok, régler la température sur 50 degrés

## <a name="configure-parameters-to-the-setalarm-command"></a>Configurer les paramètres de la commande SetAlarm

Ajoutez un paramètre appelé **DateTime** avec la configuration suivante.

   | Paramètre                           | Valeur suggérée                     | 
   | --------------------------------- | ----------------------------------------|
   | Nom                              | `DateTime`                               |
   | Obligatoire                          | cochée                                 |
   | Réponse pour le paramètre obligatoire   | Éditeur simple > `For what time?`            | 
   | Type                              | DateTime                                |
   | Date par défaut                     | Si la date n’est pas indiquée, utiliser aujourd’hui            |
   | Heure par défaut                     | Si l’heure n’est pas indiquée, utiliser le début de la journée     |


> [!NOTE]
> Dans cet article, nous avons principalement utilisé les types de paramètres String, Number et DateTime. Pour obtenir la liste de tous les types de paramètres pris en charge et leurs propriétés, consultez les [références](./custom-commands-references.md).


Remplacez les exemples d’énoncés suivants par les valeurs suivantes.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Modifiez les règles d’exécution existantes conformément à la configuration suivante.

   | Paramètre    | Valeur suggérée                               |
   | ---------- | ------------------------------------------------------- |
   | Actions    | Envoyer une réponse vocale- `Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>Faites un essai

**Effectuez l’apprentissage** et **testez** les modifications.
- Entrée : Set alarm for tomorrow at noon
- Sortie : Ok, alarm set for 2020-05-02 12:00:00
- Entrée : Set an alarm
- Sortie : What time?
- Entrée : 5pm
- Sortie : Ok, alarm set for 2020-05-01 17:00:00


## <a name="try-out-all-the-commands"></a>Testez toutes les commandes

Testez les trois commandes à la fois en utilisant les énoncés associés à différentes commandes. Notez que vous pouvez basculer entre les différentes commandes.

- Entrée : Set an alarm
- Sortie : Pour quelle heure ?
- Entrée : Allume la télévision
- Sortie : D’accord, j’allume la télévision
- Entrée : Set an alarm
- Sortie : Pour quelle heure ?
- Entrée : 5pm
- Sortie : Ok, alarm set for 2020-05-01 17:00:00

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide pratique pour Ajouter des configurations aux paramètres des commandes](./how-to-custom-commands-add-parameter-configuration.md)
