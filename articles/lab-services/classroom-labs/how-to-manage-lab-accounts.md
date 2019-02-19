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
ms.date: 02/07/2018
ms.author: spelluru
ms.openlocfilehash: 7e3142e0274f2328d3e0c8a3e6f9a2e4c3d45d87
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959135"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gérer des comptes de laboratoire dans Azure Lab Services 
Dans Azure Lab Services, un compte de laboratoire est un conteneur pour les laboratoires gérés tels que les laboratoires de classe. Un administrateur configure un compte de laboratoire avec Azure Lab Services et fournit l’accès à tous les propriétaires de laboratoire qui peuvent alors créer des laboratoires dans leur compte. Cet article explique comment créer un compte de laboratoire, voir tous les comptes de laboratoire et supprimer un compte de laboratoire.

## <a name="create-a-lab-account"></a>Créer un compte de laboratoire
Les étapes suivantes montrent comment utiliser le portail Azure pour créer un compte de laboratoire avec Azure Lab Services. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** dans le menu de gauche. Sélectionnez **Comptes lab** dans la section **DEVOPS**. Si vous sélectionnez l'étoile (`*`) en regard de **Comptes lab**, celle-ci est ajoutée à la section **FAVORIS** dans le menu de gauche. Dès la prochaine fois, vous sélectionnerez **Comptes lab** sous **FAVORIS**.

    ![Tous les services -> Comptes lab](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Sur la page **Comptes lab**, sélectionnez l'option **Ajouter** de la barre d'outils. 

    ![Sélectionnez Ajouter sur la page Comptes lab.](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Sur la page **Compte lab**, procédez comme suit : 
    1. Pour **Lab account name** (Nom du compte de laboratoire), entrez un nom. 
    2. Sélectionnez **l’abonnement Azure** dans lequel vous souhaitez créer le compte de laboratoire.
    3. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez un nom pour le groupe de ressources.
    4. Pour **Emplacement**, sélectionnez la région ou l’emplacement où créer le compte de laboratoire. 
    5. Sélectionnez **Créer**. 

        ![Fenêtre Create a lab account (Créer un compte de laboratoire)](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Sélectionnez l'icône **Cloche** sur la barre d'outils (**Notifications**), vérifiez que le déploiement a abouti, puis sélectionnez **Accéder à la ressource**. 

    Vous pouvez également sélectionner **Actualiser** sur la page **Comptes lab**, puis sélectionner le compte lab que vous avez créé. 

    ![Fenêtre Create a lab account (Créer un compte de laboratoire)](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. La page **Lab account** (Compte de laboratoire) suivante s’affiche :

    ![Page Lab account (Compte de laboratoire)](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Ajouter un utilisateur au rôle Créateur de laboratoire
Pour configurer un laboratoire de classe dans un compte de laboratoire, l’utilisateur doit être membre du rôle **Créateur de laboratoire** dans le compte de laboratoire. Le compte utilisé pour créer le compte de laboratoire est automatiquement ajouté à ce rôle. Si vous envisagez d’utiliser le même compte d’utilisateur pour créer un laboratoire de classe, vous pouvez ignorer cette étape. Pour utiliser un autre compte d’utilisateur pour créer un laboratoire de classe, procédez comme suit : 

Pour donner aux formateurs l’autorisation de créer des laboratoires pour leurs classes, ajoutez-les au rôle **Créateur de laboratoire** :

1. Dans la page **Compte Lab**, sélectionnez **Contrôle d’accès (IAM)**, puis cliquez sur **+ Ajouter une attribution de rôle** dans la barre d’outils. 

    ![Contrôle d'accès -> Bouton Ajouter une attribution de rôle](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Sur la page **Ajouter une attribution de rôle**, sélectionnez **Créateur de laboratoire** en regard de **Rôle**, choisissez l'utilisateur à ajouter au rôle Créateur de laboratoire, puis sélectionnez **Enregistrer**. 

    ![Ajouter un créateur de laboratoire](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="specify-marketplace-images-available-to-lab-creators"></a>Spécifier les images de la Place de marché disponibles pour les créateurs de laboratoire
En tant que propriétaire d’un compte de laboratoire, vous pouvez spécifier les images de la place de marché que les créateurs de laboratoire peuvent utiliser pour créer des laboratoires dans le compte de laboratoire. 

1. Sélectionnez **Images de la Place de marché** à gauche du menu. Par défaut, la liste complète des images (activées et désactivées) s’affiche. Vous pouvez filtrer la liste pour afficher uniquement les images activées/désactivées en sélectionnant l’option **Enabled only (Activées uniquement)**/**Disabled only (Désactivées uniquement)** dans la liste déroulante en haut. 
    
    ![Page des images de la Place de marché](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Les images de la place de marché affichées dans la liste sont uniquement celles qui correspondent aux conditions suivantes :
        
    - Crée une seule machine virtuelle.
    - Utilise Azure Resource Manager pour approvisionner des machines virtuelles
    - Ne nécessite pas l’achat d’un plan de licences supplémentaire
2. Pour **désactiver** une image de la Place de marché qui a été activée, effectuez l’une des actions suivantes : 
    1. Sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Disable image (Désactiver l’image)**. 

        ![Désactiver une image](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Sélectionnez une ou plusieurs images dans la liste en cochant la case à côté du nom de l’image, puis sélectionnez **Disable selected images (Désactiver les images sélectionnées)**. 

        ![Désactiver plusieurs images](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De même, pour **activer** une image de la Place de marché, effectuez l’une des actions suivantes : 
    1. Sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Enable image (Désactiver l’image)**. 
    2. Sélectionnez une ou plusieurs images dans la liste en cochant la case à côté du nom de l’image, puis sélectionnez **Disable selected images (Désactiver les images sélectionnées)**. 

## <a name="view-lab-accounts"></a>Afficher les comptes de laboratoire
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu, sélectionnez **Toutes les ressources**. 
3. Pour le **type**, sélectionnez **Comptes lab**. 
    Vous pouvez également filtrer par abonnement, par groupe de ressources, par emplacement et par balise. 

    ![Toutes les ressources -> Comptes lab](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Supprimer un compte de laboratoire
Suivez les instructions de la section précédente, qui permettent d’afficher la liste de comptes de laboratoire. Utilisez les instructions suivantes pour supprimer un compte de laboratoire : 

1. Sélectionnez le **compte de laboratoire** que vous voulez supprimer. 
2. Sélectionnez **Supprimer** dans la barre d’outils. 

    ![Comptes lab -> Bouton Supprimer](../media/how-to-manage-lab-accounts/delete-button.png)
1. Tapez **Oui** pour confirmer.
1. Sélectionnez **Supprimer**. 

    ![Supprimer le compte lab - confirmation](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

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

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant que propriétaire de labo, configurer et contrôler l’utilisation d’un labo](how-to-configure-student-usage.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)