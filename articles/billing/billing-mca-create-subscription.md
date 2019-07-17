---
title: Créer un nouvel abonnement Azure associé à votre compte de facturation
description: Découvrez comment ajouter un nouvel abonnement Azure dans le Portail Azure.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 558410d980d261780f7287d1e27ed704b356fc2b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490940"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Créer un abonnement Azure associé dans le cadre de votre Contrat client Microsoft

Créez des abonnements supplémentaires dans votre compte de facturation pour configurer des environnements séparés pour le développement et le test, pour des raisons de sécurité ou pour isoler les données pour des raisons de conformité.

Cet article s’applique à un compte de facturation associé à un Contrat client Microsoft. [Vérifiez que vous avez accès à un contrat client Microsoft](#check-access). Si vous souhaitez créer des abonnements pour d’autres types de comptes de facturation, voir [Créer un abonnement supplémentaire dans le Portail Azure](billing-create-subscription.md).

Pour créer un abonnement, vous devez être **propriétaire de section de facture**, **contributeur de section de facture** ou **créateur d’abonnement Azure**. Pour plus d’informations, consultez [Rôles et tâches liés à la facturation des abonnements](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Pour fournir à d’autres utilisateurs l’autorisation de créer des abonnements Azure pour votre compte de facturation, consultez [Autoriser d’autres utilisateurs à créer des abonnements Azure](#give-others-permission).

## <a name="create-a-subscription"></a>Création d’un abonnement

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez **Abonnements**.

   ![Capture d’écran montrant la recherche d’abonnements dans le portail](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Sélectionnez **Ajouter**

4. Si vous avez accès à plusieurs comptes de facturation, sélectionnez le compte de facturation pour votre Contrat client Microsoft.

   ![Capture d’écran qui montre la page d’abonnement](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Sélectionnez un profil de facturation. Les frais de votre abonnement seront facturés au profil de facturation sélectionné. Si vous avez accès à un seul profil de facturation, la sélection est grisée.

6. Sélectionnez une section de facture. Les frais de votre abonnement sont facturés en fonction de cette section de la facture du profil de facturation. Si vous avez accès à une seule section de facturation, la sélection est grisée.

7. Sélectionnez un plan pour l’abonnement. Sélectionnez **Offre Azure pour DevTest** si vous envisagez d’utiliser cet abonnement pour le développement. Pour tester des charges de travail, utilisez **Offre Microsoft Azure**. Si vous n’avez accès qu’à un seul plan, la sélection est grisée.

8. Entrez un nom pour l’abonnement. Le nom vous aide à identifier facilement l’abonnement dans le portail Azure.

9. Sélectionnez **Créer**.

## <a name="give-others-permission"></a>Donner des autorisations à d’autres utilisateurs

Ajoutez des utilisateurs en tant que créateurs d’abonnements Azure sur une section de la facture afin de leur donner l’autorisation de créer des abonnements Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche d’abonnements dans le portail](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Accédez à la section de facture. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation ou un profil de facturation. Dans le profil ou compte de facturation, sélectionnez **Sections de facture**, puis une section de la facture dans la liste. Tous les abonnements créés par les utilisateurs seront facturés en fonction de cette section de la facture.
   
   ![Capture d’écran montrant comment sélectionner des sections de factures](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. Sélectionnez **Gestion des accès (IAM)** dans le volet en haut à gauche.

5. En haut de la page, sélectionnez **Ajouter**.

6. Sélectionnez le rôle **Créateur de l’abonnement Azure**.

7. Entrez l’adresse e-mail de l’utilisateur auquel vous souhaitez accorder l’accès.

8. Sélectionnez **Enregistrer**.

## <a name="check-access"></a>Vérifier l’accès
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez des questions ou besoin d’aide, créez une [demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Autoriser d’autres utilisateurs à créer des ressources Azure à l’aide de rôles intégrés](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Créer une machine virtuelle Windows](../virtual-machines/windows/quick-create-portal.md)
- [Créer une machine virtuelle Linux](../virtual-machines/linux/quick-create-portal.md)
- [Créer des groupes d’administration pour l’organisation et l’administration des ressources](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
