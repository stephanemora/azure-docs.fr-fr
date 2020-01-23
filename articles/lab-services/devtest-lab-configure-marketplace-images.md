---
title: Configuration des paramètres d’image Azure Marketplace dans Azure DevTest Labs
description: Configurer quelles images Azure Marketplace peuvent être utilisées lors de la création d’une machine virtuelle dans Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2dc3aa000cefc0e65305b58f8fdce93b94bfd35f
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169549"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Configuration des paramètres d’image Azure Marketplace dans Azure DevTest Labs
DevTest Labs prend en charge la création de machines virtuelles basées sur des images Azure Marketplace en fonction de la manière dont vous avez configuré des images Azure Marketplace à utiliser dans votre laboratoire. Cet article montre comment spécifier, le cas échéant, les images Azure Marketplace qui peuvent être utilisées lors de la création de machines virtuelles dans un laboratoire. Cela garantit que votre équipe a accès uniquement aux images de la Place de marché dont elle a besoin. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Sélection des images Azure Marketplace autorisées lors de la création d’une machine virtuelle
1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires. 
4. Dans le panneau du laboratoire, sélectionnez **Configuration et stratégies**.
5. Dans le panneau **Configuration et stratégies** du laboratoire, sous **Bases de machine virtuelle**, sélectionnez **Images de la Place de marché**.
6. Spécifiez si vous souhaitez que toutes les images Azure Marketplace qualifiées puissent être utilisées comme base d’une nouvelle machine virtuelle. Si vous sélectionnez **Oui**, toutes les images Azure Marketplace répondant à tous les critères suivants sont autorisées dans le laboratoire :
   
   * L’image crée une seule machine virtuelle, **et**
   * L’image utilise Azure Resource Manager pour configurer des machines virtuelles, **et**
   * L’image ne nécessite pas l’achat d’un plan de licences supplémentaire
     
     Si vous ne souhaitez autoriser aucune image, ou vous souhaitez spécifier les images pouvant être utilisées, sélectionnez **Non**.
     
     ![Option permettant d’autoriser l’utilisation de toutes les images Marketplace en tant qu’images de base pour des machines virtuelles](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Si vous sélectionnez **Non** à l’étape précédente, la case **Images autorisées/Sélectionner tout** est cochée. 
   Vous pouvez utiliser cette option avec la zone de recherche pour rapidement sélectionner ou désélectionner tous les éléments affichés dans la liste.
   * Sélectionnez les images Azure Marketplace que vous souhaitez autoriser pour la création de machines virtuelles individuellement en activant la case à cocher correspondante de chaque image.
   * N’effectuez aucune sélection dans la liste si vous ne souhaitez autoriser aucune image Azure Marketplace dans le laboratoire.
   
     ![Vous pouvez spécifier les images Azure Marketplace pouvant être utilisées en tant qu’images de base pour des machines virtuelles](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez configuré comment les images Azure Marketplace sont autorisées durant la création d’une machine virtuelle, l’étape suivante consiste à [ajouter une machine virtuelle à votre laboratoire](devtest-lab-add-vm.md).

