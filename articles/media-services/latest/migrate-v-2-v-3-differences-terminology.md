---
title: Changements de terminologie et d’entité pour la version v3 de Media Services
description: Cet article décrit les différences de terminologie entre les versions v2 et v3 d’Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 42c4c91b6715ffec4c734632c69623206bc6dbae
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076000"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Changements de terminologie et d’entités entre les versions V2 et V3 d’Azure Media Services

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-2.svg)

Cet article décrit les différences de terminologie entre les versions v2 et v3 d’Azure Media Services.

## <a name="naming-conventions"></a>Conventions d’affectation de noms

Examinez les conventions d’affectation de noms appliquées aux ressources Media Services V3. Examinez également [Affectation de noms à des objets blob](assets-concept.md#naming)

## <a name="terminology-changes"></a>Changements terminologiques

- Un *localisateur* est désormais appelé *Localisateur de streaming*.
- Un *Canal* est désormais appelé *Événement en direct*.
- Un *Programme* est désormais appelé *Sortie en direct*.
- Une *Tâche* est désormais appelée *JobOutput*. Celui-ci fait partie d’un travail.

## <a name="entity-changes"></a>Modifications d’entités
| **Entité v2**<!-- row --> | **Entité v3** | **Assistance** | **Accessible à v3** | **Mise à jour par v3** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  L’entité `AccessPolicies` n’existe pas dans dans v3. | Non | Non |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | Oui | Oui |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | Oui | Non |
| `AssetFile`<!-- row --> | <!-- empty --> |L’entité `AssetFiles` n’existe pas dans dans v3. Cependant, les fichiers (blobs de stockage) que vous chargez sont toujours considérés comme des fichiers.<br/><br/> Utilisez les API de Stockage Azure pour énumérer les blobs dans un conteneur à la place. Il existe deux façons d’appliquer une transformation aux fichiers à l’aide d’un travail :<br/><br/>Fichiers déjà chargés vers le stockage : L’URI inclut l’ID de ressource pour les travaux à effectuer sur des ressources dans un compte de stockage.<br/><br/>Fichiers à charger pendant le processus de transformation et de travail : La ressource est créée dans le stockage, une URL SAS est retournée, les fichiers sont chargés vers le stockage, puis la transformation est appliquée aux fichiers. | Non | Non |
| `Channel`<!-- row --> | `LiveEvent` | Les Événements en direct remplacent les Canaux de l’API v2. Ils reprennent la plupart des fonctionnalités et offrent de nouvelles fonctionnalités telles que les transcriptions en direct, le mode veille et la prise en charge de l’ingestion RTMPS. <br/><br/>Consultez [événement en direct dans un streaming en direct basé sur un scénario](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | Non | Non |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` n’est plus une entité, mais une propriété d’un localisateur de streaming.<br/><br/>  Dans v3, les données de clé de contenu sont associées avec le `StreamingLocator` (pour le chiffrement de sortie) ou avec la ressource elle-même (pour le chiffrement du stockage côté client). | Oui | Non |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | Oui | Non |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  Les `ContentKeyPolicyOptions` sont incluses dans la `ContentKeyPolicy`. | Oui | Non |
| `IngestManifest`<!-- row --> | <!-- empty --> | L’entité `IngestManifests` n’existe pas dans v3. Le chargement de fichiers dans v3 implique l’API de Stockage Azure. Les ressources sont créées, puis les fichiers sont chargés vers le conteneur de stockage associé. Vous pouvez également utiliser de nombreuses autres façons d’extraire des données dans un conteneur de Stockage Azure. `JobInputHttp` offre également un moyen de télécharger une entrée de travail à partir d’une URL donnée, le cas échéant. | Non | Non |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | Vous pouvez également utiliser de nombreuses autres façons d’extraire des données dans un conteneur de Stockage Azure. `JobInputHttp` offre également un moyen de télécharger une entrée de travail à partir d’une URL donnée, le cas échéant. | Non | Non |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | Vous pouvez également utiliser de nombreuses autres façons d’extraire des données dans un conteneur de Stockage Azure. `JobInputHttp` offre également un moyen de télécharger une entrée de travail à partir d’une URL donnée, le cas échéant. | Non | Non |
| `Job`<!-- row --> | `Job` | Créez une `Transform` avant de créer un `Job`. | Non | Non |
| `JobTemplate`<!-- row --> | `Transform` | Utilisez un `Transform` à la place. Une transformation est une entité distincte d’un travail et est réutilisable. | Non | Non |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | Oui | Non |
| `MediaProcessor`<!-- row --> | <!-- empty --> | Au lieu de rechercher le `MediaProcessor` à utiliser par nom, utilisez la présélection souhaitée lors de la définition d’une transformation. La présélection utilisée détermine le processeur multimédia qu’utilise le système de travaux. Consultez les rubriques sur l’encodage dans l’article relatif à l’[encodage basé sur un scénario](migrate-v-2-v-3-migration-scenario-based-encoding.md). <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | Non | NA (en lecture seule dans v2) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | Les notifications dans v3 sont gérées via Azure Event Grid. Le `NotificationEndpoint` est remplacé par l’inscription d’abonnement Event Grid qui encapsule également la configuration des types de notifications à recevoir (qui, dans v2, sont gérées par la `JobNotificationSubscription` du travail, la `TaskNotificationSubscription` de la tâche et le `ComponentMonitoringSetting`de télémétrie). La télémétrie v2 a été fractionnée entre Azure Event Grid et Azure Monitor pour s’adapter aux améliorations de l’écosystème Azure. | Non | Non |
| `Program`<!-- row --> | `LiveOutput` | Les sorties en direct remplacent désormais les programmes dans l’API v3.  | Non | Non |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | Les points de terminaison de streaming restent essentiellement les mêmes. Ils sont utilisés pour l’empaquetage dynamique, le chiffrement et la livraison de contenu TLS et DASH pour le streaming en direct et à la demande directement à partir de l’origine ou via le CDN. Les nouvelles fonctionnalités incluent la prise en charge d’une intégration et de graphiques d’Azure Monitor améliorés. |  Oui | Oui |
| `Task`<!-- row --> | `JobOutput` | Remplacé par `JobOutput` (qui n’est plus une entité distincte dans l’API).  Consultez les rubriques sur l’encodage dans l’article relatif à l’[encodage basé sur un scénario](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Non | Non |
| `TaskTemplate`<!-- row --> | `TransformOutput` | Remplacé par `TransformOutput` (qui n’est plus une entité distincte dans l’API). Consultez les rubriques sur l’encodage dans l’article relatif à l’[encodage basé sur un scénario](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Non | Non |
| `Inputs`<!-- row --> | `Inputs` | Les entrées et les sorties se situent désormais au niveau du travail. Consultez les rubriques sur l’encodage dans l’article relatif à l’[encodage basé sur un scénario](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Non | Non |
| `Outputs`<!-- row --> | `Outputs` | Les entrées et les sorties se situent désormais au niveau du travail.  Dans v3, le format des métadonnées a changé, passant de XML à JSON.  Les sorties en direct démarrent dès leur création et s’arrêtent à leur suppression. Consultez les rubriques sur l’encodage dans l’article relatif à l’[encodage basé sur un scénario](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Non | Non |


| **Autres modifications** | **V2**  | **V3** |
|---|---|---|
| **Stockage** <!--new row --> |||
| Stockage <!--new row --> | | Les kits de développement logiciel (SDK) v3 sont dissociés du Kit de développement logiciel (SDK) Stockage, ce qui offre davantage de contrôle sur la version du Kit de développement logiciel (SDK) Stockage à utiliser, et évite les problèmes de contrôle de version.                      |
| **Encodage** <!--new row --> |||
| Débits d’encodage <!--new row --> | Débits mesurés en Kbits/s. Par exemple : 128 (Kbits/s)| Bits par seconde. Par exemple : 128000 (bits/seconde)|
| Encodage de DRM FairPlay <!--new row --> | Dans Media Services v2, vous pouvez spécifier le vecteur d’initialisation. | Dans Media Services v3, vous ne pouvez pas spécifier le vecteur d’initialisation FairPlay.|
| Encodeur Premium <!--new row --> | Encodeur Premium et indexeur hérité| L’[Encodeur Premium](../previous/media-services-encode-asset.md) et les [processeurs Media Analytics](../previous/legacy-components.md) (indexeur Azure Media Services 2 en préversion, Face Redactor, etc.) ne sont pas accessibles via v3. Nous avons ajouté la prise en charge du mappage de canal audio à l’encodeur Standard.  Consultez [Audio dans la documentation relative au Swagger d’encodage de Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).  <br/> Consultez les rubriques sur l’encodage dans l’article relatif à l’[encodage basé sur un scénario](migrate-v-2-v-3-migration-scenario-based-encoding.md) |
| **Transformations et travaux** <!--new row -->|||
| HTTPS de traitement basé sur un travail <!--new row --> |<!-- empty -->| Pour un traitement de travail basé sur un fichier, vous pouvez utiliser une URL HTTPS en guise d’entrée. Vous n’avez pas besoin de contenu déjà stocké dans Azure ou de créer des ressources. |
| Modèles ARM pour les travaux <!--new row --> | Les modèles ARM n’existent pas dans v2. | Vous pouvez utiliser une transformation pour créer des configurations réutilisables, créer des modèles Azure Resource Manager, et isoler des paramètres de traitement entre plusieurs clients ou locataires. |
| **Événements en direct** <!--new row --> |||
| point de terminaison de diffusion en continu <!--new row --> | Un point de terminaison de streaming représente un service de diffusion en continu qui peut fournir du contenu directement à une application de lecteur cliente ou à un réseau de distribution de contenu (CDN) en vue de sa redistribution. | Les points de terminaison de streaming restent essentiellement les mêmes. Ils sont utilisés pour l’empaquetage dynamique, le chiffrement et la livraison de contenu TLS et DASH pour le streaming en direct et à la demande directement à partir de l’origine ou via le CDN.  Les nouvelles fonctionnalités incluent la prise en charge d’une intégration et de graphiques d’Azure Monitor améliorés. |
| Canaux d’événements en direct <!--new row --> | Les canaux sont responsables du traitement du contenu de streaming en direct. Un canal fournit un point de terminaison d’entrée (URL de réception) que vous fournissez ensuite à un transcodeur live. Le canal reçoit des flux d’entrée en direct provenant du transcodeur, et les met à disposition pour la diffusion en continu via un ou plusieurs points de terminaison de streaming. Les canaux fournissent également un point de terminaison d’aperçu (URL d’aperçu) que vous utilisez pour obtenir un aperçu et valider votre flux avant tout traitement et remise supplémentaires.| Les Événements en direct remplacent les Canaux de l’API v2. Ils reprennent la plupart des fonctionnalités et offrent de nouvelles fonctionnalités telles que les transcriptions en direct, le mode veille et la prise en charge de l’ingestion RTMPS. |
| Programmes d’événement en direct <!--new row --> | Un programme vous permet de contrôler la publication et le stockage des segments dans un flux temps réel. Les canaux gèrent des programmes. La relation entre canal et programme est similaire au contenu multimédia traditionnel où un canal a un flux de contenu constant et un programme est limité à un événement minuté sur ce canal. Vous pouvez spécifier le nombre d'heures pendant lesquelles vous voulez conserver le contenu enregistré pour le programme en définissant la propriété `ArchiveWindowLength`. Cette valeur peut être comprise entre 5 minutes et 25 heures.| Les sorties en direct remplacent désormais les programmes dans l’API v3. |
| Longueur d’événement en direct <!--new row --> |<!-- empty -->| Vous pouvez diffuser des événements en direct en permanence quand vous utilisez Media Services pour transcoder un flux de contribution à débit unique en un flux de sortie à plusieurs débits.|
| Latence d’événement en direct <!--new row --> |<!-- empty -->| Prise en charge du nouveau streaming en direct à faible latence sur des événements en direct. |
| Préversion d’Événement en direct <!--new row --> |<!-- empty -->| L’aperçu des événements en direct prend en charge l’empaquetage dynamique et le chiffrement dynamique. Ceci permet la protection du contenu sur l’aperçu, ainsi que l’empaquetage DASH et HLS. |
| RTMPS d’événement en direct <!--new row --> |<!-- empty-->| Prise en charge améliorée du protocole RTMPS (protocole de messagerie en temps réel sur une connexion SSL) offrant un stabilité accrue et meilleure prise en charge de l’encodeur source. |
| Ingestion sécurisée RTMPS d’événement en direct <!--new row --> | | Quand vous créez un événement en direct, vous obtenez 4 URL d’ingestion. Les 4 URL d’ingestion sont presque identiques et ont le même jeton de streaming `AppId`. Seule la partie du numéro de port est différente. Il existe deux URL principales et de secours pour RTMPS.| 
| Transcription d’événement en direct <!--new row --> |<!-- empty--> | Azure Media Services diffuse de la vidéo, de l’audio et du texte dans différents protocoles. Lorsque vous publiez votre stream en direct en MPEG-DASH ou HLS/CMAF, notre service diffuse le texte transcrit en IMSC1.1 compatible TTML avec la vidéo et le son.|
| Mode veille d’événement en direct <!--new row --> | Il n’y a aucun mode veille pour v2. | Le mode veille est une nouvelle fonctionnalité v3 qui permet de gérer les pools chauds d’événements en direct. Les clients peuvent désormais démarrer un événement en direct en mode veille à moindre coût avant de le faire passer à l’état En cours d’exécution. Cela améliore les temps de démarrage des canaux et réduit les coûts d’exploitation des pools chauds pour des démarrages plus rapides. |
| Facturation des événements en direct <!--new row --> | <!-- empty-->| La facturation des événements en direct est basée sur des compteurs de canal live. |
| Sorties en direct <!--new row --> | Les programmes devaient être démarrés après création. | Les sorties en direct démarrent dès leur création et s’arrêtent à leur suppression. |
