---
title: 'Démarrage rapide : Créer une application Commandes personnalisées (préversion) avec des paramètres - Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans cet article, vous allez ajouter des paramètres à une application Commandes personnalisées, afin qu’elle puisse activer ou désactiver plusieurs appareils.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: nitinme
ms.openlocfilehash: 940b3604487fbef7736c0d8f4f9299563b0b86d5
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509302"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>Démarrage rapide : Créer une application Commandes personnalisées (préversion) avec des paramètres

Dans l'[article précédent](./quickstart-custom-speech-commands-create-new.md), vous avez créé une application Commandes personnalisées simple sans paramètres.

Dans cet article, vous allez étendre cette application avec des paramètres, afin qu’elle puisse activer ou désactiver plusieurs appareils.

## <a name="create-parameters"></a>Créer des paramètres

1. Ouvrez le projet que vous avez créé dans l’[article précédent](./quickstart-custom-speech-commands-create-new.md).

   Nous allons modifier la commande existante afin de pouvoir l’utiliser pour activer et désactiver plusieurs appareils.
1. Dans la mesure où la commande pourra désormais gérer à la fois l’activation et la désactivation, renommez-la **ActiverDésactiver**.
   1. Dans le volet gauche, sélectionnez la commande **TurnOn**, puis cliquez sur le bouton points de suspension ( **...** ) en regard de **Nouvelle commande** en haut du volet.
   
   1. Sélectionnez **Renommer**. Dans la fenêtre **Renommer la commande**, définissez le **Nom** sur **ActiverDésactiver**. Sélectionnez **Enregistrer**.

1. Créez un paramètre pour indiquer si l’utilisateur souhaite activer ou désactiver l’appareil.
   1. Sélectionnez **Ajouter** en haut du volet central. Dans la liste déroulante, sélectionnez **Paramètre**.
   1. Dans le volet droit, dans la section **Paramètres**, ajoutez une valeur dans la zone **Nom**.
   1. Sélectionnez **Obligatoire**. Dans la fenêtre **Ajouter une réponse pour un paramètre obligatoire**, sélectionnez **Éditeur simple**. Dans la zone **Première variante**, entrez le texte suivant :
        ```
        On or Off?
        ```
   1. Sélectionnez **Update**.

       > [!div class="mx-imgBorder"]
       > ![Créer une réponse pour un paramètre obligatoire](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. Configurez le reste des propriétés du paramètre comme suit :
       

    | Configuration      | Valeur suggérée     | Description                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **Nom**               | **ActiverDésactiver**           | Nom descriptif du paramètre.                                                                  |
    | **Est global**          | Désactivé       | Case à cocher qui indique si une valeur du paramètre est globalement appliquée à toutes les commandes de l’application.|
    | **Obligatoire**           | Volumes sélectionnés         | Case à cocher qui indique si une valeur est nécessaire pour le paramètre.  |
    | **Réponse pour le paramètre obligatoire**      |**Éditeur simple -> activé ou désactivé ?**      | Invite permettant de demander la valeur du paramètre quand elle n’est pas connue. |
    | **Type**               | **Chaîne**          | Type de paramètre. Par exemple, Nombre, Chaîne, Date et heure, Zone géographique.   |
    | **Configuration**      | **Accepter les valeurs d’entrée prédéfinies du catalogue interne** | Pour les chaînes, ce paramètre limite les entrées à un ensemble de valeurs possibles. |
    | **Valeurs d’entrée prédéfinies**     | **activé**, **désactivé**             | Ensemble de valeurs possibles et leurs alias.         |
       


    > [!div class="mx-imgBorder"]
    > ![Créer un paramètre](media/custom-speech-commands/create-on-off-parameter.png)

1. Sélectionnez **Enregistrer** pour enregistrer les paramètres.

 1. Resélectionnez **Ajouter** pour ajouter un deuxième paramètre. Ce paramètre représente le nom de l’appareil. Utilisez les paramètres suivants :
   

       | Paramètre            | Valeur suggérée       | Description                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **Nom**               | **AppareilSujet**         | Nom descriptif du paramètre.                                                                     |
       | **Est global**          | Désactivé             | Case à cocher qui indique si une valeur du paramètre est globalement appliquée à toutes les commandes de l’application. |
       | **Obligatoire**           | Volumes sélectionnés               | Case à cocher qui indique si une valeur est nécessaire pour le paramètre.          |
       | **Éditeur simple**      | **Quel appareil ?**    | Invite permettant de demander la valeur du paramètre quand elle n’est pas connue                                       |
       | **Type**               | **Chaîne**                | Type de paramètre. Par exemple, Nombre, Chaîne, Date et heure, Zone géographique.                                                |
       | **Configuration**      | **Accepter les valeurs d’entrée prédéfinies du catalogue interne** | Pour les chaînes, ce paramètre limite les entrées à un ensemble de valeurs possibles.       |
       | **Valeurs d’entrée prédéfinies** | **télé**, **ventilateur**               | Ensemble de valeurs possibles et leurs alias.                               |
       | **Alias** (TV)      | **télévision**, **TV**     | Alias facultatifs pour chacune des valeurs d’entrée prédéfinies possibles.                                 |

## <a name="add-example-sentences"></a>Ajouter des exemples de phrases

Pour les commandes qui ont des paramètres, il convient d’ajouter des exemples de phrases couvrant toutes les combinaisons possibles. Par exemple :

- Informations complètes sur les paramètres : `turn {OnOff} the {SubjectDevice}`
- Informations partielles sur les paramètres : `turn it {OnOff}`
- Aucune information sur les paramètres : `turn something`

Les exemples de phrases, qui peuvent contenir plus ou moins d’informations, permettent à l’application Commandes personnalisées de procéder à des résolutions en une ou plusieurs fois comportant des informations partielles.

Sachant cela, modifiez les exemples de phrases pour utiliser les paramètres suggérés ici :

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> Dans l’éditeur d’exemples de phrases, utilisez des accolades pour faire référence à vos paramètres : `turn {OnOff} the {SubjectDevice}`.
>
> Utilisez la touche Tab pour l’autocomplétion définie par les paramètres créés.

## <a name="add-parameters-to-completion-rules"></a>Ajouter des paramètres aux règles d’exécution

Modifiez la règle d’exécution que vous avez créée dans le [précédent guide de démarrage rapide](./quickstart-custom-speech-commands-create-new.md).

1. Dans la section **Conditions**, sélectionnez **Ajouter une condition**.
1. Dans la fenêtre **Nouvelle condition**, dans la liste **Type**, sélectionnez **Paramètres requis**. Dans la liste, sélectionnez à la fois **ActiverDésactiver** et **ActiverDésactiver**.
1. Sélectionnez **Create** (Créer).
1. Dans la section **Actions**, modifiez l’action existante **Envoyer une réponse vocale** en pointant l’action et en sélectionnant le bouton de modification. Cette fois-ci, utilisez les nouveaux paramètres `OnOff` et `SubjectDevice` :

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Faites un essai
1. Sélectionnez **Entraîner** en haut du volet droit.

1. Une fois l’entraînement terminé, sélectionnez **Tester**.
    
    Une fenêtre **Tester votre application** apparaît.

1. Essayez quelques interactions.

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Démarrage rapide : Utiliser des commandes personnalisées avec Custom Voice (préversion)](./quickstart-custom-speech-commands-select-custom-voice.md)
