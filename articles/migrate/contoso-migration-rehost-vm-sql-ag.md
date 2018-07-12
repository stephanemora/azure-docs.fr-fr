---
title: Réhéberger une application Contoso en la migrant vers des machines virtuelles Azure et vers un groupes de disponibilité AlwaysOn SQL Server | Microsoft Docs
description: Découvrir comment Contoso réhéberge une application locale en la migrant vers des machines virtuelles Azure et vers un groupes de disponibilité AlwaysOn SQL Server
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 33ab56003f2d9428816ea0f32cfd6381ea857df3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611286"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-sql-server-alwayson-availability-group"></a>Migration de Contoso : réhéberger une application locale vers des machines virtuelles Azure et vers un groupe de disponibilité AlwaysOn SQL Server

Cet article explique comment Contoso réhéberge son application SmartHotel dans Azure. Elle effectue une migration de la machine virtuelle frontale de l’application vers une machine virtuelle Azure, et de la base de données de l’application vers une machine virtuelle SQL Server en cours d’exécution dans un cluster de basculement Windows Server avec des groupes de disponibilité SQL Server AlwaysOn.

Ce document fait partie d’une série d’articles qui montrent comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations générales et des scénarios qui illustrent comment configurer une infrastructure de migration, évaluer des ressources locales pour la migration et exécuter différents types de migration. Les scénarios croissant en complexité, nous ajouterons des articles au fil du temps.

**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Fournit une vue d’ensemble de la stratégie de migration de Contoso, de la série d’articles et des exemples d’application que nous utilisons. | Disponible
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Décrit comment Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration. | Disponible
[Article 3 : Évaluer les ressources locales](contoso-migration-assessment.md)  | Montre comment Contoso évalue une application à deux niveaux locale SmartHotel s’exécutant sur VMware. Contoso évalue les machines virtuelles de l’application avec le service [Azure Migrate](migrate-overview.md) et la base de données SQL Server de l’application avec [l’Assistant Migration de données Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponible
[Article 4 : Réhéberger une application sur des machines virtuelles Azure et une instance SQL Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Montre comment Contoso exécute une migration lift-and-shift vers Azure pour l’application SmartHotel. Elle migre la machine virtuelle frontale de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), et la base de données de l’application vers une instance SQL Managed Instance à l’aide du [service Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview). | Disponible
[Article 5 : Réhéberger une application sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Montre comment Contoso migre les machines virtuelles de l’application SmartHotel en utilisant uniquement Site Recovery.
Article 6 : Réhéberger une application sur des machines virtuelles et un groupes de disponibilité AlwaysOn SQL Server (cet article) | Montre comment Contoso migre l’application SmartHotel. Elle utilise Site Recovery pour migrer la machine virtuelle de l’application, et Database Migration Service pour migrer la base de données de l’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. | Disponible
[Article 7 : Réhéberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Montre comment Contoso effectue une migration lift-and-shift de l’application osTicket Linux sur des machines virtuelles Azure à l’aide de Site Recovery. | Prévu
[Article 8 : ré-héberger une application Linux sur des machines virtuelles Azure et Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Montre comment Contoso migre l’application osTicket Linux vers des machines virtuelles Azure à l’aide de Site Recovery, et migre la base de données de l’application vers une instance Azure MySQL Server à l’aide de MySQL Workbench. | Disponible



Cet article explique comment Contoso migre les fenêtres à deux niveaux. Application NET SmartHotel s’exécutant sur des machines virtuelles VMware vers Azure. Si vous souhaitez utiliser cette application, elle est disponible en open source et vous pouvez la télécharger à partir de [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec ses partenaires commerciaux pour comprendre le résultat qu’ils souhaitent obtenir avec cette migration :

- **Répondre à la croissance** : Contoso étant en croissance, son infrastructure et ses systèmes locaux subissent une pression.
- **Augmenter l’efficacité** : Contoso doit supprimer les procédures inutiles et rationaliser les processus pour les développeurs et les utilisateurs.  L’entreprise a besoin d’une informatique rapide et doit éviter de perdre du temps ou d’argent en répondant plus rapidement aux exigences des clients.
- **Augmenter l’agilité** : l’informatique de Contoso doit être plus réactive aux besoins de l’entreprise. Elle doit être en mesure de réagir plus rapidement que l’évolution du marché pour réussir dans une économie mondiale.  L’informatique ne doit pas devenir une entrave à l’activité.
- **Mise à l’échelle** : à mesure que l’entreprise croît, l’informatique de Contoso doit fournir des systèmes capables de croître au même rythme.

## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a épinglé les objectifs de cette migration. Ces objectifs ont été utilisés pour déterminer la meilleure méthode de migration :

- Après la migration, l’application devra offrir les mêmes performances dans Azure qu’aujourd’hui dans VMware.  L’application restera tout aussi vitale dans le cloud que localement.
- Contoso ne souhaite pas investir dans cette application.  Elle est importante pour l’entreprise mais, dans sa forme actuelle, le seul objectif est de la migrer vers le cloud en toute sécurité.
- La base de données locale de l’application a connu des problèmes de disponibilité. Contoso souhaite qu’elle soit déployée dans Azure en tant que cluster de haute disponibilité, avec des fonctionnalités de basculement.
- Contoso souhaite mettre à niveau de sa plateforme SQL Server 2008 R2 actuelle vers SQL Server 2017.
- Contoso ne souhaite pas utiliser Azure SQL Database pour cette application et recherche des alternatives.

## <a name="proposed-architecture"></a>Architecture proposée

Dans ce scénario :

- L’application est hiérarchisée sur deux machines virtuelles (WEBVM et SQLVM).
- Les machines virtuelles sont situées sur un hôte VMware ESXi **contosohost1.contoso.com** (version 6.5).
- L’environnement VMware est géré par le serveur vCenter Server 6.5 (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- Contoso dispose d’un centre de données local (contoso-datacenter), avec un contrôleur de domaine local (**contosodc1**).
- Les machines virtuelles locales dans le centre de données Contoso seront désaffectées une fois la migration terminée.

![Architecture du scénario](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="azure-services"></a>Services Azure

**Service** | **Description** | **Coût**
--- | --- | ---
[Service de gestion de base de données](https://docs.microsoft.com/azure/dms/dms-overview) | Contoso migrera la couche de données d’application à l’aide de DMS. DMS se connectera à l’ordinateur local SQLVM via un VPN de site à site et la migration de DMS permet des migrations fluides depuis plusieurs sources de base de données vers des plateformes de données Azure, avec un temps d’arrêt minimal. | Apprenez-en davantage sur les [régions couvertes](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) par DMS, et consultez les [prix](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Contoso utilise Site Recovery pour une migration de courbes d’élévation et MAJ de frontend application virtuelle. Site Recovery orchestre et gère la migration et la récupération d’urgence pour les machines virtuelles Azure, les machines virtuelles locales et les serveurs physiques.  | Lors de la réplication vers Azure, des frais sur le Stockage Azure sont facturés.  Des machines virtuelles Azure sont créées en cas de basculement, et entraînent des frais. [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les frais et la tarification.

 

## <a name="migration-process"></a>Processus de migration

- Contoso migrera l’application de machines virtuelles vers Azure.
- Elle migrera la machine virtuelle frontale vers la machine virtuelle Azure à l’aide de Site Recovery :
    - Dans un premier temps, elle va préparer et configurer les composants Azure et préparer l’infrastructure VMware locale.
    - Une fois tous les éléments préparés, la réplication de la machine virtuelle peut commencer.
    - Lorsque la réplication est activée et opère, la machine virtuelle est mígrée en la basculant vers Azure.
- La base de données est migrée vers un cluster SQL Server dans Azure avec le Service de Migration de données (DMS).
    - Dans un premier temps, il faut approvisionner les machines virtuelles SQL Server dans Azure, configurer le cluster et un équilibreur de charge interne, et configurer des groupes de disponibilité AlwaysOn.
    - Lorsque tout cela est en place, on peut migrer la base de données.
- Après la migration, la protection AlwaysOn est activée pour la base de données.

![Processus de migration](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Prérequis

Voici ce que vous (et Contoso) devez faire dans le cadre de ce scénario :

**Configuration requise** | **Détails**
--- | ---
**Abonnement Azure** | Vous devez avoir déjà créé un abonnement lorsque vous avez effectué l’évaluation dans le premier article de cette série. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur.<br/><br/> S’il vous faut plus d’autorisations granulaires, consultez [cet article](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastructure Azure** | [Découvrez comment](contoso-migration-infrastructure.md) Contoso configure une infrastructure Azure.<br/><br/> Apprenez-en davantage sur les configurations de [réseau](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) et de [stockage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) requises pour Site Recovery.
**Récupération de site (local)** | Votre serveur vCenter local doit exécuter la version 5.5, 6.0 ou 6.5<br/><br/> Un hôte ESXi qui exécute la version 5.5, 6.0 ou 6.5<br/><br/> Une ou plusieurs machines virtuelles VMware exécutées sur l’hôte ESXi.<br/><br/> Les machines virtuelles doivent répondre aux [exigences Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configuration [réseau](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) et [stockage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) prise en charge.<br/><br/> Les machines virtuelles que vous souhaitez répliquer doivent satisfaire aux [conditions requises par Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).
**DMS** | Pour le service DMS, il vous faut un [appareil VPN local compatible](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Vous devez être en mesure de configurer l’appareil VPN local. Il doit disposer d’une adresse IPv4 publique. Elle ne peut pas être placée derrière un appareil NAT.<br/><br/> Veillez à disposer de l’accès à votre base de données SQL Server locale.<br/><br/> Le pare-feu Windows doit pouvoir accéder au moteur de la base de données source. [Plus d’informations](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)<br/><br/> S’il y a un pare-feu devant votre machine de base de données, ajoutez des règles pour autoriser l’accès à la base de données, et aux fichiers via le port SMB 445.<br/><br/> Les informations d’identification utilisées pour se connecter à l’instance source SQL Server et à l’instance cible Managed Instance doivent appartenir à des membres du rôle serveur sysadmin.<br/><br/> Il vous faut un partage réseau dans votre base de données locale que le service DMS peut utiliser pour sauvegarder la base de données source.<br/><br/> Vérifiez que le compte de service qui exécute l’instance source de SQL Server dispose de privilèges d’écriture pour le partage réseau.<br/><br/> Notez l’utilisateur Windows (et son mot de passe) qui dispose d’un contrôle total sur le partage réseau. Azure Database Migration Service emprunte ces informations d’identifications pour charger les fichiers de sauvegarde sur le conteneur de stockage Azure.<br/><br/> Le processus d’installation SQL Server Express définit le protocole TCP/IP sur **Désactivé** par défaut. Veillez à l’activer.


## <a name="scenario-steps"></a>Étapes du scénario

Voici comment Contoso exécutera la migration :

> [!div class="checklist"]
> * **Étape 1 : création des machines virtuelles de SQL Server dans Azure**. Pour une haute disponibilité, Contoso souhaite déployer une base de données en cluster dans Azure. Déploiement de deux machines virtuelles SQL Server et d’un équilibreur de charge interne Azure.
> * **Étape 2 : déploiement du cluster**. Après le déploiement des machines virtuelles SQL Server, préparation d’un cluster de serveur SQL Azure.  Migration de la base de données dans ce cluster préalablement créé.
> * **Étape 3 : préparation du service DMS**. Pour préparer le service DMS , inscription du fournisseur de migration de base de données et création d’une instance DMS ainsi que d’un projet. Obtention d’un URI (Uniform Resource Identifier) de signature d’accès partagé (SAP). DMS utilise L’URI d’administrateur système pour accéder au conteneur de compte de stockage sur lequel le service charge les fichiers de sauvegarde SQL Server.
> * **Étape 4 : préparation d’Azure pour Site Recovery**. Un compte de stockage Azure pour accueillir les données répliquées et un coffre Recovery Services sont créés.
> * **Étape 5 : préparation d’une machine virtuelle VMware locale pour Site Recovery**. Il s’agit de préparer des comptes pour la détection de machines virtuelles et l’installation d’agents, ainsi que des machines virtuelles qui pourront se connecter aux machines virtuelles Azure après basculement.
> * **Étape 6 : répliquer les machines virtuelles**. Les paramètres de réplication sont configurés pour permettre la réplication des machines virtuelles.
> * **Étape 7 : migrer la base de données avec DMS**. La base de données est migrée.
> * **Étape 8 : migrer les machines virtuelles avec Site Recovery**. Un test de basculement est d’abord effectué pour s’assurer que tout fonctionne correctement, avant d’opérer un basculement complet pour migrer la machine virtuelle.


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>Étape 1 : préparer un cluster de groupes à haute disponibilité SQL Server AlwaysOn

Contoso souhaite planifier en déployant la base de données au sein d’un cluster dans Azure. Elle peut ensuite utiliser ce cluster pour d’autres bases de données. 

- Les machines virtuelles SQL Server seront déployées dans le groupe de ressources ContosoRG (utilisé pour les ressources de production).
- Indépendamment de noms uniques, les deux machines virtuelles utilisent les mêmes paramètres.
- L’équilibreur de charge interne sera déployé dans ContosoNetworkingRG (utilisé pour les ressources de mise en réseau).
- Les machines virtuelles exécuteront Windows Server 2016 avec l’édiction Entreprise de SQL Server 2017. Contoso n’a pas de licences pour ce système d’exploitation et utilisera donc une image dans la place de marché Azure qui fournit la licence associée à un contrat d’entreprise contre paiement.

Voici comment Contoso configure le cluster :

1. Deux machines virtuelles SQL Server sont créées en sélectionnant l’image de SQL Server 2017 Enterprise Windows Server 2016 dans la place de marché Azure. 

    ![Référence SKU de la machine virtuelle SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. Dans l’**Assistant Créer une machine virtuelle** > **Fonctions de base**, on configure :

    - Noms des machines virtuelles : **SQLAOG1** et **SQLAOG2**.
    - Étant donné que les machines sont vitales pour l’entreprise, on active SSD comme type de disque de machine virtuelle.
    - Des informations d’identification sont spécifiées pour la machine.
    - Les machines virtuelles sont déployées dans la région primaire de l’Est des États-Unis 2, dans le groupe de ressources ContosoRG.

3. Dans **Taille**, on commence par la référence SKU D2s_V3 pour les deux machines virtuelles. Elles seront mises à l’échelle ultérieurement, en fonction des besoins.
4. Dans **Paramètres**, on procède comme suit :

    - Étant donné que ces machines virtuelles sont des bases de données critiques pour l’application, des disques disque managé sont utilisés.
    - Les machines sont placées dans le réseau de production de la région primaire de l’Est des États-Unis 2(**VNET-PROD-EUS2**), dans le sous-réseau de la base de données (**PROD-DB-EUS2**).
    - Un nouveau groupe à haute disponibilité est créé : **SQLAOGAVSET**, avec deux domaines d’erreur et cinq domaines de mise à jour.

    ![Machine virtuelle SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. Dans **Paramètres SQL Server**, la connectivité SQL sur le réseau virtuel (privé) est limitée sur le port par défaut 1433. Pour l’authentification, les mêmes informations d’identification que sur le site sont utilisées (**contosoadmin**).

    ![Machine virtuelle SQL](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**Besoin de plus d’aide ?**

- [Obtenir de l’aide](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) pour l’approvisionnement d’une machine virtuelle SQL Server.
- [En savoir plus sur la](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) configuration de machines virtuelles pour les différentes références SKU de SQL Server.

## <a name="step-2-deploy-the-failover-cluster"></a>Étape 2 : le cluster de basculement

Voici comment Contoso configure le cluster :

1. Un compte de stockage Azure est configuré en tant que le témoin de cloud.
2. Les machines virtuelles SQL Server sont ajoutées au domaine Active Directory dans le centre de données local Contoso.
3. Le cluster est créé dans Azure.
4. Le témoin cloud est configuré.
5. Enfin, les groupes de disponibilité SQL Always On sont activés.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Configurer un compte de stockage en tant que témoin de cloud

Pour configurer un témoin de cloud, Contoso a besoin d’un compte de stockage Azure qui contiendra le fichier blob utilisé pour l’arbitrage du cluster. Le même compte de stockage peut être utilisé afin de configurer un témoin de cloud pour plusieurs clusters. 

Contoso crée un compte de stockage comme suit :

1. Un nom identifiable est spécifié pour le compte (**contosocloudwitness**).
2. Un compte polyvalent général est déployé, avec stockage localement redondant (LRS).
3. Ce compte est placé dans une région tierce, à savoir les USA Centre Sud. Il est placé en dehors des régions primaire et secondaire et reste donc disponible en cas de panne régionale.
4. Il se trouve dans le groupe de ressources de Contoso, qui contient les ressources d’infrastructure : **ContosoInfraRG**.

    ![Témoin de cloud](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. Lors de la création du compte de stockage, des clé d'accès principale et secondaire sont générées pour lui. La clé d’accès principale est nécessaire pour créer le témoin de cloud. La clé s’affiche sous le nom du compte de stockage > **Clés d’accès**.

    ![Clé d’accès](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Ajouter des machines virtuelles SQL Server au domaine Contoso

1. Contoso ajoute SQLAOG1 et SQLAOG2 au domaine contoso.com.
2. Ensuite, sur chaque machine virtuelle le cluster de basculement et les outils Windows sont installés.

## <a name="set-up-the-cluster"></a>Configurer le cluster

Avant de configurer le cluster, Contoso prend un instantané du disque du système d’exploitation sur chaque machine.

![instantané](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

2. Ensuite, elle exécute un script assemblé pour créer le cluster de basculement Windows.

    ![Créer un cluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

3. Après la création du cluster, on vérifie que les machines virtuelles s’affichent en tant que nœuds de cluster.

     ![Créer un cluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>Configurer le témoin de cloud

1. Contoso configure le témoin de cloud à l’aide de l’**Assistant de configuration de quorum** dans le gestionnaire du cluster de basculement.
2. Dans l’Assistant, on sélectionne un témoin de cloud avec le compte de stockage.
3. Une fois que le témoin de cloud est configuré, il s’affiche dans le composant logiciel enfichable Gestionnaire du Cluster de basculement.

    ![Témoin de cloud](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

## <a name="enable-sql-server-always-on-availability-groups"></a>Activer les groupes de disponibilité SQL Server Always On

Contoso peut désormais activer Always On :

1. Dans le gestionnaire de configuration SQL Server, les **groupes de disponibilité AlwaysOn** sont activés pour le service **SQL Server (MSSQLSERVER)**.

    ![Activer AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. Le service est redémarré pour que les modifications prennent effet.

Une fois qu’AlwaysOn est activé, Contoso peut configurer le groupe de disponibilité AlwaysOn qui protègera la base de données SmartHotel.

**Besoin de plus d’aide ?**

- [En savoir plus sur](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) le témoin de cloud et la configuration d’un compte de stockage pour celui-ci.
- [Obtenir des instructions](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) pour la configuration d’un cluster et la création d’un groupe de disponibilité.

## <a name="step-3-deploy-the-azure-load-balancer"></a>Étape 3 : déployer l’équilibreur de charge Azure.

Contoso souhaite maintenant déployer un équilibreur de charge interne situé devant les nœuds de cluster. L’équilibrage de charge écoute le trafic et le dirige vers le nœud approprié.

![Équilibrage de la charge](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

L’équilibrage de charge est créé comme suit :

1. Dans le portail Azure > **Mise en réseau** > **Équilibreur de charge**, on configure un nouvel équilibreur de charge interne : **ILB-PROD-DB-EUS2-SQLAOG**.
2. L’équilibreur de charge est placé dans le réseau de production **VNET-PROD-EUS2**, dans le sous-réseau de base de données **PROD-DB-EUS2**.
3. Une adresse IP statique lui est affectée : 10.245.40.100.
4. En tant qu’élément de mise en réseau, l’équilibreur de charge est déployé dans le groupe de ressources de mise en réseau **ContosoNetworkingRG**.

    ![Équilibrage de la charge](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

Après le déploiement de l’équilibreur de charge interne, Contoso doit le configurer. Elle crée un pool d’adresses principales, configure une sonde d’intégrité et configure une règle d’équilibrage de charge.

### <a name="add-a-backend-pool"></a>Ajouter un pool principal

Pour distribuer le trafic vers les machines virtuelles du cluster, Contoso configure un pool d’adresses principal qui contient les adresses IP des cartes réseau pour les machines virtuelles qui recevront le trafic réseau à partir de l’équilibreur de charge.

1. Dans les paramètres de l’équilibreur de charge dans le portail, Contoso ajoute un pool principal : **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. Il associe le pool au groupe à haute disponibilité SQLAOGAVSET. Les machines virtuelles dans l’ensemble (**SQLAOG1** et **SQLAOG2**) sont ajoutées au pool.

    ![Pool principal](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

Contoso crée une sonde d’intégrité pour permettre à l’équilibreur de charge de surveiller l’état de l’application. La sonde d’intégrité ajoute ou supprime dynamiquement des machines virtuelles de la rotation de l’équilibreur de charge en fonction de leur réponse aux vérifications d’intégrité.

La sonde est créée comme suit : 

1. Dans les paramètres de l’équilibreur de charge dans le portail, Contoso crée une sonde d’intégrité : **SQLAlwaysOnEndPointProbe**.
2. La sonde est configurée pour surveiller les machines virtuelles sur le port TCP 59999.
3. Un intervalle de 5 secondes et un seuil de 2 sont définis entre les sondes. Si deux sondes échouent, la machine virtuelle est considérée comme défectueuse.

    ![Sonde](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>Configurer l’équilibreur de charge pour recevoir le trafic


Contoso a maintenant besoin d’une règle d’équilibreur de charge pour définir la distribution du trafic vers les machines virtuelles.

- L’adresse IP frontale gère le trafic entrant.
- Le pool d’IP principal reçoit le trafic.

La règle est créée comme suit :

1. Dans les paramètres de l’équilibreur de charge dans le portail, une nouvelle règle d’équilibrage de charge est ajoutée : **SQLAlwaysOnEndPointListener**.
2. Contoso définit un écouteur frontal pour recevoir le trafic entrant du client SQL sur TCP 1433.
3. Elle spécifie le pool principal vers lequel le trafic sera acheminé et le port sur lequel les machines virtuelles écoutent le trafic.
4. Contoso active l’adresse IP flottante (retour direct du serveur). Elle est toujours requise pour SQL AlwaysOn.

    ![Sonde](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**Besoin de plus d’aide ?**

- [Obtenez une vue d’ensemble](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) d’Azure Load Balancer.
- [En savoir plus sur la](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) création d’un équilibreur de charge.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>Étape 4 : préparer Azure pour le service Site Recovery

Les composants Azure dont Contoso a besoin pour déployer Site Recovery sont les suivants :

- Un réseau virtuel dans lequel les machines virtuelles seront situées après leur création pendant le basculement.
- Un compte de stockage Azure pour accueillir les données répliquées. 
- Un coffre Recovery Services dans Azure.

La configuration est effectuée´comme suit :

1.  Contoso a déjà crée un réseau/sous-réseau utilisable pour Site Recovery lors du [déploiement de l’infrastructure Azure](contoso-migration-rehost-vm-sql-ag.md).

    - L’application SmartHotel est une application de production, et la machine virtuelle WEBVM sera migrée vers le réseau de production Azure (VNET-PROD-EUS2) dans la région USA Est 2 principale.
    - La machine virtuelle WEBVM sera placée dans le groupe de ressources ContosoRG utilisé pour les ressources de production et dans le sous-réseau de production (PROD-FE-EUS2).

2. Contoso crée un compte de stockage Azure (contosovmsacc20180528) dans la région principale.

    - Ils utilisent un compte à usage général, avec un stockage standard et une réplication de stockage localement redondant.
    - Ce compte doit se trouver dans la même région que le coffre.

    ![Stockage Site Recovery](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Avec le réseau et le compte de stockage en place, Contoso crée à présent un coffre Recovery Services (**ContosoMigrationVault**) et le place dans le groupe de ressources **ContosoFailoverRG**, dans la région USA Est 2 principale.

    ![Coffre Recovery Services](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**Besoin de plus d’aide ?**

[En savoir plus sur la](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) configuration d’Azure pour Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Étape 4 : préparer VMware en local pour Site Recovery

Voici ce que Contoso prépare localement :

- Un compte sur le serveur vCenter ou sur l’hôte vSphere ESXi pour automatiser la détection de machines virtuelles.
- Un compte permettant l’installation automatique du service Mobilité sur les machines virtuelles VMware à répliquer.
- Des paramètres de machine virtuelle locale pour que Contoso puisse se connecter aux machines virtuelles Azure répliquées après le basculement.


### <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique

Site Recovery doit pouvoir accéder aux serveurs VMware pour :

- Détectez automatiquement les machines virtuelles. 
- Orchestrer la réplication, le basculement et la restauration automatique.
- Au moins un compte en lecture seule est nécessaire. Il vous faut un compte qui peut exécuter des opérations telles que la création et la suppression de disques, et l’allumage de machines virtuelles.

Contoso configure le compte comme suit :

1. Contoso crée un rôle au niveau du serveur vCenter.
2. Contoso attribue ensuite à ce rôle les autorisations requises.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Préparer un compte pour l’installation du service Mobilité

Le service Mobilité doit être installé sur chaque machine virtuelle.

- Site Recovery peut faire une installation automatique par push de ce composant lorsque la réplication est activée pour la machine virtuelle.
- Vous avez besoin d’un compte dont Site Recovery pourra se servir pour accéder à la machine virtuelle pour l’installation par push. Vous spécifiez ce compte quand vous configurez la réplication dans la console d’Azure.
- Il vous faut un compte de domaine ou local avec les autorisations nécessaires pour l’installation sur la machine virtuelle.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer la connexion aux machines virtuelles Azure après le basculement

Après le basculement, Contoso souhaite se connecter à des machines virtuelles Azure. Pour ce faire, elle effectue les opérations suivantes avant la migration :

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
- [En savoir plus sur la](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) création d’un compte pour une installation push du service Mobilité.


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Étape 5 : répliquer les machines virtuelles locales sur Azure avec Site Recovery

Avant de pouvoir exécuter une migration vers Azure, Contoso doit configurer et activer la réplication.

### <a name="set-a-replication-goal"></a>Définir un objectif de réplication

1. Dans le coffre, sous le nom du coffre (ContosoVMVault), on définit un objectif de réplication (**Prise en main** > **Site Recovery** > **Préparer l’infrastructure**).
2. On spécifie que les ordinateurs sont situés localement, qu’ils s’exécutent sur VMware et qu’ils sont répliqués sur Azure.

    ![Objectif de réplication](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmer la planification d’un déploiement

Pour continuer, il faut confirmer que la planification du déploiement est terminée en sélectionnant **Yes, I have done it** (Oui, c’est fait). Dans ce scénario, Contoso ne migre qu’une machine virtuelle et n’a pas besoin de planification du déploiement.

### <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Contoso doit configurer son environnement source. Pour ce faire, elle télécharge un modèle OVF et l’utilise pour déployer le serveur de configuration Site Recovery comme étant une machine virtuelle VMware locale hautement disponible. Une fois le serveur de configuration opérationnel, elle l’inscrit dans le coffre.

Le serveur de configuration exécute plusieurs composants :

- Le composant de serveur de configuration qui coordonne la communication entre les ordinateurs locaux et Azure, et gère la réplication des données.
- Le serveur de traitement qui fait office de passerelle de réplication. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure.
- De plus, le serveur de processus installe le service Mobilité sur les machines virtuelles que vous voulez répliquer et effectue la détection automatique sur les machines virtuelles VMware locales.

Contoso procède comme suit :


1. Dans le coffre, elle télécharge le modèle OVF à partir de **Préparer l’Infrastructure** > **Source** > **Serveur de configuration**.
    
    ![Télécharger le modèle OVF](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. Le modèle est importé dans VMware pour créer et déployer la machine virtuelle.

    ![Modèle OVF](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. Lors de la première activation de la machine virtuelle, celle-ci démarre dans un environnement d’installation Windows Server 2016. Contoso accepte le contrat de licence, puis entre un mot de passe d’administrateur.
4. Une fois l’installation terminée, elle se connecte à la machine virtuelle en tant qu’administrateur. À la première connexion, l’outil de configuration d’Azure Site Recovery s’exécute par défaut.
5. Dans l’outil, il faut spécifier un nom à utiliser pour l’inscription du serveur de configuration dans le coffre.
6. L’outil vérifie que la machine virtuelle peut se connecter à Azure. Une fois la connexion établie, la machine se connecte à l’abonnement Azure. Les informations d’identification doivent avoir accès au coffre dans lequel vous souhaitez inscrire le serveur de configuration.

    ![Inscrire un serveur de configuration](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. L’outil effectue des tâches de configuration, puis redémarre.
8. Il y a une nouvelle connexion à la machine et l’Assistant de gestion du serveur de configuration démarre automatiquement.
9. Dans l’Assistant, la carte réseau qui doit recevoir le trafic de réplication est sélectionnée. Une fois configuré, ce paramètre ne peut pas être modifié.
10. L’abonnement, le groupe de ressources et le coffre dans lequel inscrire le serveur de configuration sont sélectionnés.
        ![coffre](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. Ensuite, on télécharge puis on installe MySQL Server et VMWare PowerCLI. 
11. Après la validation, le nom de domaine complet (FQDN) ou l’adresse IP du serveur vCenter ou de l’hôte vSphere sont spécifiés. On quitte le port par défaut, puis on spécifie un nom convivial pour le serveur vCenter.
12. Le compte qu’ils ont créés pour la détection automatique et les informations d’identification utilisées pour installer automatiquement le service Mobilité sont spécifiés. Pour des machines virtuelles Windows, le compte doit disposer des privilèges d’administrateur local sur celles-ci.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. Une fois l’inscription terminée, dans le portail Azure, Contoso vérifie que le serveur de configuration et le serveur VMware sont répertoriés sur la page **Source** dans le coffre. La détection peut prendre 15 minutes ou plus. 
8. Site Recovery se connecte ensuite aux serveurs VMware en utilisant les paramètres spécifiés et détecte les machines virtuelles.

### <a name="set-up-the-target"></a>Configurer la cible

Maintenant, Contoso spécifie les paramètres de réplication de la cible.

1. Dans **Préparer l’infrastructure** > **Cible**, les paramètres de la cible sont sélectionnés.
2. Site Recovery vérifie qu’il existe un compte de stockage et un réseau Azure dans la cible spécifiée.

### <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

Maintenant, Contoso peut créer une stratégie de réplication.

1. Dans **Préparer l’infrastructure** > **Paramètres de réplication** > **Stratégie de réplication** >  **Créer et associer**, elle crée une stratégie **ContosoMigrationPolicy**.
2. Elle utilise les paramètres par défaut :
    - **Seuil d'objectif de point de récupération** : la valeur par défaut est 60 minutes. Cette valeur définit la fréquence à laquelle les points de récupération sont créés. Une alerte est générée lorsque la réplication continue dépasse cette limite.
    - **Rétention des points de récupération**. La valeur par défaut est 24 heures. Cette valeur spécifie la durée de la fenêtre de rétention pour chaque point de récupération. Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre.
    - **Fréquence des captures instantanées de cohérence d’application**. La valeur par défaut est une heure. Cette valeur spécifie la fréquence à laquelle les captures instantanées de cohérence d’application sont créées.
 
        ![Créer une stratégie de réplication](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. La stratégie est automatiquement associée au serveur de configuration. 

    ![Associer la stratégie de réplication](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>Activer la réplication

Maintenant, Contoso peut commencer à répliquer la machine virtuelle WebVM.

1. Dans **Répliquer l’application** > **Source** > **+Répliquer**, elle sélectionne les paramètres de la source.
2. Ces paramètres indiquent qu’ils souhaitent activer les machines virtuelles, puis sélectionnent le serveur vCenter et le serveur de configuration.

    ![Activer la réplication](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. On spécifie maintenant les paramètres de la cible, dont le groupe de ressources et le réseau virtuel, ainsi que le compte de stockage dans lequel les données répliquées seront stockées.

     ![Activer la réplication](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Contoso sélectionne la machine WebVM pour la réplication, vérifie la stratégie de réplication et active la réplication. Site Recovery installe le service Mobilité sur la machine virtuelle quand la réplication est activée.
 
    ![Activer la réplication](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. La progression de la réplication est suivie dans **Travaux**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.
5. Dans le portail Azure, dans **Bases**, Contoso peut afficher la structure des machines virtuelles répliquées sur Azure.

    ![Vue de l’infrastructure](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**Besoin de plus d’aide ?**

- Une procédure pas à pas complète de toutes ces étapes est décrite dans [Configurer la récupération d’urgence pour des machines virtuelles VMware locales](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Des instructions détaillées sont disponibles pour vous aider à [configurer l’environnement source](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), à [déployer le serveur de configuration](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) et à [configurer les paramètres de réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Vous pouvez en apprendre davantage sur l’[activation de la réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-install-the-database-migration-assistant-dma"></a>Étape 5 : installer l’Assistant Migration de données (DMA)

Contoso migrera la base de données SmartHotel vers la machine virtuelle Azure **SQLAOG1** à l’aide du DMA. Le DMA est configuré comme suit :

1. Télécharger l’outil à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=53595) sur la machine virtuelle locale SQL Server (**SQLVM**).
2. Exécuter le programme d’installation (DownloadMigrationAssistant.msi) sur la machine virtuelle.
3. Dans la page **Terminer**, sélectionner **Lancer l’Assistant Migration de données Microsoft** avant la fin de l’exécution de l’Assistant.

## <a name="step-6-migrate-the-database-with-dma"></a>Étape 6 : migrer la base de données avec le DMA

1. Exécuter une nouvelle migration dans le DMA : **SmartHotel**.
2. Sélectionner le **Type de serveur source** comme **serveur SQL Server sur machines virtuelles Azure**. 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. Dans les détails de la migration, ajouter **SQLVM** comme serveur source et **SQLAOG1** comme cible. Spécifier des informations d’identification pour chaque machine.

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. Créer une part locale pour la base de données et les informations de configuration. Elle doit être accessible en écriture par le compte de Service SQL sur SQLVM et SQLAOG1.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso sélectionne les connexions qui doivent être migrées et démarre la migration. Une fois celle-ci terminée, DMA affiche la migration comme ayant réussi.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. Vérifie que la base de données est en cours d’exécution sur **SQLAOG1**.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

DMS se connecte à la machine virtuelle SQL Server locale via une connexion VPN de site à site entre le centre de données de Contoso et Azure, puis migre la base de données.

## <a name="step-7-protect-the-database"></a>Étape 7 : protéger la base de données

Contoso peut protéger la base de données de l’application en cours d’exécution sur **SQLAOG1** avec les groupes de disponibilité AlwaysOn. Elle configure AlwaysOn à l’aide de SQL Management Studio, puis attribuez un écouteur à l’aide du clustering Windows. 

### <a name="create-an-alwayson-availability-group"></a>Créer un groupe de disponibilité AlwaysOn

1. Dans SQL Management Studio, cliquer avec le bouton droit sur **Haute disponibilité Always On** pour démarrer l’**Assistant du nouveau groupe de disponibilité**.
2. Dans **Spécifier les options**, nommer le groupe de disponibilité **SHAOG**. Dans **Sélectionner les bases de données**, sélectionner la base de données SmartHotel.

    ![Groupe de disponibilité AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. Dans **Spécifier les réplicas**, ajouter les deux nœuds SQL en tant que réplicas de disponibilité et les configurer pour assurer un basculement automatique avec validation synchrone.

     ![Groupe de disponibilité AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. Un écouteur est configuré pour le groupe (**SHAOG**) et le port. L’adresse IP de l’équilibreur de charge interne est ajouté en tant qu’adresse IP statique (10.245.40.100).

    ![Groupe de disponibilité AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. L’amorçage automatique est activé dans **Sélectionner la synchronisation des données**. Avec cette option, SQL Server crée automatiquement les réplicas secondaires pour chaque base de données du groupe, pour que Contoso n’ait pas à les sauvegarder et à les restaurer manuellement. Après la validation, le groupe de disponibilité est créé.

    ![Groupe de disponibilité AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso a rencontré un problème lors de la création du groupe. Elle n’utilise pas la sécurité intégrée d’Active Directory et doit donc accorder des autorisations au compte de connexion SQL pour créer les rôles de cluster de basculement Windows.

    ![Groupe de disponibilité AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. Une fois que le groupe est créé, Contoso peut le voir dans SQL Management Studio.

### <a name="configure-a-listener-on-the-cluster"></a>Configurer un écouteur sur le cluster

Lors de la dernière étape de configuration du déploiement de SQL, Contoso configure l’équilibreur de charge interne en tant que l’écouteur sur le cluster et met l’écouteur en ligne. Elle utilise un script pour effectuer cette tâche.

![Écouteur de cluster](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Vérifier la configuration

Une fois que tout est configuré, Contoso a un groupe de disponibilité fonctionnelle dans Azure qui utilise la base de données migrée. Elle le vérifie grâce à la connexion à l’équilibreur de charge interne dans SQL Management Studio.

![Connexion de l’équilibreur de charge interne](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**Besoin de plus d’aide ?**
- En savoir plus sur la création d’un [groupe de disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) et d’un [écouteur](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- [Configurer le cluster pour qu’il utilise l’adresse IP de l’équilibreur de charge](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address) (manuellement).
- [En savoir plus](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) sur la création et l’utilisation de SAP.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>Étape 8 : migrer la machine virtuelle avec Site Recovery

Contoso exécute un test rapide de basculement, puis migre la machine virtuelle.

### <a name="run-a-test-failover"></a>Exécuter un test de basculement

L’exécution d’un test de basculement permet de vérifier que tout fonctionne comme prévu avant la migration. 

1. Contoso exécute un test de basculement jusqu’au dernier point dans le temps disponible (**Dernier point traité**).
2. Elle sélectionne **Shut down machine before beginning failover** (Arrêter la machine avant de commencer le basculement), de façon à ce que Site Recovery tente d’arrêter la machine virtuelle source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. 
3. Le test de basculement est exécuté : 

    - Une vérification des prérequis est effectuée pour garantir que toutes les conditions nécessaires pour la migration sont en place.
    - Le basculement traite les données pour permettre la création d’une machine virtuelle Azure. Si vous avez sélectionné le dernier point de récupération, un point de récupération est créé à partir des données.
    - Une machine virtuelle Azure est créée en utilisant les données traitées à l’étape précédente.

3. Une fois le basculement terminé, la machine virtuelle Azure répliquée apparaît dans le portail Azure. Contoso vérifie que la machine virtuelle est de la taille appropriée, qu’elle est connectée au bon réseau et qu’elle est en cours d’exécution. 
4. Après ces vérifications, elle nettoie le basculement, puis consigne et enregistre les observations éventuelles. 

### <a name="run-a-failover"></a>Exécuter un basculement

1. Après avoir vérifié que le test de basculement a fonctionné comme prévu, Contoso crée un plan de récupération pour la migration et ajoute une machine WEBVM au plan.

     ![Plan de récupération](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. Un basculement est exécuté sur le plan. Contoso sélectionne le dernier point de récupération et spécifie que Site Recovery doit essayer d’arrêter la machine virtuelle locale avant de déclencher le basculement.

    ![Basculement](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. Après le basculement, elle vérifie que l’ordinateur virtuel Azure s’affiche comme prévu dans le portail Azure.

    ![Plan de récupération](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Après avoir vérifié la machine virtuelle dans Azure, elle achève le processus de migration, arrête la réplication de la machine virtuelle, et arrête la facturation de Site Recovery pour la machine virtuelle.

    ![Basculement](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Mettre à jour la chaîne de connexion

La dernière étape du processus de migration pour Contoso consiste à mettre à jour la chaîne de connexion de l’application pour qu’elle pointe vers la base de données migrée s’exécutant sur l’écouteur SHAOG. Cette configuration est modifiée sur la machine virtuelle WEBVM en cours d’exécution dans Azure.  Cette configuration se trouve dans le fichier web.config de l’application ASP. 

1. Rechercher le fichier sous C:\inetpub\SmartHotelWeb\web.config.  Modifier le nom du serveur afin de refléter le nom de domaine complet d’AOG : shaog.contoso.com.

    ![Basculement](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. Après avoir mis à jour et enregistré le fichier, elle redémarre IIS sur la machine WEBVM. Pour ce faire, elle entre la commande IISRESET /RESTART à partir d’une invite de commande.
2. Après le redémarrage d’IIS, l’application utilise la base de données en cours d’exécution sur l’instance SQL MI.


**Besoin de plus d’aide ?**

- [En savoir plus sur](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) l’exécution d’un test de basculement. 
- [Découvrez](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) comment créer un plan de récupération.
- [Découvrez](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) comment basculer vers Azure.

## <a name="clean-up-after-migration"></a>Nettoyer après la migration

Après la migration, l’application SmartHotel s’exécute sur une machine virtuelle Azure, et la base de données SmartHotel se trouve dans le cluster Azure SQL.

Contoso doit à présent effectuer les étapes de nettoyage suivantes :  

- Supprimer les machines virtuelles locales de l’inventaire vCenter.
- Supprimer les machines virtuelles des travaux de sauvegarde locale.
- Mettre à jour la documentation interne afin qu’elle indique les nouveaux emplacements et les adresses IP des machines virtuelles.
- Passer en revue toutes les ressources qui interagissent avec les machines virtuelles désactivées, et mettre à jour les paramètres ou la documentation appropriés afin de refléter la nouvelle configuration.
- Ajouter les deux nouvelles machines virtuelle (SQLAOG1 et SQLAOG2) aux systèmes de surveillance de la production.

## <a name="review-the-deployment"></a>Examiner le déploiement

Avec les ressources migrées dans Azure, Contoso doit rendre sa nouvelle infrastructure entièrement opérationnelle et la sécuriser.

### <a name="security"></a>Sécurité

L’équipe de sécurité de Contoso examine les machines virtuelles Azure WEBVM, SQLAOG1 et SQLAOG2 afin d’identifier d’éventuels problèmes de sécurité. 

- Elle examine les groupes de sécurité réseau (NSG) pour la machine virtuelle afin de contrôler l’accès. Les NSG sont utilisés pour s’assurer que seul le trafic autorisé vers l’application peut passer.
- Elles prennent également en considération la sécurisation des données sur le disque à l’aide d’Azure Disk Encryption et de KeyVault.
- Elles doivent évaluer le chiffrement transparent des données (TDE), puis l’activer sur la base de données SmartHotel en cours d’exécution sur le nouvel OG SQL. [Plus d’informations](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)

[En savoir plus](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sur les pratiques de sécurité pour les machines virtuelles.

### <a name="backups"></a>Sauvegardes

Contoso va sauvegarder les données sur les machines virtuelles WEBVM, SQLAOG1 et SQLAOG2 à l’aide du service Sauvegarde Azure. [Plus d’informations](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="licensing-and-cost-optimization"></a>Gestion des licences et optimisation des coûts

1. Contoso a un contrat de licence pour sa machine virtuelle WEBVM et tirera parti d’Azure Hybrid Benefit.  Elle va convertir les machines virtuelles Azure existantes pour profiter de cette tarification.
2. Contoso va activer Azure Cost Management sous licence de Cloudyn, une filiale de Microsoft. Il s’agit d’une solution de gestion des coûts multicloud qui aide à utiliser et à gérer Azure ainsi que d’autres ressources cloud.  [En savoir plus](https://docs.microsoft.com/azure/cost-management/overview) sur Azure Cost Management. 

## <a name="conclusion"></a>Conclusion

Cet article décrit comment Contoso a réhébergé l’application SmartHotel dans Azure en migrant la machine virtuelle frontale de celle-ci vers Azure à l’aide du service Site Recovery. Ils migrées de la base de données d’application vers un cluster de SQL Server configuré dans Azure et protégé dans un groupe de disponibilité AlwaysOn de SQL Server.

## <a name="next-steps"></a>Étapes suivantes

Dans l’article suivant de la série, nous allons montrer comment Contoso réhéberge son système d’exploitation de Service Desk, l’application Ticket, en cours d’exécution sur Linux et déployé avec une base de données MySQL.


