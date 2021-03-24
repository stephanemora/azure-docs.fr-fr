---
title: Base de référence de sécurité Azure pour Azure Policy
description: La base de référence de sécurité Azure Policy fournit des instructions et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans le benchmark de sécurité Azure.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9916ad6d3b6cb1a63d34004915666226b7836490
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740345"
---
# <a name="azure-security-baseline-for-azure-policy"></a>Base de référence de sécurité Azure pour Azure Policy

Cette base de référence de sécurité applique les conseils du [benchmark de sécurité Azure](../../../security/benchmarks/overview.md) à Azure Policy. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure. Le contenu est regroupé par **domaines de conformité** et par **contrôles de sécurité** définis par le benchmark de sécurité Azure et les conseils associés applicables à Azure Policy. **Les contrôles** non applicables à Azure Policy ont été exclus. Pour voir comment Azure Policy est entièrement mappé au benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité Azure Policy](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Pour un mappage des contrôles du benchmark de sécurité Azure aux définitions de stratégie intégrées via l’initiative intégrée, consultez [Conformité réglementaire : benchmark de sécurité Azure](../samples/azure-security-benchmark.md).

Azure Policy utilise le terme de _Propriété_ à la place de _Responsabilité_. Pour des détails sur la _propriété_, consultez [Définition de stratégie Azure Policy](./definition-structure.md#type) et [Responsabilité partagée dans le cloud](../../../security/fundamentals/shared-responsibility.md).

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : Azure Policy utilise les journaux d’activité, qui sont automatiquement activés, pour inclure la source de l’événement, la date, l’utilisateur, l’horodatage, les adresses sources, les adresses de destination et d’autres éléments utiles.

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Présentation de la journalisation et des différents types de journaux dans Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseils** : Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration. Vous pouvez également activer une solution JIT (Juste-à-temps)/JEA (Just-Enough-Access) à l’aide de rôles privilégiés [Azure Active Directory (Azure AD) Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) ou d’[Azure Resource Manager](../../../azure-resource-manager/management/overview.md).

**Responsabilité** : Customer

**Supervision Azure Security Center** : [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) est l’initiative de stratégie par défaut pour Azure Security Center et constitue la base des [recommandations de Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Les alertes liées à ce contrôle peuvent nécessiter un plan [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) pour les services associés.

**Définitions Azure Policy intégrées - Microsoft.GuestConfiguration** :

[!INCLUDE [Resource Policy for Microsoft.GuestConfiguration 3.3](../../../../includes/policy/standards/asb/rp-controls/microsoft.guestconfiguration-3-3.md)]

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6 : Utiliser des stations de travail sécurisées et gérées par Azure pour les tâches administratives

**Conseil** : utilisez des stations de travail disposant d’un accès privilégié avec l’authentification multifacteur configurée pour vous connecter aux ressources Azure et les configurer.

- [En savoir plus sur les stations de travail à accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 : Utiliser Azure RBAC pour contrôler l’accès aux ressources 

**Aide** : Utilisez le contrôle d’accès en fonction du rôle Azure (Azure RBAC) pour contrôler l’accès à Azure Policy.

- [Autorisations Azure RBAC dans Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview#azure-rbac-permissions-in-azure-policy)

- [Comment configurer Azure RBAC](../../../role-based-access-control/role-assignments-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Utilisez Azure Monitor avec les journaux d’activités pour créer des alertes lorsque des modifications sont apportées à Azure Policy.

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Conseils** : Appliquez des balises aux ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories. Utilisez l’effet _modifier_ d’Azure Policy pour signaler et appliquer la conformité et la gouvernance cohérente des balises.

- [Tutoriel : Créer et gérer les stratégies](../tutorials/create-and-manage.md)

- [Tutoriel : Gérer la gouvernance des balises](../tutorials/govern-tags.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4 : Définir et tenir un inventaire des ressources Azure approuvées

**Aide** : créer un inventaire des définitions de stratégie approuvées et des affectations de stratégie conformément aux besoins de votre organisation.

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Aide** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans vos abonnements.

- [Guide pratique pour configurer et gérer Azure Policy](../tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
