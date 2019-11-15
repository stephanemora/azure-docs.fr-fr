---
title: Gérer des comptes de laboratoire dans Azure Lab Services | Microsoft Docs
description: Découvrez comment créer un compte de laboratoire, voir tous les comptes de laboratoire et supprimer un compte de laboratoire dans un abonnement Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: ee64780bca13bf497793dc90ad22d3eaf91949a6
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606329"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gérer des comptes de laboratoire dans Azure Lab Services 
Dans Azure Lab Services, un compte de laboratoire est un conteneur pour les types de laboratoires gérés tels que les laboratoires de classe. Un administrateur configure un compte de laboratoire avec Azure Lab Services et fournit l’accès à tous les propriétaires de laboratoire qui peuvent alors créer des laboratoires dans leur compte. Cet article explique comment créer un compte de laboratoire, voir tous les comptes de laboratoire et supprimer un compte de laboratoire.

## <a name="create-a-lab-account"></a>Créer un compte de laboratoire
Les étapes suivantes montrent comment utiliser le portail Azure pour créer un compte de laboratoire avec Azure Lab Services. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Tous les services**. Sélectionnez **Comptes Lab** dans la section **DevOps**. Si vous sélectionnez l’étoile (`*`) en regard de **Comptes Lab**, celui-ci est ajouté à la section **FAVORIS** dans le menu de gauche. Les fois suivantes, sélectionnez **Comptes Lab** sous **FAVORIS**.

    ![Tous les services -> Comptes Lab](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Dans la page **Comptes Lab**, sélectionnez **Ajouter** dans la barre d’outils. 

    ![Sélectionnez Ajouter dans la page Comptes Lab.](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Dans la page **Comptes Lab**, effectuez les actions suivantes : 
    1. Pour **Lab account name** (Nom du compte de laboratoire), entrez un nom. 
    2. Sélectionnez **l’abonnement Azure** dans lequel vous souhaitez créer le compte de laboratoire.
    3. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez un nom pour le groupe de ressources.
    4. Pour **Emplacement**, sélectionnez la région ou l’emplacement où créer le compte de laboratoire. 
    5. Sélectionnez une **galerie d’images partagées** ou créez-en une. Vous pouvez enregistrer le modèle de machine virtuelle dans la galerie d’images partagées pour que d’autres utilisateurs puissent la réutiliser. Pour plus d’informations sur les galeries d’images partagées, consultez [Utiliser une galerie d’images partagées dans Azure Lab Services](how-to-use-shared-image-gallery.md).
    6. Dans **Appairer un réseau virtuel**, sélectionnez un réseau pair virtuel pour le réseau de laboratoire. Les laboratoires créés dans ce compte sont connectés au réseau virtuel sélectionné et ont accès aux ressources situées sur celui-ci. 
    7. Spécifiez une **plage d’adresses** pour les machines virtuelles du laboratoire. La plage d’adresses doit être mentionnée dans la notation CIDR (Classless Inter-Domain Routing), par exemple : 10.20.0.0/23). Les machines virtuelles du laboratoire seront créées dans cette plage d’adresses. Pour plus d’informations, consultez [Spécifier une plage d’adresses pour les machines virtuelles du laboratoire](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).    
    8. Dans le champ **Autoriser le créateur du lab à choisir l’emplacement du lab**, indiquez si vous souhaitez que les créateurs de laboratoire soient en mesure de sélectionner un emplacement pour le laboratoire. Par défaut, l’option est désactivée. Lorsqu’elle est désactivée, les créateurs de laboratoire ne peuvent pas spécifier un emplacement pour le laboratoire qu’ils créent. Les laboratoires sont créés dans l’emplacement géographique le plus proche du compte de laboratoire. Lorsqu’elle est activée, un créateur de laboratoire peut sélectionner un emplacement au moment de créer le laboratoire.      
    9. Sélectionnez **Create** (Créer). 

        ![Fenêtre Create a lab account (Créer un compte de laboratoire)](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Sélectionnez l’**icône représentant une cloche** dans la barre d’outils (**Notifications**), confirmez la réussite du déploiement, puis sélectionnez **Accéder à la ressource**. 

    Sinon, sélectionnez **Actualiser** dans la page **Comptes Lab**, puis sélectionnez le compte lab que vous avez créé. 

    ![Fenêtre Create a lab account (Créer un compte de laboratoire)](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. La page **Lab account** (Compte de laboratoire) suivante s’affiche :

    ![Page Lab account (Compte de laboratoire)](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Afficher les comptes de laboratoire
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu, sélectionnez **Toutes les ressources**. 
3. Pour le **type**, sélectionnez **Comptes lab**. 
    Vous pouvez également filtrer par abonnement, par groupe de ressources, par emplacement et par balise. 

    ![Toutes les ressources -> Comptes lab](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Afficher et gérer les labos dans le compte lab

1. Dans la page **Compte lab**, sélectionnez **Labs** dans le menu de gauche.

    ![Option Labs dans le compte](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Vous voyez la **liste des labs** du compte, avec les informations suivantes : 
    1. Nom du lab.
    2. Date de création du lab. 
    3. Adresse e-mail de l’utilisateur qui a créé le lab. 
    4. Nombre maximal d’utilisateurs autorisés dans le lab. 
    5. État du lab. 

## <a name="delete-a-lab-in-the-lab-account"></a>Supprimer un labo du compte lab
Suivez les instructions de la section précédente pour afficher la liste des labs du compte lab.

1. Sélectionnez **...** (points de suspension), puis sélectionnez **Supprimer**. 

    ![Bouton Supprimer un lab](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Sélectionnez **Oui** dans le message d’avertissement. 

    ![Confirmer la suppression du lab](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Supprimer un compte de laboratoire
Suivez les instructions de la section précédente, qui permettent d’afficher la liste de comptes de laboratoire. Utilisez les instructions suivantes pour supprimer un compte de laboratoire : 

1. Sélectionnez le **compte de laboratoire** que vous voulez supprimer. 
2. Sélectionnez **Supprimer** dans la barre d’outils. 

    ![Comptes lab -> Bouton Supprimer](../media/how-to-manage-lab-accounts/delete-button.png)
1. Tapez **Oui** pour confirmer.
1. Sélectionnez **Supprimer**. 

    ![Supprimer le compte lab - confirmation](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Vous pouvez également utiliser le module PowerShell Az.LabServices (préversion) pour gérer les comptes lab. Pour plus d’informations, voir la [page d’accueil Az.LabServices sur GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant : [Configurer des comptes lab](how-to-configure-lab-accounts.md).