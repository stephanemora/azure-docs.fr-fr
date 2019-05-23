---
title: 'Portail Azure : Créer une instance managée SQL Database | Microsoft Docs'
description: Créez une instance managée SQL Database, un environnement réseau et une machine virtuelle cliente pour l’accès.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: f54cea75e6f7866f1be41d3b0f82393af2235a0a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949871"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Démarrage rapide : Créer une instance managée Azure SQL Database

Ce guide de démarrage rapide vous montre comment créer une [instance managée](sql-database-managed-instance.md) Azure SQL Database dans le portail Azure.

> [!IMPORTANT]
> Pour connaître les limitations, consultez les [régions prises en charge](sql-database-managed-instance-resource-limits.md#supported-regions) et les [types d’abonnement pris en charge](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/).

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Créer une instance gérée

Les étapes suivantes détaillent la création d’une instance managée.

1. Sélectionnez **Créer une ressource** en haut à gauche du portail Azure.
2. Recherchez **managed instance**, puis sélectionnez **Azure SQL Managed Instance**.
3. Sélectionnez **Créer**.

   ![Créer une instance gérée](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Renseignez le formulaire de l’option **Instance managée SQL** avec les informations demandées, en utilisant les données du tableau suivant.

   | Paramètre| Valeur suggérée | Description |
   | ------ | --------------- | ----------- |
   | **Abonnement** | Votre abonnement. | Abonnement vous autorisant à créer des ressources. |
   |**Nom de l’instance managée**|Nom valide.|Pour connaître les noms valides, consultez [Conventions d’affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Connexion administrateur de l’instance managée**|N’importe quel nom d’utilisateur valide.|Pour connaître les noms valides, consultez [Conventions d’affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). N’utilisez pas « serveradmin », car il s’agit d’un rôle réservé au niveau du serveur.|
   |**Mot de passe**|Mot de passe valide.|Le mot de passe doit contenir au moins 16 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Fuseau horaire**|Fuseau horaire devant être respecté par votre instance managée.|Pour plus d’informations, consultez [Fuseaux horaires](sql-database-managed-instance-timezone.md).|
   |**Classement**|Classement à utiliser pour votre instance managée.|Si vous migrez des bases de données à partir de SQL Server, vérifiez le classement de la source avec `SELECT SERVERPROPERTY(N'Collation')` et utilisez cette valeur. Pour plus d’informations sur les classements, consultez [Définir ou changer le classement du serveur](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Lieu**|Emplacement dans lequel vous souhaitez créer l’instance managée.|Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/).|
   |**Réseau virtuel**|Sélectionnez **Créer un réseau virtuel** ou un réseau virtuel et un sous-réseau valides.| Si un réseau ou un sous-réseau n’est pas disponible, il doit être [modifié pour respecter les exigences réseau](sql-database-managed-instance-configure-vnet-subnet.md) afin de pouvoir être sélectionné comme cible de la nouvelle instance managée. Pour obtenir des informations sur les exigences liées à la configuration de l’environnement réseau d’une instance managée, consultez [Configurer un réseau virtuel pour une instance managée](sql-database-managed-instance-connectivity-architecture.md). |
   |**Activer le point de terminaison public**   |Cochez cette option pour activer le point de terminaison public.   |Pour que l’instance managée soit accessible par le biais du point de terminaison de données publiques, vous devez cocher la case **Activer le point de terminaison public**.| 
   |**Autoriser l’accès depuis**   |Sélectionnez l’une des options suivantes : <ul> <li>**Services Azure**</li> <li>**Internet**</li> <li>**Aucun accès**</li></ul>   |Le portail vous permet de configurer un groupe de sécurité avec un point de terminaison public. </br> </br> Selon votre scénario, sélectionnez l’une des options suivantes : </br> <ul> <li>Services Azure : option recommandée quand vous vous connectez à partir de Power BI ou d’un autre service multilocataire. </li> <li> Internet : utilisez cette option à des fins de test quand vous souhaitez lancer rapidement une instance managée. Son utilisation n’est pas recommandée dans les environnements de production. </li> <li> Aucun accès : cette option crée une règle de sécurité de refus. Vous devez modifier cette règle pour rendre l’instance managée accessible par le biais d’un point de terminaison public. </li> </ul> </br> Pour plus d’informations sur la sécurité du point de terminaison public, consultez [Utilisation d’Azure SQL Database Managed Instance de manière sécurisée avec un point de terminaison public](sql-database-managed-instance-public-endpoint-securely.md).|
   |**Type de connexion**|Choisissez entre le type de connexion Proxy et Redirection.|Pour plus d’informations sur les types de connexion, consultez [Stratégie de connexion Azure SQL Database](sql-database-connectivity-architecture.md#connection-policy).|
   |**Groupe de ressources**|nouveau groupe de ressources ou groupe de ressources existant.|Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom).|

   ![Formulaire d’instance managée](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Pour utiliser l’instance managée en tant que groupe de basculement d’instances secondaire, activez la case à cocher de l’option et spécifiez l’instance managée DnsAzurePartner. Cette fonctionnalité est en préversion et ne figure pas dans la capture d’écran suivante.
6. Sélectionnez **Niveau tarifaire** pour dimensionner les ressources de calcul et de stockage, ainsi que pour examiner les options de niveau tarifaire. Le niveau tarifaire Usage général avec 32 Go de mémoire et 16 vCores est la valeur par défaut.
7. Utilisez les curseurs ou zones de texte pour spécifier la quantité de stockage et le nombre de v-cores.
8. Quand vous avez terminé, sélectionnez **Appliquer** pour enregistrer votre sélection. 
9. Sélectionnez **Créer** pour déployer l’instance managée.
10. Sélectionnez l’icône **Notifications** pour afficher l’état du déploiement.

    ![Progression du déploiement de l’instance managée](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Sélectionnez **Déploiement en cours** pour ouvrir la fenêtre de l’instance managée et superviser de façon plus approfondie la progression du déploiement. 

> [!IMPORTANT]
> Pour la première instance dans un sous-réseau, la durée de déploiement est généralement plus importante que pour les instances ultérieures. N’annulez pas l’opération de déploiement car elle dure plus longtemps que prévu. La création de la deuxième instance managée dans le sous-réseau ne prend que quelques minutes.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Passer en revue les ressources et récupérer le nom complet du serveur

Une fois le déploiement terminé, passez en revue les ressources créées et récupérez le nom complet du serveur en vue d’une utilisation dans les guides de démarrage rapide ultérieurs.

1. Ouvrez le groupe de ressources de votre instance managée. Examinez les ressources créées pour vous dans le guide de démarrage rapide, à la [création d’une instance managée](#create-a-managed-instance).

   ![Ressources de l’instance managée](./media/sql-database-managed-instance-get-started/resources.png)

2. Sélectionnez la table de routage pour examiner celle définie par l’utilisateur, qui a été créée pour vous.

   ![Table de routage](./media/sql-database-managed-instance-get-started/route-table.png)

3. Dans la table de routage, passez en revue les entrées pour acheminer le trafic à partir du réseau virtuel de l’instance managée et au sein de celui-ci. Si vous créez ou configurez votre table de routage manuellement, vous devez veiller à créer ces entrées dans la table de routage.

   ![Entrée pour un sous-réseau local d’une instance managée](./media/sql-database-managed-instance-get-started/udr.png)

4. Revenez au groupe de ressources et sélectionnez le groupe de sécurité réseau pour examiner les règles de sécurité.

   ![Groupe de sécurité réseau](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Passez en revue les règles de sécurité entrantes et sortantes. Si vous avez configuré les points de terminaison publics pour votre instance managée, consultez l’article [Configurer un point de terminaison public](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) pour plus d’informations.

   ![Règles de sécurité](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Revenez au groupe de ressources et sélectionnez votre instance managée.

   ![Instance gérée](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Sous l’onglet **Vue d’ensemble**, recherchez la propriété **Hôte**. Copiez l’adresse complète de l’hôte pour l’instance managée, car vous en aurez besoin dans le prochain guide de démarrage rapide.

   ![Nom de l’hôte](./media/sql-database-managed-instance-get-started/host-name.png)

   Le nom est similaire à **nom_de_votre_machine.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment se connecter à une instance managée :
  - Pour obtenir une vue d’ensemble des options de connexion pour les applications, consultez [Connecter vos applications à une instance managée](sql-database-managed-instance-connect-app.md).
  - Pour obtenir un guide de démarrage rapide montrant comment se connecter à une instance managée à partir d’une machine virtuelle Azure, consultez [Configurer une connexion de machine virtuelle Azure](sql-database-managed-instance-configure-vm.md).
  - Pour obtenir un guide de démarrage rapide montrant comment se connecter à une instance managée à partir d’un ordinateur client local avec une connexion point à site, consultez [Configurer une connexion point à site](sql-database-managed-instance-configure-p2s.md).
- Pour restaurer une base de données SQL Server locale existante dans une instance managée : 
    - Utilisez [Azure Database Migration Service (DMS) pour la migration](../dms/tutorial-sql-server-to-managed-instance.md) pour effectuer la restauration à partir d’un fichier de sauvegarde de base de données. 
    - Utilisez la [commande T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) pour effectuer la restauration à partir d’un fichier de sauvegarde de base de données.
- Pour une supervision avancée des performances de base de données des instances managées avec des informations de dépannage intégrées, consultez [Superviser Azure SQL Database avec Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
