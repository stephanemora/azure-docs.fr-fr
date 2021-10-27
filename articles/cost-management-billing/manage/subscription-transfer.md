---
title: Hub de transfert des réservations et des abonnements Azure
description: Cet article explique les prérequis pour le transfert d’abonnements Azure et contient des liens vers d’autres articles fournissant des informations plus détaillées.
author: bandersmsft
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: banders
ms.custom: ''
ms.openlocfilehash: dbe893dc471f055dd5b062e7ce5dcf492dd0f421
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007353"
---
# <a name="azure-subscription-and-reservation-transfer-hub"></a>Hub de transfert des réservations et des abonnements Azure

Cet article décrit les types de transferts pris en charge pour les abonnements Azure et les réservations Azure, désignés par le terme _produits_. Il aide aussi à comprendre les prérequis pour le transfert le produits Azure entre différents contrats de facturation et contient des liens vers d’autres articles fournissant des informations plus détaillées sur des transferts spécifiques. Les produits Azure sont créés sur la base de différents types de contrats Azure et un transfert d’un type de contrat source vers un autre varie selon les types de contrat source et de destination. Les transferts de produits Azure peuvent être automatiques ou manuels, en fonction du type de contrat source et de destination. En cas de transfert manuel, les types de contrat déterminent l’effort manuel à fournir.

> [!NOTE]
> Il existe de nombreux types de produits Azure, mais tous les produits ne peuvent pas être transférés d’un type à un autre. Cet article documente uniquement les transferts de produits pris en charge. Si vous avez besoin d’aide pour une situation qui n’est pas répertoriée dans cet article, vous pouvez créer une [demande de support Azure](https://go.microsoft.com/fwlink/?linkid=2083458).

Cet article vous explique ce que vous devez savoir _avant_ de transférer la propriété de facturation d’un produit Azure vers un autre compte. Vous souhaiterez peut-être transférer la propriété de facturation de votre produit Azure si vous quittez votre organisation ou si vous voulez que votre abonnement soit facturé sur un autre compte. Le transfert de la propriété de la facturation à un autre compte fournit aux administrateurs du nouveau compte l’autorisation sur les tâches de facturation. Ils peuvent changer le mode de paiement, visualiser les coûts facturés et annuler le produit.

Si vous souhaitez conserver la propriété de facturation mais changer le type de produit, consultez [Changer d’offre pour votre abonnement Azure](switch-azure-offer.md). Pour contrôler qui peut accéder aux ressources du produit, consultez [Rôles intégrés Azure](../../role-based-access-control/built-in-roles.md).

Si vous êtes un client disposant d’un Contrat Entreprise (EA), les administrateurs de votre entreprise peuvent transférer la propriété de facturation de vos produits entre des comptes dans le portail EA. Pour plus d’informations, consultez [Modifier l’abonnement Azure ou la propriété du compte](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

Cet article est consacré aux transferts de produits. Toutefois, il aborde également le transfert de ressources, car ce dernier est requis pour certains scénarios de transfert de produits.

Pour plus d’informations sur les transferts de produits entre différents locataires Azure AD, consultez [Transférer un abonnement Azure vers un autre répertoire Azure AD](../../role-based-access-control/transfer-subscription.md).

> [!NOTE]
> La plupart des transferts de propriété de facturation ne modifient pas le locataire de service des produits sous-jacents. Ils n’entraînent pas de temps d’arrêt. Toutefois, même en cas de changement d’un locataire de facturation, cela n’affecte pas les services ou ressources en cours d’exécution.

## <a name="product-transfer-planning"></a>Planification du transfert de produits

Lorsque vous commencez à planifier un transfert de produits, tenez compte des informations nécessaires pour répondre aux questions suivantes :

- Pourquoi le transfert de produits est-il nécessaire ?
- Quelle est la chronologie souhaitée pour le transfert des produits ?
- Quel est le type d’offre actuel du produit et vers quel type souhaitez-vous le transférer ?
  - Microsoft Online Subscription Program (MOSP), également appelé Paiement à l’utilisation (PAYG)
  - Offre Azure précédente dans le programme CSP
  - Nouvelle offre Azure dans le programme CSP, également appelée Plan Azure avec un Contrat Partenaire Microsoft (MPA)
  - Contrat Entreprise (EA)
  - Contrat client Microsoft (MCA) de type Entreprise quand vous achetez des services Azure par le biais d’un représentant Microsoft et MCA individuel quand vous achetez des services Azure via Azure.com
  - Autres comme MSDN, BizSpark, EOPEN, Pass Azure et Essai gratuit
- Disposez-vous des autorisations nécessaires sur le produit pour effectuer un transfert ? Les autorisations spécifiques nécessaires pour chaque type de transfert sont répertoriées dans le tableau suivant relatif à la prise en charge des transferts de produits.
  - Seul l’administrateur de facturation d’un compte peut transférer la propriété d’un abonnement.
  - Seul le propriétaire administrateur de facturation peut transférer la propriété d’une réservation.
- Parmi les abonnements existants, y en-a-t-il qui bénéficient de réservations ? Les réservations devront-elles être transférées avec l’abonnement ?

Avant de poursuivre le transfert, vous devez pouvoir répondre à chacune des questions.

Les réponses aux questions ci-dessus peuvent vous aider à communiquer rapidement avec d’autres afin de définir les attentes et la chronologie. L’effort de transfert d’un produit varie considérablement, mais il est probable qu’un transfert dure plus longtemps que prévu.

Les réponses aux questions relatives au type d’offre source et de destination permettent de définir les procédures techniques que vous devrez suivre et d’identifier les limites que peut présenter une combinaison de transferts. Les limites sont abordées plus en détail dans la section suivante.

## <a name="product-transfer-support"></a>Prise en charge du transfert de produits

Le tableau suivant décrit la prise en charge du transfert de produits entre les différents types de contrats. Des liens sont fournis pour un complément d’informations sur chaque type de transfert.

Actuellement, le transfert n’est pas pris en charge pour les produits [Essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p/) ou [Azure en licence Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). Pour obtenir une solution de contournement, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Les plans de support ne sont pas transférables. Si vous avez un plan de support, vous devez l’annuler pour l’ancien contrat, puis en acheter un nouveau pour le nouveau contrat.

Les produits Dev/Test ne sont pas répertoriés dans le tableau suivant. Les transferts des produits Dev/Test sont gérés de la même manière que les autres types de produits. Par exemple, un transfert de produits Dev/Test EA est géré comme un transfert de produits EA.

> [!NOTE]
> Les réservations sont transférées avec la plupart des transferts de produits pris en charge. Toutefois, il existe des transferts pour lesquels les réservations ne sont pas transférées, comme indiqué dans le tableau suivant.

| Type de contrat de produit source (actuel) | Type de contrat de produit de destination (futur) | Notes |
| --- | --- | --- |
| EA | MOSP (PAYG) | <ul><li>Le transfert d’une inscription EA vers un abonnement MOSP nécessite un [ticket de support de facturation](https://azure.microsoft.com/support/create-ticket/). <li> Les réservations ne sont pas automatiquement transférées et leur transfert n’est pas pris en charge. |
| EA | MCA - individuel | <ul><li>Pour plus d’informations, consultez [Transférer la propriété de facturation d’un abonnement Azure pour un Contrat client Microsoft](mca-request-billing-ownership.md). <li> Les réservations ne sont pas automatiquement transférées. Vous devez donc ouvrir un [ticket de support de facturation](https://azure.microsoft.com/support/create-ticket/) pour les transférer. |
| EA | EA | <ul><li>Le transfert entre les inscriptions EA nécessite un [ticket de support de facturation](https://azure.microsoft.com/support/create-ticket/). <li>Les réservations ne sont pas automatiquement transférées entre les inscriptions. Vous devez donc ouvrir un [ticket de support de facturation](https://azure.microsoft.com/support/create-ticket/) pour les transférer. <li> Le transfert au sein d’une même inscription revient à modifier le propriétaire du compte. Pour plus d’informations, consultez [Modifier la propriété d’un compte ou d’un abonnement EA](ea-portal-administration.md#change-azure-subscription-or-account-ownership). |
| EA | MCA - Entreprise | <ul><li> Le transfert de tous les produits d’inscription s’effectue dans le cadre du processus de transition MCA d’un contrat Entreprise. Pour plus d’informations, consultez [Effectuer des tâches Contrat Entreprise dans votre compte de facturation pour un Contrat client Microsoft](mca-enterprise-operations.md). <li> Si vous souhaitez transférer des produits spécifiques, et non tous les produits d’une inscription, consultez [Transférer la propriété de facturation d’un abonnement Azure pour un Contrat client Microsoft](mca-request-billing-ownership.md). |
| EA | MPA | <ul><li> Seuls les partenaires CSP à facturation directe certifiés [Fournisseur de services managés Azure Expert (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) peuvent demander le transfert de produits Azure pour leurs clients ayant conclu un contrat Entreprise (EA) direct. Pour plus d’informations, consultez [Obtenir la propriété de facturation d’abonnements Azure sur votre compte MPA](mpa-request-ownership.md). Les transferts de produits ne sont autorisés que pour les clients qui ont accepté un Contrat client Microsoft (MCA) et acheté un plan Azure avec le programme CSP. <li> Il existe des limitations et des restrictions. Pour plus d’informations, consultez [Transférer des abonnements EA à un partenaire CSP](transfer-subscriptions-subscribers-csp.md#transfer-ea-subscriptions-to-a-csp-partner).  |
| MCA - individuel | MOSP (PAYG) | <ul><li> Pour plus d’informations, consultez [Transférer la propriété de facturation d’un abonnement Azure à un autre compte](billing-subscription-transfer.md). <li> Les réservations ne sont pas automatiquement transférées et leur transfert n’est pas pris en charge. |
| MCA - individuel | MCA - individuel | <ul><li>Pour plus d’informations, consultez [Transférer la propriété de facturation d’un abonnement Azure pour un Contrat client Microsoft](mca-request-billing-ownership.md). <li> Les transferts de réservation en libre-service sont pris en charge. |
| MCA - individuel | EA | <ul><li> Pour plus d’informations, consultez [Transférer un abonnement à un EA](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea). <li> Les transferts de réservation en libre-service sont pris en charge. |
| MCA - individuel | MCA - Entreprise | <ul><li> Pour plus d’informations, consultez [Transférer la propriété de facturation d’un abonnement Azure pour un Contrat client Microsoft](mca-request-billing-ownership.md).<li> Les transferts de réservation en libre-service sont pris en charge. |
| MCA - Entreprise | MOSP | <ul><li> Exige un [ticket de support de facturation](https://azure.microsoft.com/support/create-ticket/). <li> Les réservations ne sont pas automatiquement transférées et leur transfert n’est pas pris en charge. |
| MCA - Entreprise | MCA - individuel | <ul><li> Pour plus d’informations, consultez [Transférer la propriété de facturation d’un abonnement Azure pour un Contrat client Microsoft](mca-request-billing-ownership.md). <li> Les réservations ne sont pas automatiquement transférées. Vous devez donc ouvrir un [ticket de support de facturation](https://azure.microsoft.com/support/create-ticket/) pour les transférer. |
| MCA - Entreprise | MCA - Entreprise | <ul><li> Pour plus d’informations, consultez [Transférer la propriété de facturation d’un abonnement Azure pour un Contrat client Microsoft](mca-request-billing-ownership.md). <li> Les réservations ne sont pas automatiquement transférées. Vous devez donc ouvrir un [ticket de support de facturation](https://azure.microsoft.com/support/create-ticket/) pour les transférer.  |
| Offre Azure précédente dans le programme CSP | Offre Azure précédente dans le programme CSP | <ul><li> Exige un [ticket de support de facturation](https://azure.microsoft.com/support/create-ticket/). <li> Les réservations ne sont pas automatiquement transférées et leur transfert n’est pas pris en charge. |
| Offre Azure précédente dans le programme CSP | MPA | Pour plus d’informations, consultez [Transférer les abonnements Azure d’un client vers un autre CSP (dans le cadre d’un plan Azure)](/partner-center/transfer-azure-subscriptions-under-azure-plan). |
| MPA | EA | <ul><li> Le transfert automatique n’est pas pris en charge. Tout transfert nécessite le déplacement manuel des ressources du produit MPA existant vers un produit EA existant ou récemment créé. <li> Utilisez les informations figurant dans la section [Effectuer des transferts de ressources](#perform-resource-transfers). <li> Les réservations ne sont pas automatiquement transférées et leur transfert n’est pas pris en charge. |
| MPA | MPA | <ul><li> Pour plus d’informations, consultez [Transférer les abonnements Azure d’un client vers un autre CSP (dans le cadre d’un plan Azure)](/partner-center/transfer-azure-subscriptions-under-azure-plan). <li> Les réservations ne sont pas automatiquement transférées et leur transfert n’est pas pris en charge. |
| MOSP (PAYG) | MOSP (PAYG) | <ul><li> Si vous modifiez le propriétaire de facturation de l’abonnement, consultez [Transférer la propriété de facturation d’un abonnement Azure vers un autre compte](billing-subscription-transfer.md). <li> Les réservations ne sont pas automatiquement transférées. Vous devez donc ouvrir un [ticket de support de facturation](https://azure.microsoft.com/support/create-ticket/) pour les transférer.  |
| MOSP (PAYG) | MCA - individuel | <ul><li> Pour plus d’informations, consultez [Transférer la propriété de facturation d’un abonnement Azure pour un Contrat client Microsoft](mca-request-billing-ownership.md). <li> Les transferts de réservation en libre-service sont pris en charge. |
| MOSP (PAYG) | EA | Pour plus d’informations, consultez [Transférer un abonnement à un EA](mosp-ea-transfer.md#transfer-the-subscription-to-the-ea). |
| MOSP (PAYG) | MCA - Entreprise | <ul><li> Pour plus d’informations, consultez [Transférer la propriété de facturation d’un abonnement Azure pour un Contrat client Microsoft](mca-request-billing-ownership.md). <li> Les transferts de réservation en libre-service sont pris en charge. |

## <a name="perform-resource-transfers"></a>Effectuer des transferts de ressources

Certains transferts de produits exigent que vous déplaciez manuellement des ressources Azure entre des abonnements. Le déplacement des ressources peut entraîner des temps d’arrêt et il existe des limites au déplacement de certains types de ressources Azure comme les machines virtuelles, les NSG, App Services, etc.

Microsoft ne fournit pas d’outil permettant de déplacer automatiquement les ressources entre les abonnements. Si nécessaire, vous devez déplacer manuellement les ressources Azure entre les abonnements. Pour plus d’informations, consultez [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Si le nombre de ressources à déplacer est important, il faudra plus de temps et de planification.

## <a name="other-planning-considerations"></a>Autres considérations en matière de planification

Lisez les sections suivantes pour en savoir plus sur les autres éléments à considérer avant de démarrer un transfert de produits.

### <a name="resources-transfer-with-subscriptions"></a>Transfert de ressources avec des abonnements

Si seule la propriété de facturation change, les ressources ne sont pas affectées. Toutefois, lorsque vous effectuez un déplacement de ressources ou modifiez le locataire du service, les ressources peuvent être affectées.

### <a name="transfer-a-product-from-one-account-to-another"></a>Transférer un produit d’un compte à un autre

Si vous êtes administrateur de deux comptes, vous pouvez transférer un produit entre vos comptes. Vos comptes étant conceptuellement considérés comme les comptes de deux utilisateurs différents, vous pouvez transférer des produits entre eux.

Pour connaître la procédure à suivre pour transférer votre produit, consultez [Transférer la propriété de facturation d’un abonnement Azure](billing-subscription-transfer.md).

### <a name="transferring-a-product-shouldnt-create-downtime"></a>Le transfert d’un produit ne devrait pas entraîner de temps d’arrêt

Si vous transférez un produit vers un compte dans le même locataire Azure AD, les ressources en cours d’exécution dans l’abonnement ne seront pas affectées. Cependant, les informations de contexte enregistrées dans PowerShell ne sont pas mises à jour : il peut donc être nécessaire de les effacer ou de changer des paramètres. Lorsque vous déplacez des ressources ou modifiez le locataire du service, les ressources peuvent être affectées.

### <a name="new-account-usage-and-billing-history"></a>Utilisation de nouveau compte et historique de facturation

Les seules informations disponibles pour les utilisateurs du nouveau compte sont l’historique d’utilisation et de facturation à partir du moment du transfert. L’historique d’utilisation et de facturation n’est pas transféré avec le produit.

### <a name="remaining-product-credits"></a>Crédits de produits restants

Si vous avez un produit Visual Studio ou Microsoft Partner Network, vous bénéficiez de crédits mensuels. Votre crédit n’est pas reporté avec le produit dans le nouveau compte. L’utilisateur qui a accepté la demande de transfert doit avoir une licence Visual Studio pour procéder à l’acceptation. Le produit utilise le crédit Visual Studio disponible dans le compte de l’utilisateur. Pour plus d’informations, consultez [Transfert des abonnements Visual Studio et Partner Network](billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions).

### <a name="users-keep-access-to-transferred-resources"></a>Les utilisateurs conservent l’accès aux ressources transférées

Gardez à l’esprit que les utilisateurs ayant accès aux ressources d’un produit conservent cet accès lors du transfert de la propriété de facturation. Cependant, les [rôles d’administrateur](add-change-subscription-administrator.md) et les [attributions de rôles Azure](../../role-based-access-control/role-assignments-portal.md) peuvent être supprimées. L’accès est perdu quand votre compte se trouve dans un locataire Azure AD autre que le locataire du produit et que l’utilisateur qui a envoyé la demande de transfert déplace le produit vers le locataire de votre compte.

Vous pouvez afficher les utilisateurs disposant d’attributions de rôle Azure pour accéder aux ressources du produit dans le portail Azure. Accédez à la [page Abonnement du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Choisissez ensuite le produit que vous souhaitez vérifier, puis sélectionnez **Contrôle d’accès (IAM)** dans le volet gauche. Sélectionnez alors **Attributions de rôles** en haut de la page. La page des attributions de rôles liste tous les utilisateurs qui disposent d’un accès au produit.

Même si les [attributions de rôles Azure](../../role-based-access-control/role-assignments-portal.md) sont supprimées pendant le transfert, les utilisateurs du compte du propriétaire d’origine peuvent continuer à avoir accès au produit par le biais d’autres mécanismes de sécurité, notamment :

- Certificats de gestion accordant à l’utilisateur des droits d’administrateur sur les ressources d’abonnement. Pour plus d'informations, consultez la rubrique [Créer et télécharger un certificat de gestion pour Microsoft Azure](../../cloud-services/cloud-services-certs-create.md).
- Touches d’accès rapide pour les services tels que Storage. Pour plus d’informations, consultez la rubrique [À propos des comptes de stockage Azure](../../storage/common/storage-account-create.md).
- Informations d’identification d’accès à distance pour les services tels que les machines virtuelles Azure.

Lorsque le destinataire doit restreindre l’accès aux ressources, il doit envisager la mise à jour des secrets associés au service. La plupart des ressources peuvent être mises à jour. Connectez-vous au [portail Azure](https://portal.azure.com/) puis, dans le menu Hub, sélectionnez **Toutes les ressources**. Ensuite, sélectionnez la ressource. Dans la page de la ressource, sélectionnez **Paramètres**. Ici, vous pouvez afficher et mettre à jour des secrets existants.

### <a name="you-pay-for-usage-when-you-receive-ownership"></a>Vous payez pour l’utilisation quand vous recevez la propriété

Votre compte est responsable du paiement de toute utilisation qui est signalée à compter du moment du transfert. Certaines utilisations peuvent avoir été effectuées avant le transfert mais déclarées ultérieurement. L’utilisation figure sur la facture de votre compte.

### <a name="transfer-enterprise-agreement-product-ownership"></a>Transférer la propriété d’un produit Contrat Entreprise

L’Administrateur d’entreprise peut mettre à jour la propriété de tout compte, même si le propriétaire original du compte ne fait plus partie de l’organisation. Pour plus d’informations sur le transfert de comptes Contrat Entreprise Azure, consultez [Transferts Azure Enterprise](ea-transfers.md).

## <a name="next-steps"></a>Étapes suivantes

- [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
