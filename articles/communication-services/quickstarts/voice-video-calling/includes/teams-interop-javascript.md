---
title: 'Démarrage rapide : rejoindre une réunion Teams à partir d’une application web'
description: Dans ce tutoriel, vous allez découvrir comment rejoindre une réunion Teams à l’aide du kit de développement logiciel (SDK) Azure Communication Services Calling pour JavaScript.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6747d1d3cfba1c9e2bee7a8a7a48d67d6bed9f8e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564667"
---
Dans ce démarrage rapide, vous allez découvrir comment rejoindre une réunion Teams à l’aide du kit de développement logiciel (SDK) appelant Azure Communication Services Calling pour JavaScript.

## <a name="prerequisites"></a>Prérequis

- Une [application web appelant Communication Services](../getting-started-with-calling.md) qui fonctionne
- Un [déploiement de Teams](/deployoffice/teams-install)


## <a name="add-the-teams-ui-controls"></a>Ajouter les contrôles d’interface utilisateur de Teams

Remplacez le code dans index.html par l’extrait de code suivant.
La zone de texte servira à entrer le contexte de la réunion Teams, tandis que le bouton permettra de rejoindre la réunion spécifiée :

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
        <p><span style="font-weight: bold" id="recording-state"></span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Activer les contrôles d’interface utilisateur de Teams

Remplacez le contenu du fichier client.js par l’extrait de code suivant.

```javascript
import { CallClient } from "@azure/communication-calling";
import { Features } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');
const recordingStateElement = document.getElementById('recording-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })

    call.api(Features.Recording).on('isRecordingActiveChanged', () => {
        if (call.api(Features.Recording).isRecordingActive) {
            recordingStateElement.innerText = "This call is being recorded";
        }
        else {
            recordingStateElement.innerText = "";
        }
    });
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Obtenir le lien de réunion Teams

Le lien de réunion Teams peut être récupéré par le biais des API Graph. Cette procédure est détaillée dans la [documentation de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Le kit SDK Communication Services Calling accepte un lien de réunion Teams complet. Ce lien est retourné comme faisant partie de la ressource `onlineMeeting`, accessible sous la [propriété `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Vous pouvez également récupérer les informations de réunion nécessaires à partir de l’URL **Rejoindre la réunion** dans l’invite de réunion Teams elle-même.

## <a name="run-the-code"></a>Exécuter le code

Les utilisateurs de Webpack peuvent utiliser `webpack-dev-server` pour générer et exécuter votre application. Exécutez la commande suivante pour créer un bundle de votre application hôte sur un serveur web local :

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Ouvrez votre navigateur et accédez à http://localhost:8080/. Les éléments suivants doivent s’afficher :

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Capture d’écran de l’application JavaScript terminée.":::

Insérez le contexte Teams dans la zone de texte et appuyez sur *Join Teams Meeting* pour rejoindre la réunion Teams à partir de votre application Communication Services.
