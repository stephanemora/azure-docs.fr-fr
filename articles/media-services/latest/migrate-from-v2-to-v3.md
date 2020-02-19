---
title: Migrer d’Azure Media Services v2 vers v3 | Microsoft Docs
description: Cet article décrit les changements qui ont été introduits dans Azure Media Services v3 et montre les différences entre les deux versions. Il fournit également des conseils de migration pour le passage de Media Services v2 à Media Services v3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, flux, diffusion, en direct, hors connexion
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/02/2019
ms.author: juliako
ms.openlocfilehash: 3520b7d6b0fd67fdbff3e1dd78d038f36ad5f0af
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133422"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Conseils de migration pour le passage de Media Services v2 à Media Services v3

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` dans votre lecteur de flux RSS.

Cet article décrit les changements introduits dans Azure Media Services v3, montre les différences entre les deux versions, et fournit des conseils en matière de migration.

Si vous avez un service vidéo développé aujourd’hui sur la base des [API héritées de Media Services v2](../previous/media-services-overview.md), vous devez prendre en compte les instructions et considérations suivantes avant d’opérer une migration vers les API v3. Les API v3 présentent un grand nombre d’avantages et de fonctionnalités nouvelles qui améliorent l’expérience de développement et les fonctionnalités de Media Services. Toutefois, comme mentionné dans la section [Problèmes connus](#known-issues) de cet article, il existe certaines limitations résultant de différences entre les versions d’API. Cette page sera actualisée à mesure que l’équipe Media Services apportera des améliorations continues aux API v3, et traitera des différences entre les versions. 

> [!NOTE]
> Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer des ressources v3. Utilisez l’[API REST](https://aka.ms/ams-v3-rest-ref), l’interface [CLI](https://aka.ms/ams-v3-cli-ref) ou l’un des kits [SDK](media-services-apis-overview.md#sdks) pris en charge.

## <a name="benefits-of-media-services-v3"></a>Avantages de Media Services v3
  
### <a name="api-is-more-approachable"></a>L’API est plus accessible

*  v3 est basée sur une surface d’API unifiée qui expose des fonctionnalités de gestion et de fonctionnement qui s’appuient sur Azure Resource Manager. Vous pouvez utiliser les modèles Azure Resource Manager pour créer et déployer des transformations, des points de terminaison de streaming, des événements en direct, etc.
* Document sur la [spécification OpenAPI (anciennement Swagger)](https://aka.ms/ams-v3-rest-sdk).
    Expose le schéma pour tous les composants de service, dont l’encodage basé sur un fichier.
* Kits de développement logiciel (SDK) disponibles pour [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref) et Ruby.
* Intégration d’[Azure CLI](https://aka.ms/ams-v3-cli-ref) pour la prise en charge de scripts simples.

### <a name="new-features"></a>Nouvelles fonctionnalités

* Pour le traitement de travaux basé sur un fichier, vous pouvez utiliser une URL HTTP(S) comme entrée.<br/>Vous n’avez pas besoin de contenu déjà stocké dans Azure, ou de créer des ressources.
* Introduit le concept de [transformations](transforms-jobs-concept.md) pour le traitement de travaux basé sur fichier. Vous pouvez utiliser une transformation pour créer des configurations réutilisables, créer des modèles Azure Resource Manager, et isoler des paramètres de traitement entre plusieurs clients ou locataires.
* Un actif multimédia peut avoir plusieurs [localisateurs de streaming](streaming-locators-concept.md), chacun avec des paramètres différents d’[empaquetage dynamique](dynamic-packaging-overview.md) et de chiffrement dynamique.
* La [protection du contenu](content-key-policy-concept.md) prend en charge les fonctionnalités à plusieurs clés.
* Vous pouvez diffuser des événements en direct d’une durée maximale de 24 heures quand vous utilisez Media Services pour transcoder un flux de contribution à une seule vitesse de transmission en un flux de sortie à vitesse de transmission multiple.
* Nouvelle prise en charge du streaming en direct à faible latence sur des événements en direct. Pour plus d’informations, consultez [latence](live-event-latency.md).
* L’aperçu des événements en direct prend en charge l’[empaquetage dynamique](dynamic-packaging-overview.md) et le chiffrement dynamique. Ceci permet la protection du contenu sur l’aperçu, ainsi que l’empaquetage DASH et HLS.
* La sortie en direct est plus simple à utiliser que l’entité Program dans les API v2. 
* Prise en charge améliorée de RTMP (stabilité accrue et meilleure prise en charge de l’encodeur source).
* Ingestion sécurisée RTMPS.<br/>Quand vous créez un événement en direct, vous obtenez 4 URL de réception. Les 4 URL d’ingestion sont presque identiques, ont le même jeton de streaming (AppId) ; seule la partie du numéro de port est différente. Il existe deux URL principales et de secours pour RTMPS.   
* Vous disposez d’un contrôle d’accès en fonction du rôle (RBAC) sur vos entités. 

## <a name="changes-from-v2"></a>Changements par rapport à la v2

* Pour les ressources créées avec la version v3, Media Services prend en charge uniquement le [chiffrement du stockage côté serveur de Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Vous pouvez utiliser des API v3 avec des ressources créées à l’aide d’API v2 qui disposaient d’un [chiffrement de stockage](../previous/media-services-rest-storage-encryption.md) (AES 256) fourni par Media Services.
    * Vous ne pouvez pas créer de ressources avec le [chiffrement du stockage](../previous/media-services-rest-storage-encryption.md) AES 256 hérité à l’aide d’API v3.
* Si les propriétés de la [ressource](assets-concept.md) dans v3 sont différentes de celles de v2, voir [comment les propriétés sont mappées](assets-concept.md#map-v3-asset-properties-to-v2).
* Les kits de développement logiciel (SDK) v3 sont dissociés du Storage SDK, ce qui vous offre davantage de contrôle sur la version de SDK Stockage à utiliser, et évite les problèmes de gestion de version. 
* Dans les API v3, toutes les vitesses d’encodage sont exprimées en bits par seconde. Ceci diffère des préréglages de Media Encoder Standard v2. Par exemple, un débit en bits dans v2 exprimé sous la forme 128 Kbits/s, sera exprimé dans v3 sous la forme 128000 (bits/seconde). 
* Les entités AssetFiles, AccessPolicies et IngestManifests n’existent pas dans v3.
* La propriété IAsset.ParentAssets n’existe pas dans la version 3.
* ContentKeys n’est plus une entité, mais une propriété du localisateur de streaming.
* La prise en charge de la grille d’événements remplace NotificationEndpoints.
* Les entités suivantes ont été renommées :
    * JobOutput remplace Task et fait désormais partie d’un travail.
    * StreamingLocator remplace Locator.
    * LiveEvent remplace Channel.<br/>La facturation des événements en direct est basée sur les compteurs de canal live. Pour plus d’informations, consultez [facturation](live-event-states-billing.md) et [tarifs](https://azure.microsoft.com/pricing/details/media-services/).
    * LiveOutput remplace Program.
* Les sorties en direct démarrent dès leur création et s’arrêtent à leur suppression. Les programmes fonctionnaient différemment dans les API v2. Il fallait les démarrer après leur création.
* Pour obtenir des informations sur un travail, vous devez connaître le nom de la transformation sous lequel le travail a été créé. 
* Dans la v2, les fichiers XML de métadonnées [entrants](../previous/media-services-input-metadata-schema.md) et [sortants](../previous/media-services-output-metadata-schema.md) sont générés à la suite d’un travail d’encodage. Dans la v3, le format de métadonnées est passé de XML à JSON. 

> [!NOTE]
> Examinez les conventions d’affectation de noms appliquées aux [ressources Media Services v3](media-services-apis-overview.md#naming-conventions). Voir également [Attribution de noms à des objets blob](assets-concept.md#naming-blobs).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Différences de fonctionnalités par rapport aux API v2

Les API v3 présentent les différences de fonctionnalités suivantes par rapport aux API v2. Le gommage de ces différences est en cours.

* L’[Encodeur Premium](../previous/media-services-premium-workflow-encoder-formats.md) et les [processeurs d’analytique multimédia](../previous/media-services-analytics-overview.md) hérités (indexeur Azure Media Services 2 en préversion, Face Redactor, etc.) ne sont pas accessibles via v3.<br/>Les clients qui souhaitent migrer à partir de l’indexeur multimédia 1 ou 2 en préversion peuvent utiliser immédiatement l’AudioAnalyzer prédéfini dans l’API v3.  Cet nouveau préréglage contient davantage de fonctionnalités que l’ancien indexeur multimédia 1 ou 2. 
* La plupart des [fonctionnalités avancées de Media Encoder Standard dans les API v2](../previous/media-services-advanced-encoding-with-mes.md) ne sont actuellement pas disponibles dans v3, par exemple :
  
    * Combinaison de ressources
    * Superpositions
    * Rognage
    * Sprites de miniatures
    * Insertion d’une piste audio en mode silencieux lorsque l’entrée ne produit pas de son
    * Insertion d’une piste vidéo lorsque l’entrée ne comporte aucune vidéo
* Les événements en direct avec transcodage ne gèrent actuellement pas l’insertion d’ardoise à mi-parcours ni l’insertion de marqueur publicitaire par le biais d’un appel d’API. 

> [!NOTE]
> Veuillez marquer cet article d’un signet afin de pouvoir continuer à suivre les mises à jour.
 
## <a name="code-differences"></a>Différences de code

Le tableau suivant présente les différences de code entre v2 et v3 pour des scénarios courants.

|Scénario|API V2|API V3|
|---|---|---|
|Créer une ressource et charger un fichier |[Exemple .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Exemple .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Soumettre un travail|[Exemple .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Exemple .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Montre comment créer au préalable une transformation, puis soumettre un travail.|
|Publier une ressource avec chiffrement AES |1. Créer une ContentKeyAuthorizationPolicyOption<br/>2. Créer une ContentKeyAuthorizationPolicy<br/>3. Créer une AssetDeliveryPolicy<br/>4. Créer une ressource et charger du contenu OU soumettre un travail et utiliser une ressource en sortie<br/>5. Associer une AssetDeliveryPolicy à un Asset<br/>6. Créer une ContentKey<br/>7. Attacher un ContentKey à un Asset<br/>8. Créer une AccessPolicy<br/>9. Créer un Locator<br/><br/>[Exemple .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Créer une stratégie de clé de contenu<br/>2. Créer un Asset<br/>3. Charger du contenu ou utiliser un Asset comme JobOutput<br/>4. Créer un localisateur de streaming<br/><br/>[Exemple .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Obtenir des détails de travaux et gérer des travaux |[Gérer des travaux avec v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gérer des travaux avec v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Problèmes connus

* Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer des ressources v3. Utilisez l’[API REST](https://aka.ms/ams-v3-rest-sdk), Azure CLI ou l’un des kits de développement logiciel (SDK) pris en charge.
* Vous devez provisionner des unités réservées Multimédia (MRU) dans votre compte afin de contrôler la concurrence et les performances de vos travaux, en particulier ceux impliquant une analyse audio ou vidéo. Pour plus d’informations, consultez [Mise à l’échelle du traitement multimédia](../previous/media-services-scale-media-processing-overview.md). Vous pouvez gérer les MRU à l’aide de [CLI 2.0 pour Media Services v3](media-reserved-units-cli-how-to.md), du [portail Azure](../previous/media-services-portal-scale-media-processing.md) ou des [API v2](../previous/media-services-dotnet-encoding-units.md). Vous devez provisionner des unités réservées Multimédia, indépendamment de la version (v2 ou v3) des API Media Services que vous utilisez.
* Des entités Media Services créées avec l’API v3 ne peuvent pas être gérées avec l’API v2.  
* Toutes les entités de l’API V2 ne s’affichent pas automatiquement dans l’API V3.  Voici quelques exemples d’entités dans les deux versions et qui sont incompatibles :  
    * Les travaux et tâches créés dans v2 n’apparaissent pas dans v3, car elles ne sont pas associées à une transformation. Il est recommandé de basculer vers des transformations et travaux v3. Il y aura une période de temps relativement courte du basculement durant laquelle il sera nécessaire de surveiller les travaux v2 en cours.
    * Les canaux et programmes créés avec v2 (qui sont mappés à des événements et sorties en direct dans v3) ne peuvent plus être gérés avec v3. Il est recommandé de basculer vers des événements et sorties en direct v3 à un point d’arrêt approprié du canal.<br/>Actuellement, vous ne pouvez pas migrer en continu des canaux en cours d’exécution.  

> [!NOTE]
> Cette page sera actualisée à mesure que l’équipe Media Services apportera des améliorations continues aux API v3, et traitera des différences entre les versions.

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir combien il est facile de démarrer le codage et le streaming de fichiers vidéo, consultez [Diffuser des fichiers](stream-files-dotnet-quickstart.md). 

