---
title: 'Procédure : Développer des applications de commandes personnalisées - Service vocal'
titleSuffix: Azure Cognitive Services
description: Découvrez comment développer et personnaliser des applications de commandes. Ces applications de commandes vocales sont les plus adaptées à la réalisation de tâches ou aux scénarios de commande et de contrôle.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: ddf36530e52703ab1033b8e2e787b42b6dc60332
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553275"
---
# <a name="develop-custom-commands-applications"></a>Développer des applications de commandes personnalisées

Dans cet article sur les procédures, vous apprendrez à développer et à configurer des applications de commandes personnalisées. La fonctionnalité de commandes personnalisées vous aide à créer des applications de commandes vocales complètes, optimisées pour les expériences d’interaction de type « voice-first ». Cette fonctionnalité est la plus adaptée à la réalisation de tâches ou aux scénarios de commande et de contrôle. Elle est particulièrement adéquate pour les appareils IoT, ambiants et sans périphérique de contrôle.

Dans cet article, vous allez créer une application capable d’allumer et d’éteindre un téléviseur, de régler la température et de définir une alarme. Après avoir créé ces commandes de base, vous découvrirez les options suivantes pour la personnalisation des commandes :

* Ajout de paramètres aux commandes
* Ajout de configurations aux paramètres des commandes
* Création de règles d’interaction
* Création de modèles de génération de langage pour les réponses vocales
* Utilisation des outils Custom Voice

## <a name="create-an-application-by-using-simple-commands"></a>Créer une application à l’aide de commandes simples

Commencez par créer une application de commandes personnalisées vide. Pour plus d’informations, reportez-vous au [démarrage rapide](quickstart-custom-commands-application.md). Dans cette application, au lieu d’importer un projet, vous créez un projet vide.

1. Dans la zone **Nom**, entrez le nom du projet *Smart-Room-Lite* (ou un autre nom de votre choix).
1. Dans la liste **Langue**, sélectionnez **Français - France**.
1. Sélectionnez ou créez une ressource LUIS.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran représentant la fenêtre Nouveau projet.](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Mettre à jour les ressources LUIS (facultatif)

Vous pouvez mettre à jour la ressource de création que vous avez sélectionnée dans la fenêtre **Nouveau projet**. Vous pouvez également définir une ressource de prédiction. 

La ressource de prédiction est utilisée pour la reconnaissance quand votre application de commandes personnalisées est publiée. Aucune ressource de prédiction n’est nécessaire pendant les phases de développement et de test.

### <a name="add-a-turnon-command"></a>Ajouter une commande TurnOn

Dans l’application de commandes personnalisées Smart-Room-Lite vide que vous avez créée, ajoutez une commande. La commande traitera un énoncé, `Turn on the tv`. Elle répondra avec le message `Ok, turning the tv on`.

1. Créez une commande en sélectionnant **Nouvelle commande** en haut du volet gauche. La fenêtre **Nouvelle commande** s’ouvre.
1. Dans le champ **Nom**, spécifiez la valeur `TurnOn`.
1. Sélectionnez **Create** (Créer).

Le volet central liste les propriétés de la commande. 

Le tableau suivant décrit les paramètres de configuration de la commande. Pour plus d’informations, consultez [Concepts et définitions de commandes personnalisées](./custom-commands-references.md).

| Configuration            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Exemples de phrases | Exemples d’énoncés que l’utilisateur peut prononcer pour déclencher la commande.                                                                 |
| Paramètres       | Informations requises pour exécuter la commande.                                                                                |
| Règles d'exécution | Actions à effectuer pour exécuter la commande. Exemples : répondre à l’utilisateur ou communiquer avec un service web. |
| Règles d'interaction   | Règles supplémentaires pour gérer des situations plus spécifiques ou complexes.                                                              |


> [!div class="mx-imgBorder"]
> ![Capture d'écran montrant où créer une commande.](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Ajouter des exemples de phrases

Dans la section **Exemples de phrases**, vous fournissez un exemple de ce que l'utilisateur peut dire.

1. Dans le volet central, sélectionnez **Exemples de phrases**.
1. Dans le volet de droite, ajoutez des exemples :

    ```
    Turn on the tv
    ```

1.  En haut du volet, sélectionnez **Enregistrer**.

Vous n’avez pas encore de paramètres ; vous pouvez donc passer à la section **Règles d’exécution**.

#### <a name="add-a-completion-rule"></a>Ajouter une règle d’exécution

La commande doit ensuite avoir une règle d’exécution. Cette règle indique à l’utilisateur qu’une action d’exécution est en cours. 

Pour plus d’informations sur les règles et les règles d’exécution, consultez [Concepts et définitions de commandes personnalisées](./custom-commands-references.md).

1. Sélectionnez la règle d’exécution par défaut **Terminé**. Modifiez-la ensuite comme suit :

    
    | Paramètre    | Valeur suggérée                          | Description                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nom**       | `ConfirmationResponse`                  | Nom décrivant l’objectif de la règle          |
    | **Conditions** | None                                     | Conditions déterminant le moment où la règle peut s’exécuter    |
    | **Actions**    | **Envoyer une réponse vocale** > **Éditeur simple** > **Première variation** > `Ok, turning the tv on` | Action à exécuter lorsque la condition de la règle est remplie |

   > [!div class="mx-imgBorder"]
   > ![Capture d'écran montrant où créer une réponse vocale.](media/custom-commands/create-speech-response-action.png)

1. Sélectionnez **Enregistrer** pour enregistrer l’action.
1. Revenez à la section **Règles d'exécution** et sélectionnez **Enregistrer** pour enregistrer toutes les modifications apportées. 

    > [!NOTE]
    > Il n’est pas nécessaire d’utiliser la règle d’exécution par défaut fournie avec la commande. Vous pouvez supprimer la règle d’exécution par défaut et ajouter votre propre règle.

### <a name="add-a-settemperature-command"></a>Ajouter une commande SetTemperature

Ajoutez maintenant une commande supplémentaire, `SetTemperature`. Cette commande prendra un seul énoncé, `Set the temperature to 40 degrees`, puis répondra avec le message `Ok, setting temperature to 40 degrees`.

Pour créer la commande, suivez les mêmes étapes que pour la commande `TurnOn`, mais en utilisant l’exemple de phrase `Set the temperature to 40 degrees`.

Modifiez ensuite les règles d’exécution **Terminé** existantes comme suit :

| Paramètre    | Valeur suggérée                          |
| ---------- | ---------------------------------------- |
| **Nom**  | `ConfirmationResponse`                  |
| **Conditions** | None                                     |
| **Actions**    | **Envoyer une réponse vocale** > **Éditeur simple** > **Première variation** > `Ok, setting temperature to 40 degrees` |

Sélectionnez **Enregistrer** pour enregistrer toutes les modifications apportées à la commande.

### <a name="add-a-setalarm-command"></a>Ajouter une commande SetAlarm

Créez une commande `SetAlarm`. Utilisez l’exemple de phrase `Set an alarm for 9 am tomorrow`. Modifiez ensuite les règles d’exécution **Terminé** existantes comme suit :

| Paramètre    | Valeur suggérée                          |
| ---------- | ---------------------------------------- |
| **Nom**  | `ConfirmationResponse`                  |
| **Conditions** | None                                     |
| **Actions**    | **Envoyer une réponse vocale** > **Éditeur simple** > **Première variation** > `Ok, setting an alarm for 9 am tomorrow` |

Sélectionnez **Enregistrer** pour enregistrer toutes les modifications apportées à la commande.

### <a name="try-it-out"></a>Faites un essai

Testez le comportement de l’application à l’aide du volet de test : 

1. Dans l’angle supérieur droit du volet, sélectionnez l’icône **Effectuer l'apprentissage**. 
1. Une fois la formation terminée, sélectionnez **Test**. 

Essayez les exemples d’énoncés suivants à l’aide de la voix ou du texte :

- Saisissez : *Règle la température sur 40 degrés*
- La réponse attendue est : Régler la température sur 40 degrés
- Saisissez : *Allume la télévision*
- La réponse attendue est : D’accord, j’allume la télévision
- Saisissez : *Règle une alarme pour 9 h demain matin*
- La réponse attendue est : OK, régler une alarme pour 9 h demain matin

> [!div class="mx-imgBorder"]
> ![Capture d’écran montrant le test dans une interface de conversation web.](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> Dans le panneau de test, vous pouvez sélectionner **Activer les détails** pour obtenir plus d’informations sur la manière dont cette entrée vocale/texte a été traitée.

## <a name="add-parameters-to-commands"></a>Ajouter des paramètres aux commandes

Dans cette section, vous apprendrez à ajouter des paramètres à vos commandes. Les commandes requièrent des paramètres pour effectuer une tâche. Dans des scénarios complexes, les paramètres peuvent servir à définir des conditions qui déclenchent des actions personnalisées.

### <a name="configure-parameters-for-a-turnon-command"></a>Configurer les paramètres d’une commande TurnOn

Commencez par modifier la commande `TurnOn` existante pour activer et désactiver plusieurs appareils.

1. Maintenant que la commande pourra gérer un scénario d’activation et de désactivation, renommez-la *TurnOnOff*.
   1. Dans le volet de gauche, sélectionnez la commande **TurnOn**. Ensuite, en regard de **Nouvelle commande** en haut du volet, sélectionnez le bouton représentant des points de suspension ( **...** ).
   
   1. Sélectionnez **Renommer**. Dans la fenêtre **Renommer la commande**, remplacez le nom par *TurnOnOff*.

1. Ajoutez un nouveau paramètre à la commande. Le paramètre indique si l’utilisateur souhaite activer ou désactiver l’appareil.
   1. En haut du volet central, sélectionnez **Ajouter**. Dans le menu déroulant, sélectionnez **Paramètre**.
   1. Dans le volet de droite, dans la section **Paramètres**, ajoutez `OnOff` dans la zone **Nom**.
   1. Sélectionnez **Obligatoire**. Dans la fenêtre **Ajouter une réponse pour un paramètre obligatoire**, sélectionnez **Éditeur simple**. Dans le champ **Première variation**, ajoutez *On or Off?* .
   1. Sélectionnez **Update**.

       > [!div class="mx-imgBorder"]
       > ![Capture d’écran qui montre la section « Ajouter une réponse pour un paramètre requis » avec l’onglet « Éditeur simple » sélectionné.](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Configurez les propriétés du paramètre à l’aide du tableau suivant. Pour plus d’informations sur toutes les propriétés de configuration d’une commande, consultez [Concepts et définitions de commandes personnalisées](./custom-commands-references.md).
      

       | Configuration      | Valeur suggérée     | Description                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Nom**               | `OnOff`           | Nom descriptif du paramètre.                                                                           |
       | **Est global**          | Non sélectionné       | Case à cocher indiquant si une valeur de ce paramètre est globalement appliquée à toutes les commandes de l'application.|
       | **Obligatoire**           | Volumes sélectionnés         | Case à cocher indiquant si une valeur de ce paramètre est nécessaire pour exécuter la commande. |
       | **Réponse pour le paramètre obligatoire**      |**Éditeur simple** > `On or Off?`      | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue. |
       | **Type**               | **Chaîne**          | Type du paramètre, par exemple Nombre, Chaîne, Date/heure ou Zone géographique.   |
       | **Configuration**      | **Accepter les valeurs d’entrée prédéfinies d’un catalogue interne** | Pour les chaînes, ce paramètre limite les entrées à un ensemble de valeurs possibles. |
       | **Valeurs d’entrée prédéfinies**     | `on`, `off`           | Ensemble de valeurs possibles et leurs alias.         |
       
        
   1. Pour ajouter des valeurs d’entrée prédéfinies, sélectionnez **Ajouter une entrée prédéfinie**. Dans la fenêtre **Nouvel élément**, tapez *Nom* comme indiqué dans le tableau précédent. Dans ce cas, vous n’utilisez pas d’alias. Vous pouvez donc laisser ce champ vide.
   
      > [!div class="mx-imgBorder"]
      > ![Capture d’écran montrant comment créer un paramètre.](media/custom-commands/create-on-off-parameter.png)

   1. Sélectionnez **Enregistrer** pour enregistrer toutes les configurations du paramètre.
 
#### <a name="add-a-subjectdevice-parameter"></a>Ajouter un paramètre SubjectDevice

1. Pour ajouter un second paramètre représentant le nom des appareils qui peuvent être contrôlés avec cette commande, sélectionnez **Ajouter**. Utilisez la configuration suivante.


    | Paramètre            | Valeur suggérée       |
    | ------------------ | --------------------- |
    | **Nom**               | `SubjectDevice`         |
    | **Est global**          | Non sélectionné             |
    | **Obligatoire**           | Volumes sélectionnés               |
    | **Réponse pour le paramètre obligatoire**     | **Éditeur simple** > `Which device do you want to control?`    | 
    | **Type**               | **Chaîne**                |     
    | **Configuration**      | **Accepter les valeurs d’entrée prédéfinies d’un catalogue interne** | 
    | **Valeurs d’entrée prédéfinies** | `tv`, `fan`               |
    | **Alias** (`tv`)      | `television`, `telly`     |

1. Sélectionnez **Enregistrer**.

#### <a name="modify-example-sentences"></a>Modifier des exemples de phrases

Pour les commandes qui utilisent des paramètres, il convient d’ajouter des exemples de phrases couvrant toutes les combinaisons possibles. Par exemple :

* Informations complètes sur les paramètres : `turn {OnOff} the {SubjectDevice}`
* Informations partielles sur les paramètres : `turn it {OnOff}`
* Aucune information sur les paramètres : `turn something`

Les exemples de phrases, qui peuvent utiliser plus ou moins d’informations, permettent à l’application de commandes personnalisées de procéder à des résolutions en une ou plusieurs fois en utilisant des informations partielles.

Sachant cela, modifiez les exemples de phrases pour utiliser ces paramètres suggérés :

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Sélectionnez **Enregistrer**.

> [!TIP]
> Dans l'éditeur d'exemples de phrases, utilisez des accolades pour faire référence à vos paramètres. Par exemple : `turn {OnOff} the {SubjectDevice}`.
> Utilisez un onglet pour l’exécution automatique basée sur les paramètres créés précédemment.

#### <a name="modify-completion-rules-to-include-parameters"></a>Modifier les règles d’exécution pour inclure des paramètres

Modifiez la règle d’exécution `ConfirmationResponse` existante.

1. Dans la section **Conditions**, sélectionnez **Ajouter une condition**.
1. Dans la fenêtre **Nouvelle condition**, dans la liste **Type**, sélectionnez **Paramètres requis**. Dans la liste qui suit, sélectionnez à la fois **OnOff** et **SubjectDevice**.
1. Laissez **IsGlobal** désélectionné.
1. Sélectionnez **Create** (Créer).
1. Dans la section **Actions**, modifiez l’action **Envoyer une réponse vocale** en pointant l’action et en sélectionnant le bouton de modification. Cette fois-ci, utilisez les nouveaux paramètres `OnOff` et `SubjectDevice` :

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Sélectionnez **Enregistrer**.

Essayez les modifications en sélectionnant l’icône **Effectuer l'apprentissage** en haut du volet de droite. 

Une fois la formation terminée, sélectionnez **Tester**. Une fenêtre **Tester votre application** apparaît. Essayez les interactions suivantes :

- Entrée : *Éteins la télé*
- Sortie : OK, éteindre la télé
- Entrée : *Éteins la télévision*
- Sortie : OK, éteindre la télé
- Entrée : *Éteins*
- Sortie : Quel appareil voulez-vous contrôler ?
- Entrée : *La télé*
- Sortie : OK, éteindre la télé

### <a name="configure-parameters-for-a-settemperature-command"></a>Configurer les paramètres d’une commande SetTemperature

Modifiez la commande `SetTemperature` pour lui permettre de régler la température comme indiqué par l’utilisateur.

Ajoutez un paramètre `Temperature`. Utilisez la configuration suivante :

| Configuration      | Valeur suggérée     |
| ------------------ | ----------------|
| **Nom**               | `Temperature`           |
| **Obligatoire**           | Volumes sélectionnés         |
| **Réponse pour le paramètre obligatoire**      | **Éditeur simple** > `What temperature would you like?`
| **Type**               | `Number`          |


Remplacez les exemples d’énoncés suivants par les valeurs suivantes.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Modifiez les règles d’exécution existantes. Utilisez la configuration suivante.

| Configuration      | Valeur suggérée     |
| ------------------ | ----------------|
| **Conditions**         | **Paramètre obligatoire** > **Température**           |
| **Actions**           | **Envoyer une réponse vocale** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>Configurer les paramètres d’une commande SetAlarm

Ajoutez un paramètre nommé `DateTime`. Utilisez la configuration suivante.

   | Paramètre                           | Valeur suggérée                     | 
   | --------------------------------- | ----------------------------------------|
   | **Nom**                              | `DateTime`                               |
   | **Obligatoire**                          | Volumes sélectionnés                                 |
   | **Réponse pour le paramètre obligatoire**   | **Éditeur simple** > `For what time?`            | 
   | **Type**                              | **DateTime**                                |
   | **Date par défaut**                     | Si la date n’est pas indiquée, utilisez aujourd’hui.            |
   | **Heure par défaut**                     | Si l’heure n’est pas indiquée, utilisez le début de la journée.     |


> [!NOTE]
> Cet article utilise principalement des types de paramètres Chaîne, Nombre et DateTime. Pour obtenir la liste de tous les types de paramètres pris en charge et leurs propriétés, consultez [Concepts et définitions de commandes personnalisées](./custom-commands-references.md).


Modifiez les exemples d’énoncés. Utilisez les valeurs suivantes.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Modifiez les règles d’exécution existantes. Utilisez la configuration suivante.

   | Paramètre    | Valeur suggérée                               |
   | ---------- | ------------------------------------------------------- |
   | **Actions**    | **Envoyer une réponse vocale** > `Ok, alarm set for {DateTime}`  |

Testez les trois commandes à la fois en utilisant les énoncés associés à différentes commandes. (Vous pouvez basculer entre les différentes commandes.)

- Entrée : *Définit une alarme*
- Sortie : Pour quelle heure ?
- Entrée : *Allume la télévision*
- Sortie : D’accord, j’allume la télévision
- Entrée : *Définit une alarme*
- Sortie : Pour quelle heure ?
- Entrée : *17 h*
- Sortie : Ok, alarm set for 2020-05-01 17:00:00

## <a name="add-configurations-to-command-parameters"></a>Ajouter des configurations aux paramètres des commandes

Dans cette section, vous allez en savoir plus sur la configuration des paramètres avancés, notamment :

 - Comment les valeurs de paramètres peuvent appartenir à un ensemble défini en dehors de l’application de commandes personnalisées.
 - Comment ajouter des clauses de validation sur les valeurs des paramètres.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Configurer un paramètre en tant qu'entité de catalogue externe

La fonctionnalité de commandes personnalisées vous permet de configurer des paramètres de type chaîne pour faire référence à des catalogues externes hébergés sur un point de terminaison web. Vous pouvez ainsi mettre à jour le catalogue externe indépendamment, sans apporter de modifications à l’application de commandes personnalisées. Cette approche est utile dans les cas où les entrées de catalogue sont nombreuses.

Réutilisez le paramètre `SubjectDevice` de la commande `TurnOnOff`. La configuration actuelle de ce paramètre est **Accepter les entrées prédéfinies du catalogue interne**. Cette configuration fait référence à la liste statique d’appareils de la configuration des paramètres. Déplacez ce contenu vers une source de données externe qui peut être mise à jour de manière indépendante.

Pour déplacer le contenu, commencez par ajouter un nouveau point de terminaison web. Dans le volet de gauche, accédez à la section **Points de terminaison web**. À partir de là, ajoutez un nouveau point de terminaison web. Utilisez la configuration suivante.

| Paramètre | Valeur suggérée |
|----|----|
| **Nom** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **Méthode** | **GET** |


Si la valeur suggérée pour l’URL ne fonctionne pas dans votre cas, configurez et hébergez un point de terminaison web qui retourne un fichier JSON composé de la liste des appareils qui peuvent être contrôlés. Le point de terminaison web doit retourner un fichier au format JSON comme suit :
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Accédez ensuite à la page des propriétés du paramètre **SubjectDevice**. Définissez les propriétés suivantes.

| Paramètre | Valeur suggérée |
| ----| ---- |
| **Configuration** | **Accepter les entrées prédéfinies du catalogue externe** |                               
| **Point de terminaison du catalogue** | `getDevices` |
| **Méthode** | **GET** |

Ensuite, sélectionnez **Enregistrer**.

> [!IMPORTANT]
> Aucune option ne vous permet de configurer un paramètre pour accepter les entrées d’un catalogue externe, sauf si le point de terminaison web est défini dans la section **Point de terminaison web** du volet de gauche.

Essayez-le en sélectionnant **Effectuer l'apprentissage**. Une fois la formation terminée, sélectionnez **Tester** et essayez quelques interactions.

* Entrée : *Allume*
* Sortie : Quel appareil voulez-vous contrôler ?
* Entrée : *Les lumières*
* Sortie : Ok, allumer les lumières

> [!NOTE]
> Vous pouvez maintenant contrôler tous les appareils hébergés sur le point de terminaison web. Vous devez tout de même effectuer l'apprentissage de l’application pour tester les nouvelles modifications, puis republier l’application.

### <a name="add-validation-to-parameters"></a>Ajouter la validation aux paramètres

Les *validations* sont des constructions applicables à certains types de paramètres qui vous permettent de configurer des contraintes sur la valeur du paramètre. Elles vous invitent à corriger les valeurs qui ne respectent pas les contraintes. Pour obtenir la liste des types de paramètres qui étendent la construction de validation, consultez [Concepts et définitions de commandes personnalisées](./custom-commands-references.md).

Testez les validations à l’aide de la commande `SetTemperature`. Procédez comme suit afin d’ajouter une validation pour le paramètre `Temperature`.

1. Dans le volet de gauche, sélectionnez la commande **SetTemperature**.
1. Dans le volet central, sélectionnez **Température**.
1. Dans le volet de droite, sélectionnez **Ajouter une validation**.
1. Dans la fenêtre **Nouvelle validation**, configurez la validation comme indiqué dans le tableau suivant. Sélectionnez ensuite **Créer**.


    | Configuration des paramètres | Valeur suggérée | Description |
    | ---- | ---- | ---- |
    | **Valeur minimale** | `60` | Pour les paramètres numériques, valeur minimale que ce paramètre peut accepter |
    | **Valeur maximale** | `80` | Pour les paramètres numériques, valeur maximale que ce paramètre peut accepter |
    | **Réponse d'échec** |  **Éditeur simple** > **Première variation** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Invite pour demander une nouvelle valeur en cas d'échec de la validation |

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant comment ajouter une validation de plage.](media/custom-commands/add-validations-temperature.png)

Faites un test en sélectionnant l’icône **Effectuer l'apprentissage** en haut du volet de droite. Une fois la formation terminée, sélectionnez **Tester**. Essayez quelques interactions :

- Entrée : *Règle la température sur 72 degrés*
- Sortie : Régler la température sur 72 degrés
- Entrée : *Règle la température sur 45 degrés*
- Sortie : Désolé, je peux uniquement régler une température comprise entre 60 et 80 degrés
- Entrée : *Règle plutôt sur 72 degrés*
- Sortie : Régler la température sur 72 degrés

## <a name="add-interaction-rules"></a>Ajouter des règles d’interaction

Les règles d’interaction sont des règles *supplémentaires* qui traitent des situations plus spécifiques ou complexes. Même si vous êtes libre de créer vos propres règles d’interaction, dans cet exemple, vous utilisez des règles d’interaction pour les scénarios suivants :

* Confirmation de commandes
* Ajout d’une correction en une étape à des commandes

Pour plus d’informations sur les règles d’interaction, consultez [Concepts et définitions de commandes personnalisées](./custom-commands-references.md).

### <a name="add-confirmations-to-a-command"></a>Ajouter des confirmations à une commande

Pour ajouter une confirmation, utilisez la commande `SetTemperature` . Pour obtenir une confirmation, créez des règles d’interaction en procédant comme suit :

1. Dans le volet de gauche, sélectionnez la commande **SetTemperature**.
1. Dans le volet central, ajoutez des règles d’interaction en sélectionnant **Ajouter**. Sélectionnez ensuite **Règles d’interaction** > **Confirmer la commande**.

    Cette action ajoute trois règles d’interaction. Les règles demandent à l’utilisateur de confirmer la date et l’heure de l’alarme. Elles attendent une confirmation (oui ou non) pour passer à l’étape suivante.

    1. Modifiez la règle d’interaction **Confirmer la commande** à l’aide de la configuration suivante :
        1. Remplacez le nom par **Confirmer la température**.
        1. Ajouter une nouvelle condition : **Paramètres obligatoires** > **Température**.
        1. Ajouter une nouvelle action : **Type** > **Envoyer une réponse vocale** > **Voulez-vous vraiment définir la température sur {Temperature} degrés ?**
        1. Dans la section **Attentes**, conservez la valeur par défaut **Attente de confirmation de l'utilisateur**.
      
         > [!div class="mx-imgBorder"]
         > ![Capture d'écran montrant comment créer la réponse pour le paramètre obligatoire.](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modifiez la règle d'interaction **Confirmation réussie** pour gérer une confirmation réussie (l’utilisateur a répondu oui).
      
          1. Remplacez le nom par **Confirmation de la température réussie**.
          1. Conservez la condition existante **Confirmation réussie**.
          1. Ajouter une nouvelle condition : **Type** > **Paramètres obligatoires** > **Température**.
          1. Conservez la valeur par défaut de **État après exécution** sur **Exécuter les règles d’exécution**.

    1. Modifiez la règle d’interaction **Confirmation refusée** pour gérer les scénarios où une confirmation est refusée (l’utilisateur a répondu non).

          1. Remplacez le nom par **Confirmation de la température refusée**.
          1. Conservez la condition existante **Confirmation refusée**.
          1. Ajouter une nouvelle condition : **Type** > **Paramètres obligatoires** > **Température**.
          1. Ajouter une nouvelle action : **Type** > **Envoyer une réponse vocale** > **Aucun problème. Quelle température ?** .
          1. Modifiez la valeur par défaut de **État après exécution** sur **Attente d'entrée de l'utilisateur**.

> [!IMPORTANT]
> Dans cet article, vous utilisez la fonction de confirmation intégrée. Vous pouvez également ajouter manuellement des règles d’interaction une par une.
   
Essayez les modifications en sélectionnant **Effectuer l'apprentissage**. Une fois la formation terminée, sélectionnez **Tester**.

- **Entrée**: *Règle la température sur 80 degrés*
- **Sortie** : êtes-vous sûr de vouloir régler la température sur 80 degrés ?
- **Entrée**: *Non*
- **Sortie**: Pas de problème. Quelle température ?
- **Entrée**: *72 degrés*
- **Sortie** : êtes-vous sûr de vouloir définir la température sur 72 degrés ?
- **Entrée**: *Oui*
- **Sortie**: OK, réglage de la température sur 72 degrés

### <a name="implement-corrections-in-a-command"></a>Implémenter des corrections dans une commande

Dans cette section, vous allez configurer une correction en une étape. Cette correction est utilisée après l’exécution de l’action d’exécution. Vous verrez également un exemple de la façon dont une correction est activée par défaut si la commande n’est pas encore exécutée. Pour ajouter une correction lorsque la commande n’est pas terminée, ajoutez le nouveau paramètre `AlarmTone`.

Dans le volet de gauche, sélectionnez la commande **SetAlarm**. Ajoutez ensuite le nouveau paramètre **AlarmTone**.
        
- **Nom** > `AlarmTone`
- **Type** > **Chaîne**
- **Valeur par défaut** > **Carillon**
- **Configuration** > **Accepter les valeurs d’entrée prédéfinies du catalogue interne**
- **Valeurs d’entrée prédéfinies** > **Carillon**, **Jingle** et **Écho** (ces valeurs sont des entrées prédéfinies individuelles).


Ensuite, mettez à jour la réponse pour le paramètre **DateTime** sur **Prêt à régler l’alarme avec une tonalité {AlarmTone}. Pour quelle heure ?** . Modifiez ensuite la règle d’exécution comme suit :

1. Sélectionnez la règle d’exécution **ConfirmationResponse** existante.
1. Dans le volet de droite, pointez sur l’action existante et sélectionnez **Modifier**.
1. Mettez à jour la réponse vocale avec la valeur `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}`.

> [!IMPORTANT]
> La tonalité d’alarme peut changer sans aucune configuration explicite dans une commande en cours. Par exemple, elle peut changer lorsque la commande n’est pas encore terminée. Une correction est activée *par défaut* pour tous les paramètres de la commande, quel que soit le nombre de tours si la commande n’est pas encore terminée.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Implémenter une correction lorsqu’une commande est terminée

La plateforme Commandes personnalisées permet d’effectuer une correction en une étape même lorsque la commande est terminée. Cette fonctionnalité n’est pas activée par défaut. Elle doit être configurée de manière explicite. 

Procédez comme suit pour configurer une correction en une étape :

1. Dans la commande **SetAlarm**, ajoutez une règle d’interaction de type **Mettre à jour la commande précédente** pour mettre à jour l’alarme précédemment définie. Renommez la règle d’interaction en **Mettre à jour l’alarme précédente**.
1. Conservez la condition par défaut : **La commande précédente doit être mise à jour**.
1. Ajouter une nouvelle condition : **Type** > **Paramètre obligatoire** > **DateTime**.
1. Ajouter une nouvelle action : **Type** > **Envoyer une réponse vocale** > **Éditeur simple** > **Mise à jour de l’heure d’alarme précédente sur {DateTime}** .
1. Pour **État après exécution**, conservez la valeur par défaut **Commande terminée**.

Essayez les modifications en sélectionnant **Effectuer l'apprentissage**. Attendez la fin de la formation, puis sélectionnez **Tester**.

- **Entrée**: *Règle une alarme.*
- **Sortie**: Prêt à régler l’alarme avec une tonalité carillon. Pour quelle heure ?
- **Entrée**: *Règle une alarme avec la tonalité Jingle pour 9 h demain matin.*
- **Sortie**: OK, alarme réglée pour 21-05-2020 09:00:00. La tonalité de l’alarme est Jingle.
- **Entrée**: *Non, 8 heures.*
- **Sortie**: Mise à jour de l’alarme précédente à 29-05-2020 08:00.

> [!NOTE]
> Dans une application réelle, dans la section **Actions** de cette règle de correction, vous devrez également renvoyer une activité au client ou appeler un point de terminaison HTTP pour mettre à jour l’heure de l’alarme dans votre système. Cette action doit uniquement être responsable de la mise à jour de l’heure de l’alarme. Elle ne doit pas être responsable d’un autre attribut de la commande. Dans ce cas, cet attribut serait la tonalité de l’alarme.

## <a name="add-language-generation-templates-for-speech-responses"></a>Ajouter des modèles de génération de langage pour les réponses vocales

Les modèles de génération de langage vous permettent de personnaliser les réponses envoyées au client. Ils permettent également d’introduire une variance dans les réponses. Vous pouvez utiliser la génération de langage à l’aide des éléments suivants :

* des modèles de génération de langage ;
* des expressions adaptatives.

Les modèles de commandes personnalisés sont basés sur les [modèles de génération de langage](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) BotFramework. Dans la mesure où les commandes personnalisées créent un nouveau modèle de génération de langage quand cela est nécessaire (pour les réponses vocales dans les paramètres ou les actions), vous n’avez pas besoin de spécifier le nom du modèle de génération de langage. 

Vous n’avez donc pas besoin de définir votre modèle de la façon suivante :

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Au lieu de cela, vous pouvez définir le corps du modèle sans le nom, comme suit :

> [!div class="mx-imgBorder"]
> ![Capture d’écran montrant un exemple d’éditeur de modèle.](./media/custom-commands/template-editor-example.png)


Cette modification introduit une variation aux réponses vocales envoyées au client. Pour un énoncé, la réponse vocale correspondante est choisie de manière aléatoire parmi les options fournies.

Les modèles de génération de langage vous permettent également de définir des réponses vocales complexes pour les commandes à l’aide d’expressions adaptatives. Pour plus d’informations, consultez le [format de modèles de génération de langage](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). 

Par défaut, la fonctionnalité de commandes personnalisées prend en charge toutes les fonctionnalités, avec les différences mineures suivantes :

* Dans les modèles de génération de langage, les entités sont représentées sous la forme `${entityName}`. La fonctionnalité de commandes personnalisées n’utilise pas d’entités. Vous pouvez toutefois utiliser des paramètres en tant que variables avec la représentation `${parameterName}` ou la représentation `{parameterName}`.
* La fonctionnalité de commandes personnalisées ne prend pas en charge la composition et l’expansion de modèle, car vous ne modifiez jamais directement le fichier *.lg*. Vous ne modifiez que les réponses des modèles créés automatiquement.
* La fonctionnalité de commandes personnalisées ne prend pas en charge les fonctions personnalisées que la génération de langage injecte. Les fonctions prédéfinies sont prises en charge.
* La fonctionnalité de commandes personnalisées ne prend pas en charge les options, telles que `strict`, `replaceNull`et `lineBreakStyle`.

### <a name="add-template-responses-to-a-turnonoff-command"></a>Ajouter des modèles de réponses à une commande TurnOnOff

Modifiez la commande `TurnOnOff` pour ajouter un nouveau paramètre. Utilisez la configuration suivante.

| Paramètre            | Valeur suggérée       | 
| ------------------ | --------------------- | 
| **Nom**               | `SubjectContext`         | 
| **Est global**          | Non sélectionné             | 
| **Obligatoire**           | Non sélectionné               | 
| **Type**               | **Chaîne**                |
| **Valeur par défaut**      | `all` |
| **Configuration**      | **Accepter les valeurs d’entrée prédéfinies du catalogue interne** | 
| **Valeurs d’entrée prédéfinies** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Modifier une règle d’exécution

Modifiez la section **Actions** de la règle d’exécution existante **ConfirmationResponse**. Dans la fenêtre **Modifier l’action**, basculez vers **Éditeur de modèles**. Remplacez ensuite le texte par l’exemple suivant.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Effectuez l'apprentissage et testez votre application à l’aide de l’entrée et de la sortie suivantes. Notez la variation des réponses. La variation des réponses est créée en raison de plusieurs alternatives du modèle de valeur et de l’utilisation d’expressions adaptatives.

* Entrée : *Allume la télévision*
* Sortie : D’accord, j’allume la télévision
* Entrée : *Allume la télévision*
* Sortie : Ok, télévision allumée
* Entrée : *Éteins les lumières*
* Sortie : OK, éteindre toutes les lumières
* Entrée : *Éteins les lumières de la pièce*
* Sortie : OK, éteindre les lumières de la pièce

## <a name="use-a-custom-voice"></a>Utiliser une voix personnalisée

Une autre façon de personnaliser les réponses de commandes personnalisées consiste à sélectionner une voix de sortie. Procédez comme suit pour remplacer la voix par défaut par une voix personnalisée :

1. Dans le volet de gauche de votre application de commandes personnalisées, sélectionnez **Paramètres**.
1. Dans le volet central, sélectionnez **Custom Voice**.
1. Dans le tableau, sélectionnez une voix personnalisée ou une voix publique.
1. Sélectionnez **Enregistrer**.

> [!div class="mx-imgBorder"]
> ![Capture d’écran montrant des exemples de phrases et de paramètres.](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> Concernant les voix publiques, les types neuronaux ne sont disponibles que pour certaines régions. Pour plus d’informations, consultez [Régions prises en charge par le service Speech](./regions.md#neural-and-standard-voices).
>
> Vous pouvez créer des voix personnalisées sur la page du projet **Custom Voice**. Pour plus d’informations, consultez [Bien démarrer avec Custom Voice](./how-to-custom-voice.md).

L’application répond à présent avec la voix sélectionnée, et non plus avec la voix par défaut.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [intégrer votre application de commandes personnalisées](how-to-custom-commands-setup-speech-sdk.md) avec une application cliente à l’aide du kit de développement logiciel (SDK) Speech.
* [Configurez un déploiement continu](how-to-custom-commands-deploy-cicd.md) pour votre application de commandes personnalisées à l’aide d’Azure DevOps. 
                      
