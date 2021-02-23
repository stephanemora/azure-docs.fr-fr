---
title: Transférer la propriété de facturation d’un abonnement Azure
description: Décrit comment transférer la propriété de facturation d’un abonnement Azure à un autre compte.
keywords: transférer abonnement azure, azure transfert abonnement, déplacer un abonnement azure vers un autre compte, changer abonnement azure, transférer abonnement azure à un autre compte, transférer facturation azure
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/05/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 2fb1a8c3e583a4bdc88f2b61844e9bcb16dc7cdf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367195"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Transfert de la propriété de facturation d’un abonnement Azure à un autre compte

Cet article décrit les étapes nécessaires pour transférer la propriété de facturation d’un abonnement Azure à un autre compte. Avant de transférer la propriété de facturation d’un abonnement, lisez [À propos du transfert de propriété de facturation pour un abonnement Azure](../understand/subscription-transfer.md).

Si vous souhaitez conserver votre propriété de facturation mais changer de type d’abonnement, consultez [Changer d’offre pour votre abonnement Azure](switch-azure-offer.md). Pour contrôler qui peut accéder aux ressources de l’abonnement, consultez [Rôles intégrés Azure](../../role-based-access-control/built-in-roles.md).

Si vous êtes client Accord Entreprise (EA), l’administrateur de votre entreprise peut transférer la propriété de facturation de vos abonnements entre les comptes. Pour plus d’informations, [modifiez l’abonnement Azure ou la propriété du compte](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

Seul l’administrateur de facturation d’un compte peut transférer la propriété d’un abonnement.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Transférer la propriété de facturation d’un abonnement Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur du compte de facturation auquel est rattaché l’abonnement que vous souhaitez transférer. Si vous ignorez si vous êtes administrateur ou si vous devez déterminer qui l’est, consultez [Déterminer l’administrateur de facturation d’un compte](../understand/subscription-transfer.md#whoisaa).
1. Recherchez **Gestion des coûts + facturation**.  
   ![Capture d’écran montrant une recherche dans le portail Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. Sélectionnez **Abonnements** dans le volet de gauche. Selon votre accès, vous devrez peut-être sélectionner une étendue de facturation, puis sélectionner **Abonnements** ou **Abonnements Azure**.
1. Sélectionnez **Transférer la propriété de facturation** pour l’abonnement que vous voulez transférer.  
   ![Sélectionner l’abonnement à transférer](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. Entrez l’adresse e-mail d’un utilisateur qui est administrateur de facturation du compte qui sera le nouveau propriétaire de l’abonnement.
1. Si vous transférez votre abonnement au compte d’un autre locataire Azure AD, choisissez si vous souhaitez déplacer l’abonnement vers le locataire du nouveau compte. Pour plus d’informations, consultez [Transfert de l’abonnement à un compte dans un autre locataire Azure AD](#transfer-a-subscription-to-another-azure-ad-tenant-account).
    > [!IMPORTANT]
    > Si vous choisissez de déplacer l’abonnement vers le locataire Azure AD du nouveau compte, toutes les [attributions de rôles Azure](../../role-based-access-control/role-assignments-portal.md) permettant d’accéder aux ressources de l’abonnement sont définitivement supprimées. Seul l’utilisateur dans le nouveau compte qui accepte votre demande de transfert a accès à la gestion des ressources de l’abonnement. Vous pouvez également désactiver l’option **Locataire Azure AD de l’abonnement** pour transférer la propriété de facturation sans déplacer l’abonnement vers le locataire du nouveau compte. Dans ce cas, les attributions de rôles Azure existantes permettant d’accéder aux ressources Azure sont conservées.  
    ![Page d’envoi du transfert](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. Sélectionnez **Envoyer la demande de transfert**.
1. L’utilisateur reçoit un e-mail contenant des instructions pour l’examen de votre demande de transfert.  
   ![E-mail de transfert d’abonnement envoyé au destinataire](./media/billing-subscription-transfer/billing-receiver-email.png)
1. Pour approuver la demande de transfert, l’utilisateur sélectionne le lien figurant dans l’e-mail, puis suit les instructions. L’utilisateur sélectionne ensuite un mode de paiement à utiliser pour payer l’abonnement. Si l’utilisateur n’a pas de compte Azure, il doit s’inscrire pour un nouveau compte.  
   ![Première page web de transfert d’abonnement](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![Deuxième page web de transfert d’abonnement](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![Troisième page web de transfert d’abonnement](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. Opération réussie. L’abonnement est à présent transféré.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Transférer un abonnement vers un autre compte de locataire Azure AD

Un locataire Azure Active Directory (AD) est créé pour vous quand vous vous inscrivez à Azure. Le locataire représente votre compte. Le locataire vous permet de gérer l’accès à vos abonnements et ressources.

Quand vous créez un abonnement, il est hébergé dans le locataire Azure AD de votre compte. Si vous voulez donner à d’autres utilisateurs l’accès à votre abonnement ou à ses ressources, vous devez les inviter à rejoindre votre locataire. Ceci vous aide à contrôler l’accès à vos abonnements et à vos ressources.

Quand vous transférez la propriété de facturation de votre abonnement à un compte dans un autre locataire Azure AD, vous pouvez déplacer l’abonnement vers le locataire du nouveau compte. Dans ce cas, l’ensemble des utilisateurs, groupes ou principaux de service qui disposaient d’[attributions de rôles Azure](../../role-based-access-control/role-assignments-portal.md) pour gérer des abonnements et leurs ressources perdent leur accès. Seul l’utilisateur dans le nouveau compte qui accepte votre demande de transfert a accès à la gestion des ressources. Le nouveau propriétaire doit ajouter manuellement ces utilisateurs à l’abonnement pour fournir l’accès à l’utilisateur l’a perdu. Pour plus d’informations, consultez [Transférer un abonnement Azure vers une autre instance Azure AD Directory](../../role-based-access-control/transfer-subscription.md).

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Transférer des abonnements Visual Studio et Partner Network

Les abonnements Visual Studio et Microsoft Partner Network bénéficient chaque mois de crédits Azure. Quand vous transférez ces abonnements, votre crédit n’est pas disponible dans le compte de facturation de destination. L’abonnement utilise le crédit disponible dans le compte de facturation de destination. Par exemple, supposons que Pierre transfère un abonnement Visual Studio Enterprise au compte de Marie le 9 septembre et que Marie accepte le transfert. Une fois le transfert terminé, l’abonnement commence à utiliser le crédit disponible dans le compte de Marie. Le crédit est réinitialisé tous les neuvièmes jours du mois.

## <a name="next-steps-after-accepting-billing-ownership"></a>Étapes suivantes après avoir accepté la propriété de facturation

Si vous avez accepté la propriété de la facturation d’un abonnement Azure, nous vous recommandons de passer en revue ces étapes suivantes :

1. Passez en revue et mettez à jour l’administrateur de service, les coadministrateurs et les attributions de rôles Azure. Pour plus d’informations, consultez [Ajout ou modification des administrateurs d’abonnements Azure](add-change-subscription-administrator.md) et [Ajouter ou supprimer des attributions de rôle Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).
1. Mettez à jour les informations d’identification associées aux services de cet abonnement, notamment :
   1. Certificats de gestion accordant à l’utilisateur des droits d’administrateur sur les ressources d’abonnement. Pour plus d'informations, consultez [Créer et télécharger un certificat de gestion pour Microsoft Azure](../../cloud-services/cloud-services-certs-create.md)
   1. Touches d’accès rapide pour les services tels que Storage. Pour plus d’informations, consultez [À propos des comptes de stockage Azure](../../storage/common/storage-account-create.md).
   1. Informations d’identification d’accès à distance pour les services tels que les machines virtuelles Azure.
1. Si vous collaborez avec un partenaire, envisagez de mettre à jour son ID de partenaire pour l’abonnement. Vous pouvez mettre à jour l’ID partenaire dans le [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez [Lier un ID partenaire à vos comptes Azure](link-partner-id.md).

## <a name="cancel-a-transfer-request"></a>Annuler une demande de transfert

Vous ne pouvez avoir qu’une seule demande de transfert active à la fois. Une demande de transfert est valide pendant 15 jours. Au bout de 15 jours, la demande de transfert expire.

Pour annuler une demande de transfert :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à **Abonnements**, sélectionnez l’abonnement pour lequel vous avez envoyé une demande de transfert, puis sélectionnez **Transférer la propriété de facturation**.
1. Au bas de la page, sélectionnez **Annuler la demande de transfert**.

:::image type="content" source="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" alt-text="Exemple montrant la fenêtre Transférer la propriété de facturation avec l’option Annuler la demande de transfert" lightbox="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" :::

## <a name="troubleshooting"></a>Dépannage

Si vous éprouvez des difficultés à transférer des abonnements, utilisez les informations de résolution de problèmes suivantes.

### <a name="original-azure-subscription-billing-owner-leaves-your-organization"></a>Le propriétaire d’origine de la facturation de l’abonnement Azure quitte votre organisation

> [!Note]
> Cette section s’applique spécifiquement à un compte de facturation pour un contrat client Microsoft. Vérifiez que vous avez accès à un [contrat client Microsoft](mca-request-billing-ownership.md#check-for-access).

Il est possible que le propriétaire du compte de facturation d’origine qui a créé un compte Azure et un abonnement Azure quitte votre organisation. Si cette situation se produit, son identité d’utilisateur ne figure plus dans l’annuaire Azure Active Directory de l’organisation. L’abonnement Azure n’a donc plus de propriétaire de facturation. Cette situation empêche quiconque d’effectuer des opérations de facturation sur le compte, notamment de voir et de payer les factures. L’abonnement peut passer à l’état impayé et, par la suite, être désactivé en raison du non-paiement. L’abonnement peut finalement être supprimé, ce qui affectera tous les services qui s’y exécutent.

Quand un abonnement n’a plus de propriétaire de compte de facturation valide, Azure envoie un e-mail à d’autres propriétaires de compte de facturation, aux administrateurs de service (s’il y en a), aux coadministrateurs (s’il y en a) et aux propriétaires d’abonnement les informant de la situation, et leur fournit un lien pour accepter la propriété de la facturation de l’abonnement. Tout utilisateur peut sélectionner le lien pour accepter la propriété de facturation. Pour plus d’informations sur les rôles de facturation, consultez les pages sur les [rôles de facturation](understand-mca-roles.md) et les [rôles d’abonnement classique et RBAC Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).

L’exemple suivant montre à quoi ressemble l’e-mail.

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-email.png" alt-text="Capture d’écran montrant un exemple de l’e-mail permettant d’accepter la propriété de facturation." lightbox="./media/billing-subscription-transfer/orphaned-subscription-email.png" :::

Azure affiche également une bannière dans la fenêtre des détails de l’abonnement du portail Azure pour les propriétaires de facturation, administrateurs de services, coadministrateurs et propriétaires d’abonnement. Sélectionnez le lien dans la bannière pour accepter la propriété de facturation.

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-example.png" alt-text="Capture d’écran montrant un exemple d’abonnement sans propriétaire de facturation valide." lightbox="./media/billing-subscription-transfer/orphaned-subscription-example.png" :::

### <a name="the-transfer-subscription-option-is-unavailable"></a>L’option « Transférer l’abonnement » n’est pas disponible

<a name="no-button"></a> 

Le transfert d’abonnement en libre-service n’est pas disponible pour votre compte de facturation. Nous ne prenons pas en charge le transfert de la propriété de facturation pour les abonnements des comptes Contrat Entreprise (EA) dans le portail Azure. En outre, les comptes Contrat client Microsoft qui sont créés dans le cadre d’une collaboration avec un représentant Microsoft ne prennent pas en charge le transfert de la propriété de la facturation.

###  <a name="not-all-subscription-types-can-transfer"></a>Certains types d’abonnements ne peuvent pas opérer de transfert

Tous les types d’abonnements ne prennent pas en charge le transfert de la propriété de facturation. Pour voir la liste des types d’abonnements qui prennent en charge les transferts, consultez [Types d’abonnements pris en charge](../understand/subscription-transfer.md#supported-subscription-types)

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>Erreur d’accès refusé affichée lors de la tentative de transfert de propriété de facturation d’abonnement

Vous voyez cette erreur si vous essayez de transférer un abonnement Plan Microsoft Azure et que vous n’avez pas l’autorisation nécessaire. Pour transférer un abonnement Plan Microsoft Azure, vous devez être propriétaire ou contributeur de la section de facture à laquelle l’abonnement est facturé. Pour plus d’informations, consultez [Gérer les abonnements associés à la section de facture](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue et mettez à jour l’administrateur de service, les coadministrateurs et les attributions de rôles Azure. Pour plus d’informations, consultez [Ajout ou modification des administrateurs d’abonnements Azure](add-change-subscription-administrator.md) et [Ajouter ou supprimer des attributions de rôle Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).