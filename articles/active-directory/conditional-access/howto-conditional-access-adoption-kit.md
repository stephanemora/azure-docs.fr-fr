---
title: Kit d’adoption de l’accès conditionnel - Azure Active Directory
description: Adoption de l’accès conditionnel Azure AD pour accéder aux ressources
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387067"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Adoption de l’accès conditionnel Azure AD

Dans les environnements mobiles et cloud, les utilisateurs peuvent accéder aux ressources de votre organisation en utilisant différents appareils et applications, n’importe où. Par conséquent, contrôler les personnes autorisées à accéder à une ressource ne suffit plus. Vous pouvez contrôler qui a accès aux ressources et identifier l’emplacement de l’utilisateur, ainsi que l’appareil utilisé, entre autres choses.

Pour fournir ce contrôle, **l’accès conditionnel Azure Active Directory (AD)** vous permet de spécifier les conditions que tous les utilisateurs doivent remplir pour accéder à une application, telles que Multi-Factor Authentication (MFA). Avec des stratégies d’accès conditionnel, vous contrôlez la manière dont les utilisateurs autorisés (utilisateurs autorisés à accéder à une application cloud) accèdent aux applications cloud dans des conditions spécifiques. Pour plus d’informations, consultez [Qu’est-ce que l’accès conditionnel ?](overview.md#conditional-access-policies).

## <a name="key-benefits"></a>Principaux avantages

Les principaux avantages de l’utilisation de l’accès conditionnel Azure AD sont les suivants :

* **Augmenter la productivité :** Les stratégies d’accès conditionnel vous permettent de cibler le point où les utilisateurs sont invités à utiliser MFA, où ils ont un accès bloqué, ou le point où ils doivent utiliser un appareil de confiance. Par exemple, vous pouvez définir des stratégies, comme celle d’exiger l’utilisation de la MFA par les utilisateurs dans une application uniquement en dehors du réseau de l’entreprise. Réduire les requêtes MFA permet aux utilisateurs d’être plus productifs que s’ils avaient à l’utiliser à chaque connexion. En outre, l’accès conditionnel Azure AD vous permet de spécifier des stratégies par utilisateur, ainsi que de créer des stratégies spécifiques aux applications.
* **Gérer les risques :** L’activation des stratégies d’accès vous offre une protection des identités à l’échelle du cloud, des fonctionnalités de contrôle d’accès en fonction des risques et une prise en charge de l’authentification multifacteur native. L’association de l’accès conditionnel avec la protection des identités vous permet de définir quand l’accès à une application est bloqué ou sécurisé.
* **Assurer la gouvernance et la conformité :** Auditer les approbations et demandes d’accès pour l’application, et comprendre l’utilisation de l’application globale est plus simple avec Azure AD, car les journaux d’audit natifs sont pris en charge pour chaque demande d’accès à une application envoyée. L’audit inclut l’identité du demandeur, la date demandée, la justification métier, l’état d’approbation et l’identité de l’approbateur. Ces données sont également disponibles à partir d’une API, ce qui permettra d’importer ces données dans un système Security Information and Event Management (SIEM) de choix.
* **Gérer les coûts :** Le déplacement de stratégies d’accès vers Azure AD réduit la dépendance envers les solutions locales ou personnalisées telles qu’Active Directory Federation Services (ADFS) pour l’accès conditionnel, ce qui réduit le coût d’exécution de cette infrastructure.

## <a name="customer-case-studies"></a>Études de cas clients

Découvrez comment la plupart des organisations utilisent l’accès conditionnel Azure AD pour définir et implémenter des décisions de contrôle d’accès automatisé pour accéder aux applications cloud en fonction de conditions. Les récits présentés ci-après montrent comment ces besoins client sont respectés.

* [**Wipro** favorise la productivité mobile avec les outils de sécurité cloud Microsoft pour améliorer les engagements des clients.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Les stratégies d’accès conditionnel dans Azure AD ont permis à la société de partager des documents, des ressources et des applications avec des entités approuvées extérieures, qui peuvent utiliser leurs propres informations d’identification, tout en conservant le contrôle sur ses propres données d’entreprise.
* [**Accenture** protège sa transition vers le cloud avec Microsoft Cloud App Security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture évalue la fonctionnalité de [contrôle d’application par accès conditionnel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) de Cloud App Security, qui utilise l’accès conditionnel Azure Active Directory pour réguler l’accès aux applications selon certaines conditions. LePenske indique que cette fonctionnalité peut être utile pour, par exemple, permettre l’accès à des fichiers en lecture seule tout en interdisant les téléchargements.
* [**Aramex** delivery limited - L’entreprise de transport et de logistique mondiale crée un bureau connecté au cloud avec une solution de gestion des accès et des identités](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Garantir un accès sécurisé était particulièrement difficile avec les employés distants d’Aramex. La société applique désormais l’accès conditionnel pour permettre à ces employés distants d’accéder à leurs applications SaaS à partir de l’extérieur du réseau. La règle d’accès conditionnel décide s’il faut appliquer Multi-Factor Authentication, ce qui donne uniquement l’accès approprié aux personnes appropriées.

Pour en savoir plus sur les expériences des clients et partenaires quant à l’accès conditionnel Azure AD, consultez - [Découvrez les choses incroyables qu’Azure permet de faire](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Annonces

Azure AD bénéficie d’améliorations en continu. Pour rester à jour avec les développements les plus récents, consultez [What’s new in Azure Active Directory?](../fundamentals/whats-new.md) (Nouveautés d’Azure Active Directory)

Blogs récents de la Communauté technologique et la Division Microsoft Identity :

* 24 septembre 2018, [Accès conditionnel Azure Active Directory dans Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21 septembre 2018, [Contrôles personnalités d’accès conditionnel Azure AD disponibles en préversion publique](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21 septembre 2018, [Prise en charge de l’accès conditionnel Azure AD pour un accès limité avec Microsoft Cloud App Security désormais disponible](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 21 septembre 2018, [Accès conditionnel Azure AD : Prise en charge de Managed Browser pour les plateformes iOS/Android désormais disponible en préversion](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21 septembre 2018, [Accès conditionnel Azure AD pour les codes pays disponible en préversion publique](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21 septembre 2018, [Conditions d’utilisation d’Azure AD désormais disponibles](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Ressources d’apprentissage

Suivez les liens ci-dessous pour obtenir une vue d’ensemble du fonctionnement de l’accès conditionnel Azure AD.

* Apprenez « [Qu’est-ce que l’accès conditionnel ?](overview.md) »
* Découvrez « [Que sont les conditions dans l’accès conditionnel Azure Active Directory ?](conditions.md) »
* Découvrez « [Qu’est-ce que la condition d’emplacement dans l’accès conditionnel Azure Active Directory ?](location-condition.md) »
* Découvrez « [Que sont les contrôles d’accès dans l’accès conditionnel Azure Active Directory ?](controls.md) »
* Découvrez « [Qu’est-ce que l’outil de simulation d’accès conditionnel Azure Active Directory ?](what-if-tool.md) »
* Suivez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md)

En outre, consultez les liens suivants pour obtenir des conseils afin de protéger l’accès à tous les services qui sont intégrés à Azure Active Directory.

* [What are baseline policies?](baseline-protection.md) (Que sont les stratégies de base ?) La protection de base garantit qu’au moins le niveau de sécurité de base est activé dans votre environnement Azure Active Directory.
* [Configurations des identités et de l’accès aux appareils](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Décrit comment configurer un accès sécurisé aux services de cloud via les produits Enterprise Mobility + Security en implémentant un environnement et une configuration recommandés, y compris un ensemble prescrit de stratégies d’accès conditionnel et des fonctions connexes.
* [Azure Active Directory conditional access settings reference](technical-reference.md) (Référence des paramètres d’accès conditionnel Azure Active Directory). Découvrez :
   * Quelles applications utilisent un accès conditionnel.
   * Quels services sont activés avec un accès conditionnel.
* [Enable Azure Active Directory Conditional Access for Secure User Access](https://www.youtube.com/watch?v=eLAYBwjCGoA) (Activer l’accès conditionnel Azure Active Directory pour sécuriser l’accès des utilisateurs). Regardez cette vidéo pour découvrir comment l’accès conditionnel joue un rôle dans d’autres charges de travail de la suite Enterprise Mobility + Security.

### <a name="training-videos"></a>Vidéos de formation

**Conditional Access in Enterprise Mobility + Security** (Accès conditionnel dans Enterprise Mobility + Security)
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Device-based Conditional Access** (Accès conditionnel basé sur les appareils)
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Enable Azure Active Directory Conditional Access for Secure User Access** (Activer l’accès conditionnel Azure Active Directory pour sécuriser l’accès des utilisateurs)
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Cours en ligne

Consultez les cours sur l’accès conditionnel suivants, et bien d’autres sur [Pluralsight.com](https://www.pluralsight.com/) :

* Pluralsight.com : [Design Identity Management in Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design) (Conception de la gestion des identités dans Microsoft Azure)
   * « Ce cours explique les points clés à connaître pour concevoir votre solution de gestion des identités avec Azure AD. » L’accès conditionnel Azure AD est abordé dans le module « Using Roles and Access Control with Azure AD » (Utilisation de rôles et du contrôle d’accès avec Azure AD).

* Pluralsight.com : [Design Authentication for Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design) (Conception de l’authentification pour Microsoft Azure)
   * « Ce cours explique comment utiliser Azure AD pour répondre à toutes vos exigences en matière d’authentification cloud. » L’accès conditionnel Azure AD est abordé dans le module « Authentication Requirements for Different Scenarios » (Exigences d’authentification pour différents scénarios).

* Pluralsight.com : [Design Authorization for Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design) (Conception des autorisations pour Microsoft Azure)
   * « Ce cours présente les options d’autorisation disponibles avec Azure et Azure AD. » L’accès conditionnel Azure AD est abordé dans le module « Authorization with Azure Resource Manager and Azure AD » (Autorisation avec Azure Resource Manager et Azure AD).

### <a name="books"></a>Livres

* O’Reilly- [Implementing Azure Solutions - Second Edition](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml) (Implémentation des solutions Azure - Deuxième édition).
   * « Mettez rapidement en service les services Azure et découvrez comment les implémenter dans votre organisation. L’accès conditionnel Azure AD est abordé dans le chapitre [Deploying and Synchronizing Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml) (Déploiement et synchronisation d’Azure Active Directory). »

* Wiley- [Mastering Microsoft Azure Infrastructure Services](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298) (Maîtriser les services d’infrastructure Microsoft Azure)
   * « Voici tout ce dont vous avez besoin pour comprendre, évaluer, déployer et gérer des environnements qui utilisent Microsoft Azure. »

## <a name="white-papers"></a>Livres blancs

* Publié le 18 décembre 2018 - [Create a resilient access control management strategy with Azure Active Directory](../authentication/concept-resilient-controls.md) (Créer une stratégie de gestion du contrôle d’accès résiliente avec Azure Active Directory)
   * Ce document fournit des conseils sur les stratégies qu’une organisation peut adopter pour faire preuve de résilience et réduire le risque de verrouillage en cas d’interruption de service imprévue.

* Publié le 18 décembre 2018 - [Resources for migrating applications to Azure Active Directory](../manage-apps/migration-resources.md) (Ressources pour la migration d’applications vers Azure Active Directory)
   * Ce livre blanc inclut une liste des ressources pour vous aider à migrer l’accès et l’authentification d’applications vers Azure Active Directory (Azure AD).

* Publié le 12 juillet 2018 - [Blueprint de sécurité et de conformité Azure : Hébergement d’applications web PaaS pour les charges de travail « UK OFFICIAL »](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Les blueprints Azure sont constitués de documents de conseils et de modèles d’automatisation qui déploient des architectures cloud pour offrir des solutions à des scénarios ayant des spécifications particulières en matière d’accréditation ou de conformité.

## <a name="guidance-for-it-administrators"></a>Conseils pour les administrateurs informatiques

Connectez-vous au [portail Microsoft Azure](https://portal.azure.com/) en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel. Reportez-vous à [Autorisations des rôles d’administrateur dans Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

En tant qu’administrateur informatique, utilisez [l’accès conditionnel Azure AD](overview.md) pour obliger les utilisateurs à s’authentifier à l’aide d’Azure Multi-Factor Authentication ou à se connecter à partir d’un réseau ou d’un appareil approuvés.

Voici des liens utiles pour vous aider à bien démarrer :

* [Best practices for conditional access in Azure Active Directory](best-practices.md) (Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory)
* [Use Azure AD access reviews to manage users excluded from conditional access policies](../governance/conditional-access-exclusion.md) (Utiliser les révisions d’accès Azure AD pour gérer les utilisateurs exclus des stratégies d’accès conditionnel)
* [Guide pratique pour Planifier votre déploiement d’accès conditionnel Azure Active Directory](plan-conditional-access.md)
* [Démarrage rapide : Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel à Azure Active Directory](app-based-mfa.md)
* [Démarrage rapide : Exiger l’acceptation des conditions d’utilisation avant d’accorder l’accès à des applications cloud](require-tou.md)
* [Démarrage rapide : Bloquer l’accès lorsqu’un risque de session est détecté avec l’accès conditionnel Azure Active Directory](app-sign-in-risk.md)
* [Azure AD Conditional Access FAQs](faqs.md) (FAQ sur l’accès conditionnel Azure AD)
   * Pour toute question supplémentaire, vous pouvez également consulter le [forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * Si vous ne trouvez pas la réponse à un problème, nos équipes de support technique sont toujours disponibles pour vous aider davantage. Utilisez [Contact Microsoft support](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support) (Contacter le support Microsoft).

### <a name="tutorials"></a>Tutoriels

* [**Démarrage rapide : Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel à Azure Active Directory**](app-based-mfa.md)
   * Ce démarrage rapide montre comment configurer une stratégie d’accès conditionnel Azure AD qui exige une authentification multifacteur pour une application cloud sélectionnée dans votre environnement.

* [**Démarrage rapide : Exiger l’acceptation des conditions d’utilisation avant d’accorder l’accès à des applications cloud**](require-tou.md)
   * Ce démarrage rapide vous montre comment configurer une stratégie d’accès conditionnel Azure AD qui vous permet de demander aux utilisateurs d’accepter vos conditions d’utilisation pour accéder à une certaine application cloud de votre environnement.

* [**Démarrage rapide : Bloquer l’accès lorsqu’un risque de session est détecté avec l’accès conditionnel Azure Active Directory**](app-sign-in-risk.md)
   * Ce démarrage rapide décrit comment configurer une stratégie d’accès conditionnel qui bloque une connexion lorsqu’un niveau de risque de connexion configuré a été détecté.

* [Tutoriel : **Migrer une stratégie classique nécessitant l’authentification multifacteur dans le portail Microsoft Azure**](policy-migration-mfa.md)
   * Ce tutoriel montre comment migrer une stratégie classique qui nécessite l’authentification multifacteur pour une application cloud.

## <a name="end-user-readiness-and-communication"></a>Communication et préparation de l’utilisateur final

L’accès conditionnel utilise d’autres fonctionnalités d’Azure AD qui peuvent affecter l’expérience de l’utilisateur final. Par exemple, vous pouvez utiliser l’authentification multifacteur Azure pour activer l’authentification renforcée pour les utilisateurs. Dans ce cas, vous allez utiliser les modèles de l’utilisateur final pour Azure MFA.

## <a name="next-steps"></a>Étapes suivantes

* Commencez votre déploiement avec la [documentation de planification du déploiement de l’accès conditionnel](plan-conditional-access.md).
