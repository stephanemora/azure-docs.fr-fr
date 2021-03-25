---
title: Base de référence de sécurité Azure pour Azure Cosmos DB
description: La base de référence de sécurité Azure Cosmos DB fournit des instructions et des ressources pour l’implémentation des recommandations de sécurité spécifiées dans le benchmark de sécurité Azure.
author: msmbaldwin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5f9cf6dba0a1e670e3a27cb3546de9237466e7ce
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659424"
---
# <a name="azure-security-baseline-for-azure-cosmos-db"></a>Base de référence de sécurité Azure pour Azure Cosmos DB

Cette base de référence de sécurité applique les instructions du [benchmark de sécurité Azure version 1.0](../security/benchmarks/overview-v1.md) à Azure Cosmos DB. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure.
Le contenu est regroupé selon les **contrôles de sécurité** définis par le benchmark de sécurité Azure et les instructions associées applicables à Azure Cosmos DB. Les **contrôles** non applicables à Azure Cosmos DB ont été exclus.

 
Pour voir comment s’effectue le mappage intégral d’Azure Cosmos DB au benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité Azure Cosmos DB](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Conseils** : À l’aide d’Azure Private Link, vous pouvez vous connecter à un compte Azure Cosmos via un point de terminaison privé. Le trafic entre votre réseau virtuel et le service transite par le réseau principal de Microsoft, éliminant ainsi toute exposition à l’Internet public. Vous pouvez également réduire le risque d’exfiltration de données en configurant un ensemble strict de règles de trafic sortant sur un groupe de sécurité réseau (NSG) et en associant ce groupe de sécurité réseau à votre sous-réseau.

Vous pouvez également utiliser des points de terminaison de service pour sécuriser votre compte Azure Cosmos. En activant un point de terminaison de service, vous pouvez configurer des comptes Azure Cosmos pour autoriser l’accès uniquement à partir d’un sous-réseau spécifique de réseau virtuel Azure. Une fois le point de terminaison de service Azure Cosmos DB activé, vous pouvez limiter l’accès à un compte Azure Cosmos avec des connexions à partir d’un sous-réseau dans un réseau virtuel.

Vous pouvez également sécuriser les données stockées dans votre compte Azure Cosmos à l’aide de pare-feu IP. Azure Cosmos DB prend en charge les contrôles d’accès basés sur IP pour le pare-feu entrant. Vous pouvez définir un pare-feu IP sur le compte Azure Cosmos à l’aide du Portail Azure, de modèles Azure Resource Manager ou par le biais d’Azure CLI ou d’Azure PowerShell.

- [Vue d’ensemble d’Azure Private Link](../private-link/private-link-overview.md)

- [Guide pratique pour configurer un point de terminaison privé pour Azure Cosmos DB](how-to-configure-private-endpoints.md) 

- [Guide pratique pour créer un groupe de sécurité réseau avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

- [Guide pratique pour configurer le pare-feu IP dans Cosmos DB](how-to-configure-firewall.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : le [benchmark de sécurité Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) pour les services associés.

**Définitions intégrées à Azure Policy - Microsoft.DocumentDB** :

[!INCLUDE [Resource Policy for Microsoft.DocumentDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.documentdb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des interfaces réseau

**Aide** : Utilisez Azure Security Center et suivez les recommandations de protection du réseau pour sécuriser les ressources réseau associées à votre compte Azure Cosmos.

Lorsque des machines virtuelles sont déployées dans le même réseau virtuel que votre compte Azure Cosmos, vous pouvez utiliser un groupe de sécurité réseau (NSG) pour réduire le risque d’exfiltration de données. Activez les journaux de flux NSG et transférez-les vers un compte de stockage Azure pour les audits du trafic. Vous pouvez aussi envoyer ces journaux vers un espace de travail Log Analytics et utiliser Traffic Analytics pour fournir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

- [Présentation de la sécurité réseau assurée par Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="13-protect-critical-web-applications"></a>1.3 : Protéger les applications web critiques

**Aide** : Utilisez la fonctionnalité CORS (Cross Origin Resource Sharing) pour permettre à une application web exécutée dans un domaine d’accéder aux ressources d’un autre domaine. Les navigateurs web implémentent une restriction de sécurité, appelée stratégie de même origine, qui empêche une page web d’appeler des API dans un domaine différent. Toutefois, CORS permet au domaine d’origine d’appeler des API dans un autre domaine de façon sécurisée. Après avoir activé la prise en charge CORS pour votre compte Azure Cosmos, seules les requêtes authentifiées sont évaluées pour déterminer si elles sont autorisées selon les règles que vous avez spécifiées.

- [Configurer le partage des ressources cross-origin (CORS)](how-to-configure-cross-origin-resource-sharing.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Conseils** : Utilisez ATP (Advanced Threat Protection) pour Azure Cosmos DB. ATP pour Azure Cosmos DB offre une couche supplémentaire de sécurité intelligente qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes Azure Cosmos. Cette couche de protection vous permet de traiter efficacement les menaces et de les intégrer aux systèmes centraux de supervision de la sécurité.

Activez la protection DDoS standard sur les réseaux virtuels associés à vos instances Azure Cosmos DB pour vous protéger contre les attaques DDoS. Utilisez la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center pour refuser les communications avec des adresses IP Internet connues comme étant malveillantes ou inutilisées.

- [Guide pratique pour configurer Advanced Threat Protection pour Azure Cosmos DB](cosmos-db-advanced-threat-protection.md)

- [Guide pratique pour configurer la protection DDoS](/azure/virtual-network/manage-ddos-protection)

- [Présentation de la fonctionnalité Threat Intelligence intégrée à Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Aide** : Activez les journaux de flux de groupe de sécurité réseau (NSG) et envoyez les journaux dans un compte de stockage pour auditer le trafic. Vous pouvez envoyer ces journaux vers un espace de travail Log Analytics et utiliser Traffic Analytics pour fournir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions (IDS/IPS) basés sur le réseau

**Conseils** : Utilisez ATP (Advanced Threat Protection) pour Azure Cosmos DB. ATP pour Azure Cosmos DB offre une couche supplémentaire de sécurité intelligente qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes Azure Cosmos. Cette couche de protection vous permet de traiter efficacement les menaces et de les intégrer aux systèmes centraux de supervision de la sécurité. 

- [Guide pratique pour configurer Advanced Threat Protection pour Cosmos DB](cosmos-db-advanced-threat-protection.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Aide** : Pour les ressources qui doivent accéder à votre compte Azure Cosmos, utilisez des étiquettes de service de réseau virtuel afin de définir des contrôles d’accès réseau sur des groupes de sécurité réseau ou le Pare-feu Azure. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de l’étiquette de service (par exemple, AzureCosmosDB) dans le champ Source ou Destination approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

- [Pour plus d’informations sur l’utilisation des étiquettes de service](../virtual-network/service-tags-overview.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Aide** : Définissez et implémentez des configurations de sécurité standard pour les ressources réseau à l'aide d'Azure Policy. Utilisez des alias Azure Policy dans les espaces de noms « Microsoft.DocumentDB » et « Microsoft.Network » afin de créer des stratégies personnalisées pour auditer ou appliquer la configuration réseau de vos instances Azure Cosmos DB. Vous pouvez également utiliser des définitions de stratégie intégrée pour Azure Cosmos DB, par exemple :

- Déployer Advanced Threat Protection pour les comptes Cosmos DB

- Cosmos DB doit utiliser un point de terminaison de service de réseau virtuel

Vous pouvez également utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant les artefacts d’environnement clés, tels que les modèles Resource Manager, le contrôle d’accès en fonction du rôle Azure (Azure RBAC) et les stratégies au sein d’une seule définition de blueprint. Vous pouvez facilement appliquer le blueprint aux nouveaux abonnements et environnements, et ajuster le contrôle et la gestion par le biais du contrôle de version.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour créer un blueprint Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Aide** : Utilisez des balises pour les ressources réseau associées à votre déploiement Azure Cosmos DB afin de les organiser logiquement dans une taxonomie.

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Aide** : Utilisez le journal d’activité Azure pour surveiller les configurations des ressources réseau et détecter les modifications de celles-ci associées à vos instances Azure Cosmos DB. Créez des alertes dans Azure Monitor, qui se déclenchent lors de la modification de ressources réseau critiques. 

- [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](/azure/azure-monitor/platform/activity-log-view)

- [Guide pratique pour créer des alertes dans Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Ingérez les journaux via Azure Monitor pour agréger les données de sécurité générées par Azure Cosmos DB. Dans Azure Monitor, utilisez des espaces de travail Log Analytics pour interroger et effectuer l’analytique, et utilisez des comptes de stockage pour le stockage à long terme/d’archivage. Vous pouvez également intégrer des données dans Azure Sentinel ou une solution SIEM (Security Information and Event Management) de tiers. 

- [Guide pratique pour activer les journaux de diagnostic pour Azure Cosmos DB](/azure/cosmos-db/logging)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Conseils** : Activez les paramètres de diagnostic pour Azure Cosmos DB et envoyez les journaux dans un espace de travail Log Analytics ou un compte de stockage Azure. Les paramètres de diagnostic dans Azure Cosmos DB sont utilisés pour collecter les journaux des ressources. Ces journaux sont capturés par requête et sont également appelés **journaux de plans de données**. Les opérations Delete, Insert et Read sont des exemples d’opérations de plan de données. 

Vous pouvez aussi activer les paramètres de diagnostic du journal d’activité d’Azure et envoyer ces journaux au même espace de travail Log Analytics que celui utilisé pour les journaux d’Azure Cosmos DB.

- [Guide pratique pour activer les paramètres de diagnostic pour Azure Cosmos DB](/azure/cosmos-db/logging)

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Conseils** : Dans Azure Monitor, définissez la période de rétention des journaux pour les espaces de travail Log Analytics associés à vos instances Azure Cosmos DB conformément aux réglementations de conformité de votre organisation.

- [Guide pratique pour définir les paramètres de conservation des journaux](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Aide** : Vous pouvez exécuter des requêtes dans Log Analytics, un espace de travail permet de rechercher des termes, d’identifier des tendances, d’analyser des modèles et de fournir de nombreuses autres informations basées sur les journaux de Azure Cosmos DB que vous avez collectés.

- [Guide pratique pour exécuter des requêtes pour Azure Cosmos DB dans des espaces de travail Log Analytics](monitor-cosmos-db.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Aide** : Dans Azure Security Center, activez Advanced Threat Protection pour Azure Cosmos DB afin de surveiller les activités anormales dans les journaux de sécurité et les événements. Activez les paramètres de diagnostic dans Azure Cosmos DB et envoyez les journaux à un espace de travail Log Analytics.

 

Vous pouvez également intégrer votre espace de travail Log Analytics à Azure Sentinel, car cela fournit une solution SOAR (Security Orchestration Automated Response). Cela permet de créer des playbooks (solutions automatisées) utilisables pour corriger des problèmes de sécurité. En outre, vous pouvez créer des alertes de journal personnalisées dans votre espace de travail Log Analytics à l’aide d’Azure Monitor.

- [Liste des alertes de protection contre les menaces pour Azure Cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Aide** : Vous pouvez utiliser le volet de gestion des identités et des accès (IAM) dans le Portail Azure pour configurer le contrôle d’accès en fonction du rôle (RBAC) et conserver l’inventaire sur les ressources d’Azure Cosmos DB. Les rôles sont appliqués aux utilisateurs, aux groupes, aux principaux de service et aux identités managées dans Azure Active Directory (Azure AD). Vous pouvez utiliser des rôles intégrés ou des rôles personnalisés pour les individus et les groupes.

Azure Cosmos DB intègre Azure RBAC pour les scénarios de gestion courants dans Azure Cosmos DB. Une personne ayant d’un profil dans Azure AD peut affecter ces rôles Azure à des utilisateurs, des groupes, des principaux de service ou des identités managées pour accorder ou refuser l’accès à des ressources et des opérations sur les ressources d’Azure Cosmos DB.

Vous pouvez également utiliser le module Azure AD PowerShell pour effectuer des requêtes ad hoc afin de découvrir les comptes qui sont membres de groupes d’administration.

De plus, certaines actions dans Azure Cosmos DB peuvent être contrôlées avec Azure AD et des clés principales spécifiques au compte. Utilisez le paramètre de compte « disableKeyBasedMetadataWriteAccess » pour contrôler l’accès aux clés.

- [Comprendre le contrôle d’accès en fonction du rôle (RBAC) dans Azure Cosmos DB](role-based-access-control.md)

- [Créer vos propres rôles personnalisés en utilisant des actions Azure Cosmos DB (espace de noms Microsoft.DocumentDB)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb)

- [Créer un rôle dans Azure AD](../role-based-access-control/custom-roles.md)

- [Guide pratique pour obtenir un rôle d’annuaire dans Azure AD avec PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Guide pratique pour obtenir les membres d’un rôle d’annuaire dans Azure AD avec PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Restreindre l’accès des utilisateurs aux opérations de données uniquement](how-to-restrict-user-data.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Conseils** : Le concept de mots de passe par défaut ou vides n’existe pas pour ce qui concerne Azure Active Directory (Azure AD) ou Azure Cosmos DB. À la place, Azure Cosmos DB utilise deux types de clés pour authentifier les utilisateurs et permettre d’accéder à ses données et à ses ressources : les clés principales et les jetons de ressources. Les clés peuvent être régénérées à tout moment.

- [Comprendre l’accès sécurisé aux données dans Azure Cosmos DB](secure-access-to-data.md)

- [Guide pratique pour regénérer des clés d’Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys)

- [Guide pratique pour accéder programmatiquement aux clés en utilisant Azure AD](certificate-based-authentication.md)

**Responsabilité** : Partagé

**Supervision d’Azure Security Center** : Aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseils** : Non applicable ; Azure Cosmos DB ne prend pas en charge les comptes d’administrateur. Tous les accès sont intégrés à Azure Active Directory (Azure AD) et au contrôle d’accès en fonction du rôle Azure (Azure RBAC).

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Aide** : Azure Cosmos DB utilise deux types de clés pour autoriser les utilisateurs et ne prend pas en charge l’authentification unique (SSO) au niveau du plan de données. L’accès au plan de contrôle pour Cosmos DB est disponible via l’API REST et prend en charge l’authentification unique. Pour vous authentifier, définissez l’en-tête d’autorisation pour vos demandes sur un jeton web JSON que vous obtenez auprès d’Azure Active Directory (Azure AD).

- [Comprendre l’API REST d’Azure Database for Cosmos DB](/rest/api/cosmos-db/)

- [Présentation de l’authentification SSO avec Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseils** : Activez l’authentification multifacteur Azure Active Directory (Azure AD), puis suivez les recommandations de gestion des identités et des accès d’Azure Security Center.

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Conseils** : Utilisez des stations de travail disposant d’un accès privilégié avec l’authentification multifacteur configurée pour vous connecter aux ressources Azure et les configurer. 

- [En savoir plus sur les stations de travail à accès privilégié](/security/compass/privileged-access-devices)
 
- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration

**Conseils** : Utilisez ATP (Advanced Threat Protection) pour Azure Cosmos DB. ATP pour Azure Cosmos DB offre une couche supplémentaire de sécurité intelligente qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes Azure Cosmos. Cette couche de protection vous permet de traiter efficacement les menaces et de les intégrer aux systèmes centraux de supervision de la sécurité.

En outre, vous pouvez utiliser Azure Active Directory (Azure AD) Privileged Identity Management pour générer des journaux et des alertes quand des activités suspectes ou potentiellement dangereuses se produisent dans l’environnement.

Utilisez les détections de risque Azure AD pour visualiser les alertes et des rapports sur les comportements à risque des utilisateurs.

- [Déploiement de Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Présentation des détections de risques Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

**Conseils** : Configurez la condition d’emplacement d’une stratégie d’accès conditionnel et gérez vos emplacements nommés. Avec les emplacements nommés, vous pouvez créer des regroupements logiques de plages d’adresses IP ou de pays et régions. Vous pouvez restreindre l’accès à des ressources sensibles, telles que vos instances Azure Cosmos DB, à vos emplacements nommés configurés.

- [Guide pratique pour configurer des emplacements nommés dans Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Utiliser Azure Active Directory (Azure AD) comme système d’authentification et d’autorisation central. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

- [Création et configuration d’une instance Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Guide pratique pour configurer et gérer l’authentification Azure AD avec Azure SQL](/azure/sql-database/sql-database-aad-authentication-configure)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Azure Active Directory (Azure AD) fournit des journaux pour vous aider à découvrir les comptes obsolètes. De plus, vous pouvez utiliser les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès de l’utilisateur peut être passé en revue régulièrement pour vérifier que seuls les utilisateurs appropriés bénéficient d’un accès permanent.

- [Comment utiliser les révisions d’accès des identités Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Conseils** : Vous pouvez créer des paramètres de diagnostic pour des comptes d’utilisateur Azure Active Directory (Azure AD), en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics où vous pouvez configurer les alertes souhaitées.

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Conseils** : Utilisez ATP (Advanced Threat Protection) pour Azure Cosmos DB. ATP pour Azure Cosmos DB offre une couche supplémentaire de sécurité intelligente qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes Azure Cosmos. Cette couche de protection vous permet de traiter efficacement les menaces et de les intégrer aux systèmes centraux de supervision de la sécurité.

Vous pouvez aussi utiliser Azure Active Directory (Azure AD) Identity Protection et la fonctionnalité de détections de risques pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités utilisateur. De plus, vous pouvez ingérer des journaux dans Azure Sentinel pour approfondir votre examen.

- [Guide pratique pour afficher les connexions risquées Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Conseils** : Utilisez des étiquettes pour faciliter le suivi des instances Azure Cosmos DB qui stockent ou traitent des informations sensibles.

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Les instances Azure Cosmos DB sont séparées par un réseau virtuel/sous-réseau, étiquetés de manière appropriée et sécurisés dans un groupe de sécurité réseau (NSG) ou un Pare-feu Azure. Les instances Azure Cosmos DB stockant des données sensibles doivent être isolées. À l’aide d’Azure Private Link, vous pouvez vous connecter à un compte d’instance Azure Cosmos DB via un point de terminaison privé. Le point de terminaison privé est un ensemble d’adresses IP privées dans un sous-réseau au sein de votre réseau virtuel. Vous pouvez ensuite limiter l’accès aux adresses IP privées sélectionnées. 

- [Guide pratique pour créer des abonnements Azure supplémentaires](/azure/billing/billing-create-subscription)

- [Guide pratique pour créer des groupes d’administration](/azure/governance/management-groups/create)

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

- [Guide pratique pour configurer un point de terminaison privé pour Azure Cosmos DB](how-to-configure-private-endpoints.md)

- [Guide pratique pour créer un groupe de sécurité réseau avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

**Conseils** : Vous pouvez déployer Advanced Threat Protection pour Azure Cosmos DB, qui va détecter les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles ou potentiellement dangereuses de bases de données. Il peut actuellement déclencher les alertes suivantes :

- Accès à partir d’emplacements inhabituels

- Extraction de données inhabituelles

En outre, lorsque vous utilisez des machines virtuelles pour accéder à vos instances Azure Cosmos DB, utilisez un lien privé, un pare-feu, des groupes de sécurité réseau et des étiquettes de service pour limiter les risques d’exfiltration de données. Microsoft gère l’infrastructure sous-jacente d’Azure Cosmos DB et a implémenté des contrôles stricts pour empêcher la perte ou l’exposition de données client.

- [Guide pratique pour configurer Advanced Threat Protection pour Cosmos DB](cosmos-db-advanced-threat-protection.md)

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision d’Azure Security Center** : Aucune

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Aide** : Les fonctionnalités d’identification automatique des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour Azure Cosmos DB. Toutefois, vous pouvez utiliser l’intégration de la Recherche cognitive Azure pour la classification et l’analyse des données. Vous pouvez également implémenter une solution tierce si nécessaire à des fins de conformité.

Pour la plateforme sous-jacente qui est gérée par Microsoft, Microsoft traite tout le contenu client comme sensible et déploie d'importants efforts pour vous protéger contre la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Indexer les données Azure Cosmos DB avec Recherche cognitive Azure](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp;bc=/azure/cosmos-db/breadcrumb/toc.json)

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision d’Azure Security Center** : Aucune

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6 : Utiliser le contrôle d’accès en fonction du rôle pour contrôler l’accès aux ressources

**Conseils** : Azure Cosmos DB fournit un contrôle d’accès en fonction du rôle Azure (Azure RBAC) intégré pour les scénarios de gestion courants dans Azure Cosmos DB. Une personne ayant un profil dans Azure Active Directory (Azure AD) peut affecter ces rôles Azure à des utilisateurs, des groupes, des principaux de service ou des identités managées pour accorder ou refuser l’accès aux ressources et aux opérations sur les ressources Azure Cosmos DB. L’attribution des rôles est limitée au seul accès au plan de contrôle, ce qui inclut l’accès aux comptes, bases de données, conteneurs et offres (débit) d’Azure Cosmos.

- [Guide pratique pour implémenter Azure RBAC dans Azure Cosmos DB](role-based-access-control.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Conseils** : Toutes les données utilisateur stockées dans Cosmos DB sont chiffrées au repos par défaut. Il n’y a aucun contrôle pour le désactiver. Azure Cosmos DB utilise le chiffrement AES-256 dans toutes les régions où le compte est exécuté.

Par défaut, Microsoft gère les clés utilisées pour chiffrer les données dans votre compte Azure Cosmos. Vous pouvez éventuellement choisir d’ajouter une deuxième couche de chiffrement avec vos propres clés.

- [Comprendre le chiffrement au repos avec Azure Cosmos DB](database-encryption-at-rest.md)

- [Comprendre la gestion des clés pour le chiffrement au repos avec Azure Cosmos DB](/azure/cosmos-db/cosmos-db-security-controls)

- [Guide pratique pour configurer des clés gérées par le client pour votre compte Azure Cosmos DB](how-to-setup-cmk.md)

**Responsabilité** : Partagé

**Supervision d’Azure Security Center** : Aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Conseils** : Utilisez Azure Monitor avec le journal des activités Azure pour créer des alertes lorsque des modifications sont apportées à des instances de production d’Azure Cosmos DB.

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](/azure/azure-monitor/platform/alerts-activity-log)

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Aide** : Suivez les recommandations d’Azure Security Center pour vos instances d’Azure Cosmos DB. 

Microsoft effectue des mises à jour correctives du système et la gestion des vulnérabilités sur les hôtes sous-jacents qui prennent en charge vos instances Azure Cosmos DB. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Fonctionnalités prises en charge disponibles dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows)

**Responsabilité** : Partagé

**Supervision d’Azure Security Center** : Aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Aide** : Utilisez le Portail Azure ou Azure Resource Graph pour découvrir toutes les ressources (sans se limiter à Azure Cosmos DB, mais également pour les ressources telles que le calcul, les autres stockages, le réseau, les ports et les protocoles, etc.) au sein de vos abonnements. Vérifiez que vous disposez des autorisations appropriées dans votre locataire et pouvez répertorier tous les abonnements Azure ainsi que les ressources qu’ils contiennent.

Bien que les ressources Azure classiques puissent être découvertes via Resource Graph, il est vivement recommandé de créer et d’utiliser des ressources Azure Resource Manager à l’avenir.

- [Guide pratique pour créer des requêtes avec Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Guide pratique pour afficher ses abonnements Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Comprendre le contrôle d’accès en fonction du rôle Azure](../role-based-access-control/overview.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Aide** : Appliquez des étiquettes à vos instances Azure Cosmos DB et aux ressources associées présentant des métadonnées pour les organiser logiquement dans une taxonomie.

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

- [Quelles ressources Azure Cosmos DB prennent en charge les étiquettes ?](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Conseils** : Utilisez des étiquettes, des groupes d’administration, voire des abonnements séparés, pour organiser et suivre les ressources associées, y compris mais sans s’y limiter les ressources Azure Cosmos DB. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.

- [Guide pratique pour créer des abonnements Azure supplémentaires](/azure/billing/billing-create-subscription)

- [Guide pratique pour créer des groupes d’administration](/azure/governance/management-groups/create)

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Conseils** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans les abonnements clients selon les définitions de stratégies intégrées suivantes :

- Types de ressources non autorisés

- Types de ressources autorisés

Utilisez également Azure Resource Graph pour interroger et découvrir des ressources au sein des abonnements.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour créer des requêtes avec Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Conseils** : Appliquez des restrictions quant au type de ressources pouvant être créées dans les abonnements clients, en utilisant Azure Policy avec les définitions intégrées suivantes :
- Types de ressources non autorisés
- Types de ressources autorisés

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Aide** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ». Cela peut empêcher la création et la modification de ressources dans un environnement de haute sécurité.

- [Configuration de l’accès conditionnel pour bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Aide** : Définissez et implémentez des configurations de sécurité standard pour vos instances Cosmos DB avec Azure Policy. Utilisez des alias Azure Policy dans l’espace de noms **Microsoft.DocumentDB** afin de créer des stratégies personnalisées pour auditer ou appliquer la configuration de vos instances Cosmos DB. Vous pouvez également utiliser des définitions de stratégie intégrée pour Azure Cosmos DB, par exemple :
- Déployer Advanced Threat Protection pour les comptes Cosmos DB
- Cosmos DB doit utiliser un point de terminaison de service de réseau virtuel

- [Affichage des alias Azure Policy disponibles](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Aide** : Utilisez les stratégies Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Présentation des effets d’Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Conseils** : Si vous utilisez des définitions Azure Policy personnalisées pour vos ressources Cosmos DB ou associées, utilisez Azure Repos pour stocker et gérer vos définitions de stratégie personnalisée en tant que code.

- [Concevoir des workflows Stratégie en tant que code](../governance/policy/concepts/policy-as-code.md)

- [Documentation Azure Repos](/azure/devops/repos/git/gitworkflow)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Aide** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.DocumentDB » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Aide** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.DocumentDB » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. Utilisez les commandes Azure Policy [auditer], [refuser] et [déployer si elle n’existe pas] pour appliquer automatiquement des configurations pour vos instances Azure Cosmos DB et les ressources associées. 

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Aide** : Pour les machines virtuelles Azure ou les applications web s’exécutant sur Azure App Service utilisées pour accéder à vos instances Azure Cosmos DB, utilisez Managed Service Identity conjointement avec Azure Key Vault pour simplifier et sécuriser la gestion des secrets Azure Cosmos DB. Vérifiez que la suppression réversible est activée dans Key Vault.

- [Intégration aux identités managées Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Créer un coffre de clés](/azure/key-vault/quick-create-portal)

- [Comment s’authentifier auprès de Key Vault](../key-vault/general/authentication.md)

- [Comment attribuer une stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Aide** : Pour les machines virtuelles Azure ou les applications web s’exécutant sur Azure App Service utilisées pour accéder à vos instances Azure Cosmos DB, utilisez Managed Service Identity conjointement avec Azure Key Vault pour simplifier et sécuriser la gestion des secrets Azure Cosmos DB.

Utilisez des identités managées pour fournir aux services Azure une identité gérée automatiquement dans Azure Active Directory (Azure AD). Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

- [Configurer des identités managées](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Intégration aux identités managées Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault.

- [Comment configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Défense contre les programmes malveillants](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Conseils** : Microsoft Antimalware est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple Azure Corsmos DB), mais il ne s’exécute pas sur du contenu client.

Il vous incombe de pré-analyser tous les fichiers téléchargés vers des ressources Azure sans calcul, y compris Azure Cosmos DB. Microsoft ne peut pas accéder aux données client et ne peut donc pas effectuer d’analyses anti-programme malveillant du contenu client en votre nom.

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés managées par le client

**Conseils** : Azure Cosmos DB sauvegarde automatiquement vos données à intervalles réguliers. Si une base de données ou un conteneur est supprimé, vous pouvez émettre un ticket de support ou appeler le support Azure pour restaurer les données à partir de sauvegardes en ligne automatiques. Le support Azure est disponible seulement pour certains plans, comme Standard, Développeur et les plans plus élevés. Pour restaurer une capture instantanée spécifique de la sauvegarde, Azure Cosmos DB exige que les données soient accessibles pendant la durée du cycle de sauvegarde de cette capture instantanée. 

Si vous utilisez Key Vault pour stocker les informations d’identification de vos instances Cosmos DB, veillez à faire des sauvegardes automatisées régulières de vos clés.

- [Comprendre les sauvegardes automatisées Azure Cosmos DB](online-backup-and-restore.md)

- [Guide pratique pour restaurer des données dans Azure Cosmos DB](/azure/cosmos-db/how-to-backup-and-restore)

- [Comment sauvegarder des clés Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Responsabilité** : Partagé

**Supervision d’Azure Security Center** : Aucune

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Aide** : Si une base de données ou un conteneur est supprimé, vous pouvez émettre un ticket de support ou appeler le support Azure pour restaurer les données à partir de sauvegardes en ligne automatiques. Le support Azure est disponible seulement pour certains plans, comme Standard, Développeur et les plans plus élevés. Pour restaurer une capture instantanée spécifique de la sauvegarde, Azure Cosmos DB exige que les données soient accessibles pendant la durée du cycle de sauvegarde de cette capture instantanée.

Testez la restauration de vos secrets stockés dans Azure Key Vault à l’aide de PowerShell. Le cmdlet Restore-AzureKeyVaultKey crée une clé dans le coffre de clés spécifié. Cette clé est un réplica de la clé sauvegardée dans le fichier d’entrée et porte le même nom que la clé d’origine.

- [Comprendre les sauvegardes automatisées Azure Cosmos DB](online-backup-and-restore.md)

- [Guide pratique pour restaurer des données dans Azure Cosmos DB](/azure/cosmos-db/how-to-backup-and-restore)

- [Guide pratique pour restaurer des secrets Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilité** : Partagé

**Supervision d’Azure Security Center** : Aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés managées par le client

**Aide** : Comme toutes les données utilisateur stockées dans Cosmos DB sont chiffrées au repos et lors du transport, vous n’avez aucune action à effectuer. En d’autres termes, le chiffrement au repos est « activé » par défaut. Il n’existe aucun contrôle à activer ou à désactiver. Azure Cosmos DB utilise le chiffrement AES-256 dans toutes les régions où le compte est exécuté.

Activez la suppression réversible dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante.

- [Comprendre le chiffrement des données dans Azure Cosmos DB](database-encryption-at-rest.md)

- [Guide pratique pour activer la suppression réversible dans Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Responsabilité** : Partagé

**Supervision d’Azure Security Center** : Aucune

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.

- [Vous pouvez également tirer parti du guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de votre propre plan de réponse aux incidents](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

- [Comment configurer des automatisations de workflow dans Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Conseils** : Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que le Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements (par ex. production, non production) et créez un système d’attribution de noms pour identifier et classer les ressources Azure de façon claire.

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Aide** : Exécutez des exercices pour tester les fonctionnalités de réponse aux incidents de vos systèmes de façon régulière. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

- [Reportez-vous à la publication du NIST : Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide de test, d’entraînement et d’utilisation des programmes destinés aux plans et fonctionnalités informatiques)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 : Fournir des informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

**Conseils** : Les informations de contact d’incident de sécurité seront utilisées par Microsoft pour vous contacter si Microsoft Security Response Center (MSRC) découvre que les données du client ont été utilisées par un tiers illégal ou non autorisé.  Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus.

- [Comment définir le contact de sécurité d’Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Conseils** : Exportez vos alertes et recommandations d’Azure Security Center à l’aide de la fonctionnalité d’exportation continue. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser en continu les alertes vers Azure Sentinel.

- [Comment configurer l’exportation continue](../security-center/continuous-export.md)

- [Comment envoyer des alertes à Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Conseils** : Utilisez la fonctionnalité d’automatisation du workflow dans Azure Security Center pour déclencher automatiquement des réponses via « Logic Apps » sur les alertes et recommandations de sécurité.

- [Comment configurer l’automatisation des workflows et Logic Apps](../security-center/workflow-automation.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : tests d’intrusion et exercices Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1 : Procéder régulièrement à des tests d’intrusion des ressources Azure et veiller à corriger tous les problèmes de sécurité critiques détectés

**Aide** : Suivez les règles d’engagement de pénétration du cloud Microsoft pour vous assurer que vos tests d’intrusion sont conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft. 

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilité** : Partagé

**Supervision d’Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
