---
title: Autorisations pour afficher et gérer les réservations Azure
description: Découvrez comment afficher et gérer les réservations Azure dans le portail Azure.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: fe2f36b08f98ceb2a5f6085510b589a712ff194d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780458"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Autorisations pour afficher et gérer les réservations Azure

Cet article explique le fonctionnement des autorisations de réservation et comment les utilisateurs peuvent afficher et gérer les réservations Azure dans le portail Azure.

## <a name="who-can-manage-a-reservation-by-default"></a>Qui peut gérer une réservation par défaut

Par défaut, les utilisateurs suivants peuvent voir et gérer des réservations :

- La personne qui achète une réservation et l’administrateur de compte de l’abonnement de facturation utilisé pour acheter la réservation sont ajoutés à l’ordre de réservation.
- Les administrateurs de facturation de l’Accord Entreprise et du Contrat client Microsoft.
- Les utilisateurs disposant d’un accès avec élévation de privilèges pour gérer tous les abonnements et groupes d’administration Azure.

Le cycle de vie des réservations étant indépendant d’un abonnement Azure, la réservation n’est pas une ressource dans le cadre de l’abonnement Azure. Il s’agit plutôt d’une ressource au niveau du locataire avec sa propre autorisation Azure RBAC distincte des abonnements. Les réservations n’héritent pas des autorisations des abonnements après achat.

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>Comment les administrateurs de facturation peuvent afficher et gérer les réservations

Si vous êtes administrateur de facturation, suivez les étapes ci-dessous pour afficher et gérer toutes les réservations et les transactions de réservation.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à **Cost Management and Billing**.
    - Si vous êtes un administrateur d’entreprise, dans le menu de gauche, sélectionnez **Étendues de facturation**, puis, dans la liste des étendues de facturation, sélectionnez-en une.
    - Si vous êtes propriétaire d’un profil de facturation Contrat client Microsoft, dans le menu de gauche, sélectionnez **Profils de facturation**. Dans la liste des profils de facturation, sélectionnez-en un.
1. Dans le menu de gauche, sélectionnez **Produits + services** > **Réservations**.
1. La liste complète des réservations pour votre profil d’inscription ou de facturation d’administrateur d’entreprise s’affiche.
1. Les administrateurs de facturation peuvent prendre possession d’une réservation en la sélectionnant, puis en sélectionnant **Accorder l’accès** dans la fenêtre qui s’affiche.

### <a name="how-to-add-billing-administrators"></a>Comment ajouter des administrateurs de facturation

Ajouter un utilisateur en tant qu’administrateur de facturation à un Contrat Entreprise ou à un Contrat client Microsoft :

- Pour un Contrat Entreprise, ajoutez des utilisateurs avec le rôle d’_Administrateur d’entreprise_ qui permet d’afficher et de gérer tous les ordres de réservation qui s’appliquent au Contrat Entreprise. Les administrateurs d’entreprise peuvent afficher et gérer les réservations dans **Gestion des coûts + facturation**.
    - Les utilisateurs dotés du rôle _Administrateur d’entreprise (lecture seule)_ peuvent uniquement afficher la réservation à partir de **Gestion des coûts + facturation**. 
    - Les administrateurs de service et les propriétaires de compte ne peuvent pas afficher les réservations _à moins_ d’être explicitement ajoutés à celles-ci à l’aide du contrôle d’accès (IAM). Pour plus d’informations, consultez [Gestion des rôles Azure Enterprise](../manage/understand-ea-roles.md).
- Pour un Contrat client Microsoft, les utilisateurs détenant le rôle de propriétaire du profil de facturation ou le rôle de contributeur du profil de facturation peuvent gérer l’ensemble des achats de réservation effectués à l’aide du profil de facturation. Les lecteurs de profil de facturation et les gestionnaires de facture peuvent voir toutes les réservations qui sont réglées avec le profil de facturation. Toutefois, ils ne peuvent apporter aucune modification aux réservations.
    Pour plus d’informations, consultez [Rôles et tâches liés au profil de facturation](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="view-reservations-with-azure-rbac-access"></a>Afficher les réservations avec l’accès Azure RBAC

Si vous avez acheté la réservation ou si vous êtes ajouté à une réservation, suivez les étapes ci-dessous pour afficher et gérer les réservations.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Tous les services** > **Réservation** pour afficher la liste des réservations auxquelles vous avez accès.

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>Les utilisateurs dotés d’un accès avec élévation de privilèges peuvent gérer tous les abonnements et groupes d’administration Azure

Vous pouvez élever l’[accès pour gérer tous les abonnements et groupes d’administration Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json) d’un utilisateur.

Après avoir élevé l’accès :

1. Accédez à **Toutes les services** > **Réservation** pour afficher toutes les réservations qui se trouvent dans le locataire.
1. Pour apporter des modifications à une réservation, ajoutez vous-même en tant que propriétaire de l’ordre de réservation à l’aide du contrôle d’accès (IAM).

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>Accorder aux utilisateurs l’accès Azure RBAC à des réservations individuelles

Les utilisateurs qui disposent d’un accès propriétaire sur les réservations et les administrateurs de facturation peuvent déléguer la gestion des accès pour un ordre de réservation individuel.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Tous les services** > **Réservation** pour afficher la liste des réservations auxquelles vous avez accès.
1. Sélectionnez la réservation pour laquelle vous souhaitez déléguer l’accès à d’autres utilisateurs.
1. Dans Détails de la réservation, sélectionnez l’ordre de réservation.
1. Sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter une attribution de rôle** > **Rôle** > **Propriétaire**. Si vous souhaitez accorder un accès limité, sélectionnez un autre rôle.
1. Tapez l’adresse e-mail de l’utilisateur à ajouter comme propriétaire.
1. Sélectionnez l’utilisateur, puis **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les réservations Azure](manage-reserved-vm-instance.md)