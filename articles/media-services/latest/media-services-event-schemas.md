---
title: Schémas Azure Event Grid pour les événements Media Services
description: Décrit les propriétés qui sont fournies pour les événements Media Services avec Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: a6a6c459e170627d26aa1445f4f4dd193965fe70
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42145331"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Schémas Azure Event Grid pour les événements Media Services

Cet article fournit les schémas et les propriétés pour les événements Media Services.

Pour obtenir la liste des exemples de scripts et des didacticiels, consultez [Media Services event source](../../event-grid/event-sources.md#azure-subscriptions) (Source d’événement Media Services).

## <a name="available-event-types"></a>Types d’événement disponibles

Media Services émet les types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| État des modifications du travail. |
| Microsoft.Media.LiveEventConnectionRejected | La tentative de connexion de l’encodeur est rejetée. |
| Microsoft.Media.LiveEventEncoderConnected | L’encodeur établit une connexion avec l’événement en direct. |
| Microsoft.Media.LiveEventEncoderDisconnected | L’encodeur se déconnecte. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Le serveur multimédia supprime le bloc de données, car il est trop tard ou les timestamps se chevauchent (le timestamp du nouveau bloc de données est inférieur à l’heure de fin du bloc de données précédent). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Le serveur multimédia reçoit le premier bloc de données pour chaque piste dans le flux ou la connexion. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Le serveur multimédia détecte des flux audio et vidéo qui ne sont pas synchronisés. Ce type d’événement doit être utilisé comme un avertissement, car l’expérience utilisateur peut ne pas être affectée. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Le serveur multimédia détecte que l’un des deux flux vidéo provenant de l’encodeur externe n’est pas synchronisé. Ce type d’événement doit être utilisé comme un avertissement, car l’expérience utilisateur peut ne pas être affectée. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publié toutes les 20 secondes pour chaque piste pendant l’événement en direct. Fournit un résumé de l’intégrité d’ingestion. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Le serveur multimédia détecte une discontinuité dans la piste entrante. |

Il existe deux catégories d’événements **en direct** : les événements de flux de données et les événements de piste. 

Les événements de flux sont déclenchés par le flux de données ou la connexion. Chaque événement possède un paramètre `StreamId` qui identifie la connexion ou le flux. Chaque flux de données ou connexion dispose d’une ou de plusieurs pistes de différents types. Par exemple, une même connexion provenant d’un encodeur peut avoir une seule piste audio et quatre pistes vidéo. Les types d’événements de flux de données sont :

* LiveEventConnectionRejected
* LiveEventEncoderConnected
* LiveEventEncoderDisconnected

Les événements de piste sont déclenchés en fonction de la piste. Les types d’événements de piste sont :

* LiveEventIncomingDataChunkDropped
* LiveEventIncomingStreamReceived
* LiveEventIncomingStreamsOutOfSync
* LiveEventIncomingVideoStreamsOutOfSync
* LiveEventIngestHeartbeat
* LiveEventTrackDiscontinuityDetected

## <a name="jobstatechange"></a>JobStateChange

L’exemple suivant montre le schéma d’un événement **JobStateChange** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| previousState | chaîne | L’état du travail avant l’événement. |
| state | chaîne | Le nouvel état de la tâche notifié dans cet événement. Par exemple, « en file d’attente : la tâche est en attente de ressources » ou « planifiée : le travail est prêt à démarrer ».|

Où l’état du travail peut prendre l’une des valeurs suivantes : *En file d’attente*, *Planifié*, *Traitement*, *Terminé*, *Erreur*, *Annulé*, *Annulation en cours*

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

L’exemple suivant montre le schéma d’un événement **LiveEventConnectionRejected** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "StreamId":"Mystream1",
      "IngestUrl": "http://abc.ingest.isml",
      "EncoderIp": "118.238.251.xxx",
      "EncoderPort": 52859,
      "ResultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0"
  }
]
```

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| StreamId | chaîne | Identificateur du flux de données ou de la connexion. L’encodeur ou le client est chargé d’ajouter cet ID dans l’URL d’ingestion. |  
| IngestUrl | chaîne | URL d’ingestion fournie par l’événement en direct. |  
| EncoderIp | chaîne | Adresse IP de l’encodeur. |
| EncoderPort | chaîne | Port de l’encodeur dont provient ce flux. |
| ResultCode | chaîne | Motif de rejet de la connexion. Les codes de résultat sont répertoriés dans le tableau suivant. |

Les codes de résultat sont :

| Code de résultat | Description |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | URL d’ingestion incorrecte |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | L’adresse IP de l’encodeur n’est pas présente dans la liste d’adresses IP autorisées configurée |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | L’encodeur n’a pas envoyé de métadonnées concernant le flux. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Le codec spécifié n’est pas pris en charge. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | A reçu un fragment avant la réception et l’en-tête pour ce flux. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | Le nombre de qualités spécifiées dépasse la limite maximale autorisée. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | Le débit binaire agrégé dépasse la limite maximale autorisée. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Le timestamp du FLVTag vidéo ou audio n’est pas valide à partir de l’encodeur RTMP. |

## <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

L’exemple suivant montre le schéma d’un événement **LiveEventEncoderConnected** : 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| StreamId | chaîne | Identificateur du flux de données ou de la connexion. L’encodeur ou le client est chargé d’ajouter cet ID dans l’URL d’ingestion. |
| IngestUrl | chaîne | URL d’ingestion fournie par l’événement en direct. |
| EncoderIp | chaîne | Adresse IP de l’encodeur. |
| EncoderPort | chaîne | Port de l’encodeur dont provient ce flux. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

L’exemple suivant montre le schéma d’un événement **LiveEventEncoderDisconnected** : 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| StreamId | chaîne | Identificateur du flux de données ou de la connexion. L’encodeur ou le client est chargé d’ajouter cet ID dans l’URL d’ingestion. |  
| IngestUrl | chaîne | URL d’ingestion fournie par l’événement en direct. |  
| EncoderIp | chaîne | Adresse IP de l’encodeur. |
| EncoderPort | chaîne | Port de l’encodeur dont provient ce flux. |
| ResultCode | chaîne | Motif de déconnexion de l’encodeur. Il peut s’agir d’une déconnexion normale ou due à une erreur. Les codes de résultat sont répertoriés dans le tableau suivant. |

Les codes de résultat d’erreur sont :

| Code de résultat | Description |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | La session RTMP a expiré après avoir été inactive pendant le temps maximal autorisé. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Le timestamp du FLVTag vidéo ou audio n’est pas valide à partir de l’encodeur RTMP. |
| MPE_CAPACITY_LIMIT_REACHED | L’encodeur envoie les données trop vite. |
| Codes d’erreur inconnus | Ces codes d’erreur peuvent varier de l’erreur de mémoire à des entrées de table de hachage en double. |

Les codes de résultat de déconnexion normale sont :

| Code de résultat | Description |
| ----------- | ----------- |
| S_OK | Encodeur déconnecté avec succès. |
| MPE_CLIENT_TERMINATED_SESSION | Encodeur déconnecté (RTMP). |
| MPE_CLIENT_DISCONNECTED | Encodeur déconnecté (FMP4). |
| MPI_REST_API_CHANNEL_RESET | La commande de réinitialisation du canal est reçue. |
| MPI_REST_API_CHANNEL_STOP | La commande d’arrêt du canal est reçue. |
| MPI_REST_API_CHANNEL_STOP | Canal en cours de maintenance. |
| MPI_STREAM_HIT_EOF | Le flux EOF est envoyé par l’encodeur. |

## <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

L’exemple suivant montre le schéma d’un événement **LiveEventIncomingDataChunkDropped** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "TrackType": "Video",
      "TrackName": "Video",
      "Bitrate": 300000,
      "Timestamp": 36656620000,
      "Timescale": 10000000,
      "ResultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0"
  }
]
```

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| TrackType | chaîne | Type de la piste (Audio/Vidéo). |
| TrackName | chaîne | Nom de la piste. |
| Bitrate | integer | Débit binaire de la piste. |
| Timestamp | chaîne | Timestamp du bloc de données supprimé. |
| Échelle de temps | chaîne | Échelle de temps du timestamp. |
| ResultCode | chaîne | Motif de suppression du bloc de données. **FragmentDrop_OverlapTimestamp** ou **FragmentDrop_NonIncreasingTimestamp**. |

## <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

L’exemple suivant montre le schéma d’un événement **LiveEventIncomingStreamReceived** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| TrackType | chaîne | Type de la piste (Audio/Vidéo). |
| TrackName | chaîne | Nom de la piste (fourni par l’encodeur ou, dans le cas du RTMP, généré par le serveur selon le format *TypePiste_DébitBinaire*). |
| Bitrate | integer | Débit binaire de la piste. |
| IngestUrl | chaîne | URL d’ingestion fournie par l’événement en direct. |
| EncoderIp | chaîne  | Adresse IP de l’encodeur. |
| EncoderPort | chaîne | Port de l’encodeur dont provient ce flux. |
| Timestamp | chaîne | Premier timestamp du bloc de données reçu. |
| Échelle de temps | chaîne | Échelle de temps dans laquelle le timestamp est représenté. |

## <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

L’exemple suivant montre le schéma d’un événement **LiveEventIncomingStreamsOutOfSync** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| MinLastTimestamp | chaîne | Minimum des derniers timestamps parmi toutes les pistes (audio ou vidéo). |
| TypeOfTrackWithMinLastTimestamp | chaîne | Type de la piste (audio ou vidéo) avec dernier timestamp minimum. |
| MaxLastTimestamp | chaîne | Maximum de tous les timestamps parmi toutes les pistes (audio ou vidéo). |
| TypeOfTrackWithMaxLastTimestamp | chaîne | Type de la piste (audio ou vidéo) avec le dernier timestamp maximum. |

## <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

L’exemple suivant montre le schéma d’un événement **LiveEventIncomingVideoStreamsOutOfSync** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "FirstTimestamp": "2162058216",
      "FirstDuration": "2000",
      "SecondTimestamp": "2162057216",
      "SecondDuration": "2000"
    },
    "dataVersion": "1.0"
  }
]
```

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| FirstTimestamp | chaîne | Timestamp reçu pour l’une des pistes/l’un des niveaux de qualité de type vidéo. |
| FirstDuration | chaîne | Durée du bloc de données avec le premier timestamp. |
| SecondTimestamp | chaîne  | Timestamp reçu pour une autre piste/un autre niveau de qualité de type vidéo. |
| SecondDuration | chaîne | Durée du bloc de données avec un deuxième timestamp. |

## <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

L’exemple suivant montre le schéma d’un événement **LiveEventIngestHeartbeat** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| TrackType | chaîne | Type de la piste (Audio/Vidéo). |
| TrackName | chaîne | Nom de la piste (fourni par l’encodeur ou, dans le cas du RTMP, généré par le serveur selon le format *TypePiste_DébitBinaire*). |
| Bitrate | integer | Débit binaire de la piste. |
| IncomingBitrate | integer | Débit binaire calculé et basé sur des blocs de données provenant de l’encodeur. |
| LastTimestamp | chaîne | Timestamp le plus récent reçu pour une piste dans les 20 dernières secondes. |
| Échelle de temps | chaîne | Échelle de temps dans laquelle les timestamps sont exprimés. |
| OverlapCount | integer | Nombre de blocs de données avec des timestamps qui se chevauchent au cours des 20 dernières secondes. |
| DiscontinuityCount | integer | Nombre de discontinuités observées dans les dernières 20 secondes. |
| NonIncreasingCount | integer | Nombre de blocs de données présentant des timestamps passés dans les dernières 20 secondes. |
| UnexpectedBitrate | bool | Indique si les débits binaires attendus et réels diffèrent de plus de la valeur maximale autorisée au cours des 20 dernières secondes. Si la valeur est true, et uniquement dans ce cas, >= 2* bitrate OR IncomingBitrate <= bitrate/2 OR IncomingBitrate = 0. |
| État | chaîne | État de l’événement en direct. |
| Healthy | bool | Indique si l’ingestion est intègre en fonction des nombres et des indicateurs. Healthy a la valeur true si OverlapCount = 0 && DiscontinuityCount = 0 && NonIncreasingCount = 0 && UnexpectedBitrate = false. |

## <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

L’exemple suivant montre le schéma d’un événement **LiveEventTrackDiscontinuityDetected** : 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| TrackType | chaîne | Type de la piste (Audio/Vidéo). |
| TrackName | chaîne | Nom de la piste (fourni par l’encodeur ou, dans le cas du RTMP, généré par le serveur selon le format *TypePiste_DébitBinaire*). |
| Bitrate | integer | Débit binaire de la piste. |
| PreviousTimestamp | chaîne | Timestamp du fragment précédent. |
| NewTimestamp | chaîne | Timestamp du fragment actuel. |
| DiscontinuityGap | chaîne | Écart entre les deux timestamps ci-dessus. |
| Échelle de temps | chaîne | Échelle de temps dans laquelle les écarts de timestamp et de discontinuité sont représentés. |

## <a name="common-event-properties"></a>Propriétés d’événements courantes

Un événement contient les données générales suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| rubrique | chaîne | Rubrique EventGrid. Cette propriété comporte l’ID de ressource pour le compte Media Services. |
| subject | chaîne | Chemin d’accès à la ressource pour le canal Media Services sous le compte Media Services. La concaténation de la rubrique et de l’objet indique l’ID de ressource pour le travail. |
| eventType | chaîne | Un des types d’événements inscrits pour cette source d’événement. Par exemple, « Microsoft.Media.JobStateChange ». |
| eventTime | chaîne | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | chaîne | Identificateur unique de l’événement. |
| données | objet | Données d’événement Media Services. |
| dataVersion | chaîne | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | chaîne | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

## <a name="next-steps"></a>Étapes suivantes

[S’inscrire à des événements de changement d’état d’un travail](job-state-events-cli-how-to.md)