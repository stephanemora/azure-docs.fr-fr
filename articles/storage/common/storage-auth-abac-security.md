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
ms.openlocfilehash: 83b3f4f9b84c25e3fe2822cbabb63adec1fb22ab
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489676"
---
# <a name="security-considerations-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Considérations relatives à la sécurité pour les conditions d'attribution de rôle Azure dans le service Stockage Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d'attribution de rôle Azure sont en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pour que les ressources bénéficient d'une sécurité optimale via le [contrôle d'accès en fonction des attributs Azure (Azure ABAC)](storage-auth-abac.md), vous devez également protéger les [attributs](storage-auth-abac-attributes.md) utilisés dans les [conditions d'attribution de rôle Azure](../../role-based-access-control/conditions-format.md). Pour cela, vous devez sécuriser toutes les autorisations ou actions qui peuvent servir à modifier les attributs utilisés dans les conditions d'attribution de rôle. Par exemple, lorsque vous créez une condition basée sur un chemin pour un compte de stockage, vous devez garder à l'esprit que l'accès risque d'être compromis si le principal est autorisé sans réserve à renommer un chemin de fichier.

Cet article décrit les considérations de sécurité à prendre en compte dans vos conditions d'attribution de rôle.

## <a name="use-of-other-authorization-mechanisms"></a>Utilisation d'autres mécanismes d'autorisation 

Le contrôle Azure ABAC est implémenté sous forme de conditions sur les attributions de rôle. Dans la mesure où ces conditions sont uniquement évaluées lors de l'utilisation du [contrôle d'accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md), elles peuvent être contournées si vous activez l'accès à l'aide d'autres méthodes d'autorisation. Par exemple, les conditions ne sont pas évaluées lors de l'utilisation de la clé partagée ou de l'autorisation de signature d'accès partagé. De même, les conditions ne sont pas évaluées lorsque l'accès est accordé à un fichier ou dossier à l'aide de [listes de contrôle d'accès (ACL)](../blobs/data-lake-storage-access-control.md) sur les comptes où la fonctionnalité d'espace de noms hiérarchique est activée. 

Pour contourner ce problème, vous pouvez [désactiver l'autorisation de clé partagée](shared-key-authorization-prevent.md) sur votre compte de stockage.

## <a name="securing-storage-attributes-used-in-conditions"></a>Sécurisation des attributs de stockage utilisés dans les conditions

### <a name="blob-path"></a>Chemin d’accès d’objet blob

Si vous utilisez le chemin d'un objet blob comme attribut *@Resource* d'une condition, vous devez également empêcher les utilisateurs de renommer l'objet blob pour accéder à un fichier lorsqu'ils utilisent des comptes dotés d'un espace de noms hiérarchique. Par exemple, si vous souhaitez créer une condition basée sur le chemin d'un objet blob, vous devez également restreindre l'accès de l'utilisateur aux actions suivantes :

| Action | Description |
| :--- | :--- |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | Permet aux clients de renommer un fichier à l'aide de l'API Path Create. |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | Permet d'accéder à différentes opérations en lien avec le système de fichiers et le chemin d'accès. |

### <a name="blob-index-tags"></a>Étiquettes d’index d’objet blob

Les [balises d'index de blob](../blobs/storage-manage-find-blobs.md) sont utilisées comme attributs de forme libre pour les conditions de stockage. Si vous créez des conditions d'accès à l'aide de ces balises, vous devez également protéger les balises proprement dites. Plus précisément, l'action `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` DataAction permet aux utilisateurs de modifier les balises sur un objet de stockage. L'accès d'un principal de sécurité à cette action doit également être suffisamment restreint afin d'éviter qu'il modifie une clé ou une valeur de balise pour accéder à un objet stocké auquel il n'aurait pas accès autrement.

En outre, lorsque des balises d'index de blob sont utilisées dans des conditions, les données peuvent régulièrement se retrouver en situation de vulnérabilité si ces données et les balises d'index associées sont mises à jour dans le cadre d'opérations distinctes. Pour sécuriser les données dès l'instant où elles sont écrites dans le stockage, les conditions relatives aux opérations d'écriture des objets blob doivent exiger que les balises d'index associées soient définies sur les valeurs appropriées lors de la même opération de mise à jour.

#### <a name="tags-on-copied-blobs"></a>Balises des objets blob copiés

Par défaut, les balises d'index de blob ne sont pas copiées de l'objet blob source vers la destination lorsque l'API [Copy Blob](/rest/api/storageservices/Copy-Blob) ou l'une de ses variantes est utilisée. Pour conserver l'étendue de l'accès de l'objet blob lors de la copie, ses balises doivent également être explicitement copiées.

#### <a name="tags-on-snapshots"></a>Balises des instantanés

La mise à jour des balises des instantanés d'objet blob n'est pas prise en charge dans la préversion. Vous devez mettre à jour les balises de l'objet blob avant de prendre l'instantané. Toute mise à jour des balises s'appliquera uniquement à l'objet blob de base. Les balises de l'instantané conserveront la valeur précédente.

Si une balise de l'objet blob de base est modifiée après la prise d'un instantané et qu'une condition utilise cette balise, l'étendue de l'accès de l'objet blob de base sera peut-être différente de celle de l'instantané de l'objet blob.

#### <a name="tags-on-blob-versions"></a>Balises des versions des objets blob

Les balises d'index de blob ne sont pas copiées lorsqu'une version d'un objet blob est créée via les API [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) ou [Copy Blob](/rest/api/storageservices/Copy-Blob). Vous pouvez spécifier des balises via l'en-tête de ces API.

Vous pouvez modifier les balises des différentes versions d'un objet blob, mais celles-ci ne sont pas automatiquement mises à jour lorsque les balises d'un objet blob de base sont modifiées. Si vous souhaitez modifier l'étendue de l'accès d'un objet blob et de toutes ses versions à l'aide de balises, vous devez mettre à jour les balises de l'objet blob de base ainsi que de toutes ses versions.

#### <a name="querying-and-filtering-limitations-for-versions-and-snapshots"></a>Limitations de l'interrogation et du filtrage pour les versions et les instantanés

Lorsque vous utilisez des balises pour interroger et filtrer les objets blob dans un conteneur, seuls les objets blob de base sont inclus dans la réponse. Les versions des objets blob ou les instantanés disposant des clés et des valeurs demandées ne sont pas inclus.

## <a name="roles-and-permissions"></a>Rôles et autorisations

Si vous utilisez des conditions d'attribution de rôle pour les [rôles intégrés Azure](../../role-based-access-control/built-in-roles.md), vous devez examiner attentivement toutes les autorisations que le rôle accorde à un principal.

### <a name="inherited-role-assignments"></a>Attributions de rôle héritées

Les attributions de rôle peuvent être configurées pour un groupe d'administration, un abonnement, un groupe de ressources, un compte de stockage ou un conteneur, et celles-ci sont héritées à chaque niveau dans l'ordre indiqué. Le contrôle Azure RBAC repose sur un modèle additif, de sorte que les autorisations effectives correspondent à la somme des attributions de rôle de chaque niveau. Si un principal de sécurité se voit attribuer une autorisation par le biais de plusieurs rôles ou d'un rôle donné à plusieurs niveaux, l'accès d'une opération utilisant cette autorisation est évalué séparément pour chaque rôle attribué à chaque niveau.

Comme les conditions sont implémentées en tant que conditions sur les attributions de rôle, toute attribution de rôle inconditionnelle peut permettre aux utilisateurs de contourner les restrictions d'accès prévues par la stratégie de conditions. Par exemple, si un principal de sécurité se voit attribuer un rôle, tel que *Contributeur aux données Blob du stockage*, à la fois au niveau de l'abonnement et du compte de stockage, et que la condition d'attribution de rôle est uniquement définie au niveau du compte de stockage, le principal disposera d'un accès illimité au compte par le biais de l'attribution de rôle au niveau de l'abonnement, et vice versa.

Par conséquent, les conditions doivent être appliquées de manière cohérente à tous les niveaux d'une hiérarchie de ressources où les principaux de sécurité ont obtenu l'accès à une ressource.

## <a name="other-considerations"></a>Autres éléments à prendre en compte

### <a name="condition-operations-that-write-blobs"></a>Opérations de condition qui écrivent des objets blob

De nombreuses opérations qui écrivent des objets blob requièrent l'autorisation `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` ou `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`. Des rôles intégrés, tels que [Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) et [Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) accordent ces deux autorisations à un principal de sécurité.

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

