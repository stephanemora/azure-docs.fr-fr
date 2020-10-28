---
title: Unités administratives dans Azure Active Directory | Microsoft Docs
description: Utilisation d'unités administratives pour une délégation plus précise des autorisations dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc7f28f9369d54c65f633bd24da5443415a80625
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378746"
---
# <a name="administrative-units-in-azure-active-directory"></a>Unités administratives dans Azure Active Directory

Cet article décrit les unités administratives dans Azure Active Directory (Azure AD). Une unité administrative est une ressource Azure AD qui peut être un conteneur pour d’autres ressources Azure AD. Une unité administrative peut contenir seulement des utilisateurs et des groupes.

Les unités administratives vous permettent d’accorder des autorisations d’administrateur limitées à un service, une région ou un autre segment de votre organisation que vous définissez. Vous pouvez utiliser des unités administratives pour déléguer des autorisations à des administrateurs régionaux ou définir une stratégie à un niveau précis. Par exemple, un administrateur de comptes d’utilisateur peut mettre à jour des informations de profil, réinitialiser des mots de passe et attribuer des licences aux utilisateurs uniquement dans son unité administrative.

Par exemple, la délégation du rôle [administrateur du support technique](permissions-reference.md#helpdesk-administrator) à des spécialistes du support d’une région peut ainsi être limité à la gestion des seuls utilisateurs de la région pour laquelle ils assurent le support.

## <a name="deployment-scenario"></a>Scénario de déploiement

La restriction de l’étendue d’administration à l’aide d’unités administratives peut être pratique dans les organisations qui sont composées de divisions indépendantes de tout type. Prenons l’exemple d’une grande université qui se compose de nombreuses écoles autonomes (école de commerce, école d’ingénieurs, etc.) ayant chacune une équipe d’administrateurs informatiques qui contrôlent les accès, gèrent les utilisateurs et définissent les stratégies pour leur école. Un administrateur central peut :

- Créer un rôle avec des autorisations d’administration seulement sur les utilisateurs Azure AD de l’unité administrative de l’école de commerce
- Créer une unité administrative pour l’école de commerce
- Affecter à l’unité administrative seulement les étudiants et le personnel de l’école de commerce
- Ajouter l’équipe informatique de l’école informatique au rôle avec son étendue

## <a name="license-requirements"></a>Conditions de licence :

L’utilisation d’unités administratives nécessite une licence Azure Active Directory Premium pour chaque administrateur d’une unité administrative et des licences Azure Active Directory gratuites pour les membres des unités administratives. Pour plus d'informations, consultez la section [Prise en main d’Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Gérer des unités administratives

Vous pouvez gérer des unités administratives en utilisant le Portail Azure, des applets de commande et des scripts PowerShell, ou Microsoft Graph. Pour plus d’informations, vous pouvez vous reporter à notre documentation :

- [Créer, supprimer, remplir et ajouter des rôles aux unités administratives](admin-units-manage.md) : Procédures complètes
- [Utilisation des unités administratives](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true) : Comment utiliser les unités administratives avec PowerShell
- [Prise en charge des unités administratives par Graph](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true) : Documentation détaillée sur Microsoft Graph pour les unités administratives.

### <a name="planning-your-administrative-units"></a>Planification de vos unités administratives

Les unités administratives peuvent être utilisées pour regrouper logiquement des ressources Azure AD. Par exemple, pour une organisation dont le service informatique est réparti dans le monde entier, il peut être judicieux de créer des unités administratives qui définissent ces limites géographiques. Dans un autre scénario où une organisation multinationale a différentes « sous-organisations » qui sont semi-autonomes dans leur fonctionnement, chaque sous-organisation peut être représentée par une unité administrative.

Les critères de création des unités administratives dépendront des exigences spécifiques de l’organisation. Les unités administratives sont une façon courante de définir la structure des services Microsoft 365. Nous vous recommandons de préparer vos unités administratives en pensant à leur utilisation dans les services Microsoft 365. Vous pouvez tirer le meilleur parti des unités administratives quand vous pouvez associer des ressources communes à Microsoft 365 sous une unité administrative.

Vous pouvez vous attendre à ce que la création d’unités administratives au sein de l’organisation passe par les étapes suivantes :

1. Adoption initiale : Votre organisation va commencer à créer des unités administratives en fonction de critères initiaux et le nombre d’unités administratives va augmenter à mesure que les critères sont affinés.
1. Élagage : Une fois que les critères sont bien définis, les unités administratives qui ne sont plus nécessaires sont supprimées.
1. Stabilisation : La structure organisationnelle est bien définie et le nombre d’unités administratives ne va pas changer de façon significative sur des périodes courtes.

## <a name="currently-supported-scenarios"></a>Scénarios actuellement pris en charge

Les administrateurs généraux ou les administrateurs de rôles privilégiés peuvent utiliser le portail Azure AD pour créer des unités administratives, ajouter des utilisateurs comme membres d’unités administratives, puis attribuer au personnel informatique des rôles d’administrateur limités à une unité. Les administrateurs limités à l’unité administrative peuvent ensuite utiliser le centre d’administration Microsoft 365 pour la gestion de base des utilisateurs de leur unité administrative.

En outre, des groupes peuvent être ajoutés en tant que membres d’une unité administrative et un administrateur de groupe limité à l’unité administrative peut les gérer avec PowerShell, Microsoft Graph et le portail Azure AD.

Le tableau ci-dessous décrit la prise en charge actuelle des scénarios d’unité administrative.

### <a name="administrative-unit-management"></a>Gestion des unités administratives

Autorisations |   MS Graph/PowerShell   | Portail Azure AD | Centre d’administration Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Création et suppression des unités administratives   |    Prise en charge    |   Prise en charge   |    Non pris en charge
Ajout et suppression de membres individuels d’une unité administrative    |   Prise en charge    |   Prise en charge   |    Non pris en charge
Ajout et suppression en bloc de membres d’unité administrative avec un fichier .csv   |    Non pris en charge     |  Prise en charge   |    Pas de prise en charge prévue
Affectation d’administrateurs limités à une unité administrative  |     Prise en charge    |   Prise en charge    |   Non pris en charge
Ajout et suppression dynamique de membres d’unités administratives en fonction d’attributs | Non pris en charge | Non pris en charge | Non pris en charge

### <a name="user-management"></a>User Management

Autorisations |   MS Graph/PowerShell   | Portail Azure AD | Centre d’administration Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Gestion limitée à une unité administrative des propriétés, des mots de passe et des licences utilisateur   |    Prise en charge     |  Prise en charge   |   Prise en charge
Blocage et déblocage des connexions utilisateur limités à une unité administrative    |   Prise en charge   |    Prise en charge   |    Prise en charge
Gestion limitée à une unité administrative des informations d’identification MFA des utilisateurs   |    Prise en charge   |   Prise en charge   |   Non pris en charge

### <a name="group-management"></a>Gestion des groupes

Autorisations |   MS Graph/PowerShell   | Portail Azure AD | Centre d’administration Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Gestion limitée à une unité administrative des propriétés et des membres des groupes     |  Prise en charge   |    Prise en charge    |  Non pris en charge
Gestion limitée à une unité administrative des licences de groupe   |    Prise en charge  |    Prise en charge   |   Non pris en charge


Les unités administratives appliquent l’étendue seulement aux autorisations de gestion. Ils n’empêchent pas les membres ou les administrateurs d’utiliser leurs [autorisations utilisateur par défaut](../fundamentals/users-default-permissions.md) pour parcourir d’autres utilisateurs, groupes ou ressources en dehors de l’unité administrative. Dans le centre d’administration Microsoft 365, les utilisateurs en dehors des unités administratives d’un administrateur limité sont filtrés, mais vous pouvez parcourir d’autres utilisateurs dans le portail Azure AD, PowerShell et d’autres services Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des unités administratives](admin-units-manage.md)
- [Gérer les utilisateurs dans les unités administratives](admin-units-add-manage-users.md)
- [Gérer les groupes dans les unités administratives](admin-units-add-manage-groups.md)
- [Attribuer des rôles limités à une unité administrative](admin-units-assign-roles.md)
