---
title: Obtenir la propriété de facturation d’abonnements Azure
description: Découvrez comment demander la propriété de facturation des abonnements Azure à partir d’autres utilisateurs.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: f7d6c6cbe5b99cb429b5399df7ba9765c1553901
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75985385"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Obtenir la propriété de facturation des abonnements Azure des autres comptes

Vous pouvez acquérir la propriété d’abonnements Azure si le propriétaire de facturation existant quitte votre organisation ou si vous souhaitez payer les abonnements via votre compte de facturation. L’acquisition de propriété a pour effet de transférer les responsabilités liées à la facturation des abonnements vers votre compte.

Cet article s'applique à un compte de facturation associé à un contrat client Microsoft. [Vérifiez que vous avez accès à un contrat client Microsoft](#check-for-access).

Pour demander la propriété de facturation, vous devez être **propriétaire de section facture** ou **contributeur de section facture**. Pour plus d’informations, voir [Tâches et rôles de la section de facture](understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Demander la propriété de facturation

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant que propriétaire ou contributeur de la section de facture d’un compte de facturation pour un contrat client Microsoft.

2. Recherchez **Gestion des coûts + facturation**.

    ![Capture d’écran montrant une recherche relative à la gestion des coûts et à la facturation sur le portail Microsoft Azure](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Dans la page des étendues de facturation, sélectionnez le compte de facturation, qui sera utilisé pour payer l’utilisation des abonnements. Le compte de facturation doit être de type **Contrat client Microsoft**.

    ![Capture d’écran montrant la recherche dans le portail pour gestion des coûts + facturation](./media/mca-request-billing-ownership/list-of-scopes.png)

    > [!NOTE]
    >
    > Le portail Azure mémorise la dernière étendue de facturation à laquelle vous accédez et affiche l’étendue la prochaine fois que vous revenez à la page Gestion des coûts + facturation. Vous ne verrez pas la page des étendues de facturation si vous avez déjà consulté Gestion des coûts + facturation. Si c’est le cas, vérifiez que vous êtes dans l’[étendue appropriée](#check-for-access). Si ce n’est pas le cas, [changez d’étendue](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) pour sélectionner le compte de facturation d’un Contrat client Microsoft.

4. Sélectionnez **Profils de facturation** sur le côté gauche.

    ![Capture d’écran montrant la sélection de profils de facturation](./media/mca-request-billing-ownership/mca-select-profiles.png)     

    > [!Note]
    >
    > Si vous ne voyez pas les profils de facturation, vous n’êtes pas dans l’étendue de facturation appropriée. Vous devez sélectionner un compte de facturation pour un Contrat client Microsoft, puis les profils de facturation. Pour savoir comment changer d’étendues, consultez [Changer d’étendues de facturation dans le portail Azure](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Sélectionnez un **profil de facturation** dans la liste. Une fois que vous obtenez la propriété des abonnements, leur utilisation sera facturée à ce profil de facturation.

6. Sélectionnez **Sections de facture** situé à gauche.

    ![Capture d’écran montrant comment sélectionner des sections de facture](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)   

7. Sélectionnez une section de facture dans la liste. Une fois que vous obtenez la propriété des abonnements, leur utilisation sera affectée à cette section de la facture du profil de facturation.

8. Sélectionnez **Demandes de transfert** dans la partie inférieure gauche, puis sélectionnez **Ajouter une nouvelle demande**.

    ![Capture d’écran montrant la sélection des demandes de transfert](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)

9. Entrez l’adresse e-mail de l’utilisateur dont vous demandez la propriété de facturation. L’utilisateur doit être administrateur de comptes sur un programme de service en ligne Microsoft ou propriétaire de compte sur un Contrat Entreprise. Pour plus d’informations, voir [Afficher vos comptes de facturation dans le portail Azure](view-all-accounts.md). Sélectionnez **Envoyer la demande de transfert**.

    ![Capture d’écran montrant l’envoi d’une demande de transfert](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)

10. L’utilisateur reçoit un e-mail contenant des instructions pour l’examen de votre demande de transfert.

    ![Capture d’écran de l’e-mail invitant à examiner une demande de transfert](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)

11. Pour approuver la demande de transfert, l’utilisateur sélectionne le lien figurant dans l’e-mail, puis suit les instructions.

    ![Capture d’écran de l’e-mail invitant à examiner une demande de transfert](./media/mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Vérifier l’état d’une demande de transfert

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

    ![Capture d’écran montrant une recherche relative à la gestion des coûts et à la facturation sur le portail Microsoft Azure](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Dans la page des étendues de facturation, sélectionnez le compte de facturation pour lequel la demande de transfert a été envoyée.

4. Sélectionnez **Profils de facturation** sur le côté gauche.

    ![Capture d’écran montrant la sélection de profils de facturation](./media/mca-request-billing-ownership/mca-select-profiles.png)     

5. Sélectionnez le **profil de facturation** pour lequel la demande de transfert a été envoyée.

6. Sélectionnez **Sections de facture** situé à gauche.

    ![Capture d’écran montrant comment sélectionner des sections de facture](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)   

7. Dans la liste, sélectionnez la section de facture pour laquelle la demande de transfert a été envoyée.

8. Sélectionnez **Demandes de transfert** dans la partie inférieure gauche. La page Demandes de transfert affiche les informations suivantes :

    ![Capture d’écran montrant la liste des demandes de transfert](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Colonne|Définition|
   |---------|---------|
   |Date de la demande|Date à laquelle la demande de transfert a été envoyée|
   |Recipient|Adresse e-mail de l’utilisateur auquel vous avez envoyé la demande de transfert de la propriété de facturation|
   |Date d'expiration|Date d’expiration de la demande|
   |Statut|Statut de la demande de transfert|

    La demande de transfert peut avoir l’un des statuts suivants :

   |Statut|Définition|
   |---------|---------|
   |En cours|L’utilisateur n’a pas accepté la demande de transfert.|
   |Traitement en cours|L’utilisateur a approuvé la demande de transfert. La facturation des abonnements que l’utilisateur a sélectionnés est en cours de transfert vers votre section de facture.|
   |Completed| La facturation des abonnements que l’utilisateur a sélectionnés a été transférée vers votre section de facture.|
   |Transfert terminé avec des erreurs|Le traitement de la demande est terminé, mais la facturation de certains abonnements que l’utilisateur a sélectionnés n’a pas pu être transférée.|
   |Expiré|L’utilisateur n’ayant pas accepté la demande à temps, celle-ci a expiré.|
   |Opération annulée|Une personne ayant accès à la demande de transfert a annulé celle-ci.|
   |Refusée|L’utilisateur a refusé la demande de transfert.|

9. Sélectionnez une demande de transfert pour en afficher les détails. La page des détails du transfert affiche les informations suivantes :

    ![Capture d’écran qui montre la liste des abonnements transférés](./media/mca-request-billing-ownership/mca-transfer-completed.png)

   |Colonne  |Définition|
   |---------|---------|
   |ID de la demande de transfert|ID unique de votre demande de transfert. Si vous envoyez une demande de support, partagez l’ID avec le support Azure pour accélérer le traitement de votre demande|
   |Transfert demandé le|Date à laquelle la demande de transfert a été envoyée|
   |Transfert demandé par|Adresse e-mail de l’utilisateur qui a envoyé la demande de transfert|
   |La requête de transfert expire le| Date d’expiration de la demande de transfert|
   |Adresse e-mail du destinataire|Adresse e-mail de l’utilisateur auquel vous avez envoyé la demande de transfert de la propriété de facturation|
   |Lien du transfert envoyé au destinataire|URL envoyée à l’utilisateur pour l’examen de la demande de transfert|

## <a name="supported-subscription-types"></a>Types d’abonnements pris en charge

Vous pouvez demander à obtenir la propriété de facturation des types d’abonnement répertoriés ci-dessous.

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Azure en licence Open](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Pass Azure Sponsorship](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Offre Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Offre sponsorisée Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Contrat Entreprise Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Plateformes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Abonnés Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Abonnés Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Abonnés Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Abonnés Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Tous les crédits disponibles sur l’abonnement ne seront pas disponibles dans le nouveau compte après le transfert.

\*\* Uniquement pris en charge pour les abonnements des comptes créés lors de l’inscription sur le site web Azure.


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

L’accès pour les utilisateurs, groupes ou principaux de service existants qui ont été affectés avec (Azure RBAC (contrôle d’accès en fonction du rôle))[../role-based-access-control/overview.md] n’est pas affecté lors de la transition.

### <a name="azure-support-plan"></a>Plan de support Azure

Le support Azure n’est pas transféré avec les abonnements. Si l’utilisateur transfère tous les abonnements Azure, demandez-leur d’annuler leur plan de support.

### <a name="charges-for-transferred-subscription"></a>Frais de transfert d’abonnement

Le propriétaire de facturation d’origine des abonnements est responsable de tous les frais déclarés jusqu’à ce que le transfert soit terminé. Votre section de facture est responsable des frais déclarés à partir de l’heure de transfert. Certains frais peuvent avoir été exposés avant le transfert mais déclarés ultérieurement. Ces frais apparaissent dans votre section de facture.

### <a name="cancel-a-transfer-request"></a>Annuler une demande de transfert

Vous pouvez annuler une demande de transfert jusqu’à ce qu’elle soit approuvée ou refusée. Pour annuler une demande de transfert, accédez à la [page des détails du transfert](#check-the-transfer-request-status) et sélectionnez Annuler en bas de celle-ci.

### <a name="software-as-a-service-saas-transfer"></a>Transfert SaaS (Software as a Service)

Les produits SaaS ne sont pas transférés avec les abonnements. Demandez à l’utilisateur de [contacter le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour transférer la propriété de facturation de produits SaaS. En même temps que la propriété de facturation, l’utilisateur peut transférer la propriété de la ressource. La propriété de ressource vous permet d’effectuer des opérations de gestion telles que la suppression et l’affichage des détails du produit. Pour transférer la propriété d’une ressource, l’utilisateur doit en être le propriétaire sur le produit SaaS.

## <a name="check-for-access"></a>Vérification de l’accès
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- La propriété de facturation des abonnements Azure est transférée vers votre section de facture. Suivez les frais liés à ces abonnements dans le [Portail Microsoft Azure](https://portal.azure.com).
- Autorisez d’autres personnes à afficher et à gérer la facturation de ces abonnements. Pour plus d’informations, voir [Tâches et rôles de la section de facture](understand-mca-roles.md#invoice-section-roles-and-tasks).
