---
title: 'Gestion des identités et des accès : bonnes pratiques de sécurité Azure | Microsoft Docs'
description: Cet article détaille un ensemble de meilleures pratiques en matière de gestion des identités et du contrôle d’accès à l’aide de fonctionnalités Azure intégrées.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2019
ms.author: barclayn
ms.openlocfilehash: 2a669f5b46db4d5de7d1d6863b94e6c117667aee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153236"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Meilleures pratiques en matière de sécurité du contrôle d’accès et de la gestion des identités Azure
Dans cet article, nous étudions une collection de bonnes pratiques en matière de sécurité du contrôle d’accès et de la gestion des identités Azure. Ces meilleures pratiques sont issues de notre expérience avec [Azure AD](../active-directory/fundamentals/active-directory-whatis.md), mais également de celle des clients, comme vous.

Pour chaque bonne pratique, nous détaillons les éléments suivants :

* Nature de la bonne pratique
* Raison pour laquelle activer cette bonne pratique
* Conséquence possible en cas de non-utilisation de la meilleure pratique
* Alternatives possibles à la meilleure pratique
* Comment apprendre à utiliser la bonne pratique

Cet article repose sur un consensus, ainsi que sur les fonctionnalités et ensembles de fonctions de la plateforme Azure disponibles lors de l’écriture. Les opinions et avis évoluent au fil du temps ; cet article sera régulièrement mis à jour de manière à tenir compte de ces changements.

Dans cet article, nous allons étudier les points suivants :

* Traiter l’identité en tant que périmètre de sécurité principal
* La centralisation de la gestion des identités
* Gérer les clients connectés
* Activer l’authentification unique
* Activer l’accès conditionnel
* Activer la gestion des mots de passe
* Appliquer la vérification multifacteur pour les utilisateurs
* Utiliser le contrôle d’accès en fonction du rôle
* Exposition réduite des comptes privilégiés
* Contrôle des emplacements où se trouvent les ressources
* Utiliser Azure AD pour l’authentification du stockage

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Traiter l’identité en tant que périmètre de sécurité principal

Beaucoup de gens considèrent l’identité comme le périmètre principal pour la sécurité. Il s’agit d’un changement par rapport à l’objectif traditionnel sur la sécurité réseau. Les périmètres de réseau continuent d’être plus poreux et la défense du périmètre ne peut plus être aussi efficace qu’avant l’explosion des appareils [BYOD](https://aka.ms/byodcg) et des applications cloud.

[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) est la solution Azure pour la gestion des identités et des accès. Azure AD est un service multilocataire basé sur le cloud pour la gestion des identités et des annuaires par Microsoft. Il associe des services d’annuaires principaux, la gestion de l’accès aux applications et la protection des identités dans une même solution.

Les sections suivantes répertorient les meilleures pratiques pour la sécurité des identités et des accès à l’aide d’Azure AD.

## <a name="centralize-identity-management"></a>La centralisation de la gestion des identités

Dans un scénario [d’identité hybride](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?), nous vous recommandons d’intégrer vos répertoires cloud et locaux. Intégration permet à votre équipe informatique gérer les comptes à partir d’un seul emplacement, quel que soit l’endroit où un compte est créé. Intégration également votre aide les utilisateurs à être plus productif en fournissant une identité commune pour accéder aux ressources de cloud et locales.

**Bonne pratique** : Établir une seule instance d’Azure AD. La cohérence et une source faisant autorité unique sera plus de clarté et réduire les risques de sécurité à partir de la complexité de la configuration et les erreurs humaines.
**Détail** : Désignez un annuaire Azure AD unique répertoire comme source faisant autoritée pour les comptes d’entreprise.

**Bonne pratique** : Intégrez vos répertoires locaux à Azure AD.  
**Détail** : Utilisez [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) pour synchroniser votre annuaire local avec votre annuaire cloud.

> [!Note]
> Il existe [les facteurs qui affectent les performances d’Azure AD Connect](../active-directory/hybrid/plan-connect-performance-factors.md). Assurez-vous de Qu'azure AD Connect a une capacité suffisante pour conserver trouvons des systèmes de sécurité d’instance et de productivité. Les organisations complexes ou volumineux (organisations approvisionnement plus de 100 000 objets) doivent suivre le [recommandations](../active-directory/hybrid/whatis-hybrid-identity.md) pour optimiser leur implémentation Azure AD Connect.

**Bonne pratique** : Ne pas synchroniser les comptes qui ont des privilèges élevés dans votre instance Active Directory existante vers Azure AD.
**Détail** : Ne modifiez pas la valeur par défaut [configuration Azure AD Connect](../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) qui exclut ces comptes. Cette configuration réduit le risque d’adversaires glissement du cloud à des ressources locales (qui peuvent créer un incident majeur).

**Bonne pratique** : Activez la synchronisation de hachage de mot de passe.  
**Détail** : Synchronisation de hachage de mot de passe est une fonctionnalité permettant de synchroniser les hachages de mot de passe utilisateur à partir d’une instance d’Active Directory en local à Azure basé sur le cloud instance AD. Cette synchronisation vous protège contre les informations d’identification volées cours de relecture contre les attaques précédentes.

Même si vous décidez d’utiliser la fédération avec Active Directory Federation Services (AD FS) ou d’autres fournisseurs d’identité, vous pouvez éventuellement configurer la synchronisation de hachage de mot de passe en tant que sauvegarde au cas où vos serveurs locaux connaîtraient une défaillance ou deviendraient temporairement non disponibles. Cette synchronisation permet aux utilisateurs de se connecter au service en utilisant le même mot de passe qu’ils utilisent pour se connecter à leur instance d’Active Directory en local. Il permet également d’Identity Protection détecter les informations d’identification compromises en comparant les hachages de mot de passe synchronisé avec les mots de passe connus pour être compromis, si un utilisateur a utilisé la même adresse e-mail et le mot de passe sur d’autres services qui ne sont pas connectées à Azure AD.

Pour plus d’informations, consultez [Implémenter la synchronisation de hachage du mot de passe avec la synchronisation Azure AD Connect](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

**Bonne pratique** : Nouveau développement d’applications, utilisez Azure AD pour l’authentification.
**Détail** : Utiliser les fonctionnalités appropriées pour prendre en charge l’authentification :

  - Azure AD pour les employés
  - [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) pour les utilisateurs invités et des partenaires externes
  - [Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/) pour contrôler la façon dont les clients s’inscrire, connectez-vous et gèrent leurs profils lorsqu’ils utilisent vos applications

Les organisations qui n’intègrent pas leur identité locale avec leur identité cloud peuvent avoir une charge plus importante dans la gestion des comptes. Cette surcharge augmente la probabilité d’erreurs et de failles de sécurité.

> [!Note]
> Vous devez choisir les répertoires dans lesquels essentiels résident dans les comptes et que la station de travail admin utilisée est géré par les nouveaux services cloud ou existant traite. À l’aide de gestion existante et l’identité de processus d’approvisionnement peut réduire certains risques mais peut également créer le risque d’une personne malveillante de compromettre un compte local et le glissement vers le cloud. Vous souhaiterez peut-être utiliser une stratégie différente pour différents rôles (par exemple, les administrateurs informatiques et aux administrateurs d’unité). Deux options s’offrent à vous. Première option consiste à créer des comptes Azure AD qui ne sont pas synchronisées avec votre instance d’Active Directory en local. Joindre votre station de travail d’administrateur à Azure AD, ce que vous pouvez gérer et des correctifs à l’aide de Microsoft Intune. Deuxième option consiste à utiliser des comptes d’administrateur existant en synchronisant à votre instance d’Active Directory en local. Utiliser des stations de travail existantes dans votre domaine Active Directory pour la gestion et de sécurité.

## <a name="manage-connected-tenants"></a>Gérer les clients connectés
Votre organisation de sécurité a besoin de visibilité pour évaluer les risques et pour déterminer si les stratégies de votre organisation et des exigences réglementaires, sont suivies. Vous devez vous assurer que votre organisation de sécurité a une visibilité sur tous les abonnements connectés à votre environnement de production et le réseau (via [Azure ExpressRoute](../expressroute/expressroute-introduction.md) ou [VPN de site à site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). Un [administrateur/entreprise de l’administrateur général](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) dans Azure AD peuvent élever leur accès à la [administrateur des accès utilisateur](../role-based-access-control/built-in-roles.md#user-access-administrator) rôle et afficher tous les abonnements et groupes gérés connectés à votre environnement.

Consultez [élever l’accès pour gérer tous les abonnements Azure et les groupes d’administration](../role-based-access-control/elevate-access-global-admin.md) pour vous assurer que vous et votre groupe de sécurité peuvent afficher tous les abonnements ou groupes d’administration connectés à votre environnement. Vous devez supprimer cet accès avec élévation de privilèges une fois que vous avez évalué les risques.

## <a name="enable-single-sign-on"></a>Activer l’authentification unique

Dans un monde où mobilité et cloud occupent le premier plan, vous souhaitez activer l’authentification unique (SSO) sur tous les appareils, les applications et les services afin que vos utilisateurs soient être productifs où qu’ils soient et à tout moment. La gestion de plusieurs solutions d’identité pose un problème d’administration, non seulement pour l’informatique, mais également pour les utilisateurs qui auront à mémoriser plusieurs mots de passe.

En utilisant la même solution d’identité pour toutes vos applications et vos ressources, vous pouvez obtenir une authentification unique. Vos utilisateurs peuvent utiliser le même jeu d’informations d’identification pour s’authentifier et accéder aux ressources dont ils ont besoin, qu’elles soient situées en local ou dans le cloud.

**Bonne pratique** : Activez l’authentification unique.  
**Détail** : Azure AD [étend les versions locales d’Active Directory](../active-directory/connect/active-directory-aadconnect.md) sur le cloud. Les utilisateurs peuvent utiliser leur compte professionnel ou scolaire principal pour leurs appareils joints au domaine, les ressources de l’entreprise et toutes les applications web et SaaS dont ils ont besoin pour accomplir leur travail. Les utilisateurs n’ont plus besoin de gérer plusieurs combinaisons de nom d’utilisateur et mot de passe et l’accès aux applications peut être automatiquement mis en service (ou au contraire retiré) en fonction de leur appartenance aux groupes de l’entreprise et de leur statut en tant qu’employé. Vous pouvez en outre contrôler l’accès aux applications de la galerie ou aux applications en local que vous avez développées et publiées via le [proxy d’application Azure AD](../active-directory/active-directory-application-proxy-get-started.md).

L’authentification unique permet aux utilisateurs d’accéder à leurs [applications SaaS](../active-directory/active-directory-appssoaccess-whatis.md) avec leur compte professionnel ou scolaire dans Azure AD. Ceci s’applique non seulement aux applications SaaS de Microsoft, mais également à d’autres applications, telles que [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) et [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Vous pouvez configurer votre application pour utiliser Azure AD comme fournisseur [d’identité SAML](../active-directory/fundamentals-identity.md). Pour contrôler la sécurité, Azure AD n’émet pas de jetons permettant aux utilisateurs de se connecter à l’application avant que l’accès n’ait été octroyé par Azure AD. Les utilisateurs peuvent accorder un accès direct ou via un groupe dont ils sont membres.

Les organisations qui ne créent pas une identité commune pour établir l’authentification unique pour leurs utilisateurs et les applications sont davantage exposées à des scénarios où les utilisateurs disposent de plusieurs mots de passe. Ces scénarios augmentent les chances qu’ils réutilisent des mots de passe ou qu’ils choisissent des mots de passe faibles.

## <a name="turn-on-conditional-access"></a>Activer l’accès conditionnel

Les utilisateurs peuvent accéder aux ressources de votre organisation en utilisant différents appareils et applications, n’importe où. En tant qu’administrateur informatique, vous souhaitez vous assurer que ces appareils répondent à vos normes de sécurité et de conformité. Contrôler les personnes autorisées à accéder à une ressource ne suffit plus.

Pour équilibrer la sécurité et la productivité, vous devez réfléchir à la façon dont une ressource est accessible avant que vous pouvez prendre une décision à propos du contrôle d’accès. L’accès conditionnel Azure AD vous permet de satisfaire cette exigence. Avec l’accès conditionnel, vous pouvez prendre des décisions de contrôle d’accès automatisée en fonction des conditions pour accéder à vos applications cloud.

**Bonne pratique** : Gérez et contrôlez l’accès aux ressources de l’entreprise.  
**Détail** : Configurez [l’accès conditionnel](../active-directory/active-directory-conditional-access-azure-portal.md) Azure AD en fonction du groupe, de l’emplacement et du niveau de confidentialité des applications SaaS et des applications connectées à Azure AD.

**Bonne pratique** : Bloquer les protocoles d’authentification hérités.
**Détail** : Les pirates exploitent les failles de protocoles plus anciens chaque jour, en particulier pour les attaques de mot de passe pulvérisation. Configurer l’accès conditionnel pour bloquer les protocoles hérités. Regardez la vidéo [Azure AD : Choses à faire et](https://www.youtube.com/watch?v=wGk0J4z90GI) pour plus d’informations.

## <a name="enable-password-management"></a>Activer la gestion des mots de passe

Si vous avez plusieurs locataires ou si vous voulez permettre aux utilisateurs de [réinitialiser leurs mots de passe](../active-directory/active-directory-passwords-update-your-own-password.md), il est important d’utiliser des stratégies de sécurité appropriées afin d’éviter les abus.

**Bonne pratique** : Configurez la réinitialisation de mot de passe en libre-service pour vos utilisateurs.  
**Détail** : Utilisez la fonctionnalité de [réinitialisation de mot de passe en libre-service](../active-directory-b2c/active-directory-b2c-reference-sspr.md) d’Azure AD.

**Bonne pratique** : Effectuez un monitoring de l’utilisation réelle de la réinitialisation de mot de passe en libre-service.  
**Détail** : Effectuez un monitoring des utilisateurs qui s’inscrivent avec le [Rapport d’activité d’inscription à la réinitialisation de mot de passe](../active-directory/active-directory-passwords-get-insights.md) Azure AD. La fonctionnalité de création de rapports fournie par Azure AD vous aide à répondre aux questions à l’aide de rapports prédéfinis. Si vous disposez d’une licence appropriée, vous pouvez également créer des requêtes personnalisées.

**Bonne pratique** : Étendre des stratégies de mot de passe basé sur le cloud à votre infrastructure sur site.
**Détail** : Améliorer les stratégies de mot de passe de votre organisation en effectuant les mêmes vérifications pour les modifications de mot de passe en local, comme vous le feriez pour les modifications de mot de passe basé sur le cloud. Installer [protection de mot de passe Azure AD](../active-directory/authentication/concept-password-ban-bad.md) pour Windows Server Active Directory agents sur site étendre les listes de mots de passe interdits à votre infrastructure existante. Les utilisateurs et administrateurs de modifient, définir ou réinitialiser les mots de passe en local est requis pour se conformer à la même stratégie de mot de passe en tant qu’utilisateurs cloud uniquement.

## <a name="enforce-multi-factor-verification-for-users"></a>Appliquer la vérification multifacteur pour les utilisateurs

Nous vous recommandons d’exiger une vérification en deux étapes pour tous vos utilisateurs. Cela inclut les administrateurs et les autres membres de votre organisation (par exemple, les responsables financiers) dont la compromission de leur compte pourrait avoir un impact significatif si leur compte est compromis.

Il existe plusieurs options pour exiger une vérification en deux étapes. La meilleure option pour vous dépend de vos objectifs, de l’édition d’Azure AD utilisée et de votre programme de licence. Consultez [Comment exiger la vérification en deux étapes pour un utilisateur](../active-directory/authentication/howto-mfa-userstates.md) pour déterminer la meilleure option pour vous. Consultez les pages de tarification [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) et [Authentification multifacteur Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) pour plus d’informations concernant les licences et la tarification.

Voici les options et les avantages de la vérification en deux étapes :

**Option 1** : [Activez Multi-Factor Authentication en modifiant l’état de l’utilisateur](../active-directory/authentication/howto-mfa-userstates.md).   
**Avantage** : C’est la méthode traditionnelle pour exiger une vérification en deux étapes. Elle fonctionne avec [l’authentification multifacteur Azure dans le cloud et le serveur Multi-Factor Authentication Azure](../active-directory/authentication/concept-mfa-whichversion.md). Cette méthode nécessite que les utilisateurs effectuent la vérification en deux étapes chaque fois qu’ils se connectent, puis remplace les stratégies d’accès conditionnel.

Pour déterminer où l’authentification multifacteur doit être activé, consultez [quelle version de l’authentification Multifacteur Azure est appropriée pour mon organisation ?](../active-directory/authentication/concept-mfa-whichversion.md).

**Option 2** : [Activez Multi-Factor Authentication avec stratégie d’accès conditionnel](../active-directory/authentication/howto-mfa-getstarted.md).
**Avantage** : Cette option permet de demander une vérification en deux étapes sous certaines conditions à l’aide de [l’accès conditionnel](../active-directory/active-directory-conditional-access-azure-portal.md). Les conditions spécifiques peuvent être une connexion de l’utilisateur à partir d’emplacements différents, d’appareils non approuvés ou d’applications que vous considérez comme risquées. Le fait de définir des conditions spécifiques pour une vérification en deux étapes vous permet d’éviter de la demander continuellement à vos utilisateurs, ce qui peut être désagréable.

Il s’agit de la méthode la plus souple pour activer la vérification en deux étapes pour vos utilisateurs. Activer une stratégie d’accès conditionnel fonctionne uniquement pour l’authentification multifacteur Azure dans le cloud, et c’est une fonctionnalité payante d’Azure AD. Vous pouvez trouver plus d’informations sur cette méthode dans [Déployer une authentification multifacteur Azure basée sur le cloud](../active-directory/authentication/howto-mfa-getstarted.md).

**Option 3** : Activez Multi-Factor Authentication avec des stratégies d’accès conditionnel en évaluant les risques de l’utilisateur et de la connexion [d’Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md).   
**Avantage** : Cette option permet de :

- Détecter des vulnérabilités potentielles qui affectent les identités de votre organisation.
- Configurer des réponses automatiques aux actions suspectes détectées qui sont liées aux identités de votre organisation.
- Examiner les incidents suspects et prendre les mesures appropriées pour les résoudre.

Cette méthode utilise l’évaluation de risques d’Azure AD Identity Protection pour déterminer si la vérification en deux étapes est exigée en se basant sur les risques de l’utilisateur et de la connexion pour toutes les applications cloud. Cette méthode requiert une licence Azure Active Directory P2. Vous trouverez plus d’informations sur cette méthode dans [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> Option 1, l’activation de Multi-Factor Authentication en changeant l’état de l’utilisateur remplace les stratégies d’accès conditionnel. Étant donné que les options 2 et 3 utilisent des stratégies d’accès conditionnel, vous ne pouvez pas utiliser l’option 1 avec elles.

Les organisations qui n’ajoutent pas de couche supplémentaire de protection d’identité, comme la vérification en deux étapes, sont plus sensibles au vol d’informations d’identification. Le vol d’informations d’identification peut entraîner une compromission des données.

## <a name="use-role-based-access-control"></a>Utiliser le contrôle d’accès en fonction du rôle
Gestion de l’accès pour les ressources de cloud est essentielle pour toute organisation qui utilise le cloud. [Contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) vous aide à gérer qui a accès aux ressources Azure, qu’ils peuvent faire avec ces ressources, et quelles zones, ils ont accès.

Désignation des groupes ou rôles individuels responsables des fonctions spécifiques dans Azure permet d’éviter toute confusion pouvant conduire à humaine et erreurs d’automatisation qui créent des risques de sécurité. Restreindre l’accès en fonction des principes du [besoin de connaître](https://en.wikipedia.org/wiki/Need_to_know) et du [privilège minimum](https://en.wikipedia.org/wiki/Principle_of_least_privilege) est impératif pour les organisations désireuses d’appliquer des stratégies de sécurité pour l’accès aux données.

Votre équipe de sécurité a besoin d’une visibilité sur vos ressources Azure afin d’évaluer et de résoudre ces risques. Si l’équipe de sécurité a la responsabilité opérationnelle, dont ils ont besoin d’autorisations supplémentaires pour effectuer leurs tâches.

Vous pouvez utiliser [RBAC](../role-based-access-control/overview.md) pour affecter des autorisations aux utilisateurs, groupes et applications dans une étendue donnée. L’étendue d’une attribution de rôle peut être une seule ressource, un groupe de ressources ou un abonnement.

**Bonne pratique** : Séparer les tâches au sein de votre équipe et accorder uniquement les droits d’accès aux utilisateurs dont ils ont besoin pour accomplir leur travail. Au lieu de donner à tous des autorisations illimitées dans votre abonnement Azure ou vos ressources, autoriser uniquement certaines actions dans une étendue particulière.
**Détail** : Utilisez [rôles RBAC intégrés](../role-based-access-control/built-in-roles.md) dans Azure pour affecter des privilèges aux utilisateurs.

> [!Note]
> Des autorisations spécifiques créez complexité inutile et toute confusion, accumulation dans une configuration « héritée » qui est difficile à corriger sans craindre de perturber quelque chose. Évitez les autorisations propres à la ressource. Au lieu de cela, utilisez des groupes d’administration pour les autorisations de l’entreprise et les groupes de ressources pour les autorisations dans les abonnements. Évitez les autorisations spécifiques à l’utilisateur. Attribuer à la place, l’accès à des groupes dans Azure AD.

**Bonne pratique** : Accorder des équipes de sécurité avec un accès de responsabilités Azure pour voir les ressources Azure afin de pouvoir évaluer et résoudre ces risques.
**Détail** : Accorder des équipes de sécurité le RBAC [lecteur sécurité](../role-based-access-control/built-in-roles.md#security-reader) rôle. Vous pouvez utiliser le groupe d’administration racine ou le groupe d’administration de segment, selon l’étendue des responsabilités :

- **Groupe d’administration racine** pour les équipes responsables pour toutes les ressources d’entreprise
- **Groupe d’administration segment** pour les équipes avec une portée limitée (généralement en raison de limites organisationnelles réglementaires ou autres)

**Bonne pratique** : Accordez les autorisations appropriées aux équipes de sécurité ayant la responsabilité opérationnelle directe.
**Détail** : Passez en revue les rôles intégrés RBAC pour l’attribution de rôle appropriée. Si les rôles intégrés ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer [des rôles personnalisés pour les ressources Azure](../role-based-access-control/custom-roles.md). Comme avec les rôles intégrés, vous pouvez attribuer des rôles personnalisés aux utilisateurs, groupes et principaux de service à l’abonnement, groupe de ressources, étendue et des ressources.

**Bonnes pratiques** : Octroi de centre de sécurité l’accès à des rôles de sécurité qui en ont besoin. Security Center permet aux équipes de sécurité de rapidement identifier et remédier aux risques.
**Détail** : Ajouter des équipes de sécurité avec ces besoins pour le RBAC [administrateur de la sécurité](../role-based-access-control/built-in-roles.md#security-admin) rôle pour qu’elles peuvent afficher les stratégies de sécurité, afficher les États de sécurité, modifier les stratégies de sécurité, afficher les alertes et recommandations, ignorer les alertes et recommandations. Pour cela, à l’aide du groupe d’administration racine ou le groupe d’administration de segment, selon l’étendue des responsabilités.

Les organisations qui n’appliquent les données de contrôle d’accès à l’aide de fonctionnalités telles que RBAC peut octroyer plus de privilèges que nécessaire à leurs utilisateurs. Cela peut entraîner compromission des données en permettant aux utilisateurs d’accéder aux types de données (par exemple, un fort impact commercial) auxquelles ils n’avaient pas.

## <a name="lower-exposure-of-privileged-accounts"></a>Exposition réduite des comptes privilégiés

La sécurisation de l’accès privilégié est une première étape essentielle pour protéger les ressources d’entreprise. Limiter le nombre de personnes qui ont accès aux informations ou aux ressources sécurisées réduit le risque qu’un utilisateur malveillant accède à ces données ou qu’une ressource sensible soit accidentellement affectée par un utilisateur autorisé.

Les comptes privilégiés sont ceux qui administrent et gèrent des systèmes informatiques. Les pirates informatiques ciblent ces comptes pour accéder aux données et aux systèmes d’une organisation. Pour sécuriser l’accès privilégié, vous devez isoler les comptes et les systèmes contre les risques d’exposition à un utilisateur malveillant.

Nous vous recommandons de créer et de suivre une feuille de route pour sécuriser l’accès privilégié contre les cybercriminels. Pour plus d’informations sur la création d’un programme détaillé pour sécuriser les identités et les accès gérés ou signalés dans Azure AD, Microsoft Azure, Office 365 et d’autres services cloud, passez en revue l’article [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

Les éléments suivants résument les meilleures pratiques indiquées dans [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md) :

**Bonne pratique** : Gérez, contrôlez et effectuez le monitoring de l’accès aux comptes privilégiés.   
**Détail** : Activez [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Après avoir activé Privileged Identity Management, vous recevez des notifications par courrier électronique de changements de rôles d’accès privilégié. Ces notifications vous informent lorsque des utilisateurs supplémentaires sont ajoutés aux rôles disposant de privilèges élevés dans votre annuaire.

**Bonne pratique** : Vérifiez tous les comptes administrateur critiques sont gérés les comptes Azure AD.
**Détail** : Supprimez les comptes de consommateur à partir des rôles d’administrateur critiques (par exemple, les comptes Microsoft telles que hotmail.com, live.com et outlook.com).

**Bonne pratique** : Vérifiez que tous les rôles d’administrateur critiques ont un compte distinct pour les tâches administratives afin d’éviter d’hameçonnage et autres attaques de compromettre des privilèges d’administrateur.
**Détail** : Créer un compte administratif séparé qui a affecté les privilèges nécessaires pour effectuer les tâches administratives. Bloque l’utilisation de ces comptes d’administration pour les outils de productivité quotidienne telles que la messagerie électronique Microsoft Office 365 ou de la navigation web arbitraire.

**Bonne pratique** : Identifiez et catégorisez les comptes présentant des rôles très privilégiés.   
**Détail** : Après avoir activé Azure AD Privileged Identity Management, vous voyez les utilisateurs Administrateur général, Administrateur à rôle privilégié et d’autres rôles très privilégiés. Supprimez les comptes qui ne sont plus nécessaires dans ces rôles et classez les autres comptes qui sont affectés à des rôles d’administrateur :

- Affectés individuellement à des utilisateurs administratifs, et pouvant servir à des fins non administratives (par exemple, messagerie personnelle)
- Affectés individuellement à des utilisateurs administratifs et dédiés à des fins administratives uniquement
- Partagés entre plusieurs utilisateurs
- Pour les scénarios d’accès d’urgence
- Pour les scripts automatisés
- Pour les utilisateurs externes

**Bonne pratique** : Implémentez l’accès juste-à-temps pour réduire encore le temps d’exposition des privilèges et augmenter votre visibilité sur l’utilisation des comptes privilégiés.   
**Détail** : Grâce à Azure AD Privileged Identity Management, vous pouvez :

- Limiter les utilisateurs à l’utilisation de leur accès Juste à temps privilégiés.
- Attribuer des rôles pour une durée plus courte en sachant que les privilèges sont automatiquement révoqués.

**Bonne pratique** : Définissez au moins deux comptes d’accès d’urgence.   
**Détail** : Les comptes d’accès d’urgence aident les organisations à restreindre l’accès privilégié dans un environnement Azure Active Directory existant. Ces comptes sont hautement privilégiés et ne sont pas affectés à des individus spécifiques. Les comptes d’accès d’urgence sont limités aux scénarios où il est impossible d’utiliser des comptes d’administration normaux. Les organisations doivent limiter l’utilisation des comptes d’urgence au temps strictement nécessaire.

Évaluez les comptes qui sont affectés ou éligibles pour le rôle d’administrateur général. Si vous ne voyez pas de comptes cloud uniquement à l’aide du domaine `*.onmicrosoft.com` (conçu pour l’accès d’urgence), créez-les. Pour plus d’informations, consultez [Managing emergency access administrative accounts in Azure AD](../active-directory/users-groups-roles/directory-emergency-access.md) (Gestion des comptes d’administration de l’accès d’urgence dans Azure AD).

**Bonne pratique** : Avoir un processus « secours » en place en cas d’urgence.
**Détail** : Suivez les étapes de [sécurisation PAM pour les déploiements hybrides et cloud dans Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

**Bonne pratique** : Nécessitent tous les comptes administrateur critiques soient sans mot de passe (recommandé) ou exiger une authentification multifacteur.
**Détail** : Utilisez le [application Microsoft Authenticator](../active-directory/authentication/howto-authentication-phone-sign-in.md) se connecter à n’importe quel compte Azure AD sans utiliser un mot de passe. Comme [Windows Hello entreprise](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator utilise l’authentification basée sur la clé pour activer une information d’identification de l’utilisateur qui est liée à un appareil et utilise l’authentification biométrique ou un code confidentiel.

Exiger l’authentification multifacteur Azure à la connexion pour tous les utilisateurs individuels auxquels sont affectés à un ou plusieurs des rôles administrateur Azure AD : Administrateur général, administrateur de rôle privilégié, administrateur Exchange Online et administrateur SharePoint Online. Activer [multi-Factor Authentication pour vos comptes administrateur](../active-directory/authentication/howto-mfa-userstates.md) et vérifiez que les utilisateurs du compte administrateur ont inscrit.

**Bonne pratique** : Pour les comptes d’administrateur critiques, ont une station de travail administration dans lequel les tâches de production ne sont pas autorisés (par exemple, la navigation et e-mail). Cela permet de protéger vos comptes administrateur à partir de vecteurs d’attaque qui utilisent de navigation et de messagerie et de réduire considérablement les risques d’un incident majeur.
**Détail** : Utiliser une station de travail d’administration. Choisissez un niveau de sécurité de la station de travail :

- Les périphériques de productivité hautement sécurisée fournissent des fonctions avancées de sécurité pour la navigation et d’autres tâches de productivité.
- [Privilégié (Paw) des stations de travail accès](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) fournissent un système d’exploitation dédié qui est protégé contre les attaques internet et les vecteurs de menaces aux tâches sensibles.

**Bonne pratique** : Annuler l’approvisionnement de comptes d’administrateur quand un employé quitte votre organisation.
**Détail** : Avoir un processus en place qui désactive ou supprime les comptes d’administrateur quand un employé quitte votre organisation.

**Bonne pratique** : Testez régulièrement les comptes d’administrateur à l’aide de techniques d’attaque en cours.
**Détail** : Utilisez Office 365 attaque simulateur ou un tiers offre pour exécuter des scénarios d’attaque réaliste dans votre organisation. Cela peut vous aider à trouver les utilisateurs vulnérables avant une attaque réelle se produit.

**Bonne pratique** : Prenez des mesures pour atténuer les techniques d’attaque les plus fréquentes.  
**Détail** : [Identifier les comptes Microsoft ayant des rôles d’administrateur à basculer vers des comptes professionnels ou scolaires](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Vérifier les comptes d’utilisateur distincts et le transfert de messagerie pour les comptes administrateur général](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[Vérifier que les mots de passe des comptes administrateur ont été récemment modifiés](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Activer la synchronisation de hachage de mot de passe](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[Exiger l’authentification multifacteur pour les utilisateurs dans tous les rôles privilégiés, ainsi que pour les utilisateurs exposés](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Obtenir votre Office 365 Secure Score (si vous utilisez Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Passer en revue l’aide Office 365 en matière de sécurité et de conformité (si vous utilisez Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Configurer la surveillance de l’activité Office 365 (si vous utilisez Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[Définir des propriétaires de plan de réponse d’incident/d’urgence](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[Sécuriser les comptes d’administration privilégiés locaux](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

Si vous ne sécurisez l’accès privilégié, vous constaterez peut-être qu’un trop grand nombre d’utilisateurs disposent de rôles hautement privilégiés et sont plus vulnérables aux attaques. Les personnes malveillantes, comme les pirates informatiques, ciblent souvent des comptes administrateur et d’autres moyens d’accès privilégié pour accéder à des données sensibles et à des systèmes à l’aide de vols d’informations d’identification.

## <a name="control-locations-where-resources-are-created"></a>Contrôle des emplacements où les ressources sont créées

Le fait de permettre aux opérateurs de cloud d’effectuer des tâches tout en les empêchant de briser les conventions qui sont nécessaires à la gestion des ressources de votre organisation est très important. Les organisations qui veulent contrôler les emplacements où les ressources sont créées doivent coder en dur ces emplacements.

Vous pouvez utiliser [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pour créer des stratégies de sécurité dotées de définitions décrivant les actions ou les ressources spécifiquement refusées. Vous affectez ces définitions de stratégies selon l’étendue souhaitée, au niveau de l’abonnement, du groupe de ressources ou d’une ressource individuelle.

> [!NOTE]
> Les stratégies de sécurité ne sont pas identiques au contrôle d’accès en fonction du rôle (RBAC). En fait, ils utilisent le RBAC pour autoriser des utilisateurs à créer ces ressources.
>
>

Les organisations qui ne contrôlent pas la création des ressources sont plus sensibles aux utilisateurs susceptibles d’abuser du service en créant plus de ressources que nécessaire. Le renforcement du processus de création des ressources est une étape importante de sécurisation d’un scénario à plusieurs locataires.

## <a name="actively-monitor-for-suspicious-activities"></a>Surveillance active des activités suspectes

Un système de surveillance d’identité actif peut détecter rapidement un comportement suspect et déclencher une alerte pour un examen approfondi. Le tableau suivant répertorie les deux fonctionnalités Azure AD pouvant aider les organisations à surveiller leurs identités :

**Bonne pratique** : Définissez une méthode pour identifier :

- Les tentatives de connexion [sans être suivi](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md).
- Les attaques par [force brute](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) contre un compte particulier.
- Les tentatives de connexion à partir de plusieurs emplacements.
- Les connexions depuis des [appareils infectés](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md).
- Les adresses IP suspectes.

**Détail** : Utilisez les [rapports d’anomalies](../active-directory/active-directory-view-access-usage-reports.md) d’Azure AD Premium. Disposer de processus et de procédures permettant aux administrateurs informatiques d’exécuter ces rapports de manière quotidienne ou à la demande (généralement dans un scénario de réponse aux incidents).

**Bonne pratique** : Disposez d’un système de monitoring actif qui vous informe des risques et adapte le niveau de risque (élevé, moyen ou faible) à vos besoins métier.   
**Détail** : Utilisez [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), qui marque les risques actuels sur son propre tableau de bord et envoie des notifications de synthèse quotidiennes par e-mail. Pour aider à protéger les identités de votre organisation, vous pouvez configurer des stratégies qui répondent automatiquement aux problèmes détectés lorsqu’un niveau de risque spécifié est atteint.

Les organisations qui ne surveillent pas activement leurs systèmes d’identité risquent de compromettre les informations d’identification des utilisateurs. Si elles n’ont pas connaissance des activités suspectes se déroulant avec ces informations d’identification, elles ne sont pas en mesure de limiter ce type de menace.

## <a name="use-azure-ad-for-storage-authentication"></a>Utiliser Azure AD pour l’authentification du stockage
[Stockage Azure](../storage/common/storage-auth-aad.md) prend en charge l’authentification et autorisation avec Azure AD pour le stockage d’objets Blob et stockage file d’attente. Avec l’authentification Azure AD, vous pouvez utiliser le contrôle d’accès en fonction du rôle Azure pour accorder des autorisations spécifiques aux utilisateurs, groupes et applications à l’étendue d’un conteneur d’objets blob individuels ou de la file d’attente.

Nous vous recommandons d’utiliser [Azure AD pour authentifier l’accès au stockage](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Étape suivante

Consultez l’article [Bonnes pratiques et tendances Azure relatives à la sécurité](security-best-practices-and-patterns.md) pour découvrir d’autres bonnes pratiques en matière de sécurité à appliquer dans le cadre de la conception, du déploiement et de la gestion de vos solutions cloud avec Azure.
