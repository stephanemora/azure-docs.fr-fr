---
title: 'Démarrage rapide : Créer une instance managée SQL Azure (portail)'
description: Ce guide de démarrage rapide explique comment créer, à l’aide du portail Azure, une instance managée, un environnement réseau et une machine virtuelle cliente pour l’accès.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 1/29/2021
ms.openlocfilehash: 77b43cfe781499c408f1952aed532ddb12c621e5
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082340"
---
# <a name="quickstart-create-an-azure-sql-managed-instance"></a>Démarrage rapide : Créer une instance gérée SQL Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ce guide de démarrage rapide explique comment créer une [instance managée SQL Azure](sql-managed-instance-paas-overview.md) dans le portail Azure.

> [!IMPORTANT]
> Pour connaître les limitations, consultez les [régions prises en charge](resource-limits.md#supported-regions) et les [types d’abonnement pris en charge](resource-limits.md#supported-subscription-types).

## <a name="create-an-azure-sql-managed-instance"></a>Créer une instance gérée SQL Azure

Pour créer une instance managée SQL, effectuez les étapes suivantes : 

### <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/).

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Sélectionnez **Azure SQL** dans le menu de gauche du portail Azure. Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis entrez **Azure SQL** dans la zone de recherche.
1. Sélectionnez **+Ajouter** pour ouvrir la page **Sélectionner l’option de déploiement SQL**. Vous pouvez consulter des informations supplémentaires sur Azure SQL Managed Instance en sélectionnant **Afficher les détails** sur la vignette **Instances managées SQL**.
1. Sélectionnez **Create** (Créer).

   ![Créer une instance gérée](./media/instance-create-quickstart/create-azure-sql-managed-instance.png)

4. Utilisez les onglets du formulaire de provisionnement **Créer une instance managée SQL Azure** pour ajouter les informations obligatoires et facultatives. Les sections suivantes décrivent ces onglets.

### <a name="basics-tab"></a>Onglet Informations de base

- Renseignez les informations obligatoires sous l’onglet **Informations de base**. Il s’agit d’informations de base qui sont obligatoires pour provisionner une instance managée SQL.

   ![Onglet « De base » pour la création d’une instance managée SQL](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-basics.png)

   Référez-vous au tableau ci-dessous pour connaître les informations à renseigner sous cet onglet.

   | Paramètre| Valeur suggérée | Description |
   | ------ | --------------- | ----------- |
   | **Abonnement** | Votre abonnement. | Abonnement vous autorisant à créer des ressources. |
   | **Groupe de ressources** | nouveau groupe de ressources ou groupe de ressources existant.|Pour les noms de groupe de ressources valides, consultez [Naming conventions](/azure/architecture/best-practices/resource-naming) (Conventions d’affectation de nom).|
   | **Nom de l’instance managée** | Nom valide.|Pour connaître les noms valides, consultez [Conventions d’affectation de noms](/azure/architecture/best-practices/resource-naming).|
   | **Région** |Région dans laquelle vous voulez créer l’instance managée.|Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/).|
   | **Connexion administrateur de l’instance managée** | N’importe quel nom d’utilisateur valide. | Pour connaître les noms valides, consultez [Conventions d’affectation de noms](/azure/architecture/best-practices/resource-naming). N’utilisez pas « serveradmin », car il s’agit d’un rôle réservé au niveau du serveur.|
   | **Mot de passe** | Mot de passe valide.| Le mot de passe doit contenir au moins 16 caractères et satisfaire aux [exigences de complexité définies](../../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-).|

- Sélectionnez **Configurer l’instance managée** pour dimensionner les ressources de calcul et de stockage, et pour examiner les niveaux tarifaires. Utilisez les curseurs ou zones de texte pour spécifier la quantité de stockage et le nombre de v-cores. Quand vous avez terminé, sélectionnez **Appliquer** pour enregistrer votre sélection. 

   ![Formulaire d’instance managée](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-configure-performance.png)

| Paramètre| Valeur suggérée | Description |
| ------ | --------------- | ----------- |
| **Niveau de service** | Sélectionnez l'une des options. | Selon votre scénario, sélectionnez l’une des options suivantes : </br> <ul><li>**Usage général** : option par défaut pour la plupart des charges de travail de production.</li><li>**Critique pour l’entreprise** : option conçue pour les charges de travail à faible latence avec une haute résilience aux défaillances et des basculements rapides.</li></ul><BR>Pour plus d’informations, consultez [Niveaux de service Azure SQL Database et Azure SQL Managed Instance](../../azure-sql/database/service-tiers-general-purpose-business-critical.md) ainsi que [Vue d’ensemble des limites de ressources Azure SQL Managed Instance](../../azure-sql/managed-instance/resource-limits.md).|
| **Génération du matériel** | Sélectionnez l'une des options. | La génération du matériel définit généralement les limites de calcul et de mémoire, ainsi que d’autres caractéristiques qui ont un impact sur les performances de la charge de travail. **Gen5** est l’option par défaut.|
| **Modèle de calcul vCore** | Sélectionnez une option. | Les vCores représentent la quantité exacte de ressources de calcul qui sont toujours provisionnées pour votre charge de travail. La valeur par défaut est **Huit vCores**.|
| **Stockage en Go** | Sélectionnez une option. | Taille de stockage en Go : sélectionnez une valeur en fonction de la taille de données attendue. Si vous migrez des données existantes à partir d’un emplacement local ou sur différentes plateformes cloud, consultez [Vue d’ensemble de la migration : de SQL Server vers SQL Managed Instance](../../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-overview.md).|
| **Azure Hybrid Benefit** | Sélectionnez l’option, le cas échéant. | Permet de bénéficier d’une licence Azure existante. Pour plus d’informations, consultez [Azure Hybrid Benefit – Azure SQL Database et SQL Managed Instance](../../azure-sql/azure-hybrid-benefit.md). |
| **Redondance du stockage de sauvegarde** | Sélectionnez **Stockage de sauvegarde géoredondant**. | Redondance de stockage dans Azure pour le stockage de sauvegarde. Notez que cette valeur ne sera pas modifiable. Le stockage de sauvegarde géoredondant est l’option par défaut. Elle est recommandée, même si la redondance de zone et la redondance locale offrent une plus grande flexibilité en matière de coûts et la possibilité de résidence des données dans une région unique. Pour plus d’informations, consultez [Redondance du stockage de sauvegarde](../database/automated-backups-overview.md?tabs=managed-instance#backup-storage-redundancy).|


- Pour passer en revue vos choix avant de créer une instance managée SQL, vous pouvez sélectionner **Vérifier + créer**. Vous pouvez aussi configurer les options de réseau en sélectionnant **Suivant : Mise en réseau**.

### <a name="networking-tab"></a>Onglet Réseau

- Renseignez les informations facultatives sous l’onglet **Réseau**. Si vous omettez ces informations, le portail applique les paramètres par défaut.

   ![Onglet « Mise en réseau » pour créer une instance managée](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-networking.png)

   Référez-vous au tableau ci-dessous pour connaître les informations à renseigner sous cet onglet.

   | Paramètre| Valeur suggérée | Description |
   | ------ | --------------- | ----------- |
   | **Réseau virtuel** | Sélectionnez **Créer un réseau virtuel** ou un réseau virtuel et un sous-réseau valides.| Si un réseau ou un sous-réseau n’est pas disponible, il doit être [modifié pour respecter les exigences réseau](vnet-existing-add-subnet.md) afin de pouvoir être sélectionné comme cible de la nouvelle instance managée. Pour obtenir des informations sur les exigences liées à la configuration de l’environnement réseau pour SQL Managed Instance, consultez [Configurer un réseau virtuel pour SQL Managed Instance](connectivity-architecture-overview.md). |
   | **Type de connexion** | Choisissez entre le type de connexion de proxy et de redirection.|Pour plus d’informations sur les types de connexion, consultez [Type de connexion Azure SQL Managed Instance](../database/connectivity-architecture.md#connection-policy).|
   | **Point de terminaison public**  | Sélectionnez **Désactiver**. | Pour qu’une instance managée soit accessible via le point de terminaison de données public, vous devez activer cette option. | 
   | **Autoriser l’accès depuis** (si **Point de terminaison public** est activé) | Sélectionnez **Aucun accès**  |L’expérience du portail vous permet de configurer un groupe de sécurité avec un point de terminaison public. </br> </br> Selon votre scénario, sélectionnez l’une des options suivantes : </br> <ul> <li>**Services Azure** : Nous vous recommandons d’utiliser cette option quand vous vous connectez depuis Power BI ou d’un autre service multilocataire. </li> <li> **Internet** : Utilisez cette option à des fins de test quand vous voulez lancer rapidement une instance managée. Son utilisation n’est pas recommandée pour les environnements de production. </li> <li> **Pas d’accès** : Cette option crée une règle de sécurité **Refuser**. Modifiez cette règle pour rendre une instance managée accessible via un point de terminaison public. </li> </ul> </br> Pour plus d’informations sur la sécurité du point de terminaison public, consultez [Utilisation d’Azure SQL Managed Instance de manière sécurisée avec un point de terminaison public](public-endpoint-overview.md).|

- Sélectionnez **Vérifier + créer** pour passer en revue vos choix avant de créer une instance managée. Vous pouvez aussi configurer d’autres paramètres personnalisés en sélectionnant **Suivant : Paramètres supplémentaires**.


### <a name="additional-settings"></a>Paramètres supplémentaires

- Renseignez les informations facultatives sous l’onglet **Paramètres supplémentaires**. Si vous omettez ces informations, le portail applique les paramètres par défaut.

   ![Onglet « Paramètres supplémentaires » pour créer une instance managée](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-additional-settings.png)

   Référez-vous au tableau ci-dessous pour connaître les informations à renseigner sous cet onglet.

   | Paramètre| Valeur suggérée | Description |
   | ------ | --------------- | ----------- |
   | **Classement** | Choisissez le classement à utiliser pour votre instance managée. Si vous migrez des bases de données à partir de SQL Server, vérifiez le classement de la source avec `SELECT SERVERPROPERTY(N'Collation')` et utilisez cette valeur.| Pour plus d’informations sur les classements, consultez [Définir ou changer le classement du serveur](/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Fuseau horaire** | Sélectionnez le fuseau horaire que l’instance managée doit utiliser.|Pour plus d’informations, consultez [Fuseaux horaires](timezones-overview.md).|
   | **Utiliser comme basculement secondaire** | Sélectionnez **Oui**. | Activez cette option pour utiliser l’instance managée comme groupe de basculement secondaire.|
   | **Instance managée SQL principale** (si **Utiliser comme basculement secondaire** est défini sur **Oui**) | Choisissez une instance managée principale existante qui sera jointe à la même zone DNS avec l’instance managée que vous créez. | Cette étape permet de configurer le groupe de basculement après sa création. Pour plus d’informations, consultez [Didacticiel : Ajoutez une instance managée à un groupe de basculement](failover-group-add-instance-tutorial.md).|

- Sélectionnez **Vérifier + créer** pour passer en revue vos choix avant de créer une instance managée. Ou configurez des étiquettes Azure en sélectionnant **Suivant : Étiquettes** (recommandé).

### <a name="tags"></a>Balises

- Ajoutez des étiquettes aux ressources dans votre modèle Azure Resource Manager (modèle ARM). Les [étiquettes](../../azure-resource-manager/management/tag-resources.md) vous aident à organiser logiquement vos ressources. Les valeurs des étiquettes sont affichées dans les rapports sur les coûts et permettent d’autres activités de gestion par étiquette. 

- Il est recommandé de définir au moins deux étiquettes pour votre nouvelle instance managée SQL : l’étiquette de propriétaire (Owner) pour identifier la personne qui a créé l’instance, et l’étiquette d’environnement (Environment) pour identifier s’il s’agit d’un système de production, de développement, etc. Pour plus d’informations, consultez [Développer une stratégie de nommage et de catégorisation des ressources Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
 
- Sélectionnez **Vérifier + créer** pour continuer.

## <a name="review--create"></a>Vérifier + créer

1. Sélectionnez l’onglet **Vérifier + créer** pour passer en revue vos choix avant de créer une instance managée.

   ![Onglet pour vérifier et créer une instance managée](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-review-create.png)

1. Sélectionnez **Créer** pour commencer le provisionnement de l’instance managée.

> [!IMPORTANT]
> Le déploiement d’une instance managée est une opération longue. Le déploiement de la première instance dans le sous-réseau prend généralement plus de temps que le déploiement dans un sous-réseau avec des instances managées existantes. Pour plus d’informations sur les temps de provisionnement moyens, consultez [Vue d’ensemble des opérations de gestion Azure SQL Managed Instance](management-operations-overview.md#duration).

## <a name="monitor-deployment-progress"></a>Superviser la progression du déploiement

1. Sélectionnez l’icône **Notifications** pour afficher l’état du déploiement.

   ![Progression du déploiement d’une instance managée SQL](./media/instance-create-quickstart/azure-sql-managed-instance-create-deployment-in-progress.png)

1. Sélectionnez **Déploiement en cours** dans la notification pour ouvrir la fenêtre SQL Managed Instance et pour superviser de façon plus approfondie la progression du déploiement. 

> [!TIP]
> - Si vous avez fermé votre navigateur web ou quitté l’écran de progression du déploiement, vous pouvez superviser l’opération de provisionnement via la page **Vue d’ensemble** de l’instance managée, ou à l’aide de PowerShell ou d’Azure CLI. Pour plus d’informations, consultez [Superviser des opérations](management-operations-monitor.md#monitor-operations). 
> - Vous pouvez annuler le processus de provisionnement via le portail Azure ou bien à l’aide de PowerShell, d’Azure CLI ou de tout autre outil basé sur l’API REST. Consultez [Annulation d’opérations de gestion Azure SQL Managed Instance](management-operations-cancel.md).

> [!IMPORTANT]
> - Le début de la création d’une instance managée SQL peut être retardé quand d’autres opérations conséquentes sont exécutées, par exemple de longues opérations de restauration ou de mise à l’échelle sur d’autres instances managées du même sous-réseau. Pour en savoir plus, consultez [Impact sur les opérations de gestion](management-operations-overview.md#management-operations-cross-impact).
> - Pour connaître l’état de la création d’une instance managée, vous devez disposer d’**autorisations de lecture** pour le groupe de ressources. Si vous ne disposez pas de cette autorisation ou si vous la révoquez pendant la création de l’instance managée, SQL Managed Instance peut ne pas figurer dans la liste des déploiements de groupes de ressources.
>

## <a name="view-resources-created"></a>Afficher les ressources créées

Lorsque le déploiement d’une instance managée a réussi, pour voir les ressources créées :

1. Ouvrez le groupe de ressources de votre instance managée. 

   ![Ressources de l’instance managée SQL](./media/instance-create-quickstart/azure-sql-managed-instance-resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Consulter et ajuster les paramètres réseau

Pour ajuster les paramètres réseau, examinez les éléments suivants :

1. Dans la liste des ressources, sélectionnez la table de routage pour examiner la table des routes définies par l’utilisateur (UDR) qui a été créée.

2. Dans la table de routage, passez en revue les entrées pour router le trafic à partir et au sein du réseau virtuel SQL Managed Instance. Si vous créez ou configurez votre table de routage manuellement, créez ces entrées dans la table de routage de l’instance managée SQL.

   ![Entrée pour un sous-réseau local d’une instance managée SQL](./media/instance-create-quickstart/azure-sql-managed-instance-route-table-user-defined-route.png)

    Pour modifier ou ajouter des routes, ouvrez **Routes** dans les paramètres de la table de routage.

3. Revenez au groupe de ressources et sélectionnez le groupe de sécurité réseau (NSG) qui a été créé.

4. Passez en revue les règles de sécurité entrantes et sortantes. 

   ![Règles de sécurité](./media/instance-create-quickstart/azure-sql-managed-instance-security-rules.png)

    Pour modifier ou ajouter des règles, ouvrez **Règles de sécurité de trafic entrant** et **Règles de sécurité de trafic sortant** dans les paramètres du groupe de sécurité réseau.

> [!IMPORTANT]
> Si vous avez configuré un point de terminaison public pour SQL Managed Instance, vous devez ouvrir les ports pour autoriser le trafic réseau en autorisant les connexions à SQL Managed Instance à partir de l’Internet public. Pour plus d’informations, consultez [Configurer un point de terminaison public pour SQL Managed Instance](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Récupérer les informations concernant la connexion à l’instance managée SQL

Pour vous connecter à SQL Managed Instance, effectuez les étapes suivantes afin de récupérer le nom d’hôte et le nom de domaine complet (FQDN) :

1. Revenez au groupe de ressources et sélectionnez l’instance managée SQL qui a été créée.

2. Sous l’onglet **Vue d’ensemble**, recherchez la propriété **Hôte**. Cliquez sur le bouton **Copier dans le Presse-papiers** pour copier le nom d’hôte dans le Presse-papiers de l’instance managée, car vous en aurez besoin dans le prochain guide de démarrage rapide.

   ![Nom de l’hôte](./media/instance-create-quickstart/azure-sql-managed-instance-host-name.png)

   La valeur copiée représente un nom de domaine complet (FQDN) qui peut être utilisé pour se connecter à l’instance managée SQL. Il est similaire à l’exemple d’adresse suivant : *votre_nom_d’hôte.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment se connecter à SQL Managed Instance :
- Pour obtenir une vue d'ensemble des options de connexion des applications, consultez [Connecter vos applications à SQL Managed Instance](connect-application-instance.md).
- Pour suivre un guide de démarrage rapide montrant comment se connecter à SQL Managed Instance à partir d’une machine virtuelle Azure, consultez [Configurer une connexion de machine virtuelle Azure](connect-vm-instance-configure.md).
- Pour suivre un guide de démarrage rapide montrant comment se connecter à SQL Managed Instance à partir d’un ordinateur client local avec une connexion point à site, consultez [Configurer une connexion point à site](point-to-site-p2s-configure.md).

Pour restaurer une base de données SQL Server locale existante dans SQL Managed Instance : 
- Utilisez [Azure Database Migration Service pour la migration](../../dms/tutorial-sql-server-to-managed-instance.md) pour effectuer la restauration à partir d’un fichier de sauvegarde de base de données. 
- Utilisez la [commande T-SQL RESTORE](restore-sample-database-quickstart.md) pour effectuer la restauration à partir d’un fichier de sauvegarde de base de données.

Pour une supervision avancée des performances de base de données SQL Managed Instance avec des informations de dépannage intégrées, consultez [Superviser Azure SQL Managed Instance avec Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).