---
title: Activer des environnements de plusieurs machines virtuelles dans Azure Lab Services | Microsoft Docs
description: Découvrez comment créer un environnement avec plusieurs machines virtuelles à l’intérieur d’une machine virtuelle dans un laboratoire de salle de classe de Azure Lab Services.
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190559"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Créer un environnement avec plusieurs machines virtuelles à l’intérieur d’un modèle de machine virtuelle d’un laboratoire de salle de classe
Actuellement Azure Lab Services vous permet de configurer une machine virtuelle dans un laboratoire et proposer une seule copie pour chacun de vos utilisateurs. Mais si vous êtes un professeur enseigne une classe de l’informatique sur la façon de configurer des pare-feu ou des serveurs, vous devrez peut-être fournir chacune de vos élèves avec un environnement dans lequel plusieurs ordinateurs virtuels peuvent communiquer entre eux sur un réseau.

Virtualisation imbriquée vous permet de créer un environnement de plusieurs machines virtuelles au sein de la machine virtuelle d’un laboratoire. Publication du modèle fournit à chaque utilisateur dans le laboratoire avec un ordinateur virtuel configuré avec plusieurs machines virtuelles qu’il contient.

## <a name="what-is-nested-virtualization"></a>Quelle est la virtualisation imbriquée ?
Virtualisation imbriquée vous permet de créer des machines virtuelles au sein d’une machine virtuelle. La virtualisation imbriquée est effectuée via Hyper-V et est uniquement disponible sur les machines virtuelles Windows.

Pour plus d’informations sur la virtualisation imbriquée, consultez les articles suivants :

- [Virtualisation imbriquée dans Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Comment activer la virtualisation imbriquée dans une machine virtuelle Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Utiliser la virtualisation imbriquée dans Azure Lab Services
Les étapes importantes sont :

1. Créer un **grand** taille **Windows** machine de modèle pour le laboratoire. 
2. Se connecter à celui-ci et [activer la virtualisation imbriquée](../../virtual-machines/windows/nested-virtualization.md).


La procédure suivante vous donne les étapes détaillées : 

1. Créez un compte de laboratoire si vous n’en avez pas déjà. Pour obtenir des instructions, consultez [didacticiel : Configurer un compte de laboratoire avec Azure Lab Services](tutorial-setup-lab-account.md).
2. Accédez au [site web Azure Lab Services](https://labs.azure.com). 
3. Sélectionnez **Se connecter** et entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft. 
4. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez un **nom** pour votre laboratoire. 
    2. Spécifiez le **nombre de machines virtuelles** maximal dans le laboratoire. Vous pouvez augmenter ou diminuer le nombre de machines virtuelles après avoir créé un lab ou dans un lab existant. Pour plus d’informations, consultez [Mettre à jour le nombre de machines virtuelles d’un lab](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Sélectionnez **Enregistrer**.

        ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Sur la page **Select virtual machine specifications** (sélectionner les spécifications de machine virtuelle), procédez aux étapes suivantes :
    1. Sélectionnez **grande** pour la taille des machines virtuelles (VM) doit être créé dans le laboratoire. Actuellement, uniquement la grande taille prend en charge la virtualisation imbriquée.
    2. Choisissez une image de machine virtuelle est un **image de Windows**. Virtualisation imbriquée est disponible uniquement sur les ordinateurs Windows. 
    3. Sélectionnez **Suivant**.

        ![Définir les spécifications de VM](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
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
8. Sur le **configurer modèle** page, sélectionnez **Connect** pour se connecter au modèle de machine virtuelle pour configurer la virtualisation imbriquée. Vous pouvez également configurer ultérieurement après avoir terminé les étapes décrites dans cet Assistant. 
9. À l’intérieur de la machine virtuelle du modèle, configurez la virtualisation imbriquée et configurer un réseau virtuel avec plusieurs machines virtuelles. Pour obtenir des instructions détaillées, consultez [comment activer la virtualisation imbriquée dans une machine virtuelle Azure](../../virtual-machines/windows/nested-virtualization.md). Voici un bref résumé des étapes : 
    1. Activer la fonctionnalité Hyper-V sur l’ordinateur virtuel de modèle.
    2. Configurer un réseau virtuel interne avec une connectivité internet pour les machines virtuelles imbriquées
    3. Créer des machines virtuelles via le Gestionnaire Hyper-V
    4. Affecter une adresse IP aux machines virtuelles
10. Sélectionnez **Suivant** sur la page du modèle. 
11. Sur la page **Publier le modèle**, effectuez les actions suivantes. 
    1. Pour publier le modèle immédiatement, sélectionnez **Publier**.  

        > [!WARNING]
        > Une fois que vous publiez, vous ne pouvez pas annuler la publication. 
    2. Pour publier ultérieurement, sélectionnez **Enregistrer pour plus tard**. Vous pouvez publier le modèle de VM une fois l’Assistant terminé. Pour plus d’informations sur la façon de configurer et publier une fois l’Assistant terminé, consultez la section [Publier le modèle](how-to-create-manage-template.md#publish-the-template-vm) dans l’article [Gérer des laboratoires de classe](how-to-manage-classroom-labs.md).

        ![Publier un modèle](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. La **progression de la publication** du modèle s’affiche. Ce processus peut prendre jusqu’à une heure. 

    ![Publier un modèle - progression](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. La page suivante s’affiche lorsque le modèle est publié avec succès. Sélectionnez **Terminé**.

    ![Publier un modèle - succès](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Le **tableau de bord** du laboratoire s’affiche. 
    
    ![Tableau de bord de laboratoire de classe](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>Étapes suivantes

À présent, chaque utilisateur obtient une seule machine virtuelle qui comprend un environnement de plusieurs machines virtuelles qu’il contient. Pour savoir comment ajouter des utilisateurs au laboratoire et de leur envoyer le lien d’inscription, consultez l’article suivant : [Ajouter des utilisateurs au laboratoire](tutorial-setup-classroom-lab.md#add-users-to-the-lab).