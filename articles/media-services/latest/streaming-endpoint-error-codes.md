---
title: Erreurs d’empaquetage et l’origine Azure Media Services | Microsoft Docs
description: Cette rubrique décrit les erreurs que vous pouvez recevoir à partir du service de terminaison de Streaming Azure Media Services (Orgin).
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: ebcda6026f79bc88df91471d8be88316ba57bfc6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411369"
---
# <a name="streaming-endpoint-origin-errors"></a>Diffusion en continu des erreurs de point de terminaison (origine) 

Cette rubrique décrit les erreurs que vous pouvez recevoir à partir d’Azure Media Services [service de point de terminaison de diffusion en continu](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 Demande incorrecte

La demande contient des informations non valides et est rejetée avec ces codes d’erreur et en raison d’une des raisons suivantes :

|Code d'erreur|Valeur hexadécimale |Description de l’erreur|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Une erreur de syntaxe ou le format d’URL. Exemples incluent des demandes pour un type non valide, un fragment non valide ou une piste non valide. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|La requête n’a aucune balise de chiffrement dans l’URL. Demandes CMAF nécessitent une balise de chiffrement dans l’URL. D’autres protocoles qui sont configurés avec plusieurs types de chiffrement nécessitent également la balise de chiffrement pour éviter les ambiguïtés. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|La demande vers le stockage pour répondre à la demande a échoué avec une erreur demande incorrecte. |

## <a name="403-forbidden"></a>403 Interdit

La demande n’est pas autorisée pour l’une des raisons suivantes :

|Code d'erreur|Valeur hexadécimale |Description de l’erreur|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|La demande vers le stockage pour répondre à la demande a échoué avec un échec d’authentification. Cela peut se produire si les clés de stockage ont été pivotés et que le service n’a pas pu synchroniser les clés de stockage. <br/><br/>Contactez Azure prennent en charge en accédant à [aide + support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) dans le portail Azure.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Erreur d’opération de stockage, l’accès a échoué en raison d’autorisations insuffisantes de compte. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |La demande vers le stockage pour répondre à la demande a échoué, car le compte de stockage est désactivé. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Erreur d’opération de stockage, l’accès a échoué en raison d’erreurs génériques. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Le format de sortie est bloqué en raison de la configuration dans le StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Le chiffrement est requis pour le contenu, la stratégie de remise est requise pour le format de sortie. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Le chiffrement n’est pas défini dans les paramètres de stratégie de remise. |

## <a name="404-not-found"></a>404 Non trouvé

L’opération tente d’agir sur une ressource qui n’existe plus. Par exemple, la ressource peut déjà avoir été supprimée.

|Code d'erreur|Valeur hexadécimale |Description de l’erreur|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |La piste demandée est introuvable. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |La ressource demandée est introuvable. |
|MPE_UNAUTHORIZED |0x80890244 |L’accès n’est pas autorisé. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |L’horodatage demandée est introuvable. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Le filtre de manifeste dynamique demandé est introuvable. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |L’index de fragment demandé est au-delà de la plage valide. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Impossible de trouver les entrées pour obtenir la mémoire tampon moov de media en direct. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Impossible de trouver le fragment au moment demandé une formation particulière.<br/><br/>Peut-être que le fragment n’est pas dans le stockage. Essayez une couche différente de la présentation qui peut-être avoir un fragment. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Impossible de trouver l’entrée de média pour la vitesse de transmission demandée dans le manifeste. <br/><br/>Peut-être que le joueur invité à entrer une piste vidéo d’une certaine vitesse de transmission qui n’était pas dans le manifeste.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Impossible de trouver certaines métadonnées dans le manifeste ou Impossible de trouver le rebasage à partir du stockage. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Erreur de l’opération stockage, ressource introuvable. |

## <a name="409-conflict"></a>409 Conflit

L’ID fourni pour une ressource sur un `PUT` ou `POST` opération a été prise par une ressource existante. Pour résoudre ce problème, utilisez un autre ID pour la ressource.

|Code d'erreur|Valeur hexadécimale |Description de l’erreur|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Erreur de l’opération stockage, erreur de conflit.  |

## <a name="410"></a>410

|Code d'erreur|Valeur hexadécimale |Description de l’erreur|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Pour la diffusion en continu en direct, lorsque le filtre qui a forceEndTimestamp défini sur true, le début ou la fin horodatage est en dehors de la fenêtre DVR actuelle.|

## <a name="412-precondition-failure"></a>Échec de la précondition 412

L’opération spécifiait un eTag est différent de la version disponible sur le serveur, autrement dit, une erreur d’accès concurrentiel optimiste. Relancez la demande après avoir lu la dernière version de la ressource et de la mise à jour de l’eTag dans la demande.

|Code d'erreur|Valeur hexadécimale |Description de l’erreur|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Le fragment demandé n’est pas prêt.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Erreur de l’opération stockage, un échec de la précondition.|

## <a name="415-unsupported-media-type"></a>Type de média non pris en charge 415

Le format de charge utile envoyé par le client est dans un format non pris en charge.

|Code d'erreur|Valeur hexadécimale |Description de l’erreur|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Doit pas appliquer le chiffrement sur le contenu déjà chiffré.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Le chiffrement n’est pas valide pour le format d’entrée.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Type de stratégie de remise n’est pas valide.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Les paramètres d’origine peuvent être partagées par plusieurs formats de sortie.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Le format de média ou un type non pris en charge. Par exemple, Media Services ne prend pas en charge le nombre de niveau de qualité qui est plus de 64. Dans la balise vidéo FLV, Media Services ne prend pas en charge une trame vidéo avec SPS plusieurs et plusieurs PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Le format d’entrée de ressource demandée n’est pas pris en charge. Media Services prend en charge (actif), Smooth MP4 (VoD) et télécharger des formats de progressif.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Le format de sortie demandé n’est pas pris en charge. Prend en charge de Media Services Smooth, DASH (CSF, CMAF), HLS (v3, v4, CMAF) et progressif télécharger formats.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Type de chiffrement non pris en charge s’est produite.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Le type de média demandé n’est pas pris en charge par le format de sortie. Les types pris en charge sont vidéo, audio ou un sous-titre « SUBT ».|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Le support de la ressource source a été encodé avec un format de média qui n’est pas compatible avec le format de sortie.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|La ressource source a été encodée avec un format vidéo n’est pas compatible avec le format de sortie. H.264 AVC, H.265 (HEVC, hev1 ou hvc1) sont pris en charge.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|La ressource source a été encodée avec un format audio qui n’est pas compatible avec le format de sortie. Formats audio pris en charge sont AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|La ressource protégée source ne peut pas être convertie au format de sortie.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Le format de protection n’est pas pris en charge par le format de sortie.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Le format de protection n’est pas pris en charge par le format d’entrée.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Unité non valide vidéo NAL, par exemple, uniquement la première NAL dans l’exemple peut être un AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Taille d’unité NAL non valide.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Valeur de longueur unitaire NAL non valide.|
|MPE_FILTER_INVALID|0x80890236|Filtres de manifeste dynamique non valides.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Versions de filtre non valide ou non pris en charge.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Type de filtre non valide.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Plage non valide est spécifiée par le filtre.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Attribut de suivi non valide est spécifié par le filtre.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Longueur de fenêtre de présentation non valide est spécifiée par le filtre.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|En direct d’interruption non valide est spécifié par le filtre.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Qu’un seul élément absTimeInHNS est pris en charge dans les filtres hérités.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Il n’a aucun flux de données du tout après avoir appliqué les filtres.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|La dynamique d’interruption est au-delà de la fenêtre du magnétoscope numérique.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|La dynamique d’interruption est supérieure à la fenêtre de présentation.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Dépassé filtres maximal autorisé par défaut dix (10).|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Opérateur de qualité vidéo de premier plusieurs n’est pas autorisé dans les filtres de requête combiné.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Le nombre d’attributs de vitesse de transmission de qualité première doit être un (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|Durée du segment HLS doit inférieurs à un tiers de la fenêtre DVR et HLS sauvegarde.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Durées de fragments doivent être inférieure ou égale à environ 20 secondes, ou les niveaux de qualité d’entrée ne sont pas alignées de temps.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Erreur DTS spécifiques, Impossible de trouver le ReservedBox quand il doit présenter dans le DTSSpecficBox pendant la DTS d’analyse.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS-erreur, aucun canal trouvé dans le DTSSpecficBox lors de l’analyse de zone DTS.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS-erreur, incompatibilité de type d’exemple dans le DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Erreur de DTS spécifiques, asset multi est définie, mais d’incompatibilité de type DTSH exemple.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Erreur DTS spécifiques, taille du flux core n’est pas valide.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Erreur DTS spécifiques, résolution d’échantillon n’est pas valide.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Erreur de DTS spécifique, sous-extension index de flux est non valide.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Erreur DTS spécifiques, numéro de bloc de flux de données secondaire n’est pas valide.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Erreur DTS spécifiques, fréquence d’échantillonnage n’est pas valide.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Erreur DTS spécifiques, le code de l’horloge de référence dans l’extension du flux de données secondaire n’est pas valide.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Erreur DTS spécifiques, le nombre de jeu de remappage des haut-parleurs n’est pas valide.|

Pour les articles de chiffrement et des exemples, consultez :

- [Concept : protection du contenu](content-protection-overview.md)
- [Concept : Stratégies de clé de contenu](content-key-policy-concept.md)
- [Concept : Stratégies de diffusion en continu](streaming-policy-concept.md)
- [Exemple : protéger avec le chiffrement AES](protect-with-aes128.md)
- [Exemple : protéger par DRM](protect-with-drm.md)

Pour obtenir des conseils de filtre, consultez :

- [Concept : manifestes dynamiques](filters-dynamic-manifest-overview.md)
- [Concept : filtres](filters-concept.md)
- [Exemple : créer des filtres avec les API REST](filters-dynamic-manifest-rest-howto.md)
- [Exemple : créer des filtres avec .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemple : créer des filtres avec l’interface CLI](filters-dynamic-manifest-cli-howto.md)

Pour les articles en direct et des exemples, consultez :

- [Concept : présentation de diffusion en continu en direct](live-streaming-overview.md)
- [Concept : Événements en direct et des sorties en direct](live-events-outputs-concept.md)
- [Exemple : didacticiel de diffusion en continu en direct](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 ne plage pas satisfaisante

|Code d'erreur|Valeur hexadécimale |Description de l’erreur|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Erreur d’opération de stockage, a retourné l’erreur http 416, plage non valide.|

## <a name="500-internal-server-error"></a>500 Erreur interne du serveur

Lors du traitement de la demande, Media Services rencontre une erreur qui empêche la poursuite du traitement.  

|Code d'erreur|Valeur hexadécimale |Description de l’erreur|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Code d’erreur Winhttp reçu et converti à partir de ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Code d’erreur Winhttp reçu et converti à partir de ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Code d’erreur Winhttp reçu et converti à partir d’à ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Erreur de l’opération stockage, InternalError général de l’une des erreurs HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Erreur de l’opération stockage, OperationTimedOut général de l’une des erreurs HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Erreur de l’opération stockage, autres erreurs HTTP 500 Erreur interne ou OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Service indisponible

Le serveur est actuellement incapable de recevoir des demandes. Cette erreur peut résulter d’un excès de demandes adressées au service. Le mécanisme de limitation de Media Services restreint l'utilisation des ressources pour les applications qui recourent de manière excessive au service.

> [!NOTE]
> Examinez le message d’erreur et la chaîne de code d’erreur pour obtenir des informations plus détaillées sur la raison pour laquelle vous avez reçu l’erreur 503. Cette erreur n’indique pas toujours une limitation.
> 

|Code d'erreur|Valeur hexadécimale |Description de l’erreur|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Erreur d’opération de stockage, a reçu l’erreur « occupé » serveur HTTP 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="see-also"></a>Voir aussi

- [Codes d’erreur d’encodage](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Concepts Azure Media Services](concepts-overview.md)
- [Quotas et limitations](limits-quotas-constraints.md)

## <a name="next-steps"></a>Étapes suivantes

[Exemple : accéder à code d’erreur et le Message à partir de ApiException avec .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
