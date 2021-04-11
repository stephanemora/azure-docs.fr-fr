---
title: Base de référence de sécurité Azure pour Azure Functions
description: La base de référence de sécurité Azure Functions fournit des instructions et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans le benchmark de sécurité Azure.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8987c4ac61e81c35f664e19dfc72936bb6754e18
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566952"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Base de référence de sécurité Azure pour Azure Functions

Cette base de référence de sécurité applique les instructions d’aide du [benchmark de sécurité Azure version 1.0](../security/benchmarks/overview-v1.md) à Azure Functions. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure.
Le contenu est regroupé selon les **contrôles de sécurité** définis par le benchmark de sécurité Azure et les instructions d’aide associées applicables à Azure Functions. Les **contrôles** non applicables à Azure Functions ont été exclus.

 
Pour voir comment Azure Functions est entièrement mappé au benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité Azure Functions](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Aide** : Intégrez vos applications Azure Functions à un réseau virtuel Azure. Les applications de fonction qui s’exécutent dans le plan Premium ont les mêmes capacités d’hébergement que les applications web dans Azure App Service, ce qui inclut la fonctionnalité d’intégration au réseau virtuel.  Les réseaux virtuels Azure vous permettent de placer un grand nombre de vos ressources Azure, par exemple Azure Functions, dans un réseau routable non Internet.

- [Guide pratique pour intégrer Azure Functions à un réseau virtuel Azure](functions-create-vnet.md)

- [Comprendre l’intégration au réseau virtuel pour Azure Functions et Azure App Service](../app-service/web-sites-integrate-with-vnet.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés.

**Définitions intégrées à Azure Policy - Microsoft.Web** :

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des interfaces réseau

**Aide** : Utilisez Azure Security Center et suivez les recommandations de protection de réseau pour sécuriser les ressources réseau et les configurations réseau liées à vos applications Azure Functions.

Si vous utilisez des groupes de sécurité réseau dans votre implémentation d’Azure Functions, activez les journaux de flux de groupes de sécurité réseau, puis envoyez-les vers un compte Stockage Azure pour des audits du trafic. Vous pouvez également envoyer ces journaux à un espace de travail Log Analytics et utiliser Traffic Analytics pour fournir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

- [Présentation de la sécurité réseau assurée par Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="13-protect-critical-web-applications"></a>1.3 : Protéger les applications web critiques

**Conseils** : Pour sécuriser complètement vos points de terminaison Azure Functions en production, envisagez d’implémenter l’une des options de sécurité suivantes au niveau de l’application de fonction :

- Activer l’authentification ou l’autorisation App Service pour votre application de fonction

- Utiliser la Gestion des API Azure (APIM) pour authentifier les requêtes

- Déployer l’application de fonction sur Azure App Service Environment.

De plus, vérifiez que le débogage à distance a été désactivé pour vos ressources Azure Functions de production. De plus, le mécanisme CORS (Cross-Origin Resource Sharing) ne doit pas autoriser tous les domaines à accéder à votre application de fonction dans Azure. Autorisez uniquement les domaines nécessaires à interagir avec votre application de fonction.

Déployez le pare-feu WAF (pare-feu d’applications web) dans le cadre de la configuration réseau pour bénéficier d’une inspection supplémentaire du trafic entrant. Activez le paramètre de diagnostic du WAF et ingérez les journaux dans un compte de stockage, un hub d'événements ou un espace de travail Log Analytics. 

- [Guide pratique pour sécuriser les points de terminaison Azure Functions en production](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Guide pratique pour déployer Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés.

**Définitions intégrées à Azure Policy - Microsoft.Web** :

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Conseils** : Activez le service Protection DDoS Standard sur les réseaux virtuels associés à vos applications de fonctions pour vous protéger contre les attaques DDoS. Utilisez les fonctionnalités de renseignement sur les menaces d’Azure Security Center Integrated pour refuser les communications avec les adresses IP publiques connues comme étant malveillantes ou inutilisées.

De plus, configurez une passerelle front-end, par exemple le pare-feu d’applications web Azure, pour authentifier toutes les requêtes entrantes et filtrer le trafic malveillant. Azure Web Application Firewall permet de sécuriser votre application de fonction en inspectant le trafic web entrant pour bloquer les attaques par injection de code SQL, les attaques XSS (scripts intersites), les attaques par chargement de programmes malveillants ainsi que les attaques DDoS. L’introduction d’un pare-feu d'applications web nécessite la fonctionnalité App Service Environment ou l’utilisation de points de terminaison privés. Vous pouvez utiliser des points de terminaison privés pour vos fonctions hébergées dans les plans Premium et App Service.

- [Options de mise en réseau d’Azure Functions](functions-networking-options.md)

- [Guide pratique pour configurer la protection DDoS](../ddos-protection/manage-ddos-protection.md)

- [Présentation de la fonctionnalité Threat Intelligence intégrée à Azure Security Center](../security-center/azure-defender.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Conseils** : Si vous utilisez un groupe de sécurité réseau dans votre implémentation d’Azure Functions, activez les journaux de flux de groupe de sécurité réseau, puis envoyez-les vers un compte de stockage pour un audit du trafic. Vous pouvez également envoyer les journaux vers un espace de travail Log Analytics et utiliser Traffic Analytics pour obtenir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

- [Tutoriel : journaliser le trafic réseau à destination et en provenance d’une machine virtuelle à l’aide du portail Azure](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Guide pratique pour activer Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions (IDS/IPS) basés sur le réseau

**Aide** : Configurez une passerelle front-end, par exemple le pare-feu d’applications web Azure, pour authentifier toutes les requêtes entrantes et filtrer le trafic malveillant. Le pare-feu d’applications web Azure permet de sécuriser vos applications Functions en inspectant le trafic web entrant pour bloquer les attaques par injection de code SQL, les attaques XSS (scripts intersites), les attaques par chargement de programmes malveillants ainsi que les attaques DDoS. L’introduction d’un pare-feu d'applications web nécessite la fonctionnalité App Service Environment ou l’utilisation de points de terminaison privés. Vous pouvez utiliser des points de terminaison privés pour vos fonctions hébergées dans les plans Premium et App Service.

Il existe également plusieurs options de place de marché, par exemple le pare-feu d'applications web Barracuda pour Azure, disponibles sur la Place de Marché Azure, qui incluent des fonctionnalités de détection et de prévention des intrusions.

- [Options de mise en réseau d’Azure Functions](functions-networking-options.md)

- [Plan Premium Azure Functions](functions-premium-plan.md)

- [Introduction aux environnements App Service](../app-service/environment/intro.md)

- [Considérations relatives à la mise en réseau pour un environnement App Service](../app-service/environment/network-info.md) 

- [Présentation du pare-feu d’applications web Azure](../web-application-firewall/overview.md)

- [Utilisation de points de terminaison privés pour Azure Functions](../app-service/networking/private-endpoint.md)

- [Présentation du service cloud WAF Barracuda](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="17-manage-traffic-to-web-applications"></a>1.7 : Gérer le trafic à destination des applications web

**Aide** : Configurez une passerelle front-end pour votre réseau, par exemple le pare-feu d’applications web Azure avec un chiffrement TLS de bout en bout. L’introduction d’un pare-feu d'applications web nécessite la fonctionnalité App Service Environment ou l’utilisation de points de terminaison privés. Vous pouvez utiliser des points de terminaison privés pour vos fonctions hébergées dans les plans Premium et App Service.

- [Options de mise en réseau d’Azure Functions](functions-networking-options.md)

- [Plan Premium Azure Functions](functions-premium-plan.md)

- [Introduction aux environnements App Service](../app-service/environment/intro.md)

- [Considérations relatives à la mise en réseau pour un environnement App Service](../app-service/environment/network-info.md) 

- [Présentation du pare-feu d’applications web Azure](../web-application-firewall/overview.md)

- [Guide pratique pour la configuration du chiffrement TLS de bout en bout avec Application Gateway et le portail](../application-gateway/end-to-end-ssl-portal.md)

- [Utilisation de points de terminaison privés pour Azure Functions](../app-service/networking/private-endpoint.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Conseils** : Utilisez des étiquettes de service de réseau virtuel pour définir des contrôles d’accès réseau sur un groupe de sécurité réseau ou sur le Pare-feu Azure. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de l’étiquette de service (par exemple AzureAppService) dans le champ Source ou Destination approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

- [Pour plus d’informations sur l’utilisation des étiquettes de service](../virtual-network/service-tags-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Aide** : Définissez et implémentez des configurations de sécurité standard pour les paramètres réseau liés à votre instance d’Azure Functions. Utilisez des alias Azure Policy dans les espaces de noms « Microsoft.Web » et « Microsoft.Network » pour créer des stratégies personnalisées d’audit ou d’application de la configuration réseau de vos applications Azure Functions. Vous pouvez également utiliser des définitions de stratégie intégrées pour Azure Functions, par exemple :

- CORS ne doit pas autoriser toutes les ressources à accéder à vos applications de fonction

- L’application de fonction doit uniquement être accessible via HTTPS

- La dernière version de TLS doit être utilisée dans votre application de fonction

Vous pouvez également utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant les artefacts d’environnement clés, tels que les modèles Resource Manager, le contrôle d’accès en fonction du rôle Azure (Azure RBAC) et les stratégies au sein d’une seule définition de blueprint. Vous pouvez facilement appliquer le blueprint aux nouveaux abonnements et environnements, et ajuster le contrôle et la gestion par le biais du contrôle de version.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour créer un blueprint Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Conseils** : Si vous utilisez un groupe de sécurité réseau dans votre implémentation d’Azure Functions, utilisez des étiquettes pour les NSG et autres ressources liées à la sécurité réseau et au flux de trafic. Utilisez le champ « Description » des règles de groupe de sécurité réseau individuelles afin de spécifier le besoin métier et/ou la durée, etc., pour toutes les règles qui autorisent le trafic vers et depuis un réseau.

Utilisez l’une des définitions de stratégie Azure intégrée en lien avec l’étiquetage, telles que « Exiger une étiquette et sa valeur » pour vous assurer que toutes les ressources créées sont étiquetées et être informé de l’existence de ressources non étiquetées.

Vous pouvez utiliser Azure PowerShell ou Azure CLI pour rechercher des ressources ou effectuer des actions sur des ressources en fonction de leurs étiquettes.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Aide** : Utilisez le journal d’activité Azure pour superviser les configurations des ressources réseau et détecter les changements apportés aux paramètres et ressources réseau liés à vos déploiements d’Azure Functions. Créez des alertes dans Azure Monitor, qui se déclenchent quand des changements sont apportés à des paramètres ou ressources réseau critiques. 

- [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Guide pratique pour créer des alertes dans Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Pour la journalisation d’audit du plan de contrôle, activez les paramètres de diagnostic des journaux d’activité Azure et envoyez les journaux vers un espace de travail Log Analytics, Event Hub ou un compte de stockage Azure pour archivage. À l’aide des données des journaux d’activité Azure, vous pouvez déterminer les éléments « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) effectuées au niveau du plan de contrôle pour vos ressources Azure.

Azure Functions est également intégré à Azure Application Insights pour la supervision des fonctions. Application Insights collecte des données de journal, de performances et d’erreur. Il détecte automatiquement les anomalies de performances et intègre de puissants outils d’analyse conçus pour aider à diagnostiquer les problèmes et à comprendre la manière dont vos fonctions sont utilisées.

Si vous disposez d’une journalisation de sécurité/d’audit personnalisée intégrée dans votre application de fonction, activez le paramètre de diagnostic « FunctionAppLogs », et envoyez les journaux à un espace de travail Log Analytics, un Azure Event Hub ou un compte de stockage Azure à des fins d’archivage. 

Vous pouvez également activer et intégrer des données à Azure Sentinel ou à une solution SIEM (Security Information and Event Management) de tiers. 

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

- [Guide pratique pour configurer Azure Functions avec Azure Application Insights](functions-monitoring.md)

- [Guide pratique pour activer les paramètres de diagnostic (journaux générés par l’utilisateur) pour Azure Functions](functions-monitor-log-analytics.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : Pour la journalisation d’audit du plan de contrôle, activez les paramètres de diagnostic des journaux d’activité Azure et envoyez les journaux vers un espace de travail Log Analytics, Event Hub ou un compte de stockage Azure pour archivage. À l’aide des données des journaux d’activité Azure, vous pouvez déterminer les éléments « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) effectuées au niveau du plan de contrôle pour vos ressources Azure.

Si vous disposez d’une journalisation de sécurité/d’audit personnalisée intégrée dans votre application de fonction, activez le paramètre de diagnostic « FunctionAppLogs », et envoyez les journaux à un espace de travail Log Analytics, un Azure Event Hub ou un compte de stockage Azure à des fins d’archivage. 

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

- [Guide pratique pour activer les paramètres de diagnostic (journaux générés par l’utilisateur) pour Azure Functions](functions-monitor-log-analytics.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés.

**Définitions intégrées à Azure Policy - Microsoft.Web** :

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4 : Collecter les journaux de sécurité des systèmes d’exploitation

**Aide** : Non applicable. Cette recommandation concerne les ressources de calcul IaaS.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Conseils** : Dans Azure Monitor, définissez la période de conservation des journaux pour les espaces de travail Log Analytics associés à vos applications de fonction conformément aux réglementations de conformité de votre organisation.

- [Guide pratique pour définir les paramètres de conservation des journaux](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Conseils** : Activez les paramètres de diagnostic du journal d’activité Azure ainsi que les paramètres de diagnostic de votre application de fonction, puis envoyez les journaux à un espace de travail Log Analytics. Effectuez des requêtes dans Log Analytics pour rechercher des termes, identifier des tendances, analyser des modèles et fournir de nombreux autres insights basés sur les données collectées.

Activez Application Insights pour vos applications de fonction afin de collecter les données de journal, de performances et d’erreur. Vous pouvez voir les données de télémétrie collectées par Application Insights dans le portail Azure.

Si vous disposez d’une journalisation de sécurité/d’audit personnalisée intégrée dans votre application de fonction, activez le paramètre de diagnostic « FunctionAppLogs », et envoyez les journaux à un espace de travail Log Analytics, un Azure Event Hub ou un compte de stockage Azure à des fins d’archivage. 

Vous pouvez également activer et intégrer des données à Azure Sentinel ou à une solution SIEM (Security Information and Event Management) de tiers.

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

- [Guide pratique pour activer les paramètres de diagnostic pour Azure Functions](functions-monitor-log-analytics.md)

- [Guide pratique pour configurer Azure Functions avec Azure Application Insights et voir les données de télémétrie](functions-monitoring.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Conseils** : Activez les paramètres de diagnostic du journal d’activité Azure ainsi que les paramètres de diagnostic de votre application de fonction, puis envoyez les journaux à un espace de travail Log Analytics. Effectuez des requêtes dans Log Analytics pour rechercher des termes, identifier des tendances, analyser des modèles et fournir de nombreux autres insights basés sur les données collectées. Vous pouvez créer des alertes basées sur vos requêtes d’espace de travail Log Analytics.

Activez Application Insights pour vos applications de fonction afin de collecter les données de journal, de performances et d’erreur. Vous pouvez voir les données de télémétrie collectées par Application Insights, et créer des alertes dans le portail Azure.

Vous pouvez également activer et intégrer des données à Azure Sentinel ou à une solution SIEM (Security Information and Event Management) de tiers.

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

- [Guide pratique pour activer les paramètres de diagnostic pour Azure Functions](functions-monitor-log-analytics.md)

- [Guide pratique pour activer Application Insights pour Azure Functions](./configure-monitoring.md#enable-application-insights-integration)

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

**Conseils** : L’accès au plan de contrôle par Azure Functions est contrôlé via Azure Active Directory (Azure AD). Azure AD n’intègre pas le concept des mots de passe par défaut.

L’accès au plan de données peut être contrôlé par plusieurs moyens, notamment les clés d’autorisation, les restrictions de réseau et la validation d’une identité Azure AD. Les clés d’autorisation sont utilisées par les clients qui se connectent à vos points de terminaison HTTP Azure Functions, et peuvent être regénérées à tout moment. Ces clés sont générées pour les nouveaux points de terminaison HTTP par défaut.

Plusieurs méthodes de déploiement sont disponibles pour les applications de fonction. Certaines d’entre elles peuvent tirer profit d’un ensemble d’informations d’identification générées. Passez en revue les méthodes de déploiement qui vont être utilisées pour votre application.

- [Sécuriser un point de terminaison HTTP](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Guide pratique pour obtenir et regénérer les clés d’autorisation](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Technologies de déploiement dans Azure Functions](functions-deployment-technologies.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseils** : Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration.

En outre, pour vous aider à suivre les comptes d’administration dédiés, vous pouvez utiliser des recommandations d’Azure Security Center ou des stratégies Azure intégrées, telles que les suivantes :

- Plusieurs propriétaires doivent être affectés à votre abonnement

- Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement

- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement

Des informations supplémentaires sont disponibles sur les liens référencés.

- [Utilisation d’Azure Security Center pour surveiller l’identité et l’accès](../security-center/security-center-identity-access.md)

- [Utilisation d’Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Conseils** : Dans la mesure du possible, utilisez l’authentification unique SSO d’Azure Active Directory (Azure AD) au lieu de configurer des informations d’identification autonomes individuelles pour l’accès aux données de votre application de fonction. Suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center. Implémentez l’authentification unique pour vos applications de fonction à l’aide de la fonctionnalité d’authentification/autorisation App Service.

- [Présentation de l’authentification et de l’autorisation dans Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Présentation de l’authentification SSO avec Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseils** : activez l’authentification multifacteur Azure Active Directory (Azure AD), puis suivez les recommandations relatives à la gestion des identités et des accès d’Azure Security Center.

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Aide** : utilisez des stations de travail à accès privilégié (PAW) avec l’authentification multifacteur configurée pour vous connecter à des ressources Azure et les configurer. 

- [En savoir plus sur les stations de travail à accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration

**Aide** : Utilisez Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pour générer des journaux et des alertes quand des activités suspectes ou potentiellement dangereuses se produisent dans l’environnement.

Utilisez également les détections de risque Azure AD pour visualiser les alertes et des rapports sur les comportements à risque des utilisateurs.

- [Déploiement de Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Présentation des détections de risques Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

**Conseils** : Utilisez des emplacements nommés avec accès conditionnel pour autoriser l’accès au Portail Azure uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.

- [Guide pratique pour configurer des emplacements nommés dans Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Conseils** : Utilisez Azure Active Directory (Azure AD) en tant que système central d’authentification et d’autorisation pour vos applications de fonction. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

- [Guide pratique pour configurer votre application de fonction afin d’utiliser la connexion Azure AD](../app-service/configure-authentication-provider-aad.md)

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

**Conseils** : Utilisez Azure Active Directory (Azure AD) en tant que système central d’authentification et d’autorisation pour vos applications de fonction. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

Vous avez accès aux activités de connexion Azure AD et aux sources des journaux des événements à risque et des audits, ce qui vous permet de les intégrer à Azure Sentinel ou à un outil SIEM tiers.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure AD et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Vous pouvez configurer les alertes de journal souhaitées dans un espace de travail Log Analytics.

- [Guide pratique pour configurer votre application de fonction afin d’utiliser la connexion Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Procédure d’intégration d’Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Conseils** : Utilisez Azure Active Directory (Azure AD) en tant que système central d’authentification et d’autorisation pour vos applications de fonction. Pour les écarts du comportement de connexion au compte sur le plan de contrôle (le Portail Azure), utilisez Azure AD Identity Protection et les fonctionnalités de détections de risques pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités d’utilisateur. Vous pouvez également ingérer des données dans Azure Sentinel pour approfondir votre examen.

- [Guide pratique pour afficher les connexions risquées Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Conseils** : Utilisez des étiquettes pour faciliter le suivi des ressources Azure qui stockent ou traitent des informations sensibles.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Les applications de fonction doivent être séparées par un réseau/sous-réseau virtuel et étiquetées de manière appropriée.

Vous pouvez également utiliser des points de terminaison privés pour effectuer l’isolement réseau. Un point de terminaison privé Azure est une interface réseau qui vous connecte de façon privée et sécurisée à un service (par exemple : point de terminaison HTTPS d’une application de fonction) basé sur Azure Private Link. Private Endpoint utilise une adresse IP privée de votre réseau virtuel, plaçant de fait le service dans votre réseau virtuel. Vous pouvez utiliser des points de terminaison privés pour vos fonctions hébergées dans les plans Premium et App Service.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

- [Options de mise en réseau d’Azure Functions](functions-networking-options.md)

- [Plan Premium Azure Functions](functions-premium-plan.md)

- [Présentation du point de terminaison privé](../private-link/private-endpoint-overview.md)

- [Utilisation de points de terminaison privés pour Azure Functions](../app-service/networking/private-endpoint.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Conseils** : Dans le portail Azure pour vos applications de fonction, sous « Fonctionnalités de la plateforme :  Réseaux : SSL », activez le paramètre « HTTPS uniquement », puis affectez à la version minimale de TLS la valeur 1.2.

- [Exiger HTTPS sur les applications de fonction](./security-concepts.md#require-https)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés.

**Définitions intégrées à Azure Policy - Microsoft.Web** :

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Aide** : Non disponible pour le moment. Les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas disponibles pour Azure Functions. Étiquetez des applications de fonction qui peuvent traiter des informations sensibles en tant que telles et implémenter une solution tierce si nécessaire à des fins de conformité.

Pour la plateforme sous-jacente qui est gérée par Microsoft, Microsoft traite tout le contenu client comme sensible et déploie d'importants efforts pour vous protéger contre la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6 : Utiliser le contrôle d’accès en fonction du rôle pour contrôler l’accès aux ressources

**Conseils** : Utilisez le contrôle d’accès en fonction du rôle Azure (RBAC Azure) pour gérer l’accès au plan de contrôle de l’application de fonction (portail Azure).

- [Comment configurer Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7 : Utiliser la protection contre la perte de données basée sur l’hôte pour appliquer le contrôle d’accès

**Aide** : Non applicable. Cette recommandation concerne les ressources de calcul IaaS.

Microsoft gère l’infrastructure sous-jacente d’Azure Functions, et a implémenté des contrôles stricts pour empêcher la perte ou la divulgation des données client.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Aide** : Quand vous créez une application de fonction, vous devez créer un compte de stockage Azure à usage général qui prend en charge le stockage Blob, File d’attente et Table, ou établir un lien vers un compte de ce type. Cela est dû au fait que Functions s’appuie sur Stockage Azure pour les opérations telles que la gestion des déclencheurs et la journalisation des exécutions de fonctions. Le stockage Azure chiffre toutes les données dans un compte de stockage au repos. Par défaut, les données sont chiffrées avec des clés managées par Microsoft Afin d’avoir un contrôle supplémentaire sur les clés de chiffrement, vous pouvez fournir des clés gérées par le client à utiliser pour le chiffrement des données de fichiers et d’objets blob. Ces clés doivent être présentes dans Azure Key Vault pour que l’application de fonction puisse accéder au compte de stockage.

- [Présentation des considérations relatives au stockage pour Azure Functions](storage-considerations.md)

- [Présentation du chiffrement du Stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Conseils** : Utilisez Azure Monitor avec le journal d’activité Azure pour créer des alertes quand des changements sont apportés aux applications de fonction en production, ainsi qu’à d’autres ressources critiques ou connexes.

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Conseils** : Adoptez une pratique DevSecOps pour vous assurer que vos applications de fonction sont sécurisées et qu’elles le restent autant que possible tout au long de leur cycle de vie. DevSecOps intègre l’équipe de sécurité de votre organisation et ses capacités dans vos pratiques DevOps, faisant ainsi de la sécurité une responsabilité partagée par tous les membres de l’équipe.

De plus, suivez les recommandations d’Azure Security Center sur la sécurisation de vos applications de fonction.

- [Guide pratique pour ajouter une validation de sécurité continue à votre pipeline CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Implémenter les recommandations d'évaluation des vulnérabilités d'Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes.

**Aide** : Microsoft effectue une gestion des vulnérabilités sur les systèmes sous-jacents qui prennent en charge Azure Functions. Toutefois, vous pouvez utiliser la gravité des recommandations dans Azure Security Center ainsi que le niveau de sécurité pour mesurer les risques dans votre environnement. Votre niveau de sécurité est basé sur le nombre de recommandations Security Center que vous avez appliquées pour atténuer les risques. Pour classer par ordre de priorité les recommandations à résoudre en premier, réfléchissez à la gravité de chacune d’entre elles.

- [Guide de référence des recommandations de sécurité](../security-center/recommendations-reference.md)

**Responsabilité** : Partagé

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

Des informations supplémentaires sont disponibles sur les liens référencés.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4 : Définir et tenir un inventaire des ressources Azure approuvées

**Aide** : Définissez des ressources Azure approuvées et des logiciels approuvés pour les ressources de calcul.

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

**Aide** : Définissez et implémentez des configurations de sécurité standard pour votre application de fonction avec Azure Policy. Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.Web » pour créer des stratégies personnalisées d’audit ou d’application de la configuration de vos applications de fonction. Vous pouvez également utiliser des définitions de stratégie intégrée, par exemple :

- Une identité managée doit être utilisée dans votre application de fonction

- Le débogage à distance doit être désactivé pour les applications de fonction

- L’application de fonction doit uniquement être accessible via HTTPS

Des informations supplémentaires sont disponibles sur les liens référencés.

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

**Aide** : Stockez et gérez les modèles ARM et les définitions de stratégie Azure personnalisées de manière sécurisée dans le contrôle de code source.

- [Qu’est-ce que l’infrastructure en tant que code](/azure/devops/learn/what-is-infrastructure-as-code)

- [Concevoir des workflows de type Policy as Code](../governance/policy/concepts/policy-as-code.md)

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentation Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Conseils** : Utilisez les définitions Azure Policy intégrées ainsi que les alias Azure Policy dans l’espace de noms « Microsoft.Web » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Conseils** : Utilisez les définitions Azure Policy intégrées ainsi que les alias Azure Policy dans l’espace de noms « Microsoft.Web » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. Utilisez une stratégie Azure [auditer], [refuser] et [déployer si elle n’existe pas] pour appliquer automatiquement des configurations pour vos ressources Azure.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Aide** : Utilisez les identités managées conjointement avec Azure Key Vault afin de simplifier et sécuriser la gestion des secrets pour vos applications cloud. Les identités managées permettent à votre application de fonction de s’authentifier auprès d’un service qui prend en charge l’authentification Azure Active Directory (Azure AD), notamment Key Vault, sans informations d’identification dans votre code.

- [Créer un coffre de clés](../key-vault/secrets/quick-create-portal.md)

- [Guide pratique pour utiliser des identités managées avec App Service et Azure Functions](../app-service/overview-managed-identity.md)

- [Comment s’authentifier auprès de Key Vault](../key-vault/general/authentication.md)

- [Comment attribuer une stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md)

- [Utiliser des références Key Vault pour App Service et Azure Functions](../app-service/app-service-key-vault-references.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Conseils** : Utilisez des identités managées pour fournir à votre application de fonction une identité gérée automatiquement dans Azure Active Directory (Azure AD). Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

- [Guide pratique pour utiliser des identités managées avec App Service et Azure Functions](../app-service/overview-managed-identity.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés.

**Définitions intégrées à Azure Policy - Microsoft.Web** :

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault. 

- [Configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Garantir des sauvegardes automatiques régulières

**Conseils** : Utilisez la fonctionnalité de sauvegarde et de restauration pour planifier des sauvegardes régulières de votre application. Les applications de fonction qui s’exécutent dans le plan Premium ont les mêmes capacités d’hébergement que les applications web dans Azure App Service, ce qui inclut la fonctionnalité Sauvegarde et restauration.

Utilisez également une solution de contrôle de code source telle qu’Azure Repos et Azure DevOps pour stocker et gérer votre code de manière sécurisée. Azure DevOps Services tire profit de nombreuses fonctionnalités du stockage Azure pour garantir la disponibilité des données en cas de défaillance matérielle, d’interruption de service ou de sinistre régional. De plus, l’équipe Azure DevOps suit des procédures pour protéger les données contre toute suppression accidentelle ou malveillante.

- [Sauvegarde de votre application dans Azure](../app-service/manage-backup.md)

- [Présentation de la disponibilité des données dans Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentation Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés managées par le client

**Conseils** : Utilisez la fonctionnalité de sauvegarde et de restauration pour planifier des sauvegardes régulières de votre application. Les applications de fonction qui s’exécutent dans le plan Premium ont les mêmes capacités d’hébergement que les applications web dans Azure App Service, ce qui inclut la fonctionnalité Sauvegarde et restauration. Sauvegardez les clés gérées par le client dans Azure Key Vault.

Utilisez également une solution de contrôle de code source telle qu’Azure Repos et Azure DevOps pour stocker et gérer votre code de manière sécurisée. Azure DevOps Services tire profit de nombreuses fonctionnalités du stockage Azure pour garantir la disponibilité des données en cas de défaillance matérielle, d’interruption de service ou de sinistre régional. De plus, l’équipe Azure DevOps suit des procédures pour protéger les données contre toute suppression accidentelle ou malveillante.

- [Sauvegarde de votre application dans Azure](../app-service/manage-backup.md)

- [Guide pratique pour sauvegarder des clés de coffre de clés dans Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Présentation de la disponibilité des données dans Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentation Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Aide** : Vérifiez périodiquement que vous pouvez effectuer une restauration à partir de la fonctionnalité de sauvegarde et de restauration. Si vous utilisez un autre emplacement hors connexion pour sauvegarder votre code, vérifiez périodiquement que vous pouvez effectuer des restaurations complètes. Testez la restauration des clés gérées par le client sauvegardées.

- [Restaurer une application dans Azure à partir d’une sauvegarde](../app-service/web-sites-restore.md)

- [Restaurer une application dans Azure à partir d’une capture instantanée](../app-service/app-service-web-restore-snapshots.md)

- [Guide pratique pour restaurer des clés de coffre de clés dans Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés managées par le client

**Aide** : Les sauvegardes effectuées à partir de la fonctionnalité de sauvegarde et de restauration utilisent un compte de stockage Azure dans votre abonnement. Le stockage Azure chiffre toutes les données dans un compte de stockage au repos. Par défaut, les données sont chiffrées avec des clés managées par Microsoft Afin d’avoir un contrôle supplémentaire sur les clés de chiffrement, vous pouvez fournir des clés gérées par le client à utiliser pour le chiffrement des données de stockage.

Si vous utilisez des clés gérées par le client, vérifiez que la suppression réversible est activée dans Key Vault pour protéger les clés contre toute suppression accidentelle ou malveillante.

- [Chiffrement du Stockage Azure au repos](../storage/common/storage-service-encryption.md)

- [Guide pratique pour activer la suppression réversible dans Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.

- [Comment configurer des automatisations de workflow dans Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Guide de gestion des incidents de sécurité informatique du NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Conseils** : Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements (par ex. production, non production) et créez un système d’attribution de noms pour identifier et classer les ressources Azure de façon claire.

**Responsabilité** : Partagé

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

**Aide** : Utilisez la fonctionnalité d’automatisation du workflow dans Azure Security Center pour déclencher automatiquement des réponses via « Logic Apps » aux alertes et recommandations de sécurité.

- [Comment configurer l’automatisation des workflows et Logic Apps](../security-center/workflow-automation.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : tests d’intrusion et exercices Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1 : Procéder régulièrement à des tests d’intrusion des ressources Azure et veiller à corriger tous les problèmes de sécurité critiques détectés

**Aide** : Suivez les règles d’engagement de Microsoft pour que vos tests d’intrusion soient conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft.

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](../security/benchmarks/overview.md)
- En savoir plus sur les [bases de référence de la sécurité Azure](../security/benchmarks/security-baselines-overview.md)
