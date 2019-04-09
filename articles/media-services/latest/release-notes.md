---
title: Notes de publication Azure Media Services v3 | Microsoft Docs
description: Pour vous informer des développements les plus récents, cet article détaille les toutes dernières mises à jour concernant Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: de5432c4e04fb0cfaf0517426fe9ee9da2a57b37
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058082"
---
# <a name="azure-media-services-v3-release-notes"></a>Notes de publication Azure Media Services v3

Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

* Versions les plus récentes
* Problèmes connus
* Résolution des bogues
* Fonctionnalités dépréciées

## <a name="known-issues"></a>Problèmes connus

> [!NOTE]
> Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer des ressources v3. Utilisez l’[API REST](https://aka.ms/ams-v3-rest-sdk), Azure CLI ou l’un des kits de développement logiciel (SDK) pris en charge.

Pour plus d’informations, consultez [Conseils de migration pour le passage de Media Services v2 à Media Services v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="march-2019"></a>Mars 2019

Maintenant l’empaquetage dynamique prend en charge les Atmos de Dolby. Pour plus d’informations, consultez [codecs Audio pris en charge par l’empaquetage dynamique](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

Vous pouvez maintenant spécifier une liste de filtres de compte d’actif ou, s’applique également à votre localisateur de diffusion en continu. Pour plus d’informations, consultez [associer des filtres de localisateur de diffusion en continu](filters-concept.md#associate-filters-with-streaming-locator).

## <a name="february-2019"></a>Février 2019

Media Services v3 est maintenant pris en charge dans les clouds Azure nationaux. Toutes les fonctionnalités ne sont pas encore disponibles dans tous les clouds. Pour en savoir plus, consultez [Clouds et régions dans lesquels Azure Media Services v3 existe](azure-clouds-regions.md).

L'événement [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) a été ajouté aux schémas Azure Event Grid de Media Services.

## <a name="january-2019"></a>Janvier 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Fichiers Media Encoder Standard et MPI 

En cas d'encodage avec Media Encoder Standard pour produire des fichiers MP4, un nouveau fichier .mpi est généré et ajouté à la ressource de sortie. Ce fichier MPI est destiné à améliorer les performances pour les scénarios d'[empaquetage dynamique](dynamic-packaging-overview.md) et de diffusion en continu.

Vous ne devez ni modifier ni supprimer le fichier MPI, ni dépendre de l'existence (ou non) d'un tel fichier dans votre service.

## <a name="december-2018"></a>Décembre 2018

Les mises à jour de la version mise à la disposition générale de l'API V3 incluent :
       
* Les propriétés **PresentationTimeRange** ne sont plus « obligatoires » pour **AssetFilters** et **AccountFilters**. 
* Les options de requête $top et $skip de **Jobs** et **Transforms** ont été supprimées et $orderby a été ajouté. Lors de l'ajout de la nouvelle fonctionnalité de commande, il a été découvert que les options $top et $skip avaient déjà été accidentellement exposées, même si elles n'avaient pas été implémentées.
* L'extensibilité de l'énumération a été réactivée. Cette fonctionnalité a été activée dans les préversions du kit de développement logiciel (SDK), puis accidentellement désactivée dans la version mise à la disposition générale.
* Deux stratégies de diffusion en continu prédéfinies ont été renommées. **SecureStreaming** s'appelle désormais **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** s'appelle désormais **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Novembre 2018

Le module CLI 2.0 est désormais disponible pour [Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Nouvelles commandes

- [compte d’ams AZ](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [filtre de compte AZ ams](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [ressource d’ams AZ](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [AZ ams asset-filtre](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [travail d’ams AZ](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [AZ ams-terminaison de streaming](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [ams AZ de diffusion en continu-recherche](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) : permet de gérer les unités réservées Multimédia. Pour plus d’informations, consultez [Mise à l’échelle des unités réservées Multimédia](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nouvelles fonctionnalités et dernières modifications

#### <a name="asset-commands"></a>Commandes des éléments multimédia

- ```--storage-account``` et ```--container``` arguments ajoutés.
- Les valeurs par défaut pour le délai d’expiration (présent + 23h) et les autorisations (lecture) dans la commande ```az ams asset get-sas-url``` ont été ajoutées.

#### <a name="job-commands"></a>Commandes des tâches

- ```--correlation-data``` et ```--label``` arguments ajoutés
- ```--output-asset-names``` renommé en ```--output-assets```. Il accepte désormais une liste de ressources séparée par des espaces au format « assetName=label ». Une ressource sans étiquette peut être envoyée comme ceci : « assetName= ».

#### <a name="streaming-locator-commands"></a>Commandes du localisateur de diffusion en continu

- ```az ams streaming locator``` commande de base est remplacé par ```az ams streaming-locator```.
- ```--streaming-locator-id``` et ```--alternative-media-id support``` arguments ajoutés.
- ```--content-keys argument``` argument mis à jour.
- ```--content-policy-name``` renommé en ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Commandes des stratégies de diffusion en continu

- ```az ams streaming policy``` commande de base est remplacé par ```az ams streaming-policy```.
- La prise en charge des paramètres de chiffrement dans ```az ams streaming-policy create``` a été ajoutée.

#### <a name="transform-commands"></a>Commandes de transformation

- ```--preset-names``` argument remplacé par ```--preset```. À présent vous ne pouvez définir qu’une sortie/présélection à la fois (pour en ajouter d’autres, vous devez exécuter ```az ams transform output add```). En outre, vous pouvez définir StandardEncoderPreset de façon personnalisée en transmettant le chemin à votre JSON personnalisé.
- ```az ams transform output remove``` peut être effectuée en passant l’index de sortie à supprimer.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` arguments ajoutés dans ```az ams transform create``` et ```az ams transform output add``` commandes.

## <a name="october-2018---ga"></a>Octobre 2018 - Mise à la disposition générale

Cette section décrit les mises à jour d’octobre d’Azure Media Services (AMS).

### <a name="rest-v3-ga-release"></a>Version en disponibilité générale de REST v3

La [version en disponibilité générale de REST v3](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) contient davantage d’API pour les filtres de manifeste de niveau En direct, Compte/Élément multimédia, ainsi qu’une prise en charge de DRM.

#### <a name="azure-resource-management"></a>Gestion des ressources Azure 

La prise en charge de la gestion des ressources Azure permet l’unification des API de gestion et des opérations (désormais tout se trouve à un même emplacement).

À partir de cette version, vous pouvez utiliser des modèles Resource Manager pour créer des événements en direct.

#### <a name="improvement-of-asset-operations"></a>Amélioration des opérations sur les éléments multimédias 

Voici les améliorations qui ont été apportées :

- Ingestion à partir d’URL HTTP(s) ou d’URL SAP de Stockage Blob Azure.
- Possibilité de spécifier des noms de conteneurs personnalisés pour les éléments multimédias. 
- Meilleure prise en charge de la sortie pour créer des flux de travail personnalisés avec Azure Functions.

#### <a name="new-transform-object"></a>Nouvel objet Transform

Le nouvel objet **Transform** simplifie le modèle d’encodage. Le nouvel objet facilite la création et le partage de modèles et de préréglages Resource Manager d’encodage. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Authentification et RBAC Azure Active Directory

L’authentification et le contrôle d’accès en fonction du rôle (RBAC) Azure AD autorise les transformations sécurisées, les événements en direct, les stratégies de clé de contenu ou les éléments multimédias par rôle ou utilisateurs dans Azure AD.

#### <a name="client-sdks"></a>Kits de développement logiciel (SDK) client  

Langages pris en charge dans Media Services v3 : .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Mises à jour de l’encodage en direct

Voici les mises à jour de l’encodage en direct qui ont été apportées :

- Nouveau mode de faible latence pour le direct (10 secondes de bout en bout).
- Prise en charge améliorée de RTMP (stabilité accrue et meilleure prise en charge de l’encodeur source).
- Ingestion sécurisée RTMPS.

    Quand vous créez un événement en direct, vous obtenez maintenant 4 URL d’ingestion. Les 4 URL d’ingestion sont presque identiques, ont le même jeton de streaming (AppId) ; seule la partie du numéro de port est différente. Il existe deux URL principales et de secours pour RTMPS. 
- Prise en charge d’un transcodage de 24 heures. 
- Prise en charge améliorée de la signalisation des annonces dans RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Prise en charge améliorée d’Event Grid

Voici les améliorations de la prise en charge d’Event Grid que vous pouvez constater :

- Intégration d’Azure EventGrid pour un développement facilité avec Logic Apps et Azure Functions. 
- Abonnement aux événements Encodage, Canaux live, etc.

### <a name="cmaf-support"></a>Prise en charge de CMAF

Prise en charge du chiffrement CMAF et « cbcs » pour les lecteurs Apple HLS (iOS 11+) et MPEG-DASH qui prennent en charge CMAF.

### <a name="video-indexer"></a>Video Indexer

La version en disponibilité générale de l’indexeur vidéo a été annoncée en août. Pour être au fait des dernières informations concernant les fonctionnalités actuellement prises en charge, consultez [Qu’est-ce que Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Modifications planifiées

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Le module Azure CLI 2.0 qui inclut des opérations sur toutes les fonctionnalités (dont En direct, Stratégies de clé de contenu, Filtres de comptes/éléments multimédias, Stratégies de streaming) sera bientôt disponible. 

### <a name="known-issues"></a>Problèmes connus

Seuls les clients qui utilisaient la préversion de l’API pour les filtres d’éléments multimédias ou de comptes sont concernés par le problème suivant.

Si vous avez créé des filtres d’éléments multimédias ou de comptes entre le 28/09 et le 12/10 avec l’interface CLI ou les API Media Services v3, vous devez supprimer tous les filtres d’éléments multimédias et de comptes et les recréer en raison d’un conflit de version. 

## <a name="may-2018---preview"></a>Mai 2018 - Préversion

### <a name="net-sdk"></a>Kit de développement logiciel (SDK) .NET

Les fonctionnalités suivantes sont présentes dans le kit SDK .NET :

* **Transformations** et **Travaux** pour encoder ou analyser le contenu du média. Pour obtenir des exemples, consultez [Diffuser des fichiers](stream-files-tutorial-with-api.md) et [Analyser](analyze-videos-tutorial-with-api.md).
* **StreamingLocators** pour la publication et la diffusion en continu de contenu sur les appareils de l’utilisateur final
* **StreamingPolicies** et **ContentKeyPolicies** pour configurer la distribution de clés et la protection du contenu (DRM) lors de la remise de contenu.
* **LiveEvents** et **LiveOutputs** pour configurer l’ingestion et l’archivage de contenu en continu en direct.
* **Éléments multimédias** pour stocker et publier du contenu multimédia dans le stockage Azure. 
* **StreamingEndpoints** pour configurer et mettre à l’échelle l’empaquetage dynamique, le chiffrement et la diffusion en continu du contenu multimédia en direct et à la demande.

### <a name="known-issues"></a>Problèmes connus

* Quand vous soumettez un travail, vous pouvez spécifier l’ingestion de votre vidéo source à l’aide d’URL HTTPS, d’URL SAP ou de chemins de fichiers situés dans le stockage Blob Azure. AMS v3 ne prend pas en charge l’encodage de transfert mémorisé en bloc sur les URL HTTPS.

## <a name="next-steps"></a>Étapes suivantes

[Présentation](media-services-overview.md)
