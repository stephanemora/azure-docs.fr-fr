---
title: Configurer des paramètres d’utilisation dans les labos de classe Azure Lab Services | Microsoft Docs
description: Découvrez comment configurer le nombre d’utilisateurs d’un labo, comment les inscrire au labo, comment contrôler le nombre d’heures qu’ils peuvent utiliser la machine virtuelle, et bien plus encore.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 494c285f1c096a84925d9d9a4fb98409960e5230
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650078"
---
# <a name="configure-usage-settings-and-policies"></a>Configurer des paramètres et des stratégies d’utilisation
Cet article explique comment ajouter des utilisateurs au labo, comment les inscrire au labo, comment contrôler le nombre d’heures qu’ils peuvent utiliser la machine virtuelle, et bien plus encore. 


## <a name="add-users-to-the-lab"></a>Ajouter des utilisateurs au laboratoire
Si l’option **Restreindre l’accès** est activée, ajoutez des utilisateurs (adresses e-mail) à la liste.

1. Sélectionnez **Utilisateurs** dans le menu de gauche.
2. Sélectionnez **Ajouter des utilisateurs** dans la barre d’outils. 

    ![Bouton Ajouter des utilisateurs](../media/how-to-configure-student-usage/add-users-button.png)
1. Dans la page **Ajouter des utilisateurs**, entrez les adresses e-mail des utilisateurs sur des lignes distinctes, ou sur une seule ligne en les séparant par des points-virgules. 

    ![Ajouter les adresses e-mail des utilisateurs](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Sélectionnez **Enregistrer**. Dans la liste, vous voyez les adresses e-mail des utilisateurs et leur état (inscrits ou non). 

    ![Liste des utilisateurs](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>Partager un lien d’inscription avec les étudiants
Pour envoyer le lien d’inscription aux étudiants, utilisez une des méthodes suivantes. La première méthode vous montre comment envoyer des courriers électroniques aux étudiants avec le lien d’inscription et un message facultatif. La deuxième méthode vous montre comment obtenir le lien d’inscription que vous pouvez partager avec d’autres personnes comme vous le souhaitez. 

Si l’option **Restreindre l’accès** est activée pour le labo, seuls les utilisateurs figurant dans la liste des utilisateurs peuvent utiliser le lien d’inscription pour s’inscrire au labo. Cette option est activée par défaut. 

### <a name="send-email-to-users"></a>Envoyer un e-mail aux utilisateurs
Azure Lab Services permet aux enseignants d’invitations de laboratoire à l’ensemble de la messagerie ou sélectionné les étudiants sans avoir à utiliser un autre client de messagerie. Les enseignants peuvent pointer sur individuels des étudiants dans la liste pour voir l’icône de courrier électronique pour chaque étudiant ou sélectionnez les étudiants d’un ou plusieurs et utiliser **envoyer une invitation** sur la barre d’outils. Cette fonctionnalité envoie un e-mail avec un lien d’inscription et un message (le cas échéant) est ajouté par le formateur. Une fois l’invitation est envoyée, l’état de l’invitation passe à **Invitation envoyée** afin que les enseignants peuvent effectuer le suivi des étudiants ont déjà reçu le lien d’inscription et la date d’expédition.

1. Basculez vers la vue **Utilisateurs** si vous n’y êtes pas déjà. 
2. Sélectionnez des utilisateurs spécifiques ou tous les utilisateurs dans la liste. Pour sélectionner des utilisateurs spécifiques, activez les cases à cocher dans la première colonne de la liste. Pour sélectionner tous les utilisateurs, sélectionnez la case à cocher devant le titre de la première colonne (**Nom**) ou sélectionnez toutes les cases à cocher pour tous les utilisateurs figurant dans la liste. Vous pouvez voir l’état de la **état de l’invitation** dans cette liste.  Dans l’image suivante, l’état de l’invitation pour tous les étudiants est défini sur **Invitation ne pas envoyée**. 

    ![Sélectionner des étudiants](../media/tutorial-setup-classroom-lab/select-students.png)
1. Sélectionnez le **icône de courrier électronique (enveloppe)** dans une des lignes (ou) sélectionnez **envoyer une invitation** sur la barre d’outils. Vous pouvez également pointer la souris sur un nom de l’étudiant dans la liste pour afficher l’icône de courrier électronique. 

    ![Envoyer un lien d’inscription par e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. Dans la page **Envoyer un lien d’inscription par e-mail**, procédez comme suit : 
    1. Tapez un **message facultatif** que vous souhaitez envoyer aux étudiants. L’e-mail inclut automatiquement le lien d’inscription. 
    2. Dans la page **Envoyer un lien d’inscription par e-mail**, sélectionnez **Envoyer**. Vous voyez l’état de l’invitation à la modification de **envoi invitation** puis **Invitation envoyée**. 
        
        ![Invitations envoyées](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>Obtenir le lien d’inscription
1. Basculez vers la vue **Utilisateurs** en sélectionnant **Utilisateurs** dans le menu de gauche. 
2. Sélectionnez la vignette **Obtenir un lien d’inscription**.

    ![Lien d’inscription de l’étudiant](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez le bouton **Copier**. Le lien est copié dans le Presse-papiers. Collez-le dans un éditeur d’e-mail et envoyez un e-mail à l’étudiant. 

    ![Lien d’inscription de l’étudiant](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez **Fermer**. 
4. Partage le **lien d’inscription** avec un étudiant afin que les étudiants peuvent s’inscrire à la classe. 

## <a name="view-users-registered-with-the-lab"></a>Afficher les utilisateurs inscrits au laboratoire

Dans le menu de gauche, sélectionnez **Utilisateurs** pour afficher la liste des utilisateurs inscrits au labo. 

![Liste des utilisateurs inscrits au labo](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>Définir des quotas par utilisateur
Vous pouvez définir des quotas par utilisateur en effectuant les étapes suivantes : 

1. Sélectionnez **Utilisateurs** dans le menu de gauche.
2. Sélectionnez **Quota par utilisateur : illimité** dans la barre d’outils. 
3. Dans la page **Quota par utilisateur**, sélectionnez l’une des options suivantes : 
    1. **Aucun**. Les utilisateurs ne peuvent employer leurs machines virtuelles que pendant les heures planifiées ou lorsque le propriétaire du laboratoire active les machines virtuelles pour eux.
    2. **Illimité (valeur par défaut)**. Les utilisateurs peuvent employer leurs machines virtuelles sans aucune restriction de temps.
    3. **Spécifiez le nombre d’heures par utilisateur**. Les utilisateurs peuvent employer leurs machines virtuelles pendant le nombre d'heures défini (spécifié ci-dessous) en plus de l'heure planifiée. Si vous sélectionnez cette option, entrez le **nombre d’heures** dans la zone de texte. 

        ![Nombre d’heures par utilisateur](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Sélectionnez **Enregistrer**. 
5. Les valeurs modifiées figurent maintenant dans la barre d’outils : **Quota par utilisateur : &lt;nombre d’heures&gt;**. 

    ![Quota par utilisateur](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> Le [temps d’exécution planifié des machines virtuelles](how-to-create-schedules.md) ne compte pas dans le quota alloué à un utilisateur. Le quota s'applique au temps qu'un étudiant consacre aux machines virtuelles en dehors des heures planifiées. 

### <a name="add-users-by-uploading-a-csv-file"></a>Ajouter des utilisateurs en chargeant un fichier CSV
Vous pouvez également ajouter des utilisateurs en chargeant un fichier CSV avec les adresses e-mail des utilisateurs.

1. Créez un fichier CSV en indiquant les adresses e-mail des utilisateurs dans une colonne.

    ![Quota par utilisateur](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Dans la page **Utilisateurs** du labo, sélectionnez **Charger un fichier CSV** dans la barre d’outils.

    ![Bouton Charger un fichier CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Sélectionnez le fichier CSV avec les adresses e-mail des utilisateurs. Lorsque vous sélectionnez **Ouvrir** après avoir sélectionné le fichier CSV, la fenêtre **Ajouter des utilisateurs** s’affiche. La liste d’adresses e-mail est remplie avec les adresses e-mail issues du fichier CSV. 

    ![Fenêtre Ajouter des utilisateurs remplie d’adresses e-mail issues du fichier CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Sélectionnez **Enregistrer** dans la fenêtre **Ajouter des utilisateurs**. 
5. Vérifiez que les utilisateurs figurent dans la liste d’utilisateurs. 

    ![Liste d’utilisateurs ajoutés](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Gérer les utilisateurs des machines virtuelles
Une fois que les étudiants s’inscrivent avec Azure Lab Services à l’aide du lien d’enregistrement que vous leur avez fourni, vous voyez les machines virtuelles assignées aux étudiants sur l’onglet **machines virtuelles**. 

![Ordinateurs virtuels assignées aux étudiants](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Vous pouvez effectuer les tâches suivantes sur une machine virtuelle d’étudiant : 

- Arrêter une machine virtuelle si elle est en cours d’exécution. 
- Démarrer une machine virtuelle si elle est arrêté. 
- Connectez-vous à la machine virtuelle. 
- Supprimez la machine virtuelle. 
- Affichez le nombre d’heures durant lesquelles les utilisateurs ont utilisé la machine virtuelle. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Mettre à jour le nombre de machines virtuelles dans le labo
Pour mettre à jour le nombre de machines virtuelles dans le labo, effectuez les étapes mentionnées dans la page **Machines virtuelles** :

1. Sélectionnez **Machines virtuelles** dans le menu de gauche. 
2. Sélectionnez **Capacité du labo : &lt;nombre&gt; machine(s)** dans la barre d’outils. 
3. Entrez le **nombre** de machines virtuelles.
4. Sélectionnez **Enregistrer**.

    ![Machines virtuelles dans le labo](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)
