---
title: Problèmes connus avec Azure Data Lake Storage Gen2
description: Apprenez-en davantage sur les limitations et les problèmes connus d’Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 6a612621ef316e7d4e7c248968b3cc87c04b85ae
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046257"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problèmes connus avec Azure Data Lake Storage Gen2

Cet article décrit les limitations et les problèmes connus liés aux comptes pour lesquels la fonctionnalité d’espace de noms hiérarchique est activée.

> [!NOTE]
> Certaines des fonctionnalités décrites dans cet article peuvent ne pas être prises en charge dans les comptes pour lesquels la prise en charge du Système de fichiers réseau (NFS) 3.0 est activée. Pour afficher un tableau qui indique l’impact de la prise en charge des fonctionnalités lorsque différentes fonctionnalités sont activées, consultez [prise en charge des fonctionnalités de stockage d’objets Blob dans les comptes de stockage Azure](storage-feature-support-in-storage-accounts.md).

## <a name="supported-blob-storage-features"></a>Fonctionnalités du stockage Blob prises en charge

Les comptes ayant un espace de noms hiérarchique prennent en charge un nombre croissant de fonctionnalités de stockage Blob. Pour en obtenir la liste complète, consultez [Fonctionnalités de stockage blob disponibles dans Azure Data Lake Storage Gen2](./storage-feature-support-in-storage-accounts.md).

## <a name="supported-azure-service-integrations"></a>Intégrations de service Azure prises en charge

Azure Data Lake Storage Gen2 prend en charge plusieurs services Azure permettant d’ingérer des données, d’obtenir des données d’analytique et de créer des représentations visuelles. Pour obtenir la liste des services Azure pris en charge, consultez [Services Azure prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Pour plus d’informations, consultez les [Services Azure prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plateformes open source prises en charge

Plusieurs plateformes open source prennent en charge le stockage Data Lake Gen2. Pour obtenir une liste complète, consultez [Plateformes open source prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Pour plus d’informations, consultez [plateformes Open source prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>API Stockage Blob

Les API Data Lake Storage Gen2, NFS 3.0 et les API d’objets Blob peuvent utiliser les mêmes données.

Cette section décrit les problèmes et les limitations liés à l’utilisation des API d’objets Blob, de NFS 3.0 et des API Data Lake Storage Gen 2 pour fonctionner sur les mêmes données.

- Il n’est pas possible d’utiliser l’API d’objets Blob, NFS 3.0 et les API Data Lake Storage pour écrire dans la même instance d’un fichier. Si vous écrivez dans un fichier à l’aide de Data Lake Storage Gen2, des API ou NFS 3,0, les blocs de ce fichier ne seront pas visibles pour les appels vers l’API d’objets blob [Obtenir la liste rouge](/rest/api/storageservices/get-block-list). La seule exception concerne les cas de remplacement. Vous pouvez remplacer un fichier/objet blob à l’aide de l’API ou de NFS 3.0 en utilisant l’option de troncation zéro.

- Lorsque vous utilisez l’opération [Lister les objets BLOB](/rest/api/storageservices/list-blobs) sans spécifier de délimiteur, les résultats incluront à la fois des répertoires et des objets BLOB. Si vous choisissez d’utiliser un délimiteur, n’utilisez qu’une barre oblique (`/`). Il s’agit du seul délimiteur pris en charge.

- Si vous utilisez l’API [Supprimer un objet BLOB](/rest/api/storageservices/delete-blob) pour supprimer un répertoire, ce répertoire est supprimé uniquement s’il est vide. Cela signifie que vous ne pouvez pas utiliser les répertoires de suppression de l’API d’objet BLOB de manière récursive.

Ces API REST BLOB ne sont pas prises en charge :

- [Placer BLOB (Page)](/rest/api/storageservices/put-blob)
- [Put Page](/rest/api/storageservices/put-page)
- [Obtenir les portées de page](/rest/api/storageservices/get-page-ranges)
- [Copie incrémentielle BLOB](/rest/api/storageservices/incremental-copy-blob)
- [Placer la page à partir de l’URL](/rest/api/storageservices/put-page-from-url)

Les disques de machine virtuelle non gérés ne sont pas pris en charge dans les comptes qui ont un espace de noms hiérarchique. Si vous souhaitez activer un espace de noms hiérarchique sur un compte de stockage, placez les disques de machine virtuelle non gérés dans un compte de stockage pour lequel la fonctionnalité espace de noms hiérarchique n’est pas activée.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Prise en charge de la définition de listes de contrôle d’accès (ACL) de manière récursive

La possibilité d’appliquer les modifications aux listes ACL de manière récursive du répertoire parent vers les éléments enfants est mise à la disposition générale. Dans la version actuelle de cette fonctionnalité, vous pouvez appliquer des modifications à la liste de contrôle d'accès à l’aide de l’Explorateur Stockage Azure, PowerShell, Azure CLI, et du Kit de développement logiciel (SDK) .NET, Java et Python. Le support n’est pas encore disponible pour le Portail Azure.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Listes de contrôle d’accès (ACL) et accès en lecture anonyme

Si l’[accès en lecture anonyme](./anonymous-read-access-configure.md) a été accordé à un conteneur, les listes de contrôle d’accès n’ont aucun effet sur ce conteneur ou les fichiers de ce conteneur.  Cela affecte uniquement les demandes de lecture.  Les demandes d’écriture continuent de respecter les listes de contrôle d’accès.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Utilisez uniquement la dernière version d’AzCopy ([AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Les versions antérieures d’AzCopy, telles qu’AzCopy v8.1, ne sont pas prises en charge.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Explorateur de stockage Azure

Utilisez uniquement les versions `1.6.0` ou ultérieures.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Explorateur Stockage dans le portail Azure

Les ACL ne sont pas encore prises en charge.

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>Applications tierces

Les applications tierces qui utilisent l’API REST continueront à fonctionner si vous les utilisez avec Data Lake Storage Gen2. Les applications qui appellent des API Blob ont de grandes chances de fonctionner.

## <a name="storage-analytics-logs-classic"></a>Journaux Storage Analytics (classique)

Le réglage du nombre de jours de rétention n’est pas encore pris en charge, mais vous pouvez supprimer les journaux manuellement à l’aide de n’importe quel outil pris en charge, comme Explorateur Stockage Azure, REST ou un Kit de développement logiciel (SDK).

## <a name="windows-azure-storage-blob-wasb-driver"></a>Pilote Windows Azure Storage Blob (WASB)

Actuellement, le pilote WASB, qui a été conçu pour fonctionner avec l’API Blob uniquement, rencontre des problèmes dans quelques scénarios courants. C’est le cas en particulier quand il s’agit d’un client pour un compte de stockage prenant en charge un espace de noms hiérarchique. L’accès multiprotocole sur Data Lake Storage n’atténue pas ces problèmes.

L’utilisation du pilote WASB comme client pour un compte de stockage prenant en charge un espace de noms hiérarchique n’est pas prise en charge. Nous vous recommandons plutôt d’utiliser le pilote [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) dans votre environnement Hadoop. Si vous tentez d’effectuer une migration à partir d’un environnement Hadoop local avec une version antérieure à Hadoop Branch-3, ouvrez un ticket de support Azure pour que nous puissions vous contacter et vous indiquer la bonne direction pour vous et votre organisation.

## <a name="soft-delete-for-blobs-capability-currently-in-preview"></a>Suppression conditionnelle pour la fonctionnalité des blobs (actuellement en préversion)

Si les répertoires parents des fichiers ou répertoires supprimés de manière conditionnelle sont renommés, les éléments supprimés de manière conditionnelle peuvent ne pas s’afficher correctement dans le portail Azure. Dans ce cas, vous pouvez utiliser [PowerShell](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-powershell) ou [Azure CLI](soft-delete-blob-manage.md?tabs=dotnet#restore-soft-deleted-blobs-and-directories-by-using-azure-cli) pour restaurer les éléments supprimés de manière conditionnelle.

## <a name="events"></a>Événements

Si votre compte dispose d’un abonnement aux événements, les opérations de lecture sur le point de terminaison secondaire provoqueront une erreur. Pour résoudre ce problème, supprimez les abonnements aux événements.

> [!TIP]
> L’accès en lecture au point de terminaison secondaire est disponible uniquement lorsque vous activez le stockage géo-redondant avec accès en lecture (RA-GRS) ou le stockage géo-redondant avec accès en lecture (RA-GZRS).
