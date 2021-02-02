---
title: Glossaire du service Sauvegarde Azure
description: Cet article définit des termes qui peuvent être utiles lors de l’utilisation du service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 5b575e0f56c9cf39987e9e77850ab1d9b2e80d93
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98723912"
---
# <a name="azure-backup-glossary"></a>Glossaire du service Sauvegarde Azure

Ce glossaire peut être utile lors de l’utilisation du service Sauvegarde Azure.

>[!NOTE]
>
> - Les termes qui sont marqués avec le préfixe « (Terme propre aux charges de travail) » font référence aux termes qui sont uniquement pertinents dans le contexte d’un sous-ensemble spécifique de charges de travail prises en charge par le service Sauvegarde Azure.
> - Pour les termes couramment utilisés dans la documentation du service Sauvegarde Azure, mais qui font référence à d’autres services Azure, un lien direct est fourni vers la documentation du service Azure concerné.

## <a name="afs-azure-file-shares"></a>AFS (partages de fichiers Azure)

Reportez-vous à la [documentation Azure Files](../storage/files/storage-files-introduction.md).

## <a name="alternate-location-recovery"></a>Récupération à un autre emplacement

Récupération opérée à partir du point de récupération vers un emplacement autre que l’emplacement d’origine à partir duquel les sauvegardes ont été effectuées. Lors de l’utilisation d’une sauvegarde de machine virtuelle Azure, l’opération consiste à restaurer la machine virtuelle sur un serveur autre que le serveur d’origine où les sauvegardes ont été effectuées. Lors de l’utilisation d’une sauvegarde de partage de fichiers Azure, l’opération consiste à restaurer les données sur un partage de fichiers différent du partage de fichiers sauvegardé.

## <a name="application-consistent-backup"></a>Sauvegarde cohérente des applications

(Terme propre aux charges de travail)

Les sauvegardes cohérentes des applications capturent le contenu et les opérations d’E/S en attente de la mémoire. Les instantanés de cohérence d’application utilisent l’enregistreur [VSS](#vss-windows-volume-shadow-copy-service) (ou bien un pré-script ou un post-script pour Linux) pour vérifier la cohérence des données d’application avant une sauvegarde. [Plus d’informations](backup-azure-vms-introduction.md)

## <a name="azure-resource-manager-arm-templates"></a>Modèles Azure Resource Manager (ARM)

Reportez-vous à la [documentation sur les modèles ARM](../azure-resource-manager/templates/overview.md).

## <a name="autoprotection-for-databases"></a>Autoprotection (pour les bases de données)

(Terme propre aux charges de travail)

L’autoprotection est une fonctionnalité qui vous permet de protéger automatiquement toutes les bases de données d’une instance SQL autonome ou d’un groupe de disponibilité SQL Server Always On. Elle permet non seulement d’activer des sauvegardes pour les bases de données existantes, mais également de protéger toutes les bases de données que vous êtes susceptible d’ajouter à l’avenir.

## <a name="availability-storage-replication-types"></a>Disponibilité (types de réplication de stockage)

Le service Sauvegarde Azure offre trois types de réplication pour conserver votre stockage et vos données hautement disponibles :

### <a name="lrs"></a>LRS

Le [stockage localement redondant (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) réplique vos données de sauvegarde trois fois (il crée trois copies de vos données de sauvegarde) dans une unité d’échelle de stockage d’un centre de données. Toutes les copies des données de sauvegarde existent dans la même région. Le stockage LRS est une option à faible coût qui protège vos données de sauvegarde contre les défaillances matérielles locales.

### <a name="grs"></a>GRS

Le [stockage géoredondant (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) est l’option de réplication par défaut : c’est l’option recommandée. Le stockage géo-redondant réplique vos données de sauvegarde vers une région secondaire, distante de plusieurs centaines de kilomètres de l’emplacement principal des données sources. Le stockage GRS est plus onéreux que le stockage LRS, mais il offre une durabilité supérieure des données de sauvegarde, même en cas de panne au niveau régional.

>[!NOTE]
>Pour les coffres GRS pour lesquels la fonctionnalité de restauration inter-régions est activée, le stockage de sauvegarde est mis à niveau de GRS vers RA-GRS (stockage géoredondant avec accès en lecture).

### <a name="zrs"></a>ZRS

[Le stockage redondant interzone (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) réplique vos données de sauvegarde dans des [zones de disponibilité](../availability-zones/az-overview.md#availability-zones), garantissant ainsi la résidence et la résilience des données de sauvegarde dans la même région. Vos charges de travail critiques qui nécessitent la [résidence des données](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) peuvent donc être sauvegardées dans un stockage redondant interzone.

## <a name="azure-command-line-interface-cli"></a>Interface de ligne de commande Azure (CLI)

Reportez-vous à la [Documentation sur Azure CLI](/cli/azure/what-is-azure-cli).

## <a name="azure-policy"></a>Azure Policy

Reportez-vous à la [documentation sur Azure Policy](../governance/policy/overview.md).

## <a name="azure-powershell"></a>Azure PowerShell

Reportez-vous à la [documentation sur Azure PowerShell](/powershell/azure/).

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM)

Reportez-vous à la [documentation sur Azure Resource Manager](../azure-resource-manager/management/overview.md).

## <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Reportez-vous à la [documentation sur Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md).

## <a name="backend-storage--cloud-storage--backup-storage"></a>Stockage back-end / Stockage cloud / Stockage de sauvegarde

Stockage réel utilisé par une instance de sauvegarde. Comprend la taille de tous les points de conservation qui existent pour l’instance de sauvegarde (tels que définis par la stratégie de sauvegarde et de conservation).

## <a name="bare-metal-backup"></a>Sauvegarde complète

Sauvegarde des fichiers du système d’exploitation et de toutes les données sur les volumes critiques, sauf les données utilisateur. Par définition, une sauvegarde complète inclut une sauvegarde de l’état du système. Elle offre une protection quand un ordinateur ne démarre pas et que vous devez récupérer tous les éléments nécessaires. [Plus d’informations](backup-mabs-system-state-and-bmr.md)

## <a name="backup-extensions--vm-extensions"></a>Extensions de sauvegarde / Extensions de machine virtuelle

(Propre au type de charge de travail de machine virtuelle Azure)

Les extensions de machine virtuelle Azure sont de petites applications permettant d’exécuter des tâches de configuration et d’automatisation post-déploiement sur des machines virtuelles Azure. Sauvegarde Azure sauvegarde les machines virtuelles Azure en installant une extension à l’agent de machine virtuelle Azure en cours d’exécution sur l’ordinateur. Le service Sauvegarde Azure gère automatiquement ces extensions, ce qui évite aux utilisateurs de devoir les mettre à jour manuellement.

## <a name="backup-instance--backup-item"></a>Instance de sauvegarde / Élément de sauvegarde

Une source de données sauvegardée dans un coffre avec une stratégie de sauvegarde et de conservation particulière constitue une instance de sauvegarde ou un élément de sauvegarde.

## <a name="backup-rule--backup-policy"></a>Règle de sauvegarde / Stratégie de sauvegarde

Une règle de sauvegarde est une règle définie par l’utilisateur qui spécifie quand et à quelle fréquence les sauvegardes doivent être effectuées sur une source de données. Pour certains types de charges de travail, la stratégie de sauvegarde fournit également un moyen de spécifier la méthode d’instantané à appliquer à la source de données (complet, incrémentiel, différentiel). Les stratégies de sauvegarde sont souvent créées sous la forme d’une combinaison de règles de sauvegarde et de règles de conservation.

## <a name="backup-vault"></a>Archivage de sauvegarde

Ressource Azure Resource Manager de type *Microsoft.DataProtection/BackupVaults*. Les coffres de sauvegarde sont utilisés pour sauvegarder des bases de données Azure pour serveur PostgreSQL. [Découvrez-en plus sur les coffres de sauvegarde](backup-azure-recovery-services-vault-overview.md).

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (Continuité d’activité et reprise d’activité)

BCDR implique un ensemble de processus qu’une organisation doit adopter pour s’assurer que les applications et les charges de travail sont opérationnelles pendant les interruptions de service planifiées et non planifiées ou les pannes Azure, avec une interruption minimale des activités de l’entreprise. [Découvrez-en plus](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) sur les différents services proposés par Azure pour vous aider à créer une stratégie BCDR solide.

## <a name="churn"></a>Évolution

Pourcentage de changement des données sauvegardées entre deux sauvegardes consécutives. Ce changement peut être dû à l’ajout de nouvelles données ou à la modification ou à la suppression de données existantes.

## <a name="crash-consistent-backup"></a>Sauvegarde avec cohérence en cas d’incident

(Terme propre aux charges de travail)

Des instantanés de cohérence des incidents sont pris généralement si une machine virtuelle Azure s’arrête au moment de la sauvegarde. Seules les données déjà présentes sur le disque au moment de la sauvegarde sont capturées et sauvegardées. [Plus d’informations](backup-azure-vms-introduction.md#snapshot-consistency)

## <a name="cross-region-restore-crr"></a>Restauration entre régions (CRR)

Parmi les [options de restauration](backup-azure-arm-restore-vms.md#restore-options), la restauration entre régions (CRR) vous permet de restaurer des éléments de sauvegarde dans une région secondaire, qui est une [région jumelée Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="data-box"></a>Data Box

Reportez-vous à la [documentation sur Data Box](../databox/data-box-overview.md).

## <a name="datasource"></a>Source de données

Ressource (ressource Azure, ressource proxy ou ressource locale) susceptible d’être sauvegardée. Par exemple, une machine virtuelle Azure ou un partage de fichiers Azure.

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager)

(Terme propre aux charges de travail)

Reportez-vous à la [documentation sur DPM](/system-center/dpm/dpm-overview).

## <a name="expressroute"></a>ExpressRoute

Reportez-vous à la [documentation sur ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="file-system-consistent-backup"></a>Sauvegarde avec cohérence du système de fichiers

(Terme propre aux charges de travail)

Les sauvegardes cohérentes de système de fichiers assurent la cohérence en prenant une capture instantanée de tous les fichiers au même moment. [Plus d’informations](backup-azure-vms-introduction.md#snapshot-consistency)

## <a name="frontend-storage--source-size"></a>Stockage front-end / Taille de la source

Taille des données à sauvegarder pour une source de données. La taille front-end d’une source de données détermine son nombre d’[instances protégées](#protected-instance).

## <a name="full-backup"></a>Sauvegarde complète

Dans les sauvegardes complètes, une copie de la totalité de la source de données est stockée pour chaque sauvegarde.

## <a name="gfs-backup-policy"></a>Stratégie de sauvegarde GFS

Une stratégie de sauvegarde GFS (Grandfather-father-son, grand-père-père-fils) est une stratégie qui vous permet de définir des planifications de sauvegardes hebdomadaires, mensuelles et annuelles en plus de la planification de sauvegardes quotidiennes. Les sauvegardes hebdomadaires sont appelées « fils », les sauvegardes mensuelles « pères » et les sauvegardes annuelles « grand-pères ». Chacun de ces jeux de copies de sauvegarde peut être configuré pour être conservé pendant différentes durées, ce qui permet une meilleure personnalisation des choix de conservation des copies de sauvegarde. Les stratégies GFS sont utiles pour conserver des sauvegardes pendant une longue période de manière plus efficace en matière de stockage.

## <a name="iaas-vms--azure-vms"></a>Machines virtuelles IaaS / Machines virtuelles Azure

Reportez-vous à la [documentation sur les machines virtuelles Azure](../virtual-machines/index.yml).

## <a name="incremental-backup"></a>Sauvegarde incrémentielle

Les sauvegardes incrémentielles stockent uniquement les blocs qui ont changé depuis la sauvegarde précédente.

## <a name="instant-restore"></a>Restauration instantanée

(Terme propre aux charges de travail) La restauration instantanée implique la restauration d’une machine directement à partir de son instantané de sauvegarde plutôt qu’à partir de la copie de l’instantané dans le coffre. Les restaurations instantanées sont plus rapides que les restaurations à partir d’un coffre. Le nombre de points de restauration instantanée disponibles dépend de la durée de conservation configurée pour les captures instantanées. Actuellement applicable à la sauvegarde de machine virtuelle Azure uniquement.

## <a name="iops"></a>E/S par seconde

Opérations d’entrée/sortie par seconde.

## <a name="item-level-restore"></a>Restauration au niveau élément

(Terme propre aux charges de travail)

Restauration de fichiers ou dossiers individuels à l’intérieur de la machine à partir du point de récupération.

## <a name="job"></a>Travail

Tâche liée à la sauvegarde qui est créée par un utilisateur ou le service Sauvegarde Azure. Les travaux peuvent être planifiés ou effectués à la demande (ad-hoc). Il existe différents types de travaux : sauvegarde, restauration, configuration de la protection, etc. [Découvrez-en plus sur les travaux](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

## <a name="mabs--azure-backup-server"></a>MABS / Serveur de sauvegarde Azure

(Terme propre aux charges de travail)

Le serveur de sauvegarde Azure vous permet de protéger des charges de travail d’application telles que des machines virtuelles Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange et des clients Windows à partir d’une console unique. Il hérite d’une grande partie des fonctionnalités de sauvegarde de charge de travail de DPM, mais avec quelques différences. [En savoir plus](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>Disques managés

Reportez-vous à la [documentation sur les disques managés](../virtual-machines/managed-disks-overview.md).

## <a name="mars-agent"></a>Agent MARS

(Terme propre aux charges de travail)

Également appelé **agent de sauvegarde Azure** ou **agent Recovery Services**, l’agent MARS est utilisé par le service Sauvegarde Azure pour sauvegarder les données de machines locales et de machines virtuelles Azure dans un coffre Recovery Services de sauvegarde sur Azure. [Plus d’informations](backup-support-matrix-mars-agent.md)

## <a name="nsg-network-security-group"></a>NSG (groupe de sécurité réseau)

Reportez-vous à la [documentation sur les groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md).

## <a name="offline-seeding"></a>Amorçage hors connexion

L’amorçage hors connexion fait référence au processus de transfert de la sauvegarde initiale (complète) hors connexion, sans utilisation de la bande passante réseau. Ce service propose un mécanisme de copie des données de sauvegarde sur des appareils de stockage physiques, qui sont ensuite remis à un centre de données Azure situé à proximité, puis chargés dans un coffre Recovery Services. [Plus d’informations](offline-backup-overview.md)

## <a name="on-demand-backup--ad-hoc-backup"></a>Sauvegarde à la demande / Sauvegarde ad hoc

Travail de sauvegarde qui est déclenché ponctuellement par un utilisateur, et non basée sur la planification de sauvegarde (stratégie) qui a été configurée pour la ressource.

## <a name="original-location-recovery-olr"></a>Récupération à l’emplacement d’origine (OLR)

Récupération opérée à partir du point de restauration vers l’emplacement source à partir de laquelle les sauvegardes ont été effectuées, en la remplaçant par l’état stocké dans le point de récupération. Lors de l’utilisation d’une sauvegarde de machine virtuelle Azure, l’opération consiste à restaurer la machine virtuelle sur le serveur d’origine où les sauvegardes ont été effectuées. Lors de l’utilisation d’une sauvegarde de partage de fichiers Azure, l’opération consiste à restaurer les données sur le partage de fichiers sauvegardé.

## <a name="passphrase"></a>Passphrase

(Terme propre aux charges de travail)

Une phrase secrète est utilisée pour chiffrer et déchiffrer les données lors de la sauvegarde ou de la restauration de votre ordinateur local à l’aide de l’agent MARS sur ou à partir d’Azure.

## <a name="private-endpoint"></a>Point de terminaison privé

Reportez-vous à la [documentation sur les points de terminaison privé](../private-link/private-endpoint-overview.md).

## <a name="protected-instance"></a>Instance protégée

Une instance protégée fait référence à l’ordinateur ou au serveur physique ou virtuel que vous utilisez pour configurer la sauvegarde sur Azure.  Du **point de vue de la facturation**, le nombre d’instances protégées pour une machine dépend de sa taille front-end. Ainsi, une seule instance de sauvegarde (telle qu’une machine virtuelle sauvegardée dans Azure) peut correspondre à plusieurs instances protégées, en fonction de sa taille front-end. [Plus d’informations](https://azure.microsoft.com/pricing/details/backup/)

## <a name="rbac-role-based-access-control"></a>RBAC (Contrôle d’accès en fonction du rôle)

Reportez-vous à la [documentation sur le contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md).

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>Point de récupération / Point de restauration / Point de rétention / Instant dans le passé

Copie des données d’origine en cours de sauvegarde. Un point de conservation étant associé à un horodatage, vous pouvez l’utiliser pour restaurer un élément à un point spécifique dans le temps.

## <a name="recovery-services-vault"></a>Coffre Recovery Services

Ressource Azure Resource Manager de type *Microsoft.RecoveryServices/vaults*. Les coffres Recovery Services sont utilisés pour sauvegarder les charges de travail suivantes : Machines virtuelles Azure, SQL dans des machines virtuelles Azure, SAP HANA dans des machines virtuelles Azure et partages de fichiers Azure. Ils servent également à sauvegarder des charges de travail locales à l’aide de MARS, du serveur de sauvegarde Azure (MABS) et de System Center DPM. [Découvrez-en plus sur les coffres Recovery Services](backup-azure-recovery-services-vault-overview.md).

## <a name="resource-group"></a>Resource group

Reportez-vous à la [documentation sur Azure Resource Manager](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

## <a name="rest-api"></a>API REST

Reportez-vous à la [documentation sur l’API REST](/rest/api/azure/).

## <a name="retention-rule"></a>Règle de conservation

Règle définie par l’utilisateur qui spécifie la durée pendant laquelle les sauvegardes doivent être conservées.

## <a name="rpo-recovery-point-objective"></a>RPO (objectif de point de récupération)

Le RPO indique la perte de données maximale possible dans un scénario de perte de données. Il est déterminé par la fréquence de sauvegarde.

## <a name="rto-recovery-time-objective"></a>RTO (objectif de délai de récupération)

Le RTO indique le délai possible maximal pendant lequel les données peuvent être restaurées au dernier instant dans le passé disponible après un scénario de perte de données.

## <a name="scheduled-backup"></a>Sauvegarde planifiée

Travail de sauvegarde qui est automatiquement déclenché par la stratégie de sauvegarde configurée pour l’élément donné.

## <a name="secondary-region--paired-region"></a>Région secondaire / Région jumelée

Une paire régionale est constituée de deux régions au sein de la même zone géographique. L’une est la région primaire, l’autre la région secondaire. Les régions jumelées sont utilisées par certains services Azure (y compris le service Sauvegarde Azure avec les paramètres GRS) pour assurer la continuité des activités et la protection contre la perte de données. [Plus d’informations](../best-practices-availability-paired-regions.md)

## <a name="soft-delete"></a>Suppression réversible

La suppression réversible est une fonctionnalité qui contribue à vous protéger contre la suppression accidentelle de données de sauvegarde. Avec la suppression réversible, même si un intervenant malveillant supprime une sauvegarde (ou même si les données de sauvegarde sont accidentellement supprimées), les données de sauvegarde sont conservées pendant une période supplémentaire, ce qui permet la récupération de cet élément de sauvegarde sans perte de données. [Plus d’informations](backup-azure-security-feature-cloud.md)

## <a name="snapshot"></a>Instantané

Une capture instantanée est une copie complète en lecture seule d’un disque dur virtuel (VHD) ou d’un partage de fichiers Azure. En savoir plus sur les [captures instantanées de disque](../virtual-machines/windows/snapshot-copy-managed-disk.md) et les [captures instantanées de fichiers](../storage/files/storage-snapshots-files.md).

## <a name="storage-account"></a>Compte de stockage

Reportez-vous à la [documentation sur les comptes de stockage](../storage/common/storage-account-overview.md).

## <a name="subscription"></a>Abonnement

Un abonnement Azure est un conteneur logique utilisé pour provisionner des ressources dans Azure. Il contient les détails de toutes vos ressources, telles que les machines virtuelles ou les bases de données.

## <a name="system-state-backup"></a>Sauvegarde de l’état du système

(Terme propre aux charges de travail)

Sauvegarde les fichiers du système d’exploitation. Cette sauvegarde vous permet d’effectuer une récupération au démarrage de l’ordinateur, mais les fichiers système et le Registre sont perdus. [Plus d’informations](backup-mabs-system-state-and-bmr.md)

## <a name="tenant"></a>Locataire

Le locataire est la représentation d’une organisation. Il s’agit d’une instance dédiée d’Azure AD, reçue par une organisation ou un développeur d’applications lors de la création d’une relation avec Microsoft, comme une inscription à Azure, Microsoft Intune ou Microsoft 365.

## <a name="unmanaged-disk"></a>Disque non managé

Reportez-vous à la [documentation sur les disques non managés](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks).

## <a name="vault"></a>Coffre

Entité de stockage dans Azure qui héberge des données de sauvegarde. Il s’agit également d’une unité de facturation et du contrôle d’accès en fonction du rôle. Il existe deux types de coffres : le coffre Recovery Services et le coffre de sauvegarde.

## <a name="vault-credentials"></a>Informations d’identification du coffre

Le fichier d’informations d’identification de coffre est un certificat qui est généré par le portail pour chaque coffre. Il est utilisé lors de l’inscription d’un serveur local dans le coffre. [Plus d’informations](backup-azure-dpm-introduction.md)

## <a name="vnet-virtual-network"></a>VNet (Réseau virtuel)

Reportez-vous à la documentation sur les [réseaux virtuels](../virtual-network/virtual-networks-overview.md).

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Service Windows VSS)

Reportez-vous à la [documentation sur VSS](/windows-server/storage/file-server/volume-shadow-copy-service).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du service Sauvegarde Azure](backup-overview.md)
- [Architecture et composants d’Azure Backup](backup-architecture.md)