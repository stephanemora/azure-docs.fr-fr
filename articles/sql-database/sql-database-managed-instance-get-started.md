---
title: 'Portail Azure : Créer une instance gérée SQL | Microsoft Docs'
description: Créez une instance gérée SQL, un environnement réseau et une machine virtuelle client pour l’accès.
keywords: didacticiel sql database, créer une instance gérée sql
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: quickstart
ms.date: 08/13/2018
ms.author: jovanpop-msft
ms.openlocfilehash: cb378c2d2773096992ef688653fd77b2625f8754
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42024235"
---
# <a name="create-an-azure-sql-managed-instance"></a>Créer une instance gérée SQL Azure

Ce démarrage rapide vous montre comment créer une instance gérée SQL dans Azure. Azure SQL Database Managed Instance est une instance de moteur de base de données de SQL Server PaaS (Platform-as-a-Service) qui vous permet d’exécuter et de mettre à l’échelle des bases de données SQL Server hautement disponibles dans le cloud Azure. Ce démarrage rapide vous indique comment commencer en créant une instance gérée SQL.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="prepare-network-environment"></a>Préparer un environnement réseau

Une instance gérée SQL est un service sécurisé placé dans votre propre réseau virtuel Azure (VNet). Pour créer une instance gérée, vous devez préparer l’environnement réseau Azure, comprenant :
 - Un réseau virtuel Azure où se situera votre instance gérée.
 - Un sous-réseau dans votre réseau virtuel Azure où seront placés les instances gérées.
 - Un itinéraire défini par l’utilisateur qui permettra à l’instance gérée de communiquer avec les services Azure qui contrôlent et gèrent l’instance.

Le sous-réseau est dédié aux instances gérées et vous ne pouvez pas créer d’autres ressources (par exemple, des machines virtuelles Azure) dedans. Ce guide de démarrage rapide permettra de créer deux sous-réseaux dans votre réseau virtuel Azure afin que vous puissiez placer des instances gérées dans le sous-réseau dédié aux instances gérées, et d’autres ressources dans le sous-réseau par défaut.

1. Déployez un environnement réseau Azure préparé pour Azure SQL Database Managed Instance en cliquant sur le bouton ci-dessous :

    <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener"> <img src="http://azuredeploy.net/deploybutton.png"> </a>

    Ce bouton ouvre un formulaire dans le portail Azure au sein duquel vous pouvez configurer votre environnement réseau avant de le déployer.

2. Si vous le souhaitez, modifiez les noms du réseau virtuel et des sous-réseaux et ajustez les plages d’adresses IP associées à vos ressources réseau. Appuyez ensuite sur le bouton « Acheter » pour créer et configurer votre environnement :

    ![créer un environnement d’instance gérée](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

    > [!Note]
    > Ce déploiement Azure Resource Manager créera deux sous-réseaux dans le réseau virtuel : un pour les instances gérées appelé **ManagedInstances**, et l’autre appelé **Par défaut** pour les autres ressources telle qu’une machine virtuelle client utilisable pour se connecter à une instance gérée. Si vous n’avez besoin de deux sous-réseaux, vous pouvez supprimer le sous-réseau par défaut plus tard. Toutefois, il sera alors impossible d’effectuer l’étape 3 de ce guide de démarrage rapide ([Préparer la machine client](#prepare-client-machine)).

    > [!Note]
    > Si vous modifiez les noms du réseau virtuel et du sous-réseaux, assurez-vous de vous rappeler des nouveaux noms, car vous en aurez besoin dans les sections suivantes. Dans le reste du didacticiel, il sera considéré que vous avez créé un réseau virtuel appelé **MyNewVNet**, un sous-réseau **ManagedInstances** pour les instances gérées SQL et un sous-réseau **Par défaut** pour les machines virtuelles et les autres ressources.

## <a name="create-a-managed-instance"></a>Créer une option Managed Instance

Les étapes suivantes vous montrent comment créer votre option Managed Instance après la validation de votre préversion.

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2. Recherchez **Managed Instance**, puis sélectionnez **Azure SQL Database Managed Instance (preview)** (Azure SQL Database Managed Instance (préversion)).
3. Cliquez sur **Créer**.

   ![Créer une instance gérée](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

4. Sélectionnez votre abonnement et vérifiez que les conditions d’utilisation de la préversion sont définies sur l’état **Accepté**.

   ![conditions d’utilisation de la préversion de l’option managed instance acceptées](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. Remplissez le formulaire de l’option Managed Instance avec les informations demandées, en utilisant les données du tableau suivant :

   | Paramètre| Valeur suggérée | Description |
   | ------ | --------------- | ----------- |
   |**Nom de l’instance managée**|Nom valide|Pour connaître les noms valides, consultez [Conventions d’affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Connexion administrateur de l’instance managée**|Nom d’utilisateur non valide|Pour connaître les noms valides, consultez [Conventions d’affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). N’utilisez pas « serveradmin », car il s’agit d’un rôle réservé au niveau du serveur.| 
   |**Mot de passe**|Mot de passe valide|Le mot de passe doit contenir au moins 16 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Groupe de ressources**|Le groupe de ressources que vous avez créé précédemment||
   |**Lieu**|L’emplacement que vous avez précédemment sélectionné|Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/).|
   |**Réseau virtuel**|Le réseau virtuel que vous avez créé précédemment| Choisissez un élément **MyNewVNet/ManagedInstances** si vous n’avez pas modifié les noms à l’étape précédente. Sinon, choisissez le nom du réseau virtuel et le sous-réseau ManagedInstance saisis dans la section précédente. **N’utilisez pas le sous-réseau Par défaut, car il n’est pas configuré pour héberger les instances gérées**. |

   ![formulaire de création de l’option managed instance](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. Cliquez sur **Niveau de tarification** pour dimensionner les ressources de calcul et de stockage, ainsi que pour examiner les options de niveau tarifaire. Par défaut, votre instance bénéficie de 32 Go d’espace de stockage gratuitement, **ce qui peut être insuffisant pour vos applications**.
7. Utilisez les curseurs ou zones de texte pour spécifier la quantité de stockage et le nombre de v-cores. 
   ![niveau tarifaire d’instance gérée](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

8. Lorsque vous avez terminé, cliquez sur **Appliquer** pour enregistrer votre sélection.  
9. Cliquez sur **Créer** pour déployer l’option Managed Instance.
10. Cliquez sur l’icône **Notifications** pour afficher l’état du déploiement.
 
   ![progression du déploiement](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

11. Cliquez sur **Déploiement en cours** pour ouvrir la fenêtre de l’option Managed Instance et surveiller de façon précise la progression du déploiement.
 
   ![progression du déploiement 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

Pendant le déploiement, passez à la procédure suivante.

> [!IMPORTANT]
> Pour la première instance dans un sous-réseau, la durée du déploiement est généralement plus importante que celle des instances suivantes. 24 heures peuvent parfois être nécessaires. N’annulez pas l’opération de déploiement car elle dure plus longtemps que prévu. Il s’agit d’une situation temporaire, propre au déploiement de votre première instance. Prévoyez une réduction significative du temps de déploiement peu après le début de la préversion publique. La création de la deuxième instance gérée dans le sous-réseau prendra quelques minutes.

## <a name="prepare-client-machine"></a>Préparer une machine client

Dans la mesure où l’instance gérée SQL est placée dans votre réseau virtuel privé, vous devez créer une machine virtuelle Azure avec un outil de client SQL installé comme SQL Server Management Studio ou SQL Operations Studio pour vous connecter à l’instance gérée et exécuter des requêtes.

> [!Note]
> Au lieu de la machine virtuelle client Azure, vous pouvez configurer une connexion [point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) et vous connectez à l’instance gérée à partir de votre ordinateur local.

Pour créer une machine virtuelle client avec tous les outils nécessaires, le plus simple consiste à utiliser les modèles Azure Resource Manager.

1. Cliquez sur le bouton suivant (vérifiez que vous êtes connecté au portail Azure dans un autre onglet de navigateur) :

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Dans le formulaire qui s’ouvre, entrez le nom de la machine virtuelle, le nom d’utilisateur administrateur et le mot de passe que vous utiliserez pour vous connecter à la machine virtuelle.

    ![créer une machine virtuelle client](./media/sql-database-managed-instance-get-started/create-client-sql-vm.png)

    Si vous n’avez pas modifié le nom du réseau virtuel et du sous-réseau par défaut, vous n’avez pas besoin de modifier les deux derniers paramètres. Dans le cas contraire, vous devez remplacer ces valeurs par les valeurs saisies lors de la configuration de l’environnement réseau.

3. Cliquez sur le bouton « Acheter » et la machine virtuelle Azure sera déployée dans le réseau que vous avez préparé.

4. Connectez-vous à votre machine virtuelle à l’aide de la connexion Bureau à distance et trouvez SQL Server Management Studio ou SQL Operation Studio, installés automatiquement sur la machine virtuelle.

5. Ouvrez SSMS et saisissez le **nom d’hôte** pour votre instance gérée dans la zone **Nom du serveur**, sélectionnez **Authentification SQL Server**, indiquez votre identifiant et mot de passe dans la boîte de dialogue **Se connecter au serveur**, puis cliquez sur **Se connecter**.

    ![connecter ssms](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

Après vous être connecté, vous pouvez voir votre système et vos bases de données utilisateur dans le nœud Bases de données, ainsi que divers objets dans les nœuds Sécurité, Objets serveur, Réplication, Gestion, SQL Server Agent et XEvent Profiler.

## <a name="next-steps"></a>Étapes suivantes

 - [Connecter vos applications à une instance gérée](sql-database-managed-instance-connect-app.md).
 - [Migrer vos bases de données locales vers une instance gérée](sql-database-managed-instance-migrate.md).


