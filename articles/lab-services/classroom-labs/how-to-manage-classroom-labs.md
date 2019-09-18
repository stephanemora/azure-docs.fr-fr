---
title: Gérer des laboratoires de classe dans Azure Lab Services | Microsoft Docs
description: Apprenez à créer et configurer un laboratoire de classe, à afficher tous les laboratoires de classe, à partager le lien d'inscription avec un autre utilisateur du laboratoire et à supprimer un laboratoire.
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
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 1f9cb82abd5bc0823f5e7bc23fe437007bccc8e0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873578"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gérer des laboratoires de classe dans Azure Lab Services 
Cet article décrit comment créer et supprimer un laboratoire de classe. Il montre également comment afficher tous les laboratoires de classe dans un compte de laboratoire. 

## <a name="prerequisites"></a>Prérequis
Pour configurer un laboratoire de classe dans un compte de laboratoire, vous devez être membre du rôle **Créateur de laboratoire** dans le compte de laboratoire. Le compte utilisé pour créer un compte de laboratoire est ajouté automatiquement à ce rôle. Un propriétaire de laboratoire peut ajouter d’autres utilisateurs au rôle Créateur de laboratoire à l’aide des étapes fournies dans l’article suivant : [Ajouter un utilisateur au rôle Créateur de laboratoire](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Créer un laboratoire de classe

1. Accédez au [site web Azure Lab Services](https://labs.azure.com). Notez qu’Internet Explorer 11 n’est pas encore pris en charge. 
2. Sélectionnez **Connexion**. Sélectionnez ou entrez un **ID utilisateur** qui est un membre du rôle **Créateur de laboratoire** dans le compte de laboratoire, puis entrez un mot de passe. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft. 
3. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez un **nom** pour votre laboratoire. 
    2. Spécifiez le **nombre de machines virtuelles** maximal dans le laboratoire. Vous pouvez augmenter ou diminuer le nombre de machines virtuelles dans le laboratoire par la suite. 
    6. Sélectionnez **Enregistrer**.

        ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Sur la page **Select virtual machine specifications** (sélectionner les spécifications de machine virtuelle), procédez aux étapes suivantes :
    1. Sélectionnez une **taille** pour les machines virtuelles (VM) créées dans le laboratoire. Actuellement, les tailles **petite**, **moyenne**, **moyenne (virtualisation)** , **grande** et **GPU** sont autorisées. Pour plus d’informations, consultez la section [Tailles des machines virtuelles](#vm-sizes).
    1. Sélectionnez la **région** dans laquelle vous souhaitez créer les VM. 
    1. Sélectionnez l'**image de machine virtuelle** à utiliser pour créer des VM dans le laboratoire. Si vous sélectionnez une image Linux, vous voyez une option pour activer la connexion Bureau à distance pour celle-ci. Pour plus d’informations, consultez [Activer la connexion Bureau à distance pour Linux](how-to-enable-remote-desktop-linux.md).
    1. Sélectionnez **Suivant**.

        ![Définir les spécifications de VM](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Sur la page **Définir les informations d'identification**, spécifiez les informations d’identification par défaut pour toutes les VM dans le laboratoire. 
    1. Spécifiez le **nom d’utilisateur** pour toutes les VM du laboratoire.
    2. Spécifiez le **mot de passe** de l’utilisateur. 

        > [!IMPORTANT]
        > Notez le nom d'utilisateur et le mot de passe que vous créez. Ils ne s’afficheront plus.
    3. Désactivez l’option **Use same password for all virtual machines** (Utiliser le même mot de passe pour toutes les machines virtuelles) si vous souhaitez que les étudiants définissent leurs propres mots de passe. Cette étape est **facultative**. 

        Un enseignant peut choisir d’utiliser le même mot de passe pour toutes les machines virtuelles du laboratoire ou d’autoriser les étudiants à des mots de passe pour leurs machines virtuelles. Par défaut, ce paramètre est activé pour toutes les images Windows et Linux, à l’exception d’Ubuntu. Lorsque vous sélectionnez une machine virtuelle **Ubuntu**, ce paramètre est désactivé. Les étudiants sont ainsi invités à définir un mot de passe lorsqu’ils se connectent à la machine virtuelle pour la première fois.
    1. Sélectionnez **Create** (Créer). 

        ![Définition des informations d’identification](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Sur la page **Configurer le modèle**, vous pouvez consulter l’état du processus de création de laboratoire. La création du modèle dans le laboratoire prend jusqu'à 20 minutes. Le modèle de laboratoire est une image de machine virtuelle de base, à partir de laquelle toutes les machines virtuelles des utilisateurs sont créées. Configurez la machine virtuelle du modèle de façon qu’elle propose exactement ce que vous souhaitez fournir aux utilisateurs du laboratoire.  

    ![Configurer le modèle](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Une fois la configuration du modèle terminée, la page suivante s’affiche : 

    ![Page Configurer le modèle une fois terminé](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Les étapes suivantes sont facultatives dans ce didacticiel : 
    2. Connectez-vous au modèle de machine virtuelle en sélectionnant **Se connecter**. S’il s’agit d’un modèle de machine virtuelle Linux, choisissez si vous voulez vous connecter avec SSH ou RDP (si RDP est activé).
    1. Sélectionnez **Réinitialiser le mot de passe** pour réinitialiser le mot de passe pour la machine virtuelle. 
    1. Installez et configurez des logiciels sur votre modèle de machine virtuelle. 
    1. **Arrêtez** la machine virtuelle.  
    1. Entrez une **description** pour le modèle
9. Sélectionnez **Suivant** sur la page du modèle. 
10. Sur la page **Publier le modèle**, effectuez les actions suivantes. 
    1. Pour publier le modèle immédiatement, cochez la case *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Je comprends que je ne peux pas modifier le modèle après publication. Ce processus ne peut être fait qu’une fois et cela peut prendre jusqu’à une heure), puis sélectionnez **Publier**.  Publiez le modèle pour rendre les instances du modèle de machine virtuelle accessible aux utilisateurs de votre laboratoire.

        > [!WARNING]
        > Une fois que vous publiez, vous ne pouvez pas annuler la publication. 
    2. Pour publier ultérieurement, sélectionnez **Enregistrer pour plus tard**. Vous pouvez publier le modèle de VM une fois l’Assistant terminé. Pour plus d’informations sur la configuration et la publication après l’exécution de l’assistant, consultez la section Publier le modèle de l’article [Gérer des laboratoires de classe dans Azure Lab Services](how-to-manage-classroom-labs.md).

        ![Publier un modèle](../media/tutorial-setup-classroom-lab/publish-template.png)
11. La **progression de la publication** du modèle s’affiche. Ce processus peut prendre jusqu’à une heure. 

    ![Publier un modèle - progression](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. La page suivante s’affiche lorsque le modèle est publié avec succès. Sélectionnez **Terminé**.

    ![Publier un modèle - succès](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Le **tableau de bord** du laboratoire s’affiche. 
    
    ![Tableau de bord de laboratoire de classe](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Passez à la page **Machines virtuelles** et vérifiez que des machines virtuelles se trouvent à l’état **Non affectée**. Ces machines virtuelles ne sont pas encore affectées aux étudiants. Elles doivent être à l’état **Arrêtée**. Vous pouvez démarrer la machine virtuelle d’un étudiant, vous y connecter, l’arrêter et la supprimer dans cette page. Vous pouvez démarrer les machines virtuelles dans cette page ou laisser les étudiants le faire. 

    ![Machines virtuelles à l’état Arrêtée](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>Tailles de machine virtuelle  

| Size | Cœurs | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| Petite | 2 | 3,5 Go | Cette taille est idéale pour la ligne de commande, l’ouverture d’un navigateur web, les serveurs web à faible trafic et les bases de données de petite et moyenne taille. |
| Moyenne | 4 | 7 Go | Cette taille est idéale pour les bases de données relationnelles, le caching en mémoire et l’analyse | 
| Moyenne (virtualisation imbriquée) | 4 | 16 Go | Cette taille est idéale pour les bases de données relationnelles, le caching en mémoire et l’analyse. Cette taille prend également en charge la virtualisation imbriquée. <p>Cette taille peut être utilisée dans des scénarios où chaque étudiant a besoin de plusieurs machines virtuelles. Les enseignants peuvent utiliser la virtualisation imbriquée pour configurer quelques machines virtuelles imbriquées de petite taille dans la machine virtuelle. </p> |
| grand | 8 | 32 Go | Cette taille est idéale pour les applications nécessitant des UC plus rapides, de meilleures performances du disque local, des bases de données volumineuses et des caches mémoire volumineux. Cette taille prend également en charge la virtualisation imbriquée |  
| GPU de petite taille (visualisation) | 6 | 56 Go | Cette taille est optimisée pour la visualisation à distance, la diffusion en continu, les jeux et l’encodage avec des infrastructures comme OpenGL ou DirectX. | 
| GPU de petite taille (calcul) | 6 | 56 Go | Cette taille est optimisée pour les applications nécessitant beaucoup de ressources système et de ressources réseau, notamment l'intelligence artificielle et les applications d'apprentissage approfondi (Deep Learning). | 
| GPU de taille moyenne (visualisation) | 12 | 112 Go | Cette taille est optimisée pour la visualisation à distance, la diffusion en continu, les jeux et l’encodage avec des infrastructures comme OpenGL ou DirectX. | 

## <a name="view-all-classroom-labs"></a>Afficher tous les laboratoires de classe
1. Accédez au [portail Azure Lab Services](https://labs.azure.com).
2. Sélectionnez **Connexion**. Sélectionnez ou entrez un **ID utilisateur** qui est un membre du rôle **Créateur de laboratoire** dans le compte de laboratoire, puis entrez un mot de passe. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft. 
3. Vérifiez que tous les laboratoires se trouvent dans le compte de laboratoire sélectionné. 

    ![Tous les laboratoires](../media/how-to-manage-classroom-labs/all-labs.png)
3. Utilisez la liste déroulante du haut pour sélectionner un autre compte de laboratoire. Vous voyez les laboratoires du compte de laboratoire sélectionné. 

## <a name="delete-a-classroom-lab"></a>Supprimer un laboratoire de classe
1. Sur la vignette du laboratoire, sélectionnez les points de suspension (...) situés dans l’angle. 

    ![Sélectionner le laboratoire](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Sélectionnez **Supprimer**. 

    ![Bouton Supprimer](../media/how-to-manage-classroom-labs/delete-button.png)
3. Dans la boîte de dialogue **Delete lab** (Supprimer le laboratoire), sélectionnez **Supprimer**. 

    ![Supprimer la boîte de dialogue](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Basculer vers un autre laboratoire de classe
Pour passer d'un laboratoire de classe à un autre, sélectionnez la liste déroulante des laboratoires du compte lab en haut de la page.

![Sélectionnez le laboratoire dans la liste déroulante du haut de la page](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant que propriétaire de labo, configurer et contrôler l’utilisation d’un labo](how-to-configure-student-usage.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)

