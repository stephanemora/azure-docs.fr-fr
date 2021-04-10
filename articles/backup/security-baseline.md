---
title: Base de référence de sécurité Azure pour Azure Backup
description: La base de référence de sécurité Azure Backup propose des instructions procédurales et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans le Benchmark de sécurité Azure.
author: msmbaldwin
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0530baf5c198b5d82527cfb02c66765ec885bd94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566708"
---
# <a name="azure-security-baseline-for-azure-backup"></a>Base de référence de sécurité Azure pour Azure Backup

Cette base de référence de sécurité applique les instructions du [Benchmark de sécurité Azure version 1.0](../security/benchmarks/overview-v1.md) à Azure Backup. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure.
Le contenu est regroupé selon les **contrôles de sécurité** définis par le Benchmark de sécurité Azure et les instructions associées applicables à Azure Backup. Les **contrôles** non applicables à Azure Backup ont été exclus.

 
Pour voir la correspondance complète entre Azure Backup et le Benchmark de sécurité Azure, consultez le [fichier complet de mise en correspondance de la base de référence de sécurité Azure Backup](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-control-network-security.md).*

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions (IDS/IPS) basés sur le réseau

**Aide** : Les points de terminaison utilisés par Sauvegarde Azure (y compris l’agent Microsoft Azure Recovery Services) sont tous gérés par Microsoft. Vous êtes responsable de tous les contrôles supplémentaires que vous souhaitez déployer sur vos systèmes locaux.

- [Présentation de la prise en charge de la mise en réseau et de l’accès pour l’agent MARS](./backup-support-matrix-mars-agent.md#networking-and-access-support)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Conseils** : Si vous utilisez l’agent MARS sur une machine virtuelle Azure, utilisez la balise de service AzureBackup sur vos groupes de sécurité réseau ou votre Pare-feu Azure pour autoriser l’accès sortant vers Sauvegarde Azure.

- [Sauvegarder des bases de données SQL Server sur des machines virtuelles Azure](backup-sql-server-database-azure-vms.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Aide** : Si vous utilisez l’agent MARS sur une machine virtuelle Azure, associez cette machine virtuelle à un groupe de sécurité réseau. Utilisez la description pour spécifier le besoin professionnel de la règle.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Conseils** : Si vous utilisez l’agent MARS sur une machine virtuelle Azure protégée par un NSG ou un Pare-feu Azure, utilisez le journal d’activité Azure pour surveiller la configuration du groupe de sécurité réseau ou du Pare-feu. Vous pouvez créer des alertes dans Azure Monitor qui se déclenchent lorsque des modifications sont apportées à ces ressources.

- [Afficher et récupérer les événements du journal d’activité Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Créer, afficher et gérer des alertes de journal d’activité à l’aide d’Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Pour la journalisation d’audit du plan de contrôle, activez les paramètres de diagnostic des journaux d’activité Azure et envoyez les journaux vers un espace de travail Log Analytics, Event Hub ou un compte de stockage Azure pour archivage. À l’aide des données des journaux d’activité Azure, vous pouvez déterminer les éléments « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) effectuées au niveau du plan de contrôle pour vos ressources Azure.

Ingérez également les journaux via Azure Monitor pour agréger les données de sécurité générées par Sauvegarde Azure. Dans Azure Monitor, utilisez des espaces de travail Log Analytics pour interroger et effectuer l’analytique, et utilisez des comptes de stockage pour le stockage à long terme/d’archivage. Vous pouvez également intégrer des données dans Azure Sentinel ou une solution SIEM (Security Information and Event Management) de tiers.

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

- [Utilisation des paramètres de diagnostic pour les coffres Recovery Services](backup-azure-diagnostic-events.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : Pour la journalisation d’audit du plan de contrôle, activez les paramètres de diagnostic des journaux d’activité Azure et envoyez les journaux vers un espace de travail Log Analytics, Event Hub ou un compte de stockage Azure pour archivage. À l’aide des données des journaux d’activité Azure, vous pouvez déterminer les éléments « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) effectuées au niveau du plan de contrôle pour vos ressources Azure.

En outre, Sauvegarde Azure envoie des événements de diagnostic qui peuvent être collectés et utilisés à des fins d’analyse, d’alerte et de création de rapports. Vous pouvez configurer les paramètres de diagnostic d’un coffre Recovery Services par le biais du portail Azure. Vous pouvez envoyer un ou plusieurs événements de diagnostic à un compte de stockage, un Event Hub ou un espace de travail Log Analytics.

- [Activation des paramètres de diagnostic pour le journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

- [Utilisation des paramètres de diagnostic pour les coffres Recovery Services](backup-azure-diagnostic-events.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Aide** : Dans Azure Monitor, définissez la période de rétention des journaux pour les espaces de travail Log Analytics associés à vos coffres Recovery Services conformément aux réglementations de conformité de votre organisation.

- [Définition des paramètres de rétention des journaux](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Aide** : Sauvegarde Azure fournit des capacités intégrées de supervision et d’alerte dans un coffre Recovery Services. Ces fonctionnalités sont disponibles sans infrastructure de gestion supplémentaire. Vous pouvez également augmenter l’échelle de votre supervision et de vos rapports à l’aide d’Azure Monitor.

Activez les paramètres de diagnostic des journaux d’activité Azure et envoyez les journaux à un espace de travail Log Analytics. Exécutez des requêtes dans Log Analytics pour rechercher des termes, identifier des tendances, analyser des modèles et fournir de nombreuses autres informations basées sur les données du journal d’activité qui ont pu être collectées pour les coffres Recovery Services.

- [Surveillance des charges de travail de Sauvegarde Azure](backup-azure-monitoring-built-in-monitor.md)

- [Activation des paramètres de diagnostic pour le journal d’activité Azure](../azure-monitor/essentials/activity-log.md)

- [Collecte et analyse des journaux d’activité Azure dans l’espace de travail Log Analytics dans Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Aide** : Sauvegarde Azure fournit des capacités intégrées de supervision et d’alerte dans un coffre Recovery Services. Ces fonctionnalités sont disponibles sans infrastructure de gestion supplémentaire. Vous pouvez également augmenter l’échelle de votre supervision et de vos rapports à l’aide d’Azure Monitor.

Les alertes sont principalement des scénarios dans le cadre desquels les utilisateurs reçoivent des notifications leur permettant de prendre les mesures nécessaires. La section Alertes de sauvegarde affiche les alertes générées par le service Sauvegarde Azure. Ces alertes sont définies par le service et vous ne pouvez pas créer d’alerte de façon personnalisée.

Vous pouvez également intégrer un espace de travail Log Analytics à Azure Sentinel, car cela fournit une solution d’orchestration, d’automatisation et de réponse aux incidents de sécurité informatique (Security Orchestration Automated Response, SOAR). Cela permet de créer des playbooks (solutions automatisées) utilisables pour corriger des problèmes de sécurité. En outre, vous pouvez créer des alertes de journal personnalisées dans votre espace de travail Log Analytics à l’aide d’Azure Monitor.

- [Surveillance des charges de travail de Sauvegarde Azure](backup-azure-monitoring-built-in-monitor.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Conseils** : Azure Active Directory (Azure AD) inclut des rôles intégrés qui doivent être explicitement attribués, et vous pouvez lancer une requête pour déterminer les comptes qui sont membres de ces rôles. Utilisez le module Azure AD PowerShell pour effectuer des requêtes ad hoc afin de découvrir les comptes membres de groupes d’administration.

- [Guide pratique pour obtenir un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Guide pratique pour obtenir les membres d’un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Aide** : Azure Active Directory (Azure AD) n’intègre pas le concept des mots de passe par défaut. Selon le service, d’autres ressources Azure qui exigent un mot de passe forcent la création d’un mot de passe conforme à des exigences de complexité et d’une longueur minimale. Vous êtes responsable des applications tierces et des services de la place de marché susceptibles d’utiliser des mots de passe par défaut.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseils** : Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration.

En outre, pour vous aider à suivre les comptes d’administration dédiés, vous pouvez utiliser des recommandations d’Azure Security Center ou des stratégies Azure intégrées, telles que les suivantes :

- Plusieurs propriétaires doivent être affectés à votre abonnement

- Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement

- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement

Des informations supplémentaires sont disponibles sur les liens référencés.

- [Utilisation d’Azure Security Center pour superviser l’identité et l’accès (préversion)](../security-center/security-center-identity-access.md)

- [Utilisation d’Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4 : Utiliser l’authentification unique (SSO) Azure Active Directory

**Aide** : Utilisez une inscription d’application Azure (principal du service) afin de récupérer un jeton qui peut être utilisé pour interagir avec vos coffres Recovery Services via des appels d’API.

- [Appels d’API REST Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Guide pratique pour inscrire votre application cliente (principal de service) à l’aide d’Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informations sur l’API Azure Recovery Services](/rest/api/recoveryservices/)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseils** : Lorsque vous effectuez des opérations critiques dans Sauvegarde Azure, vous devez entrer un code PIN de sécurité, disponible sur le Portail Azure. L’activation de l’authentification multifacteur Azure Active Directory (Azure AD) ajoute une couche de sécurité. Seuls les utilisateurs autorisés, disposant d’informations d’identification Azure valides et authentifiés à partir d’un deuxième appareil peuvent accéder au portail Azure.

- [Azure Multi-Factor Authentication dans Sauvegarde Azure](backup-azure-security-feature.md)

- [Planifier un déploiement d'Azure AD Multi-Factor Authentication basé sur le cloud](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Conseils** : Utilisez des stations de travail gérées par Azure sur lesquelles le service Azure AD Multi-Factor Authentication (MFA) est configuré pour vous connecter à vos ressources compatibles avec Sauvegarde Azure et les configurer.

- [Stations de travail d’accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planifier un déploiement d'Azure AD Multi-Factor Authentication basé sur le cloud](../active-directory/authentication/howto-mfa-getstarted.md)

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

**Conseils** : Utilisez Azure Active Directory (Azure AD) comme système central d’authentification et d’autorisation pour vos instances Sauvegarde Azure. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

- [Configuration de Sauvegarde Azure pour utiliser la connexion Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Création et configuration d’une instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Azure Active Directory (Azure AD) fournit des journaux pour vous aider à découvrir les comptes obsolètes. Utilisez également les révisions d’accès Azure AD pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs doit être passé en revue régulièrement pour vérifier que seuls les utilisateurs appropriés bénéficient d’un accès permanent.

- [Présentation des rapports Azure AD](../active-directory/reports-monitoring/index.yml)

- [Utiliser les révisions d’accès Azure AD](../active-directory/governance/access-reviews-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Conseils** : Utilisez Azure Active Directory (Azure AD) comme système central d’authentification et d’autorisation pour vos instances Sauvegarde Azure. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

Vous avez accès aux activités de connexion Azure AD et aux sources des journaux des événements à risque et des audits, ce qui vous permet de les intégrer à Azure Sentinel ou à un outil SIEM tiers.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure AD et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Vous pouvez configurer les alertes de journal souhaitées dans un espace de travail Log Analytics.

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Procédure d’intégration d’Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12 : Alerter en cas d’écart de comportement de connexion à un compte

**Conseils** : Utilisez Azure Active Directory (Azure AD) comme système central d’authentification et d’autorisation pour vos coffres Recovery Services. Pour les écarts du comportement de connexion au compte sur le plan de contrôle (le Portail Azure), utilisez Azure AD Identity Protection et les fonctionnalités de détections de risques pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités d’utilisateur. Vous pouvez également ingérer des données dans Azure Sentinel pour approfondir votre examen.

- [Guide pratique pour afficher une connexion risquée Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

**Aide** : Actuellement non disponible ; Customer Lockbox n’est pas encore pris en charge pour Sauvegarde Azure.

- [Liste des services pris en charge pour Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

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

**Conseils** : Lors de la sauvegarde de machines virtuelles IaaS Azure Infrastructure as a service (IaaS), Sauvegarde Azure fournit des sauvegardes indépendantes et isolées pour éviter une destruction accidentelle des données d’origine. Les sauvegardes sont stockées dans un coffre Recovery Services avec gestion intégrée des points de récupération.

Implémentez des abonnements et éventuellement des groupes d’administration distincts pour le développement, les tests et la production de coffres Recovery Services. Les ressources doivent être séparées par un réseau virtuel ou un sous-réseau, étiquetés de manière appropriée et sécurisés par des groupes de sécurité réseau (NSG) ou un Pare-feu Azure. Les ressources de stockage ou de traitement des données sensibles doivent être suffisamment isolées. Pour les machines virtuelles qui stockent ou traitent des données sensibles, implémentez la stratégie et les procédures pour les désactiver lorsqu’elles ne sont pas utilisées.

- [Vue d’ensemble d’Azure Backup](backup-overview.md)

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

**Aide** : Actuellement non disponible ; les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour Sauvegarde Azure.

Microsoft gère l’infrastructure sous-jacente de Sauvegarde Azure et a implémenté des contrôles stricts pour empêcher la perte ou la divulgation des données des clients.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Aide** : Actuellement non disponible ; les fonctionnalités d’identification des données, de classification des données et de protection contre la perte de données ne sont pas encore disponibles pour Sauvegarde Azure.

Microsoft gère l’infrastructure sous-jacente de Sauvegarde Azure et a implémenté des contrôles stricts pour empêcher la perte ou la divulgation des données des clients.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6 : Utiliser le contrôle d’accès en fonction du rôle pour contrôler l’accès aux ressources

**Conseils** : Le contrôle d’accès en fonction du rôle Azure (Azure RBAC) permet une gestion des accès affinée pour Azure. Avec Azure RBAC, vous pouvez séparer les tâches au sein de votre équipe et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail.

Le service Sauvegarde Azure fournit trois rôles intégrés pour contrôler les opérations de gestion des sauvegardes : Contributeur de sauvegarde, Opérateur de sauvegarde et Lecteur de sauvegarde. Vous pouvez mapper les rôles intégrés de Sauvegarde à différentes actions de gestion des sauvegardes.

- [Comment configurer Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Utilisation du contrôle d’accès en fonction du rôle Azure pour gérer les points de récupération Sauvegarde Azure](backup-rbac-rs-vault.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

**Aide** : Sauvegarde Azure prend en charge le chiffrement des données au repos. Pour la sauvegarde sur site, le chiffrement au repos est assuré à l’aide de la phrase secrète que vous fournissez lorsque vous sauvegardez sur Azure. Pour les charges de travail cloud, les données sont chiffrées au repos à l’aide de Storage Service Encryption (SSE). Microsoft ne déchiffre les données de sauvegarde à aucun moment.

Lorsque vous sauvegardez avec l’agent MARS ou à l’aide d’un coffre Recovery Services chiffré avec une clé gérée par le client, vous seul avez accès à la clé de chiffrement. Microsoft ne conserve jamais de copie de la clé de chiffrement et n’y a pas accès. Si la clé est égarée, Microsoft ne peut pas récupérer les données de sauvegarde.

- [Présentation du chiffrement au repos dans Sauvegarde Azure](backup-encryption.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Utilisez Azure Monitor avec le journal d’activité Azure pour créer des alertes en cas de modifications sur des coffres Azure Recovery Services en production et d’autres ressources cruciales ou associées.

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Conseils** : Pas encore disponible ; l’évaluation des vulnérabilités dans Azure Security Center n’est pas encore disponible pour Sauvegarde Azure.

La plateforme sous-jacente est analysée et corrigée par Microsoft. Examinez les contrôles de sécurité disponibles pour Sauvegarde Azure afin de réduire les vulnérabilités liées à la configuration du service.

- [Comprendre les contrôles de sécurité disponibles pour Sauvegarde Azure]()

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes.

**Aide** : Actuellement non disponible ; les configurations de sécurité pour Sauvegarde Azure ne sont pas encore prises en charge dans Azure Security Center.

- [Liste des services PaaS pris en charge par Azure Security Center](../security-center/features-paas.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Conseils** : Utilisez Azure Resource Graph pour interroger/découvrir toutes les ressources (telles que calcul, stockage, réseau, ports et protocoles) dans vos abonnements. Vérifiez les autorisations (lecture) appropriées dans votre locataire et répertoriez tous les abonnements Azure, ainsi que les ressources dans vos abonnements.

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

En outre, utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans les abonnements clients à l’aide des définitions de stratégie intégrées suivantes :

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

Utilisez Azure Resource Graph pour interroger et découvrir des ressources dans leurs abonnements.  Vérifiez que toutes les ressources Azure présentes dans l’environnement sont approuvées.

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

**Aide** : Définissez et implémentez des configurations de sécurité standard pour votre coffre Recovery Services à l’aide d’Azure Policy. Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.RecoveryServices » pour créer des stratégies personnalisées permettant d’auditer vos coffres Recovery Services ou d’appliquer leur configuration.

- [Affichage des alias Azure Policy disponibles](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Conseils** : Utilisez les effets Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Présentation des effets d’Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Si vous utilisez des définitions Azure Policy personnalisées, utilisez Azure DevOps ou Azure Repos pour stocker et gérer votre code en toute sécurité.

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentation Azure Repos](/azure/devops/repos)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Conseils** : Utilisez les définitions Azure Policy intégrées ainsi que des alias Azure Policy dans l’espace de noms « Microsoft.RecoveryServices » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Conseils** : Utilisez les définitions Azure Policy intégrées ainsi que des alias Azure Policy dans l’espace de noms **Microsoft.RecoveryServices** pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. Utilisez les effets Azure Policy [auditer], [refuser] et [déployer s’il n’existe pas] afin d’appliquer automatiquement des configurations pour vos ressources Azure.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Aide** : Lors de la configuration de l’agent MARS, stockez votre phrase secrète de chiffrement dans Azure Key Vault.

- [Comment s’authentifier auprès de Key Vault](../key-vault/general/authentication.md)

- [Comment attribuer une stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md)

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

**Conseils** : Microsoft Antimalware pour Azure est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple, Sauvegarde Azure), mais il ne s’exécute pas sur votre contenu.

Pré-analysez les fichiers chargés sur des ressources Azure non liées au calcul, comme App Service, Data Lake Storage et Stockage Blob.

Utilisez la protection contre les menaces pour les services de données dans Azure Security Center pour détecter les programmes malveillants chargés sur les comptes de stockage.

- [Présentation de Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles](../security/fundamentals/antimalware.md)

- [Présentation de la protection contre les menaces pour les services de données d’Azure Security Center](../security-center/azure-defender.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés managées par le client

**Aide** : Le stockage localement redondant (LRS) réplique vos données trois fois (il crée trois copies de vos données) dans une unité d’échelle de stockage dans un centre de données. Toutes les copies des données existent dans la même région. Le stockage LRS est une option à faible coût qui protège vos données contre les défaillances matérielles locales. Le stockage géo-redondant (GRS) est l’option de réplication recommandée par défaut. Le stockage géo-redondant réplique vos données vers une région secondaire, distante de plusieurs centaines de kilomètres de l’emplacement principal des données sources. Le stockage GRS est plus onéreux que le stockage LRS, mais il offre une durabilité des données supérieure, même en cas de panne au niveau régional.

Sauvegardez les clés gérées par le client dans Azure Key Vault.

- [Vue d’ensemble d’Azure Backup](backup-overview.md)

- [Guide pratique pour sauvegarder des clés de coffre de clés dans Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Présentation du chiffrement dans Sauvegarde Azure](backup-encryption.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/azure/governance/policy/samples/azure-security-benchmark) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/azure/security-center/security-center-recommendations). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/azure/security-center/azure-defender) pour les services associés.

**Azure Policy les définitions intégrées-Microsoft.RecoveryServices** :

[!INCLUDE [Resource Policy for Microsoft.RecoveryServices 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.recoveryservices-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés managées par le client

**Aide** : Testez la restauration des clés gérées par le client sauvegardées.

- [Guide pratique pour restaurer des clés de coffre de clés dans Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés managées par le client

**Aide** : Pour la sauvegarde sur site, le chiffrement au repos est assuré à l’aide de la phrase secrète que vous fournissez lorsque vous sauvegardez sur Azure. Pour les machines virtuelles Azure, les données sont chiffrées au repos à l’aide de Storage Service Encryption (SSE). Vous pouvez activer la suppression réversible dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante.

- [Activation de la suppression réversible dans Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Conseils** : Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.

- [Comment configurer des automatisations de workflow dans Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Guide de gestion des incidents de sécurité informatique du NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Conseils** : Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements et créez un système de nommage pour identifier et classer les ressources Azure de façon claire.

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

**Aide** : Suivez les règles d’engagement de Microsoft pour que vos tests d’intrusion soient conformes aux stratégies de Microsoft. Suivez les liens référencés pour trouver un complément d’informations sur la stratégie de Microsoft, l’exécution de Red Teaming et les tests d’intrusion de site actif sur l’infrastructure, les services et les applications cloud managés par Microsoft.

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](../security/benchmarks/overview.md)
- En savoir plus sur les [bases de référence de la sécurité Azure](../security/benchmarks/security-baselines-overview.md)
