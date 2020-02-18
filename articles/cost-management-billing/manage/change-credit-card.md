---
title: Changer de carte de crédit pour Azure
description: Décrit comment changer la carte de crédit utilisée pour payer un abonnement Azure.
author: bandersmsft
manager: judupont
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: 10d475b9c0c33dfd17c3d87beed00154511e1ab6
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188458"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Ajouter, mettre à jour ou supprimer une carte de crédit pour Azure

Ce document s’applique aux clients qui se sont inscrits à Azure Online avec une carte de crédit.

Dans le portail Azure, vous pouvez changer votre mode de paiement par défaut pour une nouvelle carte de crédit, mettre à jour les informations de votre carte de crédit et supprimer les cartes de crédit que vous n’utilisez pas. Vous devez être [administrateur de compte](billing-subscription-transfer.md#whoisaa) pour effectuer ces changements.

Les modes de paiement pris en charge pour Microsoft Azure sont les cartes de crédit et les chèques/virements. Pour obtenir l’approbation du paiement par chèque/virement, consultez [Payer pour les abonnements Azure par facture](pay-by-invoice.md).

Si vous avez un Contrat client Microsoft, vos modes de paiement sont associés à des profils de facturation. Découvrez comment [vérifier l’accès à un Contrat client Microsoft](#check-the-type-of-your-account). Si vous disposez d’un Contrat client Microsoft, allez directement à [Gérer les cartes de crédit d’un Contrat client Microsoft](#manage-credit-cards-for-a-microsoft-customer-agreement).

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Gérer les cartes de crédit pour un abonnement Azure

Les sections suivantes s’appliquent aux clients qui disposent d’un compte de facturation du Programme Microsoft Online Services. Découvrez comment [vérifier votre type de compte de facturation](#check-the-type-of-your-account). Si votre type de compte de facturation est Programme Microsoft Online Services, les modes de paiement sont associés à des abonnements Azure individuels.

### <a name="change-credit-card-for-a-subscription"></a>Changer la carte de crédit associée à un abonnement

Vous pouvez remplacer la carte de crédit par défaut de votre abonnement Azure par une nouvelle carte de crédit ou une carte de crédit déjà enregistrée dans le portail Azure. Vous devez être l’administrateur du compte pour changer de carte de crédit. Si plusieurs de vos abonnements ont le même mode de paiement actif, le changement de mode de paiement actif sur un de ces abonnements met également à jour le mode de paiement actif sur les autres.


Vous pouvez remplacer la carte de crédit par défaut de votre abonnement par une nouvelle en effectuant les étapes suivantes :

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

Vous pouvez aussi changer la carte de crédit par défaut de votre abonnement pour la remplacer par une autre carte déjà enregistrée sur votre compte en effectuant les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.
1. Recherchez **Gestion des coûts + facturation**.

    ![Capture d'écran illustrant la recherche](./media/change-credit-card/search.png)

1. Sélectionnez l’abonnement auquel vous voulez ajouter la carte de crédit.
1. Sélectionnez **Modes de paiement**.

    ![Capture d’écran qui montre l’option Gérer les modes de paiement sélectionnée.](./media/change-credit-card/payment-methods-blade-x.png)

1. Cochez la case en regard de la carte que vous souhaitez définir comme mode de paiement actif.
1. Cliquez sur **Définir comme actif**.
    ![Capture d’écran montrant une carte de crédit sélectionnée et définie comme active.](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>Entrer les détails de la carte de crédit

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

### <a name="delete-a-credit-card-from-the-account"></a>Supprimer une carte de crédit du compte

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.
1. Sélectionnez **Gestion des coûts + facturation** sur le côté gauche de la page.

    ![Capture d'écran illustrant la recherche](./media/change-credit-card/search.png)

1. Sous **Facturation**, sélectionnez **Modes de paiement**.

    ![Capture d’écran qui montre l’option Gérer les modes de paiement sélectionnée.](./media/change-credit-card/payment-methods-blade-x.png)

1. Cochez la case en regard de la carte à supprimer.
1. Cliquez sur **Supprimer**.

Si votre carte de crédit est le mode de paiement actif pour un de vos abonnements Microsoft, vous ne pouvez pas la supprimer de votre compte Azure. Changez le mode de paiement actif pour tous les abonnements liés à cette carte de crédit, puis réessayez.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Gérer les cartes de crédit pour un Contrat client Microsoft

Les sections suivantes s’appliquent aux clients qui disposent d’un Contrat client Microsoft et qui sont inscrits à Azure Online avec une carte de crédit. [Découvrez comment vérifier si vous avez un Contrat client Microsoft](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Changer la carte de crédit par défaut 
Si vous avez un Contrat client Microsoft, votre carte de crédit est associée à un profil de facturation. Pour changer le mode de paiement d’un profil de facturation, vous devez être la personne qui s’est inscrite à Azure et qui a créé le compte de facturation.

Si vous voulez changer le mode de paiement par défaut de votre profil de facturation en chèque/virement, consultez [Payer pour les abonnements Azure par facture](pay-by-invoice.md).

Pour changer votre carte de crédit, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Effectuez une recherche sur **Gestion des coûts + facturation**.
1. Dans le menu de gauche, cliquez sur **Profils de facturation**.
1. Sélectionnez un profil de facturation.
1. Dans le menu de gauche, sélectionnez **Méthodes de paiement**.

   ![Capture d’écran montrant les modes de paiement dans le menu](./media/change-credit-card/payment-methods-tab-mca.png)

1. Dans la section **Mode de paiement par défaut**, cliquez sur **Modifier**.

    ![Capture d’écran illustrant le bouton de modification](./media/change-credit-card/change-payment-method-mca.png)

1. Dans le nouveau panneau à droite, sélectionnez une carte existante dans la liste déroulante ou ajoutez-en une en cliquant sur le lien « Ajouter un nouveau mode de paiement » en bleu.

### <a name="edit-or-delete-a-credit-card"></a>Modifier ou supprimer une carte de crédit

Vous pouvez modifier les informations de la carte de crédit (par exemple mettre à jour la date d’expiration) et supprimer des cartes de crédit de votre compte dans le portail Azure. Vous pouvez supprimer une carte de crédit seulement si elle n’est associée à aucun abonnement ou profil de facturation Azure. Si elle est associée à un abonnement Azure désactivé, vous devez attendre que l’abonnement soit supprimé (de 30 à 90 jours après l’annulation) pour pouvoir supprimer la carte de crédit.

Pour modifier ou supprimer une carte de crédit, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Effectuez une recherche sur **Gestion des coûts + facturation**.
1. Dans le menu de gauche, cliquez sur **Profils de facturation**.
1. Sélectionnez un profil de facturation.
1. Dans le menu de gauche, sélectionnez **Méthodes de paiement**.

   ![Capture d’écran montrant les modes de paiement dans le menu](./media/change-credit-card/payment-methods-tab-mca.png)

1. Dans la section **Vos cartes de crédit**, recherchez la carte de crédit que vous voulez modifier ou supprimer.
1. Sélectionnez les points de suspension (`...`) à la fin de la ligne.

    ![Capture d’écran montrant les points de suspension](./media/change-credit-card/edit-delete-credit-card-mca.png)

1. Pour modifier les informations de votre carte de crédit, sélectionnez **Modifier** dans le menu contextuel.
1. Pour supprimer votre carte de crédit, sélectionnez **Supprimer** dans le menu contextuel.

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

## <a name="check-the-type-of-your-account"></a>Vérifier le type de votre compte
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [réservations Azure](../reservations/save-compute-costs-reservations.md) pour déterminer si elles peuvent vous permettre de réaliser des économies.
