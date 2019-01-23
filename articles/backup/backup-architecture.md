---
title: Architecture de Sauvegarde Azure
description: Fournit une vue d’ensemble de l’architecture, des composants et des processus utilisés par le service Sauvegarde Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: raynew
ms.openlocfilehash: d3e6a17ba9d0712d921d8e0a1d0bcbcd68ce5cfb
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360482"
---
# <a name="azure-backup-architecture"></a>Architecture de Sauvegarde Azure

Vous pouvez utiliser le [service Sauvegarde Azure](backup-overview.md) pour sauvegarder des données dans le cloud Microsoft Azure. Cet article résume l’architecture, les composants et les processus de Sauvegarde Azure. 


## <a name="what-does-azure-backup-do"></a>Que fait Sauvegarde Azure ?

Sauvegarde Azure sauvegarde les données, l’état de la machine et les charges de travail en cours d’exécution sur les ordinateurs locaux et machines virtuelles Azure. Il existe plusieurs scénarios de Sauvegarde Azure.

- **Sauvegarder des ordinateurs locaux** :
    - vous pouvez sauvegarder des ordinateurs locaux directement dans Azure à l’aide de Sauvegarde Azure.
    - Vous pouvez protéger les ordinateurs locaux avec System Center Data Protection Manager (DPM) ou avec Serveur Sauvegarde Microsoft Azure (MABS), puis sauvegarder les données protégées sur DPM/MABS dans Azure à l’aide de Sauvegarde Azure.
- **Sauvegarder des machines virtuelles Azure** :
    - vous pouvez sauvegarder des machines virtuelles Azure directement avec Sauvegarde Azure.
    - Vous pouvez protéger des machines virtuelles Azure avec DPM ou MABS s’exécutant dans Azure, puis sauvegarder les données protégées sur DPM/MABS avec Sauvegarde Azure.

Apprenez-en davantage sur [ce que vous pouvez sauvegarder](backup-overview.md) et sur les [scénarios de sauvegarde pris en charge](backup-support-matrix.md).


## <a name="where-is-data-backed-up"></a>Où les données sont-elles sauvegardées ?

Sauvegarde Azure stocke les données sauvegardées dans un coffre Recovery Services. Un coffre est une entité de stockage en ligne dans Azure qui permet de conserver les données telles que les copies de sauvegarde, les points de récupération et les stratégies de sauvegarde.

- Les coffres Recovery Services facilitent l’organisation de vos données de sauvegarde, tout en réduisant le temps de gestion.
- Dans chaque abonnement Azure, vous pouvez créer jusqu’à 500 coffres Recovery Services. 
- Vous pouvez superviser les éléments sauvegardés dans un coffre, notamment les machines virtuelles Azure et les ordinateurs locaux.
- Vous pouvez gérer l’accès au coffre avec le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) Azure.
- Vous spécifiez le mode de réplication des données dans le coffre pour la redondance :
    - **LRS** : vous pouvez utiliser le stockage localement redondant (LRS) pour offrir une protection contre les défaillances dans un centre de données. LRS réplique les données vers une unité d’échelle de stockage. [Plus d’informations](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
    - **GRS** : vous pouvez utiliser le stockage géoredondant (GRS) : Il offre une protection contre les défaillances à l’échelle régionale. Il réplique vos données dans une région secondaire. [Plus d’informations](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) 
    - Par défaut, les coffres Recovery Services pour Sauvegarde Azure utilisent GRS. 



## <a name="how-does-azure-backup-work"></a>Comment fonctionne Sauvegarde Azure ?

Sauvegarde Azure exécute des travaux de sauvegarde sur la base d’une stratégie de sauvegarde par défaut ou personnalisée. La façon dont Sauvegarde Azure effectue une sauvegarde dépend du scénario.

**Scénario** | **Détails** 
--- | ---
**Sauvegarder directement des ordinateurs locaux** | Pour sauvegarder directement des ordinateurs locaux, Sauvegarde Azure utilise l’agent MARS (Microsoft Azure Recovery Services). L’agent est installé sur chaque ordinateur que vous souhaitez sauvegarder. <br/><br/> Ce type de sauvegarde n’est pas disponible pour les ordinateurs Linux locaux. 
**Sauvegarder directement des machines virtuelles Azure** | Pour sauvegarder directement des machines virtuelles Azure, une extension de machine virtuelle Azure est installée sur la machine virtuelle la première fois qu’une sauvegarde s’exécute pour la machine virtuelle. 
**Sauvegarder des machines et des applications protégées par DPM ou MABS** | Dans ce scénario, l’ordinateur/application est tout d’abord sauvegardé dans le stockage local DPM ou MABS. Ensuite, les données dans DPM/MABS sont sauvegardées dans le coffre par Sauvegarde Azure. Les ordinateurs locaux peuvent être protégés par DPM/MABS exécuté localement. Les machines virtuelles Azure peuvent être protégées par DPM/MABS exécuté dans Azure.

[Obtenez une vue d’ensemble](backup-overview.md) et consultez [ce qui est pris en charge](backup-support-matrix.md) pour chaque scénario.


### <a name="backup-agents"></a>Agents de sauvegarde

Sauvegarde Azure fournit différents agents en fonction du type de sauvegarde.

**Agent** | **Détails** 
--- | --- 
**Agent Microsoft Azure Recovery Services (MARS)** | Cet agent s’exécute sur chaque serveur Windows local pour sauvegarder les fichiers, les dossiers et l’état du système<br/><br/> Cet agent s’exécute sur les serveurs DPM/MABS pour sauvegarder le disque de stockage local DPM/MABS. Les applications et les ordinateurs sont sauvegardés localement sur ce disque DPM/MABS.
**Extension de machine virtuelle Azure** | Pour sauvegarder des machines virtuelles Azure, une extension de sauvegarde est ajoutée à l’agent de machine virtuelle Azure en cours d’exécution sur les machines virtuelles. 


## <a name="backup-types"></a>Types de sauvegarde

**Type de sauvegarde** | **Détails** | **Utilisation**
--- | --- | ---
**Complète** | Une sauvegarde contient la source de données entière.<br/><br/> Une sauvegarde complète occupe plus de bande passante réseau. | Utilisée pour la sauvegarde initiale.
**Différentielle** |  Stocke les blocs ayant changé depuis la sauvegarde complète initiale. Utilise une plus petite quantité de stockage et de réseau, et ne conserve pas de copies redondantes des données inchangées.<br/><br/> Inefficace, car les blocs de données inchangés entre les sauvegardes ultérieures sont transférés et stockés. | Non utilisée par Sauvegarde Azure.
**Incrémentielle** | Efficacité élevée du point de vue du stockage et du réseau. Stocke uniquement les blocs de données ayant changé depuis la sauvegarde précédente. <br/><br/> Avec une sauvegarde incrémentielle, il est inutile d’effectuer des sauvegardes complètes régulières. | Utilisée par DPM/MABS pour les sauvegardes sur disque, et utilisée dans toutes les sauvegardes vers Azure.

### <a name="comparison"></a>Comparaison

La consommation du stockage, l’objectif de délai de récupération (RTO) et la consommation réseau varient pour chaque type de sauvegarde. L’illustration suivante compare les types de sauvegarde.
- La source de données A est composée de 10 blocs de stockage A1-A10, qui sont sauvegardés tous les mois.
- Les blocs A2, A3, A4 et A9 ont changé lors du premier mois et le bloc A5 a changé lors du mois suivant.
- Pour les sauvegardes différentielles, lors du deuxième mois, les blocs A2, A3, A4 et A9 qui ont changé sont sauvegardés. Lors du troisième mois, ces mêmes blocs sont à nouveau sauvegardés, ainsi que le bloc A5 qui a changé. Les blocs modifiés continuent d’être sauvegardés jusqu’à la prochaine sauvegarde complète.
- Pour les sauvegardes différentielles, après la sauvegarde complète effectuée pour le premier mois, les blocs A2, A3, A4 et A9 sont marqués comme modifiés et transférés pour le deuxième mois. Lors du troisième mois, seul le bloc A5 qui a changé est marqué et transféré. 

![illustration montrant la comparaison des méthodes de sauvegarde](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Fonctionnalités de sauvegarde

Le tableau suivant récapitule les fonctionnalités pour différents types de sauvegarde.

**Fonctionnalité** | **Ordinateurs Windows locaux (direct)** | **Machines virtuelles Azure** | **Ordinateurs/applications avec DPM/MABS**
--- | --- | --- | ---
Sauvegarder vers le coffre | ![Oui][green] | ![OUI][green] | ![Oui][green] 
Sauvegarder sur disque DPM/MABS puis Azure | | | ![Oui][green] 
Compresser les données envoyées pour la sauvegarde | ![Oui][green] | Aucune compression n’est effectuée lors du transfert des données. Le stockage croît légèrement, mais la restauration est plus rapide.  | ![Oui][green] 
Exécuter une sauvegarde incrémentielle |![Oui][green] |![Oui][green] |![Oui][green] 
Sauvegarder les disques dédupliqués | | | ![Partiellement][yellow]<br/><br/> Uniquement pour les serveurs DPM/MABS déployés localement. 

![clé de table](./media/backup-architecture/table-key.png)


## <a name="architecture-direct-backup-of-on-premises-windows-machines"></a>Architecture : sauvegarde directe des ordinateurs Windows locaux

1. Pour configurer le scénario, vous téléchargez et installez l’agent MARS sur l’ordinateur, vous sélectionnez les éléments à sauvegarder, quand les sauvegardes seront exécutées, et la durée pendant laquelle elles seront conservées dans Azure.
2. La sauvegarde initiale s’exécute conformément à vos paramètres de sauvegarde.
3. L’agent MARS utilise le service Windows VSS (Volume Shadow Copy) pour prendre un instantané dans le temps des volumes sélectionnés pour la sauvegarde.
    - L’agent MARS utilise uniquement l’écriture système de Windows pour capturer l’instantané.
    - Il n’utilise aucun enregistreur VSS d’application, et ne capture donc pas d’instantané cohérent au niveau de l’application.
3. Après avoir pris l’instantané avec VSS, l’agent MARS crée un disque dur virtuel dans le dossier de cache que vous avez spécifié quand vous avez configuré la sauvegarde, et il stocke les sommes de contrôle pour chaque bloc de données. 
4. Les sauvegardes incrémentielles s’exécutent conformément à la planification que vous spécifiez, sauf si vous exécutez une sauvegarde ad hoc.
5. Dans les sauvegardes incrémentielles, les fichiers modifiés sont identifiés et un nouveau disque dur virtuel est créé. Il est compressé et chiffré, puis envoyé dans le coffre.
6. Une fois la sauvegarde incrémentielle terminée, le nouveau disque dur virtuel est fusionné avec le disque dur virtuel créé après la réplication initiale, fournissant ainsi l’état le plus récent à utiliser pour la comparaison pour la sauvegarde en cours. 

![Sauvegarde d’un serveur Windows local avec l’agent MARS](./media/backup-architecture/architecture-on-premises-mars.png)


## <a name="architecture-direct-backup-of-azure-vms"></a>Architecture : sauvegarde directe de machines virtuelles Azure

1. Quand vous activez la sauvegarde pour une machine virtuelle Azure, une sauvegarde s’exécute conformément à la planification que vous spécifiez.
2. Lors de la première sauvegarde, une extension de sauvegarde est installée sur la machine virtuelle si elle est en cours d’exécution.
    - Pour les machines virtuelles Windows, l’extension VMSnapshot est installée.
    - Pour les machines virtuelles Linux, l’extension Linux VMSnapshot est installée.
3. L’extension prend un instantané au niveau du stockage. 
    - Pour les machines virtuelles Windows en cours d’exécution, Sauvegarde Azure coopère avec VSS afin de prendre un instantané cohérent au niveau de l’application de la machine virtuelle. Par défaut, Sauvegarde Azure effectue des sauvegardes VSS complètes. Si Sauvegarde Azure ne peut pas prendre d’instantané cohérent au niveau de l’application, il prend un instantané cohérent au niveau fichier.
    - Pour les machines virtuelles Linux, Sauvegarde Azure effectue une sauvegarde cohérente au niveau fichier. Pour les instantanés cohérents au niveau de l’application, vous devez personnaliser manuellement le pré-script et le post-script.
    - La sauvegarde est optimisée grâce à la sauvegarde de chaque disque de machine virtuelle en parallèle. Pour chaque disque en cours de sauvegarde, Sauvegarde Azure lit les blocs sur le disque et stocke uniquement les données modifiées. 
4. Une fois l’instantané pris, les données sont transférées vers le coffre. 
    - Seuls les blocs de données qui ont changé depuis la dernière sauvegarde sont copiés.
    - Les données ne sont pas chiffrées. Sauvegarde Azure peut sauvegarder des machines virtuelles Azure chiffrées avec ADE (Azure Disk Encryption).
    - Les données d’instantanés peuvent ne pas être immédiatement copiées dans le coffre. Aux heures de pointe, cela peut prendre quelques heures. La durée de sauvegarde totale d’une machine virtuelle sera inférieure à 24 heures pour des stratégies de sauvegarde quotidienne.
5. Une fois les données envoyées dans le coffre, l’instantané est supprimé et un point de récupération est créé.


![Sauvegarde des machines virtuelles Azure](./media/backup-architecture/architecture-azure-vm.png)


## <a name="architecture-back-up-to-dpmmabs"></a>Architecture : sauvegarder dans DPM/MABS

1. Vous installez l’agent de protection DPM ou MABS sur les ordinateurs que vous souhaitez protéger, et vous ajoutez les ordinateurs à un groupe de protection DPM.
    - Pour protéger des ordinateurs locaux, le serveur DPM ou MABS doit être local.
    - Pour protéger des machines virtuelles Azure, le serveur DPM ou MABS doit se trouver dans Azure, exécuté en tant que machine virtuelle Azure.
    - À l’aide de DPM/MABS, vous pouvez protéger des volumes, des partages, des fichiers et des dossiers. Vous pouvez protéger l’état du système des ordinateurs, et protéger des applications spécifiques avec des paramètres de sauvegarde prenant en charge les applications.
2. Quand vous configurez la protection d’une machine ou d’une application dans DPM, vous choisissez de sauvegarder sur le disque local DPM pour le stockage à court terme et sur Azure (protection en ligne). Vous spécifiez également quand doit s’exécuter la sauvegarde vers le stockage local DPM/MABS, et quand doit s’exécuter la sauvegarde en ligne sur Azure.
3. Le disque de la charge de travail protégée est sauvegardé vers les disques locaux DPM et dans Azure, conformément à la planification que vous avez spécifiée.
4. La sauvegarde en ligne dans le coffre est gérée par l’agent MARS en cours d’exécution sur le serveur DPM/MABS.

![Sauvegarde de machines/charges de travail protégées par DPM ou MABS](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Stockage de machine virtuelle Azure

- Les machines virtuelles Azure utilisent des disques pour stocker leur système d’exploitation, applications et données.
- Les machines virtuelles Azure ont au moins deux disques : un pour le système d’exploitation et un disque temporaire. Elles peuvent également avoir des disques de données pour les données d’application. Les disques sont stockés en tant que disques durs virtuels.
- Les disques durs virtuels sont stockés sous la forme d’objets blob de pages dans des comptes de stockage standard ou premium dans Azure.
    - Stockage standard : prise en charge de disque fiable et économique pour les machines virtuelles exécutant des charges de travail qui ne sont pas sensibles à la latence. Le stockage standard peut utiliser des disques SSD Standard ou des disques SSD Premium.
    - Stockage Premium : prise en charge de disque à hautes performances. Utilise des disques SSD Premium.
- Il existe différents niveaux de performances pour les disques :
    - Disque HDD Standard : repose sur des disques durs et est utilisé pour un stockage économique.
    - Disque SSD Standard : combine des caractéristiques des disques SSD Premium et des disques HDD Standard, offrant une fiabilité et des performances plus cohérentes que les disques HDD tout en restant rentable.
    - Disque SSD Premium : fournissent de hautes performances et une faible latence pour les machines virtuelles exécutant des charges de travail gourmandes en E/S.
- Les disques peuvent être managés ou non managés :
    - Disques non managés : types de disques classiques utilisés par les machines virtuelles. Pour ces disques, vous créez votre propre compte de stockage et spécifiez ce dernier lors de la création du disque. Vous devez déterminer comment optimiser les ressources de stockage pour vos machines virtuelles.
    - Disques managés : Azure gère la création et la gestion des comptes de stockage pour vous. Vous spécifiez le niveau de performances et la taille des disques, et Azure crée et gère les disques pour vous. Azure gère le stockage à mesure que vous ajoutez des disques et mettez à l’échelle les machines virtuelles.

Pour en savoir plus :

- Apprenez-en davantage sur le stockage sur disque pour les machines virtuelles [Windows](../virtual-machines/windows/about-disks-and-vhds.md) et [Linux](../virtual-machines/linux/about-disks-and-vhds.md).
- Apprenez-en davantage sur le stockage [Standard](../virtual-machines/windows/standard-storage.md) et [Premium](../virtual-machines/windows/premium-storage.md).


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>Sauvegarde et restauration de machines virtuelles Azure avec le stockage Premium 

Vous pouvez sauvegarder des machines virtuelles Azure utilisant du stockage Premium avec Sauvegarde Azure :

- Au moment de sauvegarder des machines virtuelles avec stockage Premium, le service Sauvegarde crée un emplacement intermédiaire temporaire nommé « AzureBackup- » dans le compte de stockage. La taille de l’emplacement intermédiaire est égale à la taille de la capture instantanée de point de récupération.
- Vérifiez que le compte de stockage Premium dispose d’un espace libre suffisant pour prendre en compte cet emplacement intermédiaire temporaire. [Plus d’informations](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets) Ne modifiez pas l’emplacement intermédiaire.
- Une fois la sauvegarde terminée, l’emplacement intermédiaire est supprimé.
- Le prix du stockage utilisé pour l’emplacement intermédiaire est conforme à la [tarification du stockage Premium](../virtual-machines/windows/premium-storage.md#pricing-and-billing).

Quand vous restaurez des machines virtuelles Azure utilisant le stockage Premium, vous pouvez les restaurer vers un stockage Premium ou Standard. En général, vous les restaurerez vers du stockage Premium, mais il peut être rentable de les restaurer vers un stockage Standard si vous avez uniquement besoin d’un sous-ensemble de fichiers de la machine virtuelle.

### <a name="backing-up-and-restoring-managed-disks"></a>Sauvegarde et restauration de disques managés

Vous pouvez sauvegarder des machines virtuelles Azure avec disques managés.
- Pour sauvegarder des machines virtuelles avec disques managés, vous devez procéder de la même façon que pour toute autre machine virtuelle Azure. Vous pouvez sauvegarder la machine virtuelle directement à partir des paramètres de machine virtuelle, ou vous pouvez activer la sauvegarde pour des machines virtuelles dans le coffre Recovery Services.
- Vous pouvez sauvegarder des machines virtuelles sur des disques managés par le biais des collections RestorePoint basées sur des disques managés.
- Sauvegarde Azure prend également en charge la sauvegarde des machines virtuelles avec disques managés chiffrées avec ADE.

Quand vous restaurez des machines virtuelles avec disques managés, vous pouvez effectuer la restauration sur une machine virtuelle complète avec disques managés ou dans un compte de stockage.
- Azure gère les disques managés pendant le processus de restauration, et avec l’option de compte de stockage vous gérez le compte de stockage qui est créé pendant la restauration.
- Si vous restaurez une machine virtuelle managée qui est chiffrée, les clés et les secrets de la machine virtuelle doivent cesser d’être applicables dans le coffre de clés avant que vous ne commenciez le processus de restauration.




## <a name="next-steps"></a>Étapes suivantes

- [Passez en revue](backup-support-matrix.md) la matrice de prise en charge pour en savoir plus sur les fonctionnalités prises en charge et sur les limitations pour les scénarios de sauvegarde.
- Configurez la sauvegarde pour l’un des scénarios :
    - [Sauvegarder des machines virtuelles Azure](backup-azure-arm-vms-prepare.md)
    - [Sauvegarder des ordinateurs Windows directement](tutorial-backup-windows-server-to-azure.md), sans serveur de sauvegarde.
    - [Configurer MABS](backup-azure-microsoft-azure-backup.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans MABS.
    - [Configurer DPM](backup-azure-dpm-introduction.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

