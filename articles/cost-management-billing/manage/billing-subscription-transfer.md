---
title: Transférer la propriété de facturation d’un abonnement Azure
description: Décrit comment transférer la propriété de facturation d’un abonnement Azure à un autre compte et Questions fréquentes (FAQ) concernant la procédure
keywords: transférer abonnement azure, azure transfert abonnement, déplacer un abonnement azure vers un autre compte, changer abonnement azure, transférer abonnement azure à un autre compte, transférer facturation azure
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 84b36c1357bedfc120cec72af84fdd79f52a2f57
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78245382"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Transfert de la propriété de facturation d’un abonnement Azure à un autre compte

Vous pouvez transférer la propriété de la facturation de votre abonnement Azure si vous quittez votre organisation ou si voulez que votre abonnement soit facturé sur un autre compte. Le transfert de la propriété de la facturation à un autre compte fournit aux administrateurs du nouveau compte l’autorisation sur les tâches de facturation. Ils peuvent changer le mode de paiement, visualiser les coûts facturés et annuler l’abonnement.

Si vous souhaitez conserver la propriété de facturation tout en changeant le type de votre abonnement, consultez [Changer d’offre pour votre abonnement Azure](switch-azure-offer.md). Pour contrôler qui peut gérer les ressources de l’abonnement, consultez [Rôles intégrés pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Si vous êtes client Contrat Entreprise (EA), les administrateurs de votre entreprise peuvent transférer la propriété de facturation de vos abonnements entre les comptes. Pour plus d’informations, consultez [Transférer la propriété de facturation des abonnements Contrat Entreprise (EA)](#EA).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Transférer la propriété de facturation d’un abonnement Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur du compte de facturation auquel est rattaché l’abonnement que vous souhaitez transférer. Pour savoir si vous êtes administrateur, consultez [Questions fréquentes (FAQ)](#faq).

1. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Sélectionnez **Abonnements** dans le volet de gauche. Selon votre accès, vous devrez peut-être sélectionner une étendue de facturation, puis sélectionner **Abonnements** ou **Abonnements Azure**.

1. Sélectionnez **Transférer la propriété de facturation** pour l’abonnement que vous voulez transférer.

   ![Sélectionner l’abonnement à transférer](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Entrez l’adresse e-mail d’un utilisateur qui est administrateur de facturation du compte qui sera le nouveau propriétaire de l’abonnement.

1. Si vous transférez votre abonnement au compte d’un autre locataire Azure AD, choisissez si vous souhaitez déplacer l’abonnement vers le locataire du nouveau compte. Pour plus d’informations, consultez [Transfert de l’abonnement à un compte dans un autre locataire Azure AD](#transfer-a-subscription-to-another-azure-ad-tenant-account).

    > [!IMPORTANT]
    >
    > Si vous choisissez de déplacer l’abonnement vers le locataire Azure AD du nouveau compte, toutes les affectations de [contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/overview.md) permettant de gérer les ressources de l’abonnement sont définitivement supprimées. Seul l’utilisateur dans le nouveau compte qui accepte votre demande de transfert a accès à la gestion des ressources de l’abonnement. Pour plus d’informations, consultez [Transfert de l’abonnement à un utilisateur dans un autre locataire Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories). Vous pouvez également désactiver la case à cocher Locataire Azure AD de l’abonnement pour transférer la propriété de facturation sans déplacer l’abonnement vers le locataire du nouveau compte. Dans ce cas, les autorisations RBAC existantes permettant de gérer les ressources Azure sont conservées.

    ![Page d’envoi du transfert](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Sélectionnez **Envoyer la demande de transfert**.

1. L’utilisateur reçoit un e-mail contenant des instructions pour l’examen de votre demande de transfert.

   ![E-mail de transfert d’abonnement envoyé au destinataire](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Pour approuver la demande de transfert, l’utilisateur sélectionne le lien figurant dans l’e-mail, puis suit les instructions. L’utilisateur sélectionne ensuite un mode de paiement à utiliser pour payer l’abonnement. Si l’utilisateur n’a pas de compte Azure, il doit s’inscrire pour un nouveau compte.

   ![Page web de transfert du premier abonnement](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Page web de transfert du deuxième abonnement](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Page web de transfert du deuxième abonnement](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Opération réussie. L’abonnement est à présent transféré.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Transférer un abonnement vers un autre compte de locataire Azure AD

Un locataire Azure Active Directory (AD) est créé pour vous quand vous vous inscrivez à Azure. Le locataire représente votre compte. Le locataire vous permet de gérer l’accès à vos abonnements et ressources.

Quand vous créez un abonnement, il est hébergé dans le locataire Azure AD de votre compte. Si vous voulez donner à d’autres utilisateurs l’accès à votre abonnement ou à ses ressources, vous devez les inviter à rejoindre votre locataire. Ceci vous aide à contrôler l’accès à vos abonnements et à vos ressources.

Quand vous transférez la propriété de facturation de votre abonnement à un compte dans un autre locataire Azure AD, vous pouvez déplacer l’abonnement vers le locataire du nouveau compte. Dans ce cas, tous les utilisateurs, groupes ou principaux de service qui disposaient d’un [accès RBAC](../../role-based-access-control/role-assignments-portal.md) pour gérer les abonnements et leurs ressources perdent cet accès. Seul l’utilisateur dans le nouveau compte qui accepte votre demande de transfert a accès à la gestion des ressources. Le nouveau propriétaire doit [ajouter manuellement ces utilisateurs à l’abonnement](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) pour fournir l’accès à l’utilisateur l’a perdu.


## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Transférer des abonnements Visual Studio et Partner Network

Les abonnements Visual Studio et Microsoft Partner Network bénéficient chaque mois de crédits Azure. Quand vous transférez ces abonnements, votre crédit n’est pas disponible dans le compte de facturation de destination. L’abonnement utilise le crédit disponible dans le compte de facturation de destination. Par exemple, supposons que Pierre transfère un abonnement Visual Studio Enterprise au compte de Marie le 9 septembre et que Marie accepte le transfert. Une fois le transfert terminé, l’abonnement commence à utiliser le crédit disponible dans le compte de Marie. Le crédit est réinitialisé tous les neuvièmes jours du mois.


<a id="EA"></a>

## <a name="transfer-ea-subscription-billing-ownership"></a>Transférer la propriété de la facturation d’un abonnement Contrat Entreprise

L’administrateur d’entreprise peut transférer la propriété des abonnements entre des comptes au sein d’une inscription. Pour plus d’informations, consultez [Changer le propriétaire du compte](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#change-account-owner) dans le portail EA.

## <a name="next-steps-after-accepting-billing-ownership"></a>Étapes suivantes après avoir accepté la propriété de facturation

Si vous avez accepté la propriété de la facturation d’un abonnement Azure, nous vous recommandons de passer en revue ces étapes suivantes :

1. Passez en revue et mettez à jour l’administrateur de service, les coadministrateurs et les autres rôles RBAC. Pour plus d’informations, consultez [Ajout ou modification des administrateurs d’abonnements Azure](add-change-subscription-administrator.md) et [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../../role-based-access-control/role-assignments-portal.md).
1. Mettez à jour les informations d’identification associées aux services de cet abonnement, notamment :
   1. Certificats de gestion accordant à l’utilisateur des droits d’administrateur sur les ressources d’abonnement. Pour plus d'informations, consultez [Créer et télécharger un certificat de gestion pour Microsoft Azure](../../cloud-services/cloud-services-certs-create.md)
   1. Touches d’accès rapide pour les services tels que Storage. Pour plus d’informations, consultez [À propos des comptes de stockage Azure](../../storage/common/storage-create-storage-account.md).
   1. Informations d’identification d’accès à distance pour les services tels que les machines virtuelles Azure.
1. Si vous collaborez avec un partenaire, envisagez de mettre à jour son ID de partenaire pour cet abonnement. Vous pouvez mettre à jour l’ID partenaire dans le [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez [Lier un ID partenaire à vos comptes Azure](link-partner-id.md).

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Types d’abonnements pris en charge

Le transfert d’abonnement dans le portail Azure est disponible pour les types d’abonnements listés ci-dessous. Actuellement, le transfert n’est pas pris en charge pour les abonnements [Essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p/) ou [Azure dans Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). Pour obtenir une solution de contournement, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Pour transférer d’autres abonnements, comme [Sponsoring](https://azure.microsoft.com/offers/ms-azr-0036p/) ou des plans de support, [contactez le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Abonnés Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [Plateformes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Dev/Test – Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise : BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Offre Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [Via le portail EA](#EA).

\*\* Uniquement pris en charge pour les comptes qui sont créés lors de l’inscription sur le site web Azure.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Questions fréquentes (FAQ) pour les expéditeurs

Ces Questions fréquentes (FAQ) concernent les utilisateurs qui transfèrent la propriété de facturation d’un abonnement Azure à un autre compte.

### <a name="whoisaa"></a> Qui est administrateur de facturation d’un compte ?

Un administrateur de facturation est une personne qui a l’autorisation de gérer la facturation d’un compte. Il est autorisé à accéder à la facturation sur le [portail Azure](https://portal.azure.com) et à effectuer différentes tâches liées à la facturation, comme la création d’abonnements, la consultation et le paiement des factures, ou la mise à jour des modes de paiement.

Pour identifier les comptes pour lesquels vous êtes administrateur de facturation, suivez les étapes ci-après :

1. Accédez à la [page Gestion des coûts + facturation dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Sélectionnez **Toutes les étendues de facturation** dans le volet de gauche.
1. La page Abonnements liste tous les abonnements pour lesquels vous êtes administrateur de la facturation.

Si vous ne savez pas qui est l’administrateur de compte d’un abonnement, procédez comme suit pour le découvrir.

1. Accédez à la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement que vous souhaitez vérifier, puis regardez sous **Paramètres**.
1. Sélectionner **Propriétés**. L’administrateur de compte de l’abonnement s’affiche dans la zone **Administrateur de compte** .

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Tous les éléments sont-ils transférés ? Notamment les groupes de ressources, les machines virtuelles, les disques et les autres services en cours d’exécution ?

Toutes les ressources telles que les machines virtuelles, les disques et les sites web sont transférées au nouveau compte. Cependant, si vous transférez un abonnement à un compte dans un autre locataire Azure AD, les affectations des [rôles Administrateur](add-change-subscription-administrator.md) et de [RBAC (contrôle d’accès en fonction du rôle)](../../role-based-access-control/role-assignments-portal.md) sur l’abonnement [ne sont pas transférées](#transfer-a-subscription-to-another-azure-ad-tenant-account). Les [inscriptions des applications](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) et autres services spécifiques du locataire ne sont pas non plus transférés avec l’abonnement.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Puis-je transférer la propriété à un compte dans un autre pays ?
Malheureusement, les transferts entre pays ne peuvent pas être effectués dans le portail Azure. Pour transférer votre abonnement d’un pays à un autre, [contactez le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Je suis administrateur de deux comptes. Puis-je transférer un abonnement depuis un de mes comptes à l’autre ?
Oui, vous pouvez transférer l’abonnement entre vos comptes. Vos comptes étant conceptuellement considérés comme les comptes de deux utilisateurs différents, vous pouvez effectuer les étapes ci-dessus pour transférer des abonnements entre ces comptes.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Un transfert d’abonnement entraîne-t-il une interruption de service ?

Si vous transférez un abonnement à un compte dans le même locataire Azure AD, il n’y a pas d’impact sur les ressources en cours d’exécution dans l’abonnement. Cependant, les informations de contexte enregistrées dans PowerShell ne sont pas mises à jour : il peut donc être nécessaire de les effacer ou de changer des paramètres. Si vous transférez l’abonnement à un compte dans un autre locataire et que vous décidez de déplacer l’abonnement vers ce locataire, tous les utilisateurs, groupes et principaux de service qui disposaient d’un [accès RBAC](../../role-based-access-control/overview.md) pour gérer les ressources de l’abonnement perdent cet accès. Il peut en résulter un temps d’arrêt du service.

### <a name="can-users-in-new-account-access-usage-and-billing-history"></a>Les utilisateurs du nouveau compte ont-ils accès à l’historique d’utilisation et de facturation ?

Le coût de votre abonnement sur le dernier mois représente la seule information accessible aux utilisateurs du nouveau compte. Le reste de l’historique d’utilisation et de facturation n’est pas transféré avec l’abonnement.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Comment migrer des données et des services de mon abonnement Azure vers un nouvel abonnement ?

Si vous ne pouvez pas transférer la propriété de l’abonnement, vous pouvez migrer vos ressources manuellement. Consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Si je transfère un abonnement Visual Studio ou Microsoft Partner Network, mon crédit est-il reporté avec l’abonnement dans le nouveau compte ?

Non, votre crédit n’est pas disponible dans le nouveau compte. L’utilisateur qui a accepté la demande de transfert doit avoir une licence Visual Studio pour procéder à l’acceptation. L’abonnement utilise le crédit Visual Studio qui est disponible dans le compte de l’utilisateur. Pour plus d’informations, consultez [Transfert des abonnements Visual Studio et Partner Network](#transfer-visual-studio-and-partner-network-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Questions fréquentes (FAQ) pour les destinataires

Ces FAQ concernent les utilisateurs qui acceptent la propriété de facturation d’un abonnement Azure d’un autre compte.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Si je deviens titulaire de la facturation d’un abonnement d’un autre compte, les utilisateurs dans ce compte ont-ils toujours accès à mes ressources ?

Oui. Cependant, les [rôles d’administrateur](add-change-subscription-administrator.md) et les attributions de [Contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/role-assignments-portal.md) peuvent être supprimées. La perte de l’accès se produit quand votre compte se trouve dans un locataire Azure AD autre que le locataire de l’abonnement et que l’utilisateur qui a envoyé la demande de transfert déplace l’abonnement vers le locataire de votre compte. Pour visualiser les utilisateurs disposant d’un [accès RBAC](../../role-based-access-control/overview.md) permettant de gérer les ressources de l’abonnement, suivez les étapes ci-après :

1. Accédez à la [page Abonnement du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez l’abonnement que vous souhaitez vérifier, puis sélectionnez **Contrôle d’accès (IAM)** dans le volet gauche.
1. Sélectionnez **Attributions de rôles** en haut de la page. La page des attributions de rôles liste tous les utilisateurs qui disposent d’un accès RBAC à l’abonnement.

Même si les attributions de [Contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/role-assignments-portal.md) sont supprimées lors du transfert, les utilisateurs du compte du propriétaire d’origine peuvent toujours avoir accès à l’abonnement via certains mécanismes de sécurité, notamment :

* Certificats de gestion accordant à l’utilisateur des droits d’administrateur sur les ressources d’abonnement. Pour plus d'informations, consultez la rubrique [Créer et télécharger un certificat de gestion pour Microsoft Azure](../../cloud-services/cloud-services-certs-create.md).
* Touches d’accès rapide pour les services tels que Storage. Pour plus d’informations, consultez la rubrique [À propos des comptes de stockage Azure](../../storage/common/storage-create-storage-account.md).
* Informations d’identification d’accès à distance pour les services tels que les machines virtuelles Azure.

Le destinataire doit envisager la mise à jour des secrets associés au service s’il doit restreindre l’accès aux ressources. La plupart des ressources peuvent être mises à jour en procédant comme suit :

  1. Connectez-vous au [portail Azure](https://portal.azure.com).
  2. Dans le menu Hub, sélectionnez **Toutes les ressources**.
  3. Sélectionnez la ressource.
  4. Dans la page des ressources, cliquez sur **Paramètres**. Ici, vous pouvez afficher et mettre à jour les clés secrètes existantes.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Si je deviens titulaire de la facturation d’un abonnement au milieu d’un cycle de facturation, dois-je payer pour l’intégralité du cycle ?

Votre compte est responsable du paiement de toute utilisation qui est signalée à compter du moment du transfert. Certaines opérations peuvent avoir été effectuées avant le transfert mais déclarées ultérieurement. L’utilisation figure sur la facture de votre compte.

### <a name="can-i-use-a-different-payment-method"></a>Puis-je utiliser un autre mode de paiement ?

Oui. Lors de l’acceptation de la demande de transfert, vous pouvez sélectionner un mode de paiement existant qui est lié à votre compte ou ajouter un nouveau mode de paiement.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>Comment transférer la propriété de mon compte d’abonnement Contrat Entreprise (EA) si le propriétaire du compte d’origine n’est plus associé à l’organisation ?

L’administrateur d’entreprise peut mettre à jour la propriété d’un compte même si le propriétaire du compte d’origine ne fait plus partie de l’organisation. Pour cela, il peut suivre les instructions relatives au [transfert de la propriété du compte de tous les abonnements](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) dans le portail EA.

## <a name="troubleshooting"></a>Dépannage

### <a id="no-button"></a> Pourquoi le bouton Transférer un abonnement ne s’affiche-t-il pas ?

Le transfert d’abonnement en libre-service n’est pas disponible pour votre compte de facturation. Nous ne prenons pas en charge le transfert de la propriété de facturation pour les abonnements des comptes Contrat Entreprise (EA) dans le portail Azure. En outre, les comptes Contrat client Microsoft qui sont créés dans le cadre d’une collaboration avec un représentant Microsoft ne prennent pas en charge le transfert de la propriété de la facturation.

### <a id="no-button"></a> Pourquoi mon type d’abonnement ne prend-il pas en charge le transfert ?

Tous les types d’abonnements ne prennent pas en charge le transfert de la propriété de facturation. Pour voir la liste des types d’abonnements qui prennent en charge les transferts, consultez [Types d’abonnements pris en charge](#supported-subscription-types)

### <a id="no-button"></a> Quand j’essaie de transférer la propriété de facturation d’un abonnement, je reçois une erreur de refus d’accès. Pourquoi ?

Vous voyez cette erreur si vous essayez de transférer un abonnement Plan Microsoft Azure et que vous n’avez pas l’autorisation nécessaire. Pour transférer un abonnement Plan Microsoft Azure, vous devez être propriétaire ou contributeur de la section de facture à laquelle l’abonnement est facturé. Pour plus d’informations, consultez [Gérer les abonnements associés à la section de facture](understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue et mettez à jour l’administrateur de service, les coadministrateurs et les autres rôles RBAC. Pour plus d’informations, consultez [Ajout ou modification des administrateurs d’abonnements Azure](add-change-subscription-administrator.md) et [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../../role-based-access-control/role-assignments-portal.md).
