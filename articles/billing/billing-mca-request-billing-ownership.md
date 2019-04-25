---
title: Obtenir la propriété des abonnements Azure de facturation | Microsoft Docs
description: Découvrez comment demander la propriété de facturation des abonnements Azure à partir d’autres utilisateurs.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: be8c7fcebca224196d9eac7d22387989b1bdfd46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371926"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>Obtenir la propriété des abonnements Azure à partir d’autres utilisateurs de facturation

Vous souhaiterez peut-être prendre possession des abonnements Azure si le propriétaire de facturation existant quitte votre organisation, ou vous souhaitez payer pour les abonnements via votre compte de facturation.

Vous pouvez envoyer une demande de prendre possession des abonnements Azure à partir de propriétaires existants dans d’autres comptes de facturation. Appropriation transfère les responsabilités de facturation des abonnements à votre section de facture.

Pour demander la propriété de facturation, vous devez être un **propriétaire de section facture** ou **contributeur de section facture**. Pour plus d’informations, consultez [facturer les tâches de rôles de section](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Cet article s’applique à un compte de facturation d’un contrat de client de Microsoft. [Vérifiez si vous avez accès à un contrat de client Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="request-billing-ownership-in-the-azure-portal"></a>Demander la propriété de facturation dans le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche du portail Azure pour la gestion des coûts + facturation](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Accédez à la section de facture. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation ou d’un profil de facturation. Dans le compte de facturation ou un profil, sélectionnez **sections de facture** et ensuite une section de la facture.
   <!-- Todo - Add a screenshot -->

4. Sélectionnez **demande de transfert** à partir de la partie inférieure gauche.

5. En haut de la page, sélectionnez **Ajouter**.

6. Entrez l’adresse de messagerie de l’utilisateur que vous demandez à partir de la propriété de facturation. L’utilisateur doit être un administrateur de compte sur un compte de facturation du programme de Service en ligne Microsoft ou un propriétaire de compte sur un contrat entreprise. Pour plus d’informations, consultez [afficher vos comptes de facturation dans le portail Azure](billing-view-all-accounts.md).

   ![Capture d’écran qui illustre l’ajout d’une nouvelle demande de transfert](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. Sélectionnez **envoi de demande de transfert**.

8. L’utilisateur reçoit un e-mail contenant des instructions pour passer en revue votre demande de transfert.

   ![Capture d’écran de ce message de demande de transfert de révision montre](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. Pour approuver la demande de transfert, l’utilisateur sélectionne le lien dans le courrier électronique et suit les instructions.

    ![Capture d’écran de ce message de demande de transfert de révision montre](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>Vérifier l’état de votre demande de transfert dans le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche du portail Azure pour la gestion des coûts + facturation](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Accédez à la section de facture. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation ou d’un profil de facturation. Dans le compte de facturation ou un profil, sélectionnez **sections de facture** et ensuite une section de la facture.
   <!-- Todo - Add a screenshot -->

4. Sélectionnez **demande de transfert** à partir de la partie inférieure gauche.

5. La page demandes de transfert affiche les informations suivantes :

    ![Capture d’écran montrant la liste des demandes de transfert](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |Colonne|Définition|
   |---------|---------|
   |Date de la demande|La date à laquelle la demande de transfert a été envoyée|
   |Recipient|L’adresse de messagerie de l’utilisateur que vous avez envoyé la demande de transfert de la propriété de facturation|
   |Date d'expiration|La date d’expiration de la demande|
   |Statut|L’état de demande de transfert|

    La demande de transfert peut avoir un des statuts suivants :

   |Statut|Définition|
   |---------|---------|
   |En cours|L’utilisateur n’a pas accepté la demande de transfert|
   |Traitement en cours|L’utilisateur approuve la demande de transfert. La facturation des abonnements sélectionné par l’utilisateur est transférée vers votre section de facture|
   |Completed| La facturation des abonnements sélectionné par l’utilisateur est transférée vers votre section de facture|
   |Terminé avec des erreurs|La demande terminée, mais la facturation pour certains abonnements sélectionné par l’utilisateur n’a pas pu être transférée.|
   |Expiré|L’utilisateur n’a pas accepté la demande sur le temps et il a expiré|
   |Canceled|Les personnes ayant accès à la demande de transfert annulé la demande|
   |Refusé|L’utilisateur a refusé la demande de transfert|

6. Sélectionnez une demande de transfert pour afficher les détails. La page de détails de transfert affiche les informations suivantes :
   <!-- Todo - Add a screenshot -->

   |Colonne  |Définition|
   |---------|---------|
   |ID de demande de transfert|ID unique pour votre demande de transfert. Si vous envoyez une demande de support, partager l’ID avec le support Azure pour accélérer votre demande de support|
   |Transfert demandé|La date à laquelle la demande de transfert a été envoyée|
   |Transfert demandé par|L’adresse de messagerie de l’utilisateur qui a envoyé la demande de transfert|
   |Demande de transfert expire le| La date d’expiration de la demande de transfert|
   |Adresse e-mail du destinataire|L’adresse de messagerie de l’utilisateur que vous avez envoyé la demande de transfert de la propriété de facturation|
   |Lien du transfert envoyé au destinataire|L’url qui a été envoyé à l’utilisateur pour passer en revue la demande de transfert|

## <a name="additional-information"></a>Informations supplémentaires

La section suivante fournit des informations supplémentaires sur le transfert des abonnements.

### <a name="no-service-downtime"></a>Aucune interruption de service.

Services Azure dans l’abonnement se poursuivre sans interruption. Transition uniquement la relation de facturation pour les abonnements Azure auxquels l’utilisateur sélectionne à transférer.

### <a name="disabled-subscriptions"></a>Abonnements désactivés

Abonnements désactivés ne peuvent pas être transférés. Abonnements doivent être dans un état actif pour transférer la propriété de leur facturation.

### <a name="azure-resources-transfer"></a>Transfert de ressources Azure

Toutes les ressources à partir d’abonnements telles que les machines virtuelles, les disques et les sites Web sont transférées.

### <a name="azure-marketplace-products-transfer"></a>Transfert de produits de la place de marché Azure

Transférer des produits de la place de marché Azure, ainsi que leurs abonnements respectifs.

### <a name="azure-reservations-transfer"></a>Transfert de réservations Azure

Réservations d’Azure ne se déplacent automatiquement avec les abonnements. [Contactez le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour déplacer des réservations.

### <a name="access-to-azure-services"></a>Accès aux services Azure

Accès aux ressources Azure qui a été défini à l’aide d’Azure RBAC (contrôle d’accès en fonction du rôle) n’est pas affectée lors de la transition.

### <a name="azure-support-plan"></a>Plan de support Azure

Prise en charge Azure ne transfère pas avec les abonnements. Si l’utilisateur transfère tous les abonnements Azure, leur demander d’annuler leur plan de support.

### <a name="charges-for-transferred-subscription"></a>Frais d’abonnement transférée

Le propriétaire d’origine de la facturation des abonnements est responsable de tous les frais qui ont été signalés jusqu’au point que le transfert est terminé. Section de votre facture est responsable des frais signalées à partir de l’heure de transfert et versions ultérieures. Il existe peut-être des frais qui a eu lieu avant le transfert mais déclarées ultérieurement. Ces frais s’affichent dans la section de votre facture.

### <a name="supported-offers"></a>Offres prises en charge

Abonnement de tous les types ou des offres, sauf offre CSP peut être transférée.

### <a name="cancel-a-transfer-request"></a>Annuler une demande de transfert

Vous pouvez annuler la demande de transfert jusqu'à ce que la demande est approuvée ou refusée. Pour annuler la demande de transfert, accédez à la page de détails de transfert et sélectionnez Annuler en bas de la page.

### <a name="software-as-a-service-saas-transfer"></a>Logiciel en tant qu’un transfert de Service (SaaS)

Produits SaaS ne sont pas transférés avec les abonnements. Demander à l’utilisateur [contactez le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour transférer la propriété de facturation de produits SaaS. En même temps que la propriété de facturation, l’utilisateur peut également transférer la propriété de ressource. La propriété de ressource vous permet d’effectuer des opérations de gestion telles que la suppression et affichage des détails du produit. Utilisateur doit être un propriétaire de la ressource sur le produit SaaS pour transférer la propriété de ressource.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifiez l’accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- La propriété de facturation des abonnements Azure est transférée vers votre section de facture. Effectuer le suivi des frais pour ces abonnements dans le [Azure portal](https://portal.azure.com).
- Que d’autres personnes d’autorisations pour afficher et gérer la facturation de ces abonnements. Pour plus d’informations, consultez [section rôles et tâches de facture](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
