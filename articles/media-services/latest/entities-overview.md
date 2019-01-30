---
title: Vue d'ensemble des entités Azure Media Services - Azure | Microsoft Docs
description: Cet article fournit une vue d'ensemble des entités Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 902014ff3c242a18b3872ba490845eb0923f39a4
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451185"
---
# <a name="azure-media-services-entities-overview"></a>Vue d'ensemble des entités Azure Media Services

Cet article offre un bref aperçu des entités Azure Media Services et renvoie vers un article pour plus d'informations sur chaque entité. 

| Rubrique | Description |
|---|---|
| [Filtres de comptes et d'éléments multimédia](filters-dynamic-manifest-overview.md)|Lorsque vous transmettez du contenu à des clients (diffusion en continu d'événements en direct ou vidéos à la demande), le fichier manifeste de l'élément multimédia par défaut ne permet pas toujours au client d'interagir avec le contenu comme il le voudrait. Azure Media Services vous permet de définir des [filtres de compte](https://docs.microsoft.com/rest/api/media/accountfilters) et des [filtres d'éléments multimédia](https://docs.microsoft.com/rest/api/media/assetfilters). Ensuite, utilisez les **manifestes dynamiques** basés sur les filtres prédéfinis. |
| [Éléments multimédias](assets-concept.md)|Une entité de type [Élément multimédia](https://docs.microsoft.com/rest/api/media/assets) contient des fichiers numériques (vidéo, audio, images, collections de miniatures, légendes et fichiers de sous-titres) et les métadonnées associées à ces fichiers. Une fois les fichiers numériques chargés dans un élément multimédia, ils peuvent être utilisés dans des workflows Media Services d'encodage, de diffusion en continu et d'analyse de contenu.|
| [Stratégies de clé de contenu](content-key-policy-concept.md)|Vous pouvez utiliser Media Services pour sécuriser votre contenu multimédia du moment où il quitte votre ordinateur jusqu'à la remise, en passant par le stockage et le traitement. Media Services vous permet de transmettre votre contenu dynamique ou à la demande chiffré dynamiquement avec la norme Advanced Encryption Standard (AES-128) ou un des principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés.|
| [Événements en direct et Sorties en direct](live-events-outputs-concept.md)|Media Services vous permet de transmettre des événements en direct auprès de vos clients dans le cloud Azure. Pour configurer vos événements de streaming en direct dans Media Services v3, vous devez en savoir plus sur les [Événements en direct](https://docs.microsoft.com/rest/api/media/liveevents) et les [Sorties en direct](https://docs.microsoft.com/rest/api/media/liveoutputs).|
| [Points de terminaison de streaming](streaming-endpoint-concept.md)|Une entité [Points de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) représente un service de diffusion en continu qui peut fournir du contenu directement à une application de lecteur cliente ou à un réseau de distribution de contenu (CDN) en vue de sa redistribution. Le flux sortant d’un service de point de terminaison de streaming peut être un flux dynamique ou une ressource de vidéo à la demande dans votre compte Media Services. Quand vous créez un compte Media Services, un point de terminaison de streaming **par défaut** est créé pour vous dans l’état Arrêté. Vous ne pouvez pas supprimer le point de terminaison de streaming **par défaut**. Vous pouvez créer d’autres points de terminaison de streaming sous votre compte. Pour démarrer le streaming de vidéos, vous devez démarrer le point de terminaison de streaming à partir duquel vous souhaitez diffuser votre vidéo. |
| [Localisateurs de diffusion en continu](streaming-locators-concept.md)|Vous devez fournir à vos clients une URL qu'ils pourront utiliser pour lire les fichiers audio ou vidéo encodés. Vous devez également créer un [localisateur de diffusion en continu](https://docs.microsoft.com/rest/api/media/streaminglocators) et générer les URL de diffusion en continu.|
| [Stratégies de diffusion en continu](streaming-policy-concept.md)| Les [stratégies de diffusion en continu](https://docs.microsoft.com/rest/api/media/streamingpolicies) vous permettent de définir les protocoles de diffusion en continu et les options de chiffrement de vos localisateurs de diffusion en continu. Vous pouvez spécifier le nom d'une stratégie de diffusion en continu personnalisée que vous avez créée ou utiliser l'une des stratégies prédéfinies proposées par Media Services. <br/><br/>Lorsque vous utilisez une stratégie de diffusion en continu personnalisée, vous devez concevoir un ensemble limité de ces stratégies pour votre compte Media Services et les réutiliser pour vos localisateurs de diffusion en continu chaque fois que les mêmes protocoles et options de chiffrement sont nécessaires. Vous ne devez pas créer une stratégie de diffusion en continu pour chaque localisateur de diffusion en continu.|
| [Transformations et travaux](transforms-jobs-concept.md)|Utilisez des [transformations](https://docs.microsoft.com/rest/api/media/transforms) pour configurer des tâches courantes de codage ou d'analyse des vidéos. Chaque **transformation** décrit une recette, ou un flux de travail de tâches pour le traitement de vos fichiers vidéo ou audio.<br/><br/>Un [travail](https://docs.microsoft.com/rest/api/media/jobs) est la requête réelle envoyée à Azure Media Services pour appliquer la **transformation** à un contenu vidéo ou audio d’entrée donné. Le **travail** spécifie des informations comme l’emplacement de la vidéo d’entrée et celui de la sortie. Vous pouvez spécifier l'emplacement de votre vidéo d'entrée en utilisant : des URL HTTPS, des URL SAS ou un élément multimédia.|

## <a name="next-steps"></a>Étapes suivantes

[Diffuser un fichier](stream-files-dotnet-quickstart.md)
