---
title: Migrer et ré-héberger une application Linux locale vers des machines virtuelles Azure | Microsoft Docs
description: Découvrez comment Contoso ré-héberge une application Linux locale en la migrant vers des machines virtuelles Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: raynew
ms.openlocfilehash: dc2e116e9e6bb60da4ba9fecb308ad0f9d7c127b
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126792"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Migration de Contoso : ré-héberger une application Linux locale vers des machines virtuelles Azure

Cet article explique comment Contoso ré-héberge une application de Service Desk Linux locale (**osTicket**) vers des machines virtuelles Azure IaaS.

Ce document fait partie d’une série d’articles décrivant comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations contextuelles et des scénarios qui montrent comment configurer une infrastructure de migration et exécuter différents types de migrations. Les scénarios augmentent en complexité. Nous ajouterons des articles au fil du temps.

**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Vue d’ensemble de la série d’articles, de la stratégie de migration de Contoso et des exemples d’applications utilisés dans la série. | Disponible
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration de la série. | Disponible
[Article 3 : Évaluer les ressources locales à migrer vers Azure](contoso-migration-assessment.md)  | Contoso évalue son application locale SmartHotel qui s’exécute sur VMware. Contoso évalue les machines virtuelles de l’application à l’aide du service Azure Migrate et la base de données SQL Server de l’application à l’aide de l’Assistant Migration de données. | Disponible
[Article 4 : Réhéberger une application sur une machine virtuelle Azure et SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso exécute une migration lift-and-shift vers Azure pour son application SmartHotel locale. Contoso migre la machine virtuelle frontale de l’application à l’aide [d’Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migre la base de données de l’application vers Azure SQL Database Managed Instance à l’aide [d’Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponible  
[Article 5 : Réhéberger une application sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Contoso migre les machines virtuelles de son application SmartHotel vers des machines virtuelles Azure à l’aide du service Site Recovery. | Disponible
[Article 6 : Réhéberger une application sur des machines virtuelles Azure et dans un groupe de disponibilité SQL Server AlwaysOn](contoso-migration-rehost-vm-sql-ag.md) | Contoso migre l’application SmartHotel. Contoso utilise Site Recovery pour migrer les machines virtuelles d’application. La société utilise Database Migration Service pour migrer la base de données d’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. | Disponible    
Article 7 : réhéberger une application Linux sur des machines virtuelles Azure | Contoso effectue une migration lift-and-shift de l’application osTicket Linux vers des machines virtuelles Azure à l’aide d’Azure Site Recovery. | Cet article
[Article 8 : Réhéberger une application Linux sur des machines virtuelles Azure et Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migre l’application osTicket Linux vers des machines virtuelles Azure à l’aide d’Azure Site Recovery et la base de données de l’application vers une instance Azure MySQL Server à l’aide de MySQL Workbench. | Disponible
[Article 9 : Refactoriser une application sur Azure Web Apps et Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migre l’application SmartHotel vers Azure Web App et la base de données de l’application vers une instance Azure SQL Server à l’aide de l’Assistant Migration de données. | Disponible
[Article 10 : Refactoriser une application Linux sur Azure Web Apps et Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migre son application osTicket Linux vers Azure Web App dans plusieurs régions Azure à l’aide d’Azure Traffic Manager, intégré à GitHub de façon à assurer une livraison continue. Contoso migre la base de données d’application vers une instance d’Azure Database pour MySQL. | Disponible 
[Article 11 : Refactoriser TFS sur VSTS](contoso-migration-tfs-vsts.md) | Contoso migre son déploiement local de Team Foundation Server vers Visual Studio Team Services dans Azure. | Disponible
[Article 12 : Réarchitecturer une application sur des conteneurs Azure et Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migre son application SmartHotel vers Azure. Ensuite, l’entreprise restructure la couche web de l’application comme un conteneur Windows s’exécutant dans Azure Service Fabric, et la base de données avec Azure SQL Database. | Disponible
[Article 13 : Regénérer une application dans Azure](contoso-migration-rebuild.md) | Contoso regénère son application SmartHotel à l’aide d’une série de fonctionnalités et services Azure, notamment Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services et Azure Cosmos DB. | Disponible



Dans cet article, Contoso va migrer l’application **osTicket** à deux niveaux s’exécutant sur Linux Apache MySQL PHP (LAMP) vers Azure. Les machines virtuelles de l’application seront migrées à l’aide du service Azure Site Recovery. Si vous souhaitez utiliser cette application open source, vous pouvez la télécharger à partir de [GitHub](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec des partenaires commerciaux pour comprendre le résultat qu’ils souhaitent obtenir avec cette migration :

- **Répondre à la croissance** : Contoso étant en croissance, son infrastructure et ses systèmes locaux subissent une pression.
- **Limiter les risques** : l’application de Service Desk est vitale pour l’activité de Contoso. Contoso veut la migrer vers Azure sans risque.
- **Étendre** : Contoso ne souhaite pas modifier l’application dans l’immédiat. L’entreprise veut simplement qu’elle soit stable.


## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a épinglé les objectifs de cette migration, afin de déterminer la meilleure méthode de migration :

- Après la migration, l’application dans Azure devra offrir les mêmes performances qu’aujourd’hui dans l’environnement VMWare local.  L’application restera tout aussi vitale dans le cloud que localement. 
- Contoso ne souhaite pas investir dans cette application.  Elle est importante pour l’activité mais, sous sa forme actuelle, Contoso veut simplement la migrer de manière sécurisée vers le cloud.
- Contoso ne souhaite pas modifier le modèle d’exploitation de cette application. L’entreprise souhaite interagir avec elle dans le cloud de la même façon qu’actuellement.
- Contoso ne souhaite pas modifier les fonctionnalités de l’application. Seul son emplacement doit changer.
- Après avoir effectué quelques migrations d’application Windows, Contoso souhaite apprendre à utiliser une infrastructure basée sur Linux dans Azure.


## <a name="solution-design"></a>Conception de la solution

Après avoir défini précisément les objectifs et les exigences, Contoso conçoit et examine une solution de déploiement et identifie le processus de migration, notamment les services Azure qui seront utilisés pour la migration.

### <a name="current-app"></a>Application actuelle

- L’application OSTicket est répartie sur deux machines virtuelles (**OSTICKETWEB** et **OSTICKETMYSQL**).
- Les machines virtuelles sont situées sur un hôte VMware ESXi **contosohost1.contoso.com** (version 6.5).
- L’environnement VMware est géré par le serveur vCenter Server 6.5 (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- Contoso dispose d’un centre de données local (**contoso-datacenter**), avec un contrôleur de domaine local (**contosodc1**).

### <a name="proposed-architecture"></a>Architecture proposée

- Dans la mesure où l’application représente une charge de travail de production, les machines virtuelles dans Azure résideront dans le groupe de ressources de production **ContosoRG**.
- Les machines virtuelles seront migrées vers la région primaire (USA Est 2), et placées dans le réseau de production (VNET-PROD-EUS2) :
    - La machine virtuelle web résidera dans le sous-réseau frontal (PROD-FE-EUS2).
    - La machine virtuelle de l’instance de base de données résidera dans le sous-réseau de la base de données (PROD-DB-EUS2).
- Les machines virtuelles locales dans le centre de données Contoso seront désaffectées une fois la migration terminée.

![Architecture du scénario](./media/contoso-migration-rehost-linux-vm/architecture.png) 

### <a name="solution-review"></a>Examen de la solution

Contoso évalue la conception proposée en dressant la liste des avantages et des inconvénients.

**Considération** | **Détails**
--- | ---
**Avantages** | Les deux machines virtuelles de l’application seront déplacées vers Azure sans changement, ce qui simplifie la migration.<br/><br/> Dans la mesure où Contoso utilise la méthode lift-and-shift pour les deux machines virtuelles de l’application, aucun outil de migration ou de configuration spécial n’est nécessaire pour la base de données de l’application.<br/><br/> Contoso conserve le contrôle total des machines virtuelles de l’application dans Azure. </br>/br> Les machines virtuelles de l’application fonctionnent sous Ubuntu 16.04-TLS, une distribution Linux approuvée. [Plus d’informations](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)
**Inconvénients** | La couche Web et Données de l’application restera un point de basculement unique. <br/><br/> Contoso devra continuer à prendre en charge l’application sous forme de machines virtuelles Azure au lieu d’opter pour un service géré, comme Azure App Service et Azure Database pour MySQL.<br/><br/> Contoso sait bien que, en faisant simple avec une migration de machines virtuelles lift-and-shift, elle ne profite pas complètement des fonctionnalités offertes par [Azure Database pour MySQL](https://docs.microsoft.com/azure/mysql/overview) (haute disponibilité intégrée, performances prédictibles, mise à l’échelle simple, sauvegardes automatiques et sécurité intégrée).

### <a name="migration-process"></a>Processus de migration

Contoso effectuera la migration comme suit :

1. Dans un premier temps, Contoso configure les infrastructures Azure et locale nécessaires pour déployer Site Recovery.
2. Après avoir préparé les composants Azure et locaux, Contoso configure et active la réplication pour les machines virtuelles.
3. Une fois la réplication opérationnelle, Contoso migre les machines virtuelles par basculement vers Azure.

![Processus de migration](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Services Azure

**Service** | **Description** | **Coût**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Le service orchestre et gère la migration et la récupération d’urgence pour les machines virtuelles Azure, les machines virtuelles locales et les serveurs physiques.  | Lors de la réplication vers Azure, des frais sur le Stockage Azure sont facturés.  Des machines virtuelles Azure sont créées en cas de basculement, et entraînent des frais. [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les frais et la tarification.

 
## <a name="prerequisites"></a>Prérequis

Voici ce dont Contoso a besoin pour ce scénario.

**Configuration requise** | **Détails**
--- | ---
**Abonnement Azure** | Contoso a créé des abonnements dans un précédent article de cette série. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur.<br/><br/> S’il vous faut plus d’autorisations granulaires, consultez [cet article](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastructure Azure** | Contoso configure son infrastructure Azure comme décrit dans [Infrastructure Azure pour la migration](contoso-migration-infrastructure.md).<br/><br/> Apprenez-en davantage sur les configurations de [réseau](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) et de [stockage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) requises pour Site Recovery.
**Serveurs locaux** | L’instance vCenter Server locale doit exécuter la version 5.5, 6.0 ou 6.5.<br/><br/> Un hôte ESXi qui exécute la version 5.5, 6.0 ou 6.5<br/><br/> Une ou plusieurs machines virtuelles VMware exécutées sur l’hôte ESXi.
**Machines virtuelles locales** | [Passez en revue les machines virtuelles Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) qui sont prises en charge pour la migration avec Site Recovery.<br/><br/> Vérifiez les [systèmes de fichiers et de stockage Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage) pris en charge.<br/><br/> Les machines virtuelles doivent répondre aux [exigences Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Étapes du scénario

Voici comment Contoso effectue la migration :

> [!div class="checklist"]
> * **Étape 1 : préparer Azure pour Site Recovery** : Contoso crée un compte de stockage Azure pour accueillir les données répliquées, et crée un coffre Recovery Services.
> * **Étape 2 : préparer une machine virtuelle VMware locale pour Site Recovery** : Contoso prépare les comptes à utiliser pour la détection de machines virtuelles et l’installation d’agents, et prépare la connexion aux machines virtuelles Azure après basculement.
> * **Étape 3 : répliquer les machines virtuelles** : Contoso configure les environnements de migration source et cible, crée une stratégie de réplication, puis commence à répliquer les machines virtuelles vers le stockage Azure.
> * **Étape 4 : migrer les machines virtuelles avec Site Recovery** : Contoso effectue un test de basculement pour vérifier que tout fonctionne correctement, puis opère un basculement complet pour migrer les machines virtuelles vers Azure.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Étape 1 : préparer Azure pour le service Site Recovery

Contoso a besoin de quelques composants Azure pour Site Recovery :

- Un nouveau compte de stockage Azure pour accueillir les données répliquées. 
- Un coffre Recovery Services dans Azure.
- Un réseau virtuel dans lequel se trouvent les ressources basculées. Contoso ayant déjà créé le réseau virtuel durant le [déploiement de l’infrastructure Azure](contoso-migration-infrastructure.md), il suffit de créer un compte de stockage et un coffre.

1. Les administrateurs de Contoso créent un compte de stockage Azure (contosovmsacc20180528) dans la région USA Est 2.

    - Le compte de stockage doit se trouver dans la même région que le coffre Recovery Services.
    - Ils utilisent un compte à usage général, avec un stockage standard, et la réplication LRS.

    ![Stockage Site Recovery](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Une fois le réseau et le compte de stockage en place, ils créent un coffre (ContosoMigrationVault) et le placent dans le groupe de ressources **ContosoFailoverRG**, dans la région USA Est 2 principale.

    ![Coffre Recovery Services](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**Besoin de plus d’aide ?**

[Apprenez-en davantage sur la](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) configuration d’Azure pour Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Étape 2 : préparer la machine virtuelle VMware locale pour Site Recovery

Les administrateurs de Contoso préparent l’infrastructure VMware locale de la façon suivante :

- Ils créent un compte sur le serveur vCenter ou sur l’hôte vSphere ESXi pour automatiser la détection de machines virtuelles.
- Ils créent un compte permettant l’installation automatique du service Mobilité sur les machines virtuelles VMware à répliquer.
- Ils préparent les machines virtuelles locales afin qu’elles puissent se connecter aux machines virtuelles Azure une fois celles-ci créées après la migration.


### <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique

Site Recovery doit pouvoir accéder aux serveurs VMware pour :

- Détectez automatiquement les machines virtuelles. Au moins un compte en lecture seule est nécessaire.
- Orchestrer la réplication, le basculement et la restauration automatique. Il vous faut un compte qui peut exécuter des opérations telles que la création et la suppression de disques, et l’allumage de machines virtuelles.

Les administrateurs de Contoso configurent le compte de la façon suivante :

1. Ils créent un rôle au niveau du vCenter.
2. Ils attribuent à ce rôle les autorisations requises.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Préparer un compte pour l’installation du service Mobilité

Le service Mobilité doit être installé sur les machines virtuelles Linux qui seront migrées.

- Site Recovery peut effectuer une installation Push automatique de ce composant si la réplication est activée pour les machines virtuelles.
- L’installation Push automatique exige de préparer un compte qui sera utilisé par Site Recovery pour accéder aux machines virtuelles.
- Les détails des comptes sont entrés lors de la configuration de la réplication. 
- Le compte peut être un compte de domaine ou local avec les autorisations nécessaires pour l’installation sur les machines virtuelles.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer la connexion aux machines virtuelles Azure après le basculement

Après le basculement vers Azure, Contoso souhaite pouvoir se connecter aux machines virtuelles répliquées dans Azure. Pour cela, les administrateurs de Contoso doivent effectuer quelques opérations :

- Pour accéder aux machines virtuelles par Internet, ils activent SSH sur la machine virtuelle Linux locale avant la migration.  Pour Ubuntu, cette opération peut être effectuée à l’aide de la commande suivante : **Sudo apt-get ssh installer -y**.
- Après avoir opéré la migration (basculement), ils peuvent consulter les **Diagnostics de démarrage** pour afficher une capture d’écran de la machine virtuelle.
- Si cela ne fonctionne pas, ils devront vérifier que la machine virtuelle est en cours d’exécution et consulter ces [conseils de dépannage](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Besoin de plus d’aide ?**

- [En savoir plus sur la](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) création et l’attribution d’un rôle pour la détection automatique.
- [Apprenez-en davantage sur](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) la création d’un compte pour une installation push du service Mobilité.


## <a name="step-3-replicate-the-on-premises-vms"></a>Étape 3 : répliquer les machines virtuelles locales

Pour pouvoir migrer la machine virtuelle web vers Azure, les administrateurs de Contoso configurent et activent la réplication.

### <a name="set-a-protection-goal"></a>Définir un objectif de protection

1. Dans le coffre, sous le nom du coffre (ContosoVMVault), Contoso définit un objectif de réplication (**Prise en main** > **Site Recovery** > **Préparer l’infrastructure**).
2. Il convient de spécifier que les ordinateurs sont situés localement, qu’il s’agit de machines virtuelles VMware, et qu’il faut les répliquer sur Azure.
    ![Objectif de réplication](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmer la planification d’un déploiement

Pour continuer, Contoso confirme que la planification du déploiement est terminée en sélectionnant **Yes, I have done it** (Oui, c’est fait). Dans ce scénario, Contoso ne migre qu’une seule machine virtuelle et n’a pas besoin de planification du déploiement.

### <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Les administrateurs de Contoso doivent maintenant configurer l’environnement source. Pour ce faire, elle télécharge un modèle OVF et l’utilise pour déployer le serveur de configuration Site Recovery comme étant une machine virtuelle VMware locale hautement disponible. Une fois le serveur de configuration opérationnel, elle l’inscrit dans le coffre.

Le serveur de configuration exécute plusieurs composants :

- Le composant de serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure, et gère la réplication des données.
- Le serveur de traitement qui fait office de passerelle de réplication. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure.
- De plus, le serveur de processus installe le service Mobilité sur les machines virtuelles que vous voulez répliquer et effectue la détection automatique sur les machines virtuelles VMware locales.

Les administrateurs de Contoso suivent les étapes ci-dessous :

1. Contoso télécharge le modèle OVF à partir de **Préparer l’Infrastructure** > **Source** > **Serveur de configuration**.
    
    ![Télécharger le modèle OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. Contoso importe le modèle dans VMware pour créer et déployer la machine virtuelle.

    ![Modèle OVF](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. Lors de l’activation de la machine virtuelle pour la première fois, celle-ci démarre dans un environnement d’installation de Windows Server 2016. Ils acceptent le contrat de licence, puis entrent un mot de passe d’administrateur.
4. Une fois l’installation terminée, ils se connectent à la machine virtuelle en tant qu’administrateur. À la première connexion, l’outil de configuration d’Azure Site Recovery s’exécute par défaut.
5. Dans l’outil, Contoso spécifie un nom à utiliser pour l’inscription du serveur de configuration dans le coffre.
6. L’outil vérifie que la machine virtuelle peut se connecter à Azure. Une fois la connexion établie, Contoso se connecte à l’abonnement Azure. Les informations d’identification doivent avoir accès au coffre dans lequel vous souhaitez inscrire le serveur de configuration.

    ![Inscrire un serveur de configuration](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. L’outil effectue des tâches de configuration, puis redémarre.
8. Contoso se reconnecte à l’ordinateur et l’Assistant de gestion du serveur de configuration démarre automatiquement.
9. Dans l’Assistant, Contoso sélectionne la carte réseau qui doit recevoir le trafic de réplication. Une fois configuré, ce paramètre ne peut pas être modifié.
10. Contoso sélectionne l’abonnement, le groupe de ressources et le coffre dans lequel inscrire le serveur de configuration.

    ![coffre](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. Ensuite, on télécharge puis on installe MySQL Server et VMWare PowerCLI. 
12. Après la validation, le nom de domaine complet (FQDN) ou l’adresse IP du serveur vCenter ou de l’hôte vSphere sont spécifiés. Ils quittent le port par défaut, puis spécifient un nom convivial pour le serveur vCenter.
13. Ils spécifient le compte qu’ils ont créés pour la détection automatique, et les informations d’identification à utiliser pour installer automatiquement le service Mobilité.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. Une fois l’inscription terminée, ils vérifient sur le Portail Azure que le serveur de configuration et le serveur VMware apparaissent sur la page **Source** du coffre. La détection peut prendre 15 minutes ou plus. 
15. Site Recovery se connecte ensuite à des serveurs VMware, puis détecte les machines virtuelles.

### <a name="set-up-the-target"></a>Configurer la cible

Les administrateurs de Contoso configurent à présent les paramètres de réplication cibles.

1. Dans **Préparer l’infrastructure** > **Cible**, Contoso sélectionne les paramètres de la cible.
2. Site Recovery vérifie qu’il existe un compte de stockage et un réseau Azure dans la cible spécifiée.

### <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

Une fois la source et la cible configurées, ils sont prêts à créer une stratégie de réplication.

1. Dans **Préparer l’infrastructure** > **Paramètres de réplication** > **Stratégie de réplication** >  **Créer et associer**, Contoso crée une stratégie **ContosoMigrationPolicy**.
2. Contoso utilise les paramètres par défaut :
    - **Seuil d'objectif de point de récupération** : la valeur par défaut est de 60 minutes. Cette valeur définit la fréquence à laquelle les points de récupération sont créés. Une alerte est générée lorsque la réplication continue dépasse cette limite.
    - **Rétention des points de récupération**. La valeur par défaut est 24 heures. Cette valeur spécifie la durée de la fenêtre de rétention pour chaque point de récupération. Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre.
    - **Fréquence des captures instantanées de cohérence d’application**. La valeur par défaut est une heure. Cette valeur spécifie la fréquence à laquelle les captures instantanées de cohérence d’application sont créées.
 
        ![Créer une stratégie de réplication](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. La stratégie est automatiquement associée au serveur de configuration. 

    ![Associer la stratégie de réplication](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**Besoin de plus d’aide ?**

- Une procédure pas à pas complète de toutes ces étapes est décrite dans [Configurer la récupération d’urgence pour des machines virtuelles VMware locales](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Des instructions détaillées sont disponibles pour vous aider à [configurer l’environnement source](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), à [déployer le serveur de configuration](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) et à [configurer les paramètres de réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Apprenez-en davantage](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sur l’agent invité Azure pour Linux.

**Besoin de plus d’aide ?**

- Une procédure pas à pas complète de toutes ces étapes est décrite dans [Configurer la récupération d’urgence pour des machines virtuelles VMware locales](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Des instructions détaillées sont disponibles pour vous aider à [configurer l’environnement source](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), à [déployer le serveur de configuration](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) et à [configurer les paramètres de réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Apprenez-en davantage](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sur l’agent invité Azure pour Linux.



### <a name="enable-replication-for-osticketweb"></a>Activer la réplication pour OSTICKETWEB

Maintenant, les administrateurs de Contoso peuvent commencer à répliquer la machine virtuelle **OSTICKETWEB**.

1. Dans **Répliquer l’application** > **Source** > **+Répliquer**, Contoso sélectionne les paramètres de la source.
2. Ils indiquent qu’ils souhaitent activer des machines virtuelles, puis sélectionnent les paramètres de la source, dont le serveur vCenter et le serveur de configuration.

    ![Activer la réplication](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. Ils spécifient les paramètres de la cible, dont le groupe de ressources et le réseau virtuel dans lequel la machine virtuelle Azure se trouvera après le basculement, ainsi que le compte de stockage dans lequel les données répliquées seront stockées.

     ![Activer la réplication](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Ils sélectionnent la machine virtuelle **OSTICKETWEB** pour la réplication. 

    - À ce stade, ils sélectionnent seulement **OSTICKETWEB**, car le réseau virtuel et le sous-réseau doivent être tous deux sélectionnés et les machines virtuelles ne sont pas dans le même sous-réseau.
    - Site Recovery installe automatiquement le service Mobilité une fois la réplication activée pour la machine virtuelle.

    ![Activer la réplication](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. Dans les propriétés de la machine virtuelle, ils sélectionnent le compte utilisé par le serveur de traitement pour installer automatiquement le service Mobilité sur la machine.

     ![Service Mobilité](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, Contoso vérifie que la stratégie de réplication appliquée est correcte, puis sélectionnent **Activer la réplication**.
6.  Contoso suit la progression de la réplication dans **Travaux**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.



### <a name="enable-replication-for-osticketmysql"></a>Activer la réplication pour OSTICKETMYSQL

Les administrateurs de Contoso peuvent à présent commencer à répliquer **OSTICKETMYSQL**.

1. Dans **Répliquer l’application** > **Source** > **+Répliquer**, ils sélectionnent les paramètres de la cible.
2. Ils sélectionnent la machine virtuelle **OSTICKETMYSQL** pour la réplication, ainsi que le compte à utiliser pour l’installation du service Mobilité.

    ![Activer la réplication](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Ils appliquent la même stratégie de réplication que pour OSTICKETWEB auparavant, et activent la réplication.  

**Besoin de plus d’aide ?**

La procédure pas à pas complète de toutes ces étapes est décrite dans [Activer la réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Étape 4 : migrer les machines virtuelles 

Les administrateurs de Contoso effectuent un rapide test de basculement, puis migrent les machines virtuelles.

### <a name="run-a-test-failover"></a>Exécuter un test de basculement

L’exécution d’un test de basculement permet de vérifier que tout fonctionne comme prévu avant la migration. 

1. Ils exécutent un test de basculement jusqu’au dernier point dans le temps disponible (**Dernier point traité**).
2. Contoso sélectionne **Shut down machine before beginning failover** (Arrêter la machine avant de commencer le basculement), de façon à ce que Site Recovery tente d’arrêter la machine virtuelle source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. 
3. Le test de basculement est exécuté : 
    - Une vérification des prérequis est effectuée pour garantir que toutes les conditions nécessaires pour la migration sont en place.
    - Le basculement traite les données pour permettre la création d’une machine virtuelle Azure. Si le dernier point de récupération est sélectionné, un point de récupération est créé à partir des données.
    - Une machine virtuelle Azure est créée en utilisant les données traitées à l’étape précédente.
3. Une fois le basculement terminé, la machine virtuelle Azure répliquée apparaît dans le portail Azure. Ils vérifient que la machine virtuelle est de la taille appropriée, qu’elle est connectée au réseau approprié et qu’elle est en cours d’exécution. 
4. Après ces vérifications, ils nettoient le basculement, puis consignent et enregistrent les observations éventuelles.

### <a name="create-and-customize-a-recovery-plan"></a>Créer et personnaliser un plan de récupération

 Après avoir vérifié que le test de basculement fonctionne comme prévu, les administrateurs de Contoso créent un plan de récupération pour la migration. 

- Un plan de récupération spécifie l’ordre du basculement et indique comment les machines virtuelles Azure seront mises en ligne dans Azure.
- L’application à migrer étant à deux niveaux, ils personnalisent le plan de récupération afin que la machine virtuelle des données (SQLVM) démarre avant la machine virtuelle frontale (WEBVM).


1. Dans **Plans de récupération (Site Recovery)** > **+Plan de récupération**, ils créent un plan et y ajoutent les machines virtuelles.

    ![Plan de récupération](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. Une fois le plan créé, ils le sélectionnent afin de le personnaliser (**Plans de récupération** > **OsTicketMigrationPlan** > **Personnaliser**.
3.  Ils suppriment **OSTICKETWEB** du **Groupe 1 : Démarrage**.  Cela garantit que la première action de démarrage affecte uniquement **OSTICKETMYSQL**.

    ![Groupe de récupération](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  Dans **+Groupe** > **Ajouter des éléments protégés**, ils ajoutent **OSTICKETWEB** au **Group 2 : Démarrage**.  Ils ont besoin que ces éléments soient dans deux groupes différents.

    ![Groupe de récupération](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>Migrer les machines virtuelles


Les administrateurs de Contoso sont maintenant prêts à opérer un basculement sur le plan de récupération pour migrer les machines virtuelles.

1. Ils sélectionnent le plan > **Basculement**.
2.  Ils choisissent de basculer vers le dernier point de récupération et spécifient que Site Recovery doit essayer d’arrêter la machine virtuelle locale avant de déclencher le basculement. Contoso peut suivre la progression du basculement sur la page **Travaux**.

    ![Basculement](./media/contoso-migration-rehost-vm/failover1.png)

3. Pendant le basculement, le serveur vCenter émet des commandes pour arrêter les deux machines virtuelles en cours d’exécution sur l’hôte ESXi.

    ![Basculement](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. Après le basculement, elle vérifie que l’ordinateur virtuel Azure s’affiche comme prévu dans le portail Azure.

    ![Basculement](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Après avoir vérifié la machine virtuelle dans Azure, ils achèvent la migration de façon à finir le processus de migration pour chaque machine virtuelle. Cela met fin à la réplication de la machine virtuelle et arrête la facturation Site Recovery pour celle-ci.

    ![Basculement](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>Connecter la machine virtuelle à la base de données

La dernière étape du processus de migration consiste pour les administrateurs de Contoso à mettre à jour la chaîne de connexion de l’application pour qu’elle pointe vers la base de données de l’application s’exécutant sur la machine virtuelle **OSTICKETMYSQL**. 

1. Ils établissent une connexion SSH à la machine virtuelle **OSTICKETWEB** à l’aide de Putty ou d’un autre client SSH. La machine virtuelle étant privée, ils établissent la connexion en utilisant l’adresse IP privée.

    ![Connecter à la base de données](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![Connecter à la base de données](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. Ils ont besoin de s’assurer que la machine virtuelle **OSTICKETWEB** peut communiquer avec la machine virtuelle **OSTICKETMYSQL**. Actuellement, la configuration est codée en dur avec l’adresse IP locale 172.16.0.43.

    **Avant la mise à jour**
    
    ![Mettre à jour l’adresse IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **Après la mise à jour**
    
    ![Mettre à jour l’adresse IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. Contoso redémarre le service avec la commande **systemctl restart apache2**.

    ![Redémarrer](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Enfin, ils mettent à jour les enregistrements DNS pour **OSTICKETWEB** et **OSTICKETMYSQL** sur l’un des contrôleurs de domaine de Contoso.

    ![Mettre à jour le DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![Mettre à jour le DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**Besoin de plus d’aide ?**

- [En savoir plus sur](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) l’exécution d’un test de basculement. 
- [Découvrez](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) comment créer un plan de récupération.
- [Découvrez](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) comment basculer vers Azure.

## <a name="clean-up-after-migration"></a>Nettoyer après la migration

Une fois la migration terminée, les niveaux de l’application osTicket s’exécutent sur des machines virtuelles Azure.

À présent, Contoso doit effectuer un nettoyage de la façon suivante : 

- Supprimer les machines virtuelles locales de l’inventaire vCenter.
- Supprimer les machines virtuelles locales des travaux de sauvegarde locale.
- Mettre à jour sa documentation interne pour afficher le nouvel emplacement et les adresses IP pour OSTICKETWEB et OSTICKETMYSQL.
- Passer en revue toutes les ressources qui interagissent avec les machines virtuelles, et mettre à jour les paramètres ou la documentation appropriés afin de refléter la nouvelle configuration.
- Contoso a utilisé le service Azure Migrate avec le mappage de dépendance pour évaluer les machines virtuelles pour la migration. Les administrateurs doivent supprimer de la machine virtuelle Microsoft Monitoring Agent et Dependency Agent, qu’ils ont installés à cette fin.

## <a name="review-the-deployment"></a>Examiner le déploiement

L’application étant en cours d’exécution, Contoso doit à présent rendre son infrastructure entièrement opérationnelle et la sécuriser.

### <a name="security"></a>Sécurité

L’équipe de sécurité de Contoso examine les machines virtuelles OSTICKETWEB les OSTICKETMYSQL pour détecter d’éventuels problèmes de sécurité.

- L’équipe examine les groupes de sécurité réseau (NSG) des machines virtuelles pour en contrôler l’accès. Contoso utilise des groupes de sécurité réseau pour s’assurer que seul le trafic autorisé vers l’application puisse passer.
- L’équipe prend également en considération la sécurisation des données sur les disques de machine virtuelle à l’aide de Disk Encryption et d’Azure Key Vault.

[En savoir plus](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sur les pratiques de sécurité pour les machines virtuelles.

### <a name="bcdr"></a>BCDR

Pour assurer la continuité d'activité et la reprise d'activité, Contoso effectue les actions suivantes :

- **Sécuriser les données** : Contoso sauvegarde les données sur les machines virtuelles à l’aide du service Sauvegarde Azure. [Plus d’informations](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- **Faire en sorte que les applications soient opérationnelles** : Contoso réplique les machines virtuelles de l’application dans Azure vers une région secondaire à l’aide de Site Recovery. [Plus d’informations](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

### <a name="licensing-and-cost-optimization"></a>Gestion des licences et optimisation des coûts

- Après déploiement des ressources, Contoso affecte des balises Azure de la manière définie lors du [déploiement de l’infrastructure Azure](contoso-migration-infrastructure.md#set-up-tagging).
- Contoso n’a aucun problème de licence avec les serveurs Ubuntu.
- Contoso va activer Azure Cost Management sous licence de Cloudyn, une filiale de Microsoft. Il s’agit d’une solution de gestion des coûts multicloud qui aide à utiliser et à gérer Azure ainsi que d’autres ressources cloud.  [Apprenez-en davantage](https://docs.microsoft.com/azure/cost-management/overview) sur Azure Cost Management. 


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons montré comment Contoso a migré une application de Service Desk locale hiérarchisée sur deux machines virtuelles Linux vers des machines virtuelles Azure IaaS à l’aide d’Azure Site Recovery.

Dans l’article suivant de la série, nous allons vous montrer comment Contoso migre la même application d’assistance vers Azure. Cette fois, Contoso utilise Site Recovery pour migrer la machine virtuelle frontale de l’application, et migre la base de données de l’application par sauvegarde et restauration vers Azure Database pour MySQL, à l’aide de l’outil MySQL Workbench. [Commencez](contoso-migration-rehost-linux-vm-mysql.md).
