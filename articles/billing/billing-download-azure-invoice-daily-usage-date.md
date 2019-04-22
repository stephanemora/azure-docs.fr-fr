---
title: Téléchargement de votre facture Azure et de vos données d’utilisation quotidienne | Microsoft Docs
description: Décrit comment télécharger ou afficher votre facture Azure et vos données d’utilisation quotidienne.
keywords: facturation, facture, téléchargement de facture, facture Azure, utilisation d’Azure
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: b78fb7d697f8a72b3c2f99c4509ea6ac5c5e5566
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281180"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Télécharger ou afficher votre facture Azure et vos données d’utilisation quotidienne

Pour la plupart des abonnements, cous pouvez télécharger votre facture à partir du [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou la faire envoyer par courrier électronique. Si vous êtes un client Azure avec un contrat Entreprise (client EA), vous ne pouvez pas télécharger les factures de votre organisation. Les factures sont envoyées à toute personne autorisée à recevoir des factures pour l’inscription.

Si vous êtes client EA ou que vous avez un [contrat de client Microsoft](#check-access-to-a-microsoft-customer-agreement), vous pouvez télécharger l’utilisation dans le [Azure portal](https://portal.azure.com/). Pour les autres abonnements, accédez à la [centre des comptes Azure](https://account.azure.com/Subscriptions) télécharger l’utilisation.

Seuls certains rôles sont autorisé à obtenir des informations de facturation et utilisation, telles que le compte administrateur ou administrateur d’entreprise. Pour en savoir plus sur l’accès aux informations de facturation, consultez [Manage access to Azure billing using roles](billing-manage-access.md) (Utiliser des rôles pour gérer l’accès à la facturation Azure).

Si vous avez un contrat de client de Microsoft, vous devez être un profil de facturation propriétaire, collaborateur ou lecteur, ou facturer manager pour afficher les informations de facturation et d’utilisation. Pour en savoir plus sur les rôles de facturation pour les contrats de client de Microsoft, consultez [tâches et rôles de profil de facturation](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Télécharger vos factures Azure (.pdf)

Pour la plupart des abonnements, vous pouvez télécharger votre facture à partir du portail Azure. Si vous avez un contrat de client de Microsoft, consultez le téléchargement de factures pour un profil de facturation.

### <a name="download-invoices-for-an-individual-subscription"></a>Télécharger les factures pour un abonnement individuel

1. Sélectionnez votre abonnement dans le [page abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) dans le portail Azure en tant que [un utilisateur ayant accès aux factures](billing-manage-access.md).

2. Sélectionnez **Factures**.

    ![Capture d’écran qui montre l’option Facturation et utilisation](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Cliquez sur **Télécharger la facture** pour afficher une copie de votre facture au format PDF. Si vous recevez le message **Non disponible**, consultez la section [Pourquoi ne puis-je pas voir de facture pour la dernière période de facturation ?](#noinvoice)

    ![Capture d’écran qui montre les périodes de facturation, l’option de téléchargement et le total des frais pour chaque période de facturation](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Vous pouvez également afficher votre utilisation quotidienne en cliquant sur la période de facturation.

Pour plus d’informations sur votre facture, consultez la page [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md). Pour obtenir de l’aide sur la gestion des coûts, voir [Éviter les coûts inattendus avec la gestion de la facturation et des coûts Azure](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Télécharger les factures d’un contrat de client de Microsoft

Les factures sont générées pour chaque [profil de facturation](billing-mca-overview.md#understand-billing-profiles) dans le contrat de client de Microsoft. Vous devez être un profil de facturation propriétaire, collaborateur, lecteur, ou la facture manager pour le téléchargement des factures à partir du portail Azure.

1. Effectuez une recherche sur **Gestion des coûts + facturation**.
2. Sélectionnez un profil de facturation.
3. Sélectionnez **Factures**.
4. Dans la grille de la facture, recherchez la ligne de la facture que vous souhaitez télécharger.
5. Cliquez sur le bouton de sélection (`...`) à la fin de la ligne.
6. Dans le menu contextuel de téléchargement, sélectionnez **facture**.

Si vous ne voyez pas une facture pour la dernière période de facturation, consultez **des informations supplémentaires**. <!-- Fix this -->
### <a name="noinvoice"></a> Pourquoi ne puis-je pas voir de facture pour la dernière période de facturation ?

Si vous ne voyez pas de facture, cela peut être pour plusieurs raisons :

- Cela fait moins de 30 jours que vous êtes inscrit à Azure.

- La facture n’est pas encore générée. Patientez jusqu’à la fin de la période de facturation.

- Vous n’êtes pas autorisé à afficher les factures. Si vous avez un contrat de client de Microsoft, vous devez être le profil de facturation propriétaire, collaborateur ou lecteur, ou Gestionnaire de facture. Pour les autres abonnements, vous ne pouvez pas voir anciennes factures si vous n’êtes pas administrateur de compte. Pour en savoir plus sur l’accès aux informations de facturation, consultez [Manage access to Azure billing using roles](billing-manage-access.md) (Utiliser des rôles pour gérer l’accès à la facturation Azure).

- Si vous avez une version d’évaluation gratuite ou d’un crédit mensuel avec votre abonnement vous n’est pas dépassé, vous n’obtiendrez une facture, sauf si vous avez un contrat de client de Microsoft.

## <a name="get-your-invoice-in-email-pdf"></a>Obtenir votre facture par e-mail (.pdf)

Vous pouvez choisir de configurer des destinataires supplémentaires qui recevront votre facture Azure par e-mail. Cette fonctionnalité n’est peut-être pas disponible pour certains abonnements tels que les offres de support, les contrats Entreprise ou Azure dans Open. Si vous avez un contrat Microsoft Customer, consultez la section obtenir votre profil de facturation factures par courrier électronique.

### <a name="get-your-subscriptions-invoices-in-email"></a>Obtenir les factures de votre abonnement par courrier électronique

1. Sélectionnez votre abonnement sur la [page Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Choisissez cette option pour chacun de vos abonnements. Cliquez sur **Factures** pour sélectionner **l’envoi de votre facture par E-mail**.

    ![Capture d’écran montrant le processus d’abonnement](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Cliquez sur **Activation** et acceptez les termes du contrat.

    ![Capture d’écran montrant l’étape 2 du flux de l’adhésion](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Une fois que vous avez accepté le contrat, vous pouvez configurer des destinataires supplémentaires. Quand un destinataire est supprimé, l’adresse e-mail n’est plus stockée. Si vous changez d’avis, vous devez les ajouter à nouveau.

    ![Capture d’écran montrant l’étape 3 du flux de l’adhésion](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Si vous n’obtenez pas de courrier électronique une fois ces étapes terminées, assurez-vous que votre adresse de messagerie est correcte dans les [préférences de communication sur votre profil](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Ne pas obtenir les factures de votre abonnement par courrier électronique

Vous pouvez refuser de l’obtention de votre facture par e-mail en suivant les étapes ci-dessus et en cliquant sur **refuser de factures par e-mail**. Cette option supprime toutes les adresses e-mail définies pour recevoir les factures par e-mail. Vous pouvez reconfigurer les destinataires si vous choisissez Nouveau.

 ![Capture d’écran montrant le flux de l’adhésion](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Obtenir les factures de votre contrat de client de Microsoft par courrier électronique

Si vous avez un contrat de client de Microsoft, vous pouvez choisir pour obtenir votre facture dans un message électronique. Tous les facturation profil aux propriétaires, contributeurs, lecteurs et facture gestionnaires obtiendra la facture par courrier électronique. Lecteurs ne peut pas mettre à jour la préférence de facture de courrier électronique.

1. Effectuez une recherche sur **Gestion des coûts + facturation**.
1. Sélectionnez un profil de facturation.
1. Sous **Paramètres**, sélectionnez **Propriétés**.
1. Sous **facture par E-mail**, sélectionnez **préférence de facture de courrier électronique de mise à jour**.
1. Sélectionnez **participer**.
1. Cliquez sur **Update**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Refuser l’obtention de vos factures de profil de facturation par courrier électronique

Vous pouvez refuser de l’obtention de votre facture par e-mail en suivant les étapes ci-dessus et en cliquant sur **refus**. Tous les propriétaires, contributeurs, lecteurs et facture gestionnaires de désinscription de l’obtention de la facture par courrier électronique, trop. Si vous êtes un lecteur, vous ne pouvez pas modifier la préférence de facture de courrier électronique.

## <a name="download-usage"></a>Télécharger l’utilisation

 Pour la plupart des abonnements, recherchez votre fichier d’utilisation quotidienne dans le [Centre des comptes Azure](https://account.azure.com/Subscriptions). Si vous êtes un client contrat entreprise ou que vous avez un contrat de client de Microsoft, vous pouvez télécharger l’utilisation dans le [Azure portal](https://portal.azure.com/). <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>Télécharger l’utilisation à partir du centre de compte (.csv)

1. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/subscriptions) en tant qu’administrateur de compte.

2. Sélectionnez l’abonnement pour lequel vous voulez obtenir les informations de facturation et d’utilisation.

3. Sélectionnez **HISTORIQUE DE FACTURATION**.

    ![Capture d’écran qui montre l’option d’historique de facturation](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Vous pouvez consulter vos relevés des six dernières périodes de facturation, ainsi que la période non facturée en cours.

    ![Capture d’écran qui montre les périodes de facturation, les options de téléchargement des factures et de l’utilisation quotidienne et le total des frais pour chaque période de facturation](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Sélectionnez **Afficher la déclaration actuelle** pour voir une estimation de vos frais au moment où l’estimation a été générée. Cette information est uniquement mise à jour quotidiennement et peut ne pas inclure l’ensemble de votre utilisation. Votre facture mensuelle peut différer de l’estimation.

    ![Capture d’écran qui montre l’option Afficher la déclaration actuelle](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Capture d’écran qui montre l’estimation des frais actuels](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Sélectionnez **Télécharger l’utilisation** pour télécharger les données d’utilisation quotidienne dans un fichier CSV. Si deux versions sont disponibles, téléchargez la version 2.

    ![Capture d’écran qui montre l’option Télécharger les informations d’utilisation](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Seul l’administrateur de compte peut accéder au centre des comptes Azure. Les autres administrateurs de facturation (notamment les propriétaires) peuvent obtenir des informations d’utilisation à l’aide des [API de facturation](billing-usage-rate-card-overview.md).

Pour plus d’informations sur votre utilisation quotidienne, consultez la page [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md). Pour obtenir de l’aide sur la gestion des coûts, voir [Éviter les coûts inattendus avec la gestion de la facturation et des coûts Azure](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Télécharger l’utilisation pour les clients EA

Pour afficher et télécharger des données d’utilisation en tant que contrat entreprise client, vous devez être un administrateur d’entreprise, propriétaire du compte, ou administrateur de service avec la vue de frais stratégie est activée.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Recherchez *coût Management + facturation*.

    ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Sélectionnez **Utilisation + frais**.
1. Pour le mois que vous souhaitez télécharger, sélectionnez **Télécharger**.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Télécharger l’utilisation de votre contrat de client de Microsoft

Pour afficher et télécharger des données d’utilisation pour un profil de facturation, vous devez être un profil de facturation propriétaire, collaborateur ou lecteur, ou le Gestionnaire de facture.

#### <a name="download-usage-for-billed-charges"></a>Télécharger l’utilisation pour les frais de facturation

1. Effectuez une recherche sur **Gestion des coûts + facturation**.
2. Sélectionnez un profil de facturation.
3. Sélectionnez **Factures**.
4. Dans la grille de la facture, recherchez la ligne de la facture correspondant à l’utilisation que vous souhaitez télécharger.
5. Cliquez sur le bouton de sélection (`...`) à la fin de la ligne.
6. Dans le menu contextuel de téléchargement, sélectionnez **l’utilisation d’Azure et les frais**.

#### <a name="download-usage-for-open-charges"></a>Télécharger l’utilisation pour les frais en cours

Vous pouvez également télécharger l’utilisation month-to-date pour la période de facturation actuelle, ce qui signifie que les frais n’ont pas encore été facturés.

1. Effectuez une recherche sur **Gestion des coûts + facturation**.
2. Sélectionnez un profil de facturation.
3. Dans le **vue d’ensemble** panneau, cliquez sur **Azure télécharger utilisation et des frais**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifiez l’accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur votre facture et vos frais, consultez :

- [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md)
- [Comprendre les termes du contrat sur votre facture Azure](billing-understand-your-invoice.md)
- [Comprendre les termes figurant dans le détail de votre utilisation de Microsoft Azure](billing-understand-your-usage.md)
- [Affichez la tarification Azure de votre organisation](billing-ea-pricing.md)

Si vous avez un contrat de client de Microsoft, consultez :

- [Comprendre les frais sur la facture pour votre profil de facturation](billing-mca-understand-your-bill.md)
- [Comprendre les termes du contrat sur la facture de votre profil de facturation](billing-mca-understand-your-invoice.md)
- [Comprendre le fichier d’utilisation et votre facturation Azure pour votre profil de facturation](billing-mca-understand-your-usage.md)
- [Afficher et télécharger des documents de taxe pour votre profil de facturation](billing-mca-download-tax-document.md)
- [Affichez la tarification Azure de votre organisation](billing-ea-pricing.md)
