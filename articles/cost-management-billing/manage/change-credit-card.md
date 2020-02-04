---
title: Modifier votre carte de crédit pour Azure
description: Décrit comment modifier la carte de crédit utilisée pour payer un abonnement Azure.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 6ae55e0075883de08af516b71089f00353975b34
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75985917"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Ajouter, mettre à jour ou supprimer une carte de crédit pour Azure

Ce document s’applique aux clients qui se sont inscrits à Azure Online avec une carte de crédit.

Dans le portail Azure, vous pouvez ajouter une nouvelle carte de crédit, mettre à jour une carte de crédit existante ou supprimer une carte de crédit que vous n’utilisez pas. Vous devez être [administrateur de compte](billing-subscription-transfer.md#whoisaa) pour effectuer ces changements.

Si vous avez un [contrat client Microsoft](#check-access-to-a-microsoft-customer-agreement), vos modes de paiement sont associés à des profils de facturation. Découvrez comment [modifier le mode de paiement par défaut d’un profil de facturation](#change-payment-method-for-a-billing-profile). Seul l’utilisateur s’étant inscrit à Azure peut mettre à jour le mode de paiement.

**Vous voulez passer au mode de paiement par facture (par chèque/virement bancaire) ?** Consultez [Paiement des abonnements Azure par facture](pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Ajouter une carte de crédit à un abonnement Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.
1. Recherchez **Gestion des coûts + facturation**.

    ![Capture d'écran illustrant la recherche](./media/change-credit-card/search.png)

1. Sélectionnez l’abonnement auquel vous voulez ajouter la carte de crédit.
1. Sélectionnez **Modes de paiement**.

    ![Capture d’écran qui montre l’option Gérer les modes de paiement sélectionnée.](./media/change-credit-card/payment-methods-blade-x.png)

1. Dans l’angle supérieur gauche, sélectionnez « + » pour ajouter une carte. Un formulaire de carte de crédit s’affiche à droite.
1. Entrez les détails de la carte de crédit.

    ![Capture d’écran qui illustre l’ajout d’une nouvelle carte](./media/change-credit-card/sub-add-new-x.png)

1. Pour faire de cette carte le mode de paiement actif, cochez la case en regard de **Définir comme mon mode de paiement actif** au-dessus du formulaire. Cette carte devient l’instrument de paiement actif pour tous les abonnements utilisant la même carte que l’abonnement sélectionné.

1. Sélectionnez **Suivant**.

Si vous obtenez une erreur après avoir ajouté la carte bancaire, consultez [Carte bancaire est refusée lors de l’inscription à Azure](../../billing/billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Mettre à jour une carte de crédit existante

Si le numéro de votre carte bancaire reste le même en cas de renouvellement, mettez à jour les détails de la carte bancaire existante, tels que la date d’expiration. Si votre numéro de carte bancaire change, en cas de perte, de vol ou d’expiration, procédez comme indiqué dans la section [Ajouter une nouvelle carte bancaire à votre compte](#addcard). Vous n’avez pas besoin de mettre à jour le cryptogramme visuel.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.
1. Recherchez **Gestion des coûts + facturation**.

    ![Capture d'écran illustrant la recherche](./media/change-credit-card/search.png)

1. Sélectionnez **Modes de paiement**.

    ![Capture d’écran qui montre l’option Gérer les modes de paiement sélectionnée.](./media/change-credit-card/payment-methods-blade-x.png)

1. Cliquez sur la carte de crédit que vous souhaitez modifier. Un formulaire de carte de crédit s’affiche à droite.

    ![Capture d’écran montrant la carte de crédit sélectionnée.](./media/change-credit-card/edit-card-x.png)

1. Mettez à jour les détails de la carte de crédit.
1. Sélectionnez **Enregistrer**.

## <a name="use-a-different-credit-card"></a>Utiliser une autre carte de crédit

Si plusieurs de vos abonnements ont le même mode de paiement actif, le changement de mode de paiement actif sur un de ces abonnements met également à jour le mode de paiement actif sur les autres.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.
1. Recherchez **Gestion des coûts + facturation**.

    ![Capture d'écran illustrant la recherche](./media/change-credit-card/search.png)

1. Sélectionnez l’abonnement auquel vous voulez ajouter la carte de crédit.
1. Sélectionnez **Modes de paiement**.

    ![Capture d’écran qui montre l’option Gérer les modes de paiement sélectionnée.](./media/change-credit-card/payment-methods-blade-x.png)

1. Cochez la case en regard de la carte que vous souhaitez définir comme mode de paiement actif.
1. Cliquez sur **Définir comme actif**.
    ![Capture d’écran montrant une carte de crédit sélectionnée et définie comme active.](./media/change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Suppression d’une carte de crédit à partir du compte

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.
1. Sélectionnez **Gestion des coûts + facturation** sur le côté gauche de la page.

    ![Capture d'écran illustrant la recherche](./media/change-credit-card/search.png)

1. Sous **Facturation**, sélectionnez **Modes de paiement**.

    ![Capture d’écran qui montre l’option Gérer les modes de paiement sélectionnée.](./media/change-credit-card/payment-methods-blade-x.png)

1. Cochez la case en regard de la carte à supprimer.
1. Cliquez sur **Supprimer**.

Si votre carte de crédit est le mode de paiement actif pour un de vos abonnements Microsoft, vous ne pouvez pas la supprimer de votre compte Azure. Changez le mode de paiement actif pour tous les abonnements liés à cette carte de crédit, puis réessayez.

## <a name="change-payment-method-for-a-billing-profile"></a>Modifiez le mode de paiement associé à un profil de facturation

Pour modifier le mode de paiement d’un profil de facturation, vous devez être la personne qui s’est inscrite à Azure.

Si vous basculez vers le mode de paiement par chèque/virement bancaire, découvrez comment [Modifier un profil de facturation pour le basculer vers un paiement par chèque/virement bancaire](pay-by-invoice.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Effectuez une recherche sur **Gestion des coûts + facturation**.
1. Dans le menu de gauche, cliquez sur **Profils de facturation**.

    ![capture d’écran montrant le profil de facturation dans le menu](./media/change-credit-card/billing-profile.png)

1. Sélectionnez un profil de facturation.
1. Dans le menu de gauche, sélectionnez **Méthodes de paiement**.

   ![Capture d’écran montrant les modes de paiement dans le menu](./media/change-credit-card/billing-profile-payment-methods.png)

1. Au-dessus du mode de paiement par défaut, cliquez sur **Modifier**.

    ![Capture d’écran illustrant le bouton de modification](./media/change-credit-card/customer-led-switch-credit-card.png)

1. Sélectionnez une carte existante ou ajoutez en une nouvelle.

## <a name="troubleshooting"></a>Dépannage
Les cartes virtuelles ou prépayées ne sont pas prises en charge. Si vous obtenez des erreurs lors de l’ajout ou de la mise à jour d’une carte de crédit valide, essayez d’ouvrir votre navigateur en mode privé.

## <a name="frequently-asked-questions"></a>Forum aux questions
Les sections suivantes répondent à des questions fréquemment posées sur la modification des informations de votre carte de crédit.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Mon abonnement est désactivé. Pourquoi je ne peux pas supprimer ma carte de crédit ?

Une fois que votre abonnement est désactivé ou annulé, nous attendons 90 jours avant de supprimer définitivement votre abonnement. Nous conservons votre mode de paiement dans nos dossiers pendant la période de rétention au cas où vous souhaiteriez réactiver l’abonnement. À l’issue de cette période, l’abonnement est supprimé définitivement.

Si vous avez besoin de supprimer votre carte de crédit avant la fin de la période de rétention de 90 jours, [réactivez votre abonnement](subscription-disabled.md). Si vous ne pouvez pas le réactiver, [contactez le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Pourquoi je reçois continuellement le message « Votre ouverture de session a expiré. Cliquez ici pour en ouvrir une nouvelle » ?

Si vous continuez à recevoir ce message d’erreur même après vous être déconnecté et reconnecté, réessayez avec une session de navigation privée.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Comment utiliser une carte différente pour chacun de mes abonnements ?

Malheureusement, si vos abonnements utilisent déjà la même carte, il n’est pas possible de les distinguer pour qu’ils utilisent des cartes différentes. Toutefois, quand vous souscrivez un nouvel abonnement, vous pouvez choisir d’utiliser un nouveau mode de paiement pour lui.

### <a name="how-do-i-make-payments"></a>Comment payer ?

Si vous configurez une carte de crédit comme mode de paiement, nous débitons automatiquement votre carte à l’issue de chaque période de facturation. Vous n’avez rien à faire.

Si vous [payez par facture](pay-by-invoice.md), envoyez votre paiement à l’endroit indiqué au bas de celle-ci.

### <a name="how-do-i-change-the-tax-id"></a>Comment modifier l’ID de taxe ?

Pour ajouter ou mettre à jour le numéro d’identification du contribuable, mettez à jour votre profil dans le [Centre des comptes Azure](https://account.azure.com/Profile), puis sélectionnez **Enregistrement de taxe**. Cet ID de taxe est utilisé pour les calculs d’exemption de taxe et figure sur votre facture.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l'accès à un contrat client Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [réservations Azure](../reservations/save-compute-costs-reservations.md) pour déterminer si elles peuvent vous permettre de réaliser des économies.
