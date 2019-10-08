---
title: 'Didacticiel : Partager en dehors de votre organisation - Azure Data Share Preview'
description: Tutoriel - Partager des données avec des clients et des partenaires via Azure Data Share Preview
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: f7df46a6a6f149ef0228fda8c967469a25dc3d50
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327417"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Didacticiel : Partager vos données avec Azure Data Share Preview

Dans ce tutoriel, vous allez découvrir comment créer un partage Azure Data Share et commencer à partager vos données avec des clients et des partenaires externes à votre organisation Azure. 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un partage Data Share
> * Ajouter des jeux de données à votre partage Data Share
> * Activer une planification de synchronisation pour votre partage Data Share 
> * Ajouter des destinataires à votre partage Data Share 

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Compte Stockage Azure : Si vous n’en avez pas déjà, vous pouvez créer un [compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Autorisation d’ajouter l’attribution de rôle au compte de stockage, qui est présente dans l’autorisation *Microsoft. Authorization/Role Assignments/Write*. Cette autorisation existe dans le rôle propriétaire. 
* L’adresse e-mail de connexion Azure de vos destinataires (l’utilisation de leur alias de courrier ne fonctionnera pas).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Créer un compte Data Share

Créez une ressource Azure Data Share dans un groupe de ressources Azure.

1. Cliquez sur le bouton **Créer une ressource** (+) dans le coin supérieur gauche du portail.

1. Recherchez *Data Share*.

1. Sélectionnez Data Share (préversion), puis sélectionnez **Créer**.

1. Indiquez les détails de base de votre ressource Azure Data Share à partir des informations suivantes. 

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom | *datashareacount* | Spécifiez un nom pour votre compte de partage de données. |
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

    ![EnterShareDetails](./media/enter-share-details.png "Entrer les détails du partage") 

1. Sélectionnez **Continue** (Continuer)

1. Pour ajouter des jeux de données à votre partage Data Share, sélectionnez **Add Datasets** (Ajouter des jeux de données). 

    ![Jeux de données](./media/datasets.png "Jeux de données")

1. Sélectionnez le type de jeu de données à ajouter. 

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
