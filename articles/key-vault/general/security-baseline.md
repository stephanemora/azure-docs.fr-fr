---
title: Base de référence de sécurité Azure pour Key Vault
description: La base de référence de sécurité Key Vault fournit des instructions et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans le benchmark de sécurité Azure.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 305fdd3a0b8e0c876924c6e1f090424e67571af0
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968703"
---
# <a name="azure-security-baseline-for-key-vault"></a>Base de référence de sécurité Azure pour Key Vault

Cette base de référence de sécurité applique les conseils du [benchmark de sécurité Azure version 1.0](../../security/benchmarks/overview-v1.md) à Key Vault. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure. Le contenu est regroupé en fonction des **contrôles de sécurité** définis par le benchmark de sécurité Azure et les conseils associés applicables à Key Vault. Les **contrôles** non applicables à Key Vault, ou dont la responsabilité incombe à Microsoft, ont été exclus.

Pour voir la façon dont Key Vault est entièrement mappé au benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité Key Vault](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Conseils** : Intégrez Azure Key Vault avec Azure Private Link. Le service Azure Private Link vous permet d’accéder aux services Azure (par exemple, Azure Key Vault) ainsi qu’aux services de partenaires ou de clients hébergés par Azure sur un point de terminaison privé de votre réseau virtuel.

Un point de terminaison privé Azure est une interface réseau qui vous connecte de façon privée et sécurisée à un service basé sur la technologie Azure Private Link. Le point de terminaison privé utilise une adresse IP privée de votre réseau virtuel, plaçant de fait le service dans votre réseau virtuel. Sachant que l’ensemble du trafic à destination du service peut être routé via le point de terminaison privé, il n’y a aucun besoin de passerelles, d’appareils NAT, de connexions ExpressRoute ou VPN ou d’adresses IP publiques. Le trafic entre votre réseau virtuel et le service transite par le réseau principal de Microsoft, éliminant ainsi toute exposition à l’Internet public. Vous pouvez vous connecter à une instance d’une ressource Azure, ce qui vous donne le plus haut niveau de granularité en matière de contrôle d’accès.

- [Comment intégrer Key Vault avec Azure Private Link](/azure/key-vault/private-link-service)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés peut être nécessaire pour les alertes liées à ce contrôle.

**Définitions intégrées d’Azure Policy – Microsoft.KeyVault** :

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des interfaces réseau

**Conseils** : Utilisez Azure Security Center et suivez les recommandations de protection du réseau pour sécuriser vos ressources configurées par Key Vault dans Azure. 

- [Pour plus d’informations sur la sécurité réseau fournie par Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications avec des adresses IP connues comme étant malveillantes

**Conseils** : Activez le Service Protection DDoS Standard Azure sur les réseaux virtuels Azure associés à vos instances Key Vault pour la protection contre des attaques par déni de service distribué. Utilisez la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center pour refuser les communications avec des adresses IP Internet connues comme étant malveillantes ou inutilisées.

 
- [Gérer la norme Azure DDoS Protection à l’aide du portail Azure](/azure/virtual-network/manage-ddos-protection)

- [Détection des menaces pour la couche des services Azure dans Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions basés sur le réseau (IDS/IPS)

**Conseils** : Cette exigence peut être satisfaite en configurant la protection avancée contre les menaces (ATP, Advanced Threat Protection) pour Azure Key Vault. ATP fournit une couche supplémentaire de veille de sécurité. Cet outil détecte les tentatives potentiellement dangereuses d’accès à des comptes Azure Key Vault ou d’exploitation de ceux-ci.

Quand Azure Security Center détecte une activité anormale, il affiche des alertes. Il envoie également un e-mail à l’administrateur des abonnements avec les détails de l’activité suspecte et des recommandations sur la façon d’examiner et de corriger les menaces identifiées.

- [Configurer la protection avancée contre les menaces pour Azure Key Vault](/azure/security-center/advanced-threat-protection-key-vault)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Conseils** : Pour les ressources qui doivent accéder à vos instances Azure Key Vault, utilisez des étiquettes de service Azure pour Azure Key Vault afin de définir des contrôles d’accès réseau sur des groupes de sécurité réseau ou le Pare-feu Azure. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de la balise de service (par exemple, ApiManagement) dans le champ Source ou Destination approprié d'une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

- [Vue d’ensemble des étiquettes de service Azure](../../virtual-network/service-tags-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Conseils** : Définissez et implémentez des configurations de sécurité standard pour les ressources réseau associées à vos instances Azure Key Vault à l’aide d’Azure Policy. Utilisez des alias Azure Policy dans les espaces de noms « Microsoft.keyvault » et « Microsoft. Network » afin de créer des stratégies personnalisées pour auditer ou appliquer la configuration réseau de vos instances Azure Key Vault. Vous pouvez également utiliser des définitions de stratégie intégrée en lien avec Azure Key Vault, par exemple :
- Key Vault doit utiliser un point de terminaison de service de réseau virtuel

Pour plus d’informations, consultez les références suivantes :

- [Créer et gérer des stratégies pour assurer la conformité](../../governance/policy/tutorials/create-and-manage.md)

- [Exemples Azure Policy](/azure/governance/policy/samples)

- [Définir et affecter un blueprint dans le portail](../../governance/blueprints/create-blueprint-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Conseils** : Utilisez des étiquettes pour les ressources liées à la sécurité réseau et au flux de trafic de vos instances Azure Key Vault afin de fournir des métadonnées et une organisation logique.

Utilisez les définitions Azure Policy intégrées en lien avec l’étiquetage, comme « Exiger une étiquette et sa valeur », pour vous assurer que toutes les ressources créées sont étiquetées et pour être informé des ressources non étiquetées.

Vous pouvez utiliser Azure PowerShell ou Azure CLI pour rechercher des ressources ou effectuer des actions sur des ressources en fonction de leurs étiquettes.

- [Organisation des ressources Azure à l’aide de catégories](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Conseils** : Utilisez le journal d’activité Azure pour surveiller les configurations des ressources réseau et détecter les modifications de celles-ci associées à vos instances Azure Key Vault. Créez des alertes dans Azure Monitor, qui se déclenchent lors de la modification de ressources réseau critiques.

- [Afficher et récupérer les événements du journal d’activité Azure](/azure/azure-monitor/platform/activity-log-view)

- [Créer, afficher et gérer des alertes de journal d’activité à l’aide d’Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Conseils** : Ingérez les journaux via Azure Monitor pour agréger les données de sécurité générées par Azure Key Vault. Dans Azure Monitor, utilisez un espace de travail Azure Log Analytics pour effectuer des requêtes et des analyses, et servez-vous de comptes de stockage Azure pour le stockage à long terme et l’archivage. Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce. 

- [Journalisation d’Azure Key Vault](/azure/key-vault/key-vault-logging)

- [Démarrage rapide : Comment intégrer Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Conseils** : Activez les paramètres de diagnostic sur vos instances Azure Key Vault pour accéder aux journaux d’audit, de sécurité et de diagnostic. Les journaux d’activité, automatiquement disponibles, incluent la source de l’événement, la date, l’utilisateur, l’horodatage, les adresses sources, les adresses de destination et d’autres éléments utiles.

- [Journalisation d’Azure Key Vault](/azure/key-vault/key-vault-logging)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés peut être nécessaire pour les alertes liées à ce contrôle.

**Définitions intégrées d’Azure Policy – Microsoft.KeyVault** :

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Conseils** : Dans Azure Monitor, pour l’espace de travail Log Analytics utilisé pour stocker vos journaux Azure Key Vault, définissez la période de rétention dans le respect des réglementations de conformité de votre organisation. Utilisez les comptes de stockage Azure pour le stockage à long terme/d’archivage.

- [Changer la période de rétention des données](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Conseils** : Analysez et supervisez les journaux afin de détecter les comportements anormaux, et examinez régulièrement les résultats pour vos ressources protégées par Azure Key Vault. Utilisez l’espace de travail Log Analytics d’Azure Monitor pour examiner les journaux et effectuer des requêtes sur leurs données. Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce. 

- [Intégrer Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Prise en main de Log Analytics dans Azure Monitor](/azure/azure-monitor/log-query/get-started-portal)

- [Bien démarrer avec les requêtes de journal Azure Monitor](/azure/azure-monitor/log-query/get-started-queries).

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Conseils** : Dans Azure Security Center, activez la protection avancée contre les menaces (ATP, Advanced Threat Protection) pour Key Vault. Activez les paramètres de diagnostic dans Azure Key Vault et envoyez les journaux à un espace de travail Log Analytics. Intégrez votre espace de travail Log Analytics à Azure Sentinel, car cela fournit une solution de réponse automatisée d’orchestration de sécurité. Cela permet de créer des playbooks (solutions automatisées) utilisables pour corriger des problèmes de sécurité.

- [Démarrage rapide : Intégrer Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Gérer et répondre aux alertes de sécurité dans Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [Répondre à des événements avec des alertes Azure Monitor](/azure/azure-monitor/learn/tutorial-response)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Conseils** : Conservez un inventaire de vos applications inscrites auprès d’Azure Active Directory (Azure AD), ainsi que tous les comptes d’utilisateur qui ont accès à vos clés, secrets et certificats Azure Key Vault. Vous pouvez utiliser le portail Azure ou PowerShell pour interroger et rapprocher les accès au Key Vault. Pour afficher les accès dans PowerShell, utilisez la commande suivante :

(Get-AzResource -ResourceId [KeyVaultResourceID]).Properties.AccessPolicies

- [Inscrire une application auprès d’Azure AD](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Sécuriser l’accès à un coffre de clés](secure-your-key-vault.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseils** : Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés ayant accès à vos instances Azure Key Vault. Utilisez la fonctionnalité de gestion des identités et des accès d’Azure Security Center (actuellement en préversion) pour superviser le nombre de comptes d’administration actifs.

- [Superviser les identités et les accès (préversion)](../../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Conseils** : Utilisez un principal de service Azure conjointement avec l’AppId, le TenantID et le ClientSecret pour authentifier votre application en toute transparence et récupérer le jeton à utiliser pour accéder à vos secrets Azure Key Vault.

- [Authentification service à service auprès d’Azure Key Vault à l’aide de .NET](/azure/key-vault/service-to-service-authentication)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseils** : Activez l’authentification multifacteur d’Azure Active Directory (Azure AD) et suivez les recommandations relatives à la fonctionnalité Gestion de l’identité et de l’accès (actuellement en préversion) d’Azure Security Center pour protéger vos ressources d’Event Hub.

- [Planifier un déploiement d'Azure AD Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Superviser les identités et les accès (préversion)](../../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6 : Utiliser des stations de travail sécurisées et gérées par Azure pour les tâches administratives

**Conseils** : Utilisez une station de travail à accès privilégié sur laquelle l’authentification multifacteur d’Azure AD est configurée pour vous connecter aux ressources Key Vault et les configurer.

- [Stations de travail d’accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Planification d’un déploiement cloud de l’authentification multifacteur Azure AD](../../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration

**Aide** : Utilisez Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pour générer des journaux et des alertes quand des activités suspectes ou potentiellement dangereuses se produisent dans l’environnement. Utilisez les détections de risque Azure AD pour visualiser les alertes et des rapports sur les comportements à risque des utilisateurs. Pour une journalisation complémentaire, envoyez les alertes de détection des risques d’Azure Security Center à Azure Monitor et configurez des alertes/notifications personnalisées à l’aide de groupes d’actions.

Activez la protection avancée contre les menaces pour Azure Key Vault de façon à générer des alertes en cas d’activité suspecte.

- [Déployer Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Configurer la protection avancée contre les menaces pour Azure Key Vault (préversion)](/azure/security-center/advanced-threat-protection-key-vault)

- [Alertes pour Azure Key Vault (préversion)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Détections des risques Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Créer et gérer des groupes d’actions dans le portail Azure](/azure/azure-monitor/platform/action-groups)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 : Gérer les ressources Azure à partir des emplacements approuvés uniquement

**Conseils** : Configurez la condition d’emplacement d’une stratégie d’accès conditionnel et gérez vos emplacements nommés. Avec les emplacements nommés, vous pouvez créer des regroupements logiques de plages d’adresses IP ou de pays et régions. Vous pouvez restreindre l’accès à des ressources sensibles, telles que vos secrets Key Vault, à vos emplacements nommés configurés.

- [Présentation de la condition d’emplacement dans l’accès conditionnel Azure Active Directory (Azure AD)](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Utilisez Azure Active Directory (Azure AD) comme système central d’authentification et d’autorisation pour des ressources Azure telles que Key Vault. Ainsi, le mécanisme de contrôle d’accès en fonction du rôle Azure (Azure RBAC) est applicable aux ressources administratives sensibles.

- [Créer un locataire dans Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Examinez les journaux Azure Active Directory (Azure AD) pour découvrir les comptes obsolètes disposant de rôles d’administrateur Azure Key Vault. Utilisez également des révisions d’accès Azure AD pour gérer efficacement les appartenances aux groupes, les accès à des applications métier susceptibles d’être utilisées pour accéder à Azure Key Vault et les attributions de rôles. Il convient d’examiner régulièrement les accès des utilisateurs, par exemple, tous les 90 jours, pour vérifier que seuls les utilisateurs appropriés sont autorisés à accéder.

- [Documentation sur les rapports et l’analyse Azure AD](/azure/active-directory/reports-monitoring/)

- [Présentation des révisions d’accès Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Conseils** : Activez les paramètres de diagnostic pour Azure Key Vault et Azure Active Directory (Azure AD)y de façon à envoyer tous les journaux à un espace de travail Log Analytics. Configurez les alertes souhaitées (par exemple, les tentatives d’accès aux secrets désactivés) dans Log Analytics.

- [Intégrer les journaux Azure AD avec les journaux Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Migration à partir d’une ancienne solution Key Vault](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Conseils** : Utilisez les fonctionnalités de protection des identités et de détection des risques d’Azure Active Directory (Azure AD) pour configurer des réponses automatiques aux actions suspectes détectées en lien avec vos ressources protégées Azure Key Vault. Vous devez activer des réponses automatisées via Azure Sentinel pour implémenter les réponses de sécurité de votre organisation.

- [Rapports sur les connexions risquées dans le portail Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Guide pratique pour Configurer et activer des stratégies de risque](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Guide pratique pour intégrer Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Conseils** : Utilisez des étiquettes pour faciliter le suivi des ressources Azure qui stockent ou traitent des informations sensibles sur des ressources Azure Key Vault. 

- [Organisation des ressources Azure à l’aide de catégories](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Conseils** : Vous pouvez sécuriser l’accès à Azure Key Vault en utilisant des points de terminaison de service de réseau virtuel configurés pour restreindre l’accès à des sous-réseaux spécifiques.

Une fois les règles de pare-feu appliquées, vous ne pouvez effectuer des opérations de plan de données Azure Key Vault que si votre demande provient de sous-réseaux ou de plages d’adresses IP autorisés. Cela s’applique également à l’accès à Azure Key Vault à partir du portail Azure. Bien que vous puissiez pouvez accéder à un coffre de clés à partir du portail Azure, il se peut que vous ne puissiez pas répertorier les clés, secrets ou certificats qu’il contient si votre ordinateur client ne figure pas dans la liste des ordinateurs autorisés. Cela affecte également le sélecteur Azure Key Vault et d’autres services Azure. Il se peut que vous puissiez afficher des listes de coffres de clés, mais pas de clés, si des règles de pare-feu empêchent votre ordinateur client de le faire.

- [Configurer les pare-feux et réseaux virtuels d’Azure Key Vault](/azure/key-vault/key-vault-network-security)

- [Points de terminaison de service de réseau virtuel pour Azure Key Vault](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

**Conseils** : Toutes les données stockées dans Azure Key Vault sont considérées comme sensibles. Utilisez contrôles d’accès au plan de données Azure Key Vault pour contrôler l’accès aux secrets Azure Key Vault. Vous pouvez également utiliser le pare-feu intégré d’Azure Key Vault pour contrôler l’accès au niveau de la couche réseau. Pour surveiller l’accès à Azure Key Vault, activez les paramètres de diagnostic de Key Vault et envoyez les journaux à un compte de stockage Azure ou à un espace de travail Log Analytics.

- [Sécuriser l’accès à un coffre de clés](secure-your-key-vault.md)

- [Configurer les pare-feux et réseaux virtuels d’Azure Key Vault](/azure/key-vault/key-vault-network-security)

- [Journalisation d’Azure Key Vault](/azure/key-vault/key-vault-logging)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6 : Utiliser Azure RBAC pour contrôler l’accès aux ressources

**Aide** : Sécurisez l’accès à la gestion et au plan de données de vos instances Azure Key Vault.

- [Sécuriser l’accès à un coffre de clés](secure-your-key-vault.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Conseils** : Utilisez la solution Azure Key Vault Analytics dans Azure Monitor pour consulter les journaux des événements d’audit d’Azure Key Vault.

- [Solution Azure Key Vault Analytics dans Azure Monitor](/azure/azure-monitor/insights/azure-key-vault)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes.

**Conseils** : Utilisez les évaluations de risques par défaut (degré de sécurisation) fournies par Azure Security Center.

- [Améliorer votre degré de sécurisation dans Azure Security Center](/azure/security-center/security-center-secure-score)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Conseils** : Utilisez Azure Resource Graph pour interroger et découvrir toutes les ressources (y compris les instances Azure Key Vault) au sein de votre abonnement. Vérifiez que vous disposez des autorisations (en lecture) appropriées dans votre locataire et pouvez répertorier tous les abonnements Azure ainsi que les ressources qu’ils contiennent.

- [Exécuter votre première requête Resource Graph à l’aide de l’Explorateur Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Obtenir les abonnements auxquels le compte actuel peut accéder](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Contrôle d’accès en fonction du rôle Azure (RBAC)](../../role-based-access-control/overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Conseils** : Appliquez des étiquettes aux ressources Azure Key Vault en fournissant des métadonnées pour les organiser de façon logique par catégories.

- [Guide pratique pour créer et utiliser des étiquettes](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Conseils** : Utilisez des étiquettes, des groupes d’administration, voire des abonnements séparés, pour organiser et suivre les instances Azure Key Vault et les ressources associées. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.

- [Créer un abonnement Azure supplémentaire](/azure/billing/billing-create-subscription)

- [Créer des groupes d’administration pour l’organisation et l’administration des ressources](/azure/governance/management-groups/create)

- [Organisation des ressources Azure à l’aide de catégories](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Conseils** : Appliquez des restrictions aux types de ressources pouvant être créées dans des abonnements clients en utilisant les définitions de stratégies Azure intégrées suivantes :

- Types de ressources non autorisés

- Types de ressources autorisés

Utilisez également Azure Resource Graph pour interroger/découvrir des ressources dans les abonnements.

- [Tutoriel : Créer et gérer des stratégies pour assurer la conformité](../../governance/policy/tutorials/create-and-manage.md)

- [Démarrage rapide : Exécuter votre première requête Resource Graph en utilisant l’Explorateur Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Conseils** : Appliquez des restrictions aux types de ressources pouvant être créées dans des abonnements clients en utilisant les définitions de stratégies Azure intégrées suivantes :

- Types de ressources non autorisés

- Types de ressources autorisés

Pour plus d’informations, consultez les références suivantes :

- [Tutoriel : Créer et gérer des stratégies pour assurer la conformité](../../governance/policy/tutorials/create-and-manage.md)

- [Exemples Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Aide** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ». Cela peut empêcher la création et la modification de ressources dans un environnement de haute sécurité, comme en cas de configuration d’Azure Key Vault.

- [Gérer l’accès à la gestion Azure avec l’accès conditionnel](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Conseils** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.KeyVault » pour créer des stratégies personnalisées d’audit ou d’application de la configuration de vos instances Azure Key Vault. Vous pouvez également utiliser des définitions Azure Policy intégrées pour Azure Key Vault telles que :

- Les objets Key Vault doivent être récupérables

- Déployer les paramètres de diagnostic de Key Vault sur l’espace de travail Log Analytics

- Les journaux de diagnostic dans Key Vault doivent être activés.

- Key Vault doit utiliser un point de terminaison de service de réseau virtuel

- Déployer les paramètres de diagnostic de Key Vault sur Event Hub

- Utilisez les recommandations d’Azure Security Center comme ligne de base de configuration sécurisée pour vos instances Azure Key Vault.

Pour plus d’informations, consultez les références suivantes :

- [Affichage des alias Azure Policy disponibles](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Tutoriel : Créer et gérer des stratégies pour assurer la conformité](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Aide** : Utilisez les stratégies Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure Key Vault. 

- [Créer et gérer des stratégies pour assurer la conformité](../../governance/policy/tutorials/create-and-manage.md)

  
- [Comprendre les effets d'Azure Policy](../../governance/policy/concepts/effects.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Si vous utilisez des définitions Azure Policy personnalisées pour vos ressources Azure Key Vault, utilisez Azure Repos pour stocker et gérer votre code en toute sécurité.

- [Stocker du code dans Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentation Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Conseils** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.KeyVault » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

- [Guide pratique pour configurer et gérer Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Conseils** : Utilisez Azure Security Center pour effectuer des analyses de ligne de base de vos ressources protégées par Azure Key Vault.

- [Corriger les recommandations dans Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Conseils** : Utilisez Managed Service Identity conjointement avec Azure Key Vault pour simplifier et sécuriser la gestion des secrets pour vos applications cloud. Assurez-vous que la suppression réversible est activée dans Azure Key Vault.

- [Intégrer avec des identités managées Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Créer un coffre de clés](quick-create-portal.md)

- [S’authentifier auprès de Key Vault](authentication.md)

- [Attribuer une stratégie d’accès Key Vault](assign-access-policy-portal.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés peut être nécessaire pour les alertes liées à ce contrôle.

**Définitions intégrées d’Azure Policy – Microsoft.KeyVault** :

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Conseils** : Utilisez Managed Service Identity conjointement avec Azure Key Vault pour simplifier et sécuriser la gestion des secrets pour vos applications Cloud.

  

- [Intégration aux identités managées Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Créer un coffre de clés](quick-create-portal.md)    

- [Comment s’authentifier auprès de Key Vault](authentication.md)

- [Attribuer une stratégie d’accès Key Vault](assign-access-policy-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault.  
  
- [Comment configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Défense contre les programmes malveillants](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Conseils** : Microsoft Antimalware est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple, Azure Key Vault), mais il ne s’exécute pas sur du contenu client.

Pré-analysez tout contenu chargé ou envoyé à des ressources Azure autres que de calcul, telles qu’Azure Key Vault. Microsoft ne peut pas accéder à vos données dans ces instances.

- [Présentation de Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles](../../security/fundamentals/antimalware.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : Garantir des sauvegardes automatiques régulières

**Conseils** : Veillez à effectuer des sauvegardes automatisées régulières de vos certificats, clés, comptes de stockage managés et secrets Key Vault avec les commandes PowerShell suivantes :

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Si vous le souhaitez, vous pouvez stocker vos sauvegardes Key Vault dans Sauvegarde Azure.

- [Comment sauvegarder des certificats Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Comment sauvegarder des clés Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Comment sauvegarder des comptes de stockage managés Key Vault](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Comment sauvegarder des secrets Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Guide pratique pour activer la Sauvegarde Azure](/azure/backup)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés gérées par le client

**Conseils** : Effectuez des sauvegardes automatisées régulières de vos certificats, clés, comptes de stockage managés et secrets Key Vault avec les commandes PowerShell suivantes :

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Si vous le souhaitez, vous pouvez stocker vos sauvegardes Key Vault dans Sauvegarde Azure.

- [Comment sauvegarder des certificats Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Comment sauvegarder des clés Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Comment sauvegarder des comptes de stockage managés Key Vault](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Comment sauvegarder des secrets Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Guide pratique pour activer la Sauvegarde Azure](/azure/backup)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Conseils** : Effectuez régulièrement une restauration de données de vos certificats, clés, comptes de stockage managés et secrets Key Vault avec les commandes PowerShell suivantes :

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Pour plus d’informations, consultez les références suivantes :

- [Restauration de certificats Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Restauration de clés Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Restauration des comptes de stockage gérés par Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Restauration de secrets Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés managées par le client

**Conseils** : Assurez-vous que la suppression réversible est activée pour Azure Key Vault. La suppression réversible permet de récupérer des coffres de clés et des objets de coffre supprimés, tels que des clés, secrets et autres certificats. 

- [Comment utiliser la suppression réversible d’Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés peut être nécessaire pour les alertes liées à ce contrôle.

**Définitions intégrées d’Azure Policy – Microsoft.KeyVault** :

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé. Ces processus doivent se concentrer sur la protection des systèmes sensibles, tels que ceux qui utilisent des secrets Key Vault.

- [Comment configurer des automatisations de workflow dans Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)   

- [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [Le client peut également tirer parti du guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de son propre plan de réponse aux incidents](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Conseils** : Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend de la confiance que Security Center accorde à la recherche ou à la métrique utilisées pour émettre l’alerte, ainsi qu’à la conviction quand à l’existence d’une intention malveillante derrière l’activité à l’origine de l’alerte. En outre, marquez clairement les abonnements (par ex. production, non-production) et créez un système d’attribution de noms pour identifier et classer clairement les ressources Azure, en particulier celles qui traitent des données sensibles telles que des secrets Azure Key Vault.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Conseils** : Effectuez des exercices pour tester les capacités de réponse aux incidents de vos systèmes à intervalles réguliers afin de protéger vos instances Azure Key Vault et les ressources associées. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

- [Reportez-vous à la publication du NIST : Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide de test, d’entraînement et d’utilisation des programmes destinés aux plans et fonctionnalités informatiques)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 : Fournir des informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

**Instructions** : Microsoft utilisera les informations de contact pour le signalement d’incidents de sécurité pour vous contacter si le Microsoft Security Response Center (MSRC) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé.  Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus.

- [Comment définir le contact de sécurité d’Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Conseils** : Exportez vos alertes et recommandations d’Azure Security Center à l’aide de la fonctionnalité d’exportation continue pour identifier les risques pesant sur les ressources Azure Key Vault. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue.  Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser en continu les alertes vers Azure Sentinel. 

 

- [Comment configurer l’exportation continue](../../security-center/continuous-export.md) 

  

- [Comment envoyer des alertes à Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Conseils** : Utilisez la fonctionnalité d’automatisation de flux de travail disponible dans Azure Security Center pour déclencher automatiquement des réponses via « Logic Apps » aux alertes de sécurité et recommandations afin de protéger vos ressources Azure Key Vault. 

 

- [Comment configurer l’automatisation des workflows et Logic Apps](../../security-center/workflow-automation.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : tests d’intrusion et exercices Red Team](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1 : Procéder régulièrement à des tests d’intrusion des ressources Azure et veiller à corriger tous les problèmes de sécurité critiques détectés

**Aide** : Suivez les règles d’engagement de pénétration du cloud Microsoft pour vous assurer que vos tests d’intrusion sont conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft.

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
