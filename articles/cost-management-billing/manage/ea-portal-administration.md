---
title: Administration du portail Azure EA
description: Cet article présente les tâches courantes à la charge d’un administrateur dans le portail Azure EA.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 764346c7d37e4738992ddf25c11f5ee0913e308d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689896"
---
# <a name="azure-ea-portal-administration"></a>Administration du portail Azure EA

Cet article présente les tâches courantes à la charge d’un administrateur dans le portail Azure EA (https://ea.azure.com). Le portail Azure EA est un portail de gestion en ligne qui permet aux clients de gérer le coût de leurs services Azure EA. Pour obtenir des informations préliminaires sur le portail Azure EA, consultez l’article [Bien démarrer avec le portail Azure EA](ea-portal-get-started.md).

## <a name="associate-an-account-to-a-department"></a>Associer un compte à un service

Les administrateurs d’entreprise peuvent associer des comptes existants à des services dans le cadre de l’inscription.

### <a name="to-associate-an-account-to-a-department"></a>Pour associer un compte à un service

1. Connectez-vous au portail Azure EA en tant qu’administrateur d’entreprise.
1. Sélectionnez **Gérer** dans le volet de navigation de gauche.
1. Sélectionnez **Service**.
1. Placez le pointeur sur la ligne contenant le compte, puis sélectionnez l’icône du crayon à droite.
1. Sélectionnez le service dans le menu déroulant.
1. Sélectionnez **Enregistrer**.

## <a name="department-spending-quotas"></a>Quotas de dépenses des services

Les clients EA peuvent définir ou changer des quotas de dépenses pour chaque service dans le cadre d’une inscription. Le montant de quota de dépenses est défini pour la période de Paiement anticipé actuelle. À la fin de la période de Paiement anticipé actuelle, le système prolonge le quota de dépenses existant jusqu’à la période de Paiement anticipé suivante, sauf si les valeurs sont mises à jour.

L’administrateur de service peut afficher le quota de dépenses, mais seul l’administrateur d’entreprise peut mettre à jour le montant du quota. L’administrateur d’entreprise et l’administrateur de service recevront des notifications lorsque le quota aura atteint 50 %, 75 %, 90 % et 100 %.

### <a name="enterprise-administrator-to-set-the-quota"></a>Administrateur d’entreprise, pour définir le quota :

 1. Ouvrez le portail Azure EA.
 1. Sélectionnez **Gérer** dans le volet de navigation de gauche.
 1. Sélectionnez l’onglet **Service**.
 1. Sélectionnez le Service.
 1. Sélectionnez le symbole de crayon dans la section Détails du service, ou sélectionnez le symbole **+ Ajouter un service** pour ajouter un quota de dépenses à un nouveau service.
 1. Sous Détails du service, entrez un montant de quota de dépenses dans la devise de l’inscription, dans la zone Quota de dépenses $ (doit être supérieur à 0).
    - Vous pouvez également modifier le nom du service et le centre de coûts à ce stade.
 1. Sélectionnez **Enregistrer**.

Le quota de dépenses du service apparaît maintenant dans la vue de liste des services de l’onglet Service. À la fin du Paiement anticipé actuel, le portail Azure EA conserve les quotas de dépenses pour la période de Paiement anticipé suivante.

Le montant du quota du service est indépendant du Paiement anticipé Azure actuel, et le montant et les alertes du quota s’appliquent uniquement à l’utilisation de la première partie. Le quota de dépenses du service est fourni à titre d’information uniquement et n’applique pas de limites de dépense.

### <a name="department-administrator-to-view-the-quota"></a>Administrateur de service, pour afficher le quota :

1. Ouvrez le portail Azure EA.
1. Sélectionnez **Gérer** dans le volet de navigation de gauche.
1. Sélectionnez l’onglet **Service**, puis consultez la vue de liste des services avec des quotas de dépenses.

Si vous êtes un client indirect, les fonctionnalités de coût doivent être activées par votre partenaire de canal.

## <a name="enterprise-user-roles"></a>Rôles d’utilisateur Entreprise

Le portail Azure EA vous permet de gérer vos coûts et votre utilisation d’Azure EA. Trois rôles principaux existent dans le portail Azure EA :

- Administrateur Contrat Entreprise
- Administrateur de service
- Propriétaire du compte

Chaque rôle a un niveau d’accès et une autorité différents.

Pour plus d’informations sur les rôles d’utilisateur, consultez [Rôles d’utilisateur Entreprise](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Ajouter un compte Azure EA

Le compte Azure EA est une unité d’organisation dans le portail Azure EA. Il est utilisé pour gérer des abonnements, ainsi que pour créer des rapports. Pour accéder aux services Azure et les utiliser, vous devez créer un compte ou vous en faire créer-un.

Pour plus d’informations sur les comptes Azure, consultez [Ajouter un compte](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#add-an-account).

## <a name="enterprise-devtest-offer"></a>Offre Enterprise Dev/Test

En tant qu’administrateur d’entreprise Azure, vous pouvez autoriser les propriétaires de comptes de votre organisation à créer des abonnements basés sur l’offre EA Dev/Test. Pour ce faire, sélectionnez la zone Dev/Test du propriétaire du compte dans le portail Azure EA.

Une fois que vous avez coché la case Dev/Test, informez le propriétaire du compte afin qu’il puisse configurer les abonnements Dev/Test EA nécessaires pour ses équipes d’abonnés Dev/Test.

L’offre permet aux abonnés Visual Studio actifs d’exécuter des charges de travail de développement et de test sur Azure à des tarifs Dev/Test spéciaux. Elle fournit un accès à la galerie complète d’images Dev/Test, notamment Windows 8.1 et Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Pour configurer l’offre Enterprise Dev/Test :

1. Connectez-vous en tant qu’administrateur d’entreprise.
1. Sélectionnez **Gérer** dans le volet de navigation de gauche.
1. Sélectionnez l’onglet **Compte**.
1. Sélectionnez la ligne du compte sur lequel vous souhaitez activer l’accès Dev/Test.
1. Sélectionnez le symbole de crayon à droite de la ligne.
1. Cochez la case Dev/Test.
1. Sélectionnez **Enregistrer**.

Lorsqu’un utilisateur est ajouté en tant que propriétaire du compte par le biais du portail Azure EA, tous les abonnements Azure associés au propriétaire du compte qui sont basés sur l’offre PAYG Dev/Test ou les offres de crédit mensuel pour les abonnés Visual Studio sont convertis en l’offre EA Dev/Test. Les abonnements basés sur d’autres types d’offres, tels que PAYG, associés au propriétaire du compte, sont convertis en offres Microsoft Azure Enterprise.

L’offre Dev/Test ne s’applique pas aux clients Azure Government pour le moment.

## <a name="delete-subscription"></a>Supprimer l’abonnement

Pour supprimer un abonnement dans lequel vous êtes le propriétaire du compte :

1. Connectez-vous au portail Azure avec les informations d’identification associées à votre compte.
1. Dans le menu Hub, sélectionnez **Abonnements**.
1. Dans le panneau des abonnements situé en haut à gauche de la page, sélectionnez celui que vous voulez annuler, puis sélectionnez **Annuler l’abonnement** pour ouvrir l’onglet d’annulation.
1. Entrez le nom de l’abonnement et choisissez un motif d’annulation, puis sélectionnez le bouton **Annuler l’abonnement**.

Seuls les administrateurs de compte peuvent annuler des abonnements.

Pour plus d’informations, consultez [Que se passe-t-il après l’annulation de mon abonnement ?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription)

## <a name="delete-an-account"></a>Supprimer un compte

La suppression de compte ne peut être effectuée que pour les comptes actifs sans abonnements actifs.

1. Dans Enterprise Portal, sélectionnez **Gérer** dans le volet de navigation de gauche.
1. Sélectionnez l’onglet **Compte**.
1. Dans la table comptes, sélectionnez le compte que vous souhaitez supprimer.
1. Sélectionnez le symbole X à droite de la ligne de compte.
1. Une fois qu’il n’y a pas d’abonnements actifs sous le compte, sélectionnez **Oui** sous la ligne de compte pour confirmer la suppression du compte.

## <a name="update-notification-settings"></a>Mettre à jour les paramètres de notification

Les administrateurs d’entreprise sont automatiquement inscrits pour recevoir les notifications d’utilisation associées à leur inscription. Chaque administrateur d’entreprise peut modifier l’intervalle des notifications individuelles ou les désactiver complètement.

Les contacts de notification sont affichés dans le portail Azure EA, dans la section **Contact de notification**. La gestion de vos contacts de notification vous permet de vous assurer que les bonnes personnes de votre organisation obtiennent les notifications Azure EA.

Pour afficher les paramètres actuels des notifications :

1. Dans le portail Azure EA, accédez à **Gérer** > **Contact de notification**.
2. Adresse e-mail – Adresse e-mail associée au compte Microsoft, professionnel ou scolaire de l’administrateur d’entreprise qui reçoit les notifications.
3. Fréquence de notification du solde non facturé – Fréquence à laquelle les notifications sont configurées pour être envoyées à chaque administrateur d’entreprise individuel.

Pour ajouter un contact :

1. Sélectionnez **+Ajouter un contact**.
2. Entrez l’adresse e-mail, puis confirmez-la.
3. Sélectionnez **Enregistrer**.

Le nouveau contact de notification s’affiche dans la section **Contact de notification**. Pour changer la fréquence de notification, sélectionnez le contact de notification, puis le symbole de crayon à droite de la ligne sélectionnée. Définissez la fréquence sur **quotidienne**, **hebdomadaire**, **mensuelle** ou **aucune**.

Vous pouvez supprimer les notifications de cycle de vie _La date de fin de la période de couverture approche_ et _La date de désactivation et de déprovisionnement approche_. La désactivation des notifications de cycle de vie supprime les notifications relatives à la période de couverture et à la date de fin du contrat.

## <a name="azure-sponsorship-offer"></a>Offre Azure Sponsorship

L’offre Azure Sponsorship est un compte Microsoft Azure sponsorisé limité. Elle est disponible sur invitation par e-mail pour certains clients sélectionnés par Microsoft. Si vous bénéficiez de l’offre Microsoft Azure Sponsorship, vous recevrez une invitation par e-mail pour votre ID de compte.

Pour plus d’informations, créez une [demande de support pour l’activation de Sponsorship](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Conversion en authentification de compte professionnel ou scolaire

Les utilisateurs d’Azure Enterprise peuvent convertir un compte Microsoft (MSA ou Live ID) en un type d’authentification de compte professionnel ou scolaire (qui utilise Active Directory dans Azure).

Pour commencer :

1. Ajoutez le compte professionnel ou scolaire au portail Azure EA dans le ou les rôles nécessaires.
1. Si vous recevez des erreurs, le compte n’est peut-être pas valide dans Active Directory.  Azure utilise le nom d’utilisateur principal (UPN), qui n’est pas toujours identique à l’adresse e-mail.
1. Authentifiez-vous sur le portail Azure EA à l’aide du compte professionnel ou scolaire.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Pour convertir des abonnements de comptes Microsoft en comptes professionnels ou scolaires :

1. Connectez-vous au portail de gestion à l’aide du compte Microsoft qui possède les abonnements.
1. Utilisez le transfert de propriété de compte pour passer au nouveau compte.
1. Le compte Microsoft doit à présent être gratuit à partir de tous les abonnements actifs et peut être supprimé.
1. Un compte supprimé reste affiché dans le portail dans un état inactif pour des raisons de facturation historique.  Vous pouvez l’effacer de la vue en cochant une case afin d’afficher les comptes actifs uniquement.

## <a name="account-subscription-ownership-faq"></a>FAQ sur la propriété de l’abonnement de compte

Ce document répond à des questions fréquemment posées relatives à la propriété de l’abonnement de compte.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Combien de propriétaires de compte Azure pouvez-vous avoir par abonnement ?

Un seul propriétaire de compte est autorisé par abonnement.  Des rôles supplémentaires peuvent être ajoutés à l’aide de l’accès en fonction du rôle ou (Contrôle d’accès (IAM)) sous l’onglet de l’abonnement dans le coin supérieur gauche de la page du [portail Azure](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Un propriétaire de compte Azure peut-il être répertorié sous plusieurs services ?

Non, un propriétaire de compte ne peut être associé qu’à un seul service. La stratégie permet de garantir une supervision et une répartition précises des coûts et des dépenses associés au service auquel il est associé dans le cadre de l’inscription EA dans le portail Azure EA.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Un propriétaire de compte Azure peut-il être répertorié en tant que groupe de sécurité ?

Non, un propriétaire d’abonnement doit être une authentification de compte Microsoft (MSA) ou Azure Active Directory (AAD) unique. Pour tenir compte des successions au sein de votre organisation, vous pouvez envisager de créer des comptes génériques et d’utiliser AAD pour gérer l’accès aux abonnements.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Un utilisateur individuel peut-il avoir plusieurs abonnements ?

Un propriétaire de compte Azure peut créer et gérer un nombre illimité d’abonnements.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Comment puis-je accéder à tous les abonnements de mon organisation et les consulter ?

Aujourd’hui, cette opération doit être effectuée via une stratégie. Cela signifie que vous devez exiger que, pour chaque abonnement créé, votre compte soit ajouté à un rôle d’abonnement à l’aide de l’accès en fonction du rôle.

### <a name="where-do-i-go-to-create-a-subscription"></a>Où puis-je créer un abonnement ?

Avant de pouvoir créer un abonnement d’offre Enterprise Azure (EA), votre compte doit être ajouté au rôle de propriétaire du compte par l’administrateur de votre inscription EA dans le portail Azure EA. Vous devez ensuite vous connecter au portail Azure EA pour obtenir votre droit de créer des abonnements de type EA. Nous vous recommandons de créer votre premier abonnement EA à partir du lien « + Ajouter un abonnement » sous l’onglet de l’abonnement sur le portail EA.  Toutefois, une fois que votre compte est autorisé, il peut être plus facile de créer des abonnements dans portal.azure.com sous l’onglet de l’abonnement dans le coin supérieur gauche de la page, où vous pouvez créer et renommer votre abonnement en une seule étape.

### <a name="who-can-create-a-subscription"></a>Qui peut créer un abonnement ?

Pour créer un abonnement de type Enterprise Azure, vous devez être titulaire du rôle de propriétaire du compte sur le [portail EA](https://ea.azure.com).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la façon dont les [réservations de machines virtuelles](ea-portal-vm-reservations.md) peuvent vous aider à réaliser des économies.
- Si vous avez besoin d’aide pour résoudre des problèmes rencontrés avec le portail Azure EA, consultez [Résoudre les problèmes d’accès au portail Azure EA](ea-portal-troubleshoot.md).
