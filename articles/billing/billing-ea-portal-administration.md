---
title: Administration du portail Azure EA
description: Cet article présente les tâches courantes à la charge d’un administrateur dans le portail Azure EA.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: c53a051df0a0100d9209530490d910612be2f30d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849919"
---
# <a name="azure-ea-portal-administration"></a>Administration du portail Azure EA

Cet article présente les tâches courantes à la charge d’un administrateur dans le portail Azure EA (https://ea.azure.com). Le portail Azure EA est un portail de gestion en ligne qui permet aux clients de gérer le coût de leurs services Azure EA. Pour obtenir des informations préliminaires sur le portail Azure EA, consultez l’article [Bien démarrer avec le portail Azure EA](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Ajouter un nouvel administrateur d’entreprise

Les administrateurs d’entreprise disposent de la majorité des privilèges lors de la gestion d’une inscription Azure EA. L’administrateur Azure EA initial a été créé lors de la configuration du Contrat Entreprise. Toutefois, vous pouvez ajouter ou supprimer de nouveaux administrateurs à tout moment. Les nouveaux administrateurs peuvent être ajoutés uniquement par des administrateurs existants. Pour plus d’informations sur l’ajout d’administrateurs d'entreprise supplémentaires, consultez [Créer un autre administrateur d’entreprise](billing-ea-portal-get-started.md#create-another-enterprise-admin). Pour plus d’informations sur les rôles et les tâches d’un profil de facturation, consultez [Tâches et rôles du profil de facturation](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Mettre à jour l’état utilisateur de En attente à Actif

Lorsque de nouveaux propriétaires de compte sont ajoutés pour la première fois à une inscription Azure EA, leur état est _En attente_. Quand un nouveau propriétaire de compte reçoit l’e-mail de bienvenue et d’activation, il peut se connecter pour activer son compte. Une fois qu’il active son compte, l’état du compte est mis à jour de _En attente_ à _Actif_. Le propriétaire du compte doit lire le message d’« avertissement », puis cliquer sur **Continuer**. Un nouvel utilisateur peut être invité à entrer son prénom et son nom pour créer un compte de commerce. Il doit alors ajouter les informations requises pour continuer et le compte est activé.

## <a name="add-a-department-admin"></a>Ajouter un administrateur de service

Une fois qu’un administrateur Azure EA a créé un service, l’administrateur d’entreprise Azure peut ajouter des administrateurs de service et associer chacun d’eux à un service. Un administrateur de service peut créer des comptes. Ces comptes sont nécessaires pour créer des abonnements Azure EA.

Pour plus d’informations sur l’ajout d’un administrateur de service, consultez [Créer un administrateur de service Azure EA](billing-ea-portal-get-started.md#add-a-department-admin).

## <a name="associate-an-account-to-a-department"></a>Associer un compte à un service

Les administrateurs d’entreprise peuvent associer des comptes existants à des services dans le cadre de l’inscription.

### <a name="to-associate-an-account-to-a-department"></a>Pour associer un compte à un service

1. Connectez-vous au portail Azure EA en tant qu’administrateur d’entreprise.
1. Cliquez sur **Gérer** dans le volet de navigation de gauche.
1. Cliquez sur **Service**.
1. Placez le pointeur sur la ligne contenant le compte souhaité, puis cliquez sur l’icône du crayon à droite.
1. Sélectionnez le service de votre choix dans le menu déroulant.
1. Cliquez sur **Enregistrer**.

## <a name="department-spending-quotas"></a>Quotas de dépenses des services

Les clients EA peuvent définir ou modifier des quotas de dépenses pour chaque service pour une inscription. Le montant de quota de dépenses est défini pour la période d’engagement actuelle. À la fin de la période d’engagement actuelle, le système prolonge le quota de dépenses existant jusqu’à la période d’engagement suivante, sauf si les valeurs sont mises à jour.

L’administrateur de service peut afficher le quota de dépenses, mais seul l’administrateur d’entreprise peut mettre à jour le montant du quota. L’administrateur d’entreprise et l’administrateur de service recevront des notifications lorsque le quota aura atteint 50 %, 75 %, 90 % et 100 %.

### <a name="enterprise-administrator-to-set-the-quota"></a>Administrateur d’entreprise, pour définir le quota :

 1. Ouvrez le portail Azure EA.
 1. Cliquez sur **Gérer** dans le volet de navigation de gauche.
 1. Cliquez sur l’onglet **Service**.
 1. Cliquez sur le service souhaité.
 1. Cliquez sur l’icône du crayon dans la section des détails du service, ou cliquez sur le bouton **+ Ajouter un service** pour ajouter un quota de dépenses à un nouveau service.
 1. Sous Détails du service, entrez un montant de quota de dépenses dans la devise de l’inscription, dans la zone Quota de dépenses $ (doit être supérieur à 0).
    - Vous pouvez également modifier le nom du service et le centre de coûts à ce stade.
 1. Cliquez sur **Enregistrer**.

Le quota de dépenses du service apparaît maintenant dans la vue de liste des services de l’onglet Service. À la fin de l’engagement actuel, le portail Azure EA conserve les quotas de dépenses pour la période d’engagement suivante.

Le montant du quota du service est indépendant de l’engagement financier actuel, et le montant et les alertes du quota s’appliquent uniquement à l’utilisation de la première partie. Le quota de dépenses du service est fourni à titre d’information uniquement et n’applique pas de limites de dépense.

### <a name="department-administrator-to-view-the-quota"></a>Administrateur de service, pour afficher le quota :

1. Ouvrez le portail Azure EA.
1. Cliquez sur **Gérer** dans le volet de navigation de gauche.
1. Cliquez sur l’onglet **Service** et affichez la vue de liste des services avec des quotas de dépenses.

Si vous êtes un client indirect, les fonctionnalités de coût doivent être activées par votre partenaire de canal.

## <a name="enterprise-user-roles"></a>Rôles d’utilisateur Entreprise

Le portail Azure EA vous permet de gérer vos coûts et votre utilisation d’Azure EA. Trois rôles principaux existent dans le portail Azure EA :

- Administrateur Contrat Entreprise
- Administrateur de service
- Propriétaire du compte

Chaque rôle a un niveau d’accès et une autorité différents.

Pour plus d’informations sur les rôles d’utilisateur, consultez [Rôles d’utilisateur Entreprise](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Ajouter un compte Azure EA

Le compte Azure EA est une unité d’organisation dans le portail Azure EA qui est utilisée pour administrer les abonnements et pour la création de rapports. Pour accéder aux services Azure et les utiliser, vous devez créer un compte ou vous en faire créer-un.

Pour plus d’informations sur les comptes Azure, consultez Ajouter un compte.

## <a name="enterprise-devtest-offer"></a>Offre Enterprise Dev/Test

En tant qu’administrateur d’entreprise Azure, vous pouvez désormais autoriser les propriétaires de comptes de votre organisation à créer des abonnements basés sur l’offre EA Dev/Test. Pour cela, cochez la case à cocher Dev/Test pour ce propriétaire de compte dans le portail Azure EA.

Une fois que vous avez coché la case Dev/Test, informez le propriétaire du compte afin qu’il puisse configurer les abonnements Dev/Test EA nécessaires pour ses équipes d’abonnés Dev/Test.

Cette offre permet aux abonnés Visual Studio actifs d’exécuter des charges de travail de développement et de test sur Azure à des tarifs Dev/Test spéciaux, avec un accès à la galerie complète d’images Dev/Test, y compris Windows 8.1 et Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Pour configurer l’offre Enterprise Dev/Test :

1. Connectez-vous en tant qu’administrateur d’entreprise.
1. Cliquez sur **Gérer** dans le volet de navigation de gauche.
1. Cliquez sur l’onglet **Compte**.
1. Cliquez sur la ligne du compte sur lequel vous souhaitez activer l’accès Dev/Test.
1. Cliquez sur l’icône du crayon à droite de la ligne.
1. Cochez la case Dev/Test.
1. Cliquez sur **Enregistrer**.

Lorsqu’un utilisateur est ajouté en tant que propriétaire du compte par le biais du portail Azure EA, tous les abonnements Azure associés au propriétaire du compte qui sont basés sur l’offre PAYG Dev/Test ou les offres de crédit mensuel pour les abonnés Visual Studio sont convertis en l’offre EA Dev/Test. Les abonnements basés sur d’autres types d’offres, tels que PAYG, associés au propriétaire du compte, sont convertis en offres Microsoft Azure Enterprise.

L’offre Dev/Test ne s’applique pas aux clients Azure Gov pour le moment.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Transférer un compte d’entreprise vers une nouvelle inscription

Gardez les points suivants à l’esprit quand vous transférez un compte d’entreprise vers une nouvelle inscription :

- Seuls les comptes spécifiés dans la demande sont transférés. Si tous les comptes sont choisis, ils sont tous transférés.
- L’inscription source conserve son état actif ou étendu. Vous pouvez continuer à utiliser l’inscription jusqu’à ce qu’elle expire.

### <a name="effective-transfer-date"></a>Date de transfert effectif

La date de transfert effectif peut être la date de début de l’inscription vers laquelle vous souhaitez effectuer le transfert ou une date ultérieure. L’inscription vers laquelle vous effectuez le transfert est l’_inscription cible_. Une fois le compte transféré, toutes les informations sur l’utilisation du compte avant la date de transfert effectif restent dans l’inscription à partir de laquelle vous effectuez le transfert. L’inscription à partir de laquelle vous effectuez le transfert est l’_inscription source_.  L’utilisation de l’inscription source est facturée dans le cadre de l’engagement monétaire ou comme dépassement. L’utilisation postérieure à la date de transfert effectif est transférée vers la nouvelle inscription et facturée en conséquence.

Vous pouvez antidater un transfert d’inscription jusqu’à la date de début de l’inscription cible. Ou, jusqu’à la date de début effectif de l’inscription source.

### <a name="monetary-commitment"></a>Engagement monétaire

L’engagement monétaire n’est pas transférable entre les inscriptions. Les soldes d’engagement monétaire sont liées contractuellement à l’inscription dans laquelle elles ont été commandées. L’engagement monétaire n’est pas transféré dans le cadre du processus de transfert d’un compte ou d’une inscription.

### <a name="services-affected"></a>Services affectés

Il n’y a aucun temps d’arrêt pendant le transfert de compte. Il peut être effectué le même jour que votre demande si toutes les informations requises sont fournies.

### <a name="prerequisites"></a>Prérequis

Lorsque vous demandez un transfert de compte, fournissez les informations suivantes :


- Nom du compte et ID du propriétaire du compte à transférer
- Pour l’inscription source, le numéro d’inscription et le compte à transférer
- Pour l’inscription cible, le numéro de l’inscription vers laquelle transférer
- Pour la date de transfert effectif du compte, il peut s’agir d’une date identique ou postérieure à la date de début de l’inscription cible.

Autres points à garder à l’esprit avant un transfert de compte :

- L’approbation d’un administrateur EA est requise pour les inscriptions source et cible.
- Si un transfert de compte ne répond pas à vos exigences, envisagez un transfert d’inscription.
- Le transfert de compte transfère tous les services et abonnements associés aux comptes spécifiques.
- Au terme du transfert, le compte transféré est indiqué comme inactif sous l’inscription source.
- Un transfert de compte peut être antidaté à n’importe quelle date avant la date de début de l’inscription cible.
- Le compte affiche la date de fin correspondant à la date de transfert effectif sur l’inscription source, et comme date de début sur l’inscription cible.
- Toute utilisation du compte effectuée avant la date de transfert effectif reste sous l’inscription source.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Transférer l’inscription d’entreprise vers une nouvelle inscription

Lorsque vous demandez à transférer une inscription d’entreprise complète vers une autre inscription, les actions suivantes se produisent :

- Tous les services, les abonnements et les comptes Azure, ainsi que la structure d’inscription complète, y compris tous les administrateurs de service EA, sont transférés.
- L’état de l’inscription est défini sur _Transféré_. L’inscription transférée est disponible uniquement à des fins de création de rapports d’utilisation.
- Vous ne pouvez pas ajouter de rôles ni d’abonnements à une inscription transférée. L’état transféré empêche une utilisation supplémentaire en relation avec l’inscription.
- Tout solde restant de l’engagement monétaire dans le contrat est perdu, y compris les termes futurs.
-   Si l’inscription à partir de laquelle vous transférez inclut des achats RI, les frais d’achat RI resteront dans l’inscription source. Toutefois, tous les avantages RI seront transférés pour être utilisés dans la nouvelle inscription.
-   Les frais d’achat à usage unique de la place de marché et les frais fixes mensuels déjà engagés sur l’ancienne inscription ne seront pas transférés vers la nouvelle inscription. Les frais de la place de marché basés sur la consommation seront transférés.
-   Après un transfert d’inscription en retard, les frais à usage unique des achats restent dans l’inscription source.

### <a name="effective-transfer-date"></a>Date de transfert effectif

Le jour du transfert effectif peut correspondre à la date de début de l’inscription que vous souhaitez transférer vers l’inscription cible ou à une date ultérieure.

L’utilisation de l’inscription source est facturée dans le cadre de l’engagement monétaire ou comme dépassement. L’utilisation postérieure à la date de transfert effectif est transférée vers la nouvelle inscription et facturée en conséquence.

### <a name="effective-transfer-date-in-the-past"></a>Date de transfert effectif dans le passé

Vous pouvez antidater un transfert de compte jusqu’à la date de début de l’inscription cible. Ou, jusqu’à la date de début effectif de l’inscription source.

### <a name="monetary-commitment"></a>Engagement monétaire

L’engagement monétaire n’est pas transférable entre les inscriptions. Les soldes d’engagement monétaire sont liées contractuellement à l’inscription dans laquelle elles ont été commandées. L’engagement monétaire n’est pas transféré dans le cadre du processus de transfert d’un compte ou d’une inscription.

### <a name="services-affected"></a>Services affectés

Il n’y a aucun temps d’arrêt pendant le transfert de compte. Il peut être effectué le même jour que votre demande si toutes les informations requises sont fournies.

### <a name="prerequisites"></a>Prérequis

Lorsque vous demandez un transfert d’inscription, fournissez les informations suivantes :

- Pour l’inscription source, le numéro d’inscription et le compte à transférer.
- Pour l’inscription cible, le numéro de l’inscription vers laquelle effectuer le transfert.
- Pour la date de transfert effectif de l’inscription, il peut s’agir d’une date identique ou postérieure à la date de début de l’inscription cible. La date choisie ne peut pas affecter l’utilisation pour une facture de dépassement déjà émise.

Autres points à garder à l’esprit avant un transfert d’inscription :

- L’approbation d’un administrateur EA est obligatoire pour les inscriptions source et cible.
- Si un transfert d’inscription ne répond pas à vos exigences, envisagez un transfert de compte.
- Seuls les comptes que vous spécifiez sont transférés. Vous pouvez demander à transférer tous vos comptes.
- L’inscription source conserve son état actif ou étendu. Vous pouvez continuer à utiliser l’inscription jusqu’à ce qu’elle expire.

## <a name="change-account-owner"></a>Changer le propriétaire du compte

Le portail Azure EA peut transférer des abonnements d’un propriétaire de compte à un autre. Pour plus d’informations, consultez [Changer le propriétaire du compte](billing-ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Effets d’un transfert d'abonnement

Quand un abonnement Azure est transféré vers un compte figurant dans le même locataire Azure Active Directory, tous les utilisateurs, groupes et principaux de service qui disposaient d’un [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) pour gérer les ressources conservent leur accès.

Pour afficher les utilisateurs disposant d’un accès RBAC à l’abonnement :

1. Sur le portail Azure, ouvrez **Abonnements**.
2. Sélectionnez l’abonnement à visualiser, puis sélectionnez **Contrôle d’accès (IAM)** .
3. Sélectionnez **Attributions de rôles**. La page des attributions de rôles liste tous les utilisateurs qui disposent d’un accès RBAC à l’abonnement.

Si l’abonnement est transféré vers un compte figurant dans un autre locataire Azure AD, tous les utilisateurs, groupes et principaux de service qui disposaient d’un contrôle [RBAC](../role-based-access-control/overview.md) pour gérer les ressources _perdent_ leur accès. Même si l’accès RBAC n’est pas présent, l’accès à l’abonnement peut être disponible via des mécanismes de sécurité, notamment :

- Certificats de gestion accordant à l’utilisateur des droits d’administrateur sur les ressources d’abonnement. Pour plus d'informations, consultez la rubrique [Créer et télécharger un certificat de gestion pour Microsoft Azure](../cloud-services/cloud-services-certs-create.md).
- Touches d’accès rapide pour les services tels que Storage. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage Azure](../storage/common/storage-account-overview.md).
- Informations d’identification d’accès à distance pour les services tels que les machines virtuelles Azure.

Le destinataire doit envisager la mise à jour des secrets associés au service s’il doit restreindre l’accès à ses ressources Azure. La plupart des ressources peuvent être mises à jour en procédant comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Dans le menu Hub, sélectionnez **Toutes les ressources**.
3. Sélectionnez la ressource.
4. Dans la page de la ressource, cliquez sur **Paramètres** pour afficher et mettre à jour les secrets existants.

## <a name="delete-subscription"></a>Supprimer l’abonnement

Pour supprimer un abonnement dans lequel vous êtes le propriétaire du compte :

1. Connectez-vous au portail Azure avec les informations d’identification associées à votre compte.
1. Dans le menu Hub, sélectionnez **Abonnements**.
1. Dans le panneau des abonnements dans le coin supérieur gauche de la page, sélectionnez celui que vous souhaitez annuler et cliquez sur **Annuler l’abonnement** pour ouvrir l’onglet d’annulation.
1. Entrez le nom de l’abonnement et choisissez un motif d’annulation, puis cliquez sur le bouton **Annuler l’abonnement**.

Notez que seuls les administrateurs de compte peuvent annuler des abonnements.

## <a name="delete-an-account"></a>Supprimer un compte

La suppression de compte ne peut être effectuée que pour les comptes actifs sans abonnements actifs.

1. Dans Enterprise Portal, sélectionnez **Gérer** dans le volet de navigation de gauche.
1. Cliquez sur l’onglet **Compte**.
1. Dans la table comptes, sélectionnez le compte que vous souhaitez supprimer.
1. Cliquez sur l’icône X à droite de la ligne de compte.
1. Une fois qu’il n’y a pas d’abonnements actifs sous le compte, cliquez sur le bouton **Oui** sous la ligne de compte pour confirmer la suppression du compte.

## <a name="update-notification-settings"></a>Mettre à jour les paramètres de notification

Les administrateurs d’entreprise sont automatiquement inscrits pour recevoir les notifications d’utilisation associées à leur inscription. Chaque administrateur d’entreprise peut modifier l’intervalle des notifications individuelles ou les désactiver complètement.

Les contacts de notification sont affichés dans le portail Azure EA, dans la section **Contact de notification**. La gestion de vos contacts de notification vous permet de vous assurer que les bonnes personnes de votre organisation obtiennent les notifications Azure EA.

Pour afficher les paramètres actuels des notifications :

1. Dans le portail Azure EA, accédez à **Gérer** > **Contact de notification**.
2. Adresse e-mail – Adresse e-mail associée au compte Microsoft, professionnel ou scolaire de l’administrateur d’entreprise qui reçoit les notifications.
3. Fréquence de notification du solde non facturé – Fréquence à laquelle les notifications sont configurées pour être envoyées à chaque administrateur d’entreprise individuel.

Pour ajouter un contact :

1. Cliquez sur **+Ajouter un contact**.
2. Entrez l’adresse e-mail, puis confirmez-la.
3. Cliquez sur **Enregistrer**.

Le nouveau contact de notification s’affiche dans la section **Contact de notification**. Pour modifier la fréquence de notification, sélectionnez le contact de notification et cliquez sur le symbole de crayon à droite de la ligne sélectionnée. Définissez la fréquence sur **quotidienne**, **hebdomadaire**, **mensuelle** ou **aucune**.

Vous pouvez supprimer les notifications de cycle de vie _La date de fin de la période de couverture approche_ et _La date de désactivation et de déprovisionnement approche_. La désactivation des notifications de cycle de vie supprime les notifications relatives à la période de couverture et à la date de fin du contrat.

## <a name="manage-partner-administrators"></a>Gérer les administrateurs partenaires

Chaque administrateur partenaire dans le portail Azure EA peut ajouter ou supprimer d’autres administrateurs partenaires. Les administrateurs partenaires sont associés aux organisations partenaires d’inscriptions indirectes et ne sont pas directement associés aux inscriptions.

### <a name="add-a-partner-administrator"></a>Ajouter un administrateur partenaire

Pour afficher la liste de toutes les inscriptions associées à la même organisation partenaire que l’utilisateur actuel, cliquez sur l’onglet **Inscription** et sélectionnez une zone d’inscription souhaitée.

1. Connectez-vous en tant qu’administrateur partenaire.
1. Cliquez sur **Gérer** dans le volet de navigation de gauche.
1. Cliquez sur l’onglet **Partenaire**.
1. Cliquez sur **+ Ajouter un administrateur** et renseignez l’adresse e-mail, le contact de notification et les détails de notification.
1. Appuyez sur **Ajouter**.

### <a name="remove-a-partner-administrator"></a>Supprimer un administrateur partenaire

Pour afficher la liste de toutes les inscriptions associées à la même organisation partenaire que l’utilisateur actuel, cliquez sur l’onglet **Inscription** et sélectionnez une zone d’inscription souhaitée.

1. Connectez-vous en tant qu’administrateur partenaire.
1. Cliquez sur **Gérer** dans le volet de navigation de gauche.
1. Cliquez sur l’onglet **Partenaire**.
1. Sous la section Administrateur, sélectionnez la ligne appropriée pour l’administrateur que vous souhaitez supprimer.
1. Appuyez sur l’icône X à droite.
1. Confirmez la suppression.

## <a name="manage-partner-notifications"></a>Gérer les notifications des partenaires

Les administrateurs partenaires peuvent gérer la fréquence à laquelle ils reçoivent des notifications d’utilisation pour leurs inscriptions. Ils reçoivent automatiquement des notifications hebdomadaires de leur solde non facturé. Ils peuvent modifier la fréquence des notifications individuelles pour spécifier une fréquence mensuelle, hebdomadaire ou quotidienne, ou pour les désactiver complètement.

Si une notification n’est pas reçue par un utilisateur, vérifiez que les paramètres de notification de l’utilisateur sont corrects en procédant comme suit.

1. Connectez-vous au portail Azure EA en tant qu’administrateur d’entreprise.
2. Cliquez sur **Gérer**, puis sur l’onglet **Partenaire**.
3. Affichez la liste des administrateurs dans la section Administrateur.
4. Pour modifier les préférences de notification, pointez sur l’administrateur approprié et cliquez sur le symbole de crayon.
5. Augmentez la fréquence de notification et les notifications de cycle de vie selon les besoins.
6. Ajoutez un contact, si nécessaire, puis cliquez sur **Ajouter**.
7. Cliquez sur **Enregistrer**.

![Exemple montrant la zone Ajouter un contact ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>Afficher les inscriptions pour les administrateurs partenaires

Les administrateurs partenaires peuvent consulter une liste de toutes leurs inscriptions directes et indirectes dans le portail Azure EA. Les zones contenant une vue d’ensemble de chaque inscription s’affichent avec le numéro d’inscription, le nom de l’inscription, le solde et les quantités de dépassement.

### <a name="view-a-list-of-enrollments"></a>Afficher une liste d’inscriptions

1. Connectez-vous en tant qu’administrateur partenaire.
1. Cliquez sur **Gérer** dans la barre de navigation sur le côté gauche de la page.
1. Cliquez sur l’onglet **Inscription**.
1. Cochez la case correspondant à l’inscription souhaitée.

Une vue de toutes les inscriptions reste en haut de la page, avec des zones pour chaque inscription. Vous pouvez également naviguer entre les inscriptions en cliquant sur le numéro d’inscription en cours dans la barre de navigation sur le côté gauche de la page. Une fenêtre contextuelle s’affiche pour vous permettre de rechercher des inscriptions ou de sélectionner une autre inscription en cliquant sur la zone appropriée.

## <a name="azure-sponsorship-offer"></a>Offre Azure Sponsorship

L’offre Azure Sponsorship est un compte Microsoft Azure sponsorisé limité. Elle est disponible sur invitation électronique pour certains clients sélectionnés par Microsoft. Si vous bénéficiez de l’offre Microsoft Azure Sponsorship, vous recevrez une invitation par e-mail pour votre ID de compte.

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

Un seul propriétaire de compte est autorisé par abonnement.  Des rôles supplémentaires peuvent être ajoutés à l’aide de l’accès en fonction du rôle ou (Contrôle d’accès (IAM)) dans l’onglet de l’abonnement dans le coin supérieur gauche de la page sur [portal.azure.com]] (https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Un propriétaire de compte Azure peut-il être répertorié sous plusieurs services ?

Un propriétaire de compte ne peut être associé qu’à un seul service.  Cela permet d’assurer une surveillance et une répartition précises des coûts et des dépenses associés au service auquel il est associé dans le cadre de l’inscription EA dans le portail Azure EA.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Un propriétaire de compte Azure peut-il être répertorié en tant que groupe de sécurité ?

Non, un propriétaire d’abonnement doit être une authentification de compte Microsoft (MSA) ou Azure Active Directory (AAD) unique. Pour tenir compte des successions au sein de votre organisation, vous pouvez envisager de créer des comptes génériques et d’utiliser AAD pour gérer l’accès aux abonnements.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Un utilisateur individuel peut-il avoir plusieurs abonnements ?

Un propriétaire de compte Azure peut créer et gérer un nombre illimité d’abonnements.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Comment puis-je accéder à tous les abonnements de mon organisation et les afficher ?

Aujourd’hui, cette opération doit être effectuée via une stratégie. Cela signifie que vous devez exiger que, pour chaque abonnement créé, votre compte soit ajouté à un rôle d’abonnement à l’aide de l’accès en fonction du rôle.

### <a name="where-do-i-go-to-create-a-subscription"></a>Où puis-je créer un abonnement ?

Avant de pouvoir créer un abonnement d’offre Enterprise Azure (EA), votre compte doit être ajouté au rôle de propriétaire du compte par l’administrateur de votre inscription EA dans le portail Azure EA. Vous devez ensuite vous connecter au portail Azure EA pour obtenir votre droit de créer des abonnements de type EA. Nous vous recommandons de créer votre premier abonnement EA à partir du lien « + Ajouter un abonnement » dans l’onglet de l’abonnement sur le portail EA.  Toutefois, une fois que votre compte est autorisé, il peut être plus facile de créer des abonnements dans portal.azure.com sous l’onglet de l’abonnement dans le coin supérieur gauche de la page, où vous pouvez créer et renommer votre abonnement en une seule étape.

### <a name="who-can-create-a-subscription"></a>Qui peut créer un abonnement ?

Pour créer un abonnement de type Enterprise Azure, vous devez être titulaire du rôle de propriétaire du compte sur le [portail EA](https://ea.azure.com).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la façon dont les [réservations de machines virtuelles](billing-ea-portal-vm-reservations.md) peuvent vous aider à réaliser des économies.
- Si vous avez besoin d’aide pour résoudre des problèmes rencontrés avec le portail Azure EA, consultez [Résoudre les problèmes d’accès au portail Azure EA](billing-ea-portal-troubleshoot.md).
