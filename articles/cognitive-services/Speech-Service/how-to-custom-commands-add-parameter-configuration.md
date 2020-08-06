---
title: 'Procédure : Configurer un paramètre en tant qu’entité externe'
titleSuffix: Azure Cognitive Services
description: Dans cet article, nous expliquons comment configurer un paramètre de chaîne pour faire référence au catalogue exposé sur un point de terminaison Web.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284181"
---
# <a name="add-configurations-to-commands-parameters"></a>Ajouter des configurations aux paramètres des commandes

Dans cet article, vous allez en savoir plus sur la configuration des paramètres avancés, notamment :

 - Comment les valeurs de paramètres peuvent appartenir à un ensemble défini en externe à une application Commandes personnalisées
 - Comment ajouter des clauses de validation sur la valeur des paramètres

## <a name="prerequisites"></a>Prérequis

Vous devez avoir effectué les étapes décrites dans les articles suivants :

> [!div class="checklist"]
> * [Guide pratique pour Créer une application avec des commandes simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Guide pratique pour Ajouter des paramètres aux commandes](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>Configurer le paramètre en tant qu’entité de catalogue externe

Dans cette section, vous configurez des paramètres de type chaîne pour faire référence à des catalogues externes hébergés sur un point de terminaison Web. Cela vous permet de mettre à jour le catalogue externe indépendamment, sans apporter de modifications à l’application Commandes personnalisées. Cette approche est utile dans les cas où les entrées de catalogue peuvent être volumineuses.

Réutilisez le paramètre **SubjectDevice** de la commande **TurnOnOff**. La configuration actuelle de ce paramètre est **Accepter les entrées prédéfinies du catalogue interne**. Fait référence à la liste statique d’appareils telle que définie dans la configuration des paramètres. Nous voulons déplacer ce contenu vers une source de données externe qui peut être mise à jour de manière indépendante.

Pour cela, commencez par ajouter un nouveau point de terminaison Web. Accédez à la section **Points de terminaison Web** dans le volet gauche et ajoutez un nouveau point de terminaison Web avec la configuration suivante.

| Paramètre | Valeur suggérée |
|----|----|
| Nom | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Méthode | GET |


Si la valeur suggérée pour l’URL ne fonctionne pas dans votre cas, vous devez configurer et héberger un point de terminaison Web simple qui retourne un fichier json composé d’une liste d’appareils qui peuvent être contrôlés. Le point de terminaison Web doit retourner un fichier au format json comme suit :
    
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


Accédez ensuite à la page du paramètre **SubjectDevice** et modifiez les propriétés comme suit.

| Paramètre | Valeur suggérée |
| ----| ---- |
| Configuration | Accepter les entrées prédéfinies du catalogue externe |                               
| Point de terminaison du catalogue | getDevices |
| Méthode | GET |

Sélectionnez ensuite **Enregistrer**.

> [!IMPORTANT]
> Aucune option ne vous permet de configurer un paramètre pour accepter les entrées d’un catalogue externe, sauf si le point de terminaison Web est défini dans la section **Point de terminaison Web** du volet gauche.

### <a name="try-it-out"></a>Faites un essai

Sélectionnez **Former** et attendez la fin de l’apprentissage. Une fois l’apprentissage terminé, sélectionnez **Tester** et essayez quelques interactions.

* Entrée : allume
* Sortie : Quel appareil voulez-vous contrôler ?
* Entrée : lumières
* Sortie : Ok, allumer les lumières

> [!NOTE]
> Notez comment vous pouvez maintenant contrôler tous les appareils hébergés sur le point de terminaison Web. Vous devez quand même former l’application pour tester les nouvelles modifications et republier l’application.

## <a name="add-validation-to-parameters"></a>Ajouter la validation aux paramètres

Les **validations** sont des constructions applicables à certains types de paramètres qui vous permettent de configurer des contraintes sur la valeur du paramètre et de demander une correction si les valeurs ne figurent pas dans les contraintes. Pour obtenir la liste complète des types de paramètres qui étendent la construction de validation, accédez aux [références](./custom-commands-references.md)

Testez les validations à l’aide de la commande **SetTemperature**. Procédez comme suit afin d’ajouter une validation pour le paramètre **Temperature**.

1. Sélectionnez la commande **SetTemperature** dans le volet gauche.
1. Sélectionnez **Temperature** dans le volet central.
1. Sélectionnez **Ajouter une validation** dans le volet droit.
1. Dans la fenêtre **Nouvelle validation**, configurez la validation comme suit, puis sélectionnez **Créer**.


    | Configuration des paramètres | Valeur suggérée | Description |
    | ---- | ---- | ---- |
    | Valeur minimale | `60` | Pour les paramètres numériques, valeur minimale que ce paramètre peut accepter |
    | Valeur maximale | `80` | Pour les paramètres numériques, valeur maximale que ce paramètre peut accepter |
    | Réponse d'échec |  Éditeur simple > Première variation > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Inviter à demander une nouvelle valeur en cas d'échec de la validation |

    > [!div class="mx-imgBorder"]
    > ![Ajouter une validation de plage](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>Faites un essai

1. Sélectionnez l'icône **Former** en haut du volet droit.

1. Une fois l’apprentissage terminé, sélectionnez **Tester** et essayez quelques interactions :

    - Entrée : Régler la température sur 72 degrés
    - Sortie : Régler la température sur 72 degrés
    - Entrée : Régler la température sur 45 degrés
    - Sortie : Désolé, je peux uniquement régler une température comprise entre 60 et 80 degrés
    - Entrée : Régler plutôt sur 72 degrés
    - Sortie : Régler la température sur 72 degrés

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide pratique pour Ajouter des règles d’interaction](./how-to-custom-commands-add-interaction-rules.md)
