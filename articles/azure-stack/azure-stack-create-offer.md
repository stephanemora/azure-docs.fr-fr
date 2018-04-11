---
title: Créer une offre dans Azure Stack | Microsoft Docs
description: En tant qu’administrateur cloud, apprenez à créer une offre pour vos utilisateurs dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2018
ms.author: brenduns
ms.openlocfilehash: 6a59d0c8144492ef907e5c395f05e4e8a16678a5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-offer-in-azure-stack"></a>Créer une offre dans Azure Stack

Les [offres](azure-stack-key-features.md) sont des groupes d’un ou plusieurs plans que les fournisseurs proposent à l’achat ou à l’abonnement aux utilisateurs. Ce document montre comment créer une offre comprenant le [plan créé](azure-stack-create-plan.md) à la dernière étape. Cette offre donne aux abonnés la possibilité d’approvisionner des machines virtuelles.

1. Connectez-vous au portail d’administration Azure Stack (https://adminportal.local.azurestack.external) > cliquez sur **Nouveau** > **Offres + plans de locataire** > **Offre**.

   ![](media/azure-stack-create-offer/image01.png)
2. Sur le volet **Nouvelle offre**, renseignez le **Nom d’affichage** et le **Nom de la ressource**, puis sélectionnez un **Groupe de ressources** nouveau ou existant. Le nom d’affichage correspond au nom convivial de l’offre. Ce nom convivial est la seule information sur l’offre que les utilisateurs verront au moment de l’abonnement. Par conséquent, veillez à utiliser un nom intuitif qui aide l’utilisateur à comprendre en quoi elle consiste. Seul l’administrateur peut voir le nom de la ressource. Il s’agit du nom que les administrateurs utilisent pour gérer l’offre en tant que ressource Azure Resource Manager.

   ![](media/azure-stack-create-offer/image01a.png)
3. Cliquez sur **Plans de base** pour ouvrir le volet **Plan**, sélectionnez les plans que vous souhaitez inclure à l’offre, puis cliquez sur **Sélectionnez**. Cliquez sur **Créer** pour créer l’offre.

   ![](media/azure-stack-create-offer/image02.png)
4. Après avoir créé l’offre, vous pouvez modifier son état. Les offres doivent être rendues *publiques* pour permettre aux utilisateurs d’avoir une vue d’ensemble lorsqu’ils s’abonnent. Les offres peuvent être :
   - **Public** : ils sont visibles pour les utilisateurs.
   - **Privé** : visibles uniquement par les administrateurs cloud. Utile lors de l’élaboration du plan ou de l’offre, ou si l’administrateur cloud souhaite [créer chaque abonnement pour les utilisateurs](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Retiré**: ils sont fermés aux nouveaux abonnés. L’administrateur cloud peut utiliser cet état pour empêcher tout abonnement futur, sans que cela affecte les abonnés actuels.

   > [!TIP]  
   > Les modifications apportées à l’offre ne sont pas immédiatement visibles par l’utilisateur. Pour voir les modifications et afficher la nouvelle offre, les utilisateurs pourraient devoir se déconnecter et se reconnecter au portail de l’utilisateur. 

   Pour modifier l’état de l’offre : 

   - **Version 1803 et versions ultérieures** :  
     Dans le panneau Vue d’ensemble de l’offre, cliquez sur **État de l’accessibilité**, sélectionnez l’état que vous souhaitez utiliser, comme *Public*, puis cliquez sur **Enregistrer**. 
 
     ![Sélectionner État de l’accessibilité](media/azure-stack-create-offer/change-state.png) 

     Vous pouvez également, après avoir accédé à une offre, accéder à **Paramètres de l’offre**, puis sélectionner **État de l’accessibilité** pour modifier l’état. 

   - **Avant la version 1803** :  
     Cliquez sur **Toutes les ressources**, recherchez votre nouvelle offre, cliquez dessus, puis sur **Changer d’état** et enfin sur **Public**.

  
   > [!NOTE] 
   > Vous pouvez également utiliser PowerShell pour créer des offres, des plans et des quotas par défaut. Pour plus d’informations, consultez [Azure Stack Service Administrator readme](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin) (Lisez-moi d’Azure Stack Service Administrator).
   >


### <a name="next-steps"></a>Étapes suivantes
[Création d’abonnements](azure-stack-subscribe-plan-provision-vm.md)      
[Approvisionner une machine virtuelle](azure-stack-provision-vm.md)
