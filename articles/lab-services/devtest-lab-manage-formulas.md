---
title: Gérer les formules dans Azure DevTest Labs pour créer des machines virtuelles | Microsoft Docs
description: Cet article explique comment créer une formule à partir d’une base (une image personnalisée, une image Place de marché ou une autre formule) ou d’une machine virtuelle existante.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a668c1f7cf401c109c1041232d7f28dd2accd750
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760400"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Gérer les formules Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Cet article explique comment créer une formule à partir d’une base (une image personnalisée, une image Place de marché ou une autre formule) ou d’une machine virtuelle existante. Cet article vous guide aussi dans la gestion des formules existantes.

## <a name="create-a-formula"></a>Créer une formule
Toute personne disposant des autorisations *Utilisateurs* de DevTest Labs est en mesure de créer des machines virtuelles en utilisant une formule comme base. Il existe deux façons de créer des formules : 

* À partir d’une base : quand vous voulez définir toutes les caractéristiques de la formule.
* À partir d’une machine virtuelle de laboratoire existante : quand vous voulez créer une formule à partir des paramètres d’une machine virtuelle existante.

Pour plus d’informations sur l’ajout des utilisateurs et des autorisations, consultez [Ajouter des propriétaires et des utilisateurs dans Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Créer une formule à partir d’une base
Les étapes suivantes vous guideront tout au long du processus de création d’une formule à partir d’une image personnalisée, image Marketplace ou autre formule.

1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.

3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  

4. Dans la page du laboratoire, sélectionnez **Formules (bases réutilisables)** .
   
    ![Menu de formule](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Dans la page **Formules**, sélectionnez **+ Ajouter**.
   
    ![Ajouter une formule](./media/devtest-lab-create-formulas/add-formula.png)

6. Dans la page **Choisir une base**, sélectionnez la base (image personnalisée, image de la Place de marché ou formule) à partir de laquelle vous voulez créer la formule.
   
    ![Liste de base](./media/devtest-lab-create-formulas/base-list.png)

7. Sur l’onglet **Paramètres de base** de la page **Créer une formule**, spécifiez les valeurs suivantes :
   
    * **Nom de la formule** : entrez un nom pour votre formule. Cette valeur s’affiche dans la liste des images de base lorsque vous créez une machine virtuelle. Le nom est validé au fur et à mesure que vous le tapez. S’il n’est pas valide, un message indique les exigences d’un nom valide.
    * **Nom d’utilisateur** - Entrez un nom d’utilisateur qui obtient les privilèges d’administrateur.
    * **Mot de passe** - Entrez, ou sélectionnez dans la liste déroulante, une valeur qui est associée au secret (mot de passe) que vous souhaitez utiliser pour l’utilisateur spécifié. Pour en savoir plus sur l’enregistrement des secrets dans un coffre de clés et leur utilisation lors de la création des ressources de laboratoire, consultez [Stocker les secrets dans Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).
    * **Taille de la machine virtuelle** : sélectionnez **Modifier la taille** pour modifier la taille de la machine virtuelle. 
    * **Artefacts** : sélectionnez la page **Ajouter ou supprimer des artefacts**, dans laquelle vous pouvez sélectionner et configurer les artefacts que vous souhaitez ajouter à l’image de base. Pour plus d’informations sur les artefacts, consultez [Créer des artefacts personnalisés pour votre machine virtuelle Azure DevTest Labs](devtest-lab-artifact-author.md).
8. Basculez vers l’onglet **Paramètres avancés** et spécifiez les valeurs suivantes :
    - **Réseau virtuel** : pour changer de réseau virtuel, sélectionnez **Changer le réseau virtuel**. 
    - **Sous-réseau** : pour changer de sous-réseau, sélectionnez **Changer de sous-réseau**. 
    - **Configuration de l’adresse IP** - Spécifiez si vous souhaitez que les adresses IP soient partagées, privées ou publiques. Pour plus d’informations sur les adresses IP partagées, consultez [Comprendre les adresses IP partagées dans Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Date et heure d’expiration** : spécifiez la date et l’heure d’expiration de la machine virtuelle afin qu’elle soit automatiquement supprimée. 
    - **Make this machine claimable** (Rendre cette machine exigible) - Lorsqu’une machine est « exigible », cela signifie qu’aucune propriété ne lui sera affectée au moment de la création. En revanche, les utilisateurs du laboratoire seront en mesure de prendre possession de la machine (de la « revendiquer ») dans la page du laboratoire.     
    - **Nombre d’instances revendicables** : indiquez le nombre d’instances revendicables à créer. 
8. Sélectionnez **Envoyer** pour créer la formule.

9. Une fois la formule créée, elle s’affiche dans la liste de la page **Formules**.

### <a name="create-a-formula-from-a-vm"></a>Créer une formule depuis une machine virtuelle
Les étapes suivantes vous guident dans le processus de création d’une formule à partir d’une machine virtuelle existante. 

> [!NOTE]
> Pour créer une formule à partir d’une machine virtuelle, la machine virtuelle doit avoir été créée après le 30 mars 2016. 
> 
> 

1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  
4. Dans la page **Vue d’ensemble** du laboratoire, sélectionnez la machine virtuelle à partir de laquelle vous souhaitez créer la formule.
   
    ![Machines virtuelles de labo](./media/devtest-lab-create-formulas/my-vms.png)
5. Dans la page de la machine virtuelle, cliquez sur **Créer une formule (base réutilisable)** .
   
    ![Créer une formule](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Dans la page **Créer une formule**, entrez un **Nom** et une **Description** pour votre nouvelle formule.
   
    ![Page Créer une formule](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Sélectionnez **OK** pour créer la formule.

## <a name="modify-a-formula"></a>Modifier une formule
Pour modifier une formule, procédez comme suit :

1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  
4. Dans la page du laboratoire, sélectionnez **Formules (bases réutilisables)** .
   
    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Dans la page des **formules de laboratoire**, sélectionnez la formule à modifier.
6. Dans la page **Mettre à jour la formule**, effectuez les modifications souhaitées, puis sélectionnez **Mettre à jour**.

## <a name="delete-a-formula"></a>Supprimer une formule
Pour supprimer une formule, procédez comme suit :

1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  
4. Dans la page **Paramètres** du laboratoire, sélectionnez **Formules**.
   
    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Dans la page des **formules de laboratoire**, sélectionnez les points de suspension à droite de la formule à supprimer.
   
    ![Menu de formule](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Dans le menu contextuel de la formule, sélectionnez **Supprimer**.
   
    ![Menu contextuel de la formule](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. sélectionnez **Oui** dans la boîte de dialogue de confirmation de la suppression.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Billets de blog connexes
* [Custom images or formulas? (Images personnalisées ou formules ?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Étapes suivantes
Quand vous avez créé une formule à utiliser lors de la création d’une machine virtuelle, l’étape suivante consiste à [ajouter une machine virtuelle à votre laboratoire](devtest-lab-add-vm.md).

