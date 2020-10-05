---
title: Lier votre ID de partenaire pour activer le crédit Partenaires sur des ressources déléguées
description: Découvrez comment associer votre ID de partenaire pour utiliser le crédit Partenaires sur des ressources de clients que vous gérez par le biais d’Azure Lighthouse.
ms.date: 09/04/2020
ms.topic: how-to
ms.openlocfilehash: 0a9e7f51e90b38bad24eada5a665ca60bf43452f
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89493271"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>Lier votre ID de partenaire pour activer le crédit Partenaires sur des ressources déléguées

Si vous êtes membre du [Microsoft Partner Network](https://partner.microsoft.com/), vous pouvez lier votre ID de partenaire à des informations d’identification utilisées pour gérer des ressources de clients déléguées. Vous pouvez ainsi suivre votre impact sur tous les engagements de clients et recevoir un [crédit Partenaires pour des services gérés](/partner-center/partner-earned-credit).

Si vous [intégrez des clients à des offres de service gérés dans la Place de marché Azure](publish-managed-services-offers.md), ce lien se produit automatiquement, en utilisant l’ID MPN associé au compte Espace partenaires utilisé pour publier les offres. Aucune action supplémentaire n’est nécessaire pour recevoir le crédit Partenaires de ces clients.

Si vous [intégrez des clients à l’aide de modèles Azure Resource Management](onboard-customer.md), vous devez prendre des mesures pour créer ce lien. Pour cela, [liez votre ID MPN](../../cost-management-billing/manage/link-partner-id.md) avec au moins un compte d’utilisateur dans votre locataire qui a accès à chacun de vos abonnements intégrés.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Associer votre ID de partenaire lors de l’intégration de nouveaux clients

Quand vous intégrez des clients à l’aide de modèles Azure Resource Manager, utilisez la procédure suivante pour lier votre ID partenaire et activer le crédit Partenaires. Vous devez connaître votre [ID de partenaire MPN](/partner-center/partner-center-account-setup#locate-your-mpn-id) pour effectuer ces étapes.

Pour simplifier, nous vous recommandons de créer un compte de principal de service dans votre locataire, de l’associer à votre ID MPN, puis d’accorder à chaque client que vous intégrez un accès à ce compte avec un [rôle intégré Azure éligible au crédit Partenaires](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

1. [Créez un compte de principal de service](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) dans votre locataire gérant. Pour cet exemple, nous allons nommer ce principal de service Compte Automation de crédit Partenaires.
1. En utilisant ce compte de principal de service, [établissez un lien avec votre ID de partenaire](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) dans votre locataire gérant. Cette opération est unique.
1. Quand vous [intégrez un client à l’aide de modèles Azure Resource Manager](onboard-customer.md), veillez à inclure une autorisation qui inclut le compte Automation de crédit Partenaires en tant qu’utilisateur disposant d’un [rôle intégré Azure éligible au crédit Partenaires](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

En suivant ces étapes, chaque locataire de client que vous gérez est associé à votre ID de partenaire, ce qui vous permet de recevoir le crédit Partenaires de ces clients. Le compte Automation de crédit Partenaires n’a pas besoin de s’authentifier ni d’effectuer des actions dans le locataire du client.

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Ajoute votre ID de partenaire aux clients précédemment intégrés

Si vous avez déjà intégré un client, vous ne voulez peut-être pas effectuer un autre déploiement pour ajouter le principal de service de votre compte Automation de crédit Partenaires. Vous pouvez plutôt associer l’ID MPN à un compte d’utilisateur qui a déjà accès au locataire de ce client. Vérifiez que le compte a reçu un [rôle intégré Azure éligible au crédit Partenaires](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

Une fois que le compte a été [lié à votre ID de partenaire](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) dans votre locataire gérant, vous pouvez recevoir le crédit Partenaires de ce client.

## <a name="confirm-partner-earned-credit"></a>Confirmer le crédit Partenaires

Vous pouvez [voir les détails du crédit Partenaires dans le portail Azure](/partner-center/partner-earned-credit-explanation#azure-cost-management) pour vérifier les coûts qui ont bénéficié de l’avantage de ce crédit Partenaires.

Si vous avez suivi les étapes ci-dessus et que vous ne voyez pas l’association, ouvrez une demande de support dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez le programme [Microsoft Partner Network](/partner-center/mpn-overview).
- Découvrez comment le [crédit Partenaires est calculé et payé](/partner-center/partner-earned-credit-explanation).
- [Intégrez des clients](onboard-customer.md) à Azure Lighthouse.
