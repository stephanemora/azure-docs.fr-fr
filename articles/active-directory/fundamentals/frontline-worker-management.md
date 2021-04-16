---
title: Gestion des travailleurs de première ligne – Azure Active Directory
description: En savoir plus sur les capacités de gestion des travailleurs de première ligne fournies par le biais du portail Mon personnel.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 32cee4ca0558166c44ff83c5ce9d61360e79e535
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975663"
---
# <a name="frontline-worker-management"></a>Gestion des travailleurs de première ligne

Les travailleurs de première ligne représentent plus de 80 % de la main-d’œuvre mondiale. Pourtant, en raison de l’échelle élevée, du taux important de renouvellement du personnel et des processus fragmentés, les travailleurs de première ligne manquent souvent d’outils pour rendre leur travail exigeant un peu plus facile. La gestion des travailleurs de première ligne apporte la transformation numérique à l’ensemble de la main-d’œuvre de première ligne. Il peut s’agir de directeurs, de travailleurs de première ligne, de personnel d’exploitation et de personnel informatique.

La gestion des travailleurs de première ligne autonomise la main-d’œuvre de première ligne en facilitant l’accomplissement des activités suivantes :
- Rationalisation des tâches informatiques courantes avec Mon personnel
- Intégration facile des travailleurs de première ligne grâce à une authentification simplifiée
- Approvisionnement en toute transparence des appareils partagés et déconnexion sécurisée des travailleurs de première ligne

## <a name="delegated-user-management-through-my-staff"></a>Gestion déléguée des utilisateurs via Mon personnel

Azure Active Directory (Azure AD) offre la possibilité de déléguer la gestion des utilisateurs aux directeurs de première ligne via le [portail Mon personnel](../roles/my-staff-configure.md), ce qui permet de gagner un temps précieux et de réduire les risques. En permettant la réinitialisation simplifiée des mots de passe et la gestion des téléphones directement à partir du magasin ou de l’usine, les directeurs peuvent accorder l’accès aux employés sans faire passer la demande par le support technique, le service informatique ou l’équipe d’exploitation.

![Gestion déléguée des utilisateurs dans le portail Mon personnel](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>Accélération de l’intégration grâce à une authentification simplifiée

Mon personnel permet également aux directeurs de première ligne d’inscrire les numéros de téléphone des membres de leur équipe pour une [connexion par SMS](../authentication/howto-authentication-sms-signin.md). Dans de nombreux secteurs verticaux, les travailleurs de première ligne conservent une combinaison locale de nom d’utilisateur et de mot de passe, une solution souvent fastidieuse, coûteuse et sujette aux erreurs. Lorsque le service informatique active l’authentification par SMS, les travailleurs de première ligne peuvent se connecter avec l’[authentification unique](../manage-apps/what-is-single-sign-on.md) pour Microsoft Teams et d’autres applications en utilisant simplement leur numéro de téléphone et un code secret à usage unique (OTP) envoyé par SMS. Les travailleurs de première ligne peuvent ainsi se connecter de manière simple et sécurisée et accéder rapidement aux applications dont ils ont le plus besoin.

![Connexion par SMS](media/concept-fundamentals-frontline-worker/sms-signin.png)

Les directeurs de première ligne peuvent également utiliser l’application Managed Home Screen (MHS) pour permettre aux travailleurs d’avoir accès à un ensemble spécifique d’applications sur leurs appareils Android dédiés inscrits à Intune. Les appareils dédiés sont inscrits auprès du [mode d’appareil partagé d’Azure AD](../develop/msal-shared-devices.md). Lorsqu’elle est configurée en mode plein écran à plusieurs applications dans la console Microsoft Endpoint Manager (MEM), l’application MHS est automatiquement lancée comme écran d’accueil par défaut sur l’appareil et apparaît à l’utilisateur final comme le *seul* écran d’accueil. Pour en savoir plus, renseignez-vous sur la façon de [configurer l’application Microsoft Managed Home Screen pour Android Enterprise](/mem/intune/apps/app-configuration-managed-home-screen-app).

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>Déconnexion sécurisée des travailleurs de première ligne à partir d’appareils partagés

De nombreuses entreprises utilisent des appareils partagés afin que les travailleurs de première ligne puissent gérer les stocks et effectuer des transactions sur les points de vente sans avoir à assumer la charge informatique liée à l’approvisionnement et au suivi des appareils individuels. Grâce à la déconnexion des appareils partagés, il est facile pour un travailleur de première ligne de se déconnecter en toute sécurité de toutes les applications de n’importe quel appareil partagé avant de le remettre à un hub ou de le passer à un coéquipier pour l’équipe suivante. Microsoft Teams est l’une des applications actuellement prises en charge sur les appareils partagés. Elle permet aux travailleurs de première ligne de visualiser les tâches qui leur sont attribuées. Lorsqu’un travailleur se déconnecte d’un appareil partagé, Intune et Azure AD effacent toutes les données de l’entreprise afin que l’appareil puisse être remis en toute sécurité au collaborateur suivant. Vous pouvez choisir d’intégrer cette capacité dans toutes les applications [iOS](../develop/msal-ios-shared-devices.md) et [Android](../develop/msal-android-shared-devices.md) de votre entreprise à l’aide de la [bibliothèque d’authentification Microsoft](../develop/msal-overview.md).

![Déconnexion des appareils partagés](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la gestion déléguée des utilisateurs, consultez la [documentation relative aux utilisateurs de Mon personnel](../user-help/my-staff-team-manager.md).
- Pour l’approvisionnement des utilisateurs entrants à partir de SAP SuccessFactors, consultez le tutoriel sur la [configuration de l’approvisionnement d’utilisateurs SAP SuccessFactors vers Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
- Pour l’approvisionnement des utilisateurs entrants à partir de Workday, consultez le tutoriel sur la [configuration de Workday pour l’approvisionnement automatique d’utilisateurs](../saas-apps/workday-inbound-tutorial.md).
