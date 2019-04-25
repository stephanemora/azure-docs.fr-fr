---
title: Comprendre la facturation des rôles d’administration pour les contrats de client de Microsoft - Azure
description: En savoir plus sur les rôles de facturation pour la facturation des comptes dans Azure pour les contrats de client de Microsoft.
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 780870cc71e95507a52ba6a9338026f895a96ac1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370888"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Comprendre les rôles d’administrateur de contrat de client de Microsoft dans Azure

Pour gérer votre compte de facturation pour un contrat de client de Microsoft, utilisez les rôles décrits dans les sections suivantes. Ces rôles sont ajoutent les rôles intégrés pour contrôler l’accès aux ressources Azure. Pour plus d’informations, consultez [Rôles intégrés pour les ressources Azure](../role-based-access-control/built-in-roles.md).

Cet article s’applique à un compte de facturation d’un contrat de client de Microsoft. Vérifiez si vous avez accès à un contrat de client de Microsoft.

## <a name="billing-role-definitions"></a>Facturation des définitions de rôles

Le tableau suivant décrit les rôles de facturation vous permet de gérer votre compte de facturation, les profils de facturation et sections de facture.

|Rôle|Description|
|---|---|
|Propriétaire de compte de facturation|Gérer tous les éléments pour le compte de facturation|
|Collaborateur du compte de facturation|Gérer tout sauf les autorisations sur le compte de facturation|
|Lecteur du compte de facturation|Vue en lecture seule de tous les éléments sur le compte de facturation|
|Propriétaire du profil de facturation|Gérer tous les éléments pour le profil de facturation|
|Collaborateur du profil de facturation|Gérer tout sauf les autorisations sur le profil de facturation|
|Lecteur du profil de facturation|Vue en lecture seule de tous les éléments sur le profil de facturation|
|Gestionnaire de facture|Afficher et de payer des factures pour le profil de facturation|
|Propriétaire de section de facture|Gérer tout le contenu de la section de la facture|
|Contributeur de section de facture|Gérer tout sauf les autorisations sur la section de la facture|
|Lecteur de section de facture|Vue en lecture seule de tous les éléments dans la section de la facture|
|Créateur de l'abonnement Azure|Créer des abonnements Azure|

## <a name="billing-account-roles-and-tasks"></a>Tâches et rôles du compte de facturation

Un compte de facturation vous permet de gérer la facturation pour votre organisation. Compte de facturation vous permet d’organiser les coûts, les coûts de surveillance et les factures et de contrôler l’accès de facturation pour votre organisation. Pour plus d’informations, consultez [comprendre le compte de facturation](billing-mca-overview.md#understand-billing-account).

Les tableaux suivants indiquent le rôle que vous avez besoin pour effectuer des tâches dans le contexte du compte de facturation.

### <a name="manage-billing-account-permissions-and-properties"></a>Gérer les propriétés et les autorisations du compte de facturation

|Tâche|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation|
|---|---|---|---|
|Afficher les autorisations existantes pour le compte de facturation|✔|✔|✔|
|D’autres accorder des autorisations pour afficher et gérer le compte de facturation|✔|✘|✘|
|Afficher les propriétés de compte facturation comme nom de la société, adresse et bien plus encore|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Gérer les profils de facturation pour le compte de facturation

|Tâche|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation|
|---|---|---|---|
|Afficher tous les profils de facturation dans le compte|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Gérer les factures pour le compte de facturation

|Tâche|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation|
|---|---|---|---|
|Affichez toutes les factures dans le compte|✔|✔|✔|
|Télécharger les factures, les fichiers d’utilisation et des frais Azure, les tables de tarification et de documents dans le compte de taxe|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Gérer les sections de facture pour le compte de facturation

|Tâche|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation|
|---|---|---|---|
|Affichez toutes les sections de facture dans le compte|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Gérer les transactions de compte de facturation

|Tâche|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation|
|---|---|---|---|
|Afficher toutes les transactions de facturation pour le compte|✔|✔|✔|
|Afficher tous les produits précédemment achetés pour le compte|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Gérer des abonnements pour le compte de facturation

|Tâche|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation|
|---|---|---|---|
|Afficher tous les abonnements Azure dans le compte de facturation|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Tâches et rôles du profil de facturation

Un profil de facturation vous permet de gérer vos factures et les modes de paiement. Une facture mensuelle est générée pour les abonnements Azure et d’autres produits achetés en utilisant le profil de facturation. Les méthodes de paiements vous permet de payer la facture. Pour plus d’informations, consultez [comprendre les profils de facturation](billing-mca-overview.md#understand-billing-profiles).

Les tableaux suivants indiquent le rôle que vous avez besoin pour effectuer des tâches dans le contexte du profil de facturation.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Gérer les stratégies, les propriétés et les autorisations de profil facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher les autorisations existantes pour le profil de facturation|✔|✔|✔|✔|✔|✔|✔|
|D’autres accorder des autorisations pour afficher et gérer le profil de facturation|✔|✘|✘|✘|✘|✘|✘|
|Afficher les propriétés de profil facturation comme nombre bon de commande, de préférence de facture de messagerie et bien plus encore|✔|✔|✔|✔|✔|✔|✔|
|Mettre à jour les propriétés du profil de facturation |✔|✔|✘|✘|✘|✘|✘|
|Afficher les stratégies appliquées sur le profil de facturation comme activer les achats de réservation Azure, activez les achats Azure marketplace et bien plus encore|✔|✔|✔|✔|✔|✔|✔|
|Appliquer des stratégies sur le profil de facturation |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Gérer les factures pour le profil de facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher toutes les factures pour le profil de facturation|✔|✔|✔|✔|✔|✔|✔|
|Télécharger les factures, les fichiers d’utilisation et des frais Azure, les tables de tarification et de documents pour le profil de facturation de taxe|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Gérer les sections de facture pour le profil de facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher toutes les sections de facture pour le profil de facturation|✔|✔|✔|✔|✔|✔|✔|
|Créer une nouvelle section de facture pour le profil de facturation|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Gérer les transactions de profil de facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher toutes les transactions de facturation pour le profil de facturation|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Gérer les modes de paiement pour le profil de facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher les modes de paiement pour le profil de facturation|✔|✔|✔|✔|✔|✔|✔|
|Suivre le solde de crédits Azure pour le profil de facturation|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Gérer des abonnements pour le profil de facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher tous les abonnements Azure pour le profil de facturation|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Tâches et rôles de la section de facture

Une section de la facture vous permet d’organiser les coûts sur votre facture. Vous pouvez créer une section pour organiser vos coûts par département, environnement de développement, ou selon les besoins de votre organisation. Autoriser d’autres utilisateurs à créer des abonnements Azure pour la section. Les frais d’utilisation et les achats pour les abonnements puis les afficher dans la section de la facture. Pour plus d’informations, consultez [section Présentation des factures](billing-mca-overview.md#understand-invoice-sections).

Les tableaux suivants indiquent le rôle que vous avez besoin pour effectuer des tâches dans le contexte des sections de facture.

### <a name="manage-invoice-section-permissions-and-properties"></a>Gérer les autorisations de section de facture et de propriétés

|Tâches|Propriétaire de section de facture|Contributeur de section de facture|Lecteur de section de facture|Créateur de l'abonnement Azure|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation | |
|---|---|---|---|---|---|---|---|---|
|Afficher toutes les autorisations sur la section de la facture|✔|✔|✔|✔|✔|✔|✔| |
|D’autres accorder des autorisations pour afficher et gérer la section de la facture|✔|✘|✘|✘|✘|✘|✘| |
|Afficher les propriétés de section de facture|✔|✔|✔|✔|✔|✔|✔| |
|Mettre à jour les propriétés de la section facture|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Gérer les produits pour la section de la facture

|Tâches|Propriétaire de section de facture|Contributeur de section de facture|Lecteur de section de facture|Créateur de l'abonnement Azure|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher tous les produits achetés dans la section de la facture|✔|✔|✔|✘|✔|✔|✔|
|Gérer la facturation pour les produits pour la section de la facture comme Annuler, de désactiver le renouvellement automatique et bien plus encore|✔|✔|✘|✘|✘|✘|✘|
|Section de facture de modification pour les produits|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Gérer les abonnements pour la section de facture

|Tâches|Propriétaire de section de facture|Contributeur de section de facture|Lecteur de section de facture|Créateur de l'abonnement Azure|Propriétaire de compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher tous les abonnements Azure pour la section de la facture|✔|✔|✔|✘|✔|✔|✔|
|Section de facture de modification pour les abonnements|✔|✔|✘|✘|✘|✘|✘|
|Demander la propriété de facturation des abonnements d’utilisateurs dans d’autres comptes de facturation|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Tâches et rôles de la facturation de l'abonnement

Le tableau suivant présente le rôle que vous avez besoin pour effectuer des tâches dans le contexte d’un abonnement.

|Tâches|Propriétaire de section de facture|Contributeur de section de facture|Lecteur de section de facture|Créateur de l'abonnement Azure|
|---|---|---|---|---|
|Créer des abonnements Azure|✔|✔|✘|✔|
|Centre de coût de mise à jour pour l’abonnement|✔|✔|✘|✘|
|Section de facture de modification pour l’abonnement|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Gérer les rôles de facturation dans le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. Sélectionnez **contrôle d’accès (IAM)** dans une étendue comme compte de facturation, profil de facturation ou section de facture, où vous souhaitez accorder l’accès.

4. La page d’accès (IAM) de contrôle répertorie les utilisateurs et groupes qui sont affectés à chaque rôle pour cette étendue.

   ![Capture d’écran montrant la liste des administrateurs de compte de facturation](./media/billing-understand-mca-roles/billing-list-admins.png)

5. Pour donner accès à un utilisateur, sélectionnez **ajouter** à partir du haut de la page. Dans la liste déroulante de rôle, sélectionnez un rôle. Entrez l’adresse de messagerie de l’utilisateur auquel vous souhaitez accorder l’accès. Sélectionnez **enregistrer** pour affecter le rôle.

   ![Capture d’écran qui illustre l’ajout d’un administrateur à un compte de facturation](./media/billing-understand-mca-roles/billing-add-admin.png)

6. Pour supprimer l’accès pour un utilisateur, sélectionnez l’utilisateur avec l’attribution de rôle que vous souhaitez supprimer. Sélectionnez Supprimer.

   ![Capture d’écran qui montre comment supprimer un administrateur à partir d’un compte de facturation](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifiez l’accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique
Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur votre compte de facturation :

- [Prise en main votre compte de facturation pour un contrat de client de Microsoft](billing-mca-overview.md)
- [Créer un abonnement Azure pour votre compte de facturation pour un contrat de client de Microsoft](billing-mca-create-subscription.md)
