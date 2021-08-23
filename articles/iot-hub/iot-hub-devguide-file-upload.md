---
title: Bien comprendre le chargement de fichiers Azure IoT Hub | Microsoft Docs
description: Guide du développeur - utilisez la fonctionnalité de chargement de fichier de IoT Hub pour gérer le chargement de fichiers depuis un appareil vers un conteneur d’objets blob de stockage Azure.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: dd7adc3a1fdb45255bd13ee279d66e64bdf4c1d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562343"
---
# <a name="upload-files-with-iot-hub"></a>Chargement de fichiers avec IoT Hub

Dans de nombreux scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Par exemple :
* Fichiers image volumineux
* Fichiers vidéo
* Données de vibration échantillonnées à une fréquence élevée
* Un certain type de données prétraitées

S’il vous faut charger des fichiers de ce type à partir d’un appareil, vous pouvez toujours exploiter la sécurité et la fiabilité d’IoT Hub. Au lieu de distribuer les messages via sa propre plate-forme cependant, IoT Hub joue le rôle de répartiteur vers un compte Stockage Azure associé. Un appareil demande à IoT Hub un jeton de stockage spécifique au fichier que l’appareil souhaite télécharger. L’appareil utilise l’URI SAP pour télécharger le fichier vers le stockage. Une fois le téléchargement terminé, l’appareil envoie une notification à IoT Hub pour l’en informer. IoT Hub vérifie que le chargement du fichier est terminé.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

### <a name="when-to-use"></a>Quand l’utiliser

Utilisez le chargement des fichiers pour envoyer des fichiers multimédias et de gros traitements télémétriques par lots chargés par des appareils connectés par intermittence ou compressés pour économiser de la bande passante. Reportez-vous à [l’aide sur la communication appareil-à-cloud](iot-hub-devguide-d2c-guidance.md) si vous hésitez entre l’utilisation des propriétés signalées, l’utilisation des messages appareil-à-cloud ou l’utilisation du chargement de fichiers.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Association d’un compte Stockage Azure à IoT Hub

Vous devez disposer d’un compte Stockage Azure associé à votre IoT Hub. 

Pour plus d’informations sur la création d’un compte à l’aide du portail, consultez [Créer un compte de stockage](../storage/common/storage-account-create.md). 

Vous pouvez également créer un compte par programmation à l’aide des [API REST de fournisseur de ressources IOT Hub](/rest/api/iothub/iothubresource). 

Lorsque vous associez un compte de stockage Azure avec un IoT Hub, celui-ci génère un URI SAS. Un appareil peut utiliser cet URI SAS pour charger en toute sécurité un fichier vers un conteneur d’objets blob.

## <a name="create-a-container"></a>Créez un conteneur.

 Pour créer un conteneur d’objets blob via le portail :

1. Dans le volet gauche de votre compte de stockage, sous **Stockage des données**, sélectionnez **Conteneurs**.
1. Dans le panneau Conteneur, sélectionnez **+ Conteneur**.
1. Dans le volet **Nouveau conteneur** qui s’ouvre, donnez un nom à votre conteneur, puis sélectionnez **Créer**.

Après avoir créé un conteneur, suivez les instructions disponibles dans [Configurer les téléchargements de fichiers à l’aide du portail Azure](iot-hub-configure-file-upload.md). Assurez-vous qu’un conteneur de blobs est associé à votre IoT Hub et que les notifications de fichier sont activées.

Vous pouvez également utiliser les [API REST de fournisseur de ressources IoT Hub](/rest/api/iothub/iothubresource) pour créer un conteneur associé au stockage de votre IoT Hub.

## <a name="file-upload-using-an-sdk"></a>Charger des fichiers à l’aide d’un kit de développement logiciel (SDK)

Les guides pratiques suivants fournissent des procédures pas à pas complètes du processus de chargement de fichiers dans différentes langues de SDK. Ces guides vous montrent comment utiliser le portail Azure pour associer un compte de stockage à un hub IoT. Ils contiennent également des extraits de code ou font référence à des exemples qui vous guident tout au long du processus de chargement.

* [.NET](iot-hub-csharp-csharp-file-upload.md)
* [Java](iot-hub-java-java-file-upload.md)
* [Node.JS](iot-hub-node-node-file-upload.md)
* [Python](iot-hub-python-python-file-upload.md)

> [!NOTE]
> Les kits [Azure IoT SDK](iot-hub-devguide-sdks.md) gèrent automatiquement la récupération de l’URI de signature d’accès partagé, le chargement du fichier et l’envoi d’une notification à IoT Hub de la fin du chargement. Si un pare-feu bloque l’accès au point de terminaison Stockage Blob, mais autorise l’accès au point de terminaison IoT Hub, le processus de téléchargement de fichiers échoue et affiche l’erreur suivante pour le Kit de développement logiciel (SDK) d’appareil IoT C# :
>
> `---> System.Net.Http.HttpRequestException: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond`
>
> Pour que la fonctionnalité de chargement de fichier fonctionne, l’accès au point de terminaison IoT Hub et au point de terminaison Stockage Blob doit être disponible pour l’appareil.
> 


## <a name="initialize-a-file-upload-rest"></a>Initialiser un téléchargement de fichier (REST)

Vous pouvez utiliser des API REST plutôt que l’un des kits de développement logiciel (SDK) pour charger un fichier. IoT Hub a un point de terminaison spécifique aux appareils pour demander une URI SAS pour le stockage afin de télécharger un fichier. Pour démarrer le processus de chargement de fichiers, l’appareil envoie une requête POST à `{iot hub}.azure-devices.net/devices/{deviceId}/files` avec le corps JSON suivant :

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub renvoie les données suivantes. L’appareil l’utilise pour télécharger le fichier :

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Déconseillé : initialiser un téléchargement de fichier avec une commande GET

> [!NOTE]
> Cette section décrit les fonctionnalités déconseillées pour la réception d’une URI SAS d’IoT Hub. Utilisez la méthode POST décrite précédemment.

IoT Hub utilise deux points de terminaison REST pour prendre en charge le téléchargement de fichier, le premier afin d’obtenir l’URI SAP pour le stockage et le second pour informer IoT hub de la fin du téléchargement. L’appareil démarre le processus de téléchargement de fichier en envoyant une commande GET à IoT Hub à `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. L’IoT Hub renvoie :

* Un URI SAS spécifique au fichier à charger.

* Un ID de corrélation à utiliser une fois le chargement terminé.

## <a name="notify-iot-hub-of-a-completed-file-upload-rest"></a>Notifier IoT Hub de la fin du téléchargement d’un fichier (REST)

L’appareil charge le fichier dans le stockage à l’aide des kits SDK Stockage Azure. Une fois le chargement terminé, l’appareil envoie une requête POST à `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` avec le corps JSON suivant :

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

La valeur de `isSuccess` est une valeur booléenne qui indique si le fichier a bien été téléchargé. Le code d’état de `statusCode` est l’état pour le téléchargement du fichier vers le stockage et `statusDescription` correspond à `statusCode`.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence suivantes vous fournissent des informations supplémentaires sur le téléchargement de fichiers depuis un appareil.

### <a name="file-upload-notifications"></a>Notifications de téléchargement de fichier

Éventuellement, lorsqu’un appareil notifie IoT Hub que le chargement est terminé, le service peut générer un message de notification. Ce message contient l’emplacement de stockage et le nom du fichier.

Comme l’explique la section [Points de terminaison](iot-hub-devguide-endpoints.md), IoT Hub émet des notifications de chargement de fichiers sous forme de messages par le biais d’un point de terminaison orienté service ( **/messages/servicebound/fileuploadnotifications**). La sémantique de réception des notifications de chargement de fichiers est identique à celle des messages cloud-à-appareil et présente le même [cycle de vie des messages](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle). Chaque message récupéré à partir du point de terminaison de notification de téléchargement de fichier est un enregistrement JSON qui possède les propriétés suivantes :

| Propriété | Description |
| --- | --- |
| EnqueuedTimeUtc |Horodatage indiquant la date et l’heure de création de la notification. |
| deviceId |**DeviceId** de l’appareil qui a téléchargé le fichier. |
| BlobUri |URI du fichier téléchargé. |
| BlobName |Nom du fichier téléchargé. |
| LastUpdatedTime |Horodatage indiquant la date et l’heure de dernière mise à jour du fichier. |
| BlobSizeInBytes |Taille du fichier téléchargé. |

**Exemple**. Voici un exemple illustrant le corps de message de notification de téléchargement de fichier.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

### <a name="file-upload-notification-configuration-options"></a>Options de configuration de notification de téléchargement de fichier

Chaque IoT Hub dispose des options de configuration suivantes pour les notifications de chargement de fichier :

| Propriété | Description | Plage et valeur par défaut |
| --- | --- | --- |
| **enableFileUploadNotifications** |Indique si les notifications de téléchargement de fichier sont écrites dans le point de terminaison de notification de fichier. |Valeur booléenne. Valeur par défaut : True. |
| **fileNotifications.ttlAsIso8601** |Durée de vie par défaut des notifications de téléchargement de fichier. |Intervalle ISO_8601 jusqu’à 48h (minimum 1 minute). Valeur par défaut : 1 heure. |
| **fileNotifications.lockDuration** |Durée de verrouillage de la file d’attente des notifications de téléchargement de fichiers. |5 à 300 secondes (5 secondes au minimum). Valeur par défaut : 60 secondes. |
| **fileNotifications.maxDeliveryCount** |Nombre maximal de diffusions pour la file d’attente de notification de téléchargement de fichier. |1 à 100. Valeur par défaut : 100. |

Vous pouvez définir ces propriétés sur votre hub IoT en utilisant le portail Azure, Azure CLI ou PowerShell. Pour savoir comment procéder, consultez les rubriques sous [Configurer le chargement de fichiers](iot-hub-configure-file-upload.md).

## <a name="additional-reference-material"></a>Matériel de référence supplémentaire

Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* La rubrique [Points de terminaison IoT Hub](iot-hub-devguide-endpoints.md), qui décrit les différents points de terminaison de chaque hub IoT pour les opérations d’exécution et de gestion.

* La rubrique [Quotas et limitation IoT Hub](iot-hub-devguide-quotas-throttling.md) décrit les quotas et le comportement de limitation qui s’appliquent au service IoT Hub.

* La rubrique [SDK des services et appareils Azure IoT](iot-hub-devguide-sdks.md), qui répertorie les SDK en différents langages que vous pouvez utiliser pour le développement d’applications d’appareil et de service qui interagissent avec IoT Hub.

* La rubrique [Langage de requête IoT Hub](iot-hub-devguide-query-language.md) décrit le langage de requête permettant de récupérer à partir d’IoT Hub des informations sur les jumeaux d’appareil et les travaux.

* La rubrique [Prise en charge de MQTT au niveau d’IoT Hub](iot-hub-mqtt-support.md), qui fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez comment télécharger des fichiers depuis des appareils avec IoT Hub, vous serez peut-être intéressé par les rubriques suivantes du Guide du développeur IoT :

* [Gérer les identités des appareils dans IoT Hub](iot-hub-devguide-identity-registry.md)

* [Contrôler l’accès à IoT Hub](iot-hub-devguide-security.md)

* [Utiliser des jumeaux d’appareil pour synchroniser les données d’état et de configuration](iot-hub-devguide-device-twins.md)

* [Appeler une méthode directe sur un appareil](iot-hub-devguide-direct-methods.md)

* [Planifier des travaux sur plusieurs appareils](iot-hub-devguide-jobs.md)

Pour mettre en pratique certains des concepts décrits dans cet article, consultez le didacticiel IoT Hub suivant :

* [Guide pratique pour charger des fichiers à partir d’appareils vers le cloud avec IoT Hub](iot-hub-csharp-csharp-file-upload.md)
