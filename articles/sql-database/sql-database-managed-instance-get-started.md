---
title: 'Portail Azure : Créer une instance SQL Database Managed Instance | Microsoft Docs'
description: Créez une instance gérée SQL, un environnement réseau et une machine virtuelle client pour l’accès.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: Carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 201ba431a4382741815536db2bb4d08f0068be80
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329539"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Démarrage rapide : Créer une instance Azure SQL Database Managed Instance

Ce guide de démarrage rapide vous montre comment créer une instance Azure SQL Database [Managed Instance](sql-database-managed-instance.md) dans le portail Azure.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Créer une option Managed Instance

La procédure suivante explique comment créer une instance Managed Instance.

1. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du Portail Azure.
2. Recherchez **Managed Instance**, puis sélectionnez **Azure SQL Managed Instance**.
3. Sélectionnez **Créer**.

   ![Créer une instance gérée](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Remplissez le formulaire de l’option **Managed Instance** avec les informations demandées, en utilisant les données du tableau suivant :

   | Paramètre| Valeur suggérée | Description |
   | ------ | --------------- | ----------- |
   | **Abonnement** | Votre abonnement | Abonnement dans lequel vous êtes autorisé à créer des ressources. |
   |**Nom de l’instance managée**|Nom valide|Pour connaître les noms valides, consultez [Conventions d’affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Connexion administrateur de l’instance managée**|Nom d’utilisateur non valide|Pour connaître les noms valides, consultez [Conventions d’affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). N’utilisez pas « serveradmin », car il s’agit d’un rôle réservé au niveau du serveur.|
   |**Mot de passe**|Mot de passe valide|Le mot de passe doit contenir au moins 16 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Classement**|Classement à utiliser pour votre instance Managed Instance|Pour plus d’informations sur les classements, consultez [Classements](https://docs.microsoft.com/sql/t-sql/statements/collations).|
   |**Lieu**|Emplacement dans lequel vous souhaitez créer l’instance Managed Instance.|Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/).|
   |**Réseau virtuel**|Sélectionnez **Créer un réseau virtuel** ou un réseau virtuel et un sous-réseau valides.| Si un réseau/sous-réseau est grisé, il doit être [modifié pour respecter la configuration réseau requise](sql-database-managed-instance-configure-vnet-subnet.md) avant de pouvoir le sélectionner en tant que cible de la nouvelle instance Managed Instance. Pour obtenir des informations sur les exigences liées à la configuration de l’environnement réseau pour une instance Managed Instance, consultez [Configurer un réseau virtuel pour Azure SQL Database Managed Instance](sql-database-managed-instance-connectivity-architecture.md). |
   |**Groupe de ressources**|Nouveau groupe de ressources ou groupe de ressources existant.|Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom).|

   ![formulaire de l’instance Managed Instance](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Pour utiliser Managed Instance en tant que Groupe de basculement d’instances secondaire, sélectionnez l’extraction et spécifiez l’instance managée DnsAzurePartner. Cette fonctionnalité est en préversion et ne figure pas dans la capture d’écran qui accompagne cet article.
6. Sélectionnez **Niveau tarifaire** pour dimensionner les ressources de calcul et de stockage, ainsi que pour examiner les options de niveau tarifaire. Le niveau tarifaire Usage général avec 32 Go de mémoire et 16 vCores est la valeur par défaut.
7. Utilisez les curseurs ou zones de texte pour spécifier la quantité de stockage et le nombre de v-cores.
8. Lorsque vous avez terminé, choisissez **Appliquer** pour enregistrer votre sélection.  
9. Sélectionnez **Créer** pour déployer Managed Instance.
10. Sélectionnez l’icône **Notifications** pour afficher l’état du déploiement.

    ![progression du déploiement de Managed Instance](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Sélectionnez **Déploiement en cours** pour ouvrir la fenêtre de l’option Managed Instance et surveiller la progression du déploiement.

> [!IMPORTANT]
> Pour la première instance dans un sous-réseau, la durée de déploiement est généralement plus importante que pour les instances ultérieures. N’annulez pas l’opération de déploiement car elle dure plus longtemps que prévu. La création de la deuxième instance Managed Instance dans le sous-réseau ne prend que quelques minutes.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Passer en revue les ressources et récupérer le nom complet du serveur

Une fois le déploiement terminé, passez en revue les ressources créées et récupérez le nom complet du serveur en vue d’une utilisation dans les guides de démarrage rapide ultérieurs.

1. Ouvrez le groupe de ressources de votre instance Managed Instance et examinez les ressources créés pour vous dans le guide de démarrage rapide [Créer une instance gérée](#create-a-managed-instance).

2. Sélectionnez votre instance Managed Instance.

   ![Ressources Managed Instance](./media/sql-database-managed-instance-get-started/resources.png)

3. Sous l’onglet **Vue d’ensemble**, recherchez la propriété **Hôte** et copiez l’adresse complète de l’hôte pour l’instance Managed Instance.

   ![Ressources Managed Instance](./media/sql-database-managed-instance-get-started/host-name.png)

   Le nom est similaire à **nom_de_votre_machine.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment se connecter à une instance Managed Instance, consultez :
  - Pour obtenir une vue d’ensemble des options de connexion pour les applications, consultez [Connecter vos applications à Managed Instance](sql-database-managed-instance-connect-app.md).
  - Pour obtenir un guide de démarrage rapide montrant comment se connecter à une instance Managed Instance à partir d’une machine virtuelle Azure, consultez [Configurer une connexion de machine virtuelle Azure](sql-database-managed-instance-configure-vm.md).
  - Pour obtenir un guide de démarrage rapide montrant comment se connecter à une instance Managed Instance à partir d’un ordinateur client local à l’aide d’une connexion point à site, consultez [Configurer une connexion point à site](sql-database-managed-instance-configure-p2s.md).
- Pour restaurer une base de données SQL Server locale existante vers une instance Managed Instance, vous pouvez utiliser [Azure Database Migration Service (DMS) pour la migration](../dms/tutorial-sql-server-to-managed-instance.md) ou la [commande T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) afin d’effectuer la restauration à partir d’un fichier de sauvegarde de base de données.
- Pour une supervision avancée des performances d’une base de données Managed Instance avec des informations de dépannage intégrées, consultez [Surveiller Azure SQL Database avec Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)
