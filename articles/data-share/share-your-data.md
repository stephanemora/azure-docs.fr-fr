---
title: 'Tutoriel : Partager en dehors de votre organisation - Azure Data Share'
description: Tutoriel - Partager des données avec des clients et des partenaires via Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/30/2020
ms.openlocfilehash: 1de793dc2f4f72efb67c954e60262c3d7f1b74fc
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511971"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Tutoriel : Partagez des données avec Azure Data Share  

Dans ce tutoriel, vous allez découvrir comment créer un partage Azure Data Share et commencer à partager vos données avec des clients et des partenaires externes à votre organisation Azure. 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un partage Data Share
> * Ajouter des jeux de données à votre partage Data Share
> * Activer une planification d’instantanés pour votre partage Data Share. 
> * Ajouter des destinataires à votre partage Data Share 

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* L’adresse e-mail de connexion Azure de vos destinataires (l’utilisation de leur alias de courrier ne fonctionnera pas).
* Si le magasin de données Azure source se trouve dans un autre abonnement Azure que celui que vous utiliserez pour créer une ressource de partage de données, inscrivez le [fournisseur de ressources Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) dans l’abonnement où se trouve le magasin de données Azure. 

### <a name="share-from-a-storage-account"></a>Partager à partir d’un compte de stockage :

* Compte Stockage Azure : Si vous n’en avez pas déjà, vous pouvez créer un [compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Autorisation d’écrire dans le compte de stockage, qui est présent dans *Microsoft.Storage/storageAccounts/write*. Cette autorisation existe dans le rôle Contributeur.
* Autorisation d’ajouter l’attribution de rôle au compte de stockage, qui est présente dans *Microsoft.Authorization/role assignments/write*. Cette autorisation existe dans le rôle Propriétaire. 


### <a name="share-from-a-sql-based-source"></a>Partage à partir d’une source SQL :

* Une base de données Azure SQL ou un entrepôt de données Azure Synapse Analytics (antérieurement Azure SQL Data Warehouse) avec des tables et des vues que vous voulez partager.
* Autorisation d’écrire dans les bases de données sur SQL Server, qui est présente dans *Microsoft.Sql/servers/databases/write*. Cette autorisation existe dans le rôle Contributeur.
* Autorisation pour l’accès du partage de données à l’entrepôt de données. Pour ce faire, procédez comme suit : 
    1. Définissez-vous comme administrateur Azure Active Directory pour le serveur SQL Server.
    1. Connectez-vous à la base de données ou à l’entrepôt de données Azure SQL avec Azure Active Directory.
    1. Utilisez l’Éditeur de requêtes (préversion) pour exécuter le script suivant afin d’ajouter l’identité managée de la ressource Data Share en tant que db_datareader. Vous devez vous connecter avec Active Directory et non avec l’authentification SQL Server. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Notez que *<share_acc_name>* est le nom de votre ressource Data Share. Si vous n’avez pas encore créé de ressource Data Share, vous pourrez le faire plus tard.  

* Un utilisateur Azure SQL Database avec un accès « db_datareader » pour parcourir et sélectionner les tables et/ou les vues que vous voulez partager. 

* Accès au pare-feu SQL Server de l’adresse IP du client. Pour ce faire, procédez comme suit : 
    1. Dans SQL Server, dans le portail Azure, accédez à *Pare-feux et réseaux virtuels*
    1. Cliquez sur le bouton **activer** pour autoriser l’accès à Azure Services.
    1. Cliquez sur **+ Ajouter une adresse IP de client**, puis cliquez sur **Enregistrer**. Cette adresse IP est susceptible d’être modifiée. Il peut être nécessaire de répéter ce processus la prochaine fois que vous partagerez des données SQL à partir du portail Azure. Vous pouvez également ajouter une plage d’adresses IP. 

### <a name="share-from-azure-data-explorer"></a>Partager depuis Azure Data Explorer
* Un cluster Azure Data Explorer avec des bases de données que vous voulez partager.
* Autorisation d’écrire sur le cluster Azure Data Explorer, qui est présente dans *Microsoft.Kusto/clusters/write*. Cette autorisation existe dans le rôle Contributeur.
* Autorisation d’ajouter l’attribution de rôle au cluster Azure Data Explorer, qui est présente dans *Microsoft.Authorization/role assignments/write*. Cette autorisation existe dans le rôle Propriétaire.

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
    | Nom | *datashareacount* | Spécifiez un nom pour votre compte de partage de données. |
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser pour votre compte de partage de données.|
    | Resource group | *test-resource-group* | Utilisez un groupe de ressources existant ou créez-en un. |
    | Emplacement | *USA Est 2* | Sélectionnez une région pour votre compte de partage de données.
    | | |

1. Sélectionnez **Créer** pour provisionner votre compte de partage de données. En règle générale, le provisionnement d’un nouveau compte de partage de données prend environ 2 minutes au maximum. 

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

## <a name="create-a-data-share"></a>Créer un partage Data Share

1. Accédez à la page de présentation de votre partage Data Share.

    ![Partager vos données](./media/share-receive-data.png "Partager vos données") 

1. Sélectionnez **Start sharing your data** (Commencer à partager vos données).

1. Sélectionnez **Create** (Créer).   

1. Indiquez les détails relatifs à votre partage Data Share. Spécifiez un nom, un type de partage, une description du contenu du partage et des conditions d’utilisation (facultatif). 

    ![EnterShareDetails](./media/enter-share-details.png "Entrer les détails de partage") 

1. Sélectionnez **Continue** (Continuer)

1. Pour ajouter des jeux de données à votre partage Data Share, sélectionnez **Add Datasets** (Ajouter des jeux de données). 

    ![Groupes de données](./media/datasets.png "Groupes de données")

1. Sélectionnez le type de jeu de données à ajouter. Vous verrez une liste différente de types de jeux de données en fonction du type de partage (instantané ou sur place) que vous avez sélectionné à l’étape précédente. Si vous partagez à partir d’Azure SQL Database ou d’Azure SQL Data Warehouse, vous êtes invité à entrer des informations d’identification SQL. Authentifiez-vous avec l’utilisateur que vous avez créé dans le cadre des prérequis.

    ![AddDatasets](./media/add-datasets.png "Ajouter des jeux de données")    

1. Accédez à l’objet à partager, puis sélectionnez Add Datasets (Ajouter des jeux de données). 

    ![SelectDatasets](./media/select-datasets.png "Sélectionner des jeux de données")    

1. Sous l’onglet Destinataires, entrez les adresses e-mail de votre consommateur de données en sélectionnant + Add Recipient (Ajouter un destinataire). 

    ![AddRecipients](./media/add-recipient.png "Ajouter des destinataires") 

1. Sélectionnez **Continue** (Continuer)

1. Si vous avez sélectionné le type de partage d’instantané, vous pouvez configurer la planification d’instantanés pour fournir des mises à jour de vos données à votre consommateur de données. 

    ![EnableSnapshots](./media/enable-snapshots.png "Activer les captures instantanées") 

1. Sélectionnez une heure de début et un intervalle de périodicité. 

1. Sélectionnez **Continue** (Continuer)

1. Sous l’onglet Review + Create (Revoir + Créer), passez en revue le contenu du package, les paramètres, les destinataires ainsi que les paramètres de synchronisation. Sélectionnez **Créer**

Votre partage Azure Data Share est désormais créé. Le destinataire de votre partage Data Share est maintenant prêt à accepter votre invitation. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer un partage Azure Data Share et à inviter des destinataires. Pour découvrir comment un consommateur de données peut accepter et recevoir un partage de données, passez au tutoriel [Accepter et recevoir des données](subscribe-to-data-share.md). 
