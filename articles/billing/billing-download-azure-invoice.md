---
title: Afficher et télécharger votre facture Microsoft Azure
description: Explique comment afficher et télécharger votre facture Microsoft Azure.
keywords: facturation, facture, téléchargement de facture, facture Azure, utilisation d’Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 80ec40a7411a370460d663084f9f7034b28e1a2e
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375757"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Afficher et télécharger votre facture Microsoft Azure

Pour la plupart des abonnements, vous pouvez télécharger votre facture à partir du [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou la faire envoyer par courrier électronique. Si vous êtes un client Azure avec un contrat Entreprise (client EA), vous ne pouvez pas télécharger les factures de votre organisation. Les factures sont envoyées à toute personne autorisée à recevoir des factures pour l’inscription.

Seuls certains rôles sont autorisés à afficher des factures. Par exemple, l’administrateur de compte ou l’administrateur d’entreprise. Pour en savoir plus sur l’accès aux informations de facturation, consultez [Gérer l’accès à la facturation Azure à l’aide de rôles](billing-manage-access.md).

Si vous avez un Contrat Client Microsoft (MCA), vous devez avoir l’un des rôles suivants pour obtenir vos factures :

- Propriétaire du profil de facturation
- Contributeur
- Lecteur
- Gestionnaire de factures

Si vous avez un Contrat Partenaire Microsoft (MPA), vous devez avoir le rôle Administrateur général ou Agent d’administration dans l’organisation partenaire pour voir et télécharger les factures Azure. [Vérifiez votre type de compte de facturation](#check-your-billing-account-type) pour déterminer les autorisations dont vous avez besoin. 

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](billing-understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Pourquoi vous pouvez ne pas recevoir de facture

Si vous ne voyez pas de facture, cela peut être pour plusieurs raisons :

- Cela fait moins de 30 jours que vous êtes inscrit à Azure.

- Azure vous facture à la fin de votre période de facturation. Par conséquent, une facture n’a peut-être pas encore été générée. Patientez jusqu’à la fin de la période de facturation.

- Vous n’êtes pas autorisé à afficher les factures. Si vous avez un compte MCA ou MPA, vous devez être gestionnaire de factures, ou propriétaire, contributeur ou lecteur de profil de facturation. Pour les autres abonnements, il est possible que vous ne voyiez pas les anciennes factures si vous n’êtes pas l’administrateur de compte. Pour en savoir plus sur l’accès aux informations de facturation, consultez [Manage access to Azure billing using roles](billing-manage-access.md) (Utiliser des rôles pour gérer l’accès à la facturation Azure).

- Si vous disposez d’une version d’évaluation gratuite ou d’un crédit mensuel avec votre abonnement, vous recevez une facture uniquement lorsque vous dépassez le montant du crédit mensuel. Si vous avez un Contrat Client Microsoft ou un Contrat Partenaire Microsoft, vous recevez toujours une facture. 

## <a name="download-invoices-in-the-azure-portal"></a>Télécharger les factures dans le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Recherchez *Gestion des coûts + facturation*.
1. Selon votre type d’accès, vous devrez peut-être sélectionner un compte de facturation ou un profil de facturation.
1. Dans le menu de gauche, sélectionnez **Factures** sous **Facturation**.
1. Dans la grille des factures, recherchez la ligne de la facture que vous souhaitez télécharger.
1. Cliquez sur l’icône de téléchargement ou sur les points de suspension (`...`) à la fin de la ligne.
1. Dans le menu de téléchargement, sélectionnez **Facture**.

Pour plus d’informations sur votre facture, consultez la page [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md). Pour vous aider à gérer vos coûts, consultez [Éviter les coûts inattendus avec la gestion de la facturation et des coûts Azure](billing-getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Obtenir les factures de votre abonnement par e-mail

Vous pouvez choisir de configurer des destinataires supplémentaires qui recevront votre facture Azure par e-mail. Cette fonctionnalité n’est peut-être pas disponible pour certains abonnements tels que les offres de support, les contrats Entreprise ou Azure dans Open.

1. Sélectionnez votre abonnement dans la [page Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Choisissez cette option pour chacun de vos abonnements. Cliquez sur **Factures** pour sélectionner **l’envoi de votre facture par E-mail**.

    ![Capture d’écran montrant le processus d’abonnement](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Cliquez sur **Activation** et acceptez les termes du contrat.

    ![Capture d’écran montrant l’étape 2 du flux de l’adhésion](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Une fois que vous avez accepté le contrat, vous pouvez configurer des destinataires supplémentaires. Quand un destinataire est supprimé, l’adresse e-mail n’est plus stockée. Si vous changez d’avis, vous devez les ajouter à nouveau.

    ![Capture d’écran montrant l’étape 3 du flux de l’adhésion](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Si vous n’obtenez pas de courrier électronique une fois ces étapes terminées, assurez-vous que votre adresse de messagerie est correcte dans les [préférences de communication sur votre profil](https://account.windowsazure.com/profile).

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Ne pas adhérer à l’obtention des factures de votre abonnement par e-mail

Pour annuler votre adhésion à l’obtention de votre facture par e-mail, suivez les étapes précédentes et cliquez sur **Désactiver l’envoi de facture par e-mail**. Cette option supprime toutes les adresses e-mail définies pour recevoir les factures par e-mail. Vous pouvez reconfigurer les destinataires si vous réadhérez.

 ![Capture d’écran montrant le flux de l’adhésion](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Obtenir les factures de votre Contrat client Microsoft par e-mail

Si vous avez un Contrat client Microsoft, vous pouvez adhérer à l’obtention de votre facture par e-mail. Tous les Gestionnaires de factures, Propriétaires, Contributeurs et Lecteurs de profil de facturation obtiennent la facture par e-mail. Les lecteurs ne peuvent pas mettre à jour la préférence d’envoi par e-mail de la facture.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Recherchez **Gestion des coûts + facturation**.
1. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner au préalable un compte de facturation.
1. Sous **Paramètres**, sélectionnez **Propriétés**.
1. Sous **Facture par e-mail**, sélectionnez **Mettre à jour la préférence d’envoi par e-mail de la facture**.

    ![Capture d’écran qui montre les propriétés de l’envoi par e-mail de la facture](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Sélectionnez **Activer**.
1. Cliquez sur **Update**.

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Ne pas adhérer à l’obtention des factures de votre Contrat client Microsoft par e-mail

Pour annuler votre adhésion à l’obtention de votre facture par e-mail, suivez les étapes précédentes et cliquez sur **Désactiver**. Tous les Gestionnaires de factures, Propriétaires, Contributeurs et Lecteurs sont également exclus de l’adhésion à l’obtention de la facture par e-mail. Si vous êtes lecteur, vous ne pouvez pas changer la préférence d’envoi par e-mail de la facture.

## <a name="check-your-billing-account-type"></a>Vérifier votre type de compte de facturation
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur vos facture et frais, consultez :

- [Afficher et télécharger votre utilisation et vos frais Microsoft Azure](billing-download-azure-daily-usage.md)
- [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md)
- [Comprendre les modalités sur votre facture Azure](billing-understand-your-invoice.md)
- [Comprendre les termes figurant dans le détail de votre utilisation de Microsoft Azure](billing-understand-your-usage.md)
- [Afficher les tarifs Azure de votre organisation](billing-ea-pricing.md)

Si vous avez un Contrat client Microsoft, consultez :

- [Comprendre les frais indiqués sur la facture de votre profil de facturation](billing-mca-understand-your-bill.md)
- [Comprendre les termes indiqués sur la facture de votre profil de facturation](billing-mca-understand-your-invoice.md)
- [Comprendre le fichier d’utilisation et de frais Azure pour votre profil de facturation](billing-mca-understand-your-usage.md)
- [Afficher et télécharger les documents fiscaux pour votre profil de facturation](billing-mca-download-tax-document.md)
- [Afficher les tarifs Azure de votre organisation](billing-ea-pricing.md)
