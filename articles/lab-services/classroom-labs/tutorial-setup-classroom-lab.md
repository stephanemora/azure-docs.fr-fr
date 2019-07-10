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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 803fe6eff8804dbd407642386865fe975c8db524
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123257"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Didacticiel : Configurer un laboratoire de salle de classe 
Dans ce tutoriel, vous allez configurer un laboratoire de classe avec des machines virtuelles utilisées par les étudiants dans la classe.  

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un laboratoire de classe
> * Ajouter des utilisateurs au laboratoire
> * Envoyer un lien d’inscription aux étudiants

## <a name="prerequisites"></a>Prérequis
Pour configurer un lab de classe dans un compte lab, vous devez être membre de l’un des rôles suivants dans le compte lab : Propriétaire, Créateur de laboratoire ou Contributeur. Le compte que vous avez utilisé pour créer un compte lab est automatiquement ajouté au rôle Propriétaire.

Un propriétaire de laboratoire peut ajouter d’autres utilisateurs au rôle **Créateur de laboratoire**. Par exemple, un propriétaire de laboratoire ajoute des professeurs au rôle Créateur de laboratoire. Ensuite, les professeurs créent des laboratoires avec des machines virtuelles pour leurs classes. Les étudiants utilisent le lien d’inscription qu’ils reçoivent des professeurs pour s’inscrire au laboratoire. Une fois inscrits, ils peuvent utiliser des machines virtuelles dans les laboratoires pour faire le travail en classe et leurs devoirs. Pour obtenir des instructions détaillées sur l’ajout d’utilisateurs au rôle Créateur de laboratoire, consultez [Ajouter un utilisateur au rôle Créateur de laboratoire](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Créer un laboratoire de classe

1. Accédez au [site web Azure Lab Services](https://labs.azure.com). Notez qu’Internet Explorer 11 n’est pas encore pris en charge. 
2. Sélectionnez **Se connecter** et entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft. 
3. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez un **nom** pour votre laboratoire. 
    2. Spécifiez le **nombre de machines virtuelles** maximal dans le laboratoire. Vous pouvez augmenter ou diminuer le nombre de machines virtuelles après avoir créé un lab ou dans un lab existant. Pour plus d’informations, consultez [Mettre à jour le nombre de machines virtuelles d’un lab](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Sélectionnez **Enregistrer**.

        ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Sur la page **Select virtual machine specifications** (sélectionner les spécifications de machine virtuelle), procédez aux étapes suivantes :
    1. Sélectionnez une **taille** pour les machines virtuelles (VM) créées dans le laboratoire. Actuellement, les tailles **petite**, **moyenne**, **moyenne (virtualisation)** , **grande** et **GPU** sont autorisées.
    3. Sélectionnez l'**image de machine virtuelle** à utiliser pour créer des VM dans le laboratoire. Si vous sélectionnez une image Linux, vous voyez une option pour activer la connexion Bureau à distance pour celle-ci. Pour plus d’informations, consultez [Activer la connexion Bureau à distance pour Linux](how-to-enable-remote-desktop-linux.md).
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
    2. Connectez-vous au modèle de machine virtuelle en sélectionnant **Se connecter**. S’il s’agit d’un modèle de machine virtuelle Linux, choisissez si vous voulez vous connecter avec SSH ou RDP (si RDP est activé).
    1. Sélectionnez **Réinitialiser le mot de passe** pour réinitialiser le mot de passe pour la machine virtuelle. 
    1. Installez et configurez des logiciels sur votre modèle de machine virtuelle. 
    1. **Arrêtez** la machine virtuelle.  
    1. Entrez une **description** pour le modèle
9. Sélectionnez **Suivant** sur la page du modèle. 
10. Sur la page **Publier le modèle**, effectuez les actions suivantes. 
    1. Pour publier le modèle immédiatement, sélectionnez **Publier**.  

        > [!WARNING]
        > Une fois que vous publiez, vous ne pouvez pas annuler la publication. 
    2. Pour publier ultérieurement, sélectionnez **Enregistrer pour plus tard**. Vous pouvez publier le modèle de machine virtuelle une fois l’Assistant terminé. Pour plus d’informations sur la façon de configurer et de publier une fois l’Assistant terminé, consultez la section [Publier le modèle](how-to-create-manage-template.md#publish-the-template-vm) dans l’article [Gérer des labs de classe](how-to-manage-classroom-labs.md).

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

## <a name="set-quotas-for-users"></a>Définir des quotas pour les utilisateurs
Vous pouvez définir des quotas par utilisateur en effectuant les étapes suivantes : 

1. Sélectionnez **Utilisateurs** dans le menu de gauche si la page n’est pas déjà active. 
2. Sélectionnez **Quota par utilisateur :** dans la barre d’outils. 
3. Sur la page **Quota par utilisateur**, spécifiez le nombre d’heures que vous souhaitez donner à chaque utilisateur (étudiant) : 
    1. **0 heure (heures planifiées uniquement)** . Les utilisateurs peuvent utiliser leurs machines virtuelles uniquement pendant les heures planifiées, ou quand vous, en tant que le propriétaire de laboratoire, activez leurs machines virtuelles.

        ![Zéro heure - uniquement aux heures planifiées](../media/how-to-configure-student-usage/zero-hours.png)
    1. **Nombre total d’heures de laboratoire par utilisateur**. Les utilisateurs peuvent employer leurs machines virtuelles pendant le nombre d'heures défini (spécifié pou ce champ) **en plus de l'heure planifiée**. Si vous sélectionnez cette option, entrez le **nombre d’heures** dans la zone de texte. 

        ![Nombre d’heures par utilisateur](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Sélectionnez **Enregistrer**. 
5. Les valeurs modifiées figurent maintenant dans la barre d’outils : **Quota par utilisateur : &lt;nombre d’heures&gt;** . 

    ![Quota par utilisateur](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>Définir un calendrier pour le laboratoire
Si vous avez configuré le paramètre de quota sur **0 heure (heures planifiées uniquement)** , vous devez définir un calendrier pour le laboratoire. Dans ce didacticiel, vous définissez le calendrier sous forme de planification hebdomadaire.

1. Basculez vers la page **Planifications**, puis, dans la barre d’outils, sélectionnez **Ajouter une planification**. 

    ![Bouton Ajouter une planification dans la page Planifications](../media/how-to-create-schedules/add-schedule-button.png)
2. Dans la page **Ajouter une planification**, basculez vers **Hebdomadaire** en haut. 
3. Pour **Schedule days (required)** (Planifier des jours (obligatoire)), sélectionnez les jours auxquels vous voulez appliquer la planification. Dans l’exemple suivant, l’option Monday-Friday (Lundi-vendredi) est sélectionnée. 
4. Dans le champ **De**, entrez la **date de début de la planification** ou choisissez une date en sélectionnant le bouton **Calendrier**. Ce champ est obligatoire. 
5. Pour **Schedule end date** (Planifier une date de fin), entrez ou sélectionnez une date de fin à laquelle les machines virtuelles devront être arrêtées. 
6. Pour **Start time** (Heure de démarrage), sélectionnez l’heure à laquelle vous voulez que les machines virtuelles démarrent. L’heure de démarrage est obligatoire si l’heure d’arrêt n’est pas définie. Sélectionnez **Remove start event** (Supprimer l’événement de démarrage) si vous souhaitez spécifier uniquement l’heure d’arrêt. Si l’option **Start time** (Heure de démarrage) est désactivée, sélectionnez **Add start event** (Ajouter un événement de démarrage) en regard de la liste déroulante pour l’activer. 
7. Pour **Stop time** (Heure de l’arrêt), sélectionnez l’heure à laquelle vous souhaitez que les machines virtuelles s’arrêtent. L’heure d’arrêt est obligatoire si l’heure de démarrage n’est pas définie. Sélectionnez **Remove stop event** (Supprimer l’événement d’arrêt) si vous souhaitez spécifier uniquement l’heure de démarrage. Si l’option **Stop time** (Heure d’arrêt) est désactivée, sélectionnez **Add stop event** (Ajouter un événement d’arrêt) en regard de la liste déroulante pour l’activer.
8. Pour **Time zone (required)** (Fuseau horaire (obligatoire)), sélectionnez le fuseau horaire pour les heures de démarrage et d’arrêt que vous avez spécifiées.  
9. Pour **Notes**, entrez une description ou des notes pour la planification. 
10. Sélectionnez **Enregistrer**. 

    ![Planification hebdomadaire](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>Envoyer un e-mail avec le lien d’inscription

1. Basculez vers la vue **Utilisateurs** si vous n’y êtes pas déjà. 
2. Sélectionnez des utilisateurs spécifiques ou tous les utilisateurs dans la liste. Pour sélectionner des utilisateurs spécifiques, activez les cases à cocher dans la première colonne de la liste. Pour sélectionner tous les utilisateurs, sélectionnez la case à cocher devant le titre de la première colonne (**Nom**) ou sélectionnez toutes les cases à cocher pour tous les utilisateurs figurant dans la liste. Vous pouvez voir l’**état de l’invitation** dans cette liste.  Dans l’image suivante, l’état de l’invitation pour tous les étudiants est défini sur **Invitation non envoyée**. 

    ![Sélectionner des étudiants](../media/tutorial-setup-classroom-lab/select-students.png)
1. Sélectionnez l’**icône d’e-mail (une enveloppe)** dans une des lignes (ou) sélectionnez **Envoyer une invitation** sur la barre d’outils. Vous pouvez également pointer la souris sur un nom d’étudiant dans la liste pour voir l’icône d’e-mail. 

    ![Envoyer un lien d’inscription par e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Dans la page **Envoyer un lien d’inscription par e-mail**, procédez comme suit : 
    1. Tapez un **message facultatif** que vous souhaitez envoyer aux étudiants. L’e-mail inclut automatiquement le lien d’inscription. 
    2. Dans la page **Envoyer un lien d’inscription par e-mail**, sélectionnez **Envoyer**. Vous voyez l’état de l’invitation passer à **Envoi d’une invitation**, puis à **Invitation envoyée**. 
        
        ![Invitations envoyées](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé un laboratoire de classe et configuré le laboratoire. Pour savoir comment un étudiant peut accéder à une machine virtuelle dans le laboratoire à l’aide du lien d’inscription, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Se connecter à une machine virtuelle dans le laboratoire de classe](tutorial-connect-virtual-machine-classroom-lab.md)

