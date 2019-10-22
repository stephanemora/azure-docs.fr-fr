---
title: Administration du portail Azure EA
description: Cet article présente les tâches courantes à la charge d’un administrateur dans le portail Azure EA.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: f344436b1e1f1a97b981c1ead469d965b06a12ce
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329334"
---
# <a name="azure-ea-portal-administration"></a>Administration du portail Azure EA

Cet article présente les tâches courantes à la charge d’un administrateur dans le portail Azure EA (https://ea.azure.com). Le portail Azure EA est un portail de gestion en ligne qui permet aux clients de gérer le coût de leurs services Azure EA. Pour obtenir des informations préliminaires sur le portail Azure EA, consultez l’article [Bien démarrer avec le portail Azure EA](billing-ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Ajouter un nouvel administrateur d’entreprise

Les administrateurs d’entreprise disposent de la majorité des privilèges lors de la gestion d’une inscription Azure EA. L’administrateur Azure EA initial a été créé lors de la configuration du Contrat Entreprise. Toutefois, vous pouvez ajouter ou supprimer de nouveaux administrateurs à tout moment. Les nouveaux administrateurs peuvent être ajoutés uniquement par des administrateurs existants. Pour plus d’informations sur l’ajout d’administrateurs d'entreprise supplémentaires, consultez [Créer un autre administrateur d’entreprise](billing-ea-portal-get-started.md#create-another-enterprise-admin). Pour plus d’informations sur les rôles et les tâches d’un profil de facturation, consultez [Tâches et rôles du profil de facturation](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>Mettre à jour l’état utilisateur de En attente à Actif

Lorsque de nouveaux propriétaires de compte sont ajoutés pour la première fois à une inscription Azure EA, leur état est _En attente_. Quand un nouveau propriétaire de compte reçoit l’e-mail de bienvenue et d’activation, il peut se connecter pour activer son compte. Quand il active son compte, l’état du compte est mis à jour de _En attente_ à _Actif_. Un nouvel utilisateur peut être invité à entrer son prénom et son nom pour créer un compte de commerce. Il doit alors ajouter les informations requises pour continuer et le compte est activé.

## <a name="add-a-department-admin"></a>Ajouter un administrateur de service

Une fois qu’un administrateur Azure EA a créé un service, l’administrateur d’entreprise Azure peut ajouter des administrateurs de service et associer chacun d’eux à un service. Un administrateur de service peut créer des comptes. Ces comptes sont nécessaires pour créer des abonnements Azure EA.

Pour plus d’informations sur l’ajout d’un administrateur de service, consultez [Créer un administrateur de service Azure EA](billing-ea-portal-get-started.md#add-a-department-admin).

## <a name="enterprise-user-roles"></a>Rôles d’utilisateur Entreprise

Le portail Azure EA vous permet de gérer vos coûts et votre utilisation d’Azure EA. Trois rôles principaux existent dans le portail Azure EA :

- Administrateur Contrat Entreprise
- Administrateur de service
- Propriétaire du compte

Chaque rôle a un niveau d’accès et une autorité différents.

Pour plus d’informations sur les rôles d’utilisateur, consultez Rôles d’utilisateur Entreprise.

## <a name="add-an-azure-ea-account"></a>Ajouter un compte Azure EA

Le compte Azure EA est une unité d’organisation dans le portail Azure EA qui est utilisée pour administrer les abonnements et pour la création de rapports. Pour accéder aux services Azure et les utiliser, vous devez créer un compte ou vous en faire créer-un.

Pour plus d’informations sur les comptes Azure, consultez Ajouter un compte.

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
  - Dans certains cas, Microsoft peut demander une approbation supplémentaire de la part d’un administrateur EA de l’inscription source.
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
  - Dans certains cas, Microsoft peut demander une approbation supplémentaire de la part d’un administrateur EA de l’inscription source.
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



## <a name="close-an-azure-enterprise-enrollment"></a>Clore une inscription Azure Entreprise

Si vous souhaitez clore votre inscription Azure EA, vous pouvez :

- Annuler tous les abonnements associés à votre Contrat Entreprise Azure sur le portail Azure.
- Contacter votre conseiller ou partenaire logiciel client et lui demander de clôturer votre Contrat Entreprise Azure.

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

## <a name="manage-partner-notifications"></a>Gérer les notifications des partenaires

Les administrateurs partenaires peuvent gérer la fréquence à laquelle ils reçoivent des notifications d’utilisation pour leurs inscriptions. Ils reçoivent automatiquement des notifications hebdomadaires de leur solde non facturé. Ils peuvent modifier la fréquence des notifications individuelles pour spécifier une fréquence mensuelle, hebdomadaire ou quotidienne, ou pour les désactiver complètement.

Si une notification n’est pas reçue par un utilisateur, vérifiez que les paramètres de notification de l’utilisateur sont corrects en procédant comme suit.

1. Connectez-vous au portail Azure EA en tant qu’administrateur d’entreprise.
2. Cliquez sur **Gérer**, puis sur l’onglet **Partenaire**.
3. Affichez la liste des administrateurs sous la section **Administrateur**.
4. Pour modifier les préférences de notification, pointez sur l’administrateur approprié et cliquez sur le symbole de crayon.
5. Augmentez la fréquence de notification et les notifications de cycle de vie selon les besoins.
6. Ajoutez un contact, si nécessaire, puis cliquez sur **Ajouter**.
7. Cliquez sur **Enregistrer**.

![Exemple montrant la zone Ajouter un contact ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Offre Azure Sponsorship

L’offre Azure Sponsorship est un compte Microsoft Azure sponsorisé limité. Elle est disponible sur invitation électronique pour certains clients sélectionnés par Microsoft. Si vous bénéficiez de l’offre Microsoft Azure Sponsorship, vous recevrez une invitation électronique pour votre ID de compte.

Pour plus d'informations, consultez les pages suivantes :

- Demande de support concernant l’activation de Sponsorship - http://aka.ms/azrsponsorship

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur la façon dont les [réservations de machines virtuelles](billing-ea-portal-vm-reservations.md) peuvent vous aider à réaliser des économies.
- Si vous avez besoin d’aide pour résoudre des problèmes rencontrés avec le portail Azure EA, consultez [Résoudre les problèmes d’accès au portail Azure EA](billing-ea-portal-troubleshoot.md).
