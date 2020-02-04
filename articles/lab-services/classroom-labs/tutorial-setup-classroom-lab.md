---
title: Configurer un laboratoire de classe à l’aide d’Azure Lab Services | Microsoft Docs
description: Dans ce tutoriel, vous allez utiliser Azure Lab Services pour configurer un laboratoire de classe composé de machines virtuelles utilisées par les étudiants de votre classe.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2c28375ce7252e93340f395b97224d292940ce65
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719175"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutoriel : Configurer un laboratoire de salle de classe 
Dans ce tutoriel, vous allez configurer un laboratoire de classe avec des machines virtuelles utilisées par les étudiants dans la classe.  

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un laboratoire de classe
> * Ajouter des utilisateurs au laboratoire
> * Définir un calendrier pour le labo
> * Envoyer un e-mail d’invitation aux étudiants

## <a name="prerequisites"></a>Conditions préalables requises
Pour configurer un lab de classe dans un compte lab, vous devez être membre de l’un des rôles suivants dans le compte lab : Propriétaire, Créateur de laboratoire ou Contributeur. Le compte que vous avez utilisé pour créer un compte lab est automatiquement ajouté au rôle Propriétaire.

Un propriétaire de laboratoire peut ajouter d’autres utilisateurs au rôle **Créateur de laboratoire**. Par exemple, un propriétaire de laboratoire ajoute des professeurs au rôle Créateur de laboratoire. Ensuite, les professeurs créent des laboratoires avec des machines virtuelles pour leurs classes. Les étudiants utilisent le lien d’inscription qu’ils reçoivent des professeurs pour s’inscrire au laboratoire. Une fois inscrits, ils peuvent utiliser des machines virtuelles dans les laboratoires pour faire le travail en classe et leurs devoirs. Pour obtenir des instructions détaillées sur l’ajout d’utilisateurs au rôle Créateur de laboratoire, consultez [Ajouter un utilisateur au rôle Créateur de laboratoire](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Créer un laboratoire de classe

1. Accédez au [site web Azure Lab Services](https://labs.azure.com). Notez qu’Internet Explorer 11 n’est pas encore pris en charge. 
2. Sélectionnez **Se connecter** et entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft. 
3. Sélectionnez **Nouveau labo**. 
    
    ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez un **nom** pour votre labo et sélectionnez **Suivant**.  

        ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        Si vous sélectionnez une image Linux, vous voyez une option pour activer la connexion Bureau à distance pour celle-ci. Pour plus d’informations, consultez [Activer la connexion Bureau à distance pour Linux](how-to-enable-remote-desktop-linux.md).
    2. Dans la page **Informations d’identification de machine virtuelle**, spécifiez les informations d’identification par défaut pour toutes les machines virtuelles du labo. Spécifiez le **nom** et le **mot de passe** de l’utilisateur, puis sélectionnez **Suivant**.  

        ![Fenêtre Nouveau labo](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Notez le nom d'utilisateur et le mot de passe que vous créez. Ils ne s’afficheront plus.
    3. Dans la page **Stratégies du labo**, entrez le nombre d’heures allouées à chaque utilisateur (**quota pour chaque utilisateur**) en dehors des heures planifiées pour le labo, puis sélectionnez **Terminer**. 

        ![Quota pour chaque utilisateur](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Vous devez voir l’écran suivant qui indique l’état de la création du modèle de machine virtuelle. La création du modèle dans le laboratoire prend jusqu'à 20 minutes. 

    ![État de la création du modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Dans la page **Modèle**, effectuez les étapes suivantes : Ces étapes sont **facultatives** pour le tutoriel.

    2. Connectez-vous au modèle de machine virtuelle en sélectionnant **Se connecter**. S’il s’agit d’un modèle de machine virtuelle Linux, choisissez si vous voulez vous connecter avec SSH ou RDP (si RDP est activé).
    1. Sélectionnez **Réinitialiser le mot de passe** pour réinitialiser le mot de passe pour la machine virtuelle. 
    1. Installez et configurez des logiciels sur votre modèle de machine virtuelle. 
    1. **Arrêtez** la machine virtuelle.  
    1. Entrez une **description** pour le modèle
10. Dans la page **Modèle**, sélectionnez **Publier** dans la barre d’outils. 

    ![Bouton Publier le modèle](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Une fois que vous publiez, vous ne pouvez pas annuler la publication. 
8. Dans la page **Publier le modèle**, entrez le nombre de machines virtuelles que vous souhaitez créer dans le labo, puis sélectionnez **Publier**. 

    ![Publier le modèle - nombre de machines virtuelles](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Vous pouvez voir l’**état de publication** du modèle sur la page. Ce processus peut prendre jusqu’à une heure. 

    ![Publier un modèle - progression](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Passez à la page **Pool de machines virtuelles** en sélectionnant Machines virtuelles dans le menu de gauche ou en sélectionnant la vignette Machines virtuelles. Vérifiez que des machines virtuelles se trouvent à l’état **Non affectée**. Ces machines virtuelles ne sont pas encore affectées aux étudiants. Elles doivent être à l’état **Arrêtée**. Vous pouvez démarrer la machine virtuelle d’un étudiant, vous y connecter, l’arrêter et la supprimer dans cette page. Vous pouvez démarrer les machines virtuelles dans cette page ou laisser les étudiants le faire. 

    ![Machines virtuelles à l’état Arrêtée](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Vous effectuerez les tâches suivantes dans cette page, mais pas dans le cadre de ce tutoriel. Elles sont fournies ici à titre indicatif uniquement : 
    
    1. Pour changer la capacité du labo (nombre de machines virtuelles dans le labo), sélectionnez **Capacité du labo** dans la barre d’outils.
    2. Pour démarrer toutes les machines virtuelles en même temps, sélectionnez **Démarrer tout** dans la barre d’outils. 
    3. Pour démarrer une machine virtuelle spécifique, sélectionnez la flèche vers le bas sous **État**, puis sélectionnez **Démarrer**. Vous pouvez également démarrer une machine virtuelle en la sélectionnant dans la première colonne, puis en sélectionnant **Démarrer** dans la barre d’outils.

    Pour plus d’informations sur la création et la gestion de modèles, ainsi que sur la configuration et la gestion des machines virtuelles pour les étudiants, consultez les articles suivants : 
    
    - [Créer et gérer les modèles de laboratoire de classe](how-to-create-manage-template.md)
    - [Configurer et gérer un pool de machines virtuelles](how-to-set-virtual-machine-passwords.md)

## <a name="add-users-to-the-lab"></a>Ajouter des utilisateurs au laboratoire

1. Sélectionnez **Utilisateurs** dans le menu de gauche. Par défaut, l’option **Restreindre l’accès** est activée. Lorsque ce paramètre est activé, l’utilisateur ne peut pas s’inscrire au laboratoire, même s’il dispose du lien d’inscription. Il ne pourra s’inscrire que s’il figure dans la liste des utilisateurs. Seuls les utilisateurs de la liste peuvent s’inscrire au laboratoire à l’aide du lien d’inscription que vous envoyez. Dans cette procédure, vous allez ajouter des utilisateurs à la liste. Vous pouvez également désactiver l’option **Restreindre l’accès** afin de permettre aux utilisateurs disposant du lien d’inscription de s’inscrire au laboratoire. 
2. Sélectionnez **Ajouter des utilisateurs**  dans la barre d’outils, puis **Ajouter par adresse e-mail**. 

    ![Bouton Ajouter des utilisateurs](../media/how-to-configure-student-usage/add-users-button.png)
1. Dans la page **Ajouter des utilisateurs**, entrez les adresses e-mail des utilisateurs sur des lignes distinctes, ou sur une seule ligne en les séparant par des points-virgules. 

    ![Ajouter les adresses e-mail des utilisateurs](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Sélectionnez **Enregistrer**. Dans la liste, vous voyez les adresses e-mail des utilisateurs et leur état (inscrits ou non). 

    ![Liste des utilisateurs](../media/how-to-configure-student-usage/users-list-new.png)

    Les noms des utilisateurs s’affichent dans la liste après leur inscription au laboratoire. 
    
## <a name="set-a-schedule-for-the-lab"></a>Définir un calendrier pour le laboratoire
Créez un événement planifié pour le labo de sorte que les machines virtuelles du labo démarrent ou s’arrêtent automatiquement à des moments précis. Le quota utilisateur que vous avez spécifié précédemment est le temps supplémentaire attribué à chaque utilisateur en dehors de ces heures planifiées. 

1. Basculez vers la page **Planifications**, puis, dans la barre d’outils, sélectionnez **Ajouter un événement planifié**. 

    ![Bouton Ajouter une planification dans la page Planifications](../media/how-to-create-schedules/add-schedule-button.png)
2. Dans la page **Ajouter un événement planifié**, effectuez les étapes suivantes :
    1. Vérifiez que **Standard** est sélectionné pour **Type d’événement**.  
    2. Spécifiez la **date de démarrage** pour la classe. 
    4. Spécifiez l’**heure de démarrage** des machines virtuelles démarrent.
    5. Spécifiez l’**heure d’arrêt** des machines virtuelles. 
    6. Spécifiez le **fuseau horaire** pour les heures de démarrage et d’arrêt que vous avez spécifiées. 
3. Dans la même page **Ajouter un événement planifié**, sélectionnez la planification active à la section **Répéter**.  

    ![Bouton Ajouter une planification dans la page Planifications](../media/how-to-create-schedules/select-current-schedule.png)
5. Dans la boîte de dialogue **Répéter**, effectuez les étapes suivantes :
    1. Vérifiez que le champ **Répéter** est défini sur **chaque semaine**. 
    2. Sélectionnez les jours auxquels vous voulez appliquer la planification. Dans l’exemple suivant, l’option Monday-Friday (Lundi-vendredi) est sélectionnée. 
    3. Sélectionnez une **date de fin** pour la planification.
    8. Sélectionnez **Enregistrer**. 

        ![Définir la répétition de la planification](../media/how-to-create-schedules/set-repeat-schedule.png)

3. À présent, dans la page **Ajouter un événement planifié**, sous **Notes (facultatif)** , entrez une description ou des remarques pour la planification. 
4. Dans la page **Ajouter un événement planifié**, sélectionnez **Enregistrer**. 

    ![Planification hebdomadaire](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Accédez à la date de démarrage dans le calendrier pour vérifier que la planification y est configurée.
    
    ![Planification dans le calendrier](../media/how-to-create-schedules/schedule-calendar.png)

    Pour plus d’informations sur la création et la gestion des planifications d’une classe, consultez [Créer et gérer une planification pour les laboratoires de classe](how-to-create-schedules.md).

## <a name="send-invitation-emails-to-students"></a>Envoyer des e-mails d’invitation aux étudiants

1. Basculez vers la vue **Utilisateurs** si vous n’y êtes pas déjà. Sélectionnez **Inviter tout le monde** dans la barre d’outils. 

    ![Sélectionner des étudiants](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Dans la page **Envoyer l’invitation par e-mail**, entrez un message si vous le souhaitez, puis sélectionnez **Envoyer**. L’e-mail inclut automatiquement le lien d’inscription. Pour obtenir ce lien d’inscription, sélectionnez **...** (points de suspension) dans la barre d’outils, puis sélectionnez **Lien d’inscription**. 

    ![Envoyer un lien d’inscription par e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. L’état de l’**invitation**  est indiqué dans la liste **Utilisateurs**. L’état doit passer à **Envoi**, puis à **Envoyée le &lt;date&gt;** . 

    Pour plus d’informations sur l’ajout d’élèves à une classe et sur la gestion de leur utilisation du laboratoire, consultez [Comment configurer l’utilisation des étudiants](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé un laboratoire de classe et configuré le laboratoire. Pour savoir comment un étudiant peut accéder à une machine virtuelle dans le laboratoire à l’aide du lien d’inscription, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Se connecter à une machine virtuelle dans le laboratoire de classe](tutorial-connect-virtual-machine-classroom-lab.md)

