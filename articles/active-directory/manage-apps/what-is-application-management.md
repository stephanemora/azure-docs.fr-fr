---
title: Gestion des applications avec Azure Active Directory | Microsoft Docs
description: Cet article présente les avantages de l’intégration d’Azure Active Directory avec vos applications locales, cloud et SaaS.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/30/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 657527272ccf8b5f69764052a2385ceec57ddc03
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826030"
---
# <a name="application-management-with-azure-active-directory"></a>Gestion des applications avec Azure Active Directory

Azure Active Directory (Azure AD) fournit un accès sécurisé et fluide aux applications cloud et locales. Les utilisateurs peuvent se connecter une seule fois pour accéder à Office 365 et à d’autres applications métier de Microsoft, à des applications SaaS (software as a service), à des applications locales et des applications cœur de métier. Réduisez les coûts d’administration en automatisant le provisionnement des utilisateurs. Utilisez les stratégies d’authentification multifacteur et d’accès conditionnel pour fournir un accès sécurisé aux applications.

![Applications fédérées via Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Pourquoi gérer des applications avec une solution cloud ?

Souvent, les organisations comptent des centaines d’applications qui sont indispensables aux utilisateurs pour effectuer leur travail. Les utilisateurs accèdent à ces applications à partir de nombreux appareils et emplacements. De nouvelles applications sont ajoutées, développées et supprimées tous les jours. Avec autant d’applications et de points d’accès, il est plus important que jamais d’utiliser une solution basée sur le cloud pour gérer l’accès utilisateur de toutes ces applications.

## <a name="manage-risk-with-conditional-access-policies"></a>Gérer les risques avec les stratégies d’accès conditionnel
Le couplage de l’authentification unique (SSO) Azure AD avec les stratégies d’accès conditionnel offre des niveaux élevés de sécurité d’accès aux applications. Les fonctionnalités de sécurité comprennent la protection des identités à l’échelle du cloud, le contrôle d’accès en fonction des risques, l’authentification multifacteur native et les stratégies d’accès conditionnel. Ces fonctionnalités offrent des stratégies de contrôle précises, basées sur les applications ou sur les groupes qui ont besoin d’un niveau plus élevé de sécurité.

## <a name="improve-productivity-with-single-sign-on"></a>Améliorer la productivité avec l’authentification unique
L’activation de l’authentification unique (SSO) sur les applications et Office 365 offre une expérience de connexion supérieure aux utilisateurs en réduisant ou en éliminant des invites de connexion. L’environnement utilisateur semble plus cohésif et est moins perturbant sans la multiplication des invites ou l’obligation de gérer plusieurs mots de passe. Le groupe professionnel peut gérer et approuver l’accès au moyen de l’appartenance dynamique et en libre-service. Autoriser les bonnes personnes dans l’entreprise à gérer l’accès à une application améliore la sécurité du système des identités.

SSO améliore la sécurité. *Sans l’authentification unique*, les administrateurs doivent créer et mettre à jour des comptes d’utilisateur pour chaque application séparément, ce qui demande du temps. Les utilisateurs doivent aussi conserver plusieurs informations d’identification différentes pour accéder à leurs applications. Par conséquent, ils ont tendance à noter leurs mots de passe ou à utiliser d’autres solutions de gestion de mots de passe, ce qui crée d’autres risques sur la sécurité des données. 

## <a name="address-governance-and-compliance"></a>Assurer la gouvernance et la conformité
Avec Azure AD, vous pouvez superviser les connexions aux applications par le biais de rapports tirant parti des outils SIEM (Security Incident and Event Monitoring). Vous pouvez accéder aux rapports à partir du portail ou des API. Auditez par programmation toute personne qui a accès à vos applications, puis supprimez l’accès aux utilisateurs inactifs par l’intermédiaire des révisions d’accès.

## <a name="manage-costs"></a>Gérer les coûts
En migrant vers Azure AD, vous pouvez réduire les coûts et supprimer la contrainte que représente la gestion de votre infrastructure locale. Azure AD fournit également un accès en libre-service aux applications, ce qui fait gagner du temps aux administrateurs et aux utilisateurs. L’authentification unique élimine les mots de passe propres à l’application. L’authentification unique permet de réduire les coûts liés à la réinitialisation des mots de passe des applications et à la perte de productivité lors de la récupération des mots de passe.

