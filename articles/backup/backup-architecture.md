---
title: Architecture de Sauvegarde Azure
description: Fournit une vue d’ensemble de l’architecture, des composants et des processus utilisés par le service Sauvegarde Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 98ffe145103b4be04014627ed04d04dcf7542015
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368950"
---
# <a name="azure-backup-architecture"></a>Architecture de Sauvegarde Azure

Vous pouvez utiliser la [service Azure Backup](backup-overview.md) pour sauvegarder des données à la plateforme de cloud Microsoft Azure. Cet article résume l’architecture, les composants et les processus de Sauvegarde Azure. 

## <a name="what-does-azure-backup-do"></a>Que fait Sauvegarde Azure ?

Azure Backup sauvegarde les données, état de la machine et les charges de travail en cours d’exécution sur des ordinateurs locaux et les instances de machine virtuelle (VM). Il existe plusieurs scénarios de Sauvegarde Azure.

## <a name="how-does-azure-backup-work"></a>Comment fonctionne Sauvegarde Azure ?

Vous pouvez sauvegarder les ordinateurs et les données à l’aide de plusieurs méthodes :

- **Sauvegarder des ordinateurs locaux** :
    - Vous pouvez sauvegarder les ordinateurs Windows en local directement sur Azure à l’aide de l’agent Azure sauvegarde Microsoft Azure Recovery Services (MARS). Les machines Linux ne sont pas prises en charge.
    - Vous pouvez sauvegarder des machines locales vers un serveur de sauvegarde (System Center Data Protection Manager (DPM) ou Microsoft Azure sauvegarde serveur (MABS)). Vous pouvez ensuite sauvegarder le serveur de sauvegarde dans un coffre Recovery Services dans Azure.

- **Sauvegarder des machines virtuelles Azure** :
    - vous pouvez directement sauvegarder des machines virtuelles Azure. Sauvegarde Azure installe une extension de sauvegarde à l’agent de machine virtuelle Azure qui s’exécute sur la machine virtuelle. Cette extension sauvegarde la totalité de la machine virtuelle.
    - Vous pouvez sauvegarder des fichiers et dossiers spécifiques sur la machine virtuelle Azure en exécutant l’agent MARS.
    - Vous pouvez sauvegarder des machines virtuelles Azure pour le serveur de sauvegarde AZURE qui s’exécute dans Azure, et vous pouvez ensuite sauvegarder le serveur de sauvegarde AZURE dans un coffre Recovery Services.

En savoir plus sur [ce que vous pouvez sauvegarder](backup-overview.md) et environ [pris en charge les scénarios de sauvegarde](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Où les données sont-elles sauvegardées ?

Sauvegarde Azure stocke les données sauvegardées dans un coffre Recovery Services. Un coffre est une entité de stockage en ligne dans Azure qui permet de conserver les données, telles que des copies de sauvegarde, les points de récupération et les stratégies de sauvegarde.

Les coffres Recovery Services possèdent les fonctionnalités suivantes :

- Les coffres facilitent l’organisation de vos données de sauvegarde, tout en réduisant le temps de gestion.
- Dans chaque abonnement Azure, vous pouvez créer jusqu’à 500 coffres.
- Vous pouvez surveiller des éléments de sauvegarde dans un coffre, y compris les ordinateurs de machines virtuelles Azure et locales.
- Vous pouvez gérer l’accès au coffre avec le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) Azure.
- Vous spécifiez le mode de réplication des données dans le coffre pour la redondance :
    - **Stockage localement redondant (LRS)**  : Pour vous protéger contre les défaillances dans un centre de données, vous pouvez utiliser LRS. LRS réplique les données vers une unité d’échelle de stockage. [Plus d’informations](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
    - **Stockage géo-redondant (GRS)**  : Pour vous protéger contre les pannes au niveau régional, vous pouvez utiliser GRS. GRS réplique vos données dans une région secondaire. [Plus d’informations](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) 
    - Par défaut, les coffres Recovery Services utilisent GRS. 

## <a name="backup-agents"></a>Agents de sauvegarde

Sauvegarde Azure fournit différents agents de sauvegarde, selon le type d’ordinateur est en cours de sauvegarde :

**Agent** | **Détails** 
--- | --- 
**Agent MARS** | <ul><li>S’exécute sur les ordinateurs individuels en local de Windows Server pour sauvegarder des fichiers, dossiers et l’état du système.</li> <li>S’exécute sur les machines virtuelles Azure pour sauvegarder des fichiers, dossiers et l’état du système.</li> <li>S’exécute sur les serveurs DPM/de sauvegarde AZURE pour sauvegarder le disque de stockage local de DPM/de sauvegarde AZURE vers Azure.</li></ul> 
**Extension de machine virtuelle Azure** | S’exécute sur des machines virtuelles Azure pour les sauvegarder dans un coffre.

## <a name="backup-types"></a>Types de sauvegarde

Le tableau suivant décrit les différents types de sauvegardes et lorsqu’ils sont utilisés :

**Type de sauvegarde** | **Détails** | **Utilisation**
--- | --- | ---
**Complète** | Une sauvegarde complète contient la source de données entière. Prend davantage de bande passante réseau que les sauvegardes différentielles ou incrémentielles. | Utilisée pour la sauvegarde initiale.
**Différentielle** |  Une sauvegarde différentielle stocke les blocs modifiés depuis la sauvegarde complète initiale. Utilise une plus petite quantité de stockage et de réseau et ne conserve pas des copies redondantes des données inchangées.<br/><br/> Inefficace, car les blocs de données qui sont inchangés entre les sauvegardes ultérieures sont transférées et stockées. | Non utilisée par Sauvegarde Azure.
**Incrémentielle** | Une sauvegarde incrémentielle stocke uniquement les blocs de données modifiées depuis la sauvegarde précédente. Efficacité élevée du point de vue du stockage et du réseau. <br/><br/> Avec une sauvegarde incrémentielle, il est inutile de compléter avec les sauvegardes complètes. | Utilisée par DPM/MABS pour les sauvegardes sur disque, et utilisée dans toutes les sauvegardes vers Azure.

## <a name="sql-server-backup-types"></a>Types de sauvegarde SQL Server

Le tableau suivant décrit les différents types de sauvegardes utilisées pour les bases de données SQL Server et la fréquence à laquelle ils sont utilisés :

**Type de sauvegarde** | **Détails** | **Utilisation**
--- | --- | ---
**Sauvegarde complète** | une sauvegarde complète de base de données sauvegarde l’intégralité de la base de données. Il contient toutes les données dans une base de données spécifique ou dans un ensemble de groupes de fichiers ou de fichiers. Une sauvegarde complète contient également suffisamment de journaux pour récupérer ces données. | Vous pouvez déclencher au plus une sauvegarde complète par jour.<br/><br/> Vous pouvez choisir effectuer une sauvegarde complète sur un intervalle quotidien ou hebdomadaire.
**Sauvegarde différentielle** | Une sauvegarde différentielle est basée sur le plus récent, précédente sauvegarde complète des données.<br/><br/> Elle capture uniquement les données qui ont changé depuis la sauvegarde complète. |  Vous pouvez déclencher au plus une sauvegarde différentielle par jour.<br/><br/> Vous ne pouvez pas configurer une sauvegarde complète et une sauvegarde différentielle le même jour.
**Sauvegarde de fichier journal** | une sauvegarde de fichier journal permet d’effectuer une restauration ponctuelle à la seconde donnée. | Au plus, vous pouvez configurer des sauvegardes du journal des transactions toutes les 15 minutes.

### <a name="comparison-of-backup-types"></a>Comparaison des types de sauvegarde

La consommation du stockage, l’objectif de délai de récupération (RTO) et la consommation réseau varient pour chaque type de sauvegarde. L’illustration suivante montre une comparaison des types de sauvegarde :

- Source de données A est composé de 10 blocs de stockage, A1-A10, qui sont sauvegardés tous les mois.
- Les blocs A2, A3, A4 et A9 ont changé lors du premier mois et le bloc A5 a changé lors du mois suivant.
- Pour les sauvegardes différentielles, lors du deuxième mois, les blocs A2, A3, A4 et A9 qui ont changé sont sauvegardés. Lors du troisième mois, ces mêmes blocs sont à nouveau sauvegardés, ainsi que le bloc A5 qui a changé. Les blocs modifiés continuent d’être sauvegardés jusqu’à la prochaine sauvegarde complète.
- Pour les sauvegardes incrémentielles, dans le deuxième mois, blocs A2, A3, A4 et A9 sont marqués comme modifiés et transférés. Lors du troisième mois, seul le bloc A5 qui a changé est marqué et transféré. 

![Image montrant la comparaison des méthodes de sauvegarde](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Fonctionnalités de sauvegarde

Le tableau suivant récapitule les fonctionnalités prises en charge pour les différents types de sauvegarde :

**Fonctionnalité** | **Ordinateurs Windows Server en local (direct)** | **Machines virtuelles Azure** | **Ordinateurs ou applications avec DPM/de sauvegarde AZURE**
--- | --- | --- | ---
Sauvegarder vers le coffre | ![Oui][green] | ![OUI][green] | ![Oui][green] 
Sauvegarder sur disque DPM/de sauvegarde AZURE, puis vers Azure | | | ![Oui][green] 
Compresser les données envoyées pour la sauvegarde | ![Oui][green] | Aucune compression n’est effectuée lors du transfert des données. Le stockage croît légèrement, mais la restauration est plus rapide.  | ![Oui][green] 
Exécuter une sauvegarde incrémentielle |![Oui][green] |![OUI][green] |![Oui][green] 
Sauvegarder les disques dédupliqués | | | ![Partiellement][yellow]<br/><br/> Uniquement pour les serveurs DPM/MABS déployés localement. 

![Clé de table](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>Architecture : sauvegarde directe de machines virtuelles Azure

1. Lorsque vous activez la sauvegarde pour une machine virtuelle Azure, une sauvegarde s’exécute selon la planification que vous spécifiez.
1. Lors de la première sauvegarde, une extension de sauvegarde est installée sur la machine virtuelle si la machine virtuelle est en cours d’exécution.
    - Pour les machines virtuelles Windows, l’extension VMSnapshot est installé.
    - Pour les machines virtuelles Linux, l’extension VMSnapshot Linux est installée.
1. L’extension prend un instantané au niveau du stockage. 
    - Pour les machines virtuelles Windows sont en cours d’exécution, sauvegarde coordonne avec le Windows VSS Volume Shadow Copy Service () pour prendre un instantané cohérent de l’application de la machine virtuelle. Par défaut, la sauvegarde a des sauvegardes complètes de VSS. Si Sauvegarde Azure ne peut pas prendre d’instantané cohérent au niveau de l’application, il prend un instantané cohérent au niveau fichier.
    - Pour les machines virtuelles Linux, sauvegarde prend un instantané cohérent de fichier. Pour les instantanés cohérents d’application, vous devez personnaliser manuellement les scripts de pré/post.
    - La sauvegarde est optimisée grâce à la sauvegarde de chaque disque de machine virtuelle en parallèle. Pour chaque disque en cours de sauvegarde, Sauvegarde Azure lit les blocs sur le disque et stocke uniquement les données modifiées. 
1. Une fois l’instantané pris, les données sont transférées vers le coffre. 
    - Seuls les blocs de données modifiées depuis la dernière sauvegarde sont copiées.
    - Les données ne sont pas chiffrées. Sauvegarde Azure peut sauvegarder des machines virtuelles Azure qui ont été chiffrées à l’aide d’Azure Disk Encryption.
    - Les données d’instantanés peuvent ne pas être immédiatement copiées dans le coffre. Aux heures de pointe, la sauvegarde peut prendre quelques heures. Durée de sauvegarde totale pour une machine virtuelle sera inférieure à 24 heures pour les stratégies de sauvegarde quotidiennes.
1. Une fois que les données sont envoyées dans le coffre, l’instantané est supprimé et un point de récupération est créé.

Machines virtuelles Azure doivent accéder à internet pour les commandes de contrôle. Si vous sauvegardez des charges de travail à l’intérieur de la machine virtuelle (par exemple, les sauvegardes de base de données de SQL Server), les données back-end doivent également accès à internet. 

![Sauvegarde des machines virtuelles Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architecture : Sauvegarde directe des ordinateurs de Windows Server en local ou des fichiers de machine virtuelle Azure ou des dossiers

1. Pour configurer le scénario, vous téléchargez et installez l’agent MARS sur l’ordinateur. Puis, vous sélectionnez les éléments à sauvegarder, lorsque les sauvegardes seront exécute et la durée pendant laquelle ils amèneront rester dans Azure.
1. La sauvegarde initiale s’exécute en fonction de vos paramètres de sauvegarde.
1. L’agent MARS utilise VSS pour prendre un instantané de point-à-temps des volumes sélectionnés pour la sauvegarde.
    - L’agent MARS utilise uniquement l’opération d’écriture de système Windows pour capturer l’instantané.
    - Étant donné que l’agent n’utilise pas les enregistreurs VSS d’application, il ne capture pas instantanés cohérents au niveau de l’application.
1. Après la capture instantanée avec VSS, l’agent MARS crée un disque dur virtuel (VHD) dans le dossier du cache que vous avez spécifié lorsque vous avez configuré la sauvegarde. L’agent stocke également les sommes de contrôle pour chaque bloc de données.
1. Exécution des sauvegardes incrémentielles conformément à la planification que vous spécifiez, sauf si vous exécutez une sauvegarde ad hoc.
1. Dans les sauvegardes incrémentielles, les fichiers modifiés sont identifiés et un nouveau disque dur virtuel est créé. Le disque dur virtuel est compressé et chiffré, et il est envoyé dans le coffre.
1. Une fois la sauvegarde incrémentielle est terminée, le nouveau disque dur virtuel est fusionné avec le disque dur virtuel créé après la réplication initiale. Ce disque dur virtuel fusionné fournit le dernier état à utiliser pour la comparaison pour la sauvegarde en cours.

![Sauvegarde d’ordinateurs de Windows Server sur site avec l’agent MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architecture : sauvegarder dans DPM/MABS

1. Vous installez l’agent de protection DPM ou MABS sur les ordinateurs que vous souhaitez protéger. Vous ajoutez ensuite les machines à un groupe de protection DPM.
    - Pour protéger des ordinateurs locaux, le serveur DPM ou MABS doit être local.
    - Pour protéger des machines virtuelles Azure, le serveur MABS doit se trouver dans Azure et être exécuté en tant que machine virtuelle Azure.
    - Avec DPM/de sauvegarde AZURE, vous pouvez protéger des dossiers, les partages, les fichiers et les volumes de sauvegarde. Vous pouvez également protéger état du système d’un ordinateur (système nu), et vous pouvez protéger des applications spécifiques avec les paramètres de sauvegarde prenant en charge les applications.
1. Lorsque vous configurez la protection d’une machine ou d’une application dans DPM/de sauvegarde AZURE, vous sélectionnez pour sauvegarder sur le disque local du serveur de sauvegarde AZURE/DPM pour le stockage à court terme et sur Azure pour la protection en ligne. Vous spécifiez également quand la sauvegarde dans le stockage local de DPM/de sauvegarde AZURE doit s’exécuter et quand la sauvegarde en ligne sur Azure doit s’exécuter.
1. Le disque de la charge de travail protégé est sauvegardé sur les disques du serveur de sauvegarde AZURE/DPM locales, conformément à la planification que vous avez spécifié.
4. Les disques DPM/de sauvegarde AZURE sont sauvegardés dans le coffre par l’agent MARS qui s’exécute sur le serveur DPM/de sauvegarde AZURE.

![Sauvegarde des ordinateurs et des charges de travail protégées par DPM ou MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Stockage de machine virtuelle Azure

Les machines virtuelles Azure utilisent des disques pour stocker leur système d’exploitation, applications et données. Chaque machine virtuelle Azure a au moins deux disques : un disque pour le système d’exploitation et un disque temporaire. Les machines virtuelles Azure peuvent également avoir des disques de données pour les données d’application. Les disques sont stockés en tant que disques durs virtuels.

- Disques durs virtuels sont stockés sous la forme d’objets BLOB de pages dans les comptes de stockage standard ou premium dans Azure :
    - **Stockage standard :** prise en charge de disque fiable et économique pour les machines virtuelles exécutant des charges de travail qui ne sont pas sensibles à la latence. Stockage standard peut utiliser des disques SSD standard (SSD) ou lecteur de disque dur standard (HDD).
    - **Stockage Premium :** prise en charge de disque à hautes performances. Utilise des disques SSD Premium.
- Il existe différents niveaux de performances pour les disques :
    - **Disque dur standard :** repose sur des disques durs et est utilisé pour un stockage économique.
    - **Disque SSD standard :** Combine les éléments de disques SSD de premium et des disques HDD standard. Offre des performances et la fiabilité que le disque dur, mais toujours économique plus cohérente.
    - **Disque SSD de Premium :** Appuie sur des disques SSD et fournit de hautes performances et à faible latence pour les machines virtuelles qui exécutent des charges de travail e/S intensives.
- Les disques peuvent être managés ou non managés :
    - **Disques non gérés :** Type traditionnel de disques utilisés par les machines virtuelles. Pour ces disques, vous créez votre propre compte de stockage et spécifiez ce dernier lors de la création du disque. Vous devez ensuite déterminer comment optimiser les ressources de stockage pour vos machines virtuelles.
    - **Disques managés :** Azure crée et gère les comptes de stockage pour vous. Vous spécifiez le niveau de performance et de taille de disque, et Azure crée des disques gérés pour vous. Lorsque vous ajoutez des disques et mettre à l’échelle de machines virtuelles, Azure gère les comptes de stockage.

Pour plus d’informations sur le stockage sur disque et les types de disque disponible pour les machines virtuelles, consultez les articles suivants :

- [Disques gérés Azure pour les machines virtuelles Windows](../virtual-machines/windows/managed-disks-overview.md)
- [Disques gérés Azure pour les machines virtuelles Linux](../virtual-machines/linux/managed-disks-overview.md)
- [Types de disque disponible pour les machines virtuelles](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Sauvegarder et restaurer des machines virtuelles Azure avec stockage premium 

Vous pouvez sauvegarder des machines virtuelles Azure à l’aide de stockage premium avec sauvegarde Azure :

- Pendant le processus de sauvegarde des machines virtuelles avec stockage premium, le service de sauvegarde crée un emplacement temporaire intermédiaire, nommé *AzureBackup -*, dans le compte de stockage. La taille de l’emplacement intermédiaire est égale à la taille de la capture instantanée de point de récupération.
- Vérifiez que le compte de stockage Premium dispose d’un espace libre suffisant pour prendre en compte cet emplacement intermédiaire temporaire. [Plus d’informations](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits) Ne modifiez pas l’emplacement intermédiaire.
- Une fois la sauvegarde terminée, l’emplacement intermédiaire est supprimé.
- Le prix du stockage utilisé pour l’emplacement intermédiaire est conforme à la [tarification du stockage Premium](../virtual-machines/windows/disks-types.md#billing).

Lorsque vous restaurez des machines virtuelles Azure à l’aide de stockage premium, vous pouvez les restaurer vers premium ou stockage standard. En règle générale, vous restaurez les vers le stockage premium. Mais si vous devez uniquement un sous-ensemble de fichiers à partir de la machine virtuelle, il peut être rentable de les restaurer vers le stockage standard.

### <a name="back-up-and-restore-managed-disks"></a>Sauvegarder et restaurer des disques gérés

Vous pouvez sauvegarder des machines virtuelles Azure avec des disques gérés :

- Pour sauvegarder des machines virtuelles avec disques managés, vous devez procéder de la même façon que pour toute autre machine virtuelle Azure. Vous pouvez sauvegarder la machine virtuelle directement à partir des paramètres de machine virtuelle, ou vous pouvez activer la sauvegarde pour des machines virtuelles dans le coffre Recovery Services.
- Vous pouvez sauvegarder des machines virtuelles sur des disques managés par le biais des collections RestorePoint basées sur des disques managés.
- Sauvegarde Azure prend également en charge la sauvegarde des machines virtuelles avec des disques gérés qui ont été chiffrées à l’aide d’Azure Disk Encryption.

Lorsque vous restaurez des machines virtuelles avec des disques gérés, vous pouvez restaurer à une machine virtuelle complète avec des disques gérés ou à un compte de stockage :

- Pendant le processus de restauration, Azure gère les disques gérés. Si vous utilisez l’option de compte de stockage, vous gérez le compte de stockage qui est créé pendant le processus de restauration.
- Si vous restaurez une machine virtuelle est chiffrée, vérifiez que les clés de la machine virtuelle et secrets existent dans le coffre de clés avant de commencer le processus de restauration.

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue la matrice de prise en charge pour [en savoir plus sur les fonctionnalités prises en charge et les limitations pour les scénarios de sauvegarde](backup-support-matrix.md).
- Configurer la sauvegarde pour l’une de ces scénarios :
    - [Sauvegarder des machines virtuelles Azure](backup-azure-arm-vms-prepare.md).
    - [Sauvegarder des ordinateurs Windows directement](tutorial-backup-windows-server-to-azure.md), sans serveur de sauvegarde.
    - [Configurer MABS](backup-azure-microsoft-azure-backup.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans MABS.
    - [Configurer DPM](backup-azure-dpm-introduction.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

