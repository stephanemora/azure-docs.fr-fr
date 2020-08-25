---
title: 'Tutoriel : Accepter et recevoir des données - Azure Data Share'
description: Didacticiel - Accepter et recevoir des données avec Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 08/14/2020
ms.openlocfilehash: 409f143ce67e301e3b2a973d8d2db80380fbd50e
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258628"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Tutoriel : Accepter et recevoir des données avec Azure Data Share  

Dans ce didacticiel, vous allez apprendre à accepter une invitation de partage de données à l’aide d’Azure Data Share. Vous allez apprendre à recevoir les données qui ont été partagées avec vous et à activer un intervalle d’actualisation régulier pour avoir la certitude de toujours disposer de la capture instantanée la plus récente des données partagées. 

> [!div class="checklist"]
> * Guide pratique pour accepter une invitation Azure Data Share
> * Créer un compte Azure Data Share
> * Spécifier une destination pour vos données
> * Créer un abonnement à votre partage de données pour l’actualisation planifiée

## <a name="prerequisites"></a>Prérequis
Avant de pouvoir accepter une invitation de partage de données, vous devez provisionner un certain nombre de ressources Azure listées ci-dessous. 

Vérifiez que tous les prérequis sont remplis avant d’accepter une invitation de partage de données. 

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Invitation Data Share : Invitation de Microsoft Azure dont l’objet est « Invitation Azure Data Share de **<yourdataprovider@domain.com>**  ».
* Inscrivez le [fournisseur de ressources Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) dans l’abonnement Azure où vous allez créer une ressource de partage de données et dans l’abonnement Azure où se trouvent vos magasins de données Azure cibles.

### <a name="receive-data-into-a-storage-account"></a>Recevoir des données dans un compte de stockage : 

* Compte Stockage Azure : Si vous n’en avez pas déjà, vous pouvez créer un [compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Autorisation d’écrire dans le compte de stockage, qui est présent dans *Microsoft.Storage/storageAccounts/write*. Cette autorisation existe dans le rôle Contributeur. 
* Autorisation d’ajouter l’attribution de rôle au compte de stockage, qui est présente dans *Microsoft.Authorization/role assignments/write*. Cette autorisation existe dans le rôle Propriétaire.  

### <a name="receive-data-into-a-sql-based-source"></a>Recevoir des données dans une source SQL :

* Autorisation d’écrire dans les bases de données sur le serveur SQL Server, qui est présente dans *Microsoft.Sql/servers/databases/write*. Cette autorisation existe dans le rôle Contributeur. 
* Autorisation permettant à l’identité managée de la ressource de partage de données d’accéder à la base de données Azure SQL ou à l’entrepôt de données Azure SQL. Pour ce faire, procédez comme suit : 
    1. Définissez-vous comme administrateur Azure Active Directory pour le serveur SQL Server.
    1. Connectez-vous à la base de données ou à l’entrepôt de données Azure SQL avec Azure Active Directory.
    1. Utilisez l’Éditeur de requêtes (préversion) pour exécuter le script suivant afin d’ajouter l’identité managée Data Share en tant que « db_datareader, db_datawriter, db_ddladmin ». Vous devez vous connecter avec Active Directory et non avec l’authentification SQL Server. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Notez que *<share_acc_name>* est le nom de votre ressource Data Share. Si vous n’avez pas encore créé de ressource Data Share, vous pourrez le faire plus tard.         

* Accès au pare-feu SQL Server de l’adresse IP du client. Pour ce faire, procédez comme suit : 
    1. Dans SQL Server, dans le portail Azure, accédez à *Pare-feux et réseaux virtuels*
    1. Cliquez sur le bouton **activer** pour autoriser l’accès à Azure Services.
    1. Cliquez sur **+ Ajouter une adresse IP de client**, puis cliquez sur **Enregistrer**. Cette adresse IP est susceptible d’être modifiée. Il peut être nécessaire de répéter ce processus la prochaine fois que vous recevrez des données dans une cible SQL à partir du portail Azure. Vous pouvez également ajouter une plage d’adresses IP. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Recevez des données dans un cluster Azure Data Explorer : 

* Un cluster Azure Data Explorer dans le même centre de données Azure que le cluster Data Explorer du fournisseur de données : Si vous n’en avez pas déjà un, vous pouvez créer un [cluster Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal). Si vous ne connaissez pas le centre de données Azure du cluster du fournisseur de données, vous pouvez créer le cluster plus tard dans le processus.
* Autorisation d’écrire dans le cluster Azure Data Explorer, qui est présente dans *Microsoft.Kusto/clusters/write*. Cette autorisation existe dans le rôle Contributeur. 
* Autorisation d’ajouter l’attribution de rôle au cluster Azure Data Explorer, qui est présente dans *Microsoft.Authorization/role assignments/write*. Cette autorisation existe dans le rôle Propriétaire. 

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Ouvrir l’invitation

1. Vous pouvez ouvrir une invitation à partir d’un e-mail, ou directement à partir du portail Azure. 

   Pour ouvrir une invitation à partir d’un e-mail, recherchez dans votre boîte de réception une invitation émanant de votre fournisseur de données. L’invitation provient de Microsoft Azure et s’intitule **Invitation Azure Data Share de <yourdataprovider@domain.com>** . Cliquez sur **Afficher l’invitation** pour voir votre invitation dans Azure. 

   Pour ouvrir l’invitation directement à partir du portail Azure, recherchez **Invitations Azure Data Share** dans le portail Azure. Vous accédez à la liste des invitations Data Share.

   ![Invitations](./media/invitations.png "Liste des invitations") 

1. Sélectionnez le partage que vous souhaitez voir. 

## <a name="accept-invitation"></a>Accepter l’invitation
1. Veillez à passer en revue tous les champs, notamment les **Conditions d’utilisation**. Si vous acceptez les conditions d’utilisation, vous devez cocher la case indiquant que vous donnez votre accord. 

   ![Conditions d’utilisation](./media/terms-of-use.png "Conditions d’utilisation") 

1. Sous *Target Data Share Account* (Compte Data Share cible), sélectionnez l’abonnement et le groupe de ressources à utiliser pour le déploiement de votre partage de données. 

   Pour le champ **Data Share Account** (Compte Data Share), sélectionnez **Create new** (Créer) si vous n’avez pas de compte Data Share existant. Sinon, sélectionnez un compte Data Share existant dans lequel vous souhaitez accepter votre partage de données. 

   Pour le champ **Nom de partage reçu**, vous pouvez conserver la valeur par défaut spécifiée par le fournisseur de données ou spécifier le nouveau nom du partage reçu. 

   Une fois que vous avez accepté les conditions d’utilisation et spécifié un compte Data Share pour gérer votre partage reçu, sélectionnez **Accepter et configurer**. Un abonnement de partage est créé. 

   ![Options d’acceptation](./media/accept-options.png "Options d’acceptation") 

   Vous accédez alors au partage reçu dans votre compte Data Share. 

   Si vous ne souhaitez pas accepter l’invitation, sélectionnez *Reject* (Rejeter). 

## <a name="configure-received-share"></a>Configurer un partage reçu
Suivez les étapes ci-dessous pour configurer l’emplacement où vous souhaitez recevoir les données.

1. Sélectionnez l’onglet **Jeux de données**. Cochez la case en regard du jeu de données auquel vous souhaitez affecter une destination. Sélectionnez **+ Mapper à la cible** pour choisir un magasin de données cible. 

   ![Mapper sur cible](./media/dataset-map-target.png "Mapper sur cible") 

1. Sélectionnez un type de magasin de données cible dans lequel vous souhaitez que les données arrivent. Tous les fichiers de données ou les tables dans le magasin de données cible ayant le même chemin et le même nom seront remplacés. 

   Pour les partages sur place, sélectionnez un magasin de données à l’emplacement spécifié. L’emplacement est le centre de données Azure où se trouve le magasin de données source du fournisseur de données. Une fois le jeu de données mappé, vous pouvez suivre le lien dans le chemin cible pour accéder aux données.

   ![Compte de stockage cible](./media/dataset-map-target-sql.png "Stockage cible") 

1. Pour le partage basé sur un instantané, si le fournisseur de données a créé une planification d’instantané pour fournir une mise à jour régulière des données, vous pouvez également activer la planification des instantanés en sélectionnant l’onglet **Planification d’instantanés**. Cochez la case en regard de la planification d’instantané et sélectionnez **+ Activer**.

   ![Activer la planification d’instantané](./media/enable-snapshot-schedule.png "Activer la planification d’instantané")

## <a name="trigger-a-snapshot"></a>Déclencher une capture instantanée
Ces étapes s’appliquent seulement au partage basé sur des instantanés.

1. Vous pouvez déclencher un instantané en sélectionnant l’onglet **Détails** puis **Déclencher un instantané**. Ici, vous pouvez déclencher une capture instantanée complète ou incrémentielle de vos données. Si vous recevez des données de votre fournisseur de données pour la première fois, sélectionnez l’option de copie complète. 

   ![Déclencher un instantané](./media/trigger-snapshot.png "Déclencher un instantané") 

1. Une fois que le dernier état d’exécution est *Réussi*, accédez au magasin de données cible pour voir les données reçues. Sélectionnez **Jeux de données**, puis cliquez sur le lien dans le chemin cible. 

   ![Jeux de données consommateur](./media/consumer-datasets.png "Mappage de jeu de données consommateur") 

## <a name="view-history"></a>Afficher l’historique
Cette étape s’applique uniquement au partage basé sur un instantané. Pour afficher l’historique de vos instantanés, sélectionnez l’onglet **Historique**. Vous trouverez ici un historique de tous les instantanés générés au cours des 30 derniers jours. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez découvert comment accepter et recevoir un partage Azure Data Share. Pour en savoir plus sur les concepts liés à Azure Data Share, consultez [Concepts : Terminologie propre à Azure Data Share](terminology.md).