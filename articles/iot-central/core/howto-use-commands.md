---
title: Utilisation des commandes d'appareil dans une solution Azure IoT Central
description: Utilisation des commandes d'appareil dans une solution Azure IoT Central Ce tutoriel destiné aux développeurs d'appareils explique comment utiliser les commandes d'appareil d'une application cliente dans une application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e53bf377a7ef8f2293debd288ba25ef8f04ff4fc
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610996"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Utilisation des commandes dans une solution Azure IoT Central

Ce guide pratique destiné aux développeurs d'appareils explique comment utiliser les commandes définies dans un modèle d'appareil.

Un opérateur peut utiliser l'interface utilisateur d'IoT Central pour appeler une commande sur un appareil. Les commandes déterminent le comportement d'un appareil. Par exemple, un opérateur peut appeler une commande pour redémarrer un appareil ou collecter des données de diagnostic.

Un appareil peut :

* répondre immédiatement à une commande ;
* répondre à IoT Central lorsqu'il reçoit la commande, puis notifier IoT Central lorsque la *commande de longue durée* est terminée.

Par défaut, les commandes partent du principe qu'un appareil est connecté ; elles échouent si l'appareil est inaccessible. Si vous sélectionnez l'option **Mettre en file d'attente si hors connexion** dans l'interface utilisateur des modèles d'appareil, une commande peut être mise en file d'attente jusqu'à ce qu'un appareil se connecte. Ces *commandes hors connexion* sont décrites dans une section distincte plus loin dans cet article.

## <a name="define-your-commands"></a>Définir vos commandes

Les commandes standard sont envoyées à un appareil pour lui demander d'effectuer une opération. Une commande peut inclure des paramètres spécifiant des informations supplémentaires. Par exemple, une commande permettant d'ouvrir une vanne sur un appareil peut inclure un paramètre qui spécifie de combien la vanne doit être ouverte. Les commandes peuvent également recevoir une « valeur renvoyée » obtenue au terme de leur exécution. Par exemple, une commande qui demande à un appareil d'exécuter des diagnostics peut recevoir un rapport de diagnostic comme valeur renvoyée.

Les commandes sont définies dans le cadre d'un modèle d'appareil. La capture d'écran suivante illustre la définition de la commande de **rapport Get Max-Min** dans le modèle d'appareil **Thermostat**. Cette commande contient à la fois des paramètres de requête et de réponse : 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Capture d'écran de la commande de rapport Get Max Min dans le modèle d'appareil Thermostat":::

Le tableau suivant décrit les paramètres de configuration d’une fonctionnalité de commande :

| Champ             |Description|
|-------------------|-----------|
|Nom d’affichage       |Valeur de commande utilisée dans les tableaux de bord et formulaires.|
| Nom            | Nom de la commande. IoT Central génère une valeur pour ce champ à partir du nom d’affichage, mais vous pouvez choisir votre propre valeur si nécessaire. Ce champ doit être alphanumérique. Le code de l'appareil utilise cette valeur **Name**.|
| Type de fonctionnalité | Commande.|
| Mettre en file d'attente si hors connexion | Indique si cette commande doit être transformée en commande *hors connexion*. |
| Description     | Description de la fonctionnalité de commande.|
| Commentaire     | Commentaires sur la fonctionnalité de commande.|
| Requête     | Charge utile de la commande d'appareil.|
| response     | Charge utile de la réponse à la commande d'appareil.|

L'extrait de code suivant illustre la représentation JSON de la commande dans le modèle d'appareil. Dans cet exemple, la valeur de réponse est un type d'**objet** complexe comportant plusieurs champs :

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> Vous pouvez exporter un modèle d'appareil à partir de la page des modèles d'appareil.

Vous pouvez associer cette définition de commande à la capture d'écran de l'interface utilisateur en utilisant les champs suivants :

* `@type` pour spécifier le type de capacité : `Command`.
* `name` pour la valeur de commande.

Des champs facultatifs, tels que le nom d’affichage et la description, vous permettent d’ajouter des détails à l’interface et aux fonctionnalités.

## <a name="standard-commands"></a>Commandes standard

Cette section montre comment un appareil envoie une valeur de réponse dès qu'il reçoit la commande.

L'extrait de code suivant montre comment un appareil peut répondre à une commande en envoyant immédiatement un code de réussite :

> [!NOTE]
> Cet article utilise Node.js pour des raisons de simplicité. Pour obtenir d'autres exemples de langage, consultez le tutoriel [Créer et connecter une application cliente à votre application Azure IoT Central](tutorial-connect-device.md).

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

L'appel à `onDeviceMethod` configure la méthode `commandHandler`. Ce gestionnaire de commandes :

1. vérifie le nom de la commande ;
1. pour la commande `getMaxMinReport`, il appelle `getMaxMinReportObject` afin de récupérer les valeurs à inclure dans l'objet renvoyé ;
1. appelle `sendCommandResponse` pour renvoyer la réponse à IoT Central. Cette réponse comprend le code de réponse `200` qui indique que l'opération a abouti.

La capture d'écran suivante montre comment la réponse apparaît dans l'interface utilisateur d'IoT Central :

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="Capture d'écran montrant comment afficher la charge utile d'une commande standard":::

## <a name="long-running-commands"></a>Commandes de longue durée

Cette section montre comment un appareil peut retarder l'envoi d’une confirmation de commande.

L'extrait de code suivant montre comment un appareil peut implémenter une commande de longue durée :

> [!NOTE]
> Cet article utilise Node.js pour des raisons de simplicité. Pour obtenir d'autres exemples de langage, consultez le tutoriel [Créer et connecter une application cliente à votre application Azure IoT Central](tutorial-connect-device.md).

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

L'appel à `onDeviceMethod` configure la méthode `commandHandler`. Ce gestionnaire de commandes :

1. vérifie le nom de la commande ;
1. appelle `sendCommandResponse` pour renvoyer la réponse à IoT Central. Cette réponse comprend le code de réponse `202` qui indique que les résultats sont en attente ;
1. termine l'opération de longue durée ;
1. utilise une propriété rapportée qui porte le même nom que la commande pour indiquer à IoT Central que la commande est terminée.

La capture d'écran suivante montre comment la réponse à la commande apparaît dans l'interface utilisateur d'IoT Central lorsque celle-ci reçoit le code de réponse 202 :

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="Capture d'écran illustrant une réponse immédiate de l'appareil":::

La capture d'écran suivante présente l'interface utilisateur d'IoT Central lorsqu'elle reçoit la mise à jour de propriété qui indique que la commande est terminée :

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="Capture d'écran montrant la fin d'une commande de longue durée":::

## <a name="offline-commands"></a>Commandes hors connexion

Cette section montre comment un appareil gère une commande hors connexion. Si un appareil est connecté, il peut gérer la commande hors connexion dès sa réception. S'il est hors connexion, il gèrera la commande hors connexion lors de sa prochaine connexion à IoT Central. Les appareils ne peuvent pas transmettre de valeur renvoyée en réponse à une commande hors connexion.

> [!NOTE]
> Cet article utilise Node.js pour des raisons de simplicité.

La capture d'écran suivante illustre une commande hors connexion appelée **GenerateDiagnostics**. Le paramètre de requête est un objet doté d'une propriété date/heure appelée **StartTime** et d'une propriété d'énumération d'entiers appelée **Bank** :

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="Capture d'écran illustrant l'interface utilisateur pour une commande hors connexion":::

L'extrait de code suivant montre comment un client peut écouter les commandes hors connexion et afficher le contenu des messages :

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

La sortie de l'extrait de code précédent montre la charge utile avec les valeurs **StartTime** et **Bank**. La liste des propriétés comprend le nom de la commande dans l'élément de liste **method-name** :

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

> [!NOTE]
> La durée de vie par défaut des commandes hors connexion est de 24 heures, après quoi le message expire.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser des commandes dans votre application Azure IoT Central, reportez-vous à [Charges utiles](concepts-telemetry-properties-commands.md) pour en savoir plus sur les paramètres des commandes et à [Créer une application cliente et la connecter à votre application IoT Central Azure](tutorial-connect-device.md) pour bénéficier d'exemples de code complets dans différents langages.
