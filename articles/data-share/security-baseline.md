---
title: Base de référence de sécurité Azure pour Azure Data Share
description: La base de référence de sécurité Azure Data Share fournit des instructions et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans le Benchmark de sécurité Azure.
author: msmbaldwin
ms.service: data-share
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d423a8aaf3ace78ccb4b2eb1d8424c6a1dfb61bc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705974"
---
# <a name="azure-security-baseline-for-azure-data-share"></a>Base de référence de sécurité Azure pour Azure Data Share

Cette base de référence de sécurité applique les conseils du [Benchmark de sécurité Azure version 1.0](../security/benchmarks/overview-v1.md) à Microsoft Azure Data Share. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure.
Le contenu est regroupé par les **contrôles de sécurité** définis par le Benchmark de sécurité Azure et les conseils associés applicables à Azure Data Share. **Les contrôles** non applicables à Azure Data Share ont été exclus.

 
Pour voir comment Azure Data Share est entièrement mappé au Benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité Azure Data Share](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Conseils** : Ingérez des journaux relatifs à Azure Data Share par le biais d’Azure Monitor pour agréger les données de sécurité générées par les appareils de point de terminaison, les ressources réseau et d’autres systèmes de sécurité. Dans Azure Monitor, utilisez des espaces de travail Log Analytics pour interroger et effectuer l’analytique, et utilisez des comptes Stockage Azure pour le stockage à long terme et l’archivage.

Vous pouvez également activer et intégrer ces données dans Azure Sentinel ou une solution SIEM tierce.

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings) 

- [Guide pratique pour bien démarrer avec Azure Monitor et l’intégration d’une solution SIEM tierce](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Conseils** : Les journaux d’activité, disponibles automatiquement, contiennent toutes les opérations d’écriture (PUT, POST, DELETE) pour vos ressources Azure Data Share, à l’exception des opérations de lecture (GET). Les journaux d’activité peuvent être utilisés pour rechercher une erreur lors de la résolution de problèmes ou pour surveiller la manière dont un utilisateur de votre organisation a modifié une ressource.

Activez les journaux de diagnostic pour Azure Data Share, en particulier les journaux de diagnostic pour MicrosoftDataShareSentShareSnapshotsLog et MicrosoftDataShareReceivedShareSnapshotsLog. Ces journaux vous permettent de capturer des informations clés telles que l’heure de début, l’heure de fin, l’état et d’autres détails de la synchronisation. Ces journaux peuvent être essentiels pour l’examen ultérieur des incidents de sécurité et l’exécution d’exercices légaux.

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings) 

- [Présentation de la journalisation et des différents types de journaux dans Azure](/azure/azure-monitor/platform/platform-logs-overview)

- [Guide pratique pour configurer les paramètres de diagnostic pour le journal d’activité Azure](/azure/azure-monitor/platform/activity-log)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Conseils** : Vérifiez que la période de conservation des journaux définie dans les comptes de stockage ou les espaces de travail Log Analytics utilisés pour le stockage des journaux d’Azure Data Share est conforme aux obligations réglementaires de votre organisation.

- [Comment configurer la période de conservation d’un espace de travail Log Analytics](/azure/azure-monitor/platform/manage-cost-storage)

- [Stockage des journaux des ressources dans un compte de stockage Azure](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Conseils** : Analysez et supervisez les journaux relatifs à Azure Data Share afin de détecter les comportements anormaux et examinez régulièrement les résultats. Utilisez Azure Monitor et un espace de travail Log Analytics pour examiner les journaux et effectuer des requêtes sur leurs données.

Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce.

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Bien démarrer avec les requêtes Log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Guide pratique pour effectuer des requêtes personnalisées dans Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Conseil** : utilisez Azure Security Center avec un espace de travail Log Analytics pour superviser les activités anormales détectées dans les journaux de sécurité et les événements et générer des alertes s’y rapportant. Vous pouvez également activer et intégrer les données dans Azure Sentinel.

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Guide pratique pour gérer les alertes dans Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md) 

- [Guide pratique pour générer une alerte sur des données de journal Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4 : Utiliser l’authentification unique (SSO) avec Azure Active Directory

**Conseils** : Azure Data Share prend en charge l’authentification unique auprès d’Azure Active Directory (Azure AD). Réduisez le nombre d’identités et d’informations d’identification devant être gérées par les utilisateurs en activant l’authentification unique pour le service avec les identités existantes de votre organisation.

- [Présentation de l’authentification SSO avec Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseils** : activez une authentification multifacteur (MFA) Azure AD, et suivez les recommandations de gestion des identités et des accès du Azure Security Center.

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Aide** : Utilisez une station de travail sécurisée et managée par Azure (également appelée station de travail à accès privilégié) pour les tâches administratives qui requièrent des privilèges élevés.

- [Comprendre les stations de travail sécurisées gérées par Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Guide pratique pour activer Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Utiliser Azure Active Directory (Azure AD) comme système d’authentification et d’autorisation central. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

- [Création et configuration d’une instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Azure Data Share fonctionne avec les rôles intégrés Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#general)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Conseils** : Azure AD fournit des journaux pour vous aider à découvrir les comptes obsolètes. Par ailleurs, utilisez les révisions d’accès et des identités Azure AD pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seuls les utilisateurs appropriés continuent de bénéficier d’un accès.

- [Présentation des rapports Azure AD](/azure/active-directory/reports-monitoring/) 

- [Utilisation des révisions d’accès et des identités Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Azure Data Share fonctionne avec les rôles intégrés Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#general)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Conseil** : vous avez accès aux sources des journaux Activité de connexion, Audit et Événement à risque Azure Active Directory (Azure AD), qui permettent l’intégration à n’importe quel outil SIEM/de supervision.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure AD et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Vous pouvez configurer les alertes souhaitées dans un espace de travail Log Analytics.

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12 : Alerte en cas d’écart de comportement de connexion à un compte

**Aide** : Utilisez les fonctionnalités d’Azure Active Directory (Azure AD) Identity Protection pour configurer des réponses automatisées aux actions suspectes détectées, liées aux identités d’utilisateur. Vous pouvez également ingérer des données dans Azure Sentinel pour approfondir votre examen.

- [Guide pratique pour afficher les connexions risquées Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6 : Utiliser Azure RBAC pour contrôler l’accès aux ressources

**Conseils** : Utilisez le contrôle d’accès en fonction du rôle Azure (Azure RBAC) pour gérer l’accès aux données et aux ressources relatives aux ressources Azure Data Share ; sinon, utilisez des méthodes de contrôle d’accès spécifiques au service.

- [Configurer le contrôle d'accès en fonction du rôle dans Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Conseils** : Utilisez Azure Monitor avec le journal des activités Azure pour créer des alertes Azure Monitor lorsque des modifications sont apportées à des ressources Azure critiques.

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 : Exécuter les outils d’analyse des vulnérabilités automatisés

**Conseils** : Azure Data Share ne permet pas l’installation d’outils d’analyse des vulnérabilités pour ses ressources.

En général, suivez les recommandations d’Azure Security Center relatives à l’évaluation des vulnérabilités sur vos machines virtuelles Azure, images conteneur et serveurs SQL.

Utilisez une solution tierce pour effectuer des évaluations des vulnérabilités sur les périphériques réseau et les applications Web. Lors de l’exécution d’analyses à distance, n’utilisez pas un compte d’administration unique et perpétuel. Envisagez d’implémenter une méthodologie d’approvisionnement JIT pour le compte d’analyse. Les informations d’identification du compte d’analyse doivent être protégées, surveillées et utilisées uniquement pour l’analyse des vulnérabilités.

- [Implémenter les recommandations d'évaluation des vulnérabilités d'Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Conseils** : Vous allez appliquer des étiquettes à vos ressources Azure, groupes de ressources et abonnements pour les organiser de façon logique dans une taxonomie. Chaque balise se compose d’une paire nom-valeur. Par exemple, vous pouvez appliquer le nom « Environnement » et la valeur « Production » à toutes les ressources en production.

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Conseils** : Appliquez des étiquettes à vos ressources Azure, groupes de ressources et abonnements pour les organiser de façon logique dans une taxonomie. Chaque balise se compose d’une paire nom-valeur. Par exemple, vous pouvez appliquer le nom « Environnement » et la valeur « Production » à toutes les ressources en production.

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Aide** : Le cas échéant, utilisez des étiquettes, des groupes d’administration et des abonnements séparés pour organiser et suivre les ressources. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4 : Dresser et tenir un inventaire des ressources Azure approuvées

**Conseils** : Créez un inventaire des ressources Azure et logiciels approuvés pour les ressources de calcul en fonction des besoins de votre organisation.

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Aide** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans vos abonnements.
Utilisez Azure Resource Graph pour interroger et découvrir des ressources dans leurs abonnements. Vérifiez que toutes les ressources Azure présentes dans l’environnement sont approuvées.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Guide pratique pour créer des requêtes avec Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 : Supprimer des ressources et applications logicielles Azure non approuvées

**Conseils** : Supprimez les ressources Azure lorsqu’elles ne sont plus nécessaires. Pour ce faire, vous pouvez utiliser le portail Azure, PowerShell ou CLI.

- [Suppression de ressources et de groupes de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)

Azure Data Share n’expose pas le système d’exploitation et ne vous permet pas d’installer des applications logicielles tierces sur ses ressources.

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Aide** : Utilisez Azure Policy pour limiter les services que vous pouvez approvisionner dans votre environnement.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Aide** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ».

- [Guide pratique pour configurer l’accès conditionnel de façon à bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Utilisez Azure DevOps pour stocker et gérer de manière sécurisée votre code, comme les définitions de stratégies Azure personnalisées, les modèles Azure Resource Manager et les scripts Desired State Configuration. Pour accéder aux ressources que vous gérez dans Azure DevOps, vous pouvez accorder ou refuser des autorisations à des utilisateurs spécifiques, à des groupes de sécurité intégrés ou à des groupes définis dans Azure Active Directory (Azure AD) s’ils sont intégrés à Azure DevOps, ou à Active Directory s’il est intégré à TFS.

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [À propos des autorisations et des groupes dans Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Conseils** : Utilisez des alias Azure Policy dans l’espace de noms « DataShare » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Utiliser des alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Conseils** : Utilisez des identités managées pour fournir aux services Azure une identité gérée automatiquement dans Azure Active Directory (Azure AD). Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

- [Guide pratique pour configurer des identités managées pour des ressources Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
