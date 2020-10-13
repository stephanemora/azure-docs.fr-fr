---
title: Utilisation des propriétés dans une solution Azure IoT Central
description: Comment utiliser les propriétés en lecture seule et accessibles en écriture dans la solution Azure IoT Central
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: fa9b07d80c34ec26ca920fe147ada8f8ef7f2fd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91338970"
---
# <a name="how-to-use-properties-in-an-azure-iot-central-solution"></a>Utilisation des propriétés dans une solution Azure IoT Central

Cet article explique comment utiliser les propriétés de l’appareil qui sont définies dans un modèle d’appareil dans votre application Azure IoT Central.

Les propriétés représentent des valeurs à un moment donné. Par exemple, un appareil peut utiliser une propriété pour indiquer la température cible qu’il tente d’atteindre. Les propriétés vous permettent également de synchroniser l’état entre votre appareil et votre application IoT Central.  Vous pouvez définir des propriétés accessibles en écriture à partir d’IoT Central.

Vous pouvez également définir des propriétés de cloud dans une application IoT Central. Les valeurs de propriétés de cloud ne sont jamais échangées avec un appareil et ne sont pas concernées par cet article.

## <a name="define-your-properties"></a>Définir vos propriétés

Les propriétés sont des champs de données qui représentent l’état de votre appareil. Utilisez les propriétés pour représenter l’état durable de l’appareil, par exemple l’état marche/arrêt d’un appareil. Les propriétés peuvent également représenter des propriétés d’appareil de base, telles que la version logicielle de l’appareil. Vous pouvez déclarer des propriétés comme étant accessibles en lecture seule ou en écriture.

La capture d’écran suivante montre une définition de propriété dans l’application Azure IoT Central.

![Définir une propriété](./media/howto-use-properties/property-definition.png)

Le tableau suivant décrit les paramètres de configuration d’une fonctionnalité de données de propriété :

| Champ           | Description                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nom d’affichage    | Nom complet de la valeur de propriété utilisée sur les tableaux de bord et les formulaires.                                                                                                                                                              |
| Nom            | Nom de la propriété. IoT Central génère une valeur pour ce champ à partir du nom d’affichage, mais vous pouvez choisir votre propre valeur si nécessaire. Ce champ doit être alphanumérique.                                                 |
| Type de fonctionnalité | Propriété.                                                                                                                                                                                                                          |
| Type sémantique   | Type sémantique de la propriété, telles que la température, l’état ou l’événement. Le choix du type sémantique détermine lequel des champs suivants est disponible.                                                                       |
| schéma          | Type de données de propriété, tel que double, chaîne ou vecteur. Les options disponibles sont déterminées par le type sémantique. Le schéma n’est pas disponible pour les types sémantiques d’événement et d’état.                                               |
| Inscriptible       | Si la propriété n’est pas inscriptible, l’appareil peut signaler des valeurs de propriété à IoT Central. Si la propriété est inscriptible, l’appareil peut signaler des valeurs de propriétés à IoT Central, et IoT Central peut envoyer des mises à jour de propriétés à l’appareil. |
| severity        | Disponible uniquement pour le type sémantique d’événement. Les gravités sont **Erreur**, **Information** ou **Avertissement**.                                                                                                                         |
| Valeurs d’état    | Disponible uniquement pour le type sémantique d’état. Définissez les valeurs d’état possibles, chacune ayant un nom d’affichage, un nom, un type d’énumération et une valeur.                                                                                   |
| Unité            | Unité pour la valeur de propriété, telle que **mph**, **%** ou **&deg;C**.                                                                                                                                                              |
| Unités d'affichage    | Unité d’affichage à utiliser sur les tableaux de bord et les formulaires.                                                                                                                                                                                    |
| Commentaire         | Commentaires sur la fonctionnalité de propriété.                                                                                                                                                                                        |
| Description     | Description de la fonctionnalité de propriété.                                                                                                                                                                                          |

Les propriétés peuvent également être définies dans une interface dans un modèle d’appareil comme indiqué ci-dessous :

``` json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

Cet exemple montre cinq propriétés, qui peuvent être liées à la définition de propriété dans l’interface utilisateur comme indiqué ci-dessous :

* `@type` pour spécifier le type de capacité : `Property`.
* `name` pour la valeur de propriété.
* `schema` pour spécifier le type de données de la propriété. Cette valeur peut être un type primitif, tel que double, entier, valeur booléenne ou chaîne. Les types d’objets complexes, les tableaux et les mappages sont également pris en charge.
* `writable`. Par défaut, les propriétés sont en lecture seule. Vous pouvez marquer une propriété comme étant accessible en écriture, à l’aide de ce champ.

Des champs facultatifs, tels que le nom d’affichage et la description, vous permettent d’ajouter des détails à l’interface et aux fonctionnalités.

Lorsque vous créez une propriété, vous pouvez spécifier des types de **schéma** complexes tels que Objet, Enum, etc.

![Ajouter une capacité](./media/howto-use-properties/property.png)

Lorsque vous sélectionnez le schéma complexe, par exemple **Objet**, vous devez également définir l’objet.

![Définir un objet](./media/howto-use-properties/object.png)

Le code suivant illustre la définition d’un type de propriété Objet. Cet objet a deux champs dont les types sont « string » et « integer » :

``` json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Implémenter des propriétés en lecture seule

Par défaut, les propriétés sont en lecture seule. Les propriétés en lecture seule signifient que l’appareil signale des mises à jour de valeurs de propriété à votre application IoT Central. Votre application IoT Central ne peut pas définir la valeur d’une propriété en lecture seule.

IoT Central utilise les jumeaux d’appareil pour synchroniser les valeurs de propriété entre l’appareil et l’application IoT Central. Les valeurs de propriété d’appareil utilisent les propriétés signalées du jumeau d’appareil. Pour plus d’informations, consultez la documentation relative aux [jumeaux d’appareil](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

L’extrait de code suivant d’un modèle de fonctions d’appareil affiche la définition d’un type de propriété en lecture seule :

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Les propriétés en lecture seule sont envoyées par l’appareil à IoT Central. Les propriétés sont envoyées en tant que charge utile JSON. Pour plus d’informations, consultez la documentation relative aux [charges utiles](./concepts-telemetry-properties-commands.md).

Vous pouvez utiliser l’Azure IoT device SDK pour envoyer une mise à jour de propriété à votre application IoT Central.

Les propriétés de jumeaux d’appareil peuvent être envoyées à votre application Azure IoT Central à l’aide de la fonction ci-dessous :

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

Cet article utilise Node.js pour des raisons de simplicité. Pour obtenir des informations complètes sur les exemples d’application d’appareil, consultez les tutoriels [Créer et connecter une application cliente à votre application Azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md) et [Créer et connecter une application cliente à votre application Azure IoT Central (Python)](tutorial-connect-device-python.md).

L’affichage suivant dans l’application Azure IoT Central montre les propriétés : vous pouvez voir que l’affichage fait automatiquement de la propriété Modèle de l’appareil une _propriété d’appareil en lecture seule_.

![Affichage de la propriété en lecture seule](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>Implémenter des propriétés accessibles en écriture

Les propriétés accessibles en écriture sont définies par un opérateur dans l’application IoT Central sur un formulaire. IoT Central envoie la propriété à l’appareil. IoT Central attend un accusé de réception de la part de l’appareil. 

L’extrait de code suivant d’un modèle de fonctions d’appareil affiche la définition d’un type de propriété accessible en écriture :

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Un client d’appareil doit envoyer une charge utile JSON ressemblant à l’exemple suivant en tant que propriété signalée dans le jumeau d'appareil :

``` json
{ "Brightness Level": 2 }
```

Pour définir et gérer les propriétés accessibles en écriture auxquelles votre appareil répond, vous pouvez utiliser le code suivant.

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

Le message de réponse doit inclure les champs `ac` et `av`. Le champ `ad` est facultatif. Pour obtenir des exemples, consultez les extraits de code suivants.

* `ac` est un champ numérique qui utilise les valeurs du tableau suivant :

* `av` est le numéro de version envoyé à l’appareil.

* `ad` est une description de chaîne d’option.

| Valeur | Étiquette | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Completed | L’opération de modification de la propriété a été effectuée. |
| `'ac': 202` ou `'ac': 201` | Pending | L’opération de modification de la propriété est en attente ou en cours |
| `'ac': 4xx` | Error | La modification de la propriété demandée n’est pas valide ou a rencontré une erreur |
| `'ac': 5xx` | Error | L’appareil a rencontré une erreur inattendue lors du traitement de la modification demandée. |


Pour plus d’informations, consultez la documentation relative aux [jumeaux d’appareil](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

Quand l’opérateur définit une propriété inscriptible dans l’application IoT Central, l’application utilise une propriété souhaitée du jumeau d’appareil pour envoyer la valeur à l’appareil. L’appareil répond alors en utilisant une propriété signalée du jumeau d’appareil. Quand IoT Central reçoit la valeur de la propriété rapportée, il met à jour l’affichage des propriétés avec l’état **Accepté**.

L’affichage suivant montre les propriétés accessibles en écriture. Lorsque vous entrez la valeur et **enregistrer**, l’état initial est **En attente** ; lorsque l’appareil accepte la modification, l’état devient **Accepté**.

![État en attente](./media/howto-use-properties/status-pending.png)

![Propriété acceptée](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser des propriétés dans votre application Azure IoT Central, vous pouvez consulter [Charges utiles](concepts-telemetry-properties-commands.md) et [Créer et connecter une application cliente à votre application Azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md).
