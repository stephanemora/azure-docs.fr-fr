---
title: Suppression réversible pour les objets blob
titleSuffix: Azure Storage
description: La fonctionnalité de suppression réversible pour les objets blob protège vos données, ce qui vous permet de récupérer plus facilement vos données en cas de modification ou de suppression malencontreuses de celles-ci par une application ou un autre utilisateur du compte de stockage.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 29d9dd7757319e59fc12b42d89c2ce16dec71b8b
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551065"
---
# <a name="soft-delete-for-blobs"></a>Suppression réversible pour les objets blob

La suppression réversible d’objets blob protège un objet blob, un instantané ou une version contre les suppressions ou les remplacements accidentels en conservant les données supprimées dans le système pendant un laps de temps spécifié. Pendant la période de conservation, vous pouvez restaurer un objet supprimé de manière réversible à son état au moment de sa suppression. Une fois la période de conservation expirée, l’objet est supprimé définitivement.

## <a name="recommended-data-protection-configuration"></a>Configuration recommandée de la protection des données

La suppression réversible d’objets blob fait partie d’une stratégie complète de protection des données pour les données blob. Pour une protection optimale de vos données blob, Microsoft recommande d’activer toutes les fonctionnalités de protection des données suivantes :

- Suppression réversible de conteneur, pour restaurer un conteneur supprimé. Pour savoir comment activer la suppression réversible de conteneur, consultez [Activer et gérer la suppression réversible pour les conteneurs](soft-delete-container-enable.md).
- Gestion des versions des objets blob, pour gérer automatiquement les versions précédentes d’un objet blob. Lorsque le contrôle de version est activé, vous pouvez restaurer une version antérieure d’un objet blob pour récupérer vos données si celles-ci ont été modifiées ou supprimées par erreur. Pour savoir comment activer le contrôle de version des blobs, consultez [Activer et gérer le contrôle de version des blobs](versioning-enable.md).
- Suppression réversible de blob, pour restaurer un blob, un instantané ou une version supprimés. Pour savoir comment activer la suppression réversible de blob, consultez [Activer et gérer la suppression réversible pour les blobs](soft-delete-blob-enable.md).

Pour en savoir plus sur les recommandations de Microsoft en matière de protection des données, consultez [Vue d’ensemble de la protection des données](data-protection-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-soft-delete-works"></a>Fonctionnement de la suppression réversible d’objets blob

Lorsque vous activez la suppression réversible d’objets blob pour un compte de stockage, vous spécifiez une période de conservation pour les objets supprimés comprise entre 1 et 365 jours. La période de conservation indique la durée pendant laquelle les données restent disponibles une fois qu’elles ont été supprimées ou remplacées. Le décompte de la période de conservation commence dès qu’un objet est supprimé ou remplacé.

Lorsque la période de conservation est active, vous pouvez restaurer un objet blob supprimé, ainsi que ses instantanés, ou une version supprimée en appelant l’opération [Annuler la suppression d’un objet blob](/rest/api/storageservices/undelete-blob). Le diagramme suivant montre comment un objet supprimé peut être restauré lorsque la suppression réversible d’objets blob est activée :

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Diagramme montrant comment un objet Blob supprimé de manière réversible peut être restauré":::

Vous pouvez modifier la période de rétention de suppression réversible à tout moment. Une période de conservation mise à jour s’applique uniquement aux données qui ont été supprimées après la modification de la période de conservation. Toutes les données qui ont été supprimées avant la modification de la période de conservation sont soumises à la période de conservation qui était en vigueur lors de la suppression.

Une tentative de suppression d’un objet supprimé de manière réversible n’affecte pas son délai d’expiration.

Si vous désactivez la suppression réversible d’objets blob, vous pouvez continuer à accéder aux objets supprimés de manière réversible et à les récupérer dans votre compte de stockage jusqu’à ce que la période de conservation de la suppression réversible soit écoulée.

Le contrôle de version des objets blob est disponible pour les comptes de stockage d’objets blob et d’objets blob de blocs à usage général v2. Les comptes de stockage avec espace de noms hiérarchique activé pour une utilisation avec Azure Data Lake Storage Gen2 ne sont actuellement pas pris en charge.

La version 2017-07-29 et les versions ultérieures de l’API REST de Stockage Azure prennent en charge la suppression réversible d’objets blob.

> [!IMPORTANT]
> Vous pouvez utiliser la suppression réversible d’objets blob uniquement pour restaurer un objet blob, un instantané ou une version individuels. Pour restaurer un conteneur et son contenu, la suppression réversible de conteneur doit également être activée pour le compte de stockage. Microsoft recommande d’activer la suppression réversible de conteneur et le contrôle de version des objets blob avec la suppression réversible d’objets blob pour garantir une protection complète des données blob. Pour plus d’informations, consultez [Vue d’ensemble de la protection des données](data-protection-overview.md).
>
> La suppression réversible d’objets blob ne protège pas contre la suppression d’un compte de stockage. Pour empêcher toute suppression d’un compte de stockage, configurez un verrou sur la ressource du compte de stockage. Pour plus d’informations sur le verrouillage d’un compte de stockage, consultez [Appliquer un verrou Azure Resource Manager à un compte de stockage](../common/lock-account-resource.md).

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>Traitement des suppressions lorsque la suppression réversible est activée

Lorsque la suppression réversible d’objets blob est activée, la suppression d’un objet blob marque celui-ci comme étant supprimé de manière réversible. Aucun instantané n’est créé. Lorsque la période de conservation expire, l’objet blob supprimé de manière réversible est définitivement supprimé.

Si un objet blob a des instantanés, il ne peut pas être supprimé, à moins de supprimer aussi les instantanés. Lorsque vous supprimez un objet blob et ses instantanés, ils sont marqués comme étant supprimés de manière réversible. Aucun nouvel instantané n’est créé.

Vous pouvez également supprimer un ou plusieurs instantanés actifs sans supprimer l’objet blob de base. Dans ce cas, l’instantané est supprimé de manière réversible.

Les objets supprimés de manière réversible sont invisibles, sauf s’ils sont explicitement affichés ou répertoriés. Pour plus d’informations sur la façon de répertorier les objets supprimés de manière réversible, consultez [Gérer et restaurer des objets blob supprimés de manière réversible](soft-delete-blob-manage.md).

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>Traitement des remplacements lorsque la suppression réversible est activée

L’appel d’une opération telle que [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list)ou [Copy Blob](/rest/api/storageservices/copy-blob) remplace les données dans un objet blob. Lorsque la suppression réversible d’objets blob est activée, le remplacement d’un objet blob crée automatiquement un instantané supprimé de manière réversible de l’état de l’objet blob avant l’opération d’écriture. Lorsque la période de conservation expire, l’instantané supprimé de manière réversible est définitivement supprimé.

Les instantanés supprimés de manière réversible sont invisibles, sauf si les objets supprimés de manière réversible sont explicitement affichés ou répertoriés. Pour plus d’informations sur la façon de répertorier les objets supprimés de manière réversible, consultez [Gérer et restaurer des objets blob supprimés de manière réversible](soft-delete-blob-manage.md).

Pour protéger une opération de copie, la suppression réversible d’objets blob doit être activée pour le compte de stockage de destination.

La suppression réversible d’objets blob ne protège pas contre les opérations d’écriture de métadonnées ou de propriétés de blob. Aucun instantané supprimé de manière réversible n’est créé lors de la mise à jour des métadonnées ou des propriétés d’un objet blob.

La suppression réversible d’objets blob n’offre pas de protection contre le remplacement pour les objets blob au niveau archive. Si un objet blob au niveau archive est remplacé par un nouvel objet blob à un niveau quelconque, alors l’objet blob remplacé est supprimé définitivement.

Pour les comptes de stockage Premium, les instantanés supprimés de manière réversible ne sont pas pris en compte dans la limite de 100 instantanés par blob.

### <a name="restoring-soft-deleted-objects"></a>Restauration d’objets supprimés de manière réversible

Vous pouvez restaurer des objets blob supprimés de manière réversible en appelant l’opération [Annuler la suppression d’un objet blob](/rest/api/storageservices/undelete-blob) durant la période de conservation. L’opération **Annuler la suppression d’un objet blob** restaure un objet blob et tous les instantanés supprimés de manière réversible qui lui sont associés. Les instantanés qui ont été supprimés pendant la période de conservation sont restaurés.

L’appel de l’action **Annuler la suppression d’un objet blob** sur un objet blob qui n’est pas supprimé de manière réversible restaure les instantanés supprimés de manière réversible associés à l’objet blob. Si l’objet blob n’a pas d’instantanés et n’est pas supprimé de manière réversible, l’appel de l’action **Annuler la suppression d’un objet blob** n’a aucun effet.

Pour promouvoir un instantané supprimé de manière réversible vers l’objet blob de base, commencez par appeler l’action **Annuler la suppression d’un objet blob** sur l’objet blob de base pour restaurer l’objet blob et ses instantanés. Ensuite, copiez l’instantané souhaité sur l’objet blob de base. Vous pouvez également copier l’instantané vers un nouvel objet blob.

Les données d’un objet blob ou d’un instantané supprimé de manière réversible ne peuvent pas être lues tant que l’objet n’a pas été restauré.

Pour plus d’informations sur la façon de restaurer les objets supprimés de manière réversible, consultez [Gérer et restaurer des objets blob supprimés de manière réversible](soft-delete-blob-manage.md).

## <a name="blob-soft-delete-and-versioning"></a>Suppression réversible d’objets blob et contrôle de version

Si le contrôle de version des objets blob et la suppression réversible d’objets blob sont tous deux activés sur un compte de stockage, alors le remplacement d’un objet blob crée automatiquement une nouvelle version. La nouvelle version n’est pas supprimée de manière réversible et n’est pas supprimée à l’expiration de la période de rétention de la suppression réversible. Aucun instantané supprimé de manière réversible n’est créé. Lorsque vous supprimez un objet blob, la version actuelle de l’objet blob devient la version antérieure et la version actuelle est supprimée. Aucune nouvelle version n’est créée et aucun instantané supprimé de manière réversible n’est créé.

L’activation de la suppression réversible et du contrôle de version empêche la suppression des versions d’objets blob. Lorsque la suppression réversible est activée, la suppression d’une version crée une version supprimée de manière réversible. Vous pouvez utiliser l’opération **Annuler la suppression d’un objet blob** pour restaurer une version supprimée de manière réversible, à condition qu’il y ait une version actuelle de l’objet blob. S’il n’existe aucune version actuelle, vous devez copier une version précédente dans la version actuelle avant d’appeler l’opération **Annuler la suppression d’un objet blob**.

> [!NOTE]
> L’appel de l’opération **Annuler la suppression d’un objet blob** sur un objet blob supprimé lorsque le contrôle de version est activé restaure les versions ou les instantanés supprimés de manière réversible, mais ne restaure pas l’objet blob de base. Pour restaurer l’objet blob de base, promouvez une version précédente en la copiant dans l’objet blob de base.

Microsoft recommande d’activer le contrôle de version et la suppression réversible d’objets blob pour vos comptes de stockage afin d’obtenir une protection optimale des données. Pour plus d’informations sur l’utilisation conjointe de la gestion des versions d’objets BLOB et de la suppression réversible, consultez [Contrôle de version d’objet BLOB et suppression réversible](versioning-overview.md#blob-versioning-and-soft-delete).

## <a name="blob-soft-delete-protection-by-operation"></a>Protection contre la suppression réversible d’objets blob par opération

Le tableau suivant décrit le comportement attendu pour les opérations de suppression et d’écriture lorsque la suppression réversible d’objets blob est activée, avec ou sans le contrôle de version d’objet blob :

| Opérations de l'API REST | Suppression réversible activée | Suppression réversible et contrôle de version activés |
|--|--|--|
| [Supprimer le compte de stockage](/rest/api/storagerp/storageaccounts/delete) | Aucune modification. Les conteneurs et objets blob dans le compte supprimé ne sont pas récupérables. | Aucune modification. Les conteneurs et objets blob dans le compte supprimé ne sont pas récupérables. |
| [Delete Container](/rest/api/storageservices/delete-container) | Aucune modification. Les objets blob figurant dans le conteneur supprimé ne sont pas récupérables. | Aucune modification. Les objets blob figurant dans le conteneur supprimé ne sont pas récupérables. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | Si cette opération est utilisée pour supprimer un objet blob, celui-ci est marqué comme étant supprimé de manière réversible. <br /><br /> Si cette opération est utilisée pour supprimer un instantané blob, l’instantané est marqué comme supprimé de manière réversible. | Si cette opération est utilisée pour supprimer un objet blob, la version actuelle devient la version antérieure et la version actuelle est supprimée. Aucune nouvelle version n’est créée et aucun instantané supprimé de manière réversible n’est créé.<br /><br /> Si cette opération est utilisée pour supprimer une version d’objet blob, la version est marquée comme étant supprimée de manière réversible. |
| [Annuler la suppression d’un objet blob](/rest/api/storageservices/delete-blob) | Restaure un objet blob et les instantanés qui ont été supprimés pendant la période de conservation. | Restaure un objet blob et les versions qui ont été supprimés pendant la période de conservation. |
| [Put Blob](/rest/api/storageservices/put-blob)<br />[Put Block List](/rest/api/storageservices/put-block-list)<br />[Copy Blob](/rest/api/storageservices/copy-blob)<br />[Copier un objet blob à partir d’une URL](/rest/api/storageservices/copy-blob) | Si cette opération est appelée sur un objet blob actif, un instantané de l’état de l’objet blob avant l’opération est généré automatiquement. <br /><br /> Si cette opération est appelée sur un objet blob supprimé de manière réversible, un instantané de l’état précédent de l’objet blob est généré uniquement s’il est remplacé par un objet blob du même type. Si le type de l’objet blob est différent, toutes les données supprimées de manière réversible sont définitivement supprimées. | Une nouvelle version qui capture l’état de l’objet blob avant l’opération est générée automatiquement. |
| [Put Block](/rest/api/storageservices/put-block) | Si cette opération est utilisée pour valider un bloc dans un objet blob actif, rien ne change.<br /><br />Si cette opération est utilisée pour valider un bloc dans un objet blob supprimé de manière réversible, un nouvel objet blob est créé et un instantané est généré automatiquement pour capturer l’état de l’objet blob supprimé de manière réversible. | Aucune modification. |
| [Put Page](/rest/api/storageservices/put-page)<br />[Placer la page à partir de l’URL](/rest/api/storageservices/put-page-from-url) | Aucune modification. Les données de l’objet blob de pages remplacées ou effacées par cette opération n’étant pas enregistrées, elles sont irrécupérables. | Aucune modification. Les données de l’objet blob de pages remplacées ou effacées par cette opération n’étant pas enregistrées, elles sont irrécupérables. |
| [Append Block](/rest/api/storageservices/append-block)<br />[Ajouter un bloc à partir d’une URL](/rest/api/storageservices/append-block-from-url) | Aucune modification. | Aucune modification. |
| [Set Blob Properties](/rest/api/storageservices/set-blob-properties) | Aucune modification. Les propriétés d’objet blob remplacées ne sont pas récupérables. | Aucune modification. Les propriétés d’objet blob remplacées ne sont pas récupérables. |
| [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) | Aucune modification. Les métadonnées de l’objet blob remplacé ne sont pas récupérables. | Une nouvelle version qui capture l’état de l’objet blob avant l’opération est générée automatiquement. |
| [Set Blob Tier](/rest/api/storageservices/set-blob-tier) | L’objet blob de base est déplacé vers le nouveau niveau. Les instantanés actifs ou supprimés de manière réversible restent dans le niveau d’origine. Aucun instantané supprimé de manière réversible n’est créé. | L’objet blob de base est déplacé vers le nouveau niveau. Les versions actives ou supprimées de manière réversible restent dans le niveau d’origine. Aucune nouvelle version n’est créée. |

## <a name="pricing-and-billing"></a>Tarification et facturation

Toutes les données supprimées de manière réversible sont facturées au même tarif que des données actives. Vous ne serez pas facturé pour des données supprimées définitivement à l’issue de la période de conservation.

Lorsque vous activez la suppression réversible, Microsoft vous recommande d’utiliser une courte période de conservation pour mieux comprendre l’impact de cette fonctionnalité sur votre facture. La période de conservation minimale recommandée est de sept jours.

L’activation de la suppression réversible pour les données fréquemment remplacées peut entraîner une augmentation des frais en termes de capacité de stockage, ainsi qu’une latence plus élevée lors de la classification des objets blob. Vous pouvez réduire ce coût et cette latence supplémentaires en stockant les données fréquemment remplacées dans un compte de stockage distinct où la suppression réversible est désactivée.

Vous n’êtes pas facturé pour les transactions liées à la génération automatique d’instantanés ou de versions lorsqu’un objet blob est remplacé ou supprimé. Vous êtes facturé pour les appels à l’opération **Annuler la suppression d’un objet blob** au taux de transaction pour les opérations d’écriture.

Pour plus d’informations sur la tarification du Stockage Blob, consultez la page [Tarification du Stockage Blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>Suppression réversible d’objets blob et disques de machine virtuelle  

La suppression réversible d’objets blob est disponible pour les disques non managés Standard et Premium, qui sont des objets blob de pages en arrière-plan. La suppression réversible peut vous aider à récupérer des données supprimées ou remplacées par les opérations **Delete Blob**, **Put Blob**, **Put Block List** et **Copy Blob** uniquement.

Les données remplacées par un appel de l’opération **Put Page** ne sont pas récupérables. Étant donné qu’une machine virtuelle Azure écrit sur un disque non managé à l'aide d'appels à **Put Page**, la suppression réversible pour annuler des écritures sur un disque non managé à partir d'une machine virtuelle Azure n'est pas un scénario pris en charge.

## <a name="next-steps"></a>Étapes suivantes

- [Activer la suppression réversible pour les objets blob](./soft-delete-blob-enable.md)
- [Gérer et restaurer des objets blob supprimés de manière réversible](soft-delete-blob-manage.md)
- [Contrôle de version des blobs](versioning-overview.md)