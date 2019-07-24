---
title: Transfert de la propriété de facturation d’un abonnement Azure à un autre compte | Microsoft Docs
description: Décrit comment transférer la propriété de facturation d’un abonnement Azure à un autre compte et Questions fréquentes (FAQ) concernant la procédure
keywords: transférer abonnement azure, azure transfert abonnement, déplacer un abonnement azure vers un autre compte, changer abonnement azure, transférer abonnement azure à un autre compte, transférer facturation azure
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d41e32065e3de37eb8f01ab1b836040e7f57b12
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657846"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Transfert de la propriété de facturation d’un abonnement Azure à un autre compte

Vous pouvez souhaiter transférer la propriété de facturation de votre abonnement Azure si vous quittez votre organisation ou voulez que votre abonnement soit facturé sur un autre compte. Le transfert de la propriété de facturation à un autre compte permet aux administrateurs dans le nouveau compte d’effectuer des tâches liées à la facturation telles que le changement du mode de paiement, l’affichage des frais et l’annulation de l’abonnement.

Si vous souhaitez conserver la propriété de facturation tout en changeant le type de votre abonnement, consultez [Changer d’offre pour votre abonnement Azure](billing-how-to-switch-azure-offer.md). Si vous souhaitez contrôler qui peut gérer les ressources de l’abonnement, consultez [Rôles intégrés pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Si vous êtes client Contrat Entreprise (EA), les administrateurs de votre entreprise peuvent transférer la propriété de facturation de vos abonnements entre les comptes. Pour plus d’informations, consultez [Transférer la propriété de facturation des abonnements Contrat Entreprise (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Transférer la propriété de facturation d’un abonnement Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur du compte de facturation auquel est rattaché l’abonnement que vous souhaitez transférer. Pour savoir si vous êtes administrateur, consultez [Questions fréquentes (FAQ)](#faq).

1. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Sélectionnez **Abonnements** dans le volet gauche. Selon votre accès, vous devrez peut-être sélectionner une étendue de facturation, puis sélectionner **Abonnements** ou **Abonnements Azure**.

1. Sélectionnez **Transférer la propriété de facturation** pour l’abonnement que vous voulez transférer. 

   ![Sélectionner l’abonnement à transférer](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Entrez l’adresse e-mail d’un utilisateur qui est administrateur de facturation du compte qui sera le nouveau propriétaire de l’abonnement, puis sélectionnez **Envoyer une demande de transfert**.

    > [!IMPORTANT]
    >
    > Si vous transférez la propriété de facturation de votre abonnement à un compte d’utilisateur dans un autre locataire Azure AD, toutes les affectations [RBAC (contrôle d’accès en fonction du rôle)](../role-based-access-control/overview.md) servant à gérer les ressources de l’abonnement sont définitivement supprimées. Seul le nouveau propriétaire aura accès à la gestion des ressources de l’abonnement. Pour plus d’informations, consultez [Transfert de l’abonnement à un utilisateur dans un autre locataire Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md).
  
    ![Page d’envoi du transfert](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. L’utilisateur reçoit un e-mail contenant des instructions pour l’examen de votre demande de transfert.

   ![E-mail de transfert d’abonnement envoyé au destinataire](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Pour approuver la demande de transfert, l’utilisateur sélectionne le lien figurant dans l’e-mail, puis suit les instructions. L’utilisateur doit sélectionner un mode de paiement à utiliser pour payer l’abonnement. En outre, si l’utilisateur n’a pas de compte Azure, il doit s’inscrire pour obtenir un compte. 

   ![Page web de transfert du premier abonnement](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Page web de transfert du deuxième abonnement](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Page web de transfert du deuxième abonnement](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Vous avez réussi ! L’abonnement est à présent transféré.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Transfert de l’abonnement à un compte dans un autre locataire Azure AD

Un locataire Azure Active Directory (AD) est créé pour vous quand vous vous inscrivez à Azure. Le locataire représente votre compte. Le locataire vous permet de gérer l’accès à vos abonnements et ressources.

Quand vous créez un abonnement, il est hébergé dans le locataire Azure AD de votre compte. Si vous souhaitez fournir à d’autres utilisateurs l’accès à vos abonnement ou ressources, vous devez les inviter à votre locataire. Cela vous aide à contrôler l’accès à vos abonnements et ressources.

Quand vous transférez la propriété de facturation de votre abonnement à un compte dans un autre locataire Azure AD, l’abonnement est déplacé vers le locataire du nouveau compte. Tous les utilisateurs, groupes ou principaux de service qui disposaient d’un [RBAC (contrôle d’accès en fonction du rôle)](../role-based-access-control/overview.md) pour gérer les ressources de l’abonnement perdent leur accès. Seul l’utilisateur dans le nouveau compte qui accepte votre demande de transfert a accès à la gestion des ressources. Pour que les utilisateurs continuent de bénéficier de l’accès, le nouveau propriétaire doit [les ajouter manuellement à l’abonnement](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Transfert des abonnements Dev/Test Paiement à l’utilisation, Microsoft Partner Network (MPN) et Visual Studio

Les abonnements Visual Studio et Microsoft Partner Network bénéficient chaque mois de crédits Azure. Quand vous transférez ces abonnements, votre crédit n’est pas disponible dans le compte de facturation de destination. L’abonnement utilise le crédit disponible dans le compte de facturation de destination. Supposons que Pierre transfère un abonnement Visual Studio Enterprise au compte de Marie le 9 septembre et que Marie accepte le transfert. Une fois le transfert terminé, l’abonnement commence à utiliser le crédit disponible dans le compte de Marie. Le crédit est réinitialisé le 9 de chaque mois. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Transférer la propriété de facturation des abonnements Contrat Entreprise (EA)

L’administrateur d’entreprise peut transférer la propriété des abonnements entre des comptes au sein d’une inscription. Pour plus d’informations, consultez [Transférer la propriété du compte](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) dans le portail EA.

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>Étapes suivantes après avoir accepté la propriété de facturation

Si vous avez accepté la propriété de facturation d’un abonnement Azure, nous vous recommandons de passer en revue ces étapes suivantes :

1. Passez en revue et mettez à jour l’administrateur de service, les coadministrateurs et les autres rôles RBAC. Pour plus d’informations, consultez [Ajout ou modification des administrateurs d’abonnements Azure](billing-add-change-azure-subscription-administrator.md) et [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../role-based-access-control/role-assignments-portal.md).
1. Mettez à jour les informations d’identification associées aux services de cet abonnement, notamment :
   1. Certificats de gestion accordant à l’utilisateur des droits d’administrateur sur les ressources d’abonnement. Pour plus d'informations, consultez [Créer et télécharger un certificat de gestion pour Microsoft Azure](../cloud-services/cloud-services-certs-create.md)
   1. Touches d’accès rapide pour les services tels que Storage. Pour plus d’informations, consultez [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md).
   1. Informations d’identification d’accès à distance pour les services tels que les machines virtuelles Azure.
1. Si vous collaborez avec un partenaire, envisagez de mettre à jour son ID de partenaire pour cet abonnement. Vous pouvez mettre à jour l’ID partenaire dans le [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez [Lier un ID partenaire à vos comptes Azure](billing-partner-admin-link-started.md).

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Types d’abonnements pris en charge

Le transfert d’abonnement dans le portail Azure est disponible pour les types d’abonnements listés ci-dessous. Le transfert n’est pas pris en charge pour les abonnements [Essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p/) ou [Azure dans Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) Pour obtenir une solution de contournement, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md). Pour transférer d’autres abonnements, comme [Sponsoring](https://azure.microsoft.com/offers/ms-azr-0036p/) ou des plans de support, [contactez le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Abonnés Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [Plateformes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise : BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plan Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [Via le portail EA](#EA).

\*\* Uniquement pris en charge pour les comptes qui sont créés lors de l’inscription sur le site web Azure. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Questions fréquentes (FAQ) pour les expéditeurs

Ces Questions fréquentes (FAQ) concernent les utilisateurs qui transfèrent la propriété de facturation d’un abonnement Azure à un autre compte.

### <a name="whoisaa"></a> Qui est administrateur de facturation d’un compte ?

Un administrateur de facturation est une personne qui a l’autorisation de gérer la facturation d’un compte. Il est autorisé à accéder à la facturation sur le [portail Azure](https://portal.azure.com) et à effectuer diverses tâches liées à la facturation comme la création d’abonnements, la consultation et le paiement des factures ou la mise à jour des modes de paiement.

Pour identifier les abonnements pour lesquels vous êtes administrateur de facturation, effectuez les étapes suivantes :

1. Accédez à la [page Gestion des coûts + facturation dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Sélectionnez **Abonnements** dans le volet gauche. Selon votre accès, vous devrez peut-être sélectionner une étendue de facturation, puis sélectionner **Abonnements** ou **Abonnements Azure**.
1. La page Abonnements liste tous les abonnements pour lesquels vous êtes administrateur de facturation.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Tous les éléments sont-ils transférés ? Notamment les groupes de ressources, les machines virtuelles, les disques et les autres services en cours d’exécution ?

Toutes les ressources telles que les machines virtuelles, les disques et les sites web sont transférées au nouveau compte. Toutefois, si vous transférez l’abonnement à un compte dans un autre locataire Azure AD, les affectations des [rôles Administrateur](billing-add-change-azure-subscription-administrator.md) et de [RBAC (contrôle d’accès en fonction du rôle)](../role-based-access-control/role-assignments-portal.md) sur l’abonnement [ne sont pas transférées](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Les [inscriptions des applications](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) et autres services spécifiques du locataire ne sont pas non plus transférés avec l’abonnement.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Puis-je transférer la propriété à un compte dans un autre pays ?
Malheureusement, les transferts entre pays ne peuvent pas être effectués dans le portail Azure. Pour transférer votre abonnement d’un pays à un autre, [contactez le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Je suis administrateur de deux comptes. Puis-je transférer un abonnement depuis un de mes comptes à l’autre ?
Oui, vous pouvez transférer l’abonnement entre vos comptes. Vos comptes étant conceptuellement considérés comme les comptes de deux utilisateurs différents, vous pouvez effectuer les étapes ci-dessus pour transférer des abonnements entre ces comptes.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Un transfert d’abonnement entraîne-t-il une interruption de service ?

Si vous transférez un abonnement à un utilisateur dans le même locataire Azure AD, l’impact est nul sur les ressources en cours d’exécution dans l’abonnement.  Toutefois, si vous transférez l’abonnement à un utilisateur dans un autre locataire, tous les utilisateurs, groupes et principaux de service qui disposaient d’un [RBAC (contrôle d’accès en fonction du rôle)](../role-based-access-control/overview.md) pour gérer les ressources de l’abonnement perdent leur accès. 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Le destinataire a-t-il accès à l’historique des opérations et de facturation ?

Les seules informations accessibles au destinataire sont le coût du dernier mois de votre abonnement. L’utilisation restante et l’historique de facturation ne sont pas transférés avec l’abonnement.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Comment migrer des données et des services de mon abonnement Azure vers un nouvel abonnement ?

Si vous ne pouvez pas transférer la propriété de l’abonnement, vous pouvez migrer vos ressources manuellement. Consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Si je transfère un abonnement Visual Studio ou Microsoft Partner Network, mon crédit est-il reporté avec l’abonnement dans le nouveau compte ?

Non, votre crédit n’est pas disponible dans le nouveau compte. L’utilisateur qui a accepté la demande de transfert doit avoir une licence Visual Studio pour procéder à l’acceptation. L’abonnement utilise le crédit Visual Studio qui est disponible dans le compte de l’utilisateur. Pour plus d’informations, consultez [Transfert des abonnements Dev/Test Paiement à l’utilisation, Microsoft Partner Network (MPN) et Visual Studio](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Questions fréquentes (FAQ) pour les destinataires

Ces Questions fréquentes (FAQ) concernent les utilisateurs qui deviennent titulaires de la facturation d’un abonnement Azure d’un autre compte.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Si je deviens titulaire de la facturation d’un abonnement d’un autre compte, les utilisateurs dans ce compte ont-ils toujours accès à mes ressources ?

Si l’abonnement est transféré à un compte dans le même locataire Azure AD, tous les utilisateurs, groupes et principaux de service qui disposaient d’un [RBAC (contrôle d’accès en fonction du rôle)](../role-based-access-control/overview.md) pour gérer les ressources de l’abonnement conservent leur accès. Pour afficher les utilisateurs qui ont un accès RBAC à l’abonnement, effectuez les étapes suivantes :

1. Accédez à la [page Abonnement du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement que vous souhaitez vérifier, puis sélectionnez **Contrôle d’accès (IAM)** dans le volet gauche.
1. Sélectionnez **Attributions de rôles** en haut de la page. La page des attributions de rôles liste tous les utilisateurs qui disposent d’un accès RBAC à l’abonnement.

Si l’abonnement est transféré à un compte dans un autre locataire Azure AD, tous les utilisateurs, groupes et principaux de service qui disposaient d’un [RBAC (contrôle d’accès en fonction du rôle)](../role-based-access-control/overview.md) pour gérer les ressources de l’abonnement perdent leur accès. Toutefois, même s’ils n’ont plus d’accès RBAC, ils peuvent toujours accéder à l’abonnement par le biais de certains mécanismes de sécurité, notamment :

* Certificats de gestion accordant à l’utilisateur des droits d’administrateur sur les ressources d’abonnement. Pour plus d'informations, consultez la rubrique [Créer et télécharger un certificat de gestion pour Microsoft Azure](../cloud-services/cloud-services-certs-create.md).
* Touches d’accès rapide pour les services tels que Storage. Pour plus d’informations, consultez la rubrique [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md).
* Informations d’identification d’accès à distance pour les services tels que les machines virtuelles Azure.

Le destinataire doit envisager la mise à jour des secrets associés au service s’il doit restreindre l’accès aux ressources. La plupart des ressources peuvent être mises à jour en procédant comme suit :

  1. Connectez-vous au [Portail Azure](https://portal.azure.com).
  2. Dans le menu Hub, sélectionnez **Toutes les ressources**.
  3. Sélectionnez la ressource.
  4. Dans la page des ressources, cliquez sur **Paramètres**. Ici, vous pouvez afficher et mettre à jour les clés secrètes existantes.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Si je deviens titulaire de la facturation d’un abonnement au milieu d’un cycle de facturation, dois-je payer pour l’intégralité du cycle ?

Votre compte est responsable du paiement de toute utilisation qui est signalée à compter du moment du transfert. Certaines opérations peuvent avoir été effectuées avant le transfert mais déclarées ultérieurement. L’utilisation figure sur la facture de votre compte.

### <a name="can-i-use-a-different-payment-method"></a>Puis-je utiliser un autre mode de paiement ?

Oui. Lors de l’acceptation de la demande de transfert, vous pouvez sélectionner un mode de paiement existant qui est lié à votre compte ou ajouter un nouveau mode de paiement.

## <a name="troubleshooting"></a>Résolution de problèmes

### <a id="no-button"></a> Pourquoi le bouton Transférer un abonnement ne s’affiche-t-il pas ?

Malheureusement, le transfert d’abonnement en libre-service n’est pas disponible pour votre compte de facturation. Nous ne prenons pas en charge le transfert de la propriété de facturation d’un compte Contrat Entreprise (EA) dans le portail Azure. En outre, les comptes Contrat client Microsoft qui sont créés dans le cadre d’une collaboration avec l’équipe des ventes Microsoft ne prennent pas en charge le transfert de la propriété de facturation des abonnements. 

### <a id="no-button"></a> Pourquoi mon type d’abonnement ne prend-il pas en charge le transfert ? 

Malheureusement, tous les types d’abonnements ne prennent pas en charge le transfert de la propriété de facturation. Pour voir la liste des types d’abonnements qui prennent en charge les transferts, consultez [Types d’abonnements pris en charge](#supported-subscription-types)

### <a id="no-button"></a> Quand j’essaie de transférer la propriété de facturation d’un abonnement, je reçois une erreur de refus d’accès. Pourquoi ? 

Vous voyez cette erreur si vous essayez de transférer un abonnement Plan Microsoft Azure et que vous n’avez pas l’autorisation nécessaire. Pour transférer un abonnement Plan Microsoft Azure, vous devez être propriétaire ou contributeur de la section de facture à laquelle l’abonnement est facturé. Pour plus d’informations, consultez [Gérer les abonnements associés à la section de facture](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue et mettez à jour l’administrateur de service, les coadministrateurs et les autres rôles RBAC. Pour plus d’informations, consultez [Ajout ou modification des administrateurs d’abonnements Azure](billing-add-change-azure-subscription-administrator.md) et [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../role-based-access-control/role-assignments-portal.md).
