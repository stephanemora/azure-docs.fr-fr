---
title: Ré_héberger une application Contoso à l’aide d’une migration vers des machines virtuelles Azure avec Azure Site Recovery | Microsoft Docs
description: Découvrez comment réhéberger une application locale avec une migration lift-and-shift d’ordinateurs locaux vers Azure, à l’aide du service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: raynew
ms.openlocfilehash: 40efd1b556db5cbebff80293a91a228afe17eab5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297415"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migration de Contoso : ré-héberger une application locale vers des machines virtuelles Azure


Cet article explique comment Contoso réhéberge l’application SmartHotel360 locale dans Azure en migrant les machines virtuelles de l’application vers des machines virtuelles Azure.


Ce document fait partie d’une série d’articles qui montrent comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations générales et des scénarios qui illustrent comment configurer une infrastructure de migration, évaluer des ressources locales pour la migration et exécuter différents types de migration. Les scénarios augmentent en complexité. Nous ajouterons des articles au fil du temps.

**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Vue d’ensemble de la série d’articles, de la stratégie de migration de Contoso et des exemples d’applications utilisés dans la série. | Disponible
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration de la série. | Disponible
[Article 3 : Évaluer les ressources locales à migrer vers Azure](contoso-migration-assessment.md)  | Contoso évalue son application locale SmartHotel360 qui s’exécute sur VMware. Contoso évalue les machines virtuelles de l’application à l’aide du service Azure Migrate et la base de données SQL Server de l’application à l’aide de l’Assistant Migration de données. | Disponible
[Article 4 : Réhéberger une application sur une machine virtuelle Azure et SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso exécute une migration lift-and-shift vers Azure pour son application SmartHotel360 locale. Contoso migre la machine virtuelle frontend de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migre la base de données de l’application vers Azure SQL Database Managed Instance à l’aide [d’Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponible   
Article 5 : Réhéberger une application sur des machines virtuelles Azure | Contoso migre les machines virtuelles de son application SmartHotel360 vers des machines virtuelles Azure avec le service Site Recovery. | Cet article
[Article 6 : Réhéberger une application sur des machines virtuelles Azure et dans un groupe de disponibilité SQL Server AlwaysOn](contoso-migration-rehost-vm-sql-ag.md) | Contoso migre l’application SmartHotel360. Contoso utilise Site Recovery pour migrer les machines virtuelles d’application. La société utilise Database Migration Service pour migrer la base de données d’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. | Disponible 
[Article 7 : ré-héberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Contoso effectue une migration lift-and-shift de l’application osTicket Linux vers des machines virtuelles Azure à l’aide d’Azure Site Recovery. | Disponible
[Article 8 : Réhéberger une application Linux sur des machines virtuelles Azure et Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migre l’application osTicket Linux vers des machines virtuelles Azure à l’aide d’Azure Site Recovery et la base de données de l’application vers une instance Azure MySQL Server à l’aide de MySQL Workbench. | Disponible
[Article 9 : Refactoriser une application sur Azure Web Apps et Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migre l’application SmartHotel360 vers Azure Web App et la base de données de l’application vers une instance Azure SQL Server avec l’Assistant Migration de données. | Disponible
[Article 10 : Refactoriser une application Linux sur Azure Web Apps et Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migre son application osTicket Linux vers Azure Web App dans plusieurs régions Azure à l’aide d’Azure Traffic Manager, intégré à GitHub de façon à assurer une livraison continue. Contoso migre la base de données d’application vers une instance d’Azure Database pour MySQL. | Disponible 
[Article 11 : Refactoriser TFS sur Azure DevOps Services](contoso-migration-tfs-vsts.md) | Contoso migre son déploiement local de Team Foundation Server vers Azure DevOps Services dans Azure. | Disponible
[Article 12 : Réarchitecturer une application sur des conteneurs Azure et Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migre son application SmartHotel vers Azure. Ensuite, l’entreprise restructure la couche web de l’application comme un conteneur Windows s’exécutant dans Azure Service Fabric, et la base de données avec Azure SQL Database. | Disponible
[Article 13 : Regénérer une application dans Azure](contoso-migration-rebuild.md) | Contoso régénère son application SmartHotel à l’aide d’une série de fonctionnalités et services Azure, notamment Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services et Azure Cosmos DB. | Disponible


Cet article explique comment Contoso migre les fenêtres à deux niveaux. Application .NET SmartHotel360 s’exécutant sur des machines virtuelles VMware vers Azure. Si vous souhaitez utiliser cette application, elle est disponible en open source et vous pouvez la télécharger à partir de [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec des partenaires commerciaux pour comprendre le résultat qu’ils souhaitent obtenir avec cette migration :

- **Répondre à la croissance** : Contoso étant en croissance, son infrastructure et ses systèmes locaux subissent une pression.
- **Limiter les risques** : l’application SmartHotel360 est vitale pour l’activité de Contoso. Il veut migrer l’application vers Azure sans risque.
- **Étendre** : Contoso ne souhaite pas modifier l’application, mais veut s’assurer qu’elle est stable.


## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a épinglé les objectifs de cette migration. Ces objectifs sont utilisés pour déterminer la meilleure méthode de migration :

- Après la migration, l’application dans Azure devra offrir les mêmes performances qu’aujourd’hui dans l’environnement VMware.  L’application restera tout aussi vitale dans le cloud que localement. 
- Contoso ne souhaite pas investir dans cette application.  Elle est importante pour l’activité mais, sous sa forme actuelle, Contoso veut simplement la migrer de manière sécurisée vers le cloud.
- Contoso ne souhaite pas modifier le modèle d’exploitation de cette application. Contoso souhaite interagir avec elle dans le cloud comme elle le fait en local actuellement.
- Contoso ne souhaite pas modifier les fonctionnalités de l’application. Seul son emplacement doit changer.


## <a name="solution-design"></a>Conception de la solution

Après avoir défini précisément les objectifs et les exigences, Contoso conçoit et examine une solution de déploiement et identifie le processus de migration, notamment les services Azure qui seront utilisés pour la migration.

### <a name="current-app"></a>Application actuelle

- L’application est hiérarchisée sur deux machines virtuelles (**WEBVM** et **SQLVM**).
- Les machines virtuelles sont situées sur un hôte VMware ESXi **contosohost1.contoso.com** (version 6.5).
- L’environnement VMware est géré par le serveur vCenter Server 6.5 (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- Contoso dispose d’un centre de données local (contoso-datacenter), avec un contrôleur de domaine local (**contosodc1**).

### <a name="proposed-architecture"></a>Architecture proposée

- Dans la mesure où l’application représente une charge de travail de production, les machines virtuelles de l’application dans Azure résideront dans le groupe de ressources de production ContosoRG.
- Les machines virtuelles de l’application seront migrées vers la région Azure primaire (USA Est 2), et placées dans le réseau de production (VNET-PROD-EUS2).
- La machine virtuelle du serveur web frontal résidera dans le sous-réseau frontal (PROD-FE-EUS2) du réseau de production.
- La machine virtuelle de la base de données résidera dans le sous-réseau de la base de données (PROD-FE-EUS2) du réseau de production.
- Les machines virtuelles locales dans le centre de données Contoso seront désaffectées une fois la migration terminée.

![Architecture du scénario](./media/contoso-migration-rehost-vm/architecture.png) 

### <a name="database-considerations"></a>Considérations liées à la base de données

Dans le cadre de la conception de la solution, Contoso a fait une comparaison des fonctionnalités entre Azure SQL Database et SQL Server. Les administrateurs ont choisi SQL Server exécuté sur une machine virtuelle IaaS Azure sur la base des considérations suivantes : 

- L’utilisation d’une machine virtuelle Azure exécutant SQL Server semble être une bonne solution si Contoso a besoin de personnaliser le système d’exploitation ou le serveur de base de données, ou s’il souhaite colocaliser et exécuter des applications tierces sur la même machine virtuelle.
- Avec Software Assurance, dans le futur Contoso pourra échanger ses licences existantes contre une réduction de tarif sur SQL Database Managed Instance en utilisant Azure Hybrid Benefit pour SQL Server. Ceci permettra économiser jusqu'à 30 % sur Managed Instance.



### <a name="solution-review"></a>Examen de la solution

Contoso évalue la conception proposée en dressant la liste des avantages et des inconvénients.

**Considération** | **Détails**
--- | ---
**Avantages** | Les deux machines virtuelles de l’application seront déplacées vers Azure sans changement, ce qui simplifie la migration.<br/><br/> Dans la mesure où Contoso utilise la méthode lift-and-shift pour les deux machines virtuelles de l’application, aucun outil de migration ou de configuration spécial n’est nécessaire pour la base de données de l’application.<br/><br/> Contoso peut exploiter leur investissement dans Software Assurance en utilisant Azure Hybrid Benefit.<br/><br/> Contoso conserve le contrôle total des machines virtuelles de l’application dans Azure. 
**Inconvénients** | WEBVM et SQLVM exécutent Windows Server 2008 R2. Le système d’exploitation est pris en charge par Azure pour certains rôles (juillet 2018). [Plus d’informations](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)<br/><br/> Les couches web et données de l’application resteront un point de basculement unique.</br><br/> SQLVM est en cours d’exécution sur SQL Server 2008 R2, qui ne bénéficie pas du support standard. Toutefois, la prise en charge est assurée pour les machines virtuelles Azure (juillet 2018). [Plus d’informations](https://support.microsoft.com/en-us/help/956893)<br/><br/> Contoso devra continuer à prendre en charge l’application sous forme de machines virtuelles Azure au lieu d’opter pour un service managé, tel qu’Azure App Service et Azure SQL Database.



### <a name="migration-process"></a>Processus de migration

Contoso va migrer le serveur frontal et les machines virtuelles de la base de données de l’application vers des machines virtuelles Azure avec Site Recovery :

- Dans un premier temps, Contoso va préparer et configurer les composants Azure pour Site Recovery et préparer l’infrastructure VMware locale.
- L’[infrastructure Azure](contoso-migration-infrastructure.md) étant déjà en place, Contoso n’a qu’à ajouter quelques composants Azure spécifiquement pour Site Recovery.
- Une fois tous les éléments préparés, Contoso peut commencer à répliquer les machines virtuelles.
- Lorsque la réplication est activée et opère, la machine virtuelle est migrée en la basculant vers Azure.

![Processus de migration](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Services Azure

**Service** | **Description** | **Coût**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Le service orchestre et gère la migration et la récupération d’urgence pour les machines virtuelles Azure, les machines virtuelles locales et les serveurs physiques.  | Lors de la réplication vers Azure, des frais sur le Stockage Azure sont facturés.  Des machines virtuelles Azure sont créées en cas de basculement, et entraînent des frais. [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les frais et la tarification.


## <a name="prerequisites"></a>Prérequis

Voici ce dont Contoso a besoin pour exécuter ce scénario.

**Configuration requise** | **Détails**
--- | ---
**Abonnement Azure** | Contoso a créé des abonnements dans un précédent article de cette série. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur.<br/><br/> S’il vous faut plus d’autorisations granulaires, consultez [cet article](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastructure Azure** | [Découvrez comment](contoso-migration-infrastructure.md) Contoso configure une infrastructure Azure.<br/><br/> Apprenez-en davantage sur les configurations de [réseau](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) et de [stockage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) requises pour Site Recovery.
**Serveurs locaux** | Les serveurs vCenter locaux doivent exécuter la version 5.5, 6.0 ou 6.5<br/><br/> Les hôtes ESXi doivent exécuter la version 5.5, 6.0 ou 6.5<br/><br/> Une ou plusieurs machines virtuelles VMware doivent s’exécuter sur l’hôte ESXi.
**Machines virtuelles locales** | Les machines virtuelles doivent répondre aux [exigences Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Étapes du scénario

Voici comment les administrateurs de Contoso exécuteront la migration :

> [!div class="checklist"]
> * **Étape 1 : préparer Azure pour Site Recovery** : il s’agit de créer un compte de stockage Azure pour accueillir les données répliquées, et un coffre Recovery Services.
> * **Étape 2 : préparer une machine virtuelle VMware locale pour Site Recovery** : il s’agit de préparer des comptes pour la découverte de machine virtuelle et l’installation d’agents, et de préparer la connexion aux machines virtuelles Azure après basculement.
> * **Étape 3 : répliquer les machines virtuelles** : il s’agit de configurer la réplication et de commencer à répliquer des machines virtuelles dans le stockage Azure.
> * **Étape 4 : migrer des machines virtuelles avec Site Recovery** : il s’agit d’exécuter un test de basculement pour s’assurer que tout fonctionne correctement, puis d’opérer un basculement complet pour migrer les machines virtuelles vers Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Étape 1 : préparer Azure pour le service Site Recovery

Les composants Azure dont Contoso a besoin pour migrer les machines virtuelles vers Azure sont les suivants :

- Un réseau virtuel dans lequel les machines virtuelles Azure seront situées après leur création pendant le basculement.
- Un compte de stockage Azure pour accueillir les données répliquées. 
- Un coffre Recovery Services dans Azure.

La configuration est effectuée´comme suit :

1. Configurer un réseau : Contoso a déjà configuré un réseau utilisable pour Site Recovery lorsqu’elle a [déployé l’infrastructure Azure](contoso-migration-infrastructure.md).

    - L’application SmartHotel360 est une application de production, et les machines virtuelles seront migrées vers le réseau de production Azure (VNET-PROD-EUS2) dans la région États-Unis de l’Est 2 principale.
    - Les deux machines virtuelles seront placées dans le groupe de ressources ContosoRG utilisé pour les ressources de production.
    - La machine virtuelle du serveur frontal de l’application (WEBVM) migrera vers le sous-réseau frontal (PROD-FE-EUS2) dans le réseau de production.
    - La machine virtuelle de la base de données de l’application (SQLVM) migrera vers le sous-réseau de la base de données (PROD-DB-EUS2) dans le réseau de production.

2. Configurer un compte de stockage : Contoso crée un compte de stockage Azure (contosovmsacc20180528) dans la région principale.
    - Le compte de stockage doit se trouver dans la même région que le coffre Recovery Services.
    - Ils utilisent un compte à usage général, avec un stockage standard et une réplication de stockage localement redondant. 

    ![Stockage Site Recovery](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Créer un coffre : avec le réseau et le compte de stockage en place, Contoso crée à présent un coffre Recovery Services (ContosoMigrationVault) et le place dans le groupe de ressources ContosoFailoverRG, dans la région USA Est 2 principale.

    ![Coffre Recovery Services](./media/contoso-migration-rehost-vm/asr-vault.png)

**Besoin de plus d’aide ?**

[Apprenez-en davantage sur la](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) configuration d’Azure pour Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Étape 2 : préparer la machine virtuelle VMware locale pour Site Recovery

Voici ce que Contoso prépare localement :

- Un compte sur le serveur vCenter ou sur l’hôte vSphere ESXi pour automatiser la détection de machines virtuelles.
- Un compte pour l’installation automatique du service Mobilité sur les machines virtuelles VMware. 
- Des paramètres de machine virtuelle locale pour que Contoso puisse se connecter aux machines virtuelles répliquées après le basculement.


### <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique

Site Recovery doit pouvoir accéder aux serveurs VMware pour :

- Détectez automatiquement les machines virtuelles. 
- Orchestrer la réplication, le basculement et la restauration automatique des machines virtuelles.
- Au moins un compte en lecture seule est nécessaire. Le compte doit pouvoir exécuter des opérations telles que la création et la suppression de disques, et l’activation de machines virtuelles.

Les administrateurs de Contoso configurent le compte de la façon suivante :

1. Ils créent un rôle au niveau du vCenter.
2. Ils attribuent à ce rôle les autorisations requises.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Préparer un compte pour l’installation du service Mobilité

Le service Mobilité doit être installé sur chaque machine virtuelle.

- Site Recovery peut faire une installation push automatique du service Mobilité quand la réplication de machine virtuelle est activée.
- Un compte étant requis, Site Recovery peut accéder aux machines virtuelles pour l’installation push. Vous spécifiez ce compte quand vous configurez la réplication.
- Il vous faut un compte de domaine ou local avec les autorisations nécessaires pour l’installation sur les machines virtuelles.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer la connexion aux machines virtuelles Azure après le basculement

Après le basculement, Contoso souhaite se connecter aux machines virtuelles Azure. Pour ce faire, les administrateurs effectuent les opérations suivantes avant la migration :

1. Pour l’accès via Internet :

 - Elle active le protocole RDP sur la machine virtuelle locale avant le basculement.
 - Elle s’assure que les règles de TCP et UDP sont ajoutées au profil **Public**.
 - Elle vérifie que le protocole RDP est autorisé dans **Pare-feu Windows** > **Applications autorisées** pour tous les profils.
 
2. Pour l’accès via le VPN de site à site :

 - Elle active le protocole RDP sur l’ordinateur local.
 - Elle autorise le protocole RDP dans **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux **Domaine et privé**.
 - Elle définit la stratégie SAN du système d’exploitation sur la machine virtuelle locale sur **OnlineAll**.

De plus, quand elle opèrent un basculement, elle doit vérifier les points suivants :

- Aucune mise à jour de Windows ne peut être en attente sur la machine virtuelle lors du déclenchement d’un basculement. Autrement, il ne sera pas possible de se connecter à la machine virtuelle avant la fin de la mise à jour.
- Après basculement, on peut consulter les **diagnostics de démarrage** pour afficher une capture d’écran de la machine virtuelle. Si cela ne fonctionne pas, il faut vérifier que la machine virtuelle est en cours d’exécution et consulter ces [conseils de dépannage](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Besoin de plus d’aide ?**

- [En savoir plus sur la](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) création et l’attribution d’un rôle pour la détection automatique.
- [Apprenez-en davantage sur](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) la création d’un compte pour une installation push du service Mobilité.


## <a name="step-3-replicate-the-on-premises-vms"></a>Étape 3 : répliquer les machines virtuelles locales

Les administrateurs de Contoso peuvent exécuter une migration vers Azure, ils doivent configurer et activer la réplication.

### <a name="set-a-replication-goal"></a>Définir un objectif de réplication

1. Dans le coffre, sous le nom du coffre (ContosoVMVault), on définit un objectif de réplication (**Prise en main** > **Site Recovery** > **Préparer l’infrastructure**).
2. On spécifie que les ordinateurs sont situés localement, qu’ils s’exécutent sur VMware et qu’ils sont répliqués sur Azure.

    ![Objectif de réplication](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmer la planification d’un déploiement

Pour continuer, ils confirment que la planification du déploiement est terminée en sélectionnant **Yes, I have done it** (Oui, c’est fait). Dans ce scénario, Contoso ne migre que deux machines virtuelles et n’a pas besoin de planification du déploiement.


### <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Les administrateurs de Contoso doivent configurer l’environnement source. Pour ce faire, elle télécharge un modèle OVF et l’utilise pour déployer le serveur de configuration Site Recovery comme étant une machine virtuelle VMware locale hautement disponible. Une fois le serveur de configuration opérationnel, elle l’inscrit dans le coffre.

Le serveur de configuration exécute plusieurs composants :

- Le composant de serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure, et gère la réplication des données.
- Le serveur de traitement qui fait office de passerelle de réplication. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure.
- De plus, le serveur de processus installe le service Mobilité sur les machines virtuelles que vous voulez répliquer et effectue la détection automatique sur les machines virtuelles VMware locales.



Les administrateurs de Contoso suivent les étapes ci-dessous :

1. Dans le coffre, elle télécharge le modèle OVF à partir de **Préparer l’Infrastructure** > **Source** > **Serveur de configuration**.
    
    ![Télécharger le modèle OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. Le modèle est importé dans VMware pour créer et déployer la machine virtuelle.

    ![Modèle OVF](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  Lors de l’activation de la machine virtuelle pour la première fois, celle-ci démarre dans un environnement d’installation de Windows Server 2016. Contoso accepte le contrat de licence, puis entrent un mot de passe d’administrateur.
4. Une fois l’installation terminée, Contoso se connecte à la machine virtuelle en tant qu’administrateur. À la première connexion, l’outil de configuration d’Azure Site Recovery s’exécute par défaut.
5. Dans l’outil, ils spécifient un nom pour l’inscription du serveur de configuration dans le coffre.
6. L’outil vérifie que la machine virtuelle peut se connecter à Azure. Une fois la connexion établie, Contoso se connecte à l’abonnement Azure. Les informations d’identification doivent avoir accès au coffre dans lequel Contoso va inscrire le serveur de configuration.

    ![Inscrire un serveur de configuration](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. L’outil effectue des tâches de configuration, puis redémarre.
8. Contoso se reconnecte à l’ordinateur et l’Assistant de gestion du serveur de configuration démarre automatiquement.
9. Dans l’Assistant, Contoso sélectionne la carte réseau qui doit recevoir le trafic de réplication. Une fois configuré, ce paramètre ne peut pas être modifié.
10. Ils sélectionnent l’abonnement, le groupe de ressources et le coffre dans lequel inscrire le serveur de configuration.
        ![coffre](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. Ils téléchargent et installent MySQL Server et VMWare PowerCLI. 
11. Après la validation, ils spécifient le nom de domaine complet (FQDN) ou l’adresse IP du serveur vCenter ou de l’hôte vSphere. Ils quittent le port par défaut et spécifient un nom convivial pour le serveur dans Azure.
12. Ils spécifient le compte qu’ils ont créés pour la découverte automatique et les informations d’identification utilisées pour installer automatiquement le service Mobilité. Pour des machines virtuelles Windows, le compte doit disposer des privilèges d’administrateur local sur celles-ci.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Une fois l’inscription terminée, dans le portail Azure, ils vérifient attentivement que le serveur de configuration et le serveur VMware sont répertoriés dans la page **Source** du coffre. La détection peut prendre 15 minutes ou plus. 
8. Site Recovery se connecte ensuite aux serveurs VMware en utilisant les paramètres spécifiés et détecte les machines virtuelles.

### <a name="set-up-the-target"></a>Configurer la cible

Maintenant, les administrateurs de Contoso spécifient les paramètres de réplication de la cible.

1. Dans **Préparer l’infrastructure** > **Cible**, Contoso sélectionne les paramètres de la cible.
2. Site Recovery vérifie qu’il existe un compte de stockage et un réseau Azure dans l’emplacement cible spécifié.

### <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

Ils peuvent maintenant créer une stratégie de réplication.

1. Dans **Préparer l’infrastructure** > **Paramètres de réplication** > **Stratégie de réplication** >  **Créer et associer**, Contoso crée une stratégie **ContosoMigrationPolicy**.
2. Contoso utilise les paramètres par défaut :
    - **Seuil d'objectif de point de récupération** : la valeur par défaut est de 60 minutes. Cette valeur définit la fréquence à laquelle les points de récupération sont créés. Une alerte est générée lorsque la réplication continue dépasse cette limite.
    - **Rétention des points de récupération**. La valeur par défaut est 24 heures. Cette valeur spécifie la durée de la fenêtre de rétention pour chaque point de récupération. Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre.
    - **Fréquence des captures instantanées de cohérence d’application**. La valeur par défaut est une heure. Cette valeur spécifie la fréquence à laquelle les captures instantanées de cohérence d’application sont créées.

        ![Créer une stratégie de réplication](./media/contoso-migration-rehost-vm/replication-policy.png)

5. La stratégie est automatiquement associée au serveur de configuration. 

    ![Associer la stratégie de réplication](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Activer la réplication pour WEBVM

Quand tout est en place, les administrateurs de Contoso peuvent activer la réplication pour les machines virtuelles. Ils commencent avec WebVM.

1. Dans **Répliquer l’application** > **Source** > **+Répliquer**, ils sélectionnent les paramètres de la source.
2. Ces paramètres indiquent qu’ils souhaitent activer les machines virtuelles, puis sélectionnent le serveur vCenter et le serveur de configuration.

    ![Activer la réplication](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Ils sélectionnent les paramètres de la cible, dont le groupe de ressources, le réseau et le compte de stockage Azure.

     ![Activer la réplication](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Ils sélectionnent **WebVM** pour la réplication, vérifient la stratégie de réplication et activent la réplication.

    - À ce stade, ils sélectionnent uniquement WEBVM, parce que le réseau virtuel et le sous-réseau doivent être sélectionnés et que les machines virtuelles de l’application seront placées dans des sous-réseaux différents.
    - Site Recovery installe automatiquement le service Mobilité sur la machine virtuelle quand la réplication est activée.

    ![Activer la réplication](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Contoso suit la progression de la réplication dans **Travaux**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.
6. Dans le portail Azure, dans **Bases**, ils peuvent afficher la structure des machines virtuelles répliquées sur Azure.


### <a name="enable-replication-for-sqlvm"></a>Activer la réplication pour SQLVM

Les administrateurs de Contoso peuvent maintenant démarrer la réplication de la machine virtuelle SQLVM en utilisant le même processus que celui décrit ci-dessus.

1. Ils sélectionnent les paramètres de la source.

    ![Activer la réplication](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Ils spécifient ensuite les paramètres de la cible.

     ![Activer la réplication](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Ils sélectionnent SQLVM pour la réplication. 

    ![Activer la réplication](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. Ils appliquent la même stratégie de réplication que celle utilisée pour WEBVM, et activent la réplication.

    ![Vue de l’infrastructure](./media/contoso-migration-rehost-vm/essentials.png)

**Besoin de plus d’aide ?**

- Une procédure pas à pas complète de toutes ces étapes est décrite dans [Configurer la récupération d’urgence pour des machines virtuelles VMware locales](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Des instructions détaillées sont disponibles pour vous aider à [configurer l’environnement source](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), à [déployer le serveur de configuration](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) et à [configurer les paramètres de réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Vous pouvez en apprendre davantage sur l’[activation de la réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Étape 4 : migrer les machines virtuelles 

Les administrateurs de Contoso exécutent un test rapide de basculement, puis un basculement complet pour migrer les machines virtuelles.

### <a name="run-a-test-failover"></a>Exécuter un test de basculement

Un test de basculement aide à vérifier que tout fonctionne comme prévu. 

1. Ils exécutent un test de basculement jusqu’au dernier point dans le temps disponible (**Dernier point traité**).
2. Contoso sélectionne **Shut down machine before beginning failover** (Arrêter la machine avant de commencer le basculement), de façon à ce que Site Recovery tente d’arrêter la machine virtuelle source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. 
3. Le test de basculement est exécuté : 

    - Une vérification des prérequis est effectuée pour garantir que toutes les conditions nécessaires pour la migration sont en place.
    - Le basculement traite les données pour permettre la création d’une machine virtuelle Azure. Si vous avez sélectionné le dernier point de récupération, un point de récupération est créé à partir des données.
    - Une machine virtuelle Azure est créée en utilisant les données traitées à l’étape précédente.
    
3. Une fois le basculement terminé, la machine virtuelle Azure répliquée apparaît dans le portail Azure. Les administrateurs de Contoso vérifient que la machine virtuelle est de la taille appropriée, qu’elle est connectée au bon réseau et qu’elle est en cours d’exécution. 
4. Après avoir vérifié le test du basculement, ils nettoient le basculement, puis consignent et enregistrent les observations éventuelles. 

### <a name="create-and-customize-a-recovery-plan"></a>Créer et personnaliser un plan de récupération

 Après avoir vérifié que le test de basculement fonctionne comme prévu, les administrateurs de Contoso créent un plan de récupération pour la migration. 

- Un plan de récupération spécifie l’ordre du basculement et indique comment les machines virtuelles Azure seront mises en ligne dans Azure.
- L’application étant à deux niveaux, ils personnalisent le plan de récupération afin que la machine virtuelle des données (SQLVM) démarre avant le serveur frontal (WEBVM).

1. Dans **Plans de récupération (Site Recovery)** > **+Plan de récupération**, ils créent un plan et y ajoutent les machines virtuelles.

    ![Plan de récupération](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Après avoir créé le plan, ils le personnalisent (**Plans de récupération** > **SmartHotelMigrationPlan** > **Personnaliser**).
2.  Ils suppriment WEBVM de **Groupe 1 : Démarrage**.  Cela garantit que la première action de démarrage affecte uniquement la machine virtuelle SQLVM.
3.  Dans **+Groupe** > **Ajouter des éléments protégés**, ils ajoutent WEBVM à Groupe 2 : Démarrage.  Les machines virtuelles doivent se trouver dans deux groupes différents.


### <a name="migrate-the-vms"></a>Migrer les machines virtuelles


Les administrateurs de Contoso peuvent à présent opérer un basculement complet pour achever la migration.

1. Ils sélectionnent le plan de récupération > **Basculement**.
2. Ils choisissent de basculer vers le dernier point de récupération et spécifient que Site Recovery doit essayer d’arrêter la machine virtuelle locale avant de déclencher le basculement. Contoso peut suivre la progression du basculement sur la page **Travaux**.

    ![Basculement](./media/contoso-migration-rehost-vm/failover1.png)


3. Après le basculement, elle vérifie que l’ordinateur virtuel Azure s’affiche comme prévu dans le portail Azure.

    ![Basculement](./media/contoso-migration-rehost-vm/failover2.png)  

3. Après vérification, ils achèvent la migration de chaque machine virtuelle. Cela met fin à la réplication de la machine virtuelle et arrête la facturation Site Recovery pour celle-ci.

    ![Basculement](./media/contoso-migration-rehost-vm/failover3.png)

**Besoin de plus d’aide ?**

- [En savoir plus sur](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) l’exécution d’un test de basculement. 
- [Découvrez](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) comment créer un plan de récupération.
- [Découvrez](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) comment basculer vers Azure.

## <a name="clean-up-after-migration"></a>Nettoyer après la migration

Une fois la migration terminée, les niveaux de l’application SmartHotel360 s’exécutent sur des machines virtuelles Azure.

Contoso doit à présent effectuer les étapes de nettoyage suivantes :  

- Supprimer la machine virtuelle WEBVM de l’inventaire vCenter.
- Supprimer la machine virtuelle SQLVM de l’inventaire vCenter.
- Supprimer les machines virtuelles WEBVM et SQLVM des travaux de sauvegarde locaux.
- Mettre à jour la documentation interne afin qu’elle indique le nouvel emplacement et les adresses IP des machines virtuelles.
- Passer en revue toutes les ressources qui interagissent avec les machines virtuelles, et mettre à jour les paramètres ou la documentation appropriés afin de refléter la nouvelle configuration.

## <a name="review-the-deployment"></a>Examiner le déploiement

L’application étant en cours d’exécution, Contoso doit à présent la rendre entièrement opérationnelle et la sécuriser dans Azure.

### <a name="security"></a>Sécurité

L’équipe de sécurité de Contoso examine les machines virtuelles Azure afin d’identifier d’éventuels problèmes de sécurité.

- Pour contrôler l’accès, l’équipe examine les groupes de sécurité réseau (NSG) des machines virtuelles. Les groupes de sécurité réseau sont utilisés pour s’assurer que seul le trafic autorisé vers l’application puisse atteindre celle-ci.
- L’équipe envisage également la sécurisation des données sur le disque à l’aide d’Azure Disk Encryption et de KeyVault.

[En savoir plus](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sur les pratiques de sécurité pour les machines virtuelles.

## <a name="bcdr"></a>BCDR

Pour assurer la continuité et la reprise d’activité (BCDR), Contoso effectue les actions suivantes :

- Sécuriser les données : Contoso sauvegarde les données sur les machines virtuelles à l’aide du service Sauvegarde Azure. [Plus d’informations](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- Faire en sorte que les applications soient opérationnelles : Contoso réplique les machines virtuelles de l’application dans Azure vers une région secondaire à l’aide de Site Recovery. [Plus d’informations](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)



### <a name="licensing-and-cost-optimization"></a>Gestion des licences et optimisation des coûts

1. Contoso a un contrat de licence pour ses machines virtuelles et tirera parti d’Azure Hybrid Benefit.  Contoso va convertir les machines virtuelles Azure existantes pour tirer parti de cette tarification.
2. Contoso va activer Azure Cost Management sous licence de Cloudyn, une filiale de Microsoft. Il s’agit d’une solution de gestion des coûts multicloud qui aide à utiliser et à gérer d’Azure ainsi que d’autres ressources cloud. [En savoir plus](https://docs.microsoft.com/azure/cost-management/overview) sur Azure Cost Management. 

## <a name="conclusion"></a>Conclusion

Cet article décrit comment Contoso a réhébergé l’application SmartHotel360 dans Azure en migrant les machines virtuelles de l’application vers des machines virtuelles Azure avec le service Site Recovery. 


## <a name="next-steps"></a>Étapes suivantes

Dans [l’article suivant](contoso-migration-rehost-vm-sql-ag.md) de la série, nous allons vous montrer comment Contoso réhéberge la machine virtuelle du frontend de l’application SmartHotel360 vers une machine virtuelle Azure, et comment il migre la base de données vers un groupe de disponibilité SQL Server AlwaysOn dans Azure.

