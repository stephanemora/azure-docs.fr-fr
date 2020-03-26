---
title: Exemples de contrôles de blueprint CIS Microsoft Azure Foundations Benchmark
description: Correspondance des recommandations de l’exemple de blueprint CIS Microsoft Azure Foundations Benchmark avec Azure Policy.
ms.date: 11/04/2019
ms.topic: sample
ms.openlocfilehash: 34d38f34dcd4233706f9b4578bc2dc2a644e4c2c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74707420"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Exemple de blueprint CIS Microsoft Azure Foundations Benchmark : correspondance des recommandations

L’article suivant explique en détail comment l’exemple de blueprint CIS Microsoft Azure Foundations Benchmark d’Azure Blueprints correspond aux recommandations de CIS Microsoft Azure Foundations Benchmark. Pour plus d’informations sur les recommandations, consultez [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

Les correspondances suivantes sont relatives aux recommandations **CIS Microsoft Azure Foundations Benchmark v1.1.0**. Utilisez le volet de navigation de droite pour accéder directement à une correspondance de recommandation spécifique.
De nombreuses recommandations mises en correspondance sont implémentées avec une initiative [Azure Policy](../../../policy/overview.md). Pour examiner l’initiative complète, ouvrez **Stratégie** dans le portail Azure et sélectionnez la page **Définitions**. Ensuite, recherchez et sélectionnez l’initiative de stratégie intégrée **\[Préversion\] Auditer les recommandations CIS Microsoft Azure Foundations Benchmark v1.1.0 et déployer des extensions de machine virtuelle spécifiques pour prendre en charge les exigences d’audit**.

> [!IMPORTANT]
> Chaque contrôle ci-dessous est associé à une ou plusieurs définitions [Azure Policy](../../../policy/overview.md). Ces stratégies peuvent vous aider à [évaluer la conformité](../../../policy/how-to/get-compliance-data.md) avec le contrôle ; toutefois, il n’existe pas souvent de correspondance de 1:1 ou parfaite entre un contrôle et une ou plusieurs stratégies. Ainsi, la **conformité** dans Azure Policy fait uniquement référence aux stratégies elles-mêmes ; cela ne garantit pas que vous êtes entièrement conforme à toutes les exigences d’un contrôle. En outre, la norme de conformité comprend des contrôles qui ne sont traités par aucune définition Azure Policy pour l’instant. Par conséquent, la conformité dans Azure Policy n’est qu’une vue partielle de l’état de conformité global. Les associations entre les contrôles et les définitions Azure Policy pour cet exemple de blueprint de conformité peuvent changer au fil du temps. Pour afficher l’historique des changements, consultez l’[historique des validations GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Vérifier que l’authentification multifacteur est activée pour tous les utilisateurs privilégiés

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de superviser le moment où l’authentification multifacteur n’est pas activée sur les comptes Azure Active Directory privilégiés.

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement
- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Vérifier que l’authentification multifacteur est activée pour tous les utilisateurs non privilégiés

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de superviser le moment où l’authentification multifacteur n’est pas activée sur les comptes Azure Active Directory non privilégiés.

- L'authentification multifacteur doit être activée sur les comptes disposant d’autorisations de lecture de votre abonnement

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Vérifier qu’il n’y a aucun utilisateur invité

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de superviser les comptes invités qui doivent peut-être être supprimés.

- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 Vérifier qu’aucun rôle de propriétaire d’abonnement personnalisé n’est créé

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de superviser les rôles de propriétaire d’abonnement personnalisé qui sont éventuellement à supprimer.

- Les rôles de propriétaire d’abonnement personnalisé ne doivent pas exister

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Vérifier que le niveau tarifaire standard est sélectionné

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de superviser les réseaux et les machines virtuelles sur lesquels le niveau standard Security Center n’est pas activé.

 - Le niveau tarifaire standard Security Center doit être sélectionné

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 Vérifier que l’option « Provisionnement automatique de l’agent de supervision » est définie sur « Activé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que le provisionnement automatique de l’agent Log Analytics est activé.

- Le provisionnement automatique de l’agent de supervision Log Analytics doit être activé sur votre abonnement

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller les mises à jour système » n’est pas défini sur « Désactivé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les mises à jour système sont installées sur les machines virtuelles.

- Des mises à jour système doivent être installées sur vos machines

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller les vulnérabilités de système d’exploitation » n’est pas défini sur « Désactivé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de superviser les vulnérabilités des machines virtuelles non corrigées.

- Les vulnérabilités de la configuration de sécurité sur vos machines doivent être corrigées

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller Endpoint Protection » n’est pas défini sur « Désactivé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier qu’Endpoint Protection est activé sur les machines virtuelles.

- Superviser les agents Endpoint Protection manquants dans Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller le chiffrement des disques » n’est pas défini sur « Désactivé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les disques de machines virtuelles sont chiffrés.

- Le chiffrement de disque doit être appliqué sur les machines virtuelles

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 Vérifier que le paramètre de stratégie par défaut ASC « Superviser les groupes de sécurité réseau » n’est pas défini sur « Désactivé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de protéger les machines virtuelles accessibles sur Internet.

- Les règles de groupe de sécurité réseau pour les machines virtuelles accessibles sur Internet doivent être renforcées

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller le pare-feu d’applications web » n’est pas défini sur « Désactivé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de protéger les machines virtuelles qui exécutent des applications web.

- Les règles de groupe de sécurité réseau pour les applications web IaaS doivent être renforcées

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 Vérifier que le paramètre de stratégie par défaut ASC « Activer la supervision du pare-feu de nouvelle génération » n’est pas défini sur « Désactivé »

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de protéger les sous-réseaux et les machines virtuelles contre les menaces en restreignant l’accès. La stratégie Security Center référencée par cette recommandation CIS Microsoft Azure Foundations Benchmark a été remplacée par deux nouvelles recommandations. Les stratégies référencées ci-dessous traitent les nouvelles recommandations.

- Les sous-réseaux doivent être associés à un groupe de sécurité réseau
- Les machines virtuelles doivent être associées à un groupe de sécurité réseau

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Vérifier que le paramètre de stratégie par défaut d’ASC « Superviser l’évaluation des vulnérabilités » n’est pas défini sur « Désactivé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier la détection et la correction des vulnérabilités.

- Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 Vérifier que le paramètre de stratégie par défaut ASC « Superviser le chiffrement des objets blob de stockage » n’est pas défini sur « Désactivé »

Le chiffrement du Stockage Azure est activé pour tous les comptes de stockage nouveaux et existants et ne peut pas être désactivé. (Il s’agit d’une fonctionnalité Azure par défaut ; il n’y a aucune affectation de stratégie.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller l’accès réseau JIT » n’est pas défini sur « Désactivé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de contrôler l’accès aux machines virtuelles.

- Le contrôle d’accès réseau juste-à-temps doit être appliqué sur les machines virtuelles

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 Vérifier que le paramètre de stratégie par défaut ASC « Superviser la mise en liste verte des contrôles d’application adaptatifs » n’est pas défini sur « Désactivé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les contrôles d’application adaptatifs sont activés sur les machines virtuelles.

- Les ces contrôles d’application adaptatifs doit être activés sur les machines virtuelles

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 Vérifier que le paramètre de stratégie par défaut ASC « Superviser l’audit SQL » n’est pas défini sur « Désactivé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que l’audit SQL Server est activé.

- L’audit doit être activé sur les paramètres de sécurité des données avancés sur SQL Server

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Vérifier que le paramètre de stratégie par défaut d’ASC « Surveiller le chiffrement SQL » n’est pas défini sur « Désactivé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que Transparent Data Encryption est activé sur les bases de données SQL.

- La technologie Transparent Data Encryption doit être activée sur les bases de données SQL

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 Vérifier que l’option « E-mails de contact de sécurité » est définie

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les notifications de sécurité sont correctement activées

- Une adresse e-mail de contact de sécurité doit être fournie pour votre abonnement

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 Vérifier que l’option « Numéro de téléphone » de contact de sécurité est définie

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les notifications de sécurité sont correctement activées

- Un numéro de téléphone de contact de sécurité doit être fourni pour votre abonnement

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 Vérifier que l’option « Envoyer une notification par e-mail pour les alertes à gravité élevée » est définie sur « Activé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les notifications de sécurité sont correctement activées

- La notification par e-mail pour les alertes à gravité élevée doit être activée

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 Vérifier que l’option « Envoyer également un e-mail aux propriétaires de l’abonnement » est définie sur « Activé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les notifications de sécurité sont correctement activées

- La notification par e-mail au propriétaire de l’abonnement pour les alertes à gravité élevée doit être activée

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Vérifier que l’option « Transfert sécurisé requis » est définie sur « Activé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de superviser les comptes de stockage qui autorisent les connexions non sécurisées.

- La sécurisation du transfert vers des comptes de stockage doit être activée

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Vérifier que la règle d’accès réseau par défaut pour les comptes de stockage est définie sur Refuser

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de superviser les comptes de stockage qui autorisent un accès illimité.

- Auditer l'accès réseau non restreint aux comptes de stockage

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 Vérifier que l’option « Services Microsoft approuvés » est activée pour l’accès au compte de stockage

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de superviser les comptes de stockage qui n’autorisent pas l’accès à partir de services Microsoft approuvés.

- Les comptes de stockage doivent autoriser l’accès à partir des services Microsoft approuvés

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Vérifier que l’option « Audit » est définie sur « Activé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que l’audit SQL Server est activé. 

- L’audit doit être activé sur les paramètres de sécurité des données avancés sur SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Vérifier que « AuditActionGroups » dans la stratégie « Audit » pour un serveur SQL est correctement défini

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que l’audit SQL Server est correctement configuré.

- Les paramètres d’audit SQL doivent avoir des groupes d’actions configurés pour capturer les activités critiques.

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Vérifier que la période de conservation d’audit est supérieure à 90 jours

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les journaux SQL Server sont conservés pendant au moins 90 jours.

- Les serveurs SQL doivent être configurés avec une période de conservation d’audit supérieure à 90 jours.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Vérifier que l’option « Advanced Data Security » sur un serveur SQL Server est définie sur « Activé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier qu’Advanced Data Security est activé sur les serveurs SQL et les instances managées SQL.

- Advanced Data Security doit être activée sur vos instances managées SQL.
- Advanced Data Security doit être activé sur vos serveurs SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 Vérifier que l’option « Types de détection des menaces » est définie sur « Tous »

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de vérifier qu’Advanced Threat Protection est correctement configuré sur les serveurs SQL et les instances managées SQL.

- Les types Advanced Threat Protection doivent être définis sur « Tous » dans les paramètres Advanced Data Security du serveur SQL.
- Les types Advanced Threat Protection doivent être définis sur « Tous » dans les paramètres Advanced Data Security de l’instance gérée SQL.

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 Vérifier que l’option « Envoyer des alertes à » est définie

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les notifications Advanced Data Security sont correctement activées.

- Les paramètres Advanced Data Security pour l’instance gérée SQL doivent inclure une adresse e-mail pour la réception des alertes de sécurité.
- Les paramètres Advanced Data Security pour le serveur SQL doivent inclure une adresse e-mail pour la réception des alertes de sécurité.

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Vérifier que l’option « Envoyer un e-mail au service et aux coadministrateurs » est définie sur « Activé ».

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les notifications Advanced Data Security sont correctement activées.

- Les notifications par e-mail aux administrateurs et propriétaires d’abonnements doivent être activées dans les paramètres Advanced Data Security de l’instance managée SQL
- Les notifications par e-mail aux administrateurs et propriétaires d’abonnements doivent être activées dans les paramètres Advanced Data Security SQL Server

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Vérifier que l’administrateur Azure Active Directory est configuré

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier qu’un administrateur Azure Active Directory est provisionné pour les serveurs SQL.

- Un administrateur Azure Active Directory doit être approvisionné pour les serveurs SQL

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Vérifiez que l’option « Chiffrement des données » est définie sur « Activé » sur une base de données SQL

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que Transparent Data Encryption est activé sur les bases de données SQL.

- La technologie Transparent Data Encryption doit être activée sur les bases de données SQL

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Vérifier que le protecteur TDE du serveur SQL est chiffré avec BYOK (Bring Your Own Key)

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de vérifier que la protection Transparent Data Encryption pour les serveurs SQL et les instances managées SQL est chiffrée avec votre propre clé.

- Le protecteur TDE de l’instance gérée SQL doit être chiffré avec votre propre clé.
- Le protecteur TDE du serveur SQL doit être chiffré avec votre propre clé.

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 Vérifier que l’option « Appliquer une connexion SSL » est définie sur « ACTIVÉ » pour le serveur de base de données MySQL

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les serveurs de base de données MySQL appliquent les connexions SSL.

- L’application de la connexion SSL doit être activée pour les serveurs de base de données MySQL

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 Vérifier que le paramètre de serveur « log_checkpoints » a la valeur « ON » pour le serveur de base de données PostgreSQL

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les serveurs de bases de données PostgreSQL journalisent les points de contrôle.

- Les points de contrôle de journal doivent être activés pour les serveurs de bases de données PostgreSQL

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 Vérifier que l’option « Appliquer une connexion SSL » est définie sur « ACTIVÉ » pour le serveur de base de données PostgreSQL

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les serveurs de base de données PostgreSQL appliquent les connexions SSL.

- L’application de la connexion SSL doit être activée pour les serveurs de base de données PostgreSQL

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 Vérifier que le paramètre de serveur « log_connections » a la valeur « ON » pour le serveur de base de données PostgreSQL

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les serveurs de bases de données PostgreSQL journalisent les connexions.

- Les connexions de journal doivent être activées pour les serveurs de bases de données PostgreSQL

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 Vérifier que le paramètre de serveur « log_disconnections » a la valeur « ON » pour le serveur de base de données PostgreSQL

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les serveurs de bases de données PostgreSQL journalisent les déconnexions.

- Les déconnexions doivent être journalisées pour les serveurs de bases de données PostgreSQL.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 Vérifier que le paramètre de serveur « log_duration » a la valeur « ON » pour le serveur de base de données PostgreSQL

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les serveurs de bases de données PostgreSQL journalisent la durée des instructions exécutées.

- La durée de journal doit être activée pour les serveurs de bases de données PostgreSQL

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 Vérifier que le paramètre de serveur « connection_throttling » est défini sur « Activé » pour le serveur de base de données PostgreSQL

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet d’atténuer les attaques par force brute sur les serveurs de base de données PostgreSQL.

- La limitation de connexion doit être activée pour les serveurs de base de données PostgreSQL

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Vérifier que l’administrateur Azure Active Directory est configuré

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier qu’un administrateur Azure Active Directory est provisionné pour les serveurs SQL. CIS Microsoft Azure Foundations Benchmark comprend cette recommandation. Toutefois, il s’agit d’un doublon de la [recommandation 4.8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Un administrateur Azure Active Directory doit être approvisionné pour les serveurs SQL

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Vérifier l’existence d’un profil de journal

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier l’existence d’un profil de journal pour tous les abonnements Azure. 

- Les abonnements Azure doivent avoir un profil de journal pour le journal d’activité

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Vérifier que la conservation du journal d’activité est définie sur 365 jours ou plus

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les journaux d’activité sont conservés pendant au moins un an.

- Le journal d’activité doit être conservé pendant au moins un an

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Vérifier que le profil d’audit capture toutes les activités

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que le profil de journal est correctement configuré.

- Le profil de journal Azure Monitor doit collecter des journaux pour les catégories « écriture », « suppression » et « action »

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Vérifier que le profil de journal capture les journaux d’activité pour toutes les régions, notamment la région globale

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que le profil de journal est correctement configuré.

- Azure Monitor doit collecter les journaux d’activité dans toutes les régions

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Vérifier que la journalisation d’Azure KeyVault est définie sur « Activé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les journaux de diagnostic sont activés pour les coffres de clés.

- Les journaux de diagnostic dans Key Vault doivent être activés.

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Vérifier que Network Watcher est défini sur « Activé »

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que Network Watcher est activé pour toutes les régions où les ressources sont déployées. Cette stratégie requiert un tableau de paramètres qui spécifie toutes les régions applicables. La valeur par défaut de cette définition d’initiative de stratégie est « eastus ».

- Network Watcher doit être activé

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Vérifier que les disques du système d’exploitation sont chiffrés

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que le chiffrement de disque est activé sur les machines virtuelles.

- Le chiffrement de disque doit être appliqué sur les machines virtuelles

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Vérifier que les disques de données sont chiffrés

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que le chiffrement de disque est activé sur les machines virtuelles.

- Le chiffrement de disque doit être appliqué sur les machines virtuelles

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 Vérifier que les disques non attachés sont chiffrés

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les disques non attachés sont chiffrés.

- Les disques non attachés doivent être chiffrés

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 Vérifier que seules les extensions approuvées sont installées

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que seules les extensions de machine virtuelle approuvées sont installées. Cette stratégie requiert un tableau de paramètres qui spécifie toutes les extensions de machine virtuelle approuvées. Cette définition d’initiative de stratégie contient des valeurs par défaut suggérées que les clients doivent valider. 

 - Seules les extensions de machine virtuelle approuvées doivent être installées

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Vérifier que les derniers correctifs de système d’exploitation sont appliqués pour toutes les machines virtuelles

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les mises à jour système sont installées sur les machines virtuelles.

- Des mises à jour système doivent être installées sur vos machines

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Vérifier que la protection de point de terminaison est installée pour toutes les machines virtuelles

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier qu’Endpoint Protection est activé sur les machines virtuelles.

- Superviser les agents Endpoint Protection manquants dans Azure Security Center

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 Vérifier que le coffre de clés est récupérable

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les objets de coffre de clés sont récupérables en cas de suppression accidentelle.

- Les objets Key Vault doivent être récupérables

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Activer le contrôle d’accès en fonction du rôle (RBAC) dans Azure Kubernetes Services

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que le contrôle d’accès en fonction du rôle est utilisé pour gérer les autorisations dans les clusters de service Kubernetes

- \[Préversion\] : Le contrôle d’accès en fonction du rôle (RBAC) doit être utilisé sans Kubernetes Services.

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Vérifier que l’application web redirige tout le trafic HTTP vers HTTPS dans Azure App Service

Ce blueprint affecte une définition [Azure Policy](../../../policy/overview.md) qui vous permet de vérifier que les applications web sont accessibles uniquement via des connexions sécurisées.

- L'application web ne doit pas être accessible via HTTPS

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 Vérifier que l’application web utilise la dernière version du chiffrement TLS

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de vérifier que les applications web utilisent la dernière version de TLS.

- La dernière version de TLS doit être utilisée dans votre application API
- La dernière version de TLS doit être utilisée dans votre application de fonction
- La dernière version de TLS doit être utilisée dans votre application web

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 Vérifier que « Certificats clients (certificats clients entrants) » est activé pour l’application web

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de vérifier que seuls les clients ayant des certificats valides peuvent accéder à une application web.

- Vérifier que « Certificats clients (certificats clients entrants) » est activé pour l’application API
- Vérifier que « Certificats clients (certificats clients entrants) » est activé pour l’application de fonction
- Vérifier que « Certificats clients (certificats clients entrants) » est activé pour l’application web

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 Vérifier que l’inscription auprès d’Azure Active Directory est activée pour App Service

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de vérifier que les applications web utilisent une identité managée.

- Vérifier que l’inscription auprès d’Azure Active Directory est activée pour l’application API
- Vérifier que l’inscription auprès d’Azure Active Directory est activée pour l’application de fonction
- Vérifier que l’inscription auprès d’Azure Active Directory est activée pour l’application web

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 Vérifier que la version du .Net Framework est la plus récente, si elle est utilisée dans le cadre de l’application web

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de vérifier que les applications web utilisent la dernière version du .Net Framework.

- Vérifier que la version du .Net Framework est la plus récente, si elle est utilisée dans le cadre de l’application API
- Vérifier que la version du .Net Framework est la plus récente, si elle est utilisée dans le cadre de l’application de fonction
- Vérifier que la version du .Net Framework est la plus récente, si elle est utilisée dans le cadre de l’application web

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 Vérifier que la version de PHP est la plus récente, si elle est utilisée pour exécuter l’application web

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de vérifier que les applications web utilisent la dernière version de PHP.

- Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application API
- Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application de fonction
- Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application web

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 Vérifier que la version de Python est la plus récente, si elle est utilisée pour exécuter l’application web

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de vérifier que les applications web utilisent la dernière version de Python.

- Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application API
- Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application de fonction
- Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application web

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 Vérifier que la version de Java est la plus récente, si elle est utilisée pour exécuter l’application web

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de vérifier que les applications web utilisent la dernière version de Java.

- Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application API
- Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application de fonction
- Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application web

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application web

Ce blueprint affecte des définitions [Azure Policy](../../../policy/overview.md) qui vous permettent de vérifier que les applications web utilisent la dernière version de HTTP.

- Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application API
- Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application de fonction
- Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application web


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez revu la correspondance des recommandations du blueprint CIS Microsoft Azure Foundations Benchmark, consultez les articles suivants pour en savoir plus sur le blueprint, ou visitez Azure Policy dans le portail Azure pour attribuer l'initiative :

> [!div class="nextstepaction"]
> [Blueprint CIS Microsoft Azure Foundations Benchmark - Vue d'ensemble](./index.md)
> [Blueprint CIS Microsoft Azure Foundations Benchmark - Étapes du déploiement](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).