---
title: Guide de migration dans un scénario basé sur l’encodage
description: Cet article fournit des recommandations basées sur un scénario d’encodage, qui vous aideront à opérer une migration de la version v2 à la version v3 d’Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 2cdf068d31cf621538eb265977ea0dae20e16e85
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109320"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Recommandations en matière de migration basées sur un scénario d’encodage

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-4.svg)

Cet article fournit des recommandations basées sur un scénario d’encodage, qui vous aideront à opérer une migration de la version v2 à la version v3 d’Azure Media Services.

## <a name="prerequisites"></a>Prérequis

Avant de modifier votre flux de travail d’encodage, il est recommandé de comprendre les différences dans la façon dont le stockage est géré.  Dans AMS v3, l’API de Stockage Azure est utilisée pour gérer les comptes de stockage associés à votre compte Media Services.

> [!NOTE]
> Les travaux et tâches créés dans v2 n’apparaissent pas dans v3, car ils ne sont pas associés à une transformation. Il est recommandé de basculer vers des transformations et travaux v3.

## <a name="encoding-workflow-comparison"></a>Comparaison du flux de travail d’encodage

Prenez quelques minutes pour examiner les organigrammes ci-dessous, qui présentent une comparaison visuelle des flux de travail d’encodage pour les versions v2 et v3.

### <a name="v2-encoding-workflow"></a>Flux de travail d’encodage v2

Cliquez sur l’image ci-dessous pour afficher une version plus volumineuse.

[![Flux de travail d’encodage pour V2](./media/migration-guide/V2-pretty.svg) ](./media/migration-guide/V2-pretty.svg#lightbox)

1. Configuration
    1. Créez un ressource ou utilisez une ressource existante. Si vous utilisez une nouvelle ressource, chargez le contenu vers celle-ci. Si vous utilisez une ressource existante, vous devez encoder des fichiers qui existent déjà dans la ressource.
    2. Copiez les valeurs des éléments suivants :
        - ID ou objet de processeur multimédia ;
        - chaîne d’encodeur (nom) de l’encodeur que vous souhaitez utiliser ;
        - ID de ressource de la nouvelle ressource ou de la ressource existante.
    3. Pour la surveillance, créez un travail, un abonnement aux notifications de niveau tâche ou un gestionnaire d’événements SDK.
2. Créez le travail contenant les tâches. Chaque tâche doit inclure les éléments ci-dessus et :
    - une directive stipulant qu’une ressource de sortie doit être créée,  que créée système ;
    - Nom facultatif pour la ressource de sortie.
3. Envoyez le travail.
4. Surveillez la tâche.

### <a name="v3-encoding-workflow"></a>Flux de travail d’encodage v3

[![Flux de travail d’encodage pour v3](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. Configurer
    1. Créez une ressource ou utilisez une ressource existante. Si vous utilisez une nouvelle ressource, chargez le contenu vers celle-ci. Si vous utilisez une ressource existante, vous devez encoder des fichiers qui existent déjà dans la ressource. Vous *ne devez pas charger plus de contenu dans cette ressource.*
    1. Créez une ressource de sortie.  La ressource de sortie est l’emplacement où seront stockés les fichiers encodés et les métadonnées d’entrée et de sortie.
    1. Obtenez les valeurs pour la transformation :
        - présélection d’encodeur Standard ;
        - groupe de ressources AMS ;
        - nom du compte AMS.
    1. Créez la transformation ou utilisez une transformation existante.  Les transformations sont réutilisables. Il n’est pas nécessaire de créer une transformation chaque fois que vous souhaitez soumettre un travail.
1. Créer un travail
    1. Pour le travail, obtenez les valeurs pour les éléments suivants :
        - nom de la transformation ;
        - URI de base de l’URL SAS de votre ressource, chemin d’accès source HTTPs de votre partage de fichiers ou chemin d’accès local des fichiers ; La `JobInputAsset` peut également utiliser un nom de ressource en guise d’entrée.
        - nom(s) de fichier ;
        - ressource(s) de sortie ;
        - Un groupe de ressources
        - nom du compte AMS.  
1. Utilisez [Event Grid](monitoring/monitor-events-portal-how-to.md) pour surveiller votre travail.
1. Envoyez le travail.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Présélections personnalisées de l’encodage v2 à l’encodage V3

Si votre code V2 a appelé l’encodeur Standard avec une présélection personnalisée, vous commencez par créer une transformation avec la présélection d’encodeur Standard personnalisé avant de soumettre un travail.

Les présélections personnalisées sont désormais JSON et ne sont plus basées sur XML. Recréez votre présélection en JSON, en suivant le schéma de présélection personnalisée, tel que défini dans la documentation de l’[API Transform Open (Swagger)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json).

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>Fichiers de métadonnées d’entrée et de sortie d’une tâche d’encodage

Dans la v2, les fichiers XML de métadonnées entrants et sortants sont générés à la suite d’un travail d’encodage. Dans la v3, le format de métadonnées est passé de XML à JSON. Pour plus d’informations sur les métadonnées, consultez [Métadonnées d’entrée](input-metadata-schema.md) et [Métadonnées de sortie](output-metadata-schema.md).

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>D’encodeur Premium à encodeur Standard v3 ou solutions basées sur les partenaires

L’API v2 ne prend plus en charge l’encodeur Premium. Si vous avez précédemment utilisé l’encodeur Premium basé sur un flux de travail pour HEVC, l’encodage doit opérer une migration vers le nouvel [encodeur Standard](encode-media-encoder-standard-formats-reference.md) v3 avec prise en charge de l’encodage HEVC.

Si vous avez besoin des fonctionnalités de flux de travail avancées de l’encodeur Premium, il est recommandé de commencer à utiliser une solution de partenaire d’encodage avancé Azure de [Imagine Communications](https://imaginecommunications.com), [Telestream](https://www.telestream.net) ou [Bitmovin](https://bitmovin.com).

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>Travaux avec des entrées qui se trouvent sur des URL hébergées HTTPS

Vous pouvez à présent soumettre des travaux dans v3 à partir de fichiers stockés dans Stockage Azure, localement, ou sur des serveurs Web externes à l’aide de la [prise en charge d’entrée de travail HTTP(S)](job-input-from-http-how-to.md).

Si vous avez précédemment utilisé des flux de travail pour copier des fichiers à partir de fichiers d’objets blob Azure dans des ressources vides avant de soumettre des travaux, vous pouvez simplifier votre flux de travail en passant une URL SAS pour le fichier dans le Stockage Blob Azure directement dans le travail.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Transcription audio d’indexeur v1 au nouveau « mode de base » AudioAnalyzer

Pour les clients qui utilisent le processeur indexeur v1 dans l’API v2, vous devez créer une transformation qui appelle le nouvel `AudioAnalyzer` en [mode de base](how-to-create-basic-audio-transform.md) avant de soumettre un travail.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>Concepts, tutoriels et guides pratiques concernant l’encodage, les transformations et les travaux

### <a name="concepts"></a>Concepts

- [Encodage vidéo et audio avec Media Services](encode-concept.md)
- [Codecs et formats de Media Encoder Standard](encode-media-encoder-standard-formats-reference.md)
- [Encoder avec une échelle de débit générée automatiquement](encode-autogen-bitrate-ladder.md)
- [Utiliser la présélection de l’encodage sensible au contenu pour rechercher la valeur optimale de vitesse de transmission pour une résolution donnée](encode-content-aware-concept.md)
- [Unités réservées multimédias](concept-media-reserved-units.md)
- [Métadonnées d’entrée](input-metadata-schema.md)
- [Métadonnées de sortie](output-metadata-schema.md)
- [Empaquetage dynamique dans Media Services v3 : codecs audio](encode-dynamic-packaging-concept.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Tutoriels

- [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu – .NET](stream-files-dotnet-quickstart.md)
- [Tutoriel : Télécharger, encoder et diffuser des vidéos avec Media Services v3](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Guides pratiques

- [Créer une entrée de travail à partir d’une URL HTTPS](job-input-from-http-how-to.md)
- [Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md)
- [Créer une transformation audio simple](how-to-create-basic-audio-transform.md)
- Avec .NET
  - [Comment encoder avec une transformation personnalisée - .NET](encode-custom-presets-how-to.md)
  - [Comment créer une superposition à l’aide de Media Encoder Standard](how-to-create-overlay.md)
  - [Comment générer des miniatures à l’aide de Media Encoder Standard avec .NET](media-services-generate-thumbnails-dotnet.md)
- Avec l’interface de ligne de commande Azure
  - [Comment encoder une transformation personnalisée avec Azure CLI](encode-custom-preset-cli-how-to.md)
- Avec REST
  - [Comment encoder avec une transformation personnalisée - REST](encode-custom-preset-rest-how-to.md)
  - [Comment générer des miniatures à l’aide de l’encodeur standard avec REST](media-services-generate-thumbnails-rest.md)
- [Sous-découper une vidéo lors de l’encodage avec Media Services - .NET](subclip-video-dotnet-howto.md)
- [Sous-découper une vidéo lors de l’encodage avec Media Services - REST](subclip-video-rest-howto.md)

## <a name="samples"></a>Exemples

Vous pouvez aussi [comparer les codes V2 et V3 dans les exemples de code](migrate-v-2-v-3-migration-samples.md).
