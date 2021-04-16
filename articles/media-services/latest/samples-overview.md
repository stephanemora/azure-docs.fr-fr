---
title: Exemples Media Services v3
description: Cet article contient une liste de tous les exemples disponibles pour Media Services v3, classés par méthode et par SDK.  Les exemples incluent .NET, Node.JS, Python et Java ainsi que REST avec Postman.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1d827d734c434204ff6b7ec60d27e507ae626abd
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227685"
---
# <a name="media-services-v3-samples"></a>Exemples Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article contient une liste de tous les exemples disponibles pour Media Services, classés par méthode et par SDK.  Les exemples incluent .NET, Node.JS, Python et Java ainsi que REST avec Postman.

## <a name="rest-postman-collection"></a>Collection Postman REST

Les exemples [Postman REST](https://github.com/Azure-Samples/media-services-v3-rest-postman) comportent un environnement et une collection Postman que vous pouvez importer dans le client Postman.

## <a name="samples-by-sdk"></a>Exemples par SDK

Vous trouverez une description et des liens vers les exemples que vous pouvez rechercher dans chacun des onglets.

## <a name="net"></a>[.NET](#tab/net/)

| Dossier | Description |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESPredefinedPreset)|Procédure pour envoyer un travail en utilisant un préréglage intégré et une entrée d’URL HTTP, publier un actif multimédia de sortie pour le streaming et télécharger les résultats à des fins de vérification.|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)|Procédure pour envoyer un travail en utilisant un préréglage d’encodage H.264 personnalisé et une entrée d’URL HTTP, publier un actif multimédia de sortie pour le streaming et télécharger les résultats à des fins de vérification.|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)|Procédure pour envoyer un travail en utilisant un préréglage d’encodage HEVC personnalisé et une entrée d’URL HTTP, publier un actif multimédia de sortie pour le streaming et télécharger les résultats à des fins de vérification.|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)|Procédure pour envoyer un travail en utilisant JobInputSequence pour assembler 2 actifs multimédia ou plus qui peuvent être découpés par heure de début ou de fin. Le fichier encodé obtenu est une vidéo unique avec tous les actifs multimédia assemblés.  L’exemple publiera également un actif multimédia de sortie pour le streaming et téléchargera les résultats à des fins de vérification.|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPresetAndSprite)|Procédure pour envoyer un travail en utilisant un préréglage personnalisé avec un sprite de miniature et une entrée d’URL HTTP, publier un actif multimédia de sortie pour le streaming et télécharger les résultats à des fins de vérification.|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|Procédure pour créer un LiveEvent avec une archive complète jusqu’à 25 heures et un filtre sur l’actif multimédia avec une fenêtre DVR de 5 minutes. Procédure pour utiliser un filtre afin de créer un localisateur pour le streaming.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Procédure pour créer une transformation de l’analyseur vidéo, charger un fichier vidéo sur un actif multimédia d’entrée, envoyer un travail avec la transformation et télécharger les résultats à des fins de vérification.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Procédure pour créer une transformation de l’analyseur audio, charger un fichier multimédia sur un actif multimédia d’entrée, envoyer un travail avec la transformation et télécharger les résultats à des fins de vérification.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Procédure pour créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, créer une stratégie ContentKeyPolicy à l’aide d’une clé secrète, l’associer à StreamingLocator, obtenir un jeton et imprimer une URL pour la lecture dans Azure Media Player. Lorsqu’un lecteur demande un flux, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu avec AES-128 et Azure Media Player utilise le jeton pour le déchiffrer.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Procédure pour créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, créer une stratégie ContentKeyPolicy avec une configuration Widevine à l’aide d’une clé secrète, l’associer à StreamingLocator, obtenir un jeton et imprimer une URL pour la lecture dans un lecteur Widevine. Quand un utilisateur demande le contenu protégé par Widevine, l’application de lecteur demande une licence auprès du service de licence Media Services. Si l’application de lecteur est autorisée, le service de licence Media Services remet une licence au lecteur. Une licence Widevine contient la clé de déchiffrement qui peut être utilisée par le lecteur client pour déchiffrer et diffuser le contenu.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Procédure pour créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, créer une stratégie ContentKeyPolicy avec une configuration PlayReady à l’aide d’une clé secrète, l’associer à StreamingLocator, obtenir un jeton et imprimer une URL pour la lecture dans Azure Media Player. Quand un utilisateur demande le contenu protégé par PlayReady, l’application de lecteur demande une licence auprès du service de licence Media Services. Si l’application de lecteur est autorisée, le service de licence Media Services remet une licence au lecteur. Une licence PlayReady contient la clé de déchiffrement qui permet au lecteur client de déchiffrer le contenu et de le lire en continu.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|Procédure pour chiffrer dynamiquement votre contenu avec PlayReady et Widevine DRM, et lire le contenu sans demander de licence auprès du service de licence. Il montre comment créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, créer une stratégie ContentKeyPolicy avec restriction ouverte et une configuration permanente PlayReady/Widevine, l’associer à StreamingLocator et imprimer une URL pour la lecture.|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Procédure pour créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, créer un filtre d’actif multimédia et un filtre de compte, les associer aux localisateurs de streaming et imprimer des URL pour la lecture.|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Procédure pour créer une transformation avec un préréglage AdaptiveStreaming intégré, envoyer un travail, publier un actif multimédia de sortie pour le streaming DASH et HLS.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | Aide et bonnes pratiques pour un système de production utilisant l’analytique ou l’encodage à la demande. Les lecteurs doivent commencer par l’article associé [Haute disponibilité avec Media Services et VOD (vidéo à la demande)](https://docs.microsoft.com/azure/media-services/latest/architecture-high-availability-encoding-concept). Un fichier solution distinct est fourni pour l’exemple [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/HighAvailabilityEncodingStreaming/README.md). |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Dossier|Description|
|---|---|
|[HelloWorld-ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |Procédure pour se connecter et lister les actifs multimédia |
|[En direct](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| Procédure pour effectuer un streaming en direct de base. **AVERTISSEMENT** : Veillez à vérifier que toutes les ressources sont nettoyées et qu’elles ne sont plus facturées dans le portail lors du streaming en direct.|
|[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| Procédure pour charger un fichier local ou un encodage à partir d’une URL source, utiliser un SDK de stockage pour télécharger du contenu et diffuser sur un lecteur. |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Procédure pour encoder et diffuser à l’aide de Widevine et PlayReady DRM |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| Procédure pour utiliser les préréglages de l’analyseur vidéo et audio afin de générer des métadonnées et des insights à partir d’un fichier vidéo ou audio. |

## <a name="python"></a>[Python](#tab/python)

Actuellement, il existe un exemple Python, l’[encodage de base avec Python](https://github.com/Azure-Samples/media-services-v3-python).

## <a name="java"></a>[Java](#tab/java)

|Dossier|Description|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Procédure pour analyser l’audio dans un fichier multimédia. |
|Protection du contenu|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|Procédure pour chiffrer dynamiquement votre contenu avec AES-128.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|Procédure pour chiffrer dynamiquement votre contenu avec PlayReady DRM.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Procédure pour chiffrer dynamiquement votre contenu avec Widevine DRM.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|Procédure pour chiffrer dynamiquement votre contenu avec FairPlay DRM et lire le contenu sans demander de licence auprès du service de licence.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|Procédure pour chiffrer dynamiquement votre contenu avec PlayReady et Widevine DRM, et lire le contenu sans demander de licence auprès du service de licence.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Procédure pour filtrer le contenu à l’aide des filtres d’actif multimédia et de compte.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|Procédure pour empaqueter dynamiquement du contenu VOD en HLS/DASH pour le streaming.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|Procédure pour créer un LiveEvent avec une archive complète jusqu’à 25 heures et un filtre sur l’actif multimédia avec une fenêtre DVR de 5 minutes, créer un localisateur pour le streaming et utiliser le filtre.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|Procédure pour créer un LiveEvent avec une archive complète jusqu’à 25 heures et un filtre sur l’actif multimédia avec une fenêtre DVR de 5 minutes, créer un localisateur pour le streaming et utiliser le filtre.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Procédure pour créer une transformation d’encodage personnalisée à l’aide des paramètres StandardEncoderPreset.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|Procédure pour envoyer un travail en utilisant un préréglage intégré et une entrée d’URL HTTP, publier un actif multimédia de sortie pour le streaming et télécharger les résultats à des fins de vérification.|

---
