---
title: Fonctionnalités du stockage de blobs disponibles dans Azure Data Lake Storage Gen2 | Microsoft Docs
description: En savoir plus sur les fonctionnalités de stockage de blobs que vous pouvez utiliser avec Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b270ce3cd15dbd1e8dd53bd60376a87d6e08f75c
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637211"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Fonctionnalités du stockage de blobs disponibles dans Azure Data Lake Storage Gen2

Les fonctionnalités de Stockage Blob Azure, telles que la [journalisation des diagnostics](../common/storage-analytics-logging.md), les [niveaux d’accès](storage-blob-storage-tiers.md) et les  [stratégies de gestion du cycle de vie de Stockage Blob Azure](storage-lifecycle-management-concepts.md), fonctionnent désormais avec les comptes qui ont un espace de noms hiérarchique. Par conséquent, vous pouvez activer des espaces de noms hiérarchiques sur vos comptes Stockage Blob sans perdre l’accès à ces fonctionnalités.

Ce tableau répertorie les fonctionnalités de stockage de blobs que vous pouvez utiliser avec Azure Data Lake Storage Gen2. Les éléments qui apparaissent dans ces tables seront modifiés au fil du temps, car la prise en charge continue à se développer.

## <a name="supported-blob-storage-features"></a>Fonctionnalités Stockage Blob prises en charge

> [!NOTE]
> Le niveau de prise en charge fait référence uniquement à la façon dont la fonctionnalité est prise en charge avec Data Lake Storage Gen2.

|Fonctionnalité Stockage Blob |Niveau de support |Articles connexes |
|---------------|-------------------|---|
|Niveau d’accès chaud|Mise à la disposition générale|[Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md)|
|Niveau d’accès froid|Mise à la disposition générale|[Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md)|
|Événements|Mise à la disposition générale|[Réaction aux événements de stockage Blob](storage-blob-event-overview.md)|
|Métriques (classique)|Mise à la disposition générale|[Métriques Azure Storage Analytics (classique)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Mesures dans Azure Monitor|Mise à la disposition générale|[Métriques de Stockage Azure dans Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Commandes PowerShell de stockage de blobs|Mise à la disposition générale|[Démarrage rapide : Charger, télécharger et lister des blobs avec PowerShell](storage-quickstart-blobs-powershell.md)|
|Commandes Azure CLI de stockage de blobs|Mise à la disposition générale|[Démarrage rapide : Créer, télécharger et lister des blobs avec Azure CLI](storage-quickstart-blobs-cli.md)|
|API Stockage Blob|Mise à la disposition générale|[Démarrage rapide : Bibliothèque de client Stockage Blob Azure v12 pour .NET](storage-quickstart-blobs-dotnet.md)<br>[Démarrage rapide : Gérer les blobs avec le Kit de développement logiciel (SDK) Java v12](storage-quickstart-blobs-java.md)<br>[Démarrage rapide : Gérer les blobs avec le Kit de développement logiciel (SDK) Python v12](storage-quickstart-blobs-python.md)<br>[Démarrage rapide : Gérer les blobs avec le Kit de développement logiciel (SDK) JavaScript v12 en Node.js](storage-quickstart-blobs-nodejs.md)|
|Niveau d’accès archive|PRÉVERSION|[Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md)|
|Stratégies de gestion du cycle de vie|PRÉVERSION|[Gérer le cycle de vie du stockage Blob Azure](storage-lifecycle-management-concepts.md)|
|Journaux de diagnostic|Mise à la disposition générale|[Journalisation Azure Storage Analytics](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Modifier le flux|Pas encore pris en charge|[Prise en charge du flux de modification dans Stockage Blob Azure](storage-blob-change-feed.md)|
|Basculement de compte|Pas encore pris en charge|[Récupération d’urgence et basculement de compte](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|ACL de conteneurs de blobs|Pas encore pris en charge|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domaines personnalisés|Pas encore pris en charge|[Mapper un domaine personnalisé à un point de terminaison Stockage Blob Azure](storage-custom-domain-name.md)|
|Stockage non modifiable|Pas encore pris en charge|[Stocker des données blob critiques pour l’entreprise avec un stockage immuable](storage-blob-immutable-storage.md)|
|Instantanés|Pas encore pris en charge|[Créer et gérer un instantané blob dans .NET](storage-blob-snapshots.md)|
|Suppression réversible|Pas encore pris en charge|[Suppression réversible pour les objets blob de Stockage Azure](storage-blob-soft-delete.md)|
|Sites web statiques|Pas encore pris en charge|[Hébergement de sites web statiques dans le service Stockage Azure](storage-blob-static-website.md)|
|Connexion à Azure Monitor|Pas encore pris en charge|Pas encore disponible|
|Objets blob de blocs Premium|Pas encore pris en charge|[Créer un compte BlockBlobStorage](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Voir aussi

- [Problèmes connus avec Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Services Azure qui prennent en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plateformes open source prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Accès multiprotocole pour Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)