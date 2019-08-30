---
title: 'Didacticiel : Ajouter une instance managée SQL Database à un groupe de basculement'
description: Apprenez à configurer un groupe de basculement pour votre instance gérée Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: e4b7de3931c0d3508e5af6aa6bf85dfa18641aee
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624987"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Didacticiel : Ajouter une instance managée SQL Database à un groupe de basculement

Ajoutez une instance managée SQL Database à un groupe de basculement. Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
> - Créer une instance managée principale
> - Créez une instance managée secondaire dans le cadre d’un [groupe de basculement](sql-database-auto-failover-group.md). 
> - Test de basculement

  > [!NOTE]
  > La création d’une instance gérée peut prendre beaucoup de temps. En conséquence, ce didacticiel peut prendre plusieurs heures. Pour plus d’informations sur les délais d’approvisionnement, consultez [Opérations de gestion des instances gérées](sql-database-managed-instance.md#managed-instance-management-operations). L’utilisation de groupes de basculement avec des instances gérées est actuellement en préversion. 

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, veillez à disposer des éléments suivants : 

- Si vous n’avez pas encore d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/). 


## <a name="1----create-resource-group-and-primary-managed-instance"></a>1 - Créer un groupe de ressources et une instance managée principale
Au cours de cette étape, vous allez créer le groupe de ressources et l’instance managée principale pour votre groupe de basculement à l’aide du portail Azure. 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du Portail Azure. 
1. Saisissez `managed instance` dans la zone de recherche et sélectionnez l’option pour Azure SQL Managed instance. 
1. Sélectionnez **Créer** pour lancer la page de création de **l’instance managée SQL**. 
1. Sur la page **Créer une Azure SQL Database Managed Instance** sous l'onglet **Général**
    1. Sous **Détails du projet**, sélectionnez votre **Abonnement** dans la liste déroulante, puis choisissez de **Créer** un groupe de ressources. Saisissez un nom pour votre groupe de ressources, par exemple `myResourceGroup`. 
    1. Sous **Détails de l’instance managée**, indiquez le nom de votre instance managée et la région où vous souhaitez déployer votre instance managée. Veillez à sélectionner une région avec une [région jumelée](/azure/best-practices-availability-paired-regions). Laissez **Calcul + Stockage** aux valeurs par défaut. 
    1. Sous **Compte administrateur**, fournissez un identifiant d’administrateur, comme `azureuser`, et un mot de passe d’administrateur complexe. 

    ![Créer une instance managée principale](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Laissez le reste des paramètres aux valeurs par défaut, puis sélectionnez **Vérifier + créer** pour passer en revue les paramètres de votre instance managée. 
1. Sélectionnez **Créer** pour créer votre instance managée principale. 


## <a name="2---create-a-virtual-network"></a>2 - Créer un réseau virtuel
Dans cette étape, vous allez créer un réseau virtuel pour l’instance managée secondaire. Cette étape est nécessaire car le sous-réseau des instances managées principales et secondaires a des plages d’adresses qui ne se chevauchent pas. 

Pour vérifier la plage du sous-réseau de votre réseau virtuel principal, procédez comme suit :
1. Dans le [portail Azure](https://portal.azure.com), accédez à votre groupe de ressources et sélectionnez le réseau virtuel pour votre instance principale. 
1. Sélectionnez **Sous-réseaux** sous **Paramètres** et notez la **Plage d’adresses**. La plage d’adresses du sous-réseau du réseau virtuel pour l’instance managée secondaire ne peut pas la chevaucher. 


   ![Sous-réseau principal](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Pour créer un réseau virtuel, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** et recherchez *réseau virtuel*. 
1. Sélectionnez l'option **Réseau virtuel** publiée par Microsoft, puis sélectionnez **Créer** sur la page suivante. 
1. Renseignez les champs requis pour configurer le réseau virtuel de votre instance managée secondaire, puis sélectionnez **Créer**. 

   Le tableau suivant montre les valeurs nécessaires pour le réseau virtuel secondaire :

    | **Champ** | Valeur |
    | --- | --- |
    | **Nom** |  Le nom du réseau virtuel qui sera utilisé par l’instance gérée secondaire, par exemple `vnet-sql-mi-secondary`. |
    | **Espace d’adressage** | L’espace d’adressage pour votre réseau virtuel, par exemple `10.128.0.0/16`. | 
    | **Abonnement** | L’abonnement dans lequel votre instance managée principale et le groupe de ressources se trouvent. |
    | **Région** | L’emplacement auquel vous allez déployer votre instance managée secondaire ; il doit se trouver dans une [région jumelée](/azure/best-practices-availability-paired-regions) à l’instance managée principale.  |
    | **Sous-réseau** | Le nom de votre sous-réseau. `default` est fourni pour vous par défaut. |
    | **Plage d’adresses**| La plage d’adresses de votre sous-réseau. Celle-ci doit être différente de la plage d’adresses de sous-réseau utilisée par le réseau virtuel de votre instance managée principale, comme `10.128.0.0/24`.  |
    | &nbsp; | &nbsp; |

    ![Valeurs de réseau virtuel secondaire](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3 - Créer une instance managée secondaire
Lors de cette étape, vous allez créer une instance gérée secondaire dans le portail Azure, ce qui configurera également la mise en réseau entre les deux instances managées. 

Votre deuxième instance gérée doit :
- Être vide. 
- Se trouver dans une [région jumelée](/azure/best-practices-availability-paired-regions) avec son instance gérée principale équivalente. 
- Avoir un sous-réseau et une plage d’adresses IP différents de ceux de l’instance managée principale. 

Pour créer votre instance managée secondaire, procédez comme suit : 

1. Dans le [portail Azure](http://portal.azure.com), sélectionnez **Créer une ressource**, puis recherchez *Azure SQL Managed Instance*. 
1. Sélectionnez l'option **Azure SQL Managed Instance** publiée par Microsoft, puis sélectionnez **Créer** sur la page suivante.
1. Dans l'onglet **Général** de la page **Créer une Azure SQL Database Managed Instance**, renseignez les champs requis pour configurer votre instance managée secondaire. 

   Le tableau suivant montre les valeurs nécessaires pour l’instance managée secondaire :
 
    | **Champ** | Valeur |
    | --- | --- |
    | **Abonnement** |  L’abonnement dans lequel votre instance managée principale se trouve. |
    | **Groupe de ressources**| Le groupe de ressources où se trouve votre instance managée principale. |
    | **Nom de l’instance managée** | Le nom de votre nouvelle instance managée secondaire, par exemple `sql-mi-secondary`  | 
    | **Région**| L’emplacement de la [région jumelée](/azure/best-practices-availability-paired-regions) pour votre instance managée secondaire.  |
    | **Connexion administrateur de l’instance managée** | L’identifiant que vous souhaitez utiliser pour votre nouvelle instance managée secondaire, par exemple `azureuser`. |
    | **Mot de passe** | Un mot de passe complexe qui sera utilisé par la connexion de l’administrateur à la nouvelle instance gérée secondaire.  |
    | &nbsp; | &nbsp; |

1. Sous l'onglet **Mise en réseau**, pour le **Réseau virtuel**, sélectionnez le réseau virtuel que vous avez créé pour l’instance managée secondaire dans la liste déroulante.

   ![Réseau MI secondaire](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Sous l'onglet **Paramètres supplémentaires**, pour **Géoréplication**, choisissez **Oui** pour _Utiliser pour le basculement secondaire_. Sélectionnez l’instance managée principale dans la liste déroulante. 
    1. Assurez-vous que le classement et le fuseau horaire correspondent à ceux de l’instance managée principale. L’instance managée principale créée dans ce didacticiel utilisait le classement `SQL_Latin1_General_CP1_CI_AS` par défaut et le fuseau horaire `(UTC) Coordinated Universal Time`. 

   ![Réseau MI secondaire](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Sélectionnez **Vérifier + créer** pour passer en revue les paramètres de votre instance managée secondaire. 
1. Sélectionnez **Créer** pour créer votre instance managée secondaire. 


## <a name="4---create-primary-virtual-network-gateway"></a>4 - Créer une passerelle de réseau virtuel principal 

Pour que deux instances gérées participent à un groupe de basculement, une passerelle doit être configurée entre les réseaux virtuels des deux instances managées pour permettre la communication réseau. Vous pouvez créer la passerelle pour l’instance managée principale à l’aide du portail Azure :

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre groupe de ressources et sélectionnez la ressource **Réseau virtuel** pour votre instance managée principale. 
1. Sélectionnez **Sous-réseaux** sous **Paramètres**, puis choisissez d’ajouter un nouveau **Sous-réseau de passerelle**. Laissez les valeurs par défaut. 

   ![Ajouter une passerelle pour l’instance managée principale](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Une fois la passerelle de sous-réseau créée, sélectionnez **Créer une ressource** dans le volet de navigation gauche, puis saisissez `Virtual network gateway` dans la zone de recherche. Sélectionnez la ressource de **Passerelle de réseau virtuel** publiée par **Microsoft**. 

   ![Créer une passerelle de réseau virtuel](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Renseignez les champs requis pour configurer la passerelle de votre instance managée principale. 

   Le tableau suivant montre les valeurs nécessaires pour la passerelle de l’instance managée principale :
 
    | **Champ** | Valeur |
    | --- | --- |
    | **Abonnement** |  L’abonnement dans lequel votre instance managée principale se trouve. |
    | **Nom** | Le nom de votre passerelle de réseau virtuel, par exemple `primary-mi-gateway`. | 
    | **Région** | La région dans laquelle votre instance managée secondaire se trouve. |
    | **Type de passerelle** | Sélectionnez **VPN**. |
    | **Type de VPN** | Sélectionnez **Route-based** |
    | **Référence (SKU)**| Laissez la valeur `VpnGw1` par défaut. |
    | **Lieu**| L’emplacement où se trouve votre instance managée principale et votre réseau virtuel principal.   |
    | **Réseau virtuel**| Sélectionnez le réseau virtuel créé dans la section 2, par exemple `vnet-sql-mi-primary`. |
    | **Adresse IP publique**| Sélectionnez **Créer nouveau**. |
    | **Nom de l’adresse IP publique**| Entrez un nom pour votre adresse IP, par exemple `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |
1. Laissez les autres valeurs par défaut, puis sélectionnez **Vérifier + créer** pour passer en revue les paramètres de votre passerelle de réseau virtuel.

   ![Paramètres de la passerelle principale](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Sélectionnez **Créer** pour créer votre passerelle de réseau virtuel. 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5 - Configurer la passerelle de réseau virtuel secondaire 

Répétez les étapes de la section précédente pour créer le sous-réseau et la passerelle du réseau virtuel pour l’instance managée secondaire. Renseignez les champs requis pour configurer la passerelle de votre instance managée secondaire. 

   Le tableau suivant montre les valeurs nécessaires pour la passerelle de l’instance managée secondaire :

   | **Champ** | Valeur |
   | --- | --- |
   | **Abonnement** |  L’abonnement dans lequel votre instance managée secondaire se trouve. |
   | **Nom** | Le nom de votre passerelle de réseau virtuel, par exemple `secondary-mi-gateway`. | 
   | **Région** | La région dans laquelle votre instance managée secondaire se trouve. |
   | **Type de passerelle** | Sélectionnez **VPN**. |
   | **Type de VPN** | Sélectionnez **Route-based** |
   | **Référence (SKU)**| Laissez la valeur `VpnGw1` par défaut. |
   | **Lieu**| L’emplacement où se trouve votre instance gérée secondaire et votre réseau virtuel secondaire.   |
   | **Réseau virtuel**| Sélectionnez le réseau virtuel créé dans la section 2, par exemple `vnet-sql-mi-secondary`. |
   | **Adresse IP publique**| Sélectionnez **Créer nouveau**. |
   | **Nom de l’adresse IP publique**| Entrez un nom pour votre adresse IP, par exemple `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Paramètres de la passerelle secondaire](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6 : Connecter les passerelles
Dans cette étape, créez une connexion entre les passerelles. Une connexion doit être établie du serveur principal à la passerelle secondaire, puis une connexion distincte doit être établie entre les passerelles secondaire et principale. Veillez à utiliser la même **clé partagée** lors de la configuration de la connectivité entre les deux passerelles. 

Pour configurer la connectivité, procédez comme suit :

1. Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez la passerelle principale que vous avez créée à l’étape 4. 
1. Sélectionnez **Connexions** sous **Paramètres**, puis sélectionnez **Ajouter** pour créer une nouvelle connexion. 

   ![Ajouter une connexion à la passerelle principale](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Entrez un nom pour votre connexion, par exemple `Primary-connection`, et saisissez une valeur pour la **clé partagée**, comme `mi1mi2psk`. 
1. Sélectionnez la **deuxième passerelle de réseau virtuel**, puis sélectionnez la passerelle pour l’instance managée secondaire, par exemple `secondary-mi-gateway`. 

   ![Créer une connexion entre les passerelles principale et secondaire](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Sélectionnez **OK** pour ajouter votre nouvelle connexion entre les passerelles principale et secondaire.
1. Répétez ces étapes pour créer une connexion de la passerelle de l’instance managée secondaire à la passerelle de l’instance managée principale. 

   ![Créer la connexion de la passerelle secondaire à principale](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7 - Créer un groupe de basculement
Au cours de cette étape, vous allez créer le groupe de basculement et y ajouter des instances managées. 

1. Dans la [portail Azure](https://portal.azure.com), accédez à **Tous les services** et saisissez `managed instance` dans la zone de recherche. 
1. (Facultatif) Sélectionnez l’étoile en face **d’Instances managées SQL** pour ajouter des instances managées en tant que raccourci à la barre de navigation de gauche. 
1. Sélectionnez **Instances managées SQL** et sélectionnez votre instance managée principale, par exemple `sql-mi-primary`. 
1. Sous **Paramètres**, accédez à **Groupes de basculement d’instance**, puis choisissez **Ajouter un groupe** pour ouvrir la page **Groupe de basculement d’instance**. 

   ![Ajouter un groupe de basculement](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. Dans la page **Groupe de basculement d’instance**, saisissez le nom de votre groupe de  basculement, par exemple `failovergrouptutorial`, puis  choisissez l’instance gérée secondaire, par exemple `sql-mi-secondary`, dans la liste déroulante. Sélectionnez **Créer** pour créer votre groupe de basculement. 

   ![Créer un groupe de basculement](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Une fois le déploiement du groupe de basculement terminé, vous serez redirigé vers la page **Groupe de basculement**. 

## <a name="8---test-failover"></a>8 - Tester le basculement
Dans cette étape, vous allez faire basculer votre groupe de basculement sur le serveur secondaire, puis effectuer une restauration automatique en utilisant le portail Azure. 

1. Accédez à votre instance managée dans le [portail Azure](https://portal.azure.com) et sélectionnez **Groupes de basculement d’instance** sous paramètres. 
1. Vérifiez quelle instance managée est la principale et laquelle est la secondaire. 
1. Sélectionnez **Basculement**, puis cliquez sur **Oui** dans l’avertissement concernant les sessions TDS sur le point d’être déconnectées. 

   ![Basculer le groupe de basculement](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Vérifiez quelle instance est la principale et laquelle est la secondaire. Si le basculement a réussi, les deux instances doivent avoir échangé leur rôle. 

   ![Les instances managées ont échangé leurs rôles après le basculement](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Sélectionnez de nouveau **Basculement** pour faire basculer à nouveau l’instance principale vers le rôle principal. 


## <a name="clean-up-resources"></a>Supprimer des ressources
Nettoyez les ressources en supprimant d’abord l’instance managée, puis le cluster virtuel, puis les ressources restantes et enfin le groupe de ressources. 

1. Accédez à votre groupe de ressources sur le [portail Azure](https://portal.azure.com). 
1. Sélectionnez l’instance managée, puis choisissez **Supprimer**. Saisissez `yes` dans la zone de texte pour confirmer que vous souhaitez supprimer la ressource, puis sélectionnez **Supprimer**. Ce processus peut prendre un certain temps en arrière-plan et tant qu’il n’est pas terminé, vous ne pourrez pas supprimer le *cluster virtuel* ou d’autres ressources dépendantes. Surveillez la suppression dans l’onglet Activité pour confirmer que votre instance managée a été supprimée. 
1. Une fois l’instance managée supprimée, supprimez le *cluster virtuel* en le sélectionnant dans votre groupe de ressources, puis en choisissant **Supprimer**. Saisissez `yes` dans la zone de texte pour confirmer que vous souhaitez supprimer la ressource, puis sélectionnez **Supprimer**. 
1. Supprimez toutes les ressources restantes. Saisissez `yes` dans la zone de texte pour confirmer que vous souhaitez supprimer la ressource, puis sélectionnez **Supprimer**. 
1. Supprimez le groupe de ressources en sélectionnant **Supprimer le groupe de ressources**, saisissez le nom du groupe de ressources, `myResourceGroup`, puis sélectionnez **Supprimer**. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un groupe de basculement entre deux instances managées. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> - Créer une instance managée principale
> - Créez une instance managée secondaire dans le cadre d’un [groupe de basculement](sql-database-auto-failover-group.md). 
> - Test de basculement

Passez au démarrage rapide suivant pour savoir comment vous connecter à votre instance managée et comment restaurer une base de données sur votre instance managée : 

> [!div class="nextstepaction"]
> [Se connecter à votre instance managée](sql-database-managed-instance-configure-vm.md)
> [Restaurer une base de données sur une instance managée](sql-database-managed-instance-get-started-restore.md)


