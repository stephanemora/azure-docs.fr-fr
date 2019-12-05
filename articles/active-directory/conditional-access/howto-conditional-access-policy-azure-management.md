---
title: Accès conditionnel - Exiger l’authentification multifacteur pour la gestion Azure - Azure Active Directory
description: Créer une stratégie d’accès conditionnel personnalisée pour exiger l’authentification multifacteur pour les tâches de gestion Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d0a1f56624314ff0cebe93a46027d3ca5b6a838
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803714"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Accès conditionnel : Exiger MFA pour la gestion Azure

Les organisations utilisent divers services Azure et les gèrent à partir d’outils reposant sur Azure Resource Manager :

* Portail Azure
* Azure PowerShell
* D’Azure CLI

Ces outils peuvent offrir un accès privilégié aux ressources, ce qui permet de modifier les configurations à l’échelle de l’abonnement, les paramètres du service et la facturation des abonnements. Pour protéger ces ressources privilégiées, Microsoft recommande d’exiger l’authentification multifacteur pour tous les utilisateurs qui accèdent à ces ressources.

## <a name="user-exclusions"></a>Exclusions d’utilisateurs

Les stratégies d’accès conditionnel sont des outils puissants. Nous vous recommandons donc d’exclure les comptes suivants de votre stratégie :

* Comptes **d’accès d’urgence** ou **de secours** pour empêcher le verrouillage du compte sur l’ensemble du locataire. Dans le scénario improbable où tous les administrateurs seraient verrouillés hors de votre locataire, votre compte administratif d’accès d’urgence peut être utilisé pour vous connecter au locataire et prendre les mesures nécessaires pour récupérer l’accès.
   * Pour plus d’informations, consultez l’article [Gérer des comptes d’accès d’urgence dans Azure AD](../users-groups-roles/directory-emergency-access.md).
* Les **comptes de service** et les **principaux de service**, comme le compte de synchronisation Azure AD Connect. Les comptes de service sont des comptes non interactifs qui ne sont pas liés à un utilisateur particulier. Ils sont généralement utilisés par les services principaux et autorisent l’accès par programme aux applications. Les comptes de service doivent être exclus, car l’authentification MFA ne peut pas être effectuée par programme.
   * Si votre organisation utilise ces comptes dans des scripts ou du code, envisagez de les remplacer par des [identités managées](../managed-identities-azure-resources/overview.md). Pour contourner provisoirement le problème, vous pouvez exclure ces comptes spécifiques de la stratégie de base.

## <a name="create-a-conditional-access-policy"></a>Créer une stratégie d’accès conditionnel

Les étapes suivantes vous aideront à créer une stratégie d’accès conditionnel pour demander à ces rôles d’administration affectés d’effectuer l’authentification multifacteur.

1. Connectez-vous au **portail Microsoft Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs**.
   1. Sous **Exclure**, sélectionnez **Utilisateurs et groupes**, puis choisissez les comptes d’accès d’urgence ou de secours de votre organisation. 
   1. Sélectionnez **Terminé**.
1. Sous **Applications ou actions cloud** > **Inclure**, sélectionnez **Sélectionner les applications**, choisissez **Gestion Microsoft Azure**, puis **Sélectionner** et **Terminé**.
1. Sous **Contrôles d’accès** > **Accorder**, sélectionnez **Accorder l'accès**, **Requérir l’authentification multifacteur**, et sélectionnez **Sélectionner**.
1. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer votre stratégie.

## <a name="next-steps"></a>Étapes suivantes

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

[Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel](troubleshoot-conditional-access-what-if.md)
