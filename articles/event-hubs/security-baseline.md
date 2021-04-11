---
title: Base de référence de sécurité Azure pour Event Hubs
description: La base de référence de sécurité pour Event Hubs fournit des instructions et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans Azure Security Benchmark.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 57862a450d313f5d8850b14047ecc3d25d6ba5c1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563682"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Base de référence de sécurité Azure pour Event Hubs

Cette base de référence de sécurité applique les conseils [Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) à Event Hubs. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure.
Le contenu est regroupé par les **contrôles de sécurité** définis par Azure Security Benchmark et les conseils associés applicables à Event Hubs. Les **contrôles** non applicables à Event Hubs ont été exclus.

 
Pour voir comment Event Hubs est entièrement mappé à Azure Security Benchmark, consultez le [fichier de mappage complet de la base de référence de sécurité Event Hubs](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Aide** : l’intégration d’Azure Event Hubs à des points de terminaison de service de réseau virtuel permet de sécuriser l’accès aux fonctionnalités de messagerie à partir de charges de travail, notamment celles de machines virtuelles liées à des réseaux virtuels. Le chemin du trafic réseau est sécurisé aux deux extrémités.

Une fois lié à au moins un point de terminaison de service de sous-réseau de réseau virtuel, l’espace de noms Event Hubs respectif n’accepte que le trafic provenant de sous-réseaux autorisés dans les réseaux virtuels. Du point de vue du réseau virtuel, la liaison de votre espace de noms Event Hubs à un point de terminaison de service configure un tunnel réseau isolé allant du sous-réseau de réseau virtuel au service de messagerie. 

Vous pouvez également créer un point de terminaison privé, c’est-à-dire une interface réseau qui vous connecte de manière privée et sécurisée au service Event Hubs à l’aide du service Azure Private Link. Le point de terminaison privé utilise une adresse IP privée de votre réseau virtuel, plaçant de fait le service dans votre réseau virtuel. Sachant que l’ensemble du trafic à destination du service peut être routé via le point de terminaison privé, il n’y a aucun besoin de passerelles, d’appareils NAT, de connexions ExpressRoute ou VPN ou d’adresses IP publiques. 

Vous pouvez également sécuriser votre espace de noms Azure Event Hubs à l’aide de pare-feu. Event Hubs prend en charge les contrôles d’accès basés sur l’adresse IP pour le pare-feu entrant. Vous pouvez définir des règles de pare-feu à l’aide du portail Azure, de modèles Azure Resource Manager ou par le biais d’Azure CLI ou d’Azure PowerShell.

- [Guide pratique pour utiliser des points de terminaison de service de réseau virtuel avec Azure Event Hubs](event-hubs-service-endpoints.md)

- [Intégrer Azure Event Hubs à Azure Private Link](private-link-service.md)

- [Guide pratique pour configurer des règles de pare-feu IP pour des espaces de noms Azure Event Hubs](event-hubs-ip-filtering.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés.

**Définitions intégrées à Azure Policy – Microsoft.EventHub** :

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des interfaces réseau

**Aide** : Utilisez Azure Security Center et suivez les recommandations de protection du réseau pour sécuriser vos ressources Event Hubs dans Azure. Si vous utilisez des machines virtuelles Azure pour accéder à vos hubs d’événements, activez les journaux d’activité des flux de groupe de sécurité réseau et envoyez les journaux vers un compte de stockage pour l’audit du trafic.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Présentation de la sécurité réseau fournie par Azure Security Center](../security-center/security-center-network-recommendations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Conseils** : Activez le service Protection DDoS Standard sur les réseaux virtuels associés à vos hubs d’événements à des fins de protection contre les attaques par déni de service distribué (DDoS). Utilisez la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center pour refuser les communications avec des adresses IP Internet connues comme étant malveillantes ou inutilisées.

- [Guide pratique pour configurer la protection DDoS](../ddos-protection/manage-ddos-protection.md)

- [Plus d’informations sur la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center](../security-center/azure-defender.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Aide** : si vous utilisez des machines virtuelles Azure pour accéder à vos hubs d’événements, activez les journaux de flux de groupe de sécurité réseau et envoyez les journaux vers un compte de stockage pour auditer le trafic. Vous pouvez également envoyer les journaux des flux de groupe de sécurité réseau à un espace de travail Log Analytics et utiliser Traffic Analytics pour fournir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

Si cela s'avère nécessaire pour analyser une activité anormale, activez la capture de paquets Network Watcher.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Guide pratique pour activer Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions (IDS/IPS) basés sur le réseau

**Aide** : Si vous utilisez des machines virtuelles Azure pour accéder à vos hubs d’événements, sélectionnez une offre de la Place de marché Azure qui prend en charge les fonctionnalités IDS/IPS avec des fonctionnalités d’inspection de charge utile. Si la détection et/ou la prévention des intrusions basées sur l’inspection de la charge utile ne sont pas nécessaires pour votre organisation, vous pouvez utiliser la fonctionnalité de pare-feu Azure intégrée d’Azure Event Hubs. Vous pouvez limiter l’accès à votre espace de noms Event Hubs pour une plage limitée d’adresses IP ou pour une adresse IP spécifique à l’aide de règles de pare-feu.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Guide pratique pour ajouter une règle de pare-feu dans Event Hubs pour une adresse IP spécifiée](event-hubs-ip-filtering.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Aide** : Définissez et implémentez des configurations de sécurité standard pour les ressources réseau associées à vos espaces de noms Azure Event Hubs à l’aide d’Azure Policy. Utilisez des alias Azure Policy dans les espaces de noms **Microsoft.EventHub** et **Microsoft.Network** pour créer des définitions de stratégies personnalisées d’audit ou d’application de la configuration réseau de vos espaces de noms Event Hubs. Vous pouvez également utiliser des définitions de stratégie intégrée en lien avec Azure Event Hubs, par exemple :

- Event Hub doit utiliser un point de terminaison de service de réseau virtuel.

Des informations supplémentaires sont disponibles sur les liens référencés.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Stratégie intégrée Azure pour l’espace de noms Event Hubs](../governance/policy/samples/built-in-policies.md#event-hub)

- [Exemples Azure Policy pour le réseau](../governance/policy/samples/built-in-policies.md#network)

- [Guide pratique pour créer un blueprint Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Aide** : Utilisez des étiquettes pour les réseaux virtuels et autres ressources liées à la sécurité réseau et au flux de trafic qui sont associés à vos hubs d’événements.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Aide** : Utilisez le journal d’activité Azure pour superviser les configurations des ressources réseau et détecter les changements des ressources réseau associées à Azure Event Hubs. Créez des alertes dans Azure Monitor, qui se déclenchent lors de la modification de ressources réseau critiques.

- [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Guide pratique pour créer des alertes dans Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Dans Azure Monitor, configurez les journaux liés aux hubs d’événements dans les paramètres de diagnostic Journal d’activité et Event Hub pour envoyer les journaux vers un espace de travail Log Analytics à des fins d’interrogation ou vers un compte de stockage pour un stockage d’archivage à long terme.

- [Guide pratique pour configurer les paramètres de diagnostic pour Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Présentation du journal d’activité Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Conseils** : Activez les paramètres de diagnostic pour votre espace de noms Azure Event Hubs. Il existe trois catégories de paramètres de diagnostic pour Azure Event Hubs : journaux d’archivage, Journaux des opérations et journaux de mise à l’échelle automatique. Activez les journaux des opérations pour capturer les informations relatives à ce qui se passe pendant les opérations Event Hubs, en particulier le type d’opération tel que la création d’un hub d’événements, les ressources utilisées et l’état de l’opération.

En outre, vous pouvez activer les paramètres de diagnostic des journaux d’activité Azure et les envoyer vers un compte de stockage Azure, un hub d’événements ou un espace de travail Log Analytics. Les journaux d’activité fournissent des insights sur les opérations qui ont été effectuées sur vos ressources Azure Event Hubs et autres. À l’aide des journaux d’activité, vous pouvez déterminer « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur vos espaces de noms Azure Event Hubs.

- [Guide pratique pour activer les paramètres de diagnostic pour Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés.

**Définitions intégrées à Azure Policy – Microsoft.EventHub** :

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Aide** : Dans Azure Monitor, définissez la période de conservation de votre espace de travail Log Analytics en fonction des obligations réglementaires de votre organisation pour capturer et examiner les incidents liés aux hubs d’événement.

- [Définir les paramètres de conservation des journaux pour les espaces de travail Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Aide** : Analysez et supervisez les journaux pour détecter les comportements anormaux et examinez régulièrement les résultats liés à vos hubs d’événements. Utilisez Log Analytics d’Azure Monitor pour examiner les journaux et effectuer des requêtes sur leurs données. Vous pouvez également activer et intégrer des données dans Azure Sentinel ou une solution tierce de gestion des événements et des informations système.

- [En savoir plus sur l’espace de travail Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Guide pratique pour effectuer des requêtes personnalisées dans Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Aide** : Dans Azure Monitor, configurez les journaux liés à Azure Event Hubs dans les paramètres de diagnostic Journal d’activité et Event Hub pour envoyer les journaux vers un espace de travail Log Analytics à des fins d’interrogation ou vers un compte de stockage pour un stockage d’archivage à long terme. Utilisez l’espace de travail Log Analytics afin de créer des alertes pour les activités anormales détectées dans les événements et journaux de sécurité.

Vous pouvez également activer et intégrer les données dans Azure Sentinel. 

- [Comprendre le journal d’activité Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Guide pratique pour configurer les paramètres de diagnostic pour Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Guide pratique pour générer une alerte sur des données de journal de l’espace de travail Log Analytics](../azure-monitor/alerts/tutorial-response.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Aide** : Azure Active Directory (Azure AD) comporte des rôles intégrés qui doivent être explicitement attribués et qui peuvent être interrogés. Utilisez le module Azure AD PowerShell pour effectuer des requêtes ad hoc afin de découvrir les comptes membres de groupes d’administration.

- [Guide pratique pour obtenir un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Guide pratique pour obtenir les membres d’un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Aide** : l’accès au plan de contrôle pour Event Hubs est contrôlé par le biais d’Azure Active Directory (Azure AD). Azure AD n’intègre pas le concept des mots de passe par défaut.

L’accès au plan de données pour Event Hubs est contrôlé par le biais d’Azure AD avec des identités managées ou des inscriptions d’applications ainsi que des signatures d’accès partagé. Les signatures d’accès partagé sont utilisées par les clients qui se connectent à vos hubs d’événements et peuvent être regénérées à tout moment.

- [Comprendre les signatures d’accès partagé pour Event Hubs](authenticate-shared-access-signature.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseils** : Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration.

En outre, pour vous aider à suivre les comptes d’administration dédiés, vous pouvez utiliser des recommandations d’Azure Security Center ou des stratégies Azure intégrées, telles que les suivantes :

- Plusieurs propriétaires doivent être affectés à votre abonnement

- Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement

- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement

Des informations supplémentaires sont disponibles sur les liens référencés.

- [Utilisation d’Azure Security Center pour superviser l’identité et l’accès (préversion)](../security-center/security-center-identity-access.md)

- [Utilisation d’Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Conseils** : Microsoft Azure offre la gestion du contrôle d’accès intégré pour les ressources et les applications basées sur Azure Active Directory (Azure AD). L’un des principaux avantages de l’utilisation d’Azure AD avec Azure Event Hubs est que vous n’avez plus besoin de stocker vos informations d’identification dans le code. Au lieu de cela, vous pouvez demander un jeton d’accès OAuth 2.0 à partir de la Plateforme d’identité Microsoft. Le nom de ressource à utiliser pour demander un jeton est https:\//eventhubs.azure.net/. Azure AD authentifie le principal de sécurité (un utilisateur, un groupe ou un principal de service) qui exécute l’application. Si l’authentification réussit, Azure AD retourne un jeton d’accès à l’application et l’application peut ensuite l’utiliser pour autoriser les demandes vers les ressources Azure Event Hubs.

- [Guide pratique pour authentifier une application avec Azure AD pour accéder aux ressources Event Hubs](authenticate-application.md)

- [Présentation de l’authentification unique avec Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Aide** : activez l’authentification multifacteur Azure Active Directory (Azure AD) et suivez les recommandations Azure Security Center sur la gestion des identités et des accès pour protéger vos ressources Event Hub.

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Aide** : utilisez des stations de travail disposant d’un accès privilégié avec l’authentification multifacteur configurée pour se connecter aux ressources Event Hub et les configurer.

- [En savoir plus sur les stations de travail à accès privilégié](/security/compass/privileged-access-devices)

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration

**Aide** : utilisez Azure Active Directory (Azure AD) Privileged Identity Management pour générer des journaux et des alertes quand des activités suspectes ou potentiellement dangereuses se produisent dans l’environnement. Utilisez les détections de risque Azure AD pour visualiser les alertes et des rapports sur les comportements à risque des utilisateurs. Pour une journalisation complémentaire, envoyez les alertes de détection des risques d’Azure Security Center à Azure Monitor et configurez des alertes/notifications personnalisées à l’aide de groupes d’actions.

- [Présentation des détections de risques Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Configuration des groupes d’actions pour générer des alertes et des notifications personnalisées](../azure-monitor/alerts/action-groups.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

**Aide** : Utilisez des emplacements nommés à accès conditionnel pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.

- [Guide pratique pour configurer des emplacements nommés dans Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : utilisez Azure Active Directory (Azure AD) comme système central d’authentification et d’autorisation pour des ressources Azure telles qu’Event Hubs. Ainsi, le mécanisme de contrôle d’accès en fonction du rôle Azure (Azure RBAC) est applicable aux ressources administratives sensibles.

- [Guide pratique pour créer et configurer une instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autoriser l’accès aux ressources Event Hubs à l’aide d’Azure AD](authorize-access-azure-active-directory.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Azure Active Directory (Azure AD) fournit des journaux pour vous aider à découvrir les comptes obsolètes. De plus, utilisez les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seules les utilisateurs appropriés continuent de bénéficier d’un accès.

En outre, effectuez une rotation régulière de vos signatures d’accès partagé Event Hubs.

- [Présentation des rapports Azure AD](../active-directory/reports-monitoring/index.yml)

- [Comment utiliser les révisions d’accès des identités Azure](../active-directory/governance/access-reviews-overview.md)

- [Comprendre les signatures d’accès partagé pour Event Hubs](authenticate-shared-access-signature.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Aide** : vous avez accès aux sources de journaux sur l’activité de connexion, l’audit et les événements à risque d’Azure Active Directory (Azure AD), ce qui vous permet d’effectuer l’intégration à n’importe quelle solution de gestion des événements et des informations système ou n’importe quel outil de supervision.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure AD et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Vous pouvez configurer les alertes de journal souhaitées dans un espace de travail Log Analytics.

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Autoriser l’accès aux ressources Event Hubs à l’aide d’Azure AD](authorize-access-azure-active-directory.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Aide** : utilisez les fonctionnalités de protection des identités et de détection des risques dans Azure Active Directory (Azure AD) pour configurer des réponses automatiques aux actions suspectes détectées en lien avec vos ressources Event Hubs. Vous devez activer des réponses automatisées via Azure Sentinel pour implémenter les réponses de sécurité de votre organisation.

- [Guide pratique pour afficher les connexions risquées Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

**Conseils** : Non disponible ; Customer Lockbox n’est pas encore pris en charge pour Event Hubs.

- [Liste des services pris en charge pour Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Aide** : Utilisez des étiquettes sur des ressources associées à vos hubs d’événements pour faciliter le suivi des ressources Azure qui stockent ou traitent des informations sensibles.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Aide** : implémentez des abonnements ou des groupes d’administration distincts pour le développement, les tests et la production. Les espaces de noms Event Hubs doivent être séparés par un réseau virtuel avec des points de terminaison de service activés et correctement étiquetés.

Vous pouvez également sécuriser votre espace de noms Azure Event Hubs à l’aide de pare-feu. Azure Event Hubs prend en charge les contrôles d’accès basés sur IP pour le pare-feu entrant. Vous pouvez définir des règles de pare-feu à l’aide du portail Azure, de modèles Azure Resource Manager ou par le biais d’Azure CLI ou d’Azure PowerShell.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Configurer des règles de pare-feu IP pour des espaces de noms Azure Event Hubs](event-hubs-ip-filtering.md)

- [Créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

- [Guide pratique pour créer un réseau virtuel](../virtual-network/quick-create-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

**Aide** : Quand vous utilisez des machines virtuelles pour accéder à vos hubs d’événements, utilisez des réseaux virtuels, des points de terminaison de service, un pare-feu Event Hubs, des groupes de sécurité réseau et des étiquettes de service pour limiter les risques d’exfiltration de données.

Microsoft gère l’infrastructure sous-jacente d’Azure Event Hubs et a implémenté des contrôles stricts pour empêcher la perte ou l’exposition de données client.

- [Configurer des règles de pare-feu IP pour des espaces de noms Azure Event Hubs](event-hubs-ip-filtering.md)

- [Comprendre les points de terminaison de service de réseau virtuel avec Azure Event Hubs](event-hubs-service-endpoints.md)

- [Intégrer Azure Event Hubs à Azure Private Link](private-link-service.md)

- [Comprendre les groupes de sécurité réseau et les étiquettes de service](../virtual-network/network-security-groups-overview.md)

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Conseils** : Les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour Azure Event Hubs. Implémentez une solution tierce si nécessaire à des fins de conformité.

Pour la plateforme sous-jacente managée par Microsoft, Microsoft considère tout le contenu client comme sensible et met tout en œuvre pour empêcher la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6 : Utiliser le contrôle d’accès en fonction du rôle pour contrôler l’accès aux ressources

**Aide** : Azure Event Hubs prend en charge l’utilisation d’Azure Active Directory (Azure AD) pour autoriser les requêtes de ressources Event Hubs. Azure AD vous permet d’utiliser le contrôle d’accès en fonction du rôle (Azure RBAC) pour accorder des autorisations à un principal de sécurité, qui peut être un utilisateur ou un principal de service d’application.

- [Comprendre les rôles Azure RBAC et les rôles disponibles pour Azure Event Hubs](authorize-access-azure-active-directory.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Aide** : Azure Event Hubs prend en charge le chiffrement des données au repos à l’aide de clés gérées par Microsoft ou de clés gérées par le client. Cette fonctionnalité vous permet de créer, de faire pivoter, de désactiver et de révoquer l'accès aux clés gérées par le client qui sont utilisées pour chiffrer les données Azure Event Hubs au repos.

- [Guide pratique pour configurer les clés managées par le client pour le chiffrement des données Azure Event Hubs](configure-customer-managed-key.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Utilisez Azure Monitor avec le journal d’activité Azure pour créer des alertes en cas de modifications sur des instances de production Azure Event Hubs et autres ressources critiques ou associées.

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Aide** : utilisez Azure Resource Graph pour interroger et découvrir toutes les ressources (y compris les espaces de noms Azure Event Hubs) au sein de vos abonnements. Vérifiez que vous disposez des autorisations (en lecture) appropriées dans votre locataire et pouvez répertorier tous les abonnements Azure ainsi que les ressources qu’ils contiennent.

- [Guide pratique pour créer des requêtes avec Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Guide pratique pour afficher ses abonnements Azure](/powershell/module/az.accounts/get-azsubscription)

- [Présentation d’Azure RBAC](../role-based-access-control/overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Conseils** : Appliquez des balises aux ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Conseils** : Utilisez des étiquettes, des groupes d’administration, voire des abonnements séparés, pour organiser et suivre les espaces de noms Azure Event Hubs et les ressources associées. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Conseils** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans les abonnements clients selon les définitions de stratégies intégrées suivantes :

- Types de ressources non autorisés

- Types de ressources autorisés

Utilisez également Azure Resource Graph pour interroger/découvrir des ressources dans les abonnements.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour créer des requêtes avec Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Conseils** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans les abonnements clients selon les définitions de stratégies intégrées suivantes :

- Types de ressources non autorisés

- Types de ressources autorisés

Des informations supplémentaires sont disponibles sur les liens référencés.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Aide** : Configurez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ».

- [Configuration de l’accès conditionnel pour bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Aide** : Définissez et implémentez des configurations de sécurité standard pour vos déploiements Azure Event Hubs. Utilisez des alias Azure Policy dans l’espace de noms **Microsoft.EventHub** pour créer des stratégies personnalisées d’audit ou d’application de configurations. Vous pouvez également utiliser des définitions de stratégie intégrée pour Azure Event Hubs, par exemple :

- Les journaux de diagnostic dans Event Hub doivent être activés.

- Event Hub doit utiliser un point de terminaison de service de réseau virtuel

Des informations supplémentaires sont disponibles sur les liens référencés.

- [Stratégie intégrée Azure pour l’espace de noms Event Hubs](../governance/policy/samples/built-in-policies.md#event-hub)

- [Affichage des alias Azure Policy disponibles](/powershell/module/az.resources/get-azpolicyalias)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Aide** : utilisez les effets Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Event Hubs. 

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

 
- [Pour plus d’informations sur les effets d’Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Aide** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.EventHub » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Aide** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.EventHub » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. Utilisez les effets Azure Policy [auditer], [refuser] et [déployer s’il n’existe pas] afin d’appliquer automatiquement des configurations pour vos déploiements Azure Event Hubs et les ressources associées.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Aide** : Pour les machines virtuelles Azure ou les applications web s’exécutant sur Azure App Service utilisées pour accéder à vos hubs d’événements, utilisez Managed Service Identity conjointement avec Azure Key Vault afin de simplifier et sécuriser la gestion des signatures d’accès partagé pour vos déploiements Azure Event Hubs. Assurez-vous que Key Vault est configuré avec la suppression réversible activée.

- [Authentifier une identité managée avec Azure Active Directory (Azure AD) pour accéder aux ressources Event Hubs](./authenticate-managed-identity.md?tabs=latest)

- [Configurer des clés managées par le client pour Event Hubs](configure-customer-managed-key.md)

- [Intégration aux identités managées Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Créer un coffre de clés](../key-vault/secrets/quick-create-portal.md)

- [Fournir une authentification Key Vault avec une identité managée](../key-vault/general/assign-access-policy-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Aide** : pour les machines virtuelles Azure ou les applications web s’exécutant sur Azure App Service utilisées pour accéder à vos hubs d’événements, utilisez Managed Service Identity conjointement avec Azure Key Vault pour simplifier et sécuriser Azure Event Hubs. Assurez-vous que Key Vault est configuré avec la suppression réversible activée.

Utilisez des identités managées pour fournir aux services Azure une identité gérée automatiquement dans Azure Active Directory (Azure AD). Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Azure Key Vault, sans informations d’identification dans votre code.

- [Authentifier une identité managée avec Azure AD pour accéder aux ressources Event Hubs](./authenticate-managed-identity.md?tabs=latest)

- [Configurer des clés managées par le client pour Event Hubs](configure-customer-managed-key.md)

- [Configurer des identités managées](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Intégration aux identités managées Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault.

- [Configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Défense contre les programmes malveillants](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Aide** : Pré-analysez tout contenu chargé sur des ressources Azure non liées au calcul, comme Azure Event Hubs, App Service, Data Lake Storage, Stockage Blob, Azure Database pour PostgreSQL, etc. Microsoft ne peut pas accéder à vos données dans ces instances.

Microsoft Antimalware est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple, Azure Cache pour Redis), mais il ne s’exécute pas sur du contenu client.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Garantir des sauvegardes automatiques régulières

**Aide** : Configurez la géo-reprise d’activité après sinistre pour Azure Event Hubs. Si tout un centre de données ou une région Azure complète (si aucune zone de disponibilité n’est utilisée) connaît un temps d’arrêt, il est essentiel que le traitement des données puisse continuer dans les autres régions ou centres de données. Pour cette raison, la géo-reprise d’activité après sinistre et la géoréplication sont des fonctionnalités importantes pour les entreprises. Azure Event Hubs prend en charge la géorécupération d’urgence et la géoréplication au niveau de l’espace de noms. 

- [Comprendre la géo-reprise d’activité après sinistre pour Azure Event Hubs](./event-hubs-geo-dr.md#availability-zones)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés managées par le client

**Aide** : Azure Event Hubs fournit une fonctionnalité de chiffrement des données au repos avec Azure Storage Service Encryption (Azure SSE). Event Hubs utilise le service Stockage Azure pour stocker les données. Par défaut, toutes les données stockées avec ce service sont chiffrées à l'aide de clés gérées par Microsoft. Si vous utilisez Azure Key Vault pour stocker des clés gérées par le client, veillez à effectuer des sauvegardes automatisées régulières de vos clés.

Veillez à effectuer des sauvegardes automatisées régulières de vos secrets Key Vault avec la commande PowerShell suivante : Backup-AzKeyVaultSecret

- [Guide pratique pour configurer les clés managées par le client pour le chiffrement des données Azure Event Hubs au repos](configure-customer-managed-key.md)

- [Comment sauvegarder des secrets Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés managées par le client

**Aide** : Testez la restauration des clés gérées par le client sauvegardées.

- [Guide pratique pour restaurer des clés de coffre de clés dans Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés managées par le client

**Aide** : activez la suppression réversible dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante. Azure Event Hubs nécessite que les fonctionnalités Suppression réversible et Ne pas vider soient configurées pour les clés managées par le client.

Configurez la suppression réversible pour le compte Stockage Azure servant à capturer les données Event Hubs. Notez que cette fonctionnalité n’est pas encore prise en charge pour Azure Data Lake Storage Gen 2.

- [Guide pratique pour activer la suppression réversible dans Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Configurer un coffre de clés avec des clés](configure-customer-managed-key.md)

- [Suppression réversible pour les objets blob de Stockage Azure](//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Vérifiez qu’il existe des plans de réponse aux incidents écrits qui définissent les rôles du personnel ainsi que les phases de gestion des incidents.

- [Comment configurer des automatisations de workflow dans Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Conseils** : Security Center affecte un degré de gravité aux alertes pour vous aider à hiérarchiser l’ordre dans lequel vous remédiez à chaque alerte. Ainsi, quand une ressource est compromise, vous pouvez vous en occuper immédiatement. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou à l’analytique utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Aide** : Exécutez des exercices pour tester les fonctionnalités de réponse aux incidents de vos systèmes de façon régulière. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

- [Reportez-vous à la publication du NIST : Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide de test, d’entraînement et d’utilisation des programmes destinés aux plans et fonctionnalités informatiques)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 : Fournir des informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

**Conseils** : Les informations de contact d’incident de sécurité seront utilisées par Microsoft pour vous contacter si Microsoft Security Response Center (MSRC) découvre que les données du client ont été utilisées par un tiers illégal ou non autorisé.  Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus. 

- [Comment définir le contact de sécurité d’Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Conseils** : Exportez vos alertes et recommandations d’Azure Security Center à l’aide de la fonctionnalité d’exportation continue. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser en continu les alertes vers Azure Sentinel.

- [Comment configurer l’exportation continue](../security-center/continuous-export.md)

- [Comment envoyer des alertes à Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Conseils** : Utilisez la fonctionnalité d’automatisation du workflow dans Azure Security Center pour déclencher automatiquement des réponses via « Logic Apps » sur les alertes et recommandations de sécurité.

- [Comment configurer l’automatisation des workflows et Logic Apps](../security-center/workflow-automation.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : tests d’intrusion et exercices Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1 : Procéder régulièrement à des tests d’intrusion des ressources Azure et veiller à corriger tous les problèmes de sécurité critiques détectés

**Aide** : Suivez les règles d’engagement de pénétration du cloud Microsoft pour vous assurer que vos tests d’intrusion sont conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft. 

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](../security/benchmarks/overview.md)
- En savoir plus sur les [bases de référence de la sécurité Azure](../security/benchmarks/security-baselines-overview.md)
