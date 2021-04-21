---
title: Partager et recevoir des données à partir de Stockage Blob Azure et d’Azure Data Lake Storage
description: Découvrez comment partager et recevoir des données de Stockage Blob Azure et d’Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/23/2021
ms.openlocfilehash: 4db523624922d8ddcb8c1868b84927926d9ed3d5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103808"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Partager et recevoir des données à partir de Stockage Blob Azure et d’Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Le service Azure Data Share prend en charge le partage basé sur un instantané à partir d’un compte de stockage. Cet article explique comment partager et recevoir des données à partir de Stockage Blob Azure, d’Azure Data Lake Storage Gen1 et d’Azure Data Lake Storage Gen2.

Azure Data Share prend en charge le partage de fichiers, dossiers et de systèmes de fichiers à partir d’Azure Data Lake Gen1 et Azure Data Lake Gen2. Le service prend également en charge le partage de blobs, de dossiers et de conteneurs à partir de Stockage Blob Azure. Seuls les objets blob de blocs sont actuellement pris en charge. Les données partagées à partir de ces sources peuvent être reçues par Azure Data Lake Gen2 ou dans Stockage Blob Azure.

Lorsque les systèmes de fichiers, les conteneurs ou les dossiers sont partagés dans un partage basé sur un instantané, les consommateurs de données peuvent choisir d’effectuer une copie complète des données de partage. Ils peuvent également utiliser la fonctionnalité d’instantané incrémentiel pour copier uniquement les fichiers nouveaux ou mis à jour. La fonctionnalité d’instantané incrémentiel est basée sur l’heure de la dernière modification des fichiers. 

Les fichiers existants portant le même nom seront remplacés lors d’un instantané. Un fichier supprimé de la source n’est pas supprimé de la cible. Les sous-dossiers vides sur la source ne sont pas copiés vers la cible. 

## <a name="share-data"></a>Partager des données

Utilisez les informations des sections suivantes pour partager des données à l’aide d’Azure Data Share. 
### <a name="prerequisites-to-share-data"></a>Conditions préalables pour partager des données

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Recherchez l’adresse e-mail de connexion Azure de votre destinataire. L’alias d’e-mail du destinataire ne répondra pas à vos besoins.
* Si le magasin de données Azure source se trouve dans un autre abonnement Azure que celui où vous allez créer une ressource de partage de données, inscrivez le [fournisseur de ressources Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) dans l’abonnement où se trouve le magasin de données Azure. 

### <a name="prerequisites-for-the-source-storage-account"></a>Conditions préalables pour le compte de stockage source

* Un compte de stockage Azure. Si vous n’avez pas encore de compte, [créez-en un](../storage/common/storage-account-create.md).
* L’autorisation d’écrire sur le compte de stockage. L’autorisation d’écriture se trouve dans *Microsoft.Storage/storageAccounts/write*. Elle fait partie du rôle Contributeur.
* Autorisation d’ajouter une attribution de rôle au compte de stockage. Cette autorisation se trouve dans *Microsoft.Authorization/role assignments/write*. Elle est incluse dans le rôle Propriétaire. 

### <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Créer un compte Data Share

Créez une ressource Azure Data Share dans un groupe de ressources Azure.

1. Dans le coin supérieur gauche du portail, ouvrez le menu, puis sélectionnez **Créer une ressource** (+).

1. Recherchez *Data Share*.

1. Sélectionnez **Data Share**, puis **Créer**.

1. Fournissez les détails de base de votre ressource Azure Data Share : 

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Abonnement | Votre abonnement | Sélectionnez un abonnement Azure pour votre compte de partage de données.|
    | Groupe de ressources | *test-resource-group* | Utilisez un groupe de ressources existant ou créez-en un. |
    | Emplacement | *USA Est 2* | Sélectionnez une région pour votre compte de partage de données.
    | Nom | *datashareaccount* | Nommez votre compte de partage de données. |
    | | |

1. Sélectionnez **Vérifier + créer** > **Créer** pour provisionner votre compte de partage de données. En règle générale, le provisionnement d’un nouveau compte de partage de données prend environ 2 minutes. 

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

### <a name="create-a-share"></a>Créer un partage

1. Accédez à la page de **présentation** de votre partage de données.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Capture d’écran montrant la présentation du partage de données.":::

1. Sélectionnez **Start sharing your data** (Commencer à partager vos données).

1. Sélectionnez **Create** (Créer).   

1. Fournissez les détails de votre partage. Spécifiez un nom, un type de partage, une description du contenu du partage et des conditions d’utilisation (facultatif). 

    ![Capture d’écran montrant les détails du partage de données.](./media/enter-share-details.png "Entrez les détails du partage de données.") 

1. Sélectionnez **Continuer**.

1. Pour ajouter des jeux de données à votre partage, sélectionnez **Add Datasets** (Ajouter des jeux de données). 

    ![Capture d’écran montrant comment ajouter des jeux de données à votre partage.](./media/datasets.png "DataSets.")

1. Sélectionnez un type de jeu de données à ajouter. La liste des types de jeux de données varie selon que vous avez sélectionné un partage basé sur un instantané ou un partage sur place à l’étape précédente. 

    ![Capture d'écran montrant où sélectionner un type de jeu de données.](./media/add-datasets.png "Ajouter des jeux de données.")    

1. Accédez à l'objet que vous souhaitez partager. Puis sélectionnez **Ajouter des jeux de données**. 

    ![Capture d’écran montrant comment sélectionner un objet à partager.](./media/select-datasets.png "Sélectionnez des jeux de données.")    

1. Sous l’onglet **Destinataires**, ajoutez l’adresse e-mail de votre consommateur de données en sélectionnant **Ajouter un destinataire**. 

    ![Capture d’écran montrant comment ajouter des adresses e-mail de destinataires.](./media/add-recipient.png "Ajouter des destinataires.") 

1. Sélectionnez **Continuer**.

1. Si vous avez sélectionné un type de partage d’instantané, vous pouvez configurer la planification d’instantanés afin de mettre à jour vos données pour le consommateur de données. 

    ![Capture d’écran montrant les paramètres de planification d’instantanés.](./media/enable-snapshots.png "Activer les instantanés.") 

1. Sélectionnez une heure de début et un intervalle de périodicité. 

1. Sélectionnez **Continuer**.

1. Sous l’onglet **Review + Create** (Revoir + Créer), passez en revue le contenu du package, les paramètres, les destinataires ainsi que les paramètres de synchronisation. Sélectionnez ensuite **Créer**.

Vous avez maintenant créé votre partage de données Azure. Le destinataire de votre partage de données peut accepter votre invitation. 

## <a name="receive-data"></a>Recevoir des données

Les sections suivantes décrivent comment recevoir des données partagées.
### <a name="prerequisites-to-receive-data"></a>Conditions préalables pour recevoir des données
Avant d’accepter une invitation de partage de données, vérifiez que les conditions préalables suivantes sont remplies : 

* Un abonnement Azure. Si vous n’avez pas d’abonnement, créez un [compte gratuit](https://azure.microsoft.com/free/).
* Une invitation d’Azure. L’objet de l’e-mail doit être « Invitation Azure Data Share de *\<yourdataprovider\@domain.com>*  ».
* Un [fournisseur de ressources Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) inscrit dans :
    * L’abonnement Azure dans lequel vous allez créer une ressource de partage de données.
    * L’abonnement Azure dans lequel se trouvent vos magasins de données Azure cibles.

### <a name="prerequisites-for-a-target-storage-account"></a>Conditions préalables pour un compte de stockage cible

* Un compte de stockage Azure. [Créez un compte](../storage/common/storage-account-create.md) si vous n’en avez pas déjà un. 
* L’autorisation d’écrire sur le compte de stockage. Cette autorisation se trouve dans *Microsoft.Storage/storageAccounts/write*. Elle fait partie du rôle Contributeur. 
* Autorisation d’ajouter une attribution de rôle au compte de stockage. Cette attribution se trouve dans *Microsoft.Authorization/role assignments/write*. Elle est incluse dans le rôle Propriétaire.  

### <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Ouvrir une invitation

Vous pouvez ouvrir une invitation à partir d’un e-mail, ou directement à partir du portail Azure.

1. Pour ouvrir une invitation à partir d’un e-mail, recherchez dans votre boîte de réception une invitation émanant de votre fournisseur de données. L’invitation provient de Microsoft Azure et s’intitule « Invitation Azure Data Share de *\<yourdataprovider\@domain.com>*  ». Sélectionnez **Afficher l’invitation** pour voir votre invitation dans Azure. 

   Pour ouvrir une invitation à partir du portail Azure, recherchez *Invitations Azure Data Share*. Une liste d’invitations de partages de données s’affiche.

   ![Capture d’écran montrant la liste des invitations dans le portail Azure.](./media/invitations.png "Liste des invitations.") 

1. Sélectionnez le partage à afficher. 

### <a name="accept-an-invitation"></a>Accepter une invitation
1. Passez en revue tous les champs, y compris les **Conditions d’utilisation**. Si vous acceptez les conditions juridiques, cochez la case. 

   ![Capture d’écran montrant les Conditions d’utilisation.](./media/terms-of-use.png "Conditions d’utilisation.") 

1. Sous **Compte Data Share cible**, sélectionnez l’abonnement et le groupe de ressources où vous déploierez votre partage de données. Renseignez ensuite les champs suivants :

   * Dans le champ **Compte Data Share**, sélectionnez **Créer** si vous n’avez pas de compte Data Share. Sinon, sélectionnez un compte Data Share existant qui acceptera votre partage de données. 

   * Dans le champ **Nom de partage reçu**, conservez la valeur par défaut spécifiée par le fournisseur de données, ou spécifiez le nouveau nom du partage reçu. 

1. Sélectionnez **Accepter et configurer**. Un abonnement de partage est créé. 

   ![Capture d’écran montrant où accepter les options de configuration.](./media/accept-options.png "Options d’acceptation") 

    Le partage reçu apparaît dans votre compte Data Share. 

    Si vous ne souhaitez pas accepter l’invitation, sélectionnez **Reject** (Rejeter). 

### <a name="configure-a-received-share"></a>Configurer un partage reçu
Suivez les étapes de cette section pour configurer un emplacement de réception des données.

1. Sous l’onglet **Jeux de données**, cochez la case en regard du jeu de données dans lequel vous souhaitez affecter une destination. Sélectionnez **Mapper sur cible** pour choisir un magasin de données cible. 

   ![Capture d’écran montrant comment mapper à une cible.](./media/dataset-map-target.png "Mapper sur cible.") 

1. Sélectionnez un magasin de données cible pour les données. Les fichiers dans le magasin de données cible ayant les mêmes chemin et nom que ceux des fichiers des données reçues seront remplacés. 

   ![Capture d'écran montrant où sélectionner un compte de stockage cible.](./media/map-target.png "Stockage cible.") 

1. Pour un partage basé sur un instantané, si le fournisseur de données utilise une planification d’instantanés pour mettre régulièrement à jour les données, vous pouvez activer la planification dans l’onglet **Planification d'instantanés**. Cochez la case en regard de la planification d’instantanés. Ensuite, sélectionnez **Activer**. Notez que le premier instantané planifié démarre dans un délai d’une minute après l’heure planifiée et que les instantanés suivants démarrent en quelques secondes après l’heure planifiée.

   ![Capture d’écran montrant comment activer une planification d’instantanés.](./media/enable-snapshot-schedule.png "Activer la planification d’instantané.")

### <a name="trigger-a-snapshot"></a>Déclencher une capture instantanée
Les étapes de cette section s’appliquent uniquement au partage basé sur un instantané.

1. Vous pouvez déclencher un instantané à partir de l’onglet **Détails**. Sous l’onglet, sélectionnez **Déclencher un instantané**. Vous pouvez choisir de déclencher un instantané complet ou un instantané incrémentiel de vos données. Si vous recevez pour la première fois des données de votre fournisseur de données, sélectionnez **Copie complète**. Quand un instantané est en cours d’exécution, les instantanés suivants ne démarrent pas tant que l’exécution de l’instantané précédent n’est pas terminée.

   ![Capture d’écran montrant la sélection de l’option Déclencher un instantané.](./media/trigger-snapshot.png "Déclencher un instantané.") 

1. Une fois que le dernier état d’exécution est *Réussi*, accédez au magasin de données cible pour voir les données reçues. Sélectionnez **Jeux de données**, puis choisissez le lien du chemin cible. 

   ![Capture d’écran montrant un mappage de jeu de données consommateur.](./media/consumer-datasets.png "Mappage de jeu de données consommateur.") 

### <a name="view-history"></a>Afficher l’historique
Vous pouvez afficher l’historique de vos instantanés uniquement dans un partage basé sur un instantané. Pour afficher l’historique, ouvrez l’onglet **Historique**. Vous trouverez ici l’historique de tous les instantanés générés au cours des 30 derniers jours. 

## <a name="storage-snapshot-performance"></a>Performance de capture instantanée de stockage
La performance de capture instantanée de stockage est affectées par un certain nombre de facteurs en plus du nombre de fichiers et de la taille des données partagées. Il est toujours recommandé d’effectuer vos propres tests de performances. Voici quelques exemples de facteurs qui ont un impact sur les performances.

* Accès simultané aux magasins de données sources et cibles.  
* Emplacement des magasins de données sources et cibles. 
* Pour un instantané incrémentiel, le nombre de fichiers contenus dans le jeu de données partagé peut avoir un impact sur le temps nécessaire pour trouver la liste des fichiers dont la dernière modification est postérieure à la dernière capture instantanée réussie. 


## <a name="next-steps"></a>Étapes suivantes
Vous avez appris à partager et recevoir des données à partir d’un compte de stockage à l’aide du service Azure Data Share. Pour en savoir plus sur le partage à partir d’autres sources de données, consultez [Magasins de données pris en charge](supported-data-stores.md).
