---
title: Fonctionnalités du stockage de blobs disponibles dans Azure Data Lake Storage Gen2 | Microsoft Docs
description: En savoir plus sur les fonctionnalités de stockage de blobs que vous pouvez utiliser avec Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/29/2021
ms.author: normesta
ms.openlocfilehash: 234f3ae60aaf389b347ce8378c889cdeb61e0738
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531709"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Fonctionnalités du stockage de blobs disponibles dans Azure Data Lake Storage Gen2

Les fonctionnalités de stockage Blob, telles que la [journalisation des diagnostics](../common/storage-analytics-logging.md), les [niveaux d’accès](storage-blob-storage-tiers.md) et les [stratégies de gestion du cycle de vie du stockage Blob](storage-lifecycle-management-concepts.md), fonctionnent désormais avec les comptes qui ont un espace de noms hiérarchique. Par conséquent, vous pouvez activer des espaces de noms hiérarchiques sur vos comptes Stockage Blob sans perdre l’accès à ces fonctionnalités.

Ce tableau répertorie les fonctionnalités de stockage de blobs que vous pouvez utiliser avec Azure Data Lake Storage Gen2. Les éléments figurant dans ces tableaux seront modifiés au fil du temps, car la prise en charge continue de s’étendre. Pour plus d’informations sur les problèmes spécifiques associés à la préversion d’une fonctionnalité, consultez l’article [Problèmes connus](data-lake-storage-known-issues.md).

## <a name="supported-blob-storage-features"></a>Fonctionnalités du stockage Blob prises en charge

Le tableau suivant présente la prise en charge de chaque fonctionnalité de stockage Blob avec Data Lake Storage Gen2. Il existe une colonne pour les niveaux de [performances Premium](premium-tier-for-data-lake-storage.md) et standard. 

|Fonctionnalité Stockage Blob |Standard |Premium |Articles connexes |
|---------------|-------------------|---|
|Niveau d’accès chaud|Mise à la disposition générale|Non pris en charge|[Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md)|
|Niveau d’accès froid|Mise à la disposition générale|Non pris en charge|[Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md)|
|Événements|Mise à la disposition générale|Mise à la disposition générale|[Réaction aux événements de stockage Blob](storage-blob-event-overview.md)|
|Métriques (classique)|Mise à la disposition générale|Mise à la disposition générale|[Métriques Azure Storage Analytics (classique)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Mesures dans Azure Monitor|Mise à la disposition générale|PRÉVERSION|[Métriques de Stockage Azure dans Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Commandes PowerShell de stockage de blobs|Mise à la disposition générale|Mise à la disposition générale|[Démarrage rapide : Charger, télécharger et lister des blobs avec PowerShell](storage-quickstart-blobs-powershell.md)|
|Commandes Azure CLI de stockage de blobs|Mise à la disposition générale|Mise à la disposition générale|[Démarrage rapide : Créer, télécharger et lister des blobs avec Azure CLI](storage-quickstart-blobs-cli.md)|
|API Stockage Blob|Mise à la disposition générale|Mise à la disposition générale|[Démarrage rapide : Bibliothèque de client Stockage Blob Azure v12 pour .NET](storage-quickstart-blobs-dotnet.md)<br>[Démarrage rapide : Gérer les blobs avec le Kit de développement logiciel (SDK) Java v12](storage-quickstart-blobs-java.md)<br>[Démarrage rapide : Gérer les blobs avec le Kit de développement logiciel (SDK) Python v12](storage-quickstart-blobs-python.md)<br>[Démarrage rapide : Gérer les blobs avec le Kit de développement logiciel (SDK) JavaScript v12 en Node.js](storage-quickstart-blobs-nodejs.md)|
|Clés gérées par le client|Mise à la disposition générale|Mise à la disposition générale|[Clés gérées par le client pour le chiffrement du Stockage Azure](../common/customer-managed-keys-overview.md?toc=/azure/storage/blobs/toc.json)|
|Journaux de diagnostic|Mise à la disposition générale|PRÉVERSION |[Journalisation Azure Storage Analytics](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Niveau d’accès archive|Mise à la disposition générale|Non pris en charge|[Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md)|
|Stratégies de gestion du cycle de vie (hiérarchisation)|Mise à la disposition générale|Pas encore pris en charge|[Gérer le cycle de vie du stockage Blob Azure](storage-lifecycle-management-concepts.md)|
|Stratégies de gestion du cycle de vie (supprimer l’objet blob)|Mise à la disposition générale|Mise à la disposition générale|[Gérer le cycle de vie du stockage Blob Azure](storage-lifecycle-management-concepts.md)|
|Suppression réversible de conteneur|Mise à la disposition générale|Mise à la disposition générale|[Suppression réversible des conteneurs](soft-delete-container-overview.md)|
|Connexion à Azure Monitor|PRÉVERSION |PRÉVERSION|[Surveillance du stockage Azure](./monitor-blob-storage.md)|
|Instantanés|PRÉVERSION|PRÉVERSION|[Instantanés d’objet blob](snapshots-overview.md)|
|Sites web statiques|Mise à la disposition générale<div role="complementary" aria-labelledby="preview-form"></div>|Mise à la disposition générale<div role="complementary" aria-labelledby="preview-form"></div>|[Hébergement de sites web statiques dans le service Stockage Azure](storage-blob-static-website.md)|
|Stockage non modifiable|PRÉVERSION<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|PRÉVERSION<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Stocker des données blob critiques pour l’entreprise avec un stockage immuable](immutable-storage-overview.md)|
|Inventaire du Stockage Azure|PRÉVERSION|PRÉVERSION|[Gestion des données d’objets blob avec l’inventaire du Stockage Azure (préversion)](blob-inventory.md)|
|Domaines personnalisés|PRÉVERSION<div role="complementary" aria-labelledby="preview-form-1"><sup>1</sup></div>|PRÉVERSION<div role="complementary" aria-labelledby="preview-form-1"><sup>1</sup></div>|[Mapper un domaine personnalisé à un point de terminaison Stockage Blob Azure](storage-custom-domain-name.md)|
|Suppression réversible de blob|PRÉVERSION|PRÉVERSION|[Suppression réversible pour les objets blob](./soft-delete-blob-overview.md)|
|Blobfuse|Mise à la disposition générale|Mise à la disposition générale|[Comment monter le stockage Blob en tant que système de fichiers avec blobfuse](storage-how-to-mount-container-linux.md)|
|Accès public anonyme |Mise à la disposition générale|Mise à la disposition générale| Consultez [Configurer l’accès en lecture public anonyme pour les conteneurs et les objets blob](anonymous-read-access-configure.md).|
|Basculement de compte géré par le client|Pas encore pris en charge|Pas encore pris en charge|[Récupération d’urgence et basculement de compte](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Clés fournies par le client|Pas encore pris en charge|Pas encore pris en charge|[Fournir une clé de chiffrement lors d’une requête au stockage d’objets blob](encryption-customer-provided-keys.md)|
|Étendues de chiffrement|Pas encore pris en charge|Pas encore pris en charge|[Créer et gérer des étendues de chiffrement](encryption-scope-manage.md)|
|Modifier le flux|Pas encore pris en charge|Pas encore pris en charge|[Prise en charge du flux de modification dans Stockage Blob Azure](storage-blob-change-feed.md)|
|Réplication d’objets|Pas encore pris en charge|Pas encore pris en charge|[Configurer la réplication d’objets pour des objets blob de blocs](object-replication-configure.md)|
|Gestion des versions des objets blob|Pas encore pris en charge|Pas encore pris en charge|[Activer et gérer le contrôle de version des objets blob](versioning-enable.md)|
|Restauration dans le temps|Pas encore pris en charge|Pas encore pris en charge|[Restauration dans le temps pour les objets blob de blocs](point-in-time-restore-overview.md)|
|Étiquettes d’index d’objet blob|Pas encore pris en charge|Pas encore pris en charge|[Gérer et rechercher des données blob Azure dans l’index d’objet blob](storage-manage-find-blobs.md)|

<div id="preview-form-2"><sup>1</sup>Un nom de domaine personnalisé peut être mappé uniquement au service blob ou au point de terminaison de site web statique. Le point de terminaison de stockage Data Lake n’est pas pris en charge.</a>.  </div>

## <a name="see-also"></a>Voir aussi

- [Problèmes connus avec Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Services Azure qui prennent en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plateformes open source prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Accès multiprotocole pour Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
