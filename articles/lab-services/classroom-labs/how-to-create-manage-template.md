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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 08fbe9565356dc1b7db952fdd265770fef600ca8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989040"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Créer et gérer un modèle de laboratoire de classe dans Azure Lab Services
Le modèle de laboratoire est une image de machine virtuelle de base, à partir de laquelle toutes les machines virtuelles des utilisateurs sont créées. Configurez la machine virtuelle du modèle de façon qu’elle propose exactement ce que vous souhaitez fournir aux utilisateurs du laboratoire. Vous pouvez indiquer le nom et la description du modèle que voient les utilisateurs du laboratoire. Ensuite, publiez le modèle pour rendre les instances du modèle de machine virtuelle accessibles aux utilisateurs de votre laboratoire. Quand vous publiez un modèle, Azure Lab Services crée les machines virtuelles dans le laboratoire à l’aide du modèle. Le nombre de machines virtuelles créées dans ce processus est identique au nombre maximal d’utilisateurs autorisés dans le laboratoire, que vous pouvez définir dans la politique d’utilisation du laboratoire. Toutes les machines virtuelles ont la même configuration que le modèle.

Cet article explique comment créer et gérer un modèle de machine virtuelle dans un laboratoire de classe Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publier un modèle lors de la création d’un laboratoire de classe
Pour savoir comment publier un modèle lors de la création d’un laboratoire de classe, consultez [Créer un laboratoire de classe](how-to-manage-classroom-labs.md#create-a-classroom-lab)
 
## <a name="set-or-update-template-title-and-description"></a>Définir ou mettre à jour un titre et une description pour le modèle
Effectuez les étapes suivantes pour définir un titre et une description pour la première fois, et pour les mettre à jour par la suite. 

1. Dans la page **Modèle**, entrez le nouveau **titre** pour le laboratoire.  
2. Entrez la nouvelle **description** pour le modèle. Lorsque vous déplacez le focus hors de la zone de texte, elle est automatiquement enregistrée. 

    ![Nom et description du modèle](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Mettre à jour un modèle de machine virtuelle
Suivez les étapes ci-dessous pour mettre à jour un modèle de machine virtuelle.  

1. Attendez le démarrage du modèle de machine virtuelle, sélectionnez le bouton **Se connecter au modèle** dans la barre d’outils pour vous connecter au modèle, et suivez les instructions. S’il s’agit d’une machine Windows, vous verrez une option permettant de télécharger le fichier RDP. 
1. Une fois que vous êtes connecté au modèle et que vous y apportez des modifications, il n’a plus la même configuration que les machines virtuelles dernièrement publiées pour vos utilisateurs. Les modifications de modèle sont reflétées sur les machines virtuelles de vos utilisateurs seulement une fois que vous republiez.

    ![Se connecter au modèle de machine virtuelle](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. Installez les logiciels nécessaires pour que les étudiants puissent travailler en laboratoire (par exemple Visual Studio, l’Explorateur Stockage Azure, et ainsi de suite). 
1. Déconnectez-vous (fermez votre session Bureau à distance) du modèle de machine virtuelle. 
1. **Arrêtez** le modèle de machine virtuelle en sélectionnant **Arrêter le modèle**. 
1. Suivez les étapes de la section suivante pour **publier** le modèle de machine virtuelle mis à jour. 

## <a name="publish-the-template-vm"></a>Publier le modèle de machine virtuelle  
Si vous ne publiez pas le modèle lors de la création du laboratoire, vous pourrez le publier plus tard. Avant de publier le modèle, vous pouvez vous connecter au modèle de machine virtuelle et le mettre à jour avec un logiciel. Quand vous publiez un modèle, Azure Lab Services crée les machines virtuelles dans le laboratoire à l’aide du modèle. Le nombre de machines virtuelles créées dans ce processus correspond au nombre de machines virtuelles que vous avez spécifié la première fois que vous les avez publiées ou à ce que vous avez spécifié dans la page de pool de machines virtuelles. Toutes les machines virtuelles ont la même configuration que le modèle. 

1. Dans la page **Modèle**, sélectionnez **Publier** dans la barre d’outils. 
1. Dans la boîte de message **Publier le modèle**, lisez le message, puis sélectionnez **Publier**. Ce processus peut prendre un certain temps en fonction du nombre de machines virtuelles en cours de création.

    ![Bouton Publier](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > Une fois un modèle publié, sa publication ne peut pas être annulée. Vous pouvez republier le modèle. 
1. Vous pouvez voir le statut du processus de publication dans la page Modèle. Attendez que l’état du modèle passe à **Publié**. 

    ![État de publication](../media/how-to-create-manage-template/publish-status.png)
1. Passez à la page **Machines virtuelles** et vérifiez que des machines virtuelles se trouvent à l’état **Non affectée**. Ces machines virtuelles ne sont pas encore affectées aux étudiants. Attendez que les machines virtuelles soient créées. Elles doivent être à l’état **Arrêtée**. Vous pouvez démarrer la machine virtuelle d’un étudiant, vous y connecter, l’arrêter et la supprimer dans cette page. Vous pouvez démarrer les machines virtuelles dans cette page ou laisser les étudiants le faire. 

    ![Machines virtuelles à l’état Arrêtée](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et contrôler l’utilisation d’un labo](how-to-configure-student-usage.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)
