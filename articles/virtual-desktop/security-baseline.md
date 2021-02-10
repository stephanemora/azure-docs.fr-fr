---
title: Base de sécurité Azure pour Windows Virtual Desktop
description: La base de sécurité pour Windows Virtual Desktop fournit des instructions pratiques et des ressources pour l’implémentation des recommandations de sécurité spécifiées dans le benchmark de sécurité Azure.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 01cd79afe3e718a32663544a76a2a43353bc082e
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575379"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Base de sécurité Azure pour Windows Virtual Desktop

Cette base de référence de sécurité applique les conseils du [benchmark de sécurité Azure version 2.0](../security/benchmarks/overview.md) à Windows Virtual Desktop. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure. Le contenu est regroupé selon les **contrôles de sécurité** définis par le benchmark de sécurité Azure et les conseils associés, applicables à Windows Virtual Desktop. Les **contrôles** non applicables à Windows Virtual Desktop ont été exclus.

Le service Windows Virtual Desktop comprend le service lui-même, la référence virtuelle Windows 10 Entreprise pour multi-session ainsi que FSLogix. Pour les recommandations de sécurité liées à FSLogix, consultez la [base de référence de la sécurité pour le stockage](../storage/common/security-baseline.md). Le service possède un agent qui s’exécute sur les machines virtuelles, mais celles-ci étant contrôlées entièrement par le client, suivez les [recommandations de sécurité pour le calcul](../virtual-machines/windows/security-baseline.md)

Pour voir comment le service Windows Virtual Desktop est entièrement mappé au benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité Windows Virtual Desktop](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1 : Implémenter la sécurité pour le trafic interne

**Aide** : Vous devez créer ou utiliser un réseau virtuel existant lorsque vous déployez des machines virtuelles à inscrire auprès de Windows Virtual Desktop. Vérifiez que tous les réseaux virtuels Azure suivent un principe de segmentation d’entreprise qui s’aligne sur les risques métier. Tout système qui peut entraîner des risques plus élevés pour l’organisation doit être isolé sur son propre réseau virtuel et être suffisamment sécurisé à l’aide d’un groupe de sécurité réseau ou du Pare-feu Azure.

Utilisez les fonctionnalités de renforcement du réseau adaptatif d’Azure Security Center pour recommander des configurations de groupe de sécurité réseau qui limitent les ports et les adresses IP sources conformément à des règles de trafic réseau externe.

En fonction de vos applications et de votre stratégie de segmentation d’entreprise, limitez ou autorisez le trafic entre des ressources internes suivant des règles de groupe de sécurité réseau. Pour des applications bien définies (par exemple, une application à 3 niveaux), il peut s’agir d’une approche « Refuser par défaut, Autoriser par exception », hautement sécurisée. Cela peut ne pas bien évoluer si vous avez un grand nombre d’applications et de points de terminaison qui interagissent les uns avec les autres. Vous pouvez également utiliser le Pare-feu Azure dans les cas où la gestion centrale est exigée sur un grand nombre de segments d’entreprise ou de spokes (dans une topologie hub/spoke).

Pour les groupes de sécurité réseau associés aux sous-réseaux de votre machine virtuelle (qui font partie de Windows Virtual Desktop), vous devez autoriser le trafic sortant vers des points de terminaison spécifiques. 

- [Découvrez quelles URL doivent être autorisées à accéder à Windows Virtual Desktop](safe-url-list.md)

- [Renforcement du réseau adaptatif dans Azure Security Center](../security-center/security-center-adaptive-network-hardening.md) 

- [Pare-feu Azure pour Windows Virtual Desktop](../firewall/protect-windows-virtual-desktop.md)

- [Comment créer un groupe de sécurité réseau avec des règles de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Guide pratique pour déployer et configurer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2 : Interconnecter des réseaux privés

**Aide** : Azure ExpressRoute ou le réseau privé virtuel Azure permettent de créer des connexions privées entre les centres de données Azure et l’infrastructure locale dans un environnement de colocation. Les connexions ExpressRoute ne sont pas établies via l’Internet public et offrent une plus grande fiabilité, des débits plus importants et des latences moindres par rapport aux connexions Internet classiques. 

Pour des réseaux virtuels point à site et site à site, vous pouvez connecter des appareils ou des réseaux locaux à un réseau virtuel à l’aide de n’importe quelle combinaison des options de réseau privé virtuel et d’Azure ExpressRoute.

Pour interconnecter deux réseaux virtuels ou plus dans Azure, utilisez l’appairage de réseaux virtuels. Le trafic réseau entre les réseaux virtuels appairés est privé et conservé sur le réseau principal Azure.

- [Quels sont les modèles de connectivité ExpressRoute ?](../expressroute/expressroute-connectivity-models.md) 

- [Vue d’ensemble des VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering de réseau virtuel](/azure/virtual-network/virtual-network-peering-overview)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4 : Protégez les applications et les services contre les attaques réseau externes

**Aide** : Utilisez le Pare-feu Azure pour protéger les applications et les services contre le trafic potentiellement malveillant provenant d’Internet et d’autres emplacements externes. Protégez vos ressources Windows Virtual Desktop contre les attaques de réseaux externes, notamment les attaques par déni de service distribué (DDoS), les attaques spécifiques aux applications et le trafic Internet non sollicité et potentiellement malveillant. Protégez vos ressources contre les attaques par déni de service distribué en activant la protection DDoS Standard sur vos réseaux virtuels Azure. Utilisez Azure Security Center pour détecter les risques de configuration incorrecte liés aux ressources associées à votre réseau.

Windows Virtual Desktop n’est pas destiné à exécuter des applications web et ne nécessite pas de configuration de paramètres supplémentaires ni de déploiement de services réseau supplémentaires pour le protéger des attaques de réseau externe qui ciblent les applications web.

- [Documentation sur le pare-feu Azure](/azure/firewall)

- [Gérer la norme Azure DDoS Protection à l’aide du portail Azure](/azure/virtual-network/manage-ddos-protection) 

- [Recommandations relatives à Azure Security Center](../security-center/recommendations-reference.md#networking-recommendations)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5 : Déployer des systèmes de détection et de prévention des intrusions (IDS/IPS)

**Aide** : Utilisez Pare-feu Azure avec un filtrage basé sur le renseignement sur les menaces pour générer des alertes et éventuellement bloquer le trafic en provenance et à destination d’adresses IP et de domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence. Si l’inspection de la charge utile est obligatoire, vous pouvez déployer, à partir de la Place de marché Azure, une solution tierce de détection ou de prévention des intrusions. 

Si vous êtes soumis à une réglementation ou une autre exigence concernant l’utilisation d’une solution de détection ou de prévention des intrusions, vérifiez qu’elle soit toujours paramétrée pour fournir des alertes de haute qualité à votre solution SIEM (Security Information and Event Management).

- [Guide pratique pour déployer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md) 

- [La Place de marché Azure comprend des fonctionnalités IDS tierces](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Fonctionnalité de détection de point de terminaison et réponse (EDR) de Microsoft Defender ATP](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6 : Simplifier les règles de sécurité réseau

**Aide** : Utilisez des étiquettes de service de réseau virtuel Azure pour définir des contrôles d’accès réseau sur des groupes de sécurité réseau ou un Pare-feu Azure configuré pour vos ressources Windows Virtual Desktop. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de l’étiquette de service (par exemple : WindowsVirtualDesktop) dans le champ source ou de destination d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

- [Présentation et usage des balises de service](../virtual-network/service-tags-overview.md)

- [Découvrez ce qui est couvert par la balise de service Windows Virtual Desktop](safe-url-list.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="identity-management"></a>Gestion des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des identités](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1 : Normaliser Azure Active Directory comme système d’authentification et d’identité central

**Aide** : Windows Virtual Desktop utilise Azure AD (Azure Active Directory) en tant que service de gestion des identités et des accès par défaut. Vous devez normaliser Azure AD pour régir la gestion des identités et des accès de votre organisation dans :

- Les ressources cloud Microsoft, comme le portail Azure, le stockage Azure, les machines virtuelles Azure (Linux et Windows), les applications Azure Key Vault, PaaS et SaaS.

- Les ressources de votre organisation, comme les applications sur Azure ou les ressources réseau de votre entreprise.

La sécurisation d’Azure AD doit être d’une priorité élevée dans les pratiques de sécurité cloud de votre organisation. Azure AD fournit un score d'identité sécurisée pour vous aider à évaluer la sécurité des identités par rapport aux recommandations de Microsoft en matière de meilleures pratiques. Utilisez le score pour évaluer avec précision votre configuration par rapport aux meilleures pratiques recommandées et apporter des améliorations à votre posture de sécurité.

Azure AD prend en charge les identités externes, qui permettent aux utilisateurs sans compte Microsoft de se connecter à leurs applications et ressources avec leur identité externe.

- [Location dans Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Utilisez des fournisseurs d'identité externes pour l’application](/azure/active-directory/b2b/identity-providers)

- [Qu’est-ce que le degré de sécurisation Identity Secure Score dans Azure AD](../active-directory/fundamentals/identity-secure-score.md)

- [Rôles spécifiques nécessaires pour utiliser Windows Virtual Desktop](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2 : Gérer les identités d’application de façon sécurisée et automatique

**Aide** : Windows Virtual Desktop prend en charge des identités managées Azure pour les comptes non humains tels que les services ou l’automatisation. Il est recommandé de recourir à la fonctionnalité d’identité managée Azure plutôt que de créer un compte humain plus puissant pour accéder aux ressources ou les exécuter. 

Windows Virtual Desktop recommande d’utiliser Azure Active Directory (Azure AD) pour créer un principal de service avec des autorisations restreintes au niveau des ressources en vue de configurer des principaux de service avec des informations d’identification de certificat et revenir aux secrets clients. Dans les deux cas, Azure Key Vault peut être utilisé conjointement avec des identités gérées par Azure, afin que l’environnement d’exécution (par exemple, une fonction Azure) puisse récupérer les informations d’identification du coffre de clés.

- [Services prenant en charge les identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Principal de service Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Créer un principal du service avec un certificat](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Utiliser Azure Key Vault pour l’inscription du principal de sécurité](../key-vault/general/authentication.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3 : Utiliser l’authentification unique Azure AD pour l’accès aux applications

**Aide** : Windows Virtual Desktop utilise Azure Active Directory (Azure AD) pour fournir la gestion des identités et des accès aux ressources Azure, aux applications cloud et aux applications locales. Cela inclut les identités d’entreprise, comme les employés, ainsi que les identités externes, comme les partenaires et les fournisseurs. Cela permet à l’authentification unique de gérer et sécuriser l’accès aux données et ressources de votre organisation localement et dans le cloud. Connectez l’ensemble de vos utilisateurs, applications et appareils à Azure AD pour un accès transparent et sécurisé ainsi qu’une visibilité et un contrôle accrus.

- [Comprendre l’authentification unique des applications avec Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4 : Utiliser des contrôles d’authentification renforcés pour tous les accès basés sur Azure Active Directory 

**Aide** : Windows Virtual Desktop utilise Azure Active Directory (Azure AD) qui prend en charge des contrôles d’authentification forts avec l’authentification multifacteur et des méthodes sans mot de passe fortes.

- Authentification multifacteur : activez l’authentification multifacteur Azure AD et suivez les recommandations relatives à la gestion des identités et des accès d’Azure Security Center pour certaines meilleures pratiques de configuration de l’authentification multifacteur. L’authentification multifacteur peut être appliquée à tous les utilisateurs, à certains d’entre eux ou au niveau de chaque utilisateur en fonction des conditions de connexion et des facteurs de risque.

- Authentification sans mot de passe – Trois options d’authentification sans mot de passe sont disponibles : Windows Hello Entreprise, l’application Microsoft Authenticator et les méthodes d’authentification locales, comme les cartes à puce.

Windows Virtual Desktop prend en charge l’authentification basée sur un mot de passe héritée telle que les comptes cloud uniquement (comptes d’utilisateur créés directement dans Azure) qui ont une stratégie de mot de passe de base ou les comptes hybrides (comptes d’utilisateur provenant d’Azure AD local qui suivent les stratégies de mot de passe locales). Lors de l’utilisation de l’authentification par mot de passe, Azure AD fournit une fonctionnalité de protection par mot de passe qui empêche les utilisateurs de définir des mots de passe faciles à deviner. Microsoft fournit une liste globale de mots de passe interdits mise à jour en fonction de la télémétrie, et les clients peuvent compléter la liste en fonction de leurs besoins (par exemple des noms de marque, des références culturelles, etc.). Cette protection par mot de passe peut être utilisée pour les comptes cloud et hybrides uniquement.

La seule authentification basée sur les informations de mot de passe est vulnérable aux méthodes d’attaque populaires. Pour une sécurité accrue, utilisez une authentification forte telle que l’authentification multifacteur et une stratégie de mot de passe forte. Pour les applications tierces et les services de la Place de marché qui peuvent avoir des mots de passe par défaut, vous devez les modifier lors de la configuration initiale du service.

Pour les utilisateurs administrateurs et privilégiés, veillez à ce que soit utilisé le niveau le plus élevé de méthode d’authentification forte. Ensuite, déployez la stratégie d’authentification forte appropriée auprès des autres utilisateurs.

- [Introduction aux options d’authentification sans mot de passe pour Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Stratégie de mot de passe par défaut d’Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Éliminer les mauvais mots de passe à l’aide de Protection de mots de passe d’Azure Active Directory](../active-directory/authentication/concept-password-ban-bad.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5 : Surveiller et alerter en cas d’anomalies de compte

**Aide** : Windows Virtual Desktop est intégré avec Azure Active Directory (Azure AD) qui fournit les sources de données suivantes :

- Connexion : le rapport de connexions fournit des informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.

- Journaux d’audit : traçabilité proposée via des journaux d’activité pour toutes les modifications effectuées par diverses fonctionnalités au sein d’Azure AD. Par exemple, les journaux d’audit peuvent inclure les modifications apportées à des ressources dans Azure AD comme l’ajout ou la suppression d’utilisateurs, d’applications, de groupes, de rôles, de stratégies, etc.

- Connexion risquée : une connexion risquée indique une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur.

- Utilisateurs avec indicateur de risque : un utilisateur à risque correspond à un indicateur de compte d’utilisateur susceptible d’être compromis.

Ces sources de données peuvent être intégrées avec Azure Monitor, avec Azure Sentinel ou avec des systèmes SIEM (Informations de sécurité et gestion d’événements) tiers. Azure Security Center peut également alerter dans le cas de certaines activités suspectes, comme un nombre excessif de tentatives d’authentification ayant échoué et la présence de comptes dépréciés dans l’abonnement. Azure Advanced Threat Protection (ATP) est une solution de sécurité qui peut utiliser les signaux Active Directory pour identifier, détecter et examiner les menaces avancées, les identités compromises et les actions malveillantes internes.

- [Rapports d’activité d’audit dans Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Guide pratique pour afficher les connexions risquées Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Alertes dans le module de protection de renseignement sur les menaces d’Azure Security Center](../security-center/alerts-reference.md)

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6 : Restreindre l’accès aux ressources Azure en fonction des conditions

**Aide** : Windows Virtual Desktop prend en charge l’accès conditionnel avec Azure Active Directory (Azure AD) pour un contrôle d’accès granulaire en fonction des conditions définies par l’utilisateur. Par exemple, les connexions utilisateur de certaines plages d’adresses IP doivent utiliser l’authentification multifacteur pour valider l’accès. 

Par ailleurs, une stratégie de gestion granulaire des sessions d’authentification peut également être utilisée dans différents cas d’usage.

- [Présentation de l’accès conditionnel Azure](../active-directory/conditional-access/overview.md) 

- [Stratégies d’accès conditionnel courantes](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurer la gestion des sessions d’authentification avec l’accès conditionnel](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [Les informations de configuration de l’accès conditionnel spécifique à Windows Virtual Desktop sont disponibles ici](set-up-mfa.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="privileged-access"></a>Accès privilégié

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Accès privilégié](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2 : Limiter l’accès administratif aux systèmes critiques de l’entreprise

**Aide** : Windows Virtual Desktop utilise le contrôle d’accès en fonction du rôle Azure (Azure RBAC) pour isoler l’accès aux systèmes critiques de l’entreprise. Veillez également à restreindre l’accès aux systèmes de gestion, d’identité et de sécurité disposant d’un accès administratif à vos ressources critiques pour l’entreprise, par exemple les contrôleurs de domaine Active Directory, les outils de sécurité et les outils d’administration système ayant des agents sur les systèmes critiques pour l’entreprise. Les attaquants qui compromettent ces systèmes de gestion et de sécurité peuvent potentiellement les armer immédiatement pour compromettre les ressources stratégiques de l’entreprise.

Pour assurer un contrôle d’accès cohérent, tous les types de contrôle d’accès doivent être alignés sur la stratégie de segmentation de votre entreprise.

- [Composants Azure et modèle de référence](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Accès aux groupes d’administration](../governance/management-groups/overview.md#management-group-access) 

- [Administrateurs d’abonnements Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Autorisations d’administrateur minimales requises pour gérer Windows Virtual Desktop](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3 : Examiner et rapprocher régulièrement les accès utilisateur

**Aide** : Windows Virtual Desktop utilise des comptes Azure AD (Azure Active Directory) pour gérer ses ressources ainsi que pour passer en revue régulièrement les comptes d’utilisateur et l’affectation d’accès afin de vérifier la validité des comptes et des accès correspondants.

Utilisez les révisions d’accès Azure AD pour réviser les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. Les rapports Azure AD peuvent fournir des journaux pour vous aider à découvrir les comptes obsolètes.

En outre, Azure Privileged Identity Management peut être configuré pour déclencher des alertes lorsqu'un nombre excessif de comptes d'administrateur est créé, et pour identifier les comptes d'administrateur obsolètes ou mal configurés.

Certains services Azure prennent en charge des utilisateurs et des rôles locaux qui ne sont pas gérés par le biais d'Azure AD. Vous devrez gérer ces utilisateurs séparément.

- [Rôles intégrés pour Windows Virtual Desktop](rbac.md)

- [Créer une révision d’accès des rôles de ressources Azure dans Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Utilisation des révisions d’accès et des identités Azure AD](../active-directory/governance/access-reviews-overview.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4 : Configurer l’accès d’urgence dans Azure AD

**Aide** : Windows Virtual Desktop utilise Azure AD (Azure Active Directory) pour gérer ses ressources. Pour empêcher le verrouillage accidentel de votre organisation Azure AD, configurez un compte d’accès d’urgence pour conserver un accès lorsque les comptes administratifs normaux ne peuvent pas être utilisés. Les comptes d’accès d’urgence sont généralement hautement privilégiés et ne doivent pas être attribués à des personnes spécifiques. Les comptes d’accès d’urgence sont limités à des cas d’urgence ou à des scénarios « de secours » où il est impossible d’utiliser des comptes d’administration normaux.

Vous devez vous assurer que les informations d’identification (telles que le mot de passe, le certificat ou la carte à puce) des comptes d’accès d’urgence restent sécurisées et connues des seules personnes autorisées à les utiliser en cas d’urgence.

- [Gérer des comptes d’accès d’urgence dans Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5 : Automatiser la gestion des droits d'utilisation 

**Aide** : Windows Virtual Desktop est intégré avec Azure AD (Azure Active Directory) pour gérer ses ressources. Utilisez les fonctionnalités de gestion des droits d’utilisation d’Azure AD pour automatiser les workflows de demandes d’accès, notamment les attributions, les révisions et l’expiration des accès. En outre, les approbations à double ou à plusieurs niveaux sont également prises en charge.

- [Présentation des révisions d’accès Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Présentation de la gestion des droits d’utilisation Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6 : Utiliser des stations de travail d’accès privilégié

**Aide** : La sécurité et l’isolement des stations de travail sont très importants pour la sécurité des rôles sensibles, tels que les rôles d’administrateur, de développeur et d’opérateur de service critique. Utilisez des stations de travail utilisateur hautement sécurisées et/ou Azure Bastion pour les tâches d’administration. 

Utilisez Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (MDATP) ou Microsoft Intune pour déployer une station de travail utilisateur sécurisée et gérée pour les tâches d’administration. Les stations de travail sécurisées peuvent être gérées de manière centralisée pour appliquer une configuration sécurisée, notamment une authentification forte, des lignes de base logicielles et matérielles et un accès réseau et logique restreint.

- [Comprendre les stations de travail d’accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Déployer une station de travail d’accès privilégié](/azure/active-directory/devices/howto-azure-managed-workstation)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7 : Suivre le principe Just Enough Administration (privilèges minimum) 

**Aide** : Windows Virtual Desktop est intégré avec le contrôle d’accès en fonction du rôle Azure (RBAC Azure) pour gérer ses ressources. Azure RBAC vous permet de gérer l’accès aux ressources Azure via des attributions de rôles. Vous pouvez affecter ces rôles à des utilisateurs, regrouper des principaux de service et des identités managées. Les rôles intégrés prédéfinis pour certaines ressources peuvent être inventoriés ou interrogés par le biais d’outils tels que Azure CLI, Azure PowerShell ou le Portail Azure. 

Les privilèges que vous attribuez aux ressources avec Azure RBAC doivent toujours être limités à ceux requis par les rôles. Ils complètent l’approche juste-à-temps (JAT) de Privileged Identity Management (PIM), avec Azure Active Directory (Azure AD) et doivent être révisés régulièrement.

Utilisez en outre des rôles intégrés pour allouer les autorisations et créez un rôle personnalisé uniquement lorsque cela est nécessaire.

- [Présentation du contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Configurer le contrôle d'accès en fonction du rôle dans Azure](../role-based-access-control/role-assignments-portal.md) 

- [Utilisation des révisions d’accès et des identités Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Rôles intégrés pour Windows Virtual Desktop](rbac.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8 : Choisir le processus d’approbation pour le support Microsoft  

**Aide** : Dans le cadre des scénarios de support où Microsoft a besoin d’accéder aux données client, Windows Virtual Desktop prend en charge Customer Lockbox pour fournir une interface qui vous permet de consulter et d’approuver ou de refuser les demandes d’accès aux données client.

- [Présentation de Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1 : Découvrir, classifier et étiqueter des données sensibles

**Aide** : Découvrez, classifiez et étiquetez vos données sensibles afin de concevoir les contrôles appropriés. Cela permet de s’assurer que les informations sensibles sont stockées, traitées et transmises en toute sécurité par les systèmes technologiques de l’organisation.

Utilisez Azure Information Protection (et son outil d’analyse associé) pour les informations sensibles présentes dans les documents Office au niveau d’Azure, de l’environnement local, d’Office 365 et ailleurs.

Vous pouvez utiliser Azure SQL Information Protection pour faciliter la classification et l’étiquetage des informations stockées dans les bases de données Azure SQL Database.

- [Étiqueter des informations sensibles à l’aide d’Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Guide pratique pour implémenter la recherche de données Azure SQL](/azure/sql-database/sql-database-data-discovery-and-classification)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="dp-2-protect-sensitive-data"></a>DP-2 : Protection des données sensibles

**Conseils** : Protégez les données sensibles en limitant l’accès avec le contrôle d’accès en fonction du rôle Azure (Azure RBAC), des contrôles d’accès basés sur le réseau et des contrôles spécifiques dans les services Azure (comme le chiffrement dans les bases de données SQL et autres).

Pour assurer un contrôle d’accès cohérent, tous les types de contrôle d’accès doivent être alignés sur la stratégie de segmentation de votre entreprise. La stratégie de segmentation de l’entreprise doit aussi être informée de l’emplacement des systèmes et données sensibles ou vitaux de l’entreprise.

Microsoft traite tout le contenu client comme sensible et vous protège contre la perte et l’exposition des données client. Pour assurer la sécurité des données des clients dans Azure, Microsoft a implémenté certains contrôles et fonctionnalités de protection des données par défaut.

- [Contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/overview.md) 

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3 : Détection des transferts non autorisés de données sensibles

**Conseils** : Détectez les transferts non autorisés de données vers des emplacements situés hors du champ de visibilité ou de contrôle de l’entreprise. Cela implique généralement de surveiller des activités anormales (transferts volumineux ou inhabituels) qui pourraient être le signe d’une exfiltration de données non autorisée.

Les fonctionnalités de la protection avancée contre les menaces (ATP), associées à Stockage Azure et Azure SQL ATP, peuvent générer une alerte en cas de transfert d’informations anormal qui pourrait être le signe d’un transfert non autorisé d’informations sensibles.

Azure Information Protection (AIP) offre des fonctionnalités de supervision pour les informations qui ont été classifiées et étiquetées.

Utilisez des solutions de protection contre la perte de données, telles que celles basées sur l’hôte, pour appliquer des contrôles de détection et/ou de prévention de façon à empêcher l’exfiltration de données.

- [Activer ATP pour Azure SQL](../azure-sql/database/threat-detection-overview.md) 

- [Activer ATP pour Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="asset-management"></a>Gestion des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des ressources](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1 : S’assurer que l’équipe de sécurité a une visibilité sur les risques pour les ressources

**Conseils** : Assurez-vous que les équipes de sécurité reçoivent des autorisations de lecteur de sécurité dans votre locataire et vos abonnements Azure afin qu’elles puissent surveiller les risques de sécurité à l’aide d’Azure Security Center. 

Selon la façon dont les responsabilités de l’équipe de sécurité sont structurées, la surveillance des risques de sécurité peut incomber à une équipe de sécurité centrale ou une équipe locale. Cela dit, les insights et les risques liés à la sécurité doivent toujours être agrégés de manière centralisée au sein d’une organisation. 

Les autorisations de lecteur de sécurité peuvent être appliquées globalement à un locataire entier (groupe d’administration racine) ou étendues à des groupes d’administration ou à des abonnements spécifiques. 

Des autorisations supplémentaires peuvent être nécessaires pour obtenir une visibilité sur les charges de travail et services. 

- [Vue d’ensemble du rôle lecteur de sécurité](../role-based-access-control/built-in-roles.md#security-reader)

- [Vue d'ensemble des groupes d'administration Azure](../governance/management-groups/overview.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2 : S’assurer que l’équipe de sécurité a accès à l’inventaire des ressources et aux métadonnées

**Conseils** : Appliquez des étiquettes à vos ressources Azure, groupes de ressources et abonnements pour les organiser de façon logique dans une taxonomie. Chaque balise se compose d’une paire nom-valeur. Par exemple, vous pouvez appliquer le nom « Environnement » et la valeur « Production » à toutes les ressources en production.

Utilisez l’inventaire des machines virtuelles Azure pour automatiser la collecte d’informations relatives aux logiciels présents sur les machines virtuelles. Le nom, la version, l’éditeur et l’heure d’actualisation du logiciel sont disponibles sur le portail Azure. Pour avoir accès à la date d’installation et à d’autres informations, activez les diagnostics au niveau de l’invité et placez les journaux des événements Windows dans un espace de travail Log Analytics.

- [Procédure pour créer des requêtes avec l’Explorateur Azure Resource Graph](../governance/resource-graph/first-query-portal.md) 

- [Gestion de l’inventaire de sécurité dans Azure Security Center](../security-center/asset-inventory.md) 

- [Guides de décision concernant le nommage et l’étiquetage des ressources](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Guide pratique pour activer l’inventaire des machines virtuelles Azure](../automation/automation-tutorial-installed-software.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3 : Utiliser des services Azure approuvés uniquement

**Conseils** : Utilisez Azure Policy pour auditer et limiter les services que les utilisateurs peuvent approvisionner dans votre environnement. Utilisez Azure Resource Graph pour interroger et découvrir des ressources dans leurs abonnements. Vous pouvez également utiliser Azure Monitor pour créer des règles afin de déclencher des alertes lorsqu’un service non approuvé est détecté.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Procédure pour créer des requêtes avec l’Explorateur Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4 : Garantir la sécurité de la gestion du cycle de vie des actifs

**Aide** : Non applicable. Windows Virtual Desktop ne peut pas être utilisé pour garantir la sécurité des ressources dans un processus de gestion du cycle de vie. Il incombe au client de gérer les attributs et les configurations réseau des ressources considérées comme ayant un impact élevé. 

Il est recommandé que le client crée un processus pour capturer les modifications des attributs et de la configuration du réseau, mesurer l’impact des modifications et créer des tâches de correction, le cas échéant.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Aide** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ».

- [Guide pratique pour configurer l’accès conditionnel de façon à bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6 : Utiliser uniquement des applications approuvées dans les ressources de calcul

**Aide** : Utilisez l’inventaire des machines virtuelles Azure pour automatiser la collecte d’informations sur tous les logiciels présents sur les machines virtuelles. Le nom, la version, l’éditeur et l’heure d’actualisation du logiciel sont disponibles sur le portail Azure. Pour avoir accès à la date d’installation et à d’autres informations, activez les diagnostics au niveau de l’invité et placez les journaux des événements Windows dans un espace de travail Log Analytics.

- [Guide pratique pour activer l’inventaire des machines virtuelles Azure](../automation/automation-tutorial-installed-software.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="logging-and-threat-detection"></a>Journalisation et détection des menaces

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Journalisation et détection des menaces](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1 : Activer la détection des menaces pour les ressources Azure

**Aide** : Utilisez la capacité de détection des menaces intégrée à Azure Security Center et activez Azure Defender (officiellement Azure Advanced Threat Protection) pour vos ressources Windows Virtual Desktop. Azure Defender pour Windows Virtual Desktop offre une couche supplémentaire de sécurité intelligente qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de vos ressources Windows Virtual Desktop.

Transférez tous les journaux de Windows Virtual Desktop vers votre solution SIEM qui peut être utilisée pour configurer des détections de menaces personnalisées. Veillez à surveiller les différents types de ressources Azure pour identifier les menaces et anomalies potentielles. Concentrez-vous sur l’obtention d’alertes de haute qualité afin de réduire les faux positifs que les analystes doivent trier. Les alertes peuvent provenir de données de journal, d’agents ou d’autres données.

- [Protection contre les menaces dans Azure Security Center](/azure/security-center/threat-protection) 

- [Guide de référence des alertes de sécurité Azure Security Center](../security-center/alerts-reference.md)

- [Créer des règles d’analytique personnalisées pour détecter des menaces](../sentinel/tutorial-detect-threats-custom.md) 

- [Renseignement sur les menaces informatiques dans Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2 : Activer la détection des menaces pour la gestion des identités et des accès Azure

**Aide** : Azure Active Directory (Azure AD) fournit les journaux d’utilisateur suivants, qui peuvent être consultés dans les rapports Azure AD ou intégrés à Azure Monitor, Azure Sentinel ou d’autres outils de surveillance ou SIEM pour des cas d’usage plus sophistiqués de surveillance et d’analyse :

- Connexion : le rapport de connexions fournit des informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.

- Journaux d’audit : traçabilité proposée via des journaux d’activité pour toutes les modifications effectuées par diverses fonctionnalités au sein d’Azure AD. Par exemple, les journaux d’audit peuvent inclure les modifications apportées à des ressources dans Azure AD comme l’ajout ou la suppression d’utilisateurs, d’applications, de groupes, de rôles, de stratégies, etc.

- Connexion risquée : une connexion risquée indique une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur.

- Utilisateurs avec indicateur de risque : un utilisateur à risque correspond à un indicateur de compte d’utilisateur susceptible d’être compromis.

Azure Security Center peut également alerter en cas de certaines activités suspectes, comme un nombre excessif de tentatives d’authentification ayant échoué et la présence de comptes dépréciés dans l’abonnement. En plus de la surveillance de base de l’hygiène de sécurité, le module Protection contre les menaces d’Azure Security Center peut également collecter des alertes de sécurité plus approfondies à partir de ressources de calcul Azure individuelles (machines virtuelles, conteneurs ou service d’application), de ressources de données (base de données SQL et stockage) et de couches de service Azure. Cette capacité vous permet d’avoir une visibilité sur les anomalies de compte à l’intérieur des ressources individuelles.

- [Rapports d’activité d’audit dans Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Activer Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Protection contre les menaces dans Azure Security Center](/azure/security-center/threat-protection)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3 : Activer la journalisation pour les activités réseau Azure

**Aide** : Windows Virtual Desktop ne génère ni ne traite les journaux de requête DNS. Toutefois, les ressources qui sont inscrites auprès du service peuvent générer des journaux de flux.

Activez et collectez les journaux de flux et de ressources des groupes de sécurité réseau, les journaux de Pare-feu Azure et ceux de Web Application Firewall (WAF) afin d’analyser la sécurité et de prendre en charge les enquêtes sur les incidents, la chasse aux menaces et la génération d’alertes de sécurité. Vous pouvez envoyer les journaux de flux à un espace de travail Log Analytics sur Azure Monitor, puis utiliser Traffic Analytics pour obtenir des insights.

- [Tutoriel : journaliser le trafic réseau à destination et en provenance d’une machine virtuelle à l’aide du portail Azure](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Journaux et métriques du pare-feu Azure](/azure/firewall/logs-and-metrics) 

- [Traffic Analytics](../network-watcher/traffic-analytics.md) 

- [Solutions de supervision réseau Azure dans Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4 : Activer la journalisation pour les ressources Azure

**Aide** : Les journaux d’activité, qui sont activés automatiquement, contiennent toutes les opérations d’écriture (PUT, POST, DELETE) pour vos ressources Windows Virtual Desktop, à l’exception des opérations de lecture (GET). Les journaux d’activité peuvent être utilisés pour rechercher une erreur lors de la résolution de problèmes ou pour surveiller la manière dont un utilisateur de votre organisation a modifié une ressource.

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Présentation de la journalisation et des différents types de journaux dans Azure](../azure-monitor/platform/platform-logs-overview.md)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Partagé

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5 : Centraliser la gestion et l’analyse des journaux de sécurité

**Conseils** : Centralisez le stockage et l’analyse de la journalisation pour activer la corrélation. Pour chaque source de journal, assurez-vous d’avoir attribué un propriétaire de données, des conseils d’accès, un emplacement de stockage, les outils utilisés pour traiter les données et y accéder, ainsi que les exigences de conservation des données.

Veillez à intégrer les journaux d’activité Azure dans votre journalisation centralisée. Ingérez des journaux par le biais d’Azure Monitor pour agréger les données de sécurité générées par les appareils de point de terminaison, les ressources réseau et d’autres systèmes de sécurité. Dans Azure Monitor, utilisez des espaces de travail Log Analytics pour interroger et effectuer l’analytique, et utilisez des comptes Stockage Azure pour le stockage à long terme et l’archivage.

De plus, activez et intégrez les données dans Azure Sentinel ou une solution SIEM (Informations de sécurité et gestion d’événements) tierce. De nombreuses organisations choisissent d’utiliser Azure Sentinel pour les données « chaudes » qui sont utilisées fréquemment et Stockage Azure pour les données « froides » qui sont utilisées moins fréquemment.

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1 : Préparation – mettre à jour le processus de réponse aux incidents pour Azure

**Conseils** : Assurez-vous que votre organisation dispose de processus pour répondre aux incidents de sécurité, qu’elle a mis à jour ces processus pour Azure et qu’elle les exerce régulièrement pour garantir la préparation.

- [Implémenter la sécurité dans l’environnement de l’entreprise](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guide de référence sur les réponses aux incidents](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2 : Préparation – configurer la notification d’incident

**Conseils** : Configurez les coordonnées des personnes à contacter en cas d’incident de sécurité dans Azure Security Center. Microsoft utilisera ces coordonnées afin de vous contacter si le Microsoft Security Response Center (MSRC) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé. Vous avez également la possibilité de personnaliser les alertes et les notifications d’incidents dans différents services Azure en fonction de vos besoins en matière de réponse aux incidents. 

- [Comment définir le contact de sécurité d’Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3 : Détection et analyse – créer des incidents en fonction d’alertes de haute qualité

**Instructions** : Veillez à avoir un processus de création d’alertes de bonne qualité et de mesure de la qualité des alertes. Cela vous permet de tirer les leçons des incidents passés et de classer par ordre de priorité les alertes pour les analystes, afin qu’ils ne perdent pas de temps sur les faux positifs. 

Vous pouvez créer des alertes de bonne qualité en vous basant sur l’expérience des incidents passés, sur les sources validées par la communauté, et sur des outils conçus pour générer et nettoyer les alertes en fusionnant et en mettant en corrélation différentes sources de signaux. 

Azure Security Center fournit des alertes de haute qualité sur de nombreuses ressources Azure. Vous pouvez utiliser le connecteur de données ASC pour diffuser en continu les alertes vers Azure Sentinel. Azure Sentinel vous permet de créer des règles d’alerte avancées pour générer automatiquement des incidents à des fins d’enquête. 

Exportez vos alertes et recommandations Azure Security Center en utilisant la fonctionnalité d’exportation pour identifier les risques pesant sur les ressources Azure. Exportez les alertes et les recommandations manuellement ou automatiquement de manière continue.

- [Procédure de configuration de l’exportation](../security-center/continuous-export.md)

- [Comment envoyer des alertes à Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4 : Détection et analyse – enquêter sur un incident

**Conseils** : Veiller à ce que les analystes puissent interroger et utiliser diverses sources de données lorsqu’ils enquêtent sur des incidents potentiels, afin d’obtenir une vision complète de ce qui s’est passé. Différents journaux doivent être collectés pour suivre les activités d’un attaquant potentiel tout au long de la chaîne de destruction afin d’éviter les angles morts.  Vous devez également vous assurer que les insights et les enseignements sont capturés pour d’autres analystes et pour une référence historique future.  

Les sources de données à examiner comprennent les sources de journalisation centralisées qui sont déjà collectées auprès des services et des systèmes en fonctionnement concernés, mais elles peuvent également inclure les éléments suivants :

- Données réseau : utilisez les journaux de flux des groupes de sécurité réseau, Azure Network Watcher et Azure Monitor pour capturer des journaux de flux réseau et d’autres informations analytiques. 

- Captures instantanées des systèmes en fonctionnement : 

    - Utilisez la capacité de capture instantanée de la machine virtuelle Azure pour créer un instantané du disque du système en fonctionnement. 

    - Utilisez la capacité native de sauvegarde de la mémoire du système d’exploitation pour créer un instantané de la mémoire du système en fonctionnement.

    - Utilisez la capacité de capture instantanée des services Azure ou celle de votre logiciel pour créer des instantanés des systèmes en fonctionnement.

Azure Sentinel fournit des analyses de données approfondies sur pratiquement toutes les sources de journal et un portail de gestion des cas pour gérer le cycle de vie complet des incidents. Les renseignements obtenus au cours d’une enquête peuvent être associés à un incident à des fins de suivi et de rapport. 

- [Capture instantanée du disque d’un ordinateur Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Capture instantanée du disque d’un ordinateur Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Collecte de l’image mémoire et des informations de diagnostic par le support Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Examiner les incidents avec Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5 : Détection et analyse – classer par ordre de priorité les incidents

**Conseils** : Donnez aux analystes le contexte sur lequel les incidents doivent se concentrer en premier lieu en fonction de la gravité de l’alerte et de la sensibilité des ressources. 

Azure Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance accordé par Security Center à la découverte ou à l’analytique ayant servi à émettre l’alerte, ainsi que du niveau de confiance quant à l’existence d’une intention malveillante sous-jacente dans l’activité à l’origine de l’alerte.

En outre, marquez les ressources à l’aide d’étiquettes et créez un système de nommage pour identifier et classer les ressources Azure, en particulier celles qui traitent des données sensibles. Il vous incombe de hiérarchiser le traitement des alertes en fonction de la criticité des ressources et de l’environnement Azure où l’incident s’est produit.

- [Alertes de sécurité dans le Centre de sécurité Azure](../security-center/security-center-alerts-overview.md)

- [Organisation des ressources Azure à l’aide de catégories](/azure/azure-resource-manager/resource-group-using-tags)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6 : Confinement, éradication et récupération – automatiser la gestion des incidents

**Conseils** : Automatisez les tâches manuelles répétitives pour accélérer le temps de réponse et réduire la charge de travail des analystes. Les tâches manuelles sont plus longues à exécuter, ce qui ralentit chaque incident et réduit le nombre d’incidents qu’un analyste peut traiter. Les tâches manuelles augmentent également la fatigue des analystes, ce qui accroît le risque d’erreur humaine entraînant des retards et dégrade la capacité des analystes à se concentrer efficacement sur des tâches complexes. Utilisez les fonctionnalités d’automatisation des workflows dans Azure Security Center et Azure Sentinel pour déclencher automatiquement des actions ou exécuter un playbook pour répondre aux alertes de sécurité entrantes. Le playbook prend des mesures, telles que l’envoi de notifications, la désactivation de comptes et l’isolement des réseaux problématiques. 

- [Configurer l’automatisation du workflow dans Security Center](../security-center/workflow-automation.md)

- [Configurer des réponses automatisées aux menaces dans Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurer des réponses automatisées aux menaces dans Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="posture-and-vulnerability-management"></a>Gestion de la posture et des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion de la posture et des vulnérabilités](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3 : Établir des configurations sécurisées pour des ressources de calcul

**Conseils** : Utilisez Azure Security Center et Azure Policy pour établir des configurations sécurisées sur toutes les ressources de calcul, notamment les machines virtuelles, les conteneurs, etc.

Vous pouvez utiliser des images de système d’exploitation personnalisées ou Azure Automation State Configuration pour établir la configuration de sécurité du système d’exploitation requise par votre organisation.

- [Suivi des recommandations d'Azure Security Center](../security-center/security-center-recommendations.md) 

- [Présentation d'Azure Automation State Configuration](../automation/automation-dsc-overview.md) 

- [Environnement Windows Virtual Desktop](environment-setup.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4 : Supporter des configurations sécurisées pour des ressources de calcul

**Aide** : Utilisez Azure Security Center et Azure Policy pour évaluer régulièrement les risques de configuration sur toutes vos ressources de calcul Azure, notamment les machines virtuelles, les conteneurs, etc., et les atténuer. En outre, vous pouvez utiliser des modèles Azure Resource Manager, des images de système d’exploitation personnalisées ou Azure Automation State Configuration pour tenir à jour la configuration de sécurité du système d’exploitation que votre organisation requiert. Combinés avec Azure Automation State Configuration, les modèles de machine virtuelle Microsoft peuvent vous aider à satisfaire aux exigences de sécurité, ainsi qu’à les tenir à jour.

Les images de machines virtuelles de la Place de marché Azure publiées par Microsoft sont gérées et appliquées par Microsoft.

Azure Security Center peut également analyser les vulnérabilités dans l’image conteneur et effectuer une supervision continue de votre configuration Docker dans des conteneurs, en fonction du benchmark Docker de Center Internet Security. Vous pouvez utiliser la page de recommandations Azure Security Center pour consulter les recommandations et corriger les problèmes.

- [Implémenter les recommandations d'évaluation des vulnérabilités d'Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Guide pratique pour créer une machine virtuelle Azure à partir d’un modèle ARM](../virtual-machines/windows/ps-template.md) 

- [Présentation d'Azure Automation State Configuration](../automation/automation-dsc-overview.md) 

- [Sécurité des conteneurs dans Security Center](../security-center/container-security.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5 : Stocker de manière sécurisée des images de système d’exploitation et des images conteneur personnalisées

**Aide** : Windows Virtual Desktop permet aux clients de gérer des images de système d’exploitation. Utilisez le contrôle d’accès en fonction du rôle Azure (RBAC Azure) pour garantir que seuls les utilisateurs autorisés peuvent accéder à vos images personnalisées. Une galerie Azure Shared Image Gallery vous permet de partager vos images avec différents utilisateurs, principaux de service ou groupes Active Directory au sein de votre organisation. Stockez les images conteneur dans Azure Container Registry, et utilisez un contrôle d’accès en fonction du rôle pour vous assurer que seuls des utilisateurs autorisés puissent y accéder.

- [Présentation d’Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Comment configurer Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Présentation de Shared Image Gallery](/azure/virtual-machines/windows/shared-image-galleries)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6 : Effectuez des évaluations des vulnérabilités logicielles

**Aide** : Windows Virtual Desktop vous permet de déployer vos propres machines virtuelles et de les inscrire auprès du service, ainsi que d’exécuter la base de données SQL dans l’environnement.

Windows Virtual Desktop peut utiliser une solution tierce pour effectuer des évaluations des vulnérabilités sur les périphériques réseau et les applications web. Lors de l’exécution d’analyses à distance, n’utilisez pas un compte d’administration unique et perpétuel. Envisagez d’implémenter une méthodologie d’approvisionnement JIT pour le compte d’analyse. Les informations d’identification du compte d’analyse doivent être protégées, surveillées et utilisées uniquement pour l’analyse des vulnérabilités.

Exportez les résultats de l’analyse à intervalles réguliers comme il convient, et comparez les résultats pour vérifier que les vulnérabilités ont été corrigées.

Suivez les recommandations d’Azure Security Center relatives à l’évaluation des vulnérabilités sur vos machines virtuelles Azure (et serveurs SQL). Azure Security Center dispose d’un analyseur de vulnérabilité intégré pour la machine virtuelle, les images conteneur et la base de données SQL.

Exportez les résultats de l’analyse à intervalles réguliers comme il convient, et comparez les résultats pour vérifier que les vulnérabilités ont été corrigées. Lorsque vous suivez les recommandations de gestion des vulnérabilités proposées par Azure Security Center, vous pouvez pivoter vers le portail de la solution sélectionnée pour afficher les données d’analyse historiques.

- [Implémenter les recommandations d'évaluation des vulnérabilités d'Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Analyseur de vulnérabilité intégré pour machines virtuelles](/azure/security-center/built-in-vulnerability-assessment) 
- [Évaluation des vulnérabilités SQL](../azure-sql/database/sql-vulnerability-assessment.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7 : Corriger rapidement et automatiquement des vulnérabilités logicielles

**Aide** : Windows Virtual Desktop n’utilise ni ne nécessite aucun logiciel tiers. Cependant, Windows Virtual Desktop vous permet de déployer vos propres machines virtuelles et de les inscrire auprès du service.

Utilisez Azure Automation Update Management ou une solution tierce pour garantir que les mises à jour de sécurité les plus récentes sont installées sur vos machines virtuelles Windows Serveur. Pour les machines virtuelles Windows, assurez-vous que Windows Update a été activé et configuré pour être mis à jour automatiquement.

Utilisez une solution tierce de gestion des correctifs pour les logiciels tiers ou un éditeur de mise à jour System Center pour Configuration Manager.

- [Configurer Update Management pour les machines virtuelles dans Azure](/azure/automation/update-management/overview) 

- [Gestion des mises à jour et des correctifs des machines virtuelles Azure](/azure/automation/update-management/manage-updates-for-vm)

- [Configurer Microsoft Endpoint Configuration Manager pour Windows Virtual Desktop](configure-automatic-updates.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8 : Effectuer une simulation d’attaque régulière

**Aide** : Windows Virtual Desktop ne permet pas aux clients d’effectuer leurs propres tests d’intrusion sur leurs ressources Windows Virtual Desktop.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

## <a name="endpoint-security"></a>Sécurité des points de terminaison

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Sécurité des points de terminaison](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1 : Utiliser la détection de point de terminaison et réponse (EDR)

**Aide** : Windows Virtual Desktop ne fournit aucune fonctionnalité spécifique pour les processus de détection des points de terminaison et réponse (EDR). Toutefois, les ressources inscrites auprès du service peuvent bénéficier de fonctionnalités de détection des points de terminaison et réponse. 

Activez les capacités de détection de point de terminaison et réponse pour les serveurs et les clients, et intégrez-les aux solutions SIEM et aux processus d’opérations de sécurité.

La solution Advanced Threat Protection de Microsoft Defender fournit des fonctionnalités de détection de point de terminaison et réponse dans le cadre d’une plateforme de sécurité des points de terminaison d’entreprise pour prévenir et détecter les menaces avancées, enquêter sur elles et y répondre.

- [Vue d’ensemble de Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Service Microsoft Defender ATP pour les serveurs Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Service Microsoft Defender ATP pour les serveurs non Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [Microsoft Defender ATP pour une infrastructure de bureau virtuel non persistante](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2 : Utiliser un logiciel anti-programme malveillant moderne géré de manière centralisée

**Aide** : Protégez vos ressources Windows Virtual Desktop avec une solution de protection contre les programmes malveillants de point de terminaison moderne et gérée de manière centralisée qui prend en charge l’analyse périodique et en temps réel.

Azure Security Center peut identifier automatiquement l’utilisation d’un certain nombre de solutions anti-programmes malveillants populaires pour vos machines virtuelles, signaler l’état de fonctionnement de la protection des points de terminaison et formuler des recommandations.

Microsoft Antimalware pour Azure Cloud Services est le logiciel anti-programme malveillant par défaut pour les machines virtuelles Windows. En outre, vous pouvez utiliser la détection des menaces avec Azure Security Center pour les services de données afin de détecter les programmes malveillants chargés sur des comptes de stockage Azure.

- [Guide pratique pour configurer Microsoft Antimalware pour les services cloud et les machines virtuelles](../security/fundamentals/antimalware.md) 

- [Solutions de protection des points de terminaison prises en charge](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3 : Vérifier que les logiciels et signatures anti-programme malveillant sont mis à jour

**Aide** : Vérifiez que les signatures anti-programme malveillant sont mises à jour rapidement et de manière cohérente.

Suivez les recommandations faites dans Azure Security Center : « Calcul &amp; applications » pour mettre à jour les signatures de toutes les machines virtuelles et/ou conteneurs.

Par défaut, Microsoft Antimalware installe automatiquement les dernières signatures et mises à jour du moteur.

- [Guide pratique pour déployer Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles](../security/fundamentals/antimalware.md) 

- [Évaluation de la protection de point de terminaison et recommandations dans Azure Security Center](../security-center/security-center-endpoint-protection.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="backup-and-recovery"></a>Sauvegarde et récupération

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Sauvegarde et récupération](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1 : Garantir des sauvegardes automatiques régulières

**Aide** : Veillez à sauvegarder les systèmes et les données pour assurer la continuité de l’activité après un événement inattendu. Il doit s’agir d’une orientation par rapport aux objectifs de point de récupération (RPO) et de délai de récupération (RTO).

Activez le service Sauvegarde Azure et configurez la source de la sauvegarde (par exemple, machines virtuelles Azure, SQL Server ou partages de fichiers), ainsi que la fréquence et la période de rétention souhaitées.

Pour un niveau de redondance plus élevé, vous pouvez activer l’option de stockage géoredondant afin de répliquer les données de sauvegarde dans une région secondaire et de les récupérer à l’aide d’une restauration inter-région.

- [Continuité d’activité et reprise d’activité à l’échelle de l’entreprise](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Guide pratique pour activer la Sauvegarde Azure](/azure/backup/) 

- [Comment activer la restauration inter-région ?](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) 

- [Configurer un plan de continuité d’activité et la reprise d’activité dans Windows Virtual Desktop](disaster-recovery.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2 : Chiffrer les données de sauvegarde

**Aide** : Assurez-vous que vos sauvegardes sont protégées contre les attaques. Cela doit inclure le chiffrement des sauvegardes afin de vous protéger contre la perte de confidentialité.

Pour une sauvegarde de service Azure normale, les données de sauvegarde sont automatiquement chiffrées à l’aide de clés gérées par la plateforme Azure. Vous pouvez choisir de chiffrer la sauvegarde à l’aide d’une clé gérée par le client. Dans ce cas, assurez-vous que cette clé gérée par le client dans le coffre de clés est également dans l’étendue de la sauvegarde.

Utilisez le contrôle d’accès en fonction du rôle dans Sauvegarde Azure, Azure Key Vault ou d’autres ressources pour protéger les sauvegardes et les clés gérées par le client. En outre, vous pouvez activer des fonctionnalités de sécurité avancées pour exiger une authentification multifacteur avant que les sauvegardes soient modifiées ou supprimées.

Vue d’ensemble des fonctionnalités de sécurité dans Sauvegarde Azure /azure/backup/security-overview 

- [Chiffrement des données de sauvegarde à l’aide de clés gérées par le client](/azure/backup/encryption-at-rest-with-cmk) 

- [Guide pratique pour sauvegarder des clés de coffre de clés dans Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&amp;preserve-view=true)

- [Fonctionnalités de sécurité pour la protection de sauvegardes hybrides contre des attaques](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Aide** : Il est recommandé de valider régulièrement l’intégrité des données sur les supports de sauvegarde en effectuant un processus de restauration des données pour garantir le bon fonctionnement de la sauvegarde.

- [Guide pratique pour récupérer des fichiers à partir d’une sauvegarde de machines virtuelles Azure](/azure/backup/backup-azure-restore-files-from-vm)

- [Implémentation de la sécurité](/azure/backup/backup-azure-restore-files-from-vm#security-implementations)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

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

-   Utilisation des fonctionnalités de protection des données natives et tierces d’Azure

-   Exigences de chiffrement des données pour les cas d’utilisation en transit et au repos

-   Normes de chiffrement appropriées

Pour plus d’informations, consultez les références suivantes :
- [Recommandation d’architecture de sécurité Azure - Stockage, données et chiffrement](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Notions de base de la sécurité Azure - Sécurité, chiffrement et stockage des données Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Meilleures pratiques en matière de chiffrement et de sécurité des données Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Benchmark de sécurité Azure - Gestion des ressources](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Benchmark de sécurité Azure - Protection des données](/azure/security/benchmarks/security-controls-v2-data-protection)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2 : Définir la stratégie de segmentation d'entreprise 

**Conseils** : Établissez une stratégie à l'échelle de l'entreprise pour segmenter l'accès aux ressources à l'aide d'une combinaison de contrôles d'identité, de réseau, d'application, d'abonnement, de groupe de gestion et autres.

Trouvez le bon équilibre entre la nécessité de séparation sur le plan de la sécurité et la nécessité d'exécuter quotidiennement les systèmes qui doivent communiquer entre eux et accéder aux données.

Veillez à ce que la stratégie de segmentation soit implémentée de manière cohérente pour tous les types de contrôle, y compris pour les modèles d'identité, d'accès et de sécurité du réseau, les modèles d'autorisation/d'accès aux applications et les contrôles des processus humains.

- [Aide relative à la stratégie de segmentation dans Azure (vidéo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Aide relative à la stratégie de segmentation dans Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Aligner la segmentation du réseau avec la stratégie de segmentation d’entreprise](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3 : Définir la stratégie de gestion de la posture de la sécurité

**Conseils** : Mesurez et atténuez en permanence les risques liés à vos ressources individuelles et à l’environnement dans lequel elles sont hébergées. Priorisez les ressources à valeur élevée et les surfaces d’attaque hautement exposées, comme les applications publiées, les points d’entrée et de sortie du réseau, les points de terminaison utilisateur et administrateur, etc.

- [Benchmark de sécurité Azure - Gestion de la posture et des vulnérabilités](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4 : Aligner les rôles et les responsabilités de l’organisation

**Conseils** : Veillez à documenter et à communiquer une stratégie claire pour les rôles et les responsabilités de votre organisation de sécurité. Veillez à définir clairement les responsabilités pour les décisions relatives à la sécurité, à former tout le monde au modèle de responsabilité partagée et à former les équipes techniques à la technologie permettant de sécuriser le cloud.

- [Meilleures pratiques pour la sécurité Azure 1 – Personnes : Former les équipes pour le parcours vers la sécurité dans le cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Meilleures pratiques pour la sécurité Azure 2 – Personnes : Former les équipes pour les technologies de sécurité dans le cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Meilleures pratiques pour la sécurité Azure 3 – Processus : Affecter les responsabilités pour les décisions de sécurité dans le cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

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

- [Benchmark de sécurité Azure – Sécurité réseau](/azure/security/benchmarks/security-controls-v2-network-security)

- [Vue d’ensemble de la sécurité réseau d’Azure](../security/fundamentals/network-overview.md)

- [Stratégie d’architecture de réseau d’entreprise](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6 : Définir une stratégie d’accès privilégié et d’identité

**Conseils** : Établissez une approche d’identité Azure et d’accès privilégié dans le cadre de la stratégie de contrôle d’accès de sécurité globale de votre organisation.  

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Un système centralisé d’identité et d’authentification et son interconnexion avec d’autres systèmes d’identité internes et externes

-   Méthodes d’authentification fortes dans différents cas d’usage et différentes conditions

-   Protection des utilisateurs disposant de privilèges élevés

-   Surveillance et gestion des activités anormales des utilisateurs  

-   Vérification de l’identité et de l’accès des utilisateurs et processus de rapprochement

Pour plus d’informations, consultez les références suivantes :

- [Benchmark de sécurité Azure - Gestion des identités](/azure/automation/update-management/overview)

- [Benchmark de sécurité Azure - Accès privilégié](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Meilleures pratiques pour la sécurité Azure 11 – Architecture. Stratégie de sécurité unifiée unique](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Vue d’ensemble de la sécurité et de la gestion des identités Azure](../security/fundamentals/identity-management-overview.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

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

- [Benchmark de sécurité Azure - Journalisation et détection des menaces](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Benchmark de sécurité Azure - Réponse aux incidents](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Meilleures pratiques pour la sécurité Azure 4 - Processus. Mise à jour des processus de réponse aux incidents pour le cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guide pour le cadre d’adoption d’Azure, la journalisation et la prise de décision pour les rapports](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Mise à l’échelle, gestion et surveillance d’entreprise Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
