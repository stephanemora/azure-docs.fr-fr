---
title: S’abonner à une offre dans Azure Stack | Microsoft Docs
description: Créer des abonnements pour des offres dans Azure Stack
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/11/2018
ms.author: brenduns
ms.openlocfilehash: 9153649774a67533649fb62da83a3f50abd592da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295208"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Créer des abonnements pour des offres dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Après avoir [créé une offre](azure-stack-create-offer.md), les utilisateurs ont besoin d’un abonnement à cette offre pour pouvoir l’utiliser. Les utilisateurs peuvent s’abonner à une offre de deux façons :

- En tant qu’opérateur cloud, vous pouvez créer un abonnement pour un utilisateur à partir du portail d’administration. Les abonnements créés peuvent être destinés à des offres publiques et privées.
- En tant qu’un utilisateur locataire, vous pouvez vous abonner à une offre publique lorsque vous utilisez le portail utilisateur.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Créer un abonnement en tant qu’opérateur cloud

Les opérateurs cloud peuvent utiliser le portail d’administration pour créer un abonnement à une offre pour un utilisateur.  Vous pouvez créer des abonnements pour les membres de votre propre locataire d’annuaire.  Lorsque la [mutualisation](azure-stack-enable-multitenancy.md) est activée, vous pouvez également créer des abonnements pour les utilisateurs dans des locataires d’annuaires supplémentaires.

Si vous ne voulez pas que vos locataires puissent créer leurs propres abonnements, rendez vos offres privées et créez ensuite des abonnements pour vos locataires. Cette approche est courante lors de l’intégration d’Azure Stack avec les systèmes externes de catalogue de service ou de facturation.

Après avoir créé un abonnement pour un utilisateur, ce dernier peut se connecter au portail utilisateur et constater qu’il est abonné à l’offre.  

### <a name="to-create-a-subscription-for-a-user"></a>Créer un abonnement pour un utilisateur

1. Dans le portail d’administration, accédez à **Abonnements utilisateur.**
2. Sélectionnez **Ajouter**. Sous **Nouvel abonnement utilisateur**, entrez les informations suivantes :  

   - **Nom d’affichage** : un nom convivial pour identifier l’abonnement qui s’affiche en tant que *nom d’abonnement utilisateur*.
   - **Utilisateur** : spécifiez un utilisateur d’un locataire d’annuaire disponible pour cet abonnement. Le nom d’utilisateur apparaît en tant que *Propriétaire*.  Le format du nom d’utilisateur dépend de votre solution d’identité. Par exemple : 

     - **Azure AD:**  *&lt;user1>@&lt;contoso.onmicrosoft.com>*

     - **AD FS:**  *&lt;user1>@&lt;azurestack.local>*

   - **Locataire d’annuaire** : sélectionnez le locataire d’annuaire auquel appartient le compte d’utilisateur. Si vous n’avez pas activé la mutualisation, seul votre locataire d’annuaire local est disponible.

3. Sélectionnez **Offre**. Sous **Offres**, choisissez une **offre** pour cet abonnement. Étant donné que vous créez l’abonnement pour un utilisateur, sélectionnez **Privé** pour l’état de l’accessibilité.

4. Sélectionnez **Créer** pour créer l’abonnement. Vous verrez le nouvel abonnement sous **Abonnement utilisateur**. Lorsque l’utilisateur se connecte au portail, il voit les détails de l’abonnement.

### <a name="to-make-an-add-on-plan-available"></a>Pour rendre un plan additionnel disponible

Un opérateur cloud peut ajouter à tout moment un plan additionnel à un abonnement créé précédemment :

1. Dans le portail d’administration, sélectionnez **Autres services** > **Abonnements utilisateur**. Sélectionnez l’abonnement que vous souhaitez modifier.

2. Sélectionnez **Modules complémentaires**, puis **+Ajouter**.  

3. Sous **Ajouter un plan**, sélectionnez le plan à ajouter en tant que module complémentaire.

## <a name="create-a-subscription-as-a-user"></a>Créer un abonnement en tant qu’utilisateur

En tant qu’utilisateur, vous pouvez vous connecter au portail utilisateur pour trouver et vous abonner aux offres publiques et aux plans additionnels pour votre locataire d’annuaire (organisation).

>[!NOTE]
>Si l’environnement de Azure Stack prend en charge la [mutualisation](azure-stack-enable-multitenancy.md), vous pouvez également vous abonner à des offres à partir d’un locataire d’annuaire distant.

### <a name="to-subscribe-to-an-offer"></a>S’abonner à une offre

1. [Connectez-vous](azure-stack-connect-azure-stack.md) au portail utilisateur Azure Stack https://portal.local.azurestack.external) et sélectionnez **Prendre un abonnement**.

   ![Prendre un abonnement](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. Sous **Prendre un abonnement**, entrez le nom convivial de l’abonnement dans **Nom d’affichage**. Sélectionnez **Offre** et, sous **Choisir une offre**, sélectionnez une offre. Sélectionnez **Créer** pour créer l’abonnement.

   ![Créer une offre](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Une fois que vous êtes abonné à une offre, actualisez le portail pour voir les services qui font partie du nouvel abonnement.
4. Pour afficher l’abonnement que vous avez créé, sélectionnez **Plus de services**, puis **Abonnements**. Sélectionnez l’abonnement pour en afficher les détails.  

### <a name="to-subscribe-to-an-add-on-plan"></a>S’abonner à un plan additionnel

Si une offre possède un plan additionnel, vous pouvez ajouter ce plan à votre abonnement à tout moment.  

1. Dans le portail utilisateur, sélectionnez **Plus de services** > **Abonnements** puis sélectionnez l’abonnement que vous souhaitez modifier. Si des plans additionnels sont disponibles, l’option **+Ajouter un plan** est active et il existe une vignette pour **Plans additionnels**.

   >[!NOTE]
   >Si l’option **+Ajouter un plan** n’est pas disponible, cela signifie qu’aucun plan additionnel n’existe pour l’offre associée à cet abonnement.

1. Sélectionnez **+Ajouter un plan** ou la vignette **Plans additionnels**. Sous **Plans additionnels**, sélectionnez le plan que vous souhaitez ajouter.

## <a name="next-steps"></a>Étapes suivantes

[Approvisionner une machine virtuelle](azure-stack-provision-vm.md)
