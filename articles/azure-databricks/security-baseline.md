---
title: Base de référence de sécurité Azure pour Azure Databricks
description: Base de référence de sécurité Azure pour Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 23057723d46ed5d9203741ab2eb1a15edb5bd510
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681697"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Base de référence de sécurité Azure pour Azure Databricks

La base de référence de sécurité Azure pour Azure Databricks contient des recommandations qui vous aideront à améliorer la posture de sécurité de votre déploiement.

La base de référence pour ce service est tirée du [benchmark de sécurité Azure version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), qui fournit des recommandations sur la façon de sécuriser vos solutions cloud sur Azure en suivant nos conseils sur les bonnes pratiques.

Pour plus d’informations, consultez la [Vue d’ensemble des bases de référence de sécurité Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sécurité du réseau

*Pour plus d’informations, consultez [Contrôle de sécurité : Sécurité réseau](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 : Protéger les ressources à l'aide de groupes de sécurité réseau ou du Pare-feu Azure sur votre réseau virtuel

**Aide** : Déployez Azure Databricks dans votre propre réseau virtuel Azure (VNet). Le déploiement par défaut d’Azure Databricks est un service entièrement géré sur Azure : toutes les ressources de plan de données, y compris un réseau virtuel auquel tous les clusters seront associés, sont déployées sur un groupe de ressources verrouillé. Cependant, si vous devez personnaliser votre réseau, vous pouvez déployer les ressources de plan de données Azure Databricks dans votre propre réseau virtuel (injection de réseau virtuel), ce qui vous permet de mettre en œuvre des configurations réseau personnalisées. Vous pouvez appliquer votre propre groupe de sécurité réseau (NSG) avec des règles personnalisées à des restrictions de trafic de sortie spécifiques.

De plus, vous pouvez configurer des règles NSG pour préciser des restrictions de trafic de sortie sur le sous-réseau sur lequel votre instance Azure Databricks est déployée. Le Pare-feu Azure peut être configuré pour les espaces de travail injectés sur le réseau virtuel.

* [Comment déployer Azure Databricks dans votre propre réseau virtuel](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Comment gérer les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des cartes réseau

**Aide** : Déployez Azure Databricks dans votre propre réseau virtuel Azure (VNet). Un groupe de sécurité réseau (NSG) n’est pas créé automatiquement. Vous devez créer un groupe de sécurité réseau de référence avec des règles Azure par défaut uniquement. Lorsque vous déployez un espace de travail, les règles demandées par Databricks sont ajoutées. Vous pouvez également commencer par un groupe de sécurité réseau vide, auxquelles les règles appropriées seront ajoutées automatiquement. Activez les journaux de flux NSG et transférez-les dans un compte de stockage pour les audits du trafic. Vous pouvez aussi envoyer ces journaux dans un espace de travail Log Analytics, et utiliser Traffic Analytics pour fournir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

* [Comment déployer Azure Databricks dans votre propre réseau virtuel](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Guide pratique pour activer les journaux de flux NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Guide pratique pour activer et utiliser Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Guide pratique pour activer Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="13-protect-critical-web-applications"></a>1.3 : Protéger les applications web critiques

**Conseils** : Non applicable. Cette recommandation a trait aux applications web s’exécutant sur Azure App Service ou des ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications présentant des adresses IP connues comme étant malveillantes

**Aide** : Activez la norme Azure DDoS Protection sur les réseaux virtuels Azure associés à vos instances Azure Databricks pour la protection contre des attaques par déni de service distribué. Utilisez la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center pour refuser les communications avec des adresses IP publiques connues comme étant malveillantes ou inutilisées.

* [Gérer la norme Azure DDoS Protection à l’aide du portail Azure](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Comprendre la protection contre les menaces pour la couche réseau Azure dans Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5 : Consigner les paquets réseau et les journaux de flux

**Aide** : Déployez Azure Databricks dans votre propre réseau virtuel Azure (VNet). Un groupe de sécurité réseau (NSG) n’est pas créé automatiquement. Vous devez créer un groupe de sécurité réseau de référence avec des règles Azure par défaut uniquement. Lorsque vous déployez un espace de travail, les règles demandées par Databricks sont ajoutées. Activez les journaux de flux NSG et transférez-les dans un compte de stockage pour l’audit du trafic. Vous pouvez aussi envoyer ces journaux dans un espace de travail Log Analytics, et utiliser Traffic Analytics pour fournir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

* [Comment déployer Azure Databricks dans votre propre réseau virtuel](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Guide pratique pour activer les journaux de flux NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Guide pratique pour activer et utiliser Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Guide pratique pour activer Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions basés sur le réseau (IDS/IPS)

**Aide** : Implémentez une appliance virtuelle réseau IDP/IPS depuis la Place de marché Azure pour créer un espace de travail intégré au réseau virtuel dans lequel tous les clusters Azure Databricks sont dotés d’une adresse IP sortante unique. L’adresse IP unique peut être utilisée comme couche de sécurité supplémentaire avec d’autres services et applications Azure qui autorisent l’accès en fonction d’adresses IP spécifiques. Vous pouvez utiliser un pare-feu Azure ou d’autres outils tiers, par exemple une appliance virtuelle réseau, pour gérer le trafic entrant et sortant.

Si la détection et/ou la prévention des intrusions basées sur l’inspection de la charge utile ne font pas partie des exigences, vous pouvez utiliser le Pare-feu Azure avec Threat Intelligence. Le filtrage basé sur le renseignement sur les menaces du Pare-feu Azure peut générer des alertes et refuser le trafic depuis ou vers des adresses IP et des domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence.

* [Attribuer une adresse IP publique unique pour les espaces de travail injectés sur un réseau virtuel à l'aide du Pare-feu Azure](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [Comment créer une appliance virtuelle réseau](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7 : Gérer le trafic à destination des applications web

**Conseils** : Non applicable. Cette recommandation a trait aux applications web s’exécutant sur Azure App Service ou des ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Aide** : Utilisez les étiquettes de service pour définir les contrôles d’accès réseau sur les groupes de sécurité réseau attachés aux sous-réseaux auxquels votre instance Azure Databricks est associée. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de la balise de service (par exemple, ApiManagement) dans le champ Source ou Destination approprié d'une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent. Les étiquettes de service ne sont pas prises en charge avec les espaces de travail de réseau virtuel non injectés.

* [Comprendre les étiquettes de service](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Aide** : Définissez et implémentez des configurations de sécurité réseau pour vos instances Azure Databricks avec Azure Policy. Vous pouvez utiliser les alias Azure Policy dans l’espace de noms « Microsoft.Databricks » pour définir les définitions des stratégies personnalisées. Les stratégies ne s’appliquent pas aux ressources au sein du groupe de ressources managé.

Vous pouvez également utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant au cœur d’une même définition de blueprint les artefacts d’environnement clés, tels que les modèles Azure Resource Management, le contrôle d’accès en fonction du rôle (RBAC) et les stratégies. Appliquez facilement le blueprint aux nouveaux abonnements et environnements, et ajustez le contrôle et la gestion par le biais du versioning.

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Comprendre la structure de définition et les alias Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Guide pratique pour créer un blueprint Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Aide** : Utilisez des étiquettes pour les groupes de sécurité réseau et d’autres ressources liées à la sécurité réseau et au flux de trafic qui sont associés à vos instances Azure Databricks. Concernant les règles NSG individuelles, utilisez le champ « Description » afin de spécifier le besoin métier et/ou la durée (etc.) pour toutes les règles qui autorisent le trafic vers/depuis un réseau.

* [Guide pratique pour créer et utiliser des étiquettes](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Aide** : Utilisez le journal d’activité Azure pour superviser les configurations des ressources réseau, et détecter les modifications de ces ressources associées à vos instances Azure Databricks. Créez des alertes dans Azure Monitor, qui se déclenchent lors de la modification de ressources réseau critiques.

* [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Guide pratique pour créer des alertes dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="logging-and-monitoring"></a>Enregistrement et surveillance

*Pour plus d’informations, consultez [Contrôle de sécurité : Journalisation et supervision](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1 : Utiliser des sources de synchronisation date/heure approuvées

**Aide** : Microsoft gère les sources de temps pour les ressources Azure. Toutefois, vous avez la possibilité de gérer les paramètres de synchronisation date/heure pour vos ressources de calcul. Comme Azure Databricks est un service PaaS, vous ne disposez d’aucun accès direct aux machines virtuelles d’un cluster Azure Databricks, et vous ne pouvez pas configurer les paramètres de synchronisation de l’heure.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Ingérez les journaux via Azure Monitor pour agréger les données de sécurité générées par Azure Databricks. Dans Azure Monitor, vous pouvez interroger l’espace de travail Log Analytics qui est configuré pour recevoir vos journaux Databricks et de diagnostic. Utilisez les comptes de stockage Azure pour les hubs d’événements ou le stockage de journaux d’archivage/à long terme, qui permettent d’exporter des données vers d’autres systèmes. Vous pouvez également activer et intégrer des données dans Azure Sentinel ou une solution SIEM (Security Information and Event Management) tierce.

Remarque : Le plan Azure Databricks Premium est nécessaire pour les journaux de diagnostic Azure Databricks

* [Comment configurer les paramètres de diagnostic](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Guide pratique pour intégrer Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Guide pratique pour bien démarrer avec Azure Monitor et l’intégration d’une solution SIEM tierce](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : activez les paramètres de diagnostic des journaux d’activité Azure et envoyez les journaux vers un espace de travail Log Analytics, Event Hub ou un compte de stockage Azure pour archivage. À l’aide des données des journaux d’activité Azure, vous pouvez déterminer les éléments « qui, quand et quoi » sur toutes les opérations d’écriture (PUT, POST, DELETE) effectuées au niveau du plan de contrôle pour vos ressources Azure.

Pour la journalisation d’audit, Azure Databricks fournit des journaux de diagnostic de bout en bout complets sur les activités effectuées par les utilisateurs Azure Databricks, ce qui permet à votre entreprise de superviser des modèles détaillés d’utilisation Azure Databricks.

Remarque : Le plan Azure Databricks Premium est nécessaire pour les journaux de diagnostic Azure Databricks

* [Comment activer les paramètres de diagnostic pour le journal d’activité Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Comment activer les paramètres de diagnostic pour Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4 : Collecter les journaux de sécurité des systèmes d’exploitation

**Aide** : Azure Databricks fournit des journaux de diagnostic de bout en bout complets des activités effectuées par les utilisateurs Azure Databricks, ce qui permet à votre entreprise de superviser des modèles d’utilisation détaillée d’Azure Databricks.

Remarque : Le plan Azure Databricks Premium est nécessaire pour les journaux de diagnostic Azure Databricks. La journalisation de la sécurité du système d’exploitation n’est pas disponible.

* [Comment activer les paramètres de diagnostic pour le journal d’activité Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Comment activer les paramètres de diagnostic pour Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Aide** : Activez les paramètres de diagnostic pour Azure Databricks. Si vous choisissez de stocker les journaux dans un espace de travail Log Analytics, définissez la période de conservation de votre espace de travail Log Analytics en fonction des obligations réglementaires de votre organisation. Utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage. Les activités liées à la sécurité sont suivies dans les journaux d’audit Databricks.

Remarque : Le plan Azure Databricks Premium est nécessaire pour les journaux de diagnostic Azure Databricks

* [Comment activer les paramètres de diagnostic dans Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Comment définir les paramètres de conservation des journaux pour les espaces de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Aide** : Activez les paramètres de diagnostic pour Azure Databricks et envoyez les journaux sur un espace de travail Log Analytics. Utilisez l’espace de travail Log Analytics pour analyser et superviser vos journaux Azure Databricks à la recherche de comportements anormaux, et consultez régulièrement les résultats.

Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce.

Remarque : Le plan Azure Databricks Premium est nécessaire pour les journaux de diagnostic Azure Databricks

* [Comment activer les paramètres de diagnostic dans Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Comment interroger les journaux Azure Databricks envoyés à l’espace de travail Log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Guide pratique pour intégrer Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 : Activer des alertes en cas d’activité anormale

**Aide** : Configurez les paramètres de diagnostic pour votre instance Azure Databricks et envoyez les journaux à un espace de travail Log Analytics. Au sein de votre espace de travail Log Analytics, configurez les alertes pour qu’elles se déclenchent lorsqu’un ensemble prédéfini de conditions se produisent.

Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce.

Remarque : Le plan Azure Databricks Premium est nécessaire pour les journaux de diagnostic Azure Databricks

* [Comment envoyer les journaux Azure Databricks à l’espace de travail Log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery)

* [Comment configurer les alertes dans un espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8 : Centraliser la journalisation anti-programme malveillant

**Aide** : Non applicable.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="29-enable-dns-query-logging"></a>2.9 : Activer la journalisation des requêtes DNS

**Aide** : Non applicable. Azure Databricks ne traite pas et ne produit pas de journaux liés au DNS accessibles par l’utilisateur.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="210-enable-command-line-audit-logging"></a>2.10 : Activer l’enregistrement d’audit en ligne de commande

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Contrôle de sécurité : Contrôle des accès et des identités](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Aide** : Vous pouvez utiliser les API SCIM Azure Databricks pour gérer les utilisateurs dans un espace de travail Azure Databricks, et accorder des privilèges d’administrateur à des utilisateurs désignés. Vous pouvez également les gérer par le biais de l’interface utilisateur Azure Databricks.

* [Comment utiliser les API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Comment ajouter et supprimer des utilisateurs dans Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Aide** : Azure Databricks utilise Azure Active Directory (AD) pour fournir l’accès au portail Azure et à la console d’administration Azure Databricks. Azure AD n’utilise pas le concept des mots de passe par défaut, cependant vous êtes chargé de modifier ou de ne pas autoriser les mots de passe par défaut pour les applications personnalisées ou tierces.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Aide** : Vous pouvez utiliser les API SCIM Azure Databricks pour ajouter des utilisateurs avec des privilèges d’administrateur dans une instance Azure Databricks. Vous pouvez également les gérer par le biais de l’interface utilisateur Azure Databricks.

* [Comment utiliser les API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Comment ajouter et supprimer des utilisateurs dans Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4 : Utiliser l’authentification unique (SSO) avec Azure Active Directory

**Aide** : La solution Azure Databricks est automatiquement configurée pour utiliser l’authentification unique Azure Active Directory permettant d’authentifier les utilisateurs. Les utilisateurs en dehors de votre organisation doivent suivre le processus d’invitation et être ajoutés à votre locataire Active Directory avant de pouvoir se connecter à Azure Databricks via l’authentification unique. Vous pouvez implémenter SCIM pour automatiser le provisionnement et le déprovisionnement des utilisateurs à partir des espaces de travail.

* [Comment utiliser les API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Comprendre l’authentification unique pour Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Comment inviter des utilisateurs sur votre locataire Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory.

**Aide** : Activez Azure AD MFA et suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center.

* [Guide pratique pour activer l’authentification MFA dans Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Guide pratique pour superviser les identités et les accès dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Aide** : Utilisez des stations de travail avec accès privilégié avec l’authentification multifacteur (MFA) configurée pour la connexion aux ressources Azure et leur configuration.

* [En savoir plus sur les stations de travail à accès privilégié](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Guide pratique pour activer l’authentification MFA dans Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes depuis les comptes d’administration

**Aide** : Utilisez les rapports de sécurité Azure Active Directory pour générer des journaux et des alertes lorsque des activités suspectes ou potentiellement dangereuses se produisent dans l’environnement. Utiliser Azure Security Center pour superviser les activités liées aux identités et aux accès. Par ailleurs, vous pouvez tirer parti des journaux de diagnostic Azure Databricks.

* [Guide pratique pour identifier les utilisateurs Azure AD pour lesquels une activité à risque a été signalée](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Guide pratique pour superviser l’activité liée aux identités et aux accès des utilisateurs dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

**Aide** : Utilisez des emplacements nommés à accès conditionnel pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.

* [Guide pratique pour configurer des emplacements nommés dans Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : La solution Azure Databricks est automatiquement configurée pour utiliser l’authentification unique Azure Active Directory permettant d’authentifier les utilisateurs. Les utilisateurs en dehors de votre organisation doivent suivre le processus d’invitation et être ajoutés à votre locataire Active Directory avant de pouvoir se connecter à Azure Databricks via l’authentification unique.

* [Comprendre l’authentification unique pour Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Comment inviter des utilisateurs sur votre locataire Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Azure AD fournit des journaux pour vous aider à découvrir les comptes obsolètes. Par ailleurs, utilisez les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seules les utilisateurs appropriés continuent de bénéficier d’un accès. Vous pouvez également implémenter des API SCIM et des journaux de diagnostic Azure Databricks pour vérifier l’accès des utilisateurs. Vous pouvez également utiliser les API SCIM et les journaux de diagnostic Azure Databricks pour vérifier l’accès des utilisateurs.

De plus, consultez et gérez périodiquement l’accès des utilisateurs dans la console d’administration Azure Databricks.

* [Génération de rapports Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Comment utiliser les révisions d’accès des identités Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Comment gérer l’accès des utilisateurs dans la console d’administration Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11 : Superviser les tentatives d’accès aux comptes désactivés

**Aide** : Vous avez accès aux activités de connexion Azure AD, aux sources des journaux d’événements à risque et d’audit, ce qui vous permet de les intégrer à un outil SIEM/de supervision. Par ailleurs, vous pouvez utiliser les journaux de diagnostic Azure Databricks pour examiner l’activité de l’accès utilisateur.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure Active Directory et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Vous pouvez configurer les alertes souhaitées dans un espace de travail Log Analytics.

* [Comment utiliser les API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12 : Alerte en cas d’écart de comportement de connexion à un compte

**Aide** : Utilisez les fonctionnalités d’Azure AD Identity Risk and Identity Protection pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités d’utilisateur. Vous pouvez également ingérer des données dans Azure Sentinel pour approfondir votre examen. Par ailleurs, vous pouvez utiliser les journaux de diagnostic Azure Databricks pour examiner l’activité de l’accès utilisateur.

* [Guide pratique pour afficher les connexions risquées Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Guide pratique pour intégrer Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

**Aide** : Lorsque des tickets de support sont ouverts, un consentement est demandé par le service clientèle et les techniciens du support technique pour accéder aux données client concernées.

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : actuellement non disponible

## <a name="data-protection"></a>Protection de données

*Pour plus d’informations, consultez [Contrôle de sécurité : Protection des données](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Aide** : Utilisez des étiquettes pour faciliter le suivi des instances Azure Databricks qui traitent des informations sensibles.

* [Guide pratique pour créer et utiliser des étiquettes](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Le déploiement par défaut d’Azure Databricks est un service managé entièrement, qui est déployé au sein même de son réseau virtuel. Si vous avez besoin d’une personnalisation du réseau, vous pouvez déployer Azure Databricks dans votre propre réseau virtuel. Il est recommandé de créer des espaces de travail Azure Databricks distincts pour différents services ou équipes de l’entreprise.

* [Comment déployer Azure Databricks dans votre propre réseau virtuel](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Guide pratique pour créer des abonnements Azure supplémentaires](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Guide pratique pour créer des groupes d’administration](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Guide pratique pour créer un réseau virtuel](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Superviser et bloquer le transfert non autorisé d’informations sensibles

**Aide** : Suivez l’architecture de protection contre l’exfiltration de Databricks pour limiter les risques d’exfiltration de données.

* [Protection contre l’exfiltration des données avec Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Microsoft gère l’infrastructure sous-jacente d’Azure Databricks et a implémenté des contrôles stricts pour empêcher la perte ou l’exposition de données client.

* [Présentation de la protection des données client dans Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : actuellement non disponible

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Aide** : Microsoft négociera TLS 1.2 par défaut lors de l’administration de votre instance Azure Databricks via le portail Azure ou la console Azure Databricks. L’application web Databricks prend en charge TLS 1.3.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Aide** : Actuellement non disponible ; les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas disponibles en ce moment pour Azure Databricks. Étiquetez les instances Azure Databricks et les ressources associées pouvant traiter des informations sensibles en tant que telles, et implémentez une solution tierce à des fins de conformité, si nécessaire.

La plateforme Databricks est dédiée au calcul, et toutes les données sont stockées sur d’autres services de données Azure. Pour la plateforme sous-jacente qui est gérée par Microsoft, Microsoft traite tout le contenu client comme sensible et déploie d'importants efforts pour vous protéger contre la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

* [Présentation de la protection des données client dans Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : actuellement non disponible

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 : Utiliser Azure RBAC pour contrôler l’accès aux ressources

**Aide** : Dans Azure Databricks, vous pouvez utiliser des listes de contrôle d’accès (ACL) pour définir les autorisations d’accès aux tables de données, aux clusters, aux pools, aux travaux et aux objets d’espace de travail tels que les notebooks, les expériences et les dossiers. Les listes de contrôle d’accès peuvent être gérées par tous les utilisateurs administrateurs et par les utilisateurs qui ont reçu des autorisations déléguées pour gérer ces listes. Vous pouvez utiliser Azure RBAC pour définir des autorisations sur l’espace de travail Azure Databricks.

Remarque : Le contrôle d’accès aux tables, clusters, pools, travaux et espaces de travail est disponible uniquement dans le plan Azure Databricks Premium.

* [Comment gérer le contrôle d’accès dans Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7 : Utiliser la protection contre la perte de données basée sur l’hôte pour appliquer le contrôle d’accès

**Aide** : Non applicable. Cette recommandation a trait aux ressources de calcul.

Microsoft gère l’infrastructure sous-jacente d’Azure Databricks et a implémenté des contrôles stricts pour empêcher la perte ou l’exposition de données client.

* [Présentation de la protection des données client dans Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Aide** : Un espace de travail Azure Databricks comprend un plan de gestion hébergé dans un réseau virtuel managé Azure Databricks, et un plan de données qui est déployé dans un réseau virtuel géré par le client. Le plan de contrôle stocke tous les notebooks des utilisateurs et les résultats des notebooks associés dans une base de données. Par défaut, tous les notebooks et leurs résultats sont chiffrés au repos, avec une clé de chiffrement différente.

Databricks File System (DBFS) est un système de fichiers distribué, monté dans un espace de travail Azure Databricks et disponible sur les clusters Azure Databricks. DBFS est implémenté en tant que compte de stockage dans le groupe de ressources managé de votre espace de travail Azure Databricks. Par défaut, le compte de stockage est chiffré à l’aide de clés gérées par Microsoft. Vos données sont stockées dans les services de données Azure dont vous êtes propriétaire, et vous avez le choix de les chiffrer. L’utilisation de DBFS pour stocker les données de production n’est pas recommandée

Remarque : Ces fonctionnalités ne sont pas disponibles pour tous les abonnements Azure Databricks. Contactez votre responsable de comptes Microsoft ou Databricks pour demander l’accès.

* [Comment activer les clés gérées par le client pour les notebooks Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Comment activer les clés gérées par le client pour Azure Databricks File System](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Utilisez Azure Monitor avec le journal d’activités Azure pour créer des alertes lorsque des modifications sont apportées à des espace de travail Azure Databricks critiques.

* [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Contrôle de sécurité : Gestion des vulnérabilités](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Instructions** : Implémentez une solution tierce de gestion des vulnérabilités.

Si vous disposez d’un abonnement à une plateforme de gestion des vulnérabilités, vous pouvez utiliser des scripts d’initialisation Azure Databricks pour installer des agents d’évaluation des vulnérabilités sur vos nœuds de cluster Azure Databricks, et gérer les nœuds par le biais du portail correspondant. Notez que chaque solution tierce fonctionne différemment.

* [Comment installer manuellement l’agent Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Comment installer manuellement l’agent Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts d’initialisation Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 : Déployer une solution de gestion des correctifs de système d’exploitation automatisée

**Aide** : Microsoft tient à jour les images de base des nœuds de cluster Azure Databricks. Vous êtes néanmoins chargé de vous assurer que vos nœuds de cluster bénéficient toujours des correctifs. Pour ajouter une mise à jour de maintenance à un cluster existant en cours d’exécution, vous devez redémarrer le cluster.

* [Comprendre les mises à jour de maintenance du Runtime pour Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 : Déployer une solution de gestion des correctifs logiciels tiers automatisée

**Aide** : Microsoft tient à jour les images de base des nœuds de cluster Azure Databricks. Vous êtes néanmoins chargé de vous assurer que les applications tierces que vous installez bénéficient toujours des correctifs.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 : Comparer les analyses de vulnérabilités dos à dos

**Aide** : Implémentez une solution tierce de gestion des vulnérabilités qui permet de comparer les analyses de vulnérabilités au fil du temps. Si vous disposez d’un abonnement de gestion des vulnérabilités, vous pouvez utiliser le portail de ce fournisseur pour afficher et comparer les analyses de vulnérabilités dos à dos. Notez que chaque solution tierce fonctionne différemment.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classification des risques pour hiérarchiser la correction des vulnérabilités découvertes.

**Conseils** : Utilisez un programme de scoring des risques courants (par exemple, Common Vulnerability Scoring System) ou bien les évaluations des risques par défaut fournies par votre outil d’analyse tiers.

**Supervision d’Azure Security Center** : N/A

**Responsabilité** : Customer

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Contrôle de sécurité : Gestion des stocks et des ressources](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1 : Utiliser la découverte de ressources Azure

**Aide** : Utilisez Azure Resource Graph pour interroger/découvrir toutes les ressources (telles que calcul, stockage, réseau, ports et protocoles) dans vos abonnements. Vérifiez l’existence des autorisations (en lecture) appropriées dans votre locataire, et dénombrez tous les abonnements Azure ainsi que les ressources dans vos abonnements.

Bien que les ressources Azure classiques puissent être découvertes via Resource Graph, il est vivement recommandé de créer et d’utiliser des ressources Azure Resource Manager à l’avenir.

* [Guide pratique pour créer des requêtes avec Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Guide pratique pour afficher ses abonnements Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Comprendre Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Conseils** : Appliquez des balises aux ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories.

* [Guide pratique pour créer et utiliser des étiquettes](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Aide** : Utilisez des étiquettes, des groupes d’administration et des abonnements distincts, le cas échéant, pour organiser et suivre les ressources Azure. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.

Appliquez également des restrictions quant au type de ressources pouvant être créées dans les abonnements clients, en utilisant la stratégie Azure avec les définitions intégrées suivantes :
- Types de ressources non autorisés
- Types de ressources autorisés

* [Guide pratique pour créer des abonnements Azure supplémentaires](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Guide pratique pour créer des groupes d’administration](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Guide pratique pour créer et utiliser des étiquettes](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4 : Tenir un inventaire des ressources Azure et logiciels approuvés

**Aide** : Définissez des ressources Azure approuvées et des logiciels approuvés pour les ressources de calcul.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Instructions** : Appliquez des restrictions quant au type de ressources pouvant être créées dans les abonnements clients, en utilisant une stratégie Azure avec les définitions intégrées suivantes :
- Types de ressources non autorisés
- Types de ressources autorisés

Utilisez Azure Resource Graph pour interroger/découvrir des ressources dans leurs abonnements. Vérifiez que toutes les ressources Azure présentes dans l’environnement sont approuvées.

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Guide pratique pour créer des requêtes avec Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6 : Analyser les applications logicielles non approuvées dans des ressources de calcul

**Aide** : Non applicable ; Azure Databricks est un service PaaS, les clients n’ont pas d’accès direct aux machines virtuelles d’un cluster Azure Databricks.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 : Supprimer des ressources et applications logicielles Azure non approuvées

**Aide** : Utilisez Azure Resource Graph pour interroger/découvrir toutes les ressources (comme le calcul, le stockage, le réseau, les ports, les protocoles, etc.), notamment les instances Azure Databricks, dans vos abonnements. Supprimez toutes les ressources Azure non approuvées que vous découvrez. Concernant les nœuds de cluster Azure Databricks, implémentez une solution tierce pour générer des alertes sur les logiciels non approuvés ou les supprimer.

* [Guide pratique pour créer des requêtes avec Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="68-use-only-approved-applications"></a>6.8 : Utiliser des applications approuvées uniquement

**Aide** : Non applicable ; Azure Databricks est un service PaaS, vous n’avez pas d’accès direct aux machines virtuelles d’un cluster Azure Databricks.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Aide** : Appliquez des restrictions quant au type de ressources pouvant être créées dans les abonnements clients, en utilisant Azure Policy avec les définitions intégrées suivantes :
- Types de ressources non autorisés
- Types de ressources autorisés

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="610-implement-approved-application-list"></a>6.10 : Implémenter une liste d’applications approuvées

**Aide** : Non applicable ; Azure Databricks est un service PaaS, vous n’avez pas d’accès direct aux machines virtuelles d’un cluster Azure Databricks.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11 : <div>Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager via des scripts</div>

**Aide** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ».

* [Comment configurer l’accès conditionnel pour bloquer l’accès à Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12 : Limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul

**Aide** : Non applicable ; cela ne concerne pas Azure Databricks, car les utilisateurs (non-administrateurs) du cluster n’ont pas besoin d’accéder aux nœuds individuels pour exécuter des travaux. L’administrateur de cluster dispose d’un accès racine à tous les nœuds de cluster par défaut.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13 : Séparer physiquement ou logiquement des applications à risque élevé

**Aide** : Non applicable. Le test d’évaluation est destiné à Azure Apps Service ou aux ressources de calcul hébergeant des applications web.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Contrôle de sécurité : Configuration sécurisée](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Aide** : Définissez et implémentez des configurations de sécurité standard pour vos instances Azure Databricks avec Azure Policy. Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.Databricks » pour créer des stratégies personnalisées d’audit ou d’application de la configuration de vos instances Azure Databricks. Notez que les stratégies appliquées ne fonctionnent pas sur le groupe de ressources managé Databricks.

* [Affichage des alias Azure Policy disponibles](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2 : Établir des configurations sécurisées du système d’exploitation

**Aide** : Non applicable ; Azure Databricks est un service PaaS, vous n’avez pas d’accès direct aux machines virtuelles d’un cluster Azure Databricks.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Aide** : Définissez et implémentez des configurations de sécurité standard pour vos instances Azure Databricks avec Azure Policy. Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.Databricks » pour créer des stratégies personnalisées d’audit ou d’application de la configuration de vos instances Azure Databricks. Utilisez Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure.

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Présentation des effets d’Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4 : Préserver la sécurité des configurations du système d'exploitation

**Aide** : Les images des systèmes d’exploitation Azure Databricks sont gérées et tenues à jour par Microsoft. L’implémentation de la configuration d’état au niveau du système d’exploitation vous incombe.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Conseils** : Si vous utilisez des définitions de stratégie Azure personnalisées, utilisez Azure DevOps ou Azure Repos pour stocker et gérer votre code en toute sécurité.

* [Stocker du code dans Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentation Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6 Stocker en toute sécurité des images de système d’exploitation personnalisées

**Aide** : Si vous utilisez des images personnalisées pour vos nœuds de cluster Azure Databricks, envoyez (push) votre image de base personnalisée vers un registre Docker, tel qu’Azure Container Registry (ACR).

* [Comment personnaliser des conteneurs avec les services de conteneur Databricks](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Comprendre Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7 : Déployer les outils de gestion de configuration système

**Aide** : Définissez et implémentez des configurations de sécurité standard pour vos instances Azure Databricks avec Azure Policy. Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.Databricks » pour créer des stratégies personnalisées d’audit ou d’application de la configuration de vos instances Azure Databricks.

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Affichage des alias Azure Policy disponibles](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8 : Déployer les outils de gestion de la configuration système pour les systèmes d’exploitation

**Aide** : Non applicable ; Azure Databricks est un service PaaS, vous n’avez pas d’accès direct aux machines virtuelles d’un cluster Azure Databricks.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 : Implémenter la supervision de la configuration automatique des services Azure

**Aide** : Définissez et implémentez des configurations de sécurité standard pour vos instances Azure Databricks avec Azure Policy. Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.Databricks » pour créer des stratégies personnalisées d’audit ou d’application de la configuration de vos instances Azure Databricks.

* [Affichage des alias Azure Policy disponibles](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Guide pratique pour configurer et gérer Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 : Implémenter la surveillance de la configuration automatique pour les systèmes d’exploitation

**Aide** : Implémentez une solution tierce pour superviser l’état des systèmes d’exploitation de vos nœuds de cluster Azure Databricks.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Aide** : Utilisez Azure Key Vault avec une étendue de secrets Azure Databricks pour gérer et utiliser de manière sécurisée les secrets.

* [Comment utiliser Azure Key Vault avec Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Aide** : Actuellement non disponible ; les identités managées ne sont pas disponibles en ce moment pour Azure Databricks

* [Services prenant en charge les identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : actuellement non disponible

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault.

* [Configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Contrôle de sécurité : Défense contre les programmes malveillants](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 : Utiliser un logiciel anti-programme malveillant géré de manière centralisée

**Aide** : Si vous disposez d’un abonnement à une plateforme de gestion des vulnérabilités, vous pouvez utiliser des scripts d’initialisation Azure Databricks pour installer des agents d’évaluation des vulnérabilités sur vos nœuds de cluster Azure Databricks, et gérer les nœuds par le biais du portail correspondant. Notez que chaque solution tierce fonctionne différemment.

* [Comment installer manuellement l’agent Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Comment installer manuellement l’agent Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts d’initialisation Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Aide** : Microsoft Antimalware est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple, Azure App Service), mais il ne s’exécute pas sur votre contenu.

Pré-analysez les fichiers en cours de chargement vers vos nœuds de cluster Azure Databricks ou les ressources associées.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 : Vérifier que les logiciels et signatures anti-programme malveillant sont mis à jour

**Aide** : Si vous disposez d’un abonnement à une plateforme de gestion des vulnérabilités, vous pouvez utiliser des scripts d’initialisation Azure Databricks pour installer des agents d’évaluation des vulnérabilités sur vos nœuds de cluster Azure Databricks, et gérer les nœuds par le biais du portail correspondant. Notez que chaque solution tierce fonctionne différemment.

* [Comment installer manuellement l’agent Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Comment installer manuellement l’agent Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts d’initialisation Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="data-recovery"></a>Récupération de données

*Pour plus d’informations, consultez [Contrôle de sécurité : Récupération de données](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : garantir des sauvegardes automatisées régulières

**Aide** : Pour vos sources de données Azure Databricks, assurez-vous que vous avez configuré un niveau approprié de redondance des données pour votre cas d’usage. Par exemple, si vous utilisez un compte de stockage Azure comme magasin de données Azure Databricks, choisissez l’option de redondance appropriée (stockage localement redondant, stockage redondant interzone, stockage géoredondant, stockage géographiquement redondant avec accès en lecture).

* [Sources de données pour Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Récupération d’urgence régionale pour les clusters Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : effectuer des sauvegardes complètes du système et sauvegarder les clés gérées par le client

**Aide** : Sauvegardez dans Azure Key Vault les clés gérées par le client qui sont associées à vos implémentations Azure Databricks. Vous pouvez également utiliser l’API REST et l’interface CLI pour créer une sauvegarde quotidienne des configurations Databricks. Vous pouvez également utiliser l’API REST/l’interface CLI pour créer une sauvegarde quotidienne des configurations Databricks.

* [Guide pratique pour sauvegarder des clés de coffre de clés dans Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : valider toutes les sauvegardes, y compris les clés gérées par le client

**Aide** : Testez la restauration des clés sauvegardées qui sont gérées par le client et associées à vos implémentations Azure Databricks.

* [Guide pratique pour restaurer des clés de coffre de clés dans Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : garantir la protection des sauvegardes et des clés gérées par le client

**Aide** : Assurez-vous que la suppression réversible est activée dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante.

* [Comment activer la suppression réversible dans Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Contrôle de sécurité : Réponse aux incidents](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.

* [Comment configurer des automatisations de workflow dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Vous pouvez également tirer parti du guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de votre propre plan de réponse aux incidents](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Conseils** : Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements (par ex. production, non production) et créez un système d’attribution de noms pour identifier et classer les ressources Azure de façon claire.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Conseils** : Exécutez des exercices pour tester les fonctionnalités de réponse aux incidents de vos systèmes de façon régulière. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

* [Reportez-vous à la publication du NIST : Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide de test, d’entraînement et d’utilisation des programmes destinés aux plans et fonctionnalités informatiques)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 : Fournir des informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

**Aide** : Les informations de contact d’incident de sécurité seront utilisées par Microsoft pour vous contacter si Microsoft Security Response Center (MSRC) découvre que les données client ont été utilisées par un tiers illégal ou non autorisé. Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus.

* [Comment définir le contact de sécurité d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Conseils** : Exportez vos alertes et recommandations d’Azure Security Center à l’aide de la fonctionnalité d’exportation continue. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser la sentinelle des alertes.

* [Comment configurer l’exportation continue](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Comment envoyer des alertes à Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Conseils** : Utilisez la fonctionnalité d’automatisation du workflow dans Azure Security Center pour déclencher automatiquement des réponses via « Logic Apps » sur les alertes et recommandations de sécurité.

* [Comment configurer l’automatisation des workflows et Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Contrôle de sécurité : Tests d’intrusion et exercices Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1 : Procédez régulièrement à des tests d’intrusion de vos ressources Azure et veillez à résoudre dans un délai de 60 jours tous les problèmes de sécurité critiques détectés.

**Aide** : * [Respectez les règles d’engagement de Microsoft pour garantir que vos tests d’intrusion sont conformes aux stratégies Microsoft.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Vous trouverez ici plus d’informations sur la stratégie de Microsoft, sur l’exécution de Red Teaming et sur les tests d’intrusion de site actif dans l’infrastructure, les services et les applications cloud gérés par Microsoft.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

## <a name="next-steps"></a>Étapes suivantes

- Consultez le [benchmark de sécurité Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Apprenez-en davantage sur les [bases de référence de sécurité Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
