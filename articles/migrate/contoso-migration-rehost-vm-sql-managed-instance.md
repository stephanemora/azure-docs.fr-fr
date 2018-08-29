---Titre des données : Héberger une application Contoso localement en migrant vers des VM Azure et Azure SQL Database Managed Instance | Description de Microsoft Docs : Découvrez comment Contoso réhéberge une application localement sur des VM Azure et à l’aide d’Azure SQL Database Managed Instance.
services : site-recovery auteur : rayne-wiselman responsable : carmonm ms.service : site-recovery ms.topic : conceptual ms.date : 13/08/2018 ms.author : raynew

---

# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Migration de Contoso : réhéberger une application locale sur une machine virtuelle Azure et SQL Database Managed Instance

Dans cet article, Contoso migre sa machine virtuelle frontend d’application SmartHotel vers une machine virtuelle Azure à l’aide du service Azure Site Recovery. Contoso migre également la base de données d’application vers Azure SQL Database Managed Instance.

> [!NOTE]
> Azure SQL Database Managed Instance est actuellement en préversion.

Cet article fait partie d’une série décrivant comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations d’arrière-plan et des scénarios qui montrent comment configurer une infrastructure de migration et exécuter différents types de migrations. Les scénarios augmentent en complexité. De nouveaux articles seront ajoutés à la série au fil du temps.


Article | Détails | Statut
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Vue d’ensemble de la stratégie de migration de Contoso, de la série d’articles et des exemples d’application utilisés dans la série. | Disponible
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration de la série. | Disponible
[Article 3 : Évaluer les ressources locales à migrer vers Azure](contoso-migration-assessment.md) | Contoso évalue son application locale SmartHotel à deux niveaux s’exécutant sur VMware. Contoso évalue les machines virtuelles d’application à l’aide du service [Azure Migrate](migrate-overview.md). Contoso évalue la base de données SQL Server d’application à l’aide de [l’Assistant Migration de données](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponible
Article 4 : Réhéberger une application sur une machine virtuelle Azure et SQL Database Managed Instance | Contoso exécute une migration lift-and-shift vers Azure pour son application SmartHotel locale. Contoso migre la machine virtuelle frontend d’application avec [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migre la base de données d’application vers Azure SQL Database Managed Instance avec [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Cet article
[Article 5 : Réhéberger une application sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Contoso migre ses machines virtuelles d’application SmartHotel vers des machines virtuelles Azure en utilisant le service Site Recovery. | Disponible
[Article 6 : Réhéberger une application sur des machines virtuelles Azure et dans un groupe de disponibilité SQL Server AlwaysOn](contoso-migration-rehost-vm-sql-ag.md) | Contoso migre l’application SmartHotel. Contoso utilise Site Recovery pour migrer les machines virtuelles d’application. La société utilise Database Migration Service pour migrer la base de données d’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. | Disponible
[Article 7 : ré-héberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Contoso effectue une migration lift-and-shift de son application osTicket Linux sur des machines virtuelles Azure à l’aide de Site Recovery. | Disponible
[Article 8 : Réhéberger une application Linux sur des machines virtuelles Azure et Azure Database pour MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migre son application osTicket Linux vers des machines virtuelles Azure à l’aide de Site Recovery. La société migre la base de données d’application vers Azure Database pour MySQL à l’aide de MySQL Workbench. | Disponible
[Article 9 : Refactoriser une application vers une application web Azure et Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migre son application SmartHotel vers une application web Azure, et migre la base de données d’application vers une instance d’Azure SQL Server. | Disponible
[Article 10 : Refactoriser une application Linux vers une application web Azure et Azure Database pour MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migre son application osTicket Linux vers une application web Azure sur plusieurs sites. L’application web est intégrée à GitHub pour la livraison continue. Contoso migre la base de données d’application vers une instance d’Azure Database pour MySQL. | Disponible
[Article 11 : Refactoriser Team Foundation Server sur Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Contoso migre son déploiement local de Team Foundation Server vers Visual Studio Team Services dans Azure. | Disponible
[Article 12 : Réarchitecturer une application dans des conteneurs Azure et Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migre son application SmartHotel vers Azure, puis réarchitecture l’application. Contoso réarchitecture la couche web d’application en tant que conteneur Windows, et réarchitecture la base de données d’application à l’aide d’Azure SQL Database. | Disponible
[Article 13 : Regénérer une application dans Azure](contoso-migration-rebuild.md) | Contoso regénère son application SmartHotel à l’aide d’une série de fonctionnalités et services Azure, notamment Azure App Service, Azure Kubernetes Service, Azure Functions, Azure Cognitive Services et Azure Cosmos DB. | Disponible

Vous pouvez télécharger l’exemple d’application SmartHotel qui est utilisé dans cet article à partir de [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Architecture locale


Le diagramme suivant montre l’infrastructure locale actuelle de Contoso :

![Architecture actuelle de Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso dispose d’un centre de données principal. Le centre de données principal se trouve dans la ville de New York dans l’Est des États-Unis.
- Contoso a trois branches locales supplémentaires aux États-Unis.
- Le centre de données principal est connecté à Internet par le biais d’une connexion Ethernet Fiber Metro (500 Mbits/s).
- Chaque branche est connectée localement à Internet par le biais de connexions de qualité professionnelle, avec des tunnels VPN IPsec vers le centre de données principal. Cette configuration permet au réseau entier d’être connecté en permanence, et elle optimise la connectivité Internet.
- Le centre de données principal est entièrement virtualisé avec VMware. Contoso dispose de deux hôtes de virtualisation ESXi 6.5 gérés par vCenter Server 6.5.
- Contoso utilise Active Directory pour la gestion d’identité. Contoso utilise des serveurs DNS sur le réseau interne.
- Les contrôleurs de domaine dans le centre de données s’exécutent sur des machines virtuelles VMware. Les contrôleurs de domaine au niveau des branches locales s’exécutent sur des serveurs physiques.

## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique de Contoso a travaillé en étroite collaboration avec ses partenaires commerciaux pour comprendre le résultat que l’entreprise souhaite obtenir avec cette migration :

- **Répondre à la croissance de l’entreprise** : Contoso se développe. Il en découle une pression accrue sur l’infrastructure et les systèmes locaux et de l’entreprise.
- **Augmenter l’efficacité** : Contoso doit supprimer les procédures inutiles et rationaliser les processus pour ses développeurs et ses utilisateurs. L’entreprise a besoin d’une informatique rapide et doit éviter les pertes de temps ou d’argent afin de répondre plus rapidement aux exigences des clients.
- **Augmenter l’agilité** : l’informatique de Contoso doit être plus réactive aux besoins de l’entreprise. Elle doit être en mesure de réagir plus rapidement que l’évolution du marché pour réussir dans une économie mondiale. L’informatique chez Contoso ne doit pas devenir une entrave à l’activité.
- **Mise à l’échelle** : à mesure que l’entreprise croît, l’informatique de Contoso doit fournir des systèmes capables de croître au même rythme.

## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a identité les objectifs de cette migration. L’entreprise utilise des objectifs de migration pour déterminer la meilleure méthode de migration.

- Après la migration, l’application dans Azure devra offrir les mêmes performances qu’aujourd’hui dans l’environnement VMWare local de Contoso. Migrer vers le cloud ne signifie nullement que les performances de l’application deviennent moins importantes.
- Contoso ne souhaite pas investir dans l’application. Elle est vitale pour l’entreprise, mais Contoso souhaite simplement la migrer dans sa forme actuelle vers le cloud.
- Une fois l’application migrée, les tâches d’administration de base de données devront être réduites au minimum.
- Contoso ne souhaite pas utiliser Azure SQL Database pour cette application et recherche des alternatives.

## <a name="proposed-architecture"></a>Architecture proposée

Dans ce scénario :

- Contoso souhaite migrer son application de voyage locale à deux niveaux.
- L’application repose sur deux machines virtuelles, **WEBVM** et **SQLVM**, et se trouve sur un hôte VMware ESXi version 6.5 (**contosohost1.contoso.com**). 
- L’environnement VMware est géré par vCenter Server 6.5 (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- Contoso migre la base de données d’application (**SmartHotelDB**) vers Azure SQL Database Managed Instance.
- Contoso migre les machines virtuelles VMware locales vers une machine virtuelle Azure.
- Contoso dispose d’un centre de données local (**contoso-datacenter**) et d’un contrôleur de domaine local (**contosodc1**).
- Les machines virtuelles locales dans le centre de données Contoso seront désaffectées une fois la migration terminée.

![Architecture du scénario](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Services Azure

de diffusion en continu | Description | Coût
--- | --- | ---
[Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Database Migration Service autorise une migration fluide à partir de plusieurs sources de base de données vers des plateformes de données Azure, avec un temps d’arrêt minimal. | Apprenez-en davantage sur les [régions prises en charge](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) et sur la [tarification de Database Migration Service](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance est un service de base de données managé qui représente une instance de SQL Server entièrement managée dans le cloud Azure. Il utilise le même code que la dernière version du moteur de base de données SQL Server et offre les dernières fonctionnalités, améliorations en termes de performances et correctifs de sécurité. | L’utilisation de SQL Database Managed Instance exécuté dans Azure entraîne des frais basés sur la capacité. Apprenez-en davantage sur la [tarification de Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Le service Site Recovery orchestre et gère la migration et la reprise d’activité pour les machines virtuelles Azure, les machines virtuelles locales et les serveurs physiques.  | Lors de la réplication vers Azure, des frais sur le Stockage Azure sont facturés.  Des machines virtuelles Azure sont créées en cas de basculement et entraînent des frais. Apprenez-en davantage sur les [frais et la tarification de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

 ## <a name="migration-process"></a>Processus de migration

Contoso migre les niveaux web et données de son application SmartHotel vers Azure en effectuant les étapes suivantes :

1. Contoso disposant déjà d’une infrastructure Azure en place, il faut simplement ajouter quelques composants Azure spécifiques pour ce scénario.
2. La couche données sera migrée à l’aide de Data Migration Service. Data Migration Service se connecte à la machine virtuelle SQL Server locale par le biais d’une connexion VPN de site à site entre le centre de données de Contoso et Azure. Ensuite, Data Migration Service migre la base de données.
3. La couche web sera migrée à l’aide d’une migration lift-and-shift avec Site Recovery. Ce processus nécessite la préparation de l’environnement VMware local, la configuration et l’activation de la réplication, et la migration des machines virtuelles par leur basculement vers Azure.

     ![Architecture de la migration](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

## <a name="prerequisites"></a>Prérequis

Contoso et les autres utilisateurs doivent respecter les prérequis suivants pour ce scénario :

Configuration requise | Détails
--- | ---
**S’inscrire à la préversion de Managed Instance** | Vous devez être inscrit pour tester la préversion publique limitée de SQL Database Managed Instance. Vous avez besoin d’un abonnement Azure pour vous [inscrire](https://portal.azure.com#create/Microsoft.SQLManagedInstance). La confirmation d’inscription prend quelques jours. Veillez donc à vous inscrire avant de commencer le déploiement de ce scénario.
**Abonnement Azure** | Vous devez avoir déjà créé un abonnement quand vous effectuez l’évaluation dans le premier article de cette série. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur de l’abonnement, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur.<br/><br/> Si vous avez besoin d’autorisations plus précises, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès à Site Recovery](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site Recovery (local)** | Votre instance locale de vCenter Server doit exécuter la version 5.5, 6.0 ou 6.5<br/><br/> Un hôte ESXi qui exécute la version 5.5, 6.0 ou 6.5<br/><br/> Une ou plusieurs machines virtuelles VMware exécutées sur l’hôte ESXi.<br/><br/> Les machines virtuelles doivent répondre aux [exigences Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configuration [réseau](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) et [stockage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) prise en charge.
**Database Migration Service** | Pour Database Migration Service, vous avez besoin d’un [périphérique VPN local compatible](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Vous devez être en mesure de configurer l’appareil VPN local. Il doit avoir une adresse IPv4 publique externe. L’adresse ne peut pas se trouver derrière un périphérique NAT.<br/><br/> Veillez à disposer de l’accès à votre base de données SQL Server locale.<br/><br/> Le Pare-feu Windows doit pouvoir accéder au moteur de base de données source. Découvrez comment [configurer le Pare-feu Windows pour accéder au moteur de base de données](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> S’il y a un pare-feu devant votre machine de base de données, ajoutez des règles pour autoriser l’accès à la base de données, et aux fichiers par le biais du port SMB 445.<br/><br/> Les informations d’identification utilisées pour se connecter à l’instance source SQL Server et à l’instance cible Managed Instance doivent appartenir à des membres du rôle serveur sysadmin.<br/><br/> Il vous faut un partage réseau dans votre base de données locale que Database Migration Service peut utiliser pour sauvegarder la base de données source.<br/><br/> Vérifiez que le compte de service qui exécute l’instance source de SQL Server dispose d’autorisations d’écriture sur le partage réseau.<br/><br/> Notez l’utilisateur Windows (et son mot de passe) qui dispose d’autorisations complètes sur le partage réseau. Database Migration Service emprunte ces informations d’identifications pour charger les fichiers de sauvegarde sur le conteneur de stockage Azure.<br/><br/> Le processus d’installation SQL Server Express définit le protocole TCP/IP sur **Désactivé** par défaut. Veillez à l’activer.

## <a name="scenario-steps"></a>Étapes du scénario

Voici comment Contoso prévoie de configurer le déploiement :

> [!div class="checklist"]
> * **Étape 1 : Configurer SQL Database Managed Instance** : Contoso doit créer au préalable une instance gérée vers laquelle la base de données SQL Server locale migrera.
> * **Étape 2 : Préparer Database Migration Service** : Contoso doit inscrire le fournisseur de migration de base de données, créer une instance, puis créer un projet Database Migration Service. Contoso doit également configurer un URI (Uniform Resource Identifier) avec signature d’accès partagé (SAP) pour Database Migration Service. Un URI SAP fournit un accès délégué aux ressources du compte de stockage Contoso. Ainsi, Contoso peut accorder des autorisations limitées aux objets de stockage. Contoso configure un URI SAP pour que Database Migration Service puisse accéder au conteneur du compte de stockage sur lequel le service charge les fichiers de sauvegarde SQL Server.
> * **Étape 3 : Préparer Azure pour Site Recovery** : Contoso doit créer un compte de stockage afin de conserver les données répliquées pour Site Recovery. Ils doivent également créer un coffre Azure Recovery Services.
> * **Étape 4 : Préparer une machine virtuelle VMware locale pour Site Recovery** : Contoso doit préparer des comptes pour la découverte de machines virtuelles et l’installation d’agents, afin d’établir une connexion aux machines virtuelles Azure après basculement.
> * **Étape 5 : Répliquer les machines virtuelles** : pour la réplication, Contoso configure les environnements source et cible de Site Recovery ainsi qu’une stratégie de réplication, et commence la réplication des machines virtuelles vers Stockage Azure.
> * **Étape 6 : Migrer la base de données à l’aide de Database Migration Service** : Contoso migre la base de données.
> * **Étape 7 : Migrer les machines virtuelles à l’aide de Site Recovery** : Contoso exécute un test de basculement pour vérifier que tout fonctionne. Ensuite, Contoso exécute un basculement complet pour migrer les machines virtuelles vers Azure.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>Étape 1 : Préparer SQL Database Managed Instance

Pour configurer Azure SQL Database Managed Instance, Contoso a besoin d’un sous-réseau qui remplit les conditions suivantes :

- Le sous-réseau doit être dédié. Il doit être vide et ne doit contenir aucun autre service cloud. Le sous-réseau ne doit pas être un sous-réseau de passerelle.
- Une fois l’instance gérée créée, Contoso ne doit pas ajouter de ressources au sous-réseau.
- Aucun groupe de sécurité réseau ne doit être associé au sous-réseau.
- Le sous-réseau doit avoir une table de routage UDR (Itinéraires définis par l’utilisateur). Le seul itinéraire affecté doit être 0.0.0.0/0 tronçon suivant vers Internet. 
- Système DNS personnalisé facultatif : si un système DNS personnalisé est spécifié sur le réseau virtuel Azure, vous devez ajouter l’adresse IP des programmes de résolution récursifs d’Azure (par exemple 168.63.129.16) à la liste. Découvrez comment [configurer un système DNS personnalisé pour Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Le sous-réseau ne doit pas avoir de point de terminaison de service (stockage ou SQL) associé. Les points de terminaison doivent être désactivés sur le réseau virtuel.
- Le sous-réseau doit avoir un minimum de 16 adresses IP. Découvrez comment [dimensionner le sous-réseau Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances).
- Dans l’environnement hybride de Contoso, des paramètres DNS personnalisés sont nécessaires. Contoso configure les paramètres DNS pour utiliser un ou plusieurs des serveurs DNS Azure de l’entreprise. Apprenez-en davantage sur la [personnalisation du système DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Configurer un réseau virtuel pour Managed Instance

Contoso configure le réseau virtuel comme suit : 

1. Contoso crée un réseau virtuel (**VNET-SQLMI-EU2**) dans la région primaire USA Est 2. Ils ajoutent le réseau virtuel au groupe de ressources **ContosoNetworkingRG**.
2. Contoso affecte un espace d’adressage 10.235.0.0/24. Contoso vérifie que la plage ne chevauche pas d’autres réseaux au sein de son entreprise.
2. Contoso ajoute deux sous-réseaux au réseau :
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Ce sous-réseau est utilisé pour joindre l’annuaire à Managed Instance.

    ![Managed Instance - Créer un réseau virtuel](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Une fois le réseau virtuel et les sous-réseaux déployés, Contoso appaire les réseaux comme suit :

    - Peering de **VNET-SQLMI-EUS2** avec **VNET-HUB-EUS2** (réseau virtuel hub pour la région USA Est 2).
    - Peering de **VNET-SQLMI-EUS2** avec **VNET-PROD-EUS2** (réseau de production).

    ![Appairage de réseau](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso définit des paramètres DNS personnalisés. Le système DNS pointe tout d’abord sur les contrôleurs de domaine Azure de Contoso. Azure DNS est secondaire. Les contrôleurs de domaine Azure de Contoso se situent comme suit :

    - Situé sur le sous-réseau **PROD-CC-EUS2**, sur le réseau de production USA Est 2 (**VNET-PROD-EUS2**)
    - Adresse de **CONTOSODC3** : 10.245.42.4
    - Adresse de **CONTOSODC4** : 10.245.42.5
    - Programme de résolution d’Azure DNS : 168.63.129.16

     ![Serveurs DNS du réseau](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*Besoin de plus d’aide ?*

- Obtenez une vue d’ensemble de [SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance).
- Découvrez comment [créer un réseau virtuel pour SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances).
- Découvrez comment [configurer le peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering).
- Découvrez comment [mettre à jour les paramètres DNS d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

### <a name="set-up-routing"></a>Configuration du routage

Managed Instance est placé sur un réseau privé virtuel. Contoso a besoin d’une table de routage pour que le réseau virtuel communique avec le service de gestion Azure. Si le réseau virtuel ne peut pas communiquer avec le service qui le gère, le réseau virtuel devient inaccessible.

Contoso prend en compte ces facteurs :

- La table de routage contient un ensemble de règles (itinéraires) qui spécifient le mode de routage des paquets envoyés à partir de Managed Instance sur le réseau virtuel.
- La table de routage est associée à des sous-réseaux dans lesquels les instances gérées sont déployées. Chaque paquet quittant un sous-réseau est géré en fonction de la table de routage associée.
- Un sous-réseau ne peut être associé qu’à une seule table de routage.
- La création de tables de routage dans Microsoft Azure n’occasionne aucun frais.

 Pour configurer le routage

1. Contoso crée une table UDR. Contoso crée la table de routage dans le groupe de ressources **ContosoNetworkingRG**.

    ![Table de routage](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Pour respecter les exigences applicables à Managed Instance, une fois la table de routage (**MIRouteTable**) déployée, Contoso ajoute un itinéraire avec un préfixe d’adresse 0.0.0.0/0. L’option **Type de tronçon suivant** est définie sur **Internet**

    ![Préfixe de table de routage](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso associe la table de routage au sous-réseau **SQLMI-DB-EUS2** (sur le réseau **VNET-SQLMI-EUS2**). 

    ![Sous-réseau de table de routage](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*Besoin de plus d’aide ?*

Découvrez comment [configurer des itinéraires pour une instance gérée](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route).

### <a name="create-a-managed-instance"></a>Créer une option Managed Instance

À présent, Contoso peut provisionner SQL Database Managed Instance :

1. Étant donné que l’instance gérée sert une application métier, Contoso la déploie dans sa région USA Est 2 primaire. Contoso ajoute l’instance gérée au groupe de ressources **ContosoRG**.
2. Contoso sélectionne un niveau tarifaire et dimensionne le calcul et le stockage pour l’instance. Apprenez-en davantage sur la [tarification de Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![Instance gérée](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Une fois l’instance gérée déployée, deux nouvelles ressources apparaissent dans le groupe de ressources **ContosoRG** :

    - Un cluster virtuel si Contoso a plusieurs instances gérées
    - L’instance gérée SQL Server Database 

    ![Instance gérée](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*Besoin de plus d’aide ?*

Découvrez comment [provisionner une instance gérée](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-migration-service"></a>Étape 2 : Préparer Database Migration Service

Pour préparer Database Migration Service, Contoso doit effectuer quelques opérations :

- Inscrire le fournisseur Database Migration Service dans Azure.
- Fournir à Database Migration Service l’accès à Stockage Azure pour charger les fichiers de sauvegarde utilisés pour migrer une base de données. Pour fournir l’accès à Stockage Azure, Contoso crée un conteneur Stockage Blob Azure. Contoso génère un URI SAP pour le conteneur de stockage d’objets blob. 
- Créer un projet Azure Database Migration Service.

Ensuite, Contoso effectue les étapes suivantes :

1. Contoso inscrit le fournisseur de migration de base de données sous son abonnement.
    ![Database Migration Service - Registre](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Contoso crée un conteneur de stockage d’objets blob. Contoso génère un URI SAP afin que Database Migration Service puisse y accéder.

    ![Database Migration Service - Générer un URI SAP](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Contoso crée une instance Database Migration Service. 

    ![Database Migration Service - Créer une instance](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso place l’instance Database Migration Service sur le sous-réseau **PROD-DC-EUS2** du réseau virtuel **VNET-PROD-DC-EUS2**.
    - Contoso place Database Migration Service à cet endroit car le service doit être sur un réseau virtuel qui peut accéder à la machine virtuelle SQL Server locale par le biais d’une passerelle VPN.
    - **VNET-PROD-EUS2** est appairé à **VNET-HUB-EUS2** et autorisé à utiliser des passerelles distantes. L’option **Utiliser des passerelles distantes** garantit que Database Migration Service peut communiquer en fonction des besoins.

        ![Database Migration Service - Configurer le réseau](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*Besoin de plus d’aide ?*

- Découvrez comment [configurer Database Migration Service](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Découvrez comment [créer et utiliser une signature d’accès partagé](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Étape 3 : préparer Azure pour le service Site Recovery

Plusieurs éléments Azure sont nécessaires afin que Contoso puisse configurer Site Recovery pour la migration de ses machines virtuelles de couche web (**WEBMV**) :

- Un réseau virtuel où se trouvent les ressources basculées
- Un compte de stockage pour accueillir les données répliquées 
- Un coffre Recovery Services dans Azure.

Contoso configure Site Recovery comme suit :

1. La machine virtuelle étant un frontend web pour l’application SmartHotel, Contoso la fait basculer vers son réseau de production (**VNET-PROD-EUS2**) et son sous-réseau (**PROD-FE-EUS2**) existants. Le réseau et le sous-réseau se trouvent dans la région primaire USA Est 2. Contoso a configuré le réseau quand ils ont [déployé l’infrastructure Azure](contoso-migration-infrastructure.md).
2. Contoso crée un compte de stockage (**contosovmsacc20180528**). Contoso utilise un compte à usage général. Contoso sélectionne le stockage standard et la réplication de stockage localement redondant.

    ![Site Recovery - Créer le compte de stockage](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Le compte de stockage et le réseau étant en place, Contoso crée un coffre (**ContosoMigrationVault**). Contoso place le coffre dans le groupe de ressources **ContosoFailoverRG**, dans la région primaire USA Est 2.

    ![Recovery Services - Créer le coffre](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*Besoin de plus d’aide ?*

Découvrez comment [configurer Azure pour Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Étape 4 : préparer VMware en local pour Site Recovery

Afin de préparer VMware pour Site Recovery, Contoso doit effectuer ces tâches :

- Préparer un compte sur l’instance de vCenter Server ou sur l’hôte vSphere ESXi. Le compte automatise la découverte des machines virtuelles
- Préparer un compte permettant l’installation automatique du service Mobilité sur les machines virtuelles VMware que Contoso souhaite répliquer
- Préparer les machines virtuelles locales afin qu’elles se connectent aux machines virtuelles Azure une fois celles-ci créées après le basculement


### <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique

Site Recovery doit pouvoir accéder aux serveurs VMware pour :

- Détectez automatiquement les machines virtuelles. Un compte en lecture seule est au minimum nécessaire.
- Orchestrer la réplication, le basculement et la restauration automatique. Contoso a besoin d’un compte qui peut exécuter des opérations telles que la création et la suppression de disques, ainsi que l’allumage de machines virtuelles.

Contoso configure le compte en effectuant ces tâches :

1. Crée un rôle au niveau du vCenter.
2. Attribue les autorisations requises à ce rôle.

*Besoin de plus d’aide ?*

Découvrez comment [créer et affecter un rôle pour la découverte automatique](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Préparer un compte pour l’installation du service Mobilité

Le service Mobilité doit être installé sur la machine virtuelle que Contoso souhaite répliquer. Contoso prend en compte ces facteurs concernant le service mobilité :

- Site Recovery peut effectuer une installation automatique par push de ce composant, quand Contoso active la réplication pour la machine virtuelle.
- Pour une installation automatique par push, Contoso doit préparer un compte utilisé par Site Recovery pour accéder à la machine virtuelle.
- Contoso spécifie ce compte quand ils configurent la réplication dans la console Azure.
- Contoso doit avoir un domaine ou un compte local disposant des autorisations nécessaires pour l’installation sur la machine virtuelle.

*Besoin de plus d’aide ?*

Découvrez comment [créer un compte pour une installation push du service Mobilité](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer la connexion aux machines virtuelles Azure après le basculement

Après le basculement vers Azure, Contoso souhaite pouvoir se connecter aux machines virtuelles répliquées dans Azure. Pour se connecter aux machines virtuelles répliquées dans Azure, Contoso doit effectuer certaines tâches sur la machine virtuelle locale avant la migration : 

1. Pour l’accès par le biais d’Internet, Contoso active le protocole RDP (Remote Desktop Protocol) sur la machine virtuelle locale avant le basculement. Contoso vérifie que des règles TCP et UDP sont ajoutées pour le profil **Public**, et que RDP est autorisé dans **Pare-feu Windows** > **Applications autorisées** pour tous les profils.
2. Pour l’accès par le biais d’un VPN de site à site de Contoso, Contoso active le protocole RDP sur la machine locale. Contoso autorise le protocole RDP dans **Pare-feu Windows** > **Applications et fonctionnalités autorisées** pour les réseaux **Domaine et privé**.
3. Contoso affecte la valeur **OnlineAll** à la stratégie SAN du système d’exploitation sur la machine virtuelle locale.

Contoso doit également vérifier ces éléments durant l’exécution d’un basculement :

- Aucune mise à jour de Windows ne doit être en attente sur la machine virtuelle quand un basculement est déclenché. Si des mises à jour de Windows sont en attente, Contoso ne peut pas se connecter à la machine virtuelle tant que la mise à jour n’est pas terminée.
- Après le basculement, Contoso doit consulter les **diagnostics de démarrage** pour afficher une capture d’écran de la machine virtuelle. Si Contoso ne peut pas afficher les diagnostics de démarrage, ils doivent vérifier que la machine virtuelle est en cours d’exécution, puis passer en revue les [conseils de dépannage](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure-by-using-site-recovery"></a>Étape 5 : Répliquer les machines virtuelles locales sur Azure à l’aide de Site Recovery

Avant d’opérer une migration vers Azure, Contoso doit configurer et activer la réplication pour ses machines virtuelles locales.

### <a name="set-a-replication-goal"></a>Définir un objectif de réplication

1. Dans le coffre, sous le nom du coffre (**ContosoVMVault**), Contoso définit un objectif de réplication (**Prise en main** > **Site Recovery** > **Préparer l’infrastructure**).
2. Contoso spécifie que les ordinateurs sont situés localement, qu’il s’agit de machines virtuelles VMware et qu’il faut les répliquer sur Azure.

    ![Préparer l’infrastructure - Objectif de protection](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmer la planification d’un déploiement

Pour continuer, Contoso doit confirmer que la planification du déploiement est terminée. Pour confirmer, Contoso sélectionne **Oui, je l’ai fait**. Dans ce déploiement, Contoso ne migre qu’une seule machine virtuelle. Aucune planification du déploiement n’est donc nécessaire.

### <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Maintenant, Contoso doit configurer son environnement source. Pour configurer son environnement source, Contoso télécharge un modèle OVF. Contoso utilise le modèle pour déployer le serveur de configuration et ses composants associés en tant que machine virtuelle VMware locale hautement disponible. Les composants sur le serveur sont les suivants :

- Le serveur de configuration qui coordonne les communications entre l’infrastructure locale et Azure. Le serveur de configuration gère la réplication des données.
- Le serveur de traitement qui fait office de passerelle de réplication. Le serveur de traitement :
    - Reçoit les données de réplication.
    - Optimise les données de réplication en utilisant la mise en cache, la compression et le chiffrement.
    - Envoie les données de réplication vers Stockage Azure.
- Le serveur de traitement installe également le service Mobilité sur les machines virtuelles que Contoso souhaite répliquer. Le serveur de traitement effectue la découverte automatique des machines virtuelles VMware locales.
- Une fois la machine virtuelle du serveur de configuration créée et démarrée, Contoso inscrit le serveur dans le coffre.

Pour configurer l’environnement source

1. Contoso télécharge le modèle OVF à partir du portail Azure (**Préparer l’infrastructure** > **Source** > **Serveur de configuration**).
    
    ![Ajouter un serveur de configuration](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Contoso importe le modèle dans VMware pour créer et déployer la machine virtuelle.

    ![Déployer le modèle OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Quand Contoso allume pour la première fois la machine virtuelle, elle démarre dans une expérience d’installation de Windows Server 2016. Contoso accepte le contrat de licence, puis entre un mot de passe d’administrateur.
4. Une fois l’installation terminée, Contoso se connecte à la machine virtuelle tant qu’administrateur. La première fois que Contoso se connecte à la machine virtuelle, l’outil de configuration Azure Site Recovery s’exécute automatiquement.
5. Dans l’outil de configuration Site Recovery, Contoso entre un nom à utiliser pour inscrire le serveur de configuration dans le coffre.
6. L’outil vérifie la connexion de la machine virtuelle à Azure. Une fois la connexion établie, Contoso sélectionne **Se connecter** pour se connecter à l’abonnement Azure. Les informations d’identification doivent avoir accès au coffre dans lequel le serveur de configuration est inscrit. 

    ![Inscrire le serveur de configuration](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. L’outil effectue des tâches de configuration, puis redémarre. Contoso se reconnecte à l’ordinateur. L’Assistant Gestion de serveur de configuration démarre automatiquement.
8. Dans l’Assistant, Contoso sélectionne la carte réseau qui doit recevoir le trafic de réplication. Une fois configuré, ce paramètre ne peut pas être modifié.
9. Contoso sélectionne l’abonnement, le groupe de ressources et le coffre Recovery Services dans lequel inscrire le serveur de configuration.

    ![Sélectionner le coffre Recovery Services](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. Contoso télécharge et installe MySQL Server et VMWare PowerCLI. Ensuite, Contoso valide les paramètres du serveur.
11. Après la validation, Contoso entre le nom de domaine complet (FQDN) ou l’adresse IP de l’instance de vCenter Server ou de l’hôte vSphere. Contoso conserve le port par défaut et entre un nom d’affichage pour l’instance de vCenter Server dans Azure.
12. Contoso doit spécifier le compte qu’ils ont créé précédemment afin que Site Recovery puisse découvrir automatiquement les machines virtuelles VMware disponibles pour la réplication. 
13. Contoso entre les informations d’identification afin que le service Mobilité soit installé automatiquement quand la réplication est activée. Pour des machines virtuelles Windows, le compte doit disposer d’autorisations d’administrateur local sur celles-ci. 

    ![Configurer vCenter Server](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Une fois l’inscription terminée, dans le portail Azure, Contoso revérifie que le serveur de configuration et le serveur VMware sont répertoriés dans la page **Source** dans le coffre. La détection peut prendre 15 minutes ou plus. 
8. Site Recovery se connecte aux serveurs VMware en utilisant les paramètres spécifiés. Site Recovery découvre les machines virtuelles.

### <a name="set-up-the-target"></a>Configurer la cible

Contoso doit à présent configurer l’environnement de réplication cible :

1. Dans **Préparer l’infrastructure** > **Cible**, Contoso sélectionne les paramètres cibles.
2. Site Recovery vérifie qu’il existe un compte de stockage et un réseau dans la cible spécifiée.

### <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

Une fois la source et la cible configurées, Contoso crée une stratégie de réplication et l’associe au serveur de configuration :

1. Dans **Préparer l’infrastructure** > **Paramètres de réplication** > **Stratégie de réplication** >  **Créer et associer**, Contoso crée la stratégie **ContosoMigrationPolicy**.
2. Contoso utilise les paramètres par défaut :
    - **Seuil d'objectif de point de récupération** : la valeur par défaut est 60 minutes. Cette valeur définit la fréquence à laquelle les points de récupération sont créés. Une alerte est générée lorsque la réplication continue dépasse cette limite.
    - **Rétention des points de récupération** : valeur par défaut de 24 heures. Cette valeur spécifie la durée de la fenêtre de rétention pour chaque point de récupération. Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre.
    - **Fréquence des captures instantanées de cohérence d’application** : valeur par défaut de 1 heure. Cette valeur spécifie la fréquence à laquelle les captures instantanées de cohérence d’application sont créées.
 
    ![Stratégie de réplication - Créer](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. La stratégie est automatiquement associée au serveur de configuration. 

    ![Stratégie de réplication - Associer](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*Besoin de plus d’aide ?*

- Une procédure pas à pas complète de ces étapes est décrite dans [Configurer la récupération d’urgence pour des machines virtuelles VMware locales](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Des instructions détaillées sont disponibles pour vous aider à [configurer l’environnement source](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), à [déployer le serveur de configuration](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server) et à [configurer les paramètres de réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Activer la réplication

Maintenant, Contoso peut commencer à répliquer la machine virtuelle WebVM.

1. Dans **Répliquer l’application** > **Source** > **Répliquer**, Contoso sélectionne les paramètres sources.
2. Contoso indique qu’ils souhaitent activer des machines virtuelles, ils sélectionnent l’instance de vCenter Server et ils définissent le serveur de configuration.

    ![Activer la réplication - Source](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Contoso spécifie les paramètres cibles, notamment le groupe de ressources et le réseau où la machine virtuelle Azure se trouvera après le basculement. Contoso spécifie le compte de stockage dans lequel seront stockées les données répliquées.

    ![Activer la réplication - Cible](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso sélectionne la machine virtuelle **WebVM** pour la réplication. Site Recovery installe le service Mobilité sur chaque machine virtuelle quand la réplication est activée. 

    ![Activer la réplication - Sélectionner la machine virtuelle](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso vérifie que la stratégie de réplication correcte est sélectionnée. Ensuite, ils activent la réplication pour **WEBVM**. Contoso suit la progression de la réplication dans **Travaux**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.
6. Dans le portail Azure, dans **Éléments principaux**, Contoso peut afficher la structure des machines virtuelles répliquées sur Azure :

    ![Vue de l’infrastructure](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*Besoin de plus d’aide ?*

La procédure pas à pas complète de ces étapes est décrite dans [Activer la réplication](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-by-using-the-database-migration-service"></a>Étape 6 : Migrer la base de données à l’aide de Database Migration Service

Contoso doit créer un projet Database Migration Service, puis migrer la base de données.

### <a name="create-a-database-migration-service-project"></a>Créer un projet Database Migration Service

1. Contoso crée un projet Database Migration Service. Contoso sélectionne le type de serveur source **SQL Server**. Contoso sélectionne **Azure SQL Database Managed Instance** comme cible.

     ![Database Migration Service - Nouveau projet de migration](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. L’Assistant Migration s’ouvre.

### <a name="migrate-the-database"></a>Migrer la base de données 

1. Dans l’Assistant Migration, Contoso spécifie la machine virtuelle source sur laquelle se trouve la base de données locale. Contoso entre les informations d’identification nécessaires pour accéder à la base de données.

    ![Database Migration Service - Détails de la source](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Contoso sélectionne la base de données à migrer (**SmartHotel.Registration**) :

    ![Database Migration Service - Sélectionner les bases de données sources](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Pour la cible, Contoso entre le nom de l’instance gérée dans Azure. Contoso entre les informations d’identification d’accès pour l’instance gérée.

    ![Database Migration Service - Détails de la cible](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Dans **Nouvelle activité** > **Exécuter la migration**, Contoso spécifie les paramètres d’exécution de la migration :
    - Informations d’identification de la source et de la cible.
    - La base de données à migrer.
    - Le partage réseau que Contoso a créé sur la machine virtuelle locale. Database Migration Service prend les sauvegardes sources sur ce partage. 
        - Le compte de service qui exécute l’instance source de SQL Server doit avoir des autorisations d’écriture sur ce partage réseau.
        - Le chemin du nom de domaine complet (FQDN) du partage doit être utilisé.
    - L’URI SAP qui donne à Database Migration Service l’accès au conteneur du compte de stockage dans lequel le service charge les fichiers de sauvegarde pour la migration.

        ![Database Migration Service - Configurer les paramètres de migration](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Contoso enregistre la migration, puis l’exécute.
6. Dans **Vue d’ensemble**, Contoso surveille l’état de la migration.

    ![Database Migration Service - Surveiller](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Une fois la migration terminée, Contoso vérifie que les bases de données cibles existent sur l’instance gérée.

    ![Database Migration Service - Vérifier la migration de base de données](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-by-using-site-recovery"></a>Étape 7 : Migrer la machine virtuelle à l’aide de Site Recovery

Contoso exécute un test rapide de basculement, puis migre la machine virtuelle.

### <a name="run-a-test-failover"></a>Exécuter un test de basculement

Avant de migrer la machine virtuelle WEBVM, un test de basculement permet de s’assurer que tout fonctionne comme prévu. Contoso effectue les étapes suivantes :

1. Contoso exécute un test de basculement jusqu’au dernier point dans le temps disponible (**Dernier point traité**).
2. Contoso sélectionne **Arrêter la machine avant de commencer le basculement**. Cette option étant sélectionnée, Site Recovery tente d’arrêter la machine virtuelle source avant de déclencher le basculement. Le basculement se poursuit même en cas d’échec de l’arrêt. 
3. Le test de basculement est exécuté : 
    1. Une vérification des prérequis est effectuée pour s’assurer que toutes les conditions nécessaires pour la migration sont en place.
    2. Le basculement traite les données pour permettre la création d’une machine virtuelle Azure. Si le dernier point de récupération est sélectionné, un point de récupération est créé à partir des données.
    3.  Une machine virtuelle Azure est créée en utilisant les données traitées à l’étape précédente.
3. Une fois le basculement terminé, la machine virtuelle Azure répliquée apparaît dans le portail Azure. Contoso vérifie que tout fonctionne correctement : la machine virtuelle est de la taille appropriée, elle est connectée au réseau approprié et elle est en cours d’exécution. 
4. Après avoir vérifié le test de basculement, Contoso nettoie le basculement. Ensuite, ils prennent note des éventuelles observations. 

### <a name="migrate-the-vm"></a>Migrer la machine virtuelle

1. Après avoir vérifié que le test de basculement a fonctionné comme prévu, Contoso crée un plan de récupération pour la migration. Contoso ajoute WEBVM au plan :

     ![Créer un plan de récupération - Sélectionner des éléments](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Contoso exécute un basculement sur le plan. Contoso sélectionne le dernier point de récupération. Contoso spécifie que Site Recovery doit essayer d’arrêter la machine virtuelle locale avant de déclencher le basculement.

    ![Basculement](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Après le basculement, Contoso vérifie que la machine virtuelle Azure apparaît comme prévu dans le portail Azure.

   ![Détails du plan de récupération](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Après avoir vérifié la machine virtuelle dans Azure, Contoso achève le processus de migration, arrête la réplication de la machine virtuelle, et arrête la facturation de Site Recovery pour la machine virtuelle.

    ![Basculement - Terminer la migration](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Mettre à jour la chaîne de connexion

La dernière étape du processus de migration pour Contoso consiste à mettre à jour la chaîne de connexion de l’application pour qu’elle pointe vers la base de données migrée qui s’exécute sur son instance gérée.

1. Dans le portail Azure, Contoso recherche la chaîne de connexion en sélectionnant **Paramètres** > **Chaînes de connexion**.

    ![Chaînes de connexion](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Contoso met à jour la chaîne avec le nom d’utilisateur et le mot de passe de SQL Database Managed Instance.
3. Une fois la chaîne configurée, Contoso remplace la chaîne de connexion actuelle dans le fichier web.config de son application.
4. Après avoir mis à jour et enregistré le fichier, Contoso redémarre IIS sur WEBVM. Pour redémarrer IIS, Contoso exécute `IISRESET /RESTART` dans une fenêtre d’invite de commandes.
5. Une fois IIS redémarré, l’application utilise la base de données qui s’exécute sur SQL Database Managed Instance.
6. À ce stade, Contoso peut arrêter la machine SQLVM locale. La migration est terminée.

*Besoin de plus d’aide ?*

- Découvrez comment [exécuter un test de basculement](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Découvrez comment [créer un plan de récupération](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Découvrez comment [basculer vers Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Nettoyer après la migration

Une fois la migration terminée, l’application SmartHotel s’exécute sur une machine virtuelle Azure, et la base de données SmartHotel est disponible dans Azure SQL Database Managed Instance.  

Contoso doit à présent effectuer les tâches de nettoyage suivantes :  

- Supprimer la machine virtuelle WEBVM de l’inventaire vCenter Server
- Supprimer la machine virtuelle SQLVM de l’inventaire vCenter Server
- Supprimer les machines virtuelles WEBVM et SQLVM des travaux de sauvegarde locaux.
- Mettre à jour la documentation interne afin qu’elle indique le nouvel emplacement et l’adresse IP de WEBVM
- Supprime SQLVM de la documentation interne. En guise d’alternative, Contoso peut réviser la documentation afin de signaler que SQLVM a été supprimée et ne figure plus dans l’inventaire des machines virtuelles
- Passer en revue toutes les ressources qui interagissent avec les machines virtuelles qui ont été mises hors service Mettre à jour les paramètres ou la documentation appropriés afin de refléter la nouvelle configuration

## <a name="review-the-deployment"></a>Examiner le déploiement

Les ressources ayant été migrées dans Azure, Contoso doit rendre sa nouvelle infrastructure entièrement opérationnelle et la sécuriser.

### <a name="security"></a>Sécurité

L’équipe de sécurité de Contoso examine les machines virtuelles Azure et SQL Database Managed Instance pour détecter d’éventuels problèmes de sécurité liés à son implémentation.

- L’équipe de sécurité examine les groupes de sécurité réseau qui sont utilisés pour contrôler l’accès pour la machine virtuelle. Les groupes de sécurité réseau aident à s’assurer que seul le trafic autorisé à accéder à l’application peut passer.
- L’équipe de sécurité de Contoso prend également en considération la sécurisation des données sur le disque à l’aide d’Azure Disk Encryption et d’Azure Key Vault.
- L’équipe de sécurité active la détection des menaces sur l’instance gérée. La détection des menaces envoie une alerte à l’équipe de sécurité/au système de support de Contoso afin d’ouvrir un ticket en cas de détection d’une menace. Apprenez-en davantage sur la [détection des menaces pour Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Sécurité de Managed Instance - Détection des menaces](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

Pour en savoir plus sur les pratiques de sécurité pour les machines virtuelles, consultez [Bonnes pratiques de sécurité pour les charges de travail IaaS dans Azure](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control).

### <a name="backups"></a>Sauvegardes

Contoso sauvegarde les données sur WEBVM à l’aide du service Sauvegarde Azure. Apprenez-en davantage sur [Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Gestion des licences et optimisation des coûts

- Contoso dispose déjà d’une licence pour WEBVM. Pour tirer parti de la tarification avec Azure Hybrid Benefit, Contoso convertit la machine virtuelle Azure existante.
- Contoso active Azure Cost Management sous licence de Cloudyn, une filiale de Microsoft. Cost Management est une solution de gestion des coûts multicloud qui aide Contoso à utiliser et à gérer Azure et d’autres ressources cloud. Apprenez-en davantage sur [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Conclusion

Cet article décrit comment Contoso a réhébergé l’application SmartHotel dans Azure en migrant la machine virtuelle frontend d’application vers Azure à l’aide du service Site Recovery. Contoso migre la base de données locale vers Azure SQL Database Managed Instance à l’aide d’Azure Database Migration Service.

## <a name="next-steps"></a>Étapes suivantes

Dans l’article suivant de la série, Contoso [réhéberge l’application SmartHotel sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) en utilisant le service Azure Site Recovery.

