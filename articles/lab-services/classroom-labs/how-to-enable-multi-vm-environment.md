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
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332311"
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
1. Accédez au [site web Azure Lab Services](https://labs.azure.com). Notez qu’Internet Explorer 11 n’est pas encore pris en charge. 
2. Sélectionnez **Se connecter** et entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft. 
3. Sélectionnez **Nouveau labo**. 
    
    ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez un **nom** pour votre laboratoire. 
    2. Sélectionnez **Grande (virtualisation imbriquée)** ou **Moyenne (virtualisation imbriquée)** pour la **taille de la machine virtuelle**.
    6. Sélectionnez une **image** Windows que vous souhaitez utiliser. La virtualisation imbriquée est disponible seulement sur les machines Windows. 
    4. Ensuite, sélectionnez **Suivant**. 

        ![Créer un laboratoire de classe](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. Dans la page **Informations d’identification de machine virtuelle**, spécifiez les informations d’identification par défaut pour toutes les machines virtuelles du labo. Spécifiez le **nom** et le **mot de passe** de l’utilisateur, puis sélectionnez **Suivant**.  

        ![Fenêtre Nouveau labo](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Notez le nom d'utilisateur et le mot de passe que vous créez. Ils ne s’afficheront plus.
    3. Dans la page **Stratégies du labo**, entrez le nombre d’heures allouées à chaque utilisateur (**quota pour chaque utilisateur**) en dehors des heures planifiées pour le labo, puis sélectionnez **Terminer**. 

        ![Quota pour chaque utilisateur](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Vous devez voir l’écran suivant qui indique l’état de la création du modèle de machine virtuelle. La création du modèle dans le laboratoire prend jusqu'à 20 minutes. 

    ![État de la création du modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. Dans la page **Modèle**, sélectionnez **Personnaliser le modèle** dans la barre d’outils. 

    ![Bouton Personnaliser le modèle](../media/how-to-create-manage-template/customize-template-button.png)
2. Dans la boîte de dialogue **Personnaliser le modèle**, sélectionnez **Continuer**. Une fois que vous avez démarré le modèle et apporté des modifications, il n’a plus la même configuration que les machines virtuelles dernièrement publiées pour vos utilisateurs. Les modifications de modèle sont reflétées sur les machines virtuelles de vos utilisateurs seulement une fois que vous republiez.

    ![Boîte de dialogue Personnaliser](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Sélectionnez le bouton **Se connecter au modèle** dans la barre d’outils pour vous connecter au modèle de machine virtuelle afin de configurer la virtualisation imbriquée, et suivez les instructions. S’il s’agit d’une machine Windows, vous verrez une option permettant de télécharger le fichier RDP. 

    ![Se connecter au modèle de machine virtuelle](../media/how-to-create-manage-template/connect-template-vm.png) 
9. Dans le modèle de machine virtuelle, configurez la virtualisation imbriquée et configurez un réseau virtuel avec plusieurs machines virtuelles. Pour obtenir des instructions pas à pas, consultez [Guide pratique pour activer la virtualisation imbriquée dans une machine virtuelle Azure](../../virtual-machines/windows/nested-virtualization.md). Voici un résumé rapide des étapes : 
    1. Activez la fonctionnalité Hyper-V sur le modèle de machine virtuelle.
    2. Configurez un réseau virtuel interne avec une connectivité Internet pour les machines virtuelles imbriquées.
    3. Créez des machines virtuelles via le Gestionnaire Hyper-V.
    4. Affectez une adresse IP aux machines virtuelles.
10. Dans la page **Modèle**, sélectionnez **Publier** dans la barre d’outils. 

    ![Bouton Publier le modèle](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Une fois que vous publiez, vous ne pouvez pas annuler la publication. 
8. Dans la page **Publier le modèle**, entrez le nombre de machines virtuelles que vous souhaitez créer dans le labo, puis sélectionnez **Publier**. 

    ![Publier le modèle - nombre de machines virtuelles](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Vous pouvez voir l’**état de publication** du modèle sur la page. Ce processus peut prendre jusqu’à une heure. 

    ![Publier un modèle - progression](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>Étapes suivantes

À présent, chaque utilisateur obtient une seule machine virtuelle qui comprend un environnement avec plusieurs machines virtuelles en son sein. Pour découvrir comment ajouter des utilisateurs au labo et leur envoyer un lien d’inscription, consultez l’article suivant : [Ajouter des utilisateurs au labo](tutorial-setup-classroom-lab.md#add-users-to-the-lab).