---
title: Mettre à jour une commande à partir d’un point de terminaison web
titleSuffix: Azure Cognitive Services
description: mettre à jour une commande à partir d’un point de terminaison web
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94572635"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Mettre à jour une commande à partir d’un point de terminaison web

Si votre application cliente doit mettre à jour l’état d’une commande en cours sans entrée vocale, vous pouvez utiliser un appel à un point de terminaison web pour mettre à jour la commande.

Dans cet article, vous allez découvrir comment mettre à jour une commande en cours à partir d’un point de terminaison web.

## <a name="prerequisites"></a>Prérequis
> [!div class = "checklist"]
> * Application [Commandes personnalisées](quickstart-custom-commands-application.md) créée précédemment

## <a name="create-an-azure-function"></a>Création d’une fonction Azure 

Pour cet exemple, nous avons besoin d’une [fonction Azure](https://docs.microsoft.com/azure/azure-functions/) déclenchée par HTTP, qui prend en charge l’entrée suivante (ou un sous-ensemble de cette entrée).

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Passons en revue les attributs clés de cette entrée.

| Attribut | Explication |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | « conversationId » est l’identificateur unique de la conversation. Notez que cet ID peut être généré à partir de l’application cliente. |
| **currentCommand** | « currentCommand » est la commande active dans la conversation. |
| **name** | « name » correspond au nom de la commande, et « parameters » représente un mappage avec les valeurs actuelles des paramètres. |
| **currentGlobalParameters** | « currentGlobalParameters » est également un mappage tout comme « parameters », mais il est utilisé pour les paramètres globaux. |

La sortie de la fonction Azure doit prendre en charge le format suivant.

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Vous pouvez reconnaître ce format, car il est identique à celui utilisé durant la [mise à jour d’une commande à partir du client](./how-to-custom-commands-update-command-from-client.md). 

À présent, créez une fonction Azure basée sur NodeJS, puis copiez et collez ce code

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Quand nous appelons cette fonction Azure à partir de Commandes personnalisées, nous envoyons les valeurs actuelles de la conversation, puis nous retournons les paramètres souhaités pour une mise à jour ou une annulation de la commande actuelle.

## <a name="update-the-existing-custom-commands-app"></a>Mettre à jour l’application Commandes personnalisées existante

À présent, connectons la fonction Azure à l’application Commandes personnalisées existante.

1. Ajoutez une nouvelle commande nommée IncrementCounter.
1. Ajoutez simplement un exemple de phrase avec la valeur « increment ».
1. Ajoutez un nouveau paramètre appelé Counter (le même nom que celui spécifié dans la fonction Azure ci-dessus) de type Number avec la valeur par défaut 0.
1. Ajoutez un nouveau point de terminaison web appelé IncrementEndpoint en indiquant l’URL de votre fonction Azure et en activant les mises à jour distantes.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Définir le point de terminaison web avec des mises à jour distantes":::
1. Créez une règle d’interaction appelée « IncrementRule », puis ajoutez une action Appeler un point de terminaison web.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Règle d’incrémentation":::
1. Dans la configuration de l’action, sélectionnez IncrementEndpoint, configurez En cas de réussite pour envoyer une réponse vocale avec la valeur de Counter, puis configurez En cas d’échec en indiquant un message d’erreur.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Définir le point de terminaison d’appel du compteur d’incrémentation":::
1. Définissez l’état postexécution de la règle à Attendre l’entrée de l’utilisateur

## <a name="test-it"></a>Testez-le

1. Enregistrez et entraînez votre application
1. Cliquez sur Tester
1. Envoyez plusieurs fois « increment » (qui est l’exemple de phrase de la commande IncrementCounter)
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Exemple de compteur d’incrémentation":::

Notez que la valeur du paramètre Counter est incrémentée à chaque passage par la fonction Azure.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Activer un processus CI/CD pour votre application Commandes personnalisées](./how-to-custom-commands-deploy-cicd.md)