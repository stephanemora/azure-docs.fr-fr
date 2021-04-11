---
title: Base de référence de sécurité Azure pour Azure Database for MariaDB
description: La base de référence de sécurité Azure Database for MariaDB fournit des instructions et ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans le Benchmark de sécurité Azure.
author: msmbaldwin
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 189fb95f9c4be4ddf9d75a8dc26a7bf403caaadd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967658"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Base de référence de sécurité Azure pour Azure Database for MariaDB

Cette base de référence de sécurité applique les instructions du [Benchmark de sécurité Azure version 1.0](../security/benchmarks/overview-v1.md) à Azure Database for MariaDB. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure. Le contenu est regroupé selon les **contrôles de sécurité** définis par le benchmark de sécurité Azure et les instructions associées applicables à Azure Database for MariaDB. Les **contrôles** non applicables à Azure DB pour MariaDB, ou pour lesquels la responsabilité est celle de Microsoft, ont été exclus.

Pour voir comment s’effectue le mappage intégral d’Azure Database for MariaDB au Benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité Azure Database for MariaDB](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Aide** : Configurer Private Link pour Azure Database for MariaDB avec des points de terminaison privés Private Link vous permet de vous connecter à différents services PaaS dans Azure par le biais d’un point de terminaison privé. Azure Private Link intègre essentiellement les services Azure à votre Réseau virtuel privé. Le trafic entre votre réseau virtuel et l’instance MariaDB transite par le réseau principal de Microsoft.

Vous pouvez également utiliser des points de terminaison de service de réseau virtuel pour protéger et limiter l’accès réseau à vos implémentations Azure Database for MariaDB. Les règles de réseau virtuel désignent une fonctionnalité de sécurité de pare-feu qui permet de contrôler si votre instance Azure Database for MariaDB doit accepter ou non les communications provenant de sous-réseaux spécifiques dans des réseaux virtuels.

Vous pouvez aussi sécuriser votre instance Azure Database for MariaDB avec des règles de pare-feu. Le pare-feu du serveur empêche tout accès à votre serveur de base de données jusqu’à ce que vous ayez spécifié les ordinateurs qui disposent d’autorisations. Pour configurer votre pare-feu, vous créez des règles de pare-feu qui spécifient les plages d’adresses IP acceptables. Vous pouvez créer des règles de pare-feu au niveau du serveur.

- [Comment configurer Private Link pour Azure Database for MariaDB](howto-configure-privatelink-portal.md)

- [Comment créer et gérer des points de terminaison de service de réseau virtuel et des règles de réseau virtuel dans le serveur Azure Database for MariaDB](howto-manage-vnet-portal.md)

- [Comment configurer des règles de pare-feu d’Azure Database for MariaDB](concepts-firewall-rules.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés peut être nécessaire pour les alertes liées à ce contrôle.

**Définitions intégrées à Azure Policy – Microsoft.DBforMariaDB** :

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des interfaces réseau

**Conseils** : Quand votre serveur Azure Database for MariaDB est sécurisée sur un point de terminaison privé, vous pouvez déployer des machines virtuelles dans le même réseau virtuel. Vous pouvez utiliser un groupe de sécurité réseau pour réduire le risque d’exfiltration de données. Activez les journaux de flux NSG et transférez-les vers un compte de stockage pour l'audit du trafic. Vous pouvez aussi envoyer ces journaux vers un espace de travail Log Analytics et utiliser Traffic Analytics pour fournir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

- [Comment configurer Private Link pour Azure Database for MariaDB](howto-configure-privatelink-portal.md)

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Aide** : Utiliser Advanced Threat Protection pour Azure Database for MariaDB. Advanced Threat Protection détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de vos bases de données.

Activez la protection DDoS standard sur les réseaux virtuels associés à vos instances Azure Database for MariaDB pour vous protéger contre les attaques DDoS. Utilisez la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center pour refuser les communications avec des adresses IP Internet connues comme étant malveillantes ou inutilisées.

- [Comment configurer Advanced Threat Protection pour Azure Database for MariaDB](howto-database-threat-protection-portal.md)

- [Guide pratique pour configurer la protection DDoS](/azure/virtual-network/manage-ddos-protection)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Conseils** : Quand votre serveur Azure Database for MariaDB est sécurisée sur un point de terminaison privé, vous pouvez déployer des machines virtuelles dans le même réseau virtuel. Vous pouvez ensuite configurer un groupe de sécurité réseau pour réduire le risque d’exfiltration de données. Activez les journaux de flux NSG et transférez-les vers un compte de stockage pour l'audit du trafic. Vous pouvez aussi envoyer ces journaux vers un espace de travail Log Analytics et utiliser Traffic Analytics pour fournir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions basés sur le réseau (IDS/IPS)

**Aide** : Utiliser Advanced Threat Protection pour Azure Database for MariaDB. Advanced Threat Protection détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de vos bases de données.

- [Comment configurer Advanced Threat Protection pour Azure Database for MariaDB](howto-database-threat-protection-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Aide** : Pour les ressources qui doivent accéder à vos instances Azure Database for MariaDB, utilisez des étiquettes de service de réseau virtuel afin de définir des contrôles d’accès réseau sur des groupes de sécurité réseau ou le pare-feu Azure. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de l’étiquette de service (par exemple SQL.WestUs) dans le champ de source ou de destination approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

Azure Database for MariaDB utilise l’étiquette de service « Microsoft.Sql ».

- [Pour plus d’informations sur l’utilisation des étiquettes de service](../virtual-network/service-tags-overview.md)

- [Comprendre l’utilisation des étiquettes de service pour Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Conseils** : Définir et implémenter des configurations de sécurité standard pour les paramètres réseau et les ressources réseau associées à vos instances Azure Database for MariaDB avec Azure Policy. Utilisez des alias Azure Policy dans les espaces de noms « Microsoft.DBforMariaDB » et « Microsoft.Network » afin de créer des stratégies personnalisées pour auditer ou appliquer la configuration réseau de vos instances Azure Database for MariaDB. Vous pouvez aussi utiliser des définitions de stratégie intégrées relatives à la mise en réseau ou à vos instances Azure Database for MariaDB comme :

- DDoS Protection Standard doit être activé

- Le point de terminaison privé doit être activé pour les serveurs MariaDB

- Le serveur MariaDB doit utiliser un point de terminaison de service de réseau virtuel

Documentation de référence :

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemples Azure Policy pour le réseau](../governance/policy/samples/built-in-policies.md)

- [Guide pratique pour créer un blueprint Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Aide** : Utilisez des étiquettes pour les ressources liées à la sécurité réseau et au flux de trafic pour vos instances MariaDB afin de fournir des métadonnées et une organisation logique.

Utilisez l’une des définitions Azure Policy intégrée en lien avec l’étiquetage comme, « Exiger une étiquette et sa valeur » pour vous assurer que toutes les ressources créées sont étiquetées et être informé de l’existence de ressources non étiquetées.

Vous pouvez utiliser Azure PowerShell ou Azure CLI pour rechercher des ressources ou effectuer des actions sur des ressources en fonction de leurs étiquettes.

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Aide** : Utiliser le journal d’activité Azure pour superviser les configurations des ressources réseau et détecter les modifications des ressources réseau associées à vos instances Azure Database for MariaDB. Créez des alertes dans Azure Monitor, qui se déclenchent lors de la modification de ressources réseau critiques.

- [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](/azure/azure-monitor/platform/activity-log-view)

- [Guide pratique pour créer des alertes dans Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Activer les paramètres de diagnostic, les journaux du serveur et les journaux d’ingestion pour agréger les données de sécurité générées par vos instances Azure Database for MariaDB. Dans Azure Monitor, utilisez des espaces de travail Log Analytics pour interroger et effectuer l’analytique, puis utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage. Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce.

- [Comment configurer et accéder aux journaux du serveur pour Azure Database for MariaDB](concepts-server-logs.md)

- [Comment configurer et accéder aux journaux d’audit pour Azure Database for MariaDB](howto-configure-audit-logs-portal.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : Activer les paramètres de diagnostic sur vos instances Azure Database for MariaDB afin d’accéder aux journaux d’audit, de sécurité et de diagnostic. Veillez à activer spécifiquement le journal d’audit de MariaDB. Les journaux d’activité, automatiquement disponibles, incluent la source de l’événement, la date, l’utilisateur, l’horodatage, les adresses sources, les adresses de destination et d’autres éléments utiles. Vous pouvez aussi activer les paramètres de diagnostic des journaux d’activité Azure et envoyer les journaux vers le même espace de travail Log Analytics ou le même compte de stockage.

- [Comment configurer et accéder aux journaux du serveur pour Azure Database for MariaDB](concepts-server-logs.md)

- [Comment configurer et accéder aux journaux d’audit pour Azure Database for MariaDB](howto-configure-audit-logs-portal.md)

- [Guide pratique pour configurer les paramètres de diagnostic pour le journal d’activité Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Aide** : Dans Azure Monitor, pour l’espace de travail Log Analytics utilisé pour stocker vos journaux Azure Database for MariaDB, définissez la période de conservation dans le respect des réglementations de conformité de votre organisation. Utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage.

- [Définir les paramètres de conservation des journaux pour les espaces de travail Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Stockage des journaux des ressources dans un compte de stockage Azure](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Aide** : Analyser et superviser les journaux de vos instances MariaDB à la recherche d’un comportement anormal. Utilisez l’espace de travail Log Analytics d’Azure Monitor pour examiner les journaux et effectuer des requêtes sur leurs données. Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce.

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

- [En savoir plus sur l’espace de travail Log Analytics](/azure/azure-monitor/log-query/get-started-portal)

- [Guide pratique pour effectuer des requêtes personnalisées dans Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Conseils** : Activer Advanced Threat Protection pour MariaDB. La protection Advanced Threat Protection pour Azure Database for MariaDB détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de vos bases de données.

En outre, vous pouvez activer les journaux et les paramètres de diagnostic du serveur pour MariaDB, et envoyer les journaux à un espace de travail Log Analytics. Intégrez votre espace de travail Log Analytics à Azure Sentinel, car cela fournit une solution SOAR (Security Orchestration Automated Response). Cela permet de créer des playbooks (solutions automatisées) utilisables pour corriger des problèmes de sécurité.

- [Comment activer Advanced Threat Protection pour MariaDB](howto-database-threat-protection-portal.md)

- [Comment configurer et accéder aux journaux du serveur pour MariaDB](concepts-server-logs.md)

- [Comment configurer et accéder aux journaux d’audit pour MariaDB](howto-configure-audit-logs-portal.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Aide** : Conserver un inventaire des comptes d’utilisateur qui ont un accès d’administration au plan de gestion (Portail Azure/Azure Resource Manager) de vos instances MariaDB. En outre, conservez un inventaire des comptes d’administration qui ont accès au plan de données de vos instances MariaDB. (Quand vous créez le serveur MariaDB, vous fournissez les informations d’identification d’un administrateur. Cet administrateur peut être utilisé pour créer d’autres utilisateurs MariaDB.)

- [Comprendre la gestion des accès pour MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-security#access-management)

- [Comprendre les rôles Azure intégrés pour les abonnements Azure](../role-based-access-control/built-in-roles.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Aide** : Azure Active Directory (Azure AD) n’intègre pas le concept des mots de passe par défaut.

Lors de la création de la ressource MariaDB elle-même, Azure force la création d’un utilisateur administrateur avec un mot de passe fort. Cependant, une fois l’instance MariaDB créée, vous pouvez utiliser le premier compte d’administrateur de serveur que vous avez créé pour créer des utilisateurs supplémentaires et leur accorder un accès d’administration. Lors de la création de ces comptes, veillez à configurer un mot de passe fort et différent pour chaque compte.

- [Comment créer des comptes supplémentaires pour MariaDB](howto-create-users.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Aide** : Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés ayant accès à vos instances MariaDB. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration.

- [Présentation de l’identité et de l’accès Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Conseils** : L’accès du plan de données à MariaDB est contrôlé par les identités stockées dans la base de données et ne prend pas en charge l’authentification unique. L’accès au plan de contrôle pour MariaDB est disponible via l’API REST et prend en charge l’authentification unique. Pour vous authentifier, définissez l’en-tête d’autorisation pour vos demandes sur un jeton web JSON que vous obtenez auprès d’Azure Active Directory (Azure AD).

- [Comprendre l’API REST d’Azure Database for MariaDB](/rest/api/mariadb/)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseils** : activez l’authentification multifacteur Azure Active Directory (Azure AD), puis suivez les recommandations relatives à la gestion des identités et des accès d’Azure Security Center.

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6 : Utiliser des stations de travail sécurisées et gérées par Azure pour les tâches administratives

**Conseil** : utilisez des stations de travail disposant d’un accès privilégié (PAW) avec l’authentification multifacteur configurée pour vous connecter aux ressources Azure et les configurer. 

- [En savoir plus sur les stations de travail à accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration

**Aide** : Activer Advanced Threat Protection pour MariaDB de façon à générer des alertes en cas d’activité suspecte.

En outre, vous pouvez utiliser Azure Active Directory (Azure AD) Privileged Identity Management pour générer des journaux et des alertes quand des activités suspectes ou potentiellement dangereuses se produisent dans l’environnement. Utilisez les détections de risque Azure AD pour visualiser les alertes et des rapports sur les comportements à risque des utilisateurs.

- [Comment configurer Advanced Threat Protection pour MariaDB](howto-database-threat-protection-portal.md)

- [Déploiement de Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Présentation des détections de risques Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

**Conseils** : Utiliser des emplacements nommés à accès conditionnel pour autoriser l’accès seulement depuis des regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions pour limiter l’accès à des ressources Azure comme MariaDB.

- [Guide pratique pour configurer des emplacements nommés dans Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Utiliser Azure Active Directory (Azure AD) comme système d’authentification et d’autorisation central. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

L’authentification Azure AD ne peut pas être utilisée pour un accès direct au plan de données MariaDB. Les informations d’identification Azure AD peuvent cependant être utilisées pour l’administration au niveau du plan de gestion (par exemple le portail Azure) pour contrôler les comptes d’administrateur MariaDB.

- [Comment mettre à jour le mot de passe de l’administrateur pour MariaDB](https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : examinez les journaux Azure Active Directory (Azure AD) pour identifier les comptes obsolètes, qui peuvent inclure ceux ayant des rôles d’administration de MariaDB. Utilisez également des révisions d’accès d’identité Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise susceptibles d’être utilisées pour accéder à Azure Database for MariaDB et les attributions de rôles. Il convient d’examiner régulièrement les accès des utilisateurs, par exemple tous les 90 jours, pour vérifier que seuls les utilisateurs appropriés sont autorisés à accéder.

- [Présentation des rapports Azure AD](/azure/active-directory/reports-monitoring/)

- [Comment utiliser les révisions d’accès des identités Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Aide** : activez les paramètres de diagnostic pour MariaDB et Azure Active Directory (Azure AD) de façon à envoyer tous les journaux à un espace de travail Log Analytics. Configurez les alertes souhaitées (comme les tentatives d’authentification en échec) dans l’espace de travail Log Analytics.

- [Comment configurer et accéder aux journaux du serveur pour MariaDB](concepts-server-logs.md)

- [Comment configurer et accéder aux journaux d’audit pour MariaDB](howto-configure-audit-logs-portal.md)

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Conseils** : Activer Advanced Threat Protection pour MariaDB. La protection Advanced Threat Protection pour Azure Database for MariaDB détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de vos bases de données.

Utilisez les fonctionnalités de protection des identités et de détection des risques d’Azure Active Directory (Azure AD) pour configurer des réponses automatisées aux actions suspectes détectées. Vous pouvez activer des réponses automatisées par le biais d’Azure Sentinel pour implémenter les réponses de sécurité de votre organisation.

- [Comment activer Advanced Threat Protection pour MariaDB](howto-database-threat-protection-portal.md)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Guide pratique pour afficher les connexions risquées Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Aide** : Utilisez des étiquettes pour faciliter le suivi des instances Azure Database for MariaDB ou des ressources associées qui stockent ou traitent des informations sensibles.

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Utilisez une combinaison de Private Link, de points de terminaison de service et/ou de règles de pare-feu MariaDB pour isoler et limiter l’accès réseau à vos instances MariaDB.

- [Guide pratique pour créer des abonnements Azure supplémentaires](/azure/billing/billing-create-subscription)

- [Guide pratique pour créer des groupes d’administration](/azure/governance/management-groups/create)

- [Comment configurer Private Link pour Azure Database for MariaDB](concepts-data-access-security-private-link.md)

- [Comment configurer des points de terminaison de service dans Azure Database for MariaDB](howto-manage-vnet-portal.md)

- [Comment configurer des règles de pare-feu d’Azure Database for MariaDB](concepts-firewall-rules.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

**Aide** : Quand vous utilisez des machines virtuelles Azure pour accéder à des instances MariaDB, utilisez Private Link, les configurations réseau MariaDB, les groupes de sécurité réseau et les étiquettes de service pour réduire le risque d’une exfiltration de données.

Microsoft gère l’infrastructure sous-jacente de MariaDB, et a implémenté des contrôles stricts pour empêcher la perte ou l’exposition de données client.

- [Comment atténuer l’exfiltration de données pour Azure Database for MariaDB](concepts-data-access-security-private-link.md)

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Aide** : Azure Database for MariaDB prend en charge la connexion de votre serveur Azure Database for MariaDB aux applications clientes via TLS (Transport Layer Security), anciennement SSL (Secure Sockets Layer). L’application de connexions TLS entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application. Dans le portail Azure, vérifiez que l’option **Appliquer une connexion SSL** est activée par défaut pour toutes vos instances MariaDB.

- [Comment configurer le chiffrement en transit pour MariaDB](howto-configure-ssl.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Aide** : Les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour Azure Database for MariaDB. Implémentez une solution tierce si nécessaire à des fins de conformité.

Pour la plateforme sous-jacente managée par Microsoft, Microsoft considère tout le contenu client comme sensible et met tout en œuvre pour empêcher la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6 : Utiliser le contrôle d’accès en fonction du rôle pour contrôler l’accès aux ressources

**Aide** : utilisez le contrôle d’accès en fonction du rôle Azure (RBAC Azure) pour gérer l’accès à la base de données Azure pour le plan de gestion MariaDB (Portail Azure/Azure Resource Manager). Pour l’accès au plan de données (dans la base de données elle-même), utilisez des requêtes SQL pour créer des utilisateurs et configurer des autorisations utilisateur.

- [Comment configurer Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Comment configurer l’accès utilisateur avec SQL pour MariaDB](howto-create-users.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Utilisez Azure Monitor avec le journal d’activité Azure pour créer des alertes en cas de modifications sur des instances de production Azure Database for MariaDB et d’autres ressources critiques ou associées.

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Aide** : Actuellement non disponible ; Azure Security Center ne prend pas encore en charge l’évaluation des vulnérabilités pour le serveur Azure Database for MariaDB.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Aide** : utilisez Azure Resource Graph pour interroger et découvrir toutes les ressources (y compris le serveur Azure Database for MariaDB) dans vos abonnements. Vérifiez que vous disposez des autorisations (en lecture) appropriées dans votre locataire et pouvez répertorier tous les abonnements Azure ainsi que les ressources qu’ils contiennent.

- [Guide pratique pour créer des requêtes avec Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Guide pratique pour afficher ses abonnements Azure](/powershell/module/az.accounts/get-azsubscription)

- [Présentation d’Azure RBAC](../role-based-access-control/overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Aide** : Appliquer des étiquettes au serveur Azure Database for MariaDB et à d’autres ressources associées en ajoutant des métadonnées pour les organiser logiquement en une taxonomie.

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Aide** : Utiliser des étiquettes, des groupes d’administration et des abonnements distincts pour organiser et suivre le serveur Azure Database for MariaDB et les ressources associées. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.

- [Guide pratique pour créer des abonnements Azure supplémentaires](/azure/billing/billing-create-subscription)

- [Guide pratique pour créer des groupes d’administration](/azure/governance/management-groups/create)

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4 : Définir et tenir un inventaire des ressources Azure approuvées

**Aide** : Non applicable. Cette recommandation concerne les ressources de calcul et Azure dans son ensemble.

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

Pour plus d’informations, consultez les références suivantes :

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Aide** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ». Ceci peut empêcher la création et les modifications des ressources dans un environnement de haute sécurité, comme le serveur Azure Database for MariaDB contenant des informations sensibles.

- [Configuration de l’accès conditionnel pour bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Conseils** : Définir et implémenter des configurations de sécurité standard pour vos instances Azure Database for MariaDB avec Azure Policy. Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.DBforMariaDB » afin de créer des stratégies personnalisées pour auditer ou appliquer la configuration réseau de vos instances Azure Database for MariaDB. Vous pouvez aussi utiliser des définitions de stratégie intégrées relatives à vos instances Azure Database for MariaDB comme :

- La sauvegarde géoredondante doit être activée pour Azure Database for MariaDB

Pour plus d’informations, consultez les références suivantes :

- [Affichage des alias Azure Policy disponibles](/powershell/module/az.resources/get-azpolicyalias)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Aide** : Utilisez les stratégies Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Présentation des effets d’Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Si vous utilisez des définitions Azure Policy personnalisées pour vos serveurs Azure Database for MariaDB et les ressources associées, utilisez Azure Repos pour stocker et gérer votre code de façon sécurisée.

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentation Azure Repos](/azure/devops/repos/)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Aide** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.DBforMariaDB » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Aide** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.DBforMariaDB » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. Utilisez une stratégie Azure Policy [auditer], [refuser] et [déployer s’il n’existe pas] afin d’appliquer automatiquement des configurations pour vos instances Azure Database for MariaDB et les ressources associées.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Aide** : Pour les machines virtuelles Azure ou les applications web s’exécutant sur Azure App Service utilisées pour accéder à vos serveurs Azure Database for MariaDB, utilisez Managed Service Identity conjointement avec Azure Key Vault pour simplifier et sécuriser la gestion des secrets d’Azure Database for MariaDB. Vérifiez que la suppression réversible est activée dans Key Vault.

- [Intégration aux identités managées Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Créer un coffre de clés](../key-vault/general/quick-create-portal.md)

- [Comment s’authentifier auprès de Key Vault](../key-vault/general/authentication.md)

- [Comment attribuer une stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Aide** : Azure Database for MariaDB Server ne prend actuellement pas en charge l’authentification Azure Active Directory (Azure ZD) pour accéder aux bases de données. Quand vous créez un serveur Azure Database for MariaDB, vous fournissez les informations d’identification d’un utilisateur administrateur. Cet administrateur peut ensuite créer d’autres utilisateurs MariaDB.

Pour les machines virtuelles Azure ou les applications web s’exécutant sur Azure App Service utilisées pour accéder à votre serveur Azure Database for MariaDB, utilisez des identités managées conjointement avec Azure Key Vault pour stocker et récupérer des informations d’identification pour Azure Database for MariaDB. Vérifiez que la suppression réversible est activée dans Key Vault.

Utilisez des identités managées pour fournir aux services Azure une identité gérée automatiquement dans Azure AD. Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

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

**Aide** : Microsoft Antimalware est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple le serveur Azure Database for MariaDB), mais il ne s’exécute pas sur du contenu client.

Pré-analysez tout contenu chargé sur des ressources Azure non liées au calcul, comme App Service, Data Lake Storage, Stockage Blob, le serveur Azure Database for MariaDB, etc. Microsoft ne peut pas accéder à vos données dans ces instances.

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Garantir des sauvegardes automatiques régulières

**Aide** : Azure Database for MariaDB accepte les sauvegardes complètes, différentielles et de journal des transactions.  Azure Database for MariaDB crée automatiquement des sauvegardes de serveur et les conserve dans un stockage géoredondant ou redondant localement configuré par l’utilisateur. Les sauvegardes peuvent être utilisées pour restaurer votre serveur à un point dans le temps. La sauvegarde et la restauration sont une partie essentielle de toute stratégie de continuité d’activité, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles.  La période de rétention de sauvegarde par défaut est de sept jours. Vous pouvez éventuellement la configurer sur 35 jours maximum. Toutes les sauvegardes sont chiffrées à l’aide du chiffrement AES de 256 bits.

- [Comprendre les sauvegardes pour MariaDB](concepts-backup.md)

- [Comprendre la configuration initiale de MariaDB](tutorial-design-database-using-portal.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : le [benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés peut être nécessaire pour les alertes liées à ce contrôle.

**Définitions intégrées à Azure Policy – Microsoft.DBforMariaDB** :

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés gérées par le client

**Aide** : Azure Database for MariaDB crée automatiquement des sauvegardes de serveur et les conserve dans un stockage géoredondant ou redondant localement configuré par l’utilisateur. Les sauvegardes peuvent être utilisées pour restaurer votre serveur à un point dans le temps.  La sauvegarde et la restauration sont une partie essentielle de toute stratégie de continuité d’activité, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles.

Si vous utilisez Key Vault pour le chiffrement des données côté client pour les données stockées sur votre serveur MariaDB, veillez à effectuer des sauvegardes automatisées régulières de vos clés.

- [Comprendre les sauvegardes pour MariaDB](concepts-backup.md)

- [Comment sauvegarder des clés Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : le [benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés peut être nécessaire pour les alertes liées à ce contrôle.

**Définitions intégrées à Azure Policy – Microsoft.DBforMariaDB** :

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Aide** : Dans Azure Database for MariaDB, effectuez une restauration à partir de sauvegardes du serveur d’origine pour tester périodiquement les sauvegardes. Deux types de restauration sont disponibles : Restauration à un point dans le temps et géorestauration. La restauration à un point dans le temps est disponible avec l’option de redondance de la sauvegarde et elle crée un serveur dans la même région que votre serveur d’origine. La géorestauration est disponible seulement si vous avez configuré votre serveur pour le stockage géoredondant. Elle vous permet de restaurer votre serveur dans une autre région.

Le délai estimé de récupération dépend de plusieurs facteurs, notamment du nombre total de bases de données à récupérer dans la même région au même moment, de la taille des bases de données, de la taille du journal des transactions et de la bande passante réseau. Le délai de récupération est généralement inférieur à 12 heures.

- [Comprendre la sauvegarde et la restauration dans Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-backup#restore)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés gérées par le client

**Aide** : Azure Database for MariaDB accepte les sauvegardes complètes, différentielles et de journal des transactions. Celles-ci vous permettent de restaurer un serveur à n’importe quel point dans le temps au sein de votre période de rétention de sauvegarde configurée. La période de rétention de sauvegarde par défaut est de sept jours. Vous pouvez éventuellement la configurer sur 35 jours maximum. Toutes les sauvegardes sont chiffrées à l’aide du chiffrement AES de 256 bits.

- [Comprendre la sauvegarde et la restauration dans Azure Database for MariaDB](concepts-backup.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.

- [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Le client peut également tirer parti du guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de son propre plan de réponse aux incidents](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Aide** : Azure Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend de la confiance que Security Center accorde à la recherche ou à la métrique utilisées pour émettre l’alerte, ainsi qu’à la conviction quand à l’existence d’une intention malveillante derrière l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements à l’aide d’étiquettes et créez un système de nommage pour identifier et classer clairement les ressources Azure, en particulier celles qui traitent des données sensibles. Il vous incombe de hiérarchiser le traitement des alertes en fonction de la criticité des ressources et de l’environnement Azure où l’incident s’est produit.

- [Alertes de sécurité dans le Centre de sécurité Azure](../security-center/security-center-alerts-overview.md)

- [Organisation des ressources Azure à l’aide de catégories](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Conseils** : Effectuez des exercices pour tester les capacités de réponse aux incidents de vos systèmes à intervalles réguliers, afin de protéger vos ressources Azure. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

- [Reportez-vous à la publication du NIST : Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide de test, d’entraînement et d’utilisation des programmes destinés aux plans et fonctionnalités informatiques)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 : Fournir des informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

**Instructions** : Microsoft utilisera les informations de contact pour le signalement d’incidents de sécurité pour vous contacter si le Microsoft Security Response Center (MSRC) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé. Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus.

- [Comment définir le contact de sécurité d’Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Aide** : Exportez vos alertes et recommandations Azure Security Center en utilisant la fonctionnalité d’exportation continue pour identifier les risques pesant sur les ressources Azure. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser en continu les alertes vers Azure Sentinel.

- [Comment configurer l’exportation continue](../security-center/continuous-export.md)

- [Comment envoyer des alertes à Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Aide** : Utilisez la fonctionnalité d’automatisation de workflow d’Azure Security Center pour déclencher automatiquement des réponses via « Logic Apps » aux alertes et aux recommandations de sécurité afin de protéger vos ressources Azure.
    

- [Comment configurer l’automatisation des workflows et Logic Apps](../security-center/workflow-automation.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : tests d’intrusion et exercices Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1 : Procéder régulièrement à des tests d’intrusion des ressources Azure et veiller à corriger tous les problèmes de sécurité critiques détectés

**Aide** : Suivez les règles d’engagement de pénétration du cloud Microsoft pour vous assurer que vos tests d’intrusion sont conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft. 

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
