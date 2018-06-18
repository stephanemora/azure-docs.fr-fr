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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: a96ba4aec7c23c040921a647cc4986aaf53fb30c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651410"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Didacticiel : Configurer un laboratoire de classe 
Dans ce didacticiel, vous allez configurer un laboratoire de classe avec un ensemble de machines virtuelles utilisées par les étudiants dans la classe.  

Dans ce didacticiel, vous effectuez les actions suivantes :

> [!div class="checklist"]
> * Créer un laboratoire de classe
> * Configurer le laboratoire de classe
> * Envoyer un lien d’inscription aux étudiants

## <a name="create-a-classroom-lab"></a>Créer un laboratoire de classe

1. Accédez au [site web Azure Lab Services](https://labs.azure.com).
2. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez le **nom** du laboratoire de classe. 
    2. Sélectionnez la **taille** de la machine virtuelle que vous prévoyez d’utiliser dans la classe.
    3. Sélectionnez **l’image** à utiliser pour créer la machine virtuelle.
    4. Spécifiez les **informations d’identification par défaut** pour vous connecter à des machines virtuelles dans le laboratoire. 
    7. Sélectionnez **Enregistrer**.

        ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-window.png)
1. La **page d’accueil** du laboratoire apparaît. 
    
    ![Page d’accueil du laboratoire de classe](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configurer la politique d’utilisation

1. Sélectionnez **Politique d’utilisation**. 
2. Dans **Politique d’utilisation**, puis dans les paramètres, entrez le **nombre d’utilisateurs** autorisés à utiliser le laboratoire.
3. Sélectionnez **Enregistrer**. 

    ![Politique d’utilisation](../media/tutorial-setup-classroom-lab/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Configurer le modèle 
Le modèle de laboratoire est une image de machine virtuelle de base, à partir de laquelle toutes les machines virtuelles des utilisateurs sont créées. Configurez la machine virtuelle du modèle de façon qu’elle propose exactement ce que vous souhaitez fournir aux utilisateurs du laboratoire. Vous pouvez indiquer le nom et la description du modèle que voient les utilisateurs du laboratoire. Définissez la visibilité du modèle au public pour rendre les instances du modèle de machine virtuelle disponible aux utilisateurs de votre laboratoire. 

### <a name="set-title-and-description"></a>Définir un titre et une description
1. Dans la section **Modèle**, sélectionnez **Modifier** (icône de crayon) pour le modèle. 
2. Dans la fenêtre **Vue Utilisateur**, entrez un **titre** pour le modèle.
3. Entrez une **description** pour le modèle.
4. Sélectionnez **Enregistrer**.

    ![Description du laboratoire de classe](../media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Rendre publiques les instances du modèle
Une fois que vous avez défini la visibilité d’un modèle sur **Publique**, Azure Lab Services crée des machines virtuelles dans le laboratoire à l’aide du modèle. Le nombre de machines virtuelles créées dans ce processus est identique au nombre maximal d’utilisateurs autorisés dans le laboratoire, que vous pouvez définir dans la politique d’utilisation du laboratoire. Toutes les machines virtuelles ont la même configuration que le modèle. 

1. Sélectionnez **Visibilité** dans la section **Modèle**. 
2. Dans la page **Disponibilité**, sélectionnez **Publique**.
    
    > [!IMPORTANT]
    > Une fois que vous avez défini un modèle comme étant public, vous ne pouvez plus rendre son accès privé. 
3. Sélectionnez **Enregistrer**.

    ![Disponibilité](../media/tutorial-setup-classroom-lab/public-access.png)

## <a name="send-registration-link-to-students"></a>Envoyer un lien d’inscription aux étudiants

1. Sélectionnez la vignette **Enregistrement de l’utilisateur**.
2. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez le bouton **Copier**. Le lien est copié dans le Presse-papiers. Collez-le dans un éditeur d’e-mail et envoyez un e-mail à l’étudiant. 

    ![Lien d’inscription de l’étudiant](../media/tutorial-setup-classroom-lab/registration-link.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez créé un laboratoire de classe et configuré le laboratoire. Pour savoir comment un étudiant peut accéder à une machine virtuelle dans le laboratoire à l’aide du lien d’inscription, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Se connecter à une machine virtuelle dans le laboratoire de classe](tutorial-connect-virtual-machine-classroom-lab.md)

