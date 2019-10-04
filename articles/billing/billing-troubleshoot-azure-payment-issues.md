---
title: Résoudre les problèmes de paiement d’Azure
description: Résolution d’un problème lors de la mise à jour du compte d’informations de paiement dans le centre des comptes du Portail Microsoft Azure.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "69656844"
---
# <a name="troubleshoot-azure-payment-issues"></a>Résoudre les problèmes de paiement d’Azure

Vous pouvez rencontrer un problème quand vous tentez de mettre à jour les informations du compte de paiement dans le Portail Microsoft Azure ou le centre des comptes Azure. Avant d’essayer de résoudre le problème, respectez les consignes suivantes :

- Vérifiez que les informations que vous avez fournies pour votre profil de compte Azure (notamment l’adresse e-mail du contact, l’adresse postale et le numéro de téléphone) sont correctes.
- Vérifiez que les informations de la carte de crédit sont correctes.
- Vérifiez que vous n’avez pas déjà un compte Microsoft avec les mêmes informations.

## <a name="issues"></a>Problèmes

Pour résoudre les erreurs éventuelles, sélectionnez le problème que vous rencontrez lorsque vous essayez de vous inscrire à Azure.

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Impossible de supprimer la carte de crédit du mode de paiement enregistré pour la facturation

Délibérément, vous ne pouvez pas supprimer la carte de crédit de l’abonnement actif.

Si une carte doit être supprimée, soit une nouvelle carte doit être ajoutée à l’abonnement pour que l’ancien instrument de paiement puisse être supprimé avec succès, soit vous devez annuler l’abonnement. Cela permet de supprimer définitivement l’abonnement, ainsi que la carte.

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Impossible de supprimer un ancien mode de paiement après en avoir ajouté un nouveau

Le nouvel instrument de paiement n’est peut-être pas associé à l’abonnement. Pour associer plus aisément l’instrument de paiement à l’abonnement, consultez [Ajouter, mettre à jour ou supprimer une carte de crédit ou débit pour Azure](billing-how-to-change-credit-card.md).

Pour résoudre les problèmes liés à un refus de carte, reportez-vous à [Comment résoudre les problèmes de carte refusée lors de l’inscription à Azure](billing-troubleshoot-declined-card.md).

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Impossible de supprimer un mode de paiement en raison du message d’erreur *Impossible de supprimer le mode de paiement*

Ce problème est dû à un solde impayé. Réglez tous les soldes impayés avant de supprimer le mode de paiement.

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Impossible de voir les abonnements sous mon compte pour mettre à jour le mode de paiement

Vous utilisez peut-être un ID de messagerie qui diffère de celui utilisé pour les abonnements.

Pour détecter ce problème, consultez [Aucun abonnement trouvé pour le Portail Azure ou le Centre des comptes Azure](billing-no-subscriptions-found.md).

### <a name="unable-to-make-payment-for-a-subscription"></a>Impossible de procéder au paiement d’un abonnement

Si vous recevez le message d’erreur suivant : *L’échéance du paiement est dépassée. Un problème est survenu avec votre moyen de paiement* ou *Nous sommes désolés, il est impossible d’enregistrer les informations. Fermez le navigateur et réessayez.* Il existe un paiement en attente sur la carte car celle-ci a été refusée par votre institution financière.

Vérifiez que le solde de la carte de crédit est suffisant pour que le paiement soit effectué. S’il n’est pas suffisant, utilisez une autre carte ou bien contactez votre institution financière pour résoudre le problème.

Consultez votre banque au sujet des problèmes suivants :

- Les transactions internationales ne sont pas activées.
- La carte comporte une limite de crédit ou le solde doit être réglé.
- Un paiement périodique est activé sur la carte.

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Impossible de modifier le mode de paiement en raison de problèmes de navigateur (le navigateur se bloque, ne charge pas, etc.)

Déconnectez-vous de toutes les sessions Azure actives, puis suivez les étapes de l’article [Navigation InPrivate dans Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) pour démarrer une session InPrivate dans Microsoft Edge ou Internet Explorer.

Dans la session privée, suivez les étapes de la [procédure de modification d’une carte de crédit](billing-how-to-change-credit-card.md) pour mettre à jour ou modifier les informations relatives à la carte de crédit.

Vous pouvez également essayer d’effectuer les opérations suivantes :

- Actualiser votre navigateur
- Utiliser un autre navigateur
- Supprimer les cookies mis en cache

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Mon abonnement toujours désactivé après la mise à jour du mode de paiement.

Ce problème est dû à un solde impayé. Réglez tous les soldes impayés avant de supprimer le mode de paiement.

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Impossible de modifier le mode de paiement en raison d’une page de réponse d’erreur XML

Vous recevez ce message si vous utilisez le [Portail Azure](https://portal.azure.com/) pour ajouter une nouvelle carte de crédit.

Pour ajouter les détails de la carte, connectez-vous au portail du compte Azure à l’aide de l’adresse e-mail de l’administrateur du compte.

## <a name="additional-help-resources"></a>Ressources d’aide supplémentaires

Autres articles sur la résolution des problèmes liés à la facturation et aux abonnements Azure

- [Carte refusée](billing-troubleshoot-declined-card.md)
- [Problèmes de connexion à l’abonnement](billing-troubleshoot-sign-in-issue.md)
- [Aucun abonnement trouvé](billing-no-subscriptions-found.md)
- [Désactivation de l’affichage des coûts d’entreprise](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contactez-nous pour obtenir de l'aide

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

- [Documentation sur la facturation Azure](index.md)
