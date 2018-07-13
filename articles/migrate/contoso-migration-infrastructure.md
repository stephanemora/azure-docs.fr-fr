---
title: Contoso - Configurer une infrastructure de migration | Microsoft Docs
description: Découvrez comment Contoso configure une infrastructure Azure pour la migration vers Azure.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: bf1406c8e361e0a1433b0e26c477c3c34e987fcf
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38562756"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso - Déployer une infrastructure de migration

Cet article explique comment Contoso configure une infrastructure locale et Azure, en préparation de la migration vers Azure et pour gérer l’entreprise dans un environnement hybride.

- C’est un exemple d’architecture spécifique à Contoso.
- Votre stratégie de migration détermine si vous avez besoin de tous les éléments décrits dans l’article. Par exemple, si vous créez uniquement des applications cloud en mode natif dans Azure, vous aurez peut-être besoin d’une structure de mise en réseau moins complexe.

Ce document est le deuxième d’une série d’articles décrivant comment l’entreprise fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations générales, et un ensemble de scénarios de déploiement qui illustre comment configurer une infrastructure de la migration, évaluer l’adéquation des ressources locales pour la migration, et exécuter différents types de migration. Les scénarios croissent en complexité, et nous ajouterons des articles au fil du temps.

**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Fournit une vue d’ensemble de la stratégie de migration de Contoso, de la série d’articles et des exemples d’application que nous utilisons. | Disponible
Article 2 : déployer une infrastructure Azure (cet article) | Décrit comment Contoso prépare ses infrastructures locales et Azure pour la migration. La même infrastructure est utilisée pour tous les scénarios de migration de Contoso. | Disponible
[Article 3 : Évaluer les ressources locales](contoso-migration-assessment.md) | Montre comment Contoso évalue son application à deux niveaux locale SmartHotel s’exécutant sur VMware. Ils évaluent les machines virtuelles de l’application avec le service [Azure Migrate](migrate-overview.md) et la base de données SQL Server de l’application avec [l’Assistant Migration de données Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponible
[Article 4 : ré-héberger sur des machines virtuelles Azure et une instance SQL Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Montre comment Contoso migre l’application SmartHotel vers Azure. Ils migrent la machine virtuelle frontale de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), et la base de données de l’application à l’aide du service [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) pour opérer la migration vers une instance SQL Managed Instance. | Disponible
[Article 5 : Réhéberger sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Montre comment Contoso migre ses machines virtuelles de l’application SmartHotel en utilisant uniquement Site Recovery.
[Article 6 : ré-héberger sur des machines virtuelles Azure et des groupes de disponibilité SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Montre comment Contoso migre l’application SmartHotel. Ils utilisent Site Recovery pour migrer les machines virtuelles de l’application, et Database Migration Service pour migrer la base de données de l’application vers un groupe de disponibilité SQL Server. | Disponible
[Article 7 : Réhéberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Montre comment Contoso migre l’application osTicket Linux sur des machines virtuelles Azure. | Disponible
[Article 8 : ré-héberger une application Linux sur des machines virtuelles Azure et Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Montre comment Contoso migre l’application osTicket Linux à l’aide de Site Recovery et MySQL Workbench pour migrer (sauvegarder et restaurer) vers une instance Azure MySQL Server. | Disponible

Dans cet article, Contoso configure tous les éléments d’infrastructure nécessaires aux scénarios de migration. 


## <a name="overview"></a>Vue d'ensemble

Avant de pouvoir les migrer vers Azure, Contoso doit impérativement préparer leur infrastructure.  Généralement, cinq larges zones doivent être prises en compte :

**Étape 1 : abonnements Azure** : comment acheter Azure et interagir avec la plateforme et les services Azure ?  
**Étape 2 : identité hybride** : comment gérer et contrôler l’accès aux ressources locales et Azure après la migration ? Comment étendre ou transférer la gestion des identités vers le cloud ?  
**Étape 3 : récupération d’urgence et résilience** : comment garantir la résilience des applications et de l’infrastructure en cas de pannes et d’incidents ?  
**Étape 4 : mise en réseau** : comment concevoir l’infrastructure réseau et établir une connectivité entre le centre de données sur site et Azure ?  
**Étape 5 : sécurité**: comment sécuriser un déploiement Azure ou hybride ?  
**Étape 6 : gouvernance**: comment conserver l’alignement du déploiement avec les exigences en termes de gouvernance et de sécurité ?

## <a name="before-you-start"></a>Avant de commencer

Avant d’examiner l’infrastructure, nous vous conseillons de lire quelques informations générales sur les fonctionnalités Azure abordées dans cet article :

- Plusieurs options sont disponibles pour l’achat d’un accès Azure, y compris le paiement à l’utilisation, les Contrats Entreprise (EA), les licences Open de revendeurs Microsoft ou de Partenaires Microsoft appelés fournisseurs de solutions Cloud (CSP). En savoir plus sur les [options d’achat](https://azure.microsoft.com/pricing/purchase-options/) et la façon dont les [abonnements Azure sont organisés](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Obtenez une vue d’ensemble de la [gestion de l'identité et de l'accès](https://www.microsoft.com/en-us/trustcenter/security/identity) Azure. En particulier, découvrez [Azure AD et l’extension AD locale vers le cloud](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Il existe un livre électronique téléchargeable très utile sur la [gestion de l’identité et de l’accès (IAM) dans un environnement hybride](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- Azure fournit une infrastructure réseau fiable avec des options de connectivité hybride. Obtenez une vue d’ensemble de la [mise en réseau et du contrôle d’accès réseau](https://docs.microsoft.com/azure/security/security-network-overview).
- Obtenez une présentation de la [sécurité Azure](https://docs.microsoft.com/azure/security/azure-security) et plus d’informations sur la création d’un plan pour la [gouvernance](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>Architecture locale

Voici un diagramme montrant l’infrastructure locale actuelle de Contoso.

 ![Architecture de Contoso](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso dispose d’un centre de données principal situé dans la ville de New York dans l’Est des États-Unis.
- Ils ont trois branches locales supplémentaires aux États-Unis.
- Le centre de données principal est connecté à Internet via une connexion Ethernet Fiber Metro (500 Mbits/s).
- Chaque branche est connectée localement à Internet via des connexions de qualité professionnelle, avec des tunnels VPN IPSec vers le centre de données principal. Cela permet que le réseau entier soit connecté en permanence et optimise la connexion Internet.
- Le centre de données principal est entièrement virtualisé avec VMware. Il dispose de deux hôtes de virtualisation ESXi 6.5, gérés par un vCenter Server 6.5.
- Contoso utilise Active Directory pour la gestion des identités, et des serveurs DNS sur le réseau interne.
- Les contrôleurs de domaine dans le centre de données s’exécutent sur des machines virtuelles VMware. Les contrôleurs de domaine au niveau des branches locales s’exécutent sur des serveurs physiques.


## <a name="step-1-buy-and-subscribe-to-azure"></a>Étape 1 : Acheter et s’abonner à Azure

Contoso doit savoir comment acheter Azure, créer des abonnements et obtenir une licence pour les services et les ressources.

### <a name="buy-azure"></a>Acheter Azure

Contoso est fourni avec un [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Cela implique un engagement financier initial en faveur d’Azure, lui permettant d’obtenir d’importants avantages, notamment des options flexibles de facturation et de tarification optimisée.

- Contoso a estimé ses dépenses Azure annuelles. Lorsqu’il a signé le contrat, Contoso a payé en totalité la première année.
- Contoso doit utiliser tous ses services dans l’année, sous peine de perdre les sommes versées.
- Si, pour une raison quelconque, Contoso outrepasse ses engagements et dépenser plus que prévu, Microsoft lui facturera la différence.
- Les coûts qui dépassent cet engagement seront calculés avec les mêmes taux que ceux stipulés dans le contrat. Aucune pénalité n’est appliquée en cas de dépassement.

### <a name="manage-subscriptions"></a>Gérer les abonnements

Après avoir payé pour Azure, Contoso doit déterminer comment gérer ses abonnements. Contoso a signé un Contrat Entreprise et, par conséquent, il n’y a aucune limite sur le nombre d’abonnements Azure qu’elles peuvent créer.

- L’inscription d’une entreprise Azure définit la forme et l’utilisation des services Azure en son sein et la structure de gouvernance principale.
- Dans un premier temps, Contoso a déterminé une structure pour l’inscription de l’entreprise. Elle a utilisé [cet article](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-governance) pour comprendre et concevoir cette structure.
- Pour l’instant, Contoso a décidé d’utiliser une approche fonctionnelle pour gérer ses abonnements.
    - Dans son entreprise, elle disposera d’un seul service informatique assurant la gestion du budget Azure. Ce sera le seul groupe avec des abonnements.
    - Elle étendra à l’avenir ce modèle afin d’ajouter d’autres groupes d’entreprise en tant que services dans l’inscription de l’entreprise.
    - Au sein du service informatique, Contoso a structuré deux abonnements, Production et Développement.
    - Si Contoso requiert ultérieurement des abonnements supplémentaires, elle devra gérer l’accès, les stratégies et la conformité de ces abonnements. Elle pourra le faire en introduisant des [groupes d’administration Azure](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), sous la forme d’une couche supplémentaire au-dessus des abonnements.

    ![Structure de l’entreprise](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Examiner les licences

Une fois les abonnements configurés, Contoso peut examiner ses licences Microsoft. Sa stratégie de licence dépendra des ressources qu’elle souhaite migrer vers Azure, et de la façon dont les machines virtuelles et services Azure sont sélectionnés et déployés. 

#### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Lors du déploiement de machines virtuelles dans Azure, les images standard incluent une licence qui facture Contoso à la minute pour le logiciel utilisé. Toutefois, Contoso est un client Microsoft de longue date qui a conservé des Contrats Entreprise et des licences avec Software Assurance (SA). 

Azure Hybrid Benefit fournit une méthode économique pour la migration de Contoso, en lui permettant de réaliser des économiser sur l’enregistrement des machines virtuelles Azure et des charges de travail SQL Server en conversion ou en réutilisant des licences Windows Server Datacenter et Standard couvertes par la garantie Software Assurance. Cela permettra à Contoso de payer un taux de calcul inférieur pour les machines virtuelles et SQL Server. [Plus d’informations](https://azure.microsoft.com/pricing/hybrid-benefit/)


#### <a name="license-mobility"></a>Mobilité de licence

Mobilité de licence à travers la Software Assurance octroie aux clients de licences en volume Microsoft comme Contoso la flexibilité de déployer des applications serveur éligibles grâce à l'activation de SA sur Azure. Cela élimine la nécessité d’acheter de nouvelles licences. Comme cette mobilité n’engendre aucuns frais, les licences existantes peuvent être facilement déployées dans Azure. [Plus d’informations](https://azure.microsoft.com/pricing/license-mobility/)

#### <a name="reserve-instances-for-predictable-workloads"></a>Réserver des instances pour les charges de travail prévisibles

Les charges de travail prévisibles sont celles qui doivent toujours être disponibles avec des machines virtuelles en cours d’exécution. Par exemple, les applications métier telles qu’un système ERP SAP.  Par opposition, les charges de travail imprévisibles sont celles qui sont variables. Par exemple, les machines virtuelles activées lors des périodes de forte demande et désactivées pendant les heures creuses.

![Instance réservée](./media/contoso-migration-infrastructure/reserved-instance.png) 

En contrepartie de l’utilisation d’instances réservées pour des instances de machines virtuelles spécifiques qui doivent être gérées sur de longues périodes, Console peut obtenir à la fois une remise et une capacité classée par priorité. Grâce aux [instances réservées Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/) et à Azure Hybrid Benefit, Contoso peut économiser jusqu'à 82 % sur le tarif d’un paiement à l'utilisation (avril 2018).


## <a name="step-2-manage-hybrid-identity"></a>Étape 2 : Gérer l’identité hybride

L’octroi et le contrôle de l’accès utilisateur à des ressources Windows Azure avec gestion de l’identité et de l’accès (IAM) constituent une étape importante dans la mise en place de votre infrastructure Windows Azure.  

- Contoso décide d’étendre son système Active Directory local dans le cloud plutôt que de créer un nouveau système distinct dans Azure.
- Elle crée pour cela un système Active Directory basé sur Azure.
- Comme Contoso ne dispose pas d’Office 365, elle doit approvisionner un nouveau système Azure AD.
- Office 365 utilise Azure AD pour la gestion des utilisateurs. Si Contoso avait utilisé Office 365, elle disposerait déjà d’un système Azure AD qui servirait de système AD principal.
- [En savoir plus](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) sur Azure AD pour Office 365 et sur [l’ajout d’un abonnement](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) à un système Azure AD existant.

### <a name="create-an-azure-ad"></a>Créer une application Azure AD

Contoso utilise l’édition Azure Active Directory Free incluse avec un abonnement Azure. Ajoutez un nouvel annuaire AD comme suit :

1. Dans le [portail Azure](http://portal.azure.com/), Contoso accède à **Créer une ressource** > **Identité** > **Azure Active Directory**.
2. Dans **Créer un annuaire**, elle spécifie le nom de l’annuaire, un nom de domaine initial et la région dans laquelle l’annuaire Azure AD doit être créé.

    ![Créer une application Azure AD](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > Lorsque Contoso crée l’annuaire, un nom de domaine initial est fourni au format nom_de_domaine.onmicrosoft.com. Ce nom ne peut pas être changé ni supprimé. Au lieu de cela, Contoso doit ajouter son nom de domaine inscrit à Azure AD.

### <a name="add-the-domain-name"></a>Ajouter le nom de domaine

Pour utiliser son nom de domaine standard, Contoso doit l’ajouter en tant que nom personnalisé à Azure AD. Cette option permet d’administrateurs d’attribuer des noms d’utilisateur courants. Par exemple, un utilisateur peut se connecter avec une adresse de messagerie billg@contoso.com, au lieu d’utiliser billg@contosomigration.onmicrosoft.com. 

Pour configurer un nom personnalisé, Contoso ajoute à l’annuaire, une entrée DNS, puis vérifie le nom dans Azure AD.

1. Dans **Noms de domaine personnalisés** > **Ajouter un domaine personnalisé**, elle ajoute le domaine.
2. Pour utiliser une entrée DNS dans Azure, elle doit l’inscrire auprès du bureau d’enregistrement de domaines. 

    - Dans la liste **Noms de domaines personnalisés**, elle note les informations DNS pour le nom. Contoso utilise une entrée MX.
    - Pour cela, elle doit accéder au serveur de noms. Dans le cas de Contoso, elle se connecte au domaine Contoso.com et crée un nouvel enregistrement MX pour l’entrée DNS fournie par Azure AD, en utilisant les informations indiquées.  
1. Une fois les enregistrements DNS propagés dans les détails du domaine, elle clique sur **Vérifier** pour vérifier le nom personnalisé.

     ![DNS Azure AD](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Configurer les utilisateurs et groupes Azure et locaux

Maintenant que son Azure AD est en cours d’exécution, Contoso doit ajouter des employés à des groupes AD locaux qui se synchroniseront avec Azure AD. Nous recommandons d’utiliser des noms de groupes locaux correspondant aux noms de groupes de ressources dans Azure. Cela facilite l’identification des correspondances à des fins de synchronisation.

#### <a name="create-resource-groups-in-azure"></a>Créer des groupes de ressources dans Azure

Les groupes de ressources Azure rassemblent des ressources Azure. Un ID de groupe de ressources permet à Azure d’effectuer des opérations sur les ressources au sein du groupe.

- Un abonnement Azure peut avoir plusieurs groupes de ressources, mais un groupe de ressources ne peut exister que dans un seul abonnement.
- En outre, un même groupe de ressources peut avoir plusieurs ressources, mais une ressource ne peut appartenir qu’à un seul groupe.

Contoso configure les groupes de ressources Azure comme décrit dans le tableau suivant.

**Groupe de ressources** | **Détails**
--- | ---
**ContosoCobRG** | Ce groupe contient toutes les ressources liées à la continuité des activités.  Il inclut les coffres que Contoso crée lors de l’utilisation des services Azure Site Recovery et Sauvegarde Azure.<br/><br/> Il inclut également les ressources utilisées pour la migration, y compris Azure Migrate et Database Migration Service.
**ContosoDevRG** | Ce groupe contient des ressources de développement et de test.
**ContosoFailoverRG** | Ce groupe sert de zone d’accueil pour les ressources basculées.
**ContosoNetworkingRG** | Ce groupe contient toutes les ressources de mise en réseau.
**ContosoRG** | Ce groupe contient les ressources associées aux bases de données et aux applications de production.

Contoso crée les groupes de ressources comme suit :

1. Dans le portail Azure > **Groupes de ressources**, elle ajoute un groupe.
2. Pour chaque groupe, elles spécifient un nom, l’abonnement auquel appartient le groupe, et la région.
3. Les groupes de ressources s’affichent dans la liste **Groupes de ressources**.

    ![Groupes de ressources](./media/contoso-migration-infrastructure/resource-groups.png) 


#### <a name="create-matching-security-groups-on-premises"></a>Créer des groupes de sécurité locaux correspondants

1. Dans son annuaire Active Directory local, Contoso configure des groupes de sécurité avec des noms correspondant aux noms des groupes de ressources Azure.
 
    ![Groupes de sécurité Azure AD locaux](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. À des fins de gestion, elle crée un groupe supplémentaire qui sera ajouté à tous les autres groupes. Ce groupe disposera d’un droit d’accès à tous les groupes de ressources dans Azure. Un nombre limité d’administrateurs généraux sera ajouté à ce groupe.

### <a name="synchronize-ad"></a>Synchroniser AD

Contoso souhaite fournir une identité commune pour accéder aux ressources locales et dans le cloud. Pour cela, elle intègre son annuaire Active Directory local à Azure AD. Avec ce modèle :

- Les utilisateurs et les organisations peuvent profiter d’une identité unique pour accéder à des applications locales et des services cloud comme Office 365, ou des milliers d’autres sites sur Internet.
- Les administrateurs peuvent utiliser les groupes dans AD pour implémenter le [contrôle d'accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) dans Azure.

Pour faciliter l’intégration, Contoso se sert de l’[outil Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Lorsque vous installez et configurez l’outil sur un contrôleur de domaine, il synchronise les identités AD locales avec Azure AD. 

### <a name="download-the-tool"></a>Télécharger l’outil

1. Dans le portail Azure, Azure accède à **Azure Active Directory** > **Azure AD Connect** et télécharge la dernière version de l’outil sur le serveur utilisé pour la synchronisation.

    ![Télécharger AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. Contoso lance l’installation de **AzureADConnect.msi** à l’aide de l’option **Utiliser la configuration rapide**. Il s’agit de l’installation la plus courante, qui peut être utilisée pour une topologie de forêt unique avec synchronisation de hachage de mot de passe pour authentification.

    ![Assistant AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. Dans **Connexion à Azure AD**, Contoso spécifie les informations d’identification pour se connecter à Azure Active Directory (dans le formulaire CONTOSO\admin ou contoso.com\admin).

    ![Assistant AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. Dans **Connexion à AD DS**, Contoso spécifie les informations d’identification pour son AD local.

     ![Assistant AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. Dans **Prêt pour la configuration**, Contoso clique sur **Démarrez le processus de synchronisation une fois la configuration terminée** pour démarrer immédiatement la synchronisation. Elle lance ensuite l’installation.


- Contoso dispose d’une connexion directe à Azure. Si votre AD local se trouve derrière un proxy, lisez cet [article](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Après la première synchronisation, les objets AD locaux apparaissent dans Azure AD.

    ![AD local dans Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- Le service informatique de Contoso est représenté dans chaque groupe, en fonction de son rôle.

    ![Membres de l’AD local dans Azure](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>Configurer le RBAC

Le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) Azure permet une gestion précise de l’accès pour Azure. L’utilisation de RBAC vous permet de n’accorder que les droits d’accès dont les utilisateurs ont besoin pour effectuer leur travail. Vous affectez le rôle RBAC approprié aux utilisateurs, groupes et applications au niveau d’une étendue. L’étendue d’une attribution de rôle peut être une seule ressource, un groupe de ressources ou un abonnement. 

Contoso affecte désormais des rôles aux groupes AD synchronisés localement.

1. Dans le groupe de ressources **ControlCobRG**, elle clique sur **Contrôle d’accès (IAM)** > **Ajouter**.
2. Dans **Ajouter des autorisations** > **Rôle**, elle sélectionne **Contributeur**, puis le groupe AD **ContosoCobRG** AD dans la liste. Le groupe apparaît alors dans la liste **Membres sélectionnés**. 
3. Contoso répète cette procédure avec les mêmes autorisations pour les autres groupes de ressources (à l’exception de **ContosoAzureAdmins**), en ajoutant les autorisations Contributeur au compte AD qui correspond au groupe de ressources.
4. Pour le groupe AD **ContosoAzureAdmins**, elle affecte le rôle **Propriétaire**.

    ![Membres de l’AD local dans Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>Étape 3 : Conception pour résilience et urgence

Les ressources Azure sont déployées dans des régions.
- Les régions sont organisées en zones géographiques et Azure garantit que les exigences en matière de résidence de données, de souveraineté, de conformité et de résilience sont respectées dans les limites géographiques.
- Une région est composée d’un ensemble de centres de données. Ces centres de données sont déployés dans un périmètre avec une latence définie et connectés via un réseau régional dédié à faible latence.
- Chaque région Azure est associée à une autre région pour assurer la résilience.
- En savoir plus sur les [régions Azure](https://azure.microsoft.com/global-infrastructure/regions/) et comprendre [comment les régions sont jumelées](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Contoso a choisi Est des États-Unis 2 (en Virginie) comme région principale et Centre des États-Unis comme région secondaire. Il existe deux raisons à cela :

- Le centre de données Contoso se trouve à New York, et Contoso a voulu limiter la latence avec le centre de données le plus proche.
- La région Est des États-Unis 2 offre tous les services et produits dont l’entreprise a besoin. Toutes les régions Azure ne proposent pas les mêmes produits et services. Vous pouvez consulter la section [Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/).
- Centre des États-Unis est la région Azure jumelée avec Est des États-Unis 2.

Dans la perspective de créer un environnement hybride, Contoso doit prévoir la mise en place une stratégie de résilience et de récupération d’urgence lors de la conception de la région. Généralement, les stratégies vont d’une seule région de déploiement, qui s’appuie sur les fonctionnalités de la plateforme Azure comme les domaines d’erreur et le jumelage régional pour résilience, à un modèle Active-Active complet dans lequel les services cloud et la base de données sont déployés et desservent les utilisateurs de deux régions.

Contoso a opté pour une solution intermédiaire. Elle déploiera ses applications et ressources dans une région principale, en conservant une infrastructure complète dans la région secondaire afin de disposer d’une sauvegarde complète en cas de défaillance totale d’une application ou d’une région.


## <a name="step-4-design-a-network-infrastructure"></a>Étape 4 : Concevoir une infrastructure réseau

Une fois la région en place, Contoso peut définir une stratégie de mise en réseau. Elle doit réfléchir à la façon dont son centre de données local et Azure se connectent et communiquent entre eux, et à la façon de concevoir son infrastructure réseau dans Azure. Plus précisément, elle doit :

**Planifier la connectivité du réseau hybride** : déterminer la façon dont elle va connecter les réseaux locaux et Azure.
**Concevoir une infrastructure réseau Azure** : déterminer la façon dont elle va déployer des réseaux sur ses régions. Mode de communication des réseaux au sein de la même région et entre les régions.
**Concevoir et configurer des réseaux Azure** : configurer les réseaux et sous-réseaux Azure et choisir les données qui y résideront.

### <a name="plan-hybrid-network-connectivity"></a>Planifier la connectivité du réseau hybride

Contoso a étudié [plusieurs architectures](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) pour créer un réseau hybride entre Azure et son centre de données local. [En savoir plus](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) sur la comparaison des options.

Pour rappel, l’infrastructure du réseau local de Contoso se compose actuellement d’un centre de données à New York et de branches locales dans la partie est des États-Unis.  Tous les emplacements disposent d’une connexion Internet de qualité professionnelle.  Chacune des branches est ensuite connectée au centre de données via un tunnel VPN IPSec sur Internet.

![Réseau Contoso](./media/contoso-migration-infrastructure/contoso-networking.png) 

Voici comment Contoso a décidé d’implémenter une connectivité hybride :

1. Configurer une nouvelle connexion VPN de site à site entre le centre de données Contoso de New York et les deux régions Azure Est des États-Unis 2 et Centre des États-Unis.
2. Le trafic des branches pour les réseaux virtuels Azure sera acheminé via le principal centre de données Contoso. 
3. À mesure que son déploiement Azure se poursuit, Contoso établira une connexion ExpressRoute entre son centre de données et les régions Azure. Dans ce cas, elle conservera la connexion VPN de site à site uniquement à des fins de basculement.
    - [En savoir plus](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) sur le choix entre une solution hybride ExpressRoute et VPN.
    - Vérifiez les [emplacements ExpressRoute et la prise en charge](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**VPN uniquement**

![VPN Contoso](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**VPN et ExpressRoute**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Concevoir l’infrastructure réseau Azure

Il est essentiel pour Contoso de mettre en place des réseaux d’une manière garantissant la sécurité et l’évolutivité de son déploiement hybride. Pour cela, Contoso adopte une approche à long terme et conçoit des réseaux virtuels résilients et prêts pour l’entreprise. [En savoir plus](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) sur la planification de réseaux virtuels.

Pour connecter ses deux régions, Contoso a décidé d’implémenter un modèle de réseau hub à hub :

- Dans chaque région, Contoso utilisera un modèle « hub et spoke ».
- Pour connecter des réseaux et des hubs, Contoso utilisera l’homologation de réseau Azure.

#### <a name="network-peering"></a>Homologation de réseau

Azure fournit un réseau d’homologation pour connecter des réseaux virtuels et des hubs. L’homologation globale permet d’établir des connexions entre des réseaux virtuels/hubs de différentes régions. L’homologation locale connecte des réseaux virtuels d’une même région. L’homologation de réseaux virtuels offre plusieurs avantages :

- Le trafic réseau entre les réseaux virtuels homologués est privé.
- Le trafic entre les réseaux virtuels reste sur le réseau principal de Microsoft. Aucun chiffrement et aucune connexion Internet publique, ni passerelle ne sont nécessaires pour que les réseaux virtuels communiquent.
- L’homologation fournit une connexion par défaut à faible latence et haut débit entre les ressources de différents réseaux virtuels.

[En savoir plus](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) sur l’homologation de réseaux.

#### <a name="hub-to-hub-across-regions"></a>Déploiement « hub à hub » entre les régions

Contoso déploiera un hub dans chaque région. Un hub est un réseau virtuel (VNet) dans Azure qui centralise la connectivité à votre réseau local. Les réseaux virtuels de hubs se connectent à l’aide d’une homologation de réseaux virtuels globale. L’homologation de réseaux virtuels globale connecte des réseaux virtuels entre des régions Azure.

- Le hub de chaque région est jumelé à son hub partenaire dans l’autre région.
- Le hub est jumelé à chaque réseau dans sa région et peut se connecter à toutes les ressources réseau.

    ![Homologation globale](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>Déploiement « hub et spoke » dans une région

Dans chaque région, Contoso déploiera des réseaux virtuels à différentes fins, sous forme de réseaux spoke à partir du hub de région. Les réseaux virtuels d’une région utilisent l’homologation pour se connecter à leur hub et entre eux.

#### <a name="design-the-hub-network"></a>Concevoir le réseau hub

Dans le modèle « hub et spoke » que Contoso a choisi, l’entreprise doit réfléchir à la façon dont sera acheminé le trafic provenant de son centre de données local et d’Internet. Voici comment Contoso a décidé de gérer le routage pour les hubs des régions Ccentre des États-Unis et Est des États-Unis 2 :

- Elle crée un réseau appelé « c inverse », car il s’agit du chemin d’accès suivi par les paquets entre le réseau entrant et le réseau sortant.
- Cette architecture de réseau a deux limites, une zone de périmètre front-end non approuvée et une zone back-end de confiance.
- Un pare-feu disposera d’une carte réseau dans chaque zone afin de contrôler l’accès aux zones de confiance.
- À partir d'Internet :
    - Le trafic Internet atteindra une adresse IP publique avec équilibrage de charge sur le réseau de périmètre.
    - Ce trafic est acheminé via le pare-feu et soumis aux règles du pare-feu.
    - Une fois les contrôles d’accès au réseau mis en place, le trafic est transféré vers l’emplacement approprié dans la zone de confiance.
    - Le trafic sortant du réseau virtuel sera acheminé vers Internet à l’aide d’itinéraires définis par l’utilisateur (UDR). Le trafic est forcé via le pare-feu et inspecté conformément aux stratégies de Contoso.
- À partir du centre de données Contoso :
    - Le trafic entrant via le VPN de site à site (ou ExpressRoute) accède à l’adresse IP publique de la passerelle VPN Azure.
    - Le trafic est acheminé via le pare-feu et soumis aux règles du pare-feu.
    - Après application de ces règles, le trafic est transféré vers un équilibreur de charge interne (référence SKU standard) sur le sous-réseau de la zone interne de confiance.
    - Le trafic sortant du sous-réseau approuvé vers le centre de données local via le VPN est acheminé via le pare-feu, où les règles sont appliquées, avant d’atteindre la connexion de site à site VPN.



### <a name="design-and-set-up-azure-networks"></a>Concevoir et configurer des réseaux Azure

Avec un réseau et une topologie de routage en place, Contoso est prête à configurer ses réseaux et sous-réseaux Azure.

- Contoso implémentera un réseau privé de classe A dans Azure (0.0.0.0 à 127.255.255.255). Cette solution fonctionne car son infrastructure locale actuelle comporte un espace d’adressage privé de classe B 172.160.0/16 pour éviter tout risque de chevauchement entre les plages d’adresses.
- Elle déploiera des réseaux virtuels dans ses régions principales et secondaires.
- Elle utilisera une convention d’affectation de noms qui inclut le préfixe **VNET** et l’abréviation de la région **EUS2** ou **CUS**. Avec cette norme, les réseaux de hubs seront nommés **VNET-HUB-EUS2** (Est des États-Unis 2) et **VNET-HUB-CUS** (Centre des États-Unis).
- Comme Contoso n’a pas de [solution IPAM](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), elle doit planifier son routage de réseau sans NAT.


#### <a name="virtual-networks-in-east-us-2"></a>Réseaux virtuels dans Est des États-Unis 2

Est des États-Unis 2 est la région principale que Contoso utilisera pour déployer des services et des ressources. Voici comment Contoso va structurer les réseaux de ces régions :

- **Hub** : le réseau virtuel hub dans la région Est des États-Unis 2 est le point central de connectivité principal vers son centre de données local.
- **Réseaux virtuels** : les réseaux virtuels spoke dans la région Est des États-Unis 2 peut servir à isoler les charges de travail, si nécessaire. En plus du réseau virtuel hub, Contoso disposera de deux réseaux virtuels spoke dans la région Est des États-Unis 2 :
    - **VNET-DEV-EUS2**. Ce réseau virtuel fournit à l’équipe de développement et de test un réseau entièrement fonctionnel pour ses projets de développement. Il sert de zone de production pilote et s’appuie sur l’infrastructure de production pour fonctionner.
    - **VNET-PROD-EUS2** : les composants de production IaaS Azure seront placés dans ce réseau. 
    -  Chaque réseau virtuel possède son propre espace d’adressage unique, sans chevauchement. Contoso souhaite configurer le routage sans NAT.
- **Sous-réseaux** :
    - Il y aura un sous-réseau dans chaque réseau pour chaque niveau d’application
    - Chaque sous-réseau du réseau de production aura un sous-réseau correspondant dans le réseau virtuel de développement.
    - En outre, le réseau de production comporte un sous-réseau pour les contrôleurs de domaine.

Les réseaux virtuels de la région Est des États-Unis 2 sont résumés dans le tableau suivant.

**Réseau virtuel** | **Plage** | **Homologue**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2, VNET-DEV-EUS2, VNET-PROD-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-PROD-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2, VNET-PROD-CUS

![Hub/spoke dans la région principale](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Sous-réseaux du réseau hub de la région Est des États-Unis 2 (VNET-HUB-EUS2)

**Sous-réseau/zone** | **CIDR** | **Adresses IP utilisables
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**OB-UntrustZone** | 10.240.2.0/24 | 251
**OB-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Sous-réseaux du réseau de développement de la région Est des États-Unis 2 (VNET-DEV-EUS2)

Le réseau virtuel de développement est utilisé par l’équipe de développement comme zone de production pilote. Il comporte trois sous-réseaux.

**Sous-réseau** | **CIDR** | **Adresses** | **Dans un sous-réseau**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | Machines virtuelles de niveau front-end/web
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | Machines virtuelles de niveau application
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | Machines virtuelles de base de données


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Sous-réseaux du réseau de production de la région Est des États-Unis 2 (VNET-PROD-EUS2)

Les composants IaaS Azure sont situés dans le réseau de production. Chaque niveau application a son propre sous-réseau. Les sous-réseaux correspondent à ceux du réseau de développement, avec ajout d’un sous-réseau pour les contrôleurs de domaine.

**Sous-réseau** | **CIDR** | **Adresses** | **Dans un sous-réseau**
--- | --- | --- | ---
**PROD-FE-EUS2** | 10.245.32.0/22 | 1019 | Machines virtuelles de niveau front-end/web
**PROD-APP-EUS2** | 10.245.36.0/22 | 1019 | Machines virtuelles de niveau application
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | Machines virtuelles de base de données
**PROD-DC-EUS2** | 10.245.42.0/23 | 251 | Machines virtuelles de contrôleur de domaine


![Architecture réseau du hub](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>Réseaux virtuels de la région Centre des États-Unis (région secondaire)

La région Centre des États-Unis est la région secondaire de Contoso. Voici comment Contoso va structurer les réseaux de ces régions :

- **Hub** : le réseau virtuel hub de la région Est des États-Unis 2 est le point central de connectivité à son centre de données local, et les réseaux virtuels spoke de la région Est des États-Unis 2 peuvent être utilisés pour isoler des charges de travail si nécessaire, gérées séparément des autres spokes.
- **Réseaux virtuels** : Contoso disposera de deux réseaux virtuels dans la région Centre des États-Unis :
    - VNET-PROD-CUS. Ce réseau virtuel est un réseau de production, semblable à VNET-PROD_EUS2. 
    - VNET-ASR-CUS. Ce réseau virtuel servira d’emplacement dans lequel les machines virtuelles sont créées après le basculement depuis le site local, ou d’emplacement pour les machines virtuelles Azure qui ont basculé de la région principale vers la région secondaire. Ce réseau est similaire aux réseaux de production, mais il ne comporte aucun contrôleur de domaine.
    -  Chaque réseau virtuel de la région possède son propre espace d’adressage, sans chevauchement. Contoso souhaite configurer le routage sans NAT.
- **Sous-réseaux** : les sous-réseaux seront structurés de la même façon que ceux de la région Est des États-Unis 2, à la différence qu’ils n’ont pas besoin d’un sous-réseau pour les contrôleurs de domaine.

Les réseaux virtuels de la région Centre des États-Unis sont résumés dans le tableau suivant.

**Réseau virtuel** | **Plage** | **Homologue**
--- | --- | ---
**VNET-HUB-CUS** | 10.250.0.0/20 | VNET-HUB-EUS2, VNET-ASR-CUS, VNET-PROD-CUS
**VNET-ASR-CUS** | 10.255.16.0/20 | VNET-HUB-CUS, VNET-PROD-CUS
**VNET-PROD-CUS** | 10.255.32.0/20 | VNET-HUB-CUS, VNET-ASR-CUS, VNET-PROD-EUS2  


![Hub/spoke d’une région jumelée](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>Sous-réseaux du réseau hub Centre des États-Unis (VNET-HUB-CUS)

**Sous-réseau** | **CIDR** | **Adresses IP utilisables**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**OB-UntrustZone** | 10.250.2.0/24 | 251
**OB-TrustZone** | 10.250.3.0/24 | 251
**GatewaySubnet** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>Sous-réseaux du réseau de production de la région Centre des États-Unis (VNET-PROD-CUS)

En parallèle au réseau de production dans la région principale Est des États-Unis 2, il existe un réseau de production dans la région secondaire Centre des États-Unis. 

**Sous-réseau** | **CIDR** | **Adresses** | **Dans un sous-réseau**
--- | --- | --- | ---
**PROD-FE-CUS** | 10.255.32.0/22 | 1019 | Machines virtuelles de niveau front-end/web
**PROD-APP-CUS** | 10.255.36.0/22 | 1019 | Machines virtuelles de niveau application
**PROD-DB-CUS** | 10.255.40.0/23 | 507 | Machines virtuelles de base de données
**PROD-DC-CUS** | 10.255.42.0/24 | 251 | Machines virtuelles de contrôleur de domaine

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Sous-réseaux du réseau de basculement et de récupération de la région Centre des États-Unis (VNET-ASR-CUS)

Le réseau VNET-ASR-CUS est utilisé à des fins de basculement entre les régions. Site Recovery servira à répliquer et à basculer les machines virtuelles Azure entre les régions. Il fonctionne également comme un centre de données Contoso vers le réseau Azure pour les charges de travail protégées qui restent sur site, mais basculent vers Azure pour la récupération d’urgence.

VNET-ASR-CUS est le même sous-réseau de base que le sous-réseau de production de la région Est des États-Unis 2, but sans sous-réseau de contrôleur de domaine.

**Sous-réseau** | **CIDR** | **Adresses** | **Dans un sous-réseau**
--- | --- | --- | ---
**ASR-FE-CUS** | 10.255.16.0/22 | 1019 | Machines virtuelles de niveau front-end/web
**ASR-APP-CUS** | 10.255.20.0/22 | 1019 | Machines virtuelles de niveau application
**ASR-DB-CUS** | 10.255.24.0/23 | 507 | Machines virtuelles de base de données

![Architecture réseau du hub](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Configurer des connexions jumelées

Le hub de chaque région sera être jumelé au hub de l’autre région et à tous les réseaux virtuels au sein de la région du hub. Les hubs peuvent ainsi communiquer et afficher tous les réseaux virtuels au sein d’une région. Notez les points suivants :

- L’homologation crée deux connexions : une à partir de l’homologue initial sur le premier réseau virtuel, et une autre sur le second réseau virtuel.
- Dans un déploiement hybride, le trafic qui transite entre les homologues doit être visualisé depuis la connexion VPN entre le centre de données local et Azure. Pour cela, quelques paramètres spécifiques doivent être définis sur les connexions jumelées.

Pour toutes les connexions depuis les réseaux virtuels spoke via le hub et jusqu’au centre de données local, Contoso doit autoriser le transfert du trafic et traverser les passerelles VPN.

##### <a name="domain-controller"></a>Contrôleur de domaine

Pour les contrôleurs de domaine du réseau VNET-PROD-EUS2, Contoso souhaite que le trafic passe entre le réseau hub/production EUS2 et sur la connexion VPN vers le site local. Pour cela, Contoso doit autoriser les éléments suivants :

1. **Autoriser le trafic transféré** et **autoriser les configurations de transit par passerelle** sur la connexion jumelée. Dans notre exemple, il s’agit de la connexion VNET-HUB-EUS2 vers VNET-PROD-EUS2.

    ![Homologation](./media/contoso-migration-infrastructure/peering1.png)

2. **Autoriser le trafic transféré** et **Utiliser des passerelles distantes** sur l’autre côté de l’homologation, sur la connexion VNET-PROD-EUS2 vers VNET-HUB-EUS2.

    ![Homologation](./media/contoso-migration-infrastructure/peering2.png)

3. En mode local, Contoso configurera un itinéraire statique qui achemine le trafic local au réseau virtuel via le tunnel VPN. La configuration est terminée sur la passerelle qui fournit le tunnel VPN de Contoso vers Azure. Pour cela, Contoso utilise le routage et l’accès à distance Windows.

    ![Homologation](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Réseaux de production 

Un réseau homologue spoke ne peut pas voir un réseau homologue spoke d’une autre région via un hub.

Pour que les réseaux de production Contoso des deux régions puissent se voir, Contoso doit établir une connexion jumelée directe pour VNET-PROD-EUS2 et VENT-PROD-CUS. 

![Homologation](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Configurer le DNS

Lorsque vous déployez des ressources dans des réseaux virtuels, vous avez deux options pour la résolution du nom de domaine. Vous pouvez utiliser la résolution de noms fournie par Azure, ou fournir des serveurs DNS pour la résolution. Le type de résolution de noms que vous utilisez dépend de la manière dont vos ressources doivent communiquer entre elles. [Plus d’informations](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) sur le service Azure DNS.

Contoso a décidé que le service Azure DNS n’est pas un bon choix pour son environnement hybride. Elle préfère plutôt tirer parti de ses serveurs DNS locaux.

- Comme il s’agit d’un réseau hybride, toutes les machines virtuelles locales et dans Azure doivent être capables de résoudre les noms afin de fonctionner correctement. Cela signifie que des paramètres DNS personnalisés doivent être appliqués à tous les réseaux virtuels.
- Actuellement, Contoso a déployé des contrôleurs de domaine dans ses centres de données et ses filiales. Ses serveurs DNS principaux sont CONTOSODC1(172.16.0.10) et CONTOSODC2(172.16.0.1)
- Lorsque les réseaux virtuels sont déployés, les contrôleurs de domaine locaux sont configurés pour être utilisés comme serveur DNS dans les réseaux. 
- Pour cette configuration, lorsque vous utilisez un DNS personnalisé sur le réseau virtuel, vous devez ajouter l’adresse IP des programmes de résolution récursifs d’Azure (168.63.129.16) à la liste DNS.  Pour cela, Contoso configure les paramètres du serveur DNS sur chaque réseau virtuel. Par exemple, les paramètres du DNS personnalisé pour le réseau virtuel VNET-HUB-EUS2 se présenteraient ainsi :
    
    ![Système DNS personnalisé](./media/contoso-migration-infrastructure/custom-dns.png)

Outre ses contrôleurs de domaine locaux, Contoso implémentera quatre autres contrôleurs pour prendre en charge ses réseaux Azure, deux pour chaque région. Voici les éléments qui seront déployés dans Azure.

**Région** | **DC** | **Réseau virtuel** | **Sous-réseau** | **Adresse IP**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.5
CUS | CONTOSODC5 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4
CUS | CONTOSODC6 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4

Après avoir déployé les contrôleurs de domaine locaux, Contoso doit mettre à jour les paramètres DNS sur les réseaux de chaque région afin d’inclure de nouveaux contrôleurs de domaine dans sa liste de serveurs DNS.



#### <a name="set-up-domain-controllers-in-azure"></a>Configurer des contrôleurs de domaine dans Azure

Après la mise à jour des paramètres du réseau, Contoso peut installer ses contrôleurs de domaine dans Azure.

1. Dans le portail Azure, elle déploie une nouvelle machine virtuelle Windows Server sur le réseau virtuel approprié.
2. Elle crée des ensembles à haute disponibilité dans chaque emplacement pour la machine virtuelle. Les ensembles de disponibilité effectuent les tâches suivantes :
    - Vérifier que la structure Azure sépare les machines virtuelles dans différentes infrastructures dans la région Azure. 
    -  Permet à Contoso de bénéficier d’un contrat SLA à 99,95 % pour les machines virtuelles dans Azure.  [Plus d’informations](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)

    ![Groupe de disponibilité](./media/contoso-migration-infrastructure/availability-group.png) 
3. Une fois la machine virtuelle déployée, Contoso configure l’interface réseau pour la machine virtuelle. Ici, elle définit l’adresse IP privée comme étant statique et spécifie une adresse valide.

    ![Carte réseau de machine virtuelle](./media/contoso-migration-infrastructure/vm-nic.png)

4. Elle attache maintenant un nouveau disque de données à la machine virtuelle. Ce disque contient la base de données Active Directory et le partage sysvol. 
    - La taille du disque déterminera le nombre d’IOPS pris en charge.
    - Au fil du temps, la taille du disque devra éventuellement être augmentée à mesure que l’environnement se développe.
    - Le lecteur ne doit pas être défini en lecture-écriture pour la mise en cache de l’hôte. Les bases de données Active Directory ne prennent pas en charge cette configuration.

     ![Disque Active Directory](./media/contoso-migration-infrastructure/ad-disk.png)

5. Une fois le disque ajouté, Contoso se connecte à la machine virtuelle via le Bureau à distance puis ouvre le Gestionnaire de serveur.
6. Ensuite, dans **Services de fichiers et de stockage**, elle exécute l’Assistant Nouveau volume, en s’assurant que le lecteur reçoit la lettre F: ou une lettre supérieure sur la machine virtuelle locale.

     ![Assistant Nouveau volume](./media/contoso-migration-infrastructure/volume-wizard.png)

7. Dans le Gestionnaire de serveur, Contoso ajoute le rôle **Active Directory Domain Services**. Ensuite, elle configure la machine virtuelle comme un contrôleur de domaine.

      ![Rôle du serveur](./media/contoso-migration-infrastructure/server-role.png)  

9. Une fois la machine virtuelle configurée comme un contrôleur de domaine et redémarrée, Contoso ouvre le Gestionnaire DNS et configure le programme de résolution d’Azure DNS comme un redirecteur.  Ainsi, le contrôleur de domaine peut transférer les requêtes DNS qu’il ne peut pas résoudre dans Azure DNS.

    ![Redirecteur DNS](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Maintenant, Contoso met à jour les paramètres DNS personnalisés pour chaque réseau virtuel avec le contrôleur de domaine approprié pour la région du réseau virtuel. Elle inclut les contrôleurs de domaine locaux de la liste.

### <a name="set-up-active-directory"></a>Configurer Active Directory

AD est un service critique pour la mise en réseau et doit être correctement configuré. Contoso générera des sites AD pour le centre de données Contoso et pour les régions EUS2 et CUS.  

1. Elle crée deux nouveaux sites (AZURE-EUS2 et AZURE-CUS), en plus du site du centre de données (ContosoDatacenter).
2. Après avoir créé ces sites, Contoso crée des sous-réseaux dans les sites pour qu’ils correspondent aux réseaux virtuels et au centre de données.

    ![Sous-réseaux DC](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Contoso crée ensuite deux liens de sites pour connecter tous les éléments. Les contrôleurs de domaine doivent être déplacés vers leur emplacement.

    ![Liens DC](./media/contoso-migration-infrastructure/dc-links.png)

4. Une fois que tout est configuré, la topologie de réplication Active Directory est en place.
    
    ![Réplication DC](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Lorsque tout est terminé, une liste des contrôleurs de domaine et des sites apparaît dans le centre d’administration Active Directory local.

    ![Centre d'administration AD](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>Étape 5 : Planifier pour la gouvernance

Azure fournit une gamme de contrôles de gouvernance pour les services et la plateforme Azure. [En savoir plus](https://docs.microsoft.com/azure/security/governance-in-azure) sur les principes de base des options.

Comme elle configure l’identité et le contrôle d’accès, Contoso a déjà commencé à mettre en place certains aspects de la gouvernance et de la sécurité. Globalement, il existe trois domaines à prendre en compte :

- **Stratégie**: une stratégie dans Azure applique des règles et des effets sur vos ressources afin que ces ressources restent conformes aux exigences de l’entreprise et aux contrats SLA.
- **Verrous** : Azure vous permet de verrouiller des abonnements, des groupes de ressources et d’autres ressources, afin que ces éléments puissent uniquement être modifiés par les personnes autorisées à le faire.
- **Balises** : les ressources peuvent être contrôlées, auditées et gérées à l’aide de balises. Les balises associent des métadonnées aux ressources pour fournir des informations sur les ressources ou les propriétaires.

### <a name="set-up-policies"></a>Configurer les stratégies

Le service Azure Policy évalue vos ressources, en analysant celles qui ne sont pas conformes avec les définitions de stratégie mises en place. Par exemple, une stratégie peut autoriser uniquement certains types de machines virtuelles, ou nécessiter des ressources comportant une balise spécifique. 

Les stratégies Azure spécifient une définition de stratégie, tandis qu’une affectation de stratégie spécifie l’étendue à laquelle une stratégie doit être appliquée. L’étendue peut aller d’un groupe d’administration à un groupe de ressources. [En savoir plus](https://docs.microsoft.com/azure/azure-policy/create-manage-policy) sur la création et la gestion des stratégies.

Contoso souhaite commencer avec deux stratégies :

- Elle souhaite une stratégie pour s’assurer que les ressources ne peuvent être déployées que dans les régions EUS2 et CUS.
- Elle souhaite limiter les références (SKU) des machines virtuelles aux références (SKU) approuvées uniquement. L’objectif est de s’assurer qu’aucune référence (SKU) de machine virtuelle coûteuse n’est utilisée.

#### <a name="limit-resources-to-regions"></a>Limiter des ressources à des régions

Contoso utilise la définition de stratégie intégrée **Emplacements autorisés** pour limiter les régions de ressources.

1. Dans le portail Azure, cliquez sur **Tous les services**, puis recherchez **Stratégie**.
2. Sélectionnez **Affectations** > **Assigner une stratégie**.
3. Dans la liste des stratégies, sélectionnez **Emplacements autorisés**.
4. Définissez l’**étendue** sur le nom de l’abonnement Azure, puis sélectionnez les deux régions dans la liste autorisée.

    ![Régions autorisées par la stratégie](./media/contoso-migration-infrastructure/policy-region.png)

5. Par défaut, la stratégie est définie sur **Deny** (Refuser), ce qui signifie que si quelqu'un lance un déploiement dans l’abonnement ne figurant pas dans la région EUS2 ou CUS, ce déploiement échoue. Voici ce qui se passe si un utilisateur dans l’abonnement Contoso tente de configurer un déploiement dans la région Ouest des États-Unis.

    ![Stratégie ayant échoué](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Autoriser des références (SKU) de machines virtuelles spécifiques

Contoso utilise la définition de stratégie intégrée **Allow virtual machines SKUs** (Autoriser les références SKU de machines virtuelles) pour limiter le type de machines virtuelles qui peuvent être créées dans l’abonnement. 

![Référence (SKU) de stratégie](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Vérifier la conformité de la stratégie

Les stratégies prennent effet immédiatement, et Contoso peut vérifier la conformité des ressources. 

1. Dans le portail Azure, cliquez sur le lien **Conformité**.
2. Le tableau de bord de conformité s’affiche. Vous pouvez explorer ce tableau de bord pour obtenir plus de détails.

    ![Conformité à la stratégie](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Configurer des verrous

Contoso utilise depuis longtemps l’infrastructure ITIL pour la gestion de ses systèmes. Un des aspects les plus importants de cette infrastructure est le contrôle des modifications, et Contoso souhaite s’assurer que ce contrôle des modifications est implémenté dans son déploiement Azure.

Contoso va implémenter des verrous comme suit :

- Tout composant de production ou de basculement doit figurer dans un groupe de ressources comportant un verrou ReadOnly.  Cela signifie que pour modifier ou supprimer des éléments de production, le verrou doit être supprimé. 
- Les groupes de ressources hors production auront des verrous CanNotDelete. Cela signifie que les utilisateurs autorisés ne peuvent pas lire ou modifier une ressource mais peuvent la supprimer.

[En savoir plus](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) sur les verrous.

### <a name="set-up-tagging"></a>Configurer le marquage

Pour suivre les ressources à mesure qu’elles sont ajoutées, il sera de plus en plus important pour Contoso d’associer des ressources à un service, client et environnement approprié. 

En plus de fournir des informations sur les ressources et les propriétaires, les balises permettront à Contoso d’agréger et de regrouper des ressources, et d’utiliser ces données à des fins de facturation interne.

Contoso doit visualiser ses ressources Azure de façon logique pour l’entreprise. Par exemple, selon un rôle ou un service. Notez que les ressources ne doivent pas nécessairement appartenir au même groupe de ressources pour partager une balise. Pour cela, Contoso créera une taxonomie de balise simple afin que tout le monde utilise les mêmes balises.

**Nom de la balise** | **Valeur**
--- | ---
CostCenter | 12345 : doit être un centre de coût valide de SAP.
BusinessUnit | Nom de l’unité commerciale (de SAP). Correspond à CostCenter.
ApplicationTeam | Alias de messagerie de l’équipe propriétaire de la prise en charge de l’application.
CatalogName | Nom de l’application ou ShareServices, par catalogue de services, que la ressource prend en charge.
ServiceManager | Alias de messagerie du Gestionnaire de services ITIL pour la ressource.
COBPriority | Priorité définie par l’entreprise pour BCDR. Valeurs de 1 à 5.
ENV | Les valeurs possibles sont DEV, STG et PROD. Représente les phases de développement, mise en lots et production.

Par exemple :  

 ![Balises Azure](./media/contoso-migration-infrastructure/azure-tag.png)

Après avoir créé la balise, Contoso reviendra en arrière pour créer de nouvelles définitions de stratégie Azure et affectations, et mettre en place les balises requises dans toute l’organisation.


## <a name="step-6-consider-security"></a>Étape 6 : Prendre en compte la sécurité

La sécurité est cruciale dans le cloud, et Azure fournit un large éventail de fonctionnalités et outils de sécurité. Ces éléments vous aident à créer des solutions sécurisées sur la plateforme Azure sécurisée. Consultez la section [Faites confiance au cloud approuvé](https://azure.microsoft.com/overview/trusted-cloud/) pour en savoir plus sur la sécurité Azure.

Contoso doit prendre en compte quelques aspects importants

- **Azure Security Center** : Azure Security Center fournit des fonctionnalités unifiées de gestion de la sécurité et de protection avancée contre les menaces sur l’ensemble des charges de travail cloud hybrides. Avec Security Center, vous pouvez appliquer des stratégies de sécurité sur l’ensemble de vos charges de travail, limiter votre exposition aux menaces, détecter et répondre aux attaques.  [Plus d’informations](https://docs.microsoft.com/azure/security-center/security-center-intro)
- **Groupes de sécurité réseau (NSG)**  : un NSG est un filtre (pare-feu) qui contient la liste des règles de sécurité qui, une fois appliquées, autorisent ou rejettent le trafic réseau vers les ressources connectées aux réseaux virtuels Azure. [Plus d’informations](https://docs.microsoft.com/azure/virtual-network/security-overview)
- **Chiffrement des données** : Azure Disk Encryption est une fonctionnalité qui vous permet de chiffrer vos disques de machine virtuelle IaaS Windows et Linux. [Plus d’informations](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

### <a name="work-with-the-azure-security-center"></a>Utiliser le Centre de sécurité Azure

Contoso recherche un moyen d’obtenir un aperçu de la sécurité dans son nouveau cloud hybride et plus précisément de ses charges de travail.  Par conséquent, Contoso a décidé d’implémenter Azure Security Center en commençant par les fonctionnalités suivantes : 

- Gestion de stratégie centralisée
- Évaluation continue
- Recommandations exploitables 

#### <a name="centralize-policy-management"></a>Gestion de stratégie centralisée

Avec la gestion de stratégie centralisée, Contoso assurera la conformité aux exigences de sécurité en gérant de façon centralisée les stratégies de sécurité dans l’ensemble de son environnement. Elle peut implémenter facilement et rapidement une stratégie qui s’applique à toutes les ressources Azure.

![Stratégie de sécurité](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Évaluation et action

Contoso s’appuiera sur l’évaluation continue de la sécurité, qui surveille la sécurité des machines, réseaux, services de stockage et de données, ainsi que des applications, pour découvrir d’éventuels problèmes de sécurité. 

- Security Center analyse l’état de sécurité des ressources de calcul, d’infrastructure et de données de Contoso, et celui des services et applications Azure.
- L’évaluation continue aide l’équipe des opérations Contoso à découvrir d’éventuels problèmes de sécurité, comme les systèmes avec des mises à jour de sécurité manquantes ou des ports réseau exposés. 
- Contoso souhaite en particulier s’assurer que toutes ses machines virtuelles sont protégées. Security Center facilite cette opération en vérifiant l’intégrité des machines virtuelles et en proposant des recommandations classées par priorité, permettant de résoudre les failles de sécurité avant qu’elles ne soient exploitées.

![Surveillance](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Utiliser des groupes de sécurité réseau

Contoso peut limiter le trafic réseau vers les ressources d’un réseau virtuel à l’aide de groupes de sécurité réseau.

- Un groupe de sécurité réseau contient une liste de règles de sécurité qui autorisent ou refusent le trafic réseau entrant ou sortant en fonction de l’adresse IP source ou de destination, du port et du protocole.
- Quand elles sont appliquées à un sous-réseau, les règles sont appliquées à toutes les ressources dans le sous-réseau. En plus des interfaces réseau, cela inclut des instances de services Azure déployées dans le sous-réseau.
- Les groupes de sécurité d’application permettent de configurer la sécurité réseau comme un prolongement naturel de la structure de l’application, et donc de regrouper les machines virtuelles et définir des stratégies de sécurité réseau basées sur ces groupes.
    - Les groupes de sécurité d’application vous permettent de réutiliser votre stratégie de sécurité à grande échelle, sans maintenance manuelle d’adresses IP explicites. La plateforme gère la complexité des adresses IP explicites et plusieurs ensembles de règles, ce qui vous permet de vous concentrer sur la logique métier.
    - Vous pouvez spécifier un groupe de sécurité d’application en tant que source et destination dans une règle de sécurité. Après avoir défini votre stratégie de sécurité, vous pouvez créer des machines virtuelles et affecter des cartes réseau de machines virtuelles à un groupe. 


Contoso implémentera une combinaison de groupes de sécurité réseau et de groupes de sécurité d’application. L’entreprise se préoccupe de la gestion des groupes de sécurité réseau. Elle se soucie de l’utilisation abusive des groupes de sécurité réseau et de la complexité que cela peut entraîner pour son personnel chargé des opérations.  Dans cet esprit, elle a adopté deux principes clés qui lui servent de règle générale :

- L’intégralité du trafic vers et depuis tous les sous-réseaux (Nord-Sud) sera soumise à une règle de groupe de sécurité réseau, à l’exception de GatewaySubnets dans les réseaux hub.
- Tous les pare-feu ou contrôleurs de domaine seront protégés à la fois par les groupes de sécurité réseau et les groupes de sécurité réseau de la carte réseau.
- Des groupes de sécurité d’application seront appliqués à toutes les applications de production.


Contoso a créé un modèle montrant le résultat potentiel pour ses applications.

![Sécurité](./media/contoso-migration-infrastructure/asg.png)


Les groupes de sécurité réseau associés aux groupes de sécurité d’application seront configurés avec des privilèges minimum pour s’assurer que seuls les paquets autorisés peuvent circuler d’une partie du réseau vers leur destination.

**Action** | **Name** | **Source** | **Cible** | **Port**
--- | --- | --- | --- | --- 
AUTORISER | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | APP1-FE 80, 443
AUTORISER | AllowWebToApp | APP1-FE | APP1-DB | 1433
AUTORISER | AllowAppToDB | APP1-APP | Quelconque | Quelconque
Deny | DenyAllInbound | Quelconque | Quelconque | Quelconque

### <a name="encrypt-data"></a>Chiffrer les données

Azure Disk Encryption est intégrée à Azure Key Vault, ce qui permet de contrôler et de gérer les clés et les secrets de chiffrement de disque dans un abonnement Key Vault. Elle garantit que toutes les données sur les disques des machines virtuelles sont chiffrées au repos dans le stockage Azure.  

- Contoso a déterminé que des machines virtuelles spécifiques exigent un chiffrement.
- Elle appliquera un chiffrement aux machines virtuelles contenant des données client, confidentielles ou PPP.


## <a name="conclusion"></a>Conclusion

Dans cet article, Contoso a configuré son infrastructure Azure et configuré ou planifié la stratégie d’infrastructure pour l’abonnement Azure, l’identité hybride, la récupération d'urgence, la mise en réseau, la gouvernance et la sécurité. 

Les étapes effectuées ici par Contoso ne sont pas toutes requises pour une migration vers le cloud. Dans son cas, Contoso souhaitait planifier une infrastructure réseau adaptée à tous les types de migrations, tout en étant sécurisée, fiable et évolutive. 

Une fois cette infrastructure en place, elle est prête à tester la migration.

## <a name="next-steps"></a>Étapes suivantes

Comme premier scénario de migration, Contoso va [évaluer son application à deux niveaux SmartHotel locale pour une migration vers Azure](contoso-migration-assessment.md). 
