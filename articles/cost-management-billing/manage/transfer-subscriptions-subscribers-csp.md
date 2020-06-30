---
title: Transférer des abonnements Azure entre des abonnés et des CSP
description: Découvrez comment transférer des abonnements Azure entre des abonnés et des CSP.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: banders
ms.openlocfilehash: 91f11502a136ca08d62072808d270f8e840a8710
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85106439"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Transférer des abonnements Azure entre des abonnés et des CSP

Cet article présente les étapes principales utilisées pour transférer des abonnements Azure vers et à partir des partenaires fournisseurs de solutions cloud (CSP) et leurs clients.

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>Transférer des abonnements EA à un partenaire CSP

Les partenaires CSP de facturation directe certifiés [Fournisseur de services managés Azure Expert (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) peuvent demander le transfert d’abonnements Azure pour leurs clients ayant contracté un contrat Entreprise direct (EA). Les transferts d’abonnements sont autorisés uniquement pour les clients qui ont accepté un contrat client Microsoft (MCA) et acheté un plan Azure.

Lorsque la demande est approuvée, le CSP peut alors fournir une facture combinée à ses clients. Pour en savoir plus sur le transfert d’abonnements par les CSP, consultez [Obtenir la propriété de facturation des abonnements Azure pour votre compte MPA](mpa-request-ownership.md).

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Autres transferts d’abonnements à un partenaire CSP

Pour transférer tout autre abonnement Azure à un partenaire CSP, l’abonné doit déplacer les ressources des abonnements sources vers les abonnements CSP. Respectez les instructions suivantes pour déplacer des ressources entre des abonnements.

1. Collaborez avec votre partenaire CSP pour créer des abonnements Azure CSP cibles.
1. Vérifiez que les abonnements CSP sources et cibles sont dans le même locataire Azure Active Directory (Azure AD).  
    Vous ne pouvez pas changer le locataire Azure AD d’un abonnement Azure CSP. Au lieu de cela, vous devez ajouter ou associer l’abonnement source au locataire CSP Azure AD. Pour plus d’informations, consultez [Associer ou ajouter un abonnement Azure à votre locataire Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Lorsque vous associez un abonnement à un autre annuaire Azure AD, les utilisateurs possédant des rôles attribués à l’aide du [contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/role-assignments-portal.md) perdent leur accès. Les administrateurs d’abonnements classiques, entre autres les administrateurs de services et les coadministrateurs perdent également leur accès.
    > - Les attributions de stratégie sont également supprimées d’un abonnement lorsque celui-ci est associé à un autre répertoire.
1. Le compte d’utilisateur que vous utilisez pour effectuer le transfert doit disposer de l’accès [RBAC](add-change-subscription-administrator.md) propriétaire aux deux abonnements.
1. Avant de commencer, [validez](/rest/api/resources/resources/validatemoveresources) le fait que toutes les ressources Azure peuvent être déplacées de l’abonnement source à l’abonnement de destination.  
    Certaines ressources Azure ne peuvent pas être déplacées entre des abonnements. Pour afficher la liste complète des ressources Azure qui peuvent être déplacées, consultez [Prise en charge des opérations de déplacement pour les ressources](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Azure CSP prend en charge uniquement les ressources Azure Resource Manager. Si des ressources Azure dans l’abonnement source ont été créées à l’aide du modèle de déploiement Azure classique, vous devez les migrer vers [Azure Resource Manager](https://docs.microsoft.com/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) avant la migration. Vous devez être partenaire pour pouvoir afficher la page web.

1. Vérifiez que tous les services d’abonnement sources utilisent le modèle Azure Resource Manager. Ensuite, transférez les ressources de l’abonnement source vers l’abonnement de destination à l’aide du [déplacement de ressources Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Le déplacement des ressources Azure entre des abonnements peut entraîner un temps d’arrêt du service, en fonction des ressources des abonnements.

## <a name="transfer-csp-subscription-to-other-offer"></a>Transférer un abonnement CSP à une autre offre

Pour transférer tout autre abonnement d’un partenaire CSP vers une autre offre Azure, l’abonné doit déplacer des ressources entre des abonnements CSP sources et des abonnements CSP cibles.

1. Créer des abonnements Azure cibles.
1. Vérifiez que les abonnements source et cible sont dans le même locataire Azure Active Directory (Azure AD). Pour plus d’informations sur la modification d’un locataire Azure AD, consultez [Associer ou ajouter un abonnement Azure à votre locataire Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    Notez que l’annuaire à changer n’est pas celui de l’abonnement CSP. Par exemple, vous passez d’un abonnement CSP à un abonnement avec paiement à l’utilisation. Vous devez changer l’annuaire de l’abonnement Paiement à l’utilisation pour qu’il corresponde à l’annuaire CSP.

    > [!IMPORTANT]
    >  - Lorsque vous associez un abonnement à un autre annuaire, les utilisateurs auxquels des rôles ont été attribués à l’aide du [contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/role-assignments-portal.md) perdent leur accès. Les administrateurs d’abonnements classiques, entre autres les administrateurs de services et les coadministrateurs perdent également leur accès.
    >  - Les attributions de stratégie sont également supprimées d’un abonnement lorsque celui-ci est associé à un autre répertoire.

1. Le compte d’utilisateur que vous utilisez pour effectuer le transfert doit disposer de l’accès [RBAC](add-change-subscription-administrator.md) propriétaire aux deux abonnements.
1. Avant de commencer, [validez](/rest/api/resources/resources/validatemoveresources) le fait que toutes les ressources Azure peuvent être déplacées de l’abonnement source à l’abonnement de destination.
    > [!IMPORTANT]
    >  - Certaines ressources Azure ne peuvent pas être déplacées entre des abonnements. Pour afficher la liste complète des ressources Azure qui peuvent être déplacées, consultez [Prise en charge des opérations de déplacement pour les ressources](../../azure-resource-manager/management/move-support-resources.md).

1. Transférez les ressources de l’abonnement source vers l’abonnement de destination à l’aide du [déplacement de ressources Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Le déplacement des ressources Azure entre des abonnements peut entraîner un temps d’arrêt du service, en fonction des ressources des abonnements.

## <a name="next-steps"></a>Étapes suivantes
- [Obtenir la propriété de facturation des abonnements Azure associés pour votre compte MPA](mpa-request-ownership.md).
- En savoir plus sur la façon de [Gérer des comptes et des abonnements avec la facturation Azure](index.yml).
