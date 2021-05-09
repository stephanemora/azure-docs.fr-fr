---
title: Détails de la conformité réglementaire pour CIS Microsoft Azure Foundations Benchmark 1.3.0
description: Détails de l’initiative intégrée de conformité réglementaire pour CIS Microsoft Azure Foundations Benchmark 1.3.0. Chaque contrôle est mis en correspondance avec une ou plusieurs définitions Azure Policy qui simplifient l’évaluation.
ms.date: 04/28/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: ba1b3a87e45433780df827390e15ee1001949e33
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108182009"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-130-regulatory-compliance-built-in-initiative"></a>Détails de l’initiative intégrée de conformité réglementaire pour CIS Microsoft Azure Foundations Benchmark 1.3.0

L’article suivant explique en détail comment la définition de l’initiative intégrée de conformité réglementaire pour Azure Policy est mappée à des **domaines de conformité** et des **contrôles** dans CIS Microsoft Azure Foundations Benchmark 1.3.0.
Pour plus d’informations sur ce standard de conformité, consultez [CIS Microsoft Azure Foundations Benchmark 1.3.0](https://www.cisecurity.org/benchmark/azure/). Pour comprendre la _propriété_, consultez [Définition de stratégie Azure Policy](../concepts/definition-structure.md#type) et [Responsabilité partagée dans le cloud](../../../security/fundamentals/shared-responsibility.md).

Les mappages suivants sont relatifs aux contrôles **CIS Microsoft Azure Foundations Benchmark 1.3.0**. Utilisez le volet de navigation de droite pour accéder directement à un **domaine de conformité** spécifique. De nombreux contrôles sont mis en œuvre avec la définition d’une initiative [Azure Policy](../overview.md). Pour examiner la définition d’initiative complète, ouvrez **Stratégie** dans le Portail Azure et sélectionnez la page **Définitions**.
Recherchez et sélectionnez ensuite la définition de l’initiative intégrée de conformité réglementaire pour **CIS Microsoft Azure Foundations Benchmark v1.3.0**.

> [!IMPORTANT]
> Chaque contrôle ci-dessous est associé à une ou plusieurs définitions [Azure Policy](../overview.md).
> Ces stratégies peuvent vous aider à [évaluer la conformité](../how-to/get-compliance-data.md) avec le contrôle ; toutefois, il n’existe pas souvent de correspondance un-à-un ou parfaite entre un contrôle et une ou plusieurs stratégies. Ainsi, la **conformité** dans Azure Policy fait uniquement référence aux définitions de stratégie elles-mêmes ; cela ne garantit pas que vous êtes entièrement conforme à toutes les exigences d’un contrôle. En outre, la norme de conformité comprend des contrôles qui ne sont traités par aucune définition Azure Policy pour l’instant. Par conséquent, la conformité dans Azure Policy n’est qu’une vue partielle de l’état de conformité global. Les associations entre les domaines de conformité, les contrôles et les définitions Azure Policy pour cette norme de conformité peuvent changer au fil du temps. Pour afficher l’historique des changements, consultez l’[historique des validations GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_3_0.json).

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Vérifier que l’authentification multifacteur est activée pour tous les utilisateurs privilégiés

**ID** : **Propriété** CIS Azure 1.1 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |MFA (Multi-Factor Authentication) doit être activé pour tous les comptes de l'abonnement disposant de privilèges d'accès en écriture pour éviter une violation des comptes ou des ressources. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[L’authentification multifacteur (MFA) doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |MFA (Multi-Factor Authentication) doit être activé pour tous les comptes de l'abonnement disposant d'autorisations de propriétaire afin d'éviter une violation des comptes ou des ressources. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Vérifier que l’authentification multifacteur est activée pour tous les utilisateurs non privilégiés

**ID** : **Propriété** CIS Azure 1.2 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’authentification multifacteur (MFA) doit être activée sur les comptes disposant d’autorisations de lecture sur votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |MFA (Multi-Factor Authentication) doit être activé pour tous les comptes de l'abonnement disposant de privilèges d'accès en lecture afin d'éviter une violation des comptes ou des ressources. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-guest-users-are-reviewed-on-a-monthly-basis"></a>Vérifier que les utilisateurs invités sont passés en revue tous les mois

**ID** : **Propriété** CIS Azure 1.3 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Les comptes externes avec des autorisations de type propriétaire doivent être supprimés de votre abonnement pour empêcher un accès non contrôlé. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Les comptes externes avec des privilèges d'accès en lecture doivent être supprimés de votre abonnement afin d'empêcher un accès non contrôlé. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Les comptes externes avec des privilèges d'accès en écriture doivent être supprimés de votre abonnement afin d'empêcher un accès non contrôlé. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Vérifier qu’aucun rôle de propriétaire d’abonnement personnalisé n’est créé

**ID** : CIS Azure 1.21 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les rôles de propriétaire d’abonnement personnalisé ne doivent pas exister](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Cette stratégie garantit qu’il n’existe aucun rôle de propriétaire d’abonnement personnalisé. |Audit, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-azure-defender-is-set-to-on-for-servers"></a>Vérifier qu’Azure Defender est activé pour les serveurs

**ID** : **Propriété** CIS Azure 2.1 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender pour les serveurs doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |Azure Defender pour les serveurs fournit une protection en temps réel contre les menaces pour les charges de travail des serveurs, et génère des recommandations de durcissement, ainsi que des alertes sur les activités suspectes. |AuditIfNotExists, Désactivé |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-app-service"></a>Vérifier qu’Azure Defender est activé pour App Service

**ID** : **Propriété** CIS Azure 2.2 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender pour App Service doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender pour App Service tire parti de l’envergure du cloud et de la visibilité dont Azure bénéficie en tant que fournisseur de cloud pour superviser les attaques d’applications web courantes. |AuditIfNotExists, Désactivé |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-azure-sql-database-servers"></a>Vérifier qu’Azure Defender est activé pour les serveurs Azure SQL Database

**ID** : **Propriété** CIS Azure 2.3 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender pour les serveurs Azure SQL Database doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender pour SQL offre des fonctionnalités permettant de mettre au jour et d’atténuer les vulnérabilités potentielles des bases de données SQL, de détecter les activités anormales susceptibles d’indiquer des menaces les ciblant, et de découvrir et de classer les données sensibles. |AuditIfNotExists, Désactivé |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-sql-servers-on-machines"></a>Vérifier qu’Azure Defender est activé pour les serveurs SQL Server sur les machines

**ID** : **Propriété** CIS Azure 2.4 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender pour les serveurs SQL sur les machines doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender pour SQL offre des fonctionnalités permettant de mettre au jour et d’atténuer les vulnérabilités potentielles des bases de données SQL, de détecter les activités anormales susceptibles d’indiquer des menaces les ciblant, et de découvrir et de classer les données sensibles. |AuditIfNotExists, Désactivé |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-storage"></a>Vérifier qu’Azure Defender est activé pour Stockage

**ID** : **Propriété** CIS Azure 2.5 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender pour le stockage doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender pour le stockage assure une détection des tentatives inhabituelles et potentiellement dangereuses d’accès ou d’exploitation des comptes de stockage. |AuditIfNotExists, Désactivé |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-kubernetes"></a>Vérifier qu’Azure Defender est activé pour Kubernetes

**ID** : **Propriété** CIS Azure 2.6 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender pour Kubernetes doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender pour Kubernetes fournit une protection contre les menaces en temps réel pour les environnements conteneurisés, et génère des alertes en cas d’activités suspectes. |AuditIfNotExists, Désactivé |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-container-registries"></a>Vérifier qu’Azure Defender est activé pour les registres de conteneurs

**ID** : **Propriété** CIS Azure 2.7 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender pour les registres de conteneurs doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |Azure Defender pour les registres de conteneurs fournit une analyse des vulnérabilités de toutes les images extraites au cours des 30 derniers jours, envoyées vers votre registre ou importées, et expose les résultats détaillés par image. |AuditIfNotExists, Désactivé |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-key-vault"></a>Vérifier qu’Azure Defender est activé pour Key Vault

**ID** : CIS Azure 2.8 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender pour Key Vault doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender pour Key Vault fournit une couche de protection supplémentaire et une veille de sécurité qui détecte les tentatives inhabituelles et potentiellement dangereuses d’accès ou d’exploitation des comptes Key Vault. |AuditIfNotExists, Désactivé |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Vérifier que l’option « Provisionnement automatique de l’agent de supervision » est définie sur « On »

**ID** : CIS Azure 2.11 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le provisionnement automatique de l’agent Log Analytics doit être activé sur votre abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Pour superviser les menaces et les vulnérabilités de sécurité, Azure Security Center collecte des données à partir de vos machines virtuelles Azure. Les données sont collectées par l’agent Log Analytics, auparavant appelé Microsoft Monitoring Agent (MMA). Cet agent lit divers journaux d’événements et configurations liés à la sécurité de la machine, puis copie les données dans votre espace de travail Log Analytics à des fins d’analyse. Nous vous recommandons d’activer l’approvisionnement automatique pour déployer automatiquement l’agent sur toutes les machines virtuelles Azure prises en charge et sur toutes celles qui sont créées. |AuditIfNotExists, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-additional-email-addresses-is-configured-with-a-security-contact-email"></a>Vérifier que les « adresses e-mail supplémentaires » sont configurées avec un e-mail de contact de sécurité

**ID** : **Propriété** CIS Azure 2.13 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les abonnements doivent avoir l’adresse e-mail d’un contact pour le signalement des problèmes de sécurité](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Pour informer les personnes concernées de votre organisation d’une violation de sécurité potentielle dans l’un de vos abonnements, définissez un contact de sécurité qui recevra des notifications par e-mail dans Security Center. |AuditIfNotExists, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-notify-about-alerts-with-the-following-severity-is-set-to-high"></a>Vérifier que « Notify about alerts with the following severity » (Envoyer une notification pour les alertes ayant la gravité suivante) a la valeur « High » (Élevée)

**ID** : **Propriété** CIS Azure 2.14 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La notification par e-mail pour les alertes à gravité élevée doit être activée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Pour informer les personnes concernées de votre organisation d’une violation de sécurité potentielle dans l’un de vos abonnements, activez les notifications par e-mail pour les alertes à gravité élevée dans Security Center. |AuditIfNotExists, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

## <a name="storage-accounts"></a>Comptes de stockage

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Vérifier que l’option « Transfert sécurisé requis » est définie sur « Enabled »

**ID** : **Propriété** CIS Azure 3.1 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La sécurisation du transfert vers des comptes de stockage doit être activée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Auditer l’exigence de transfert sécurisé dans votre compte de stockage. L’option de sécurisation du transfert oblige votre compte de stockage à accepter uniquement des requêtes provenant de connexions sécurisées (HTTPS). L’utilisation de HTTPS garantit l’authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l’intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session) |Audit, Refuser, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Vérifier que « Niveau d’accès public » est défini sur Privé pour les conteneurs d’objets blob

**ID** : CIS Azure 3.5 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’accès public au compte de stockage doit être interdit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |L’accès en lecture public anonyme aux conteneurs et aux blobs dans Stockage Azure est un moyen pratique de partager des données, mais peut présenter des risques pour la sécurité. Pour éviter les violations de données provoquées par un accès anonyme non souhaité, Microsoft recommande d’empêcher l’accès public à un compte de stockage, sauf si votre scénario l’exige. |audit, deny, disabled |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Vérifier que la règle d’accès réseau par défaut pour les comptes de stockage est définie sur Refuser

**ID** : CIS Azure 3.6 **Propriété** : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les comptes de stockage doivent limiter l’accès réseau](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |L’accès réseau aux comptes de stockage doit être limité. Configurez les règles du réseau de telle manière que seules les applications des réseaux autorisés puissent accéder au compte de stockage. Pour autoriser les connexions de clients Internet ou locaux spécifiques, l’accès au trafic peut être autorisé à partir de réseaux virtuels Azure spécifiques ou vers des plages d’adresses IP Internet publiques. |Audit, Refuser, Désactivé |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[Les comptes de stockage doivent utiliser des règles de réseau virtuel pour restreindre l’accès au réseau](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |Protégez vos comptes de stockage contre les menaces potentielles en utilisant des règles de réseau virtuel comme méthode préférée au lieu du filtrage basé sur IP. La désactivation du filtrage basé sur IP empêche les IP publiques d’accéder à vos comptes de stockage. |Audit, Refuser, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Vérifier que l’option « Services Microsoft approuvés » est activée pour l’accès au compte de stockage

**ID** : **Propriété** CIS Azure 3.7 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les comptes de stockage doivent autoriser l’accès à partir des services Microsoft approuvés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Certains services Microsoft qui interagissent avec les comptes de stockage fonctionnent à partir de réseaux qui ne peuvent pas obtenir l’accès par le biais des règles de réseau. Pour que ce type de service fonctionne comme prévu, autorisez l’ensemble des services Microsoft approuvés à contourner les règles de réseau. Ces services utilisent alors une authentification forte pour accéder au compte de stockage. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

### <a name="ensure-storage-for-critical-data-are-encrypted-with-customer-managed-key"></a>Vérifier que le stockage des données critiques est chiffré avec la clé gérée par le client

**ID** : CIS Azure 3.9 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les comptes de stockage doivent utiliser une clé gérée par le client pour le chiffrement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |Sécurisez votre compte de stockage avec une plus grande flexibilité en utilisant des clés gérées par le client. Quand vous spécifiez une clé gérée par le client, cette clé est utilisée pour protéger et contrôler l’accès à la clé qui chiffre vos données. L’utilisation de clés gérées par le client fournit des fonctionnalités supplémentaires permettant de contrôler la rotation de la clé de chiffrement de clé ou d’effacer des données par chiffrement. |Audit, Désactivé |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

## <a name="database-services"></a>Services de base de données

### <a name="ensure-that-auditing-is-set-to-on"></a>Vérifier que l’option « Audit » est définie sur « On »

**ID** : CIS Azure 4.1.1 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’audit sur SQL Server doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |L’audit sur votre serveur SQL Server doit être activé pour suivre les activités de toutes les bases de données du serveur et les enregistrer dans un journal d’audit. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Vérifiez que l’option « Chiffrement des données » est définie sur « On » sur une base de données SQL

**ID** : CIS Azure 4.1.2 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Transparent Data Encryption sur les bases de données SQL doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Le chiffrement transparent des données doit être activé pour protéger les données au repos et respecter les conditions de conformité requises |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Vérifier que la période de rétention d’audit est supérieure à 90 jours

**ID** : CIS Azure 4.1.3 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les serveurs SQL avec un audit dans la destination Compte de stockage doivent être configurés avec une conservation d'au moins 90 jours](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |À des fins d’investigation d’incident, nous vous recommandons de définir la conservation des données pour la fonctionnalité d’audit de votre serveur SQL Server sur au moins 90 jours. Confirmez que vous respectez les règles de conservation nécessaires pour les régions dans lesquelles vous travaillez. Cela est parfois nécessaire pour la conformité aux normes réglementaires. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-threat-protection-atp-on-a-sql-server-is-set-to-enabled"></a>Vérifier qu’Advanced Threat Protection (ATP) sur un serveur SQL Server a la valeur « Enabled » (Activé)

**ID** : CIS Azure 4.2.1 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Advanced Data Security doit être activé sur SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Auditez chaque instance managée SQL sans Advanced Data Security. |AuditIfNotExists, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Advanced Data Security doit être activé sur vos serveurs SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditer les serveurs SQL sans Advanced Data Security |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-vulnerability-assessment-va-is-enabled-on-a-sql-server-by-setting-a-storage-account"></a>Vérifier que la fonctionnalité Évaluation des vulnérabilités est activée sur un serveur SQL Server via la définition d’un compte de stockage

**ID** : CIS Azure 4.2.2 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’évaluation des vulnérabilités doit être activée sur SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Auditez chaque instance managée SQL qui n’a pas d’analyses récurrentes d’évaluation des vulnérabilités activées. L’évaluation des vulnérabilités peut découvrir et suivre les potentielles vulnérabilités de base de données, et vous aider à y remédier. |AuditIfNotExists, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[L’évaluation des vulnérabilités doit être activée sur vos serveurs SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Auditez les serveurs Azure SQL qui n’ont pas d’analyses récurrentes d’évaluation des vulnérabilités activées. L’évaluation des vulnérabilités peut découvrir et suivre les potentielles vulnérabilités de base de données, et vous aider à y remédier. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="ensure-that-va-setting-send-scan-reports-to-is-configured-for-a-sql-server"></a>Vérifier que le paramètre d’évaluation des vulnérabilités Envoyer les rapports d’analyse à est configuré pour un serveur SQL Server

**ID** : CIS Azure 4.2.4 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les paramètres d’évaluation des vulnérabilités de SQL Server doivent contenir une adresse e-mail pour recevoir les rapports d’analyse](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |Vérifiez qu’une adresse e-mail est fournie pour le champ « Envoyer les rapports d’analyse à » dans les paramètres d’évaluation des vulnérabilités. Cette adresse e-mail reçoit le récapitulatif des résultats d’analyse après l’exécution d’une analyse périodique sur les serveurs SQL. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Vérifier que l’option « Appliquer une connexion SSL » est définie sur « ENABLED » pour le serveur de base de données PostgreSQL

**ID** : CIS Azure 4.3.1 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’application de la connexion SSL doit être activée pour les serveurs de base de données MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |La base de données Azure pour MySQL prend en charge la connexion de votre serveur Azure Database pour MySQL aux applications clientes à l’aide de Secure Sockets Layer (SSL). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application. Cette configuration garantit que le protocole SSL est toujours activé pour l’accès à votre serveur de base de données. |Audit, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Vérifier que l’option « Appliquer une connexion SSL » est définie sur « ENABLED » pour le serveur de base de données MySQL

**ID** : CIS Azure 4.3.2 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’application de la connexion SSL doit être activée pour les serveurs de base de données PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database pour PostgreSQL prend en charge la connexion de votre serveur Azure Database pour PostgreSQL aux applications clientes via SSL (Secure Sockets Layer). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application. Cette configuration garantit que le protocole SSL est toujours activé pour l’accès à votre serveur de base de données. |Audit, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Vérifier que le paramètre de serveur « log_checkpoints » est défini sur « ON » pour le serveur de base de données PostgreSQL

**ID** : CIS Azure 4.3.3 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les points de contrôle de journal doivent être activés pour les serveurs de bases de données PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Cette stratégie permet d’auditer toutes les bases de données PostgreSQL de votre environnement sans activer le paramètre log_checkpoints. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Vérifier que le paramètre de serveur « log_connections » est défini sur « ON » pour le serveur de base de données PostgreSQL

**ID** : CIS Azure 4.3.4 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les connexions de journal doivent être activées pour les serveurs de bases de données PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Cette stratégie permet d’auditer toutes les bases de données PostgreSQL de votre environnement sans activer le paramètre log_connections. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Vérifier que le paramètre de serveur « log_disconnections » est défini sur « ON » pour le serveur de base de données PostgreSQL

**ID** : CIS Azure 4.3.5 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les déconnexions doivent être journalisées pour les serveurs de bases de données PostgreSQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Cette stratégie permet d’auditer toutes les bases de données PostgreSQL de votre environnement sans activer le paramètre log_disconnections. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Vérifier que le paramètre de serveur « connection_throttling » est défini sur « ON » pour le serveur de base de données PostgreSQL

**ID** : CIS Azure 4.3.6 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La limitation de connexion doit être activée pour les serveurs de base de données PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Cette stratégie aide à auditer toutes les bases de données PostgreSQL de votre environnement sans activer la limitation de connexion. Ce paramètre active la limitation de connexion temporaire par adresse IP à la suite d’un trop grand nombre de connexions infructueuses avec des mots de passe non valides. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Vérifier que l’administrateur Azure Active Directory est configuré

**ID** : **Propriété** CIS Azure 4.4 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Un administrateur Azure Active Directory doit être approvisionné pour les serveurs SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Auditer l’approvisionnement d’un administrateur Azure Active Directory pour votre serveur SQL afin d’activer l’authentification Azure AD. L’authentification Azure AD permet une gestion simplifiée des autorisations et une gestion centralisée des utilisateurs de bases de données et d’autres services Microsoft |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-customer-managed-key"></a>Vérifier que le protecteur TDE du serveur SQL Server est chiffré avec la clé gérée par le client

**ID** : **Propriété** CIS Azure 4.5 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les instances managées SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |L’implémentation de TDE (Transparent Data Encryption) avec votre propre clé vous offre les avantages suivants : transparence et contrôle améliorés sur le protecteur TDE, sécurité renforcée avec un service externe HSM et promotion de la séparation des tâches. Cette recommandation s’applique aux organisations ayant une exigence de conformité associée. |AuditIfNotExists, Désactivé |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Les serveurs SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |L’implémentation de TDE (Transparent Data Encryption) avec votre propre clé vous offre les avantages suivants : transparence et contrôle améliorés sur le protecteur TDE, sécurité renforcée avec un service externe HSM et promotion de la séparation des tâches. Cette recommandation s’applique aux organisations ayant une exigence de conformité associée. |AuditIfNotExists, Désactivé |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="logging-and-monitoring"></a>Journalisation et supervision

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Vérifier que le conteneur de stockage qui stocke les journaux d’activité n’est pas accessible publiquement

**ID** : **Propriété** CIS Azure 5.1.3 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’accès public au compte de stockage doit être interdit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |L’accès en lecture public anonyme aux conteneurs et aux blobs dans Stockage Azure est un moyen pratique de partager des données, mais peut présenter des risques pour la sécurité. Pour éviter les violations de données provoquées par un accès anonyme non souhaité, Microsoft recommande d’empêcher l’accès public à un compte de stockage, sauf si votre scénario l’exige. |audit, deny, disabled |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Vérifier que le compte de stockage comprenant le conteneur des journaux d’activité est chiffré avec BYOK (Bring Your Own Key)

**ID** : **Propriété** CIS Azure 5.1.4 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le compte de stockage disposant du conteneur des journaux d’activité doit être chiffré avec BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Cette stratégie vérifie si le compte de stockage disposant du conteneur des journaux d’activité est chiffré avec BYOK. La stratégie fonctionne uniquement si le compte de stockage se trouve par défaut dans le même abonnement que les journaux d’activité. Vous trouverez des informations complémentaires sur le chiffrement Stockage Azure au repos ici [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok).  |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Vérifier que la journalisation d’Azure KeyVault est définie sur « Enabled »

**ID** : CIS Azure 5.1.5 **Propriété** : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les journaux de ressources dans Key Vault doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Vérifier l’existence de l’alerte de journal d’activité pour la création d’attribution de stratégie

**ID** : **Propriété** CIS Azure 5.2.1 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Une alerte de journal d’activité doit exister pour des opérations de stratégie spécifiques](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Cette stratégie audite toute opération de stratégie spécifique sans aucune alerte de journal d’activité configurée. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-policy-assignment"></a>Vérifier qu’il existe une alerte du journal d’activité pour Supprimer l’attribution de stratégie

**ID** : **Propriété** CIS Azure 5.1.2 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Une alerte de journal d’activité doit exister pour des opérations de stratégie spécifiques](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Cette stratégie audite toute opération de stratégie spécifique sans aucune alerte de journal d’activité configurée. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Vérifier l’existence d’une alerte de journal d’activité pour la création ou la mise à jour d’un groupe de sécurité réseau

**ID** : **Propriété** CIS Azure 5.2.3 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Cette stratégie audite des opérations d’administration spécifiques sans aucune alerte de journal d’activité configurée. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Vérifier l’existence d’une alerte de journal d’activité pour la suppression de groupe de sécurité réseau

**ID** : **Propriété** CIS Azure 5.2.4 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Cette stratégie audite des opérations d’administration spécifiques sans aucune alerte de journal d’activité configurée. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Vérifier l’existence d’une alerte de journal d’activité pour la création ou la mise à jour d’une règle de groupe de sécurité réseau

**ID** : **Propriété** CIS Azure 5.2.5 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Cette stratégie audite des opérations d’administration spécifiques sans aucune alerte de journal d’activité configurée. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Vérifier l’existence d’une alerte de journal d’activité pour la suppression d’une règle de groupe de sécurité réseau

**ID** : **Propriété** CIS Azure 5.2.6 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Cette stratégie audite des opérations d’administration spécifiques sans aucune alerte de journal d’activité configurée. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Vérifier l’existence d’une alerte de journal d’activité pour la création ou la mise à jour d’une solution de sécurité

**ID** : **Propriété** CIS Azure 5.2.7 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Une alerte de journal d’activité doit exister pour des opérations de sécurité spécifiques](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Cette stratégie audite des opérations de sécurité spécifiques sans aucune alerte de journal d’activité configurée. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Vérifier l’existence d’une alerte de journal d’activité pour la suppression d’une solution de sécurité

**ID** : **Propriété** CIS Azure 5.2.8 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Une alerte de journal d’activité doit exister pour des opérations de sécurité spécifiques](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Cette stratégie audite des opérations de sécurité spécifiques sans aucune alerte de journal d’activité configurée. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Vérifier l’existence d’une alerte de journal d’activité pour la création, la mise à jour ou la suppression d’une règle de pare-feu SQL Server

**ID** : **Propriété** CIS Azure 5.2.9 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Cette stratégie audite des opérations d’administration spécifiques sans aucune alerte de journal d’activité configurée. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-diagnostic-logs-are-enabled-for-all-services-which-support-it"></a>Vérifier que les journaux de diagnostic sont activés pour tous les services qui les prennent en charge.

**ID** : CIS Azure 5.3 **Propriété** : Client

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les journaux de diagnostic d’App Services doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Auditer l’activation des journaux de diagnostic dans l’application. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[Les journaux de ressources dans Azure Data Lake Store doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Les journaux de ressources dans Azure Stream Analytics doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Les journaux de ressources dans les comptes Batch doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Les journaux de ressources dans Data Lake Analytics doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Les journaux de ressources dans Event Hub doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[Les journaux de ressources dans IoT Hub doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Les journaux de ressources dans Key Vault doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Les journaux de ressources dans Logic Apps doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Les journaux de ressources dans les services Search doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Les journaux de ressources dans Service Bus doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Auditez l’activation des journaux de ressources. Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |AuditIfNotExists, Désactivé |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Les journaux de ressources dans Virtual Machine Scale Sets doivent être activés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Il est recommandé d’activer les journaux d’activité pour permettre le traçage de l’activité lors des enquêtes requises en cas d’incident ou de compromission. |AuditIfNotExists, Désactivé |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

## <a name="networking"></a>Mise en réseau

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Vérifier que l’accès RDP à partir d’Internet est limité

**ID** : **Propriété** CIS Azure 6.1 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’accès RDP à partir d’Internet doit être bloqué](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Cette stratégie audite toute règle de sécurité réseau autorisant l’accès RDP à partir d’Internet. |Audit, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Vérifier que l’accès SSH à partir d’Internet est limité

**ID** : **Propriété** CIS Azure 6.2 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’accès SSH à partir d’Internet doit être bloqué](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Cette stratégie audite toute règle de sécurité réseau autorisant l’accès SSH à partir d’Internet. |Audit, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Vérifier que Network Watcher est défini sur « Enabled »

**ID** : **Propriété** CIS Azure 6.5 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher doit être activé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher est un service régional qui vous permet de surveiller et de diagnostiquer l’état au niveau d’un scénario réseau dans, vers et depuis Azure. La surveillance au niveau des scénarios vous permet de diagnostiquer les problèmes avec une vue de bout en bout du réseau. Les outils de visualisation et de diagnostic réseau disponibles avec Network Watcher vous aident à comprendre, diagnostiquer et obtenir des informations sur votre réseau dans Azure. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-virtual-machines-are-utilizing-managed-disks"></a>Vérifier que les machines virtuelles utilisent des disques managés

**ID** : **Propriété** CIS Azure 7.1 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Faire l’audit des machines virtuelles n’utilisant aucun disque managé](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Cette stratégie fait l’audit des machines virtuelles n’utilisant pas de disque managé |audit |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |

### <a name="ensure-that-os-and-data-disks-are-encrypted-with-cmk"></a>Vérifier que les disques d’OS et de données sont chiffrés avec CMK

**ID** : **Propriété** CIS Azure 7.2 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le chiffrement de disque doit être appliqué sur les machines virtuelles](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Les machines virtuelles sans chiffrement de disque activé seront supervisées par Azure Security Center en tant que recommandations. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted-with-cmk"></a>Vérifier que les disques non attachés sont chiffrés avec CMK

**ID** : **Propriété** CIS Azure 7.3 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les disques non attachés doivent être chiffrés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Cette stratégie permet d’auditer tous les disques non attachés sans chiffrement activé. |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Vérifier que seules les extensions approuvées sont installées

**ID** : **Propriété** CIS Azure 7.4 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Seules les extensions de machine virtuelle approuvées doivent être installées](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Cette stratégie régit les extensions de machine virtuelle qui ne sont pas approuvées. |Audit, Refuser, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Vérifier que les derniers correctifs de système d’exploitation sont appliqués pour toutes les machines virtuelles

**ID** : **Propriété** CIS Azure 7.5 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les mises à jour système doivent être installées sur vos machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Les mises à jour système de sécurité manquantes sur vos serveurs seront supervisées par Azure Security Center en tant que recommandation |AuditIfNotExists, Désactivé |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Vérifier que la protection de point de terminaison est installée pour toutes les machines virtuelles

**ID** : **Propriété** CIS Azure 7.6 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Superviser les agents Endpoint Protection manquants dans Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Les serveurs sans agent Endpoint Protection installé seront supervisés par Azure Security Center en tant que recommandation |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Autres considérations liées à la sécurité

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Vérifier que la date d’expiration est définie sur toutes les clés

**ID** : CIS Azure 8.1 **Propriété** : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les clés Key Vault doivent avoir une date d’expiration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |Les clés de chiffrement doivent avoir une date d’expiration définie et ne pas être permanentes. Les clés valides indéfiniment offrent à un intrus potentiel plus de temps pour compromettre la clé. Il est recommandé de définir les dates d’expiration des clés de chiffrement. |Audit, Refuser, Désactivé |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Vérifier que la date d’expiration est définie sur tous les secrets

**ID** : CIS Azure 8.2 **Propriété** : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Les secrets Key Vault doivent avoir une date d’expiration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |Les secrets doivent avoir une date d’expiration définie et ne pas être permanents. Les secrets valides indéfiniment offrent à un attaquant potentiel plus de temps pour les compromettre. Il est recommandé de définir les dates d’expiration des secrets. |Audit, Refuser, Désactivé |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Vérifier que le coffre de clés est récupérable

**ID** : **Propriété** CIS Azure 8.4 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La protection contre la suppression définitive doit être activée sur les coffres de clés](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |La suppression malveillante d’un coffre de clés peut entraîner une perte définitive des données. Une personne malveillante interne à votre organisation peut potentiellement supprimer et vider des coffres de clés. La protection contre la suppression définitive vous protège des attaques internes en appliquant une période de conservation obligatoire pour les coffres de clés supprimés de manière réversible. Personne au sein de votre organisation ni chez Microsoft ne pourra supprimer définitivement vos coffres de clés pendant la période de conservation de la suppression réversible. |Audit, Refuser, Désactivé |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Activer le contrôle d’accès en fonction du rôle (RBAC) dans Azure Kubernetes Services

**ID** : **Propriété** CIS Azure 8.5 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le contrôle d’accès en fonction du rôle (RBAC) doit être utilisé sur les services Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Pour fournir un filtrage précis des actions que les utilisateurs peuvent effectuer, utilisez le contrôle d’accès en fonction du rôle (RBAC) pour gérer les autorisations dans les clusters Kubernetes Service et configurez les stratégies d’autorisation appropriées. |Audit, Désactivé |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="app-service"></a>App Service

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Vérifier qu’App Service Authentication est défini sur Azure App Service

**ID** : **Propriété** CIS Azure 9.1 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’authentification doit être activée sur votre application API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |L’authentification Azure App Service est une fonctionnalité qui peut empêcher les requêtes HTTP anonymes d’accéder à l’application API ou authentifier celles ayant des jetons avant qu’elles n’accèdent à l’application API. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[L’authentification doit être activée sur votre application de fonction](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |L’authentification Azure App Service est une fonctionnalité qui peut empêcher les requêtes HTTP anonymes d’accéder à l’application de fonction ou authentifier celles ayant des jetons avant qu’elles n’accèdent à l’application de fonction. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[L’authentification doit être activée sur votre application web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |L’authentification Azure App Service est une fonctionnalité qui peut empêcher les requêtes HTTP anonymes d’accéder à l’application web ou authentifier celles ayant des jetons avant qu’elles n’accèdent à l’application web. |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Vérifier que l’application web redirige tout le trafic HTTP vers HTTPS dans Azure App Service

**ID** : **Propriété** CIS Azure 9.2 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L’application web ne doit pas être accessible via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |L'utilisation de HTTPS garantit l'authentification du serveur/service, et protège les données en transit contre les attaques par écoute clandestine de la couche réseau. |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Vérifier que l’application web utilise la dernière version du chiffrement TLS

**ID** : **Propriété** CIS Azure 9.3 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La dernière version de TLS doit être utilisée dans votre application API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Mise à niveau vers la version la plus récente de TLS |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[La dernière version de TLS doit être utilisée dans votre application de fonction](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Mise à niveau vers la version la plus récente de TLS |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[La dernière version de TLS doit être utilisée dans votre application web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Mise à niveau vers la version la plus récente de TLS |AuditIfNotExists, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Vérifier que « Certificats clients (certificats clients entrants) » est activé pour l’application web

**ID** : **Propriété** CIS Azure 9.4 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Vérifier que « Certificats clients (certificats clients entrants) » est activé pour l’application API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Les certificats clients permettent à l’application de demander un certificat pour les demandes entrantes. Seuls les clients disposant d’un certificat valide peuvent accéder à l’application. |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Vérifier que « Certificats clients (certificats clients entrants) » est activé pour l’application web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Les certificats clients permettent à l’application de demander un certificat pour les demandes entrantes. Seuls les clients disposant d’un certificat valide peuvent accéder à l’application. |Audit, Désactivé |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[Les applications de fonction doivent avoir l’option « Certificats clients (certificats clients entrants) » activée](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Les certificats clients permettent à l’application de demander un certificat pour les demandes entrantes. Seuls les clients disposant de certificats valides peuvent accéder à l’application. |Audit, Désactivé |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Vérifier que l’inscription auprès d’Azure Active Directory est activée pour App Service

**ID** : **Propriété** CIS Azure 9.5 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Une identité managée doit être utilisée dans votre application API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Utiliser une identité managée pour renforcer la sécurité de l’authentification |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Une identité managée doit être utilisée dans votre application de fonction](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Utiliser une identité managée pour renforcer la sécurité de l’authentification |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Une identité managée doit être utilisée dans votre application web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Utiliser une identité managée pour renforcer la sécurité de l’authentification |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Vérifier que la version de PHP est la plus récente si elle est utilisée pour exécuter l’application web

**ID** : **Propriété** CIS Azure 9.6 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Régulièrement, de nouvelles versions sont publiées pour le logiciel PHP, soit en raison de failles de sécurité, soit pour inclure des fonctionnalités supplémentaires. Nous vous recommandons d’utiliser la version la plus récente de PHP pour les applications API afin de tirer parti des correctifs de sécurité, le cas échéant, et/ou des nouvelles fonctionnalités. Actuellement, cette stratégie s’applique uniquement aux applications web Linux. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Régulièrement, de nouvelles versions sont publiées pour le logiciel PHP, soit en raison de failles de sécurité, soit pour inclure des fonctionnalités supplémentaires. Nous vous recommandons d’utiliser la version la plus récente de PHP pour les applications web afin de tirer parti des correctifs de sécurité, le cas échéant, et/ou des nouvelles fonctionnalités. Actuellement, cette stratégie s’applique uniquement aux applications web Linux. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Vérifier que la version de Python est la plus récente si elle est utilisée pour exécuter l’application web

**ID** : **Propriété** CIS Azure 9.7 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Régulièrement, de nouvelles versions sont publiées pour le logiciel Python, soit en raison de failles de sécurité, soit pour inclure des fonctionnalités supplémentaires. Nous vous recommandons d’utiliser la version la plus récente de Python pour les applications API, afin de tirer parti des correctifs de sécurité, le cas échéant, et/ou des nouvelles fonctionnalités. Actuellement, cette stratégie s’applique uniquement aux applications web Linux. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application de fonction](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Régulièrement, de nouvelles versions sont publiées pour le logiciel Python, soit en raison de failles de sécurité, soit pour inclure des fonctionnalités supplémentaires. Nous vous recommandons d’utiliser la version la plus récente de Python pour les applications de fonction afin de tirer parti des correctifs de sécurité, le cas échéant, et/ou des nouvelles fonctionnalités. Actuellement, cette stratégie s’applique uniquement aux applications web Linux. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Régulièrement, de nouvelles versions sont publiées pour le logiciel Python, soit en raison de failles de sécurité, soit pour inclure des fonctionnalités supplémentaires. Nous vous recommandons d’utiliser la version la plus récente de Python pour les applications web afin de tirer parti des correctifs de sécurité, le cas échéant, et/ou des nouvelles fonctionnalités. Actuellement, cette stratégie s’applique uniquement aux applications web Linux. |AuditIfNotExists, Désactivé |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Vérifier que la version de Java est la plus récente si elle est utilisée pour exécuter l’application web

**ID** : **Propriété** CIS Azure 9.8 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Régulièrement, de nouvelles versions sont publiées pour Java, soit en raison de failles de sécurité, soit pour inclure des fonctionnalités supplémentaires. Nous vous recommandons d’utiliser la version la plus récente de Python pour les applications API, afin de tirer parti des correctifs de sécurité, le cas échéant, et/ou des nouvelles fonctionnalités. Actuellement, cette stratégie s’applique uniquement aux applications web Linux. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application de fonction](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Régulièrement, de nouvelles versions sont publiées pour le logiciel Java, soit en raison de failles de sécurité, soit pour inclure des fonctionnalités supplémentaires. Nous vous recommandons d’utiliser la version la plus récente de Java pour les applications de fonction afin de tirer parti des correctifs de sécurité, le cas échéant, et/ou des nouvelles fonctionnalités. Actuellement, cette stratégie s’applique uniquement aux applications web Linux. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Régulièrement, de nouvelles versions sont publiées pour le logiciel Java, soit en raison de failles de sécurité, soit pour inclure des fonctionnalités supplémentaires. L’utilisation de la version la plus récente de Java pour les applications web est recommandée pour tirer parti des correctifs de sécurité, le cas échéant, et/ou des nouvelles fonctionnalités. Actuellement, cette stratégie s’applique uniquement aux applications web Linux. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Vérifier que la version de HTTP est la plus récente si elle est utilisée pour exécuter l’application web

**ID** : **Propriété** CIS Azure 9.9 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Régulièrement, de nouvelles versions sont publiées pour HTTP, soit en raison de failles de sécurité, soit pour inclure des fonctionnalités supplémentaires. L’utilisation de la version de HTTP la plus récente est recommandée pour les applications web afin de tirer parti des correctifs de sécurité, le cas échéant, et/ou des nouvelles fonctionnalités. Actuellement, cette stratégie s’applique uniquement aux applications web Linux. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application de fonction](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Régulièrement, de nouvelles versions sont publiées pour HTTP, soit en raison de failles de sécurité, soit pour inclure des fonctionnalités supplémentaires. L’utilisation de la version de HTTP la plus récente est recommandée pour les applications web afin de tirer parti des correctifs de sécurité, le cas échéant, et/ou des nouvelles fonctionnalités. Actuellement, cette stratégie s’applique uniquement aux applications web Linux. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Régulièrement, de nouvelles versions sont publiées pour HTTP, soit en raison de failles de sécurité, soit pour inclure des fonctionnalités supplémentaires. L’utilisation de la version de HTTP la plus récente est recommandée pour les applications web afin de tirer parti des correctifs de sécurité, le cas échéant, et/ou des nouvelles fonctionnalités. Actuellement, cette stratégie s’applique uniquement aux applications web Linux. |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

### <a name="ensure-ftp-deployments-are-disabled"></a>Vérifier que les déploiements FTP sont désactivés

**ID** : **Propriété** CIS Azure 9.10 : Customer

|Nom<br /><sub>(Portail Azure)</sub> |Description |Effet(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[FTPS uniquement doit être exigé dans votre application API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |Activer la mise en œuvre de FTPS pour renforcer la sécurité |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[FTPS uniquement doit être exigé dans votre application de fonction](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |Activer la mise en œuvre de FTPS pour renforcer la sécurité |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[FTPS doit être exigé dans votre application web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |Activer la mise en œuvre de FTPS pour renforcer la sécurité |AuditIfNotExists, Désactivé |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |

> [!NOTE]
> La disponibilité des définitions Azure Policy peut varier dans Azure Government et dans d’autres clouds nationaux.

## <a name="next-steps"></a>Étapes suivantes

Autres articles sur Azure Policy :

- Présentation de la [Conformité réglementaire](../concepts/regulatory-compliance.md).
- Voir la [structure de la définition d’initiative](../concepts/initiative-definition-structure.md).
- Passez en revue d’autres exemples de la page [Exemples Azure Policy](./index.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
- Découvrez comment [corriger des ressources non conformes](../how-to/remediate-resources.md).
