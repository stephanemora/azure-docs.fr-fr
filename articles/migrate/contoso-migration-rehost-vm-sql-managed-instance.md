---
title: Réhéberger une application locale Contoso en migrant vers une machine virtuelle Azure et une instance Azure SQL Managed Instance | Microsoft Docs
description: Découvrez comment Contoso ré-héberge une application locale sur des machines virtuelles Azure et Azure SQL Database Managed Instance
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 49a5fb13a881b206c36dcd08a4c2945880e9a4bd
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002294"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-azure-vms-and-azure-sql-managed-instance"></a>Migration de Contoso : réhéberger une application locale sur des machines virtuelles Azure et une instance Azure SQL Managed Instance

Cet article explique comment Contoso migre la machine virtuelle frontale de son application SmartHotel vers des machines virtuelles Azure à l’aide du service Azure Site Recovery, et la base de données de l’application vers une instance Azure SQL Database Managed Instance.

> [!NOTE]
> Azure SQL Database Managed Instance est actuellement en préversion.

Ce document fait partie d’une série d’articles décrivant comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations d’arrière-plan et des scénarios qui montrent comment configurer une infrastructure de migration et exécuter différents types de migrations. Les scénarios croissent en complexité, et nous ajouterons des articles au fil du temps.


**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Fournit une vue d’ensemble de la stratégie de migration de Contoso, de la série d’articles et des exemples d’application que nous utilisons. | Disponible
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Décrit comment Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration. | Disponible
[Article 3 : Évaluer les ressources locales à migrer vers Azure](contoso-migration-assessment.md)  | Montre comment Contoso évalue une application à deux niveaux locale SmartHotel s’exécutant sur VMware. Contoso évalue les machines virtuelles de l’application avec le service [Azure Migrate](migrate-overview.md) et la base de données SQL Server de l’application avec [l’Assistant Migration de données Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponible
Article 4 : Réhéberger une application sur des machines virtuelles Azure et une instance SQL Managed Instance | Montre comment Contoso exécute une migration lift-and-shift vers Azure pour l’application SmartHotel locale. Elle migre la machine virtuelle frontale de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), et la base de données de l’application vers une instance SQL Managed Instance à l’aide du [service Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview). | Cet article.
[Article 5 : Réhéberger une application sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Montre comment Contoso migre les machines virtuelles de l’application SmartHotel vers des machines virtuelles Azure en utilisant le service Site Recovery. | Disponible
[Article 6 : Réhéberger une application sur des machines virtuelles Azure et un groupe de disponibilité SQL Server AlwaysOn](contoso-migration-rehost-vm-sql-ag.md) | Montre comment Contoso migre l’application SmartHotel. Elle utilise Site Recovery pour migrer la machine virtuelle de l’application, et Database Migration Service pour migrer la base de données de l’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. | Disponible
[Article 7 : ré-héberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Montre comment Contoso effectue une migration lift-and-shift de l’application osTicket Linux sur des machines virtuelles Azure à l’aide de Site Recovery. | Disponible
[Article 8 : Réhéberger une application Linux sur des machines virtuelles Azure et Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Montre comment Contoso migre l’application osTicket Linux vers des machines virtuelles Azure à l’aide de Site Recovery, et migre la base de données de l’application vers une instance Azure MySQL Server à l’aide de MySQL Workbench. | Disponible
[Article 9 : Refactoriser une application sur Azure Web Apps et Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Montre comment Contoso migre l’application SmartHotel vers une application web Azure, et migre la base de données d’application vers une instance de serveur SQL Azure | Disponible
[Article 10 : Refactoriser une application Linux sur Azure Web Apps et Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Montre comment Contoso migre l’application Linux osTicket vers Azure Web Apps dans plusieurs sites intégrés à GitHub pour assurer une livraison continue. Elle migre la base de données d’application vers une instance Azure MySQL. | Disponible
[Article 11 : Refactoriser TFS sur VSTS](contoso-migration-tfs-vsts.md) | Montre comment Contoso migre son déploiement TFS (Team Foundation Server) local vers VSTS (Visual Studio Team Services) dans Azure. | Disponible
[Article 12 : Réarchitecturer une application sur des conteneurs Azure et Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Montre comment Contoso migre et réarchitecture son application SmartHotel sur Azure. Elle réarchitecture la couche web d’application en tant que conteneur Windows et la base de données d’application en une base de données Azure SQL Database. | Disponible
[Article 13 : Regénérer une application dans Azure](contoso-migration-rebuild.md) | Montre comment Contoso regénère son application SmartHotel à l’aide d’une série de fonctionnalités et services Azure, notamment App Services, Azure Kubernetes, Azure Functions, Cognitive Services et Cosmos DB. | Disponible


Si vous souhaitez vous servir de l’exemple d’application SmartHotel utilisé dans cet article, vous pouvez le télécharger à partir de [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Architecture locale

Voici un diagramme montrant l’infrastructure locale actuelle de Contoso.

![Architecture de Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso dispose d’un centre de données principal situé dans la ville de New York dans l’Est des États-Unis.
- Ils ont trois branches locales supplémentaires aux États-Unis.
- Le centre de données principal est connecté à Internet via une connexion Ethernet Fiber Metro (500 Mbits/s).
- Chaque branche est connectée localement à Internet via des connexions de qualité professionnelle, avec des tunnels VPN IPSec vers le centre de données principal. Cela permet que le réseau entier soit connecté en permanence et optimise la connexion Internet.
- Le centre de données principal est entièrement virtualisé avec VMware. Il dispose de deux hôtes de virtualisation ESXi 6.5, gérés par un vCenter Server 6.5.
- Contoso utilise Active Directory pour la gestion des identités, et des serveurs DNS sur le réseau interne.
- Les contrôleurs de domaine dans le centre de données s’exécutent sur des machines virtuelles VMware. Les contrôleurs de domaine au niveau des branches locales s’exécutent sur des serveurs physiques.



## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec ses partenaires commerciaux pour comprendre le résultat que l’entreprise souhaite obtenir avec cette migration :

- **Répondre à la croissance** : Contoso étant en croissance, son infrastructure et ses systèmes locaux subissent une pression.
- **Augmenter l’efficacité** : Contoso doit supprimer les procédures inutiles et rationaliser les processus pour ses développeurs et utilisateurs.  L’entreprise a besoin d’une informatique rapide et doit éviter de perdre du temps ou d’argent en répondant plus rapidement aux exigences des clients.
- **Augmenter l’agilité** : l’informatique de Contoso doit être plus réactive aux besoins de l’entreprise. Elle doit être en mesure de réagir plus rapidement que l’évolution du marché pour réussir dans une économie mondiale.  L’informatique ne doit pas devenir une entrave à l’activité.
- **Mise à l’échelle** : à mesure que l’entreprise croît, l’informatique de Contoso doit fournir des systèmes capables de croître au même rythme.

## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a épinglé les objectifs de cette migration. Ces objectifs sont utilisés pour déterminer la meilleure méthode de migration :

- Après la migration, l’application dans Azure devra offrir les mêmes performances qu’aujourd’hui dans l’environnement VMWare local.  Migrer vers le cloud ne signifie nullement que les performances de l’application deviennent moins importantes.
- Contoso ne souhaite pas investir dans cette application.  Elle est vitale pour l’entreprise mais, dans sa forme actuelle, le seul objectif est de la migrer vers le cloud.
- Après la migration de l’application, les tâches d’administration de base de données devront être réduites minimum.
- Contoso ne souhaite pas utiliser Azure SQL Database pour cette application et recherche des alternatives.

## <a name="proposed-architecture"></a>Architecture proposée

Dans ce scénario :

- Contoso souhaite migrer son application de voyage locale à deux niveaux.
- L’application repose sur deux machines virtuelles, WEBVM et SQLVM, et se trouve sur l’hôte VMware ESXi **contosohost1.contoso.com** (version 6.5)
- L’environnement VMware est géré par le serveur vCenter Server 6.5 (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- La base de données de l’application (SmartHotelDB) est migrée vers une instance SQL Managed instance.
- Les machines virtuelles VMware locales sont migrées vers une machine virtuelle Azure.
- Contoso dispose d’un centre de données local (contoso-datacenter), avec un contrôleur de domaine local (**contosodc1**).
- Les machines virtuelles locales dans le centre de données Contoso seront désaffectées une fois la migration terminée.

![Architecture du scénario](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Services Azure

**Service** | **Description** | **Coût**
--- | --- | ---
[Service de gestion de base de données](https://docs.microsoft.com/azure/dms/dms-overview) | Le service DMS permet des migrations fluides depuis plusieurs sources de base de données vers des plateformes de données Azure, avec un temps d’arrêt minimal. | Apprenez-en davantage sur les [régions couvertes](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) par DMS, et consultez les [prix](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure SQL Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance est un service de base de données gérée qui représente une instance SQL Server entièrement gérée dans le cloud Azure. Il partage le même code que la dernière version du moteur de base de données SQL Server et possède les dernières fonctionnalités, améliorations en termes de performances et les correctifs de sécurité. | L’utilisation d’instances gérées Azure SQL Database exécutées dans Azure entraîne des frais en fonction de la capacité. [Plus d’informations](https://azure.microsoft.com/pricing/details/sql-database/managed/) 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Le service orchestre et gère la migration et la récupération d’urgence pour les machines virtuelles Azure, les machines virtuelles locales et les serveurs physiques.  | Lors de la réplication vers Azure, des frais sur le Stockage Azure sont facturés.  Des machines virtuelles Azure sont créées en cas de basculement, et entraînent des frais. [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les frais et la tarification.

 

## <a name="migration-process"></a>Processus de migration

Contoso migre les niveaux web et données de l’application SmartHotel vers Azure.

1. Contoso disposant déjà d’une infrastructure Azure en place, il faut simplement ajouter quelques composants Azure spécifiques pour ce scénario.
2. La couche données est migrée à l’aide d’Azure Database Migration Service (DMS).  DMS se connecte à la machine virtuelle SQL Server locale via une connexion VPN de site à site entre le centre de données de Contoso et Azure, puis migre la base de données.
3. La couche web sera migrée à l’aide d’une migration lift-and-shift avec Azure Site Recovery. Cela nécessite la préparation de l’environnement de VMware local, la configuration et l’activation de la réplication, et la migration des machines virtuelles par basculement de celles-ci vers Azure.

     ![Architecture de la migration](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>Prérequis

Voici ce dont Contoso a besoin pour ce scénario.

**Configuration requise** | **Détails**
--- | ---
**S’inscrire à la préversion** | Vous devez avoir été sélectionné pour tester la préversion publique fermée de SQL Managed Instance. Vous avez besoin d’un abonnement Azure pour vous [inscrire](https://portal.azure.com#create/Microsoft.SQLManagedInstance). La confirmation d’inscription prend quelques jours. Veillez donc à l’effectuer avant de commencer le déploiement de ce scénario.
**Abonnement Azure** | Vous devez avoir déjà créé un abonnement lorsque vous avez effectué l’évaluation dans le premier article de cette série. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur.<br/><br/> S’il vous faut plus d’autorisations granulaires, consultez [cet article](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Récupération de site (local)** | Votre serveur vCenter local doit exécuter la version 5.5, 6.0 ou 6.5<br/><br/> Un hôte ESXi qui exécute la version 5.5, 6.0 ou 6.5<br/><br/> Une ou plusieurs machines virtuelles VMware exécutées sur l’hôte ESXi.<br/><br/> Les machines virtuelles doivent répondre aux [exigences Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configuration [réseau](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) et [stockage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) prise en charge.
**DMS** | Pour le service DMS, il vous faut un [appareil VPN local compatible](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Vous devez être en mesure de configurer l’appareil VPN local. Il doit disposer d’une adresse IPv4 publique. Elle ne peut pas être placée derrière un appareil NAT.<br/><br/> Veillez à disposer de l’accès à votre base de données SQL Server locale.<br/><br/> Le pare-feu Windows doit pouvoir accéder au moteur de la base de données source. [Plus d’informations](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)<br/><br/> S’il y a un pare-feu devant votre machine de base de données, ajoutez des règles pour autoriser l’accès à la base de données, et aux fichiers via le port SMB 445.<br/><br/> Les informations d’identification utilisées pour se connecter à l’instance source SQL Server et à l’instance cible Managed Instance doivent appartenir à des membres du rôle serveur sysadmin.<br/><br/> Il vous faut un partage réseau dans votre base de données locale que le service DMS peut utiliser pour sauvegarder la base de données source.<br/><br/> Vérifiez que le compte de service qui exécute l’instance source de SQL Server dispose de privilèges d’écriture pour le partage réseau.<br/><br/> Notez l’utilisateur Windows (et son mot de passe) qui dispose d’un contrôle total sur le partage réseau. Azure Database Migration Service emprunte ces informations d’identifications pour charger les fichiers de sauvegarde sur le conteneur de stockage Azure.<br/><br/> Le processus d’installation SQL Server Express définit le protocole TCP/IP sur **Désactivé** par défaut. Veillez à l’activer.


## <a name="scenario-steps"></a>Étapes du scénario

Voici comment Contoso va configurer le déploiement :

> [!div class="checklist"]
> * **Étape 1 : configurer une instance SQL Azure Managed Instance** : il s’agit de créer au préalable une instance gérée vers laquelle la base de données SQL Server locale migrera.
> * **Étape 2 : préparer le service DMS** : il s’agit d’inscrire le fournisseur de migration de base de données, de créer une instance, puis de créer un projet de service DMS. Il s’agit également de configurer l’URI SAP pour DMS. Un URI de signature d’accès partagé (SAP) fournit un accès délégué aux ressources présentes dans votre compte de stockage qui vous permet d’attribuer des autorisations limitées à des objets de stockage. Il s’agit de configurer un URI SAP pour que le service DMS puisse accéder au conteneur du compte de stockage sur lequel le service charge les fichiers de sauvegarde SQL Server.
> * **Étape 3 : préparer Azure pour Site Recovery** : pour Site Recovery, il faut créer un compte de stockage Azure pour accueillir les données répliquées, et créer un coffre Recovery Services.
> * **Étape 4 : préparer une machine virtuelle VMware locale pour Site Recovery** : Contoso doit préparer des comptes pour la découverte de machine virtuelle et l’installation d’agents, et préparer la connexion aux machines virtuelles Azure après basculement.
> * **Étape 5 : répliquer les machines virtuelles** : pour la réplication, il s’agit de configurer les environnements source et cible de Site Recovery ainsi qu’une stratégie de réplication, et de commencer la réplication des machines virtuelles vers le stockage Azure.
> * **Étape 6 : migrer la base de données avec DMS** : il est désormais possible de migrer la base de données.
> * **Étape 7 : migrer des machines virtuelles avec Site Recovery** : il convient d’exécuter un test de basculement pour s’assurer que tout fonctionne, puis d’opérer un basculement complet pour migrer les machines virtuelles vers Azure.


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>Étape 1 : préparer une instance Azure SQL Managed Instance

Pour configurer une instance Azure SQL Managed Instance, Contoso a besoin d’un sous-réseau :

- Le sous-réseau doit être dédié. Il doit être vide et ne contenir aucun autre service cloud, et ne doit pas être un sous-réseau de passerelle.
- Une fois l’instance gérée créée, vous ne devez pas y ajouter de ressources.
- Aucun groupe de sécurité ne doit être associé au sous-réseau.
- Le sous-réseau doit avoir une table de routage utilisateur (UDR) avec un itinéraire Internet de tronçon suivant 0.0.0.0/0 comme seul itinéraire affecté. 
- DNS personnalisé éventuel : si un DNS personnalisé est spécifié sur le réseau virtuel, vous devez ajouter l’adresse IP des programmes de résolution récursifs d’Azure (168.63.129.16) à la liste. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)
- Le sous-réseau ne doit pas avoir de point de terminaison de service (stockage ou SQL) associé. Les points de terminaison doivent être désactivés sur le réseau virtuel.
- Le sous-réseau doit avoir un minimum de 16 adresses IP. [En savoir plus](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) sur le dimensionnement d’un sous-réseau d’instance gérée.
- Dans un environnement hybride, des paramètres DNS personnalisés sont nécessaires. Contoso doit configurer les paramètres DNS pour utiliser un ou plusieurs de ses serveurs Azure DNS. [Apprenez-en davantage](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) sur la personnalisation de DNS.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Configurer un réseau virtuel pour l’instance gérée

Contoso configure le réseau virtuel comme suit : 

1. Contoso crée un réseau virtuel (VNET-SQLMI-EU2) dans la région USA Est 2 principale, dans le groupe de ressources ContosoNetworkingRG.
2. Contoso assigne un espace d’adressage de 10.235.0.0/24 pour s’assurer que la plage ne chevauche pas d’autre réseau de l’entreprise.
2. Ils ajoutent deux sous-réseaux au réseau :
    - SQLMI-DS-EUS2 (10.235.0.0.25)
    - SQLMI-SAW-EUS2 (10.235.0.128/29). Ce sous-réseau sera utilisé pour joindre l’annuaire à l’instance gérée (SQLMI).

    ![Réseau de l’instance gérée](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Une fois le réseau virtuel et les sous-réseaux déployés, Contoso appaire les réseaux comme suit :

    - Appaire VNET-SQLMI-EUS2 avec VNET-HUB-EUS2 (réseau virtuel hub pour la région USA Est 2).
    - Appaire VNET-SQLMI-EUS2 avec VNET-PROD-EUS2 (réseau de production).

    ![Appairage de réseau](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso définit des paramètres DNS personnalisés. DNS pointe d’abord vers les contrôleurs de domaine Azure. Azure DNS sera secondaire. Les contrôleurs de domaine Azure de Contoso se situent comme suit :

    - Situé dans le sous-réseau PROD-CC-EUS2, dans le réseau de production USA Est 2 (VNET-PROD-EUS2).
    - Adresse de CONTOSODC3 : 10.245.42.4
    - Adresse de CONTOSODC4 : 10.245.42.5
    - Programme de résolution d’Azure DNS : 168.63.129.16

     ![DNS du réseau](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**Besoin de plus d’aide ?**

- [Accédez à une vue d’ensemble](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) des instances Azure SQL Managed Instance.
- [Apprenez-en davantage](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances) sur la création d’un réseau virtuel pour une instance SQL Database Managed Instance.
- [Apprenez-en davantage sur la](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering) configuration de l’appairage.
- [Apprenez-en davantage sur la](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns) mise à jour des paramètres DNS d’Azure AD.



### <a name="set-up-routing"></a>Configuration du routage

L’instance gérée étant placée dans un réseau virtuel privé, Contoso a besoin d’une table de routage pour la faire communiquer avec le service de gestion Azure. Si elle ne peut pas communiquer avec le service qui le gère, elle devient inaccessible.

- La table de routage contient un ensemble de règles (itinéraires) qui spécifie le mode de routage des paquets envoyés à partir de l’instance gérée dans le réseau virtuel.
- La table de routage est associée à des sous-réseaux dans lesquels les instances gérées sont déployées. Chaque paquet quittant un sous-réseau est géré en fonction de la table de routage associée.
- Un sous-réseau ne peut être associé qu’à une seule table de routage.
- La création de tables de routage dans Microsoft Azure n’occasionne aucun frais.

1. Contoso crée une table de routage définie par l’utilisateur. La table de routage est créée dans le groupe de ressources ContosoNetworkingRG.

    ![Table de routage](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Pour respecter les exigences applicables aux instances gérées, une fois la table de routage (MIRouteTable) déployée, Contoso ajoute un itinéraire avec un préfixe d’adresse 0.0.0.0/0, et le **type de tronçon suivant** défini sur **Internet**.

    ![Préfixe de table de routage](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso associe la table de routage au sous-réseau SQLMI-DB-EUS2 (dans le réseau VNET-SQLMI-EUS2). 

    ![Sous-réseau de table de routage](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**Besoin de plus d’aide ?**

[Apprenez-en davantage sur la](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route) configuration d’itinéraires pour une instance gérée.

### <a name="create-a-managed-instance"></a>Créer une instance gérée

À présent, Contoso peut approvisionner une instance SQL Database Managed Instance.

1. Étant donné que l’instance gérée sert une application métier, Contoso la déploie dans sa région USA Est 2 principale, dans le groupe de ressources ContosoRG. 
2. Ils sélectionnent un niveau tarifaire et dimensionnent le calcul et le stockage pour l’instance. [Apprenez-en davantage sur la](https://azure.microsoft.com/pricing/details/sql-database/managed/) tarification.

    ![Instance gérée](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Après déploiement de l’instance gérée, deux nouvelles ressources apparaissent dans le groupe de ressources ContosoRG :

    - un cluster virtuel si vous avez plusieurs instances gérées,
    - l’instance SQL Server Managed Instance. 

    ![Instance gérée](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**Besoin de plus d’aide ?**

[Apprenez-en davantage sur l’](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal) approvisionnement d’une instance gérée.

## <a name="step-2-prepare-dms"></a>Étape 2 : préparer le service DMS

Pour préparer le service DMS, Contoso doit faire deux choses :

- Inscrire le fournisseur de service DMS dans Azure
- Fournir au service DMS l’accès au stockage Azure pour charger les fichiers de sauvegarde utilisés pour migrer une base de données. Pour ce faire, ils créent un conteneur de stockage d’objets blob et génèrent un URI SAP pour celui-ci. 
- Créer un projet de service DMS.


Procédez comme suit :

1. Contoso inscrit le fournisseur de migration de base de données sous son abonnement.
    ![Inscription de DMS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Ils créent un conteneur de stockage d’objets blob et génèrent un URI SAP pour que le service DMS puisse y accéder.

    ![URI SAP](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Enfin, ils créent une instance de service DMS. 

    ![Instance de service DMS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso place l’instance de service DMS dans le sous-réseau PROD-DC-EUS2 du réseau virtuel VNET-PROD-DC-EUS2.
    - Ils la placent à cet endroit parce qu’elle doit se trouver dans un réseau virtuel capable d’accéder à la machine virtuelle SQL Server locale via une passerelle VPN.
    - Le réseau virtuel VNET-PROD-EUS2 est appairé au réseau virtuel VNET-HUB-EUS2, et autorisé à utiliser des passerelles distantes.  Cela garantit que le service DMS peut communiquer correctement.

        ![Réseau de service DMS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**Besoin de plus d’aide ?**
- [Apprenez-en davantage sur la](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal) configuration du service DMS.
- [Apprenez-en davantage sur la ](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) création et l’utilisation de SAP.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Étape 3 : préparer Azure pour le service Site Recovery

Contoso a besoin de plusieurs éléments Azure afin de configurer Site Recovery pour la migration de sa machine virtuelle de couche web (WEBMV)

- Un réseau virtuel dans lequel se trouvent les ressources basculées.
- Un compte de stockage Azure pour accueillir les données répliquées. 
- Un coffre Recovery Services dans Azure.

Ils configurent Site Recovery comme suit :

1. Étant donné que la machine virtuelle est un serveur web frontal pour l’application SmartHotel, ils vont basculer la machine virtuelle vers leur réseau de production existant (VNET-PROD-EUS2) et le sous-réseau (PROD-FE-EUS2) dans la région USA Est 2 principale. Ils configurent ce réseau après avoir [déployé l’infrastructure Azure](contoso-migration-infrastructure.md)
2. Ils créent un compte de stockage Azure (contosovmsacc20180528). Ils utilisent un compte à usage général, avec un stockage standard et une réplication de stockage localement redondant.

    ![Stockage Site Recovery](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Avec le réseau et le compte de stockage en place, Contoso peut maintenant créer un coffre (ContosoMigrationVault) et le placer dans le groupe de ressources ContosoFailoverRG, dans la région USA Est 2 principale.

    ![Coffre Recovery Services](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**Besoin de plus d’aide ?**

[En savoir plus sur la](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) configuration d’Azure pour Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Étape 4 : préparer VMware en local pour Site Recovery

Afin de préparer VMware pour Site Recovery, voici ce que Contoso doit faire :

- Préparer un compte sur le serveur vCenter ou sur l’hôte vSphere ESXi pour automatiser la détection de machines virtuelles.
- Préparer un compte permettant l’installation automatique du service Mobilité sur les machines virtuelles VMware que vous souhaitez répliquer.
- Préparer les machines virtuelles locales afin qu’elles puissent se connecter aux machines virtuelles Azure une fois celles-ci créées après le basculement.


### <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique

Site Recovery doit pouvoir accéder aux serveurs VMware pour :

- Détectez automatiquement les machines virtuelles. Au moins un compte en lecture seule est nécessaire.
- Orchestrer la réplication, le basculement et la restauration automatique. Il vous faut un compte qui peut exécuter des opérations telles que la création et la suppression de disques, et l’allumage de machines virtuelles.

Contoso configure le compte comme suit :

1. Crée un rôle au niveau du vCenter.
2. Attribue les autorisations requises à ce rôle.

**Besoin de plus d’aide ?**

[En savoir plus sur la](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) création et l’attribution d’un rôle pour la détection automatique.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Préparer un compte pour l’installation du service Mobilité

Le service Mobilité doit être installé sur la machine virtuelle que vous souhaitez répliquer.

- Site Recovery peut faire une installation automatique par push de ce composant, lorsque vous activez la réplication pour la machine virtuelle.
- Pour une installation automatique par push, vous devez préparer un compte qui sera utilisé par Site Recovery pour accéder à la machine virtuelle.
- Vous spécifiez ce compte quand vous configurez la réplication dans la console d’Azure.
- Il vous faut un domaine ou un compte local avec les autorisations nécessaires pour l’installation sur la machine virtuelle.

**Besoin de plus d’aide ?**

[En savoir plus sur la](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) création d’un compte pour une installation push du service Mobilité.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer la connexion aux machines virtuelles Azure après le basculement

Après le basculement vers Azure, Contoso souhaite pouvoir se connecter aux machines virtuelles répliquées dans Azure. Pour ce faire, il y a des opérations à accomplir sur la machine virtuelle locale avant d’exécuter la migration : 

1. Pour l’accès via Internet, ils activent le protocole RDP sur la machine virtuelle locale avant le basculement, en veillant à ce que les règles TCP et UDP soient ajoutées pour le profil **Public**, et à ce que le protocole RDP soit autorisé dans **Pare-feu Windows** > **Applications autorisées** pour tous les profils.
2. Pour l’accès sur leur réseau VPN de site à site, ils activent le protocole RDP sur l’ordinateur local et autorisent le protocole RDP dans **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux **de domaine et privés**.
3. Ils définissent la stratégie SAN du système d’exploitation sur la machine virtuelle locale sur **OnlineAll**.

De plus, quand ils opèrent un basculement, ils doivent vérifier les points suivants :

- Aucune mise à jour de Windows ne peut être en attente sur la machine virtuelle lors du déclenchement d’un basculement. Autrement, vous ne pourrez pas vous connecter à la machine virtuelle avant la fin de la mise à jour.
- Après basculement, il convient de consulter les **diagnostics de démarrage** pour afficher une capture d’écran de la machine virtuelle. Si cela ne fonctionne pas, il faut vérifier que la machine virtuelle est en cours d’exécution et consulter ces [conseils de dépannage](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Étape 5 : répliquer les machines virtuelles locales sur Azure avec Site Recovery

Avant d’opérer une migration vers Azure, Contoso doit configurer et activer la réplication pour ses machines virtuelles locales.

### <a name="set-a-replication-goal"></a>Définir un objectif de réplication

1. Dans le coffre, sous le nom du coffre (ContosoVMVault), il faut définir un objectif de réplication (**Prise en main** > **Site Recovery** > **Préparer l’infrastructure**).
2. Il convient de spécifier que les ordinateurs sont situés localement, qu’il s’agit de machines virtuelles VMware, et qu’il faut les répliquer sur Azure.

    ![Objectif de réplication](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmer la planification d’un déploiement

Pour continuer, il faut confirmer que la planification du déploiement est terminée en sélectionnant **Yes, I have done it** (Oui, c’est fait). Dans ce déploiement, Contoso ne migre qu’une seule machine virtuelle et n’a pas besoin de planification du déploiement.

### <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Ils doivent maintenant configurer leur environnement source. Pour ce faire, ils téléchargent un modèle OVF et l’utilisent pour déployer le serveur de configuration et ses composants associés comme étant une machine virtuelle VMware locale hautement disponible. Les composants sur le serveur sont les suivants :

- Le serveur de configuration qui coordonne les communications entre les machines virtuelles en local et Azure, et gère la réplication des données.
- Le serveur de traitement qui fait office de passerelle de réplication. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure.
- De plus, le serveur de processus installe le service Mobilité sur les machines virtuelles que vous voulez répliquer et effectue la détection automatique sur les machines virtuelles VMware locales.
- Une fois la machine virtuelle du serveur de configuration créée et démarrée, Contoso peut l’inscrire dans le coffre.


Contoso procède comme suit :

1. Ils téléchargent le modèle OVF à partir du portail Azure (**Préparer l’infrastructure** > **Source** > **Serveur de configuration**).
    
    ![Télécharger le modèle OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Le modèle est importé dans VMware pour créer et déployer la machine virtuelle.

    ![Modèle OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Lors de la première activation de la machine virtuelle, celle-ci démarre dans un environnement d’installation Windows Server 2016. Contoso accepte le contrat de licence, puis entre un mot de passe d’administrateur.
4. Une fois l’installation terminée, ils se connectent à la machine virtuelle en tant qu’administrateur. À la première connexion, l’outil de configuration d’Azure Site Recovery s’exécute par défaut.
5. Dans l’outil, ils spécifient un nom à utiliser lors de l’inscription du serveur de configuration dans le coffre.
6. L’outil vérifie que la machine virtuelle peut se connecter à Azure. Une fois la connexion établie, ils sélectionnent **Connecter** pour se connecter à l’abonnement Azure. Les informations d’identification doivent avoir accès au coffre dans lequel le serveur de configuration sera inscrit. 

    [Inscrire un serveur de configuration](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. L’outil effectue des tâches de configuration, puis redémarre. Il y a une nouvelle connexion à la machine et l’Assistant de gestion du serveur de configuration démarre automatiquement.
8. Dans l’Assistant, la carte réseau qui doit recevoir le trafic de réplication est sélectionnée. Une fois configuré, ce paramètre ne peut pas être modifié.
9. L’abonnement, le groupe de ressources et le coffre dans lequel inscrire le serveur de configuration sont sélectionnés.
        ![coffre](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. Ensuite, ils téléchargent et installent MySQL Server et VMWare PowerCLI. Puis, ils valident les paramètres du serveur.
11. Après la validation, ils spécifient le nom de domaine complet (FQDN) ou l’adresse IP du serveur vCenter ou de l’hôte vSphere. Ils quittent le port par défaut et spécifient un nom convivial pour le serveur vCenter dans Azure.
12. Ils doivent spécifier le compte qu’ils ont créé précédemment afin que Site Recovery puisse détecter automatiquement les machines virtuelles VMware disponibles pour la réplication. 
13. Ils spécifient les informations d’identification pour installer automatiquement le service Mobilité une fois la réplication activée. Pour des machines virtuelles Windows, le compte doit disposer des privilèges d’administrateur local sur celles-ci. 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Une fois l’inscription terminée, dans le portail Azure, Contoso vérifie que le serveur de configuration et le serveur VMware sont répertoriés sur la page **Source** dans le coffre. La détection peut prendre 15 minutes ou plus. 
8. Site Recovery se connecte ensuite aux serveurs VMware en utilisant les paramètres spécifiés et détecte les machines virtuelles.

### <a name="set-up-the-target"></a>Configurer la cible

Contoso doit à présent configurer l’environnement de réplication cible.

1. Dans **Préparer l’infrastructure** > **Cible**, ils sélectionnent les paramètres de la cible.
2. Site Recovery vérifie qu’il existe un compte de stockage et un réseau Azure dans la cible spécifiée.

### <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

Une fois la source et la cible configurées, Contoso est prêt à créer une stratégie de réplication et à l’associer au serveur de configuration.

1. Dans **Préparer l’infrastructure** > **Paramètres de réplication** > **Stratégie de réplication** >  **Créer et associer**, ils créent une stratégie **ContosoMigrationPolicy**.
2. Elle utilise les paramètres par défaut :
    - **Seuil d'objectif de point de récupération** : la valeur par défaut est 60 minutes. Cette valeur définit la fréquence à laquelle les points de récupération sont créés. Une alerte est générée lorsque la réplication continue dépasse cette limite.
    - **Rétention des points de récupération**. La valeur par défaut est 24 heures. Cette valeur spécifie la durée de la fenêtre de rétention pour chaque point de récupération. Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre.
    - **Fréquence des captures instantanées de cohérence d’application**. La valeur par défaut est une heure. Cette valeur spécifie la fréquence à laquelle les captures instantanées de cohérence d’application sont créées.
 
        ![Créer une stratégie de réplication](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. La stratégie est automatiquement associée au serveur de configuration. 

    ![Associer la stratégie de réplication](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**Besoin de plus d’aide ?**

- Une procédure pas à pas complète de toutes ces étapes est décrite dans [Configurer la récupération d’urgence pour des machines virtuelles VMware locales](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Des instructions détaillées sont disponibles pour vous aider à [configurer l’environnement source](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), à [déployer le serveur de configuration](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) et à [configurer les paramètres de réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Activer la réplication

Maintenant, Contoso peut commence à répliquer la machine virtuelle WebVM.

1. Dans **Répliquer l’application** > **Source** > **+Répliquer**, ils sélectionnent les paramètres de la source.
2. Ils indiquent qu’ils souhaitent activer les machines virtuelles, puis sélectionnent le serveur vCenter et le serveur de configuration.

 ![Activer la réplication](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. À présent, ils spécifient les paramètres de la cible, dont le groupe de ressources et le réseau dans lequel la machine virtuelle Azure se trouvera après le basculement, ainsi que le compte de stockage dans lequel les données répliquées seront stockées.

     ![Activer la réplication](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso sélectionne la machine virtuelle WebVM pour la réplication. Site Recovery installe le service Mobilité sur chaque machine virtuelle quand vous activez la réplication. 

    ![Activer la réplication](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso vérifie que la stratégie de réplication appropriés est sélectionnée, puis active la réplication pour la machine virtuelle WEBVM. La progression de la réplication est suivie dans **Travaux**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.
6. Dans le portail Azure, dans **Bases**, Contoso peut afficher la structure des machines virtuelles répliquées sur Azure.

    ![Vue de l’infrastructure](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**Besoin de plus d’aide ?**

La procédure pas à pas complète de toutes ces étapes est décrite dans [Activer la réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-with-dms"></a>Étape 6 : migrer la base de données avec le service DMS

Contoso doit créer un projet de service DMS et migrer la base de données.

### <a name="create-a-dms-project"></a>Créer un projet de service DMS
1. Ils créent un projet de service DMS. Ils spécifient le type de serveur source en tant que SQL Server, et la cible en tant qu’instance Azure SQL Database Managed Instance.

     ![Projet DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Une fois le projet créé, l’assistant Migration s’affiche.

### <a name="migrate-the-database"></a>Migrer la base de données 

1. Dans l’Assistant Migration, Contoso Spécifie la machine virtuelle source sur laquelle se trouve la base de données locale, ainsi que les informations d’identification pour y accéder.

    ![Source du service DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Ils sélectionnent la base de données à migrer (SmartHotel.Registration).

    ![Base de données source du service DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. En tant que cible, ils spécifient le nom de l’instance gérée dans Azure et les informations d’identification d’accès.

    ![Cible du service DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Ensuite, dans **+Nouvelle activité** > **Exécuter la migration**, ils spécifient les paramètres d’exécution de la migration :
    - Informations d’identification de la source et de la cible.
    - Base de données à migrer.
    - Partage réseau qu’ils sont créés sur la machine virtuelle locale. DMS emmène les sauvegardes source dans ce partage.
        - Le compte de service qui exécute l’instance source de SQL Server doit avoir des privilèges d’écriture pour ce partage réseau.
        - Spécifiez le chemin d’accès du nom de domaine complet (FQDN) pour le partage.
    - URI SAP qui donne au service DMS l’accès au conteneur du compte de stockage dans lequel le service charge les fichiers de sauvegarde pour la migration.

        ![Paramètres du service DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Ils enregistrent la migration et l’exécutent.
6. Dans **Vue d’ensemble**, ils surveillent l’état de la migration.

    ![Surveillance du service DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Une fois la migration terminée, ils vérifient que les bases de données cible existent sur l’instance gérée.

    ![Surveillance du service DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>Étape 7 : migrer la machine virtuelle avec Site Recovery

Contoso exécute un test rapide de basculement, puis migre la machine virtuelle.

### <a name="run-a-test-failover"></a>Exécuter un test de basculement

Avant de migrer la machine virtuelle WEBVM, un test de basculement permet de s’assurer que tout fonctionne comme prévu. 

1. Ils exécutent un test de basculement jusqu’au dernier point dans le temps disponible (**Dernier point traité**).
2. Elle sélectionne **Shut down machine before beginning failover** (Arrêter la machine avant de commencer le basculement), de façon à ce que Site Recovery tente d’arrêter la machine virtuelle source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. 
3. Le test de basculement est exécuté : 

    - Une vérification des prérequis est effectuée pour garantir que toutes les conditions nécessaires pour la migration sont en place.
    - Le basculement traite les données pour permettre la création d’une machine virtuelle Azure. Si vous avez sélectionné le dernier point de récupération, un point de récupération est créé à partir des données.
    - Une machine virtuelle Azure est créée en utilisant les données traitées à l’étape précédente.
3. Une fois le basculement terminé, la machine virtuelle Azure répliquée apparaît dans le portail Azure. Ils vérifient que tout fonctionne correctement. La taille de la machine virtuelle est appropriée, elle est connectée au bon réseau et elle est en cours d’exécution. 
4. Après avoir vérifié le test du basculement, Contoso nettoie le basculement, puis consigne et enregistre les observations éventuelles. 

### <a name="migrate-the-vm"></a>Migrer la machine virtuelle

1. Après avoir vérifié que le test de basculement a fonctionné comme prévu, Contoso crée un plan de récupération pour la migration. Ils ajoutent une machine WEBVM au plan.

     ![Plan de récupération](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Ensuite, ils exécutent un basculement conformément au plan. Contoso sélectionne le dernier point de récupération et spécifie que Site Recovery doit essayer d’arrêter la machine virtuelle locale avant de déclencher le basculement.

    ![Basculement](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Après le basculement, Contoso vérifie que la machine virtuelle Azure apparaît comme prévu dans le portail Azure.

   ![Plan de récupération](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Après avoir vérifié la machine virtuelle dans Azure, elle achève le processus de migration, arrête la réplication de la machine virtuelle, et arrête la facturation de Site Recovery pour la machine virtuelle.

    ![Basculement](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Mettre à jour la chaîne de connexion

La dernière étape du processus de migration pour Contoso consiste à mettre à jour la chaîne de connexion de l’application pour qu’elle pointe vers la base de données migrée s’exécutant sur son instance SQL Managed Instance.

1. Dans le portail Azure, ils recherchent la chaîne de connexion en cliquant sur **Paramètres** > **Chaînes de connexion**.

    ![Basculement](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Ils mettent à jour la chaîne avec le nom d’utilisateur et le mot de passe de l’Instance SQL Managed Instance.
3. Une fois la chaîne configurée, ils remplacent la chaîne de connexion actuelle dans le fichier web.config de leur application.
4. Après avoir mis à jour et enregistré le fichier, ils redémarrent IIS sur la machine WEBVM. Ils entrent pour cela la commande **IISRESET /RESTART** à partir d’une invite de commandes.
5. Après redémarrage d’IIS, l’application utilise la base de données en cours d’exécution sur l’instance SQL Managed Instance.
6. À ce stade, Contoso peut arrêter la machine virtuelle SQLVM locale, et la migration est terminée.

**Besoin de plus d’aide ?**

- [En savoir plus sur](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) l’exécution d’un test de basculement. 
- [Découvrez](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) comment créer un plan de récupération.
- [Découvrez](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) comment basculer vers Azure.

## <a name="clean-up-after-migration"></a>Nettoyer après la migration

Une fois la migration terminée, l’application SmartHotel s’exécute sur une machine virtuelle Azure, et la base de données SmartHotel est disponible sur l’Instance Azure SQL Managed Instance.  

À présent, Contoso doit effectuer un nettoyage comme suit :  

- Supprimer la machine virtuelle WEBVM de l’inventaire vCenter.
- Supprimer la machine virtuelle SQLVM de l’inventaire vCenter.
- Supprimer les machines virtuelles WEBVM et SQLVM des travaux de sauvegarde locaux.
- Mettre à jour la documentation interne afin qu’elle indique le nouvel emplacement, l’adresse IP de la machine virtuelle WEBVM.
- Supprimer la machine virtuelle SQLVM de la documentation. Ils peuvent aussi la marquer afin qu’elle apparaisse comme supprimée et ne figure plus dans l’inventaire des machines virtuelles.
- Passer en revue toutes les ressources qui interagissent avec les machines virtuelles désaffectées, et mettre à jour les paramètres ou la documentation appropriés afin de refléter la nouvelle configuration.

## <a name="review-the-deployment"></a>Examiner le déploiement

Avec les ressources migrées dans Azure, Contoso doit rendre sa nouvelle infrastructure entièrement opérationnelle et la sécuriser.

### <a name="security"></a>Sécurité

L’équipe de sécurité de Contoso examine les machines virtuelles Azure et l’instance SQL Managed Instance pour détecter d’éventuels problèmes de sécurité liés à son implémentation.

- Ils examinent les groupes de sécurité réseau (NSG) pour la machine virtuelle, afin de contrôler l’accès. Les groupes de sécurité réseau sont utilisés pour s’assurer que seul le trafic autorisé vers l’application puisse passer.
- Ils prennent également en considération la sécurisation des données sur le disque à l’aide d’Azure Disk Encryption et d’Azure KeyVault.
- Ceux-ci permettent la détection des menaces sur l’instance SQL Managed Instance (SQLMI). Ils envoient des alertes à leur équipe de sécurité/système de support pour ouvrir un ticket en cas de détection d’une menace. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection)

     ![Sécurité de l’instance gérée](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[En savoir plus](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sur les pratiques de sécurité pour les machines virtuelles.

### <a name="backups"></a>Sauvegardes
Contoso va sauvegarder les données sur la machine virtuelle WEBVM à l’aide du service Sauvegarde Azure. [Plus d’informations](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="licensing-and-cost-optimization"></a>Gestion des licences et optimisation des coûts

1. Contoso a un contrat de licence pour WEBVM et tirera parti d’Azure Hybrid Benefit.  Ils vont convertir la machine virtuelle Azure existante pour tirer parti de cette tarification.
2. Contoso va activer Azure Cost Management sous licence de Cloudyn, une filiale de Microsoft. Il s’agit d’une solution de gestion des coûts multicloud qui aide à utiliser et à gérer d’Azure ainsi que d’autres ressources cloud.  [En savoir plus](https://docs.microsoft.com/azure/cost-management/overview) sur Azure Cost Management. 


## <a name="conclusion"></a>Conclusion

Cet article décrit comment Contoso a réhébergé l’application SmartHotel dans Azure en migrant la machine virtuelle frontale de celle-ci vers Azure à l’aide du service Site Recovery. Ils ont migré leur base de données locale vers une instance Azure SQL Managed Instance avec le service DMS.

## <a name="next-steps"></a>Étapes suivantes

Dans [l’article suivant](contoso-migration-rehost-vm.md) de la série, nous allons montrer comment Contoso réhéberge l’application SmartHotel sur des machines virtuelles Azure en utilisant le service Azure Site Recovery.

