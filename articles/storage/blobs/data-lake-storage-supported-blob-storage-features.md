---
title: Fonctionnalités du stockage de blobs disponibles dans Azure Data Lake Storage Gen2 | Microsoft Docs
description: En savoir plus sur les fonctionnalités de stockage de blobs que vous pouvez utiliser avec Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a0cba1c3a7a3b713fa5f0603ad9e116068d8df66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618469"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Fonctionnalités du stockage de blobs disponibles dans Azure Data Lake Storage Gen2

Les fonctionnalités de Stockage Blob Azure, telles que la [journalisation des diagnostics](../common/storage-analytics-logging.md), les [niveaux d’accès](storage-blob-storage-tiers.md) et les  [stratégies de gestion du cycle de vie de Stockage Blob Azure](storage-lifecycle-management-concepts.md), fonctionnent désormais avec les comptes qui ont un espace de noms hiérarchique. Par conséquent, vous pouvez activer des espaces de noms hiérarchiques sur vos comptes Stockage Blob sans perdre l’accès à ces fonctionnalités.

Ce tableau répertorie les fonctionnalités de stockage de blobs que vous pouvez utiliser avec Azure Data Lake Storage Gen2. Les éléments figurant dans ces tableaux seront modifiés au fil du temps, car la prise en charge continue de s’étendre. Pour plus d’informations sur les problèmes spécifiques associés à la préversion d’une fonctionnalité, consultez l’article [Problèmes connus](data-lake-storage-known-issues.md).

## <a name="supported-blob-storage-features"></a>Fonctionnalités du stockage Blob prises en charge

> [!NOTE]
> Le niveau de prise en charge fait référence uniquement à la façon dont la fonctionnalité est prise en charge avec Data Lake Storage Gen2. 
>
> [Les comptes BlockBlobStorage de performances Premium](storage-blob-create-account-block-blob.md) pour Data Lake Storage Gen2 sont actuellement disponibles en préversion publique. Les niveaux de prise en charge pour ces types de comptes apparaissent dans la colonne **BlockBlobStorage (Premium)** .

|Fonctionnalité Stockage Blob |Universel v2 |BlockBlobStorage (Premium) |Articles connexes |
|---------------|-------------------|---|
|Niveau d’accès chaud|Mise à la disposition générale|Non pris en charge|[Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md)|
|Niveau d’accès froid|Mise à la disposition générale|Non pris en charge|[Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md)|
|Événements|Mise à la disposition générale|PRÉVERSION|[Réaction aux événements de stockage Blob](storage-blob-event-overview.md)|
|Métriques (classique)|Mise à la disposition générale|Non pris en charge|[Métriques Azure Storage Analytics (classique)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Mesures dans Azure Monitor|Mise à la disposition générale|PRÉVERSION|[Métriques de Stockage Azure dans Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Commandes PowerShell de stockage de blobs|Mise à la disposition générale|PRÉVERSION|[Démarrage rapide : Charger, télécharger et lister des blobs avec PowerShell](storage-quickstart-blobs-powershell.md)|
|Commandes Azure CLI de stockage de blobs|Mise à la disposition générale|PRÉVERSION|[Démarrage rapide : Créer, télécharger et lister des blobs avec Azure CLI](storage-quickstart-blobs-cli.md)|
|API Stockage Blob|Mise à la disposition générale|PRÉVERSION|[Démarrage rapide : Bibliothèque de client Stockage Blob Azure v12 pour .NET](storage-quickstart-blobs-dotnet.md)<br>[Démarrage rapide : Gérer les blobs avec le Kit de développement logiciel (SDK) Java v12](storage-quickstart-blobs-java.md)<br>[Démarrage rapide : Gérer les blobs avec le Kit de développement logiciel (SDK) Python v12](storage-quickstart-blobs-python.md)<br>[Démarrage rapide : Gérer les blobs avec le Kit de développement logiciel (SDK) JavaScript v12 en Node.js](storage-quickstart-blobs-nodejs.md)|
|Journaux de diagnostic|Mise à la disposition générale|PRÉVERSION <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Journalisation Azure Storage Analytics](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Niveau d’accès archive|Mise à la disposition générale|Non pris en charge|[Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md)|
|Stratégies de gestion du cycle de vie|Mise à la disposition générale|Pas encore pris en charge|[Gérer le cycle de vie du stockage Blob Azure](storage-lifecycle-management-concepts.md)|
|Connexion à Azure Monitor|PRÉVERSION |Pas encore pris en charge|[Surveillance du stockage Azure](../common/monitor-storage.md)|
|Instantanés|PRÉVERSION|Pas encore pris en charge|[Instantanés d’objet blob](snapshots-overview.md)|
|Sites web statiques|PRÉVERSION|Pas encore pris en charge|[Hébergement de sites web statiques dans le service Stockage Azure](storage-blob-static-website.md)|
|Stockage non modifiable|PRÉVERSION|Pas encore pris en charge|[Stocker des données blob critiques pour l’entreprise avec un stockage immuable](storage-blob-immutable-storage.md)|
|Suppression réversible de conteneur|PRÉVERSION|PRÉVERSION|[Suppression réversible pour les conteneurs (préversion)](soft-delete-container-overview.md)|
|Suppression réversible de blob|Pas encore pris en charge|Pas encore pris en charge|[Suppression réversible pour les objets blob](storage-blob-soft-delete.md)|
|Blobfuse|PRÉVERSION|Pas encore pris en charge|[Comment monter le stockage Blob en tant que système de fichiers avec blobfuse](storage-how-to-mount-container-linux.md)|
|Basculement de compte|Pas encore pris en charge|Pas encore pris en charge|[Récupération d’urgence et basculement de compte](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|ACL de conteneurs de blobs|Pas encore pris en charge<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|Pas encore pris en charge<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Clés fournies par le client|Pas encore pris en charge|Pas encore pris en charge|[Fournir une clé de chiffrement lors d’une requête au stockage d’objets blob](encryption-customer-provided-keys.md)|
|Domaines personnalisés|Pas encore pris en charge|Pas encore pris en charge|[Mapper un domaine personnalisé à un point de terminaison Stockage Blob Azure](storage-custom-domain-name.md)|
|Étendues de chiffrement|Pas encore pris en charge|Pas encore pris en charge|[Créer et gérer des étendues de chiffrement (version préliminaire)](encryption-scope-manage.md)|
|Modifier le flux|Pas encore pris en charge|Pas encore pris en charge|[Prise en charge du flux de modification dans Stockage Blob Azure](storage-blob-change-feed.md)|
|Réplication d’objets|Pas encore pris en charge|Pas encore pris en charge|[Configurer la réplication d’objets pour des objets blob de blocs (préversion)](object-replication-configure.md)|
|Gestion des versions des objets blob|Pas encore pris en charge|Pas encore pris en charge|[Activer et gérer le contrôle de version des objets blob (préversion)](versioning-enable.md)|

<div id="diagnostic-logging"><sup>1</sup> Pour les comptes de stockage d’objets blob de blocs Premium, les journaux de diagnostic (classiques) ne peuvent pas être activés à l’aide du portail Azure. Activez-les à l’aide de PowerShell.</div><br>

<div id="blob-container-ACL"><sup>2</sup> Vous pouvez définir des listes de contrôle d’accès (ACL, access-control list) sur le dossier racine du conteneur, mais pas sur le conteneur lui-même.</div><br>

<div id="preview-form"><sup>3</sup> Pour utiliser des instantanés, un stockage immuable ou des sites web statiques avec Data Lake Storage Gen2, vous devez vous inscrire à la préversion en remplissant ce <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>formulaire</a>.  </div>

## <a name="see-also"></a>Voir aussi

- [Problèmes connus avec Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Services Azure qui prennent en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plateformes open source prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Accès multiprotocole pour Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
