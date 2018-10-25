---
title: Cinq étapes pour sécuriser votre infrastructure d’identité dans Azure Active Directory
description: Ce document présente une liste d’actions importantes que les administrateurs doivent implémenter pour mieux sécuriser leur organisation à l’aide des fonctionnalités d’Azure AD
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: 94d96cab28f738984b3d05d5eee0754e8c5e75b6
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341569"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Cinq étapes pour sécuriser votre infrastructure d’identité

Si vous lisez ce document, c'est que vous connaissez l’importance de la sécurité. Vous êtes sans doute déjà responsable de la sécurisation de votre organisation. Si vous avez besoin de convaincre d’autres utilisateurs de l’importance de la sécurité, demandez-leur de lire le dernier [Rapport de renseignement sur la sécurité (SIR) de Microsoft](https://www.microsoft.com/security/intelligence-report).

Ce document vous aidera à obtenir une position plus sécurisée à l’aide des fonctionnalités d’Azure Active Directory grâce à une liste de vérification en cinq étapes pour protéger votre organisation contre les cyberattaques.

Cette liste de vérification vous aidera à déployer rapidement les actions recommandées critiques pour protéger votre organisation immédiatement et explique comment :

* Renforcer vos informations d’identification.
* Réduire votre surface d’attaque.
* Automatiser la réponse aux menaces.
* Augmenter votre connaissance de l’audit et du monitoring.
* Permettre une sécurité de l’utilisateur plus prévisible et complète grâce à l’auto-assistance.

> [!NOTE]
> La plupart des recommandations de ce document s’appliquent seulement aux applications qui sont configurées pour utiliser Azure Active Directory comme fournisseur d’identité. La configuration des applications pour l’authentification unique garantit que les avantages des stratégies pour les informations d’identification, la détection des menaces, l’audit, la journalisation et d’autres fonctionnalités s’ajoutent à ces applications. [L’authentification unique via Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) est le fondement sur lequel se basent toutes ces recommandations.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Avant de commencer : protéger des comptes privilégiés avec l’authentification multifacteur (MFA)

Avant de commencer, assurez-vous de ne pas être compromis pendant que vous lisez cette liste de vérification. Vous devez d’abord protéger vos comptes privilégiés.

Les attaquants qui prennent le contrôle de comptes privilégiés peuvent causer des dégâts considérables. Il est donc essentiel de protéger d’abord ces comptes. Activez et exigez [Azure multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) pour tous les administrateurs de votre organisation utilisant la [Protection de la ligne de base](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Si vous n’avez pas encore implémenté l’authentification MFA, faites-le maintenant ! C’est particulièrement important.

Vous êtes prêt ? Nous pouvons commencer la liste de vérification.

## <a name="step-1---strengthen-your-credentials"></a>Étape 1 - Renforcer vos informations d’identification 

La plupart des failles de sécurité en entreprise proviennent d’un compte compromis avec l’une des méthodes, telles que le jet de mot de passe, la réexécution de violation ou le hameçonnage. Découvrez plus d’informations sur ces attaques dans cette vidéo (1h15) :
> [!VIDEO https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3016/player]

Si les utilisateurs dans votre système d’identité utilisent des mots de passe faibles sans les renforcer par l’authentification multifacteur, ce n’est pas une question de si ou quand vous serez compromis, mais simplement « à quelle fréquence ».

### <a name="make-sure-your-organization-use-strong-authentication"></a>Vérifier que votre organisation utilise une authentification renforcée

Étant donné la fréquence à laquelle les mots de passe sont devinés, hameçonnés, volés par un logiciel malveillant ou réutilisés, il est essentiel de renforcer le mot de passe à l’aide d’une certaine forme d’informations d’identification fortes : en savoir plus sur [Azure multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="turn-off-traditional-complexity-expiration-rules-and-start-banning-commonly-attacked-passwords-instead"></a>Désactiver la complexité traditionnelle, les règles d’expiration et commencer à interdire les mots de passe fréquemment attaqués à la place

De nombreuses organisations utilisent la complexité traditionnelle (par exemple, les caractères spéciaux) et les règles d’expiration de mot de passe. Les recherches de Microsoft ont montré que ces stratégies sont dangereuses, car les utilisateurs choisissent des mots de passe qui sont plus faciles à deviner.

Les recommandations de Microsoft, conformes aux [instructions du NIST](https://pages.nist.gov/800-63-3/sp800-63b.html), consistent à implémenter les trois règles suivantes :

1. Exiger des mots de passe comptant au moins 8 caractères. Un mot de passe plus long n’est pas forcément mieux adapté, car les utilisateurs choisissent alors des mots de passe prévisibles, enregistrent leurs mots de passe dans des fichiers ou les notent quelque part.
2. Désactivez les règles d’expiration, qui incitent les utilisateurs à choisir des mots de passe faciles à deviner comme **Summer2018!**.
3. Désactivez les exigences de composition de caractères et empêchez les utilisateurs de choisir des mots de passe fréquemment attaqués, car cela incite les utilisateurs à choisir des substitutions de caractères prévisibles dans les mots de passe.

Vous pouvez utiliser [PowerShell pour empêcher l’expiration des mots de passe](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) pour les utilisateurs si vous créez des identités directement dans Azure AD. Les organisations utilisant un AD local avec Azure AD Connect pour synchroniser les identités avec Azure AD (également désigné en tant que déploiement hybride), doivent implémenter des [stratégies de mot de passe intelligentes](https://aka.ms/passwordguidance) locales à l’aide des [paramètres de stratégie de groupe de domaine](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) ou de [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

La fonctionnalité [mot de passe interdit de manière dynamique](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) d’Azure Active Directory utilise le comportement actuel des attaquants pour empêcher les utilisateurs de définir des mots de passe qui peuvent être facilement devinés. Cette fonctionnalité est toujours activée et les organisations avec un déploiement hybride peuvent en bénéficier en activant la [réécriture de mot de passe](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-writeback) ou elles peuvent déployer la [protection du mot de passe Azure AD pour Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). La protection du mot de passe Azure AD empêche les utilisateurs de choisir des mots de passe courants et des mots de passe personnalisés que vous pouvez configurer.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Protection contre la fuite d’informations d’identification et ajout de la tolérance aux pannes

Si votre organisation utilise une solution d’identité hybride, vous devez activer la synchronisation du hachage de mot de passe pour les deux raisons suivantes :

* Le rapport [Utilisateurs avec des informations d’identification volées](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) dans l’administration d’Azure AD vous avertit si des paires nom d’utilisateur/mot de passe ont été exposées sur le « dark web ». Un volume incroyable de mots de passe fait l’objet d’une fuite via le hameçonnage, les programmes malveillants et la réutilisation de mot de passe sur des sites tiers qui sont ensuite victimes d’une violation de la sécurité. Microsoft recherche un grand nombre de ces informations d'identification ayant fuité et vous indiquera, dans ce rapport, si elles correspondent aux informations d’identification de votre organisation, mais uniquement si vous [activez la synchronisation de hachage du mot de passe](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) !
* En cas de panne locale (par exemple, au cours d’une attaque de ransomware), vous pourrez basculer vers [l’authentification cloud à l’aide de la synchronisation de hachage du mot de passe](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Cette méthode d’authentification de secours vous permettra de continuer à accéder aux applications configurées pour l’authentification avec Azure Active Directory, notamment Office 365.

En savoir plus sur le fonctionnement de la [synchronisation de hachage du mot de passe](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).

### <a name="implement-ad-fs-extranet-lockout"></a>Mettre en œuvre le verrouillage extranet AD FS

Les organisations qui configurent des applications pour s’authentifier directement auprès d’Azure AD bénéficient du [verrouillage intelligent Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Si vous utilisez AD FS dans Windows Server 2012 R2, implémentez la [protection par verrouillage extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection) AD FS. Si vous utilisez AD FS sur Windows Server 2016, implémentez le [verrouillage extranet intelligent](https://support.microsoft.com/en-us/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Le verrouillage extranet intelligent d’AD FS protège contre les attaques par force brute qui ciblent AD FS tout en empêchant le verrouillage des utilisateurs dans Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Tirer parti d’informations d’identification intrinsèquement sécurisées et plus faciles à utiliser

Avec [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), vous pouvez remplacer les mots de passe par une authentification à deux facteurs forte sur les PC et appareils mobiles. Cette authentification se compose d’un nouveau type d’informations d’identification utilisateur qui sont liées à un appareil et utilise un code biométriques ou un code PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Étape 2 - Réduire votre surface d’attaque

Étant donné l’omniprésence de la compromission des mots de passe, il est essentiel de réduire la surface d’attaque de votre organisation. Éliminer l’utilisation des protocoles plus anciens et moins sécurisés, limiter les points d’entrée et exercer un contrôle plus important de l’accès administratif aux ressources peuvent aider à réduire la surface d’attaque.

### <a name="block-legacy-authentication"></a>Bloquer l’authentification héritée

Les applications utilisant leurs propres méthodes héritées pour s’authentifier auprès d’Azure AD et accéder aux données d’entreprise présentent un autre risque pour les organisations. Exemples d’applications utilisant une authentification héritée : clients POP3, IMAP4 ou SMTP. Les applications à authentification héritée s’authentifient au nom de l’utilisateur et empêchent Azure AD de procéder à des évaluations avancées de la sécurité. L’authentification alternative et moderne permet de réduire les risques de sécurité, car elle prend en charge l’authentification multifacteur et l’accès conditionnel. Nous vous recommandons les trois actions suivantes :

1. Bloquez [l’authentification héritée, si vous utilisez AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Configurez [SharePoint Online et Exchange Online pour utiliser l’authentification moderne](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Utilisez [des stratégies d’accès conditionnel pour bloquer l’authentification héritée](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication).

### <a name="block-invalid-authentication-entry-points"></a>Bloquer les points d’entrée d’authentification non valide

En adoptant l’idée qu’une violation de la sécurité peut se produire, vous devez réduire l’impact des informations d’identification utilisateur compromises le cas échéant. Pour chaque application dans votre environnement, considérez les cas d’utilisation valides : quels groupes, réseaux, appareils et autres éléments sont autorisés, puis bloquez le reste. Veillez à restreindre l’utilisation de [comptes hautement privilégiés ou de service](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices). Avec [l’accès conditionnel Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), vous pouvez contrôler comment les utilisateurs autorisés accèdent à leurs applications et ressources selon des conditions spécifiques que vous définissez.

Portez une attention particulière aux comptes de service (comptes utilisés pour effectuer des tâches de manière automatique). Avec l’accès conditionnel, vous pouvez faire en sorte que ces comptes ne s’exécutent que sur le service, à partir de l’adresse IP et au moment de la journée qui conviennent.

### <a name="implement-azure-ad-privileged-identity-management"></a>Mettre en œuvre Azure AD Privileged Identity Management

Un autre impact de la « violation supposée » est le besoin de réduire la probabilité qu’un compte compromis peut fonctionner avec un rôle privilégié. 

[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) vous aide à réduire les privilèges de compte en vous aidant à :

* Identifier et gérer les utilisateurs affectés aux rôles d’administration.
* Comprendre les rôles avec des privilèges inutilisés ou excessifs que vous devez supprimer.
* Établir des règles pour vous assurer que les rôles privilégiés sont protégés par l’authentification multifacteur.
* Établir des règles pour vous assurer que les rôles privilégiés ne sont accordés que pendant le temps nécessaire pour accomplir la tâche privilégiée.

Activez Azure AD PIM, puis affichez les utilisateurs qui sont affectés à des rôles d’administration et supprimez les comptes inutiles dans ces rôles. Pour les autres utilisateurs privilégiés, déplacez-les de l’état permanent à l’état éligible. Enfin, établissez des stratégies appropriées pour vous assurer que lorsque ces utilisateurs ont besoin d’accéder à ces rôles privilégiés, ils peuvent le faire en toute sécurité.

Dans le cadre du déploiement de votre processus de compte privilégié, respectez les [meilleures pratiques pour créer au moins deux comptes d’urgence](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) pour être sûr de pouvoir accéder à Azure AD si vous êtes bloqué.

## <a name="step-3---automate-threat-response"></a>Étape 3 - Automatiser la réponse aux menaces

Azure Active Directory comporte de nombreuses fonctionnalités qui interceptent automatiquement les attaques, afin de supprimer la latence entre la détection et la réponse. Vous pouvez limiter les coûts et les risques lorsque vous réduisez le temps dont les attaquants ont besoin pour s’insérer dans votre environnement. Voici les étapes concrètes que vous pouvez appliquer.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Mettre en œuvre une stratégie de sécurité en matière de risque des utilisateurs à l’aide d’Azure AD Identity Protection

Le risque des utilisateurs indique la probabilité que l’identité d’un utilisateur a été compromise et est calculé en fonction des [événements à risque des utilisateurs](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) qui sont associés à l’identité d’un utilisateur. Une stratégie en matière de risque des utilisateurs est une stratégie d’accès conditionnel qui évalue le niveau de risque pour un groupe ou un utilisateur spécifique. Selon un niveau de risque Faible, Moyen ou Élevé, une stratégie peut être configurée pour bloquer l’accès ou exiger une modification du mot de passe sécurisée à l’aide de l’authentification multifacteur. La recommandation de Microsoft est d’exiger une modification du mot de passe sécurisée pour les utilisateurs présentant un risque élevé.

![Utilisateurs associés à un indicateur de risque](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Mettre en œuvre une stratégie en matière de risque à la connexion à l’aide d’Azure AD Identity Protection

Le risque à la connexion est la probabilité qu’une personne autre que le propriétaire du compte tente de se connecter à l’aide de l’identité. Une [stratégie en matière de risque à la connexion](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) est une stratégie d’accès conditionnel qui évalue le niveau de risque pour un groupe ou un utilisateur spécifique. Selon un niveau de risque (élevé, moyen, faible), une stratégie peut être configurée pour bloquer l’accès ou exiger l’authentification multifacteur. Veillez à exiger l’authentification multifacteur pour les connexions présentant un risque Moyen ou supérieur.

![Connexion à partir d’adresses IP anonymes](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Étape 4 - Augmenter votre connaissance

L’audit et la journalisation des événements liés à la sécurité, de même que les alertes associées, constituent des composants essentiels dans une stratégie de protection efficace. Les journaux et les rapports de sécurité vous fournissent un enregistrement électronique des activités suspectes et vous aident à détecter les motifs pouvant indiquer une tentative d’intrusion ou une intrusion externe effective sur le réseau, ainsi que des attaques internes. Vous pouvez avoir recours aux audits pour surveiller les activités des utilisateurs, documenter la conformité aux exigences réglementaires, effectuer des analyses d’investigation, etc. Les alertes fournissent des notifications des événements de sécurité.

### <a name="monitor-azure-ad"></a>Surveiller Azure AD

Les services et fonctionnalités Microsoft Azure vous proposent des options d’audit et de journalisation configurables qui permettent d’identifier les lacunes dans vos stratégies et mécanismes de sécurité et d’y remédier pour empêcher les violations éventuelles. Vous pouvez utiliser [l’audit et la journalisation Azure](https://docs.microsoft.com/azure/security/azure-log-audit) et les [Rapports d’activité d’audit dans le portail Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Surveiller Azure AD Connect Health dans les environnements hybrides

La [surveillance d’AD FS avec Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) vous offre un meilleur insight sur les problèmes potentiels et une meilleure visibilité des attaques sur votre infrastructure AD FS. Azure AD Connect Health fournit des alertes avec des détails, des étapes de résolution et des liens vers la documentation associée ; l’analytique de l’utilisation pour plusieurs métriques relatives au trafic de l’authentification ; la surveillance des rapports pour les performances.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Surveiller les événements Azure AD Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) est un outil de notification, de surveillance et de rapports que vous pouvez utiliser pour détecter les vulnérabilités potentielles qui affectent les identités de votre organisation. Il détecte les événements à risque, tels que la fuite d’informations d’identification, un voyage impossible et les connexions depuis des appareils infectés, les adresses IP anonymes, les adresses IP associées à une activité suspecte et les emplacements inconnus. Activez les alertes de notification pour recevoir par un e-mail désignant les utilisateurs exposés et/ou un e-mail de résumé hebdomadaire.

![Utilisateurs associés à un indicateur de risque](media/azure-ad/azure-ad-sec-steps3.png)

## <a name="step-5---enable-end-user-self-help"></a>Étape 5 - Activer l’auto-assistance pour l’utilisateur final

Vous voulez, autant que possible, équilibrer la sécurité avec la productivité. Tout comme vous vous efforcez d’établir une base pour la sécurité sur le long terme, vous pouvez supprimer toute friction dans votre organisation en habilitant vos utilisateurs tout en restant vigilant. 

### <a name="implement-self-service-password-reset"></a>Mettre en œuvre la réinitialisation du mot de passe libre-service

La [réinitialisation de mot de passe en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) d’Azure offre aux administrateurs informatiques un moyen simple pour permettre aux utilisateurs de réinitialiser ou de déverrouiller leurs comptes ou leurs mots de passe, sans intervention de l’administrateur. Le système inclut des rapports détaillés de suivi d’accès au système, ainsi que des notifications pour vous prévenir de toute utilisation malveillante ou de tout abus. 

### <a name="implement-self-service-group-management"></a>Mettre en œuvre la gestion de groupes en libre-service

Azure AD fournit la possibilité de gérer l’accès aux ressources à l’aide de groupes de sécurité et de groupes Office 365. Ces groupes peuvent être gérés par des propriétaires de groupe au lieu des administrateurs informatiques. Également appelée [gestion de groupes en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), cette fonctionnalité permet aux propriétaires de groupe, qui n’ont pas un rôle d’administrateur, de créer et gérer des groupes sans avoir à demander aux administrateurs de gérer leurs requêtes.

### <a name="implement-azure-ad-access-reviews"></a>Mettre en œuvre les révisions d’accès Azure AD

Avec les [révisions d’accès Azure](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), vous pouvez gérer les appartenances à un groupe, l’accès aux applications d’entreprise et les affectations de rôle privilégié pour maintenir une norme de sécurité qui n’offre pas aux utilisateurs un accès pendant de longues périodes lorsqu’ils n’en ont pas besoin.

## <a name="summary"></a>Résumé

Une infrastructure d’identité sécurisée est composée de nombreux aspects, mais cette liste de vérification en cinq étapes vous aidera à obtenir rapidement une infrastructure d’identité plus sûre et sécurisée :

* Renforcer vos informations d’identification.
* Réduire votre surface d’attaque.
* Automatiser la réponse aux menaces.
* Augmenter votre connaissance de l’audit et du monitoring.
* Permettre une sécurité de l’utilisateur plus prévisible et complète grâce à l’auto-assistance.

Nous sommes heureux de l’importance que vous accordez à l’identité de sécurité et nous espérons que ce document constitue une feuille de route utile vers une position plus sécurisée pour votre organisation.

## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’aide pour planifier et déployer les recommandations, consultez les [plans de déploiement de projet d’Azure AD](http://aka.ms/deploymentplans).
