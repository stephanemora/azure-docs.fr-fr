---
title: Exporter des données de IoT Central | Microsoft Docs
description: Comment utiliser la nouvelle exportation de données pour exporter vos données IoT vers Azure et des destinations cloud personnalisées.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: f51630154b77233aeb2587ac3a2d603c1da6fa4f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036553"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>Exporter des données IoT vers des destinations cloud à l’aide de l’exportation de données (préversion)

> [!Note]
> Vous recherchez l’exportation de données héritée ? Vous pouvez trouver les documents sur l’exportation de données [ici](./howto-export-data.md). Pour en savoir plus sur les différences entre la nouvelle exportation de données et l’exportation de données héritée, consultez le [tableau de comparaison](#comparison-of-legacy-data-export-and-new-data-export).

Cet article explique comment utiliser les fonctionnalités d’évaluation de la nouvelle exportation de données dans Azure IoT Central. Vous pouvez utiliser cette fonctionnalité pour une exportation continue de vos données IoT filtrées et enrichies dans vos services cloud. Vous pouvez utiliser l’exportation de données pour envoyer (push) des modifications en quasi-temps réel vers d’autres parties de votre solution cloud pour des Insights, des analyses et un stockage de voie d’accès modéré. 

 Vous pouvez par exemple :
-   appliquer une exportation continue des données de télémétrie et des modifications de propriété au format JSON en quasi-temps réel
-   filtrer ces flux de données pour exporter des fonctionnalités spécifiques qui correspondent à des conditions personnalisées
-   enrichir les flux de données avec des valeurs personnalisées et des valeurs de propriété à partir de l’appareil
-   envoyer ces données à des destinations telles qu’Azure Event Hubs, Azure Service Bus, le Stockage Blob Azure et les points de terminaison webhook

> [!Note]
> Quand vous activez l’exportation de données, vous obtenez seulement les données à partir de ce moment. Pour le moment, vous ne pouvez pas récupérer les données d’une période pendant laquelle l’exportation de données était désactivée. Pour conserver un historique des données plus étendu, activez l’exportation de données au plus tôt.

## <a name="prerequisites"></a>Prérequis

Pour utiliser l’exportation de données (préversion), vous devez disposer d’une application V3 ainsi que d’autorisations d’exportation de données.

## <a name="set-up-export-destination"></a>Définir la destination de l’exportation

Votre destination d’exportation doit exister avant la configuration de l’exportation de données. Voici les types de destinations disponibles :
  - Hubs d'événements Azure
  - File d’attente Azure Service Bus
  - Rubrique Azure Service Bus
  - Stockage Blob Azure
  - webhook

### <a name="create-an-event-hubs-destination"></a>Créer une destination Event Hubs

En l’absence d’espace de noms Event Hubs existant vers lequel exporter, suivez ces étapes :

1. Créez un [espace de noms Event Hubs dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Pour en savoir plus, consultez les [documents sur Azure Event Hubs](../../event-hubs/event-hubs-create.md).

2. Créez un Event Hub dans votre espace de noms Event Hubs. Accédez à votre espace de noms, puis sélectionnez **+ Event Hub**, en haut, pour créer une instance Event Hub.

3. Générez une clé que vous allez utiliser dans IoT Central pour configurer l’exportation de vos données. 
    - Cliquez sur l’instance Event Hub que vous avez créée. 
    - Cliquez sur **Stratégies de paramètres/accès partagé**. 
    - Créez une clé, ou choisissez une clé existante qui dispose des autorisations **Envoyer**. 
    - Copiez la chaîne de connexion primaire ou secondaire. Vous l’utiliserez pour configurer une nouvelle destination dans IoT Central.

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Créer une file d’attente Service Bus ou une destination de rubrique

En l’absence d’espace de noms Service Bus vers lequel exporter, suivez ces étapes :

1. Créez un [espace de noms Service Bus dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Pour en savoir plus, consultez les [documents sur Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).

2. Pour créer une file d’attente ou une rubrique de destination d’exportation, accédez à votre espace de noms Service Bus, puis sélectionnez **+ File d’attente** ou **+ Rubrique**, en haut.

3. Générez une clé que vous allez utiliser dans IoT Central pour configurer l’exportation de vos données. 
    - Cliquez sur la file d’attente ou la rubrique que vous avez créée. 
    - Cliquez sur **Stratégies de paramètres/accès partagé**. 
    - Créez une clé, ou choisissez une clé existante qui dispose des autorisations **Envoyer**. 
    - Copiez la chaîne de connexion primaire ou secondaire. Vous l’utiliserez pour configurer une nouvelle destination dans IoT Central.

### <a name="create-an-azure-blob-storage-destination"></a>Créer une destination de stockage Blob Azure

En l’absence de compte de stockage Azure vers lequel exporter, suivez ces étapes :

1. Créez un [compte de stockage sur le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Découvrez comment créer des [comptes de stockage Blob Azure](https://aka.ms/blobdocscreatestorageaccount) ou des [comptes de stockage Azure Data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md). L’exportation de données peut uniquement écrire des données dans des comptes de stockage qui prennent en charge les objets blob de blocs. La liste suivante répertorie les types de compte de stockage compatibles connus :

    |Niveau de performances|Type de compte|
    |-|-|
    |Standard|Usage général v2|
    |Standard|Usage général v1|
    |Standard|Stockage d'objets blob|
    |Premium|Stockage d’objets blob de blocs|

2. Créez un conteneur dans votre compte de stockage. Accédez à votre compte de stockage. Sous **Service blob**, sélectionnez **Parcourir les objets blob**. Sélectionnez **+ Conteneur**, en haut, pour créer un conteneur.

3. Générez une chaîne de connexion pour votre compte de stockage en accédant à **Paramètres/clés d’accès**. Copiez une des deux chaînes de connexion.

### <a name="create-a-webhook-endpoint"></a>Créer un point de terminaison webhook
Vous pouvez fournir un point de terminaison HTTP disponible publiquement pour les données à y exporter. Vous pouvez créer un point de terminaison de webhook test à l’aide de RequestBin. Gardez à l’esprit que RequestBin a une limite de requêtes définie avant que les requêtes soient limitées.

1. Ouvrez [RequestBin](https://requestbin.net/).
2. Créez un point de terminaison RequestBin et copiez l’URL du fichier Bin.

## <a name="set-up-data-export"></a>Configurer l’exportation de données

Maintenant que vous avez une destination pour exporter les données, suivez ces étapes pour configurer l’exportation des données.

1. Connectez-vous à votre application IoT Central.

2. Dans le volet gauche, sélectionnez **Exportation de données**.

    > [!Tip]
    > Si vous ne voyez pas **Exportation de données** dans le volet gauche, cela signifie que vous n’avez pas les autorisations nécessaires pour configurer l’exportation de données dans votre application. Contactez un administrateur pour configurer l’exportation de données.

3. Sélectionnez le bouton **+ Nouvelle exportation**. 

4. Entrez un nom d’affichage pour votre nouvelle exportation et assurez-vous que la bascule **Activé** est activée pour les données à écouler.

## <a name="1-choose-the-type-of-data-to-export"></a>1. Choisir le type de données à exporter
Vous pouvez choisir entre l’exportation continue de différents types de données. Voici les types de données pris en charge :

| Type de données | Description | Format de données |
| :------------- | :---------- | :----------- |
|  Télémétrie | Exportez des messages de télémétrie à partir d’appareils en quasi-temps réel. Chaque message exporté contient le contenu complet du message de l’appareil d’origine, normalisé.   |  [Format du message de télémétrie](#telemetry-format)   |
| Modifications de la propriété | Exportez les modifications apportées aux propriétés de l’appareil et du cloud en quasi-temps réel. Pour les propriétés de l’appareil en lecture seule, les modifications apportées aux valeurs signalées sont exportées. Pour les propriétés en lecture-écriture, les valeurs signalées et souhaitées sont exportées. | [Format du message de modification de la propriété](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2. (Facultatif) Ajouter des filtres
Ajoutez des filtres pour réduire la quantité de données exportées en fonction des conditions de filtre. Il existe différents types de filtres disponibles pour chaque type de données à exporter.

### <a name="telemetry-filters"></a>Filtres des données de télémétrie
  - **Filtre des capacités** : Si vous choisissez un élément de télémétrie dans la liste déroulante, le flux exporté contient uniquement les données de télémétrie qui répondent à la condition de filtre. Si vous choisissez un appareil ou un élément de propriété cloud dans la liste déroulante, le flux exporté contient uniquement les données de télémétrie des appareils qui ont des propriétés qui correspondent à la condition de filtre.
  - **Filtre des propriétés de message** : Les appareils qui utilisent les kits de développement logiciel (SDK) d’appareil sont autorisés à envoyer des *propriétés de message* ou des *propriétés d’application* sur chaque message de télémétrie, qui est un conteneur de paires clé-valeur généralement utilisé pour baliser le message avec des identificateurs personnalisés. Vous pouvez créer un filtre des propriétés de message en saisissant la clé de propriétés de message que vous recherchez et en spécifiant une condition. Seuls les messages de télémétrie dont les propriétés de message correspondent à la condition de filtre spécifiée seront exportés. Seuls les opérateurs de comparaison de chaînes sont pris en charge (égal à, n’est pas égal à, contient, ne contient pas, existe, n’existe pas). [En savoir plus sur les propriétés de l’application à partir des documents IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

### <a name="property-changes-filters"></a>Filtres des modifications de propriétés
**Filtre de propriétés** : Choisissez un élément de propriété dans la liste déroulante et le flux exporté contient uniquement les modifications apportées à la propriété sélectionnée qui correspond à la condition de filtre.

## <a name="3-optional-add-enrichments"></a>3. (Facultatif) Ajouter des enrichissements
Ajoutez des enrichissements pour enrichir les messages exportés avec des métadonnées supplémentaires dans les paires clé-valeur. Il s’agit des enrichissements disponibles pour les types de données de télémétrie et de modification des propriétés :
  - **Chaîne personnalisée** : Ajoute une chaîne statique personnalisée à chaque message. Entrez n’importe quelle clé, puis entrez une valeur de chaîne.
  - **Property** : Ajoute la valeur actuelle de la propriété rapportée d’appareil ou de la propriété de cloud à chaque message. Entrez une clé, puis choisissez une propriété d’appareil ou de cloud. Si le message exporté provient d’un appareil qui n’a pas la propriété d’appareil ou de cloud spécifiée, le message exporté n’aura pas cet enrichissement.

## <a name="4-add-destinations"></a>4. Ajouter des destinations
Créez une nouvelle destination ou ajoutez une destination que vous avez déjà créée. 
  
1. Cliquez sur le lien **créer une nouvelle destination**. Renseignez les informations suivantes :
    - **Nom de destination** : nom d’affichage de la destination dans IoT Central
    - **Type de destination** : choisissez le type de destination. Si vous ne l’avez pas déjà fait, [configurer votre destination d’exportation](#set-up-export-destination)
    - Pour la file d’attente ou la rubrique Azure Event Hubs, Azure Service Bus, collez la chaîne de connexion de votre ressource. 
    - Pour le Stockage Blob Azure, collez la chaîne de connexion de votre ressource et entrez le nom du conteneur (sensible à la casse).
    - Pour Webhook, collez l’URL de rappel de votre point de terminaison webhook. 
    - Cliquez sur **Créer**

2. Cliquez sur **+ destination** et choisissez une destination dans la liste déroulante. Vous pouvez ajouter jusqu’à 5 destinations à une seule exportation.

3. Une fois que vous avez fini de configurer votre exportation, cliquez sur **Enregistrer**. Après quelques minutes, vos données s’affichent dans vos destinations.

## <a name="export-contents-and-format"></a>Exporter le contenu et le format

### <a name="azure-blob-storage-destination"></a>Destination du Stockage Blob Azure

Les données sont exportées une fois par minute et chaque fichier contient le lot de modifications apportées depuis la dernière exportation de fichier. Les données exportées sont placées dans trois dossiers au format JSON. Les chemins d’accès par défaut dans votre compte de stockage sont les suivants :

- Données de télémétrie : _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Modifications apportées aux propriétés : _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Pour parcourir les fichiers exportés dans le Portail Azure, accédez au fichier puis sélectionnez l’onglet **Modifier le blob**.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Destinations Azure Event Hubs et Azure Service Bus

Les données sont exportées en quasi-temps réel. Les données se trouvent dans le corps du message, au format JSON encodé au format UTF-8. 

Dans le conteneur d’annotations ou de propriétés système du message, vous pouvez trouver `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` et `iotcentral-message-type` qui ont les mêmes valeurs que les champs correspondants dans le corps du message.

### <a name="webhook-destination"></a>Destination du webhook
Pour les destinations de webhooks, les données sont également exportées en quasi-temps réel. Les données dans le corps du message sont au même format que pour Event Hubs et Service Bus.


## <a name="telemetry-format"></a>Format des données de télémétrie.
Chaque message exporté contient un formulaire normalisé du message complet que l’appareil a envoyé dans le corps du message au format JSON encodé au format UTF-8. Les informations supplémentaires incluses dans chaque message sont les suivantes :

- `applicationId`de l’application IoT Central
- `messageSource` qui est *télémétrie* pour l’exportation des données de télémétrie
- `deviceId` de l’appareil qui a envoyé le message de télémétrie
- `schema` est le nom et la version du schéma de charge utile
- `templateId` est l’ID du modèle d’appareil associé à l’appareil
- `enrichments` représentent tous les enrichissements qui ont été configurés lors de l’exportation
- `messageProperties` représentent les éléments de données supplémentaires que l’appareil a envoyées avec le message. Il s’agit également de ce que l’on appelle *propriétés d’application*, [en savoir plus sur les documentations IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Pour Event Hubs et Service Bus, IoT Central exporte rapidement un nouveau message après l’avoir reçu d’un appareil.

Pour le stockage Blob, les messages sont regroupés par lot et exportés une fois par minute.

L’exemple suivant montre un message de télémétrie exporté :

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>Format des modifications de propriétés

Chaque message ou enregistrement représente une modification apportée à une propriété de l’appareil ou du cloud. Pour les propriétés de l’appareil, seules les modifications apportées à la valeur rapportée sont exportées en tant que message distinct. Les informations supplémentaires incluses dans le message exporté sont les suivantes :

- `applicationId`de l’application IoT Central
- `messageSource` qui est *propriétés* pour l’exportation de données de modifications des propriétés
- `messageType` qui est soit *cloudPropertyChange* soit *devicePropertyDesiredChange*, *devicePropertyReportedChange*
- `deviceId` de l’appareil dont les propriétés ont été modifiées
- `schema` est le nom et la version du schéma de charge utile
- `templateId` est l’ID du modèle d’appareil associé à l’appareil
- `enrichments` représentent tous les enrichissements qui ont été configurés lors de l’exportation

Pour Event Hubs et Service Bus, IoT Central exporte de nouvelles données de messages à la file d’attente ou à la rubrique de votre Event Hub ou Service Bus en quasi-temps réel.

Pour le stockage Blob, les messages sont regroupés par lot et exportés une fois par minute.

L’exemple suivant présente un message de modification des propriétés exportées reçu dans le stockage Blob Azure.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>Comparaison entre l’exportation de données héritée et la nouvelle exportation de données
Cette table met en évidence les différences entre la nouvelle exportation de données et l’exportation de données héritée. Vous pouvez en savoir plus sur l’exportation de données héritée [ici](howto-export-data.md).

| Fonctionnalités  | Exportation de données héritée | Nouvelle exportation de données |
| :------------- | :---------- | :----------- |
| Types de données disponibles | Télémétrie, appareils, modèles d’appareil | Télémétrie, modifications des propriétés |
| Filtrage | None | Dépend du type de données exporté. Pour la télémétrie, le filtrage par télémétrie, les propriétés de messages, les valeurs de propriété |
| Enrichissements | None | Enrichir avec une chaîne personnalisée ou une valeur de propriété sur l’appareil |
| Destinations | Files d’attente et rubriques d’Azure Event Hubs, Azure Service Bus, Stockage Blob Azure | Les mêmes que pour l’exportation de données héritée et les webhooks| 
| Applications prises en charge | V2, V3 | V3 uniquement |
| Limites notables | 5 exportations par application, 1 destination par exportation | 10 exportations-connexions de destination par application | 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment utiliser la nouvelle exportation de données, passez à l’étape suivante :

> [!div class="nextstepaction"]
> [Comment utiliser l’analytique dans IoT Central](./howto-create-analytics.md)
