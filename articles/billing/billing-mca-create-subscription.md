---
title: Créer un nouvel abonnement Azure pour votre compte de facturation | Microsoft Docs
description: Découvrez comment ajouter un nouvel abonnement Azure dans le Portail Azure.
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 105b8481486c088a05e3acb95081d3ee55b55f52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60372019"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Créer un nouvel abonnement Azure pour le contrat de client de Microsoft

Créer des abonnements supplémentaires à votre compte de facturation pour configurer des environnements distincts pour le développement et de test, de la sécurité ou pour isoler des données pour des raisons de conformité.

Cet article s’applique à un compte de facturation d’un contrat de client de Microsoft. [Vérifiez si vous avez accès à un contrat de client Microsoft](#check-access-to-a-microsoft-customer-agreement). Si vous souhaitez créer des abonnements pour les autres comptes de facturation, consultez [créer un abonnement supplémentaire dans le portail Azure](billing-create-subscription.md).

Pour créer un abonnement, vous devez être un **propriétaire de section facture**, **contributeur de section facture**, ou **créateur de l’abonnement Azure**. Pour plus d’informations, consultez [les tâches et les rôles de facturation d’abonnement](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Pour fournir d’autres d’autorisation de créer des abonnements Azure pour votre compte de facturation, consultez [autoriser d’autres utilisateurs à créer des abonnements Azure](#give-others-permission-to-create-azure-subscriptions).

## <a name="create-a-subscription-in-the-azure-portal"></a>Créer un abonnement dans le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez **Abonnements**.

   ![Capture d’écran montrant la recherche dans le portail des abonnements](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Sélectionnez **Ajouter**

4. Si vous avez accès à plusieurs comptes de facturation, sélectionnez le compte de facturation pour votre contrat de client de Microsoft.

   ![Capture d’écran montrant page Créer un abonnement](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Sélectionnez un profil de facturation. Les frais pour votre abonnement reflètent sur la facture de facturation du profil et vous seront versés à l’aide de ses méthodes de paiement. Si vous avez accès à un seul profil de facturation, la sélection est grisée.

6. Sélectionnez une section de la facture. Les frais pour votre abonnement reflète sur cette section de facture de facturation du profil. Si vous avez accès pour qu’une seule section de facture, la sélection est grisée.

7. Sélectionnez un plan pour l’abonnement. Sélectionnez **Microsoft Azure Plan pour DevTest**, si vous envisagez d’utiliser cet abonnement pour le développement ou de charges de travail tests autre utilisent **Microsoft Azure Plan**. Si vous avez accès à un seul plan, la sélection est grisée.

8. Entrez un nom pour l’abonnement. Le nom vous aide à identifier facilement l’abonnement dans le portail Azure.

9. Sélectionnez **Créer**.

## <a name="give-others-permission-to-create-azure-subscriptions"></a>Autoriser d’autres utilisateurs à créer des abonnements Azure

Ajouter des utilisateurs en tant que créateurs d’abonnement Azure sur une section de la facture afin de leur donner l’autorisation de créer des abonnements Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail des abonnements](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Accédez à la section de facture. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation ou d’un profil de facturation. Dans le compte de facturation ou un profil, sélectionnez **sections de facture** et ensuite une section de la facture.

4. Sélectionnez **gestion d’accès (IAM)** depuis le côté supérieur gauche.

5. En haut de la page, sélectionnez **Ajouter**.

6. Sélectionnez **créateur de l’abonnement Azure** pour le rôle.

   ![Capture d’écran montrant l’attribution de rôle de créateur d’abonnement Azure à un utilisateur](./media/billing-mca-create-subscription/billing-mca-add-azure-subscription-creator.png)

7. Entrez l’adresse de messagerie de l’utilisateur auquel vous souhaitez accorder l’accès.

8. Sélectionnez **Enregistrer**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifiez l’accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Autoriser d’autres utilisateurs à créer des ressources Azure à l’aide de rôles intégrés](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Créer une machine virtuelle windows](../virtual-machines/windows/quick-create-portal.md)
- [Créer une machine virtuelle linux](../virtual-machines/linux/quick-create-portal.md)
- [Créer des groupes d’administration pour l’organisation et l’administration des ressources](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
