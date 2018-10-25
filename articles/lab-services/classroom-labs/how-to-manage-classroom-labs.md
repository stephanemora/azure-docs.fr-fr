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
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: eebdc98db5ecdf518d3b0b58e6757a2b7ecd5dd7
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043802"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gérer des laboratoires de classe dans Azure Lab Services 
Cet article explique comment créer et configurer un laboratoire de classe, voir tous les laboratoires de classe et supprimer un laboratoire.

## <a name="prerequisites"></a>Prérequis
Pour configurer un laboratoire de classe dans un compte de laboratoire, vous devez être membre du rôle **Créateur de laboratoire** dans le compte de laboratoire. Le compte utilisé pour créer un compte de laboratoire est ajouté automatiquement à ce rôle. Un propriétaire de laboratoire peut ajouter d’autres utilisateurs au rôle Créateur de laboratoire à l’aide des étapes fournies dans l’article suivant : [Ajouter un utilisateur au rôle Créateur de laboratoire](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Créer un laboratoire de classe

1. Accédez au [site web Azure Lab Services](https://labs.azure.com). 
2. Sélectionnez **Se connecter** et entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft. 
3. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez un **nom** pour votre laboratoire. 
    2. Spécifiez le **nombre d’utilisateurs** maximal autorisé dans le laboratoire. 
    6. Sélectionnez **Enregistrer**.

        ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Sur la page **Select virtual machine specifications** (sélectionner les spécifications de machine virtuelle), procédez aux étapes suivantes :
    1. Sélectionnez une **taille** pour les machines virtuelles (VM) créées dans le laboratoire. 
    2. Sélectionnez la **région** dans laquelle vous souhaitez créer les VM. 
    3. Sélectionnez l'**image de machine virtuelle** à utiliser pour créer des VM dans le laboratoire. 
    4. Sélectionnez **Suivant**.

        ![Définir les spécifications de VM](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Sur la page **Définir les informations d'identification**, spécifiez les informations d’identification par défaut pour toutes les VM dans le laboratoire. 
    1. Spécifiez le **nom d’utilisateur** pour toutes les VM du laboratoire.
    2. Spécifiez le **mot de passe** de l’utilisateur. 

        > [!IMPORTANT]
        > Notez le nom d'utilisateur et le mot de passe que vous créez. Ils ne s’afficheront plus.
    3. Sélectionnez **Créer**. 

        ![Définition des informations d’identification](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Sur la page **Configurer le modèle**, vous pouvez consulter l’état du processus de création de laboratoire. La création du modèle dans le laboratoire prend jusqu'à 20 minutes. 

    ![Configurer le modèle](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Une fois la configuration du modèle terminée, la page suivante s’affiche : 

    ![Page Configurer le modèle une fois terminé](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Les étapes suivantes sont facultatives dans ce didacticiel : 
    1. Démarrez le modèle de machine virtuelle en sélectionnant **Démarrer**.
    2. Connectez-vous au modèle de machine virtuelle en sélectionnant **Se connecter**. 
    3. Installez et configurez des logiciels sur votre modèle de machine virtuelle. 
    4. **Arrêtez** la machine virtuelle.  
    5. Entrez une **description** pour le modèle

        ![Suivant sur la page Configurer le modèle](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Sélectionnez **Suivant** sur la page du modèle. 
10. Sur la page **Publier le modèle**, effectuez les actions suivantes. 
    1. Pour publier le modèle immédiatement, cochez la case *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Je comprends que je ne peux pas modifier le modèle après publication. Ce processus ne peut être fait qu’une fois et cela peut prendre jusqu’à une heure), puis sélectionnez **Publier**.  

        > [!WARNING]
        > Une fois que vous publiez, vous ne pouvez pas annuler la publication. 
    2. Pour publier ultérieurement, sélectionnez **Enregistrer pour plus tard**. Vous pouvez publier le modèle de VM une fois l’Assistant terminé. Pour plus d’informations sur la configuration et la publication après l’exécution de l’assistant, consultez la section [Publier le modèle](#publish-the-template) de l’article [Gérer des laboratoires de classe dans Azure Lab Services](how-to-manage-classroom-labs.md).

        ![Publier un modèle](../media/tutorial-setup-classroom-lab/publish-template.png)
11. La **progression de la publication** du modèle s’affiche. Ce processus peut prendre jusqu’à une heure. 

    ![Publier un modèle - progression](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. La page suivante s’affiche lorsque le modèle est publié avec succès. Sélectionnez **Terminé**.

    ![Publier un modèle - succès](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Le **tableau de bord** du laboratoire s’affiche. 
    
    ![Tableau de bord de laboratoire de classe](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Passez à la page **Machines virtuelles** et vérifiez que des machines virtuelles se trouvent à l’état **Non affectée**. Ces machines virtuelles ne sont pas encore affectées aux étudiants. Elles doivent être à l’état **Arrêtée**. Vous pouvez démarrer la machine virtuelle d’un étudiant, vous y connecter, l’arrêter et la supprimer dans cette page. Vous pouvez démarrer les machines virtuelles dans cette page ou laisser les étudiants le faire. 

    ![Machines virtuelles à l’état Arrêtée](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="configure-usage-policy"></a>Configurer la politique d’utilisation

1. Sélectionnez **Politique d’utilisation**. 
2. Dans **Politique d’utilisation**, puis dans les paramètres, entrez le **nombre d’utilisateurs** autorisés à utiliser le laboratoire.
3. Sélectionnez **Enregistrer**. 

    ![Politique d’utilisation](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Configurer le modèle
Le modèle de laboratoire est une image de machine virtuelle de base, à partir de laquelle toutes les machines virtuelles des utilisateurs sont créées. Configurez la machine virtuelle du modèle de façon qu’elle propose exactement ce que vous souhaitez fournir aux utilisateurs du laboratoire. Vous pouvez indiquer le nom et la description du modèle que voient les utilisateurs du laboratoire. Publiez le modèle pour rendre les instances du modèle de machine virtuelle accessible aux utilisateurs de votre laboratoire.  

### <a name="set-template-title-and-description"></a>Définir un titre et une description pour le modèle
1. Dans la section **Modèle**, sélectionnez **Modifier** (icône de crayon) en regard du modèle. 
2. Dans la fenêtre **Vue Utilisateur**, entrez un **titre** pour le modèle.
3. Entrez une **description** pour le modèle.
4. Sélectionnez **Enregistrer**.

    ![Description du laboratoire de classe](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>Configurer le modèle de machine virtuelle
 Vous vous connectez au modèle de machine virtuelle et vous installez les logiciels nécessaires avant de les rendre accessibles à vos étudiants. 

1. Attendez que la machine virtuelle soit prête. Une fois qu’elle est prête, le bouton **Démarrer** doit être activé. Pour démarrer la machine virtuelle, sélectionnez **Démarrer**.

    ![Démarrer le modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Pour vous connecter à la machine virtuelle, sélectionnez **Se connecter**, puis suivez les instructions. 

    ![Se connecter au modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Installez les logiciels nécessaires pour que les étudiants puissent travailler en laboratoire (par exemple Visual Studio, l’Explorateur Stockage Azure, et ainsi de suite). 
2. Déconnectez-vous (fermez votre session Bureau à distance) du modèle de machine virtuelle. 
3. **Arrêtez** le modèle de machine virtuelle en sélectionnant **Arrêter**. 

    ![Arrêter le modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


## <a name="publish-the-template"></a>Publier le modèle 
Quand vous publiez un modèle, Azure Lab Services crée les machines virtuelles dans le laboratoire à l’aide du modèle. Le nombre de machines virtuelles créées dans ce processus est identique au nombre maximal d’utilisateurs autorisés dans le laboratoire, que vous pouvez définir dans la politique d’utilisation du laboratoire. Toutes les machines virtuelles ont la même configuration que le modèle. 

1. Sélectionnez **Publier** dans la section **Modèle**. 

    ![Publier le modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/public-access.png)
1. Dans la fenêtre **Publier**, sélectionnez l’option **Publié**. 
2. Cliquez maintenant sur le bouton **Publier**. Ce processus peut prendre un certain temps en fonction du nombre de machines virtuelles en cours de création, qui est identique au nombre d’utilisateurs autorisés dans le laboratoire.
    
    > [!IMPORTANT]
    > Une fois un modèle publié, sa publication ne peut pas être annulée. 
4. Passez à la page **Machines virtuelles** et vérifiez que des machines virtuelles se trouvent à l’état **Non affectée**. Ces machines virtuelles ne sont pas encore affectées aux étudiants. 

    ![Machines virtuelles](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Attendez que les machines virtuelles soient créées. Elles doivent être à l’état **Arrêtée**. Vous pouvez démarrer la machine virtuelle d’un étudiant, vous y connecter, l’arrêter et la supprimer dans cette page. Vous pouvez démarrer les machines virtuelles dans cette page ou laisser les étudiants le faire. 

    ![Machines virtuelles à l’état Arrêtée](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="send-registration-link-to-students"></a>Envoyer un lien d’inscription aux étudiants

1. Basculez vers la vue **Tableau de bord**. 
2. Sélectionnez la vignette **Enregistrement de l’utilisateur**.

    ![Lien d’inscription de l’étudiant](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez le bouton **Copier**. Le lien est copié dans le Presse-papiers. Collez-le dans un éditeur d’e-mail et envoyez un e-mail à l’étudiant. 

    ![Lien d’inscription de l’étudiant](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez **Fermer**. 

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
 
## <a name="manage-student-vms"></a>Gérer les machines virtuelles des étudiants
Une fois que les étudiants s’inscrivent avec Azure Lab Services à l’aide du lien d’enregistrement que vous leur avez fourni, vous voyez les machines virtuelles assignées aux étudiants sur l’onglet **machines virtuelles**. 

![Ordinateurs virtuels assignées aux étudiants](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Vous pouvez effectuer les tâches suivantes sur une machine virtuelle d’étudiant : 

- Arrêter une machine virtuelle si elle est en cours d’exécution. 
- Démarrer une machine virtuelle si elle est arrêté. 
- Connectez-vous à la machine virtuelle. 
- Supprimez la machine virtuelle. 

## <a name="next-steps"></a>Étapes suivantes
Commencez à configurer un laboratoire à l’aide d’Azure Lab Services :

- [Configurer un laboratoire de classe](how-to-manage-classroom-labs.md)
- [Configurer un laboratoire](../tutorial-create-custom-lab.md)
