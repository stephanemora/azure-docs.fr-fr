---
title: Nouveautés du serveur de sauvegarde Microsoft Azure
description: Le serveur de sauvegarde Microsoft Azure offre des capacités de sauvegarde améliorées pour protéger les machines virtuelles, les fichiers et dossiers, les charges de travail et plus encore.
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: 8f7c0e8709cd899b9b6a285dcd8c556763869c65
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524024"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>Nouveautés du serveur de sauvegarde Microsoft Azure (MABS)

## <a name="whats-new-in-mabs-v3-ur2"></a>Nouveautés de MABS v3 UR2

Le serveur de sauvegarde Microsoft Azure (MABS) version 3 UR2 prend en charge les nouvelles fonctionnalités et mises à jour de fonctionnalités suivantes.

Pour plus d’informations sur les correctifs des problèmes de UR2 et les instructions d’installation, consultez l’[article de la base de connaissances](https://support.microsoft.com/topic/update-rollup-2-for-microsoft-azure-backup-server-v3-350de164-0ae4-459a-8acf-7777dbb7fd73).

### <a name="support-for-azure-stack-hci"></a>Support pour Azure Stack HCI

Avec MABS v3 UR2, vous pouvez sauvegarder des machines virtuelles sur Azure Stack HCI. [Plus d’informations](./back-up-azure-stack-hyperconverged-infrastructure-virtual-machines.md)

### <a name="support-for-vmware-70"></a>Support pour VMware 7.0

Avec MABS v3 UR2, vous pouvez sauvegarder des machines virtuelles VMware 7.0. [Plus d’informations](./backup-azure-backup-server-vmware.md)

### <a name="support-for-sql-server-failover-cluster-instance-fci-using-cluster-shared-volume-csv"></a>Prise en charge d’instance de cluster de basculement (FCI) SQL Server à l’aide de volume partagé de cluster (CSV)

MABS v3 UR2 prend en charge une instance de cluster de basculement (FCI) SQL Server à l’aide du volume partagé de cluster (CSV). Avec CSV, la gestion de votre instance de SQL Server est simplifiée. Cela vous aide à gérer le stockage sous-jacent à partir de n’importe quel nœud, car il existe une abstraction concernant le nœud propriétaire du disque. [Plus d’informations](./backup-azure-sql-mabs.md)

### <a name="optimized-volume-migration"></a>Migration optimisée de volume

MABS v3 UR2 prend en charge la migration de volume optimisée. La migration optimisée de volume vous permet de déplacer des sources de données vers le nouveau volume beaucoup plus rapidement. Le processus de migration amélioré migre uniquement la copie de sauvegarde active (réplica actif) vers le nouveau volume. Tous les nouveaux points de récupération sont créés sur le nouveau volume, tandis que les points de récupération existants sont conservés sur le volume existant et sont purgés conformément à la stratégie de rétention. [Plus d’informations](/system-center/dpm/volume-to-volume-migration?view=sc-dpm-2019&preserve-view=true)

### <a name="offline-backup-using-azure-data-box"></a>Sauvegarde hors connexion avec Azure Data Box

MABS v3 UR2 prend en charge la sauvegarde en mode hors connexion à l’aide d’Azure Data Box. Avec l’intégration de Microsoft Azure Data Box, vous pouvez surmonter le défi que constitue le déplacement de plusieurs téraoctets de données de sauvegarde de l’environnement local vers le stockage Azure. Azure Data Box vous évite de devoir fournir vos propres disques et connecteurs compatibles avec Azure, ou de devoir provisionner du stockage temporaire en tant qu’emplacement intermédiaire. Microsoft gère également la logistique du transfert de bout en bout, que vous pouvez suivre par le biais du Portail Azure. [Plus d’informations](./offline-backup-azure-data-box-dpm-mabs.md)

## <a name="whats-new-in-mabs-v3-ur1"></a>Nouveautés de MABS v3 UR1

Le serveur de sauvegarde Microsoft Azure (MABS) version 3 UR1 est la dernière mise à niveau en date et comporte des correctifs de bogues critiques et d’autres fonctionnalités et améliorations. Pour connaître la liste des bogues corrigés et les instructions d’installation de MABS v3 UR1, consultez l’article de la base de connaissances [4534062](https://support.microsoft.com/help/4534062).

>[!NOTE]
>La prise en charge de l’agent de protection 32 bits est déconseillée avec MABS v3 UR1. Consultez [Dépréciation de l’agent de protection 32 bits](#32-bit-protection-agent-deprecation).

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>Sauvegardes plus rapides avec stockage hiérarchisé à l’aide de disques SSD

MABS v2 a introduit le [stockage de sauvegarde moderne](backup-mabs-add-storage.md) (MBS), améliorant ainsi l’utilisation et les performances du stockage. MBS utilise ReFS comme système de fichiers sous-jacent et est conçu pour utiliser le stockage hybride, tel que le stockage hiérarchisé.

Pour atteindre l’échelle et les performances du MBS, nous vous recommandons d’utiliser un petit pourcentage (4 % du stockage global) de l’espace de stockage flash (SSD) avec MABS v3 UR1 comme volume hiérarchisé en association avec le stockage HDD DPM. MABS v3 UR1 avec stockage hiérarchisé offre des sauvegardes 50 à 70 % plus rapides. Découvrez comment configurer un stockage hiérarchisé dans la section [Configurer MBS avec un stockage hiérarchisé](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage) de l’article DPM.

### <a name="support-for-refs-volumes"></a>Prise en charge des volumes ReFS

Avec MABS v3 UR1, vous pouvez sauvegarder les volumes ReFS et les charges de travail déployées sur le volume ReFS. Vous pouvez sauvegarder les charges de travail suivantes déployées sur les volumes ReFS :

* Système d’exploitation (64 bits) : Windows Server 2019, 2016, 2012 R2, 2012.
* SQL Server : SQL Server 2019, SQL Server 2017, 2016.
* Exchange : Exchange 2019, 2016.
* SharePoint : SharePoint 2019, 2016 avec le dernier Service Pack.

>[!NOTE]
> Sauvegarde des machines virtuelles Hyper-V stockées sur un volume ReFS prise en charge avec MABS v3

>[IMPORTANT] Nous avons identifié quelques problèmes de sauvegarde avec les volumes ReFS dédupliqués. Nous cherchons à résoudre ce problème et mettrons à jour cette section dès que nous aurons trouvé une solution. En attendant, nous supprimons le support de la sauvegarde des volumes ReFS dédupliqués à partir de MABSv3 UR1.

### <a name="azure-vmware-solution-protection-support"></a>Prise en charge de la protection d’Azure VMware Solution

Avec MABS v3 UR1, vous pouvez désormais protéger les machines virtuelles déployées dans [Azure VMware Solution](../azure-vmware/index.yml).

### <a name="vmware-parallel-backups"></a>Sauvegardes parallèles VMware

Avec MABS v3 UR1, toutes les sauvegardes des machines virtuelles VMware appartenant à un même groupe de protection sont effectuées en parallèle, ce qui permet d’accélérer de 25 % le processus de sauvegarde.
Dans les versions antérieures de MABS, les sauvegardes parallèles n’étaient effectuées que sur les groupes de protection. Avec MABS v3 UR1, les travaux de réplication delta de VMware s’exécutent en parallèle. Par défaut, le nombre de travaux qui s’exécutent en parallèle est configuré sur 8. En savoir plus sur [les sauvegardes parallèles VMware](backup-azure-backup-server-vmware.md#vmware-parallel-backups).

### <a name="disk-exclusion-for-vmware-vm-backup"></a>Exclusion de disque pour la sauvegarde de machines virtuelles VMware

Avec MABS v3 UR1, vous pouvez exclure des disques spécifiques d’une sauvegarde de machine virtuelle VMware. En savoir plus sur [l’exclusion de disques de la sauvegarde de machine virtuelle VMware](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup).  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>Prise en charge d’une couche supplémentaire d’authentification pour supprimer la sauvegarde en ligne

Avec MABS v3 UR1, une couche supplémentaire d’authentification est ajoutée pour les opérations critiques. Vous êtes invité à entrer un code PIN de sécurité lorsque vous effectuez des opérations **Arrêter la protection avec données supprimées**.

### <a name="offline-backup-improvements"></a>Améliorations de la sauvegarde hors connexion

MABS v3 UR1 améliore l’expérience de la sauvegarde hors connexion avec le service Import/Export d’Azure. Vous trouverez plus d’informations sur les étapes mises à jour [ici](./backup-azure-backup-server-import-export.md).

>[!NOTE]
>À partir de MABS v3 UR2, MABS peut effectuer une sauvegarde en mode hors connexion à l’aide de Azure Data Box. [Plus d’informations](./offline-backup-azure-data-box-dpm-mabs.md)

### <a name="new-cmdlet-parameter"></a>Nouveau paramètre de cmdlet

MABS v3 UR1 comprend un nouveau paramètre **[-CheckReplicaFragmentation]** . Le nouveau paramètre calcule le pourcentage de fragmentation pour un réplica et est inclus dans la cmdlet **Copy-DPMDatasourceReplica**.

### <a name="32-bit-protection-agent-deprecation"></a>Dépréciation de l’agent de protection 32 bits

Avec MABS v3 UR1, l’agent de protection 32 bits n’est plus pris en charge. Vous ne pourrez pas protéger les charges de travail 32 bits après la mise à niveau du serveur MABS v3 vers UR1. Les agents de protection 32 bits existants seront dans un état désactivé et les sauvegardes planifiées échoueront avec l’erreur **Agent désactivé**. Si vous souhaitez conserver les données de sauvegarde pour ces agents, vous pouvez arrêter la protection avec l’option de conservation des données. Dans le cas contraire, l’agent de protection peut être supprimé.

>[!NOTE]
>Examinez la [matrice de protection mise à jour](./backup-mabs-protection-matrix.md) pour connaître les charges de travail prises en charge pour la protection avec MABS UR1.

## <a name="whats-new-in-mabs-v3-rtm"></a>Nouveautés de MABS v3 RTM

Le serveur de sauvegarde Microsoft Azure version 3 (MABS v3) comporte des correctifs de bogues critiques, la prise en charge de Windows Server 2019, la prise en charge de SQL 2017 et d’autres fonctionnalités et améliorations. Pour connaître la liste des bogues corrigés et les instructions d’installation de MABS v3, voir l’article de la Base de connaissances [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

Les fonctionnalités suivantes sont incluses dans MABS v3 :

### <a name="volume-to-volume-migration"></a>Migration de volume à volume

Avec le stockage de sauvegarde moderne (MBS) de MABS v2, nous avons annoncé la mise en place du stockage adapté à la charge de travail : avec cette fonctionnalité, vous configurez certaines charges de travail à sauvegarder dans un stockage spécifique, selon les propriétés de stockage. Toutefois, après la configuration, vous pourriez avoir besoin de déplacer des sauvegardes de certaines sources de données vers un autre stockage pour optimiser l’utilisation des ressources. MABS v3 offre la possibilité de migrer des sauvegardes et de les configurer de façon à les stocker sur un autre volume en [trois étapes](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

### <a name="prevent-unexpected-data-loss"></a>Empêcher les pertes de données imprévues

Dans les entreprises, MABS est géré par une équipe d’administrateurs. Malgré les instructions sur le stockage à utiliser pour les sauvegardes, un mauvais choix de volume comme stockage de sauvegarde pour MABS peut entraîner une perte de données critiques. Avec MABS v3, vous pouvez éviter ce type de situation en configurant ces volumes comme non disponibles pour le stockage à l’aide de [ces cmdlets PowerShell](./backup-mabs-add-storage.md).

### <a name="custom-size-allocation"></a>Allocation de taille personnalisée

Le stockage de sauvegarde moderne (MBS) consomme peu de stockage, selon les besoins. Pour cela, MABS calcule la taille des données à sauvegarder lorsqu’il est configuré pour la protection. Toutefois, si cela représente de nombreux fichiers et dossiers, comme dans le cas d’un serveur de fichiers, le calcul de la taille risque de prendre beaucoup de temps. Avec MABS v3, vous pouvez configurer MABS de façon à ce que, au lieu de calculer la taille de chaque fichier, il accepte la taille du volume comme valeur par défaut, ce qui fait gagner du temps.

### <a name="optimized-cc-for-rct-vms"></a>CC optimisé pour les machines virtuelles RCT

MABS utilise la fonctionnalité RCT (suivi des modifications natif d’Hyper-V), qui limite le nombre de longues vérifications de cohérence nécessaires, dans des scénarios comme des incidents sur une machine virtuelle. RCT offre une meilleure résilience que le suivi des modifications proposé par les sauvegardes sur instantané VSS. MABS v3 optimise encore davantage la consommation de réseau et de stockage en transférant seulement les données modifiées pendant les vérifications de cohérence.

### <a name="support-to-tls-12"></a>Prise en charge de TLS 1.2

TLS 1.2 est le moyen de communication sécurisé proposé par Microsoft avec le meilleur chiffrement qui soit. MABS prend maintenant en charge la communication TLS 1.2 entre MABS et les serveurs protégés, pour l’authentification par certificat et les sauvegardes cloud.

### <a name="vmware-vm-protection-support"></a>Prise en charge de la protection des machines virtuelles VMware

La sauvegarde de machines virtuelles VMware est maintenant prise en charge pour les déploiements en production. MABS v3 offre les fonctionnalités suivantes pour la protection des machines virtuelles VMware :

* Prise en charge de vCenter et d’ESXi 6.5, ainsi que de 5.5 et 6.0.
* Protection automatique des machines virtuelles VMware dans le cloud. Les machines virtuelles VMware ajoutées à un dossier protégé sont automatiquement protégées sur disque et dans le cloud.
* Amélioration de l’efficacité de la récupération à un autre emplacement VMware.

### <a name="sql-2017-support"></a>Prise en charge de SQL 2017

MABS v3 peut être installé avec SQL 2017 comme base de données MABS. Vous pouvez mettre à niveau le serveur SQL de SQL 2016 à SQL 2017, ou tout simplement l’installer. Il est également possible de sauvegarder la charge de travail SQL 2017 à la fois dans un environnement en cluster et dans un environnement non cluster avec MABS v3.

### <a name="windows-server-2019-support"></a>Prise en charge de Windows Server 2019

MABS v3 peut être installé sur Windows Server 2019. Pour utiliser MABS v3 avec Windows Server 2019, vous pouvez mettre à niveau votre système d’exploitation vers Windows Server 2019 soit avant l’installation/la mise à niveau de MABS v3, soit après l’installation/la mise à niveau de MABS v3 sur Windows Server 2016.

MABS v3 est une version complète qui peut faire l’objet d’une installation directe sur Windows Server 2016 ou Windows Server 2019, ou d’une mise à niveau à partir de MABS v2. Avant de mettre à niveau ou d’installer le serveur de sauvegarde v3, lisez les prérequis d’installation.
Pour plus d’informations sur les étapes d’installation/de mise à niveau de MABS, cliquez [ici](./backup-azure-microsoft-azure-backup.md#software-package).

> [!NOTE]
>
> MABS présente le même codebase que System Center Data Protection Manager. MABS v3 est équivalent à Data Protection Manager 1807. MABS v3 UR1 est équivalent à Data Protection Manager 2019 UR1.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment préparer votre serveur ou commencer à protéger une charge de travail :

* [Préparer les charges de travail du serveur de sauvegarde](backup-azure-microsoft-azure-backup.md)
* [Utiliser le Serveur de sauvegarde pour sauvegarder un serveur VMware](backup-azure-backup-server-vmware.md)
* [Utiliser le serveur de sauvegarde pour sauvegarder SQL Server](backup-azure-sql-mabs.md)
* [Utiliser Modern Backup Storage avec le serveur de sauvegarde](backup-mabs-add-storage.md)