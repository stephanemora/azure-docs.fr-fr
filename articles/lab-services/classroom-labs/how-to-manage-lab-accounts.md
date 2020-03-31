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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: c82b5d02ab3928eb0472f2a047cdca2238bf0b63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79237257"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gérer des comptes de laboratoire dans Azure Lab Services 
Dans Azure Lab Services, un compte de laboratoire est un conteneur pour les types de laboratoires gérés tels que les laboratoires de classe. Un administrateur configure un compte de laboratoire avec Azure Lab Services et fournit l’accès à tous les propriétaires de laboratoire qui peuvent alors créer des laboratoires dans leur compte. Cet article explique comment créer un compte de laboratoire, voir tous les comptes de laboratoire et supprimer un compte de laboratoire.

## <a name="create-a-lab-account"></a>Créer un compte de laboratoire
Les étapes suivantes montrent comment utiliser le portail Azure pour créer un compte de laboratoire avec Azure Lab Services. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Tous les services**. Sélectionnez **Comptes Lab** dans la section **DevOps**. Si vous sélectionnez l’étoile (`*`) en regard de **Comptes Lab**, celui-ci est ajouté à la section **FAVORIS** dans le menu de gauche. Les fois suivantes, sélectionnez **Comptes Lab** sous **FAVORIS**.

    ![Tous les services -> Comptes Lab](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Dans la page **Comptes Lab**, sélectionnez **Ajouter** dans la barre d’outils ou **Créer un compte lab** sur la page. 

    ![Sélectionnez Ajouter dans la page Comptes Lab.](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Sous l’onglet **De base** de la page **Créer un compte de laboratoire**, effectuez les actions suivantes : 
    1. Pour **Lab account name** (Nom du compte de laboratoire), entrez un nom. 
    2. Sélectionnez **l’abonnement Azure** dans lequel vous souhaitez créer le compte de laboratoire.
    3. Pour **Groupe de ressources**, sélectionnez **Créer un nouveau** et entrez un nom pour le groupe de ressources.
    4. Pour **Emplacement**, sélectionnez la région ou l’emplacement où créer le compte de laboratoire.
    5. Dans le champ **Autoriser le créateur du lab à choisir l’emplacement du lab**, indiquez si vous souhaitez que les créateurs de laboratoire soient en mesure de sélectionner un emplacement pour le laboratoire. Par défaut, l’option est désactivée. Lorsqu’elle est désactivée, les créateurs de laboratoire ne peuvent pas spécifier un emplacement pour le laboratoire qu’ils créent. Les laboratoires sont créés dans l’emplacement géographique le plus proche du compte de laboratoire. Lorsqu’elle est activée, un créateur de laboratoire peut sélectionner un emplacement au moment de créer le laboratoire. Pour plus d’informations, consultez [Autoriser un créateur de labo à choisir l’emplacement du labo](allow-lab-creator-pick-lab-location.md). 

        ![Créer un compte lab -> De base](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Sélectionnez **Suivant : Avancé** au bas de la page pour accéder à l’onglet **Avancé**, puis effectuez les étapes suivantes : 
    1. Sélectionnez une **galerie d’images partagées** ou créez-en une. Vous pouvez enregistrer le modèle de machine virtuelle dans la galerie d’images partagées pour que d’autres utilisateurs puissent la réutiliser. Pour plus d’informations sur les galeries d’images partagées, consultez [Utiliser une galerie d’images partagées dans Azure Lab Services](how-to-use-shared-image-gallery.md).
    2. Indiquez si vous souhaitez **arrêter automatiquement les machines virtuelles Windows** quand les utilisateurs se déconnectent. Spécifiez la durée pendant laquelle les machines virtuelles doivent attendre que l’utilisateur se reconnecte avant de s’arrêter automatiquement. 
    3. Dans **Appairer un réseau virtuel**, sélectionnez un réseau pair virtuel pour le réseau de laboratoire. Les laboratoires créés dans ce compte sont connectés au réseau virtuel sélectionné et ont accès aux ressources situées sur celui-ci. Pour plus d’informations, consultez [Connecter un réseau lab avec un réseau virtuel pair](how-to-connect-peer-virtual-network.md).    
    8. Spécifiez une **plage d’adresses** pour les machines virtuelles du laboratoire. La plage d’adresses doit être mentionnée dans la notation CIDR (Classless Inter-Domain Routing), par exemple : 10.20.0.0/23). Les machines virtuelles du laboratoire seront créées dans cette plage d’adresses. Pour plus d’informations, consultez [Spécifier une plage d’adresses pour les machines virtuelles du laboratoire](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).  

        ![Créer un compte lab -> Avancé](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Sélectionnez **Suivant : Étiquettes** au bas de la page pour basculer vers l’onglet **Étiquettes**. Ajoutez toutes les étiquettes que vous souhaitez associer au compte lab. Les étiquettes sont des paires nom/valeur qui vous permettent de catégoriser les ressources et d’afficher une facturation centralisée en appliquant la même étiquette à plusieurs ressources et groupes de ressources. Pour plus d’informations, consultez [Organisation des ressources Azure à l’aide de balises](../../azure-resource-manager/management/tag-resources.md).

    ![Créer un compte lab -> Étiquettes](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Sélectionnez **Vérifier + créer** en bas de cette page pour basculer vers l’onglet **Vérifier + créer**. 
4. Passez en revue les informations de résumé sur cette page, puis sélectionnez **Créer**. 

    ![Créer un compte lab -> Étiquettes](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Attendez jusqu’à la fin du déploiement, développez **Étapes suivantes**, puis sélectionnez **Accéder à la ressource** comme indiqué sur l’image suivante : 

    Vous pouvez également sélectionner l’**icône représentant une cloche** dans la barre d’outils (**Notifications**), confirmer la réussite du déploiement, puis sélectionner **Accéder à la ressource**. 

    Sinon, sélectionnez **Actualiser** dans la page **Comptes Lab**, puis sélectionnez le compte lab que vous avez créé. 

    ![Fenêtre Create a lab account (Créer un compte de laboratoire)](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. La page **Lab account** (Compte de laboratoire) suivante s’affiche :

    ![Page Lab account (Compte de laboratoire)](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Afficher les comptes de laboratoire
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu, sélectionnez **Toutes les ressources**. 
3. Pour le **type**, sélectionnez **Comptes lab**. 
    Vous pouvez également filtrer par abonnement, par groupe de ressources, par emplacement et par balise. 

    ![Toutes les ressources -> Comptes lab](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Afficher et gérer les labos dans le compte lab

1. Dans la page **Compte Lab**, sélectionnez **Tous les labs** dans le menu de gauche.

    ![Option Labs dans le compte](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Vous voyez la **liste des labs** du compte, avec les informations suivantes : 
    1. Nom du lab.
    2. Date de création du lab. 
    3. Adresse e-mail de l’utilisateur qui a créé le lab. 
    4. Nombre maximal d’utilisateurs autorisés dans le lab. 
    5. État du lab. 
    6. Attributions de rôle. 

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
> Vous pouvez également utiliser le module PowerShell Az.LabServices (préversion) pour gérer les comptes lab. Pour plus d’informations, consultez la [page d’accueil Az.LabServices sur GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- [Autoriser un créateur de labo à choisir l’emplacement du labo](allow-lab-creator-pick-lab-location.md)
- [Connecter un réseau lab avec un réseau virtuel pair](how-to-connect-peer-virtual-network.md)
- [Attacher une galerie d’images partagées à un lab](how-to-attach-detach-shared-image-gallery.md)
- [Ajouter un utilisateur comme propriétaire de labo](how-to-add-user-lab-owner.md)
- [Afficher les paramètres de pare-feu pour un labo](how-to-configure-firewall-settings.md)
- [Configurer les autres paramètres d’un lab](how-to-configure-lab-accounts.md)