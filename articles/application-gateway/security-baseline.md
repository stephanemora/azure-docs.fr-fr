---
title: Base de référence de sécurité Azure pour Application Gateway
description: La base de référence de sécurité Application Gateway fournit des instructions et des ressources pour l’implémentation des recommandations de sécurité spécifiées dans le benchmark de sécurité Azure.
author: msmbaldwin
ms.service: application-gateway
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4fc26d8e154dc0a58bb3436a8161d2e21efa2b86
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952012"
---
# <a name="azure-security-baseline-for-application-gateway"></a>Base de référence de sécurité Azure pour Application Gateway

Cette base de référence de sécurité applique les conseils du [benchmark de sécurité Azure version 1.0](../security/benchmarks/overview-v1.md) à Application Gateway. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure.
Le contenu est regroupé par les **contrôles de sécurité** définis par le benchmark de sécurité Azure et les conseils associés applicables à Application Gateway. Les **contrôles** non applicables à Application Gateway ont été exclus.

 
Pour voir comment Application Gateway est entièrement mappé au benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité Application Gateway](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Aide** : Assurez-vous qu'un groupe de sécurité réseau (NSG) a été appliqué à tous les déploiements de sous-réseaux du réseau virtuel Azure Application Gateway avec des contrôles d'accès réseau spécifiques aux ports et sources approuvés de votre application. Bien que les groupes de sécurité réseau soient pris en charge par Azure Application Gateway, certaines restrictions et exigences doivent être respectées pour que votre NSG et votre Azure Application Gateway fonctionnent comme prévu.

- [Comprendre les restrictions et les exigences concernant l’utilisation des NSG avec Azure Application Gateway](configuration-overview.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) pour les services associés.

**Définitions Azure Policy intégrées - Microsoft.Network** :

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des interfaces réseau

**Conseils** : Pour les groupes de sécurité réseau (NSG) associés à vos sous-réseaux Azure Application Gateway, activez les journaux de flux des groupes de sécurité réseau et envoyez les journaux dans un compte de stockage pour l’audit du trafic. Vous pouvez aussi envoyer ces journaux dans un espace de travail Log Analytics et utiliser Traffic Analytics pour obtenir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

Remarque : Dans certains cas, les journaux de flux du NSG associés à vos sous-réseaux Azure Application Gateway n’affichent pas le trafic autorisé. Si votre configuration correspond au scénario suivant, vous ne verrez pas le trafic autorisé dans vos journaux de flux NSG :

- Vous avez déployé Application Gateway v2
- Vous avez un NSG sur le sous-réseau Application Gateway
- Vous avez activé les journaux de flux NSG sur ce groupe de sécurité réseau

Pour plus d’informations, consultez les références ci-dessous.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Présentation de la sécurité réseau assurée par Azure Security Center](../security-center/security-center-network-recommendations.md)

- [FAQ pour les diagnostics et la journalisation pour Azure Application Gateway](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) pour les services associés.

**Définitions Azure Policy intégrées - Microsoft.Network** :

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3 : Protéger les applications web critiques

**Aide** : Déployez un pare-feu d'applications web (WAF) Azure devant les applications web critiques pour bénéficier d'un contrôle supplémentaire du trafic entrant. Le pare-feu d’applications web (WAF) est un service (fonctionnalité d’Azure Application Gateway) qui protège vos applications web de manière centralisée contre les vulnérabilités et les attaques courantes. Azure WAF permet de sécuriser vos applications web Azure App Service en inspectant le trafic web entrant afin de bloquer les attaques, telles que les injections de code SQL, le script de site à site, le chargement de programmes malveillants et les attaques DDoS. WAF suit des ensembles de règles de base OWASP (Open Web Application Security Project) 3.1 (WAF_v2 uniquement), 3.0 et 2.2.9. 

- [Comprendre les fonctionnalités d’Azure Application Gateway](features.md)

- [Comprendre Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Guide pratique pour déployer Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Aide** : Activez la protection standard DDoS sur vos réseaux virtuels Azure associés à vos instances de production d’Azure Application Gateway pour vous protéger des attaques DDoS. Utilisez la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center pour refuser les communications présentant des adresses IP connues comme étant malveillantes.

- [Guide pratique pour configurer la protection DDoS](../ddos-protection/manage-ddos-protection.md)

- [Présentation de la fonctionnalité Threat Intelligence intégrée à Azure Security Center](../security-center/azure-defender.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) pour les services associés.

**Définitions Azure Policy intégrées - Microsoft.Network** :

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Conseils** : Pour les groupes de sécurité réseau (NSG) associés à vos sous-réseaux Azure Application Gateway, activez les journaux de flux des groupes de sécurité réseau et envoyez les journaux dans un compte de stockage pour l’audit du trafic. Vous pouvez aussi envoyer ces journaux dans un espace de travail Log Analytics et utiliser Traffic Analytics pour obtenir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

Remarque : Dans certains cas, les journaux de flux du NSG associés à vos sous-réseaux Azure Application Gateway n’affichent pas le trafic autorisé. Si votre configuration correspond au scénario suivant, vous ne verrez pas le trafic autorisé dans vos journaux de flux NSG :

- Vous avez déployé Application Gateway v2
- Vous avez un NSG sur le sous-réseau Application Gateway
- Vous avez activé les journaux de flux NSG sur ce groupe de sécurité réseau

Pour plus d’informations, consultez les références ci-dessous.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Présentation de la sécurité réseau assurée par Azure Security Center](../security-center/security-center-network-recommendations.md)

- [FAQ pour les diagnostics et la journalisation pour Azure Application Gateway](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) pour les services associés.

**Définitions Azure Policy intégrées - Microsoft.Network** :

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions (IDS/IPS) basés sur le réseau

**Aide** : Déployez un pare-feu d'applications web (WAF) Azure devant les applications web critiques pour bénéficier d'un contrôle supplémentaire du trafic entrant. Le pare-feu d’applications web (WAF) est un service (fonctionnalité d’Azure Application Gateway) qui protège vos applications web de manière centralisée contre les vulnérabilités et les attaques courantes. Azure WAF permet de sécuriser vos applications web Azure App Service en inspectant le trafic web entrant afin de bloquer les attaques, telles que les injections de code SQL, le script de site à site, le chargement de programmes malveillants et les attaques DDoS. WAF suit des ensembles de règles de base OWASP (Open Web Application Security Project) 3.1 (WAF_v2 uniquement), 3.0 et 2.2.9. 

Il existe également plusieurs options de place de marché, par exemple le WAF Barracuda pour Azure, disponibles sur la Place de Marché Azure, qui incluent des fonctionnalités IDS/IPS.

- [Comprendre les fonctionnalités d’Azure Application Gateway](features.md)

- [Comprendre Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Guide pratique pour déployer Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Présentation du service cloud WAF Barracuda](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="17-manage-traffic-to-web-applications"></a>1.7 : Gérer le trafic à destination des applications web

**Aide** : Déployez Azure Application Gateway pour les applications web en activant le protocole HTTPS/SSL pour les certificats approuvés.

- [Guide pratique pour déployer Application Gateway](quick-create-portal.md)

- [Guide pratique pour configurer Application Gateway de façon à utiliser le protocole HTTPS](create-ssl-portal.md)

- [Présentation de l’équilibrage de charge de niveau 7 avec les passerelles d’applications web Azure](overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Aide** : Utilisez des balises de service de réseau virtuel pour définir des contrôles d’accès réseau sur les groupes de sécurité réseau ou le Pare-feu Azure. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de la balise de service (par exemple, GatewayManager) dans le champ Source ou Destination approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

Pour les groupes de sécurité réseau (NSG) associés à vos sous-réseaux Azure Application Gateway, vous devez autoriser le trafic Internet entrant sur les ports TCP 65503-65534 pour la référence (SKU) Application Gateway v1 et sur les ports TCP 65200-65535 pour la référence (SKU) v2 avec le sous-réseau de destination en tant que Any et la source en tant qu’étiquette de service GatewayManager. Cette plage de ports est nécessaire pour la communication avec l’infrastructure Azure. Ces ports sont protégés (verrouillés) par des certificats Azure. Les entités externes, y compris les clients de ces passerelles, ne peuvent pas communiquer avec ces points de terminaison.

- [Présentation et utilisation des balises de service](../virtual-network/service-tags-overview.md)

- [Présentation de la configuration d’Azure Application Gateway](configuration-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Conseils** : Définissez et implémentez des configurations de sécurité standard pour les paramètres réseau relatifs à vos déploiements d’Azure Application Gateway. Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.Network » pour créer des stratégies personnalisées d’audit ou d’application de la configuration réseau de vos passerelles Azure Application Gateway, de vos réseaux virtuels Azure et de vos groupes de sécurité réseau. Vous pouvez également utiliser une définition de stratégie intégrée.

Vous pouvez également utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant les artefacts d’environnement clés, tels que les modèles Azure Resource Manager, le contrôle d’accès en fonction du rôle Azure (Azure RBAC) et les stratégies au sein d’une seule définition de blueprint. Vous pouvez facilement appliquer le blueprint aux nouveaux abonnements et environnements, et ajuster le contrôle et la gestion par le biais du contrôle de version.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour créer un blueprint Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Aide** : Utilisez des étiquettes pour les groupes de sécurité réseau (NSG) associés à vos sous-réseaux Azure Application Gateway et autres ressources liées à la sécurité réseau et au flux de trafic. Concernant les règles NSG individuelles, utilisez le champ « Description » afin de spécifier le besoin métier et/ou la durée (etc.) pour toutes les règles qui autorisent le trafic vers/depuis un réseau.

Utilisez l’une des définitions de stratégie Azure intégrées en lien avec l’étiquetage, comme « Exiger une étiquette et sa valeur », pour garantir que toutes les ressources créées sont étiquetées et être informé de l’existence de ressources non étiquetées.

Vous pouvez utiliser Azure PowerShell ou Azure CLI pour rechercher des ressources ou effectuer des actions sur des ressources en fonction de leurs étiquettes.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

- [Guide pratique pour créer un réseau virtuel](../virtual-network/quick-create-portal.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Aide** : Utilisez le journal d’activité Azure pour superviser les configurations des ressources réseau et détecter les changements apportés aux paramètres et ressources réseau liés à vos déploiements d’Azure Application Gateway. Créez des alertes dans Azure Monitor qui se déclenchent lorsque des modifications sont apportées à des paramètres ou ressources réseau critiques.

- [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Guide pratique pour créer des alertes dans Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Pour la journalisation d’audit du plan de contrôle, activez les paramètres de diagnostic des journaux d’activité Azure et envoyez les journaux vers un espace de travail Log Analytics, Azure Event Hub ou un compte de stockage Azure. À l’aide des données du journal d’activité Azure, il vous est possible de déterminer « quoi, qui et quand » pour toutes les opérations d’écriture (PUT, POST, DELETE) effectuées au niveau du plan de contrôle pour votre passerelle Azure Application Gateway et les ressources associées, telles que les groupes de sécurité réseau (NSG), utilisées pour protéger les sous-réseaux Azure Application Gateway.

En plus de vos journaux d’activité, vous pouvez configurer les paramètres de diagnostic pour vos déploiements d’Azure Application Gateway. Les paramètres de diagnostic permettent de configurer l’exportation en continu des journaux et des métriques de la plateforme pour une ressource vers la destination de votre choix (Compte de stockage, Event Hubs et Log Analytics).

Azure Application Gateway est également intégré à Azure Application Insights. Application Insights collecte des données de journal, de performances et d’erreur. Application Insights détecte automatiquement les anomalies de performance et intègre de puissants outils d’analyse conçus pour aider à diagnostiquer les problèmes et à comprendre la manière dont vos applications web sont utilisées. Vous pouvez peut-être activer l’exportation continue pour exporter la télémétrie d’Application Insights vers un emplacement centralisé afin de garder les données plus longtemps que la période de rétention standard.

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

- [Procédure d’activation des paramètres de diagnostic pour Azure Application Gateway](application-gateway-diagnostics.md)

- [Procédure d’activation d’Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Comment configurer l’exportation continue](../azure-monitor/app/export-telemetry.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : Pour la journalisation d’audit du plan de contrôle, activez les paramètres de diagnostic des journaux d’activité Azure et envoyez les journaux vers un espace de travail Log Analytics, Azure Event Hub ou un compte de stockage Azure. À l’aide des données du journal d’activité Azure, il vous est possible de déterminer « quoi, qui et quand » pour toutes les opérations d’écriture (PUT, POST, DELETE) effectuées au niveau du plan de contrôle pour votre passerelle Azure Application Gateway et les ressources associées, telles que les groupes de sécurité réseau (NSG), utilisées pour protéger les sous-réseaux Azure Application Gateway.

En plus de vos journaux d’activité, vous pouvez configurer les paramètres de diagnostic pour vos déploiements d’Azure Application Gateway. Les paramètres de diagnostic permettent de configurer l’exportation en continu des journaux et des métriques de la plateforme pour une ressource vers la destination de votre choix (Compte de stockage, Event Hubs et Log Analytics).

Azure Application Gateway est également intégré à Azure Application Insights. Application Insights collecte des données de journal, de performances et d’erreur. Application Insights détecte automatiquement les anomalies de performance et intègre de puissants outils d’analyse conçus pour aider à diagnostiquer les problèmes et à comprendre la manière dont vos applications web sont utilisées. Vous pouvez peut-être activer l’exportation continue pour exporter la télémétrie d’Application Insights vers un emplacement centralisé afin de garder les données plus longtemps que la période de rétention standard.

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

- [Procédure d’activation des paramètres de diagnostic pour Azure Application Gateway](application-gateway-diagnostics.md)

- [Procédure d’activation d’Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Comment configurer l’exportation continue](../azure-monitor/app/export-telemetry.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Aide** : Dans Azure Monitor, définissez la période de conservation de votre espace de travail Log Analytics en fonction des obligations réglementaires de votre organisation. Utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage.

- [Définir les paramètres de conservation des journaux pour les espaces de travail Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Aide** : Activez les paramètres de diagnostic du journal d’activité Azure, ainsi que les paramètres de diagnostic de votre passerelle Azure Application Gateway, puis envoyez les journaux dans un espace de travail Log Analytics. Effectuez des requêtes dans Log Analytics pour rechercher des termes, identifier des tendances, analyser des modèles et fournir de nombreux autres insights basés sur les données collectées.

Utilisez Azure Monitor pour réseaux pour obtenir une vue complète de l’intégrité et des métriques pour toutes les ressources réseau déployées, y compris vos passerelles Azure Application Gateway. 

Vous pouvez éventuellement activer et intégrer les données dans Azure Sentinel ou une solution SIEM (Informations de sécurité et gestion d’événements) tierce. 

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

- [Procédure d’activation des paramètres de diagnostic pour Azure Application Gateway](application-gateway-diagnostics.md)

- [Utilisation d’Azure Monitor pour réseaux](../azure-monitor/insights/network-insights-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Aide** : Déployez un pare-feu d'applications web (WAF) v2 SKU Azure devant les applications web critiques pour bénéficier d'un contrôle supplémentaire du trafic entrant. Le pare-feu d’applications web (WAF) est un service (fonctionnalité d’Azure Application Gateway) qui protège vos applications web de manière centralisée contre les vulnérabilités et les attaques courantes. Azure WAF permet de sécuriser vos applications web Azure App Service en inspectant le trafic web entrant afin de bloquer les attaques, telles que les injections de code SQL, le script de site à site, le chargement de programmes malveillants et les attaques DDoS. WAF suit des ensembles de règles de base OWASP (Open Web Application Security Project) 3.1 (WAF_v2 uniquement), 3.0 et 2.2.9. 

Activez les paramètres de diagnostic du journal d’activité Azure pour votre Azure WAF et envoyez les journaux dans un espace de travail Log Analytics. Effectuez des requêtes dans Log Analytics pour rechercher des termes, identifier des tendances, analyser des modèles et fournir de nombreux autres insights basés sur les données collectées.  Vous pouvez créer des alertes en fonction de vos requêtes d’espace de travail Log Analytics.

Utilisez Azure Monitor pour réseaux pour obtenir une vue complète de l’intégrité et des métriques pour toutes les ressources réseau déployées, notamment vos passerelles Azure Application Gateway. Dans la console Azure Monitor pour réseaux, vous pouvez afficher et créer des alertes pour Azure Application Gateway.

- [Guide pratique pour déployer Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

- [Procédure d’activation des paramètres de diagnostic pour Azure Application Gateway](application-gateway-diagnostics.md)

- [Utilisation d’Azure Monitor pour réseaux](../azure-monitor/insights/network-insights-overview.md)

- [Guide pratique pour créer des alertes dans Azure](../azure-monitor/alerts/tutorial-response.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="28-centralize-anti-malware-logging"></a>2.8 : Centraliser la journalisation anti-programme malveillant

**Conseils** : Déployez un pare-feu d'applications web (WAF) v2 Azure devant les applications web critiques pour bénéficier d'un contrôle supplémentaire du trafic entrant. Le pare-feu d’applications web (WAF) est un service (fonctionnalité d’Azure Application Gateway) qui protège vos applications web de manière centralisée contre les vulnérabilités et les attaques courantes. Azure WAF permet de sécuriser vos applications web Azure App Service en inspectant le trafic web entrant afin de bloquer les attaques, telles que les injections de code SQL, le script de site à site, le chargement de programmes malveillants et les attaques DDoS.

Configurez les paramètres de diagnostic pour votre déploiement d’Azure Application Gateway. Les paramètres de diagnostic permettent de configurer l’exportation en continu des journaux et des métriques de la plateforme pour une ressource vers la destination de votre choix (Compte de stockage, Event Hubs et Log Analytics).

- [Guide pratique pour déployer Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Procédure de configuration des paramètres de diagnostic pour Azure WAF](application-gateway-diagnostics.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Aide** : Azure Active Directory (Azure AD) comporte des rôles intégrés qui doivent être explicitement attribués et qui peuvent être interrogés. Utilisez le module Azure AD PowerShell pour effectuer des requêtes ad hoc afin de découvrir les comptes membres de groupes d’administration.

- [Guide pratique pour obtenir un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Guide pratique pour obtenir les membres d’un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Conseils** : L’accès au plan de contrôle par Azure Application Gateway est contrôlé via Azure Active Directory (Azure AD). Azure AD n’intègre pas le concept des mots de passe par défaut.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseils** : Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration.

En outre, pour vous aider à suivre les comptes d’administration dédiés, vous pouvez utiliser des recommandations d’Azure Security Center ou des stratégies Azure intégrées, telles que les suivantes :

- Plusieurs propriétaires doivent être affectés à votre abonnement
- Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement

Pour plus d’informations, consultez les références ci-dessous.

- [Utilisation d’Azure Security Center pour superviser l’identité et l’accès (préversion)](../security-center/security-center-identity-access.md)

- [Utilisation d’Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Aide** : Utilisez une inscription d’application Azure (principal du service) afin de récupérer un jeton qui peut être utilisé pour interagir avec vos passerelles Azure Application Gateway via des appels d’API.

- [Appels d’API REST Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Guide pratique pour inscrire votre application cliente (principal de service) à l’aide d’Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informations sur l’API Azure Recovery Services](/rest/api/recoveryservices/)

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

**Aide** : Utilisez les rapports de sécurité Azure Active Directory (Azure AD) pour générer des journaux et des alertes quand une activité suspecte ou potentiellement dangereuse se produit dans l’environnement. Utiliser Azure Security Center pour superviser les activités liées aux identités et aux accès.

- [Guide pratique pour identifier les utilisateurs Azure AD pour lesquels une activité à risque a été signalée](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour superviser l’activité liée aux identités et aux accès des utilisateurs dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

**Aide** : Utilisez des emplacements nommés à accès conditionnel pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.

- [Guide pratique pour configurer des emplacements nommés dans Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Utiliser Azure Active Directory (Azure AD) comme système d’authentification et d’autorisation central. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

- [Création et configuration d’une instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Azure Active Directory (Azure AD) fournit des journaux pour vous aider à découvrir les comptes obsolètes. De plus, utilisez les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seules les utilisateurs appropriés continuent de bénéficier d’un accès.

- [Présentation des rapports Azure AD](../active-directory/reports-monitoring/index.yml)

- [Comment utiliser les révisions d’accès des identités Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Conseils** : Vous avez accès aux sources des journaux Activité de connexion, Audit et Événement à risque Azure Active Directory (Azure AD), qui permettent l’intégration à n’importe quel outil SIEM/de supervision.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure AD et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Vous pouvez configurer les alertes souhaitées dans un espace de travail Log Analytics.

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Aide** : Utilisez les fonctionnalités de détection de risque et de protection des identités d’Azure Active Directory (Azure AD) pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités d’utilisateur. Vous pouvez également ingérer des données dans Azure Sentinel pour approfondir votre examen.

- [Guide pratique pour afficher les connexions risquées Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Aide** : Utilisez des balises pour faciliter le suivi des ressources Azure qui stockent ou traitent des informations sensibles. 

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Assurez-vous qu'un groupe de sécurité réseau (NSG) a été appliqué à tous les déploiements de sous-réseaux du réseau virtuel Azure Application Gateway avec des contrôles d'accès réseau spécifiques aux ports et sources approuvés de votre application. Bien que les groupes de sécurité réseau soient pris en charge par Azure Application Gateway, certaines restrictions et exigences doivent être respectées pour que votre NSG et votre Azure Application Gateway fonctionnent comme prévu.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

- [Comprendre les restrictions et les exigences concernant l’utilisation des NSG avec Azure Application Gateway](configuration-overview.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

**Aide** : Assurez-vous qu'un groupe de sécurité réseau (NSG) a été appliqué à tous les déploiements de sous-réseaux du réseau virtuel Azure Application Gateway avec des contrôles d'accès réseau spécifiques aux ports et sources approuvés de votre application. Limitez le trafic sortant aux seuls emplacements approuvés afin d’atténuer la menace d’exfiltration de données. Bien que les groupes de sécurité réseau soient pris en charge par Azure Application Gateway, certaines restrictions et exigences doivent être respectées pour que votre NSG et votre Azure Application Gateway fonctionnent comme prévu.

- [Comprendre les restrictions et les exigences concernant l’utilisation des NSG avec Azure Application Gateway](configuration-overview.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Aide** : Configurez le chiffrement de bout en bout avec TLS pour vos passerelles Azure Application Gateway.

- [Procédure de configuration du chiffrement TLS de bout en bout avec Azure Application Gateway](end-to-end-ssl-portal.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6 : Utiliser le contrôle d’accès en fonction du rôle pour contrôler l’accès aux ressources

**Aide** : Utilisez le contrôle d’accès en fonction du rôle Azure (Azure RBAC) pour contrôler l’accès au plan de contrôle Azure Application Gateway (portail Azure).

- [Comment configurer Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Utilisez Azure Monitor avec le journal d’activité Azure pour créer des alertes en cas de changements apportés à des instances Azure Application Gateway de production, ainsi qu’à d’autres ressources critiques ou connexes.

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Aide** : Actuellement non disponible. L’évaluation des vulnérabilités dans Azure Security Center n’est pas encore disponible pour Azure Application Gateway.

La plateforme sous-jacente est analysée et corrigée par Microsoft. Examinez les contrôles de sécurité disponibles pour Azure Application Gateway afin de réduire les vulnérabilités liées à la configuration.

- [Couverture de fonctionnalité (notamment l’évaluation des vulnérabilités) pour les services PaaS Azure](../security-center/features-paas.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 : Comparer les analyses de vulnérabilités dos à dos

**Aide** : Actuellement non disponible. L’évaluation des vulnérabilités dans Azure Security Center n’est pas encore disponible pour Azure Application Gateway.

La plateforme sous-jacente est analysée et corrigée par Microsoft. Examinez les contrôles de sécurité disponibles pour Azure Application Gateway afin de réduire les vulnérabilités liées à la configuration.

- [Couverture de fonctionnalité (notamment l’évaluation des vulnérabilités) pour les services PaaS Azure](../security-center/features-paas.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes.

**Aide** : Actuellement non disponible. L’évaluation des vulnérabilités dans Azure Security Center n’est pas encore disponible pour Azure Application Gateway.

La plateforme sous-jacente est analysée et corrigée par Microsoft. Examinez les contrôles de sécurité disponibles pour Azure Application Gateway afin de réduire les vulnérabilités liées à la configuration.

- [Couverture de fonctionnalité (notamment l’évaluation des vulnérabilités) pour les services PaaS Azure](../security-center/features-paas.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Aide** : Utilisez Azure Resource Graph pour interroger/découvrir toutes les ressources (telles que calcul, stockage, réseau, ports et protocoles) dans vos abonnements. Vérifiez les autorisations (lecture) appropriées dans votre locataire et répertoriez tous les abonnements Azure, ainsi que les ressources dans vos abonnements.

Bien que les ressources Azure classiques puissent être découvertes via Resource Graph, il est vivement recommandé de créer et d’utiliser des ressources Azure Resource Manager à l’avenir.

- [Guide pratique pour créer des requêtes avec Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Guide pratique pour afficher ses abonnements Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Présentation d’Azure RBAC](../role-based-access-control/overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Conseils** : Appliquez des balises aux ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Aide** : Utilisez des étiquettes, des groupes d’administration et des abonnements distincts, le cas échéant, pour organiser et suivre les ressources Azure. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.

Appliquez également des restrictions quant au type de ressources pouvant être créées dans les abonnements clients, en utilisant la stratégie Azure avec les définitions intégrées suivantes :

- Types de ressources non autorisés
- Types de ressources autorisés

Pour plus d’informations, consultez les références ci-dessous.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Aide** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans vos abonnements. 

Utilisez Azure Resource Graph pour interroger/découvrir des ressources dans leurs abonnements.  Vérifiez que toutes les ressources Azure présentes dans l’environnement sont approuvées. 

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Guide pratique pour créer des requêtes avec Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Conseils** : Appliquez des restrictions quant au type de ressources pouvant être créées dans les abonnements clients, en utilisant Azure Policy avec les définitions intégrées suivantes :
- Types de ressources non autorisés
- Types de ressources autorisés

Pour plus d’informations, consultez les références ci-dessous.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Aide** : Configurez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ».

- [Configuration de l’accès conditionnel pour bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13 : Séparer physiquement ou logiquement des applications à risque élevé

**Aide** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Assurez-vous qu'un groupe de sécurité réseau (NSG) a été appliqué à tous les déploiements de sous-réseaux du réseau virtuel Azure Application Gateway avec des contrôles d'accès réseau spécifiques aux ports et sources approuvés de votre application. Bien que les groupes de sécurité réseau soient pris en charge par Azure Application Gateway, certaines restrictions et exigences doivent être respectées pour que votre NSG et votre Azure Application Gateway fonctionnent comme prévu.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

- [Comprendre les restrictions et les exigences concernant l’utilisation des NSG avec Azure Application Gateway](configuration-overview.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Aide** : Définissez et implémentez des configurations de sécurité standard pour les paramètres réseau relatifs à vos déploiements d’Azure Application Gateway. Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.Network » pour créer des stratégies personnalisées d’audit ou d’application de la configuration réseau de vos passerelles Azure Application Gateway, de vos réseaux virtuels Azure et de vos groupes de sécurité réseau. Vous pouvez également utiliser une définition de stratégie intégrée.

- [Affichage des alias Azure Policy disponibles](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Conseils** : Utilisez Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Présentation des effets d’Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Conseils** : Si vous utilisez des définitions de stratégie Azure personnalisées, utilisez Azure DevOps ou Azure Repos pour stocker et gérer votre code en toute sécurité.

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentation Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Aide** : Utilisez les définitions Azure Policy intégrées ainsi que les alias Azure Policy dans l’espace de noms « Microsoft.Network » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Aide** : Utilisez les définitions Azure Policy intégrées ainsi que les alias Azure Policy dans l’espace de noms « Microsoft.Network » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. Utilisez une stratégie Azure [auditer], [refuser] et [déployer si elle n’existe pas] pour appliquer automatiquement des configurations pour vos ressources Azure.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Conseils** : Utilisez des identités managées pour fournir à Azure Application Gateway une identité gérée automatiquement dans Azure Active Directory (Azure AD). Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

Utilisez Azure Key Vault pour stocker des certificats en toute sécurité. Azure Key Vault est un magasin de secrets managé par une plateforme, que vous pouvez utiliser pour protéger des secrets, des clés et des certificats SSL. Azure Application Gateway prend en charge l’intégration dans Key Vault des certificats de serveur associés à des écouteurs HTTPS. Cette prise en charge se limite à la référence SKU v2 d’Application Gateway.

- [Procédure de configuration de l’arrêt SSL avec les certificats du coffre de clés à l’aide d’Azure PowerShell](configure-keyvault-ps.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Conseils** : Utilisez des identités managées pour fournir à Azure Application Gateway une identité gérée automatiquement dans Azure Active Directory (Azure AD). Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

Utilisez Azure Key Vault pour stocker des certificats en toute sécurité. Azure Key Vault est un magasin de secrets managé par une plateforme, que vous pouvez utiliser pour protéger des secrets, des clés et des certificats SSL. Azure Application Gateway prend en charge l’intégration dans Key Vault des certificats de serveur associés à des écouteurs HTTPS. Cette prise en charge se limite à la référence SKU v2 d’Application Gateway.

- [Procédure de configuration de l’arrêt SSL avec les certificats du coffre de clés à l’aide d’Azure PowerShell](configure-keyvault-ps.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault. 

- [Configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Défense contre les programmes malveillants](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 : Utiliser un logiciel anti-programme malveillant géré de manière centralisée

**Aide** : Déployez un pare-feu d'applications web (WAF) Azure v2 devant les applications web critiques pour bénéficier d'un contrôle supplémentaire du trafic entrant. Le pare-feu d’applications web (WAF) est un service (fonctionnalité d’Azure Application Gateway) qui protège vos applications web de manière centralisée contre les vulnérabilités et les attaques courantes. Azure WAF permet de sécuriser vos applications web Azure App Service en inspectant le trafic web entrant afin de bloquer les attaques, telles que les injections de code SQL, le script de site à site, le chargement de programmes malveillants et les attaques DDoS.

Configurez les paramètres de diagnostic pour votre déploiement d’Azure Application Gateway. Les paramètres de diagnostic permettent de configurer l’exportation en continu des journaux et des métriques de la plateforme pour une ressource vers la destination de votre choix (Compte de stockage, Event Hubs et Log Analytics).

- [Guide pratique pour déployer Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Procédure de configuration des paramètres de diagnostic pour Azure WAF](application-gateway-diagnostics.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 : Vérifier que les logiciels et signatures anti-programme malveillant sont mis à jour

**Aide** : Lorsque vous utilisez le pare-feu d’applications web (WAF) Azure, vous pouvez configurer des stratégies WAF. Une stratégie WAF comporte deux types de règles de sécurité : des règles personnalisées, créées par le client, et des ensembles de règles managées, qui sont une collection d’ensembles de règles préconfigurées et managées par Azure. Les ensembles de règles managées par Azure fournissent un moyen simple de déployer une solution de protection contre diverses menaces de sécurité courantes. Dans la mesure où ces ensembles de règles sont managées par Azure, les règles sont mises à jour comme il convient pour vous protéger contre les nouvelles signatures d’attaque.

- [Comprendre les ensembles de règles WAF managés par Azure](../web-application-firewall/ag/ag-overview.md#waf-policy-and-rules)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Garantir des sauvegardes automatiques régulières

**Aide** : Azure Application Gateway ne stocke pas de données client. Toutefois, si vous utilisez des définitions de stratégie Azure personnalisées, utilisez Azure DevOps ou Azure Repos pour stocker et gérer votre code en toute sécurité.

Azure DevOps Services tire profit de nombreuses fonctionnalités du stockage Azure pour garantir la disponibilité des données en cas de défaillance matérielle, d’interruption de service ou de sinistre régional. De plus, l’équipe Azure DevOps suit des procédures pour protéger les données contre toute suppression accidentelle ou malveillante.

- [Présentation de la disponibilité des données dans Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentation Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés managées par le client

**Conseils** : Certificats de sauvegarde gérés par le client dans Azure Key Vault.

- [Procédure de sauvegarde de certificats du coffre de clés dans Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Conseils** : Testez la restauration des certificats gérés par le client sauvegardés.

- [Procédure de restauration des certificats du coffre de clés](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés managées par le client

**Conseils** : Assurez-vous que la suppression réversible est activée pour Azure Key Vault. La suppression réversible permet de récupérer des coffres de clés et des objets de coffre supprimés, tels que des clés, secrets et autres certificats.

- [Utilisation de la suppression réversible d’Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.

- [Comment configurer des automatisations de workflow dans Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Le client peut également tirer parti du guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de son propre plan de réponse aux incidents](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Conseils** : Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou à l’analytique utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements (par ex. production, non production) et créez un système d’attribution de noms pour identifier et classer les ressources Azure de façon claire.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Conseils** : Exécutez des exercices pour tester les fonctionnalités de réponse aux incidents de vos systèmes de façon régulière. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

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

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](../security/benchmarks/overview.md)
- En savoir plus sur les [bases de référence de la sécurité Azure](../security/benchmarks/security-baselines-overview.md)
