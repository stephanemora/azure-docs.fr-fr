---
title: Configurer la récupération après sinistre pour SQL Server avec SQL Server et Azure Site Recovery | Microsoft Docs
description: Cet article explique comment configurer la récupération après sinistre pour SQL Server à l’aide de SQL Server et d’Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 1c44b10b54a5f58dff1aecf36c3633cc8ffbd8f0
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491775"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Configurer la récupération après sinistre pour SQL Server

Cet article décrit comment protéger le serveur SQL Server principal d’une application en combinant les technologies de continuité d’activité et de récupération d’urgence (BCDR) de SQL Server et [Azure Site Recovery](site-recovery-overview.md).

Avant de commencer, veillez à bien comprendre les fonctionnalités de reprise d’activité de SQL Server, notamment le clustering de basculement, les groupes de disponibilité AlwaysOn, la mise en miroir de bases de données, la copie des journaux de transaction, la géoréplication active et les groupe de basculement automatique.

## <a name="dr-recommendation-for-integration-of-sql-server-bcdr-technologies-with-site-recovery"></a>Recommandations pour intégrer les technologies BCDR de SQL Server à Site Recovery

Le choix d’une technologie BCDR pour la récupération des serveurs SQL doit être basé sur vos besoins en termes de RTO et de RPO, conformément au tableau ci-dessous. Une fois ce choix effectué, Site Recovery peut être intégré à l’opération de basculement de cette technologie pour orchestrer la récupération de toute votre application.

**Type de déploiement** | **Technologie BCDR** | **RTO attendu pour SQL** | **RPO attendu pour SQL** |
--- | --- | --- | ---
SQL Server sur une machine virtuelle Azure IaaS ou localement| **[Groupe de disponibilité Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)** | Équivalent au temps nécessaire pour faire du réplica secondaire le réplica principal. | La réplication étant asynchrone vers le réplica secondaire, il y a une perte de données.
SQL Server sur une machine virtuelle Azure IaaS ou localement| **[Clustering de basculement (instance de cluster de basculement AlwaysOn)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017)** | Équivalent au temps nécessaire pour effectuer le basculement entre les nœuds. | Il utilise le stockage partagé ; la même vue de l’instance de stockage est donc disponible lors du basculement.
SQL Server sur une machine virtuelle Azure IaaS ou localement| **[Mise en miroir de bases de données (mode hautes performances)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017)** | Équivalent au temps nécessaire pour forcer le service, qui utilise le serveur miroir en tant que serveur de secours actif. | La réplication est asynchrone. La base de données miroir peut être un peu en retard par rapport à la base de données principale. L’intervalle est généralement faible, mais peut devenir important si le système du serveur principal ou du serveur miroir est soumis à une charge importante.<br></br>La copie des journaux de transaction peut être un supplément à la mise en miroir de bases de données, et constitue une alternative favorable à la mise en miroir de bases de données asynchrone.
SQL en tant que PaaS sur Azure<br></br>(pools élastiques, serveurs de base de données SQL) | **Géoréplication active** | 30 secondes, une fois qu’elle est déclenchée.<br></br>Lorsque le basculement est activé pour l’une des bases de données secondaires, toutes les autres bases de données secondaires sont automatiquement liées à la nouvelle base de données primaire. | RPO de cinq secondes<br></br>La géoréplication active tire parti de la technologie Always On de SQL Server pour répliquer de manière asynchrone les transactions validées sur la base de données primaire vers une base de données secondaire à l’aide de l’isolement de capture instantanée. <br></br>Il est garanti que les données secondaires n’auront jamais de transactions partielles.
SQL en tant que PaaS configuré avec la géoréplication active sur Azure<br></br>(SQL Database Managed Instance, pools élastiques, serveurs de bases de données SQL) | **Groupes de basculement automatique** | RTO de 1 heure | RPO de 5 secondes<br></br>Les groupes de basculement automatique fournissent la sémantique de groupe en plus de la géo-réplication active. Cependant, le même mécanisme de réplication asynchrone est utilisé.
SQL Server sur une machine virtuelle Azure IaaS ou localement| **Réplication avec Azure Site Recovery** | Généralement moins de 15 minutes. [Consultez cet article](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) pour en savoir plus sur le contrat SLA RTO fourni par Azure Site Recovery. | Une heure pour la cohérence des applications et cinq minutes pour la cohérence en cas d’incident. 

> [!NOTE]
> Voici quelques considérations importantes lors de la protection des charges de travail SQL avec Azure Site Recovery :
> * Azure Site Recovery est indépendant des applications. Par conséquent, toute version de SQL server qui est déployée sur un système d’exploitation pris en charge peut être protégée par Azure Site Recovery. [Plus d’informations](vmware-physical-azure-support-matrix.md#replicated-machines)
> * Vous pouvez choisir d’utiliser Site Recovery pour n’importe quel déploiement d’une infrastructure Azure, Hyper-V, VMware ou physique. Veuillez suivre les [instructions](site-recovery-sql.md#how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2) fournies à la fin du document sur la façon de protéger le cluster SQL Server avec Azure Site Recovery.
> * Vérifiez que le taux de modification des données (octets d’écriture par seconde) observé sur l’ordinateur est dans les [limites Site Recovery](vmware-physical-azure-support-matrix.md#churn-limits). Pour les ordinateurs Windows, vous le voyez sous l’onglet Performances du Gestionnaire des tâches. Observez la vitesse d’écriture pour chaque disque.
> * Azure Site Recovery prend en charge la réplication des instances de cluster de basculement sur les espaces de stockage direct. [Plus d’informations](azure-to-azure-how-to-enable-replication-s2d-vms.md)
 

## <a name="disaster-recovery-of-application"></a>Reprise d’activité de l’application

**Azure Site Recovery orchestre le test de basculement et le basculement de toute votre application à l’aide de plans de récupération.** 

Il existe quelques prérequis pour s’assurer qu’un plan de récupération est entièrement personnalisé et adapté à vos besoins. Tout déploiement de SQL Server a généralement besoin d’un annuaire Active Directory. Il a également besoin d’une connectivité pour votre couche application.

### <a name="step-1-set-up-active-directory"></a>Étape 1 : Configurer Active Directory

Configurez Active Directory sur le site de récupération secondaire afin que SQL Server fonctionne correctement.

* **Petite entreprise** : si vous avez un petit nombre d’applications et un seul contrôleur de domaine pour le site local et que vous souhaitez basculer l’ensemble du site, nous vous recommandons d’utiliser Site Recovery pour répliquer le contrôleur de domaine sur le centre de données secondaire ou sur Azure.
* **Moyenne ou grande entreprise** : si vous avez un grand nombre d’applications et une forêt Active Directory et que vous souhaitez effectuer un basculement par application ou charge de travail, nous vous recommandons de configurer un contrôleur de domaine supplémentaire dans le centre de données secondaire ou dans Azure. Si vous utilisez des groupes de disponibilité AlwaysOn pour effectuer une récupération sur un site distant, nous vous recommandons de configurer un contrôleur de domaine supplémentaire sur le site secondaire ou dans Azure, qui sera utilisé pour l’instance SQL Server récupérée.

Les instructions fournies dans cet article supposent qu’un contrôleur de domaine est disponible sur le site secondaire. [ici](site-recovery-active-directory.md) .

### <a name="step-2-ensure-connectivity-with-other-application-tiers-and-web-tier"></a>Étape 2 : Vérifier la connectivité avec d’autres couches application et avec la couche web

Vérifiez que, une fois la couche base de données opérationnelle dans la région Azure cible, vous disposez d’une connectivité avec la couche application et la couche web. Les étapes nécessaires doivent être effectuées à l’avance afin de valider la connectivité avec le test de basculement.

Découvrez comment intégrer les considérations en matière de connectivité à la conception des applications grâce à quelques exemples :
* [Concevoir une application pour la reprise d’activité dans le cloud](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Stratégies de reprise d’activité pour les pools élastiques](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-integrate-with-always-on-active-geo-replication-or-auto-failover-groups-for-application-failover"></a>Étape 3 : Intégrer avec AlwaysOn, la géoréplication active ou des groupes de basculement automatique pour le basculement d’application

Les technologies BCDR Always On, géoréplication active et groupes de basculement automatique ont des réplicas secondaires de SQL server en cours d’exécution dans la région Azure cible. Ainsi, la première étape de votre basculement d’application consiste à faire de ce réplica le réplica principal (en supposant que vous disposez déjà d’un contrôleur de domaine dans le réplica secondaire). Cette étape ne sera peut-être pas nécessaire si vous choisissez d’effectuer un basculement automatique. Vous devez faire basculer votre couche web ou application une fois le basculement de base de données terminé uniquement.

> [!NOTE] 
> Si vous avez protégé les ordinateurs SQL avec Azure Site Recovery, il vous suffit de créer un groupe de récupération de ces ordinateurs et d’ajouter leur basculement au plan de récupération.

[Créez un plan de récupération](site-recovery-create-recovery-plans.md) avec les machines virtuelles des couches application et web. Effectuez les étapes ci-dessous pour ajouter le basculement de la couche base de données :

1. Importez les scripts sur votre compte Azure Automation. Ce dernier contient les scripts pour le basculement du groupe de disponibilité SQL dans une [machine virtuelle Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) et une [machine virtuelle classique](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Déployer sur Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Ajoutez ASR-SQL-FailoverAG comme une action préalable du premier groupe dans le plan de récupération.

1. Suivez les instructions fournies dans le script pour créer une variable d’automatisation pour fournir le nom des groupes de disponibilité.

### <a name="step-4-conduct-a-test-failover"></a>Étape 4 : Effectuer un test de basculement

Certaines technologies BCDR, telles que SQL Always On, ne prennent pas en charge les tests de basculement de manière native. Nous vous recommandons par conséquent d’adopter l’approche suivante **uniquement lors de l’intégration avec ces technologies** :

1. Configurer la [Sauvegarde Azure](../backup/backup-azure-arm-vms.md) sur la machine virtuelle qui héberge le réplica du groupe de disponibilité dans Azure.

1. Avant de déclencher le test de basculement du plan de récupération, récupérez la machine virtuelle à partir de la sauvegarde effectuée à l’étape précédente.

    ![Restauration à partir de Sauvegarde Azure](./media/site-recovery-sql/restore-from-backup.png)

1. [Forcez un quorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) dans la machine virtuelle restaurée à partir d’une sauvegarde.

1. Mettez à jour l’adresse IP de l’écouteur avec une adresse IP disponible dans le réseau de test de basculement.

    ![Mettre à jour l’adresse IP de l’écouteur](./media/site-recovery-sql/update-listener-ip.png)

1. Mettez l’écouteur en ligne.

    ![Mettre l’écouteur en ligne](./media/site-recovery-sql/bring-listener-online.png)

1. Créez un équilibrage de charge avec une adresse IP créée sous le pool d’adresses IP de serveur frontal correspondant à chaque écouteur du groupe de disponibilité et avec la machine virtuelle SQL ajoutée dans le pool principal.

     ![Créer un équilibrage de charge – pool d’adresses IP frontal](./media/site-recovery-sql/create-load-balancer1.png)

    ![Créer un équilibrage de charge – pool principal](./media/site-recovery-sql/create-load-balancer2.png)

1. Ajoutez le basculement de votre couche application, suivie de la couche web dans ce plan de récupération dans les groupes de récupération suivants. 
1. Effectuez un test de basculement du plan de récupération afin de tester le basculement de bout en bout de l’application.

## <a name="steps-to-do-a-failover"></a>Procédure de basculement

Après avoir ajouté le script au plan de récupération à l’Étape 3 et l’avoir validé grâce à un test de basculement avec une approche spécialisée à l’Étape 4, vous pouvez procéder au basculement du plan de récupération créé à l’Étape 3.

Notez que les étapes de basculement pour les couches application et web doivent être les mêmes dans les plans de récupération de basculement et de test de basculement.

## <a name="how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>Comment faire pour protéger un cluster SQL Server (Standard Edition/SQL Server 2008 R2) ?

Pour un cluster exécutant SQL Server Standard Edition ou SQL Server 2008 R2, nous vous recommandons d’utiliser la réplication Site Recovery pour protéger SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure vers Azure et local vers Azure

Site Recovery ne prend pas en charge le cluster invité lors de la réplication vers une région Azure. De même, SQL Server n’inclut aucune solution de récupération d'urgence à faible coût dans l’édition Standard. Dans ce scénario, nous vous recommandons de protéger le cluster SQL Server sur un serveur SQL Server autonome à l’emplacement principal, et de le récupérer à l’emplacement secondaire.

1. Configurez une instance SQL Server autonome supplémentaire dans la région Azure principale ou sur le site local.
1. Configurez l’instance comme une copie miroir des bases de données que vous souhaitez protéger. Configurez la mise en miroir en mode haute sécurité.
1. Configurez Site Recovery sur le site local pour ([Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [serveurs physiques/machines virtuelles VMware)](site-recovery-vmware-to-azure-classic.md).
1. Utilisez la réplication Site Recovery pour répliquer la nouvelle instance SQL Server sur le site secondaire. Comme il s’agit d’une copie miroir hautement sécurisée, elle sera synchronisée avec le cluster principal, mais elle sera répliquée à l’aide de la réplication Site Recovery.


![Cluster standard](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considérations en matière de restauration automatique

Pour les clusters SQL Server Standard, la restauration automatique après un basculement non planifié nécessite une sauvegarde et une restauration SQL Server à partir de l’instance miroir sur le cluster d’origine, puis le rétablissement de la copie miroir.

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

### <a name="how-does-sql-get-licensed-when-protected-with-azure-site-recovery"></a>Comment SQL obtient une licence en cas de protection avec Azure Site Recovery ?
La réplication Azure Site Recovery pour SQL Server est couverte par l’avantage Récupération d’urgence de la Software Assurance, pour l’ensemble des scénarios Azure Site Recovery (reprise d’activité d’un environnement local vers Azure ou reprise d’activité entre plusieurs régions IaaS Azure). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-azure-site-recovery-support-my-sql-version"></a>Azure Site Recovery prendra-t-il en charge ma version de SQL ?
Azure Site Recovery est indépendant des applications. Par conséquent, toute version de SQL server qui est déployée sur un système d’exploitation pris en charge peut être protégée par Azure Site Recovery. [En savoir plus](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus](site-recovery-components.md) sur l’architecture de Site Recovery.
* Pour les serveurs SQL dans Azure, apprenez-en davantage sur [solutions de haute disponibilité](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) pour la récupération dans la région Azure secondaire.
* Pour SQL Database dans Azure, apprenez-en davantage sur les options de [continuité d’activité](../sql-database/sql-database-business-continuity.md) et de [haute disponibilité](../sql-database/sql-database-high-availability.md) pour la récupération dans la région Azure secondaire.
* Pour les ordinateurs SQL server locaux, [apprenez-en davantage](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) sur les options de haute disponibilité pour la récupération dans Machines virtuelles Azure.
