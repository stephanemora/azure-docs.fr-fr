---
title: Base de référence de la sécurité Azure pour HDInsight
description: La base de référence de sécurité HDInsight fournit des instructions et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans Azure Security Benchmark.
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c5ffdecf768be0962950bb3691dbb11fb0e70120
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565008"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Base de référence de la sécurité Azure pour HDInsight

Cette base de référence de sécurité applique les conseils [Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) à HDInsight. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure.
Le contenu est regroupé par les **contrôles de sécurité** définis par Azure Security Benchmark et les conseils associés applicables à HDInsight. Les **contrôles** non applicables à HDInsight ont été exclus.

 
Pour voir comment HDInsight est entièrement mappé à Azure Security Benchmark, consultez le [fichier de mappage complet de la base de référence de sécurité HDInsight](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Instructions** : La sécurité du périmètre dans Azure HDInsight est obtenue par le biais de réseaux virtuels. Un administrateur d’entreprise peut créer un cluster dans un réseau virtuel, et utiliser un groupe de sécurité réseau (NSG) pour restreindre l’accès au réseau virtuel. Seules les adresses IP autorisées dans les règles de groupe de sécurité réseau de trafic entrant sont en mesure de communiquer avec le cluster Azure HDInsight. Cette configuration assure la sécurité du périmètre. Tous les clusters déployés sur un réseau virtuel auront aussi un point de terminaison privé qui se résout en une adresse IP privée dans le réseau virtuel, pour un accès HTTP privé aux passerelles de cluster.

Pour réduire le risque de perte de données via l’exfiltration, limitez le trafic réseau sortant pour les clusters Azure HDInsight à l’aide du pare-feu Azure.

- [Guide pratique pour déployer Azure HDInsight dans un réseau virtuel et utiliser un groupe de sécurité réseau pour garantir la sécurité](hdinsight-create-virtual-network.md)

- [Guide pratique pour limiter le trafic sortant pour les clusters Azure HDInsight avec Pare-feu Azure](hdinsight-restrict-outbound-traffic.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des interfaces réseau

**Instructions** : Utilisez Azure Security Center et appliquez les recommandations de protection réseau pour le réseau virtuel, le sous-réseau et le groupe de sécurité réseau utilisés pour sécuriser votre cluster Azure HDInsight. Activez les journaux de flux de groupe de sécurité réseau (NSG) et envoyez les journaux dans un compte de Stockage Azure à l’audit du trafic. Vous pouvez aussi envoyer ces journaux vers un espace de travail Azure Log Analytics et utiliser Azure Traffic Analytics pour fournir des insights sur le flux de trafic dans votre cloud Azure. Azure Traffic Analytics offre certains avantages, parmi lesquels la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, de détecter les menaces de sécurité, de bien comprendre les modèles de flux de trafic et enfin de repérer les configurations réseau incorrectes.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Comment activer et utiliser Azure Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Présentation de la sécurité réseau assurée par Azure Security Center](../security-center/security-center-network-recommendations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Instructions** : Pour obtenir des protections contre les attaques DDoS, activez la protection Azure DDoS standard sur le réseau virtuel sur lequel votre instance Azure HDInsight est déployée. Utilisez la fonctionnalité Threat Intelligence intégrée à Azure Security Center pour refuser les communications avec des adresses IP Internet connues comme étant malveillantes ou inutilisées.

- [Guide pratique pour configurer la protection DDoS](../ddos-protection/manage-ddos-protection.md)

- [Présentation de la fonctionnalité Threat Intelligence intégrée à Azure Security Center](../security-center/azure-defender.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Aide** : Activez les journaux de flux de groupe de sécurité réseau (NSG) pour le groupe de sécurité réseau attaché au sous-réseau utilisé pour protéger votre cluster Azure HDInsight. Enregistrez les journaux de flux NSG dans un compte de stockage Azure pour générer des enregistrements de flux. Si cela s’avère nécessaire pour analyser une activité anormale, activez la capture de paquets Azure Network Watcher.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions (IDS/IPS) basés sur le réseau

**Instructions** : Cette condition peut être respectée avec l’ID de contrôle de sécurité Azure 1.1. Déployez un cluster Azure HDInsight dans un réseau virtuel et sécurisez-le avec un groupe de sécurité réseau (NSG).

Plusieurs dépendances pour Azure HDInsight exigent un trafic entrant. Le trafic de gestion entrant ne peut pas être envoyé via un dispositif de pare-feu. Les adresses sources pour le trafic de gestion nécessaire sont connues et publiées. Créez des règles de groupe de sécurité réseau avec ces informations pour autoriser le trafic à partir d’emplacements approuvés uniquement, en sécurisant le trafic entrant vers les clusters.

Pour réduire le risque de perte de données via l’exfiltration, limitez le trafic réseau sortant pour les clusters Azure HDInsight à l’aide du pare-feu Azure.

- [Guide pratique pour déployer HDInsight dans un réseau virtuel et utiliser un groupe de sécurité réseau pour garantir la sécurité](hdinsight-create-virtual-network.md)

- [Comprendre les dépendances HDInsight et l’utilisation du pare-feu](hdinsight-restrict-outbound-traffic.md)

- [Adresses IP de gestion HDInsight](hdinsight-management-ip-addresses.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Instructions** : Utilisez les étiquettes de service du réseau virtuel pour définir les contrôles d’accès réseau sur les groupes de sécurité réseau (NSG) attachés au sous-réseau dans lequel votre cluster Azure HDInsight est déployé. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de la balise de service (par exemple, ApiManagement) dans le champ Source ou Destination approprié d'une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

- [Comprendre et utiliser des étiquettes de service pour Azure HDInsight](../virtual-network/network-security-groups-overview.md#service-tags)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Instructions** : Définissez et implémentez des configurations de sécurité standard pour les ressources réseau liées à votre cluster Azure HDInsight. Utilisez des alias Azure Policy dans les espaces de noms « Microsoft.HDInsight » et « Microsoft.Network » afin de créer des stratégies personnalisées pour auditer ou appliquer la configuration réseau de votre cluster Azure HDInsight.

Vous pouvez également utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant les principaux artefacts d’environnement (par exemple, les modèles Resource Manager, les contrôles Azure RBAC et les stratégies) dans une même définition de blueprint. Appliquez facilement le blueprint aux nouveaux abonnements et environnements, et ajustez le contrôle et la gestion par le biais du versioning.

- [Affichage des alias Azure Policy disponibles](/powershell/module/az.resources/get-azpolicyalias)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour créer un blueprint Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Instructions** : Utilisez des étiquettes pour les groupes de sécurité réseau et autres ressources liées à la sécurité réseau et au flux de trafic qui sont associés à votre cluster Azure HDInsight. Concernant les règles NSG individuelles, utilisez le champ « Description » afin de spécifier le besoin métier et/ou la durée (etc.) pour toutes les règles qui autorisent le trafic vers/depuis un réseau.

Utilisez l’une des définitions de stratégie Azure intégrée en lien avec l’étiquetage comme « Exiger une étiquette et sa valeur » pour vous assurer que toutes les ressources créées sont étiquetées et être informé de l’existence de ressources non étiquetées.

Vous pouvez utiliser Azure PowerShell ou l’interface de ligne de commande (CLI) Azure pour rechercher des ressources ou effectuer des actions sur des ressources en fonction de leurs étiquettes.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

- [Guide pratique pour créer un réseau virtuel](../virtual-network/quick-create-portal.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Instructions** : Utilisez le journal d’activité Azure pour superviser les configurations des ressources réseau et détecter les changements des ressources réseau associées à vos déploiements Azure HDInsight. Créez des alertes dans Azure Monitor, qui se déclenchent lors de la modification de ressources réseau critiques.

- [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Guide pratique pour créer des alertes dans Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Instructions** : Vous pouvez intégrer votre cluster Azure HDInsight à Azure Monitor pour agréger les données de sécurité générées par le cluster. Utilisez des requêtes personnalisées pour détecter les menaces dans l’environnement et y répondre. 

- [Guide pratique pour intégrer un cluster Azure HDInsight à Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Guide pratique pour créer des requêtes personnalisées pour un cluster Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Instructions** : Activez Azure Monitor pour le cluster HDInsight, puis dirigez-le vers un espace de travail Log Analytics. Cela permet de journaliser des informations de cluster pertinentes et des métriques de système d’exploitation pour tous les nœuds de cluster Azure HDInsight.

- [Guide pratique pour activer la journalisation pour un cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Guide pratique pour interroger des journaux HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4 : Collecter les journaux de sécurité des systèmes d’exploitation

**Instructions** : Intégrez un cluster Azure HDInsight à Azure Monitor. Vérifiez que la période de conservation des journaux définie dans l’espace de travail Log Analytics utilisé est conforme aux obligations réglementaires de votre organisation.

- [Guide pratique pour intégrer un cluster Azure HDInsight à Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Comment configurer la période de conservation d’un espace de travail Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Instructions** : Intégrez un cluster Azure HDInsight à Azure Monitor. Vérifiez que la période de conservation des journaux définie dans l’espace de travail Azure Log Analytics utilisé est conforme aux obligations réglementaires de votre organisation.

- [Guide pratique pour intégrer un cluster Azure HDInsight à Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Comment configurer la période de conservation d’un espace de travail Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Instructions** : Utilisez des requêtes d’espace de travail Log Analytics pour interroger des journaux Azure HDInsight :

- [Guide pratique pour créer des requêtes personnalisées pour les clusters Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Instructions** : Utilisez l’espace de travail Azure Log Analytics pour superviser les activités anormales dans les événements et les journaux de sécurité liés à votre cluster Azure HDInsight, et générer des alertes s’y rapportant.

- [Guide pratique pour gérer les alertes dans Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Guide pratique pour générer une alerte sur des données de journal Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="28-centralize-anti-malware-logging"></a>2.8 : Centraliser la journalisation anti-programme malveillant

**Instructions** : Azure HDInsight est fourni avec Clamscan préinstallé et activé pour les images de nœud de cluster. Toutefois, vous devez gérer le logiciel et agréger/superviser manuellement tous les journaux générés par Clamscan.

- [Comprendre Clamscan](./hdinsight-faq.md#security-and-certificates)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="29-enable-dns-query-logging"></a>2.9 : Activer la journalisation des requêtes DNS

**Conseils** : Implémentez une solution tierce pour la journalisation DNS.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="210-enable-command-line-audit-logging"></a>2.10 : Activer l’enregistrement d’audit en ligne de commande

**Instructions** : Configurez manuellement la journalisation de console au niveau de chaque nœud.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Instructions** : Tenez à jour l’enregistrement du compte d’administration local créé lors du provisionnement du cluster Azure HDInsight ainsi que des autres comptes que vous créez. De plus, si l’intégration Azure Active Directory (Azure AD) est utilisée, Azure AD comporte des rôles intégrés qui doivent être explicitement attribués et qui peuvent donc être interrogés. Utilisez le module Azure AD PowerShell pour effectuer des requêtes ad hoc afin de découvrir les comptes qui sont membres de groupes d’administration.

Vous pouvez également suivre les recommandations liées à la gestion des identités et des accès dans Azure Security Center.

- [Guide pratique pour obtenir un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Guide pratique pour obtenir les membres d’un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Comment superviser les identités et les accès avec Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Instructions** : Lors du provisionnement d’un cluster, Azure vous oblige à créer des mots de passe pour l’accès au portail web et l’accès SSH (Secure Shell). Il n’y a pas de mot de passe par défaut à changer. Toutefois, vous pouvez spécifier des mots de passe différents pour l’accès SSH et l’accès au le portail web.

- [Guide pratique pour définir des mots de passe lors du provisionnement d’un cluster Azure HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseil** : intégrez l’authentification pour un cluster Azure HDInsight avec Azure Active Directory (Azure AD). Créez des stratégies et des procédures qui contrôlent l’utilisation de comptes d’administration dédiés.

Vous pouvez également suivre les recommandations liées à la gestion des identités et des accès dans Azure Security Center.

- [Guide pratique pour intégrer l’authentification Azure HDInsight avec Azure AD](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Comment superviser les identités et les accès avec Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Conseil** : utilisez le broker d’ID Azure HDInsight pour vous connecter à des clusters du Pack Sécurité Entreprise (ESP) à l’aide de l’authentification multifacteur, sans fournir de mot de passe. Si vous êtes déjà connecté à d’autres services Azure, comme le portail Azure, vous pouvez vous connecter à votre cluster Azure HDInsight avec une expérience d’authentification unique (SSO).

- [Guide pratique pour activer le broker d’ID Azure HDInsight](./domain-joined/identity-broker.md#enable-hdinsight-id-broker)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseil** : activez l’authentification multifacteur Azure Active Directory (Azure AD), puis suivez les recommandations relatives à la gestion des identités et des accès Azure Security Center. Comme les clusters Azure HDInsight pour lesquels le Pack Sécurité Entreprise est configuré peuvent être connectés à un domaine, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier auprès des clusters et exécuter des tâches de Big Data. Lors de l’authentification avec l’authentification multifacteur activée, les utilisateurs sont tenus de fournir un deuxième facteur d’authentification.

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Conseil** : utilisez des stations de travail à accès privilégié avec l’authentification multifacteur configurée pour la connexion à vos clusters Azure HDInsight et aux ressources qui leur sont associées, ainsi que pour leur configuration.

- [En savoir plus sur les stations de travail à accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration

**Instructions** : Comme les clusters Azure HDInsight pour lesquels le Pack Sécurité Entreprise est configuré peuvent être connectés à un domaine, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier. Vous pouvez utiliser les rapports de sécurité Azure Active Directory (Azure AD) pour générer des journaux et des alertes quand une activité suspecte ou potentiellement dangereuse se produit dans l’environnement Azure AD. Utiliser Azure Security Center pour superviser les activités liées aux identités et aux accès.

- [Guide pratique pour identifier les utilisateurs Azure AD pour lesquels une activité à risque a été signalée](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour superviser l’activité liée aux identités et aux accès des utilisateurs dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

**Instructions** : Comme les clusters Azure HDInsight pour lesquels le Pack Sécurité Entreprise est configuré peuvent être connectés à un domaine, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier. Utilisez des emplacements nommés à accès conditionnel pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.

- [Guide pratique pour configurer des emplacements nommés dans Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Utiliser Azure Active Directory (Azure AD) comme système d’authentification et d’autorisation central. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

Comme les clusters Azure HDInsight pour lesquels le Pack Sécurité Entreprise (ESP) est configuré peuvent être connectés à un domaine, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier auprès des clusters.

- [Création et configuration d’une instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Guide pratique pour configurer un Pack Sécurité Entreprise avec Azure AD Domain Services dans Azure HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Conseil** : utilisez l’authentification Azure Active Directory (Azure AD) avec votre cluster Azure HDInsight. Azure AD fournit des journaux pour vous aider à découvrir les comptes obsolètes. De plus, utilisez les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès de l’utilisateur peut être passé en revue régulièrement pour vérifier que seuls les utilisateurs appropriés bénéficient d’un accès permanent.

- [Comment utiliser les révisions d’accès des identités Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Conseil** : utilisez les journaux Connexion et Audit d’Azure Active Directory (Azure AD) pour superviser les tentatives d’accès à des comptes désactivés. Ces journaux peuvent être intégrés à n’importe quel outil SIEM/de supervision tiers.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure AD et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Azure Log Analytics. Configurez les alertes souhaitées dans un espace de travail Azure Log Analytics.

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Instructions** : Comme les clusters Azure HDInsight pour lesquels le Pack Sécurité Entreprise (ESP) est configuré peuvent être connectés à un domaine, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier auprès des clusters. Utilisez les détections de risque et la fonctionnalité Identity Protection d’Azure Active Directory (Azure AD) pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités d’utilisateur. De plus, vous pouvez ingérer des données dans Azure Sentinel pour approfondir votre examen.

- [Guide pratique pour afficher les connexions risquées Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

**Instructions** : Non disponible. Customer Lockbox n’est actuellement pas pris en charge pour Azure HDInsight.

- [Liste des services pris en charge pour Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Instructions** : Utilisez des étiquettes sur des ressources associées à vos déploiements Azure HDInsight pour faciliter le suivi des ressources Azure qui stockent ou traitent des informations sensibles.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Les clusters Azure HDInsight et tous les comptes de stockage associés doivent être séparés par un réseau/sous-réseau virtuel, étiquetés de manière appropriée et sécurisés dans un groupe de sécurité réseau (NSG) ou Pare-feu Azure. Les données de cluster doivent être contenues dans un compte de Stockage Azure sécurisé ou Azure Data Lake Storage (Gen1 ou Gen2).

- [Choisir les options de stockage pour votre cluster Azure HDInsight](hdinsight-hadoop-compare-storage-options.md)

- [Guide pratique pour sécuriser Azure Data Lake Storage](../data-lake-store/data-lake-store-security-overview.md)

- [Comment sécuriser des comptes de Stockage Azure](../storage/blobs/security-recommendations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

**Instructions** : Pour les clusters Azure HDInsight qui stockent ou traitent des informations sensibles, marquez le cluster et les ressources associées comme sensibles à l’utilisation d’étiquettes. Pour réduire le risque de perte de données via l’exfiltration, limitez le trafic réseau sortant pour les clusters Azure HDInsight à l’aide du pare-feu Azure.

Pour la plateforme sous-jacente qui est gérée par Microsoft, Microsoft traite tout le contenu client comme sensible et fait de grands efforts pour vous protéger contre la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Guide pratique pour limiter le trafic sortant pour les clusters Azure HDInsight avec Pare-feu Azure](hdinsight-restrict-outbound-traffic.md)

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Instructions** : Chiffrer toutes les informations sensibles en transit. Vérifiez que les clients qui se connectent à votre cluster Azure HDInsight ou à vos magasins de données de cluster (comptes de Stockage Azure ou Azure Data Lake Storage Gen1/Gen2) peuvent négocier TLS 1.2 ou une version ultérieure. Par défaut, les ressources Microsoft Azure négocient TLS 1.2. 

- [Comprendre le chiffrement Azure Data Lake Storage en transit](../data-lake-store/data-lake-store-security-overview.md)

- [Comprendre le chiffrement en transit pour un compte Stockage Azure](../storage/blobs/security-recommendations.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Instructions** : Les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour le Stockage Azure ou les ressources de calcul. Implémentez une solution tierce si nécessaire à des fins de conformité.

Pour la plateforme sous-jacente managée par Microsoft, Microsoft considère tout le contenu client comme sensible et met tout en œuvre pour empêcher la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : aucune

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 : Utiliser Azure RBAC pour contrôler l’accès aux ressources 

**Instructions** : Avec le Pack Sécurité Entreprise (ESP) Azure HDInsight, vous pouvez utiliser Apache Ranger pour créer et gérer des stratégies de contrôle d’accès et d’obfuscation des données affinées pour vos données stockées dans des fichiers, des dossiers, des bases de données, des tables et des lignes/colonnes. L’administrateur Hadoop peut configurer le contrôle d’accès en fonction du rôle (RBAC) pour sécuriser Apache Hive, HBase, Kafka et Spark à l’aide de ces plug-ins dans Apache Ranger.

La configuration de stratégies RBAC avec Apache Ranger vous permet d’associer des autorisations à un rôle dans l’organisation. Cette couche d’abstraction permet de s’assurer plus facilement que les utilisateurs disposent uniquement des autorisations nécessaires pour effectuer leur travail.

- [Configurations du Pack Sécurité Entreprise avec Azure Active Directory (Azure AD) Domain Services dans HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Vue d’ensemble de la sécurité d’entreprise dans Azure HDInsight](domain-joined/hdinsight-security-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7 : Utiliser la protection contre la perte de données basée sur l’hôte pour appliquer le contrôle d’accès

**Instructions** : Pour les clusters Azure HDInsight qui stockent ou traitent des informations sensibles, marquez le cluster et les ressources associées comme sensibles à l’utilisation d’étiquettes. Les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour le Stockage Azure ou les ressources de calcul. Implémentez une solution tierce si nécessaire à des fins de conformité.

Pour la plateforme sous-jacente managée par Microsoft, Microsoft considère tout le contenu client comme sensible et met tout en œuvre pour empêcher la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Instructions** : Si vous utilisez Azure SQL Database pour stocker des métadonnées Apache Hive et Apache Oozie, vérifiez que les données SQL restent chiffrées en permanence. Pour les comptes de Stockage Azure et Data Lake Storage (Gen1 ou Gen2), nous vous recommandons d’autoriser Microsoft à gérer vos clés de chiffrement. Vous avez toutefois la possibilité de gérer vos propres clés.

- [Guide pratique pour gérer les clés de chiffrement pour les comptes Stockage Azure](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Guide pratique pour créer un compte Azure Data Lake Storage à l’aide de clés de chiffrement gérées par le client](../data-lake-store/data-lake-store-get-started-portal.md)

- [Comprendre le chiffrement pour Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md#data-encryption)

- [Guide pratique pour configurer le chiffrement transparent des données (TDE, Transparent Data Encryption) pour SQL Database à l’aide de clés gérées par le client](../azure-sql/database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Instructions** : Configurez les paramètres de diagnostic de comptes de stockage Azure associés à des clusters Azure HDInsight pour superviser et journaliser toutes les opérations CRUD sur les données de cluster. Activez l’audit pour tous les comptes de Stockage ou Data Lake Stores associés au cluster Azure HDInsight.

- [Guide pratique pour activer une journalisation/un audit supplémentaires pour un compte Stockage Azure](../storage/common/manage-storage-analytics-logs.md)

- [Guide pratique pour activer une journalisation/un audit supplémentaires pour Azure Data Lake Storage](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Instructions** : Implémentez une solution tierce de gestion des vulnérabilités.

Éventuellement, si vous disposez d’un abonnement à Rapid7, à Qualys ou à toute autre plateforme de gestion des vulnérabilités, vous pouvez utiliser des actions de script pour installer des agents d’évaluation des vulnérabilités sur vos nœuds de cluster Azure HDInsight et gérer les nœuds par le biais du portail correspondant.

- [Comment installer manuellement l’agent Rapid7](https://insightvm.help.rapid7.com/docs/install)

- [Comment installer manuellement l’agent Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [Guide pratique pour utiliser des actions de script](hdinsight-hadoop-customize-cluster-linux.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 : Déployer une solution de gestion des correctifs de système d’exploitation automatisée

**Instructions** : Les mises à jour automatiques du système ont été activées pour les images de nœud de cluster. Toutefois, vous devez redémarrer régulièrement les nœuds de cluster pour garantir que les mises à jour sont appliquées.

Microsoft pour tenir à jour et mettre à jour les images de nœud Azure HDInsight de base.

- [Guide pratique pour configurer la planification de mise à jour corrective du système d’exploitation pour les clusters HDInsight](hdinsight-os-patching.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3 : Déployer une solution de gestion automatisée des correctifs des logiciels tiers

**Instructions** : Utilisez des actions de script ou d’autres mécanismes pour corriger vos clusters Azure HDInsight. Les clusters nouvellement créés disposeront toujours des mises à jour et des correctifs de sécurité les plus récents.

- [Guide pratique pour configurer la planification de la mise à jour corrective du système d’exploitation pour les clusters Azure HDInsight Linux](hdinsight-os-patching.md)

- [Guide pratique pour utiliser des actions de script](hdinsight-hadoop-customize-cluster-linux.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 : Comparer les analyses de vulnérabilités dos à dos

**Instructions** : Implémentez une solution tierce de gestion des vulnérabilités qui permet de comparer les analyses de vulnérabilités au fil du temps. Si vous disposez d’un abonnement Rapid7 ou Qualys, vous pouvez utiliser le portail de ce fournisseur pour consulter et comparer les analyses de vulnérabilités dos à dos.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes.

**Instructions** : Utilisez un programme de scoring des risques courants (par exemple, Common Vulnerability Scoring System) ou bien les évaluations des risques par défaut fournies par votre outil d’analyse tiers.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Conseil** : utilisez Azure Resource Graph pour interroger et découvrir toutes les ressources (comme le calcul, le stockage, le réseau, les ports, les protocoles, etc.), dont les clusters Azure HDInsight, dans vos abonnements. Vérifiez que vous disposez des autorisations (en lecture) appropriées dans votre locataire et pouvez répertorier tous les abonnements Azure ainsi que les ressources qu’ils contiennent.

Bien que les ressources Azure classiques puissent être découvertes via Azure Resource Graph, il est vivement recommandé de créer et d’utiliser des ressources Azure Resource Manager à l’avenir.

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

**Conseils** : Utilisez des étiquettes, des groupes d’administration et diviser des abonnements, le cas échéant, pour organiser et suivre les ressources. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4 : Définir et tenir un inventaire des ressources Azure approuvées

**Conseils** : Établissez la liste des ressources Azure et des logiciels approuvés pour vos ressources de calcul

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Conseils** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans les abonnements clients selon les définitions de stratégies intégrées suivantes :

- Types de ressources non autorisés

- Types de ressources autorisés

Utilisez le service Azure Resource Graph pour interroger et découvrir les ressources dans vos abonnements. Vérifiez que toutes les ressources Azure présentes dans l’environnement sont approuvées.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour créer des requêtes avec Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6 : Analyser les applications logicielles non approuvées dans des ressources de calcul

**Instructions** : Implémentez une solution tierce afin de superviser les nœuds de cluster pour les applications logicielles non approuvées.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 : Supprimer des ressources et applications logicielles Azure non approuvées

**Conseil** : utilisez Azure Resource Graph pour interroger et découvrir toutes les ressources (comme le calcul, le stockage, le réseau, les ports, les protocoles, etc.), dont les clusters Azure HDInsight, dans vos abonnements.  Supprimez toutes les ressources Azure non approuvées que vous découvrez. Pour les nœuds de cluster Azure HDInsight, implémentez une solution tierce afin de supprimer les logiciels non approuvés ou de générer des alertes s’y rapportant.

- [Guide pratique pour créer des requêtes avec Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="68-use-only-approved-applications"></a>6.8 : Utiliser des applications approuvées uniquement

**Instructions** : Pour les nœuds de cluster Azure HDInsight, implémentez une solution tierce qui empêche l’exécution de logiciels non autorisés.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Conseils** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans les abonnements clients selon les définitions de stratégies intégrées suivantes :
- Types de ressources non autorisés

- Types de ressources autorisés

Pour plus d’informations, consultez les références suivantes :

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10 : Tenir un inventaire des titres de logiciels approuvés

**Instructions** : Pour les nœuds de cluster Azure HDInsight, implémentez une solution tierce qui empêche l’exécution de types de fichiers non autorisés.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Aide** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ».

- [Configuration de l’accès conditionnel pour bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Instructions** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.HDInsight » afin de créer des stratégies personnalisées pour auditer ou appliquer la configuration réseau de votre cluster HDInsight.

- [Affichage des alias Azure Policy disponibles](/powershell/module/az.resources/get-azpolicyalias)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="72-establish-secure-operating-system-configurations"></a>7.2 : Établir des configurations sécurisées du système d’exploitation

**Instructions** : Images du système d’exploitation Azure HDInsight gérées et tenues à jour par Microsoft. Client chargé d’implémenter des configurations sécurisées pour le système d’exploitation de vos nœuds de cluster.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Aide** : Utilisez les stratégies Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés pour vos clusters Azure HDInsight et les ressources associées.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Présentation des effets d’Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4 : Préserver la sécurité des configurations du système d'exploitation

**Instructions** : Images du système d’exploitation Azure HDInsight gérées et tenues à jour par Microsoft. Client chargé de l’implémentation de la configuration de l’état au niveau du système d’exploitation.

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Si vous utilisez des définitions Azure Policy personnalisées, utilisez Azure DevOps ou Azure Repos pour stocker et gérer votre code en toute sécurité.

- [Tutoriel Git Azure Repos](/azure/devops/repos/git/gitworkflow)

- [Documentation Azure Repos](/azure/devops/repos/index)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Instructions** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.HDInsight » afin de créer des stratégies personnalisées afin d’alerter, d’auditer ou d’appliquer des configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8 : Déployer des outils de gestion de la configuration pour les systèmes d'exploitation

**Instructions** : Implémentez une solution tierce afin de maintenir l’état souhaité pour les systèmes d’exploitation de vos nœuds de cluster.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Conseil** : utilisez des alias Azure Policy dans l’espace de noms « Microsoft.HDInsight » afin de créer des stratégies personnalisées pour auditer ou appliquer la configuration de votre cluster HDInsight.

- [Affichage des alias Azure Policy disponibles](/powershell/module/az.resources/get-azpolicyalias)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 : Implémenter la surveillance de la configuration automatique pour les systèmes d’exploitation

**Instructions** : Implémentez une solution tierce pour superviser l’état des systèmes d’exploitation de vos nœuds de cluster.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Instructions** : Azure HDInsight assure la prise en charge du modèle BYOK (Bring Your Own Key) pour Apache Kafka. Cette fonctionnalité vous permet de posséder et de gérer les clés servant à chiffrer les données au repos.

Tous les disques managés dans Azure HDInsight sont protégés par Azure Storage Service Encryption (SSE). Par défaut, les données stockées sur ces disques sont chiffrées à l’aide de clés gérées par Microsoft. Si vous activez BYOK, vous devez fournir la clé de chiffrement pour permettre à Azure HDInsight de l’utiliser et de la gérer à l’aide d’Azure Key Vault.

Key Vault peut également être utilisé avec des déploiements Azure HDInsight pour gérer les clés du stockage de cluster (comptes de Stockage Azure et Azure Data Lake Storage)

- [Guide pratique pour apporter sa propre clé pour Apache Kafka sur Azure HDInsight](./disk-encryption.md)

- [Guide pratique pour gérer les clés de chiffrement pour les comptes Stockage Azure](../storage/common/customer-managed-keys-configure-key-vault.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Conseil** : l’utilisation d’identités managées dans Azure HDInsight permet à vos clusters d’accéder aux services de domaine Azure Active Directory (Azure AD), à Azure Key Vault ou aux fichiers stockés dans Azure Data Lake Storage Gen2.

- [Comprendre les identités managées avec Azure HDInsight](hdinsight-managed-identities.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Instructions** : Si vous utilisez du code lié à votre déploiement Azure HDInsight, vous pouvez implémenter Credential Scanner pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault. 

- [Configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Défense contre les programmes malveillants](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 : Utiliser un logiciel anti-programme malveillant géré de manière centralisée

**Instructions** : Azure HDInsight est fourni avec Clamscan préinstallé et activé pour les images de nœud de cluster. Toutefois, vous devez gérer le logiciel et agréger/superviser manuellement tous les journaux générés par Clamscan.

- [Comprendre Clamscan pour Azure HDInsight](./hdinsight-faq.md#security-and-certificates)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Instructions** : Microsoft Antimalware est activé sur l’hôte sous-jacent qui prend en charge les services Azure, mais il ne s’exécute pas sur du contenu client.

Pré-analysez tous les fichiers téléchargés vers toute ressource Azure associée à votre déploiement de cluster Azure HDInsight, comme un stockage Data Lake Storage, un stockage Blob, etc. Microsoft ne peut pas accéder aux données client dans ces instances.

- [Présentation de Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles](../security/fundamentals/antimalware.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 : Vérifier que les logiciels et signatures anti-programme malveillant sont mis à jour

**Instructions** : Azure HDInsight est fourni avec Clamscan préinstallé et activé pour les images de nœud de cluster. Clamscan effectue automatiquement des mises à jour du moteur et des définitions. Toutefois, l’agrégation et la gestion des journaux devront être effectuées manuellement.

- [Comprendre Clamscan pour Azure HDInsight](./hdinsight-faq.md#security-and-certificates)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Garantir des sauvegardes automatiques régulières

**Conseil** : quand vous utilisez un compte Stockage Azure pour le magasin de données de cluster HDInsight, choisissez l’option de redondance appropriée (LRS, ZRS, GRS, RA-GRS).  Quand vous utilisez une base de données Azure SQL pour le magasin de données de cluster Azure HDInsight, configurez la géoréplication active.

- [Guide pratique pour configurer la redondance du stockage pour les comptes Stockage Azure](../storage/common/storage-redundancy.md)

- [Guide pratique pour configurer la redondance pour Azure SQL Database](../azure-sql/database/active-geo-replication-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés managées par le client

**Conseil** : quand vous utilisez un compte Stockage Azure pour le magasin de données de cluster Azure HDInsight, choisissez l’option de redondance appropriée (LRS, ZRS, GRS, RA-GRS). Si vous utilisez Azure Key Vault pour n’importe quelle partie de votre déploiement Azure HDInsight, vérifiez que vos clés sont sauvegardées.

- [Choisir les options de stockage pour votre cluster Azure HDInsight](hdinsight-hadoop-compare-storage-options.md)

- [Guide pratique pour configurer la redondance du stockage pour les comptes Stockage Azure](../storage/common/storage-redundancy.md)

- [Guide pratique pour sauvegarder des clés de coffre de clés dans Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Conseil** : si Azure Key Vault est utilisé avec votre déploiement Azure HDInsight, testez la restauration des clés gérées par le client sauvegardées.

- [Guide pratique pour apporter sa propre clé pour Apache Kafka sur Azure HDInsight](./disk-encryption.md)

- [Guide pratique pour restaurer des clés de coffre de clés dans Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés managées par le client

**Conseil** : si Azure Key Vault est utilisé avec votre déploiement Azure HDInsight, activez la suppression réversible dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante.

- [Guide pratique pour activer la suppression réversible dans Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Aide** : Développez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé. 

- [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Utiliser le guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de votre propre plan de réponse aux incidents](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

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

**Instructions** : Microsoft utilisera les informations de contact pour le signalement d’incidents de sécurité pour vous contacter si le centre de réponse aux incidents de sécurité Microsoft (MSRC, Microsoft Security Response Center) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé.

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

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](../security/benchmarks/overview.md)
- En savoir plus sur les [bases de référence de la sécurité Azure](../security/benchmarks/security-baselines-overview.md)
