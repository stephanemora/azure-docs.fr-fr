---
title: Considérations relatives à la sécurité pour les conditions d'attribution de rôle Azure dans le service Stockage Azure (préversion)
titleSuffix: Azure Storage
description: Considérations relatives à la sécurité pour les conditions d'attribution de rôle Azure et le contrôle d'accès en fonction des attributs Azure (Azure ABAC).
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 895f53ca3e8e1c68fa01ef44ffc47d88604bbea5
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070855"
---
# <a name="security-considerations-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Considérations relatives à la sécurité pour les conditions d'attribution de rôle Azure dans le service Stockage Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d'attribution de rôle Azure sont en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pour que les ressources bénéficient d’une sécurité optimale via le [contrôle d’accès en fonction des attributs Azure (Azure ABAC)](storage-auth-abac.md), vous devez également protéger les [attributs](storage-auth-abac-attributes.md) utilisés dans les [conditions d’attribution de rôle Azure](../../role-based-access-control/conditions-format.md). Par exemple, si votre condition est basée sur un chemin de fichier, vous devez vous méfier du fait que l’accès peut être compromis si le principal dispose d’une autorisation illimitée pour renommer un chemin de fichier.

Cet article décrit les considérations de sécurité à prendre en compte dans vos conditions d'attribution de rôle.

## <a name="use-of-other-authorization-mechanisms"></a>Utilisation d'autres mécanismes d'autorisation 

Les conditions d’attribution de rôle sont évaluées uniquement lors de l’utilisation d’Azure RBAC pour l’autorisation. Ces conditions peuvent être ignorées si vous autorisez l’accès à l’aide de méthodes d’autorisation alternatives :
- Autorisation de [clé partagée](/rest/api/storageservices/authorize-with-shared-key)
- [Signature d’accès partagé](/rest/api/storageservices/create-account-sas) (SAP) du compte
- [SAP de service](/rest/api/storageservices/create-service-sas).

De même, les conditions ne sont pas évaluées lorsque l’accès est accordé à l’aide de [listes de contrôle d’accès (ACL)](../blobs/data-lake-storage-access-control.md) dans les comptes de stockage avec un [espace de noms hiérarchique](../blobs/data-lake-storage-namespace.md) (HNS).

Vous pouvez empêcher les autorisations de clés partagées, SAP au niveau du compte et SAS au niveau du service en [désactivant l’autorisation de clé partagée](shared-key-authorization-prevent.md) pour votre compte de stockage. Étant donné que la signature d’utilisateur SAS dépend d’Azure RBAC, les conditions d’affectation de rôles sont évaluées lors de l’utilisation de cette méthode d’autorisation.

> [!NOTE]
> Les conditions d’affectation de rôle ne sont pas évaluées lorsque l’accès est accordé à l’aide des ACL avec Data Lake Storage Gen2. Dans ce cas, vous devez planifier l’étendue de l’accès afin qu’elle ne chevauche pas ce qui est accordé via les ACL.

## <a name="securing-storage-attributes-used-in-conditions"></a>Sécurisation des attributs de stockage utilisés dans les conditions

### <a name="blob-path"></a>Chemin d’accès d’objet blob

Si vous utilisez le chemin d'un objet blob comme attribut *@Resource* d'une condition, vous devez également empêcher les utilisateurs de renommer l'objet blob pour accéder à un fichier lorsqu'ils utilisent des comptes dotés d'un espace de noms hiérarchique. Par exemple, si vous souhaitez créer une condition basée sur le chemin d'un objet blob, vous devez également restreindre l'accès de l'utilisateur aux actions suivantes :

| Action | Description |
| :--- | :--- |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | Cette action permet aux clients de renommer un fichier à l’aide de l’API Path Create. |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | Cette action permet d’accéder à différentes opérations en lien avec le système de fichiers et le chemin d’accès. |

### <a name="blob-index-tags"></a>Étiquettes d’index d’objet blob

Les [balises d'index de blob](../blobs/storage-manage-find-blobs.md) sont utilisées comme attributs de forme libre pour les conditions de stockage. Si vous créez des conditions d'accès à l'aide de ces balises, vous devez également protéger les balises proprement dites. Plus précisément, l'action `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` DataAction permet aux utilisateurs de modifier les balises sur un objet de stockage. Vous pouvez limiter cette action pour empêcher les utilisateurs de manipuler une clé ou une valeur de balise pour accéder à des objets non autorisés.

En outre, lorsque des balises d’index de blob sont utilisées dans des conditions, les données peuvent se retrouver en situation de vulnérabilité si ces données et les balises d’index associées sont mises à jour dans le cadre d’opérations distinctes. Vous pouvez utiliser des conditions `@Request` sur les opérations d’écriture d’objet blob pour exiger la définition de balises d’index dans la même opération de mise à jour. Cette approche peut contribuer à sécuriser les données à partir du moment où elles sont écrites dans le stockage.

#### <a name="tags-on-copied-blobs"></a>Balises des objets blob copiés

Par défaut, les balises d’index de blob ne sont pas copiées de l’objet blob source vers la destination lorsque l’API [Copy blob](/rest/api/storageservices/Copy-Blob) ou l’une de ses variantes est utilisée. Pour préserver l’étendue de l’accès au blob lors de la copie, vous devez également copier les étiquettes.

#### <a name="tags-on-snapshots"></a>Balises des instantanés

Les balises des instantanés d’objets blob ne peuvent pas être modifiées. Vous devez mettre à jour les balises de l'objet blob avant de prendre l'instantané. Si vous modifiez les balises d’un objet blob de base, les balises de sa capture instantanée continuent d’avoir leur valeur précédente.

Si une étiquette sur un blob de base est modifiée après la prise d’un instantané, l’étendue de l’accès peut être différente pour le blob de base et l’instantané.

#### <a name="tags-on-blob-versions"></a>Balises des versions des objets blob

Les balises d'index de blob ne sont pas copiées lorsqu'une version d'un objet blob est créée via les API [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) ou [Copy Blob](/rest/api/storageservices/Copy-Blob). Vous pouvez spécifier des balises via l'en-tête de ces API.

Les balises peuvent être définies individuellement sur un objet blob de base actuel et sur chaque version de l’objet blob. Lorsque vous modifiez des balises sur un objet blob de base, les balises des versions précédentes ne sont pas mises à jour. Si vous souhaitez modifier l’étendue de l’accès d’un objet blob et de toutes ses versions à l’aide de balises, vous devez mettre à jour les balises de l’objet blob sur toutes ses versions.

#### <a name="querying-and-filtering-limitations-for-versions-and-snapshots"></a>Limitations de l'interrogation et du filtrage pour les versions et les instantanés

Lorsque vous utilisez des balises pour interroger et filtrer les objets blob dans un conteneur, seuls les objets blob de base sont inclus dans la réponse. Les versions des objets blob ou les instantanés disposant des clés et des valeurs demandées ne sont pas inclus.

## <a name="roles-and-permissions"></a>Rôles et autorisations

Si vous utilisez des conditions d'attribution de rôle pour les [rôles intégrés Azure](../../role-based-access-control/built-in-roles.md), vous devez examiner attentivement toutes les autorisations que le rôle accorde à un principal.

### <a name="inherited-role-assignments"></a>Attributions de rôle héritées

Les attributions de rôle peuvent être configurées pour un groupe d'administration, un abonnement, un groupe de ressources, un compte de stockage ou un conteneur, et celles-ci sont héritées à chaque niveau dans l'ordre indiqué. Le contrôle Azure RBAC repose sur un modèle additif, de sorte que les autorisations effectives correspondent à la somme des attributions de rôle de chaque niveau. Si un principal se voit attribuer la même autorisation par le biais de plusieurs rôles, l’accès d’une opération utilisant cette autorisation est évalué séparément pour chaque attribution à chaque niveau.

Comme les conditions sont implémentées en tant que conditions sur les attributions de rôle, toute attribution de rôle inconditionnelle peut permettre aux utilisateurs de contourner la condition. Supposons que vous attribuez le rôle *Contributeur de données de stockage blob* à un utilisateur pour un compte de stockage et un abonnement, mais que vous ajoutez une condition uniquement à l’attribution pour le compte de stockage. Dans ce cas, l’utilisateur aura un accès illimité au compte de stockage par le biais de l’attribution de rôle au niveau de l’abonnement.

C’est pourquoi vous devez appliquer les conditions de manière cohérente pour toutes les attributions de rôles dans une hiérarchie de ressources.

## <a name="other-considerations"></a>Autres éléments à prendre en compte

### <a name="condition-operations-that-write-blobs"></a>Opérations de condition qui écrivent des objets blob

De nombreuses opérations qui écrivent des objets blob requièrent l’autorisation `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` ou `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`. Des rôles intégrés, tels que [Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) et [Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) accordent ces deux autorisations à un principal de sécurité.

Lorsque vous définissez une condition d'attribution de rôle sur ces rôles, vous devez utiliser des conditions identiques sur ces deux autorisations afin de garantir des restrictions d'accès cohérentes pour les opérations d'écriture.

### <a name="behavior-for-copy-blob-and-copy-blob-from-url"></a>Comportement pour Copy Blob et Copy Blob from URL

Pour les opérations [Copy Blob](/rest/api/storageservices/Copy-Blob) et [Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url), les conditions `@Request` utilisant un chemin d'objet blob comme attribut sur l'action `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` et ses sous-opérations sont uniquement évaluées pour l'objet blob de destination.

Pour les conditions de l'objet blob source, les conditions `@Resource` de l'action `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` sont évaluées.

### <a name="behavior-for-get-page-ranges"></a>Comportement pour Get Page Ranges

Pour l'opération [Get Page Ranges](/rest/api/storageservices/get-page-ranges), les conditions `@Resource` utilisant `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags` comme attribut sur l'action `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` et ses sous-opérations sont uniquement évaluées pour l'objet blob de destination.

Les conditions ne s'appliquent pas à l'accès à l'objet blob spécifié par le paramètre d'URI `prevsnapshot` dans l'API.

## <a name="see-also"></a>Voir aussi

- [Autoriser l'accès aux objets blob à l'aide des conditions d'attribution de rôle Azure (préversion)](storage-auth-abac.md)
- [Actions et attributs pour les conditions d'attribution de rôle Azure dans le service Stockage Azure (préversion)](storage-auth-abac-attributes.md)
- [Qu'est-ce que le contrôle d'accès en fonction des attributs Azure (Azure ABAC) ? (préversion)](../../role-based-access-control/conditions-overview.md)

