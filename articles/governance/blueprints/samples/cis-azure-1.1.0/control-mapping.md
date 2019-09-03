---
title: Exemple - Blueprint CIS Microsoft Azure Foundations Benchmark - Correspondance des recommandations
description: Correspondance des recommandations de l’exemple de blueprint CIS Microsoft Azure Foundations Benchmark avec Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 118ff46ca3b640c6ec24ab85fd598f213229417a
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014014"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Exemple de blueprint CIS Microsoft Azure Foundations Benchmark : correspondance des recommandations

L’article suivant explique en détail comment l’exemple de blueprint CIS Microsoft Azure Foundations Benchmark d’Azure Blueprints correspond aux recommandations de CIS Microsoft Azure Foundations Benchmark. Pour plus d’informations sur les recommandations, consultez [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

Les correspondances suivantes sont relatives aux recommandations **CIS Microsoft Azure Foundations Benchmark v1.1.0**. Utilisez le volet de navigation de droite pour accéder directement à une correspondance de recommandation spécifique.
De nombreuses recommandations mises en correspondance sont implémentées avec une initiative [Azure Policy](../../../policy/overview.md). Pour examiner l’initiative complète, ouvrez **Stratégie** dans le portail Azure et sélectionnez la page **Définitions**. Ensuite, recherchez et sélectionnez l’initiative de stratégie intégrée **\[Préversion\] Auditer les recommandations CIS Microsoft Azure Foundations Benchmark v1.1.0 et déployer des extensions de machine virtuelle spécifiques pour prendre en charge les exigences d’audit**.

> [!NOTE]
> L’exemple de blueprint complet sera bientôt disponible. L’initiative Azure Policy associée est déjà disponible.

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Vérifier que l’authentification multifacteur est activée pour tous les utilisateurs privilégiés

Ce blueprint attribue des définitions [Azure Policy](../../../policy/overview.md) qui s’alignent sur cette recommandation CIS.

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement
- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Vérifier que l’authentification multifacteur est activée pour tous les utilisateurs non privilégiés

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- L'authentification multifacteur doit être activée sur les comptes disposant d’autorisations de lecture de votre abonnement

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Vérifier qu’il n’y a aucun utilisateur invité

Ce blueprint attribue des définitions [Azure Policy](../../../policy/overview.md) qui s’alignent sur cette recommandation CIS.

- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller les mises à jour système » n’est pas défini sur « Désactivé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Des mises à jour système doivent être installées sur vos machines

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller les vulnérabilités de système d’exploitation » n’est pas défini sur « Désactivé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Les vulnérabilités de la configuration de sécurité sur vos machines doivent être corrigées

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller Endpoint Protection » n’est pas défini sur « Désactivé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Superviser les agents Endpoint Protection manquants dans Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller le chiffrement des disques » n’est pas défini sur « Désactivé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Le chiffrement de disque doit être appliqué sur les machines virtuelles

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller le pare-feu d’applications web » n’est pas défini sur « Désactivé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Les règles de groupe de sécurité réseau pour les applications web IaaS doivent être renforcées

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Vérifier que le paramètre de stratégie par défaut d’ASC « Superviser l’évaluation des vulnérabilités » n’est pas défini sur « Désactivé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller l’accès réseau JIT » n’est pas défini sur « Désactivé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller le chiffrement SQL » n’est pas défini sur « Désactivé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- La technologie Transparent Data Encryption doit être activée sur les bases de données SQL

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Vérifier que l’option « Transfert sécurisé requis » est définie sur « Activé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- La sécurisation du transfert vers des comptes de stockage doit être activée

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Vérifier que la règle d’accès réseau par défaut pour les comptes de stockage est définie sur Refuser

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Auditer l'accès réseau non restreint aux comptes de stockage

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Vérifier que l’option « Audit » est définie sur « Activé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- L’audit doit être activé sur les paramètres de sécurité des données avancés sur SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Vérifier que « AuditActionGroups » dans la stratégie « Audit » pour un serveur SQL est correctement défini

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Les paramètres d’audit SQL doivent avoir des groupes d’actions configurés pour capturer les activités critiques.

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Vérifier que la période de conservation d’audit est supérieure à 90 jours

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Les serveurs SQL doivent être configurés avec une période de conservation d’audit supérieure à 90 jours.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Vérifier que l’option « Advanced Data Security » sur un serveur SQL Server est définie sur « Activé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Advanced Data Security doit être activé sur vos serveurs SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 Vérifier que l’option « Types de détection des menaces » est définie sur « Tous »

Ce blueprint attribue des définitions [Azure Policy](../../../policy/overview.md) qui s’alignent sur cette recommandation CIS.

- Les types Advanced Threat Protection doivent être définis sur « Tous » dans les paramètres Advanced Data Security du serveur SQL.
- Les types Advanced Threat Protection doivent être définis sur « Tous » dans les paramètres Advanced Data Security de l’instance gérée SQL.

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 Vérifier que l’option « Envoyer des alertes à » est définie

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Les paramètres Advanced Data Security pour le serveur SQL doivent inclure une adresse e-mail pour la réception des alertes de sécurité.

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Vérifier que l’option « Envoyer un e-mail au service et aux coadministrateurs » est définie sur « Activé ».

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Les paramètres Advanced Data Security pour l’instance gérée SQL doivent inclure une adresse e-mail pour la réception des alertes de sécurité.

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Vérifier que l’administrateur Azure Active Directory est configuré

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Un administrateur Azure Active Directory doit être approvisionné pour les serveurs SQL

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Vérifiez que l’option « Chiffrement des données » est définie sur « Activé » sur une base de données SQL

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- La technologie Transparent Data Encryption doit être activée sur les bases de données SQL

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Vérifier que le protecteur TDE du serveur SQL est chiffré avec BYOK (Bring Your Own Key)

Ce blueprint attribue des définitions [Azure Policy](../../../policy/overview.md) qui s’alignent sur cette recommandation CIS.

- Le protecteur TDE du serveur SQL doit être chiffré avec votre propre clé.
- Le protecteur TDE de l’instance gérée SQL doit être chiffré avec votre propre clé.

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Vérifier que la journalisation d’Azure KeyVault est définie sur « Activé »

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Les journaux de diagnostic dans Key Vault doivent être activés.

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Vérifier que les disques du système d’exploitation sont chiffrés

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Le chiffrement de disque doit être appliqué sur les machines virtuelles

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Vérifier que les disques de données sont chiffrés

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Le chiffrement de disque doit être appliqué sur les machines virtuelles

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Vérifier que les derniers correctifs de système d’exploitation sont appliqués pour toutes les machines virtuelles

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Des mises à jour système doivent être installées sur vos machines

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Vérifier que la protection de point de terminaison est installée pour toutes les machines virtuelles

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- Superviser les agents Endpoint Protection manquants dans Azure Security Center

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Activer le contrôle d’accès en fonction du rôle (RBAC) dans Azure Kubernetes Services

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- \[Préversion\] : Le contrôle d’accès en fonction du rôle (RBAC) doit être utilisé sans Kubernetes Services.

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Vérifier que l’application web redirige tout le trafic HTTP vers HTTPS dans Azure App Service

Ce blueprint attribue une définition [Azure Policy](../../../policy/overview.md) qui s’aligne sur cette recommandation CIS.

- L'application web ne doit pas être accessible via HTTPS

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez revu la correspondance des recommandations du blueprint CIS Microsoft Azure Foundations Benchmark, consultez l’article suivant pour en savoir plus sur le blueprint, ou visitez Azure Policy dans le portail Azure pour attribuer l’initiative :

> [!div class="nextstepaction"]
> [Vue d’ensemble de l’exemple de blueprint CIS Microsoft Azure Foundations Benchmark](./index.md)
> [Portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Autres articles sur les blueprints et la manière de les utiliser :

- Découvrir le [cycle de vie des blueprints](../../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).