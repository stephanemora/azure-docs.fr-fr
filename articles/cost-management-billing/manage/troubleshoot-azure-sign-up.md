---
title: Résoudre les problèmes d’inscription Azure
description: Résolution d’un problème que vous pouvez rencontrer un problème quand vous tentez de créer un compte dans le centre des comptes du Portail Microsoft Azure.
services: billing
author: v-miegge
manager: dcscontentpm
editor: v-jesits
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: e13a55143e554a55a2902fc2350f6fde6a8afb09
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75984825"
---
# <a name="troubleshoot-azure-sign-up"></a>Résoudre les problèmes d’inscription Azure

Vous pouvez rencontrer un problème quand vous tentez de créer un compte dans le Portail Microsoft Azure ou le centre des comptes Azure. Avant d’essayer de résoudre le problème, vérifiez ce qui suit :

- Les informations que vous avez fournies pour votre profil de compte Azure (notamment l’adresse e-mail du contact, l’adresse postale et le numéro de téléphone) sont correctes.
- Les informations de la carte de crédit sont correctes.
- Vous n’avez pas déjà un compte Microsoft avec les mêmes informations.

## <a name="resolutions"></a>Résolutions

Pour résoudre les erreurs éventuelles, sélectionnez le problème que vous rencontrez lorsque vous essayez de vous inscrire à Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Erreur : *En raison d’un problème lié à votre compte, nous ne pouvons pas procéder à l’inscription. Veuillez contacter le support de facturation.*

Pour résoudre ce problème, effectuez les étapes suivantes :

1. Connectez-vous au [Centre des comptes Azure](https://account.azure.com/Profile) en utilisant des informations d’identification d’administrateur de compte.

2. Sélectionnez **Modifier les détails**.

3. Assurez-vous que tous les champs d’adresse sont renseignés et valides.

4. Quand vous souscrivez l’abonnement Azure, vérifiez que l’adresse de facturation entrée pendant l’enregistrement de la carte de crédit correspond à celle que possède votre banque.

Si l’erreur persiste, réessayez de vous inscrire à l’aide d’un navigateur différent.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>La barre de progression se bloque dans la section *Vérification d’identité par carte*.

Pour terminer la vérification d’identité par carte, les cookies tiers doivent être autorisés pour votre navigateur.

![Vérification d'identité par carte](./media/troubleshoot-azure-sign-up/identify-verification-by-card.png)

Effectuez la procédure suivante pour mettre à jour les paramètres de cookies de votre navigateur.

1. Si vous utilisez Chrome,sélectionnez **Paramètres** > **Afficher les paramètres avancés** > **Confidentialité** > **Paramètres de contenu**. Décochez **Bloquer les cookies et les données de site tiers**.

2. Si vous utilisez Microsoft Edge, sélectionnez **Paramètres** > **Afficher les paramètres avancés** > **Cookies** > **Ne pas bloquer les cookies**.

3. Actualisez la page d’inscription à Azure, puis vérifiez si le problème est résolu.

4. Si l’actualisation n’a pas résolu le problème, quittez le navigateur, redémarrez-le, puis réessayez.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Le formulaire relatif à la carte de crédit ne prend pas en charge mon adresse de facturation

Votre adresse de facturation doit se trouver dans le pays que vous avez sélectionné dans la section **About you** (À propos de vous). Veillez à sélectionner le bon pays.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Aucun message texte ni appel lors de la vérification du compte à l’inscription

Bien que le processus soit généralement rapide, l’envoi du code de vérification peut prendre jusqu’à quatre minutes. Le numéro de téléphone que vous entrez pour la vérification n’est pas stocké sous forme de numéro de contact pour le compte.

Voici quelques conseils supplémentaires :

- Vous ne pouvez pas utiliser un numéro de téléphone VoIP pour le processus de vérification par téléphone.
- Vérifiez par deux fois le numéro de téléphone saisi, y compris l’indicatif du pays que vous sélectionnez dans le menu déroulant.
- Si votre téléphone ne reçoit pas les messages texte (SMS), essayez l’option **M’appeler**.
- Vérifiez que votre téléphone peut recevoir des appels ou des SMS provenant d’un numéro de téléphone basé aux États-Unis.

Lorsque vous obtenez le message texte ou recevez l’appel, entrez le code que vous avez reçu dans la zone de texte.

### <a name="credit-card-declined-or-not-accepted"></a>Carte de crédit refusée

Les cartes de crédit ou de débit virtuelles ou prépayées ne sont pas acceptées comme mode de paiement pour les abonnements Azure. Pour voir les autres raisons qui peuvent conduire au refus de votre carte de crédit, consultez [Votre carte bancaire est refusée lors de l’inscription à Azure](https://support.microsoft.com/help/4042960).

### <a name="free-trial-is-not-available"></a>L’essai gratuit n’est pas disponible

Avez-vous utilisé un abonnement Azure auparavant ? Les conditions d’utilisation d’Azure n’autorisent l’activation que d’une seule évaluation gratuite pour tout utilisateur n’ayant jamais utilisé Azure. Si vous avez déjà souscrit un autre type d’abonnement Azure, vous ne pouvez pas activer une version d'évaluation gratuite. Inscrivez-vous à un [abonnement de type paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>J’ai vu des frais sur mon compte d’essai gratuit

Vous pouvez voir une petite vérification retenue sur votre compte de carte de crédit après votre inscription. La retenue est supprimée dans un délai de trois à cinq jours. Si vous êtes soucieux quant à la gestion des coûts, lisez-en davantage sur la [prévention des coûts inattendus](getting-started.md).

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Je n’arrive pas à activer un plan d’avantages Azure de type MSDN, BizSpark, BizSparkPlus ou MPN

Assurez-vous que vous utilisez les bonnes informations de connexion. Consultez ensuite le programme d’avantages pour vous assurer que vous êtes éligible.

- MSDN
  - Vérifiez l’état de votre éligibilité dans votre [page de compte MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Si vous ne pouvez pas vérifier votre état, contactez l’un des [Centres de service client des abonnements MSDN](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft for Startups
  - Connectez-vous au [Portail Microsoft for Startups](https://startups.microsoft.com/#start-two) pour vérifier l’état de votre éligibilité à Microsoft for Startups.
  - Si vous ne pouvez pas vérifier votre état, vous pouvez obtenir de l’aide sur les [forums Microsoft for Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Connectez-vous au [portail MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) pour vérifier l’état de votre éligibilité. Vous pouvez bénéficier d’avantages supplémentaires si vous disposez des [compétences de plateforme cloud](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx) appropriées.
  - Si vous ne pouvez pas vérifier votre état, contactez le [support MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Je n’arrive pas à activer un nouvel abonnement Azure dans Open

Pour pouvoir créer un nouvel abonnement Azure dans Open, vous devez disposer d’une clé d’activation de service en ligne (OSA) valide associée à au moins un jeton Azure dans Open. Si vous ne disposez d’aucune clé OSA, contactez l’un des partenaires Microsoft répertoriés dans [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Erreur : *Vous n’êtes pas éligible à un abonnement Azure*

Pour résoudre ce problème, vérifiez que les points suivants sont vrais :

- Les informations que vous avez fournies pour votre profil de compte Azure (notamment l’adresse e-mail du contact, l’adresse postale et le numéro de téléphone) sont correctes.
- Les informations de la carte de crédit sont correctes.
- Vous n’avez pas déjà un compte Microsoft utilisant les mêmes informations.

### <a name="error-your-current-account-type-is-not-supported"></a>Erreur : *Le type de votre compte actuel n’est pas pris en charge*

Ce problème peut se produire si le compte est inscrit dans un [répertoire Azure AD non managé](../../active-directory/users-groups-roles/directory-self-service-signup.md) et ne se trouve pas dans le répertoire Azure AD de votre organisation.

Pour résoudre ce problème, inscrivez-vous au compte Azure en utilisant un autre compte, ou prenez en charge le répertoire AD non managé. Pour plus d’informations, consultez [Prendre le contrôle d’un annuaire non géré en tant qu’administrateur dans Azure Active Directory](../../active-directory/users-groups-roles/domains-admin-takeover.md).

## <a name="additional-help-resources"></a>Ressources d'aide supplémentaires

Autres articles sur la résolution des problèmes liés à la facturation et aux abonnements Azure

- [Carte refusée](troubleshoot-declined-card.md)
- [Problèmes liés à la souscription d'un abonnement](troubleshoot-sign-in-issue.md)
- [Aucun abonnement trouvé](no-subscriptions-found.md)
- [Désactivation de l’affichage des coûts d’entreprise](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contactez-nous pour obtenir de l'aide

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

- [Documentation sur la facturation Azure](../../billing/index.md)
