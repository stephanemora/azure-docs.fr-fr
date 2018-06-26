---
title: Accélérateurs de solution Azure IoT et Azure Active Directory | Microsoft Docs
description: Explique comment les accélérateurs de solution Azure IoT utilisent Azure Active Directory pour gérer les autorisations.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: aa4e1d1f78549a8d1955def7a1c57e61d405e347
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297136"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>Autorisations sur le site azureiotsolutions.com

## <a name="what-happens-when-you-sign-in"></a>Que se passe-t-il lorsque vous vous connectez ?

Lorsque vous vous connectez pour la première fois à [azureiotsuite.com][lnk-azureiotsolutions], le site détermine vos niveaux d’autorisation en fonction du client Azure Active Directory (AAD) et de l’abonnement Azure sélectionné.

1. Tout d’abord, afin de remplir la liste des clients qui apparaît en regard de votre nom d’utilisateur, le site recherche dans Azure les clients ADD auxquels vous appartenez. Actuellement, le site peut obtenir des jetons d’utilisateur pour un seul client. Par conséquent, lorsque vous basculez sur d’autres clients en utilisant la liste déroulante dans le coin supérieur droit, le site vous reconnecte à ce client pour obtenir les jetons pour ce client.

2. Ensuite, le site détecte à partir d’Azure, les abonnements que vous avez associés au client sélectionné. Vous pouvez voir les abonnements disponibles lorsque vous créez un nouvel accélérateur de solution.

3. Enfin, le site récupère toutes les ressources des abonnements et des groupes de ressources marquées en tant qu’accélérateurs de solution, et les ajoute aux vignettes de la page d’accueil.

Les sections suivantes décrivent les rôles qui contrôlent l’accès aux accélérateurs de solution.

## <a name="aad-roles"></a>Rôles AAD

Les rôles AAD contrôlent la possibilité de provisionner les accélérateurs de solution et de gérer leurs utilisateurs et certains services Azure dans un accélérateur de solution.

Pour plus d’informations sur les rôles d’administrateur dans ADD, consultez la page [Attribution de rôles d’administrateur dans Azure AD][lnk-aad-admin]. Cet article se concentre sur les rôles d’annuaire **Administrateur général** et **Utilisateur**, qui sont utilisés par les accélérateurs de solution.

### <a name="global-administrator"></a>Administrateur général

Il peut y avoir de nombreux administrateurs généraux pour chaque locataire AAD :

* Lorsque vous créez un client AAD, vous en devenez par défaut l’administrateur.
* L’administrateur global peut provisionner des accélérateurs de solution de base et standard (un déploiement de base utilise une seule machine virtuelle Azure).

### <a name="domain-user"></a>Utilisateur de domaine

Il peut y avoir de nombreux utilisateurs de domaine pour chaque locataire AAD :

* Un utilisateur de domaine peut provisionner un accélérateur de solution de base via le site [azureiotsolutions.com][lnk-azureiotsolutions].
* Un utilisateur de domaine peut utiliser l’interface CLI pour créer un accélérateur de solution de base.

### <a name="guest-user"></a>Utilisateur invité

Il peut y avoir de nombreux utilisateurs invités pour chaque locataire AAD. Les utilisateurs invités disposent d’un ensemble limité de droits au sein du client AAD. Par conséquent, les utilisateurs invités ne peuvent pas provisionner un accélérateur de solution dans le locataire AAD.

Pour plus d’informations sur les utilisateurs et les rôles dans AAD, consultez les ressources suivantes :

* [Créer des utilisateurs dans Azure AD][lnk-create-edit-users]
* [Affecter des utilisateurs aux applications][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Rôles de l’administrateur d’abonnement Azure

Les rôles d’administration Azure permettent de contrôler la fonctionnalité de mappage d’un abonnement Azure sur un client AAD.

Pour plus d’informations sur les rôles d’administrateur Azure, consultez l’article [Guide pratique pour ajouter ou modifier le coadministrateur, l’administrateur de services fédérés et l’administrateur de compte][lnk-admin-roles].

## <a name="faq"></a>Forum Aux Questions

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Je suis un administrateur de service et je souhaite modifier le mappage d’annuaire entre mon abonnement et un client AAD spécifique. Comment mener à bien cette tâche ?

Voir [Comment ajouter un abonnement existant à votre répertoire Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Je souhaite modifier la fonctionnalité administrateur de service ou coadministrateur lors d’une connexion avec un compte de société

Voir l’article de support [Modification de la fonctionnalité Administrateur de service et Coadministrateur lors d’une connexion avec un compte de société][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Pourquoi est-ce que je reçois cette erreur ? « Votre compte n’a pas les autorisations suffisantes pour créer une solution. Veuillez contacter votre administrateur de compte ou essayer avec un autre compte. »

Examinez le schéma suivant pour obtenir des conseils :

![][img-flowchart]

> [!NOTE]
> Si l’erreur persiste après votre validation en tant qu’administrateur global sur le client AAD et que coadministrateur sur l’abonnement, demandez à votre administrateur de compte de supprimer l’utilisateur et de réattribuer les autorisations nécessaires dans l’ordre suivant : tout d’abord, ajoutez l’utilisateur en tant qu’administrateur global, puis ajoutez un utilisateur en tant que coadministrateur sur l’abonnement Azure. Si les problèmes persistent, contactez le service [Aide et support][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Pourquoi affiche-t-il cette erreur alors que j’ai un abonnement Azure ? « Vous devez avoir un abonnement Azure pour créer des solutions préconfigurées. Vous pouvez créer un compte d'essai gratuit en quelques minutes seulement. »

Si vous êtes sûr de disposer d’un abonnement Azure, validez le mappage de votre abonnement client et assurez-vous que c’est le bon client qui est sélectionné dans la liste déroulante. Si vous avez validé le locataire souhaité, suivez le schéma ci-dessus et validez le mappage de votre abonnement et de ce locataire AAD.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les accélérateurs de solution IoT, consultez [Personnaliser un accélérateur de solution][lnk-customize].

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles-azure-portal.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]: ../active-directory/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
