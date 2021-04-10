---
title: Base de référence de sécurité Azure pour Automation
description: La base de référence de sécurité Automation propose des instructions procédurales et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans le Benchmark de sécurité Azure.
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ea75b9c1e8c7a1a242406b5a7f73db1b05c70276
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562203"
---
# <a name="azure-security-baseline-for-automation"></a>Base de référence de sécurité Azure pour Automation

Cette base de référence de sécurité applique les conseils du [Benchmark de sécurité Azure version 1.0](../security/benchmarks/overview-v1.md) à Azure Automation. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure.
Le contenu est regroupé par les **contrôles de sécurité** définis par le Benchmark de sécurité Azure et les conseils associés applicables à Azure Automation. Les **contrôles** non applicables à Azure Automation ont été exclus.

 
Pour voir la correspondance complète entre Azure Automation et le Benchmark de sécurité Azure, consultez le [fichier complet de mise en correspondance de la base de référence de sécurité Azure Automation](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Aide** : le compte Azure Automation ne prend pas encore en charge Azure Private Link pour restreindre l’accès au service par le biais de points de terminaison privés. Les runbooks qui s’authentifient et s’exécutent sur des ressources dans Azure fonctionnent sur un bac à sable (sandbox) Azure et exploitent les ressources back-end partagées, que Microsoft est chargé d’isoler les unes des autres ; elles ne font l’objet d’aucune restriction de mise en réseau et peuvent accéder aux ressources publiques. À l’heure actuelle, Azure Automation ne propose pas d’intégration de réseau virtuel pour les réseaux privés au-delà de la prise en charge des Runbook Workers hybrides. Ce contrôle n’est pas applicable si vous utilisez le service prêt à l’emploi sans Runbook Workers hybrides.

Pour obtenir une isolation supplémentaire de vos runbooks, vous pouvez utiliser des Runbook Workers hybrides exécutés sur des machines virtuelles Azure. Lorsque vous créez une machine virtuelle Azure, vous devez utiliser un réseau virtuel existant ou en créer un et configurer vos machines virtuelles avec un sous-réseau. Veillez à ce qu’un groupe de sécurité réseau soit appliqué à tous les sous-réseaux déployés avec des contrôles d’accès réseau propres aux ports et sources approuvés de votre application. Pour connaître les exigences spécifiques des différents services, consultez la recommandation de sécurité correspondante. Si vous avez une exigence particulière, le Pare-feu Azure peut également être utilisé pour y répondre.

- [Réseaux virtuels et machines virtuelles dans Azure](../virtual-machines/network-overview.md)

- [Guide pratique pour créer un réseau virtuel](../virtual-network/quick-create-portal.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

- [Guide pratique pour déployer et configurer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Environnement d’exécution de runbook](./automation-runbook-execution.md#runbook-execution-environment)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des interfaces réseau

**Aide** : À l’heure actuelle, Azure Automation ne propose pas d’intégration de réseau virtuel pour les réseaux privés au-delà de la prise en charge des Runbook Workers hybrides. Ce contrôle n’est pas applicable si vous utilisez le service prêt à l’emploi sans Runbook Workers hybrides.

Si vous utilisez des Runbook Workers hybrides qui s’appuient sur des machines virtuelles Azure, vérifiez que le sous-réseau dans lequel ils se trouvent est activé avec un groupe de sécurité réseau (NSG) et configurez les journaux de flux de sorte qu’ils soient transférés vers un compte de stockage à des fins d’audit du trafic. Vous pouvez aussi transmettre les journaux de flux NSG dans un espace de travail Log Analytics et utiliser Traffic Analytics pour obtenir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

Si les règles NSG et les itinéraires définis par l’utilisateur ne s’appliquent pas au point de terminaison privé, les journaux de flux NSG et les informations de surveillance des connexions sortantes restent pris en charge et utilisables.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Conseils** : À l’heure actuelle, Azure Automation ne propose pas d’intégration de réseau virtuel pour les réseaux privés au-delà de la prise en charge des Runbook Workers hybrides. Ce contrôle n’est pas applicable si vous utilisez le service prêt à l’emploi sans Runbook Workers hybrides.

Si vous utilisez des Runbook Workers hybrides qui s’appuient sur des machines virtuelles Azure, activez la protection standard contre le 	déni de service distribué (DDoS) sur les réseaux virtuels qui hébergent vos Runbook Workers hybrides afin de vous prémunir contre les attaques DDoS. La fonctionnalité Threat Intelligence intégrée à Azure Security Center permet de refuser les communications présentant des adresses IP connues comme étant malveillantes.  Configurez le Pare-feu Azure sur chacun des segments du réseau virtuel, en activant la fonctionnalité Threat Intelligence et en la configurant sur **Alerter et refuser** en ce qui concerne le trafic réseau malveillant.

Vous pouvez utiliser l’accès réseau juste-à-temps d’Azure Security Center pour limiter l’exposition des machines virtuelles Windows aux adresses IP approuvées pendant une période limitée.  Suivez également les recommandations de sécurisation renforcée du réseau adaptatif Azure Security Center pour les configurations NSG afin de limiter les ports et les adresses IP sources en fonction du trafic réel et du renseignement sur les menaces.

- [Guide pratique pour configurer la protection DDoS](../ddos-protection/manage-ddos-protection.md)

- [Guide pratique pour déployer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Présentation de la fonctionnalité Threat Intelligence intégrée à Azure Security Center](../security-center/azure-defender.md)

- [Présentation de la fonctionnalité de renforcement du réseau adaptatif d’Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Présentation de la fonctionnalité de contrôle d’accès réseau juste-à-temps d’Azure Security Center](../security-center/security-center-just-in-time.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Conseils** : À l’heure actuelle, Azure Automation ne propose pas d’intégration de réseau virtuel pour les réseaux privés au-delà de la prise en charge des Runbook Workers hybrides. Ce contrôle n’est pas applicable si vous utilisez le service prêt à l’emploi sans Workers hybrides.

Si vous utilisez des Runbook Workers hybrides qui s’appuient sur des machines virtuelles Azure, vous pouvez enregistrer les journaux de flux NSG dans un compte de stockage afin de générer des enregistrements de flux pour vos machines virtuelles Azure qui jouent le rôle de Runbook Workers. Lors de la recherche d’activité anormale, vous avez la possibilité d’activer la capture de paquets Network Watcher afin de détecter les activités inhabituelles et inattendues dans le trafic réseau.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention d’intrusion (IDS/IPS) basés sur le réseau

**Conseils** : À l’heure actuelle, Azure Automation ne propose pas d’intégration de réseau virtuel pour les réseaux privés au-delà de la prise en charge des Runbook Workers hybrides. Ce contrôle n’est pas applicable si vous utilisez le service prêt à l’emploi sans Runbook Workers hybrides.

Si vous utilisez des Runbook Workers hybrides hébergés sur des machines virtuelles Azure, vous pouvez combiner les captures de paquets assurées par Network Watcher et les outils IDS open source afin d’effectuer une détection d’intrusion réseau pour un large éventail de menaces sur ces machines Worker. Vous avez également la possibilité de déployer le Pare-feu Azure sur les segments du réseau virtuel concernés, en activant la fonctionnalité Threat Intelligence et en la configurant sur « Alerter et refuser » en ce qui concerne le trafic réseau malveillant.

- [Détection d’intrusion réseau avec Azure Network Watcher et des outils open source](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Guide pratique pour déployer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Guide pratique pour configurer des alertes avec le Pare-feu Azure](../firewall/threat-intel.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Aide** : Utilisez des étiquettes de service de réseau virtuel afin de définir des contrôles d’accès réseau sur les groupes de sécurité réseau ou le Pare-feu Azure configurés dans Azure, qui ont besoin d’un accès à vos ressources Automation. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de l’étiquette de service (par exemple, GuestAndHybridManagement) dans le champ source ou de destination approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

- [Présentation et usage des balises de service](../virtual-network/service-tags-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Conseils** : Définissez et implémentez des configurations de la sécurité standard pour les ressources réseau utilisées par Azure Automation avec Azure Policy.

Vous pouvez également utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant les principaux artefacts d’environnement (par exemple, les modèles Azure Resource Manager, les contrôles Azure RBAC et les stratégies) dans une même définition de blueprint. Vous pouvez appliquer le blueprint aux nouveaux abonnements et ajuster le contrôle et la gestion grâce au contrôle de version.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemples Azure Policy pour le réseau](../governance/policy/samples/built-in-policies.md#network)

- [Guide pratique pour créer un blueprint Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Conseils** : Utilisez des balises pour les groupes de sécurité réseau (NSG) et autres ressources liées à la sécurité réseau et au trafic. Concernant les règles NSG individuelles, utilisez le champ « Description » afin de spécifier le besoin métier et/ou la durée (etc.) pour toutes les règles qui autorisent le trafic vers/depuis un réseau.

Utilisez l’une des définitions de stratégie Azure intégrée en lien avec l’étiquetage comme « Exiger une étiquette et sa valeur » pour vous assurer que toutes les ressources créées sont étiquetées et être informé de l’existence de ressources non étiquetées.

Vous pouvez utiliser Azure PowerShell ou Azure CLI pour rechercher des ressources ou effectuer des actions sur des ressources en fonction de leurs étiquettes.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

- [Guide pratique pour créer un réseau virtuel](../virtual-network/quick-create-portal.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Aide** : Utilisez le journal d’activité Azure pour effectuer le monitoring des configurations de ressources et détecter les modifications apportées à vos ressources réseau. Créez des alertes dans Azure Monitor, qui se déclenchent en cas de modification des ressources critiques.

- [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Guide pratique pour créer des alertes dans Azure Monitor](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Transférez les données de journal aux journaux Azure Monitor pour agréger les données de sécurité générées par les ressources Azure Automation. Dans Azure Monitor, utilisez les requêtes de journaux pour interroger et effectuer des tâches d’analytique, puis les comptes de stockage Azure pour le stockage à long terme/d’archivage. Azure Automation peut envoyer le statut des tâches du runbook, les flux de travaux, les données de configuration de l’état Automation, la gestion des mises à jour et le suivi des modifications ou les journaux d’inventaire à votre espace de travail Log Analytics. Ces informations sont visibles sur le Portail Azure et Azure PowerShell, ainsi qu’avec l’API Journaux Azure Monitor, qui permet d’effectuer des investigations simples.

Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce. 

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Guide pratique pour bien démarrer avec Azure Monitor et l’intégration d’une solution SIEM tierce](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Transfert de données de travaux Azure Automation aux journaux Azure Monitor](automation-manage-send-joblogs-log-analytics.md)

- [Intégration de DSC aux journaux Azure Monitor](automation-dsc-diagnostics.md)

- [Régions prises en charge pour l’espace de travail Log Analytics lié](how-to/region-mappings.md)

- [Interrogation des journaux Update Management](update-management/query-logs.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : Autorisez Azure Monitor à accéder à vos journaux d’audit et d’activité, qui comprennent la source de l’événement, la date, l’utilisateur, l’horodatage, les adresses sources, les adresses de destination et d’autres éléments utiles. 

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Affichage et récupération des événements du journal d’activité Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Aide** : Dans Azure Monitor, définissez la période de rétention de votre espace de travail Log Analytics en fonction des réglementations de conformité de votre organisation. Utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage.

- [Modification de la période de conservation des données dans Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Informations sur la conservation des données pour les comptes Automation](./automation-managing-data.md#data-retention)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Aide** : Analysez et supervisez les journaux pour détecter les comportements anormaux et examinez régulièrement les résultats. Utilisez les requêtes de journaux Azure Monitor pour examiner les journaux et effectuer des requêtes sur leurs données.

Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce. 

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Présentation des requêtes de journaux dans Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

- [Guide pratique pour effectuer des requêtes personnalisées dans Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Aide** : Utilisez Azure Security Center avec Azure Monitor pour surveiller les journaux de sécurité et les événements, y détecter des activités anormales et générer des alertes s’y rapportant.

Vous pouvez également activer et intégrer les données dans Azure Sentinel.

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Guide pratique pour gérer les alertes dans Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Guide pratique pour générer des alertes sur les données de journal Azure Monitor](../azure-monitor/alerts/tutorial-response.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="29-enable-dns-query-logging"></a>2.9 : Activer la journalisation des requêtes DNS

**Conseils** : Implémentez une solution tierce de journalisation DNS à partir de la Place de marché Azure en fonction des besoins de votre organisation.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Conseils** : Utilisez les rôles d’administrateur intégrés Azure Active Directory (Azure AD) qui peuvent être attribués explicitement et sont interrogeables. Utilisez le module Azure AD PowerShell pour effectuer des requêtes ad hoc afin de découvrir les comptes membres de groupes d’administration. Chaque fois que vous utilisez des comptes d’identification de compte Automation pour vos runbooks, veillez à ce que ces principaux de service soient également suivis dans votre inventaire, car ils possèdent souvent des autorisations élevées. Supprimez tous les comptes d’identification inutilisés pour réduire votre surface d’attaque exposée.

- [Guide pratique pour obtenir un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Guide pratique pour obtenir les membres d’un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Suppression d’un compte d’identification ou d’un compte d’identification classique](delete-run-as-account.md)

- [Gestion d’un compte d’identification Azure Automation](manage-runas-account.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Conseils** : Le compte Azure Automation n’intègre pas le concept des mots de passe par défaut.  Les clients sont responsables des applications tierces et des services de la place de marché susceptibles d’utiliser des mots de passe par défaut qui s’exécutent sur le service ou sur ses Runbook Workers hybrides.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseils** : Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration. Chaque fois que vous utilisez des comptes d’identification de compte Automation pour vos runbooks, veillez à ce que ces principaux de service soient également suivis dans votre inventaire, car ils possèdent souvent des autorisations élevées. Limitez la portée de ces identités avec les autorisations minimales nécessaires pour que vos runbooks effectuent correctement leur processus automatisé. Supprimez tous les comptes d’identification inutilisés pour réduire votre surface d’attaque exposée.

Vous pouvez aussi activer un accès Just-In-Time/Just-Enough à l’aide des rôles privilégiés d’Azure Active Directory (Azure AD) Privileged Identity Management pour les services Microsoft et d’Azure Resource Manager.

- [En savoir plus sur Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

- [Suppression d’un compte d’identification ou d’un compte d’identification classique](delete-run-as-account.md)

- [Gestion d’un compte d’identification Azure Automation](manage-runas-account.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Conseils** : Dans la mesure du possible, utilisez l’authentification SSO avec Azure Active Directory (Azure AD) plutôt que de configurer des informations d’identification autonomes individuelles par service. Suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center.

- [Authentification unique pour les applications dans Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](../security-center/security-center-identity-access.md)

- [Authentification sur Azure avec Azure AD](automation-use-azure-ad.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseils** : activez l’authentification multifacteur Azure Active Directory (Azure AD), puis suivez les recommandations relatives à la gestion des identités et des accès d’Azure Security Center.

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Conseils** : Utilisez des stations de travail à accès privilégié avec authentification multifacteur pour vous connecter aux ressources de compte Azure Automation dans des environnements de production et les configurer. 

- [En savoir plus sur les stations de travail à accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration 

**Conseils** : Utilisez la détection des risques d’Azure Active Directory (Azure AD) pour visualiser les alertes et les rapports sur les comportements à risque des utilisateurs. Le client a s’il le souhaite la possibilité de transférer les alertes de détection des risques Azure Security Center à Azure Monitor et de configurer des alertes/notifications personnalisées à l’aide de groupes d’actions.

- [Présentation des détections de risques Azure Security Center (activité suspecte)](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Configuration des groupes d’actions pour générer des alertes et des notifications personnalisées](../azure-monitor/alerts/action-groups.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement 

**Aide** : Il est recommandé d’utiliser des emplacements nommés avec accès conditionnel pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions. 

- [Guide pratique pour configurer des emplacements nommés dans Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Utiliser Azure Active Directory (Azure AD) comme système d’authentification et d’autorisation central. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur. Si vous utilisez des Runbook Workers hybrides, vous pouvez tirer parti des identités managées plutôt que des comptes d’identification pour obtenir des autorisations sécurisées plus fluides.

- [Création et configuration d’une instance Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Authentification du runbook avec des identités managées](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Azure Active Directory (Azure AD) fournit des journaux pour vous aider à découvrir les comptes obsolètes. Par ailleurs, utilisez les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès utilisateur peut être vérifié régulièrement de sorte que seuls les utilisateurs appropriés continuent de bénéficier d’un accès. Chaque fois que vous utilisez des comptes d’identification de compte Automation pour vos runbooks, veillez à ce que ces principaux de service soient également suivis dans votre inventaire, car ils possèdent souvent des autorisations élevées. Supprimez tous les comptes d’identification inutilisés pour réduire votre surface d’attaque exposée.

- [Présentation des rapports Azure AD](../active-directory/reports-monitoring/index.yml)

- [Comment utiliser les révisions d’accès des identités Azure](../active-directory/governance/access-reviews-overview.md)

- [Suppression d’un compte d’identification ou d’un compte d’identification classique](delete-run-as-account.md)

- [Gestion d’un compte d’identification Azure Automation](manage-runas-account.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Conseils** : Vous avez accès aux sources des journaux Activité de connexion, Audit et Événement à risque Azure Active Directory (Azure AD), qui permettent l’intégration à n’importe quel outil SIEM/de supervision.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure AD et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Vous pouvez configurer les alertes souhaitées dans un espace de travail Log Analytics.

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Conseils** :Utilisez les fonctionnalités de protection des identités et contre les risques d’Azure Active Directory (Azure AD) pour configurer des réponses automatisées aux actions suspectes détectées liées aux identités des utilisateurs de votre ressource réseau. Vous pouvez également ingérer des données dans Azure Sentinel pour approfondir votre examen.

- [Guide pratique pour afficher les connexions risquées Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

**Aide** : Pour les comptes Azure Automation, le support Microsoft peut accéder aux métadonnées des ressources de la plateforme sur un cas de support ouvert sans utiliser d’autre outil.

Toutefois, si vous utilisez des Runbook Workers hybrides qui s’appuient sur des machines virtuelles Azure et qu’un tiers a besoin d’accéder aux données client (par exemple, lors d’une demande de support), tirez parti de Customer Lockbox (préversion) pour que les machines virtuelles Azure examinent et approuvent ou rejettent les demandes d’accès aux données client.

- [Présentation de Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Aide** : Utilisez des étiquettes pour faciliter le suivi des ressources Azure Automation qui stockent ou traitent des informations sensibles. 

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Isolez les environnements en utilisant des ressources de compte Automation distinctes. Des ressources comme les Runbook Workers hybrides doivent être séparées par un réseau virtuel/sous-réseau, correctement étiquetées et sécurisées au sein d’un groupe de sécurité réseau (NSG) ou d’un Pare-feu Azure. En ce qui concerne les machines virtuelles qui stockent ou traitent des données sensibles, implémentez une stratégie et une ou plusieurs procédures qui les désactive lorsqu’elles ne sont pas utilisées.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

- [Guide pratique pour créer un réseau virtuel](../virtual-network/quick-create-portal.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

- [Guide pratique pour déployer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Configuration des options « Alerter » et « Alerter et refuser » du pare-feu Azure](../firewall/threat-intel.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

**Conseils** : Si vous vous servez de la fonctionnalité Runbook Worker hybride, exploitez une solution tierce issue de la Place de marché Azure sur les périmètres du réseau, qui surveille et bloque le transfert non autorisé d’informations sensibles tout en alertant les professionnels de la sécurité de l’information.

Pour la plateforme sous-jacente, gérée par Microsoft, Microsoft traite tout le contenu client comme sensible et assure une protection contre la perte et l’exposition de données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Instructions** : Chiffrer toutes les informations sensibles en transit. Veillez à ce que les clients qui se connectent à vos ressources Azure sur des réseaux virtuels puissent négocier le protocole TLS (Transport Layer Security) 1.2 (ou supérieur). Il est entièrement pris en charge et appliqué par Azure Automation, de même que tous les appels clients et toutes les versions ultérieures des points de terminaison HTPS externes (par le biais de webhooks, de nœuds DSC ou de Runbook Workers hybrides).

Suivez les recommandations d’Azure Security Center relatives au chiffrement au repos et au chiffrement en transit, le cas échéant.

- [Présentation du chiffrement en transit avec Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Mise en œuvre du protocole TLS 1.2 Azure Automation](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Aide** : Utilisez un outil de découverte actif tiers pour identifier toutes les informations sensibles stockées, traitées ou transmises par les systèmes technologiques de l’organisation, qu’elles soient situées sur site ou chez un fournisseur de services distant, et mettez à jour l’inventaire des informations sensibles de l’organisation.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6 : Utiliser le contrôle d’accès en fonction du rôle pour contrôler l’accès aux ressources

**Aide** : Utilisez le contrôle d’accès basé sur le rôle Azure (Azure RBAC) pour contrôler l’accès aux ressources Azure Automation à l’aide des définitions de rôles intégrées et y assigner l’accès aux utilisateurs suivant un modèle d’accès minimal, dit « des privilèges minimum ». Lorsque vous avez recours à des Runbook Workers hybrides, tirez parti des identités managées pour ces machines virtuelles afin d’éviter d’utiliser des principaux de service ; si vous vous servez à la fois des Runbook Workers multilocataires et hybrides, veillez à définir correctement la portée des autorisations Azure RBAC appliquées à leur identité.

- [Comment configurer Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Autorisations d’un Runbook Worker hybride](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

- [Gestion de la sécurité et des autorisations des rôles](automation-role-based-access-control.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7 : Utiliser la protection contre la perte de données basée sur l’hôte pour appliquer le contrôle d’accès

**Conseils** : À l’heure actuelle, Azure Automation n’expose pas les machines virtuelles sous-jacentes des Runbook Workers multilocataires. Cet aspect est géré par la plateforme. Ce contrôle n’est pas applicable si vous utilisez le service prêt à l’emploi sans Runbook Workers hybrides.

Si vous utilisez des Runbook Workers hybrides qui s’appuient sur des machines virtuelles Azure, vous devez tirer parti d’une solution tierce de protection contre la perte de données basée sur un hôte pour appliquer des contrôles d’accès à vos machines virtuelles Runbook Workers hybrides hébergées.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Aide** : Utilisez des clés gérées par le client avec Azure Automation. Azure Automation les prend en charge pour chiffrer toutes les « Ressources sécurisées » utilisées, notamment les informations d’identification, les certificats, les connexions et les variables chiffrées. Tirez parti des variables chiffrées avec vos runbooks pour tous vos besoins de recherche de variables persistantes pour empêcher toute exposition involontaire.

Lorsque vous utilisez des Runbook Workers hybrides, les disques virtuels des machines virtuelles sont chiffrés au repos (chiffrement côté serveur ou 	Azure Disk Encryption, ADE). Azure Disk Encryption utilise la fonctionnalité BitLocker de Windows pour chiffrer des disques managés avec des clés gérées par le client au sein de la machine virtuelle invitée. Le chiffrement côté serveur avec des clés gérées par le client améliore l’utilisation de Azure Disk Encryption en vous permettant d’utiliser des types et des images de système d’exploitation pour vos machines virtuelles en chiffrant les données dans le service de stockage.

- [Chiffrement côté serveur de disques managés Azure](../virtual-machines/disk-encryption.md)

- [Azure Disk Encryption pour les machines virtuelles Windows](../virtual-machines/windows/disk-encryption-overview.md)

- [Utilisation de clés gérées par le client pour un compte Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Variables managées dans Azure Automation](shared-resources/variables.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés.

**Définitions intégrées à Azure Policy - Microsoft.Automation** :

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Utilisez Azure Monitor avec le journal d’activité Azure pour créer des alertes en cas de modification des ressources Azure critiques, comme des composants réseau, des comptes Azure Automation et des runbooks. 

- [Journalisation des diagnostics pour un groupe de sécurité réseau](../private-link/private-link-overview.md#logging-and-monitoring)

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Aide** : Suivez les recommandations d’Azure Security Center en matière d’évaluation des vulnérabilités sur vos ressources Azure.

- [Recommandations de sécurité dans Azure Security Center](../security-center/security-center-recommendations.md)

- [Informations de référence sur les recommandations Security Center](../security-center/recommendations-reference.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 : Comparer les analyses de vulnérabilités dos à dos

**Conseils** : Exportez les résultats de l’analyse à intervalles réguliers et comparez les résultats pour vérifier que les vulnérabilités ont été corrigées. Lorsqu’ils utilisent les recommandations de gestion des vulnérabilités suggérées par Azure Security Center, les clients peuvent basculer vers le portail de la solution sélectionnée pour afficher les données d’analyse historiques.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes.

**Aide** : Utilisez les niveaux de risque par défaut (Niveau de sécurité) fournis par Azure Security Center pour classer la correction des vulnérabilités découvertes par ordre de priorité.

- [Présentation du degré de sécurisation Azure Security Center](../security-center/secure-score-security-controls.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Aide** : Utilisez Azure Resource Graph pour interroger et découvrir toutes les ressources Azure Automation de vos abonnements. Vérifiez que vous disposez des autorisations (en lecture) appropriées dans votre locataire et pouvez répertorier tous les abonnements Azure ainsi que les ressources qu’ils contiennent.

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

**Aide** : Utilisez des étiquettes, des groupes d’administration et des abonnements distincts, le cas échéant, pour organiser et suivre les ressources Azure Automation. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile. Supprimez tous les comptes d’identification inutilisés pour réduire votre surface d’attaque exposée.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

- [Suppression d’un compte d’identification ou d’un compte d’identification classique](delete-run-as-account.md)

- [Gestion d’un compte d’identification Azure Automation](manage-runas-account.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4 : Définir et tenir un inventaire des ressources Azure approuvées

**Aide** : Vous devez créer un inventaire des ressources Azure approuvées et des logiciels approuvés pour les ressources de calcul en fonction des besoins de votre organisation.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Conseils** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans les abonnements clients selon les définitions de stratégies intégrées suivantes : 

- Types de ressources non autorisés 
- Types de ressources autorisés 

Utilisez également Azure Resource Graph pour interroger/découvrir des ressources dans les abonnements. Cela peut être utile dans les environnements de haute sécurité, tels que ceux dotés de comptes de stockage. 

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Guide pratique pour créer des requêtes avec Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Exemples Azure Policy intégrés pour Azure Automation](policy-reference.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 : Supprimer des ressources et applications logicielles Azure non approuvées

**Conseils** : Le client peut empêcher la création ou l’utilisation de ressources avec Azure Policy comme l’exigent ses instructions d’entreprise. Vous pouvez implémenter votre propre processus de suppression des ressources non autorisées. Dans l’offre Azure Automation, il est possible d’installer, de supprimer et de gérer les modules PowerShell et Python auxquels les runbooks ont accès par le biais du portail ou de cmdlets. Un module non approuvé ou ancien doit être supprimé ou mis à jour pour les runbooks.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Gestion des modules dans Azure Automation](shared-resources/modules.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Conseils** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans les abonnements clients selon les définitions de stratégies intégrées suivantes :
- Types de ressources non autorisés
- Types de ressources autorisés

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Conseils** : Utilisez les stratégies d’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure » à partir d’emplacements ou d’appareils non sécurisés ou non approuvés. 

- [Configuration de l’accès conditionnel pour bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Aide** : Utilisez des alias Azure Policy pour créer des stratégies personnalisées afin d’auditer ou d’appliquer la configuration de vos ressources Azure Automation et autres ressources associées. Vous pouvez aussi utiliser des définitions Azure Policy intégrées.

Par ailleurs, Azure Resource Manager a la possibilité d’exporter le modèle au format JSON (JavaScript Object Notation), qui doit être examiné pour vérifier que les configurations répondent/dépassent les exigences de sécurité de votre organisation.

Vous pouvez aussi utiliser les recommandations d’Azure Security Center comme base de référence de configuration sécurisée pour vos ressources Azure.

- [Affichage des alias Azure Policy disponibles](/powershell/module/az.resources/get-azpolicyalias)

- [Tutoriel : Créer et gérer des stratégies pour assurer la conformité](../governance/policy/tutorials/create-and-manage.md)

- [Exemples Azure Policy intégrés pour Azure Automation](policy-reference.md)

- [Exportation monoressource ou multiressource vers un modèle sur le portail Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recommandations de sécurité - Guide de référence](../security-center/recommendations-reference.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Aide** : Utilisez les modèles Azure Resource Manager et Azure Policy pour configurer de manière sécurisée les ressources Azure associées à Azure Automation. Les modèles Azure Resource Manager sont des fichiers JSON servant à déployer des ressources Azure ; tous les modèles personnalisés doivent être stockés et gérés en toute sécurité dans un référentiel de code. Utilisez la fonctionnalité d’intégration du contrôle de code source pour maintenir les runbooks de votre compte Automation à jour par rapport aux scripts de votre référentiel de contrôle de code source. Utilisez les commandes Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure.

- [Utilisation de l’intégration du contrôle de code source](source-control-integration.md)

- [Informations sur la création de modèles Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Présentation des effets d’Azure Policy](../governance/policy/concepts/effects.md)

- [Déploiement d’un compte Automation à l’aide d’un modèle Azure Resource Manager](/azure/automation/quickstart-create-automation-account-template)

- [Exemples Azure Policy intégrés pour Azure Automation](policy-reference.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Utilisez Azure DevOps pour stocker et gérer de manière sécurisée votre code, comme les stratégies Azure personnalisées, les modèles Azure Resource Manager et les scripts Desired State Configuration. Pour accéder aux ressources que vous gérez dans Azure DevOps, vous pouvez accorder ou refuser des autorisations à des utilisateurs spécifiques, à des groupes de sécurité intégrés ou à des groupes définis dans Azure Active Directory (Azure AD) s’ils sont intégrés à Azure DevOps, ou à Active Directory s’il est intégré à TFS. Utilisez la fonctionnalité d’intégration du contrôle de code source pour maintenir les runbooks de votre compte Automation à jour par rapport aux scripts de votre référentiel de contrôle de code source.

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [À propos des autorisations et des groupes dans Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Utilisation de l’intégration du contrôle de code source](source-control-integration.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Conseils** : Définissez et implémentez des configurations de sécurité standard pour les ressources Azure à l’aide d’Azure Policy. Utilisez des alias Azure Policy pour créer des stratégies personnalisées afin d’auditer ou d’appliquer la configuration réseau de vos ressources Azure. Vous pouvez également utiliser des définitions de stratégie intégrées en lien avec vos ressources spécifiques. 

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Utiliser des alias](../governance/policy/concepts/definition-structure.md#aliases)

- [Exemples Azure Policy intégrés pour Azure Automation](policy-reference.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Aide** : Utilisez Azure Policy pour créer des alertes et auditer les configurations de ressources Azure. La stratégie peut permettre de détecter certaines ressources non configurées avec un point de terminaison privé.

Si vous vous servez de la fonctionnalité Runbook Worker hybride, tirez parti d’Azure Security Center pour effectuer des analyses de base de référence de vos machines virtuelles Azure.  Il existe d’autres méthodes de configuration automatisée, notamment Azure Automation State Configuration.

- [Corriger les recommandations dans Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Prise en main d’Azure Automation State Configuration](automation-dsc-getting-started.md)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemples Azure Policy intégrés pour Azure Automation](policy-reference.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault. 

- [Configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Garantir des sauvegardes automatiques régulières

**Aide** : Utilisez Azure Resource Manager pour déployer des comptes Azure Automation et les ressources associées. Azure Resource Manager permet d’exporter des modèles pouvant servir de sauvegardes pour restaurer les comptes Azure Automation et les ressources associées. Utilisez Azure Automation pour appeler régulièrement l’API d’exportation de modèles Azure Resource Manager.

Utilisez la fonctionnalité d’intégration du contrôle de code source pour maintenir les runbooks de votre compte Automation à jour par rapport aux scripts de votre référentiel de contrôle de code source.

- [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Informations de référence sur les modèles Azure Resource Manager pour les ressources Azure Automation](/azure/templates/microsoft.automation/allversions)

- [Création d’un compte Automation à l’aide d’un modèle Azure Resource Manager](quickstart-create-automation-account-template.md)

- [Exportation monoressource ou multiressource vers un modèle sur le portail Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Groupes de ressources – Exportation du modèle](/rest/api/resources/resourcegroups/exporttemplate)

- [Présentation d’Azure Automation](automation-intro.md)

- [Guide pratique pour sauvegarder des clés de coffre de clés dans Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Utilisation de clés gérées par le client pour un compte Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Utilisation de l’intégration du contrôle de code source](source-control-integration.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés gérées par le client

**Aide** : Utilisez Azure Resource Manager pour déployer des comptes Azure Automation et les ressources associées. Azure Resource Manager permet d’exporter des modèles pouvant servir de sauvegardes pour restaurer les comptes Azure Automation et les ressources associées. Utilisez Azure Automation pour appeler régulièrement l’API d’exportation de modèles Azure Resource Manager. Sauvegardez les clés gérées par le client dans Azure Key Vault. Vous pouvez exporter vos runbooks dans des fichiers de script à l’aide du Portail Azure ou de PowerShell.

- [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Informations de référence sur les modèles Azure Resource Manager pour les ressources Azure Automation](/azure/templates/microsoft.automation/allversions)

- [Création d’un compte Automation à l’aide d’un modèle Azure Resource Manager](quickstart-create-automation-account-template.md)

- [Exportation monoressource ou multiressource vers un modèle sur le portail Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Groupes de ressources – Exportation du modèle](/rest/api/resources/resourcegroups/exporttemplate)

- [Présentation d’Azure Automation](automation-intro.md)

- [Guide pratique pour sauvegarder des clés de coffre de clés dans Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Utilisation de clés gérées par le client pour un compte Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Sauvegarde des données Azure pour les comptes Automation](./automation-managing-data.md#data-backup)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Aide** : Veillez à pouvoir effectuer régulièrement le déploiement des modèles Azure Resource Manager dans un abonnement isolé, si nécessaire. Testez la restauration des clés gérées par le client sauvegardées.

- [Déploiement de ressources avec des modèles ARM et le Portail Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Guide pratique pour restaurer des clés de coffre de clés dans Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Utilisation de clés gérées par le client pour un compte Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés gérées par le client

**Aide** : Utilisez Azure DevOps pour stocker et gérer votre code, par exemple, les modèles Azure Resource Manager de manière sécurisée. Pour protéger les ressources que vous gérez dans Azure DevOps, vous pouvez octroyer ou refuser des autorisations à des utilisateurs spécifiques, à des groupes de sécurité intégrés ou à des groupes définis dans Azure Active Directory (Azure AD) s’ils sont intégrés à Azure DevOps ou à Active Directory s’il est intégré à TFS.

Utilisez la fonctionnalité d’intégration du contrôle de code source pour maintenir les runbooks de votre compte Automation à jour par rapport aux scripts de votre référentiel de contrôle de code source.

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [À propos des autorisations et des groupes dans Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Utilisation de l’intégration du contrôle de code source](source-control-integration.md)

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

**Conseils** : Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou à l’analytique utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte. 

En outre, marquez clairement les abonnements (par ex. production, non-production) à l’aide d’étiquettes et créez un système de nommage pour identifier et classer clairement les ressources Azure, en particulier celles qui traitent des données sensibles.  Il vous incombe de hiérarchiser le traitement des alertes en fonction de la criticité des ressources et de l’environnement Azure où l’incident s’est produit.

- [Alertes de sécurité dans le Centre de sécurité Azure](../security-center/security-center-alerts-overview.md)

- [Organisation des ressources Azure à l’aide de catégories](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Conseils** : Effectuez des exercices pour tester les capacités de réponse aux incidents de vos systèmes à intervalles réguliers, afin de protéger vos ressources Azure. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

- [Publication du NIST : Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

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

**Aide** : Suivez les règles d’engagement de Microsoft pour que vos tests d’intrusion soient conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft.

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](../security/benchmarks/overview.md)
- En savoir plus sur les [bases de référence de la sécurité Azure](../security/benchmarks/security-baselines-overview.md)
