---
title: Configurer un laboratoire de classe à l’aide d’Azure Lab Services | Microsoft Docs
description: Dans ce didacticiel, vous allez configurer un laboratoire à utiliser dans une classe.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/05/2018
ms.author: spelluru
ms.openlocfilehash: 6696d6e7e53e98dfab2a65c7c66825936020f33c
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856632"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutoriel : Configurer un laboratoire de classe 
Dans ce tutoriel, vous allez configurer un laboratoire de classe avec des machines virtuelles utilisées par les étudiants dans la classe.  

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un laboratoire de classe
> * Configurer le laboratoire de classe
> * Envoyer un lien d’inscription aux étudiants

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
    2. Pour publier ultérieurement, sélectionnez **Enregistrer pour plus tard**. Vous pouvez publier le modèle de VM une fois l’Assistant terminé. Pour plus d’informations sur la façon de configurer et publier une fois l’Assistant terminé, consultez la section [Publier le modèle](how-to-manage-classroom-labs.md#publish-the-template) dans l’article [Gérer des laboratoires de classe](how-to-manage-classroom-labs.md).

        ![Publier un modèle](../media/tutorial-setup-classroom-lab/publish-template.png)
11. La **progression de la publication** du modèle s’affiche. Ce processus peut prendre jusqu’à une heure. 

    ![Publier un modèle - progression](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. La page suivante s’affiche lorsque le modèle est publié avec succès. Sélectionnez **Terminé**.

    ![Publier un modèle - succès](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Le **tableau de bord** du laboratoire s’affiche. 
    
    ![Tableau de bord de laboratoire de classe](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Passez à la page **Machines virtuelles** et vérifiez que des machines virtuelles se trouvent à l’état **Non affectée**. Ces machines virtuelles ne sont pas encore affectées aux étudiants. Elles doivent être à l’état **Arrêtée**. Vous pouvez démarrer la machine virtuelle d’un étudiant, vous y connecter, l’arrêter et la supprimer dans cette page. Vous pouvez démarrer les machines virtuelles dans cette page ou laisser les étudiants le faire. 

    ![Machines virtuelles à l’état Arrêtée](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>Envoyer un lien d’inscription aux étudiants

1. Passez à la vue **Tableau de bord** en sélectionnant **Tableau de bord** dans le menu de gauche. 
2. Sélectionnez la vignette **Enregistrement de l’utilisateur**.

    ![Lien d’inscription de l’étudiant](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez le bouton **Copier**. Le lien est copié dans le Presse-papiers. Collez-le dans un éditeur d’e-mail et envoyez un e-mail à l’étudiant. 

    ![Lien d’inscription de l’étudiant](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez **Fermer**. 
3. Partagez le lien d’inscription avec un étudiant afin que ce dernier puisse s’inscrire à la classe. 


## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé un laboratoire de classe et configuré le laboratoire. Pour savoir comment un étudiant peut accéder à une machine virtuelle dans le laboratoire à l’aide du lien d’inscription, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Se connecter à une machine virtuelle dans le laboratoire de classe](tutorial-connect-virtual-machine-classroom-lab.md)

