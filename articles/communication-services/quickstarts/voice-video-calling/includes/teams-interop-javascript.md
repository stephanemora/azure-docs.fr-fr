---
title: Démarrage rapide - Participer à une réunion Teams
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 820659c513674dc04e914c8f1094afab4f5a89e2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356458"
---
## <a name="prerequisites"></a>Prérequis

- Une [application d’appel Communication Services](../getting-started-with-calling.md) opérationnelle
- Un [déploiement de Teams](/deployoffice/teams-install)

## <a name="enable-teams-interoperability"></a>Activer l’interopérabilité de Teams

La fonctionnalité d’interopérabilité de Teams est disponible en préversion privée. Afin d’activer cette fonctionnalité pour votre ressource Communication Services, envoyez un e-mail à [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) avec les éléments suivants :

1. L’ID de l’abonnement Azure qui contient votre ressource Communication Services
2. Votre ID de locataire Teams Le moyen le plus simple de le récupérer consiste à [obtenir et partager un lien vers l’équipe](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f).

Vous devez être membre de l’organisation propriétaire des deux entités pour pouvoir utiliser cette fonctionnalité.

## <a name="add-the-teams-ui-controls"></a>Ajouter les contrôles d’interface utilisateur de Teams

Ajoutez un nouveau bouton et une nouvelle zone de texte dans votre code HTML. La zone de texte servira à entrer le contexte de la réunion Teams, tandis que le bouton permettra de rejoindre la réunion spécifiée :

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Activer les contrôles d’interface utilisateur de Teams

Nous pouvons maintenant lier le bouton **Join Teams Meeting** au code qui permet de rejoindre la réunion Teams fournie :

```javascript
meetingButton.addEventListener("click", () => {
    
    // set display name in the meeting
    callAgent.updateDisplayName('YOUR_NAME');
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>Obtenir le contexte de la réunion

Le contexte Teams peut être récupéré à l’aide des API Graph. Cette procédure est détaillée dans la [documentation de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).

Vous pouvez également récupérer les informations de réunion requises à partir de l’URL **Rejoindre la réunion** disponible dans l’invitation à la réunion.

## <a name="run-the-code"></a>Exécuter le code

Les utilisateurs de Webpack peuvent utiliser `webpack-dev-server` pour générer et exécuter votre application. Exécutez la commande suivante pour créer un bundle de votre application hôte sur un serveur web local :

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Ouvrez votre navigateur et accédez à http://localhost:8080/. Les éléments suivants doivent s’afficher :

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Capture d’écran de l’application JavaScript terminée.":::

Insérez le contexte Teams dans la zone de texte et appuyez sur *Join Teams Meeting* pour rejoindre la réunion Teams à partir de votre application Communication Services.