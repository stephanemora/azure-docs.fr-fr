---
title: Table Azure dans un programme de la Place de marché commerciale | Place de marché Azure
description: Configurer la gestion des prospects pour Blob Azure
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285246"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Instructions de gestion des prospects pour Blob Azure

>[!Caution]
>L’option Blob Azure pour traiter les prospects de votre offre de la Place de marché est déconseillée. Si vous avez actuellement une offre publiée avec une configuration de gestion des prospects pour Blob Azure, vous ne recevez plus de prospects. Mettez à jour votre configuration de gestion des prospects avec l’une des autres options de gestion des prospects. Pour plus d’informations sur les autres options, voir la page d’accueil [Gestion des prospects](./commercial-marketplace-get-customer-leads.md).

Si votre système de gestion de la relation client (CRM) n’est pas explicitement pris en charge dans l’Espace partenaires afin de recevoir des prospects de la Place de marché Azure et d’AppSource, vous pouvez utiliser un Blob Azure pour gérer ces prospects. Vous pouvez ensuite décider d’exporter les données et de les importer dans votre système CRM. Les instructions de cet article décrivent le processus de création d’un compte de Stockage Azure et d’un Blob Azure sous ce compte. De plus, vous pouvez créer un flux à l’aide de Microsoft Flow pour envoyer une notification par e-mail quand votre offre reçoit un prospect.


## <a name="how-to-configure-azure-blob"></a>Comment configurer un Blob Azure

1. Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation gratuit](https://azure.microsoft.com/pricing/free-trial/).
1. Une fois que votre compte Azure est actif, connectez-vous au [portail Microsoft Azure](https://portal.azure.com).
1. Dans le portail Microsoft Azure, créez un compte de stockage à l’aide de la procédure suivante.  
    1. Dans la barre de menus de gauche, sélectionnez **+ Créer une ressource**.  Le volet **Nouveau** (panneau) est affiché sur la droite.
    2. Sélectionnez **Stockage** dans le volet **Nouveau**.  Une liste **Sélection** est affichée sur la droite.
    3. Sélectionnez le **Compte de stockage** pour commencer à créer un compte.  Suivez les instructions de l’article [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Procédure de création d’un compte Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Pour en savoir plus sur les comptes de stockage, sélectionnez [Tutoriel de démarrage rapide](https://docs.microsoft.com/azure/storage/).  Pour plus d’informations sur la tarification du stockage, consultez [Tarification de Storage](https://azure.microsoft.com/pricing/details/storage/).

4. Attendez que votre compte de stockage soit approvisionné (ce processus prend généralement quelques minutes).  Ensuite, accédez à votre compte de stockage à partir de la page **Accueil** du portail Microsoft Azure, en sélectionnant **Voir toutes vos ressources** ou **Toutes les ressources** dans la barre de menus de navigation de gauche du portail Microsoft Azure.

    ![Accéder à votre compte de stockage Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Dans le volet de votre compte de stockage, sélectionnez **Clés d’accès**, puis copiez la valeur *Chaîne de connexion* pour la clé. Enregistrez cette valeur, car il s’agit de la valeur de *Chaîne de connexion de compte de stockage* que vous devez fournir dans le portail de publication afin de recevoir des prospects pour votre offre de la Place de marché.

     Voici un exemple de chaîne de connexion :

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Clé de stockage Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Dans la page de votre compte de stockage, sélectionnez **Objets blob**.

   ![Clé de stockage Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Une fois sur la page des objets blob, sélectionnez le bouton **+ Conteneur**.

8. Entrez un **nom** pour votre nouveau conteneur. Le nom du conteneur doit être en minuscules, commencer par une lettre ou un chiffre, et peut comporter uniquement des lettres, des chiffres et des tirets (-). Pour plus d’informations sur les noms des conteneurs et des objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Enregistrez cette valeur, car il s’agit de la valeur de *Nom du conteneur* que vous devez fournir dans le portail de publication afin de recevoir des prospects pour votre offre de la Place de marché.

9. Définissez le niveau d’accès public au conteneur sur **Privé (aucun accès anonyme)** .

10. Sélectionnez **OK** pour créer le conteneur.

    ![Nouveau conteneur](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Configurer votre offre pour envoyer des prospects au Blob Azure

Lorsque vous êtes prêt à configurer les informations de gestion des prospects pour votre offre sur le portail de publication, procédez comme suit :

1. Accédez à la page **Configuration de l'offre** de votre offre.
2. Sélectionnez **Connexion** dans la section Gestion des prospects.

    ![Offre de connexion](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Dans la fenêtre contextuelle Détails de la connexion, sélectionnez **Blob Azure** comme Destination du prospect.

    ![Détail de connexion](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Indiquez le **Nom du conteneur** et la **Chaîne de connexion de compte de stockage** obtenus en suivant ces instructions.

    * Exemple de nom de conteneur : `marketplaceleadcontainer`
    * Exemple de chaîne de connexion de compte de stockage : `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![Détails de la connexion](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Sélectionnez **Enregistrer**.

    > [!NOTE]
    > Vous devez terminer la configuration du reste de l'offre et la publier avant de pouvoir recevoir des prospects pour cette offre.


