---
title: Activer des environnements avec plusieurs machines virtuelles dans Azure Lab Services | Microsoft Docs
description: Découvrez comment créer un environnement avec plusieurs machines virtuelles à l’intérieur d’un modèle de machine virtuelle dans un labo de salle de classe d’Azure Lab Services.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60702418"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Créer un environnement avec plusieurs machines virtuelles dans un modèle de machine virtuelle d’un labo de salle de classe
Actuellement, Azure Lab Services vous permet de configurer un seul modèle de machine virtuelle dans un labo et de rendre disponible une seule copie pour chacun de vos utilisateurs. Cependant, si vous êtes professeur apprenant à une classe d’informatique comment configurer des pare-feu ou des serveurs, il peut être nécessaire de fournir à chacun de vos étudiants un environnement où plusieurs machines virtuelles peuvent communiquer entre elles sur un réseau.

La virtualisation imbriquée vous permet de créer un environnement avec plusieurs machines virtuelles au sein de la machine virtuelle d’un modèle de labo. La publication du modèle fournit à chaque utilisateur du labo une machine virtuelle configurée avec plusieurs machines virtuelles en son sein.

## <a name="what-is-nested-virtualization"></a>Qu’est-ce que la virtualisation imbriquée ?
La virtualisation imbriquée vous permet de créer des machines virtuelles au sein d’une machine virtuelle. La virtualisation imbriquée est effectuée via Hyper-V et elle est disponible seulement sur les machines virtuelles Windows.

Pour plus d’informations sur la visualisation imbriquée, consultez les articles suivants :

- [Virtualisation imbriquée dans Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Guide pratique pour activer la virtualisation imbriquée dans une machine virtuelle Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Utiliser la virtualisation imbriquée dans Azure Lab Services
Les étapes importantes sont :

1. Créez un modèle de machine **Windows** de **grande** taille pour le labo. 
2. Connectez-vous à cette machine et [activez la virtualisation imbriquée](../../virtual-machines/windows/nested-virtualization.md).


La procédure suivante vous indique les étapes détaillées : 

1. Créez un compte de labo si vous n’en avez pas encore. Pour obtenir des instructions, consultez [Tutoriel : Configurer un compte de labo avec Azure Lab Services](tutorial-setup-lab-account.md).
2. Accédez au [site web Azure Lab Services](https://labs.azure.com). 
3. Sélectionnez **Se connecter** et entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft. 
4. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez un **nom** pour votre laboratoire. 
    2. Spécifiez le **nombre de machines virtuelles** maximal dans le laboratoire. Vous pouvez augmenter ou diminuer le nombre de machines virtuelles après avoir créé un lab ou dans un lab existant. Pour plus d’informations, consultez [Mettre à jour le nombre de machines virtuelles d’un lab](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Sélectionnez **Enregistrer**.

        ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Sur la page **Select virtual machine specifications** (sélectionner les spécifications de machine virtuelle), procédez aux étapes suivantes :
    1. Sélectionnez **Grande** pour la taille des machines virtuelles à créer dans le labo. Actuellement, seule la taille Grande prend en charge la virtualisation imbriquée.
    2. Choisissez une image de machine virtuelle qui soit une **image Windows**. La virtualisation imbriquée est disponible seulement sur les machines Windows. 
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
8. Dans la page **Configurer le modèle**, sélectionnez **Se connecter** pour vous connecter au modèle de machine virtuelle pour configurer la virtualisation imbriquée. Vous pouvez également la configurer ultérieurement après avoir terminé les étapes décrites dans cet Assistant. 
9. Dans le modèle de machine virtuelle, configurez la virtualisation imbriquée et configurez un réseau virtuel avec plusieurs machines virtuelles. Pour obtenir des instructions pas à pas, consultez [Guide pratique pour activer la virtualisation imbriquée dans une machine virtuelle Azure](../../virtual-machines/windows/nested-virtualization.md). Voici un résumé rapide des étapes : 
    1. Activez la fonctionnalité Hyper-V sur le modèle de machine virtuelle.
    2. Configurez un réseau virtuel interne avec une connectivité Internet pour les machines virtuelles imbriquées.
    3. Créez des machines virtuelles via le Gestionnaire Hyper-V.
    4. Affectez une adresse IP aux machines virtuelles.
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

À présent, chaque utilisateur obtient une seule machine virtuelle qui comprend un environnement avec plusieurs machines virtuelles en son sein. Pour découvrir comment ajouter des utilisateurs au labo et leur envoyer un lien d’inscription, consultez l’article suivant : [Ajouter des utilisateurs au labo](tutorial-setup-classroom-lab.md#add-users-to-the-lab).