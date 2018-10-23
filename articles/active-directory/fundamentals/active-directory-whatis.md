---
title: Qu’est-ce qu’Azure Active Directory ?(Azure AD) | Microsoft Docs
description: Découvrez comment utiliser Azure Active Directory pour étendre vos identités locales existantes dans le cloud ou développer des applications intégrées Azure AD.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 09/13/2018
ms.custom: it-pro
ms.openlocfilehash: 08d32a99e2f1a77bcc7a5900b47e6771c33ec356
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393319"
---
# <a name="what-is-azure-active-directory"></a>Qu’est-ce qu’Azure Active Directory ?
Azure Active Directory (Azure AD) est le service cloud Microsoft multilocataire de gestion des identités et des annuaires. Azure AD réunit les principaux services d’annuaire, la gestion des accès aux applications et la protection des identités dans une solution unique, offrant une plateforme basée sur des standards qui permet aux développeurs de contrôler l’accès à leurs applications en fonction d’une stratégie et de règles centralisées.

![Pile Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="benefits-of-azure-ad"></a>Avantages d’Azure AD
Azure AD vous permet de :

-   Créer et gérer une identité unique pour chaque utilisateur dans toute votre entreprise, en préservant la synchronisation des utilisateurs, des groupes et des appareils avec [Azure AD Connect](../connect/active-directory-aadconnect.md).

-   Fournir un accès à vos applications par le biais de l’authentification unique, notamment à des milliers d’applications SaaS pré-intégrées, et un accès à distance plus sécurisé aux applications SaaS locales à l’aide du [proxy d’application Azure AD](../manage-apps/application-proxy.md).

-   Sécuriser l’accès aux applications avec des stratégies d’[authentification multifacteur](../authentication/concept-mfa-howitworks.md) basées sur des règles, aussi bien pour les applications locales que pour les applications cloud.

-   améliorer la productivité des utilisateurs avec la [réinitialisation des mots de passe en libre-service](../user-help/user-help-reset-password.md) et les demandes d’accès aux applications et de groupe à l’aide du [portail MyApps](../user-help/active-directory-saas-access-panel-introduction.md) ;

-   tirer parti de la [haute disponibilité et fiabilité](https://docs.microsoft.com/azure/architecture/checklist/availability) d’une solution d’entreprise de gestion des identités et des accès basée sur le cloud de niveau international.

## <a name="who-uses-azure-ad"></a>Qui utilise Azure AD
Azure AD est destiné aux administrateurs informatiques, aux développeurs d’applications et aux utilisateurs d’Office 365, Azure ou Dynamics CRM Online.

- **Les administrateurs informatiques.** Azure AD fournit une solution plus sécurisée pour votre organisation via l’utilisation d’une gestion des identités et d’un accès avec authentification unique renforcés à des milliers [d’applications SaaS cloud](../saas-apps/tutorial-list.md) et d’applications locales. Via ces applications, vous bénéficiez également d’une sécurité des applications cloud, d’un accès transparent, d’une meilleure collaboration et d’une automatisation du cycle de vie des identités de vos utilisateurs pour améliorer la sécurité et la conformité.

    De plus, avec [Azure AD Connect](../connect/active-directory-aadconnect-get-started-express.md), vous pouvez intégrer Azure AD à une instance existante de Windows Server Active Directory, ce qui donne à votre organisation la possibilité d’exploiter ses investissements d’identité locale pour gérer l’accès aux applications SaaS basées sur le cloud.

- **Pour les développeurs PHP.** Azure AD vous permet de vous concentrer sur la création de vos applications en fournissant l’intégration d’une solution de gestion des identités utilisée par des millions d’organisations sur toute la planète.

- **Pour les clients Office 365, Azure ou Dynamics CRM Online.** Vous utilisez déjà Azure AD. Chaque locataire Office 365, Azure et Dynamics CRM Online est en fait un locataire Azure AD, ce qui vous permet de commencer immédiatement à gérer votre accès utilisateur à vos applications cloud intégrées.

## <a name="how-reliable-is-azure-ad"></a>Quelle est la fiabilité d’Azure AD ?
La conception d’Azure AD à architecture multilocataire, répartie géographiquement et à haute disponibilité signifie que vous pouvez vous reposer sur cette solution pour répondre à vos besoins les plus critiques. Azure AD s’étend sur 28 centres de données dans le monde entier, avec le basculement automatique. Cela signifie que même si un centre de données tombe en panne, les copies de vos données d’annuaire sont actives dans au moins deux autres centres de données situés dans des régions différentes, et disponibles pour un accès immédiat.

Pour plus d’informations sur les contrats de niveau de service, consultez [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Choisir une édition
Tous les services d’entreprise Microsoft Online reposent sur Azure AD pour l’authentification et d’autres besoins d’identification. Si vous vous abonnez à un service d’entreprise Microsoft Online (par exemple, Office 365 ou Microsoft Azure), vous disposez automatiquement d’Azure AD avec un accès à toutes les fonctionnalités gratuites. L’édition Azure Active Directory Free vous permet de gérer les utilisateurs et les groupes, d’effectuer une synchronisation avec les annuaires locaux, d’obtenir l’authentification unique entre Azure, Office 365 et des milliers d’applications SaaS populaires, telles que Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox, etc. 

Pour enrichir votre implémentation Azure AD, vous pouvez aussi ajouter des fonctionnalités payantes en mettant à niveau vers les éditions Azure Active Directory Basic, Premium P1 ou Premium P2. Les versions payantes Azure AD s’appuient sur votre annuaire gratuit existant, et vous offrent de nombreuses fonctionnalités de classe entreprise qui couvrent le libre-service, la supervision améliorée, les rapports de sécurité, l’authentification multifacteur MFA (Multi-Factor Authentication) et l’accès sécurisé pour votre personnel mobile.

> [!NOTE]
> Pour les options de tarification de ces éditions, consultez la [tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Les éditions Azure Active Directory, Premium P1, Premium P2 et Azure Active Directory de base ne sont actuellement pas prises en charge en Chine. Pour plus d’informations sur la tarification Azure AD, vous pouvez contacter le Forum Azure Active Directory.

- **Azure Active Directory Basic.** Destinée aux employés dont les besoins se situent essentiellement dans le cloud, cette édition fournit un accès aux applications tournées vers le cloud ainsi que des solutions de gestion des identités en libre-service. Avec l’édition Basic, vous disposez de fonctionnalités qui améliorent votre productivité et réduisent vos coûts, comme la gestion de l’accès basée sur les groupes, la réinitialisation du mot de passe en libre-service pour les applications cloud et un proxy d’application Azure Active Directory (pour publier des applications web locales à l’aide d’Azure AD). Le tout, avec un contrat SLA pour entreprises qui fournit 99,9 % de disponibilité.

- **Azure Active Directory Premium P1.** Destinée aux organisations ayant des besoins exigeants en termes de gestion des accès et des identités, l’édition Azure Active Directory Premium ajoute de riches fonctionnalités de gestion des identités au niveau de l’entreprise. Elle permet aussi aux utilisateurs hybrides d’accéder de façon fluide aux fonctionnalités locales et dans le cloud. Cette édition inclut tout ce dont vous avez besoin pour les travailleurs de l’information et les administrateurs d’identités dans les environnements hybrides grâce à un accès inter-applications, à une gestion des identités et de l’accès en libre-service (IAM), à la protection des identités et à la sécurité dans le cloud. Elle prend en charge des ressources d'administration et de délégation avancées telles que des groupes dynamiques et la gestion de groupes en libre-service. Elle inclut Microsoft Identity Manager (une suite de gestion locale des identités et des accès) et fournit des fonctionnalités d'écriture différée sur le cloud offrant aux utilisateurs locaux des solutions comme la réinitialisation en libre-service du mot de passe du service.

- **Azure Active Directory Premium P2.** Conçue autour de la protection avancée pour vos utilisateurs et administrateurs, cette nouvelle offre inclut toutes les fonctionnalités d’Azure AD Premium P1, ainsi que les solutions Identity Protection et Privileged Identity Management. Azure Active Directory Identity Protection exploite des milliards de signaux pour fournir à vos applications et données d’entreprise critiques un accès conditionnel basé sur les risques. Nous vous aidons également à gérer et protéger les comptes privilégiés avec Azure Active Directory Privileged Identity Management afin que vous puissiez détecter, restreindre et surveiller les administrateurs et leur accès aux ressources, et fournir un accès juste-à-temps quand cela est nécessaire.  

> [!NOTE]
> Plusieurs fonctionnalités Azure Active Directory sont également disponibles par le biais des éditions de « paiement à l’utilisation » :<ul><li>**Azure Active Directory B2C.** La solution de gestion des identités et des accès pour vos applications grand public. Pour plus d’informations, consultez [Documentation Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).</li><li>**Azure Multi-Factor Authentication.** Fournisseur par utilisateur ou par authentification utilisé. Pour plus d’informations, consultez [Présentation d’Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md).

## <a name="as-an-admin-how-do-i-get-started"></a>En tant qu’administrateur, par quoi commencer ?
Inscrivez-vous pour un essai gratuit de 30 jours et déployez votre première solution cloud en consultant [Version d’évaluation d’Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="as-a-developer-how-do-i-get-started"></a>En tant que développeur, par quoi commencer ?
Inscrivez-vous pour un essai gratuit de 30 jours et commencez à intégrer vos applications à Azure AD en consultant [Version d’évaluation d’Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Pour plus d’informations, vous pouvez également consulter le [Guide du développeur](../develop/azure-ad-developers-guide.md) pour Azure Active Directory.

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les principes fondamentaux de la gestion des identités et des accès Azure](identity-fundamentals.md).

- [Intégrer Azure AD à Windows Server Active Directory](../hybrid/how-to-connect-install-express.md).
