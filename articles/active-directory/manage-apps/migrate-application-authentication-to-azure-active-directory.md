---
title: Migrer l’authentification des applications vers Azure Active Directory
description: Ce livre blanc décrit en détail la planification et les avantages de la migration de l’authentification de votre application vers Azure AD.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/05/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7e4a92f84cdbc0061509fa710de148e9e9f2b52
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108125154"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Migrer l’authentification des applications vers Azure Active Directory

## <a name="about-this-paper"></a>À propos de ce document

Ce livre blanc décrit en détail la planification et les avantages de la migration de l’authentification de votre application vers Azure AD. Il est conçu pour les administrateurs Azure et les experts spécialistes de l’identité.

En divisant le processus en quatre phases, chacune avec des critères détaillés de planification et de sortie, il est conçu pour vous aider à planifier votre stratégie de migration et à comprendre comment Azure AD Authentication prend en charge vos objectifs organisationnels.

## <a name="introduction"></a>Introduction

Aujourd’hui, votre organisation requiert une multitude d’applications pour permettre aux utilisateurs d’effectuer leur travail. Vous continuez probablement à ajouter, développer ou mettre hors service des applications tous les jours. Les utilisateurs accèdent à ces applications sur un large éventail d’appareils d’entreprise et personnels, et à partir de divers emplacements. Ils ouvrent les applications de nombreuses façons, par exemple :

- Via la page d’accueil ou le portail de l’entreprise

- À partir de signets sur leurs navigateurs

- Via l’URL d’un fournisseur pour les applications SaaS (software as a service)

- À l’aide de liens envoyés directement sur des ordinateurs de bureau ou des appareils mobiles de l’utilisateur via une solution de gestion des appareils mobiles/applications (MDM/MAM)

Vos applications utilisent probablement les types d’authentification suivants :

- Solutions de fédération locales (par exemple services de fédération Active Directory (ADFS) et Ping)

- Active Directory (par exemple authentification Kerberos et authentification intégrée Windows)

- Autres solutions de gestion des identités et des accès (IAM) basées sur le cloud (par exemple Okta ou Oracle)

- Infrastructure web locale (par exemple IIS et Apache)

- Infrastructure hébergée dans le cloud (par exemple Azure et AWS)

**Pour vous assurer que les utilisateurs peuvent accéder facilement et en toute sécurité aux applications, votre objectif est de disposer d’un ensemble unique de contrôles et de stratégies d’accès dans vos environnements locaux et sur le cloud.**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) propose une plateforme d’identités universelle qui offre à vos employés, partenaires et clients une identité unique pour accéder aux applications qu’ils souhaitent et collaborer à partir de n’importe quelle plateforme et de n’importe quel appareil.

![Diagramme de connectivité Azure Active Directory](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD comprend une [suite complète de fonctionnalités de gestion des identités](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad). La standardisation de l’authentification et de l’autorisation de votre application avec Azure AD permet d’offrir les avantages de ces fonctionnalités.

Vous trouverez d’autres ressources de migration à l’adresse [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Avantages de la migration de l’authentification des applications vers Azure AD

La migration de l’authentification des applications vers Azure AD vous aide à gérer les risques et les coûts, accroître la productivité et répondre aux exigences de conformité et de gouvernance.

### <a name="manage-risk"></a>Gérer le risque.

Pour protéger vos applications, vous devez disposer d’une vue complète de tous les facteurs de risque. La migration de vos applications vers Azure AD consolide vos solutions de sécurité. Cela vous permet d’effectuer les opérations suivantes :

- Améliorer l’accès des utilisateurs sécurisés aux applications et aux données d’entreprise associées à l’aide de [stratégies d’accès conditionnel](../conditional-access/overview.md), [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) et des technologies [Identity Protection](../identity-protection/overview-identity-protection.md) en temps réel basées sur les risques.

- Protéger l’accès des utilisateurs privilégiés à votre environnement avec un accès administrateur [juste-à-temps](../../azure-resource-manager/managed-applications/request-just-in-time-access.md).

- Utiliser la [conception d’Azure AD multi-locataire, géodistribuée, à haute disponibilité](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/) pour les besoins les plus critiques de votre entreprise.

- Protéger vos applications héritées avec l’une de nos [intégrations partenaires d’accès hybride sécurisées](https://aka.ms/secure-hybrid-access) que vous avez peut-être déjà déployées.

### <a name="manage-cost"></a>Gérer les coûts

Votre organisation peut être dotée de plusieurs solutions IAM (Identity Access Management). La migration vers une infrastructure Azure AD vous donne les moyens de réduire les dépendances sur les licences IAM (localement ou dans le cloud) et les coûts d’infrastructure. Si vous avez déjà payé l’utilisation d’Azure AD dans le cadre de licences Microsoft 365, il n’y a aucune raison de subir le coût supplémentaire d’une autre solution IAM.

**Avec Azure AD, vous pouvez réduire les coûts d’infrastructure en :**

- Offrant un accès à distance sécurisé aux applications locales utilisant le [proxy d’application Azure AD](./application-proxy.md).

- Découplant les applications de l’approche locale en matière d’informations d’identification dans votre locataire en [configurant Azure AD comme fournisseur d’identité universel approuvé](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization).

### <a name="increase-productivity"></a>Augmenter la productivité

Les économies et les avantages en termes de sécurité motivent les organisations à adopter Azure AD, mais l’adoption et la conformité complètes sont plus probables si cette mesure présente également des avantages pour les utilisateurs. Avec Azure AD, vous pouvez :

- Améliorer l’expérience d’[authentification unique (SSO)](./what-is-single-sign-on.md) de l’utilisateur final grâce à un accès transparent et sécurisé à toute application, depuis n’importe quel appareil et n’importe quel emplacement.

- Utiliser des fonctionnalités IAM en libre-service, telles que les [réinitialisations des mots de passe en libre-service](../authentication/concept-sspr-howitworks.md) et la [gestion des groupes en libre-service](../enterprise-users/groups-self-service-management.md).

- Réduire la charge administrative en gérant une seule identité pour chaque utilisateur dans les environnements cloud et locaux :

  - [Automatiser le provisionnement](../app-provisioning/user-provisioning.md) des comptes utilisateur (dans la [galerie Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)) basé sur les identités Azure AD
  - Accéder à toutes vos applications à partir du panneau MyApps dans le [portail Azure ](https://portal.azure.com/)

- Permettre aux développeurs de sécuriser l’accès à leurs applications et d’améliorer l’expérience de l’utilisateur final à l’aide de la [Plateforme d’identités Microsoft](../develop/v2-overview.md) avec la bibliothèque d’authentification Microsoft (MSAL).

- Donner à vos partenaires l’accès aux ressources cloud à l’aide d’[Azure AD B2B Collaboration](../external-identities/what-is-b2b.md). Les ressources cloud suppriment la surcharge liée à la configuration de la fédération point à point avec vos partenaires.

### <a name="address-compliance-and-governance"></a>Assurer la gouvernance et la conformité

Garantissez la conformité aux exigences réglementaires en appliquant des stratégies d’accès d’entreprise et en surveillant l’accès des utilisateurs aux applications et aux données associées à l’aide d’outils d’audit et d’API intégrés. Avec Azure AD, vous pouvez surveiller les connexions aux applications grâce aux rapports utilisant des [outils SIEM (Security Incident and Event Monitoring)](../reports-monitoring/plan-monitoring-and-reporting.md). Vous pouvez accéder aux rapports à partir du portail ou des API et effectuer un audit programmatiquement pour identifier les utilisateurs qui ont accès à vos applications et supprimer l’accès aux utilisateurs inactifs via des révisions d’accès.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Planifier vos phases de migration et votre stratégie de projet

Quand des projets technologiques échouent, cela est souvent dû à des attentes incompatibles, au fait que les parties prenantes n’ont pas été impliquées ou à un manque de communication. Assurez votre réussite en planifiant le projet lui-même.

### <a name="the-phases-of-migration"></a>Phases de la migration

Avant de passer aux outils, vous devez comprendre comment concevoir le processus de migration. Par le biais de plusieurs ateliers en contact direct avec le client, nous vous recommandons d’effectuer les quatre phases suivantes :

![Diagramme des phases de la migration](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Constituer l’équipe de projet

La migration d’applications est un travail d’équipe, vous devez donc vous assurer que tous les postes essentiels sont occupés. Le soutien des principaux responsables commerciaux est important. Veillez à impliquer un groupe approprié de responsables commanditaires, de décideurs de l’entreprise et d’experts (SME).

Pendant le projet de migration, une personne peut jouer plusieurs rôles ou chaque rôle peut être attribué à une personne différente, en fonction de la taille et de la structure de votre organisation. Vous pouvez également dépendre d’autres équipes qui jouent un rôle clé dans votre environnement de sécurité.

Le tableau suivant présente les rôles clés et leurs contributions :

| Role          | Contributions                                              |
| ------------- | ---------------------------------------------------------- |
| **Chef de projet** | Coach en charge du projet, notamment :<br /> – obtenir le soutien de la direction<br /> – impliquer les parties prenantes<br /> – gérer la planification, la documentation et la communication |
| **Architecte Identity/administrateur Azure AD App** | En charge de :<br /> – concevoir la solution en collaboration avec les parties prenantes<br /> – documenter la conception de la solution et les procédures opérationnelles pour le transfert à l’équipe en charge des opérations<br /> – gérer les environnements de pré-production et de production |
| **Équipe des opérations AD locale** | Il s’agit de l’organisation qui gère les différentes sources d’identité locales, telles que les forêts AD, les annuaires LDAP, les systèmes RH, etc.<br /> – effectuer les tâches de correction nécessaires avant la synchronisation<br /> – fournir les comptes de service requis pour la synchronisation<br /> – fournir un accès pour configurer la fédération à Azure AD |
| **Responsable du support informatique** | Représentant de l’organisation du support informatique qui peut fournir une entrée sur la capacité de prise en charge de cette modification du point de vue du support technique. |
| **Propriétaire de la sécurité**  | Représentant de l’équipe de sécurité qui peut garantir que le plan répondra aux exigences de sécurité de votre organisation. |
| **Propriétaires techniques des applications** | Comprend les propriétaires techniques des applications et services à intégrer dans Azure AD. Ils fournissent les attributs d’identité des applications qui doivent être inclus dans le processus de synchronisation. Ils sont généralement en relation avec les représentants CSV. |
| **Propriétaires commerciaux d’applications** | Collègues des services commerciaux qui peuvent fournir des informations sur l’expérience utilisateur et l’utilité de cette modification du point de vue de l’utilisateur. Ils sont propriétaires de l’application du point de vue commercial global, ce qui peut inclure la gestion de l’accès. |
| **Groupe pilote d’utilisateurs** | Utilisateurs qui effectuent des tests dans le cadre de leur travail quotidien, de l’expérience pilote et fournissent des commentaires pour guider les autres déploiements. |

### <a name="plan-communications"></a>Planifier les communications

Une communication et un engagement commerciaux efficaces sont primordiaux pour la réussite. Il est important de fournir aux parties prenantes et aux utilisateurs finaux un moyen d’obtenir des informations et de rester informés des mises à jour planifiées. Sensibilisez tout le monde à la valeur de la migration, à la chronologie attendue et à la planification de toute interruption temporaire de l’activité. Utilisez plusieurs canaux d’information, telles que les sessions de briefing, les e-mails, les réunions individuelles, les bannières et les réunions publiques.

En fonction de la stratégie de communication que vous avez choisie pour l’application, vous souhaiterez peut-être rappeler aux utilisateurs les temps d’arrêt à venir. Vous devez également vérifier qu’il n’y a aucune modification récente ou aucun impact commercial nécessitant le report du déploiement.

Dans le tableau suivant, vous trouverez la communication minimale suggérée pour informer les parties prenantes :

**Phases de planification et stratégie de projet** :

| Communication      | Public visé                                          |
| ------------------ | ------------------------------------------------- |
| Sensibilisation et valeur commerciale/technique du projet | Tous sauf les utilisateurs finaux |
| Sollicitation des applications pilotes | – Propriétaires commerciaux de l’application<br />– Propriétaires techniques de l’application<br />– Architectes et équipe Identity |

**Phase 1 – découverte et étendue** :

| Communication      | Public visé                                          |
| ------------------ | ------------------------------------------------- |
| – Sollicitation des informations sur l’application<br />– Résultat de l’exercice d’étendue | – Propriétaires techniques de l’application<br />– Propriétaires commerciaux de l’application |

**Phase 2 – classifier les applications et planifier le pilote** :

| Communication      | Public visé                                          |
| ------------------ | ------------------------------------------------- |
| – Résultat des classifications et implications pour la planification de la migration<br />– Planification de la migration préliminaire | – Propriétaires techniques de l’application<br /> – Propriétaires commerciaux de l’application |

**Phase 3 – planifier la migration et le test** :

| Communication      | Public visé                                          |
| ------------------ | ------------------------------------------------- |
| – Résultat du test de migration de l’application | – Propriétaires techniques de l’application<br />– Propriétaires commerciaux de l’application |
| – Notification informant de la migration prochaine et explication des expériences des utilisateurs finaux en résultant.<br />– Temps d’arrêt prochains et communications complètes, notamment les actions nécessaires, les commentaires et comment obtenir de l’aide | – Utilisateurs finaux (et tous les autres) |

**Phase 4 – gérer et obtenir des informations** :

| Communication      | Public visé                                          |
| ------------------ | ------------------------------------------------- |
| Analytique disponible et accès | – Propriétaires techniques de l’application<br />– Propriétaires commerciaux de l’application |

### <a name="migration-states-communication-dashboard"></a>Tableau de bord de communication sur les états de la migration

La communication de l’état global du projet de migration est cruciale car elle montre la progression et aide les propriétaires d’applications dont la migration est proche à s’y préparer. Vous pouvez créer un tableau de bord simple à l’aide de Power BI ou d’autres outils de création de rapports pour fournir une visibilité sur l’état des applications pendant la migration.

Les états de la migration que vous pouvez envisager d’utiliser sont les suivants :

| États de migration       | Plan d’action                                   |
| ---------------------- | --------------------------------------------- |
| **Requête initiale** | Rechercher l’application et contacter le propriétaire pour plus d’informations |
| **Évaluation terminée** | Le propriétaire de l’application évalue la configuration requise des applications et retourne le questionnaire sur l’application</td>
| **Configuration en cours** | Développer les modifications nécessaires pour gérer l’authentification sur Azure AD |
| **Configuration de test réussie** | Évaluer les modifications et authentifier l’application sur le locataire de test Azure AD dans l’environnement de test |
| **Gestion de configuration de production** | Modifier les configurations pour travailler avec le locataire AD de production et évaluer l’authentification de l’application dans l’environnement de test |
| **Terminer/se déconnecter** | Déployer les modifications pour l’application dans l’environnement de production et exécuter sur le locataire de production Azure AD |

Cela permet aux propriétaires d’applications de connaître la planification de migration et de test de l’application quand leurs applications doivent être migrées prochainement, ainsi que les résultats provenant d’autres applications qui ont déjà été migrées. Vous pouvez également envisager de fournir des liens vers votre base de données de traqueur de bogues pour permettre aux propriétaires de signaler et d’afficher les problèmes des applications en cours de migration.

### <a name="best-practices"></a>Meilleures pratiques

Voici les témoignages de nos clients et partenaires, ainsi que les meilleures pratiques suggérées :

- [Cinq conseils pour améliorer le processus de migration vers Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) par Patriot Consulting, membre de notre réseau partenaire dont le but est d’aider les clients à déployer des solutions cloud Microsoft en toute sécurité.

- [Développer une stratégie de gestion des risques pour la migration de votre application Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) par Edgile, partenaire qui se concentre sur les solutions de gestion des identités et des accès (IAM) et de gestion des risques.

## <a name="phase-1-discover-and-scope-apps"></a>Phase 1 : Découverte et étendue des applications

**La découverte et l’analyse des applications est un exercice fondamental pour bien commencer.** Vous ne savez peut-être pas tout, donc préparez-vous pour les applications inconnues.

### <a name="find-your-apps"></a>Rechercher vos applications

Le premier point pour la migration d’une application est de décider quelles applications doivent migrer, quelles applications doivent rester le cas échéant et quelles applications doivent être déconseillées. Il existe toujours une opportunité de déconseiller les applications que vous n’utiliserez pas dans votre organisation. Il existe plusieurs façons de rechercher des applications dans votre organisation. **Quand vous découvrez des applications, assurez-vous d’inclure les applications en développement et en planification. Utilisez Azure AD pour l’authentification dans toutes les applications futures.**

**Utilisation des services de fédération Active Directory (ADFS) pour recueillir un inventaire des applications correct :**

- **Utiliser Azure AD Connect Health** Si vous disposez d’une licence Azure AD Premium, nous vous recommandons de déployer [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) pour analyser l’utilisation des applications dans votre environnement local. Vous pouvez utiliser le [rapport d’application ADFS](./migrate-adfs-application-activity.md) (préversion) pour découvrir les applications ADFS pouvant migrer et évaluer si l’application à migrer est prête. Une fois la migration terminée, déployez [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) qui vous permet de surveiller en continu l’informatique fantôme dans votre organisation une fois que vous êtes dans le cloud.

- **Analyse des journaux ADFS**. Si vous n’avez pas de licences Azure AD Premium, nous vous recommandons d’utiliser les outils de migration d’application ADFS vers Azure AD basés sur [PowerShell](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration). Reportez-vous au [Guide de solution](./migrate-adfs-apps-to-azure.md) :

[Migration d’applications des services de fédération Active Directory (AD FS) vers Azure AD.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Utilisation d’autres fournisseurs d’identité (IdP)

Pour les autres fournisseurs d’identité (par exemple Okta ou Ping), vous pouvez utiliser leurs outils pour exporter l’inventaire des applications. Vous pouvez envisager de consulter les principaux de service inscrits sur Active Directory qui correspondent aux applications web de votre organisation.

### <a name="using-cloud-discovery-tools"></a>Utilisation des outils Cloud Discovery

Dans l’environnement cloud, vous avez besoin d’une visibilité accrue, d’un contrôle sur le déplacement des données et d’une analytique sophistiquée pour rechercher et combattre les cybermenaces sur l’ensemble de vos services cloud. Vous pouvez collecter votre inventaire d’applications cloud à l’aide des outils suivants :

- **Cloud Access Security Broker (CASB**) – un [CASB](/cloud-app-security/) fonctionne généralement avec votre pare-feu pour offrir une visibilité de l’utilisation des applications cloud par vos employés et vous aide à protéger vos données d’entreprise contre les menaces de cybersécurité. Le rapport CASB peut vous aider à déterminer les applications les plus utilisées au sein de votre organisation, ainsi que les premières cibles à migrer vers Azure AD.

- **Cloud Discovery** – en configurant [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery), vous obtenez une visibilité sur l’utilisation des applications cloud et vous pouvez découvrir des applications non approuvées ou de type Shadow IT (informatique fantôme).

- **API** – pour les applications connectées à l’infrastructure cloud, vous pouvez utiliser les API et les outils sur ces systèmes pour commencer à effectuer un inventaire des applications hébergées. Dans l’environnement Azure :

  - Utilisez la cmdlet [Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) pour obtenir des informations sur les sites web Azure.

  - Utilisez la cmdlet [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) pour obtenir des informations sur vos applications web Azure.
D
  - Vous pouvez rechercher toutes les applications s’exécutant sur Microsoft IIS à partir de la ligne de commande Windows à l’aide de [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Utilisez les [applications](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) et les [principaux de service](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) pour obtenir des informations sur une application et une instance d’application dans un répertoire dans Azure AD.

### <a name="using-manual-processes"></a>Procédures manuelles

Après avoir suivi les approches automatisées décrites ci-dessus, vous avez une bonne maitrise de vos applications. Toutefois, vous pouvez envisager d’effectuer les opérations suivantes pour garantir une bonne couverture de tous les domaines d’accès utilisateur :

- Contactez les différents propriétaires commerciaux de votre organisation pour trouver les applications utilisées dans votre organisation.

- Exécutez un outil d’inspection HTTP sur votre serveur proxy ou analysez les journaux de proxy pour déterminer où le trafic est généralement routé.

- Consultez les journaux web des sites populaires du portail d’entreprise pour voir les liens auxquels les utilisateurs accèdent le plus.

- Contactez les responsables ou d’autres membres clés de l’entreprise pour vous assurer que vous avez couvert les applications qui sont vitales pour l’entreprise.

### <a name="type-of-apps-to-migrate"></a>Types d’applications à migrer

Une fois que vous avez trouvé vos applications, vous devez identifier ces types d’applications dans votre organisation :

- Applications qui utilisent déjà des protocoles d’authentification modernes

- Applications qui utilisent des protocoles d’authentification hérités que vous choisissez de moderniser

- Applications qui utilisent des protocoles d’authentification hérités que vous choisissez de ne PAS moderniser

- Nouvelles applications métier (LOB)

### <a name="apps-that-use-modern-authentication-already"></a>Applications qui utilisent déjà une authentification moderne

Les applications déjà modernisées sont les plus susceptibles d’être déplacées vers Azure AD. Ces applications utilisent déjà des protocoles d’authentification modernes (tels que SAML ou OpenID Connect) et peuvent être reconfigurées pour s’authentifier avec Azure AD.

Outre les choix proposés dans la [galerie d’applications Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), il peut s’agir d’applications qui existent déjà dans votre organisation ou d’applications tierces d’un fournisseur qui ne fait pas partie de la galerie Azure AD ([applications hors galerie](./add-application-portal.md)).

Applications héritées que vous choisissez de moderniser

Pour les applications héritées que vous souhaitez moderniser, le passage à Azure AD pour l’authentification et l’autorisation de base libère toute l’énergie et la richesse des données offertes par [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) et [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1).

Nous vous recommandons de **mettre à jour le code de la pile d’authentification** pour ces applications à partir du protocole hérité (par exemple l’authentification Windows intégrée, la délégation Kerberos contrainte, l’authentification basée sur les en-têtes HTTP) à un protocole moderne (par exemple SAML ou OpenID Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Applications héritées que vous choisissez de ne PAS moderniser

Pour certaines applications utilisant des protocoles d’authentification hérités, la modernisation de l’authentification n’est pas une option judicieuse pour des raisons commerciales. Il s’agit entre autres des types d’application suivants :

- Applications conservées localement pour des raisons de conformité ou de contrôle.

- Applications connectées à une identité locale ou à un fournisseur de fédération que vous ne souhaitez pas changer.

- Applications développées à l’aide de normes d’authentification locales que vous ne prévoyez pas de migrer

Azure AD peut apporter de formidables avantages à ces applications héritées, car vous pouvez activer des fonctionnalités de sécurité et de gouvernance modernes Azure AD telles que l’[authentification multifacteur](../authentication/concept-mfa-howitworks.md), l’[accès conditionnel](../conditional-access/overview.md), [Identity Protection](../identity-protection/index.yml), l’[accès à l’application déléguée](./access-panel-manage-self-service-access.md) et les [révisions d’accès](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) sur ces applications sans toucher à l’application elle-même.

Commencez par **étendre ces applications dans le cloud** avec le [proxy d’application](./application-proxy-configure-single-sign-on-password-vaulting.md) Azure AD à l’aide d’une simple authentification (comme le coffre des mots de passe) pour faire migrer rapidement vos utilisateurs ou via nos [intégrations de partenaires](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) avec les contrôleurs de livraison d’application que vous avez peut-être déjà déployés.

### <a name="new-line-of-business-lob-apps"></a>Nouvelles applications métier (LOB)

En général, vous développez des applications métier pour l’utilisation interne dans votre organisation. Si vous avez de nouvelles applications dans le pipeline, nous vous recommandons d’utiliser la [Plateforme d’identités Microsoft](../develop/v2-overview.md) pour implémenter OpenID Connect.

### <a name="apps-to-deprecate"></a>Applications à déconseiller

Applications qui n’ont pas de propriétaire avéré, qui ne bénéficient pas d’une maintenance ni d’une surveillance claire, qui présentent un risque de sécurité pour votre organisation. Envisagez de déconseiller des applications dans les cas suivants :

- leur **fonctionnalité est hautement redondante** avec d’autres systèmes • il n’existe pas de **propriétaire commercial**

- Il n’y a manifestement **aucune utilisation**.

Nous vous recommandons de ne pas **déconseiller les applications qui ont un impact important et qui sont vitales pour l’entreprise**. Dans ce cas, collaborez avec les propriétaires commerciaux pour déterminer la stratégie appropriée.

### <a name="exit-criteria"></a>Critères de sortie

La réussite de cette phase dépend des éléments suivants :

- Bonne compréhension des systèmes dans l’étendue de votre migration (que vous pouvez retirer une fois que vous êtes passé à Azure AD)

- Liste des applications qui inclut :

  - Systèmes auxquels ces applications se connectent
  - Emplacements et appareils à partir desquels les utilisateurs y accèdent
  - Ces applications vont-elles être migrées, déconseillées ou connectées à [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

> [!NOTE]
> Vous pouvez télécharger la [feuille de calcul de découverte des applications](../hybrid/whatis-azure-ad-connect.md) (Application Discovery Worksheet) pour enregistrer les applications que vous souhaitez migrer vers Azure AD Authentification et celles que vous souhaitez conserver mais gérer à l’aide d’[Azure AD Connect](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx).

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Phase 2 : Classifier les applications et planifier le pilote

La classification de la migration de vos applications est un exercice important. Toutes les applications ne doivent pas faire l’objet d’une migration et d’une transition en même temps. Une fois que vous avez collecté les informations relatives à chacune des applications, vous pouvez rationaliser les applications qui doivent être migrées en premier et celles qui peuvent avoir besoin de plus de temps.

### <a name="classify-in-scope-apps"></a>Classifier les applications dans l’étendue

On peut prendre en compte les axes suivants : caractère critique pour l’entreprise, utilisation et durée de vie, chacun de ces axes dépendant de plusieurs facteurs.

### <a name="business-criticality"></a>Caractère critique pour l’entreprise

Le caractère critique pour l’entreprise prend en compte différentes dimensions pour chaque entreprise, mais les deux mesures que vous devez prendre en considération sont les **caractéristiques et fonctionnalités** et les **profils utilisateur**. Attribuez aux applications ayant une fonctionnalité unique une valeur de point supérieur aux applications ayant des fonctionnalités redondantes ou obsolètes.

![Diagramme des spectres Caractéristiques et fonctionnalité et Profils utilisateur](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Utilisation

Les applications avec un **nombre d’utilisations élevé** doivent recevoir une valeur plus élevée que les applications dont l’utilisation est faible. Attribuez une valeur plus élevée aux applications avec des utilisateurs externes, des utilisateurs faisant partie des responsables ou de l’équipe de sécurité. Pour chaque application dans votre portefeuille de migration, effectuez ces évaluations.

![Diagramme des spectres Volume utilisateur et Mixité des utilisateurs](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Une fois que vous avez déterminé les valeurs pour le caractère critique pour l’entreprise et l’utilisation, vous pouvez déterminer la **durée de vie de l’application** et créer une matrice de priorité. Consultez l’une des matrices ci-dessous :

![Diagramme en triangle montrant les relations entre l’utilisation, la durée de vie attendue et le caractère critique pour l’entreprise](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Hiérarchiser les applications pour la migration

Vous pouvez choisir de commencer la migration par les applications ayant la priorité la plus basse ou les applications ayant la priorité la plus élevée en fonction des besoins de votre organisation.

Dans un scénario où vous avez peu d’expérience avec Azure AD et les services Identity, envisagez de commencer par déplacer vos **applications ayant la priorité la plus basse** vers Azure AD. Cela permet de réduire l’impact sur votre activité et de créer une dynamique. Une fois que vous avez correctement migré ces applications et que vous avez acquis la confiance de la partie prenante, vous pouvez continuer à migrer les autres applications.

S’il n’existe aucune priorité claire, vous devez envisager de déplacer d’abord les applications qui se trouvent dans la [galerie Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) et qui prennent en charge plusieurs fournisseurs d’identité (ADFS ou Okta), car elles sont plus faciles à intégrer. Il est probable que ces applications soient les applications dont la priorité est **la plus élevée** de votre organisation. Pour vous aider à intégrer vos applications SaaS à Azure AD, nous avons développé une collection de [tutoriels](../saas-apps/tutorial-list.md) qui vous guident lors de la configuration.

Quand vous avez une échéance pour migrer les applications, les applications présentant la priorité la plus élevée constituent la charge de travail principale. Vous pouvez finalement sélectionner les applications présentant une priorité plus basse, car elles ne modifient pas le coût même si vous avez déplacé l’échéance. Même si vous devez renouveler la licence, le coût sera faible.

En plus de cette classification et en fonction de l’urgence de votre migration, vous pouvez également envisager de créer une **planification de la migration** dans laquelle les propriétaires d’applications doivent migrer leurs applications. À la fin de ce processus, vous devez disposer d’une liste de toutes les applications dans des compartiments classés par priorité pour la migration.

### <a name="document-your-apps"></a>Documenter vos applications

Tout d’abord, commencez par collecter des détails clés sur vos applications. La [feuille de calcul de découverte des applications](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) (Application Discovery Worksheet) vous aide à prendre rapidement des décisions de migration et à transmettre des recommandations à votre groupe de travail en un rien de temps.

Les informations importantes pour prendre votre décision de migration incluent :

- **Nom de l’application** – sous quel nom cette application est-elle connue dans l’entreprise ?

- **Type d’application** – s’agit-il d’une application SaaS tierce ? Une application web métier personnalisée ? Une API ?

- **Caractère critique pour l’entreprise** – cette application est-elle hautement critique ? Peu critique ? Ou bien se situe-t-elle entre les deux ?

- **Volume d’accès utilisateur** – cette application est-elle utilisée par tous les utilisateurs ou seulement quelques personnes ?

- **Durée de vie planifiée** – quelle est la durée de vie de cette application ? Moins de six mois ? Plus de deux ans ?

- **Fournisseur d’identité actuel** – qui est le fournisseur d’identité (IdP) principal pour cette application ? Ou s’appuie-t-elle sur le stockage local ?

- **Méthode d’authentification** – l’application s’authentifie-t-elle à l’aide de normes ouvertes ?

- **Si vous envisagez de mettre à jour le code de l’application** – l’application est en développement planifié ou en cours de développement actif ?

- **Si vous envisagez de conserver l’application en local** – souhaitez-vous conserver l’application dans votre centre de données à long terme ?

- **Si l’application dépend d’autres applications ou API** – est-ce que l’application appelle actuellement d’autres applications ou API ?

- **Si l’application se trouve dans la galerie Azure AD** – est-ce que l’application est actuellement déjà intégrée à la [galerie Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)?

D’autres données qui vous aideront ultérieurement, mais pour lesquelles vous n’avez pas besoin de prendre une décision de migration immédiate, par exemple :

- **URL** de l’application – où les utilisateurs se rendent-ils pour accéder à l’application ?

- **Description de l’application** – brève description de la fonctionnalité de l’application

- **Propriétaire de l’application** – qui au sein de l’entreprise est le principal point de contact pour l’application ?

- **Commentaires ou remarques générales** – toutes les autres informations générales sur l’application ou la propriété commerciale

Une fois que vous avez classifié votre application et documenté les détails, vous devez vous assurer que vous avez obtenu l’assentiment du propriétaire commercial pour votre stratégie de migration planifiée.

### <a name="plan-a-pilot"></a>Prévoir un pilote

L’application ou les applications que vous sélectionnez pour le pilote doivent représenter l’identité clé et les exigences de sécurité de votre organisation. Vous devez disposer de l’assentiment formel des propriétaires de l’application. Les pilotes s’exécutent généralement dans un environnement de test distinct. Consultez les [Meilleures pratiques pour les pilotes](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) sur la page des plans de déploiement.

**N’oubliez pas vos partenaires externes.** Assurez-vous qu’ils participent aux planifications et aux tests de la migration. Enfin, assurez-vous qu’ils disposent d’un moyen d’accéder à votre support technique en cas de problème cassant.

### <a name="plan-for-limitations"></a>Planifier des limitations

Alors que certaines applications migrent facilement, d’autres peuvent prendre plus de temps en raison de serveurs ou d’instances multiples. Par exemple, la migration SharePoint peut prendre plus de temps en raison des pages de connexion personnalisées.

De nombreux fournisseurs d’applications SaaS facturent la modification de la connexion SSO. Consultez-les et prévoyez ces détails.

Azure AD présente également des [limites de service et des restrictions](../enterprise-users/directory-service-limits-restrictions.md) que vous devez connaître.

### <a name="app-owner-sign-off"></a>Validation du propriétaire de l’application

Les applications à caractère critique pour l’entreprise et universelles peuvent avoir besoin d’un groupe d’utilisateurs pilotes à des fins de test au cours de la phase pilote. Une fois que vous avez testé une application dans l’environnement de pré-production ou pilote, assurez-vous que les propriétaires commerciaux des applications valident les performances avant la migration de l’application et de tous les utilisateurs vers l’utilisation de production d’Azure AD pour l’authentification.

### <a name="plan-the-security-posture"></a>Planifier la posture de sécurité

Avant de lancer le processus de migration, prenez le temps de prendre en compte la posture de sécurité que vous souhaitez développer pour votre système d’identité d’entreprise. Pour ce faire, collectez ces informations : **identités, appareils et emplacements ayant accès à vos données**.

### <a name="identities-and-data"></a>Identités et données

La plupart des organisations ont des exigences spécifiques en matière d’identité et de protection des données qui varient en fonction des secteurs d’activité et des postes dans les organisations. Reportez-vous aux [configurations d’accès aux identités et aux appareils](/microsoft-365/enterprise/microsoft-365-policies-configurations) pour connaître nos recommandations, notamment un ensemble prescrit de [stratégies d’accès conditionnel](../conditional-access/overview.md) et les fonctionnalités associées.

Vous pouvez utiliser ces informations pour protéger l’accès à tous les services intégrés à Azure AD. Ces recommandations sont alignées sur le niveau de sécurité Microsoft et le [score d’identité dans Azure AD](../fundamentals/identity-secure-score.md). Le degré de sécurisation vous aide à accomplir les tâches suivantes :

- Évaluer votre méthode de sécurité relative aux identités de façon objective

- Planifier les améliorations à apporter à la sécurité des identités

- Évaluer la réussite de vos améliorations

Cela vous aidera également à mettre en œuvre les [cinq étapes de sécurisation de votre infrastructure d’identité](../../security/fundamentals/steps-secure-identity.md). Utilisez les conseils comme point de départ pour votre organisation et ajustez les stratégies pour répondre aux besoins spécifiques de votre organisation.

### <a name="who-is-accessing-your-data"></a>Qui a accès à vos données ?

Il existe deux catégories principales d’utilisateurs de vos applications et ressources prises en charge par Azure AD :

- **Internes :** Employés, sous-traitants et fournisseurs qui ont des comptes au sein de votre fournisseur d’identité. Cela peut nécessiter d’autres tableaux croisés dynamiques avec des règles différentes pour les responsables ou la direction par rapport aux autres employés.

- **Externes :** Prestataires, fournisseurs, serveurs de distribution ou autres partenaires commerciaux qui interagissent avec votre organisation dans le cadre d’une activité régulière avec [Azure AD B2B Collaboration.](../external-identities/what-is-b2b.md)

Vous pouvez définir des groupes pour ces utilisateurs et remplir ces groupes de différentes façons. Vous pouvez choisir l’ajout manuel des membres à un groupe par un administrateur ou activer l’appartenance au groupe en libre-service. Des règles peuvent être établies pour ajouter automatiquement des membres à des groupes en fonction des critères spécifiés à l’aide de [groupes dynamiques](../enterprise-users/groups-dynamic-membership.md).

Les utilisateurs externes peuvent également faire référence à des clients. [Azure AD B2C](../../active-directory-b2c/overview.md), un produit distinct prend en charge l’authentification du client. Toutefois, ce contenu sort du cadre de cet article.

### <a name="devicelocation-used-to-access-data"></a>Appareil/emplacement utilisé pour accéder aux données

L’appareil et l’emplacement qu’un utilisateur utilise pour accéder à une application sont également importants. Les appareils physiquement connectés à votre réseau d’entreprise sont plus sécurisés. Les connexions extérieures au réseau via VPN peuvent nécessiter des contrôles.

![Diagramme montrant la relation entre l’emplacement de l’utilisateur et l’accès aux données](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

En tenant compte de ces aspects de la ressource, de l’utilisateur et de l’appareil, vous pouvez choisir d’utiliser les capacités de l’[accès conditionnel Azure AD](../conditional-access/overview.md). L’accès conditionnel va au-delà des autorisations utilisateur : il est basé sur une combinaison de facteurs, tels que l’identité d’un utilisateur ou d’un groupe, le réseau auquel l’utilisateur est connecté, l’appareil et l’application qu’il utilise, et le type de données auxquelles il tente d’accéder. L’accès accordé à l’utilisateur s’adapte à ce jeu de conditions plus large.

### <a name="exit-criteria"></a>Critères de sortie

Pour la réussite de cette phase, vous devez :

- Connaître vos applications
  - Avoir documenté entièrement les applications que vous envisagez de migrer
  - Avoir des applications hiérarchisées en fonction de leur caractère critique pour l’entreprise, du volume d’utilisation et de la durée de vie

- Avoir sélectionné des applications qui représentent vos besoins pour un pilote

- Avoir l’assentiment du propriétaire commercial pour votre stratégie de hiérarchisation et de stratégie

- Comprendre vos besoins en matière de posture de sécurité et comment les implémenter

## <a name="phase-3-plan-migration-and-testing"></a>Phase 3 : Planifier la migration et le test

Une fois que vous avez obtenu l’assentiment commercial, l’étape suivante consiste à commencer à migrer ces applications vers Azure AD Authentification.

### <a name="migration-tools-and-guidance"></a>Conseils et outils de migration

Utilisez les outils et les conseils ci-dessous pour suivre les étapes précises nécessaires à la migration de vos applications vers Azure AD :

- **Conseils généraux sur la migration** – utilisez le livre blanc, les outils, les modèles d’e-mails et le questionnaire sur les applications dans la [boîte à outils de migration des applications d’Azure AD](./migration-resources.md) pour découvrir, classifier et migrer vos applications.

- **Applications SaaS** – consultez notre liste de [centaines de tutoriels sur les applications SaaS](../saas-apps/tutorial-list.md) et le [plan de déploiement complet SSO Azure AD](https://aka.ms/ssodeploymentplan) pour parcourir le processus de bout en bout.

- **Applications en cours d’exécution en local** – découvrez le [proxy d’application Azure AD](./application-proxy.md) et utilisez le [plan de déploiement complet du proxy d’application Azure AD](https://aka.ms/AppProxyDPDownload) pour avancer rapidement.

- **Applications que vous développez** – lisez notre guide d’[intégration](../develop/quickstart-register-app.md) et d’[inscription](../develop/quickstart-register-app.md) pas à pas.

Après la migration, vous pouvez envoyer des communications informant les utilisateurs que le déploiement a réussi et leur rappelant les nouvelles étapes à suivre.

### <a name="plan-testing"></a>Planifier les tests

Au cours du processus de migration, votre application a peut-être déjà un environnement de test utilisé lors des déploiements réguliers. Vous pouvez continuer à utiliser cet environnement pour le test de la migration. Si aucun environnement de test n’est actuellement disponible, vous pouvez en configurer un à l’aide d’Azure App Service ou de machines virtuelles Azure, en fonction de l’architecture de l’application. Vous pouvez choisir de configurer un client de test Azure AD distinct à utiliser lorsque vous développez vos configurations d’application. Ce locataire démarrera dans un état propre et ne sera pas configuré pour se synchroniser avec un système.

Vous pouvez tester chaque application en vous connectant avec un utilisateur de test et en vous assurant que toutes les fonctionnalités sont les mêmes qu’avant la migration. Si vous décidez pendant le test que les utilisateurs devront mettre à jour leurs paramètres [MFA](../authentication/howto-mfa-userstates.md) ou [SSPR](../authentication/tutorial-enable-sspr.md), ou si vous ajoutez cette fonctionnalité pendant la migration, veillez à l’ajouter à votre plan de communication auprès des utilisateurs finaux. Consultez les modèles de communication pour [l’authentification multifacteur](https://aka.ms/mfatemplates) et [SSPR](https://aka.ms/ssprtemplates).

Après avoir migré les applications, accédez au [portail Azure](https://aad.portal.azure.com/) pour tester si la migration a réussi. Procédez comme suit :

- Sélectionnez **Applications d’entreprise&gt; Toutes les applications** et recherchez votre application dans la liste.

- Sélectionnez **Gérer&gt; Utilisateurs et groupes** pour affecter au moins un utilisateur ou un groupe à l’application.

- Sélectionnez **Gérer&gt; Accès conditionnel**. Passez en revue votre liste de stratégies et assurez-vous que vous ne bloquez pas l’accès à l’application avec une [stratégie d’accès conditionnel](../conditional-access/overview.md).

Selon la façon dont vous configurez votre application, vérifiez que l’authentification unique fonctionne correctement.

| Type d'authentification      | Test                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect** | Sélectionnez **Applications d’entreprise&gt; Autorisations** et assurez-vous que vous avez accepté que l’application soit utilisée dans votre organisation dans les paramètres utilisateur de votre application. |
| **Authentification unique SAML** | Utilisez le bouton [Tester les paramètres SAML](./debug-saml-sso-issues.md) sous **Authentification unique**. |
| **Authentification unique par mot de passe** | Téléchargez et installez l’[extension de connexion sécurisée à MyApps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). Cette extension vous permet de lancer les applications cloud de votre organisation qui vous obligent à utiliser un processus d’authentification unique. |

|  **[Proxy d’application](./application-proxy.md)** | Vérifiez que votre connecteur est en cours d’exécution et affecté à votre application. Pour obtenir de l’aide, consultez le [Guide de résolution des problèmes de proxy d’application](./application-proxy-troubleshoot.md). |

### <a name="troubleshoot"></a>Dépanner

Si vous rencontrez des problèmes, consultez notre [Guide de résolution des problèmes liés aux applications](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) pour obtenir de l’aide. Pour consulter nos articles de résolution des problèmes, reportez-vous à [Problèmes de connexion aux applications configurées pour l’authentification unique basée sur SAML](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps).

### <a name="plan-rollback"></a>Planifier la restauration

Si votre migration échoue, la meilleure stratégie consiste à effectuer des restaurations et des tests. Voici les étapes que vous pouvez suivre pour atténuer les problèmes de migration :

- **Prenez des captures d’écran** de la configuration existante de votre application. Vous pouvez revenir en arrière si vous devez reconfigurer l’application une nouvelle fois.

- Vous pouvez également envisager de **fournir des liens vers l’authentification héritée**, en cas de problèmes liés à l’authentification cloud.

- Avant de terminer la migration, **ne modifiez pas votre configuration existante** avec le fournisseur d’identité antérieur.

- Commencez par migrer **les applications qui prennent en charge plusieurs fournisseurs IdP**. En cas de problème, vous pouvez toujours passer à la configuration du fournisseur IdP préféré.

- Assurez-vous que votre application a un **bouton Commentaires**, ou des instructions de **support technique** en cas de problèmes.

### <a name="exit-criteria"></a>Critères de sortie

Pour la réussite de cette phase, vous devez avoir :

- Déterminé la façon dont chaque application sera migrée

- Fait l’examen des outils de migration

- Fait la planification de vos tests, y compris les environnements et les groupes de test

- Planifié la restauration

## <a name="phase-4-plan-management-and-insights"></a>Phase 4 : Planifier la gestion et les insights

Une fois que les applications ont migré, vous devez vous assurer que :

- Les utilisateurs peuvent avoir accès et gérer en toute sécurité

- Vous pouvez obtenir des insights appropriés sur l’utilisation et l’intégrité des applications

Nous vous recommandons d’effectuer les actions suivantes en fonction de votre organisation.

### <a name="manage-your-users-app-access"></a>Gérer l’accès de vos utilisateurs aux applications

Une fois que vous avez migré les applications, vous pouvez enrichir l’expérience de vos utilisateurs de nombreuses façons

**Rendre les applications détectables**

**Dirigez votre utilisateur** vers l’expérience du portail [MyApps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). À cet endroit, ils peuvent accéder à toutes les applications basées sur le cloud. Ces applications sont rendues disponibles à l’aide d’[Azure AD Connect](../hybrid/whatis-azure-ad-connect.md), et aux applications qui utilisent le [proxy d’application](./application-proxy.md), à condition qu’ils disposent d’autorisations pour accéder à ces applications.


Vous pouvez guider vos utilisateurs sur la façon de découvrir leurs applications :

- Utiliser la fonctionnalité d’[authentification unique existante](./view-applications-portal.md) pour **lier vos utilisateurs à n’importe quelle application**


- Activer l’[accès aux applications en libre-service](./manage-self-service-access.md) à une application et **permettre aux utilisateurs d’ajouter des applications que vous organisez**

- [Masquer les applications des utilisateurs finaux](./hide-application-from-user-portal.md) (applications Microsoft par défaut ou autres applications) pour **rendre les applications dont ils ont besoin plus détectables**

### <a name="make-apps-accessible"></a>Rendre les applications accessibles

**Permettez aux utilisateurs d’accéder aux applications à partir de leurs appareils mobiles**. Les utilisateurs peuvent accéder au portail MyApps avec le navigateur géré par Intune sur leurs appareils [iOS](./hide-application-from-user-portal.md) 7.0 ou version ultérieure ou [Android](./hide-application-from-user-portal.md).

Les utilisateurs peuvent télécharger un **navigateur géré par Intune** :

- **Pour les appareils Android**, à partir de [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- **Pour les appareils Apple**, à partir de l’[App Store d’Apple](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) ou ils peuvent télécharger l’[application mobile MyApps pour iOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Permettez aux utilisateurs d’ouvrir leurs applications à partir d’une extension de navigateur.**

Les utilisateurs peuvent [télécharger l’extension de connexion sécurisée MyApps](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) dans [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [FireFox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) ou [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) et peuvent lancer des applications directement à partir de la barre de navigateur pour :

- **Rechercher leurs applications et afficher leurs applications les plus récemment utilisées**

- **Convertir automatiquement les URL internes** que vous avez configurées dans le [proxy d’application](./application-proxy.md) en URL externes appropriées. Vos utilisateurs peuvent désormais travailler avec des liens qui leur sont familiers, quel que soit leur emplacement.

**Permettez aux utilisateurs d’ouvrir leurs applications à partir d’Office.com.**

Les utilisateurs peuvent accéder à [Office.com](https://www.office.com/) pour **rechercher leurs applications et afficher les applications les plus récemment utilisées** directement à l’emplacement où ils travaillent.

### <a name="secure-app-access"></a>Sécuriser l’accès aux applications

Azure AD fournit un emplacement d’accès centralisé pour gérer vos applications migrées. Accédez au [portail Azure](https://portal.azure.com/) et activez les fonctionnalités suivantes :

- **Sécuriser l’accès des utilisateurs aux applications.** Activez les [stratégies d’accès conditionnel](../conditional-access/overview.md) ou [Identity Protection](../identity-protection/overview-identity-protection.md) pour sécuriser l’accès des utilisateurs aux applications en fonction de l’état de l’appareil, de l’emplacement, etc.

- **Provisionnement automatique.** Configurez l’[approvisionnement automatique des utilisateurs](../app-provisioning/user-provisioning.md) avec diverses applications SaaS tierces auxquelles les utilisateurs doivent accéder. En plus de créer des identités utilisateur, le provisionnement automatique comprend la maintenance et la suppression d’identités utilisateur en cas de modification de l’état ou des rôles.

- **Déléguer la gestion** des **accès utilisateur**. Le cas échéant, activez l’accès aux applications en libre-service à vos applications et *attribuez un approbateur d’entreprise pour approuver l’accès à ces applications*. Utilisez la [gestion de groupes en libre-service](../enterprise-users/groups-self-service-management.md) pour les groupes affectés à des regroupements d’applications.

- **Déléguer l’accès administrateur.** Utilisation du **rôle d’annuaire** pour attribuer un rôle d’administrateur (par exemple, administrateur d’application, administrateur d’application cloud ou développeur d’application) à votre utilisateur.

### <a name="audit-and-gain-insights-of-your-apps"></a>Auditer et obtenir des informations sur vos applications

Vous pouvez également utiliser le [portail Azure](https://portal.azure.com/) pour auditer toutes vos applications à partir d’un emplacement centralisé.

- **Auditez votre application** à l’aide des options **Applications d’entreprise, Audit ou accédez aux mêmes informations à partir de l’[API de création de rapports Azure AD](../reports-monitoring/concept-reporting-api.md) pour les intégrer à vos outils favoris.

- **Affichez les autorisations pour une application** à l’aide des options **Applications d’entreprise, Autorisations** pour les applications utilisant OAuth/OpenID Connect.

- **Obtenez des insights sur la connexion** à l’aide des options **Applications d’entreprise, Connexions**. Accédez aux mêmes informations à partir de l’[API de création de rapports Azure AD.](../reports-monitoring/concept-reporting-api.md)

- **Visualisez l’utilisation de votre application** à partir du [pack de contenu Azure AD Power BI](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

### <a name="exit-criteria"></a>Critères de sortie

Pour la réussite de cette phase, vous devez :

- Fournir à vos utilisateurs un accès sécurisé aux applications

- Gérer pour auditer et obtenir des insights sur les applications migrées

### <a name="do-even-more-with-deployment-plans"></a>Aller plus loin avec les plans de déploiement

Les plans de déploiement vous guident en prenant en compte la valeur métier, la planification, les étapes d’implémentation et la gestion des solutions Azure AD, y compris les scénarios de migration d’applications. Ces plans rassemblent tous les éléments dont vous avez besoin pour commencer à déployer et à tirer parti des fonctionnalités d’Azure AD. Les guides de déploiement incluent des contenus tels que les meilleures pratiques recommandées par Microsoft, les communications avec l’utilisateur final, des guides de planification, des étapes d’implémentation, des cas de test et bien plus encore.

De nombreux [plans de déploiement](../fundamentals/active-directory-deployment-plans.md) sont disponibles et nous en ajoutons sans cesse de nouveaux !

### <a name="contact-support"></a>Contacter le support technique

Consultez les liens de support suivants pour créer ou suivre le ticket de support et surveiller l’intégrité.

- **Support Azure :** Vous pouvez appeler le [support Microsoft](https://azure.microsoft.com/support) et ouvrir un ticket pour n’importe quel

problème de déploiement Azure Identity en fonction de votre Contrat Entreprise avec Microsoft.

- **FastTrack** : Si vous avez acheté des licences Enterprise Mobility and Security (EMS) ou Azure AD Premium, vous êtes éligible pour recevoir une assistance sur le déploiement dans le cadre du [programme FastTrack.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- **Contactez l’équipe d’ingénieurs produit :** Si vous travaillez sur un déploiement client majeur avec des millions d’utilisateurs, vous pouvez bénéficier du support de l’équipe des comptes Microsoft ou de votre architecte de solutions cloud. En fonction de la complexité du déploiement du projet, vous pouvez travailler directement avec l’[équipe des ingénieurs produit Azure Identity.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Blog Azure AD Identity :** Abonnez-vous au [blog Azure AD Identity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) pour rester informé des dernières annonces de produits, des informations détaillées et des informations de feuille de route fournies directement par l’équipe des ingénieurs Identity.