---
title: Modifier votre carte de crédit pour Azure
description: Décrit comment modifier la carte de crédit utilisée pour payer un abonnement Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/27/2021
ms.author: banders
ms.openlocfilehash: fb37278a30e935d2bb65a7b132177a1bd588117d
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114731168"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>Ajouter ou mettre à jour une carte de crédit pour Azure

Ce document s’applique aux clients qui se sont inscrits à Azure Online avec une carte de crédit.

Dans le portail Azure, vous pouvez remplacer votre mode de paiement par défaut par une nouvelle carte de crédit et mettre à jour les informations de votre carte de crédit. Vous devez être [Administrateur de compte](../understand/subscription-transfer.md#whoisaa) ou disposer des [autorisations MCA](understand-mca-roles.md) appropriées pour effectuer ces modifications. Vous pouvez également remplacer votre carte de crédit actuelle pour tous les abonnements.

Si vous voulez supprimer une carte de crédit, consultez [Supprimer un mode de paiement pour la facturation Azure](delete-azure-payment-method.md).

Les modes de paiement pris en charge pour Microsoft Azure sont les cartes de crédit et les chèques/virements. Pour obtenir l’approbation du paiement par chèque/virement, consultez [Payer pour les abonnements Azure par facture](pay-by-invoice.md).

Avec un Contrat client Microsoft, vos modes de paiement sont associés à des profils de facturation. Découvrez comment [vérifier l’accès à un Contrat client Microsoft](#check-the-type-of-your-account). Si vous disposez d’un Contrat client Microsoft, allez directement à [Gérer les cartes de crédit d’un Contrat client Microsoft](#manage-credit-cards-for-a-microsoft-customer-agreement).

>[!NOTE]
> Lorsque vous créez un abonnement, vous pouvez spécifier une nouvelle carte de crédit. Sachez que les autres abonnements ne seront alors pas automatiquement associés à cette nouvelle carte de crédit. En revanche, si vous effectuez plus tard l’un des changements suivants, *tous les abonnements* utiliseront le mode de paiement choisi.
  >- Activer un mode de paiement à l’aide de l’option **Définir comme actif**
  >- Utiliser l’option de paiement **Remplacer** pour l’un des abonnements
  >- Changer le mode de paiement par défaut

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Gérer les cartes de crédit pour un abonnement Azure

Les sections suivantes s’appliquent aux clients qui disposent d’un compte de facturation du Programme Microsoft Online Services. Découvrez comment [vérifier votre type de compte de facturation](#check-the-type-of-your-account). Si votre type de compte de facturation est Programme Microsoft Online Services, les modes de paiement sont associés à des abonnements Azure individuels. Si vous obtenez une erreur après avoir ajouté la carte bancaire, consultez [Carte bancaire est refusée lors de l’inscription à Azure](./troubleshoot-declined-card.md).

### <a name="change-credit-card-for-all-subscriptions-by-adding-a-new-credit-card"></a>Changer la carte de crédit pour tous les abonnements en ajoutant une nouvelle carte de crédit

Vous pouvez remplacer la carte de crédit par défaut de votre abonnement Azure par une nouvelle carte de crédit ou une carte de crédit déjà enregistrée dans le portail Azure. Vous devez être l’administrateur du compte pour changer de carte de crédit. 

Si plusieurs abonnements ont le même mode de paiement actif, le changement du mode de paiement par défaut pour un abonnement a aussi pour effet de changer le mode de paiement actif pour tous les autres abonnements.

Vous pouvez remplacer la carte de crédit par défaut de votre abonnement par une nouvelle en effectuant les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.
1. Recherchez **Gestion des coûts + facturation**.  
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="Capture d’écran illustrant la recherche." lightbox="./media/change-credit-card/search.png" :::
1. Sélectionnez l’abonnement auquel vous voulez ajouter la carte de crédit.
1. Sélectionnez **Modes de paiement**.  
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="Capture d’écran montrant l’option Gérer les modes de paiement sélectionnée." lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. En haut à gauche, sélectionnez **+ Ajouter** pour ajouter une carte. Un formulaire de carte de crédit s’affiche à droite.
1. Entrez les détails de la carte de crédit.  
    :::image type="content" source="./media/change-credit-card/sub-add-new-default.png" alt-text="Capture d’écran montrant l’ajout d’une nouvelle carte." lightbox="./media/change-credit-card/sub-add-new-default.png" :::
1. Pour activer cette carte comme mode de paiement par défaut, sélectionnez **Définir comme mon mode de paiement par défaut** au-dessus du formulaire. Cette carte devient le mode de paiement actif pour tous les abonnements utilisant la même carte que l’abonnement sélectionné.
1. Sélectionnez **Suivant**.

### <a name="replace-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Remplacer la carte de crédit pour un abonnement par une carte de crédit précédemment enregistrée

Vous pouvez aussi remplacer la carte de crédit par défaut d’un abonnement par une autre carte déjà enregistrée sur votre compte en effectuant les étapes suivantes. Cette procédure change la carte de crédit pour tous les autres abonnements.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.
1. Recherchez **Gestion des coûts + facturation**.  
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="Capture d’écran montrant une recherche sur Gestion des coûts + facturation." lightbox="./media/change-credit-card/search.png" :::
1. Sélectionnez l’abonnement auquel vous voulez ajouter la carte de crédit.
1. Sélectionnez **Modes de paiement**.
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="Capture d’écran montrant l’option Gérer les modes de paiement." lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. Sélectionnez **Remplacer** pour changer la carte de crédit actuelle avec celle que vous avez sélectionnée.
    :::image type="content" source="./media/change-credit-card/replace-credit-card.png" alt-text="Capture d’écran montrant l’option Remplacer." lightbox="./media/change-credit-card/replace-credit-card.png" :::
1. Dans la zone **Remplacer le mode de paiement par défaut**, sélectionnez une autre carte de crédit par laquelle remplacer la carte de crédit par défaut, puis sélectionnez **Suivant**.
    :::image type="content" source="./media/change-credit-card/replace-default-payment-method.png" alt-text="Capture d’écran montrant la zone Remplacer le mode de paiement par défaut." lightbox="./media/change-credit-card/replace-default-payment-method.png" :::
1. Après quelques instants, vous voyez un message confirmant que votre mode de paiement a été changé.

### <a name="edit-credit-card-details"></a>Entrer les détails de la carte de crédit

Si le numéro de votre carte bancaire reste le même en cas de renouvellement, mettez à jour les détails de la carte bancaire existante, tels que la date d’expiration. Si votre numéro de carte bancaire change, en cas de perte, de vol ou d’expiration, procédez comme indiqué dans la section [Ajouter une nouvelle carte bancaire à votre compte](#addcard). Vous n’avez pas besoin de mettre à jour le cryptogramme visuel.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur de compte.
1. Recherchez **Gestion des coûts + facturation**.
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="Capture d’écran de la recherche." lightbox="./media/change-credit-card/search.png" :::
1. Sélectionnez **Modes de paiement**.
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="Capture d’écran montrant Gérer les modes de paiement" lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. Sélectionnez la carte de crédit que vous souhaitez modifier. Un formulaire de carte de crédit s’affiche à droite.
    :::image type="content" source="./media/change-credit-card/edit-card-x.png" alt-text="Capture d’écran montrant Modifier le mode de paiement." lightbox="./media/change-credit-card/edit-card-x.png" :::
1. Mettez à jour les détails de la carte de crédit.
1. Sélectionnez **Suivant**.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Gérer les cartes de crédit pour un Contrat client Microsoft

Les sections suivantes s’appliquent aux clients qui disposent d’un Contrat client Microsoft et qui sont inscrits à Azure Online avec une carte de crédit, et à ceux qui disposent des [autorisations MCA](understand-mca-roles.md) appropriées. [Découvrez comment vérifier si vous avez un Contrat client Microsoft](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Changer la carte de crédit par défaut

Si vous avez un Contrat client Microsoft, votre carte de crédit est associée à un profil de facturation. Pour changer le mode de paiement d’un profil de facturation, vous devez être la personne qui s’est inscrite à Azure et qui a créé le compte de facturation, ou vous devez disposer des [autorisations MCA](understand-mca-roles.md) appropriées.

Si vous voulez changer le mode de paiement par défaut de votre profil de facturation en chèque/virement, consultez [Payer pour les abonnements Azure par facture](pay-by-invoice.md).

Pour changer votre carte de crédit, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Effectuez une recherche sur **Gestion des coûts + facturation**.
1. Dans le menu de gauche, sélectionnez **Profils de facturation**.
1. Sélectionnez un profil de facturation.
1. Dans le menu de gauche, sélectionnez **Méthodes de paiement**.  
    :::image type="content" source="./media/change-credit-card/payment-methods-tab-mca.png" alt-text="Capture d’écran montrant les modes de paiement dans le menu." lightbox="./media/change-credit-card/payment-methods-tab-mca.png" :::
1. Dans la section **Mode de paiement par défaut**, sélectionnez **Remplacer**.  
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="Capture d’écran montrant Remplacer." lightbox="./media/change-credit-card/change-payment-method-mca.png" :::
1. Dans la nouvelle zone de droite, sélectionnez une carte existante dans la liste déroulante ou ajoutez-en une en sélectionnant le lien de couleur bleue **Ajouter un nouveau mode de paiement**.

### <a name="edit-a-credit-card"></a>Modifier une carte de crédit

Vous pouvez modifier les informations de la carte de crédit (par exemple mettre à jour la date d’expiration) sur le portail Azure. 

Pour modifier une carte de crédit, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Effectuez une recherche sur **Gestion des coûts + facturation**.
1. Dans le menu de gauche, sélectionnez **Profils de facturation**.
1. Sélectionnez un profil de facturation.
1. Dans le menu de gauche, sélectionnez **Méthodes de paiement**.  
    :::image type="content" source="./media/change-credit-card/payment-methods-tab-mca.png" alt-text="Capture d’écran montrant les modes de paiement dans le menu." lightbox="./media/change-credit-card/payment-methods-tab-mca.png" :::
1. Dans la section **Vos cartes de crédit**, recherchez la carte de crédit que vous voulez modifier.
1. Sélectionnez les points de suspension (`...`) à la fin de la ligne.  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="Capture d’écran montrant les points de suspension." lightbox="./media/change-credit-card/edit-delete-credit-card-mca.png" :::
1. Pour modifier les informations de votre carte de crédit, sélectionnez **Modifier** dans le menu contextuel.

## <a name="troubleshooting"></a>Dépannage

Azure ne prend pas en charge les cartes virtuelles ou prépayées. Si vous obtenez des erreurs au moment d’ajouter ou mettre à jour une carte de crédit valide, essayez d’ouvrir votre navigateur en mode privé.

## <a name="frequently-asked-questions"></a>Forum aux questions

Les sections suivantes répondent à des questions fréquemment posées sur la modification des informations de votre carte de crédit.

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Pourquoi je reçois continuellement le message « Votre ouverture de session a expiré. Cliquez ici pour en ouvrir une nouvelle » ?

Si vous continuez à recevoir ce message d’erreur même après vous être déconnecté et reconnecté, réessayez avec une session de navigation privée.

### <a name="how-do-i-use-a-different-card-for-each-subscription"></a>Comment utiliser une carte différente pour chaque abonnement ?

Comme nous l’avons dit plus haut, quand vous créez un abonnement, vous pouvez spécifier une nouvelle carte de crédit. Sachez que les autres abonnements ne seront alors pas automatiquement associés à cette nouvelle carte de crédit. Vous pouvez ajouter plusieurs nouveaux abonnements, chacun associé à une seule carte de crédit. En revanche, si vous effectuez plus tard l’un des changements suivants, *tous les abonnements* utiliseront le mode de paiement choisi.

- Activer un mode de paiement à l’aide de l’option **Définir comme actif**
- Utiliser l’option de paiement **Remplacer** pour l’un des abonnements
- Changer le mode de paiement par défaut

### <a name="how-do-i-make-payments"></a>Comment payer ?

Si vous configurez une carte de crédit comme mode de paiement, nous débitons automatiquement votre carte à l’issue de chaque période de facturation. Vous n’avez rien à faire.

Si vous [payez par facture](pay-by-invoice.md), envoyez votre paiement à l’endroit indiqué au bas de celle-ci.

### <a name="how-do-i-change-the-tax-id"></a>Comment modifier l’ID de taxe ?

Pour ajouter ou mettre à jour le numéro d’identification du contribuable, mettez à jour votre profil sur le [portail Azure](https://portal.azure.com), puis sélectionnez **Enregistrement de taxe**. Cet ID de taxe est utilisé pour les calculs d’exemption de taxe et figure sur votre facture.

## <a name="check-the-type-of-your-account"></a>Vérifier le type de votre compte

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [réservations Azure](../reservations/save-compute-costs-reservations.md) pour déterminer si elles peuvent vous permettre de réaliser des économies.
- Si vous voulez supprimer une carte de crédit, consultez [Supprimer un mode de paiement pour la facturation Azure](delete-azure-payment-method.md).