---
title: Rôles de facturation pour des Contrats clients Microsoft - Azure
description: Découvrez-en plus sur les rôles de facturation des comptes de facturation Azure dans le cadre des Contrats client Microsoft.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: e334a423fd11aa3a357d52099a792dcc905aedeb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011661"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Présentation des rôles d'administrateur Azure dans le cadre des Contrats client Microsoft

Pour gérer votre compte de facturation dans le cadre d'un Contrat client Microsoft, utilisez les rôles décrits dans les sections suivantes. Ces rôles s'ajoutent aux rôles intégrés dont Azure doit contrôler l'accès aux ressources. Pour plus d’informations, voir [Rôles intégrés Azure](../../role-based-access-control/built-in-roles.md).

Cet article s'applique à un compte de facturation associé à un contrat client Microsoft. [Vérifiez que vous avez accès à un contrat client Microsoft](#check-access-to-a-microsoft-customer-agreement).

Regardez la vidéo [Gérer l’accès à votre compte de facturation MCA](https://www.youtube.com/watch?v=9sqglBlKkho) pour savoir comment contrôler l’accès à votre compte de facturation Contrat client Microsoft (MCA).

>[!VIDEO https://www.youtube.com/embed/9sqglBlKkho]

## <a name="billing-role-definitions"></a>Définitions des rôles de facturation

Le tableau suivant décrit les rôles de facturation que vous utilisez pour gérer votre compte de facturation, vos profils de facturation et vos sections de facture.

|Role|Description|
|---|---|
|Propriétaire du compte de facturation|Gérer tout le contenu du compte de facturation|
|Collaborateur du compte de facturation|Gérer tout le contenu du compte de facturation, à l'exception des autorisations|
|Lecteur du compte de facturation|Affichage en lecture seule de tout ce qui se trouve sur le compte de facturation|
|Propriétaire du profil de facturation|Gérer tout le contenu du profil de facturation|
|Collaborateur du profil de facturation|Gérer tout le contenu du profil de facturation, à l'exception des autorisations|
|Lecteur du profil de facturation|Affichage en lecture seule de tout ce qui se trouve sur le profil de facturation|
|Gestionnaire de factures|Consulter et payer les factures pour le profil de facturation|
|Propriétaire de section de facture|Gérer tout le contenu de la section de facture|
|Contributeur de section de facture|Gérer tout le contenu de la section de facture, à l'exception des autorisations|
|Lecteur de section de facture|Affichage en lecture seule de tout le contenu de la section de facture|
|Créateur de l'abonnement Azure|Créer des abonnements Azure|

## <a name="billing-account-roles-and-tasks"></a>Rôles et tâches liés au compte de facturation

Un compte de facturation est créé lorsque vous vous inscrivez pour utiliser Azure. Votre compte de facturation vous permet de gérer vos factures et paiements, et d’effectuer un suivi des coûts. Les rôles sur le compte de facturation ont le plus haut niveau d’autorisations, et les utilisateurs qui ont ces rôles peuvent voir les coûts et les informations de facturation pour l’ensemble de votre compte. Attribuez ces rôles uniquement aux utilisateurs qui doivent consulter des factures et suivre les coûts pour tout votre compte, comme les membres des équipes de la finance et de la comptabilité. Pour plus d'informations, consultez [Présentation du compte de facturation](../understand/mca-overview.md#your-billing-account).

Les tableaux suivants indiquent le rôle dont vous avez besoin pour accomplir des tâches dans le contexte du compte de facturation.

### <a name="manage-billing-account-permissions-and-properties"></a>Gérer les autorisations et les propriétés du compte de facturation

|Tâche|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation|
|---|---|---|---|
|Voir les attributions de rôles pour le compte de facturation|✔|✔|✔|
|Autoriser d'autres utilisateurs à consulter et gérer le compte de facturation|✔|✘|✘|
|Voir les propriétés du compte de facturation, comme l’adresse, les contrats, etc.|✔|✔|✔|
|Mettre à jour les propriétés du compte de facturation, comme vendu à, nom d’affichage, etc.|✔|✔|✘|

### <a name="manage-billing-profiles-for-billing-account"></a>Gérer les profils de facturation associés au compte de facturation

|Tâche|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation|
|---|---|---|---|
|Voir tous les profils de facturation pour le compte|✔|✔|✔|
|Créer des profils de facturation supplémentaires|✔|✔|✘|

### <a name="manage-invoices-for-billing-account"></a>Gérer les factures liées au compte de facturation

|Tâche|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation|
|---|---|---|---|
|Voir toutes les factures liées au compte|✔|✔|✔|
|Payer les factures avec une carte de crédit|✔|✔|✘|
|Télécharger les factures, les fichiers d’utilisation d’Azure, les grilles tarifaires et les documents fiscaux|✔|✔|✔|

### <a name="manage-products-for-billing-account"></a>Gérer les produits liés au compte de facturation

|Tâche|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation|
|---|---|---|---|
|Voir tous les produits achetés sur le compte|✔|✔|✔|
|Gérer la facturation des produits (annuler, désactiver le renouvellement automatique, etc.)|✔|✔|✘|

### <a name="manage-subscriptions-for-billing-account"></a>Gérer les abonnements associés au compte de facturation

|Tâche|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation|
|---|---|---|---|
|Voir tous les abonnements Azure créés pour le compte de facturation|✔|✔|✔|
|Créer d’autres abonnements Azure|✔|✔|✘|
|Annuler des abonnements Azure|✘|✘|✘|

## <a name="billing-profile-roles-and-tasks"></a>Rôles et tâches liés au profil de facturation

Chaque compte de facturation est associé à au moins un profil de facturation. Le premier profil de facturation est créé lors de votre inscription pour utiliser Azure. Une facture mensuelle est générée pour le profil de facturation ; elle détaille tous les frais associés du mois précédent. Vous pouvez créer des profils de facturation supplémentaires en fonction de vos besoins. Les utilisateurs ayant des rôles sur un profil de facturation peuvent voir les coûts, définir un budget ainsi que gérer et payer les factures associées. Attribuez ces rôles aux utilisateurs en charge de la gestion du budget et du paiement des factures pour le profil de facturation, comme les membres des équipes d’administration dans votre organisation. Pour plus d'informations, consultez [Présentation des profils de facturation](../understand/mca-overview.md#billing-profiles).

Les tableaux suivants indiquent le rôle dont vous avez besoin pour accomplir des tâches dans le contexte du profil de facturation.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Gérer les autorisations, les propriétés et les stratégies des profils de facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Voir les attributions de rôles pour le profil de facturation|✔|✔|✔|✔|✔|✔|✔|
|Autoriser d'autres utilisateurs à consulter et gérer le profil de facturation|✔|✘|✘|✘|✔|✘|✘|
|Voir les propriétés du profil de facturation, comme le numéro de BDC, facturer à, etc.|✔|✔|✔|✔|✔|✔|✔|
|Mettre à jour les propriétés du profil de facturation |✔|✔|✘|✘|✔|✔|✘|
|Voir les stratégies appliquées au profil de facturation, comme les achats de réservations Azure, les achats sur la Place de marché Azure, etc.|✔|✔|✔|✔|✔|✔|✔|
|Appliquer des stratégies au profil de facturation |✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-invoices-for-billing-profile"></a>Gérer les factures associées au profil de facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher toutes les factures associées au profil de facturation|✔|✔|✔|✔|✔|✔|✔|
|Payer les factures avec une carte de crédit|✔|✔|✘|✔|✔|✘|✘|
|Télécharger les factures, les fichiers d'utilisation et de frais Azure, les grilles tarifaires et les documents fiscaux associés au profil de facturation|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Gérer les sections de facture associées au profil de facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher toutes les sections de facture associées au profil de facturation|✔|✔|✔|✔|✔|✔|✔|
|Créer une nouvelle section de facture pour le profil de facturation|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-billing-profile"></a>Gérer les produits liés au profil de facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Voir tous les produits achetés sur le profil de facturation|✔|✔|✔|✔|✔|✔|✔|
|Gérer la facturation des produits (annuler, désactiver le renouvellement automatique, etc.)|✔|✔|✘|✘|✔|✔|✘|
|Changer le profil de facturation pour les produits|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-payment-methods-for-billing-profile"></a>Gérer les modes de paiement associés au profil de facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher les modes de paiement associés au profil de facturation|✔|✔|✔|✔|✔|✔|✔|
|Gérer les modes de paiement, par exemple, remplacer une carte de crédit, détacher une carte de crédit, etc.|✔|✔|✘|✘|✔|✔|✘|
|Suivre le solde de crédits Azure du profil de facturation|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Gérer les abonnements associés au profil de facturation

|Tâche|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation|Gestionnaire de factures|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation
|---|---|---|---|---|---|---|---|
|Afficher tous les abonnements Azure associés au profil de facturation|✔|✔|✔|✔|✔|✔|✔|
|Créer d’autres abonnements Azure|✔|✔|✘|✘|✔|✔|✘|
|Annuler des abonnements Azure|✘|✘|✘|✘|✘|✘|✘|
|Changer le profil de facturation pour les abonnements Azure|✔|✔|✘|✘|✔|✔|✘|

## <a name="invoice-section-roles-and-tasks"></a>Rôles et tâches liés aux sections de facture

Chaque profil de facturation contient une section de facture par défaut. Vous pouvez créer plus de sections de facture pour regrouper les coûts sur la facture du profil de facturation.  Les utilisateurs ayant des rôles sur une section de facture peuvent contrôler qui est autorisé à créer des abonnements Azure et à effectuer d’autres achats. Attribuez ces rôles aux utilisateurs configurant l’environnement Azure pour les équipes de votre organisation, tels que les responsables d'ingénierie et les architectes techniques. Pour plus d'informations, consultez [Présentation des sections de facture](../understand/mca-overview.md#invoice-sections).

Les tableaux suivants indiquent le rôle dont vous avez besoin pour accomplir des tâches dans le contexte des sections de facture.

### <a name="manage-invoice-section-permissions-and-properties"></a>Gérer les autorisations et les propriétés des sections de facture

|Tâches|Propriétaire de section de facture|Contributeur de section de facture|Lecteur de section de facture|Créateur de l'abonnement Azure|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation |Gestionnaire de factures|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Voir les attributions de rôles pour la section de facture|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Autoriser d'autres utilisateurs à consulter et gérer la section de facture|✔|✘|✘|✘|✔|✘|✘|✘|✔|✘|✘|
|Afficher les propriétés de la section de facture|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Mettre à jour les propriétés de la section de facture|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-invoice-section"></a>Gérer les produits associés à la section de facture

|Tâches|Propriétaire de section de facture|Contributeur de section de facture|Lecteur de section de facture|Créateur de l'abonnement Azure|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation |Gestionnaire de factures|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Voir tous les produits achetés sur la section de facture|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Gérer la facturation des produits (annuler, désactiver le renouvellement automatique, etc.)|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Modifier la section de facture associée aux produits|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Gérer les abonnements associés à la section de facture

|Tâches|Propriétaire de section de facture|Contributeur de section de facture|Lecteur de section de facture|Créateur de l'abonnement Azure|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation |Gestionnaire de factures|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Afficher tous les abonnements Azure associés à la section de facture|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Créer des abonnements Azure|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Annuler des abonnements Azure|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|
|Modifier la section de facture associée à l’abonnement Azure|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Demander la propriété de facturation sur les abonnements d'utilisateurs d'autres comptes de facturation|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Rôles et tâches liés à la facturation des abonnements

Les tableaux suivants indiquent le rôle dont vous avez besoin pour accomplir des tâches dans le contexte d'un abonnement.

|Tâches|Propriétaire de section de facture|Contributeur de section de facture|Lecteur de section de facture|Créateur de l'abonnement Azure|Propriétaire du profil de facturation|Collaborateur du profil de facturation|Lecteur du profil de facturation |Gestionnaire de factures|Propriétaire du compte de facturation|Collaborateur du compte de facturation|Lecteur du compte de facturation 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Création d’abonnements|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Mettre à jour le centre de coûts associé à l'abonnement|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Modifier la section de facture associée à l'abonnement|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Changer le profil de facturation associé à l’abonnement|✘|✘|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Annuler des abonnements Azure|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Gérer les rôles de facturation sur le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. Sélectionnez **Contrôle d'accès (IAM)** dans une étendue, comme un compte de facturation, un profil de facturation ou une section de facture, à laquelle vous souhaitez accorder l'accès.

4. La page Contrôle d'accès (IAM) répertorie les utilisateurs et les groupes attribués à chaque rôle pour cette étendue.

   ![Capture d'écran présentant la liste des administrateurs du compte de facturation](./media/understand-mca-roles/billing-list-admins.png)

5. Pour accorder l'accès à un utilisateur, sélectionnez **Ajouter** en haut de la page. Dans la liste déroulante Rôle, sélectionnez un rôle. Entrez l’adresse e-mail de l’utilisateur auquel vous souhaitez accorder l’accès. Sélectionnez **Enregistrer** pour attribuer le rôle.

   ![Capture d'écran illustrant l'ajout d'un administrateur à un compte de facturation](./media/understand-mca-roles/billing-add-admin.png)

6. Pour supprimer l'accès d'un utilisateur, sélectionnez l'utilisateur doté du rôle que vous souhaitez supprimer. Sélectionnez Supprimer.

   ![Capture d'écran illustrant la suppression d'un administrateur d'un compte de facturation](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l'accès à un contrat client Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique
Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus sur votre compte de facturation :

- [Prise en main de votre compte de facturation dans le cadre d'un Contrat client Microsoft](../understand/mca-overview.md)
- [Créer un abonnement Azure associé à votre compte de facturation dans le cadre d'un Contrat client Microsoft](create-subscription.md)
