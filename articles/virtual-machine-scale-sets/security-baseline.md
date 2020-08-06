---
title: Base de référence de sécurité Azure pour Virtual Machine Scale Sets
description: La base de référence de sécurité Virtual Machine Scale Sets fournit des instructions pratiques et des ressources pour l’implémentation des recommandations de sécurité spécifiées dans la documentation Benchmark de sécurité Azure.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 6db83d17a9e6f3df6d691d6de616ead5407e3f5f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133042"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Base de référence de sécurité Azure pour Virtual Machine Scale Sets

La base de sécurité Azure pour Virtual Machine Scale Sets contient des recommandations qui vous aideront à améliorer la posture de sécurité de votre déploiement.

La base de référence pour ce service est tirée du [benchmark de sécurité Azure version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), qui fournit des recommandations sur la façon de sécuriser vos solutions cloud sur Azure en suivant nos conseils sur les bonnes pratiques.

Pour plus d’informations, consultez [Vue d’ensemble des lignes de base de sécurité Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sécurité du réseau

*Pour plus d’informations, consultez [Contrôle de sécurité : Sécurité réseau](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Aide** : Lorsque vous créez une machine virtuelle Azure, vous devez utiliser un réseau virtuel existant ou en créer un et configurer la machine virtuelle avec un sous-réseau. Veillez à ce qu’un groupe de sécurité réseau soit appliqué à tous les sous-réseaux déployés avec des contrôles d’accès réseau propres aux ports et sources approuvés de votre application.

Sinon, si vous avez un cas d’usage spécifique pour un pare-feu centralisé, Pare-feu Azure peut également être utilisé pour répondre à ces besoins.

* [Mise en réseau pour des groupes de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [Guide pratique pour créer un réseau virtuel](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Guide pratique pour déployer et configurer le Pare-feu Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des interfaces réseau

**Conseils** : Utilisez Azure Security Center pour identifier et suivre les recommandations de protection du réseau afin de sécuriser vos ressources Machines Virtuelles dans Azure. Activez les journaux de flux NSG et transférez-les dans un compte de stockage pour l’audit du trafic des machines virtuelles afin de détecter toute activité inhabituelle.

* [Guide pratique pour activer les journaux de flux NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Présentation de la sécurité réseau assurée par Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="13-protect-critical-web-applications"></a>1.3 : Protéger les applications web critiques

**Aide** : Si vous utilisez votre groupe de machines virtuelles identiques pour héberger des applications web, utilisez un groupe de sécurité réseau (NSG) sur le sous-réseau du groupe de machines virtuelles identiques pour limiter le trafic, les ports et les protocoles autorisés à communiquer. Suivez une approche réseau de moindre privilège lors de la configuration de vos NSG pour n’autoriser que le trafic nécessaire à votre application.

Vous pouvez également déployer un pare-feu d’applications web (WAF) Azure devant les applications web stratégiques pour bénéficier d’une vérification supplémentaire du trafic entrant. Activez le paramètre de diagnostic du WAF et ingérez les journaux dans un compte de stockage, un hub d'événements ou un espace de travail Log Analytics.

* [Mise en réseau pour des groupes de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [Créer une passerelle d’application avec un pare-feu d’applications web à l’aide du portail Azure](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-web-application-firewall-portal)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Aide** : Activez la protection DDoS (Distributed Denial of Service) Standard sur les réseaux virtuels pour vous protéger des attaques DDoS. La fonctionnalité Threat Intelligence intégrée à Azure Security Center vous permet de surveiller les communications dont les adresses IP sont connues pour être malveillantes. Configurez Pare-feu Azure sur chacun des segments de votre réseau virtuel, en activant la fonctionnalité Threat Intelligence et en la configurant sur « Alerter et refuser » en ce qui concerne le trafic malveillant.

Vous pouvez utiliser l’accès réseau juste-à-temps d’Azure Security Center pour limiter l’exposition des machines virtuelles Windows aux adresses IP approuvées pendant une période limitée. De plus, utilisez la fonctionnalité de renforcement du réseau adaptatif d’Azure Security Center pour recommander des configurations NSG qui brident les ports et les adresses IP sources en fonction du trafic réel et du renseignement sur les menaces.

* [Guide pratique pour configurer la protection DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Guide pratique pour déployer le Pare-feu Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Présentation de la fonctionnalité Threat Intelligence intégrée à Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

* [Présentation de la fonctionnalité de renforcement du réseau adaptatif d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

* [Présentation de la fonctionnalité de contrôle d’accès réseau juste-à-temps d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Aide** : Vous pouvez enregistrer des journaux de flux NSG dans un compte de stockage afin de générer des enregistrements de flux pour Machines virtuelles Microsoft Azure. Lors de la recherche d’activité anormale, vous avez la possibilité d’activer la capture de paquets Network Watcher afin de détecter les activités inhabituelles et inattendues dans le trafic réseau.

* [Guide pratique pour activer les journaux de flux NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Guide pratique pour activer Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention d’intrusion (IDS/IPS) basés sur le réseau

**Aide** : L’association des captures de paquets fournies par Network Watcher et d’un outil open source de systèmes de détection d’intrusions vous permet de détecter des intrusions sur le réseau pour un large éventail de menaces. Vous avez également la possibilité de déployer Pare-feu Azure sur les segments concernés du réseau virtuel, en activant la fonctionnalité Threat Intelligence et en la configurant sur « Alerter et refuser » en ce qui concerne le trafic malveillant.

* [Détection d’intrusion réseau avec Network Watcher et des outils open source](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools)

* [Guide pratique pour déployer le Pare-feu Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Guide pratique pour configurer des alertes avec le Pare-feu Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7 : Gérer le trafic à destination des applications web

**Aide** : Si vous utilisez un groupe de machines virtuelles identiques pour héberger des applications web, vous pouvez déployer Azure Application Gateway pour les applications web en activant le protocole HTTPS/SSL pour les certificats approuvés. Grâce à Azure Application Gateway, vous dirigez le trafic web de votre application vers des ressources spécifiques en assignant des écouteurs à des ports, en créant des règles et en ajoutant des ressources à un pool principal comme VMSS, etc.

* [Guide pratique pour déployer Application Gateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [Guide pratique pour configurer Application Gateway de façon à utiliser le protocole HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [Créer un groupe identique qui fait référence à une passerelle d’application](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

* [Présentation de l’équilibrage de charge de niveau 7 avec les passerelles d’applications web Azure](https://docs.microsoft.com/azure/application-gateway/overview)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Conseils** : Utilisez des balises de service de réseau virtuel pour définir des contrôles d’accès réseau sur les groupes de sécurité réseau ou le compte Pare-feu Azure configurés pour vos machines virtuelles Azure. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de la balise de service (par exemple, ApiManagement) dans le champ Source ou Destination approprié d'une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

* [Présentation et usage des balises de service](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Aide** : Définissez et implémentez des configurations de sécurité standard pour des groupes de machines virtuelles identiques Azure à l’aide d’Azure Policy. Vous pouvez également utiliser Azure Blueprints pour simplifier les déploiements de machines virtuelles Azure à grande échelle en regroupant les artefacts d’environnement clés, tels que les modèles Resource Manager, les attributions de rôle et les affectations Azure Policy, au sein d’une seule définition de blueprint. Vous pouvez appliquer le blueprint aux abonnements et activer la gestion des ressources par le biais du contrôle de version des blueprints.

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [En savoir plus sur les modèles de groupes de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-start)

* [Exemples Azure Policy pour le réseau](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [Guide pratique pour créer un blueprint Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Aide** : Vous pouvez utiliser des balises pour les groupes de sécurité réseau (NSG) et d’autres ressources relatives à la sécurité réseau et au flux de trafic configurées pour vos machines virtuelles Windows. Concernant les règles NSG individuelles, utilisez le champ « Description » afin de spécifier le besoin métier ou la durée pour toutes les règles qui autorisent le trafic vers/depuis un réseau.

* [Guide pratique pour créer et utiliser des étiquettes](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Guide pratique pour créer un réseau virtuel](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Aide** : Utilisez le journal d’activité Azure pour surveiller les modifications apportées aux configurations de ressources réseau associées à votre groupe de machines virtuelles identiques Azure. Créez des alertes dans Azure Monitor qui se déclenchent lorsque des modifications sont apportées à des paramètres ou ressources réseau critiques.

Utilisez Azure Policy pour valider (ou corriger) des configurations de ressources réseau relatives à un groupe de machines virtuelles identiques.

* [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Guide pratique pour créer des alertes dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Exemples Azure Policy pour le réseau](https://docs.microsoft.com/azure/governance/policy/samples/#network)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="logging-and-monitoring"></a>Enregistrement et surveillance

*Pour plus d’informations, consultez [Contrôle de sécurité : Journalisation et supervision](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1 : Utiliser des sources de synchronisation date/heure approuvées

**Aide** : Microsoft gère les sources temporelles pour les ressources Azure. Toutefois, vous avez la possibilité de gérer les paramètres de synchronisation date/heure pour vos machines virtuelles.

* [Guide pratique pour configurer la synchronisation date/heure pour les ressources de calcul Windows Azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

* [Guide pratique pour configurer la synchronisation date/heure pour les ressources de calcul Linux Azure](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Les journaux d’activité peuvent être utilisés pour auditer les opérations et les actions effectuées sur des ressources de groupes de machines virtuelles identiques. Le journal d’activité contient toutes les opérations d’écriture (PUT, POST, DELETE) de vos ressources, à l’exception des opérations de lecture (GET). Les journaux d’activité peuvent être utilisés pour rechercher une erreur lors de la résolution de problèmes ou pour surveiller la manière dont un utilisateur de votre organisation a modifié une ressource.

Vous pouvez activer des données de journal produites à partir de journaux d’activité Azure ou de ressources de machines virtuelles et les intégrer à Azure Sentinel ou un tiers SIEM pour la gestion centralisée des journaux de sécurité.

Utilisez Azure Security Center pour assurer la surveillance du journal des événements de sécurité pour les machines virtuelles Azure. Compte tenu du volume de données généré par le journal des événements de sécurité, elles ne sont pas stockées par défaut.

Si votre organisation souhaite conserver les données du journal des événements de sécurité de la machine virtuelle, elles peuvent être stockées dans un espace de travail Log Analytics au niveau de collecte de données souhaité, configuré dans Azure Security Center.

* [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Guide pratique pour intégrer Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Guide pratique pour bien démarrer avec Azure Monitor et l’intégration d’une solution SIEM tierce](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

* [Collecte de données dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Comment surveiller des machines virtuelles dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : Les journaux d’activité peuvent être utilisés pour auditer les opérations et les actions effectuées sur des ressources de groupes de machines virtuelles identiques. Le journal d’activité contient toutes les opérations d’écriture (PUT, POST, DELETE) de vos ressources, à l’exception des opérations de lecture (GET). Les journaux d’activité peuvent être utilisés pour rechercher une erreur lors de la résolution de problèmes ou pour surveiller la manière dont un utilisateur de votre organisation a modifié une ressource.

Activez la collecte des données de diagnostic du système d’exploitation invité en déployant l’extension de diagnostic sur vos machines virtuelles. Vous pouvez utiliser l’extension de diagnostic pour collecter des données de diagnostic telles que les journaux des applications ou les compteurs de performances à partir d’une machine virtuelle Azure.

Pour une visibilité avancée des applications et services pris en charge par le groupe de machines virtuelles identiques Azure, vous pouvez activer à la fois Azure Monitor pour machines virtuelles et Application Insights. Avec Application Insights, vous pouvez surveiller votre application et capturer de la télémétrie (requêtes HTTP, exceptions, etc.) pour pouvoir mettre en corrélation des problèmes entre les machines virtuelles et votre application.

* [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Affichage et récupération des événements du journal d’activité Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Comment surveiller des machines virtuelles dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [Vue d’ensemble d’Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4 : Collecter les journaux de sécurité des systèmes d’exploitation

**Aide** : Utilisez Azure Security Center pour assurer la surveillance du journal des événements de sécurité pour les machines virtuelles Azure. Compte tenu du volume de données généré par le journal des événements de sécurité, elles ne sont pas stockées par défaut.

Si votre organisation souhaite conserver les données du journal des événements de sécurité de la machine virtuelle, elles peuvent être stockées dans un espace de travail Log Analytics au niveau de collecte de données souhaité, configuré dans Azure Security Center.

* [Collecte de données dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Comment surveiller des machines virtuelles dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Aide** : Vérifiez que la période de conservation des journaux définie dans les comptes de stockage ou les espaces de travail Log Analytics utilisés pour le stockage des journaux des machines virtuelles est conforme aux obligations réglementaires de votre organisation.

* [Comment surveiller des machines virtuelles dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [Comment configurer la période de conservation d’un espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Aide** : Analysez et supervisez les journaux pour détecter les comportements anormaux et examinez régulièrement les résultats. Utilisez Azure Monitor pour examiner les journaux et effectuer des requêtes sur leurs données.

Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce pour surveiller et examiner vos journaux.

* [Guide pratique pour intégrer Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Présentation de l’espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Guide pratique pour effectuer des requêtes personnalisées dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Aide** : Utilisez Azure Security Center configuré avec un espace de travail Log Analytics pour superviser les activités anormales détectées dans les journaux de sécurité et les événements de vos machines virtuelles Azure et générer des alertes s’y rapportant.

Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce pour configurer les alertes relatives à une activité anormale.

* [Guide pratique pour intégrer Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Guide pratique pour gérer les alertes dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Guide pratique pour générer une alerte sur des données de journal Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8 : Centraliser la journalisation anti-programme malveillant

**Aide** : Vous pouvez utiliser Microsoft Antimalware pour Azure Cloud Services et Machines Virtuelles et configurer vos machines virtuelles Windows de manière à consigner les événements dans un compte de stockage Azure. Configurez un espace de travail Log Analytics de sorte qu’il ingère les événements des comptes de stockage et crée des alertes si nécessaire. Suivez les recommandations faites dans Azure Security Center : « Calcul et applications ». Pour les machines virtuelles Linux, vous aurez besoin d’un outil tiers pour la détection des vulnérabilités anti-programme malveillant.

* [Guide pratique pour configurer Microsoft Antimalware pour les services cloud et les machines virtuelles](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Procédure d’activation de l’analyse au niveau de l’invité pour les machines virtuelles](https://docs.microsoft.com/azure/cost-management/azure-vm-extended-metrics)

* [Instructions pour l’intégration de serveurs Linux à Azure Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-linux-computer)

* [Le lien suivant présente les directives de sécurité recommandées par Microsoft, qui peuvent servir de liste de critères à prendre en compte dans le choix du logiciel de détection des vulnérabilités.](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="29-enable-dns-query-logging"></a>2.9 : Activer la journalisation des requêtes DNS

**Conseils** : Implémentez une solution tierce de journalisation DNS à partir de la Place de marché Azure en fonction des besoins de votre organisation.

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10 : Activer l’enregistrement d’audit en ligne de commande

**Aide** : Azure Security Center fournit une surveillance du journal des événements de sécurité pour les machines virtuelles Azure. Security Center approvisionne Microsoft Monitoring Agent pour toutes les machines virtuelles Azure prises en charge et toutes celles nouvellement créées lorsque l’approvisionnement automatique est activé OU vous pouvez installer l’agent manuellement. L’agent active l’événement de création de processus 4688 et le champ CommandLine à l’intérieur de l’événement 4688. Les nouveaux processus créés sur la machine virtuelle sont enregistrés par le journal des événements et analysés par les services de détection Security Center.

En ce qui concerne les machines virtuelles Linux, vous pouvez configurer manuellement la journalisation de la console au niveau de chaque nœud et utiliser Syslog pour stocker les données. Exploitez également l’espace de travail Log Analytics d’Azure Monitor pour examiner les journaux et effectuer des requêtes sur les données Syslog des machines virtuelles Azure.

* [Collecte de données dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Guide pratique pour effectuer des requêtes personnalisées dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Sources de données Syslog dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Contrôle de sécurité : contrôle des accès et des identités](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Aide** : Si Azure Active Directory soit la méthode recommandée pour gérer l’accès des utilisateurs, les machines virtuelles Azure peuvent avoir des comptes locaux. Les comptes locaux et de domaine doivent être revus et gérés, normalement avec un encombrement minimal. Tirez également parti d’Azure AD Privileged Identity Management pour les comptes d’administration utilisés pour accéder aux ressources des machines virtuelles.

* [Informations relatives aux comptes locaux](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

* [Informations sur Privileged Identity Manager](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Aide** : Azure Virtual Machine Scale Sets et Azure Active Directory n’ont pas le concept de mots de passe par défaut. Le client est responsable des applications tierces et des services de marketplace susceptibles d’utiliser des mots de passe par défaut.

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Aide** : Créez des procédures de fonctionnement standard autour de l’utilisation de comptes d’administration dédiés ayant accès à vos machines virtuelles. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration. Les comptes Administrateur utilisés pour accéder aux ressources des machines virtuelles Azure peuvent également être gérés par Azure AD Privileged Identity Management (PIM). Azure AD Privileged Identity Management fournit plusieurs options, telles que l’élévation juste-à-temps, la nécessité d’une authentification multifacteur avant de recevoir un rôle et des options de délégation, pour que les autorisations soient disponibles uniquement pendant des périodes spécifiques et nécessitent l’accord d’un approbateur.

* [Présentation de l’identité et de l’accès Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Informations sur Privileged Identity Manager](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Conseils** : Dans la mesure du possible, utilisez l’authentification SSO avec Azure Active Directory plutôt que de configurer des informations d’identification autonomes individuelles par service. Suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center.

* [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

* [Guide pratique pour superviser les identités et les accès dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Instructions** : Activez Azure AD MFA et suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center.

* [Guide pratique pour activer l’authentification MFA dans Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Guide pratique pour superviser les identités et les accès dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6 : Utiliser des stations de travail sécurisées et gérées par Azure pour les tâches administratives

**Aide** : Utilisez des stations de travail avec accès privilégié avec l’authentification multifacteur (MFA) configurée pour la connexion aux ressources Azure et leur configuration.

* [En savoir plus sur les stations de travail à accès privilégié](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Guide pratique pour activer l’authentification MFA dans Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration

**Aide** : Utilisez Azure AD Privileged Identity Management (PIM) pour générer des journaux et des alertes quand des activités suspectes ou potentiellement dangereuses se produisent dans l’environnement. Utilisez les détections de risque Azure AD pour visualiser les alertes et des rapports sur les comportements à risque des utilisateurs. Le client a, s’il le souhaite, la possibilité d’ingérer les alertes de détection de risque Azure Security Center dans Azure Monitor et de configurer des alertes/notifications personnalisées à l’aide de groupes d’actions.

* [Déploiement de Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Présentation des détections de risques Azure Security Center (activité suspecte)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

* [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Configuration des groupes d’actions pour générer des alertes et des notifications personnalisées](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

**Aide** : Utilisez des emplacements nommés et des stratégies d’accès conditionnel Azure Active Directory pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.

* [Guide pratique pour configurer des emplacements nommés dans Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Utiliser Azure Active Directory (Azure AD) comme système d’authentification et d’autorisation central. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur. Vous pouvez utiliser des identités managées pour vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, notamment Key Vault, sans informations d’identification dans votre code. Votre code, qui s’exécute sur une machine virtuelle, peut utiliser son identité managée pour faire une demande de jetons d’accès pour les services qui prennent en charge l’authentification Azure AD.

* [Création et configuration d’une instance Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

* [Vue d’ensemble des identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Azure AD fournit des journaux pour vous aider à découvrir les comptes obsolètes. Par ailleurs, utilisez les révisions d’accès des identités Azure Active Directory pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès de l’utilisateur peut être évalué régulièrement pour vérifier que seuls les utilisateurs appropriés bénéficient d’un accès permanent. Lorsque vous utilisez des machines virtuelles Azure, vous devez examiner les groupes de sécurité et les utilisateurs locaux pour vous assurer qu’il n’existe pas de comptes inattendus susceptibles de compromettre le système.

* [Comment utiliser les révisions d’accès des identités Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Aide** : Configurez des paramètres de diagnostic pour Azure Active Directory afin d’envoyer les journaux d’audit et de connexion à un espace de travail Log Analytics. Utilisez également Azure Monitor pour examiner les journaux et effectuer des requêtes sur des données de journal à partir de machines virtuelles Azure.

* [Présentation de l’espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Guide pratique pour effectuer des requêtes personnalisées dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Comment surveiller des machines virtuelles dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Conseils** : Utilisez les fonctionnalités de protection des identités et contre les risques d’Azure Active Directory pour configurer des réponses automatiques aux actions suspectes détectées liées à vos ressources de compte de stockage. Vous devez activer des réponses automatisées via Azure Sentinel pour implémenter les réponses de sécurité de votre organisation.

* [Guide pratique pour afficher les connexions risquées Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Guide pratique pour intégrer Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

**Aide** : Dans les scénarios où Microsoft a besoin d’accéder aux données client (par exemple, lors d’une demande de support), utilisez Customer Lockbox pour que les machines virtuelles Azure examinent puis approuvent ou rejettent les demandes d’accès aux données client.

* [Présentation de Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="data-protection"></a>Protection de données

*Pour plus d’informations, consultez [Contrôle de sécurité : Protection des données](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Aide** : Utilisez des balises pour faciliter le suivi des machines virtuelles Azure qui stockent ou traitent des informations sensibles.

* [Guide pratique pour créer et utiliser des étiquettes](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Les ressources doivent être séparées par un réseau virtuel ou un sous-réseau, étiquetées de manière appropriée et sécurisées au sein d’un groupe de sécurité réseau (NSG) ou par un pare-feu Azure. Pour les machines virtuelles qui stockent ou traitent des données sensibles, implémentez la stratégie et les procédures pour les désactiver lorsqu’elles ne sont pas utilisées.

* [Guide pratique pour créer des abonnements Azure supplémentaires](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Guide pratique pour créer des groupes d’administration](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Guide pratique pour créer et utiliser des étiquettes](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Guide pratique pour créer un réseau virtuel](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Guide pratique pour déployer le Pare-feu Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Configuration des options « Alerter » et « Alerter et refuser » du pare-feu Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

**Conseils** : Implémentez une solution tierce sur les périmètres du réseau qui surveille le transfert non autorisé d’informations sensibles et bloque ces transferts tout en alertant les professionnels de la sécurité des informations.

Pour la plateforme sous-jacente, gérée par Microsoft, Microsoft traite tout le contenu client comme sensible et assure une protection contre la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

* [Présentation de la protection des données client dans Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Aide** : Les données en transit vers, depuis et entre les machines virtuelles qui exécutent Windows sont chiffrées de plusieurs façons, selon la nature de la connexion, par exemple lors de la connexion à une machine virtuelle dans une session RDP ou SSH.

Microsoft utilise le protocole TLS (Transport Layer Security) pour protéger les données lorsqu’elles sont en déplacement entre les services cloud et les clients.

* [Chiffrement en transit sur des machines virtuelles](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Partagé

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Aide** : Utilisez un outil de découverte actif tiers pour identifier toutes les informations sensibles stockées, traitées ou transmises par les systèmes technologiques de l’organisation, qu’elles soient situées sur site ou chez un fournisseur de services distant, et mettez à jour l’inventaire des informations sensibles de l’organisation.

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6 : Utiliser le contrôle d’accès en fonction du rôle pour contrôler l’accès aux ressources

**Conseils** : Grâce au contrôle d’accès en fonction du rôle (RBAC), vous pouvez séparer les tâches au sein de votre équipe et n’accorder aux utilisateurs que les accès à votre machine virtuelle dont ils ont besoin pour accomplir leur travail. Plutôt que de donner à tous des autorisations illimitées sur la machine virtuelle, vous pouvez autoriser uniquement certaines actions. Vous pouvez configurer le contrôle d’accès pour la machine virtuelle dans le portail Azure, à l’aide d’Azure CLI ou d’Azure PowerShell.

* [Contrôle d’accès en fonction du rôle (RBAC) pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

* [Rôles intégrés Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7 : Utiliser la protection contre la perte de données basée sur l’hôte pour appliquer le contrôle d’accès

**Aide** : Implémentez un outil tiers, tel qu’une solution automatisée de prévention contre la perte de données basée sur l’hôte, pour appliquer des contrôles d’accès afin d’atténuer le risque de violations de données.

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Aide** : Les disques virtuels des machines virtuelles sont chiffrés au repos à l’aide d’un chiffrement côté serveur ou d’Azure Disk Encryption (ADE). Azure Disk Encryption utilise la fonctionnalité DM-Crypt de Linux pour chiffrer les disques managés avec des clés gérées par le client au sein de la machine virtuelle invitée. Le chiffrement côté serveur avec des clés gérées par le client améliore l’utilisation de Azure Disk Encryption en vous permettant d’utiliser des types et des images de système d’exploitation pour vos machines virtuelles en chiffrant les données dans le service de stockage.

* [Azure Disk Encryption pour les groupes de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Utilisez Azure Monitor avec le journal d’activité Azure pour créer des alertes qui se déclenchent lorsque des modifications sont apportées à des groupes de machines virtuelles identiques et aux ressources associées.

* [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Journalisation Azure Storage Analytics](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Contrôle de sécurité : Gestion des vulnérabilités](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Conseils** : Suivez les recommandations d’Azure Security Center en matière d’évaluation des vulnérabilités sur vos machines virtuelles Azure. Utilisez la solution de sécurité Azure recommandée ou tierce pour effectuer des évaluations de vulnérabilités pour vos machines virtuelles.

* [Implémenter les recommandations d'évaluation des vulnérabilités d'Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 : Déployer une solution de gestion des correctifs de système d’exploitation automatisée

**Aide** : Activez les mises à niveau automatiques du système d’exploitation pour les versions de système d’exploitation prises en charge ou pour les images personnalisées stockées dans Shared Image Gallery.

* [Mises à niveau automatiques du système d’exploitation pour les groupes de machines virtuelles identiques dans Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3 : Déployer une solution de gestion automatisée des correctifs des logiciels tiers

**Aide** : Les groupes de machines virtuelles identiques Azure peuvent utiliser la mise à niveau automatique des images du système d’exploitation. Vous pouvez utiliser l’extension Azure Desired State Configuration (DSC) pour les machines virtuelles sous-jacentes du groupe de machines virtuelles identiques. L’extension DSC est utilisée pour configurer les machines virtuelles à mesure de leur mise en ligne afin qu’elles exécutent le logiciel souhaité.

* [Utilisation des groupes identiques de machines virtuelles avec l’extension DSC Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-dsc)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 : Comparer les analyses de vulnérabilités dos à dos

**Conseils** : Exportez les résultats de l’analyse à intervalles réguliers et comparez les résultats pour vérifier que les vulnérabilités ont été corrigées. Lorsqu’ils utilisent les recommandations de gestion des vulnérabilités suggérées par Azure Security Center, les clients peuvent basculer vers le portail de la solution sélectionnée pour afficher les données d’analyse historiques.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes.

**Conseils** : Utilisez les évaluations de risques par défaut (degré de sécurisation) fournies par Azure Security Center.

* [Présentation du degré de sécurisation Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Contrôle de sécurité : Gestion des stocks et des ressources](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Aide** : Utilisez Azure Resource Graph pour interroger et découvrir toutes les ressources (y compris les machines virtuelles) au sein de vos abonnements. Vérifiez que vous disposez des autorisations (en lecture) appropriées dans votre locataire et pouvez répertorier tous les abonnements Azure ainsi que les ressources qu’ils contiennent.

* [Guide pratique pour créer des requêtes avec Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Guide pratique pour afficher ses abonnements Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Présentation d’Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Aide** : Appliquez des balises aux ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories.

* [Guide pratique pour créer et utiliser des étiquettes](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Aide** : Utilisez des balises, des groupes d’administration, voire des abonnements distincts, pour organiser et suivre les groupes de machines virtuelles identiques et les ressources associées. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.

* [Guide pratique pour créer des abonnements Azure supplémentaires](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Guide pratique pour créer des groupes d’administration](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Guide pratique pour créer et utiliser des étiquettes](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4 : Définir et tenir un inventaire des ressources Azure approuvées

**Aide** : Créez un inventaire des ressources Azure et logiciels approuvés pour les ressources de calcul en fonction des besoins de votre organisation.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Instructions** : Appliquez des restrictions quant au type de ressources pouvant être créées dans les abonnements clients, en utilisant une stratégie Azure avec les définitions intégrées suivantes :
- Types de ressources non autorisés
- Types de ressources autorisés

Utilisez également Azure Resource Graph pour interroger/découvrir des ressources dans les abonnements. Cela peut être utile dans les environnements de haute sécurité, tels que ceux dotés de comptes de stockage.

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Guide pratique pour créer des requêtes avec Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6 : Analyser les applications logicielles non approuvées dans des ressources de calcul

**Aide** : Azure Automation permet de contrôler totalement le déploiement, les opérations et la désaffectation des charges de travail et des ressources. Exploitez l’inventaire des machines virtuelles Azure pour automatiser la collecte d’informations sur tous les logiciels présents sur les machines virtuelles. Remarque : Le nom, la version, l’éditeur et l’heure d’actualisation du logiciel sont disponibles sur le portail Azure. Pour avoir accès à la date d’installation et à d’autres informations, le client a demandé d’activer les diagnostics au niveau de l’invité et de placer les journaux des événements Windows dans un espace de travail Log Analytics.

Actuellement, les contrôles d’application adaptatifs ne sont pas disponibles pour les groupes de machines virtuelles identiques.

* [Présentation d’Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

* [Procédure d’activation de l’inventaire des machines virtuelles Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 : Supprimer des ressources et applications logicielles Azure non approuvées

**Aide** : Azure Automation permet de contrôler totalement le déploiement, les opérations et la désaffectation des charges de travail et des ressources. Vous pouvez utiliser Change Tracking pour identifier tous les logiciels installés sur des machines virtuelles. Vous pouvez implémenter votre propre processus ou utiliser la configuration de l’état Azure Automation pour supprimer les logiciels non autorisés.

* [Présentation d’Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

* [Suivre les changements dans votre environnement avec la solution Change Tracking](https://docs.microsoft.com/azure/automation/change-tracking)

* [Présentation d'Azure Automation State Configuration](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="68-use-only-approved-applications"></a>6.8 : Utiliser des applications approuvées uniquement

**Aide** : Actuellement, les contrôles d’application adaptatifs ne sont pas disponibles pour les groupes de machines virtuelles identiques. Utilisez un logiciel tiers pour contrôler l’utilisation des applications approuvées uniquement.

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Instructions** : Appliquez des restrictions quant au type de ressources pouvant être créées dans les abonnements clients, en utilisant une stratégie Azure avec les définitions intégrées suivantes :
- Types de ressources non autorisés
- Types de ressources autorisés

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10 : Tenir un inventaire des titres de logiciels approuvés

**Conseils** : Actuellement, les contrôles d’application adaptatifs ne sont pas disponibles pour les groupes de machines virtuelles identiques. Implémentez une solution tierce si cela ne répond pas aux exigences de votre organisation.

* [Guide pratique pour utiliser les contrôles d’application adaptatifs Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Aide** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ».

* [Configuration de l’accès conditionnel pour bloquer l’accès à Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12 : Limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul

**Aide** : Selon le type de script, vous pouvez utiliser des configurations de système d’exploitation spécifiques ou des ressources tierces pour limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul Azure.

* [Guide pratique pour contrôler l’exécution des scripts PowerShell dans des environnements Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13 : Séparer physiquement ou logiquement des applications à risque élevé

**Aide** : Les applications à haut risque déployées dans votre environnement Azure peuvent être isolées à l’aide d’un réseau virtuel, d’un sous-réseau, d’abonnements, de groupes d’administration, etc. et suffisamment sécurisées avec un pare-feu Azure, un pare-feu d’applications web (WAF) ou un groupe de sécurité réseau.

* [Réseaux virtuels et machines virtuelles dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [Présentation du Pare-feu Azure](https://docs.microsoft.com/azure/firewall/overview)

* [Présentation du pare-feu d’applications web](https://docs.microsoft.com/azure/web-application-firewall/overview)

* [Vue d’ensemble de la sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview)

* [Vue d’ensemble de Réseau virtuel Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

* [Organiser vos ressources avec des groupes d’administration Azure](https://docs.microsoft.com/azure/governance/management-groups/overview)

* [Guide de décision concernant les abonnements](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Contrôle de sécurité : Configuration sécurisée](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Aide** : Utilisez Azure Policy ou Azure Security Center pour gérer les configurations de sécurité pour toutes les ressources Azure. Par ailleurs, Azure Resource Manager a la possibilité d’exporter le modèle au format JSON (JavaScript Object Notation), qui doit être examiné pour vérifier que les configurations répondent/dépassent les exigences de votre entreprise en matière de sécurité.

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informations sur le téléchargement du modèle de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2 : Établir des configurations sécurisées du système d’exploitation

**Aide** : Utilisez la recommandation « Corriger les vulnérabilités dans les configurations de sécurité sur vos machines virtuelles » d’Azure Security Center pour gérer les configurations de sécurité sur toutes les ressources de calcul.

* [Suivi des recommandations d'Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [Guide pratique pour corriger les recommandations Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Aide** : Utilisez des modèles Resource Manager et des stratégies Azure pour configurer de manière sécurisée les ressources Azure associées aux groupes de machines virtuelles identiques. Les modèles Resource Manager sont des fichiers JSON servant à déployer une machine virtuelle et ses ressources Azure ; un modèle personnalisé devra donc être conservé. Microsoft effectue la maintenance sur les modèles de base. Utilisez Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure.

* [Informations sur la création de modèles Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Présentation des effets d’Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4 : Préserver la sécurité des configurations du système d'exploitation

**Aide** : Il existe plusieurs options permettant de maintenir une configuration sécurisée pour le déploiement de machines virtuelles Azure :

1. via les modèles Azure Resource Manager : Il s’agit de fichiers JSON utilisés pour déployer une machine virtuelle à partir du portail Azure et un modèle personnalisé doit être conservé. Microsoft effectue la maintenance sur les modèles de base.

2. Disque dur virtuel (VHD) personnalisé : il peut être nécessaire dans certains cas d’utiliser des fichiers VHD personnalisés, par exemple, pour traiter des environnements complexes non gérables par d’autres moyens.

3. Azure Automation State Configuration : une fois le système d’exploitation de base déployé, vous pouvez l’utiliser pour bénéficier d’un contrôle plus granulaire des paramètres et l’appliquer à l’aide de l’infrastructure Automation.

Pour la plupart des scénarios, les modèles de machine virtuelle de base de Microsoft combinés à Azure Automation Desired State Configuration peuvent permettre de satisfaire les exigences de sécurité.

* [Informations sur le téléchargement du modèle de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

* [Informations sur la création de modèles ARM](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Guide pratique pour charger un disque dur virtuel de machine virtuelle personnalisé dans Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Utilisez Azure DevOps pour stocker et gérer de manière sécurisée votre code, comme les stratégies Azure personnalisées, les modèles Resource Manager, les scripts Desired State Configuration, etc.  Pour accéder aux ressources que vous gérez dans Azure DevOps, telles que votre code, vos builds et le suivi de vos travaux, vous devez disposer d’autorisations pour ces ressources spécifiques. La plupart des autorisations sont accordées par le biais de groupes de sécurité intégrés, comme décrit dans Autorisations et accès. Vous pouvez octroyer ou refuser des autorisations à des utilisateurs spécifiques, à des groupes de sécurité intégrés ou à des groupes définis dans Azure Active Directory (Azure AD) s’ils sont intégrés à Azure DevOps ou dans Active Directory s’ils sont intégrés à TFS.

* [Stocker du code dans Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [À propos des autorisations et des groupes dans Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6 Stocker en toute sécurité des images de système d’exploitation personnalisées

**Aide** : Si vous avez recours à des images personnalisées (par exemple, un disque dur virtuel), utilisez le contrôle d’accès en fonction du rôle Azure pour veiller à ce que seuls les utilisateurs autorisés aient accès aux images.

* [Contrôle d'accès en fonction du rôle dans Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

* [Guide pratique pour configurer le contrôle RBAC dans Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Conseils** : Tirez parti d’Azure Policy pour alerter, auditer et appliquer des configurations système pour vos machines virtuelles. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8 : Déployer des outils de gestion de la configuration pour les systèmes d'exploitation

**Conseils** : Azure Automation State Configuration est un service de gestion de la configuration pour les nœuds DSC dans n’importe quel cloud ou centre de données local. Il permet de faire évoluer des milliers d’ordinateurs rapidement et facilement à partir d’un emplacement central et sécurisé. Vous pouvez facilement intégrer des machines, leur affecter des configurations déclaratives et afficher des rapports montrant la conformité de chaque machine à l’état souhaité que vous avez spécifié.

* [Intégration des machines pour la gestion avec Azure Automation State Configuration](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Aide** : Tirez parti d’Azure Security Center pour effectuer des analyses de ligne de base pour vos machines virtuelles Azure. Il existe d’autres méthodes de configuration automatisée, notamment Azure Automation State Configuration.

* [Corriger les recommandations dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

* [Prise en main d’Azure Automation State Configuration](https://docs.microsoft.com/azure/automation/automation-dsc-getting-started)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 : Implémenter la surveillance de la configuration automatique pour les systèmes d’exploitation

**Aide** : Azure Automation State Configuration est un service de gestion de la configuration pour les nœuds DSC dans n’importe quel cloud ou centre de données local. Il permet de faire évoluer des milliers d’ordinateurs rapidement et facilement à partir d’un emplacement central et sécurisé. Vous pouvez facilement intégrer des machines, leur affecter des configurations déclaratives et afficher des rapports montrant la conformité de chaque machine à l’état souhaité que vous avez spécifié.

* [Intégration des machines pour la gestion avec Azure Automation State Configuration](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Conseils** : Utilisez Managed Service Identity conjointement avec Azure Key Vault pour simplifier et sécuriser la gestion des secrets pour vos applications Cloud.

* [Intégration aux identités managées Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [Créer un coffre de clés](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [Fournir une authentification Key Vault avec une identité managée](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Aide** : Utilisez des identités managées pour fournir aux services Azure une identité gérée automatiquement dans Azure AD. Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

* [Configurer des identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault.

* [Configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Contrôle de sécurité : Défense contre les programmes malveillants](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 : Utiliser un logiciel anti-programme malveillant géré de manière centralisée

**Aide** : Utilisez Microsoft Antimalware pour les machines virtuelles Azure Windows afin de superviser et défendre en continu vos ressources. Vous aurez besoin d’un outil tiers pour la protection contre les programmes malveillants sur la machine virtuelle Linux Azure.

* [Guide pratique pour configurer Microsoft Antimalware pour les services cloud et les machines virtuelles](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Aide** : Non applicable aux machines virtuelles Azure, car il s’agit d’une ressource de calcul.

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Non applicable

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 : Vérifier que les logiciels et signatures anti-programme malveillant sont mis à jour

**Aide** : Lorsqu’il est déployé pour des machines virtuelles Windows, Microsoft Antimalware pour Azure installe automatiquement les mises à jour les plus récentes de la signature, de la plateforme et du moteur. Suivez les recommandations faites dans Azure Security Center : « Calcul et applications » pour mettre à jour les signatures de tous les points de terminaison. La protection du système d’exploitation Windows peut encore être renforcée par une sécurité supplémentaire afin de limiter le risque d’attaques basées sur les virus ou les logiciels malveillants avec le service Microsoft Defender - Protection avancée contre les menaces, qui s’intègre à Azure Security Center.

Vous aurez besoin d’un outil tiers pour la protection contre les programmes malveillants sur la machine virtuelle Linux Azure.

* [Guide pratique pour déployer Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Microsoft Defender - Protection avancée contre les menaces](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

* [Guide pratique pour configurer Microsoft Antimalware pour les services cloud et les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="data-recovery"></a>Récupération de données

*Pour plus d’informations, consultez [Contrôle de sécurité : Récupération de données](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Garantir des sauvegardes automatiques régulières

**Aide** : Créez un instantané de l’instance Microsoft Azure Virtual Machine Scale Sets ou du disque managé attaché à l’instance à l’aide de PowerShell ou d’API REST. Vous pouvez également utiliser Azure Automation pour exécuter les scripts de sauvegarde à intervalles réguliers.

* [Guide pratique pour prendre un instantané d’un groupe de machines virtuelles identiques et d’un disque managé](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Présentation d’Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés gérées par le client

**Aide** : Créez des instantanés de vos machines virtuelles Azure ou des disques managés attachés à ces instances à l’aide de PowerShell ou d’API REST. Sauvegardez toutes les clés gérées par le client dans Azure Key Vault.

Activez Sauvegarde Azure et les machines virtuelles Azure cibles, ainsi que la fréquence souhaitée et les périodes de rétention. Cela comprend la sauvegarde complète de l’état du système. Si vous utilisez Azure Disk Encryption, la sauvegarde de machine virtuelle Azure gère automatiquement la sauvegarde des clés gérées par le client.

* [Sauvegarde sur des machines virtuelles Azure utilisant le chiffrement](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [Vue d’ensemble de la sauvegarde de machines virtuelles Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [Guide pratique pour prendre un instantané d’un groupe de machines virtuelles identiques et d’un disque managé](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Guide pratique pour sauvegarder des clés de coffre de clés dans Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Aide** : Assurez-vous que la restauration des données du disque managé s’effectue régulièrement dans Sauvegarde Azure. Si nécessaire, testez la restauration du contenu sur un réseau virtuel ou abonnement isolé. Testez régulièrement la restauration des clés gérées par le client qui ont été sauvegardées.

Si vous utilisez Azure Disk Encryption, vous pouvez restaurer vos groupes de machines virtuelles identiques Azure à l’aide des clés de chiffrement du disque. Lorsque vous utilisez le chiffrement de disque, vous pouvez restaurer la machine virtuelle Azure à l’aide des clés de chiffrement du disque.

* [Sauvegarde sur des machines virtuelles Azure utilisant le chiffrement](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [Restaurer un disque et créer une machine virtuelle récupérée dans Azure](https://docs.microsoft.com/azure/backup/tutorial-restore-disk)

* [Guide pratique pour restaurer des clés de coffre de clés dans Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Guide pratique pour activer le chiffrement de disque pour les groupes de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés gérées par le client

**Aide** : Activez la protection contre la suppression pour le disque managé à l’aide de verrous. Activez la suppression réversible et la protection contre la purge dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante.

* [Verrouiller les ressources pour empêcher les modifications inattendues](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)

* [Présentation de la protection contre la suppression réversible et la suppression définitive](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Contrôle de sécurité : réponse aux incidents](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.

* [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Tirer parti du guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de votre propre plan de réponse aux incidents](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Conseils** : Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements (par ex. production, non-production) à l’aide d’étiquettes et créez un système de nommage pour identifier et classer clairement les ressources Azure, en particulier celles qui traitent des données sensibles. Il vous incombe de hiérarchiser le traitement des alertes en fonction de la criticité des ressources et de l’environnement Azure où l’incident s’est produit.

* [Alertes de sécurité dans le Centre de sécurité Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [Organisation des ressources Azure à l’aide de catégories](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Conseils** : Effectuez des exercices pour tester les capacités de réponse aux incidents de vos systèmes à intervalles réguliers, afin de protéger vos ressources Azure. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

* [Publication du NIST : Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 : Fournir des informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

**Instructions** : Microsoft utilisera les informations de contact pour le signalement d’incidents de sécurité pour vous contacter si le Microsoft Security Response Center (MSRC) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé. Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus.

* [Comment définir le contact de sécurité d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Aide** : Exportez vos alertes et recommandations Azure Security Center en utilisant la fonctionnalité d’exportation continue pour identifier les risques pesant sur les ressources Azure. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser en continu les alertes vers Azure Sentinel.

* [Comment configurer l’exportation continue](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Comment envoyer des alertes à Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Aide** : Utilisez la fonctionnalité d’automatisation de workflow d’Azure Security Center pour déclencher automatiquement des réponses via « Logic Apps » aux alertes et aux recommandations de sécurité afin de protéger vos ressources Azure.

* [Comment configurer l’automatisation des workflows et Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Contrôle de sécurité : Tests d’intrusion et exercices Red Team](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1 : Procéder régulièrement à des tests d’intrusion des ressources Azure et veiller à corriger tous les problèmes de sécurité critiques détectés

**Aide** : Suivez les règles d’engagement de Microsoft pour que vos tests d’intrusion soient conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft.

* [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

## <a name="next-steps"></a>Étapes suivantes

- Consulter le [benchmark de sécurité Azure](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
