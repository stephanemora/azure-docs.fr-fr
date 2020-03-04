---
title: Configurer des paramètres d’utilisation dans les labos de classe Azure Lab Services
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 9fda1295bcdcd44b8a92c101c2aa445197ce6d4a
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539165"
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

    ![Liste des utilisateurs](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Les noms des utilisateurs s’affichent dans la liste après leur inscription au laboratoire. Le nom affiché dans la liste est construit en utilisant le prénom et le nom de l’utilisateur dans Azure Active Directory. 

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
1. Basculez vers la vue **Utilisateurs** si vous n’y êtes pas déjà. Sélectionnez **Inviter tout le monde** dans la barre d’outils. 

    ![Sélectionner des étudiants](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Dans la page **Envoyer l’invitation par e-mail**, entrez un message si vous le souhaitez, puis sélectionnez **Envoyer**. L’e-mail inclut automatiquement le lien d’inscription. Pour obtenir ce lien d’inscription, sélectionnez **...** (points de suspension) dans la barre d’outils, puis sélectionnez **Lien d’inscription**. 

    ![Envoyer un lien d’inscription par e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. L’état de l’**invitation**  est indiqué dans la liste **Utilisateurs**. L’état doit passer à **Envoi**, puis à **Envoyée le \<date**. 

    Pour plus d’informations sur l’ajout d’élèves à une classe et sur la gestion de leur utilisation du laboratoire, consultez [Comment configurer l’utilisation des étudiants](how-to-configure-student-usage.md).

### <a name="invite-selected-users"></a>Inviter les utilisateurs sélectionnés

1. Sélectionnez un ou plusieurs utilisateurs dans la liste. 
2. Ensuite, sélectionnez l’icône **enveloppe** que vous voyez dans la ligne sélectionnée (ou) sélectionnez **Inviter** dans la barre d’outils. 

    ![Inviter les utilisateurs sélectionnés](../media/how-to-configure-student-usage/invite-selected-users.png)
3. Dans la fenêtre **Envoyer l’invitation par e-mail**, entrez éventuellement un **message**, puis sélectionnez **Envoyer**. 

    ![Envoyer un e-mail aux utilisateurs sélectionnés](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    L’état de cette opération s’affiche dans la colonne **Invitation** de la liste **Utilisateurs**. L’e-mail d’invitation inclut le lien d’inscription que les utilisateurs peuvent utiliser pour s’inscrire auprès du labo.

1. Basculez vers l’affichage **Utilisateurs** si vous n’y êtes pas déjà. 

## <a name="get-registration-link"></a>Obtenir le lien d’inscription
Vous pouvez également accéder au lien d’inscription à partir du portail et l’envoyer à l’aide de votre propre application cliente de messagerie. 

1. Basculez vers la vue **Utilisateurs** en sélectionnant **Utilisateurs** dans le menu de gauche. 
2. Sélectionnez **... (ellipse)** dans la barre d’outils, puis sélectionnez **Lien d’inscription**.

    ![Lien d’inscription de l’étudiant](../media/how-to-configure-student-usage/registration-link-button.png)
1. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez le bouton **Copier**. Le lien est copié dans le Presse-papiers. Collez-le dans un éditeur d’e-mail et envoyez un e-mail à l’étudiant. 

    ![Lien d’inscription de l’étudiant](../media/how-to-configure-student-usage/registration-link.png)
2. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez **Terminé**. 
4. Envoyez le **lien d’inscription** à un étudiant afin que ce dernier puisse s’inscrire à la classe. 

## <a name="view-registered-users"></a>Afficher les utilisateurs inscrits

1. Accédez au [site web Azure Lab Services](https://labs.azure.com). 
2. Sélectionnez **Se connecter** et entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft.
3. Sur la page **Mes laboratoires**, sélectionnez le laboratoire pour lequel vous souhaitez suivre l’utilisation. 
4. Sélectionnez **Utilisateurs** dans le menu de gauche ou la vignette **Utilisateurs**. Vous visualisez les étudiants inscrits à votre laboratoire.  

    ![Utilisateurs inscrits](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Définir des quotas pour les utilisateurs
Vous pouvez définir des quotas par utilisateur en effectuant les étapes suivantes : 

1. Sélectionnez **Utilisateurs** dans le menu de gauche si la page n’est pas déjà active. 
2. Sélectionnez **Quota par utilisateur : \<nombre> heures** dans la barre d’outils. 
3. Sur la page **Quota par utilisateur**, spécifiez le nombre d’heures que vous souhaitez donner à chaque utilisateur (étudiant) en dehors des heures de cours prévues, puis sélectionnez **Enregistrer**.

    ![Quota par utilisateur](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Les valeurs modifiées figurent maintenant dans la barre d’outils : **Quota par utilisateur : \<nombre d’heures>** et également dans la liste des utilisateurs.

    ![Quota par utilisateur – après](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Le [temps d’exécution planifié des machines virtuelles](how-to-create-schedules.md) ne compte pas dans le quota alloué à un utilisateur. Le quota s’applique au temps pendant lequel un étudiant utilise les machines virtuelles en dehors des heures planifiées. 

## <a name="set-additional-quota-for-a-specific-user"></a>Définir un quota supplémentaire pour un utilisateur spécifique
Vous pouvez spécifier un quota supplémentaire pour un utilisateur. Ce quota s’ajoute au quota commun fixé pour tous les utilisateurs dans la section précédente. Par exemple, si vous (en tant qu’instructeur) définissez le quota pour tous les utilisateurs sur 10 heures et que vous définissez un quota supplémentaire de 5 heures pour un utilisateur spécifique, l’utilisateur en question dispose de 15 (10+5) heures de quota. Si vous modifiez ultérieurement le quota commun, par exemple en le passant à 15, l’utilisateur obtient 20 (15+5) heures de quota. N’oubliez pas que ce quota global est en dehors de l’heure planifiée. Le temps que l’étudiant passe sur une machine virtuelle de labo pendant la durée de l’horaire n’est pas pris en compte dans ce quota. 

Pour ce faire, procédez comme suit :

1. Sélectionnez un utilisateur (étudiant) dans la liste des utilisateurs sur la page **Utilisateurs**.
2. Ensuite, sélectionnez **Ajuster le quota** dans la barre d’outils. 

    ![Bouton Ajuster le quota](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Entrez le nombre **d’heures supplémentaires** pour le ou les utilisateurs sélectionnés, puis sélectionnez **Appliquer**. 

    ![Quota supplémentaire pour un utilisateur](../media/how-to-configure-student-usage/additional-quota.png)
4. L’utilisation mise à jour pour l’utilisateur s’affiche dans la colonne **Utilisation**. 

    ![Nouvelle utilisation pour l’utilisateur](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Comptes étudiants
Pour ajouter des étudiants à un labo de classe, vous utilisez leurs comptes de messagerie. Les types de comptes de messagerie suivants peuvent être utilisés :

- Un compte de messagerie étudiant fourni par l’Office 365 Azure Active Directory (AAD) de votre université. 
- Un compte de messagerie Microsoft, tel que `@outlook.com`, `@hotmail.com`, `@msn.com` ou `@live.com`.
- Un compte de messagerie non Microsoft, tel qu’un compte fourni par Yahoo ou Google. Cependant, ces types de comptes doivent être liés à un compte Microsoft.
- Un compte GitHub. Ce compte doit être lié à un compte Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Utiliser un compte de messagerie non Microsoft
Les étudiants peuvent utiliser des comptes de messagerie non Microsoft pour s’inscrire et se connecter à un labo de classe.  Toutefois, pour s’inscrire, les étudiants doivent d’abord créer un compte Microsoft lié à leur adresse e-mail non Microsoft.

De nombreux étudiants ont peut-être déjà un compte Microsoft lié à leurs adresses e-mail non Microsoft. Par exemple, les étudiants ont déjà un compte Microsoft s’ils ont utilisé leur adresse e-mail avec d’autres produits ou services Microsoft, tels que Office, Skype, OneDrive ou Windows.  

Lorsqu’un étudiant clique sur l’URL d’inscription pour se connecter à une classe, il est invité à entrer son adresse e-mail et son mot de passe. Si l’étudiant tente de se connecter avec un compte non Microsoft qui n’a pas de compte Microsoft lié, le message d’erreur suivant s’affiche : 

![Message d’erreur](../media/how-to-configure-student-usage/cant-find-account.png)

Pour vous inscrire à un compte Microsoft, les étudiants doivent accéder à la page [http://signup.live.com](http://signup.live.com).  

> [!IMPORTANT]
> Quand les étudiants se connectent à un labo de classe, ils n’ont pas la possibilité de créer un compte Microsoft. Par conséquent, nous vous recommandons d’inclure ce lien d’inscription dans l’e-mail d’inscription au labo de classe que vous envoyez aux étudiants qui utilisent des comptes non Microsoft.

### <a name="using-a-github-account"></a>Utiliser un compte GitHub
Les étudiants peuvent également utiliser un compte GitHub existant pour s’inscrire et se connecter à un labo de classe. Si l’étudiant a déjà un compte Microsoft lié à son compte GitHub, il peut se connecter et fournir son mot de passe, comme indiqué dans la section précédente. S’il n’a pas encore lié son compte GitHub à un compte Microsoft, il doit sélectionner **Options de connexion** :

![Lien Options de connexion](../media/how-to-configure-student-usage/signin-options.png)

Dans la page **Options de connexion**, sélectionnez **Se connecter avec GitHub**.

![Lien Se connecter avec GitHub](../media/how-to-configure-student-usage/signin-github.png)

Enfin, il est invité à créer un compte Microsoft qui est lié à son compte GitHub. Cela se produit automatiquement lorsque l’étudiant sélectionne **Suivant**.  L’étudiant est alors immédiatement inscrit et connecté au labo de classe.

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)
