---
title: Présentation des modèles d’appareils dans Azure IoT Central | Microsoft Docs
description: Les modèles d’appareils Azure IoT Central vous permettent de spécifier le comportement des appareils connectés à votre application. Un modèle d’appareil spécifie les données de télémétrie, les propriétés et les commandes que l’appareil doit implémenter. Un modèle d’appareil définit également l’interface utilisateur pour l’appareil dans IoT Central tels que les formulaires et les tableaux de bord utilisés par un opérateur.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 75317b5c6af2d0ce89d2db32f4343d9cc73a1a81
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91813166"
---
# <a name="what-are-device-templates"></a>Que sont les modèles d’appareils ?

_Cet article s’applique aux développeurs d’appareils et aux créateurs de solutions._

Un modèle d’appareil dans Azure IoT Central est un blueprint qui définit les caractéristiques et comportements d’un type d’appareil se connectant à votre application. Par exemple, le modèle d’appareil définit la télémétrie qu’un appareil envoie afin qu’IoT Central puisse créer des visualisations qui utilisent les unités et types de données appropriés.

Un générateur de solutions ajoute des modèles d’appareils à une application IoT Central. Un développeur d’appareils écrit le code d’appareil qui implémente les comportements définis dans le modèle d’appareil.

Un modèle d’appareil comprend les sections suivantes :

- _Un modèle de fonctionnalité d’appareil (DCM)_ Cette partie du modèle d’appareil définit la manière dont l’appareil interagit avec votre application. Un développeur d’appareils implémente les comportements définis dans le DCM.
    - _Interfaces_. Un DCM contient une ou plusieurs interfaces qui définissent les données de télémétrie, les propriétés et les commandes que l’appareil doit implémenter.
- _Propriétés Cloud_. Cette partie du modèle d’appareil permet au développeur de solutions de spécifier toutes les métadonnées de l’appareil à stocker. Les propriétés Cloud ne sont jamais synchronisées avec les appareils et n’existent que dans l’application. Les propriétés Cloud n’affectent pas le code écrit par un développeur d’appareils pour implémenter le DCM.
- _Personnalisations_. Cette partie du modèle d’appareil permet au développeur de solutions de remplacer certaines définitions dans le DCM. Des personnalisations sont utiles si le développeur de solutions souhaite affiner la façon dont l’application gère une valeur, par exemple, en modifiant le nom d’affichage d’une propriété ou de la couleur utilisée pour afficher une valeur de télémétrie. Les personnalisations n’affectent pas le code qu’un développeur d’appareils a écrit pour implémenter le DCM.
- _Affichages_. Cette partie du modèle d’appareil permet au développeur de solutions de définir des visualisations pour afficher les données de l’appareil, ainsi que des formulaires pour gérer et contrôler un appareil. Les affichages utilisent le DCM, les propriétés Cloud et les personnalisations. Les vues n’affectent pas le code qu’un développeur d’appareils a écrit pour implémenter le DCM.

## <a name="device-capability-models"></a>Modèles de capacité d’appareil

Un modèle de capacité d’appareil (DCM) définit la manière dont un appareil interagit avec votre application IoT Central. Le développeur de l’appareil doit s’assurer que l’appareil implémente les comportements définis dans le DCM afin qu’IoT Central puisse surveiller et gérer l’appareil. Un DCM est constitué d’une ou plusieurs _interfaces_ pouvant chacune définir une collection de types de _télémétries_, des _propriétés d’appareil_ et des _commandes_. Un développeur de solutions peut importer un fichier JSON définissant le DCM dans un modèle d’appareil, ou utiliser l’interface utilisateur web dans IoT Central pour créer ou modifier un DCM. Les modifications apportées à un DCM à l’aide de l’interface utilisateur web nécessitent que le modèle d’appareil fasse l’objet d’une [gestion des versions](./howto-version-device-template.md).

Un développeur de solutions peut également exporter un fichier JSON contenant le DCM. Un développeur d’appareils peut utiliser ce document JSON pour comprendre comment l’appareil doit communiquer avec l’application IoT Central.

Le fichier JSON qui définit le DCM utilise le [langage DTDL (Digital Twin Definition Language) v1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT Central s’attend à ce que le fichier JSON contienne le DCM avec les interfaces définies incluses plutôt que dans des fichiers distincts.

Un appareil IoT classique est constitué des éléments suivants :

- Des parties personnalisées, qui rendent votre appareil unique.
- Des parties standard, communes à tous les appareils.

Ces éléments sont appelés _interfaces_ dans un DCM. Les interfaces définissent les détails de chaque partie implémentée par votre appareil. Les interfaces sont réutilisables entre DCM.

L’exemple suivant montre le plan du modèle de capacité d’appareil pour un capteur d’environnement avec deux interfaces :

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Un modèle de capacité comporte certains champs obligatoires :

- `@id` : ID unique sous la forme d’un nom de ressource uniforme simple.
- `@type` : déclare que cet objet est un modèle de capacité.
- `@context` : spécifie la version DTDL utilisée pour le modèle de capacité.
- `implements` : liste les interfaces implémentées par votre appareil.

Chaque entrée de la liste d’interfaces dans la section implements comprend :

- `name` : nom de programmation de l’interface.
- `schema` : interface implémentée par le modèle de capacité.

Une interface contient certains champs obligatoires :

- `@id` : ID unique sous la forme d’un nom de ressource uniforme simple.
- `@type` : déclare que cet objet est une interface.
- `@context` : spécifie la version DTDL utilisée pour l’interface.
- `contents` : liste les propriétés, données de télémétrie et commandes qui composent votre appareil.

Il existe des champs facultatifs que vous pouvez utiliser pour ajouter des détails au modèle de capacité, tels que le nom d’affichage et la description.

## <a name="interfaces"></a>Interfaces

Le DTDL vous permet de décrire les fonctionnalités de votre appareil. Les fonctionnalités associées sont regroupées dans des interfaces. Les interfaces décrivent les propriétés, la télémétrie et les commandes qu’une partie de votre appareil implémente :

- `Properties`. Les propriétés sont des champs de données qui représentent l’état de votre appareil. Utilisez les propriétés pour représenter l’état durable de l’appareil, par exemple l’état marche/arrêt d’une pompe de refroidissement. Les propriétés peuvent également représenter des propriétés d’appareil de base, telles que la version du microprogramme de l’appareil. Vous pouvez déclarer des propriétés comme étant accessibles en lecture seule ou en écriture. Seuls les appareils peuvent mettre à jour la valeur d’une propriété en lecture seule. Un opérateur peut définir la valeur d’une propriété accessible en écriture à envoyer à un appareil.
- `Telemetry`. Les champs de télémétrie représentent les mesures des capteurs. Chaque fois que votre appareil prend une mesure de capteur, il doit envoyer un événement de télémétrie contenant les données de capteur.
- `Commands`. Les commandes représentent les méthodes que les utilisateurs de votre appareil peuvent exécuter sur l’appareil. Par exemple, une commande de réinitialisation ou une commande pour allumer ou éteindre un ventilateur.

L’exemple suivant illustre la définition d’interface de capteur d’environnement :

```json
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
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

Cet exemple montre deux propriétés (une en lecture seule et une autre accessible en écriture), un type de télémétrie et deux commandes. Une description de champ minimale comprend :

- `@type` pour spécifier le type de fonctionnalité : `Telemetry`, `Property` ou `Command`.  Dans certains cas, le type comprend un type sémantique pour permettre à IoT Central de faire des hypothèses sur la façon de gérer la valeur.
- `name` pour la valeur de télémétrie.
- `schema` pour spécifier le type de données pour la télémétrie ou la propriété. Cette valeur peut être un type primitif, tel que double, entier, valeur booléenne ou chaîne. Les types d’objets complexes, les tableaux et les mappages sont également pris en charge.
- `commandType` pour spécifier le mode de gestion de la commande.

Des champs facultatifs, tels que le nom d’affichage et la description, vous permettent d’ajouter des détails à l’interface et aux fonctionnalités.

## <a name="properties"></a>Propriétés

Par défaut, les propriétés sont en lecture seule. Les propriétés en lecture seule signifient que l’appareil signale des mises à jour de valeurs de propriété à votre application IoT Central. Votre application IoT Central ne peut pas définir la valeur d’une propriété en lecture seule.

Vous pouvez également marquer une propriété comme étant accessible en écriture sur une interface. Un appareil peut recevoir de votre application IoT Central une mise à jour de propriété accessible en écriture ainsi que signaler des mises à jour de valeurs de propriété à votre application.

Les appareils n’ont pas besoin d’être connectés pour définir des valeurs de propriété. Les valeurs mises à jour sont transférées quand l’appareil se connecte ensuite à l’application. Ce comportement s’applique aux propriétés en lecture seule et en écriture.

N’utilisez pas de propriétés pour envoyer des données de télémétrie à partir de votre appareil. Par exemple, une propriété en lecture seule telle que `temperatureSetting=80` doit signifier que la température de l’appareil a été définie sur 80 et que l’appareil tente d’atteindre cette température ou d’y rester.

Pour les propriétés accessibles en écriture, l’application d’appareil retourne un code d’état, une version et une description de l’état souhaité pour indiquer si elle a reçu et appliqué la valeur de la propriété.

## <a name="telemetry"></a>Télémétrie

IoT Central vous permet d’afficher une télémétrie sur des tableaux de bord et des graphiques, ainsi que d’utiliser des règles pour déclencher des actions lorsque certains seuils sont atteints. IoT Central utilise les informations du DCM, telles que les types de données, les unités et les noms d’affichage, pour déterminer la manière d’afficher les valeurs de télémétrie.

La fonctionnalité d’exportation de données d’IoT Central permet de diffuser en continu la télémétrie vers d’autres destinations, telles que le stockage ou Event Hubs.

## <a name="commands"></a>Commandes

Les commandes sont synchrones ou asynchrones. Une commande synchrone doit être exécutée dans un délai de 30 secondes par défaut, et l’appareil doit être connecté quand la commande arrive. Si l’appareil ne répond pas à temps ou qu’il n’est pas connecté, la commande échoue.

Utilisez des commandes asynchrones pour les opérations de longue durée. L’appareil envoie des informations de progression à l’aide de messages de télémétrie. Ces messages de progression ont les propriétés d’en-tête suivantes :

- `iothub-command-name` : nom de la commande, par exemple `UpdateFirmware`.
- `iothub-command-request-id` : ID de la demande généré côté serveur et envoyé à l’appareil lors de l’appel initial.
- `iothub-interface-id`:  ID de l’interface sur laquelle cette commande est définie, par exemple `urn:example:AssetTracker:1`.
 `iothub-interface-name` : nom de l’instance de cette interface, par exemple `myAssetTracker`.
- `iothub-command-statuscode` : code d’état retourné par l’appareil, par exemple `202`.

## <a name="cloud-properties"></a>Propriétés du cloud

Les propriétés Cloud font partie du modèle d’appareil, mais pas du DCM. Les propriétés Cloud permettent au développeur de solutions de spécifier les métadonnées d’appareil à stocker dans l’application IoT Central. Les propriétés Cloud n’affectent pas le code écrit par un développeur d’appareils pour implémenter le DCM.

Un développeur de solutions peut ajouter des propriétés Cloud à des tableaux de bord et des affichages, en même temps que des propriétés de l’appareil pour permettre à un opérateur de gérer les appareils connectés à l’application. Un développeur de solutions peut également utiliser des propriétés Cloud dans le cadre d’une définition de règle pour rendre une valeur de seuil modifiable par un opérateur.

## <a name="customizations"></a>Personnalisations

Les personnalisations font partie du modèle d’appareil, mais pas du DCM. Les personnalisations permettent au développeur de solutions d’améliorer ou de remplacer certaines définitions dans le DCM. Par exemple, un développeur de solutions peut modifier le nom d’affichage d’un type ou d’une propriété de télémétrie. Un développeur de solutions peut également utiliser des personnalisations pour ajouter une validation telle qu’une longueur minimale ou maximale pour une propriété d’appareil de type chaîne.

Les personnalisations peuvent affecter le code qu’un développeur d’appareils écrit pour implémenter le DCM. Par exemple, une personnalisation peut définir des longueurs de chaîne minimales et maximales ou des valeurs numériques minimales et maximales pour la télémétrie.

## <a name="views"></a>Les vues

Un développeur de solutions crée des affichages permettant aux opérateurs de surveiller et de gérer les appareils connectés. Les affichage faisant partie du modèle d’appareil, ils sont associés à un type d’appareil spécifique. Un affichage peut inclure les éléments suivants :

- Graphiques pour tracer la télémétrie.
- Vignettes pour afficher les propriétés d’appareil en lecture seule.
- Vignettes pour permettre à l’opérateur de modifier des propriétés d’appareil accessibles en écriture.
- Vignettes pour permettre à l’opérateur de modifier des propriétés Cloud.
- Vignettes pour permettre à l’opérateur d’appeler des commandes, y compris des commandes qui attendent une charge utile.
- Vignettes pour afficher des étiquettes, des images ou un texte Markdown.

La télémétrie, les propriétés et les commandes que vous pouvez ajouter à un affichage sont déterminées par le DCM, des propriétés Cloud et des personnalisations dans le modèle d’appareil.

## <a name="next-steps"></a>Étapes suivantes

En tant que développeur d’appareils, maintenant que vous avez découvert les modèles d’appareils, une prochaine étape suggérée consiste à lire [Charges utiles de télémétrie, de propriété et de commande](./concepts-telemetry-properties-commands.md) pour en savoir plus sur les données qu’un appareil échange avec IoT Central.

En tant que développeur de solutions, nous vous suggérons de lire [Définir un nouveau type d’appareil IoT dans votre application Azure IoT Central](./howto-set-up-template.md) pour en savoir plus sur la création d’un modèle d’appareil.
