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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 834674eb63af75088434db0f614b11c7a36e7adf
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964812"
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

## <a name="send-registration-link-to-students"></a>Envoyer un lien d’inscription aux étudiants
La procédure suivante indique comment envoyer un lien d’inscription aux utilisateurs. Si l’option **Restreindre l’accès** est activée pour le labo, seuls les utilisateurs figurant dans la liste des utilisateurs peuvent utiliser le lien d’inscription pour s’inscrire au labo. 

1. Basculez vers la vue **Utilisateurs** en sélectionnant **Utilisateurs** dans le menu de gauche. 
2. Sélectionnez la vignette **Obtenir un lien d’inscription**.

    ![Lien d’inscription de l’étudiant](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez le bouton **Copier**. Le lien est copié dans le Presse-papiers. Collez-le dans un éditeur d’e-mail et envoyez un e-mail à l’étudiant. 

    ![Lien d’inscription de l’étudiant](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez **Fermer**. 
4. Partagez le lien d’inscription avec un étudiant afin que ce dernier puisse s’inscrire à la classe. Si vous avez activé le paramètre **Restreindre l’accès** et si vous disposez d’une liste d’utilisateurs, effectuez les actions suivantes :
    1. Sélectionnez l’**adresse e-mail** de l’utilisateur dans la liste. 
    2. Vous voyez une fenêtre de votre programme de messagerie par défaut, où l’adresse du **destinataire** est renseignée. 
    3. Collez l’**URL d’inscription** que vous avez copiée précédemment. 
    4. Envoyez l’**e-mail**. 

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
