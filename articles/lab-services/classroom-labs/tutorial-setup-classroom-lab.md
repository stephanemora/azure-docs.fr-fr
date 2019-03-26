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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 31bf2de7417a1be6139de3ec9dcc8d531df586d3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58090319"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutoriel : Configurer un laboratoire de salle de classe 
Dans ce tutoriel, vous allez configurer un laboratoire de classe avec des machines virtuelles utilisées par les étudiants dans la classe.  

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un laboratoire de classe
> * Ajouter des utilisateurs au laboratoire
> * Envoyer un lien d’inscription aux étudiants

## <a name="prerequisites"></a>Prérequis
Pour configurer un laboratoire de classe dans un compte lab, vous devez être membre de l’un des rôles suivants dans le compte lab : Propriétaire, Créateur de laboratoire ou Contributeur. Le compte que vous avez utilisé pour créer un compte lab est automatiquement ajouté au rôle Propriétaire.

Un propriétaire de laboratoire peut ajouter d’autres utilisateurs au rôle **Créateur de laboratoire**. Par exemple, un propriétaire de laboratoire ajoute des professeurs au rôle Créateur de laboratoire. Ensuite, les professeurs créent des laboratoires avec des machines virtuelles pour leurs classes. Les étudiants utilisent le lien d’inscription qu’ils reçoivent des professeurs pour s’inscrire au laboratoire. Une fois inscrits, ils peuvent utiliser des machines virtuelles dans les laboratoires pour faire le travail en classe et leurs devoirs. Pour obtenir des instructions détaillées sur l’ajout d’utilisateurs au rôle Créateur de laboratoire, consultez [Ajouter un utilisateur au rôle Créateur de laboratoire](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Créer un laboratoire de classe

1. Accédez au [site web Azure Lab Services](https://labs.azure.com). 
2. Sélectionnez **Se connecter** et entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft. 
3. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez un **nom** pour votre laboratoire. 
    2. Spécifiez le **nombre de machines virtuelles** maximal dans le laboratoire. Vous pouvez augmenter ou diminuer le nombre de machines virtuelles après avoir créé un lab ou dans un lab existant. Pour plus d’informations, consultez [Mettre à jour le nombre de machines virtuelles d’un lab](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Sélectionnez **Enregistrer**.

        ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Sur la page **Select virtual machine specifications** (sélectionner les spécifications de machine virtuelle), procédez aux étapes suivantes :
    1. Sélectionnez une **taille** pour les machines virtuelles (VM) créées dans le laboratoire. 
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
8. Dans la page **Configurer le modèle**, effectuez les étapes suivantes : Ces étapes sont **facultatives** pour le tutoriel.
    1. Connectez-vous au modèle de machine virtuelle en sélectionnant **Se connecter**. 
    2. Installez et configurez des logiciels sur votre modèle de machine virtuelle.     
    3. Entrez une **description** pour le modèle
9. Sélectionnez **Suivant** sur la page du modèle. 
10. Sur la page **Publier le modèle**, effectuez les actions suivantes. 
    1. Pour publier le modèle immédiatement, sélectionnez **Publier**.  

        > [!WARNING]
        > Une fois que vous publiez, vous ne pouvez pas annuler la publication. 
    2. Pour publier ultérieurement, sélectionnez **Enregistrer pour plus tard**. Vous pouvez publier le modèle de VM une fois l’Assistant terminé. Pour plus d’informations sur la façon de configurer et publier une fois l’Assistant terminé, consultez la section [Publier le modèle](how-to-create-manage-template.md#publish-the-template-vm) dans l’article [Gérer des laboratoires de classe](how-to-manage-classroom-labs.md).

        ![Publier un modèle](../media/tutorial-setup-classroom-lab/publish-template.png)
11. La **progression de la publication** du modèle s’affiche. Ce processus peut prendre jusqu’à une heure. 

    ![Publier un modèle - progression](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. La page suivante s’affiche lorsque le modèle est publié avec succès. Sélectionnez **Terminé**.

    ![Publier un modèle - succès](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Le **tableau de bord** du laboratoire s’affiche. 
    
    ![Tableau de bord de laboratoire de classe](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Passez à la page **Machines virtuelles** en sélectionnant Machines virtuelles dans le menu de gauche, ou en sélectionnant la vignette Machines virtuelles. Vérifiez que des machines virtuelles se trouvent à l’état **Non affectée**. Ces machines virtuelles ne sont pas encore affectées aux étudiants. Elles doivent être à l’état **Arrêtée**. Vous pouvez démarrer la machine virtuelle d’un étudiant, vous y connecter, l’arrêter et la supprimer dans cette page. Vous pouvez démarrer les machines virtuelles dans cette page ou laisser les étudiants le faire. 

    ![Machines virtuelles à l’état Arrêtée](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Ajouter des utilisateurs au laboratoire

1. Sélectionnez **Utilisateurs** dans le menu de gauche. Par défaut, l’option **Restreindre l’accès** est activée. Lorsque ce paramètre est activé, l’utilisateur ne peut pas s’inscrire au laboratoire, même s’il dispose du lien d’inscription. Il ne pourra s’inscrire que s’il figure dans la liste des utilisateurs. Seuls les utilisateurs de la liste peuvent s’inscrire au laboratoire à l’aide du lien d’inscription que vous envoyez. Dans cette procédure, vous allez ajouter des utilisateurs à la liste. Vous pouvez également désactiver l’option **Restreindre l’accès** afin de permettre aux utilisateurs disposant du lien d’inscription de s’inscrire au laboratoire. 
2. Sélectionnez **Ajouter des utilisateurs** dans la barre d’outils. 

    ![Bouton Ajouter des utilisateurs](../media/how-to-configure-student-usage/add-users-button.png)
1. Dans la page **Ajouter des utilisateurs**, entrez les adresses e-mail des utilisateurs sur des lignes distinctes, ou sur une seule ligne en les séparant par des points-virgules. 

    ![Ajouter les adresses e-mail des utilisateurs](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Sélectionnez **Enregistrer**. Dans la liste, vous voyez les adresses e-mail des utilisateurs et leur état (inscrits ou non). 

    ![Liste des utilisateurs](../media/how-to-configure-student-usage/users-list-new.png)


## <a name="send-an-email-with-the-registration-link"></a>Envoyer un e-mail avec le lien d’inscription
1. Basculez vers la vue **Utilisateurs** si vous n’y êtes pas déjà. 
2. Sélectionnez des utilisateurs spécifiques ou tous les utilisateurs dans la liste. Pour sélectionner des utilisateurs spécifiques, activez les cases à cocher dans la première colonne de la liste. Pour sélectionner tous les utilisateurs, sélectionnez la case à cocher devant le titre de la première colonne (**Nom**) ou sélectionnez toutes les cases à cocher pour tous les utilisateurs figurant dans la liste.
3. Sélectionnez **Envoyer une invitation** dans la barre d’outils. Vous pouvez également pointer la souris sur un nom d’étudiant dans la liste, puis envoyer l’icône de courrier électronique. 

    ![Envoyer un lien d’inscription par e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Dans la page **Envoyer un lien d’inscription par e-mail**, procédez comme suit : 
    1. Tapez un **message facultatif** que vous souhaitez envoyer aux étudiants. L’e-mail inclut automatiquement le lien d’inscription. 
    2. Dans la page **Envoyer un lien d’inscription par e-mail**, sélectionnez **Envoyer**. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé un laboratoire de classe et configuré le laboratoire. Pour savoir comment un étudiant peut accéder à une machine virtuelle dans le laboratoire à l’aide du lien d’inscription, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Se connecter à une machine virtuelle dans le laboratoire de classe](tutorial-connect-virtual-machine-classroom-lab.md)

