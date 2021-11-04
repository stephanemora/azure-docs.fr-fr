---
title: Résoudre des problèmes d’affichage de facture dans le portail Azure
description: Résolution d’un problème lors d’une tentative d’affichage de votre facture dans le portail Azure.
services: cost-management-billing
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: banders
ms.openlocfilehash: 6f90d077b9cb7e1836170084c683e47cdafc480e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464677"
---
# <a name="troubleshoot-issues-while-trying-to-view-invoice-in-the-azure-portal"></a>Résoudre des problèmes lors d’une tentative d’affichage de facture dans le portail Azure

Vous pouvez rencontrer des problèmes lorsque vous essayez d’afficher votre facture dans le portail Azure. Ce guide rapide aborde des problèmes courants.
 
## <a name="common-issues-and-solutions"></a>Problèmes courants et solutions

#### <a name="you-see-the-message-we-cant-display-the-invoices-for-your-subscription-this-typically-happens-when-you-sign-in-with-an-email-which-doesnt-have-access-to-view-invoices-check-youve-signed-in-with-the-correct-email-address-if-you-are-still-seeing-the-error-see-why-you-might-not-see-an-invoice"></a><a name="subnotfound"></a> Vous voyez le message « Nous ne pouvons pas afficher les factures pour votre abonnement. Cela se produit généralement lorsque vous vous connectez avec une adresse e-mail qui n’est pas autorisée à afficher les factures. Vérifiez que vous vous êtes connecté avec l’adresse e-mail appropriée. Si l’erreur persiste, reportez-vous Je ne vois pas ma facture. Pourquoi ?

Cela se produit lorsque l’identité que vous avez utilisée pour vous connecter n’a pas accès à l’abonnement.

Pour résoudre ce problème, essayez l'une des options suivantes : 

**Vérifiez que vous êtes connecté avec l’adresse e-mail appropriée :**

Seule l’adresse e-mail ayant le rôle d’administrateur de compte pour l’abonnement peut afficher sa facture. Vérifiez que vous êtes connecté avec l’adresse e-mail appropriée. L’adresse e-mail s’affiche dans l’e-mail que vous avez reçu lors de la génération de votre facture.  

  ![Capture d’écran montrant l’adresse e-mail de facturation](./media/troubleshoot-cant-find-invoice/invoice-email.png)

**Vérifiez que vous êtes connecté avec le compte approprié :**

Certains utilisateurs ont deux comptes avec la même adresse e-mail : un compte professionnel ou scolaire et un compte personnel. En règle générale, un seul de leurs comptes dispose de l’autorisation d’affichage des factures. Vous pouvez avoir deux comptes avec votre adresse e-mail. Si vous vous connectez avec le compte qui ne dispose pas de l’autorisation, vous ne voyez pas la facture. Pour déterminer si vous disposez de plusieurs comptes et utilisez un autre compte, effectuez les étapes ci-dessous  :

1. Connectez-vous au [portail Azure](https://portal.azure.com) dans une fenêtre InPrivate/Incognito.
1. Si vous disposez de plusieurs comptes avec la même adresse e-mail, vous serez invité à sélectionner **Compte professionnel ou scolaire** ou **Compte personnel**. Sélectionnez l’un des comptes, puis suivez les [instructions ici pour afficher votre facture](../understand/download-azure-invoice.md#download-your-mosp-azure-subscription-invoice).  

    ![Capture d’écran montrant la sélection du compte](./media/troubleshoot-cant-find-invoice/two-accounts.png)

1. Essayez un autre compte si vous ne pouvez toujours pas afficher la facture dans le portail Azure.

**Vérifiez que vous êtes connecté au bon locataire Azure Active Directory (AAD) :**

Votre compte de facturation et abonnement sont associés à un locataire AAD. Si vous êtes connecté à un mauvais locataire, vous ne verrez pas la facture de votre abonnement. Procédez comme suit pour basculer entre les locataires dans le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez votre adresse e-mail en haut à droite de la page.
1. Sélectionnez Changer de répertoire.  

    ![Capture d’écran montrant la sélection du changement de répertoire](./media/troubleshoot-cant-find-invoice/select-switch-tenant.png)

1. Sélectionnez un locataire dans la section Tous les répertoires. Si vous ne voyez pas la section Tous les répertoires, vous n’avez pas accès à plusieurs locataires.  

    ![Capture d’écran montrant la sélection d’un autre répertoire](./media/troubleshoot-cant-find-invoice/select-another-tenant.png)

#### <a name="you-couldnt-find-the-invoice-that-you-see-on-your-credit-card-statement"></a><a name="cantsearchinvoice"></a>Vous n’avez pas trouvé la facture que vous voyez sur votre relevé de carte de crédit

Vous constatez des frais sur votre carte de crédit **Microsoft Gxxxxxxxxx**. Vous pouvez retrouver toutes les autres factures dans le portail mais pas Gxxxxxxxxx. Cela se produit lorsque la facture appartient à un autre abonnement ou profil de facturation. Effectuez les étapes ci-dessous pour afficher la facture.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez le numéro de facture dans la barre de recherche du portail Azure.
1. Sélectionnez **Afficher votre facture**.  

    ![Capture d’écran montrant la recherche de facture](./media/troubleshoot-cant-find-invoice/search-invoice.png)

## <a name="contact-us-for-help"></a>Contactez-nous pour obtenir de l'aide

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

- [Consulter et télécharger votre facture Azure](../understand/download-azure-invoice.md)
- [Afficher et télécharger vos informations d’utilisation et vos frais Azure](../understand/download-azure-daily-usage.md)
- [Erreur Aucun abonnement trouvé lors de la connexion au portail Azure](no-subscriptions-found.md)