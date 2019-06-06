---
title: Azure AD Cloud régie gestion des charges de travail en local - Azure
description: Cette rubrique décrit la gestion de cloud régi pour les charges de travail en local.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b7e54f6acfe1cbbe6e46fe92d132ebdaa91ff33
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742337"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Comment Azure AD offre Cloud régie gestion pour les charges de travail en local

Azure Active Directory (Azure AD) est une identité complète comme une solution de service (IDaaS) utilisée par des millions d’organisations qui couvrent tous les aspects de l’identité, la gestion des accès et sécurité. Azure AD conserve les identités des utilisateurs plus d’un milliard et permet aux utilisateurs de se connecter et accéder en toute sécurité à la fois :

* Ressources externes, telles que Microsoft Office 365, le portail Azure et des milliers d’autres applications Software-as-a-Service (SaaS).
* Ressources internes, telles que les applications sur le réseau d’entreprise et l’intranet, ainsi que toutes les applications cloud développé par cette organisation d’une organisation.

Les organisations peuvent utiliser Azure AD si elles sont « cloud pur », ou comme un « hybride » s’ils disposent de déploiement sur site des charges de travail. Un déploiement hybride d’Azure AD peut faire partie d’une stratégie pour une organisation à migrer ses ressources informatiques vers le cloud, ou pour continuer à intégrer l’infrastructure sur site existante en même temps que les nouveaux services cloud.

Historiquement, les organisations « hybride » ont vu Azure AD comme une extension de leurs infrastructure locale. Dans ces déploiements, l’administration de gouvernance des identités sur site, Windows Server Active Directory ou autres systèmes d’annuaire en interne, sont les points de contrôle, et les utilisateurs et groupes sont synchronisés à partir de ces systèmes dans un répertoire de cloud tels qu’Azure AD. Une fois ces identités dans le cloud, ils peuvent mettre à disposition Office 365, Azure et autres applications.

![Cycle de vie des identités](media/cloud-governed-management-for-on-premises//image1.png)

Comme les organisations déplacent plus de leur infrastructure informatique, ainsi que leurs applications vers le cloud, sont nombreux à rechercher pour les améliorer la sécurité et les fonctionnalités de gestion simplifiée de gestion des identités en tant que service. Les fonctionnalités IDaaS assurée par le cloud dans Azure AD accélèrent la transition pour la gestion régie du cloud en fournissant les solutions et les fonctionnalités qui permettent aux organisations d’adopter rapidement et de se déplacer plus leur gestion des identités à partir de locaux traditionnels systèmes à Azure AD, tout en continuant à prendre en charge des applications existantes ainsi que la nouvelle.

Ce document décrit la stratégie de Microsoft pour hybride IDaaS et décrit comment les organisations peuvent utiliser Azure AD pour leurs applications existantes.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>L’approche d’Azure AD dans le cloud de gestion des identités régis

Lorsque les organisations quittent vers le cloud, dont ils ont besoin de garanties, qu’ils disposent de contrôles au fil de leur environnement complet - davantage de sécurité et une visibilité plus des activités, pris en charge par l’automatisation et insights proactive. «**Cloud régie gestion**» décrit comment les organisations limitant et en définissant leurs utilisateurs, les applications, les groupes et les appareils à partir du cloud.

Dans ce monde moderne, les organisations doivent être en mesure de gérer efficacement à l’échelle, en raison de la prolifération d’applications SaaS et le rôle croissant de collaboration et d’identités externes. Le nouveau paysage de risque du nuage signifie une organisation doit être plus réactive, un acteur malveillant qui compromet un utilisateur de cloud peut affecter les applications cloud et locales.

En particulier, hybride, les organisations doivent pouvoir déléguer et automatiser des tâches qui historiquement informatique fait manuellement. Pour automatiser des tâches, dont ils ont besoin d’une API et les processus qui orchestrent le cycle de vie de différentes ressources liées à l’identité (utilisateurs, groupes, applications, appareils), afin qu’ils peuvent déléguer la gestion quotidienne de ces ressources à plusieurs personnes en dehors de service informatique de base. Azure AD répond à ces exigences via la gestion de compte d’utilisateur et l’authentification native pour les utilisateurs sans avoir besoin en local infrastructure d’identité. Ne pas la création d’infrastructure sur site peut bénéficier les organisations qui ont de nouvelles communautés d’utilisateurs, telles que des partenaires commerciaux, qui ne provient pas leur annuaire local, mais dont la gestion des accès est essentielle pour atteindre les résultats de votre entreprise.

En outre, gestion n’est pas terminée sans gouvernance---et gouvernance dans ce nouveau monde est intégrée au système d’identité plutôt que d’un module complémentaire. Gouvernance des identités permet aux entreprises la possibilité de gérer l’identité et d’accès du cycle de vie entre les employés, les partenaires commerciaux et les fournisseurs et les services et applications.

Incorporant la gouvernance des identités rend plus facile de permettre aux entreprises de transition vers le cloud de gestion régie, permet l’informatique à l’échelle, résout les nouveaux défis avec invités et fournit des insights plus approfondis et l’automatisation à ce que les clients avaient avec infrastructure locale. Gouvernance dans ce nouveau monde signifie la capacité pour une organisation dispose de la transparence, de visibilité et des contrôles appropriés sur l’accès aux ressources au sein de l’organisation. Azure AD, les opérations de sécurité et d’audit équipes disposent accéder de visibilité qui a---et qui doit avoir - à quelles ressources dans l’organisation (sur des périphériques), ce que ces utilisateurs font avec cet accès et si l’organisation a et utilise approprié contrôles pour supprimer ou de restreindre l’accès en fonction de la société ou les stratégies réglementaires.

Le nouveau modèle de gestion tire parti des organisations avec les applications SaaS et line-of-business (LOB), car ils sont plus facilement en mesure de gérer et sécuriser l’accès à ces applications. En intégrant les applications à Azure AD, les organisations pourront utiliser et gérer l’accès à la fois cloud et en local provient identités de manière cohérente. Gestion du cycle de vie des applications devienne plus automatisée, et Azure AD fournit un éclairage détaillé sur l’utilisation des applications qui n’était pas facilement réalisable dans Gestion des identités sur site. Via Azure AD, les groupes Office 365 et des fonctionnalités de libre-service des équipes, les organisations peuvent facilement créer des groupes de gestion de l’accès et de collaboration et ajouter ou supprimer des utilisateurs dans le cloud pour permettre la collaboration et les exigences de gestion d’accès.

En sélectionnant les fonctionnalités de droite Azure AD pour cloud gestion régie varie selon les applications à utiliser, et comment ces applications seront intégrées avec Azure AD. Les sections suivantes décrivent les approches à entreprendre pour les applications intégrées à AD et applications qui utilisent des protocoles de fédération (par exemple, SAML, OAuth ou OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Gestion de cloud régi pour les applications intégrée à Active Directory

Azure AD améliore la gestion d’une organisation en local des applications intégrées à Active Directory via l’accès à distance sécurisé et l’accès conditionnel à ces applications. En outre, Azure AD fournit également la gestion du cycle de vie des comptes et gestion des informations d’identification pour AD les comptes existants l’utilisateur, y compris :

* **Sécuriser l’accès à distance et l’accès conditionnel pour les applications locales**

Pour de nombreuses organisations, la première étape de la gestion de l’accès à partir du cloud pour le web d’intégrée à Active Directory sur site et les applications de bureau à distance consiste à déployer le [proxy d’application](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) devant ces applications afin d’offrir sécurisé accès à distance.

Après une authentification unique à Azure AD, les utilisateurs peuvent accéder aux applications cloud et locales par le biais d’une URL externe ou un portail d’applications interne. Par exemple, le Proxy d’Application fournit l’accès à distance et l’authentification unique pour Bureau à distance, SharePoint, ainsi que des applications telles que le Tableau et Qlik et des applications métier (LOB). En outre, les stratégies d’accès conditionnel peuvent inclure affichant le [conditions d’utilisation](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) et [garantissant l’utilisateur a accepté](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) avant de pouvoir accéder à une application.

![Architecture de Proxy d’application](media/cloud-governed-management-for-on-premises/image2.png)

* **Gestion du cycle de vie automatique des comptes Active Directory**

Gouvernance des identités permet aux organisations d’atteindre un équilibre entre *productivité* ---la vitesse à laquelle une personne peut avoir accès aux ressources dont ils ont besoin, tels que lorsqu’ils rejoignent l’organisation ? ---et *sécurité* ---évolution doit leur accès au fil du temps, par exemple lorsque le statut d’emploi de cette personne modifie ? La gestion du cycle de vie des identités constitue le fondement de la gouvernance des identités. Une gouvernance efficace à grande échelle implique la modernisation de l’infrastructure de gestion du cycle de vie des identités pour les applications.

Pour de nombreuses organisations, le cycle de vie des identités pour les employés est lié à la représentation sous forme de cet utilisateur dans un système de gestion du capital humain (HCM). Pour les organisations à l’aide de la journée de travail en tant que leur système HCM, Azure AD pour garantir des comptes d’utilisateur dans AD [automatiquement mis en service et de mise hors service pour les travailleurs dans Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). C’est le cas prospects à la productivité améliorée grâce à l’automatisation de birthright comptes et gère les risques en vous assurant d’application l’accès est automatiquement mis à jour quand un utilisateur modifie des rôles ou quitte l’organisation. L’approvisionnement des utilisateurs pilotée par Workday [plan de déploiement](https://aka.ms/WorkdayDeploymentPlan) est un guide pas à pas qui guide les organisations via la meilleure implémentation de pratiques de Workday à Active Directory l’approvisionnement de solution dans un processus en cinq étapes.

Azure AD Premium inclut également Microsoft Identity Manager, ce qui peut importer des enregistrements à partir d’autres systèmes HCM locaux, notamment SAP, Oracle eBusiness et Oracle PeopleSoft.

Business-to-business collaboration nécessite plus en plus accorder l’accès aux personnes extérieures à votre organisation. [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) collaboration permet aux organisations de partager en toute sécurité leurs applications et services avec les utilisateurs invités et des partenaires externes tout en conservant le contrôle sur leurs propres données d’entreprise.

Azure AD peut [créer automatiquement des comptes dans Active Directory pour les utilisateurs invités](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) en fonction des besoins, l’activation des invités professionnels accéder aux applications sur site intégrée à Active Directory sans avoir besoin d’un autre mot de passe. Les organisations peuvent configurer [des stratégies de l’authentification multifacteur (MFA) pour l’utilisateur invité](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s afin de l’authentification Multifacteur vérifications sont effectuées au cours de l’authentification de proxy d’application. En outre, les [révisions d’accès](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) qui sont effectuées sur le cloud B2B, les utilisateurs s’appliquent aux utilisateurs locaux. Par exemple, si l’utilisateur de cloud est supprimé par le biais des stratégies de gestion du cycle de vie, l’utilisateur local est également supprimé.

**Gestion des comptes Active Directory des informations d’identification** Azure AD du libre-service réinitialisation de mot de passe permet aux utilisateurs qui ont oublié leur mot de passe pour s’authentifier à nouveau et de réinitialiser leurs mots de passe avec les mots de passe modifiés [écrites dans Active Directory sur site](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback). Le processus de réinitialisation de mot de passe peut également utiliser les stratégies de mot de passe Active Directory en local : Lorsqu’un utilisateur réinitialise son mot de passe, il est vérifié pour assurer qu’il répond à la stratégie d’Active Directory en local avant de le valider à ce répertoire. Réinitialiser le mot de passe libre-service [plan de déploiement](https://aka.ms/deploymentplans/sspr) décrit les meilleures pratiques de déploiement de mot de passe libre-service, réinitialisation aux utilisateurs via le web et des expériences intégrées à Windows.

![Architecture d’Azure AD SSPR](media/cloud-governed-management-for-on-premises/image3.png)

Enfin, pour les organisations qui permettent aux utilisateurs de modifier leurs mots de passe dans Active Directory, AD peut être configuré pour utiliser la même stratégie de mot de passe à l’aide de l’organisation dans Azure AD via le [fonctionnalité de protection de mot de passe Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), actuellement dans version préliminaire publique.

Lorsqu’une entreprise s’apprête à déplacer une application intégrée à Active Directory vers le cloud en déplaçant le système d’exploitation hébergeant l’application vers Azure, [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) fournit des services de domaine AD compatibles (comme la jonction de domaine, stratégie de groupe, LDAP et Kerberos/NTLM authentication). Azure AD Domain Services s’intègre avec le locataire Azure AD existant de l’organisation, ce qui permet aux utilisateurs de se connecter à l’aide de leurs informations d’identification d’entreprise. En outre, les comptes d’utilisateur et les groupes existants peuvent être utilisés pour sécuriser l’accès aux ressources, assurant un plus lisse « lift-and-shift » des ressources locales pour les services d’infrastructure Azure.

![Services de domaine Azure AD](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Gestion de cloud régi pour les applications de fédération local

Pour une organisation qui utilise déjà un fournisseur d’identité en local, d’applications vers Azure AD permet un accès plus sécurisé et une expérience d’administration plus facile pour la gestion de la fédération. Azure AD permet la configuration des contrôles d’accès granulaires par application, y compris Azure multi-Factor Authentication, à l’aide de l’accès conditionnel Azure AD. Azure AD prend en charge plus de fonctionnalités, y compris les certificats de signature de jeton spécifique à l’application et les dates d’expiration de certificat configurable. Ces fonctionnalités, des outils et conseils permettent aux organisations de mettre hors service leurs fournisseurs d’identité en local. Propre à Microsoft IT, pour obtenir un exemple, a été déplacé 17,987 applications à partir interne Active Directory Federation Services de Microsoft (ADFS) vers Azure AD.

![Évolution de AD Azure](media/cloud-governed-management-for-on-premises/image5.png)

Pour commencer la migration des applications fédérées à Azure AD comme fournisseur d’identité, reportez-vous à https://aka.ms/migrateapps qui inclut des liens vers :

* Le livre blanc [migration de vos Applications à Azure Active Directory](https://aka.ms/migrateapps/whitepaper), qui présente les avantages de la migration et explique comment planifier la migration en quatre phases clairement expliqués : découverte, classification, migration, et gestion continue. Vous serez guidé dans comment pensez au processus et de diviser votre projet en éléments faciles à utiliser. Le document contient des liens vers des ressources importantes qui vous aideront tout au long du processus.

* Le guide de solution [migration authentification d’Application à partir d’Active Directory Federation Services à Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) explore plus en détail les mêmes quatre phases de planification et de l’exécution d’un projet de migration d’application . Dans ce guide, vous allez apprendre à appliquer ces phases à l’objectif spécifique de déplacement d’une application à partir d’Active Directory Federation Services (ADFS) à Azure AD.

* Le [Script de préparation Active Directory Federation Services Migration](https://aka.ms/migrateapps/adfstools) peut être exécuté sur des serveurs Active Directory Federation Services (ADFS) sur site existants pour déterminer l’état de préparation des applications pour la migration vers Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Gestion de l’accès en continu entre les applications cloud et locales

Les organisations ont besoin d’un processus pour gérer l’accès qui est évolutif. Les utilisateurs continuer à s’accumuler les droits d’accès et de vous retrouver avec au-delà de ce qui a été initialement configuré pour les. En outre, les entreprises doivent être capables d’évoluer efficacement de développer et appliquer la stratégie d’accès et des contrôles de manière continue.

En règle générale, le service informatique délègue aux décideurs d’entreprise la prise de décision en matière d’approbation d’accès. De plus, le service informatique peut impliquer les utilisateurs eux-mêmes. Par exemple, les utilisateurs accédant à des données client confidentielles dans l’application marketing d’une société en Europe doivent connaître les stratégies de l’entreprise. Les utilisateurs invités peuvent être sans connaître les exigences de gestion des données d’une organisation à laquelle ils avez été invités.

L’entreprise peut automatiser le processus de cycle de vie d’accès via des technologies telles que [groupes dynamiques](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership), couplée avec l’approvisionnement des utilisateurs pour [applications SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list), ou [applications intégré avec le système pour Cross-Domain Identity Management (SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)) standard. Les organisations peuvent également contrôler les [utilisateurs invités ont accès aux applications locales](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises). Ces droits d’accès peuvent ensuite être révisés régulièrement par le biais de [révisions d’accès Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) récurrentes.

## <a name="future-directions"></a>Orientations futures

Dans les environnements hybrides, la stratégie de Microsoft consiste à activer les déploiements où le **cloud est le plan de contrôle pour l’identité**et des répertoires locaux et des autres systèmes d’identité, telles que Active Directory et d’autres en local les applications, sont la cible pour l’approvisionnement des utilisateurs ayant un accès. Cette stratégie continuera à vous assurer de droits, des identités et des accès dans les applications et les charges de travail qui s’appuient sur les. Dans cet état de fin, les organisations pourront à la productivité de l’utilisateur final de lecteur entièrement à partir du cloud.

![Architecture Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la prise en main sur ce parcours, consultez les plans de déploiement Azure AD, situés dans <https://aka.ms/deploymentplans> . Ils fournissent des conseils de bout en bout sur le déploiement des fonctionnalités Azure Active Directory (Azure AD). Chaque plan explique la valeur commerciale, planification des considérations, conception et les procédures opérationnelles nécessaires pour déployer avec succès des fonctionnalités d’Azure AD courantes. Microsoft met continuellement à jour les plans de déploiement avec les meilleures pratiques apprises à partir des déploiements de client et d’autres commentaires lorsque nous ajoutons de nouvelles fonctionnalités pour la gestion à partir du cloud avec Azure AD.
