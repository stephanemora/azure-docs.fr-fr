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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 946a2a05cee0cf8f3b91eef58442fbb2e26935c4
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490445"
---
# <a name="configure-usage-settings-and-policies"></a>Configurer des paramètres et des stratégies d’utilisation
Cet article explique comment ajouter des utilisateurs au labo, comment les inscrire au labo, comment contrôler le nombre d’heures qu’ils peuvent utiliser la machine virtuelle, et bien plus encore. 


## <a name="add-users-to-the-lab"></a>Ajouter des utilisateurs au laboratoire
Si l’option **Restreindre l’accès** est activée, ajoutez des utilisateurs (adresses e-mail) à la liste.

1. Sélectionnez **Utilisateurs** dans le menu de gauche.
2. Sélectionnez **Ajouter des utilisateurs** dans la barre d’outils. 
3. Dans la page **Ajouter des utilisateurs**, entrez les adresses e-mail des utilisateurs sur des lignes distinctes, ou sur une seule ligne en les séparant par des points-virgules. 

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
3. Dans la page **Quota par utilisateur**, sélectionnez **Limiter le nombre d’heures qu’un utilisateur peut utiliser une machine virtuelle**. 
4. Pour **Combien d’heures voulez-vous accorder à chaque utilisateur**, entrez le nombre d’heures et sélectionnez **Enregistrer**. 

    ![Nombre d’heures par utilisateur](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Le nombre d’heures s’affiche désormais dans la barre d’outils : **Quota par utilisateur : &lt;nombre d’heures&gt;**. 

    ![Quota par utilisateur](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> Le [temps d’exécution planifié des machines virtuelles](how-to-create-schedules.md) ne compte pas dans le quota alloué à un utilisateur. Le quota s'applique au temps qu'un étudiant consacre aux machines virtuelles en dehors des heures planifiées. 

### <a name="add-users-by-uploading-a-csv-file"></a>Ajouter des utilisateurs en chargeant un fichier CSV
Vous pouvez également ajouter des utilisateurs en chargeant un fichier CSV avec les adresses e-mail des utilisateurs.

1. Sélectionnez **Charger un fichier CSV** dans la barre d’outils.
2. Sélectionnez le fichier CSV avec les adresses e-mail des utilisateurs. Toutes les adresses e-mail doivent figurer dans une colonne quand vous l’ouvrez avec Excel. 

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
