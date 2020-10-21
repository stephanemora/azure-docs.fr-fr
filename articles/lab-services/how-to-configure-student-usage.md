---
title: Configurer des paramètres d’utilisation dans les labos de classe Azure Lab Services
description: Découvrez comment configurer le nombre d’étudiants d’un labo, les inscrire au labo, contrôler le nombre d’heures pendant lesquelles ils peuvent utiliser la machine virtuelle, et bien plus encore.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 742fcd2b504bce7ad2257edd559e4b66a9172d24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399667"
---
# <a name="add-and-manage-lab-users"></a>Ajouter et gérer des utilisateurs lab

Cet article explique comment ajouter des étudiants à un labo, les inscrire au labo, contrôler le nombre d’heures supplémentaires pendant lesquelles ils peuvent utiliser la machine virtuelle, et bien plus encore. 

## <a name="add-users-to-a-lab"></a>Ajouter des utilisateurs à un laboratoire

Dans cette section, vous allez ajouter des étudiants à un labo manuellement ou en chargeant un fichier CSV. Effectuez les actions suivantes :

1. Dans le volet gauche, sélectionnez **Utilisateurs**. 

    Par défaut, l’option **Restreindre l’accès** est activée et, à moins qu’ils ne soient dans la liste des utilisateurs, les étudiants ne peuvent pas s’inscrire au labo même s’ils disposent d’un lien d’inscription. Seuls les utilisateurs figurant dans la liste peuvent s’inscrire au labo à l’aide du lien d’inscription que vous envoyez. Dans cette procédure, vous allez ajouter des utilisateurs à la liste. Vous pouvez également désactiver l’option **Restreindre l’accès** afin de permettre aux étudiants disposant du lien d’inscription de s’inscrire au labo. 

1. En haut du volet **Utilisateurs**, sélectionnez **Ajouter des utilisateurs**, puis **Ajouter par adresse e-mail**. 

    ![Bouton « Ajouter des utilisateurs »](./media/how-to-configure-student-usage/add-users-button.png)

1. Dans la page **Ajouter des utilisateurs**, entrez les adresses e-mail des étudiants sur des lignes distinctes, ou sur une seule ligne en les séparant par des points-virgules. 

    ![Ajouter les adresses e-mail des utilisateurs](./media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Sélectionnez **Enregistrer**. 

    La liste affiche les adresses e-mail et les états des utilisateurs actuels, qu’ils soient inscrits ou non au labo. 

    ![Liste des utilisateurs](./media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Une fois les étudiants inscrits au labo, leur nom apparaît dans la liste. Le nom affiché dans la liste est construit en utilisant le prénom et le nom des étudiants dans Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Ajouter des utilisateurs en chargeant un fichier CSV

Vous pouvez également ajouter des utilisateurs en chargeant un fichier CSV qui contient leurs adresses e-mail.

1. Dans Microsoft Excel, créez un fichier CSV qui répertorie les adresses e-mail des étudiants dans une colonne.

    ![Liste des utilisateurs dans un fichier CSV](./media/how-to-configure-student-usage/csv-file-with-users.png)

1. En haut du volet **Utilisateurs**, sélectionnez **Ajouter des utilisateurs**, puis **Charger un fichier CSV**.

    ![Bouton « Charger un fichier CSV »](./media/how-to-configure-student-usage/upload-csv-button.png)

1. Sélectionnez le fichier CSV qui contient les adresses e-mail des étudiants, puis sélectionnez **Ouvrir**.

    La fenêtre **Ajouter des utilisateurs** affiche la liste d’adresses e-mail provenant du fichier CSV. 

    ![Fenêtre « Ajouter des utilisateurs » avec les adresses e-mail issues du fichier CSV](./media/how-to-configure-student-usage/add-users-window.png)

1. Sélectionnez **Enregistrer**. 

1. Dans le volet **Utilisateurs**, affichez la liste des étudiants ajoutés. 

    ![Liste des utilisateurs ajoutés dans le volet « Utilisateurs »](./media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Envoyer des invitations aux utilisateurs

Pour envoyer un lien d’inscription aux nouveaux utilisateurs, utilisez l’une des méthodes suivantes. 

Si l’option **Restreindre l’accès** est activée pour le labo, seuls les utilisateurs de la liste peuvent utiliser le lien d’inscription pour s’inscrire au labo. Cette option est activée par défaut. 

### <a name="invite-all-users"></a>Inviter tous les utilisateurs

Cette méthode montre comment envoyer un e-mail avec un lien d’inscription et un message facultatif à tous les étudiants de la liste.

1. Dans le volet **Utilisateurs**, sélectionnez **Tous les inviter**. 

    ![Bouton « Tous les inviter »](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Dans la fenêtre **Envoyer l’invitation par e-mail**, entrez éventuellement un message, puis sélectionnez **Envoyer**. 

    L’e-mail inclut automatiquement le lien d’inscription. Pour récupérer et enregistrer le lien d’inscription séparément, sélectionnez les points de suspension ( **…** ) en haut du volet **Utilisateurs**, puis sélectionnez **Lien d’inscription**. 

    ![Fenêtre « Envoyer un lien d’inscription par e-mail »](./media/tutorial-setup-classroom-lab/send-email.png)

    La colonne **Invitation** de la liste **Utilisateurs** affiche l’état de l’invitation pour chaque utilisateur ajouté. L’état doit passer à **Envoi**, puis à **Envoyée le \<date>** . 

### <a name="invite-selected-users"></a>Inviter les utilisateurs sélectionnés

Cette méthode montre comment inviter uniquement certains étudiants et obtenir un lien d’inscription que vous pouvez partager avec d’autres personnes.

1. Dans le volet **Utilisateurs**, sélectionnez un ou plusieurs étudiants dans la liste. 

1. Dans la ligne de l’étudiant que vous avez sélectionné, sélectionnez l’icône en forme d’**enveloppe** ou, dans la barre d’outils, sélectionnez **Inviter**. 

    ![Inviter les utilisateurs sélectionnés](./media/how-to-configure-student-usage/invite-selected-users.png)

1. Dans la fenêtre **Envoyer l’invitation par e-mail**, entrez éventuellement un **message**, puis sélectionnez **Envoyer**. 

    ![Envoyer un e-mail aux utilisateurs sélectionnés](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Le volet **Utilisateurs** affiche l’état de cette opération dans la colonne **Invitation** du tableau. L’e-mail d’invitation inclut le lien d’inscription que les étudiants peuvent utiliser pour s’inscrire au labo.

## <a name="get-the-registration-link"></a>Recevoir le lien d’inscription

Dans cette section, vous pouvez accéder au lien d’inscription à partir du portail et l’envoyer à l’aide de votre propre application de messagerie. 

1. Dans le volet **Utilisateurs**, sélectionnez **Lien d’inscription**.

    ![Lien d’inscription de l’étudiant](./media/how-to-configure-student-usage/registration-link-button.png)

1. Dans la fenêtre **Inscription de l’utilisateur**, sélectionnez **Copier**, puis **Terminé**. 

    ![Fenêtre « Inscription de l’utilisateur »](./media/how-to-configure-student-usage/registration-link.png)

    Le lien est copié dans le Presse-papiers. 
    
1. Dans votre application de messagerie, collez le lien d’inscription, puis envoyez l’e-mail à un étudiant pour que celui-ci puisse s’inscrire à la classe. 

## <a name="view-registered-users"></a>Afficher les utilisateurs inscrits

1. Accédez au site web d’[Azure Lab Services](https://labs.azure.com). 
1. Sélectionnez **Se connecter**, puis entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft.
1. Sur la page **Mes labos**, sélectionnez le labo dont vous souhaitez suivre l’utilisation. 
1. Dans le volet gauche, sélectionnez **Utilisateurs** ou sélectionnez la vignette **Utilisateurs**. 

    Le volet **Utilisateurs** affiche la liste des étudiants qui se sont inscrits à votre labo.  

    ![Liste des utilisateurs inscrits](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Définir des quotas pour les utilisateurs

Vous pouvez définir un quota d’heures pour chaque étudiant en procédant comme suit : 

1. Dans le volet **Utilisateurs**, sélectionnez **Quota par utilisateur : \<number> heure(s)** dans la barre d’outils. 
1. Dans la fenêtre **Quota par utilisateur**, spécifiez le nombre d’heures que vous souhaitez donner à chaque étudiant en dehors des heures de classe prévues, puis sélectionnez **Enregistrer**.

    ![Fenêtre « Quota par utilisateur »](./media/how-to-configure-student-usage/quota-per-user.png)    

    Les valeurs modifiées s’affichent désormais dans le bouton **Quota par utilisateur : \<number of hours>** sur la barre d’outils et dans la liste des utilisateurs, comme illustré ici :

    ![Quota heures par utilisateur](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Le [temps d’exécution planifié des machines virtuelles](how-to-create-schedules.md) ne compte pas dans le quota alloué à un étudiant. Le quota s’applique au temps pendant lequel un étudiant utilise les machines virtuelles en dehors des heures planifiées. 

## <a name="set-additional-quotas-for-specific-users"></a>Définir des quotas supplémentaires pour des utilisateurs spécifiques

Vous pouvez spécifier des quotas pour certains étudiants au-delà des quotas communs qui ont été définis pour tous les utilisateurs dans la section précédente. Par exemple, si vous, en tant qu’instructeur, définissez le quota pour tous les étudiants sur 10 heures et que vous définissez un quota supplémentaire de 5 heures pour un étudiant spécifique, cet étudiant obtient un quota de 15 (10+5) heures. Si vous modifiez ultérieurement le quota commun, par exemple en le passant à 15, l’étudiant obtient un quota de 20 (15+5) heures. N’oubliez pas que ce quota global s’applique en dehors des horaires planifiés. Le temps qu’un étudiant passe sur une machine virtuelle de labo pendant la durée de l’horaire n’est pas pris en compte dans ce quota. 

Pour définir des quotas supplémentaires, procédez comme suit :

1. Dans le volet **Utilisateurs**, sélectionnez un étudiant dans la liste, puis sélectionnez **Ajuster le quota** dans la barre d’outils. 

    ![Bouton « Ajuster le quota »](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. Dans l’option **Ajuster le quota pour \<selected user or users email address>** , entrez le nombre d’heures de labo supplémentaires que vous souhaitez accorder aux étudiants sélectionnés, puis sélectionnez **Appliquer**. 

    ![Fenêtre « Ajuster le quota… »](./media/how-to-configure-student-usage/additional-quota.png)

    La colonne **Utilisation** affiche le quota mis à jour pour les étudiants sélectionnés. 

    ![Nouvelle utilisation pour l’utilisateur](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Comptes étudiants

Pour ajouter des étudiants à un labo de classe, vous utilisez leurs comptes de messagerie. Les étudiants peuvent avoir les types de comptes de messagerie suivants :

- Un compte de courrier étudiant fourni par l'instance Azure Active Directory de votre université.
- Un compte de messagerie de domaine Microsoft, par exemple *outlook.com*, *hotmail.com*, *msn.com* ou *live.com*.
- Un compte de messagerie non Microsoft, tel qu’un compte fourni par Yahoo! ou Google. Cependant, ces types de comptes doivent être liés à un compte Microsoft.
- Un compte GitHub. Ce compte doit être lié à un compte Microsoft.

### <a name="use-a-non-microsoft-email-account"></a>Utiliser un compte de messagerie non Microsoft
Les étudiants peuvent utiliser des comptes de messagerie non Microsoft pour s’inscrire et se connecter à un labo de classe.  Toutefois, pour s’inscrire, ils doivent d’abord créer un compte Microsoft lié à leur adresse e-mail non Microsoft.

De nombreux étudiants ont peut-être déjà un compte Microsoft lié à leur adresse e-mail non Microsoft. Par exemple, les étudiants ont déjà un compte Microsoft s’ils ont utilisé leur adresse e-mail avec d’autres produits ou services Microsoft, tels que Office, Skype, OneDrive ou Windows.  

Lorsque les étudiants cliquent sur le lien d’inscription pour se connecter à une classe, ils sont invités à entrer leur adresse e-mail et leur mot de passe. Si les étudiants tentent de se connecter avec un compte non Microsoft qui n’est pas lié à un compte Microsoft, le message d’erreur suivant s’affiche : 

![Message d’erreur lors de la connexion](./media/how-to-configure-student-usage/cant-find-account.png)

Voici un lien permettant aux étudiants de [s’inscrire à un compte Microsoft](http://signup.live.com).  

> [!IMPORTANT]
> Quand les étudiants se connectent à un labo de salle de classe, ils n’ont pas la possibilité de créer un compte Microsoft. Pour cette raison, nous vous recommandons d’inclure ce lien d’inscription, http://signup.live.com, dans l’e-mail d’inscription au labo de salle de classe que vous envoyez aux étudiants qui utilisent des comptes non Microsoft.

### <a name="use-a-github-account"></a>Utiliser un compte GitHub
Les étudiants peuvent également utiliser un compte GitHub existant pour s’inscrire et se connecter à un labo de classe. S’ils ont déjà un compte Microsoft lié à leur compte GitHub, les étudiants peuvent se connecter et fournir leur mot de passe, comme indiqué dans la section précédente. 

S’ils n’ont pas encore lié leur compte GitHub à un compte Microsoft, ils peuvent procéder comme suit :

1. Sélectionnez le lien **Options de connexion**, comme illustré ici :

    ![Lien « Options de connexion »](./media/how-to-configure-student-usage/signin-options.png)

1. Dans la fenêtre **Options de connexion**, sélectionnez **Se connecter avec GitHub**.

    ![Lien « Se connecter avec GitHub »](./media/how-to-configure-student-usage/signin-github.png)

    Lorsqu’ils y sont invités, les étudiants créent ensuite un compte Microsoft qui est lié à leur compte GitHub. La liaison se fait automatiquement lorsqu’ils sélectionnent **Suivant**. Ils sont alors immédiatement inscrits et connectés au labo de salle de classe.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Exporter une liste d’utilisateurs vers un fichier CSV

1. Accédez au volet **Utilisateurs**.
1. Dans la barre d’outils, sélectionnez les points de suspension ( **…** ), puis **Exporter au format CSV**. 

    ![Bouton « Exporter au format CSV »](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- Pour les administrateurs : [Créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- Pour les propriétaires de labo : [Créer et gérer des labos](how-to-manage-classroom-labs.md) et [Configurer et publier des modèles](how-to-create-manage-template.md)
- Pour les utilisateurs du labo : [Accéder aux labos de salle de classe](how-to-use-classroom-lab.md)
