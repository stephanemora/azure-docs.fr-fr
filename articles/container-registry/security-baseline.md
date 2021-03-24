---
title: Ligne de base de sécurité Azure pour Container Registry
description: La ligne de base de sécurité pour Container Registry fournit de l’aide et des ressources pour l’implémentation des recommandations de sécurité spécifiées dans Azure Security Benchmark.
author: msmbaldwin
ms.service: container-registry
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 795b938913dad149739c4591bc2a9c221d784c84
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716367"
---
# <a name="azure-security-baseline-for-container-registry"></a>Ligne de base de sécurité Azure pour Container Registry

Cette ligne de base de sécurité applique les instructions [Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) à Container Registry. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure.
Le contenu est regroupé en fonction des **contrôles de sécurité** définis par Azure Security Benchmark et les conseils associés applicables à Container Registry. Les **contrôles** non applicables à Container Registry ont été exclus.

 
Pour voir comment Container Registry est entièrement mappé à Azure Security Benchmark, consultez le [fichier de mappage complet de la base de référence de sécurité de Container Registry](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Aide** : Un réseau virtuel Azure fournit un accès réseau privé et sécurisé à vos ressources Azure et locales. En limitant l’accès à votre registre de conteneurs Azure privé à partir d’un réseau virtuel Azure, vous vous assurez que seules les ressources dans le réseau virtuel accèdent au registre. Pour les scénarios intersites, vous pouvez également configurer des règles de pare-feu pour autoriser l’accès au registre uniquement à partir d’adresses IP spécifiques.
Derrière un pare-feu, configurez les règles d’accès du pare-feu et les étiquettes de service pour accéder à votre registre de conteneurs.

- [Restreindre l’accès à un registre de conteneurs Azure à l’aide d’un réseau virtuel Azure ou de règles de pare-feu](container-registry-vnet.md) 

- [Configurer des règles pour accéder à un registre de conteneurs Azure derrière un pare-feu](container-registry-firewall-access-rules.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) est l’initiative de stratégie par défaut pour Azure Security Center et constitue la base des [recommandations de Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) pour les services associés.

**Définitions Azure Policy intégrées - Microsoft.ContainerRegistry** :

[!INCLUDE [Resource Policy for Microsoft.ContainerRegistry 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerregistry-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des interfaces réseau

**Aide** : Utilisez Azure Security Center et suivez les recommandations de protection du réseau pour protéger vos ressources réseau dans Azure. Activez les journaux de flux NSG et transférez-les vers un compte de stockage pour l'audit du trafic.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Protéger vos ressources réseau](../security-center/security-center-network-recommendations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Aide** : Activez la protection DDoS Standard sur vos réseaux virtuels Azure pour vous protéger des attaques DDoS. Utilisez la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center pour refuser les communications avec des adresses IP Internet connues comme étant malveillantes ou inutilisées. Déployez le Pare-feu Azure à chaque extrémité du réseau de l'organisation en activant la fonctionnalité de renseignement sur les menaces et en la configurant sur « Alerter et refuser » pour vous protéger de tout trafic réseau malveillant.

Vous pouvez utiliser la fonctionnalité d'accès réseau juste-à-temps d'Azure Security Center pour configurer les groupes de sécurité réseau (NSG) et limiter l'exposition des points de terminaison aux adresses IP approuvées pendant une période limitée.
De plus, utilisez la fonctionnalité de renforcement du réseau adaptatif d’Azure Security Center pour recommander des configurations NSG qui brident les ports et les adresses IP sources en fonction du trafic réel et du renseignement sur les menaces.

- [Guide pratique pour configurer la protection DDoS](/azure/virtual-network/manage-ddos-protection)
- [Guide pratique pour déployer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)
- [Présentation de la fonctionnalité Threat Intelligence intégrée à Azure Security Center](../security-center/security-center-alerts-service-layer.md)
- [Présentation de la fonctionnalité de renforcement du réseau adaptatif d’Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)
- [Fonctionnalité de contrôle d’accès réseau juste-à-temps d’Azure Security Center](../security-center/security-center-just-in-time.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Aide** : Activez les journaux de flux de groupe de sécurité réseau (NSG) pour le groupe de sécurité réseau attaché au sous-réseau utilisé pour protéger votre registre de conteneurs Azure. Vous pouvez enregistrer les journaux de flux NSG dans un compte de Stockage Azure pour générer des enregistrements de flux. Si cela s’avère nécessaire pour analyser une activité anormale, activez la capture de paquets Azure Network Watcher.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer Network Watcher](../network-watcher/network-watcher-create.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions basés sur le réseau (IDS/IPS)

**Aide** : Sélectionnez une offre de la place de marché Azure qui prend en charge les fonctionnalités IDS/IPS avec des fonctionnalités d’inspection de charge utile. Si la détection et/ou la prévention des intrusions basées sur l’inspection de la charge utile ne font pas partie des exigences, vous pouvez utiliser le Pare-feu Azure avec Threat Intelligence. Le filtrage basé sur le renseignement sur les menaces du Pare-feu Azure peut générer des alertes et refuser le trafic depuis ou vers des adresses IP et des domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence.

Déployez la solution de pare-feu de votre choix dans les limites réseau de votre organisation pour détecter et/ou refuser le trafic malveillant.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

- [Guide pratique pour déployer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Guide pratique pour configurer des alertes avec le Pare-feu Azure](../firewall/threat-intel.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Aide** : Pour les ressources qui doivent accéder à votre registre de conteneurs, utilisez des étiquettes de service de réseau virtuel pour le service Azure Container Registry afin de définir des contrôles d’accès réseau sur des groupes de sécurité réseau ou le pare-feu Azure. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de l’étiquette de service « AzureContainerRegistry » dans le champ Source ou Destination approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

- [Autoriser l’accès par étiquette de service](https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Aide** : Définissez et implémentez des configurations de sécurité standard pour les ressources réseau associées à vos registres de conteneurs Azure à l’aide d’Azure Policy. Utilisez des alias Azure Policy dans les espaces de noms « Microsoft.ContainerRegistry » et « Microsoft.Network » afin de créer des stratégies personnalisées pour auditer ou appliquer la configuration réseau de vos registres de conteneurs. 

Vous pouvez utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant les artefacts d’environnement clés, tels que les modèles Resource Manager, les contrôles Azure RBAC et les stratégies, au sein d’une seule définition de blueprint. Appliquez aisément le blueprint aux nouveaux abonnements et affiner le contrôle et la gestion via le contrôle de version.

- [Auditer la conformité des registres de conteneurs Azure à l’aide d’Azure Policy](container-registry-azure-policy.md)

- [Guide pratique pour créer un blueprint Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Aide** : Le client peut utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant les artefacts d’environnement clés, tels que les modèles Resource Manager, les contrôles Azure RBAC et les stratégies, au sein d’une seule définition de blueprint. Appliquez aisément le blueprint aux nouveaux abonnements et affiner le contrôle et la gestion via le contrôle de version.

- [Guide pratique pour créer un blueprint Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Conseils** : Utilisez le journal des activités Azure pour superviser les configurations des ressources réseau et détecter les changements des ressources réseau associées à vos registres de conteneurs. Créez des alertes dans Azure Monitor, qui se déclenchent lors de la modification de ressources réseau critiques.

- [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](/azure/azure-monitor/platform/activity-log-view)

- [Guide pratique pour créer des alertes dans Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Ingérez les journaux via Azure Monitor pour agréger les données de sécurité générées par un registre de conteneurs Azure. Dans Azure Monitor, utilisez des espaces de travail Log Analytics pour interroger et effectuer l’analytique, puis utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage.

- [Journaux d’Azure Container Registry pour l’évaluation et l’audit de diagnostics](container-registry-diagnostics-audit-logs.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : Azure Monitor collecte les journaux de ressources (anciennement appelés journaux de diagnostic) pour les événements pilotés par l’utilisateur dans votre registre. Collectez et consommez ces données pour auditer les événements d’authentification de registre et fournir une trace d’activité complète sur les artefacts du registre, tels que les événements de tirage (pull) et d’envoi (push), pour vous permettre de diagnostiquer les problèmes de sécurité avec votre registre.

- [Journaux d’Azure Container Registry pour l’évaluation et l’audit de diagnostics](container-registry-diagnostics-audit-logs.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4 : Collecter les journaux de sécurité des systèmes d’exploitation

**Aide** : Non applicable. Ce benchmark a trait aux ressources de calcul.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Aide** : Dans Azure Monitor, définissez la période de conservation de votre espace de travail Log Analytics en fonction des obligations réglementaires de votre organisation. Utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage.

- [Définir les paramètres de conservation des journaux pour les espaces de travail Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Conseils** : Analysez et supervisez les journaux Azure Container Registry pour détecter les comportements anormaux et examinez régulièrement les résultats. Utilisez l’espace de travail Log Analytics d’Azure Monitor pour examiner les journaux et effectuer des requêtes sur leurs données.

- [Journaux d’Azure Container Registry pour l’évaluation et l’audit de diagnostics](container-registry-diagnostics-audit-logs.md)

- [Présentation de l’espace de travail Log Analytics](/azure/azure-monitor/log-query/get-started-portal)

- [Guide pratique pour effectuer des requêtes personnalisées dans Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Aide** : Utilisez l’espace de travail Azure Log Analytics pour superviser les activités anormales dans les événements et les journaux de sécurité liés à votre registre de conteneurs Azure, et générer des alertes s’y rapportant.

- [Journaux d’Azure Container Registry pour l’évaluation et l’audit de diagnostics](container-registry-diagnostics-audit-logs.md)

- [Guide pratique pour générer une alerte sur des données de journal Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="28-centralize-anti-malware-logging"></a>2.8 : Centraliser la journalisation anti-programme malveillant

**Aide** : Non applicable. Azure Container Registry ne traite pas et ne produit pas de journaux liés aux logiciels anti-programmes malveillants.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="29-enable-dns-query-logging"></a>2.9 : Activer la journalisation des requêtes DNS

**Aide** : Non applicable. Azure Container Registry est un point de terminaison qui n’initie pas de communication, et le service n’interroge pas DNS.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="210-enable-command-line-audit-logging"></a>2.10 : Activer l’enregistrement d’audit en ligne de commande

**Aide** : Non applicable. Ce benchmark a trait aux ressources de calcul.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Aide** : Azure Active Directory (Azure AD) comporte des rôles intégrés qui doivent être explicitement attribués et qui peuvent être interrogés. Utilisez le module Azure AD PowerShell pour effectuer des requêtes ad hoc afin de découvrir les comptes membres de groupes d’administration.

Pour chaque registre de conteneurs Azure, assurez-vous que le compte d’administrateur intégré est activé ou désactivé. Désactivez le compte lorsqu’il n’est pas utilisé.

- [Guide pratique pour obtenir un rôle d’annuaire dans Azure AD avec PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Guide pratique pour obtenir les membres d’un rôle d’annuaire dans Azure AD avec PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Compte administrateur Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Aide** : Azure Active Directory (Azure AD) n’intègre pas le concept des mots de passe par défaut. Selon le service, d’autres ressources Azure qui exigent un mot de passe forcent la création d’un mot de passe conforme à des exigences de complexité et d’une longueur minimale. Vous êtes responsable des applications tierces et des services de la place de marché susceptibles d’utiliser des mots de passe par défaut.

Si le compte d’administrateur par défaut d’un registre de conteneurs Azure est activé, les mots de passe complexes sont automatiquement créés et doivent faire l’objet d’une rotation. Désactivez le compte lorsqu’il n’est pas utilisé.

- [Compte administrateur Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseils** : Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration.

Créez également des procédures pour activer le compte administrateur intégré d’un registre de conteneurs. Désactivez le compte lorsqu’il n’est pas utilisé.

- [Présentation de l’identité et de l’accès Azure Security Center](../security-center/security-center-identity-access.md)

- [Compte administrateur Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Conseil** : dans la mesure du possible, utilisez l’authentification unique (SSO) d’Azure Active Directory (Azure AD) au lieu de configurer des informations d’identification autonomes individuelles par service. Suivez les recommandations liées à la gestion des identités et des accès dans Azure Security Center.

Pour un accès individuel au registre de conteneurs, utilisez une connexion individuelle intégrée avec Azure AD.

- [Présentation de l’authentification SSO avec Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Connexion individuelle à un registre de conteneurs](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseil** : activez l’authentification multifacteur Azure Active Directory (Azure AD), puis suivez les recommandations relatives à la gestion des identités et des accès Azure Security Center.

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Conseil** : utilisez des stations de travail disposant d’un accès privilégié (PAW) avec l’authentification multifacteur configurée pour vous connecter aux ressources Azure et les configurer.

- [En savoir plus sur les stations de travail à accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration

**Aide** : Utilisez les rapports de sécurité Azure Active Directory (Azure AD) pour générer des journaux et des alertes quand une activité suspecte ou potentiellement dangereuse se produit dans l’environnement. Utiliser Azure Security Center pour superviser les activités liées aux identités et aux accès.

- [Guide pratique pour identifier les utilisateurs Azure AD pour lesquels une activité à risque a été signalée](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Guide pratique pour superviser l’activité liée aux identités et aux accès des utilisateurs dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

**Aide** : Utilisez des emplacements nommés à accès conditionnel pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.

- [Guide pratique pour configurer des emplacements nommés dans Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Utiliser Azure Active Directory (Azure AD) comme système d’authentification et d’autorisation central. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

- [Création et configuration d’une instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Azure Active Directory (Azure AD) fournit des journaux pour vous aider à découvrir les comptes obsolètes. De plus, utilisez les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seules les utilisateurs appropriés continuent de bénéficier d’un accès.

- [Présentation des rapports Azure AD](/azure/active-directory/reports-monitoring/)

- [Comment utiliser les révisions d’accès des identités Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Aide** : Vous pouvez accéder aux sources de journal de l’activité de connexion, de l’audit et des événements à risque d’Azure Active Directory (Azure AD), ce qui vous permet d’intégrer n’importe quel outil SIEM (Security Information and Event Management) ou de supervision.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure AD et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Vous pouvez configurer les alertes souhaitées dans un espace de travail Log Analytics.

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Aide** : Utilisez les détections de risque et la fonctionnalité Risk and Identity Protection d’Azure Active Directory (Azure AD) pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités d’utilisateur. 

- [Guide pratique pour afficher les connexions risquées Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

**Aide** : Customer Lockbox n’est actuellement pas pris en charge pour Azure Container Registry.

- [Liste des services pris en charge pour Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Aide** : Utilisez des étiquettes de ressource pour faciliter le suivi des registres de conteneurs Azure qui stockent ou traitent des informations sensibles.

Associez des étiquettes et des versions aux images de conteneur ou d’autres artefacts dans un registre, et verrouillez des images ou des référentiels pour faciliter le suivi des images qui stockent ou traitent des informations sensibles.

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

- [Suggestions pour la création d’étiquettes et de versions pour les images conteneur](container-registry-image-tag-version.md)

- [Verrouiller une image conteneur dans un registre de conteneurs Azure](container-registry-image-lock.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Aide** : Implémentez des registres de conteneurs, des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Les ressources de stockage ou de traitement des données sensibles doivent être suffisamment isolées.

Les ressources doivent être séparées par un réseau virtuel ou un sous-réseau, étiquetés de manière appropriée et sécurisés par un groupe de sécurité réseau (NSG) ou un Pare-feu Azure.

- [Guide pratique pour créer des abonnements Azure supplémentaires](/azure/billing/billing-create-subscription)

- [Guide pratique pour créer des groupes d’administration](/azure/governance/management-groups/create)

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

- [Restreindre l’accès à un registre de conteneurs Azure à l’aide d’un réseau virtuel Azure ou de règles de pare-feu](container-registry-vnet.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

- [Guide pratique pour déployer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Configuration des options « Alerter » et « Alerter et refuser » du pare-feu Azure](../firewall/threat-intel.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

**Aide** : Déployez un outil automatisé sur les périmètres du réseau qui surveille le transfert non autorisé d’informations sensibles et bloque ces transferts tout en alertant les professionnels de la sécurité des informations.

Pour la plateforme sous-jacente qui est gérée par Microsoft, Microsoft traite tout le contenu client comme sensible et déploie d'importants efforts pour vous protéger contre la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : aucune

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Aide** : Assurez-vous que les clients qui se connectent à votre registre de conteneurs Azure peuvent négocier TLS 1.2 ou une version ultérieure. Par défaut, les ressources Microsoft Azure négocient TLS 1.2.

Suivez les recommandations d’Azure Security Center relatives au chiffrement au repos et au chiffrement en transit, le cas échéant.

- [Présentation du chiffrement en transit avec Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : aucune

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Aide** : Les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour Azure Container Registry. Implémentez une solution tierce si nécessaire à des fins de conformité.

Pour la plateforme sous-jacente managée par Microsoft, Microsoft considère tout le contenu client comme sensible et met tout en œuvre pour empêcher la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : aucune

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6 : Utiliser le contrôle d’accès en fonction du rôle pour contrôler l’accès aux ressources

**Aide** : Utilisez le contrôle d’accès en fonction du rôle Azure (Azure RBAC) pour contrôler l’accès aux données et aux ressources dans un registre de conteneurs Azure. 

- [Comment configurer Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Autorisations et rôles Azure Container Registry](container-registry-roles.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7 : Utiliser la protection contre la perte de données basée sur l’hôte pour appliquer le contrôle d’accès

**Conseils** : Si nécessaire à des fins de conformité sur les ressources de calcul, implémentez un outil tiers, tel qu’une solution automatisée de prévention contre la perte de données basée sur l’hôte, pour appliquer des contrôles d’accès aux données même lorsque des données sont copiées à partir d’un système.

Pour la plateforme sous-jacente qui est gérée par Microsoft, Microsoft traite tout le contenu client comme sensible et déploie d'importants efforts pour vous protéger contre la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : aucune

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Aide** : Utilisez le chiffrement au repos sur toutes les ressources Azure. Par défaut, toutes les données d’un registre de conteneurs Azure sont chiffrées au repos à l’aide de clés gérées par Microsoft.

- [Présentation du chiffrement au repos dans Azure](../security/fundamentals/encryption-atrest.md)

- [Clés gérées par le client dans Azure Container Registry](https://aka.ms/acr/cmk)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Azure Monitor collecte les journaux de ressources (anciennement appelés journaux de diagnostic) pour les événements pilotés par l’utilisateur dans votre registre. Collectez et consommez ces données pour auditer les événements d’authentification de registre et fournir une trace d’activité complète sur les artefacts du registre, tels que les événements d’envoi (push) et de tirage (pull), pour vous permettre de diagnostiquer les problèmes opérationnels avec votre registre.

- [Journaux d’Azure Container Registry pour l’évaluation et l’audit de diagnostics](container-registry-diagnostics-audit-logs.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Aide** : Suivez les recommandations d’Azure Security Center relatives à l’évaluation des vulnérabilités sur vos images de conteneur. Déployez éventuellement des solutions tierces à partir de la Place de marché Azure pour évaluer les vulnérabilités des images.

- [Implémenter les recommandations d'évaluation des vulnérabilités d'Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Intégration d’Azure Container Registry à Security Center (préversion)](/azure/security-center/azure-container-registry-integration)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 : Déployer une solution de gestion des correctifs de système d’exploitation automatisée

**Aide** : Microsoft assure la gestion des correctifs sur les systèmes sous-jacents prenant en charge Azure Container Registry.

Automatisez les mises à jour des images de conteneur lorsque des mises à jour des images de base du système d’exploitation et d’autres correctifs sont détectées.

- [À propos des mises à jour des images de base pour les tâches Azure Container Registry](container-registry-tasks-base-images.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3 : Déployer une solution de gestion automatisée des correctifs des logiciels tiers

**Aide** : Vous pouvez utiliser une solution tierce pour corriger les images d’application.  En outre, vous pouvez exécuter des tâches de Azure Container Registry pour automatiser les mises à jour des images d’application dans un registre de conteneurs en fonction des correctifs de sécurité ou d’autres mises à jour dans les images de base.

- [À propos des mises à jour des images de base pour ACR Tasks](container-registry-tasks-base-images.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 : Comparer les analyses de vulnérabilités dos à dos

**Aide** : Intégrez Azure Container Registry (ACR) à Azure Security Center pour activer l’analyse périodique des images de conteneur à la recherche de vulnérabilités. Déployez éventuellement des solutions tierces à partir de la Place de marché Azure pour effectuer des analyses périodiques de vulnérabilités des images.

- [Intégration d’Azure Container Registry à Security Center (préversion)](/azure/security-center/azure-container-registry-integration)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes.

**Aide** : Intégrez Azure Container Registry (ACR) à Azure Security Center pour activer l’analyse périodique des images de conteneur à la recherche de vulnérabilités et pour classifier les risques. Déployez éventuellement des solutions tierces à partir de la Place de marché Azure pour effectuer des analyses périodiques de vulnérabilités des images et la classification des risques.

- [Intégration d’Azure Container Registry à Security Center (préversion)](/azure/security-center/azure-container-registry-integration)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Aide** : Utilisez Azure Resource Graph pour interroger/découvrir toutes les ressources (telles que calcul, stockage, réseau, ports et protocoles) dans vos abonnements. Vérifiez les autorisations (lecture) appropriées dans votre locataire et répertoriez tous les abonnements Azure, ainsi que les ressources dans vos abonnements.

Bien que les ressources Azure classiques puissent être découvertes via Resource Graph, il est vivement recommandé de créer et d’utiliser des ressources Azure Resource Manager à l’avenir.

- [Guide pratique pour créer des requêtes avec Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Guide pratique pour afficher ses abonnements Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Présentation d’Azure RBAC](../role-based-access-control/overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Aide** : Azure Container Registry gère les métadonnées, notamment les étiquettes et les manifestes pour les images dans un registre. Suivez les pratiques recommandées pour étiqueter les artefacts.

- [À propos des registres, des référentiels et des images](container-registry-concepts.md)

- [Suggestions pour la création d’étiquettes et de versions pour les images conteneur](container-registry-image-tag-version.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Aide** : Azure Container Registry gère les métadonnées, notamment les étiquettes et les manifestes pour les images dans un registre. Suivez les pratiques recommandées pour étiqueter les artefacts.

- [À propos des registres, des référentiels et des images](container-registry-concepts.md)

- [Suggestions pour la création d’étiquettes et de versions pour les images conteneur](container-registry-image-tag-version.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4 : Définir et tenir un inventaire des ressources Azure approuvées

**Aide** : Vous devez créer un inventaire des ressources Azure approuvées et des logiciels approuvés en fonction des besoins de votre organisation.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Aide** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans vos abonnements.

Utilisez Azure Resource Graph pour interroger/découvrir des ressources dans leurs abonnements.  Vérifiez que toutes les ressources Azure présentes dans l’environnement sont approuvées.

- [Auditer la conformité des registres de conteneurs Azure à l’aide d’Azure Policy](container-registry-azure-policy.md)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour créer des requêtes avec Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6 : Analyser les applications logicielles non approuvées dans des ressources de calcul

**Conseils** : Analysez et supervisez les journaux Azure Container Registry pour détecter les comportements anormaux et examinez régulièrement les résultats. Utilisez l’espace de travail Log Analytics d’Azure Monitor pour examiner les journaux et effectuer des requêtes sur leurs données.

- [Journaux d’Azure Container Registry pour l’évaluation et l’audit de diagnostics](container-registry-diagnostics-audit-logs.md)

- [Présentation de l’espace de travail Log Analytics](/azure/azure-monitor/log-query/get-started-portal)

- [Guide pratique pour effectuer des requêtes personnalisées dans Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 : Supprimer des ressources et applications logicielles Azure non approuvées

**Aide** : Azure Automation permet de contrôler totalement le déploiement, les opérations et la désaffectation des charges de travail et des ressources. Vous pouvez implémenter votre propre solution  de suppression des ressources Azure non autorisées.

- [Présentation d’Azure Automation](../automation/automation-intro.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="68-use-only-approved-applications"></a>6.8 : Utiliser des applications approuvées uniquement

**Aide** : Non applicable. Ce benchmark est destiné aux ressources de calcul.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Aide** : Utilisez Azure Policy pour limiter les services que vous pouvez approvisionner dans votre environnement.

- [Auditer la conformité des registres de conteneurs Azure à l’aide d’Azure Policy](container-registry-azure-policy.md)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10 : Tenir un inventaire des titres de logiciels approuvés

**Aide** : Non applicable. Ce benchmark est destiné aux ressources de calcul.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Aide** : Utilisez des configurations de système d’exploitation spécifiques ou des ressources tierces pour limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul Azure.

- [Guide pratique pour configurer l’accès conditionnel de façon à bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12 : Limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul

**Conseils** : Utilisez des configurations de système d’exploitation spécifiques ou des ressources tierces pour limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul Azure.

- [Par exemple, contrôler l’exécution d’un script PowerShell dans des environnements Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13 : Séparer physiquement ou logiquement des applications à risque élevé

**Aide** : Les logiciels nécessaires à des opérations métier, mais qui peuvent poser un risque élevé pour l’organisation, doivent être isolés sur leur propre machine virtuelle et/ou réseau virtuel et être suffisamment sécurisés à l’aide d’un Pare-feu Azure ou d’un groupe de sécurité réseau.

- [Guide pratique pour créer un réseau virtuel](../virtual-network/quick-create-portal.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Aide** : Utilisez Azure Policy ou Azure Security Center pour gérer les configurations de sécurité pour toutes les ressources Azure.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Auditer la conformité des registres de conteneurs Azure à l’aide d’Azure Policy](container-registry-azure-policy.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="72-establish-secure-operating-system-configurations"></a>7.2 : Établir des configurations sécurisées du système d’exploitation

**Aide** : Utilisez la recommandation « Corriger les vulnérabilités dans les configurations de sécurité sur vos machines virtuelles » Azure Security Center pour gérer les configurations de sécurité sur toutes les ressources de calcul.

- [Suivi des recommandations d'Azure Security Center](../security-center/security-center-recommendations.md)

- [Procédure de correction des recommandations d’Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Aide** : Utilisez les stratégies Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure.

- [Auditer la conformité des registres de conteneurs Azure à l’aide d’Azure Policy](container-registry-azure-policy.md)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Comprendre les effets d'Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4 : Préserver la sécurité des configurations du système d'exploitation

**Aide** : Non applicable. Ce benchmark a trait aux ressources de calcul.

**Responsabilité** : Partagé

**Supervision Azure Security Center** : aucune

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Si vous utilisez des définitions Azure Policy personnalisées, utilisez Azure Repos pour stocker et gérer votre code en toute sécurité.

- [Stocker du code dans Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentation Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="76-securely-store-custom-operating-system-images"></a>7.6 Stocker en toute sécurité des images de système d’exploitation personnalisées

**Aide** : Non applicable. Le test d’évaluation s’applique aux ressources de calcul.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Aide** : Utilisez Azure Policy pour alerter, auditer et appliquer des configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

- [Auditer la conformité des registres de conteneurs Azure à l’aide d’Azure Policy](container-registry-azure-policy.md)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8 : Déployer des outils de gestion de la configuration pour les systèmes d'exploitation

**Aide** : Non applicable. Le test d’évaluation s’applique aux ressources de calcul.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Aide** : Utiliser Azure Security Center pour effectuer des analyses de ligne de base pour vos ressources Azure.

Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans vos abonnements.

- [Corriger les recommandations dans Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Auditer la conformité des registres de conteneurs Azure à l’aide d’Azure Policy](container-registry-azure-policy.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 : Implémenter la surveillance de la configuration automatique pour les systèmes d’exploitation

**Aide** : Non applicable. Le test d’évaluation s’applique aux ressources de calcul.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Conseils** : Utilisez Managed Service Identity conjointement avec Azure Key Vault pour simplifier et sécuriser la gestion des secrets pour vos applications Cloud.

- [Intégration aux identités managées Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Créer un coffre de clés](../key-vault/general/quick-create-portal.md)

- [Comment s’authentifier auprès de Key Vault](../key-vault/general/authentication.md)

- [Comment attribuer une stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md)

- [Utiliser une identité managée Azure dans les tâches Azure Container Registry](container-registry-tasks-authentication-managed-identity.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Conseils** : Utilisez des identités managées pour fournir aux services Azure une identité gérée automatiquement dans Azure Active Directory (Azure AD). Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

- [Configurer des identités managées](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)

- [Utiliser une identité managée pour s’authentifier auprès d’un registre de conteneurs Azure](container-registry-authentication-managed-identity.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault.

- [Configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Défense contre les programmes malveillants](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 : Utiliser un logiciel anti-programme malveillant géré de manière centralisée

**Aide** : Utilisez Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles afin de superviser et défendre en continu vos ressources. Pour Linux, utilisez une solution logicielle anti-programme malveillant tierce.

- [Guide pratique pour configurer Microsoft Antimalware pour les services cloud et les machines virtuelles](../security/fundamentals/antimalware.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Aide** : Microsoft Antimalware est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple, Azure Container Registry), mais il ne s’exécute pas sur du contenu client.

Pré-analysez les fichiers chargés sur des ressources Azure non liées au calcul, comme App Service, des Data Lake Storage, Stockage Blob, etc.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 : Vérifier que les logiciels et signatures anti-programme malveillant sont mis à jour

**Aide** : Non applicable. Ce benchmark a trait aux ressources de calcul. Microsoft gère les logiciels anti-programme malveillant pour la plateforme sous-jacente.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Garantir des sauvegardes automatiques régulières

**Aide** : Les données de votre registre de conteneurs Microsoft Azure sont toujours répliquées pour en garantir la durabilité et la haute disponibilité. Azure Container Registry copie vos données afin qu’elles soient protégées contre les événements planifiés et les événements non planifiés

Vous pouvez également géo-répliquer un registre de conteneurs pour conserver les réplicas du Registre dans plusieurs régions Azure. 

- [Géoréplication dans Azure Container Registry](container-registry-geo-replication.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés managées par le client

**Aide** : Sauvegardez éventuellement les images conteneur en les important d’un registre vers un autre.

Sauvegardez les clés gérées par le client dans Azure Key Vault à l’aide d’outils en ligne de commande ou de kits de développement logiciel (SDK) Azure.

- [Importer des images conteneur dans un registre de conteneurs](container-registry-import-images.md)

- [Guide pratique pour sauvegarder des clés de coffre de clés dans Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Conseil** : testez la restauration des clés gérées par le client sauvegardées dans Azure Key Vault à l’aide d’outils en ligne de commande ou de SDK Azure.

- [Comment restaurer des clés Azure Key Vault dans Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés managées par le client

**Aide** : Vous pouvez activer la suppression réversible dans Azure Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante.

- [Guide pratique pour activer la suppression réversible dans Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.

- [Comment configurer des automatisations de workflow dans Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Le client peut également tirer parti du guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de son propre plan de réponse aux incidents](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Aide** : Azure Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements (par ex. production, non production) et créez un système d’attribution de noms pour identifier et classer les ressources Azure de façon claire.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Aide** : Exécutez des exercices pour tester les fonctionnalités de réponse aux incidents de vos systèmes de façon régulière. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

- [Reportez-vous à la publication du NIST : Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide de test, d’entraînement et d’utilisation des programmes destinés aux plans et fonctionnalités informatiques)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 : Fournir des informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

**Conseils** : Les informations de contact d’incident de sécurité seront utilisées par Microsoft pour vous contacter si Microsoft Security Response Center (MSRC) découvre que les données du client ont été utilisées par un tiers illégal ou non autorisé.  Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus.

- [Comment définir le contact de sécurité d’Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Conseils** : Exportez vos alertes et recommandations d’Azure Security Center à l’aide de la fonctionnalité d’exportation continue. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser en continu les alertes vers Azure Sentinel.

- [Comment configurer l’exportation continue](../security-center/continuous-export.md)

- [Comment envoyer des alertes à Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Conseils** : Utilisez la fonctionnalité d’automatisation du workflow dans Azure Security Center pour déclencher automatiquement des réponses via « Logic Apps » sur les alertes et recommandations de sécurité.

- [Comment configurer l’automatisation des workflows et Logic Apps](../security-center/workflow-automation.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : tests d’intrusion et exercices Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1 : Procéder régulièrement à des tests d’intrusion des ressources Azure et veiller à corriger tous les problèmes de sécurité critiques détectés

**Conseil** : respectez les règles d’engagement de Microsoft pour garantir que vos tests d’intrusion sont conformes aux stratégies Microsoft :  https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [Vous trouverez ici plus d’informations sur la stratégie de Microsoft, sur l’exécution de Red Teaming et sur les tests d’intrusion de site actif sur l’infrastructure, les services et les applications cloud qui sont managés par Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
