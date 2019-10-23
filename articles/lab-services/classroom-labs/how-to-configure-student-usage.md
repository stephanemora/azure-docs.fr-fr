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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 691907d1c221283f99ba59f0937cfbaf673f427a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324535"
---
# <a name="add-and-manage-lab-users"></a>Ajouter et gérer des utilisateurs lab
Cet article explique comment ajouter des utilisateurs au labo, comment les inscrire au labo, comment contrôler le nombre d’heures qu’ils peuvent utiliser la machine virtuelle, et bien plus encore. 


## <a name="add-users-to-the-lab"></a>Ajouter des utilisateurs au laboratoire

1. Sélectionnez **Utilisateurs** dans le menu de gauche. Par défaut, l’option **Restreindre l’accès** est activée. Lorsque ce paramètre est activé, l’utilisateur ne peut pas s’inscrire au laboratoire, même s’il dispose du lien d’inscription. Il ne pourra s’inscrire que s’il figure dans la liste des utilisateurs. Seuls les utilisateurs de la liste peuvent s’inscrire au laboratoire à l’aide du lien d’inscription que vous envoyez. Dans cette procédure, vous allez ajouter des utilisateurs à la liste. Vous pouvez également désactiver l’option **Restreindre l’accès** afin de permettre aux utilisateurs disposant du lien d’inscription de s’inscrire au laboratoire. 
2. Sélectionnez **Ajouter des utilisateurs**  dans la barre d’outils, puis **Ajouter par adresse e-mail**. 

    ![Bouton Ajouter des utilisateurs](../media/how-to-configure-student-usage/add-users-button.png)
1. Dans la page **Ajouter des utilisateurs**, entrez les adresses e-mail des utilisateurs sur des lignes distinctes, ou sur une seule ligne en les séparant par des points-virgules. 

    ![Ajouter les adresses e-mail des utilisateurs](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Sélectionnez **Enregistrer**. Dans la liste, vous voyez les adresses e-mail des utilisateurs et leur état (inscrits ou non). 

    ![Liste des utilisateurs](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>Ajouter des utilisateurs en chargeant un fichier CSV
Vous pouvez également ajouter des utilisateurs en chargeant un fichier CSV avec les adresses e-mail des utilisateurs.

1. Créez un fichier CSV en indiquant les adresses e-mail des utilisateurs dans une colonne.

    ![Fichier CSV avec utilisateurs](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Dans la page **Utilisateurs** du labo, sélectionnez **Ajouter des utilisateurs** dans la barre d’outils, puis sélectionnez **Charger un fichier CSV**.

    ![Bouton Charger un fichier CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Sélectionnez le fichier CSV avec les adresses e-mail des utilisateurs. Lorsque vous sélectionnez **Ouvrir** après avoir sélectionné le fichier CSV, la fenêtre **Ajouter des utilisateurs** s’affiche. La liste d’adresses e-mail est remplie avec les adresses e-mail issues du fichier CSV. 

    ![Fenêtre Ajouter des utilisateurs remplie d’adresses e-mail issues du fichier CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Sélectionnez **Enregistrer** dans la fenêtre **Ajouter des utilisateurs**. 
5. Vérifiez que les utilisateurs figurent dans la liste d’utilisateurs. 

    ![Liste d’utilisateurs ajoutés](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Envoyer des invitations aux utilisateurs
Pour envoyer le lien d’inscription à des étudiants, utilisez une des méthodes suivantes. La première méthode montre comment envoyer des e-mails aux étudiants avec le lien d’inscription et un message facultatif. La deuxième méthode montre comment obtenir le lien d’inscription que vous pouvez partager avec d’autres personnes comme vous le souhaitez. 

Si l’option **Restreindre l’accès** est activée pour le labo, seuls les utilisateurs figurant dans la liste des utilisateurs peuvent utiliser le lien d’inscription pour s’inscrire au labo. Cette option est activée par défaut. 

### <a name="invite-all-users"></a>Inviter tous les utilisateurs

1. Basculez vers la page de **Utilisateurs** du labo. 
2. Sélectionnez **Inviter tous** dans la barre d’outils. 
3. Entrez un **message** pour les utilisateurs. Il s’agit d’une étape facultative.
4. Ensuite, sélectionnez **Envoyer**.

    ![Inviter tous les utilisateurs](../media/how-to-configure-student-usage/invite-all.png)

    L’état de cette opération s’affiche dans la colonne **Invitation** de la liste **Utilisateurs**. L’e-mail d’invitation inclut le lien d’inscription que les utilisateurs peuvent utiliser pour s’inscrire auprès du labo. 

### <a name="invite-selected-users"></a>Inviter les utilisateurs sélectionnés

1. Sélectionnez un ou plusieurs utilisateurs dans la liste. 
2. Ensuite, sélectionnez l’icône **enveloppe** que vous voyez dans la ligne sélectionnée (ou) sélectionnez **Inviter** dans la barre d’outils. 

    ![Inviter les utilisateurs sélectionnés](../media/how-to-configure-student-usage/invite-selected-users.png)
3. Dans la fenêtre **Envoyer l’invitation par e-mail**, entrez éventuellement un **message**, puis sélectionnez **Envoyer**. 

    ![Envoyer un e-mail aux utilisateurs sélectionnés](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    L’état de cette opération s’affiche dans la colonne **Invitation** de la liste **Utilisateurs**. L’e-mail d’invitation inclut le lien d’inscription que les utilisateurs peuvent utiliser pour s’inscrire auprès du labo.

1. Basculez vers la vue **Utilisateurs** si vous n’y êtes pas déjà. 

## <a name="get-registration-link"></a>Obtenir le lien d’inscription
Vous pouvez également accéder au lien d’inscription à partir du portail et l’envoyer à l’aide de votre propre application cliente de messagerie. 

1. Basculez vers la vue **Utilisateurs** en sélectionnant **Utilisateurs** dans le menu de gauche. 
2. Sélectionnez **... (ellipse)** dans la barre d’outils, puis sélectionnez **Lien d’inscription**.

    ![Lien d’inscription de l’étudiant](../media/how-to-configure-student-usage/registration-link-button.png)
1. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez le bouton **Copier**. Le lien est copié dans le Presse-papiers. Collez-le dans un éditeur d’e-mail et envoyez un e-mail à l’étudiant. 

    ![Lien d’inscription de l’étudiant](../media/how-to-configure-student-usage/registration-link.png)
2. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez **Terminé**. 
4. Envoyez le **lien d’inscription** à un étudiant afin que ce dernier puisse s’inscrire à la classe. 

## <a name="view-users-registered-with-the-lab"></a>Afficher les utilisateurs inscrits au laboratoire

Dans le menu de gauche, sélectionnez **Utilisateurs** pour afficher la liste des utilisateurs inscrits au labo. 

![Liste des utilisateurs inscrits au labo](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>Définir des quotas pour les utilisateurs
Vous pouvez définir des quotas par utilisateur en effectuant les étapes suivantes : 

1. Sélectionnez **Utilisateurs** dans le menu de gauche si la page n’est pas déjà active. 
2. Sélectionnez **Quota par utilisateur : &lt;nombre&gt; heures** dans la barre d’outils. 
3. Sur la page **Quota par utilisateur**, spécifiez le nombre d’heures que vous souhaitez donner à chaque utilisateur (étudiant) en dehors des heures de cours prévues, puis sélectionnez **Enregistrer**.

    ![Quota par utilisateur](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Les valeurs modifiées figurent maintenant dans la barre d’outils : **Quota par utilisateur : &lt;nombre d’heures&gt;** . 

    ![Quota par utilisateur - après](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Le [temps d’exécution planifié des machines virtuelles](how-to-create-schedules.md) ne compte pas dans le quota alloué à un utilisateur. Le quota s’applique au temps pendant lequel un étudiant utilise les machines virtuelles en dehors des heures planifiées. 

## <a name="set-additional-quota-for-a-specific-user"></a>Définir un quota supplémentaire pour un utilisateur spécifique
Vous pouvez définir un quota distinct pour un utilisateur. Pour ce faire, procédez comme suit :

1. Sélectionnez un utilisateur (étudiant) dans la liste des utilisateurs sur la page **Utilisateurs**.
2. Ensuite, sélectionnez **Ajuster le quota** dans la barre d’outils. 

    ![Bouton Ajuster le quota](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Entrez le nombre **d’heures supplémentaires** pour le ou les utilisateurs sélectionnés, puis sélectionnez **Appliquer**. 

    ![Quota supplémentaire pour un utilisateur](../media/how-to-configure-student-usage/additional-quota.png)
4. L’utilisation mise à jour pour l’utilisateur s’affiche dans la colonne **Utilisation**. 

    ![Nouvelle utilisation pour l’utilisateur](../media/how-to-configure-student-usage/new-usage-hours.png)


## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)
