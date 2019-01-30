---
title: Gérer un modèle de laboratoire de classe dans Azure Lab Services | Microsoft Docs
description: Découvrez comment créer et gérer un modèle de laboratoire de classe dans Azure Lab Services.
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
ms.openlocfilehash: 2f34b6c71c448f7273ba7477f18f5abb8f89cdec
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391201"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Créer et gérer un modèle de laboratoire de classe dans Azure Lab Services
Le modèle de laboratoire est une image de machine virtuelle de base, à partir de laquelle toutes les machines virtuelles des utilisateurs sont créées. Configurez la machine virtuelle du modèle de façon qu’elle propose exactement ce que vous souhaitez fournir aux utilisateurs du laboratoire. Vous pouvez indiquer le nom et la description du modèle que voient les utilisateurs du laboratoire. Ensuite, publiez le modèle pour rendre les instances du modèle de machine virtuelle accessibles aux utilisateurs de votre laboratoire. Quand vous publiez un modèle, Azure Lab Services crée les machines virtuelles dans le laboratoire à l’aide du modèle. Le nombre de machines virtuelles créées dans ce processus est identique au nombre maximal d’utilisateurs autorisés dans le laboratoire, que vous pouvez définir dans la politique d’utilisation du laboratoire. Toutes les machines virtuelles ont la même configuration que le modèle.

Cet article explique comment créer et gérer un modèle de machine virtuelle dans un laboratoire de classe Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publier un modèle lors de la création d’un laboratoire de classe
Vous pouvez configurer et publier un modèle lors de la création d’un laboratoire de classe.

1. Accédez au [site web Azure Lab Services](https://labs.azure.com). 
2. Sélectionnez **Se connecter** et entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft. 
3. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez un **nom** pour votre laboratoire. 
    2. Spécifiez le **nombre d’utilisateurs** maximal autorisé dans le laboratoire. 
    6. Sélectionnez **Enregistrer**.

        ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Sur la page **Select virtual machine specifications** (sélectionner les spécifications de machine virtuelle), procédez aux étapes suivantes :
    1. Sélectionnez une **taille** pour les machines virtuelles (VM) créées dans le laboratoire. 
    2. Sélectionnez la **région** dans laquelle vous souhaitez créer les VM. 
    3. Sélectionnez l'**image de machine virtuelle** à utiliser pour créer des VM dans le laboratoire. 
    4. Sélectionnez **Suivant**.

        ![Définir les spécifications de VM](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
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
8. Les étapes suivantes sont facultatives dans ce didacticiel : 
    1. Démarrez le modèle de machine virtuelle en sélectionnant **Démarrer**.
    2. Connectez-vous au modèle de machine virtuelle en sélectionnant **Se connecter**. 
    3. Installez et configurez des logiciels sur votre modèle de machine virtuelle. 
    4. **Arrêtez** la machine virtuelle.  
    5. Entrez une **description** pour le modèle

        ![Suivant sur la page Configurer le modèle](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Sélectionnez **Suivant** sur la page du modèle. 
10. Sur la page **Publier le modèle**, effectuez les actions suivantes. 
    1. Pour publier le modèle immédiatement, cochez la case *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Je comprends que je ne peux pas modifier le modèle après publication. Ce processus ne peut être fait qu’une fois et cela peut prendre jusqu’à une heure), puis sélectionnez **Publier**.  

        > [!WARNING]
        > Une fois que vous publiez, vous ne pouvez pas annuler la publication. 
    2. Pour publier ultérieurement, sélectionnez **Enregistrer pour plus tard**. Vous pouvez publier le modèle de VM une fois l’Assistant terminé. Pour plus d’informations sur la configuration et la publication après l’exécution de l’assistant, consultez la section [Publier le modèle](#publish-the-template) de l’article [Gérer des laboratoires de classe dans Azure Lab Services](how-to-manage-classroom-labs.md).

        ![Publier un modèle](../media/tutorial-setup-classroom-lab/publish-template.png)
11. La **progression de la publication** du modèle s’affiche. Ce processus peut prendre jusqu’à une heure. 

    ![Publier un modèle - progression](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. La page suivante s’affiche lorsque le modèle est publié avec succès. Sélectionnez **Terminé**.

    ![Publier un modèle - succès](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Le **tableau de bord** du laboratoire s’affiche. 
    
    ![Tableau de bord de laboratoire de classe](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

 
## <a name="set-or-update-template-title-and-description"></a>Définir ou mettre à jour un titre et une description pour le modèle
Effectuez les étapes suivantes pour définir un titre et une description pour la première fois, et pour les mettre à jour par la suite. 

1. Dans la section **Modèle**, déplacez votre souris sur le **nom** ou la **description** du modèle, puis sélectionnez-le/la. 
2. Entrez le **nouveau nom** ou la **nouvelle description** pour le modèle, puis appuyez sur **Entrée**.

    ![Nom et description du modèle](../media/how-to-create-manage-template/template-name-description.png)

## <a name="set-up-or-update-a-template-vm"></a>Configurer ou mettre à jour un modèle de machine virtuelle
 Vous vous connectez au modèle de machine virtuelle et vous installez les logiciels nécessaires avant de les rendre accessibles à vos étudiants. Effectuez les étapes suivantes pour configurer un modèle de machine virtuelle pour la première fois ou pour mettre à jour la machine virtuelle. 

1. Attendez que la machine virtuelle soit prête. Une fois qu’elle est prête, le bouton **Démarrer** doit être activé. Pour démarrer la machine virtuelle, sélectionnez **Démarrer**.

    ![Démarrer le modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Lisez l’avertissement, puis sélectionnez **Démarrer**. 

    ![Démarrer le modèle - avertissement](../media/how-to-create-manage-template/start-template-warning.png)
1. Une fois le modèle démarré, pour vous connecter à la machine virtuelle, sélectionnez **Se connecter**, puis suivez les instructions. 
1. Installez les logiciels nécessaires pour que les étudiants puissent travailler en laboratoire (par exemple Visual Studio, l’Explorateur Stockage Azure, et ainsi de suite). 
2. Déconnectez-vous (fermez votre session Bureau à distance) du modèle de machine virtuelle. 
3. **Arrêtez** le modèle de machine virtuelle en sélectionnant **Arrêter**. 

## <a name="publish-the-template-vm"></a>Publier le modèle de machine virtuelle  
Si vous ne publiez pas le modèle lors de la création du laboratoire, vous pourrez le publier plus tard. Avant de publier le modèle, vous pouvez vous connecter au modèle de machine virtuelle et le mettre à jour avec un logiciel. Quand vous publiez un modèle, Azure Lab Services crée les machines virtuelles dans le laboratoire à l’aide du modèle. Le nombre de machines virtuelles créées dans ce processus est identique au nombre maximal d’utilisateurs autorisés dans le laboratoire, que vous pouvez définir dans la politique d’utilisation du laboratoire. Toutes les machines virtuelles ont la même configuration que le modèle. 

1. Sélectionnez **Publier** dans la section **Modèle**. 

    ![Publier le modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/public-access.png)
1. Dans la boîte de message **Publier le modèle**, lisez le message, puis sélectionnez **Publier**. Ce processus peut prendre un certain temps en fonction du nombre de machines virtuelles en cours de création.
    
    > [!IMPORTANT]
    > Une fois un modèle publié, sa publication ne peut pas être annulée. Vous pouvez republier le modèle. 
4. Attendez que l’état du modèle passe à **Publié**. 

    ![État de publication](../media/how-to-create-manage-template/publish-status.png)
1. Passez à la page **Machines virtuelles** et vérifiez que des machines virtuelles se trouvent à l’état **Non affectée**. Ces machines virtuelles ne sont pas encore affectées aux étudiants. Attendez que les machines virtuelles soient créées. Elles doivent être à l’état **Arrêtée**. Vous pouvez démarrer la machine virtuelle d’un étudiant, vous y connecter, l’arrêter et la supprimer dans cette page. Vous pouvez démarrer les machines virtuelles dans cette page ou laisser les étudiants le faire. 

    ![Machines virtuelles à l’état Arrêtée](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>Republier le modèle 
Une fois le modèle publié, vous pouvez toujours vous y connecter, le mettre à jour, puis le republier. Lorsque vous republiez un modèle de machine virtuelle, tous les utilisateurs de la machine virtuelle sont déconnectés, puis sont recréés selon le modèle mis à jour. 

1. Dans la page du tableau de bord de votre laboratoire, sélectionnez **Republier** dans la section Modèle. 

    ![Bouton Republier](../media/how-to-create-manage-template/republish-button.png)
2. Dans la boîte de message **Republier le modèle**, lisez le texte, puis sélectionnez **Republier** pour continuer. Sinon, sélectionnez **Annuler**. 

    ![Message Republier le modèle](../media/how-to-create-manage-template/republish-template-message.png)
3. L’état de la republication s’affiche dans la vignette de la section **Modèle**.

    ![État de la republication](../media/how-to-create-manage-template/republish-status-publishing.png)
4. Une fois le modèle publié, l’état est défini sur **Publié**. 

    ![Republication réussie](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et contrôler l’utilisation d’un labo](how-to-configure-student-usage.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)
