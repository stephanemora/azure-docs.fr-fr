---
title: 'Tutoriel : Partager en dehors de votre organisation - Azure Data Share'
description: Tutoriel - Partager des données avec des clients et des partenaires via Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 8749f7dee2ceeb09e37cc97d4e5bfe76c52e2da6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438737"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Tutoriel : Partagez des données avec Azure Data Share  

Dans ce tutoriel, vous allez découvrir comment créer un partage Azure Data Share et commencer à partager vos données avec des clients et des partenaires externes à votre organisation Azure. 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un partage Data Share
> * Ajouter des jeux de données à votre partage Data Share
> * Activer une planification de synchronisation pour votre partage Data Share 
> * Ajouter des destinataires à votre partage Data Share 

## <a name="prerequisites"></a>Conditions préalables requises

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* L’adresse e-mail de connexion Azure de vos destinataires (l’utilisation de leur alias de courrier ne fonctionnera pas).

### <a name="share-from-a-storage-account"></a>Partager à partir d’un compte de stockage :

* Compte Stockage Azure : Si vous n’en avez pas déjà, vous pouvez créer un [compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Autorisation d’ajouter l’attribution de rôle au compte de stockage, qui est présente dans l’autorisation *Microsoft. Authorization/Role Assignments/Write*. Cette autorisation existe dans le rôle propriétaire. 

### <a name="share-from-a-sql-based-source"></a>Partage à partir d’une source SQL :

* Une base de données ou un entrepôt de données Azure SQL avec des tables et des vues que vous souhaitez partager.
* Autorisation pour l’accès du partage de données à l’entrepôt de données. Pour ce faire, procédez comme suit : 
    1. Définissez-vous comme administrateur Azure Active Directory pour le serveur.
    1. Connectez-vous à la base de données ou à l’entrepôt de données Azure SQL avec Azure Active Directory.
    1. Utilisez l’Éditeur de requêtes (préversion) pour exécuter le script suivant afin d’ajouter le MSI Data Share en tant que db_owner. Vous devez vous connecter avec Active Directory et non avec l’authentification SQL Server. 
    
```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```                   
Notez que *<share_acc_name>* est le nom de votre compte Data Share. Si vous n’avez pas encore créé de compte Data Share, vous pourrez le faire plus tard.  

* Un [utilisateur Azure SQL Database avec accès `db_owner`](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users) pour parcourir et sélectionner les tables et/ou vues à partager. 

* Accès au pare-feu SQL Server de l’adresse IP du client : Pour ce faire, procédez comme suit : 1. Accédez à *Pare-feux et réseaux virtuels* 1. Cliquez sur le bouton **activer** pour autoriser l’accès à Azure Services. 

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Créer un compte Data Share

Créez une ressource Azure Data Share dans un groupe de ressources Azure.

1. Cliquez sur le bouton **Créer une ressource** (+) dans le coin supérieur gauche du portail.

1. Recherchez *Data Share*.

1. Sélectionnez Data Share, puis sélectionnez **Créer**.

1. Indiquez les détails de base de votre ressource Azure Data Share à partir des informations suivantes. 

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Name | *datashareacount* | Spécifiez un nom pour votre compte de partage de données. |
    | Subscription | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser pour votre compte de partage de données.|
    | Resource group | *test-resource-group* | Utilisez un groupe de ressources existant ou créez-en un. |
    | Location | *USA Est 2* | Sélectionnez une région pour votre compte de partage de données.
    | | |

1. Sélectionnez **Créer** pour provisionner votre compte de partage de données. En règle générale, le provisionnement d’un nouveau compte de partage de données prend environ 2 minutes au maximum. 

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

## <a name="create-a-data-share"></a>Créer un partage Data Share

1. Accédez à la page de présentation de votre partage Data Share.

    ![Partager vos données](./media/share-receive-data.png "Partager vos données") 

1. Sélectionnez **Start sharing your data** (Commencer à partager vos données).

1. Sélectionnez **Create** (Créer).   

1. Indiquez les détails relatifs à votre partage Data Share. Spécifiez un nom, une description du contenu du partage et des conditions d’utilisation (facultatif). 

    ![EnterShareDetails](./media/enter-share-details.png "Entrer les détails de partage") 

1. Sélectionnez **Continue** (Continuer)

1. Pour ajouter des jeux de données à votre partage Data Share, sélectionnez **Add Datasets** (Ajouter des jeux de données). 

    ![Groupes de données](./media/datasets.png "Groupes de données")

1. Sélectionnez le type de jeu de données à ajouter. Si vous partagez à partir d’Azure SQL Database ou d’Azure SQL Data Warehouse, vous êtes invité à entrer des informations d’identification SQL. Authentifiez-vous avec l’utilisateur que vous avez créé dans le cadre des prérequis.

    ![AddDatasets](./media/add-datasets.png "Ajouter des jeux de données")    

1. Accédez à l’objet à partager, puis sélectionnez Add Datasets (Ajouter des jeux de données). 

    ![SelectDatasets](./media/select-datasets.png "Sélectionner des jeux de données")    

1. Sous l’onglet Destinataires, entrez les adresses e-mail de votre consommateur de données en sélectionnant + Add Recipient (Ajouter un destinataire). 

    ![AddRecipients](./media/add-recipient.png "Ajouter des destinataires") 

1. Sélectionnez **Continue** (Continuer)

1. Si vous souhaitez que votre consommateur de données puisse obtenir des mises à jour incrémentielles de vos données, activez la planification des captures instantanées. 

    ![EnableSnapshots](./media/enable-snapshots.png "Activer les captures instantanées") 

1. Sélectionnez une heure de début et un intervalle de périodicité. 

1. Sélectionnez **Continue** (Continuer)

1. Sous l’onglet Review + Create (Revoir + Créer), passez en revue le contenu du package, les paramètres, les destinataires ainsi que les paramètres de synchronisation. Sélectionnez **Créer**

Votre partage Azure Data Share est désormais créé. Le destinataire de votre partage Data Share est maintenant prêt à accepter votre invitation. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer un partage Azure Data Share et à inviter des destinataires. Pour découvrir comment un consommateur de données peut accepter et recevoir un partage de données, passez au tutoriel [Accepter et recevoir des données](subscribe-to-data-share.md). 
