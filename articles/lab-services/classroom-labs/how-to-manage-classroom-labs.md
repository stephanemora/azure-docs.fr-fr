---
title: Gérer des laboratoires de classe dans Azure Lab Services | Microsoft Docs
description: Découvrez comment créer et configurer un laboratoire de classe, voir tous les laboratoires de classe, partager le lien d’inscription avec un autre utilisateur du laboratoire et supprimer un laboratoire.
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
ms.openlocfilehash: 705209becff7c8ad20e7d09f056aa1ae1577b7ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659487"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gérer des laboratoires de classe dans Azure Lab Services 
Cet article explique comment créer et configurer un laboratoire de classe, voir tous les laboratoires de classe et supprimer un laboratoire.

## <a name="create-a-classroom-lab"></a>Créer un laboratoire de classe

1. Accédez au [site web Azure Lab Services](https://labs.azure.com).
2. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez le **nom** du laboratoire de classe. 
    2. Sélectionnez la **taille** de la machine virtuelle que vous prévoyez d’utiliser dans la classe.
    3. Sélectionnez **l’image** à utiliser pour créer la machine virtuelle.
    4. Spécifiez les **informations d’identification par défaut** à utiliser pour vous connecter à des machines virtuelles dans le laboratoire.
    7. Sélectionnez **Enregistrer**.

        ![Créer un laboratoire de classe](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. La **page d’accueil** du laboratoire apparaît. 
    
    ![Page d’accueil du laboratoire de classe](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configurer la politique d’utilisation

1. Sélectionnez **Politique d’utilisation**. 
2. Dans **Politique d’utilisation**, puis dans les paramètres, entrez le **nombre d’utilisateurs** autorisés à utiliser le laboratoire.
3. Sélectionnez **Enregistrer**. 

    ![Politique d’utilisation](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Configurer le modèle
Le modèle de laboratoire est une image de machine virtuelle de base, à partir de laquelle toutes les machines virtuelles des utilisateurs sont créées. Configurez la machine virtuelle du modèle de façon qu’elle propose exactement ce que vous souhaitez fournir aux utilisateurs du laboratoire. Vous pouvez indiquer le nom et la description du modèle que voient les utilisateurs du laboratoire. Définissez la visibilité du modèle au public pour rendre les instances du modèle de machine virtuelle disponible aux utilisateurs de votre laboratoire.  

### <a name="set-template-title-and-description"></a>Définir un titre et une description pour le modèle
1. Dans la section **Modèle**, sélectionnez **Modifier** (icône de crayon) en regard du modèle. 
2. Dans la fenêtre **Vue Utilisateur**, entrez un **titre** pour le modèle.
3. Entrez une **description** pour le modèle.
4. Sélectionnez **Enregistrer**.

    ![Description du laboratoire de classe](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Rendre publiques les instances du modèle 
Une fois que vous avez défini la visibilité d’un modèle sur **Publique**, Azure Lab Services crée des machines virtuelles dans le laboratoire à l’aide du modèle. Le nombre de machines virtuelles créées dans ce processus est identique au nombre maximal d’utilisateurs autorisés dans le laboratoire, que vous pouvez définir dans la politique d’utilisation du laboratoire. Toutes les machines virtuelles ont la même configuration que le modèle.  

1. Sélectionnez **Visibilité** dans la section **Modèle**. 
2. Dans la page **Disponibilité**, sélectionnez **Publique**.
    
    > [!IMPORTANT]
    > Une fois que vous avez défini un modèle comme étant public, vous ne pouvez plus rendre son accès privé. 
3. Sélectionnez **Enregistrer**.

    ![Disponibilité](../media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Envoyer un lien d’inscription aux étudiants

1. Sélectionnez la vignette **Enregistrement de l’utilisateur**.
2. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez le bouton **Copier**. Le lien est copié dans le Presse-papiers. Collez-le dans un éditeur d’e-mail et envoyez un e-mail à l’étudiant. 

    ![Lien d’inscription de l’étudiant](../media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Afficher tous les laboratoires de classe
1. Accédez au [portail Azure Lab Services](https://labs.azure.com).
2. Vérifiez que tous les laboratoires se trouvent dans le compte de laboratoire sélectionné. 

    ![Tous les laboratoires](../media/how-to-manage-classroom-labs/all-labs.png)
3. Utilisez la liste déroulante du haut pour sélectionner un autre compte de laboratoire. Vous voyez les laboratoires du compte de laboratoire sélectionné. 

## <a name="delete-a-classroom-lab"></a>Supprimer un laboratoire de classe
1. Sur la vignette du laboratoire, sélectionnez les points de suspension (...) situés dans l’angle. 

    ![Sélectionner le laboratoire](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Sélectionnez **Supprimer**. 

    ![Bouton Supprimer](../media/how-to-manage-classroom-labs/delete-button.png)
3. Dans la boîte de dialogue **Delete lab** (Supprimer le laboratoire), sélectionnez **Supprimer**. 

    ![Supprimer la boîte de dialogue](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>Étapes suivantes
Commencez à configurer un laboratoire à l’aide d’Azure Lab Services :

- [Configurer un laboratoire de classe](how-to-manage-classroom-labs.md)
- [Configurer un laboratoire](../tutorial-create-custom-lab.md)
