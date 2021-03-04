---
title: Avantages de la migration vers l'API Media Services V3
description: Cet article liste les avantages de la migration de Media Services v2 vers v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: fb3e09cefa7f87fe2eb1e5013854c7965ea9330e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698839"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>Étape 1 : Comprendre les avantages de la migration vers l’API Media Services v3

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-1.svg)

Nous vous encourageons à commencer dès maintenant à utiliser la version 2020-05-01 de l’API Azure Media Services v3 pour bénéficier des avantages suivants : nouvelles fonctionnalités, optimisations du fonctionnement et des performances uniquement disponibles dans la version 3 de l’API actuelle.

Vous pouvez changer la version de l’API dans le portail, les derniers kits SDK, la dernière interface CLI et l’API REST en indiquant la bonne chaîne de version.

Des améliorations significatives ont été apportées à Media Services avec la version 3.  

## <a name="benefits-of-media-services-v3"></a>Avantages de Media Services v3

| **Fonctionnalité de v3** | **Avantage** |
| --- | --- |
| **Azure portal** | |
| Mises à jour du portail Azure | Le portail Azure a été mis à jour pour inclure la gestion des entités de l’API v3. Les clients peuvent ainsi utiliser le portail pour démarrer un streaming en direct, envoyer des travaux de transformation v3, gérer des stratégies de protection de contenu, des points de terminaison de streaming, accéder à l’API, gérer des comptes de stockage liés et effectuer des tâches de supervision. |
| **Comptes et stockage** | |
| Contrôle d’accès en fonction du rôle Azure (RBAC) | Les clients peuvent désormais définir leurs propres rôles et contrôler l’accès à chaque entité dans l’API ARM Media Services. Il est ainsi possible de contrôler l’accès aux ressources par les comptes  AAD. |
| Identités managées | Les identités managées éliminent la nécessité pour les développeurs de gérer des informations d’identification en fournissant une identité pour la ressource Azure dans Azure AD. Pour plus d’informations sur les identités managées, consultez [ceci](../../active-directory/managed-identities-azure-resources/overview.md). |
| Prise en charge des liaisons privées | Les clients ont accès à des points de terminaison Media Services pour la remise de clés, LiveEvents et StreamingEndpoints par le biais d’un PrivateEndpoint sur leur réseau virtuel. |
| Prise en charge des [clés gérées par le client](concept-use-customer-managed-keys-byok.md) ou de BYOK (Bring Your Own Key) | Les clients peuvent chiffrer les données de leur compte Media Services à l’aide d’une clé dans leur coffre de clés Azure. |
| **Éléments multimédias** | |
| Un élément multimédia peut avoir plusieurs [localisateurs de streaming](streaming-locators-concept.md), chacun avec des paramètres différents d’[empaquetage dynamique](dynamic-packaging-overview.md) et de chiffrement dynamique. | Il existe une limite de 100 localisateurs de streaming autorisés sur chaque élément multimédia. Les clients peuvent stocker une seule copie du contenu multimédia dans l’élément, mais partager différentes URL de streaming avec différentes stratégies de streaming ou stratégies de protection de contenu en fonction d’un public ciblé.
| **Traitement des travaux** ||
| La version 3 introduit le concept des  [transformations](transforms-jobs-concept.md)  pour le traitement de travaux basé sur fichier. | Vous pouvez utiliser une transformation pour créer des configurations réutilisables, créer des modèles Azure Resource Manager, et isoler des paramètres de traitement entre plusieurs clients ou locataires. |
| Pour le traitement de travaux basé sur fichier, vous pouvez utiliser une URL HTTP(S) comme entrée. | Vous n’avez pas besoin de contenu déjà stocké dans Azure ni de créer des ressources d’entrée. |
| **Événements en direct** ||
| Événements en direct Premium 1080 p | La nouvelle référence SKU d’événement en direct permet aux clients d’obtenir un encodage cloud avec une sortie pouvant aller jusqu’à une résolution de 1 080 p. |
| Nouvelle prise en charge du streaming en direct [à faible latence](live-event-latency.md) sur des événements en direct. | Cette prise en charge permet aux utilisateurs de regarder des événements en direct en étant plus près du temps réel que si ce paramètre n’est pas activé. |
| L’aperçu des événements en direct prend en charge l’ [empaquetage dynamique](dynamic-packaging-overview.md)  et le chiffrement dynamique. | Ceci permet la protection du contenu sur l’aperçu, ainsi que l’empaquetage DASH et HLS. |
| Les sorties en direct remplacent les programmes | La sortie en direct est plus simple à utiliser que l’entité du programme dans les API v2. |
| Amélioration de l’ingestion RTMP pour les événements en direct, avec une prise en charge d’un plus grand nombre d’encodeurs | Augmente la stabilité et offre une flexibilité des encodeurs sources. |
| Les événements en direct peuvent être diffusés en streaming 24 h/24, 7 j/7 | Vous pouvez héberger un événement en direct et capter l’attention de votre public pendant plus longtemps. |
| Transcription en direct pendant les événements en direct | La transcription en direct permet aux clients de transcrire automatiquement la parole en texte en temps réel pendant la diffusion des événements en direct. L’accessibilité des événements en direct s’en retrouve considérablement améliorée. |
| [Mode veille](live-events-outputs-concept.md#standby-mode) sur les événements en direct | Les événements en direct en état de veille sont moins coûteux que les événements en direct en cours d’exécution. Les clients peuvent ainsi gérer un ensemble d’événements en direct prêts à démarrer en quelques secondes à un coût inférieur à celui d’un ensemble d’événements en direct en cours. La réduction du prix des événements en direct en état de veille prendra effet en février 2021 dans la plupart des régions. Les régions restantes suivront en avril 2021.
|**Protection du contenu** ||
| La [protection du contenu](content-key-policy-concept.md)  prend en charge les fonctionnalités à plusieurs clés. | Les clients peuvent maintenant utiliser plusieurs clés de chiffrement de contenu sur leurs localisateurs de streaming. |
| **Surveillance** | |
| Prise en charge des notifications [Azure EventGrid](reacting-to-media-services-events.md) | Les notifications EventGrid sont enrichies de fonctionnalités. Il existe plus de types de notifications, une prise en charge élargie du SDK pour la réception des notifications dans votre propre application et d’autres services Azure existants pouvant jouer le rôle de gestionnaires d’événements. |
| [Prise en charge et intégration d’Azure Monitor dans le portail Azure](monitor-events-portal-how-to.md) | Les clients peuvent visualiser l’utilisation du quota de leur compte Media Services, voir les statistiques en temps réel des points de terminaison de streaming, mais aussi ingérer et archiver des statistiques pour des événements en direct. Les clients peuvent maintenant définir des alertes et prendre les mesures nécessaires en fonction de données de métriques en temps réel. |

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]