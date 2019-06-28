---
title: Obtenir la propriété de facturation d’abonnements Azure | Microsoft Docs
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371926"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>Obtenir la propriété de facturation des abonnements Azure des autres utilisateurs

L’acquisition de la propriété des abonnements Azure est indiquée si le propriétaire de facturation existant quitte votre organisation, ou si vous souhaitez payer les abonnements via votre compte de facturation.

Vous pouvez envoyer une demande d’acquisition de propriété d’abonnements Azure à des propriétaires existants dans d’autres comptes de facturation. L’acquisition de propriété a pour effet de transférer les responsabilités liées à la facturation des abonnements vers votre section de facture.

Pour demander la propriété de facturation, vous devez être **propriétaire de section facture** ou **contributeur de section facture**. Pour plus d’informations, voir [Tâches et rôles de la section de facture](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Cet article s’applique à un compte de facturation associé à un Contrat client Microsoft. [Vérifiez si vous avez accès à un Contrat client Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="request-billing-ownership-in-the-azure-portal"></a>Demander la propriété de facturation dans le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant une recherche relative à la gestion des coûts et à la facturation sur le portail Azure](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Accédez à la section de facture. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation ou un profil de facturation. Dans le profil ou compte de facturation, sélectionnez **Sections de facture**, puis une section de facture.
   <!-- Todo - Add a screenshot -->

4. Sélectionnez **Demandes de transfert** dans la partie inférieure gauche.

5. En haut de la page, sélectionnez **Ajouter**.

6. Entrez l’adresse e-mail de l’utilisateur dont vous demandez la propriété de facturation. L’utilisateur doit être administrateur de compte sur un compte de facturation du Programme Microsoft Online Services ou propriétaire de compte sur un Contrat Entreprise. Pour plus d’informations, voir [Afficher vos comptes de facturation dans le portail Azure](billing-view-all-accounts.md).

   ![Capture d’écran illustrant l’ajout d’une nouvelle demande de transfert](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. Sélectionnez **Envoyer la demande de transfert**.

8. L’utilisateur reçoit un e-mail contenant des instructions pour l’examen de votre demande de transfert.

   ![Capture d’écran de l’e-mail invitant à examiner une demande de transfert](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. Pour approuver la demande de transfert, l’utilisateur sélectionne le lien figurant dans l’e-mail, puis suit les instructions.

    ![Capture d’écran de l’e-mail invitant à examiner une demande de transfert](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>Vérifier l’état de votre demande de transfert dans le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant une recherche relative à la gestion des coûts et à la facturation sur le portail Azure](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Accédez à la section de facture. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation ou un profil de facturation. Dans le profil ou compte de facturation, sélectionnez **Sections de facture**, puis une section de facture.
   <!-- Todo - Add a screenshot -->

4. Sélectionnez **Demandes de transfert** dans la partie inférieure gauche.

5. La page Demandes de transfert affiche les informations suivantes :

    ![Capture d’écran montrant la liste des demandes de transfert](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |Colonne|Définition|
   |---------|---------|
   |Date de la demande|Date à laquelle la demande de transfert a été envoyée|
   |Destinataire|Adresse e-mail de l’utilisateur auquel vous avez envoyé la demande de transfert de la propriété de facturation|
   |Date d'expiration|Date d’expiration de la demande|
   |Statut|Statut de la demande de transfert|

    La demande de transfert peut avoir l’un des statuts suivants :

   |Statut|Définition|
   |---------|---------|
   |En cours|L’utilisateur n’a pas accepté la demande de transfert.|
   |Traitement en cours|L’utilisateur a approuvé la demande de transfert. La facturation des abonnements que l’utilisateur a sélectionnés est en cours de transfert vers votre section de facture.|
   |Terminé| La facturation des abonnements que l’utilisateur a sélectionnés a été transférée vers votre section de facture.|
   |Transfert terminé avec des erreurs|Le traitement de la demande est terminé, mais la facturation de certains abonnements que l’utilisateur a sélectionnés n’a pas pu être transférée.|
   |Expiré|L’utilisateur n’ayant pas accepté la demande à temps, celle-ci a expiré.|
   |Annulée|Une personne ayant accès à la demande de transfert a annulé celle-ci.|
   |Refusée|L’utilisateur a refusé la demande de transfert.|

6. Sélectionnez une demande de transfert pour en afficher les détails. La page des détails du transfert affiche les informations suivantes :
   <!-- Todo - Add a screenshot -->

   |Colonne  |Définition|
   |---------|---------|
   |ID de la demande de transfert|ID unique de votre demande de transfert. Si vous envoyez une demande de support, partagez l’ID avec le support Azure pour accélérer le traitement de votre demande.|
   |Transfert demandé le|Date à laquelle la demande de transfert a été envoyée|
   |Transfert demandé par|Adresse e-mail de l’utilisateur qui a envoyé la demande de transfert|
   |La requête de transfert expire le| Date d’expiration de la demande de transfert|
   |Adresse e-mail du destinataire|Adresse e-mail de l’utilisateur auquel vous avez envoyé la demande de transfert de la propriété de facturation|
   |Lien du transfert envoyé au destinataire|URL envoyée à l’utilisateur pour l’examen de la demande de transfert|

## <a name="additional-information"></a>Informations supplémentaires

La section suivante fournit des informations supplémentaires sur le transfert d’abonnements.

### <a name="no-service-downtime"></a>Aucune interruption de service.

Les services Azure compris dans l’abonnement ne subissent aucune interruption. Nous effectuons la transition de la relation de facturation uniquement pour les abonnements Azure que l’utilisateur sélectionne pour le transfert.

### <a name="disabled-subscriptions"></a>Abonnements désactivés

Les abonnements désactivés ne peuvent pas être transférés. Le transfert de propriété de facturation ne peut être effectué que pour des abonnements en état inactif.

### <a name="azure-resources-transfer"></a>Transfert de ressources Azure

Toutes les ressources associées aux abonnements, telles que les machines virtuelles, les disques et les sites web sont transférées.

### <a name="azure-marketplace-products-transfer"></a>Transfert de produits de la Place de marché Azure

Les produits de la Place de marché Azure sont transférés avec leurs abonnements respectifs.

### <a name="azure-reservations-transfer"></a>Transfert de réservations Azure

Les réservations Azure ne suivent pas automatiquement les abonnements. Pour déplacer les réservations, [contactez le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="access-to-azure-services"></a>Accès aux services Azure

L’accès aux ressources Azure défini à l’aide du RBAC (contrôle d’accès en fonction du rôle) Azure n’est pas affecté lors de la transition.

### <a name="azure-support-plan"></a>Plan de support Azure

Le support Azure n’est pas transféré avec les abonnements. Si l’utilisateur transfère tous les abonnements Azure, demandez-leur d’annuler leur plan de support.

### <a name="charges-for-transferred-subscription"></a>Frais de transfert d’abonnement

Le propriétaire de facturation d’origine des abonnements est responsable de tous les frais déclarés jusqu’à ce que le transfert soit terminé. Votre section de facture est responsable des frais déclarés à partir de l’heure de transfert. Certains frais peuvent avoir été exposés avant le transfert mais déclarés ultérieurement. Ces frais apparaissent dans votre section de facture.

### <a name="supported-offers"></a>Offres prises en charge

Les abonnement ou offres de tous types peuvent être transférés, à l’exception des offres CSP.

### <a name="cancel-a-transfer-request"></a>Annuler une demande de transfert

Vous pouvez annuler une demande de transfert jusqu’à ce qu’elle soit approuvée ou refusée. Pour annuler une demande de transfert, accédez à la page des détails du transfert et sélectionnez Annuler en bas de celle-ci.

### <a name="software-as-a-service-saas-transfer"></a>Transfert SaaS (Software as a Service)

Les produits SaaS ne sont pas transférés avec les abonnements. Demandez à l’utilisateur de [contacter le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour transférer la propriété de facturation de produits SaaS. En même temps que la propriété de facturation, l’utilisateur peut transférer la propriété de la ressource. La propriété de ressource vous permet d’effectuer des opérations de gestion telles que la suppression et l’affichage des détails du produit. Pour transférer la propriété d’une ressource, l’utilisateur doit en être le propriétaire sur le produit SaaS.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l’accès à un Contrat client Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- La propriété de facturation des abonnements Azure est transférée vers votre section de facture. Suivez les frais liés à ces abonnements dans le [Portail Microsoft Azure](https://portal.azure.com).
- Autorisez d’autres personnes à afficher et à gérer la facturation de ces abonnements. Pour plus d’informations, voir [Tâches et rôles de la section de facture](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
