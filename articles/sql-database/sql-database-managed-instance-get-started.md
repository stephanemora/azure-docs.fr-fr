---
title: 'Portail Azure : Créer une instance managée SQL Database | Microsoft Docs'
description: Créez une instance managée SQL Database, un environnement réseau et une machine virtuelle cliente pour l’accès.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 9eae757642c2a833b60c0c22a8984e83909e772c
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300810"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Démarrage rapide : Créer une instance managée Azure SQL Database

Ce guide de démarrage rapide vous montre comment créer une [instance managée](sql-database-managed-instance.md) Azure SQL Database dans le portail Azure.

> [!IMPORTANT]
> Pour connaître les limitations, consultez les [régions prises en charge](sql-database-managed-instance-resource-limits.md#supported-regions) et les [types d’abonnement pris en charge](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/).

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Créer une instance gérée

Les étapes suivantes vous montrent comment créer une instance managée :

1. Sélectionnez **Azure SQL** dans le menu de gauche du portail Azure. Si **Azure SQL** ne figure pas dans la liste, sélectionnez **Tous les services**, puis entrez **Azure SQL** dans la zone de recherche.
2. Sélectionnez **+Ajouter** pour ouvrir la page **Sélectionner l’option de déploiement SQL**. Vous pouvez voir des informations supplémentaires sur Azure SQL Database Managed Instance en sélectionnant **Afficher les détails** sur la vignette **Instances managées**.
3. Sélectionnez **Create** (Créer).

   ![Créer une instance gérée](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Utilisez les onglets du formulaire de provisionnement **Créer une instance managée Azure SQL Database** pour ajouter les informations obligatoires et facultatives. Les sections suivantes décrivent ces onglets.

### <a name="basics"></a>Concepts de base

Renseignez les informations nécessaires sous l’onglet **De base** en utilisant le tableau suivant. Il s’agit d’un ensemble minimal d’informations pour provisionner une instance managée.

![Onglet « De base » pour créer une instance managée](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

| Paramètre| Valeur suggérée | Description |
| ------ | --------------- | ----------- |
| **Abonnement** | Votre abonnement. | Abonnement vous autorisant à créer des ressources. |
| **Groupe de ressources** | nouveau groupe de ressources ou groupe de ressources existant.|Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom).|
| **Nom de l’instance managée** | Nom valide.|Pour connaître les noms valides, consultez [Conventions d’affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
| **Région** |Région dans laquelle vous voulez créer l’instance managée.|Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/).|
| **Connexion administrateur de l’instance managée** | N’importe quel nom d’utilisateur valide. | Pour connaître les noms valides, consultez [Conventions d’affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). N’utilisez pas « serveradmin », car il s’agit d’un rôle réservé au niveau du serveur.|
| **Mot de passe** | Mot de passe valide.| Le mot de passe doit contenir au moins 16 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

Sélectionnez **Configurer l’instance managée** pour dimensionner les ressources de calcul et de stockage, et pour examiner les niveaux tarifaires. Utilisez les curseurs ou zones de texte pour spécifier la quantité de stockage et le nombre de v-cores. Quand vous avez terminé, sélectionnez **Appliquer** pour enregistrer votre sélection. 

![Formulaire d’instance managée](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

Pour passer en revue vos choix avant de créer une instance managée, vous pouvez sélectionner **Vérifier + créer**. Vous pouvez aussi configurer les options de réseau en sélectionnant **Suivant : Mise en réseau**.

### <a name="networking"></a>Mise en réseau

Renseignez les informations facultatives sous l’onglet **Mise en réseau** en utilisant le tableau suivant. Si vous omettez ces informations, le portail applique les paramètres par défaut.

![Onglet « Mise en réseau » pour créer une instance managée](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

| Paramètre| Valeur suggérée | Description |
| ------ | --------------- | ----------- |
| **Réseau virtuel** | Sélectionnez **Créer un réseau virtuel** ou un réseau virtuel et un sous-réseau valides.| Si un réseau ou un sous-réseau n’est pas disponible, il doit être [modifié pour respecter les exigences réseau](sql-database-managed-instance-configure-vnet-subnet.md) afin de pouvoir être sélectionné comme cible de la nouvelle instance managée. Pour obtenir des informations sur les exigences liées à la configuration de l’environnement réseau d’une instance managée, consultez [Configurer un réseau virtuel pour une instance managée](sql-database-managed-instance-connectivity-architecture.md). |
| **Type de connexion** | Choisissez entre le type de connexion de proxy et de redirection.|Pour plus d’informations sur les types de connexion, consultez [Stratégie de connexion Azure SQL Database](sql-database-connectivity-architecture.md#connection-policy).|
| **Point de terminaison public**  | Sélectionnez **Activer**. | Pour qu’une instance managée soit accessible via le point de terminaison de données public, vous devez activer cette option. | 
| **Autoriser l’accès depuis** (si **Point de terminaison public** est activé) | Sélectionnez l'une des options.   |L’expérience du portail vous permet de configurer un groupe de sécurité avec un point de terminaison public. </br> </br> Selon votre scénario, sélectionnez l’une des options suivantes : </br> <ul> <li>**Services Azure** : Nous vous recommandons d’utiliser cette option quand vous vous connectez depuis Power BI ou d’un autre service multilocataire. </li> <li> **Internet** : Utilisez cette option à des fins de test quand vous voulez lancer rapidement une instance managée. Son utilisation n’est pas recommandée pour les environnements de production. </li> <li> **Pas d’accès** : Cette option crée une règle de sécurité **Refuser**. Modifiez cette règle pour rendre une instance managée accessible via un point de terminaison public. </li> </ul> </br> Pour plus d’informations sur la sécurité du point de terminaison public, consultez [Utilisation d’Azure SQL Database Managed Instance de manière sécurisée avec un point de terminaison public](sql-database-managed-instance-public-endpoint-securely.md).|

Sélectionnez **Vérifier + créer** pour passer en revue vos choix avant de créer une instance managée. Vous pouvez aussi configurer d’autres paramètres personnalisés en sélectionnant **Suivant : Paramètres supplémentaires**.

### <a name="additional-settings"></a>Paramètres supplémentaires

Renseignez les informations facultatives sous l’onglet **Paramètres supplémentaires** en utilisant le tableau suivant. Si vous omettez ces informations, le portail applique les paramètres par défaut.

![Onglet « Paramètres supplémentaires » pour créer une instance managée](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

| Paramètre| Valeur suggérée | Description |
| ------ | --------------- | ----------- |
| **Classement** | Choisissez le classement à utiliser pour votre instance managée. Si vous migrez des bases de données à partir de SQL Server, vérifiez le classement de la source avec `SELECT SERVERPROPERTY(N'Collation')` et utilisez cette valeur.| Pour plus d’informations sur les classements, consultez [Définir ou changer le classement du serveur](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
| **Fuseau horaire** | Sélectionnez le fuseau horaire que votre instance managée doit utiliser.|Pour plus d’informations, consultez [Fuseaux horaires](sql-database-managed-instance-timezone.md).|
| **Utiliser comme basculement secondaire** | Sélectionnez **Oui**. | Activez cette option pour utiliser l’instance managée comme groupe de basculement secondaire.|
| **Instance managée principale** (si **Utiliser comme basculement secondaire** est défini sur **Oui**) | Choisissez une instance managée principale existante qui sera jointe à la même zone DNS avec l’instance managée que vous créez. | Cette étape permet de configurer le groupe de basculement après sa création. Pour plus d’informations, consultez [Tutoriel : Ajouter une instance managée SQL Database à un groupe de basculement](sql-database-managed-instance-failover-group-tutorial.md).|

### <a name="review--create"></a>Vérifier + créer

1. Sélectionnez **Vérifier + créer** pour passer en revue vos choix avant de créer l’instance managée.

   ![Onglet pour vérifier et créer une instance managée](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

1. Sélectionnez **Créer** pour commencer le provisionnement de l’instance managée.

> [!IMPORTANT]
> Le déploiement d’une instance managée est une opération longue. Le déploiement de la première instance dans le sous-réseau prend généralement plus de temps que le déploiement dans un sous-réseau avec des instances managées existantes. Pour plus d’informations sur les temps de provisionnement moyens, consultez [Opérations de gestion des instances managées](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Superviser la progression du déploiement

1. Sélectionnez l’icône **Notifications** pour afficher l’état du déploiement.

    ![Progression du déploiement d’une instance managée](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

1. Sélectionnez **Déploiement en cours** dans la notification pour ouvrir la fenêtre de l’instance managée et pour superviser de façon plus approfondie la progression du déploiement. 

> [!TIP]
> Si vous avez fermé votre navigateur web ou quitté l’écran de progression du déploiement, suivez ces étapes pour trouver cet écran :
> 1. Dans le portail Azure, ouvrez le groupe de ressources (sous l’onglet **De base**) sur lequel vous déployez une instance managée.
> 2. Sélectionnez **Déploiements**.
> 3. Sélectionnez l’opération de déploiement de l’instance managée en cours.

## <a name="review-resources-and-retrieve-your-host-name"></a>Examiner les ressources et récupérer votre nom d’hôte

Une fois le déploiement réussi :

1. Ouvrez le groupe de ressources de votre instance managée. Examinez ses ressources créées pour vous dans le guide de démarrage rapide [Créer une instance managée](#create-a-managed-instance).

   ![Ressources de l’instance managée](./media/sql-database-managed-instance-get-started/resources.png)

2. Sélectionnez la table de routage pour examiner la route définie par l’utilisateur qui a été créée pour vous.

   ![Table de routage](./media/sql-database-managed-instance-get-started/route-table.png)

3. Dans la table de routage, passez en revue les entrées pour router le trafic à partir du réseau virtuel de l’instance managée et au sein de celui-ci. Si vous créez ou que vous configurez votre table de routage manuellement, veillez à créer ces entrées dans la table de routage.

   ![Entrée pour un sous-réseau local d’une instance managée](./media/sql-database-managed-instance-get-started/udr.png)

4. Revenez au groupe de ressources et sélectionnez le groupe de sécurité réseau.

   ![Groupe de sécurité réseau](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Passez en revue les règles de sécurité entrantes et sortantes. Si vous avez configuré des points de terminaison publics pour votre instance managée, consultez l’article [Configurer un point de terminaison public](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) pour plus d’informations.

   ![Règles de sécurité](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Revenez au groupe de ressources et sélectionnez votre instance managée.

   ![Instance managée dans le groupe de ressources](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Sous l’onglet **Vue d’ensemble**, recherchez la propriété **Hôte**. Copiez le nom d’hôte de l’instance managée pour l’utiliser dans le guide de démarrage rapide suivant.

   ![Nom de l’hôte](./media/sql-database-managed-instance-get-started/host-name.png)

   Le nom d’hôte est également appelé nom de domaine complet. Il est similaire à *nom_de_votre_machine.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment se connecter à une instance managée :
- Pour obtenir une vue d’ensemble des options de connexion pour les applications, consultez [Connecter vos applications à une instance managée](sql-database-managed-instance-connect-app.md).
- Pour obtenir un guide de démarrage rapide montrant comment se connecter à une instance managée à partir d’une machine virtuelle Azure, consultez [Configurer une connexion de machine virtuelle Azure](sql-database-managed-instance-configure-vm.md).
- Pour obtenir un guide de démarrage rapide montrant comment se connecter à une instance managée à partir d’un ordinateur client local avec une connexion point à site, consultez [Configurer une connexion point à site](sql-database-managed-instance-configure-p2s.md).

Pour restaurer une base de données SQL Server locale existante dans une instance managée : 
- Utilisez [Azure Database Migration Service pour la migration](../dms/tutorial-sql-server-to-managed-instance.md) pour effectuer la restauration à partir d’un fichier de sauvegarde de base de données. 
- Utilisez la [commande T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) pour effectuer la restauration à partir d’un fichier de sauvegarde de base de données.

Pour une supervision avancée des performances de base de données des instances managées avec des informations de dépannage intégrées, consultez [Superviser Azure SQL Database avec Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
