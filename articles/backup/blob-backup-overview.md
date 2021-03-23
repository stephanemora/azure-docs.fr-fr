---
title: Vue d’ensemble de la sauvegarde opérationnelle des objets blob Azure
description: Découvrez la sauvegarde opérationnelle des objets blob Azure (préversion).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: b10191c8a01d3cc7a92dee8ca9bf59a506497a60
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743048"
---
# <a name="overview-of-operational-backup-for-azure-blobs-in-preview"></a>Vue d’ensemble de la sauvegarde opérationnelle des objets blob Azure (préversion)

La sauvegarde opérationnelle des objets blob est une solution gérée locale de protection des données qui permet de protéger les objets blob de blocs contre différentes situations de perte de données, notamment les altérations, la suppression d’objets blob et la suppression accidentelle de comptes de stockage. Les données sont stockées localement dans le compte de stockage source proprement dit, et peuvent être récupérées au point dans le temps sélectionné si nécessaire. La solution offre ainsi un moyen simple, sécurisé et économique de protéger des objets blob.

La sauvegarde opérationnelle des objets blob s’intègre avec le [centre de sauvegarde](backup-center-overview.md), entre autres fonctionnalités de gestion des sauvegardes, pour permettre de gérer, de superviser, de lancer et d’analyser les sauvegardes à grande échelle en un seul et même endroit.

## <a name="how-operational-backup-works"></a>Fonctionnement d’une sauvegarde opérationnelle

La sauvegarde opérationnelle d’objets blob est une solution de **sauvegarde locale**. Les données de sauvegarde ne sont donc pas transférées dans le coffre de sauvegarde, mais stockées dans le compte de stockage source proprement dit. Toutefois, le coffre de sauvegarde sert quand même d’unité de gestion des sauvegardes. Il s’agit aussi d’une solution de **sauvegarde continue**, ce qui signifie qu’il n’est pas nécessaire de planifier des sauvegardes ; toutes les modifications sont conservées et peuvent être restaurées à leur état à un moment donné.

La sauvegarde opérationnelle utilise des fonctionnalités de plateforme blob pour protéger les données et permettre la récupération si nécessaire :

- **Restauration à un instant dans le passé** : la restauration [d’objets blob à un instant dans le passé](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview) permet de restaurer des données blob à un état antérieur. Elle utilise la **suppression réversible**, le **flux de modification** et le **contrôle de version des objets blob** pour conserver les données pendant la durée spécifiée. La sauvegarde opérationnelle prend en charge l’activation de la restauration à un instant dans le passé ainsi que les fonctionnalités sous-jacentes pour garantir la rétention des données pendant la durée spécifiée.

- **Verrou de suppression** : le verrou de suppression empêche la suppression du compte de stockage lorsqu’elle est effectuée par accident ou par des utilisateurs non autorisés. La sauvegarde opérationnelle peut également être configurée pour appliquer automatiquement un verrou de suppression, et ainsi réduire les possibilités de perte de données liées à la suppression du compte de stockage.

Pour plus d’informations sur les limitations de la solution actuelle, consultez la [matrice de prise en charge](blob-backup-support-matrix.md).

### <a name="protection"></a>Protection

La sauvegarde opérationnelle est configurée et gérée au niveau du **compte de stockage**. Elle s’applique à tous les objets blob de blocs du compte de stockage. Elle utilise une **stratégie de sauvegarde** pour gérer la durée de rétention des données de sauvegarde (y compris les anciennes versions et les objets blob supprimés), ce qui par là même définit la période pendant laquelle il est possible de restaurer les données. La stratégie de sauvegarde peut spécifier une rétention maximale de 360 jours, ou un nombre équivalent de semaines entières (51) ou de mois (11).

Quand vous configurez la sauvegarde pour un compte de stockage et que vous affectez une stratégie de sauvegarde avec une rétention de « n » jours, les propriétés sous-jacentes sont définies comme indiqué ci-dessous. Vous pouvez afficher ces propriétés dans l’onglet **Protection des données** du service blob dans votre compte de stockage.

- **Restauration à un instant dans le passé** : définie sur « n » jours, comme dans la stratégie de sauvegarde. Si un point dans le temps a déjà été activé pour le compte de stockage avec une rétention de « x » jours, avant la configuration de la sauvegarde, la durée de la restauration à un instant dans le passé est définie sur la plus grande des deux valeurs, soit max(n x). Si vous avez déjà activé la restauration à un instant dans le passé et spécifié qu’elle doit être supérieure à celle de la stratégie de sauvegarde, elle reste inchangée.

- **Suppression réversible** : définie sur « n + 5 » jours, c’est-à-dire cinq jours en plus de la durée spécifiée dans la stratégie de sauvegarde. Si la suppression réversible a déjà été activée sur le compte de stockage configuré pour la sauvegarde opérationnelle, avec une rétention de « y » jours, la rétention de la suppression réversible est définie sur la valeur maximale entre les deux, soit max(n + 5,y). Si vous avez déjà activé la suppression réversible et spécifié que la rétention doit être supérieure à celle de la stratégie de sauvegarde, elle reste inchangée.

- **Contrôle de version pour les objets blob et le flux de modification des objets blob** : le contrôle de version et le flux de modification sont activés pour les comptes de stockage sur lesquels la sauvegarde opérationnelle a été configurée.

- **Verrou de suppression** : le fait de configurer la sauvegarde opérationnelle sur un compte de stockage a également pour effet d’appliquer un verrou de suppression sur le compte de stockage. Ce verrou peut être affiché dans l’onglet **Verrous** du compte de stockage.

Pour permettre à la sauvegarde d’activer ces propriétés sur les comptes de stockage à protéger, le coffre de sauvegarde doit disposer du rôle **Contributeur de sauvegarde de compte de stockage** sur les comptes de stockage concernés.

>[!NOTE]
>La sauvegarde opérationnelle ne prend en charge que les opérations sur les objets blob de blocs. Les opérations sur les conteneurs, elles, ne peuvent pas être restaurées. Un conteneur supprimé du compte de stockage par l’opération **Supprimer le conteneur** ne peut pas être restauré avec une opération de restauration. Il est recommandé d’activer la suppression réversible pour améliorer la protection et la récupération des données.

### <a name="management"></a>Gestion

Une fois que vous avez activé la sauvegarde sur un compte de stockage, une instance de sauvegarde correspondant à ce compte de stockage est créée dans le coffre de sauvegarde. Vous pouvez effectuer toutes les opérations liées à la sauvegarde pour un compte de stockage (par exemple lancer des restaurations, effectuer une surveillance, arrêter la protection, etc.) par le biais de l’instance de sauvegarde correspondante.

Par ailleurs, la sauvegarde opérationnelle s’intègre directement au centre de sauvegarde pour vous permettre de gérer de manière centralisée la protection de tous vos comptes de stockage, ainsi que de toutes les autres charges de travail prises en charge par la sauvegarde. Le centre de sauvegarde constitue l’interface unique pour toutes les exigences de sauvegarde, notamment la surveillance des tâches et l’état des sauvegardes et des restaurations, la conformité et la gouvernance, l’analyse de l’utilisation de la sauvegarde et l’exécution d’opérations relatives à la sauvegarde et à la restauration des données.

### <a name="restore"></a>Restaurer

Il est possible de restaurer des données à partir de tous les points dans le temps pour lesquels un point de récupération existe. Un point de récupération est créé lorsqu’un compte de stockage se trouve dans un état protégé. Il peut être utilisé pour restaurer des données à condition de se trouver dans la période de rétention définie par la stratégie de sauvegarde (et donc la capacité de restauration à un instant dans le passé du service blob dans le compte de stockage). La sauvegarde opérationnelle utilise la restauration à un instant dans le passé des objets blob pour restaurer les données à partir d’un point de récupération.

La sauvegarde opérationnelle offre la possibilité de restaurer tous les objets blob de blocs du compte de stockage, de parcourir et de restaurer des conteneurs spécifiques ou d’utiliser des correspondances de préfixe pour restaurer un sous-ensemble d’objets blob. Toutes les restaurations peuvent être effectuées sur le compte de stockage source uniquement.

## <a name="pricing"></a>Tarifs

Aucuns frais de gestion ni frais d’instance ne sont engagés avec l’utilisation de la sauvegarde opérationnelle pour les objets blob. Toutefois, les frais suivants sont facturés :

- Les restaurations sont effectuées avec la restauration à un instant dans le passé des objets blob et induisent des frais en fonction de la quantité de données traitées. Pour plus d’informations, consultez [Tarifs de la restauration à un instant dans le passé](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview#pricing-and-billing).

- La rétention des données proposée par la [Suppression réversible pour les objets blob](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview), la [Prise en charge du flux de modification dans le Stockage Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-change-feed) et le [Contrôle de version des objets blob](https://docs.microsoft.com/azure/storage/blobs/versioning-overview).

## <a name="next-steps"></a>Étapes suivantes

- [Configuration et gestion de la sauvegarde d’objets blob Azure](blob-backup-configure-manage.md)
