---
title: Ligne de base de sécurité Azure Virtual WAN
description: La base de référence de sécurité de Virtual WAN fournit des instructions et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans Azure Security Benchmark.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328606"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Ligne de base de sécurité Azure Virtual WAN

Cette base de référence de sécurité applique les conseils du[Benchmark de sécurité Azure version 2.0](../security/benchmarks/overview.md) à Microsoft Azure Virtual WAN. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure. Le contenu est regroupé par les **contrôles de sécurité** définis par le benchmark de sécurité Azure et les conseils associés applicables à Virtual WAN. Les **contrôles** non applicables à Virtual WAN ont été exclus.

Pour voir comment Virtual WAN est entièrement mappé au benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité Virtual WAN](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1 : Implémenter la sécurité pour le trafic interne

**Conseils**  : Azure Virtual WAN ne prend pas en charge le déploiement direct dans un réseau virtuel. Toutefois, vous pouvez toujours appliquer les règles de groupe de sécurité réseau aux ressources spoke, utiliser les protections du pare-feu Azure ou créer des tables de routage personnalisées pour empêcher ou autoriser le trafic privé.

- [Scénarios de routage personnalisés](scenario-any-to-any.md)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2 : Interconnecter des réseaux privés 

**Conseils**  : Azure ExpressRoute ou le réseau privé virtuel (VPN) Azure permettent de créer des connexions privées entre les centres de données Azure et l’infrastructure locale dans un environnement de colocation. Les connexions ExpressRoute ne sont pas établies via l’Internet public et offrent une plus grande fiabilité, des débits plus importants et des latences moindres par rapport aux connexions Internet classiques. 

Pour un VPN point à site et un VPN site à site, connectez des appareils ou des réseaux locaux à un réseau virtuel à l’aide de n’importe quelle combinaison de ces options VPN et d’ExpressRoute. Pour interconnecter deux réseaux virtuels ou plus dans Azure, utilisez l’appairage de réseaux virtuels. Le trafic réseau entre les réseaux virtuels appairés est privé et conservé sur le réseau principal Azure.

- [ExpressRoute dans Virtual WAN](virtual-wan-expressroute-portal.md)

- [Vue d’ensemble des VPN site à site](virtual-wan-site-to-site-portal.md)

- [Vue d’ensemble des VPN point à site](virtual-wan-point-to-site-portal.md)

- [Liaison privée](howto-private-link.md)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Partagé

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3 : Établir un accès réseau privé aux services Azure

**Conseils**  : Utilisez Azure Private Link pour activer l’accès privé à Azure Virtual WAN à partir de vos réseaux virtuels, sans passer par Internet. L’accès privé est une autre mesure de défense renforcée en plus de la sécurité de l’authentification et du trafic proposée par les services Azure.

- [Présentation d’Azure Private Link](../private-link/private-link-overview.md)

- [Private Link Virtual WAN](howto-private-link.md)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4 : Protégez les applications et les services contre les attaques réseau externes

**Conseils**  : Protégez vos ressources Azure Virtual WAN contre les attaques de réseaux externes, notamment les attaques par déni de service distribué (DDoS), les attaques spécifiques aux applications et le trafic Internet non sollicité et potentiellement malveillant. 

Utilisez le Pare-feu Azure pour protéger les applications et les services contre le trafic potentiellement malveillant provenant d’Internet et d’autres emplacements externes. Choisissez Azure DDoS Protection pour vos ressources contre les attaques sur vos réseaux virtuels Azure. Utilisez Azure Security Center pour détecter les risques de configuration incorrecte liés aux ressources associées à votre réseau.

- [Documentation sur le pare-feu Azure](/azure/firewall)

- [Gérer la norme Azure DDoS Protection à l’aide du portail Azure](/azure/virtual-network/manage-ddos-protection) 

- [Recommandations relatives à Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Partagé

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5 : Déployer des systèmes de détection et de prévention des intrusions (IDS/IPS)

**Conseils**  : La meilleure façon de déployer des systèmes de détection ou de prévention des intrusions dans Azure Virtual WAN consiste à utiliser une appliance virtuelle réseau dans les réseaux spoke joints au hub virtuel.  Des informations supplémentaires sont disponibles dans les scénarios de routage des liens référencés. 

- [Scénario - Router le trafic via une NVA](scenario-route-through-nva.md)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6 : Simplifier les règles de sécurité réseau

**Conseils**  : Utilisez des étiquettes de service de réseau virtuel Azure pour définir des contrôles d’accès réseau sur les groupes de sécurité réseau Azure ou le Pare-feu Azure configurés pour vos ressources Virtual WAN. 

Utilisez des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de l’étiquette de service (par exemple : {VirtualWAN: Virtual Network}) dans le champ source ou de destination d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7 : Système DNS (Domain Name System) sécurisé

**Conseils**  : Azure Virtual WAN offre des serveurs DNS personnalisés pour les passerelles VPN point à site. 

Suivez les bonnes pratiques concernant la sécurité DNS afin d’atténuer les attaque courantes comme les entrées DNS non résolues, les attaques d’amplifications DNS, l’empoisonnement et l’usurpation d’identité DNS, etc.

Quand Azure DNS est utilisé comme votre service DNS faisant autorité, vérifiez que les zones et les enregistrements DNS sont protégés contre toute modification accidentelle ou malveillante à l’aide du contrôle d’accès en fonction du rôle (RBAC) Azure et de verrous de ressources.

- [Vue d’ensemble d’Azure DNS](../dns/dns-overview.md) 

- [Guide de déploiement d’un système DNS (Domain Name System) sécurisé](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Prévention des entrées DNS non résolues et de l’acquisition de sous-domaine](../security/fundamentals/subdomain-takeover.md)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

## <a name="identity-management"></a>Gestion des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des identités](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1 : Normaliser Azure Active Directory comme système d’authentification et d’identité central

**Conseils**  : Le VPN point à site d’Azure Virtual WAN est intégré à Azure Active Directory (Azure AD), qui est le service de gestion des identités et des accès par défaut d’Azure. Vous devez normaliser Azure AD pour régir la gestion des identités et des accès de votre organisation dans :

- Les ressources cloud Microsoft, comme le Portail Azure, le stockage Azure, les machines virtuelles Azure (Linux et Windows), les applications Azure Key Vault, PaaS et SaaS.
- Les ressources de votre organisation, comme les applications sur Azure ou les ressources réseau de votre entreprise.

Sécurisez Azure AD avec une priorité élevée dans les pratiques de sécurité cloud de votre organisation. Évaluez votre identité et votre niveau de sécurité avec la fonctionnalité de score de sécurité d’Azure Security Center pour évaluer la précision de votre configuration par rapport aux suggestions des meilleures pratiques. Implémentez les suggestions des meilleures pratiques de Microsoft pour améliorer votre sécurité.

Azure AD prend également en charge les identités externes, qui permettent aux utilisateurs sans compte Microsoft de se connecter à leurs applications et ressources avec leur identité externe. Consultez les informations sur l’utilisation d’Azure AD dans les scénarios VPN point à site disponibles dans les liens référencés.

- [Créer un locataire Azure Active Directory (AD) pour les connexions P2S de protocole OpenVPN](openvpn-azure-ad-tenant-multi-app.md)

- [Configurer l’authentification Azure Active Directory pour une connexion VPN utilisateur](virtual-wan-point-to-site-azure-ad.md)

- [Locataires dans Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Création et configuration d’une instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Utilisez des fournisseurs d'identité externes pour l’application](/azure/active-directory/b2b/identity-providers)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Partagé

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4 : Utiliser des contrôles d’authentification renforcés pour tous les accès basés sur Azure Active Directory

**Conseils**  : Le VPN point à site d’Azure Virtual WAN est intégré à Azure Active Directory (Azure AD), qui prend en charge des contrôles d’authentification forts avec l’authentification multifacteur et des méthodes sans mot de passe fortes.

- Authentification multifacteur : activez l’authentification multifacteur Azure AD et suivez les recommandations relatives à la gestion des identités et des accès d’Azure Security Center pour les meilleures pratiques pour votre configuration de l’authentification multifacteur. L’authentification multifacteur peut être appliquée à tous les utilisateurs, certains utilisateurs ou au niveau de chaque utilisateur en fonction des conditions de connexion et des facteurs de risque.

- Authentification sans mot de passe : trois options d’authentification sans mot de passe sont proposées. Il s’agit de Windows Hello Entreprise, de l’application Microsoft Authenticator et des méthodes d’authentification locales, comme les cartes à puce.

Pour les utilisateurs administrateurs et privilégiés, assurez-vous que le niveau le plus élevé de méthode d’authentification forte est utilisé, puis déployez la stratégie d’authentification forte appropriée pour les autres utilisateurs.

- [Procédure d’activation de l’authentification multifacteur dans P2S VPN pour Virtual WAN](openvpn-azure-ad-mfa.md)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Partagé

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6 : Restreindre l’accès aux ressources Azure en fonction des conditions

**Conseils**  : Le VPN point à site d’Azure Virtual WAN prend en charge l’accès conditionnel Azure AD pour un contrôle d’accès plus granulaire en fonction des conditions définies par l’utilisateur. Par exemple, les connexions utilisateur de certaines plages d’adresses IP doivent utiliser l’authentification multifacteur pour la connexion. Une stratégie de gestion granulaire des sessions d’authentification peut également être utilisée dans différents cas d’usage.

- [Stratégies d’accès conditionnel courantes](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurer la gestion des sessions d’authentification avec l’accès conditionnel](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [Accès conditionnel P2S VPN de Virtual WAN](openvpn-azure-ad-mfa.md#conditional)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7 : Éliminer l’exposition involontaire des informations d’identification

**Conseils**  : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault.

Pour GitHub, vous pouvez utiliser la fonctionnalité native d’analyse de secret pour identifier les informations d’identification ou d’autres formes de secrets dans le code.

- [Configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Analyse du secret GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

## <a name="privileged-access"></a>Accès privilégié

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Accès privilégié](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2 : Limiter l’accès administratif aux systèmes critiques de l’entreprise

**Conseils**  : Azure Virtual WAN utilise les contrôles d’accès en fonction du rôle Azure (Azure RBAC) pour isoler l’accès aux systèmes vitaux pour l'entreprise en restreignant les comptes disposant d’un accès privilégié aux abonnements et aux groupes d’administration dans lesquels ils se trouvent.

Limitez également l’accès aux systèmes de gestion, d’identité et de sécurité qui ont un accès administratif à vos ressources critiques, comme les contrôleurs de domaine Active Directory, les outils de sécurité et les outils de gestion du système avec les agents installés sur les systèmes critiques de l’entreprise. Les attaquants qui compromettent ces systèmes de gestion et de sécurité peuvent immédiatement les armer pour compromettre les ressources stratégiques de l’entreprise.

Pour assurer un contrôle d’accès cohérent, tous les types de contrôle d’accès doivent être alignés sur la stratégie de segmentation de votre entreprise.

- [Composants Azure et modèle de référence](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Accès aux groupes d’administration](../governance/management-groups/overview.md#management-group-access) 

- [Administrateurs d’abonnements Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6 : Utiliser des stations de travail d’accès privilégié

**Conseils**  : Les stations de travail sécurisées et isolées sont extrêmement importantes pour la sécurité des rôles sensibles comme les administrateurs, développeurs et opérateurs de service critique. Utilisez des stations de travail utilisateur hautement sécurisées ou Azure Bastion pour les tâches d’administration. Utilisez Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (MDATP) ou Microsoft Intune pour déployer une station de travail utilisateur sécurisée et gérée pour les tâches d’administration. Les stations de travail sécurisées peuvent être gérées de manière centralisée pour appliquer une configuration sécurisée, notamment une authentification forte, des lignes de base logicielles et matérielles et un accès réseau et logique restreint.

- [Comprendre les stations de travail d’accès privilégié](../active-directory/devices/concept-azure-managed-workstation.md)

- [Déployer une station de travail d’accès privilégié](../active-directory/devices/howto-azure-managed-workstation.md)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4 : Chiffrement des informations sensibles en transit

**Conseils**  : Le chiffrement est essentiel pour le trafic sur des réseaux externes et publics.

- Utilisez des contrôles d’accès, les données en transit doivent être protégées contre les attaques « hors bande » (par exemple, la capture de trafic) à l’aide du chiffrement pour empêcher les attaquants de lire ou de modifier facilement les données.

- Pour le trafic HTTP, vérifiez que les clients se connectant à vos ressources Azure peuvent négocier TLS v1.2 ou une version supérieure.

- Pour la gestion à distance, utilisez SSH (pour Linux) ou RDP/TLS (pour Windows) plutôt qu’un protocole non chiffré. Les versions SSL, TLS et SSH rendues obsolètes, les protocoles et les chiffrements faibles doivent être désactivés.

- Au niveau de l’infrastructure sous-jacente, Azure fournit des données en transit par défaut pour le trafic de données entre les centres de données Azure.

En général, nous fournissons le chiffrement sur le réseau principal sécurisé de Microsoft et les opportunités pour chiffrer le trafic dans votre VPN site à site, le VPN site à site sur Azure ExpressRoute et le VPN utilisateur point à site.

- [Présentation du chiffrement en transit avec Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informations sur la sécurité TLS](/security/engineering/solving-tls1-problem)

- [Double chiffrement pour les données Azure en transit](../security/fundamentals/double-encryption.md#data-in-transit)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Partagé

## <a name="asset-management"></a>Gestion des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des ressources](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2 : S’assurer que l’équipe de sécurité a accès à l’inventaire des ressources et aux métadonnées

**Conseils**  : Appliquez des étiquettes à vos ressources Azure, groupes de ressources et abonnements pour les organiser de façon logique dans une taxonomie. Chaque balise se compose d’une paire nom-valeur. Par exemple, vous pouvez appliquer le nom « Environnement » et la valeur « Production » à toutes les ressources en production.

Azure Virtual WAN prend également en charge les déploiements de ressources basés sur Azure Resource Manager et les requêtes Azure Resource Graph. 

- [Pour plus d’informations sur l’étiquetage des ressources, consultez le guide de décision concernant le nommage et l’étiquetage des ressources](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Procédure pour créer des requêtes avec l’Explorateur Azure Resource Graph](../governance/resource-graph/first-query-portal.md) 

- [Gestion de l’inventaire de sécurité dans Azure Security Center](../security-center/asset-inventory.md)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3 : Utiliser des services Azure approuvés uniquement

**Conseils**  : Utilisez Azure Policy pour limiter les services pouvant être approvisionnés dans votre environnement. Interrogez et découvrez des ressources avec Azure Resource Graph dans vos abonnements et utilisez Azure Monitor pour créer des règles afin de déclencher des alertes en cas de détection d’un service non approuvé.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Procédure pour créer des requêtes avec l’Explorateur Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Conseils**  : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ».

- [Configuration de l’accès conditionnel pour bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

## <a name="logging-and-threat-detection"></a>Journalisation et détection des menaces

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Journalisation et détection des menaces](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2 : Activer la détection des menaces pour la gestion des identités et des accès Azure

**Conseils**  : Azure Active Directory (Azure AD) fournit les journaux d’utilisateur suivants, qui peuvent être consultés dans les rapports Azure AD ou intégrés à Azure Monitor, Azure Sentinel, SIEM ou des outils de surveillance pour des cas d’usage plus sophistiqués de surveillance et d’analyse. Ces règles sont les suivantes :

- Connexion : le rapport de connexions fournit des informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.
- Journaux d’audit : traçabilité proposée via des journaux d’activité pour toutes les modifications effectuées par diverses fonctionnalités au sein d’Azure AD. Par exemple, les journaux d’audit peuvent inclure les modifications apportées à des ressources dans Azure AD comme l’ajout ou la suppression d’utilisateurs, d’applications, de groupes, de rôles et de stratégies.
- Connexion risquée : une connexion risquée indique une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur.
- Utilisateurs avec indicateur de risque : un utilisateur à risque correspond à un indicateur de compte d’utilisateur susceptible d’être compromis.

Azure Security Center permet de créer des alertes en cas d’activités suspectes, comme un nombre excessif de tentatives d’authentification ayant échoué, notamment de comptes déconseillés dans l’abonnement. En plus de la surveillance de base de l’hygiène de sécurité, utilisez le module Protection contre les menaces de Security Center pour collecter des alertes de sécurité plus approfondies à partir de ressources de calcul Azure individuelles (telles que des machines virtuelles, des conteneurs ou un service d’application), de ressources de données (telles que la base de données SQL et le stockage) et de couches de service Azure. Cette capacité vous permet d’avoir une visibilité sur les anomalies de compte à l’intérieur des ressources individuelles.

- [Rapports d’activité d’audit dans Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Activer Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Partagé

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3 : Activer la journalisation pour les activités réseau Azure

**Conseils**  : Utilisez les outils de capture de paquets VPN pour enregistrer les paquets réseau circulant entre vos ressources Azure Virtual WAN. Cela peut vous aider lors du processus de débogage lié à votre réseau hybride. Bien que vous ne puissiez pas déployer un groupe de sécurité réseau sur Virtual WAN, vous pouvez le déployer sur vos ressources de réseau virtuel spoke.

Activez les journaux de flux du groupe de sécurité réseau sur vos groupes de sécurité réseau pour l’audit du trafic.

Activez la fonctionnalité Azure Traffic Analytics pour traiter les journaux de flux qui sont conservés dans le compte de stockage, puis envoyez-les à un espace de travail Log Analytics. Traffic Analytics fournit des informations supplémentaires sur le flux de trafic pour vos réseaux Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

- [Tutoriel : journaliser le trafic réseau à destination et en provenance d’une machine virtuelle à l’aide du portail Azure](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Traffic Analytics](../network-watcher/traffic-analytics.md) 

Virtual WAN ne produit pas ou ne traite pas les journaux de requêtes DNS qui doivent être activés.

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4 : Activer la journalisation pour les ressources Azure

**Conseils**  : Les journaux d’activité Azure, activés automatiquement, contiennent toutes les opérations d’écriture (PUT, POST, DELETE) pour vos ressources Azure Virtual WAN, à l’exception des opérations de lecture (GET). Les journaux d’activité peuvent être utilisés pour rechercher une erreur pendant la résolution de problèmes ou pour surveiller la manière dont un utilisateur de votre organisation a modifié une ressource. Toutefois, Virtual WAN ne produit pas de journaux de ressources Azure.

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 
- [Présentation de la journalisation et des différents types de journaux dans Azure](../azure-monitor/platform/platform-logs-overview.md)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Partagé

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5 : Centraliser la gestion et l’analyse des journaux de sécurité

**Conseils**  : Centralisez le stockage et l’analyse de la journalisation pour activer la corrélation. Pour chaque source de journal, assurez-vous d’avoir attribué un propriétaire de données, des conseils d’accès, un emplacement de stockage, les outils utilisés pour traiter les données et y accéder, ainsi que les exigences de conservation des données. Veillez à intégrer les journaux d’activité Azure à vos systèmes de journalisation centralisée. 

Ingérez des journaux par le biais d’Azure Monitor pour agréger les données de sécurité générées par les appareils de point de terminaison, les ressources réseau et d’autres systèmes de sécurité. Dans Azure Monitor, utilisez des espaces de travail Log Analytics pour interroger et effectuer l’analytique, et utilisez des comptes de stockage Azure pour le stockage à long terme et l’archivage. En outre, activez et intégrez les données dans Azure Sentinel ou une solution SIEM tierce.

De nombreuses organisations choisissent d’utiliser Azure Sentinel pour les données « chaudes » qui sont utilisées fréquemment et Stockage Azure pour les données « froides » qui sont utilisées moins fréquemment.

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Partagé

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1 : Préparation – mettre à jour le processus de réponse aux incidents pour Azure

**Conseils**  : Assurez-vous que votre organisation dispose de processus pour répondre aux incidents de sécurité, qu’elle a mis à jour ces processus pour Azure et qu’elle les exerce régulièrement pour garantir la préparation. Assurez-vous que votre offre de service est incluse dans le processus de réponse aux incidents, le cas échéant.

- [Implémenter la sécurité dans l’environnement de l’entreprise](https://aka.ms/AzSec4) 
- [Guide de référence sur les réponses aux incidents](https://aka.ms/IRRG)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2 : Préparation – configurer la notification d’incident

**Conseils**  : Configurez les coordonnées des personnes à contacter en cas d’incident de sécurité dans Azure Security Center. Microsoft utilisera ces coordonnées afin de vous contacter si le Microsoft Security Response Center (MSRC) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé. Vous avez également la possibilité de personnaliser les alertes et les notifications d’incidents dans différents services Azure en fonction de vos besoins en matière de réponse aux incidents.

Microsoft utilisera les informations de contact pour le signalement d’incidents de sécurité pour vous contacter si le centre de réponse aux incidents de sécurité Microsoft (MSRC, Microsoft Security Response Center) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé. Examinez les incidents après les faits pour vous assurer que les problèmes sont intégralement résolus.

- [Comment définir le contact de sécurité d’Azure Security Center](../security-center/security-center-provide-security-contact-details.md) 

- [Comment définir le contact de sécurité d’Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3 : Détection et analyse – créer des incidents en fonction d’alertes de haute qualité

**Aide**  : Azure Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou à l’analytique utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements (par exemple, production, non-production) et créez un système de nommage pour identifier et classer les ressources Azure de façon claire.

- [Alertes de sécurité dans le Centre de sécurité Azure](../security-center/security-center-alerts-overview.md) 

- [Organisation des ressources Azure à l’aide de catégories](../azure-resource-manager/management/tag-resources.md)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4 : Détection et analyse – enquêter sur un incident

**Conseils**  : Créez un guide de réponse aux incidents pour votre organisation. Exécutez des exercices pour tester les fonctionnalités de réponse aux incidents de vos systèmes de façon régulière. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.

- [Comment configurer des automatisations de workflow dans Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Reportez-vous à la publication du NIST : Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide de test, d’entraînement et d’utilisation des programmes destinés aux plans et fonctionnalités informatiques)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5 : Détection et analyse – classer par ordre de priorité les incidents

**Aide**  : Azure Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements (tels que production, non-production) et créez un système de nommage pour identifier et classer les ressources Azure de façon claire.

- [Alertes de sécurité dans le Centre de sécurité Azure](../security-center/security-center-alerts-overview.md) 

- [Organisation des ressources Azure à l’aide de catégories](../azure-resource-manager/management/tag-resources.md)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Partagé

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6 : Confinement, éradication et récupération – automatiser la gestion des incidents

**Conseils**  : Utilisez la fonctionnalité d’automatisation du workflow dans Azure Security Center pour déclencher automatiquement des réponses via « Azure Logic Apps » sur les alertes et recommandations de sécurité.

- [Comment configurer l’automatisation des workflows et Logic Apps](../security-center/workflow-automation.md)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Customer

## <a name="posture-and-vulnerability-management"></a>Gestion de la posture et des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion de la posture et des vulnérabilités](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8 : Effectuer une simulation d’attaque régulière

**Conseils**  : Selon vos besoins, effectuez un test d’intrusion ou des activités Red Team sur vos ressources Azure et résolvez tous les problèmes de sécurité critiques détectés.

Suivez les règles d’engagement de pénétration du cloud Microsoft pour vous assurer que vos tests d’intrusion sont conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft, ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft.

- [Test d’intrusion dans Azure](../security/fundamentals/pen-testing.md)

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Supervision d’Azure Security Center**  : Non applicable

**Responsabilité**  : Customer

## <a name="endpoint-security"></a>Sécurité des points de terminaison

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Sécurité des points de terminaison](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1 : Utiliser la détection de point de terminaison et réponse (EDR)

**Conseils**  : Les clients ne sont pas explicitement autorisés à configurer les paramètres de détection et de réponse des points de terminaison. Toutefois, les machines virtuelles utilisées dans l’offre Azure Virtual WAN utilisent ces fonctionnalités. En savoir plus sur ces fonctionnalités générales à partir des liens référencés. 

- [Vue d’ensemble de Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Service Microsoft Defender ATP pour les serveurs Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Service Microsoft Defender ATP pour les serveurs non Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Supervision d’Azure Security Center**  : Oui

**Responsabilité**  : Partagé

## <a name="governance-and-strategy"></a>Gouvernance et stratégie

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gouvernance et stratégie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1 : Définir la stratégie de gestion des ressources et de protection des données 

**Conseils**  : Veillez à documenter et à communiquer une stratégie claire pour la surveillance et la protection continues des systèmes et des données. Hiérarchisez la découverte, l’évaluation, la protection et la surveillance des données et systèmes critiques de l’entreprise. 

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

- Norme de classification des données en fonction des risques pour l’entreprise
- Visibilité de l’organisation de sécurité sur les risques et l’inventaire des actifs 
- Approbation de l’organisation de sécurité pour les services Azure en vue de leur utilisation 
- Sécurité des ressources tout au long de leur cycle de vie
- Stratégie de contrôle d’accès requise conformément à la classification des données organisationnelles
- Utilisation des fonctionnalités de protection des données natives et tierces d’Azure
- Exigences de chiffrement des données pour les cas d’utilisation en transit et au repos
- Normes de chiffrement appropriées

Consultez les informations supplémentaires disponibles dans les liens référencés.

- [Recommandation d’architecture de sécurité Azure - Stockage, données et chiffrement](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Notions de base de la sécurité Azure - Sécurité, chiffrement et stockage des données Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Meilleures pratiques en matière de chiffrement et de sécurité des données Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2 : Définir la stratégie de segmentation d'entreprise 

**Conseils**  : Établissez une stratégie à l'échelle de l'entreprise pour segmenter l'accès aux ressources à l'aide d'une combinaison de contrôles d'identité, de réseau, d'application, d'abonnement, de groupe de gestion et autres. Trouvez le bon équilibre entre la nécessité de séparation sur le plan de la sécurité et la nécessité d'exécuter quotidiennement les systèmes qui doivent communiquer entre eux et accéder aux données.

Veillez à ce que la stratégie de segmentation soit implémentée de manière cohérente pour tous les types de contrôle, y compris pour les modèles d'identité, d'accès et de sécurité du réseau, les modèles d'autorisation ou d'accès aux applications et les contrôles des processus humains.

- [Aide relative à la stratégie de segmentation dans Azure (vidéo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Aide relative à la stratégie de segmentation dans Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Aligner la segmentation du réseau avec la stratégie de segmentation d’entreprise](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3 : Définir la stratégie de gestion de la posture de la sécurité

**Conseils**  : Mesurez et atténuez en permanence les risques liés à vos ressources individuelles et à l’environnement dans lequel elles sont hébergées. Priorisez les ressources à valeur élevée et les surfaces d’attaque hautement exposées, comme les applications publiées, les points d’entrée et de sortie du réseau, les points de terminaison utilisateur et administrateur, etc.

- [Benchmark de sécurité Azure - Gestion de la posture et des vulnérabilités](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4 : Aligner les rôles et les responsabilités de l’organisation

**Conseils**  : Veillez à documenter et à communiquer une stratégie claire pour les rôles et les responsabilités de votre organisation de sécurité. Veillez à définir clairement les responsabilités pour les décisions relatives à la sécurité, à former tout le monde au modèle de responsabilité partagée et à former les équipes techniques à la technologie permettant de sécuriser le cloud.

- [Meilleures pratiques pour la sécurité Azure 1 – Personnes : Former les équipes pour le parcours vers la sécurité dans le cloud](https://aka.ms/AzSec1)

- [Meilleures pratiques pour la sécurité Azure 2 – Personnes : Former les équipes pour les technologies de sécurité dans le cloud](https://aka.ms/AzSec2)

- [Meilleures pratiques pour la sécurité Azure 3 – Processus : Affecter les responsabilités pour les décisions de sécurité dans le cloud](https://aka.ms/AzSec3)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5 : Définir la stratégie de sécurité réseau

**Conseils**  : Établissez une approche de sécurité réseau Azure dans le cadre de la stratégie de contrôle d’accès de sécurité globale de votre organisation. Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

- Responsabilité centralisée pour la gestion et la sécurité du réseau
- Modèle de segmentation de réseau virtuel aligné avec la stratégie de segmentation de l’entreprise
- Stratégie de correction dans différents scénarios de menaces et d’attaques
- Stratégie de périphérie d’Internet et d’entrée et de sortie
- Stratégie de cloud hybride et d’interconnexion locale
- Artefacts de sécurité réseau à jour (par exemple diagrammes réseau, architecture de réseau de référence)

Consultez les informations supplémentaires disponibles dans les liens référencés.

- [Meilleures pratiques pour la sécurité Azure 11 – Architecture. Stratégie de sécurité unifiée unique](https://aka.ms/AzSec11)

- [Benchmark de sécurité Azure – Sécurité réseau](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Vue d’ensemble de la sécurité réseau d’Azure](../security/fundamentals/network-overview.md)

- [Stratégie d’architecture de réseau d’entreprise](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6 : Définir une stratégie d’accès privilégié et d’identité

**Conseils**  : Établissez des approches d’identité Azure et d’accès privilégié dans le cadre de la stratégie de contrôle d’accès de sécurité globale de votre organisation. Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

- Un système centralisé d’identité et d’authentification et son interconnexion avec d’autres systèmes d’identité internes et externes
- Méthodes d’authentification fortes dans différents cas d’usage et différentes conditions
- Protection des utilisateurs disposant de privilèges élevés
- Surveillance et gestion des activités anormales des utilisateurs  
- Vérification de l’identité et de l’accès des utilisateurs et processus de rapprochement

Consultez les informations supplémentaires disponibles dans les liens référencés.

- [Benchmark de sécurité Azure - Gestion des identités](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark de sécurité Azure - Accès privilégié](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Meilleures pratiques pour la sécurité Azure 11 – Architecture. Stratégie de sécurité unifiée unique](https://aka.ms/AzSec11)

- [Vue d’ensemble de la sécurité et de la gestion des identités Azure](../security/fundamentals/identity-management-overview.md)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7 : Définir la stratégie de journalisation et de réponse aux menaces

**Conseils**  : Établissez une stratégie de journalisation et de réponse aux menaces pour détecter et corriger rapidement les menaces tout en répondant aux exigences de conformité. Veillez à ce que les analystes reçoivent des alertes de haute qualité et des expériences homogènes afin qu’ils puissent se concentrer sur les menaces plutôt que sur l’intégration et les étapes manuelles. 

Cette stratégie doit inclure des recommandations, stratégies et normes documentées pour les éléments suivants : 

- Rôle et responsabilités de l’organisation d’opérations de sécurité (SecOP)
- Un processus de réponse aux incidents bien défini, aligné avec NIST ou autre cadre réglementaire du secteur
- Capture et rétention des journaux pour prendre en charge la détection des menaces, la réponse aux incidents et les besoins de conformité
- Visibilité centralisée des menaces et informations de corrélation à leur sujet, avec SIEM, les fonctionnalités Azure natives et d’autres sources : plan de communication et de notification avec vos clients, fournisseurs et parties publiques dignes d’intérêt
- Utilisation de plateformes Azure natives et tierces pour la gestion des incidents, comme la journalisation et la détection des menaces, les investigations et la correction et l’éradication des attaques
- Processus de gestion des incidents et des activités postérieures aux incidents, comme les leçons apprises et la rétention des preuves

Consultez les informations supplémentaires disponibles dans les liens référencés.
- [Benchmark de sécurité Azure - Journalisation et détection des menaces](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Benchmark de sécurité Azure - Réponse aux incidents](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Meilleures pratiques pour la sécurité Azure 4 - Processus. Mise à jour des processus de réponse aux incidents pour le cloud](https://aka.ms/AzSec4)

- [Guide pour le cadre d’adoption d’Azure, la journalisation et la prise de décision pour les rapports](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Supervision d’Azure Security Center**  : actuellement non disponible

**Responsabilité**  : Customer

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
