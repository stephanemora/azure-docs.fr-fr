---
title: Ré-héberger une application de Service Desk Contoso Linux sur Azure et Azure MySQL | Microsoft Docs
description: Découvrez comment Contoso ré-héberge une application Linux locale en la migrant vers des machines virtuelles Azure et Azure MySQL.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 15a429c033cfd1598dd01b5c8cd2743c397dacdb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225515"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Migration de Contoso : ré-héberger une application Linux locale vers des machines virtuelles Azure et Azure MySQL

Cet article explique comment Contoso ré-héberge localement son application de Service Desk Linux à deux niveaux (osTicket), en la migrant vers Azure et Azure MySQL.

Ce document est le huitième d’une série d’articles qui montrent comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations d’arrière-plan et des scénarios qui montrent comment configurer une infrastructure de migration et exécuter différents types de migrations. Les scénarios croissent en complexité, et nous ajouterons des articles au fil du temps.

**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Fournit une vue d’ensemble de la stratégie de migration de Contoso, de la série d’articles et des exemples d’application que nous utilisons. | Disponible
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Décrit comment Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les scénarios de migration de Contoso. | Disponible
[Article 3 : Évaluer les ressources locales](contoso-migration-assessment.md)  | Montre comment Contoso évalue son application à deux niveaux locale SmartHotel s’exécutant sur VMware. Ils évaluent les machines virtuelles de l’application avec le service [Azure Migrate](migrate-overview.md) et la base de données SQL Server de l’application avec [l’Assistant Migration de données Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponible
[Article 4 : ré-héberger sur des machines virtuelles Azure et une instance SQL Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Montre comment Contoso migre l’application SmartHotel vers Azure. Ils migrent la machine virtuelle web de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), et la base de données de l’application à l’aide du service [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) pour opérer la migration vers une instance SQL Managed Instance. | Disponible
[Article 5 : Réhéberger sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Montre comment Contoso migre son application SmartHotel vers des machines virtuelles Azure en utilisant le service Site Recovery.
[Article 6 : ré-héberger sur des machines virtuelles Azure et des groupes de disponibilité SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Montre comment Contoso migre l’application SmartHotel. Ils utilisent Site Recovery pour migrer les machines virtuelles de l’application, et Database Migration Service pour migrer la base de données de l’application vers un groupe de disponibilité SQL Server. | Disponible
[Article 7 : ré-héberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Montre comment Contoso migre son application osTicket Linux vers des machines virtuelles Azure IaaS à l’aide d’Azure Site Recovery.
Article 8 : ré-héberger une application Linux sur des machines virtuelles Azure et Azure MySQL Server (cet article) | Montre comment Contoso migre l’application osTicket Linux. Ils utilisent Site Recovery pour la migration de machine virtuelle et MySQL Workbench pour migrer vers une instance de serveur MySQL Azure. | Disponible

Dans cet article, Contoso migre une application de Service Desk Linux Apache MySQL PHP (LAMP) (osTicket) à deux niveaux vers Azure. Si vous souhaitez utiliser cette application open source, vous pouvez la télécharger à partir de [GitHub](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec ses partenaires commerciaux pour comprendre le résultat qu’ils souhaitent obtenir :

- **Répondre à la croissance** : Contoso étant en croissance, son infrastructure et ses systèmes locaux subissent une pression.
- **Limiter les risques** : l’application de Service Desk est vitale pour l’activité de Contoso. L’objectif est de la migrer vers Azure sans risque.
- **Étendre** : Contoso ne souhaite pas modifier l’application dans l’immédiat. Ils souhaitent simplement qu’elle reste stable.


## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a épinglé les objectifs de cette migration, afin de déterminer la meilleure méthode de migration :

- Après la migration, l’application dans Azure devra offrir les mêmes performances qu’aujourd’hui dans l’environnement VMWare local.  L’application restera tout aussi vitale dans le cloud que localement. 
- Contoso ne souhaite pas investir dans cette application.  Elle est importante pour l’entreprise mais, dans sa forme actuelle, le seul objectif est de la migrer vers le cloud en toute sécurité.
- Après avoir effectué quelques migrations d’application Windows, Contoso souhaite apprendre à utiliser une infrastructure basée sur Linux dans Azure.
- Contoso souhaite réduire au minimum les tâches d’administration de base de données après la migration de l’application vers le cloud.

## <a name="proposed-architecture"></a>Architecture proposée

Dans ce scénario :

- L’application est hiérarchisée sur deux machines virtuelles (OSTICKETWEB et OSTICKETMYSQL).
- Les machines virtuelles sont situées sur un hôte VMware ESXi **contosohost1.contoso.com** (version 6.5).
- L’environnement VMware est géré par le serveur vCenter Server 6.5 (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- Contoso dispose d’un centre de données local (contoso-datacenter), avec un contrôleur de domaine local (**contosodc1**).
- L’application de niveau web sur OSTICKETWEB sera migrée vers une machine virtuelle Azure IaaS.
- La base de données de l’application sera migrée vers le service PaaS Azure Database pour MySQL.
- Dans la mesure où ils migrent une charge de travail de production, les ressources résideront dans le groupe de ressources de production **ContosoRG**.
- Les ressources seront répliquées vers la région primaire (USA Est 2), et placées dans le réseau de production (VNET-PROD-EUS2) :
    - La machine virtuelle web résidera dans le sous-réseau frontal (PROD-FE-EUS2).
    - L’instance de base de données résidera dans le sous-réseau de la base de données (PROD-DB-EUS2).
- La base de données de l’application sera migrée vers le serveur MySQL Azure à l’aide d’outils de MySQL.
- Les machines virtuelles locales dans le centre de données Contoso seront désaffectées une fois la migration terminée.


![Architecture du scénario](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>Processus de migration

Contoso va effectuer le processus de migration comme suit :

Pour migrer la machine virtuelle web :

1. Dans un premier temps, Contoso configure les infrastructures Azure et locale nécessaires pour déployer Site Recovery.
2. Après avoir préparé les composants Azure et locaux, ils configurent et activent la réplication pour la machine virtuelle web.
3. Une la réplication opérationnelle, ils migrent la machine virtuelle en la basculant vers Azure.

Pour migrer la base de données :

1. Contoso approvisionne une instance MySQL dans Azure.
2. Ils configurent MySQL Workbench et sauvegardent la base de données localement.
3. Ils restaurent ensuite la base de données à partir de la sauvegarde locale sur Azure.

![Processus de migration](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Services Azure

**Service** | **Description** | **Coût**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Le service orchestre et gère la migration et la récupération d’urgence pour les machines virtuelles Azure, les machines virtuelles locales et les serveurs physiques.  | Lors de la réplication vers Azure, des frais sur le Stockage Azure sont facturés.  Des machines virtuelles Azure sont créées en cas de basculement, et entraînent des frais. [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les frais et la tarification.
[Azure Database pour MySQL](https://docs.microsoft.com/azure/mysql/) | La base de données est basée sur le moteur du serveur MySQL open source. Il fournit une base de données MySQL entièrement managée et de classe Entreprise, appuyée par une communauté active, en tant que service pour le développement et le déploiement d’applications. 

 
## <a name="prerequisites"></a>Prérequis

Si vous (et Contoso) voulez exécuter ce scénario, voici ce qu’il vous faut.

**Configuration requise** | **Détails**
--- | ---
**Abonnement Azure** | Vous devriez avoir déjà créé un abonnement dans le cadre d’articles précédents de cette série. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur.<br/><br/> S’il vous faut plus d’autorisations granulaires, consultez [cet article](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastructure Azure** | Contoso configure son infrastructure Azure comme décrit dans [Infrastructure Azure pour la migration](contoso-migration-infrastructure.md).<br/><br/> Apprenez-en davantage sur les configurations de [réseau](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) et de [stockage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) requises pour Site Recovery.
**Serveurs locaux** | Votre serveur vCenter local doit exécuter la version 5.5, 6.0 ou 6.5<br/><br/> Un hôte ESXi qui exécute la version 5.5, 6.0 ou 6.5<br/><br/> Une ou plusieurs machines virtuelles VMware exécutées sur l’hôte ESXi.
**Machines virtuelles locales** | [Passez en revue les exigences de machine virtuelle Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) qui sont prises en charge pour la migration avec Site Recovery.<br/><br/> Vérifiez les [systèmes de fichiers et de stockage Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage) pris en charge.<br/><br/> Les machines virtuelles doivent répondre aux [exigences Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Étapes du scénario

Voici comment Azure va effectuer la migration :

> [!div class="checklist"]
> * **Étape 1 : préparer Azure pour Site Recovery** : il s’agit de créer un compte de stockage Azure pour accueillir les données répliquées, et de créer un coffre Recovery Services.
> * **Étape 2 : préparer une machine virtuelle VMware locale pour Site Recovery** : il s’agit de préparer des comptes pour la détection de machine virtuelle et l’installation d’agents, et de préparer la connexion aux machines virtuelles Azure après basculement.
 * **Étape 3 : approvisionner la base de données** : dans Azure, il faut approvisionner une instance de base de données MySQL Azure.
> * **Étape 4 : répliquer les machines virtuelles** : il s’agit de configurer les environnements source et cible de Site Recovery ainsi qu’une stratégie de réplication, puis de commencer à répliquer des machines virtuelles sur le stockage Azure.
> * **Étape 5 : migrer la base de données** : il s’agit de configurer la migration avec des outils de MySQL.
> * **Étape 6 : migrer des machines virtuelles avec Site Recovery** : il s’agit d’exécuter un test de basculement pour s’assurer que tout fonctionne correctement, puis d’opérer un basculement complet pour migrer les machines virtuelles vers Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Étape 1 : préparer Azure pour le service Site Recovery

Contoso a besoin de quelques composants Azure pour Site Recovery :

- Un réseau virtuel dans lequel les ressources basculées se trouvent (Contoso utilisera la réseau virtuel de production déjà déployé).
- Un nouveau compte de stockage Azure pour accueillir les données répliquées. 
- Un coffre Recovery Services dans Azure.

Contoso ayant déjà créé le réseau virtuel durant le [déploiement de l’infrastructure Azure](contoso-migration-infrastructure.md), il suffit de créer un compte de stockage et un coffre.


1. Contoso crée un compte de stockage Azure (**contosovmsacc20180528**) dans la région USA Est 2.

    - Le compte de stockage doit se trouver dans la même région que le coffre Recovery Services.
    - Contoso utilise un compte à usage général, avec stockage standard et réplication de stockage localement redondant.

    ![Stockage Site Recovery](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. Avec le réseau et le compte de stockage en place, Contoso crée un coffre (ContosoMigrationVault) et le placer dans le groupe de ressources **ContosoFailoverRG**, dans la région USA Est 2 principale.

    ![Coffre Recovery Services](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**Besoin de plus d’aide ?**

[Apprenez-en davantage sur la](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) configuration d’Azure pour Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Étape 2 : préparer la machine virtuelle VMware locale pour Site Recovery

Contoso prépare l’infrastructure VMware locale comme suit :

- Ils créent un compte sur le serveur vCenter pour automatiser la détection de machine virtuelle.
- Ils créent un compte permettant l’installation automatique du service Mobilité sur les machines virtuelles VMware à répliquer.
- Ils préparent les machines virtuelles locales afin qu’elles puissent se connecter aux machines virtuelles Azure une fois celles-ci créées après la migration.


### <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique

Site Recovery doit pouvoir accéder aux serveurs VMware pour :

- Détectez automatiquement les machines virtuelles. Au moins un compte en lecture seule est nécessaire.
- Orchestrer la réplication, le basculement et la restauration automatique. Il vous faut un compte qui peut exécuter des opérations telles que la création et la suppression de disques, et l’allumage de machines virtuelles.

Contoso configure le compte comme suit :

1. Contoso crée un rôle au niveau du serveur vCenter.
2. Contoso attribue ensuite à ce rôle les autorisations requises.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Préparer un compte pour l’installation du service Mobilité

Le service Mobilité doit être installé sur chaque machine virtuelle que Contoso souhaite migrer.

- Site Recovery peut faire une installation push automatique de ce composant lorsque vous activez la réplication pour les machines virtuelles.
- Pour l’installation automatique. Site Recovery a besoin d’un compte disposant des autorisations d’accès à la machine virtuelle. 
- Les détails du compte sont entrés lors de la configuration de la réplication. 
- Le compte peut être un compte de domaine ou local pour autant qu’il dispose des autorisations d’installation.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer la connexion aux machines virtuelles Azure après le basculement

Après le basculement vers Azure, Contoso souhaite pouvoir se connecter aux machines virtuelles Azure. Pour ce faire, ils doivent faire quelques opérations : 

- Pour accéder via Internet, ils activent SSH sur la machine virtuelle Linux locale avant la migration.  Pour Ubuntu, cette opération peut être effectuée à l’aide de la commande suivante : **Sudo apt-get ssh installer -y**.
- Après basculement, ils doivent consulter les **diagnostics de démarrage** pour afficher une capture d’écran de la machine virtuelle.
- Si cela ne fonctionne pas, il doivent vérifier que la machine virtuelle est en cours d’exécution et consulter ces [conseils de dépannage](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**Besoin de plus d’aide ?**

- [Apprenez-en davantage sur](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) la création et l’attribution d’un rôle pour la découverte automatique.
- [Apprenez-en davantage sur](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) la création d’un compte pour une installation push du service Mobilité.


## <a name="step-3-provision-azure-database-for-mysql"></a>Étape 3 : approvisionner Azure Database pour MySQL

Contoso approvisionne une instance de base de données MySQL dans la région USA Est 2 principale.

1. Dans le portail Azure, ils créent une base de données Azure Database pour MySQL. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. Ils ajoutent le nom **contosoosticket** pour la base de données Azure. Ils ajoutent la base de données au groupe de ressources de production **ContosoRG**, puis spécifient les informations d’identification pour celle-ci.
3. La base de données MySQL locale étant la version 5.7, ils sélectionnent cette version à des fins de compatibilité. Ils utilisent les tailles par défaut, qui correspondent à leurs exigences en matière de base de données.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. Pour **Backup Redundancy Options (Options de redondance de sauvegarde)**, Contoso sélectionne **Geo-Redundant (Géoredondant)**. Cette option leur permet de restaurer la base de données dans leur région USA Centre secondaire en cas de panne. Ils ne peuvent configurer cette option que lors de l’approvisionnement de la base de données.

     ![Redondance](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. Dans le réseau **VNET-PROD-EUS2** > **Points de terminaison de service**, ils ajoutent un point de terminaison de service (un sous-réseau de base de données) pour le service SQL.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. Après avoir ajouté le sous-réseau, ils créent une règle de réseau virtuel qui permet d’accéder à partir du sous-réseau de base de données au réseau de production.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>Étape 4 : répliquer les machines virtuelles locales

Avant de pouvoir migrer la machine virtuelle web vers Azure, Contoso configure et active la réplication.

### <a name="set-a-protection-goal"></a>Définir un objectif de protection

1. Dans le coffre, sous le nom du coffre (ContosoVMVault), ils définissent un objectif de réplication (**Prise en main** > **Site Recovery** > **Préparer l’infrastructure**).
2. Il convient de spécifier que les ordinateurs sont situés localement, qu’il s’agit de machines virtuelles VMware, et qu’il faut les répliquer sur Azure.

    ![Objectif de réplication](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmer la planification d’un déploiement

Pour continuer, ils confirment que la planification du déploiement est terminée en sélectionnant **Yes, I have done it** (Oui, c’est fait). Dans ce scénario, Contoso ne migre qu’une seule machine virtuelle et n’a pas besoin de planification du déploiement.

### <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Contoso doit configurer son environnement source. Pour ce faire, à l’aide d’un modèle OVF, ils déploient un serveur de configuration Site Recovery en tant que machine virtuelle VMware locale hautement disponible. Une fois le serveur de configuration opérationnel, ils l’inscrivent dans le coffre.

Le serveur de configuration exécute plusieurs composants :

- Le composant de serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure, et gère la réplication des données.
- Le serveur de traitement qui fait office de passerelle de réplication. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure.
- De plus, le serveur de processus installe le service Mobilité sur les machines virtuelles que vous voulez répliquer et effectue la détection automatique sur les machines virtuelles VMware locales.

Contoso procède comme suit :


1. Ils téléchargent le modèle OVF à partir de **Préparer l’Infrastructure** > **Source** > **Serveur de configuration**.
    
    ![Télécharger le modèle OVF](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. Ils importent le modèle dans VMware pour créer et déployer la machine virtuelle.

    ![Modèle OVF](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. Lors de l’activation de la machine virtuelle pour la première fois, celle-ci démarre dans un environnement d’installation de Windows Server 2016. Ils acceptent le contrat de licence, puis entrent un mot de passe d’administrateur.
4. Une fois l’installation terminée, ils se connectent à la machine virtuelle en tant qu’administrateur. À la première connexion, l’outil de configuration d’Azure Site Recovery s’exécute par défaut.
5. Dans l’outil, ils spécifient un nom à utiliser pour l’inscription du serveur de configuration dans le coffre.
6. L’outil vérifie que la machine virtuelle peut se connecter à Azure.
7. Une fois la connexion établie, ils se connectent à l’abonnement Azure. Les informations d’identification doivent avoir accès au coffre dans lequel ils vont inscrire le serveur de configuration.

    ![Inscrire un serveur de configuration](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. L’outil effectue des tâches de configuration, puis redémarre.
9. Ils se reconnectent à l’ordinateur et l’Assistant de gestion du serveur de configuration démarre automatiquement.
10. Dans l’Assistant, ils sélectionnent la carte réseau qui doit recevoir le trafic de réplication. Une fois configuré, ce paramètre ne peut pas être modifié.
11. Ils sélectionnent l’abonnement, le groupe de ressources et le coffre dans lequel inscrire le serveur de configuration.

    ![coffre](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. À présent, ils téléchargent et installent MySQL Server et VMWare PowerCLI. 
13. Après la validation, ils spécifient le nom de domaine complet (FQDN) ou l’adresse IP du serveur vCenter ou de l’hôte vSphere. Ils quittent le port par défaut, puis spécifient un nom convivial pour le serveur vCenter.
14. Ils spécifient le compte qu’ils ont créés pour la détection automatique, et les informations d’identification que Site Recovery va utiliser pour installer automatiquement le service Mobilité. 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. Une fois l’inscription terminée, dans le portail Azure, Contoso vérifie que le serveur de configuration et le serveur VMware sont répertoriés sur la page **Source** dans le coffre. La détection peut prendre 15 minutes ou plus. 
15. Une fois tout en place, Site Recovery se connecte aux serveurs VMware et détecte les machines virtuelles.

### <a name="set-up-the-target"></a>Configurer la cible

Maintenant, Contoso spécifie les paramètres de réplication de la cible.

1. Dans **Préparer l’infrastructure** > **Cible**, ils sélectionnent les paramètres de la cible.
2. Site Recovery vérifie qu’il existe un compte de stockage et un réseau Azure dans la cible spécifiée.


### <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

Une fois la source et la cible configurées, Contoso est prêt à créer une stratégie de réplication.

1. Dans **Préparer l’infrastructure** > **Paramètres de réplication** > **Stratégie de réplication** >  **Créer et associer**, ils créent une stratégie **ContosoMigrationPolicy**.
2. Ils utilisent les paramètres par défaut :
    - **Seuil d'objectif de point de récupération** : la valeur par défaut est de 60 minutes. Cette valeur définit la fréquence à laquelle les points de récupération sont créés. Une alerte est générée lorsque la réplication continue dépasse cette limite.
    - **Rétention des points de récupération**. La valeur par défaut de 24 heures. Cette valeur spécifie la durée de la fenêtre de rétention pour chaque point de récupération. Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre.
    - **Fréquence des captures instantanées de cohérence d’application**. La valeur par défaut est d’une heure. Cette valeur spécifie la fréquence à laquelle les captures instantanées de cohérence d’application sont créées.
 
        ![Créer une stratégie de réplication](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. La stratégie est automatiquement associée au serveur de configuration. 

    ![Associer la stratégie de réplication](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**Besoin de plus d’aide ?**

- Une procédure pas à pas complète de toutes ces étapes est décrite dans [Configurer la récupération d’urgence vers Azure pour des machines virtuelles VMware locales](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Des instructions détaillées sont disponibles pour vous aider à [configurer l’environnement source](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), à [déployer le serveur de configuration](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) et à [configurer les paramètres de réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Apprenez-en davantage](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sur l’agent invité Azure pour Linux.

### <a name="enable-replication-for-the-web-vm"></a>Activer la réplication pour la machine virtuelle web

Maintenant, Contoso peut commence à répliquer la machine virtuelle **OSTICKETWEB**.

1. Dans **Répliquer l’application** > **Source** > **+Répliquer**, ils sélectionnent les paramètres de la source.
2. Ils indiquent qu’ils souhaitent activer des machines virtuelles, puis sélectionnent les paramètres de la source, dont le serveur vCenter et le serveur de configuration.

    ![Activer la réplication](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. À présent, ils spécifient les paramètres de la cible. Ceux-ci incluent le groupe de ressources et le réseau dans lequel la machine virtuelle Azure se trouvera après le basculement, ainsi que le compte de stockage dans lequel les données répliquées seront stockées. 

     ![Activer la réplication](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Contoso sélectionne la machine virtuelle **OSTICKETWEB** pour la réplication. 

    ![Activer la réplication](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. Dans les propriétés de la machine virtuelle, ils sélectionnent le compte à utiliser pour installer automatiquement le service Mobilité sur la machine virtuelle.

     ![Service Mobilité](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, ils vérifient que la stratégie de réplication appliquée est correcte, puis sélectionnent **Activer la réplication**. Le service Mobilité sera installé automatiquement.
6.  Ils suivent la progression de la réplication dans **Travaux**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.


**Besoin de plus d’aide ?**

La procédure pas à pas complète de toutes ces étapes est décrite dans [Activer la réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>Étape 5 : migrer la base de données

Contoso va migrer la base de données en utilisant une sauvegarde suivie d’une restauration, avec des outils de MySQL. Ils installent MySQL Workbench, sauvegardent la base de données à partir de OSTICKETMYSQL, puis la restaurent sur Azure Database pour MySQL.

### <a name="install-mysql-workbench"></a>Installer MySQL Workbench

1. Contoso vérifie les [conditions préalables et téléchargements de MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Ils installent MySQL Workbench pour Windows conformément aux [instructions d’installation](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. Dans MySQL Workbench, ils créent une connexion MySQL à OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. Ils exportent la base de données en tant que **osticket** dans un fichier autonome local.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. Une fois la base de données sauvegardée localement, ils créent une connexion à l’instance Azure Database pour MySQL.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. À présent, Contoso peut importer (restaurer) la base de données dans l’instance MySQL Azure à partir du fichier autonome. Un nouveau schéma (osticket) est créé pour l’instance.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>Étape 6 : migrer les machines virtuelles avec Site Recovery

Contoso exécute un test rapide de basculement, puis migre la machine virtuelle.

### <a name="run-a-test-failover"></a>Exécuter un test de basculement

L’exécution d’un test de basculement permet de vérifier que tout fonctionne comme prévu avant la migration. 

1. Contoso exécute un test de basculement jusqu’au dernier point dans le temps disponible (**Dernier point traité**).
2. Ils sélectionnent **Shut down machine before beginning failover** (Arrêter la machine avant de commencer le basculement), de façon à ce que Site Recovery tente d’arrêter la machine virtuelle source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. 
3. Le test de basculement est exécuté : 

    - Une vérification des prérequis est effectuée pour garantir que toutes les conditions nécessaires pour la migration sont en place.
    - Le basculement traite les données pour permettre la création d’une machine virtuelle Azure. Si vous avez sélectionné le dernier point de récupération, un point de récupération est créé à partir des données.
    - Une machine virtuelle Azure est créée en utilisant les données traitées à l’étape précédente.

3. Une fois le basculement terminé, la machine virtuelle Azure répliquée apparaît dans le portail Azure. Contoso vérifie que la machine virtuelle est de la taille appropriée, qu’elle est connectée au réseau approprié et qu’elle est en cours d’exécution. 
4. Après ces vérifications, ils nettoient le basculement, puis consignent et enregistrent les observations éventuelles.

### <a name="migrate-the-vm"></a>Migrer la machine virtuelle

Pour migrer la machine virtuelle, Contoso crée un plan de récupération qui inclut la machine virtuelle, puis bascule le plan sur Azure.

1. Contoso crée un plan et y ajoute **OSTICKETWEB**.

    ![Plan de récupération](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. Ils exécutent un basculement sur le plan. Ils sélectionnent le dernier point de récupération et spécifient que Site Recovery doit essayer d’arrêter la machine virtuelle locale avant de déclencher le basculement. Ils peuvent suivre la progression du basculement sur la page **Travaux**.

    ![Basculement](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. Pendant le basculement, le serveur vCenter émet des commandes pour arrêter les deux machines virtuelles en cours d’exécution sur l’hôte ESXi.

    ![Basculement](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. Après le basculement, Contoso vérifie que la machine virtuelle Azure apparaît comme prévu dans le portail Azure.

    ![Basculement](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. Après vérification de la machine virtuelle, ils achèvent la migration. Cela met fin à la réplication de la machine virtuelle et arrête la facturation Site Recovery pour celle-ci.

    ![Basculement](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**Besoin de plus d’aide ?**

- [Apprenez-en davantage sur](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) l’exécution d’un test de basculement. 
- [Découvrez](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) comment créer un plan de récupération.
- [Découvrez](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) comment basculer vers Azure.


### <a name="connect-the-vm-to-the-database"></a>Connecter la machine virtuelle à la base de données

La dernière étape du processus de migration pour Contoso consiste à mettre à jour la chaîne de connexion de l’application pour qu’elle pointe vers Azure Database pour MySQL. 

1. Ils établissent une connexion SSH à la machine virtuelle OSTICKETWEB à l’aide de Putty ou d’un autre client SSH. La machine virtuelle étant privée, ils établissent la connexion en utilisant l’adresse IP privée.

    ![Connecter à la base de données](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![Connecter à la base de données](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. Ils mettent à jour les paramètres afin que la machine virtuelle **OSTICKETWEB** puisse communiquer avec la base de données **OSTICKETMYSQL**. Actuellement, la configuration est codée en dur avec l’adresse IP locale 172.16.0.43.

    **Avant la mise à jour**
    
    ![Mettre à jour l’adresse IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **Après la mise à jour**
    
    ![Mettre à jour l’adresse IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![Mettre à jour l’adresse IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. Ils redémarrent le service avec la commande **systemctl restart apache2**.

    ![Redémarrer](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. Enfin, ils mettent à jour les enregistrements DNS pour **OSTICKETWEB** sur l’un des contrôleurs de domaine Contoso.

    ![Mettre à jour le DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>Nettoyer après la migration

Une fois la migration terminée, les niveaux de l’application osTicket s’exécutent sur des machines virtuelles Azure.

Contoso doit à présent procéder comme suit : 
- Supprimer les machines virtuelles VMware du stock vCenter
- Supprimer les machines virtuelles locales des travaux de sauvegarde locale.
- Mettre à jour la documentation interne afin qu’elle indique les nouveaux emplacements et adresses IP. 
- Passer en revue toutes les ressources qui interagissent avec les machines virtuelles locales, et mettre à jour les paramètres ou la documentation appropriés afin de refléter la nouvelle configuration.
- Contoso a utilisé le service Azure Migrate avec le mappage de dépendance pour évaluer la machine virtuelle **OSTICKETWEB** pour la migration. Ils doivent maintenant supprimer de la machine virtuelle les agents (Microsoft Monitoring Agent/Agent de dépendances) qu’ils ont installés à cet fin.

## <a name="review-the-deployment"></a>Examiner le déploiement

L’application étant en cours d’exécution, Contoso doit à présent rendre son infrastructure entièrement opérationnelle et la sécuriser.

### <a name="security"></a>Sécurité

L’équipe de sécurité de Contoso examine la machine virtuelle et la base de données afin d’identifier d’éventuels problèmes de sécurité.

- Ils examinent les groupes de sécurité réseau (NSG) pour la machine virtuelle, afin de contrôler l’accès. Ils utilisent des groupes de sécurité réseau pour s’assurer que seul le trafic autorisé vers l’application puisse passer.
- Ils prennent en considération la sécurisation des données sur les disques de machine virtuelle à l’aide d’Azure Disk Encryption et de KeyVault.
- La communication entre la machine virtuelle et l’instance de base de données n’est pas configurée pour SSL. Ils doivent faire cela pour s’assurer que le trafic de base de données ne puisse pas être piraté.

[Apprenez-en davantage](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sur les pratiques de sécurité pour les machines virtuelles.

### <a name="backups"></a>Sauvegardes

- Contoso va sauvegarder les données sur la machine virtuelle à l’aide du service Sauvegarde Azure. [Plus d’informations](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- Ils n’ont pas besoin de configurer la sauvegarde de la base de données. Azure Database pour MySQL crée automatiquement les sauvegardes et les stocke. Comme ils ont choisi d’utiliser la géo-redondance pour la base de données, celle-ci est résiliente et prête pour la production.

### <a name="licensing-and-cost-optimization"></a>Gestion des licences et optimisation des coûts

- Après le déploiement des ressources, Contoso affecte des balises Azure, conformément aux décisions prises pendant le déploiement de l’[infrastructure Azure](contoso-migration-infrastructure.md#set-up-tagging).
- Il n’y a aucun problème de licence pour les serveurs Ubuntu de Contoso.
- Contoso va activer Azure Cost Management sous licence de Cloudyn, une filiale de Microsoft. Il s’agit d’une solution de gestion des coûts multicloud qui aide à utiliser et à gérer Azure ainsi que d’autres ressources cloud.  [Apprenez-en davantage](https://docs.microsoft.com/azure/cost-management/overview) sur Azure Cost Management.


## <a name="next-steps"></a>Étapes suivantes

Dans ce scénario, nous vous avons montré comment Contoso a effectué le ré-hébergement final. Ils ont migré la machine virtuelle frontale de l’application osTicket Linux locale vers une machine virtuelle Azure, et la base de données de l’application vers une instance MySQL Azure.

Dans l’ensemble suivant de didacticiels de la série relative à la migration, nous allons vous montrer comment Contoso a effectué un ensemble plus complexe de migrations, impliquant une refactorisation d’application, plutôt que de simples migrations lift-and-shift.
