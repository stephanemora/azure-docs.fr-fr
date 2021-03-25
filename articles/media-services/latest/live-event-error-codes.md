---
title: Codes d'erreur des événements en direct Azure Media Services
description: Cet article répertorie les codes d'erreur des événements en direct.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 02/12/2020
ms.author: inhenkel
ms.openlocfilehash: 5463f1d8376cbe1a6e81d17c1f95a84e67f3b418
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581080"
---
# <a name="media-services-live-event-error-codes"></a>Codes d'erreur des événements en direct Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Les tableaux suivants répertorient les codes d'erreur associés aux [événements en direct](live-events-outputs-concept.md).

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Lorsque vous vous abonnez aux événements [Event Grid](../../event-grid/index.yml) pour un événement en direct, vous pouvez recevoir l'une des erreurs suivantes de l'événement [LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected).
> [!div class="mx-tdCol2BreakAll"]
>| Erreur | Information |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|Description | URL d’ingestion incorrecte |
>|Solution suggérée| APPID est un jeton GUID dans l'URL d'ingestion RTMP. Assurez-vous qu'il correspond à l'URL d'ingestion de l'API. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| Description |L'adresse IP de l'encodeur ne figure pas dans la liste verte d'adresses IP configurée |
>| Solution suggérée| Assurez-vous que l'adresse IP de l'encodeur figure dans la liste verte d'adresses IP configurée. Utilisez un outil en ligne tel que *whoismyip* ou la *calculatrice CIDR* pour définir la valeur appropriée.  Vérifiez que l'encodeur peut joindre le serveur avant l'événement en direct. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| Description|L'encodeur RTMP n'a pas envoyé la commande `setDataFrame`. |
>| Solution suggérée|La plupart des encodeurs commerciaux envoient des métadonnées de flux. Pour un encodeur qui envoie (push) une injection à débit unique, cela peut ne pas être un problème. En l'absence de métadonnées de flux, l'événement en direct est capable de calculer le débit entrant.  Pour l'ingestion multidébit d'un canal PassThru ou un scénario de type double envoi (push), vous pouvez essayer d'ajouter « videodatarate » et « audiodatarate » à la chaîne de requête dans l'URL d'injection. La valeur approximative peut fonctionner. L'unité est exprimée en Kbits. Par exemple, `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| Description|Le codec spécifié n'est pas pris en charge.|
>| Solution suggérée| L'événement en direct a reçu un codec non pris en charge. Par exemple, lors d'une ingestion RTMP, l'événement en direct a reçu un codec vidéo non AVC.  Vérifiez la préconfiguration de l'encodeur. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| Description |Les informations de description de média n’ont pas été reçues avant la remise des données de média réelles. |
>| Solution suggérée|L'événement en direct ne reçoit pas la description du flux (en-tête ou balise FLV) de l'encodeur. Il s'agit d'une violation de protocole. Contactez le fournisseur de l'encodeur. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| Description|Le nombre de qualités pour le type d’audio ou de vidéo dépasse la limite maximale autorisée. |
>| Solution suggérée|Lorsque le mode Live Encoding est défini pour l'événement en direct, l'encodeur doit envoyer (push) un débit unique de vidéo et d'audio.  Notez qu'un envoi (push) redondant à partir du même débit est autorisé. Vérifiez les paramètres de sortie ou la préconfiguration de l'encodeur pour vous assurer qu'il émet un flux à débit unique. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| Description|Le débit binaire total entrant dans un service d’événement ou de canal en direct dépasse la limite maximale autorisée. |
>| Solution suggérée|L'encodeur a dépassé le débit entrant maximal. Cette limite regroupe toutes les données entrantes de l'encodeur contributeur. Vérifiez les paramètres de sortie ou la préconfiguration de l'encodeur pour réduire le débit. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| Description|Le timestamp de la balise FLVTag vidéo ou audio n'est pas valide à partir de l'encodeur RTMP. |
>| Solution suggérée|Action déconseillée. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| Description|Les flux entrants avec taux de trames ingérés par l'encodeur ont dépassé le nombre maximal autorisé de 30 T/s pour l'encodage de canaux/événements en direct. |
>| Solution suggérée|Vérifiez la préconfiguration de l'encodeur pour réduire la fréquence d'images à moins de 36 T/s. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| Description|Les flux entrants ingérés par l’encodeur ont dépassé les résolutions autorisées suivantes : 1920 x 1088 pour l'encodage des canaux/événements en direct et 4096 x 2160 pour le transfert des canaux/événements en direct. |
>| Solution suggérée|Vérifiez la préconfiguration de l'encodeur pour réduire la résolution vidéo afin qu'elle ne dépasse pas la limite. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| Description|L’événement en direct a reçu une grande quantité de données audio à la fois, ou une grande quantité de données vidéo sans images clés. Nous avons déconnecté l'encodeur pour lui permettre de réessayer avec des données correctes. |
>| Solution suggérée|Assurez-vous que l'encodeur envoie une image clé pour chaque intervalle d'image clé (GOP).  Activez des paramètres tels que « Débit constant (CBR) » ou « Aligner les images clés ». Parfois, une réinitialisation de l'encodeur contributeur peut s'avérer utile. Si ce n'est pas le cas, contactez le fournisseur de l'encodeur. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Il se peut que vous voyiez l’une des erreurs suivantes de l’événement [LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected).

> [!div class="mx-tdCol2BreakAll"]
>| Erreur | Information |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| Description|La session RTMP a expiré après avoir été inactive pendant le temps maximal autorisé. |
>|Solution suggérée|Cette erreur survient généralement lorsqu'un encodeur cesse de recevoir le flux d'entrée et que la session devient inactive parce qu'il n'y a plus de données à sortir. Vérifiez l'intégrité de l'encodeur ou du flux d'entrée. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|Description| Le timestamp de la balise FLVTag vidéo ou audio n'est pas valide à partir de l'encodeur RTMP. |
>| Solution suggérée| Action déconseillée. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| Description|L’encodeur envoie les données trop vite. |
>| Solution suggérée|Cette erreur survient lorsque l'encodeur fait sortir en rafale un grand ensemble de fragments sur une courte période.  Cela peut théoriquement se produire lorsque l'encodeur ne peut pas envoyer (push) de données pendant un certain temps en raison d'un problème de réseau et que les données sortent en rafale dès que le réseau redevient disponible. Recherchez la cause dans le journal de l'encodeur ou dans le journal du système. |
>|**Codes d’erreur inconnus** |
>| Description| Ces codes d’erreur peuvent varier de l’erreur de mémoire à des entrées de table de hachage en double. |

## <a name="other-error-codes"></a>Autres codes d'erreur

> [!div class="mx-tdCol2BreakAll"]
>| Erreur | Information |Événement rejeté/déconnecté|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||Oui|
>| Description|Il s'agit d'une erreur générale. ||
>|Solution suggérée| Aucun.||
>|**MPI_SYSTEM_MAINTENANCE** ||Oui|
>| Description|L'encodeur s'est déconnecté pour cause de mise à jour du service ou de maintenance du système. ||
>|Solution suggérée|Veillez à ce que l'encodeur active la « connexion automatique ». Il s'agit d'une fonctionnalité de l'encodeur qui permet de rétablir la session après une déconnexion inattendue. ||
>|**MPE_BAD_URL_SYNTAX** ||Oui|
>| Description|Le format de l'URL d'ingestion est incorrect. ||
>|Solution suggérée|Vérifiez que l'URL d'ingestion est au bon format. Pour RTMP, il doit s'agir de `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||Oui|
>| Description|L'encodeur a déconnecté la session.  ||
>|Solution suggérée|Il ne s'agit pas d'une erreur. Cela se produit lorsque l'encodeur a initié une déconnexion, y compris une déconnexion normale. En cas de déconnexion inattendue, consultez le journal de l'encodeur ou le journal du système. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||Non|
>| Description|Le débit des données entrantes ne correspond pas au débit attendu. ||
>|Solution suggérée|Il s'agit d'un avertissement déclenché lorsque le débit des données entrantes est trop lent ou trop rapide. Consultez le journal de l'encodeur ou le journal du système.||
>|**MPE_INGEST_DISCONTINUITY** ||Non|
>| Description| Un problème de discontinuité a été constaté au niveau des données entrantes.||
>|Solution suggérée| Il s'agit d'un avertissement indiquant que l'encodeur supprime des données suite à un problème de réseau ou de ressources système. Consultez le journal de l'encodeur ou le journal du système. Surveillez également les ressources système (processeur, mémoire ou réseau). Si le processeur du système est en surrégime, essayez de réduire le débit ou utilisez l'option d'encodeur matériel de la carte graphique du système.||

## <a name="see-also"></a>Voir aussi

[Codes d'erreur du point de terminaison de streaming (origine)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Diffuser en direct avec Media Services](stream-live-tutorial-with-api.md)
