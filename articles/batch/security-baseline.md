---
title: Base de référence de sécurité Azure pour Batch
description: Base de référence de sécurité Azure pour Batch
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4094ace5772b99c646a31d6a4722bbb080fbfff7
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589652"
---
# <a name="azure-security-baseline-for-batch"></a>Base de référence de sécurité Azure pour Batch

La base de référence de sécurité Azure pour Batch contient des recommandations qui vous aideront à améliorer la situation de sécurité de votre déploiement.

La base de référence pour ces services est tirée du [benchmark de sécurité Azure version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) qui fournit des recommandations sur la façon de sécuriser des solutions cloud sur Azure en suivant nos conseils sur les bonnes pratiques.

Pour plus d’informations, consultez [Vue d’ensemble des références de base de la sécurité Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Contrôle de sécurité : Sécurité réseau](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 : Protéger les ressources à l'aide de groupes de sécurité réseau ou du Pare-feu Azure sur votre réseau virtuel

**Conseils** : Déployez le ou les pools Azure Batch dans le réseau virtuel. Pour permettre aux nœuds de calcul d’un pool de communiquer en toute sécurité avec d’autres machines virtuelles, ou avec un réseau local, vous pouvez provisionner le pool dans un sous-réseau d’un réseau virtuel Azure. En outre, en déployant le pool au sein d’un réseau virtuel, vous pouvez ensuite contrôler le groupe de sécurité réseau (NSG) utilisé pour sécuriser les interfaces réseau (NIC) des différents nœuds ainsi que le sous-réseau. Configurez le groupe de sécurité réseau pour autoriser le trafic uniquement à partir d’adresses IP/emplacements approuvés sur Internet.


Créer un pool Azure Batch dans un réseau virtuel :

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des cartes réseau

**Conseils** : Utilisez Azure Security Center et suivez les recommandations de protection du réseau applicables au réseau virtuel/groupe de sécurité réseau associé à votre pool Batch. Activez les journaux de flux sur le groupe de sécurité réseau utilisé pour protéger votre pool Batch et envoyez les journaux vers un compte de stockage Azure en vue de l’audit du trafic. Vous pouvez aussi envoyer ces journaux vers un espace de travail Azure Log Analytics et utiliser Azure Traffic Analytics pour fournir des insights sur le flux de trafic dans votre cloud Azure. Azure Traffic Analytics offre certains avantages, parmi lesquels la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, de détecter les menaces de sécurité, de bien comprendre les modèles de flux de trafic et enfin de repérer les configurations réseau incorrectes.


Activer les journaux de flux NSG :

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Activer et utiliser Traffic Analytics :

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Comprendre la sécurité réseau fournie par Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="13-protect-critical-web-applications"></a>1.3 : Protéger les applications web critiques

**Conseils** : Non applicable. Le benchmark concerne les applications web s’exécutant sur Azure App Service ou des ressources IaaS.

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Conseils** : Activez le service Protection DDoS (déni de service distribué) Standard Azure sur le réseau virtuel protégeant votre pool Azure Batch afin d’assurer la protection contre les attaques DDoS. Utilisez la fonctionnalité Threat Intelligence intégrée à Azure Security Center pour refuser les communications avec des adresses IP Internet connues comme étant malveillantes ou inutilisées.


Configurer la protection DDoS :

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Comprendre la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5 : Journaliser les paquets et les flux réseau

**Conseils** : Activez les journaux de flux sur le groupe de sécurité réseau (NSG) utilisé pour protéger votre pool Azure Batch et envoyez les journaux vers un compte de stockage Azure en vue de l’audit du trafic.


Activer les journaux de flux NSG :

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions sur le réseau

**Conseils** : Si cela est nécessaire à des fins de conformité, sélectionnez une appliance virtuelle réseau dans la Place de marché Azure qui prend en charge les systèmes de détection des intrusions (IDS) et les systèmes de prévention des intrusions (IPS) avec des fonctionnalités d’inspection de la charge utile.


Si la détection et/ou la prévention des intrusions basées sur l’inspection de la charge utile ne font pas partie des exigences, vous pouvez utiliser le Pare-feu Azure avec la Threat Intelligence. Le filtrage basé sur la Threat Intelligence du Pare-feu Azure peut générer des alertes et refuser le trafic depuis ou vers des adresses IP et des domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence.


Déployez le Pare-feu Azure avec une adresse IP publique dans le même réseau virtuel que vos nœuds de pool Azure Batch. Configurez des règles de traduction d’adresses réseau (NAT) entre des emplacements approuvés sur Internet et les adresses IP privées de vos nœuds de pool individuels. Dans le Pare-feu Azure, sous Threat Intelligence, configurez « Alerter et refuser » pour recevoir des alertes et bloquer le trafic en provenance ou à destination d’adresses IP et de domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence ; seuls les enregistrements au niveau de confiance le plus élevé sont inclus. 


Créer un pool Azure Batch dans un réseau virtuel :

https://docs.microsoft.com/azure/batch/batch-virtual-network


Déployer le Pare-feu Azure :

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal


Place de marché Azure :

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7 : Gérer le trafic à destination de vos applications web

**Conseils** : Non applicable. Le benchmark concerne les applications web s’exécutant sur Azure App Service ou des ressources IaaS.

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Conseils** : Utilisez des étiquettes de service de réseau virtuel pour définir des contrôles d’accès réseau sur les groupes de sécurité réseau ou les Pare-feu Azure associés à vos pools Azure Batch. Vous pouvez utiliser des étiquettes de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de l’étiquette de service (par exemple, ApiManagement) dans le champ Source ou Destination approprié d'une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par l’étiquette de service et met à jour automatiquement l’étiquette de service quand les adresses changent.


Comprendre et utiliser les étiquettes de service :

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les appareils réseau

**Conseils** : Définissez et implémentez des configurations de sécurité standard pour les ressources réseau associées à vos pools Azure Batch à l’aide d’Azure Policy. Utilisez des alias Azure Policy dans les espaces de noms « Microsoft.Batch » et « Microsoft.Network » pour créer des stratégies personnalisées d’audit ou d’application de la configuration réseau de vos pools Azure Batch.



Configurer et gérer Azure Policy :

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Documenter les règles de configuration du trafic

**Conseils** : Utilisez des étiquettes pour les groupes de sécurité réseau et autres ressources liées à la sécurité réseau et au flux de trafic qui sont associés à vos pools Azure Batch. Concernant les règles NSG individuelles, utilisez le champ « Description » pour spécifier le besoin métier et/ou la durée (etc.) de chaque règle qui autorise le trafic vers/depuis un réseau.


Utilisez l’une des définitions de stratégie Azure intégrées en lien avec l’étiquetage, comme « Exiger une étiquette et sa valeur », pour vous assurer que toutes les ressources créées sont étiquetées et être informé de l’existence de ressources non étiquetées.


Vous pouvez utiliser Azure PowerShell ou Azure CLI pour rechercher des ressources ou effectuer des actions sur des ressources en fonction de leurs étiquettes.


Créer et utiliser des étiquettes :

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


Créer un réseau virtuel :

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


Créer un groupe de sécurité réseau :

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les changements

**Conseils** : Utilisez le journal des activités Azure pour superviser les configurations des ressources réseau et détecter les changements des ressources réseau associées à vos pools Azure Batch. Dans Azure Monitor, créez des alertes qui doivent se déclencher quand des changements sont apportés à des ressources réseau critiques.

Consulter et récupérer les événements du journal des activités Azure : https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Créer des alertes dans Azure Monitor : https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Contrôle de sécurité : Journalisation et supervision](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1 : Utiliser des sources de synchronisation date/heure approuvées

**Conseils** : Pour Azure Batch, par défaut, Microsoft utilise la synchronisation date/heure. Toutefois, si vous avez des exigences spécifiques en matière de synchronisation date/heure, vous pouvez implémenter ces changements.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Conseils** : Intégrez le compte Azure Batch à Azure Monitor pour agréger les données de sécurité générées par les appareils de cluster. Utilisez des requêtes personnalisées pour détecter les menaces dans l’environnement et y répondre.  Dans le cadre d’une supervision au niveau des ressources Azure Batch, utilisez les API Batch pour surveiller ou interroger l’état de toutes vos ressources, y compris les travaux, les tâches, les nœuds et les pools.



Intégrer un compte Azure Batch à Azure Monitor :

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Conseils** : Dans le cadre d’une supervision au niveau du compte Azure Batch, supervisez chaque compte Batch à l’aide des fonctionnalités fournies dans Azure Monitor. Azure Monitor collecte des métriques et éventuellement des journaux de diagnostic pour les ressources au niveau du compte Batch, comme les pools, les travaux et les tâches. Collectez et utilisez ces données manuellement ou par programmation pour surveiller les activités dans votre compte Batch et diagnostiquer les problèmes.


Dans le cadre d’une supervision au niveau des ressources Azure Batch, utilisez les API Azure Batch pour surveiller ou interroger l’état de toutes vos ressources, y compris les travaux, les tâches, les nœuds et les pools.


Configurer la supervision et la journalisation au niveau du compte Azure Batch :

https://docs.microsoft.com/azure/batch/monitoring-overview


Comprendre la supervision au niveau des ressources Batch :

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="24-collect-security-logs-from-operating-system"></a>2.4 : Collecter les journaux de sécurité des systèmes d’exploitation

**Conseils** : Azure Monitor collecte des métriques et des journaux de diagnostic pour les ressources de votre compte Azure Batch. Collectez et utilisez ces données de plusieurs façons pour superviser votre compte Azure Batch et diagnostiquer les problèmes. Vous pouvez également configurer des alertes de métrique afin de recevoir des notifications lorsqu’une métrique atteint une valeur spécifiée.


Si nécessaire, vous devrez vous connecter à vos nœuds de pool individuels par le biais du protocole SSH (Secure Shell) ou du protocole RDP (Remote Desktop Protocol) pour accéder aux journaux du système d’exploitation local.


Collecter les journaux de diagnostic à partir de votre compte Azure Batch :

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics


Se connecter à distance à vos nœuds de pool Azure Batch :

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Conseils** : Intégrez le compte Azure Batch à Azure Monitor. Vérifiez que la période de conservation des journaux définie dans l’espace de travail Azure Log Analytics utilisé est conforme aux obligations réglementaires de votre organisation.


Configurer la supervision et la journalisation dans Azure Batch :

https://docs.microsoft.com/azure/batch/monitoring-overview


Configurer la période de conservation des journaux dans l’espace de travail Azure Log Analytics :

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Conseils** : Créez des alertes de métrique Azure Batch qui se déclenchent quand la valeur d’une métrique spécifiée dépasse un seuil donné.


Configurer des alertes de métrique Azure Batch :

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 : Activer les alertes d’activité anormale

**Conseils** : Créez des alertes de métrique Azure Batch qui se déclenchent quand la valeur d’une métrique spécifiée dépasse un seuil donné.


Configurer des alertes de métrique Azure Batch :

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8 : Centraliser la journalisation anti-programme malveillant

**Conseils** : Exécutez Windows Defender sur chacun de vos nœuds Batch si vous utilisez un système d’exploitation Windows, ou fournissez votre propre solution anti-programme malveillant si vous travaillez sur Linux.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="29-enable-dns-query-logging"></a>2.9 : Activer la journalisation des requêtes DNS

**Conseils** : Implémentez une solution tierce pour la journalisation DNS.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10 : Activer la journalisation d’audit en ligne de commande

**Conseils** : Configurez manuellement la journalisation de la console et la transcription PowerShell au niveau de chaque nœud.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Contrôle de sécurité : Contrôle des accès et des identités](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Conseils** : Conservez l’enregistrement du compte d’administrateur local créé lors du provisionnement du pool Azure Batch ainsi que des autres comptes que vous créez. De plus, si l’intégration Azure Active Directory (AAD) est utilisée, AAD fournit des rôles intégrés qui doivent être explicitement attribués et qui peuvent donc être interrogés. Utilisez le module AAD PowerShell pour effectuer des requêtes ad hoc afin de découvrir les comptes membres de groupes d’administration.


Suivez également les recommandations liées à la gestion des identités et des accès dans Azure Security Center.


Obtenir un rôle d’annuaire dans AAD avec PowerShell :

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Obtenir les membres d’un rôle d’annuaire dans AAD avec PowerShell :

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


Superviser les identités et les accès dans Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Changer les mots de passe par défaut quand cela est possible

**Conseils** : Quand vous provisionnez un pool Azure Batch, vous avez l’option de créer des comptes de machine locale. Il n’y a pas de mot de passe par défaut à changer. Toutefois, vous pouvez spécifier des mots de passe différents pour l’accès SSH (Secure Shell) et l’accès RDP (Remote Desktop Protocol). Une fois que vous avez configuré le pool Azure Batch, vous pouvez générer un utilisateur aléatoire pour chaque nœud à partir du portail Azure ou de l’API Azure Resource Manager.


Ajouter un utilisateur à un nœud de calcul spécifique :

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3 : Garantir l’utilisation de comptes d’administration dédiés

**Conseils** : Intégrez l’authentification des applications Azure Batch avec Azure Active Directory. Créez des stratégies et des procédures qui contrôlent l’utilisation de comptes d’administration dédiés.


Suivez également les recommandations liées à la gestion des identités et des accès dans Azure Security Center.


Authentifier des applications Batch avec Azure Active Directory :

https://docs.microsoft.com/azure/batch/batch-aad-auth


Superviser les identités et les accès dans Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4 : Utiliser l’authentification unique (SSO) avec Azure Active Directory

**Conseils** : Non applicable. Azure Batch prend en charge l’authentification Azure AD, mais pas l’authentification unique.

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseils** : Intégrez l’authentification des applications Azure Batch avec Azure Active Directory (AAD). Activez l’authentification multifacteur (MFA) AAD et suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center.
 


Comment activer l’authentification multifacteur dans Azure :

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Comment surveiller l’identité et l’accès dans Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des machines dédiées (stations de travail à accès privilégié) pour toutes les tâches administratives

**Conseils** : Utilisez des stations de travail à accès privilégié avec l’authentification multifacteur (MFA) configurée pour la connexion à vos ressources Azure Batch et leur configuration.


En savoir plus sur les stations de travail avec accès privilégié :

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Comment activer l’authentification multifacteur dans Azure :

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes depuis les comptes d’administration

**Conseils** : Si vous avez intégré l’authentification des applications Azure Batch avec Azure Active Directory (AAD), utilisez les rapports de sécurité d’Azure Active Directory pour générer des journaux et des alertes lorsque des activités suspectes ou potentiellement dangereuses se produisent dans l’environnement. Utiliser Azure Security Center pour superviser les activités liées aux identités et aux accès.


Comment identifier les utilisateurs Azure AD signalés pour une activité à risque :

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


Superviser les activités liées aux identités et aux accès des utilisateurs dans Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir d’emplacements approuvés uniquement

**Conseils** : Si vous avez intégré l’authentification des applications Azure Batch avec Azure Active Directory, vous pouvez utiliser les emplacements nommés de l’accès conditionnel pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.



Comment configurer des emplacements nommés dans Azure :

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Conseils** : Utilisez Azure Active Directory (AAD) comme système central d’authentification et d’autorisation, et intégrez l’authentification des applications Azure Batch avec AAD. AAD protège les données à l’aide d’un chiffrement renforcé pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.


Comment créer et configurer une instance AAD :

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance


Authentifier les applications Batch avec AAD :

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Revoir et harmoniser régulièrement les accès utilisateur

**Conseils** : Azure Active Directory(AAD) fournit des journaux pour vous aider à découvrir les comptes obsolètes. De plus, vous pouvez utiliser les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. Les accès des utilisateurs peuvent être revus régulièrement pour vérifier que seuls les utilisateurs appropriés continuent de bénéficier d’un accès.


Comment utiliser les révisions d’accès des identités Azure :

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11 : Superviser les tentatives d’accès aux comptes désactivés

**Conseils** : Créez des paramètres de diagnostic pour les comptes d’utilisateur Azure Active Directory, en envoyant les journaux d’audit et les journaux de connexion vers un espace de travail Azure Log Analytics. Configurez les alertes souhaitées dans un espace de travail Azure Log Analytics.


Comment intégrer des journaux d’activité Azure dans Azure Monitor :

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Conseils** : Utilisez les détections de risques et la fonctionnalité Identity Risk d’Azure Active Directory (AAD) pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités d’utilisateur. De plus, vous pouvez ingérer des données dans Azure Sentinel pour approfondir votre examen.


Examiner les connexions risquées à AAD :

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Comment configurer et activer des stratégies de protection des identités :

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


Comment intégrer Azure Sentinel :

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

**Conseils** : Non disponible. Customer Lockbox n’est pas actuellement pris en charge pour Azure Batch. Liste des services pris en charge pour Customer Lockbox : https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Contrôle de sécurité : Protection des données](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Conseils** : Utilisez des étiquettes pour faciliter le suivi des ressources Azure qui stockent ou traitent des informations sensibles.


Créer et utiliser des étiquettes :

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Les pools Azure Batch doivent être séparés par un réseau/sous-réseau virtuel étiqueté de manière appropriée, mais aussi sécurisés à l’aide de groupes de sécurité réseau (NSG). Les données Azure Batch doivent être stockées dans un compte de stockage Azure sécurisé.


Comment créer un pool Azure Batch dans un réseau virtuel :

https://docs.microsoft.com/azure/batch/batch-virtual-network


Comment sécuriser des comptes de stockage Azure :

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Superviser et bloquer le transfert non autorisé d’informations sensibles

**Conseils** : Si vous avez des comptes de stockage Azure associés à votre ou vos pools Azure Batch qui contiennent des informations sensibles, marquez-les comme sensibles à l’aide d’étiquettes et sécurisez-les en suivant les bonnes pratiques Azure en la matière.


Les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour le Stockage Azure ou les ressources de calcul. Implémentez une solution tierce si nécessaire à des fins de conformité.


Pour la plateforme sous-jacente managée par Microsoft, Microsoft considère tout le contenu client comme sensible et met tout en œuvre pour empêcher la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour un ensemble robuste de contrôles et de fonctionnalités de protection des données.


Comprendre la protection des données client dans Azure :

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


Comment sécuriser des comptes de stockage Azure :

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Conseils** : Chiffrer toutes les informations sensibles en transit. Par défaut, les ressources Microsoft Azure négocient TLS 1.2. Assurez-vous que les clients qui se connectent à vos pools Azure Batch ou magasins de données (comptes Stockage Azure) sont en mesure de négocier TLS 1.2 ou une version ultérieure.


Assurez-vous aussi que le protocole HTTPS est obligatoire pour accéder au compte de stockage contenant vos données Azure Batch.


Comprendre le chiffrement en transit pour un compte de stockage Azure  :

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Supervision Azure Security Center** : Oui

**Responsabilité** : Partagé

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Conseils** : Si vous avez des comptes de stockage Azure associés à votre ou vos pools Azure Batch qui contiennent des informations sensibles, marquez-les comme sensibles à l’aide d’étiquettes et sécurisez-les en suivant les bonnes pratiques Azure en la matière.


Les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour le Stockage Azure ou les ressources de calcul. Implémentez une solution tierce si nécessaire à des fins de conformité.


Pour la plateforme sous-jacente managée par Microsoft, Microsoft considère tout le contenu client comme sensible et met tout en œuvre pour empêcher la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour un ensemble robuste de contrôles et de fonctionnalités de protection des données.


Comprendre la protection des données client dans Azure :

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


Comment sécuriser des comptes de stockage Azure :

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Supervision Azure Security Center** : Oui

**Responsabilité** : Partagé

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 : Utiliser Azure RBAC pour contrôler l’accès aux ressources

**Conseils** : Utilisez le contrôle d’accès en fonction du rôle d’Azure Active Directory (AAD) pour contrôler l’accès au plan de gestion des ressources Azure telles que le compte Batch, les pools Batch et les comptes de stockage.


Comprendre Azure RBAC :

https://docs.microsoft.com/azure/role-based-access-control/overview


Configuration de RBAC dans Azure :

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7 : Utiliser la protection contre la perte de données basée sur l’hôte pour appliquer le contrôle d’accès

**Conseils** : Les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour le Stockage Azure ou les ressources de calcul. Implémentez une solution tierce si nécessaire à des fins de conformité.



Pour la plateforme sous-jacente managée par Microsoft, Microsoft considère tout le contenu client comme sensible et met tout en œuvre pour empêcher la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour un ensemble robuste de contrôles et de fonctionnalités de protection des données.



Comprendre la protection des données client dans Azure :

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Supervision Azure Security Center** : Oui

**Responsabilité** : Partagé

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Conseils** : Pour les comptes de stockage associés à votre compte Azure Batch, nous vous recommandons d’autoriser Microsoft à gérer les clés de chiffrement. Toutefois, vous avez la possibilité de gérer vos propres clés si nécessaire.



Comment gérer les clés de chiffrement des comptes de stockage Azure :

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Conseils** : Utilisez Azure Monitor avec le journal des activités Azure pour créer des alertes quand des modifications sont apportées à des ressources Azure critiques liées ou associées à vos comptes/pools Azure Batch.



Configurez les paramètres de diagnostic des comptes de stockage associés à des pools Azure Batch pour superviser et journaliser toutes les opérations CRUD sur les données de pool.



Comment créer des alertes pour les événements du journal des activités Azure :

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



Comment activer la journalisation/l’audit supplémentaire pour un compte de stockage Azure :

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Contrôle de sécurité : Gestion des vulnérabilités](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Conseils** : Pour les nœuds de pool Azure Batch, la gestion de la solution de gestion des vulnérabilités vous incombe.


Éventuellement, si vous avez un abonnement à Rapid7, à Qualys ou à toute autre plateforme de gestion des vulnérabilités, vous pouvez installer manuellement des agents d’évaluation des vulnérabilités sur les nœuds de pool Batch et gérer ces nœuds par le biais du portail en question.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 : Déployer une solution de gestion des correctifs de système d’exploitation automatisée

**Conseils** : Les images des nœuds de pool Azure Batch de base sont gérées et tenues à jour par Microsoft. Assurez-vous de garder le système d’exploitation des nœuds de pool Azure Batch à jour avec les correctifs pendant toute la durée de vie du cluster, ce qui peut nécessiter l’activation des mises à jour automatiques, la supervision des nœuds ou des redémarrages périodiques.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Partagé

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 : Déployer une solution de gestion automatisée des correctifs logiciels tiers

**Conseils** : Assurez-vous de garder les applications tierces installées sur les nœuds de pool Azure Batch à jour avec les correctifs pendant toute la durée de vie du cluster, ce qui peut nécessiter l’activation des mises à jour automatiques, la supervision des nœuds ou des redémarrages périodiques.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 : Comparer les analyses de vulnérabilités dos à dos

**Conseils** : Si vous avez un abonnement à Rapid7, à Qualys ou à toute autre plateforme de gestion des vulnérabilités, vous pouvez utiliser le portail de ce fournisseur pour consulter et comparer les analyses de vulnérabilités dos à dos.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classification des risques pour hiérarchiser la correction des vulnérabilités découvertes.

**Conseils** : Utilisez un programme de scoring des risques courants (par exemple, Common Vulnerability Scoring System) ou bien les évaluations des risques par défaut fournies par votre outil d’analyse tiers.

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Contrôle de sécurité : Gestion des stocks et des ressources](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1 : Utiliser la découverte de ressources Azure

**Conseils** : Utilisez Azure Resource Graph pour interroger/découvrir toutes les ressources (calcul, stockage, réseau, etc.) dans vos abonnements. Vérifiez que vous avez les autorisations (lecture) appropriées dans votre locataire et que vous êtes en mesure d’établir une liste de tous les abonnements Azure et de toutes les ressources dans vos abonnements.


Bien que les ressources Azure classiques puissent être découvertes via Resource Graph, il est vivement recommandé de créer et d’utiliser des ressources Azure Resource Manager à l’avenir.


Créer des requêtes avec Azure Graph :

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Afficher vos abonnements Azure :

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Comprendre Azure RBAC :

https://docs.microsoft.com/azure/role-based-access-control/overview

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Conseils** : Appliquez des étiquettes aux ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories.


Créer et utiliser des étiquettes :

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Conseils** : Utilisez des étiquettes, des groupes d’administration et diviser des abonnements, le cas échéant, pour organiser et suivre les ressources. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.



Créer des abonnements Azure supplémentaires :

https://docs.microsoft.com/azure/billing/billing-create-subscription



Créer des groupes d’administration :

https://docs.microsoft.com/azure/governance/management-groups/create



Créer et utiliser des étiquettes :

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4 : Tenir un inventaire des ressources Azure et logiciels approuvés

**Conseils** : Établissez la liste des ressources Azure approuvées et des logiciels approuvés pour les ressources de calcul.

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Superviser les ressources Azure non approuvées

**Conseils** : Appliquez des restrictions quant au type de ressources pouvant être créées dans les abonnements clients, en utilisant une stratégie Azure avec les définitions intégrées suivantes :

- Types de ressources non autorisés
- Types de ressources autorisés

Utilisez Azure Resource Graph pour interroger/découvrir les ressources dans vos abonnements. Vérifiez que toutes les ressources Azure présentes dans l’environnement sont approuvées.

Configurer et gérer Azure Policy : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Créer des requêtes avec Azure Graph : https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6 : Détecter les applications logicielles non approuvées dans les ressources de calcul

**Conseils** : Pour les nœuds de pool Azure Batch, implémentez une solution tierce qui détecte la présence d’applications logicielles non approuvées sur les nœuds de cluster.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 : Supprimer les ressources Azure et les applications logicielles non approuvées

**Conseils** : Pour les nœuds de pool Azure Batch, implémentez une solution tierce qui détecte la présence d’applications logicielles non approuvées sur les nœuds de cluster.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="68-use-only-approved-applications"></a>6.8 : Utiliser des applications approuvées uniquement

**Conseils** : Pour les nœuds de pool Azure Batch, implémentez une solution tierce qui empêche l’exécution de logiciels non autorisés.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Conseils** : Appliquez des restrictions quant au type de ressources pouvant être créées dans les abonnements clients, en utilisant une stratégie Azure avec les définitions intégrées suivantes :

- Types de ressources non autorisés
- Types de ressources autorisés

Configurer et gérer Azure Policy :

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Refuser un type de ressource spécifique avec Azure Policy :

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="610-implement-approved-application-list"></a>6.10 : Implémenter une liste d’applications approuvées

**Conseils** : Pour les nœuds de pool Azure Batch, implémentez une solution tierce qui empêche l’exécution de types de fichiers non autorisés.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager via des scripts

**Conseils** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ».


Configurer l’accès conditionnel pour bloquer l’accès à Azure Resource Manager :

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12 : Limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul

**Conseils** : Non applicable.

Cela ne concerne pas Azure Batch, car les utilisateurs (non-administrateurs) des pools Azure Batch n’ont pas besoin d’accéder aux nœuds individuels pour exécuter des travaux. L’administrateur de cluster a déjà accès à la racine de tous les nœuds.

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13 : Séparer physiquement ou logiquement des applications à risque élevé

**Conseils** : Non disponible.

Le benchmark concerne les applications web s’exécutant sur Azure App Service ou des ressources IaaS.

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Contrôle de sécurité : Configuration sécurisée](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Conseils** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.Batch » pour créer des stratégies personnalisées d’audit ou d’application de la configuration de vos comptes et pools Azure Batch.


Afficher les alias Azure Policy disponibles : https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Configurer et gérer Azure Policy : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2 : Établir des configurations sécurisées pour votre système d’exploitation

**Conseils** : Établissez des configurations sécurisées pour le système d’exploitation de vos nœuds Batch Pool.

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3 : Maintenir des configurations sécurisées pour toutes les ressources Azure

**Conseils** : Utilisez une stratégie Azure [refuser] et [déployer en l’absence] pour appliquer des paramètres sécurisés aux ressources Azure associées à votre compte et vos pools Batch (tels que les réseaux virtuels, les sous-réseaux, les Pare-feu Azure, les comptes de stockage Azure, etc.). Vous pouvez utiliser les alias Azure Policy des espaces de noms suivants pour créer des stratégies personnalisées :

- Microsoft.Batch
- Microsoft.Storage
- Microsoft.Network

Configurer et gérer Azure Policy : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Comprendre les effets d’Azure Policy : https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4 : Maintenir des configurations sécurisées pour les systèmes d’exploitation

**Conseils** : Les images des systèmes d’exploitation des pools Azure Batch sont gérées et tenues à jour par Microsoft. L’implémentation de la configuration d’état au niveau du système d’exploitation vous incombe.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Partagé

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Conseils** : Si vous avez des définitions de stratégie Azure personnalisées pour vos comptes ou pools Azure Batch, ou pour des ressources associées, utilisez Azure DevOps/Repos pour stocker et gérer votre code en toute sécurité.

Stocker du code dans Azure DevOps : https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentation Azure Repos : https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6 Stocker en toute sécurité des images de système d’exploitation personnalisées

**Conseils** : Si vous utilisez des images personnalisées pour vos pools Azure Batch, utilisez le contrôle d’accès en fonction du rôle (RBAC) pour garantir que seuls les utilisateurs autorisés pourront accéder aux images.


Comprendre le contrôle d’accès en fonction du rôle dans Azure : https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Configurer le contrôle d’accès en fonction du rôle dans Azure : https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7 : Déployer les outils de gestion de la configuration système

**Conseils** : Utilisez des définitions Azure Policy intégrées pour générer des alertes ainsi qu’auditer et appliquer les configurations des ressources liées à Azure Batch.  Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.Batch » pour créer des stratégies personnalisées pour vos comptes et pools Azure Batch. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.



Configurer et gérer Azure Policy :

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8 : Déployer les outils de gestion de la configuration système pour les systèmes d’exploitation

**Conseils** : Implémentez une solution tierce qui maintient l’état souhaité pour les systèmes d’exploitation de vos nœuds de pool Azure Batch.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 : Mettre en place la supervision de la configuration automatique pour les services Azure

**Conseils** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.Batch » pour créer des stratégies personnalisées d’audit ou d’application de la configuration de votre instance Azure Batch. Vous pouvez également employer des stratégies intégrées créées spécifiquement pour Azure Batch ou les ressources utilisées par Azure Batch, par exemple :

- Les sous-réseaux doivent être associés à un groupe de sécurité réseau
- Les comptes de stockage doivent utiliser un point de terminaison de service de réseau virtuel
- Les journaux de diagnostic doivent être activés dans les comptes Batch

Afficher les alias Azure Policy disponibles : https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Configurer et gérer Azure Policy : https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 : Implémenter la supervision de la configuration automatique pour les systèmes d’exploitation

**Conseils** : Implémentez une solution tierce pour superviser l’état des systèmes d’exploitation de vos nœuds de pool Azure Batch.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="711-securely-manage-azure-secrets"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Conseils** : Azure Key Vault peut être utilisé avec des déploiements Azure Batch pour gérer les clés du stockage de pools dans des comptes de stockage Azure.


Intégration aux identités managées Azure :

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity


Créer un coffre Azure Key Vault :

https://docs.microsoft.com/azure/key-vault/quick-create-portal


Fournir une authentification Key Vault avec une identité managée :

https://docs.microsoft.com/azure/key-vault/managed-identity

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="712-securely-and-automatically-manage-identities"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Conseils** : Non disponible. Managed Service Identity n’est pas pris en charge par Azure Batch.

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Non applicable

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault. 

Configuration de Credential Scanner : https://secdevtools.azurewebsites.net/helpcredscan.html

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Contrôle de sécurité : Défense contre les programmes malveillants](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 : Utiliser un logiciel anti-programme malveillant géré de manière centralisée

**Conseils** : Exécutez Windows Defender sur vos nœuds de pool Azure Batch individuels si vous utilisez un système d’exploitation Windows, ou fournissez votre propre solution anti-programme malveillant si vous travaillez sur Linux.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Conseils** : Microsoft Antimalware est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple, Azure Batch), mais il ne s’exécute pas sur du contenu client.


Pré-analysez les fichiers chargés sur des ressources Azure non liées au calcul, comme App Service, des Data Lake Storage, Stockage Blob, etc. Microsoft ne peut pas accéder aux données client dans ces instances.


Comprendre Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles :

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Partagé

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 : Vérifier que les logiciels et signatures anti-programme malveillant sont à jour

**Conseils** : Exécutez Windows Defender sur vos nœuds de pool Azure Batch individuels si vous utilisez un système d’exploitation Windows et vérifiez que la mise à jour automatique est activée. Fournissez votre propre solution anti-programme malveillant si vous êtes sur Linux.

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Contrôle de sécurité : Récupération de données](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Mettre en place des sauvegardes automatiques régulières

**Conseils** : Si vous utilisez un compte de stockage Azure comme magasin de données de pool Azure Batch, choisissez l’option de redondance appropriée (stockage localement redondant, stockage redondant interzone, stockage géoredondant, stockage géographiquement redondant avec accès en lecture). 


Configurer la redondance du stockage pour les comptes de stockage Azure :

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés gérées par le client

**Conseils** : Si vous utilisez un compte de stockage Azure comme magasin de données de pool Azure Batch, choisissez l’option de redondance appropriée (stockage localement redondant, stockage redondant interzone, stockage géoredondant, stockage géographiquement redondant avec accès en lecture).  Si vous utilisez Azure Key Vault pour n’importe quelle partie de votre déploiement Azure Batch, vérifiez que vos clés sont sauvegardées.


Configurer la redondance du stockage pour les comptes de stockage Azure :

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Comment sauvegarder des clés de coffre de clés dans Azure :

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Conseils** : Si vous gérez vos propres clés pour des comptes de stockage Azure ou toute autre ressource associée à votre implémentation d’Azure Batch, effectuez régulièrement des tests de restauration des clés sauvegardées.


Comment sauvegarder des clés de coffre de clés dans Azure :

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


Restaurer une clé gérée par le client avec PowerShell :

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés gérées par le client

**Conseils** : Si Azure Key Vault est utilisé pour conserver les clés associées aux comptes de stockage de pool Azure Batch, activez la suppression réversible dans Azure Key Vault pour protéger les clés contre toute suppression accidentelle ou malveillante.


Activer la suppression réversible dans Azure Key Vault :

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Contrôle de sécurité : Réponse aux incidents](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Vérifiez qu’il existe des plans de réponse aux incidents écrits qui définissent les rôles du personnel ainsi que les phases de gestion des incidents.



Comment configurer des automations de flux de travail dans Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de scoring et de hiérarchisation des incidents

**Conseils** : Security Center affecte un degré de gravité aux alertes pour vous aider à hiérarchiser l’ordre dans lequel vous remédiez à chaque alerte. Ainsi, quand une ressource est compromise, vous pouvez vous en occuper immédiatement. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Conseils** : Exécutez des exercices pour tester les fonctionnalités de réponse aux incidents de vos systèmes de façon régulière. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

Reportez-vous à la publication du NIST : « Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities » : https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4 : Fournir les informations de contact pour les incidents de sécurité et configurer des notifications d’alerte&nbsp;pour les incidents de sécurité

**Conseils** : Les informations de contact pour les incidents de sécurité seront utilisées par Microsoft afin de vous contacter si Microsoft Security Response Center (MSRC) découvre que vos données ont été utilisées de manière illégale ou par un tiers non autorisé.



Comment définir le contact de sécurité Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Supervision Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Conseils** : Exportez vos alertes et recommandations d’Azure Security Center à l’aide de la fonctionnalité d’exportation continue. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour envoyer les alertes à Azure Sentinel.


Comment configurer l’exportation continue :

https://docs.microsoft.com/azure/security-center/continuous-export


Comment diffuser des alertes dans Azure Sentinel :

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Conseils** : Utilisez la fonctionnalité d’automatisation du workflow dans Azure Security Center pour déclencher automatiquement des réponses via « Logic Apps » sur les alertes et recommandations de sécurité.



Comment configurer l’automatisation du flux de travail et Logic Apps :

https://docs.microsoft.com/azure/security-center/workflow-automation

**Supervision Azure Security Center** : Actuellement non disponible

**Responsabilité** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Contrôle de sécurité : Tests d’intrusion et exercices Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1 : Procéder régulièrement à des tests d’intrusion de vos ressources Azure et résoudre tous les problèmes de sécurité critiques détectés dans un délai de 60 jours

**Conseils** : Respectez les règles d’engagement de Microsoft pour vous assurer que vos tests d’intrusion sont conformes aux stratégies de Microsoft :

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



Vous trouverez plus d’informations sur la stratégie de Microsoft et l’exécution des tests Red Teaming et d’intrusion de sites en direct sur l’infrastructure, les services et les applications cloud gérés par Microsoft ici : 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Supervision Azure Security Center** : Non applicable

**Responsabilité** : Partagé

