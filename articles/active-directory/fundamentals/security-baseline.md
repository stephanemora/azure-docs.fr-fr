---
title: Ligne de base de sécurité Azure pour Azure Active Directory
description: La ligne de base de sécurité Azure Active Directory fournit des bases et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans le benchmark de sécurité Azure.
author: msmbaldwin
ms.service: active-directory
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d4203b7b5d7742bf198778864fa4f42b7423596
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285998"
---
# <a name="azure-security-baseline-for-azure-active-directory"></a>Ligne de base de sécurité Azure pour Azure Active Directory

Cette base de référence de sécurité applique les conseils du [Benchmark de sécurité Azure version 2.0](../../security/benchmarks/overview.md) à Azure Active Directory. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure. Le contenu est regroupé par les **contrôles de sécurité** définis par le Benchmark de sécurité Azure et les conseils associés applicables à Azure Active Directory. 

> [!NOTE]
> Les **contrôles** non applicables à Azure Active Directory, ou pour lesquels la responsabilité est celle de Microsoft, ont été exclus de cette ligne de base. Pour voir comment Azure Active Directory est entièrement mappé au Benchmark de sécurité Azure, consultez le [fichier de mappage complet de la ligne de base de sécurité Azure Active Directory](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6 : Simplifier les règles de sécurité réseau

**Conseils** : Utilisez les étiquettes de service Réseau virtuel Azure afin de définir des contrôles d’accès réseau sur les groupes de sécurité réseau ou le Pare-feu Azure configurés pour vos ressources Azure Active Directory. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de l’étiquette de service, comme « AzureActiveDirectory » dans le champ Source ou Destination approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent. 

- [Présentation et usage des balises de service](../../virtual-network/service-tags-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="identity-management"></a>Gestion des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des identités](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1 : Normaliser Azure Active Directory comme système d’authentification et d’identité central

**Conseil** : Utilisez Azure Active Directory (Azure AD) en tant que service de gestion des identités et des accès par défaut. Vous devez normaliser Azure AD pour régir la gestion des identités et des accès de votre organisation dans : 
- Les ressources cloud Microsoft, comme le portail Azure, le stockage Azure, les machines virtuelles Azure (Linux et Windows), les applications Azure Key Vault, PaaS et SaaS. 
- Les ressources de votre organisation, comme les applications sur Azure ou les ressources réseau de votre entreprise. 
 

La sécurisation d’Azure AD doit être d’une priorité élevée dans les pratiques de sécurité cloud de votre organisation. Azure AD fournit un score d'identité sécurisée pour vous aider à évaluer la sécurité des identités par rapport aux recommandations de Microsoft en matière de meilleures pratiques. Utilisez le score pour évaluer avec précision votre configuration par rapport aux meilleures pratiques recommandées et apporter des améliorations à votre posture de sécurité. 

Azure AD prend en charge l'identité externe, ce qui permet aux utilisateurs sans compte Microsoft de se connecter à leurs applications et ressources avec leur identité externe. 

- [Locataires dans Azure Active Directory](../develop/single-and-multi-tenant-apps.md)

- [Création et configuration d’une instance Azure AD](active-directory-access-create-new-tenant.md)

- [Utilisez des fournisseurs d'identité externes pour l’application](/azure/active-directory/b2b/identity-providers)

- [Qu’est-ce que le degré de sécurisation Identity Secure Score dans Azure Active Directory](identity-secure-score.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2 : Gérer les identités d’application de façon sécurisée et automatique

**Conseil** : Utilisez l’identité managée pour les ressources Azure pour les comptes non humains, tels que les services ou l’automatisation. Il est recommandé d’utiliser la fonctionnalité d’identité managée par Azure au lieu de créer un compte humain plus puissant pour accéder à vos ressources ou les exécuter. Vous pouvez vous authentifier en mode natif auprès des services ou des ressources Azure qui prennent en charge l’authentification Azure AD (Azure Active Directory) via une règle d’octroi d’accès prédéfinie sans utiliser d’informations d’identification codées en dur dans le code source ou les fichiers config. Vous ne pouvez pas attribuer d’identités managées Azure à des ressources Azure AD, mais Azure AD est le mécanisme permettant de s’authentifier avec des identités managées attribuées aux ressources d’autres services.

- [Identité managée pour les ressources Azure](../managed-identities-azure-resources/overview.md)

- [Services qui prennent en charge l’identité managée pour les ressources Azure](../managed-identities-azure-resources/services-support-managed-identities.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3 : Utiliser l’authentification unique Azure AD pour l’accès aux applications

**Conseil** : Utilisez Azure Active Directory pour la gestion des identités et des accès aux ressources Azure, aux applications cloud et aux applications locales. Cela inclut les identités d’entreprise, comme les employés, ainsi que les identités externes, comme les partenaires et les fournisseurs. Cela permet à l’authentification unique de gérer et sécuriser l’accès aux données et ressources de votre organisation localement et dans le cloud. Connectez l’ensemble de vos utilisateurs, applications et appareils à Azure AD pour un accès transparent et sécurisé, ainsi qu’une visibilité et un contrôle accrus.

 
- [Comprendre l’authentification unique des applications avec Azure AD](../manage-apps/what-is-single-sign-on.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4 : Utiliser des contrôles d’authentification renforcés pour tous les accès basés sur Azure Active Directory

**Conseil** : Utilisez Azure Active Directory, qui prend en charge des contrôles d’authentification forts, via l’authentification multifacteur (MFA), et des méthodes sans mot de passe fortes.
- Authentification multifacteur – Activez l’authentification multifacteur Azure AD et suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center pour appliquer quelques bonnes pratiques dans votre configuration MFA. L’authentification multifacteur peut être appliquée à tous les utilisateurs, à des utilisateurs sélectionnés ou au niveau de chaque utilisateur en fonction des conditions de connexion et des facteurs de risque.
- Authentification sans mot de passe - Trois options d’authentification sans mot de passe sont disponibles : Windows Hello Entreprise, l’application Microsoft Authenticator et les méthodes d’authentification locales, comme les cartes à puce.

Pour les utilisateurs administrateurs et privilégiés, assurez-vous que le niveau le plus élevé de méthode d’authentification forte est utilisé, puis déployez la stratégie d’authentification forte appropriée pour les autres utilisateurs.

Azure AD prend en charge l’authentification basée sur un mot de passe héritée telle que les comptes cloud uniquement (comptes d’utilisateur créés directement dans Azure) qui ont une stratégie de mot de passe de base ou les comptes hybrides (comptes d’utilisateur provenant d’Azure Active Directory local) qui suivent les stratégies de mot de passe locales. Lors de l’utilisation de l’authentification par mot de passe, Azure AD fournit une fonctionnalité de protection par mot de passe qui empêche les utilisateurs de définir des mots de passe faciles à deviner. Microsoft fournit une liste globale de mots de passe interdits mise à jour en fonction des données de télémétrie, et les clients peuvent compléter la liste en fonction de leurs besoins (par exemple, noms de marque, références culturelles, etc.). Cette protection par mot de passe peut être utilisée pour les comptes cloud et hybrides uniquement.

La seule authentification basée sur les informations de mot de passe est vulnérable aux méthodes d’attaque populaires. Pour une sécurité accrue, utilisez une authentification forte telle que l’authentification multifacteur et une stratégie de mot de passe forte. Pour les applications tierces et les services de la Place de marché qui peuvent avoir des mots de passe par défaut, vous devez les modifier lors de la configuration initiale du service.

 
- [Comment déployer Azure Active Directory Multifactor Authentication](../authentication/howto-mfa-getstarted.md) 

 

 
- [Introduction aux options d’authentification sans mot de passe pour Azure Active Directory](../authentication/concept-authentication-passwordless.md) 

 

 
- [Stratégie de mot de passe par défaut d’Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [Éliminer les mauvais mots de passe à l’aide de Protection de mots de passe d’Azure AD](../authentication/concept-password-ban-bad.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5 : Surveiller et alerter en cas d’anomalies de compte

**Conseil** : Azure Active Directory fournit les sources de données suivantes :

 
- Connexions – Le rapport de connexions fournit des informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.

 
- Journaux d’audit : traçabilité proposée via des journaux d’activité pour toutes les modifications effectuées par diverses fonctionnalités au sein d’Azure AD. Par exemple, les journaux d’audit peuvent inclure les modifications apportées à des ressources dans Azure AD comme l’ajout ou la suppression d’utilisateurs, d’applications, de groupes, de rôles, de stratégies, etc.

 
- Connexions risquées : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur.

 
- Utilisateurs avec indicateur de risque : un utilisateur à risque correspond à un indicateur de compte d’utilisateur susceptible d’être compromis.

 

Ces sources de données peuvent être intégrées à Azure Monitor, à Azure Sentinel ou à des systèmes SIEM tiers.

 

 
Azure Security Center peut également alerter dans le cas de certaines activités suspectes, comme un nombre excessif de tentatives d’authentification ayant échoué et la présence de comptes dépréciés dans l’abonnement.

 

 
Azure Advanced Threat Protection (ATP) est une solution de sécurité qui peut utiliser les signaux Active Directory pour identifier, détecter et examiner les menaces avancées, les identités compromises et les actions malveillantes internes.

 

 
- [Rapports d’activité d’audit dans Azure Active Directory](../reports-monitoring/concept-audit-logs.md) 

 

 
- [Guide pratique pour afficher les connexions risquées Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

 

 
- [Guide pratique pour identifier les utilisateurs Azure AD pour lesquels une activité à risque a été signalée](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

 

 
- [Guide pratique pour superviser l’activité liée aux identités et aux accès des utilisateurs dans Azure Security Center](../../security-center/security-center-identity-access.md) 

 

 
- [Alertes dans le module de protection de renseignement sur les menaces d’Azure Security Center](../../security-center/alerts-reference.md) 

 

 
- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6 : Restreindre l’accès aux ressources Azure en fonction des conditions

**Conseil** : Utilisez l’accès conditionnel Azure AD (Azure Active Directory) pour un contrôle d’accès plus précis en fonction des conditions définies par l’utilisateur (par exemple, les utilisateurs de certaines plages d’adresses IP doivent utiliser l’authentification MFA pour se connecter). Une stratégie de gestion granulaire des sessions d’authentification peut également être utilisée dans différents cas d’usage.

 
- [Présentation de l’accès conditionnel Azure AD](../conditional-access/overview.md) 

 

 
- [Stratégies d’accès conditionnel courantes](../conditional-access/concept-conditional-access-policy-common.md) 

 

 
- [Configurer la gestion de session d’authentification avec l’accès conditionnel](../conditional-access/howto-conditional-access-session-lifetime.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8 : Sécuriser l’accès des utilisateurs aux applications héritées

**Conseil** : Vérifiez que vous disposez de contrôles d’accès et d’une surveillance de session modernes pour les applications héritées et les données qu’elles stockent et traitent. Bien que les VPN soient couramment utilisés pour accéder aux applications héritées, elles ont souvent uniquement un contrôle d’accès de base et une surveillance limitée des sessions.

 
Le proxy d’application Azure AD vous permet de publier des applications locales héritées sur des utilisateurs distants avec l’authentification unique tout en validant de manière explicite la fiabilité des utilisateurs et des appareils distants avec l’accès conditionnel Azure AD.

 

 
Microsoft Cloud App Security est également un service CASB (Cloud Access Security Broker) qui peut fournir des contrôles pour surveiller les sessions d’application d’un utilisateur et les actions bloquantes (pour les applications locales héritées et les applications cloud Software as a Service (SaaS)).

 

 
- [Proxy d’application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#what-is-application-proxy) 

 

 
- [Meilleures pratiques avec Microsoft Cloud App Security](/cloud-app-security/best-practices)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="privileged-access"></a>Accès privilégié

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Accès privilégié](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1 : Protéger et limiter les utilisateurs disposant de privilèges élevés

**Conseil** : Les rôles intégrés les plus critiques dans Azure AD sont Administrateur général et Administrateur de rôle privilégié, car les utilisateurs affectés à ces deux rôles peuvent déléguer des rôles Administrateur :

- Administrateur général : Les utilisateurs disposant de ce rôle ont accès à toutes les fonctionnalités d’administration d’Azure AD, ainsi qu’aux services qui utilisent des identités Azure AD.

- Administrateur de rôle privilégié : Les utilisateurs avec ce rôle peuvent gérer les attributions de rôles dans Azure AD et Azure AD Privileged Identity Management (PIM). De plus, ce rôle permet de gérer tous les aspects de PIM et des unités administratives.

Vous pouvez avoir d’autres rôles critiques qui doivent être régis si vous utilisez des rôles personnalisés avec certaines autorisations attribuées. Vous pouvez également appliquer des contrôles similaires au compte Administrateur des ressources métier critiques.

Azure AD possède des comptes à privilèges élevés : les utilisateurs et les principaux de service qui sont affectés directement ou indirectement aux rôles administrateur général ou de rôle privilégié, ainsi que d’autres rôles à privilèges élevés dans Azure AD et Azure.

Limitez le nombre de comptes très privilégiés et protégez ces comptes à un niveau élevé, car les utilisateurs disposant de ces privilèges peuvent lire et modifier, directement ou non, toutes les ressources de votre environnement Azure.

Vous pouvez activer l’accès privilégié juste-à-temps (JAT) aux ressources Azure et Azure AD en utilisant Azure AD Privileged Identity Management (PIM). JAT accorde des autorisations temporaires pour effectuer des tâches privilégiées uniquement lorsque les utilisateurs en ont besoin. PIM peut également générer des alertes de sécurité en cas d’activité suspecte ou non sécurisée dans votre organisation Azure AD.

- [Autorisations de rôle Administrateur dans Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Utiliser les alertes de sécurité Azure Privileged Identity Management](../privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2 : Limiter l’accès administratif aux systèmes critiques de l’entreprise

**Conseil** : Utilisez Azure Active Directory Privileged Identity Management et Multi-Factor Authentication pour limiter l’accès administratif aux systèmes critiques de l’entreprise.

- [Approbation par Privileged Identity Management des demandes d’activation de rôle](../privileged-identity-management/azure-ad-pim-approval-workflow.md)

- [Authentification multifacteur et accès conditionnel](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3 : Examiner et rapprocher régulièrement les accès utilisateur

**Conseil** : Passez régulièrement en revue les attributions d’accès au compte d’utilisateur pour vous assurer que les comptes et leur accès sont valides, en particulier sur les rôles à privilèges élevés, notamment Administrateur général et Administrateur de rôle privilégié. Vous pouvez utiliser les révisions d’accès Azure Active Directory (Azure AD) pour examiner les appartenances aux groupes, l’accès aux applications d’entreprise et les attributions de rôles, à la fois pour les rôles Azure AD et les rôles Azure. Les rapports Azure AD peuvent fournir des journaux pour vous aider à découvrir les comptes obsolètes. Vous pouvez également utiliser Azure AD Privileged Identity Management pour créer un workflow de rapport de révision d’accès afin de faciliter le processus de révision.

En outre, Azure Privileged Identity Management peut être configuré pour déclencher des alertes lorsqu'un nombre excessif de comptes d'administrateur est créé, et pour identifier les comptes d'administrateur obsolètes ou mal configurés.

- [Créer une révision d’accès des rôles Azure AD dans Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-start-security-review.md)

- [Créer une révision d’accès des rôles de ressources Azure dans Privileged Identity Management (PIM)](../privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Utilisation des révisions d’accès et des identités Azure AD](../governance/access-reviews-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4 : Configurer l’accès d’urgence dans Azure AD

**Conseil** : Pour empêcher le verrouillage accidentel de votre organisation Azure AD, configurez un compte d’accès d’urgence pour conserver un accès lorsque les comptes administratifs normaux ne peuvent pas être utilisés. Les comptes d’accès d’urgence sont généralement hautement privilégiés et ne doivent pas être attribués à des personnes spécifiques.
Les comptes d’accès d’urgence sont limités à des cas d’urgence ou à des scénarios « de secours » où il est impossible d’utiliser des comptes d’administration normaux.

Vous devez vous assurer que les informations d’identification (telles que le mot de passe, le certificat ou la carte à puce) des comptes d’accès d’urgence restent sécurisées et connues des seules personnes autorisées à les utiliser en cas d’urgence.

- [Gérer des comptes d’accès d’urgence dans Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="pa-5-automate-entitlement-management"></a>PA-5 : Automatiser la gestion des droits d'utilisation 

**Conseil** : Utilisez les fonctionnalités de gestion des droits d’utilisation d’Azure AD pour automatiser les workflows de demandes d’accès, notamment les attributions, les révisions et l’expiration des accès. L’approbation en deux ou plusieurs étapes est également prise en charge.

- [Présentation de la gestion des droits d’utilisation Azure AD](../governance/entitlement-management-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6 : Utiliser des stations de travail d’accès privilégié

**Conseils** : Les stations de travail sécurisées et isolées sont extrêmement importantes pour la sécurité des rôles sensibles comme les administrateurs, développeurs et opérateurs de service critique. Utilisez des stations de travail utilisateur hautement sécurisées et/ou Azure Bastion pour les tâches d’administration. Utilisez Azure Active Directory, Microsoft Defender Advanced Threat Protection (MDATP) et/ou Microsoft Intune pour déployer une station de travail utilisateur sécurisée et gérée pour les tâches d’administration. Les stations de travail sécurisées peuvent être gérées de manière centralisée pour appliquer une configuration sécurisée, notamment une authentification forte, des lignes de base logicielles et matérielles et un accès réseau et logique restreint.

- [Sécurisation des appareils dans le cadre de l’accès privilégié](/security/compass/privileged-access-devices)

- [Implémentation d’un accès privilégié](/security/compass/privileged-access-deployment)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7 : Suivre le principe Just Enough Administration (privilèges minimum) 

**Conseil** : les clients peuvent configurer leur déploiement Azure Active Directory (Azure AD) pour des privilèges minimaux, en affectant des utilisateurs aux rôles avec les autorisations minimales requises pour que les utilisateurs puissent effectuer leurs tâches d’administration.

- [Autorisations de rôle Administrateur dans Azure AD](../roles/permissions-reference.md)

- [Affecter des rôles d’administrateur dans Azure AD](../roles/manage-roles-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8 : Choisir le processus d’approbation pour le support Microsoft  

**Conseil** : Azure Active Directory ne prend pas en charge Customer Lockbox. Microsoft peut travailler avec des clients via des méthodes hors Lockbox pour approuver l’accès aux données client.

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2 : Protection des données sensibles

**Conseil** : Tenez compte des conseils suivants pour la mise en œuvre de la protection de vos données sensibles :

- **Isolation :** Un annuaire est la limite de données par laquelle les services Azure Active Directory (Azure AD) stockent et traitent les données d’un client. Les clients doivent déterminer où ils souhaitent placer la majorité de données client Azure AD en définissant la propriété Pays dans leur annuaire.

- **Segmentation :** Le rôle Administrateur général a le contrôle total de toutes les données de l’annuaire et des règles qui régissent les instructions d’accès et de traitement. Un annuaire peut être segmenté en unités administratives et configuré avec des utilisateurs et des groupes pour être gérés par les administrateurs de ces unités. Les administrateurs généraux peuvent déléguer la responsabilité aux autres principes de leur organisation en les affectant à des rôles prédéfinis ou à des rôles personnalisés qu’ils peuvent créer.

 
- **Accès :** Les autorisations peuvent être appliquées à un utilisateur, un groupe, un rôle, une application ou un appareil. L’affectation peut être permanente ou temporelle selon la configuration de Privileged Identity Management. 
  
- **Chiffrement :** Azure AD chiffre toutes les données au repos ou en transit. L’offre ne permet pas aux clients de chiffrer les données d’annuaire avec leur propre clé de chiffrement. 

Pour déterminer la façon dont le pays sélectionné est mappé à l’emplacement physique de son annuaire, consultez l’article « Où se trouvent vos données ».

- [Article Où se trouvent vos données](https://www.microsoft.com/trust-center/privacy/data-location)

À mesure que le client utilise différents outils, fonctionnalités et applications Azure AD qui interagissent avec son annuaire, utilisez l’article Azure Active Directory – Où se trouvent vos données ?

- [Tableau de bord Où se trouvent vos données](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)

- [Documentation sur les rôles Azure AD](/azure/active-directory/roles/)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4 : Chiffrement des informations sensibles en transit

**Conseils** : En complément des contrôles d’accès, les données en transit doivent être protégées contre les attaques « hors bande » (par exemple, la capture de trafic) à l’aide du chiffrement pour empêcher les attaquants de lire ou modifier facilement les données.

Azure AD prend en charge le chiffrement des données en transit avec TLS 1.2 ou version ultérieure.

C’est certes facultatif pour le trafic sur les réseaux privés, mais essentiel pour le trafic sur les réseaux externes et publics. Pour le trafic HTTP, vérifiez que les clients se connectant à vos ressources Azure peuvent négocier TLS v1.2 ou une version ultérieure. Pour la gestion à distance, utilisez SSH (pour Linux) ou RDP/TLS (pour Windows) plutôt qu’un protocole non chiffré. Les versions et protocoles SSL, TLS et SSH rendus obsolètes et les chiffrements faibles doivent être désactivés.

Par défaut, Azure assure le chiffrement des données en transit entre les centres de données Azure.

- [Présentation du chiffrement en transit avec Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

- [Informations sur la sécurité TLS](/security/engineering/solving-tls1-problem) 

- [Double chiffrement pour les données Azure en transit](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="asset-management"></a>Gestion des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des ressources](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1 : S’assurer que l’équipe de sécurité a une visibilité sur les risques pour les ressources

**Conseils** : Assurez-vous que les équipes de sécurité reçoivent des autorisations de lecteur de sécurité dans votre locataire et vos abonnements Azure afin qu’elles puissent surveiller les risques de sécurité à l’aide d’Azure Security Center. 

Selon la façon dont les responsabilités de l’équipe de sécurité sont structurées, la surveillance des risques de sécurité peut tenir de la responsabilité d’une équipe de sécurité centrale ou d’une équipe locale. Cela dit, les insights et les risques liés à la sécurité doivent toujours être agrégés de manière centralisée au sein d’une organisation. 

Les autorisations de lecteur de sécurité peuvent être appliquées globalement à un locataire entier (groupe d’administration racine) ou étendues à des groupes d’administration ou à des abonnements spécifiques. 

Des autorisations supplémentaires peuvent être nécessaires pour obtenir une visibilité sur les charges de travail et services. 

- [Vue d’ensemble du rôle lecteur de sécurité](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Vue d'ensemble des groupes d'administration Azure](../../governance/management-groups/overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Conseils** : Utilisez l’accès conditionnel Azure Active Directory (Azure AD) pour limiter la capacité des utilisateurs à interagir avec Azure AD via le portail Azure en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ».

- [Guide pratique pour configurer l’accès conditionnel de façon à bloquer l’accès à Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="logging-and-threat-detection"></a>Journalisation et détection des menaces

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Journalisation et détection des menaces](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1 : Activer la détection des menaces pour les ressources Azure

**Conseil** : Utilisez la fonctionnalité de détection des menaces intégrée à la protection d’identité Azure Active Directory (Azure AD) pour vos ressources Azure AD. 
 
 
 

 
Azure AD produit des journaux d’activité qui sont souvent utilisés pour la détection des menaces et la recherche de menaces. Les journaux de connexion Azure AD fournissent un enregistrement de l’activité d’authentification et d’autorisation pour les utilisateurs, les services et les applications. Les journaux d’audit Azure AD suivent les modifications apportées à un locataire Azure AD, y compris les modifications qui améliorent ou limitent la posture de sécurité.

- [Qu’est-ce qu’Azure Active Directory Identity Protection ?](../identity-protection/overview-identity-protection.md)

- [Connecter les données Azure AD Identity Protection à Azure Sentinel](../../sentinel/connect-azure-ad-identity-protection.md)

- [Connecter les données Azure Active Directory à Azure Sentinel](../../sentinel/connect-azure-active-directory.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2 : Activer la détection des menaces pour la gestion des identités et des accès Azure

**Conseils** : Azure Active Directory (Azure AD) fournit les journaux d’utilisateur suivants, qui peuvent être consultés dans les rapports Azure AD ou intégrés à Azure Monitor, Azure Sentinel ou autres outils de surveillance/SIEM pour des cas d’usage plus sophistiqués de surveillance et d’analyse : 
- Connexions – le rapport de connexions fournit des informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs. 
- Journaux d’audit : traçabilité proposée via des journaux d’activité pour toutes les modifications effectuées par diverses fonctionnalités au sein d’Azure AD. Par exemple, les journaux d’audit peuvent inclure les modifications apportées à des ressources dans Azure AD comme l’ajout ou la suppression d’utilisateurs, d’applications, de groupes, de rôles, de stratégies, etc. 
- Connexions risquées : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. 
- Utilisateurs à risque - Un utilisateur à risque indique un compte d’utilisateur susceptible d’être compromis. 

Les détections de risques de la protection des identités sont activées par défaut et ne nécessitent aucun processus d’intégration. Les données de granularité ou de risque sont déterminées par la référence SKU de la licence. 

- [Rapports d’activité d’audit dans Azure Active Directory](../reports-monitoring/concept-audit-logs.md)  

- [Activer Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)  

- [Protection contre les menaces dans Azure Security Center](/azure/security-center/threat-protection)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4 : Activer la journalisation pour les ressources Azure

**Conseil** : Azure Active Directory (Azure AD) génère des journaux d’activité. Contrairement à certains services Azure, Azure AD ne fait pas de distinction entre les journaux d’activité et de ressources. Les journaux d’activité sont automatiquement disponibles dans la section Azure AD du portail Azure et peuvent être exportés vers Azure Monitor, Azure Event Hubs, le Stockage Azure, des SIEM et autres emplacements.
 
- Connexions – le rapport de connexions fournit des informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.  
 
- Journaux d’audit : traçabilité proposée via des journaux d’activité pour toutes les modifications effectuées par diverses fonctionnalités au sein d’Azure AD. Par exemple, les journaux d’audit peuvent inclure les modifications apportées à des ressources dans Azure AD comme l’ajout ou la suppression d’utilisateurs, d’applications, de groupes, de rôles, de stratégies, etc.  
 
 

Azure AD fournit également des journaux liés à la sécurité qui peuvent être consultés dans les rapports Azure AD ou intégrés à Azure Monitor, Azure Sentinel ou d’autres outils de surveillance/SIEM pour des cas d’usage plus sophistiqués de surveillance et d’analytique : 
- Connexions risquées : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur.
- Utilisateurs à risque - Un utilisateur à risque indique un compte d’utilisateur susceptible d’être compromis. 

Les détections de risques de la protection des identités sont activées par défaut et ne nécessitent aucun processus d’intégration. Les données de granularité ou de risque sont déterminées par la référence SKU de la licence. 

 
- [Rapports d’activité et de sécurité dans Azure Active Directory](../reports-monitoring/overview-reports.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5 : Centraliser la gestion et l’analyse des journaux de sécurité

**Conseil** : Nous vous recommandons de suivre les instructions ci-dessous lorsque les clients souhaitent centraliser leurs journaux de sécurité afin de faciliter la chasse aux menaces et l’analyse de la posture de sécurité :
 
- Centralisez le stockage et l’analyse de la journalisation pour activer la corrélation. Pour chaque source de journal dans Azure AD, assurez-vous d’avoir attribué un propriétaire de données, des conseils d’accès, un emplacement de stockage, les outils utilisés pour traiter les données et y accéder, ainsi que les exigences de conservation des données.  
 
- Veillez à intégrer les journaux d’activité Azure dans votre journalisation centralisée. Ingérez des journaux par le biais d’Azure Monitor pour agréger les données de sécurité générées par les appareils de point de terminaison, les ressources réseau et d’autres systèmes de sécurité. Dans Azure Monitor, utilisez des espaces de travail Log Analytics pour interroger et effectuer l’analytique, et utilisez des comptes Stockage Azure pour le stockage à long terme et l’archivage.  
 
- En outre, activez et intégrez les données dans Azure Sentinel ou une solution SIEM tierce.  
 

De nombreuses organisations choisissent d’utiliser Azure Sentinel pour les données « chaudes » qui sont utilisées fréquemment et Stockage Azure pour les données « froides » qui sont utilisées moins fréquemment.  
 

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)   
 

- [Guide pratique pour intégrer Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="lt-6-configure-log-storage-retention"></a>LT-6 : Configurer la rétention du stockage des journaux

**Conseil** : Vérifiez que la période de conservation des journaux définie dans les comptes de stockage ou les espaces de travail Log Analytics utilisés pour le stockage des journaux d’Azure Advisor est conforme aux obligations réglementaires de votre organisation.

Dans Azure Monitor, vous pouvez définir la période de rétention de votre espace de travail Log Analytics en fonction des règles de conformité de votre organisation. Utilisez des comptes Stockage Azure, Data Lake ou d’espace de travail Log Analytics pour le stockage à long terme et l’archivage.

- [Comment configurer la période de conservation d’un espace de travail Log Analytics](/azure/azure-monitor/platform/manage-cost-storage)  

- [Stockage des journaux des ressources dans un compte de stockage Azure](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1 : Préparation – mettre à jour le processus de réponse aux incidents pour Azure

**Conseils** : Assurez-vous que votre organisation dispose de processus pour répondre aux incidents de sécurité, qu’elle a mis à jour ces processus pour Azure et qu’elle les exerce régulièrement pour garantir la préparation.

- [Implémenter la sécurité dans l’environnement de l’entreprise](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guide de référence sur les réponses aux incidents](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2 : Préparation – configurer la notification d’incident

**Conseils** : Configurez les coordonnées des personnes à contacter en cas d’incident de sécurité dans Azure Security Center. Microsoft utilisera ces coordonnées afin de vous contacter si le Microsoft Security Response Center (MSRC) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé. Vous avez également la possibilité de personnaliser les alertes et les notifications d’incidents dans différents services Azure en fonction de vos besoins en matière de réponse aux incidents. 

- [Comment définir le contact de sécurité d’Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3 : Détection et analyse - créer des incidents en fonction d’alertes de haute qualité 

**Instructions** : Veillez à avoir un processus de création d’alertes de bonne qualité et de mesure de la qualité des alertes. Cela vous permet de tirer les leçons des incidents passés et de classer par ordre de priorité les alertes pour les analystes, afin qu’ils ne perdent pas de temps sur les faux positifs. 

Vous pouvez créer des alertes de bonne qualité en vous basant sur l’expérience des incidents passés, sur les sources validées par la communauté, et sur des outils conçus pour générer et nettoyer les alertes en fusionnant et en mettant en corrélation différentes sources de signaux. 

Azure Security Center fournit des alertes de haute qualité sur de nombreuses ressources Azure. Vous pouvez utiliser le connecteur de données ASC pour diffuser en continu les alertes vers Azure Sentinel. Azure Sentinel vous permet de créer des règles d’alerte avancées pour générer automatiquement des incidents à des fins d’enquête. 

Exportez vos alertes et recommandations Azure Security Center en utilisant la fonctionnalité d’exportation pour identifier les risques pesant sur les ressources Azure. Exportez les alertes et les recommandations manuellement ou automatiquement de manière continue.

- [Procédure de configuration de l’exportation](../../security-center/continuous-export.md)

- [Comment envoyer des alertes à Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4 : Détection et analyse – enquêter sur un incident

**Conseils** : Veiller à ce que les analystes puissent interroger et utiliser diverses sources de données lorsqu’ils enquêtent sur des incidents potentiels, afin d’obtenir une vision complète de ce qui s’est passé. Différents journaux doivent être collectés pour suivre les activités d’un attaquant potentiel tout au long de la chaîne de destruction afin d’éviter les angles morts.  Vous devez également vous assurer que les insights et les enseignements sont capturés pour d’autres analystes et pour une référence historique future.  

Les sources de données à examiner comprennent les sources de journalisation centralisées qui sont déjà collectées auprès des services et des systèmes en fonctionnement concernés, mais elles peuvent également inclure les éléments suivants :

- Données réseau : utilisez les journaux de flux des groupes de sécurité réseau, Azure Network Watcher et Azure Monitor pour capturer des journaux de flux réseau et d’autres informations analytiques. 

- Captures instantanées des systèmes en fonctionnement : 

    - Utilisez la capacité de capture instantanée de la machine virtuelle Azure pour créer un instantané du disque du système en fonctionnement. 

    - Utilisez la capacité native de sauvegarde de la mémoire du système d’exploitation pour créer un instantané de la mémoire du système en fonctionnement.

    - Utilisez la capacité de capture instantanée des services Azure ou celle de votre logiciel pour créer des instantanés des systèmes en fonctionnement.

Azure Sentinel fournit des analyses de données approfondies sur pratiquement toutes les sources de journal et un portail de gestion des cas pour gérer le cycle de vie complet des incidents. Les renseignements obtenus au cours d’une enquête peuvent être associés à un incident à des fins de suivi et de rapport. 

- [Capture instantanée du disque d’un ordinateur Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Capture instantanée du disque d’un ordinateur Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Collecte de l’image mémoire et des informations de diagnostic par le support Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Examiner les incidents avec Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5 : Détection et analyse – classer par ordre de priorité les incidents

**Conseils** : Donnez aux analystes le contexte sur lequel les incidents doivent se concentrer en premier lieu en fonction de la gravité de l’alerte et de la sensibilité des ressources. 

Azure Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance accordé par Security Center à la découverte ou à l’analytique ayant servi à émettre l’alerte, ainsi que du niveau de confiance quant à l’existence d’une intention malveillante sous-jacente dans l’activité à l’origine de l’alerte.

En outre, marquez les ressources à l’aide d’étiquettes et créez un système de nommage pour identifier et classer les ressources Azure, en particulier celles qui traitent des données sensibles.  Il vous incombe de hiérarchiser le traitement des alertes en fonction de la criticité des ressources et de l’environnement Azure où l’incident s’est produit.

- [Alertes de sécurité dans le Centre de sécurité Azure](../../security-center/security-center-alerts-overview.md)

- [Organisation des ressources Azure à l’aide de catégories](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6 : Confinement, éradication et récupération – automatiser la gestion des incidents

**Conseils** : Automatisez les tâches manuelles répétitives pour accélérer le temps de réponse et réduire la charge de travail des analystes. Les tâches manuelles sont plus longues à exécuter, ce qui ralentit chaque incident et réduit le nombre d’incidents qu’un analyste peut traiter. Les tâches manuelles augmentent également la fatigue des analystes, ce qui accroît le risque d’erreur humaine entraînant des retards et dégrade la capacité des analystes à se concentrer efficacement sur des tâches complexes. Utilisez les fonctionnalités d’automatisation des workflows dans Azure Security Center et Azure Sentinel pour déclencher automatiquement des actions ou exécuter un playbook pour répondre aux alertes de sécurité entrantes. Le playbook prend des mesures, telles que l’envoi de notifications, la désactivation de comptes et l’isolement des réseaux problématiques. 

- [Configurer l’automatisation du workflow dans Security Center](../../security-center/workflow-automation.md)

- [Configurer des réponses automatisées aux menaces dans Azure Security Center](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Configurer des réponses automatisées aux menaces dans Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="posture-and-vulnerability-management"></a>Gestion de la posture et des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion de la posture et des vulnérabilités](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1 : Établir des configurations sécurisées pour les services Azure 

**Conseil** : Les solutions de gestion des identités et des accès de Microsoft aident les services informatiques à protéger l’accès aux applications et aux ressources localement et dans le cloud. Il est important que les organisations suivent les meilleures pratiques de sécurité pour garantir que leur implémentation de la gestion des identités et des accès est sécurisée et plus résistante aux attaques. 

En fonction de votre stratégie d’implémentation de la gestion des identités et des accès, votre organisation doit suivre les recommandations de Microsoft pour sécuriser votre infrastructure d’identité. 

Les organisations qui collaborent avec des partenaires externes doivent également évaluer et mettre en œuvre des configurations de gouvernance, de sécurité et de conformité appropriées pour réduire les risques de sécurité et protéger les ressources sensibles.

- [Bonnes pratiques en matière de sécurité du contrôle d’accès et de la gestion des identités Azure](../../security/fundamentals/identity-management-best-practices.md)

- [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/fundamentals/steps-secure-identity.md)

- [Sécurisation de la collaboration externe dans Azure Active Directory et Microsoft 365](secure-external-access-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2 : Supporter des configurations sécurisées pour les services Azure

**Conseil** : Le niveau de sécurité Microsoft fournit aux organisations une mesure de leur posture de sécurité et des recommandations qui peuvent aider à protéger les organisations contre les menaces. Il est recommandé que les organisations examinent régulièrement leur niveau de sécurité pour obtenir des actions d’amélioration suggérées afin d’améliorer leur posture de sécurité des identités. 

- [Qu’est-ce que le degré de sécurisation Identity Secure Score dans Azure Active Directory ?](identity-secure-score.md)

- [Score sécurisé Microsoft](/microsoft-365/security/mtp/microsoft-secure-score)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8 : Effectuer une simulation d’attaque régulière

**Conseils** : Selon les besoins, effectuez un test d’intrusion ou des activités Red Team sur vos ressources Azure et résolvez tous les problèmes de sécurité critiques détectés.
Suivez les règles d’engagement de pénétration du cloud Microsoft pour vous assurer que vos tests d’intrusion sont conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft.

- [Test d’intrusion dans Azure](../../security/fundamentals/pen-testing.md)

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="governance-and-strategy"></a>Gouvernance et stratégie

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gouvernance et stratégie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1 : Définir la stratégie de gestion des ressources et de protection des données 

**Conseils** : Veillez à documenter et à communiquer une stratégie claire pour la surveillance et la protection continues des systèmes et des données. Hiérarchisez la découverte, l’évaluation, la protection et la surveillance des données et systèmes critiques de l’entreprise. 
 

 
Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 
 
-   Norme de classification des données en fonction des risques pour l’entreprise
 
-   Visibilité de l’organisation de sécurité sur les risques et l’inventaire des actifs 
 
-   Approbation de l’organisation de sécurité pour les services Azure en vue de leur utilisation 
 
-   Sécurité des ressources tout au long de leur cycle de vie
 
-   Stratégie de contrôle d’accès requise conformément à la classification des données organisationnelles
 
-   Utilisation des fonctionnalités de protection des données Azure natives et de tiers
 
-   Exigences de chiffrement des données pour les cas d’utilisation en transit et au repos
 
-   Normes de chiffrement appropriées
 

 
Pour plus d’informations, consultez les références suivantes : 
- [Recommandation d’architecture de sécurité Azure - Stockage, données et chiffrement](/azure/architecture/framework/security/storage-data-encryption)
 

 
- [Notions de base de la sécurité Azure - Sécurité, chiffrement et stockage des données Azure](../../security/fundamentals/encryption-overview.md) 

 
- [Cloud Adoption Framework - Meilleures pratiques en matière de chiffrement et de sécurité des données Azure](../../security/fundamentals/data-encryption-best-practices.md) 

 
- [Benchmark de sécurité Azure - Gestion des ressources](/azure/security/benchmarks/security-benchmark-v2-asset-management) 

 
- [Benchmark de sécurité Azure - Protection des données](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2 : Définir la stratégie de segmentation d'entreprise 

**Conseils** : Établissez une stratégie à l'échelle de l'entreprise pour segmenter l'accès aux ressources à l'aide d'une combinaison de contrôles d'identité, de réseau, d'application, d'abonnement, de groupe de gestion et autres.

Trouvez le bon équilibre entre la nécessité de séparation sur le plan de la sécurité et la nécessité d'exécuter quotidiennement les systèmes qui doivent communiquer entre eux et accéder aux données.

Veillez à ce que la stratégie de segmentation soit implémentée de manière cohérente pour tous les types de contrôle, y compris pour les modèles d'identité, d'accès et de sécurité du réseau, les modèles d'autorisation/d'accès aux applications et les contrôles des processus humains.

- [Aide relative à la stratégie de segmentation dans Azure (vidéo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Aide relative à la stratégie de segmentation dans Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Aligner la segmentation du réseau avec la stratégie de segmentation d’entreprise](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3 : Définir la stratégie de gestion de la posture de la sécurité

**Conseils** : Mesurez et atténuez en permanence les risques liés à vos ressources individuelles et à l’environnement dans lequel elles sont hébergées. Priorisez les ressources à valeur élevée et les surfaces d’attaque hautement exposées, comme les applications publiées, les points d’entrée et de sortie du réseau, les points de terminaison utilisateur et administrateur, etc.

- [Benchmark de sécurité Azure - Gestion de la posture et des vulnérabilités](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4 : Aligner les rôles et les responsabilités de l’organisation

**Conseils** : Veillez à documenter et à communiquer une stratégie claire pour les rôles et les responsabilités de votre organisation de sécurité. Veillez à définir clairement les responsabilités pour les décisions relatives à la sécurité, à former tout le monde au modèle de responsabilité partagée et à former les équipes techniques à la technologie permettant de sécuriser le cloud.

- [Meilleures pratiques pour la sécurité Azure 1 – Personnes : Former les équipes pour le parcours vers la sécurité dans le cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Meilleures pratiques pour la sécurité Azure 2 – Personnes : Former les équipes pour les technologies de sécurité dans le cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Meilleures pratiques pour la sécurité Azure 3 – Processus : Affecter les responsabilités pour les décisions de sécurité dans le cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="gs-5-define-network-security-strategy"></a>GS-5 : Définir la stratégie de sécurité réseau

**Conseils** : Établissez une approche de sécurité réseau Azure dans le cadre de la stratégie de contrôle d’accès de sécurité globale de votre organisation.  

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Responsabilité centralisée pour la gestion et la sécurité du réseau

-   Modèle de segmentation de réseau virtuel aligné avec la stratégie de segmentation de l’entreprise

-   Stratégie de correction dans différents scénarios de menaces et d’attaques

-   Stratégie de périphérie d’Internet et d’entrée et de sortie

-   Stratégie de cloud hybride et d’interconnexion locale

-   Artefacts de sécurité réseau à jour (par exemple diagrammes réseau, architecture de réseau de référence)

Pour plus d’informations, consultez les références suivantes :
- [Meilleures pratiques pour la sécurité Azure 11 – Architecture. Stratégie de sécurité unifiée unique](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark de sécurité Azure – Sécurité réseau](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Vue d’ensemble de la sécurité réseau d’Azure](../../security/fundamentals/network-overview.md)

- [Stratégie d’architecture de réseau d’entreprise](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6 : Définir une stratégie d’accès privilégié et d’identité

**Conseils** : Établissez une approche d’identité Azure et d’accès privilégié dans le cadre de la stratégie de contrôle d’accès de sécurité globale de votre organisation.  

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Un système centralisé d’identité et d’authentification et son interconnexion avec d’autres systèmes d’identité internes et externes

-   Méthodes d’authentification fortes dans différents cas d’usage et différentes conditions

-   Protection des utilisateurs disposant de privilèges élevés

-   Surveillance et gestion des activités anormales des utilisateurs  

-   Vérification de l’identité et de l’accès des utilisateurs et processus de rapprochement

Pour plus d’informations, consultez les références suivantes :

- [Benchmark de sécurité Azure - Gestion des identités](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark de sécurité Azure - Accès privilégié](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Meilleures pratiques pour la sécurité Azure 11 – Architecture. Stratégie de sécurité unifiée unique](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Vue d’ensemble de la sécurité et de la gestion des identités Azure](../../security/fundamentals/identity-management-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7 : Définir la stratégie de journalisation et de réponse aux menaces

**Conseils** : Établissez une stratégie de journalisation et de réponse aux menaces pour détecter et corriger rapidement les menaces tout en répondant aux exigences de conformité. En priorité, fournissez aux analystes des alertes de bonne qualité et des expériences homogènes afin qu’ils puissent se concentrer sur les menaces plutôt que sur l’intégration et les étapes manuelles. 

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Rôle et responsabilités de l’organisation d’opérations de sécurité (SecOP) 

-   Un processus de réponse aux incidents bien défini, aligné avec NIST ou autre cadre réglementaire du secteur 

-   Capture et rétention des journaux pour prendre en charge la détection des menaces, la réponse aux incidents et les besoins de conformité

-   Visibilité centralisée des informations de corrélation sur les menaces, avec SIEM, les fonctionnalités Azure natives et d’autres sources 

-   Plan de communication et de notification avec vos clients, fournisseurs et les parties publiques pertinentes

-   Utilisation de plateformes Azure natives et tierces pour la gestion des incidents, comme la journalisation et la détection des menaces, les investigations et la correction et l’éradication des attaques

-   Processus de gestion des incidents et des activités postérieures aux incidents, comme les leçons apprises et la rétention des preuves

Pour plus d’informations, consultez les références suivantes :

- [Benchmark de sécurité Azure - Journalisation et détection des menaces](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Benchmark de sécurité Azure - Réponse aux incidents](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Meilleures pratiques pour la sécurité Azure 4 - Processus. Mise à jour des processus de réponse aux incidents pour le cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guide pour le cadre d’adoption d’Azure, la journalisation et la prise de décision pour les rapports](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Mise à l’échelle, gestion et surveillance d’entreprise Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
