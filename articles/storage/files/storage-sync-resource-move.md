---
title: Déplacements de ressources et modifications de topologie Azure File Sync
description: Découvrez comment déplacer des ressources Sync entre les groupes de ressources, abonnements et locataires Azure Active Directory (AAD).
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 3/10/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: acd5f9263a74d3273dc65522e77d83c90a719311
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555031"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Déplacement de ressources Azure File Sync dans un autre groupe de ressources, abonnement ou locataire AAD

Cet article explique comment apporter des modifications à un groupe de ressources, à un abonnement ou à un locataire Azure Active Directory (AAD) pour des ressources cloud Azure File Sync et des comptes de stockage Azure.

Pour apporter des modifications aux ressources du cloud Azure File Sync, il est important de prendre en compte en même temps les ressources de stockage. Les ressources qui existent sont les suivantes :

**Ressources Azure File Sync (par ordre hiérarchique)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Service de synchronisation du stockage
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Serveur inscrit
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Groupe de synchronisation
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Point de terminaison cloud
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Point de terminaison de serveur

Dans Azure File Sync, la seule ressource déplaçable est la ressource du service de synchronisation du stockage. Toutes les sous-ressources sont liées à son parent ; elles ne peuvent pas être déplacées dans un autre service de synchronisation du stockage.

**Ressources de stockage Azure (par ordre hiérarchique)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Compte de stockage
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Partage de fichiers

Dans le Stockage Azure, la seule ressource déplaçable est le compte de stockage. En tant que sous-ressource, un partage de fichiers Azure ne peut pas être déplacé dans un autre compte de stockage.

## <a name="supported-combinations"></a>Combinaisons prises en charge

Lorsque vous planifiez un déplacement de ressources, le compte de stockage et la ressource Azure File Sync de niveau supérieur, appelée *service de synchronisation du stockage*, doivent être considérés ensemble.

Il est recommandé que le service de synchronisation du stockage et les comptes de stockage qui comportent des partages de fichiers synchronisés résident toujours dans le même abonnement. Les combinaisons prises en charge sont les suivantes :

* Le service de synchronisation du stockage et les comptes de stockage se trouvent dans des **groupes de ressources différents** (même locataire Azure).
* Le service de synchronisation du stockage et les comptes de stockage se trouvent dans des **abonnements différents** (même locataire Azure).

> [!IMPORTANT]
> Par le biais de différentes combinaisons de déplacements, un service de synchronisation du stockage et des comptes de stockage peuvent se retrouver dans différents abonnements, régis par différents locataires AAD. Même si la synchronisation semble fonctionner, il ne s’agit pas d’une configuration prise en charge. La synchronisation risque de s’arrêter à l’avenir sans possibilité de revenir à un état fonctionnel.

Lorsque vous planifiez votre déplacement de ressources, vous devez prendre en compte différentes considérations selon que le déplacement s’effectue [au sein du même locataire AAD](#move-within-the-same-azure-active-directory-tenant) ou [dans un autre locataire AAD](#move-to-a-new-azure-active-directory-tenant). En cas de déplacement de locataires AAD, déplacez toujours les ressources de synchronisation et de stockage ensemble.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Déplacement au sein du même locataire Azure Active Directory

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Image illustrant le Portail Azure pour une ressource du service de synchronisation du stockage, avec la commande Déplacer développée. Elle montre les options de déplacement dans un autre groupe de ressources et dans un autre abonnement." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Un moyen pratique de déplacer une ressource du service de synchronisation du stockage consiste à utiliser le Portail Azure. Accédez au service de synchronisation du stockage que vous souhaitez déplacer, puis sélectionnez **Déplacer** dans la barre de commandes. Pour le déplacement d’un compte de stockage, la procédure est la même. Vous pouvez également déplacer toutes les ressources d’un groupe de ressources de cette manière. Il est recommandé de déplacer tout un groupe de ressources lorsque vous disposez du service de synchronisation du stockage et de tous les comptes de stockage utilisés dans ce groupe de ressources.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Lorsque vous déplacez une ressource de compte de stockage, la synchronisation s’arrête immédiatement. Vous devez autoriser manuellement la synchronisation à accéder aux comptes de stockage concernés dans le nouvel abonnement. Pour connaître la procédure à suivre, consultez la section [Autorisation d’accès au stockage Azure File Sync](#azure-file-sync-storage-access-authorization).

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Déplacement vers un nouveau locataire Azure Active Directory

Les ressources individuelles comme un service de synchronisation du stockage ou des comptes de stockage ne peuvent pas se déplacer de manière autonome dans un autre locataire AAD. Seuls les abonnements Azure peuvent déplacer des locataires AAD. Pensez à votre structure d’abonnement dans le nouveau locataire AAD. Vous pouvez utiliser un abonnement dédié pour Azure File Sync. 

1. Créez un abonnement Azure (ou déterminez un abonnement existant dans l’ancien locataire à déplacer).
1. [Effectuez un déplacement d’abonnement au sein du même locataire AAD](#move-within-the-same-azure-active-directory-tenant) de votre service de synchronisation du stockage et de tous les comptes de stockage associés.
1. La synchronisation s’arrête. Effectuez immédiatement le déplacement de votre locataire ou [restaurez la capacité de la synchronisation à accéder aux comptes de stockage qui ont été déplacés](#azure-file-sync-storage-access-authorization). Vous pourrez passer au nouveau locataire AAD ultérieurement.

Une fois que toutes les ressources Azure File Sync associées ont été isolées dans leur propre abonnement, vous pouvez déplacer l’intégralité de l’abonnement dans le locataire AAD cible. Le [guide du transfert d’abonnement](../../role-based-access-control/transfer-subscription.md) vous permet de planifier et d’exécuter ce type de transfert.

> [!WARNING]
> Lorsque vous transférez un abonnement d’un locataire à un autre, la synchronisation s’arrête immédiatement. Vous devez autoriser manuellement la synchronisation à accéder aux comptes de stockage concernés dans le nouvel abonnement. Pour connaître la procédure à suivre, consultez la section [Autorisation d’accès au stockage Azure File Sync](#azure-file-sync-storage-access-authorization).

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Image montrant le panneau Vue d’ensemble de l’abonnement du Portail Azure, avec la commander Changer de répertoire de la barre d’outils encadrée au centre, en haut de la page." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Vous pouvez commencer la migration une fois que vous disposez d’un plan et des autorisations requises :
        1. Sur le Portail Azure, accédez au panneau **Vue d’ensemble** de votre abonnement.
        1. Sélectionnez **Changer de répertoire**
        1. Suivez les étapes de l’Assistant pour affecter le nouveau locataire AAD.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Autorisation d’accès au stockage Azure File Sync

Lorsque les comptes de stockage sont déplacés dans un nouvel abonnement ou au sein d’un abonnement dans un nouveau client Azure Active Directory (AAD), la synchronisation s’arrête. Le contrôle d’accès en fonction du rôle (RBAC) permet d’autoriser Azure File Sync à accéder à un compte de stockage. Ces attributions de rôle ne sont pas migrées avec les ressources.

### <a name="azure-file-sync-service-principal"></a>Principal de service Azure File Sync

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Image montrant les fournisseurs de ressources inscrits dans la gestion des abonnements sur le Portail Azure." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        Le principal de service Azure File Sync doit exister dans votre locataire AAD pour que vous puissiez autoriser la synchronisation à accéder à un compte de stockage. </br></br> À l’heure actuelle, quand vous créez un abonnement Azure, le fournisseur de ressources Azure File Sync *Microsoft.StorageSync* est automatiquement inscrit avec votre abonnement. L’inscription du fournisseur de ressources permet de rendre un *principal de service* disponible pour la synchronisation dans le locataire Azure Active Directory qui régit l’abonnement. Un principal de service est similaire à un compte d’utilisateur dans AAD. Vous pouvez utiliser le principal de service Azure File Sync pour autoriser l’accès aux ressources par le biais du contrôle d’accès en fonction du rôle (RBAC). La seule ressource à laquelle la synchronisation a besoin d’accéder est les comptes de stockage contenant les partages de fichiers à synchroniser. *Microsoft.StorageSync* doit posséder le rôle intégré **Lecteur et accès aux données** sur le compte de stockage. </br></br> Cette attribution est effectuée automatiquement par le biais du contexte utilisateur de l’utilisateur connecté lorsque vous ajoutez un partage de fichiers à un groupe de synchronisation ou, en d’autres termes, que vous créez un point de terminaison cloud. Lorsqu’un compte de stockage est déplacé vers un nouvel abonnement ou locataire AAD, cette attribution de rôle est perdue et [doit être rétablie manuellement](#establish-sync-access-to-a-storage-account).
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Si l’abonnement Azure cible n’a pas été créé récemment, vérifiez que le fournisseur de ressources *Microsoft.StorageSync* est inscrit auprès de l’abonnement. Si ce n’est pas le cas, ajoutez-le manuellement dans le même panneau du portail.

### <a name="establish-sync-access-to-a-storage-account"></a>Établissement de l’accès de la synchronisation à un compte de stockage

Le [principal de service Azure File Sync](#azure-file-sync-service-principal) doit être utilisé pour autoriser l’accès à un compte de stockage par le biais du contrôle d’accès en fonction du rôle (RBAC). *Microsoft.StorageSync* doit posséder le rôle intégré **Lecteur et accès aux données** sur le compte de stockage. 

Cette attribution est en général effectuée automatiquement par le biais du contexte utilisateur de l’utilisateur connecté lorsque vous ajoutez un partage de fichiers à un groupe de synchronisation ou, en d’autres termes, que vous créez un point de terminaison cloud. Cependant, lorsqu’un compte de stockage est déplacé vers un nouvel abonnement ou locataire AAD, cette attribution de rôle est perdue et doit être rétablie manuellement.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Image montrant le principal de service Microsoft.StorageSync disposant du rôle Lecteur et accès aux données sur un compte de stockage.":::
    :::column-end:::
    :::column:::
        Sur le Portail Azure, accédez au compte de stockage auquel la synchronisation doit être de nouveau autorisée à accéder. <ol><li>Sélectionnez **Contrôle d’accès (IAM)** dans la table des matières à gauche.</li><li>Sélectionnez l’onglet Attributions de rôle pour voir la liste des utilisateurs et des applications (principaux de service) ayant accès à votre compte de stockage.</li><li>Sélectionnez **Ajouter**</li><li>Dans le champ **Rôle**, sélectionnez **Lecteur et accès aux données**.</li><li>Dans le champ **Sélectionner**, tapez *Microsoft.StorageSync*, sélectionnez le rôle, puis cliquez sur **Enregistrer**.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Déplacement dans une autre région Azure

Le *service de synchronisation du stockage* des ressources Azure File Sync et les comptes de stockage contenant des partages de fichiers synchronisés sont déployés dans une région Azure spécifique. Celle-ci est déterminée lors de la création de la ressource. La région du service de synchronisation du stockage et celle des ressources de compte de stockage doivent correspondre. Elles ne peuvent être modifiées pour aucun des deux types de ressources après leur création.

L’affectation d’une autre région à une ressource est différente d’un [basculement de région](#region-fail-over), qui peut être pris en charge en fonction du paramètre de redondance de votre compte de stockage.

## <a name="region-fail-over"></a>Basculement de région

[Le Stockage Azure offre des options de géoredondance](../common/storage-redundancy.md#geo-redundant-storage) pour un compte de stockage. Ces options peuvent poser des problèmes pour les comptes de stockage utilisés avec Azure File Sync. La principale raison en est que la réplication entre régions géographiquement distantes n’est pas effectuée par Azure File Sync, mais par une technologie de réplication de stockage intégrée au sous-système de stockage dans Azure. Elle ne peut pas connaître l’état de l’application. Par ailleurs, Azure File Sync est une application dans laquelle des fichiers sont à tout moment synchronisés avec des partages de fichiers Azure, dans les deux sens. Si vous optez pour l’une de ces options de redondance du stockage avec dispersion géographique, vous ne perdez pas toutes vos données lors d’un sinistre à grande échelle. Toutefois, vous devez [anticiper la perte de données](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss).

> [!CAUTION]
> Le basculement ne constitue jamais un substitut approprié au fait de provisionner les ressources dans la bonne région Azure. Si vos ressources se trouvent dans la « mauvaise » région, vous devez envisager d’arrêter la synchronisation et de la réactiver sur de nouveaux partages de fichiers Azure déployés dans la région de votre choix.

Un basculement régional peut être lancé par Microsoft dans le cas d’un événement grave qui rend les centres de données d’une région Azure inopérants pendant une période prolongée. Il se peut que la définition du temps d’arrêt que votre entreprise peut supporter soit inférieure à la période que Microsoft est prêt à laisser passer avant d’entamer un basculement régional. Pour de telles situations, [les basculements peuvent également être à l’initiative des clients](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> En cas de basculement, vous devez enregistrer un ticket de support pour vos services de synchronisation du stockage concernés afin que la synchronisation fonctionne à nouveau.

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble des guides de migration des partages de fichiers et de la synchronisation Azure](storage-files-migration-overview.md)
- [Résoudre les problèmes d’Azure File Sync](storage-sync-files-troubleshoot.md)
- [Planifier un déploiement de la fonctionnalité Synchronisation de fichiers Azure](storage-sync-files-planning.md)
