---
title: Gérer des comptes lab dans Azure Lab Services | Microsoft Docs
description: Découvrez comment créer un compte lab, voir tous les comptes lab et supprimer un compte lab dans un abonnement Azure.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 6f9b85ec4821ff2454970136b3c8af2cb0f92154
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140821"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gérer des comptes lab dans Azure Lab Services 
Dans Azure Lab Services, un compte lab est un conteneur pour les labs gérés tels que les labs de classe. Un administrateur configure un compte lab avec Azure Lab Services et fournit l’accès à tous les propriétaires de lab qui peuvent alors créer des labs dans leur compte. Cet article explique comment créer un compte lab, voir tous les comptes lab et supprimer un compte lab.

## <a name="create-a-lab-account"></a>Créer un compte lab
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Créer une ressource** dans le menu principal de gauche.
3. Recherchez **Lab Services** dans la Place de marché Azure, puis sélectionnez **Lab Services** dans la liste déroulante. 
4. Sélectionnez **Lab Services (préversion)** dans la liste des services filtrés. 
5. Dans la fenêtre **Créer un compte lab**, sélectionnez **Créer**.
7. Dans la fenêtre **Compte lab**, effectuez les actions suivantes : 
    1. Pour **Nom du compte lab**, entrez un nom. 
    2. Sélectionnez **l’abonnement Azure** dans lequel vous souhaitez créer le compte lab.
    3. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez un nom pour le groupe de ressources.
    4. Pour **Emplacement**, sélectionnez la région ou l’emplacement où créer le compte lab. 
    5. Sélectionnez **Créer**. 

        ![Fenêtre Créer un compte lab](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Si vous ne voyez pas la page associée au compte lab, sélectionnez le bouton **Notifications**, puis cliquez sur le bouton **Accéder à la ressource** dans les notifications. 

    ![Fenêtre Créer un compte lab](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. La page **Compte lab** suivante s’affiche :

    ![Page Compte lab](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Ajouter un utilisateur au rôle Créateur de laboratoire
Pour configurer un laboratoire de classe dans un compte de laboratoire, l’utilisateur doit être membre du rôle **Créateur de laboratoire** dans le compte de laboratoire. Le compte utilisé pour créer le compte de laboratoire est automatiquement ajouté à ce rôle. Si vous envisagez d’utiliser le même compte d’utilisateur pour créer un laboratoire de classe, vous pouvez ignorer cette étape. Pour utiliser un autre compte d’utilisateur pour créer un laboratoire de classe, procédez comme suit : 

1. Dans la page **Compte lab**, sélectionnez **Contrôle d’accès (IAM)**, puis cliquez sur **+Ajouter** dans la barre d’outils. 

    ![Page Compte lab](../media/tutorial-setup-lab-account/access-control.png)
2. Dans la page **Ajouter des autorisations**, sélectionnez **Créateur de laboratoire** pour **Rôle**, sélectionnez l’utilisateur que vous souhaitez ajouter au rôle Créateur de laboratoire, puis sélectionnez **Enregistrer**. 

    ![Ajouter un utilisateur au rôle Créateur de laboratoire](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Spécifier les images de la Place de marché disponibles pour les propriétaires de laboratoire
En tant que propriétaire d’un compte lab, vous pouvez spécifier les images de la Place de marché que les créateurs de lab peuvent utiliser pour créer des labs dans le compte lab. 

1. Sélectionnez **Images de la Place de marché** à gauche du menu. Par défaut, la liste complète des images (activées et désactivées) s’affiche. Vous pouvez filtrer la liste pour afficher uniquement les images activées/désactivées en sélectionnant l’option **Activées uniquement**/**Désactivées uniquement** dans la liste déroulante en haut. 
    
    ![Page des images de la Place de marché](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Les images de la place de marché affichées dans la liste sont uniquement celles qui correspondent aux conditions suivantes :
        
    - Crée une seule machine virtuelle.
    - Utilise Azure Resource Manager pour approvisionner des machines virtuelles
    - Ne nécessite pas l’achat d’un plan de licences supplémentaire
2. Pour **désactiver** une image de la Place de marché qui a été activée, effectuez l’une des actions suivantes : 
    1. Sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Désactiver l’image**. 

        ![Désactiver une image](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Sélectionnez une ou plusieurs images dans la liste en cochant la case à côté du nom de l’image, puis sélectionnez **Désactiver les images sélectionnées**. 

        ![Désactiver plusieurs images](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De même, pour **activer** une image de la Place de marché, effectuez l’une des actions suivantes : 
    1. Sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Activer l’image**. 
    2. Sélectionnez une ou plusieurs images dans la liste en cochant la case à côté du nom de l’image, puis sélectionnez **Désactiver les images sélectionnées**. 

## <a name="view-lab-accounts"></a>Afficher les comptes lab
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu, sélectionnez **Toutes les ressources**. 
3. Sélectionnez **Lab Services** comme **type**. 
    Vous pouvez également filtrer par abonnement, par groupe de ressources, par emplacement et par étiquette. 

## <a name="delete-a-lab-account"></a>Supprimer un compte lab
Suivez les instructions de la section précédente, qui permettent d’afficher la liste de comptes lab. Utilisez les instructions suivantes pour supprimer un compte lab : 

1. Sélectionnez le **compte lab** que vous voulez supprimer. 
2. Sélectionnez **Supprimer** dans la barre d’outils. 
3. Tapez **Oui** pour confirmer.
4. Sélectionnez **Supprimer**. 

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



## <a name="next-steps"></a>Étapes suivantes
Commencez à configurer un laboratoire à l’aide d’Azure Lab Services :

- [Configurer un laboratoire de classe](tutorial-setup-classroom-lab.md)
- [Configurer un laboratoire](../tutorial-create-custom-lab.md)
