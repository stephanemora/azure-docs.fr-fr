---
title: Sauvegarde et récupération d’urgence de disques managés sur des machines virtuelles Azure
description: Cet article explique comment planifier la sauvegarde et la récupération d’urgence de machines virtuelles IaaS et de disques managés dans Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: a2c060362a74400a1356d96fb85a4e62d20cac57
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101797"
---
# <a name="backup-and-disaster-recovery-for-azure-managed-disks"></a>Sauvegarde et récupération d’urgence de disques managés Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles

Cet article explique comment planifier la sauvegarde et la récupération d’urgence de disques managés Azure.

Nous abordons tout d’abord les fonctionnalités de tolérance de panne intégrées dans la plateforme Azure, qui vous protègent contre des défaillances locales. Nous présentons ensuite les scénarios d’urgence qui ne sont pas entièrement couverts par les fonctionnalités intégrées. Par ailleurs, nous montrons plusieurs exemples de charges de travail où différentes considérations en matière de sauvegarde et de récupération d’urgence peuvent s’appliquer. Et nous abordons également certaines solutions de récupération d’urgence pour disques managés.

## <a name="introduction"></a>Introduction

Azure utilise différentes méthodes pour la redondance et la tolérance de panne afin de protéger les clients contre des défaillances matérielles localisées. Les défaillances locales peuvent inclure des problèmes avec un serveur de stockage Azure qui stocke une partie des données d’un disque virtuel, ou des défaillances de disques SSD ou de disques durs sur ce serveur. Des défaillances isolées de composants matériels peuvent se produire durant des opérations normales.

Azure est conçu pour assurer la résilience à ces défaillances. Des sinistres majeurs peuvent entraîner des défaillances ou l’inaccessibilité de nombreux serveurs de stockage, voire d’un centre de données entier. Si vos machines virtuelles et vos disques sont normalement protégés contre des défaillances localisées, des étapes supplémentaires sont nécessaires pour protéger votre charge de travail contre des défaillances irrécupérables à l’échelle d’une région (par exemple, une catastrophe majeure) susceptibles d’affecter vos machines virtuelles et vos disques.

Outre la possibilité de défaillances de la plateforme, des problèmes avec l’application ou les données d’un client peuvent se produire. Par exemple, une nouvelle version de votre application peut par inadvertance apporter des modifications aux données et les endommager. Dans ce cas, vous pouvez restaurer l’application et les données à une version antérieure qui contient le dernier état correct connu. Cela nécessite la gestion de sauvegardes régulières.

Pour la récupération d’urgence régionale, vous devez sauvegarder vos disques de machines virtuelles IaaS dans une autre région. 

Avant d’examiner les options de sauvegarde et de récupération d’urgence, récapitulons quelques méthodes disponibles pour gérer des défaillances localisées.

### <a name="azure-iaas-resiliency"></a>Résilience Azure IaaS

La résilience fait référence à la tolérance aux défaillances normales qui se produisent dans les composants matériels. La résilience est la capacité à récupérer après des défaillances et à continuer de fonctionner. Il ne s’agit pas d’éviter les défaillances, mais d’y répondre en évitant les temps d’arrêt ou la perte de données. L’objectif de la résilience est que l’application retrouve un état entièrement fonctionnel suite à une défaillance. Les machines virtuelles et les disques managés Azure sont conçus pour assurer la résilience aux défaillances matérielles courantes. Examinons comment la plateforme Azure IaaS fournit cette résilience.

Une machine virtuelle est constituée principalement de deux parties : un serveur de calcul et les disques persistants. Ces deux parties affectent la tolérance de panne d’une machine virtuelle.

Si le serveur hôte de calcul Azure qui héberge votre machine virtuelle subit une défaillance matérielle (ce qui est rare), Azure est conçu pour restaurer automatiquement la machine virtuelle sur un autre serveur. Dans ce scénario, votre ordinateur redémarre et la machine virtuelle redevient opérationnelle après un certain temps. Azure détecte automatiquement de telles défaillances matérielles et commence la récupération pour garantir la disponibilité de la machine virtuelle du client aussi vite que possible.

Concernant vos disques managés, la durabilité des données est essentielle pour une plateforme de stockage persistant. Les clients Azure ont des applications métiers importantes exécutées sur IaaS qui dépendent de la persistance des données. Azure conçoit la protection de ces disques IaaS avec trois copies redondantes des données stockées localement. Ces copies fournissent ainsi une durabilité élevée contre les défaillances locales. Si un des composants matériels qui contient le disque tombe en panne, votre machine virtuelle n’est pas affectée, car il existe deux copies supplémentaires pour prendre en charge les demandes du disque. Cela fonctionne bien même si deux composants matériels prenant en charge un disque tombent en panne en même temps (ce qui est rare). 

Pour garantir le maintien en continu de trois réplicas, Azure crée automatiquement une nouvelle copie des données en arrière-plan si l’une des trois copies devient indisponible. Par conséquent, il ne doit pas être nécessaire d’utiliser un système RAID avec des disques Azure pour assurer la tolérance de panne. Une simple configuration RAID 0 doit être suffisante pour la répartition des disques le cas échéant afin de créer des volumes de plus grande capacité.

Cette architecture a permis à Azure de fournir de façon cohérente une durabilité de classe Entreprise pour les disques IaaS, avec un [taux de défaillance annuel](https://en.wikipedia.org/wiki/Annualized_failure_rate) inégalé dans le secteur de zéro %.

Les défaillances matérielles localisées sur l’ordinateur hôte ou dans la plateforme de stockage peuvent parfois entraîner une indisponibilité temporaire de la machine virtuelle couverte par le [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) qui en garantit la disponibilité. Azure fournit également un contrat SLA de pointe pour les instances de machine virtuelle uniques qui utilisent des disques SSD Azure Premium.

Pour protéger les charges de travail d’application contre les temps d’arrêt suite à l’indisponibilité temporaire d’un disque ou d’une machine virtuelle, les clients peuvent utiliser les [groupes à haute disponibilité](./availability.md). Plusieurs machines virtuelles regroupées dans un groupe à haute disponibilité assurent la redondance de votre application. Azure crée ensuite ces machines virtuelles et disques dans des domaines d’erreur distincts avec différents composants d’alimentation, réseau et serveur.

En raison de ces domaines d’erreur distincts, les défaillances matérielles localisées n’affectent généralement pas plusieurs machines virtuelles du groupe en même temps. Ces domaines d’erreur distincts garantissent une haute disponibilité pour votre application. Il est considéré comme une bonne pratique d’utiliser des groupes à haute disponibilité lorsque la haute disponibilité est requise.

### <a name="backup-and-disaster-recovery"></a>Sauvegarde et récupération d'urgence

La récupération d’urgence est la capacité à pouvoir récupérer les données à la suite d’incidents rares mais majeurs. Ces incidents incluent les défaillances non temporaires à grande échelle telles qu’une interruption de service affectant une région entière. La récupération d’urgence inclut la sauvegarde et l’archivage des données. Elle peut également inclure une intervention manuelle, comme la restauration d’une base de données à partir d’une sauvegarde.

Il se peut que la protection intégrée de la plateforme Azure contre les défaillances localisées ne protège pas entièrement les machines virtuelles/disques si un sinistre majeur provoque des interruptions à grande échelle. Ces pannes à grande échelle incluent des événements catastrophiques tels qu’un centre de données touché par un ouragan, un tremblement de terre, un incendie ou des défaillances d’unités matérielles à grande échelle. En outre, vous pouvez rencontrer des défaillances en raison de problèmes rencontrés par l’application ou les données.

Pour protéger vos charges de travail IaaS contre les interruptions, vous devez planifier la redondance et disposer de sauvegardes afin d’activer la récupération. Pour la récupération d’urgence, vous devez effectuer la sauvegarde dans un emplacement géographique différent situé en dehors du site principal. Cette approche permet de garantir que la sauvegarde n’est pas affectée par l’événement qui a initialement eu un impact sur la machine virtuelle ou les disques. Pour plus d’informations, consultez [Récupération d’urgence pour les applications Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Les aspects à prendre en considération pour la récupération d’urgence peuvent être les suivants :

- Haute disponibilité : capacité de l’application à continuer de s’exécuter dans un état sain, sans temps d’arrêt significatif. L’état Sain signifie que l’application est réactive et que les utilisateurs peuvent s’y connecter et interagir avec elle. Certaines applications et bases de données critiques peuvent avoir besoin d’être toujours disponibles, même en cas de défaillances dans la plateforme. Pour ces charges de travail, vous devrez peut-être planifier une redondance pour l’application ainsi que les données.

- Durabilité des données : dans certains cas, la considération principale est de s’assurer que les données sont préservées en cas de sinistre. Par conséquent, vous aurez peut-être besoin d’une sauvegarde de vos données dans un autre site. Pour ces charges de travail, vous n’aurez peut-être pas besoin d’une redondance complète pour l’application, mais uniquement d’une sauvegarde régulière des disques.

## <a name="backup-and-disaster-recovery-scenarios"></a>Scénarios de sauvegarde et de récupération d’urgence

Examinons quelques exemples de charges de travail d’application et les éléments à prendre en compte pour la planification de la récupération d’urgence.

### <a name="scenario-1-major-database-solutions"></a>Scénario 1 : solutions de base de données majeures

Prenons l’exemple d’un serveur de base de données de production, tel que SQL Server ou Oracle, prenant en charge la haute disponibilité. Les utilisateurs et les applications de production critiques dépendent de cette base de données. Le plan de récupération d’urgence pour ce système peut être de prendre en charge les exigences suivantes :

- Les données doivent être protégées et récupérables.
- Le serveur doit être disponible pour utilisation.

Le plan de récupération d’urgence peut nécessiter de maintenir un réplica de la base de données dans une autre région en tant que sauvegarde. Selon les exigences liées à la récupération des données et à la disponibilité du serveur, la solution peut aller d’un site de réplica actif-actif ou actif-passif à des sauvegardes en mode hors connexion des données. Les bases de données relationnelles, telles que SQL Server et Oracle, fournissent diverses options pour la réplication. Pour SQL Server, utilisez les [groupes de disponibilité SQL Server Always On](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) pour la haute disponibilité.

Les bases de données NoSQL comme MongoDB prennent également en charge les [réplicas](https://docs.mongodb.com/manual/replication/) pour assurer la redondance. Les réplicas pour la haute disponibilité sont utilisés.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scénario 2 : un cluster de machines virtuelles redondantes

Prenons l’exemple d’une charge de travail gérée par un cluster de machines virtuelles qui fournit la redondance et l’équilibrage de charge. Il peut s’agir d’un cluster Cassandra déployé dans une région. Ce type d’architecture fournit déjà un niveau élevé de redondance dans cette région. Toutefois, pour protéger la charge de travail contre une défaillance au niveau régional, vous devez envisager de répartir le cluster entre deux régions ou d’effectuer des sauvegardes périodiques vers une autre région.

### <a name="scenario-3-iaas-application-workload"></a>Scénario 3 : charge de travail d’application IaaS

Examinons la charge de travail de l’application IaaS. Par exemple, cette application peut être une charge de travail de production standard exécutée sur une machine virtuelle Azure. Il peut s’agir d’un serveur web ou d’un serveur de fichiers contenant le contenu et d’autres ressources d’un site. Il peut également s’agir d’une application métier personnalisée exécutée sur une machine virtuelle qui stocke ses données, ressources et son état sur des disques de machine virtuelle. Dans ce cas, il est important de veiller à effectuer des sauvegardes régulièrement. La fréquence des sauvegardes doit être basée sur la nature de la charge de travail de machine virtuelle. Par exemple, si l’application s’exécute chaque jour et modifie des données, la sauvegarde doit être effectuée toutes les heures.

Voici un autre exemple : un serveur de rapports qui extrait des données provenant d’autres sources et génère des rapports agrégés. La perte de cette machine virtuelle ou des disques peut entraîner la perte des rapports. Toutefois, il est possible de réexécuter le processus de création de rapports et de regénérer la sortie. Dans ce cas, vous ne subissez pas vraiment une perte de données, même si le serveur de rapports est atteint par un sinistre. Par conséquent, vous pouvez avoir un niveau plus élevé de tolérance en cas de perte d’une partie des données sur le serveur de rapports. Dans ce cas, des sauvegardes moins fréquentes sont une option permettant de réduire les coûts.

### <a name="scenario-4-iaas-application-data-issues"></a>Scénario 4 : problèmes liés aux données d’une application IaaS

Des problèmes peuvent également affecter les données d’une application IaaS. Par exemple, vous avez une application qui calcule, tient à jour et gère des données commerciales critiques telles que des informations de tarification. Une nouvelle version de votre application a rencontré un bogue logiciel qui a incorrectement calculé la tarification et a endommagé les données commerciales actuelles prises en charge par la plateforme. Ici, le meilleur plan d’action consiste à rétablir la version antérieure de l’application et des données. Pour ce faire, effectuez des sauvegardes périodiques de votre système.

## <a name="disaster-recovery-solution-azure-disk-backup"></a>Solution de récupération d’urgence : sauvegarde de disque Azure 

La sauvegarde des disques Azure est une solution de sauvegarde cloud native qui protège vos données sur des disques managés. Il s’agit d’une solution simple, sécurisée et économique qui vous permet de configurer la protection des disques managés en quelques étapes. Elle vous garantit de pouvoir récupérer vos données dans un scénario d’urgence.

La sauvegarde des disques Azure offre une solution clé en main qui fournit une gestion du cycle de vie des instantanés pour les disques managés en automatisant la création périodique d’instantanés et en les conservant pour une durée configurée à l’aide d’une stratégie de sauvegarde. Vous pouvez gérer les instantanés des disques sans aucun coût d’infrastructure et sans avoir recours à aucun script personnalisé ni aucune surcharge de gestion. Il s’agit d’une solution de sauvegarde avec cohérence en cas de plantage qui effectue une sauvegarde ponctuelle d’un disque managé au moyen d’instantanés incrémentiels avec une prise en charge de plusieurs sauvegardes par jour. Il s’agit également d’une solution sans agent qui n’a pas d’impact sur les performances des applications de production. Elle prend en charge la sauvegarde et la restauration des disques du système d’exploitation et des données (y compris des disques partagés), qu’ils soient ou non actuellement attachés à une machine virtuelle Azure en cours d’exécution.

Pour plus d’informations sur la sauvegarde de disques Azure, consultez [Vue d’ensemble de la sauvegarde des disques Azure](../backup/disk-backup-overview.md).

## <a name="alternative-solution-consistent-snapshots"></a>Solution alternative : captures instantanées cohérentes

Si vous ne pouvez pas utiliser Sauvegarde Azure, vous pouvez implémenter votre propre mécanisme de sauvegarde à l’aide de captures instantanées. Il est compliqué de créer des captures instantanées cohérentes pour tous les disques utilisés par une machine virtuelle, puis de répliquer ces captures instantanées vers une autre région. Pour cette raison, Azure considère qu’il est préférable d’utiliser le service Sauvegarde plutôt que de créer une solution personnalisée.

Si vous utilisez le stockage localement redondant pour les disques, vous devez répliquer les données vous-même.

Une capture instantanée est une représentation sous la forme d’un objet à un point spécifique dans le temps. Une capture instantanée entraîne la facturation de la taille incrémentielle des données qu’elle contient. Pour plus d’informations, consultez [Création d’un instantané incrémentiel pour les disques managés](disks-incremental-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Création de captures instantanées pendant l’exécution de la machine virtuelle

Même si vous pouvez prendre une capture instantanée à tout moment, si la machine virtuelle est en cours d’exécution, des données sont toujours diffusées vers les disques. Les captures instantanées peuvent contenir des opérations partielles qui étaient en cours. En outre, si plusieurs disques sont impliqués, les captures instantanées des différents disques se sont peut-être produites à différents moments. Ces scénarios peuvent conduire à un défaut de coordination des captures instantanées. Ce manque de coordination est particulièrement problématique pour les volumes agrégés par bandes dont les fichiers peuvent être endommagés si des modifications sont apportées pendant la sauvegarde.

Pour éviter cette situation, le processus de sauvegarde doit implémenter les étapes suivantes :

1.  Figez tous les disques.

1.  Videz toutes les écritures en attente.

1.  [Créez un instantané incrémentiel pour les disques managés](disks-incremental-snapshots.md) pour tous les disques.

Certaines applications Windows telles que SQL Server fournissent un mécanisme de sauvegarde coordonné via le service VSS (Volume Shadow Service) pour créer des sauvegardes cohérentes entre les applications. Sur Linux, vous pouvez utiliser un outil comme *fsfreeze* pour coordonner les disques. Cet outil fournit des sauvegardes cohérentes au niveau des fichiers, mais aucune capture instantanée cohérente au niveau des applications. Ce processus étant compliqué, envisagez d’utiliser le service [Sauvegarde des disques Azure](../backup/disk-backup-overview.md) ou une solution de sauvegarde tierce qui implémente déjà cette procédure.

Le processus précédent produit une collection de captures instantanées coordonnées pour tous les disques de machine virtuelle, ce qui représente une vue dans le temps spécifique de la machine virtuelle. Il s’agit d’un point de restauration de sauvegarde pour la machine virtuelle. Vous pouvez répéter le processus à intervalles réguliers pour créer des sauvegardes périodiques. Consultez [Copier les captures instantanées vers une autre région](#copy-the-snapshots-to-another-region) pour obtenir la procédure permettant de copier les captures instantanées vers une autre région pour la récupération d’urgence.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Création de captures instantanées pendant que la machine virtuelle est hors connexion

Une autre option pour créer des sauvegardes cohérentes consiste à arrêter la machine virtuelle et à prendre des captures instantanées de chaque disque. Prendre des captures hors connexion est plus facile que de coordonner les captures instantanées d’une machine virtuelle en cours d’exécution, mais cela nécessite un temps d’arrêt de quelques minutes.

### <a name="copy-the-snapshots-to-another-region"></a>Copier les captures instantanées vers une autre région

La création de captures instantanées uniquement n’est peut-être pas suffisante pour la récupération d’urgence. Vous devez également copier les captures instantanées vers une autre région. Passez en revue les instructions dans [Copy Azure Managed Disks backups to another region with differential capability of incremental snapshots](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)

## <a name="other-options"></a>Autres options

### <a name="sql-server"></a>SQL Server

SQL Server exécuté dans une machine virtuelle a ses propres fonctionnalités intégrées permettant de sauvegarder votre base de données SQL Server vers Stockage Blob Azure ou un partage de fichiers. Pour plus d’informations, voir [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](../azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md). En plus de la sauvegarde et de la restauration, [les groupes de disponibilité SQL Server AlwaysOn](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md) peuvent gérer les réplicas secondaires de bases de données. Cela réduit considérablement le délai de reprise d’activité après sinistre.

## <a name="next-steps"></a>Étapes suivantes

Consultez [Sauvegarder des disques de machine virtuelle non managés Azure à l’aide d’instantanés incrémentiels](linux/incremental-snapshots.md).

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png