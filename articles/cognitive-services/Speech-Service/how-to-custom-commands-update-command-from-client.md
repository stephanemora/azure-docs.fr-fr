---
title: Mettre à jour une commande à partir de l’application cliente
titleSuffix: Azure Cognitive Services
description: mettre à jour une commande à partir de l’application cliente
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 1bffb09d0f49bbd0059e8a528d67bfe215f0650d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654345"
---
# <a name="update-a-command-from-the-client"></a>Mettre à jour une commande à partir du client

Dans cet article, nous allons apprendre à mettre à jour une commande en cours à partir d’une application cliente.

## <a name="prerequisites"></a>Prérequis
> [!div class = "checklist"]
> * Application [Commandes personnalisées](quickstart-custom-commands-application.md) créée précédemment

## <a name="update-the-state-of-a-command"></a>Mettre à jour l’état d’une commande

Si votre application cliente nécessite la mise à jour de l’état d’une commande en cours sans entrée vocale, vous pouvez envoyer un événement pour mettre à jour la commande.

Pour illustrer ce scénario et mettre à jour l’état d’une commande en cours (TurnOnOff), nous pouvons envoyer l’activité d’événement suivante. 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Passons en revue les attributs clés de cette activité.

| Attribut | Explication |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | L’activité est de type « event », et le nom de l’événement doit être « RemoteUpdate ». |
| **value** | L’attribut « value » contient les attributs nécessaires pour mettre à jour la commande actuelle. |
| **updatedCommand** | L’attribut « updatedCommand » contient le nom de la commande, et « updatedParameters » représente un mappage incluant le nom des paramètres et leurs valeurs mises à jour. |
| **cancel** | Si la commande en cours doit être annulée, affectez la valeur true à l’attribut « cancel ». |
| **updatedGlobalParameters** | L’attribut « updatedGlobalParameters » est également un mappage, tout comme « updatedParameters », mais il est utilisé pour les paramètres globaux. |
| **processTurn** | Si le passage doit être traité après l’envoi de l’activité, la valeur true est affectée à l’attribut « processTurn ». |

Vous pouvez tester ce scénario dans le portail de l’application Commandes personnalisées.

1. Ouvrez l'application Commandes personnalisées que vous avez précédemment créée. 
1. Cliquez sur Entraîner, puis sur Tester.
1. Envoyez « turn ».
1. Ouvrez le panneau latéral, puis cliquez sur Éditeur d’activités.
1. Tapez et envoyez l’événement RemoteCommand spécifié dans la section précédente.
    > [!div class="mx-imgBorder"]
    > ![Envoyer une commande à distance](media/custom-commands/send-remote-command-activity.png)

Notez que la valeur du paramètre « OnOff » a été définie à « on » à l’aide d’une activité du client, et non à partir d’une entrée vocale ou de texte.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Mettre à jour le catalogue du paramètre d’une commande

Quand vous configurez la liste des options valides pour un paramètre, les valeurs du paramètre sont définies globalement pour l’application. 

Dans notre exemple, le paramètre SubjectDevice a une liste fixe de valeurs prises en charge, quelle que soit la conversation.

Si vous avez besoin d’ajouter de nouvelles entrées au catalogue des paramètres pour chaque conversation, vous pouvez envoyer l’activité suivante.

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Avec cette activité, nous avons ajouté une entrée pour « stereo » au catalogue du paramètre « SubjectDevice » dans la commande "TurnOnOff".

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Mettre à jour le catalogue":::

Notez quelques points.
1. Vous ne devez envoyer cette activité qu’une seule fois (dans l’idéal, juste après le démarrage d’une connexion).
1. Après avoir envoyé cette activité, vous devez attendre que l’événement ParameterCatalogsUpdated soit renvoyé au client.

## <a name="add-additional-context-from-the-client-application"></a>Ajouter un contexte supplémentaire à partir de l’application cliente

Vous pouvez définir un contexte supplémentaire à partir de l’application cliente pour chaque conversation. Vous pouvez ensuite utiliser ce contexte dans votre application Commandes personnalisées. 

Par exemple, pensez au scénario dans lequel vous souhaitez envoyer l’ID et le nom de l’appareil connecté à l’application Commandes personnalisées.

Pour tester ce scénario, créons une commande dans notre application actuelle.
1. Créez une commande appelée GetDeviceInfo.
1. Ajoutez l’exemple de phrase « get device info » (obtenir les informations de l’appareil).
1. Dans la règle de complétion « Terminé », ajoutez une action d’envoi de réponse vocale qui contient les attributs de clientContext.
    > ![Envoyer une réponse vocale avec un contexte](media/custom-commands/send-speech-response-context.png)
1. Enregistrez, entraînez et testez votre application.
1. Une fois dans la fenêtre de test, envoyez une activité pour mettre à jour le contexte du client.
    > ```json
    >{
    >   "type": "event",
    >   "name": "RemoteUpdate",
    >   "value": {
    >     "clientContext": {
    >       "deviceId": "12345",
    >       "deviceName": "My device"
    >     },
    >     "processTurn": false
    >   }
    >}
    > ```
1. Envoyez le texte « get device info ».
    > ![Envoyer l’activité liée au contexte du client](media/custom-commands/send-client-context-activity.png)

Notez quelques points.
1. Vous ne devez envoyer cette activité qu’une seule fois (dans l’idéal, juste après le démarrage d’une connexion).
1. Vous pouvez utiliser des objets complexes pour clientContext.
1. Vous pouvez utiliser clientContext dans les réponses vocales, pour l’envoi d’activités et durant l’appel de points de terminaison web.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Mettre à jour une commande à partir d’un point de terminaison web](./how-to-custom-commands-update-command-from-web-endpoint.md)
