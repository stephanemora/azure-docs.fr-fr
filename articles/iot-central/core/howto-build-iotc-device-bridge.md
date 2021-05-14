---
title: Déployer le pont d’appareil Azure IoT Central | Microsoft Docs
description: Déployer le pont d’appareil IoT Central pour connecter d’autres clouds IoT à votre application IoT Central. Les autres clouds IoT incluent Sigfox, Particle Device Cloud et The Things Network.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 6b535ecb80fae9f55eb6ab11751c26e0c6d0e9e5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713713"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Utiliser le pont d’appareil IoT Central pour connecter d’autres clouds IoT à IoT Central

*Cet article concerne les administrateurs.*

## <a name="azure-iot-central-device-bridge"></a>Pont d’appareil Azure IoT Central

Le pont d’appareil IoT Central est une solution open source qui connecte d’autres clouds IoT à votre application IoT Central. [Sigfox](https://www.sigfox.com/), [Particle Device Cloud](https://www.particle.io/) et [The Things Network](https://www.thethingsnetwork.org/) sont des exemples d’autres clouds IoT. Le pont d’appareil fonctionne en transférant les données des appareils connectés à d’autres clouds IoT par le biais de votre application IoT Central. Le pont d’appareil transfère uniquement les données à IoT Central. Il n’envoie pas de commandes ou de mises à jour de propriétés d’IoT Central vers les appareils.

Le pont d’appareil vous permet de combiner la puissance d’IoT Central avec des appareils tels que les appareils de suivi d’actifs connectés au réseau étendu à faible consommation de Sigfox, les appareils de surveillance de la qualité de l’air sur Particle Device Cloud ou les appareils de surveillance de l’humidité du sol sur The Things Network. Vous pouvez utiliser les fonctionnalités d’application IoT Central telles que les règles et les analyses sur les données, créer des workflows dans Power Automate et Azure Logic Apps, ou exporter les données.

La solution de pont d’appareil provisionne plusieurs ressources Azure dans votre abonnement Azure qui fonctionnent ensemble pour transformer et transférer des messages d’appareil à IoT Central.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes décrites dans ce guide pratique, vous avez besoin d’un abonnement Azure actif.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Suivez le guide de démarrage rapide [Créer une application Azure IoT Central](./quick-deploy-iot-central.md) pour créer une application IoT Central avec le modèle **Application personnalisée > Application personnalisée**.

## <a name="overview"></a>Vue d’ensemble

Le pont d’appareil IoT Central est une solution open source dans GitHub. Il utilise un modèle Azure Resource Manager personnalisé pour déployer plusieurs ressources dans votre abonnement Azure, notamment une application de fonction Azure.

L’application de fonction est l’élément principal du pont d’appareil. Elle reçoit les requêtes HTTP POST d’autres plateformes IoT via un webhook simple. Le dépôt [Azure IoT Central Device Bridge](https://github.com/Azure/iotc-device-bridge) contient des exemples qui montrent comment connecter les clouds Sigfox, Particle et The Things Network. Vous pouvez étendre cette solution pour vous connecter à votre cloud IoT personnalisé si votre plateforme peut envoyer des requêtes HTTP POST à votre application de fonction.

L’application de fonction transforme les données dans un format accepté par IoT Central et les transfère à l’aide des API du service Device Provisioning et du client d’appareil :

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Capture d’écran d’Azure Functions.":::

Si votre application IoT Central reconnaît l’ID d’appareil dans le message transféré, les données de télémétrie de l’appareil s’affichent dans IoT Central. Si l’ID d’appareil n’est pas reconnu par votre application IoT Central, l’application de fonction tente d’enregistrer un nouvel appareil avec l’ID d’appareil. Le nouvel appareil apparaît comme un **appareil non associé** sur la page **Appareils** de votre application IoT Central. À partir de la page **Appareils**, vous pouvez associer le nouvel appareil à un modèle d’appareil, puis afficher les données de télémétrie.

## <a name="deploy-the-device-bridge"></a>Déployer le pont d’appareil

Pour déployer le pont d’appareil dans votre abonnement :

1. Dans votre application IoT Central, accédez à la page **Administration > Connexion de l’appareil**.

    1. Notez l’**étendue de l’ID**. Vous utilisez cette valeur lorsque vous déployez le pont d’appareil.

    1. Dans la même page, ouvrez le groupe d’inscription **SAS-IoT-Devices** . Sur la page du groupe **SAS-IoT-Devices**, copiez la **clé primaire**. Vous utilisez cette valeur lorsque vous déployez le pont d’appareil.

1. Utilisez le bouton **Déployer sur Azure** ci-dessous pour ouvrir le modèle Resource Manager personnalisé qui déploie l’application de fonction dans votre abonnement. Utilisez l’**étendue d’ID** et la **clé primaire** de l’étape précédente :

    [![Déployer sur Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

Une fois le déploiement terminé, vous devez installer les packages NPM requis par la fonction :

1. Dans le portail Azure, ouvrez l’application de fonction qui a été déployée sur votre abonnement. Accédez ensuite à **Outils de développement > Console**. Dans la console, exécutez les commandes suivantes pour installer les packages :

    ```bash
    cd IoTCIntegration
    npm install
    ```

    L’exécution de ces commandes peut prendre plusieurs minutes. Vous pouvez ignorer les messages d’avertissement qui peuvent s’afficher.

1. Une fois l’installation du package terminée, sélectionnez **Redémarrer** sur la page **Présentation** de l’application de fonction :

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="Capture d’écran du redémarrage.":::

1. La fonction est maintenant prête pour utilisation. Les systèmes externes peuvent utiliser des requêtes HTTP POST pour envoyer des données d’appareil via le pont d’appareil dans votre application IoT Central. Pour obtenir l’URL de la fonction, accédez à **Fonctions > IoTCIntegration > Code + Test > Obtenir l’URL de la fonction** :

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="Capture d’écran de l’option Obtenir l’URL de la fonction.":::

Les corps de messages envoyés au pont d’appareil doivent avoir le format suivant :

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

Chaque clé de l’objet `measurements` doit correspondre au nom d’un type de données de télémétrie dans le modèle d’appareil de l’application IoT Central. Cette solution ne prend pas en charge la spécification de l’ID d’interface dans le corps du message. Par conséquent, si deux interfaces différentes ont un type de télémétrie portant le même nom, la mesure apparaît dans les deux flux de données de télémétrie de votre application IoT Central.

Vous pouvez inclure un champ `timestamp` dans le corps pour spécifier la date et l’heure UTC du message. Ce champ doit être au format ISO 8601. Par exemple : `2020-06-08T20:16:54.602Z`. Si vous n’incluez pas de timestamp, la date et l’heure actuelles sont utilisées.

Vous pouvez inclure un champ `modelId` dans le corps. Utilisez ce champ pour associer l’appareil à un modèle d’appareil pendant le provisionnement. Cette fonctionnalité est uniquement prise en charge par les [applications v3](howto-get-app-info.md).

L’élément `deviceId` doit être alphanumérique, en minuscules et peut contenir des traits d’union.

Si vous n’incluez pas le champ `modelId`, ou si IoT Central ne reconnaît pas l’ID du modèle, un message avec un `deviceId` non reconnu crée un nouvel _appareil non associé_ dans IoT Central. Un opérateur peut migrer manuellement l’appareil vers le modèle d’appareil approprié. Pour plus d’informations, consultez [Gérer les appareils dans votre application Azure IoT Central > Migration d’appareils vers un modèle](howto-manage-devices.md).

Dans les [applications v2](howto-get-app-info.md), le nouvel appareil apparaît sur la page **Explorateur d’appareils > Appareil non associés**. Sélectionnez **Associer** et choisissez un modèle d’appareil pour commencer à recevoir les données de télémétrie entrantes de l’appareil.

> [!NOTE]
> Tant que l’appareil n’est pas associé à un modèle, tous les appels HTTP à la fonction retournent un état d’erreur 403.

Pour activer la journalisation de l’application de fonction avec Application Insights, accédez à **Surveillance > Journaux** dans votre application de fonction dans le portail Azure. Sélectionnez **Activer Application Insights**.

## <a name="provisioned-resources"></a>Ressources provisionnées

Le modèle Resource Manager provisionne les ressources suivantes dans votre abonnement Azure :

* Function App
* Plan App Service
* Compte de stockage
* Coffre de clés

Le coffre de clés stocke la clé de groupe SAS pour votre application IoT Central.

L’application de fonction s’exécute sur un [plan de consommation](https://azure.microsoft.com/pricing/details/functions/). Bien que cette option n’offre pas de ressources de calcul dédiées, elle permet au pont d’appareil de gérer des centaines de messages d’appareil par minute, ce qui convient aux plus petites flottes d’appareils ou aux appareils qui envoient des messages moins fréquemment. Si votre application dépend du streaming d’un grand nombre de messages d’appareil, remplacez le plan de consommation par un [plan App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) dédié. Ce plan offre des ressources de calcul dédiées, qui fournissent des temps de réponse plus rapides au serveur. En utilisant un plan App Service standard, les performances observées maximales de la fonction Azure dans ce dépôt étaient d’environ 1 500 messages d’appareil par minute. Pour en savoir plus, consultez [Options d’hébergement Azure Functions](../../azure-functions/functions-scale.md).

Pour utiliser un plan App Service dédié au lieu d’un plan de consommation, modifiez le modèle personnalisé avant de procéder au déploiement. Sélectionnez **Modifier un modèle**.

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="Capture d’écran de l’option Modifier un modèle.":::

Remplacez le segment suivant :

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

par

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

Ensuite, modifiez le modèle de façon à inclure `"alwaysOn": true` dans la configuration de la ressource `functionapp` sous `"properties": {"SiteConfig": {...}}`. La [configuration AlwaysOn](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan) permet de garantir que l’application de fonction est toujours en cours d’exécution.

## <a name="examples"></a>Exemples

Les exemples suivants montrent comment configurer le pont d’appareil pour différents clouds IoT :

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>Exemple 1 : connexion d’appareils Particle via le pont d’appareil

Pour connecter un appareil Particle via le pont d’appareil à IoT Central, accédez à la console Particle et créez une intégration de webhook. Définissez le **format de demande** sur **JSON**.  Sous **Paramètres avancés**, utilisez le format de corps personnalisé suivant :

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": {{{PARTICLE_EVENT_VALUE}}}
  }
}
```

Collez l’**URL de fonction** de votre application de fonction Azure. Les appareils Particle apparaissent alors comme des appareils non associés dans IoT Central. Pour plus d’informations, consultez le billet de blog sur [l’intégration de vos projets Particle avec Azure IoT Central](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/).

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>Exemple 2 : connexion d’appareils Sigfox via le pont d’appareil

Certaines plateformes peuvent ne pas vous autoriser à spécifier le format des messages d’appareil envoyés via un webhook. Pour ces systèmes, vous devez convertir la charge utile de message au format de corps attendu avant que le pont d’appareil ne la traite. Vous pouvez effectuer la conversion dans la fonction Azure qui exécute le pont d’appareil.

Cette section montre comment convertir la charge utile d’une intégration de webhook Sigfox au format de corps attendu par le pont d’appareil. Le cloud Sigfox transmet les données des appareils dans un format de chaîne hexadécimal. Par commodité, le pont d’appareil inclut une fonction de conversion pour ce format, qui accepte un sous-ensemble des types de champs possibles dans une charge utile d’appareil Sigfox : champs `int` et `uint` de 8, 16, 32 ou 64 bits ; champs `float` de 32 bits ou 64 bits, champs little-endian et big-endian. Pour traiter les messages d’une intégration de webhook Sigfox, apportez les modifications suivantes au fichier _IoTCIntegration/index.js_ dans l’application de fonction.

Pour convertir la charge utile de message, ajoutez le code suivant avant l’appel à `handleMessage` sur la ligne 21, en remplaçant `payloadDefinition` par votre définition de charge utile Sigfox :

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

Les appareils Sigfox attendent un code de réponse `204`. Ajoutez le code suivant après l’appel à `handleMessage` sur la ligne 21 :

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>Exemple 3 : connexion d’appareils de The Things Network via le pont d’appareil

Pour connecter des appareils The Things Network à IoT Central :

* Ajoutez une nouvelle intégration HTTP à votre application dans The Things Network: **Application > Intégrations > Ajouter une intégration > Intégration HTTP**.
* Assurez-vous que votre application inclut une fonction de décodeur qui convertit automatiquement la charge utile de vos messages d’appareil en JSON avant qu’elle ne soit envoyée à la fonction Azure : **Application > Charge utile > Décodeur**.

L’exemple suivant montre une fonction de décodage JavaScript que vous pouvez utiliser pour décoder des types numériques courants à partir de données binaires :

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

Après avoir défini l’intégration, ajoutez le code suivant avant l’appel à `handleMessage` sur la ligne 21 du fichier *IoTCIntegration/index.js* de votre application de fonction Azure. Ce code convertit le corps de votre intégration HTTP au format attendu.

```javascript
device: {
    deviceId: req.body.hardware_serial.toLowerCase()
},
measurements: req.body.payload_fields
};
```

## <a name="limitations"></a>Limites

Le pont d’appareil transfère uniquement les messages à IoT Central et ne renvoie pas de messages aux appareils. C’est pourquoi, les propriétés et les commandes ne fonctionnent pas pour les appareils qui se connectent à IoT Central via ce pont d’appareil. Étant donné que les opérations de représentation d’appareil ne sont pas prises en charge, il n’est pas possible de mettre à jour les propriétés des appareils via le pont d’appareil. Pour utiliser ces fonctionnalités, un appareil doit se connecter directement à IoT Central à l’aide de l’un des [Azure IoT device SDK](../../iot-hub/iot-hub-devguide-sdks.md).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez comment déployer le pont d’appareil IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Gestion de vos appareils](howto-manage-devices.md)
