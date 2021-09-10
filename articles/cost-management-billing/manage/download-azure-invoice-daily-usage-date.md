---
title: Télécharger la facture Azure
description: Décrit comment télécharger ou afficher votre facture Azure.
keywords: billing invoice,invoice download,azure invoice
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/28/2021
ms.author: banders
ms.openlocfilehash: 14183a46085ca0da60f1abb5f6ba390b9d4c334a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563026"
---
# <a name="download-or-view-your-azure-billing-invoice"></a>Télécharger ou afficher votre facture Azure

Pour la plupart des abonnements, vous pouvez télécharger votre facture à partir du [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou la faire envoyer par courrier électronique. Si vous êtes un client Azure avec un contrat Entreprise (client EA), vous ne pouvez pas télécharger les factures de votre organisation. Les factures sont envoyées à toute personne autorisée à recevoir des factures pour l’inscription.

Seuls certains rôles, par exemple l’administrateur de compte ou l’administrateur d’entreprise, sont autorisés à accéder à la facture. Pour en savoir plus sur l’accès aux informations de facturation, consultez [Manage access to Azure billing using roles](manage-billing-access.md) (Utiliser des rôles pour gérer l’accès à la facturation Azure).

Si vous avez un Contrat client Microsoft, vous devez être Gestionnaire de factures, Propriétaire, Contributeur ou Lecteur du profil de facturation pour afficher les informations de facturation. Pour en savoir plus sur les rôles de facturation pour les contrats client Microsoft, consultez [Tâches et rôles du profil de facturation](understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Télécharger vos factures Azure (.pdf)

Pour la plupart des abonnements, vous pouvez télécharger votre facture à partir du portail Azure. Si vous avez un Contrat client Microsoft, consultez Télécharger les factures pour un profil de facturation.

### <a name="download-invoices-for-an-individual-subscription"></a>Télécharger les factures pour un abonnement individuel

1. Sélectionnez votre abonnement dans la [page Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure en tant qu’[utilisateur ayant accès aux factures](manage-billing-access.md).

2. Sélectionnez **Factures**.

    ![Capture d’écran qui montre l’option Facturation et utilisation](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. Cliquez sur le bouton Télécharger pour télécharger une copie de votre facture PDF, puis sélectionnez **Télécharger la facture**. Si vous recevez le message **Non disponible**, consultez la section [Pourquoi ne puis-je pas voir de facture pour la dernière période de facturation ?](#noinvoice)

    ![Capture d’écran qui montre les périodes de facturation, l’option de téléchargement et le total des frais pour chaque période de facturation](./media/download-azure-invoice-daily-usage-date/downloadinvoice.png)

4. Vous pouvez également télécharger une analyse quotidienne des quantités consommées et des frais estimés en cliquant sur **Télécharger le fichier csv**.

    ![Capture d’écran qui montre la page Télécharger les données sur l’utilisation et la facturation](./media/download-azure-invoice-daily-usage-date/usageandinvoice.png)

Pour plus d’informations sur votre facture, consultez la page [Comprendre votre facture Microsoft Azure](../understand/review-individual-bill.md). Pour obtenir de l’aide dans la gestion de vos coûts, consultez [Analyser les frais inattendus](../understand/analyze-unexpected-charges.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Télécharger les factures pour un Contrat client Microsoft

Les factures sont générées pour chaque [profil de facturation](../understand/mca-overview.md#billing-profiles) dans le Contrat client Microsoft. Vous devez être Gestionnaire de factures, Propriétaire, Contributeur ou Lecteur de profil de facturation pour télécharger des factures à partir du portail Azure.

1. Recherchez **Gestion des coûts + facturation**.
2. Sélectionnez un profil de facturation.
3. Sélectionnez **Factures**.
4. Dans la grille des factures, recherchez la ligne de la facture que vous souhaitez télécharger.
5. Cliquez sur le bouton Télécharger à la fin de la ligne.
6. Dans le menu contextuel de téléchargement, sélectionnez **Facture**.

Si vous ne voyez pas de facture pour la dernière période de facturation, consultez **Informations supplémentaires**. <!-- Fix this -->
### <a name="why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> Pourquoi ne puis-je pas voir de facture pour la dernière période de facturation ?

Si vous ne voyez pas de facture, cela peut être pour plusieurs raisons :

- Cela fait moins de 30 jours que vous êtes inscrit à Azure.

- La facture n’est pas encore générée. Patientez jusqu’à la fin de la période de facturation.

- Vous n’êtes pas autorisé à afficher les factures. Si vous avez un Contrat client Microsoft, vous devez être le Gestionnaire de factures, Propriétaire, Contributeur ou Lecteur de profil de facturation. Pour les autres abonnements, il est possible que vous ne voyiez pas les anciennes factures si vous n’êtes pas l’administrateur de compte. Pour en savoir plus sur l’accès aux informations de facturation, consultez [Manage access to Azure billing using roles](manage-billing-access.md) (Utiliser des rôles pour gérer l’accès à la facturation Azure).

- Si vous bénéficiez d’un essai gratuit ou avez un crédit mensuel avec votre abonnement que vous n’avez pas dépassé, vous n’obtenez pas de facture, sauf si vous avez un Contrat client Microsoft.

## <a name="get-your-invoice-in-email-pdf"></a>Obtenir votre facture par e-mail (.pdf)

Vous pouvez choisir de configurer des destinataires supplémentaires qui recevront votre facture Azure par e-mail. Cette fonctionnalité n’est peut-être pas disponible pour certains abonnements tels que les offres de support, les contrats Entreprise ou Azure dans Open. Si vous avez un Contrat Client Microsoft, consultez [Obtenir les factures liées à votre profil de facturation par e-mail](../understand/download-azure-invoice.md#get-your-billing-profiles-invoice-in-email).

### <a name="get-your-subscriptions-invoices-in-email"></a>Obtenir les factures de votre abonnement par e-mail

1. Sélectionnez votre abonnement sur la [page Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Choisissez cette option pour chacun de vos abonnements. Cliquez sur **Factures** pour sélectionner **l’envoi de votre facture par E-mail**.

    ![Capture d’écran montrant le processus d’abonnement](./media/download-azure-invoice-daily-usage-date/invoicesdeeplink01.png)

2. Cliquez sur **Activation** et acceptez les termes du contrat.

    ![Capture d’écran montrant l’étape 2 du flux de l’adhésion](./media/download-azure-invoice-daily-usage-date/invoicearticlestep02.png)

3. Une fois que vous avez accepté le contrat, vous pouvez configurer des destinataires supplémentaires. Quand un destinataire est supprimé, l’adresse e-mail n’est plus stockée. Si vous changez d’avis, vous devez les ajouter à nouveau.

    ![Capture d’écran montrant l’étape 3 du flux de l’adhésion](./media/download-azure-invoice-daily-usage-date/invoicearticlestep03.png)

Si vous ne recevez pas d’e-mail une fois ces étapes terminées, vérifiez que votre adresse e-mail est correcte dans les [préférences de communication sur votre profil](https://account.microsoft.com/profile) dans le centre de compte Microsoft.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Ne pas adhérer à l’obtention des factures de votre abonnement par e-mail

Vous pouvez ne pas adhérer à l’obtention de votre facture par e-mail en suivant les étapes ci-dessus et en cliquant sur **Désactiver l’envoi de facture par e-mail**. Cette option supprime toutes les adresses e-mail définies pour recevoir les factures par e-mail. Vous pouvez reconfigurer les destinataires si vous réadhérez.

 ![Capture d’écran montrant le flux de l’adhésion](./media/download-azure-invoice-daily-usage-date/invoicearticlestep04.png)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Obtenir les factures de votre Contrat client Microsoft par e-mail

Si vous avez un Contrat client Microsoft, vous pouvez adhérer à l’obtention de votre facture par e-mail. Tous les Gestionnaires de factures, Propriétaires, Contributeurs et Lecteurs de profil de facturation obtiennent la facture par e-mail. Les lecteurs ne peuvent pas mettre à jour la préférence d’envoi par e-mail de la facture.

1. Recherchez **Gestion des coûts + facturation**.
1. Sélectionnez un profil de facturation.
1. Sous **Paramètres**, sélectionnez **Propriétés**.
1. Sous **Facture par e-mail**, sélectionnez **Mettre à jour la préférence d’envoi par e-mail de la facture**.
1. Sélectionnez **Activer**.
1. Cliquez sur **Update**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Ne pas adhérer à l’obtention des factures de votre profil de facturation par e-mail

Vous pouvez ne pas adhérer à l’obtention de votre facture par e-mail en suivant les étapes ci-dessus et en cliquant sur **Désactiver**. Tous les Gestionnaires de factures, Propriétaires, Contributeurs et Lecteurs seront également exclus de l’adhésion à l’obtention de la facture par e-mail. Si vous êtes lecteur, vous ne pouvez pas changer la préférence d’envoi par e-mail de la facture.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur vos facture et frais, consultez :

- [Comprendre votre facture Microsoft Azure](../understand/review-individual-bill.md)
- [Comprendre les modalités sur votre facture Azure](../understand/understand-invoice.md)
- [Comprendre les termes figurant dans le détail de votre utilisation de Microsoft Azure](../understand/understand-usage.md)
- [Afficher les tarifs Azure de votre organisation](ea-pricing.md)

Si vous avez un Contrat client Microsoft, consultez :

- [Comprendre les frais indiqués sur la facture de votre profil de facturation](../understand/review-customer-agreement-bill.md)
- [Comprendre les termes indiqués sur la facture de votre profil de facturation](../understand/mca-understand-your-invoice.md)
- [Comprendre le fichier d’utilisation et de frais Azure pour votre profil de facturation](../understand/mca-understand-your-usage.md)
- [Afficher et télécharger les documents fiscaux pour votre profil de facturation](../understand/mca-download-tax-document.md)
- [Afficher les tarifs Azure de votre organisation](ea-pricing.md)
