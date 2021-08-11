---
title: Guide de migration de MySQL local vers Azure Database pour MySQL – Sécurité
description: Le passage à un service basé sur le cloud ne signifie pas que tout l’Internet y aura accès à tout moment.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 55548eb40e4ac031f1c4e4a3c3aaed3b98f51e1e
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970873"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-security"></a>Guide de migration de MySQL local vers Azure Database pour MySQL – Sécurité

Le passage à un service basé sur le cloud ne signifie pas que tout l’Internet y aura accès à tout moment. Azure offre une sécurité optimale pour garantir la protection continue des charges de travail de données contre les acteurs et les programmes malveillants.

### <a name="authentication"></a>Authentification

Azure Database pour MySQL prend en charge les mécanismes d’authentification de base pour la connectivité utilisateur MySQL, ainsi que l’[intégration à Azure Active Directory.](../concepts-azure-ad-authentication.md) Cette intégration de la sécurité fonctionne via l’émission de jetons qui font office de mots de passe lors du processus de connexion MySQL. [Configurer l’intégration Active Directory](../howto-configure-sign-in-azure-ad-authentication.md) est beaucoup simple à effectuer et prend en charge non seulement les utilisateurs, mais aussi les groupes AAD.

Cette intégration étroite permet aux administrateurs et aux applications de tirer parti des fonctionnalités de sécurité renforcées d’[Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) pour mieux faire apparaître les problèmes liés aux identités.

> [!NOTE] 
> Cette fonctionnalité de sécurité est prise en charge par MySQL 5.7 et ultérieur. La plupart des [pilotes d’application](../howto-configure-sign-in-azure-ad-authentication.md) sont pris en charge dès lors que l’option `clear-text` est fournie.

### <a name="threat-protection"></a>Protection contre les menaces

En cas de compromission des informations d’identification de l’utilisateur ou de l’application, les journaux ne vont probablement pas refléter les échecs des tentatives de connexion. Les informations d’identification compromises peuvent permettre à des acteurs malveillants d’accéder aux données et de les télécharger. [Azure Threat Protection](/azure/mysql/concepts-data-access-and-security-threat-protection) peut rechercher les anomalies dans les connexions (comme des emplacements inhabituels, des utilisateurs rares ou des attaques par force brute) et d’autres activités suspectes. Les administrateurs peuvent être avertis dans le cas où quelque chose ne paraît pas correct.

### <a name="audit-logging"></a>Journalisation d’audit

MySQL a une fonctionnalité de journal d’audit intégrée robuste. Par défaut, cette [fonctionnalité de journal d’audit est désactivée](../concepts-audit-logs.md) dans Azure Database pour MySQL. La journalisation au niveau du serveur peut être activée en modifiant le paramètre de serveur `audit\_log\_enabled`. Une fois activés, les journaux sont accessibles via [Azure Monitor](../../azure-monitor/overview.md) et [Log Analytics](../../azure-monitor/logs/design-logs-deployment.md) en activant la [journalisation des diagnostics.](../howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)

Pour interroger les événements liés aux connexions des utilisateurs, exécutez la requête KQL suivante :

```
AzureDiagnostics  
| where ResourceProvider =="MICROSOFT.DBFORMYSQL" 
| where Category == 'MySqlAuditLogs' and event\_class\_s == "connection\_log"  
| project TimeGenerated, LogicalServerName\_s, event\_class\_s, event\_subclass\_s  
, event\_time\_t, user\_s , ip\_s , sql\_text\_s  
| order by TimeGenerated asc
```

### <a name="encryption"></a>Chiffrement

Les données de l’instance MySQL sont chiffrées au repos par défaut. Les sauvegardes automatisées sont également chiffrées pour empêcher les fuites potentielles de données vers des tiers non autorisés. Ce chiffrement est généralement effectué avec une clé qui est créée lors de la création de l’instance. En plus de cette clé de chiffrement par défaut, les administrateurs ont la possibilité d’utiliser [BYOK (Bring Your Own Key).](../concepts-data-encryption-mysql.md)

Lors de l’utilisation d’une stratégie de clé gérée par le client, il est essentiel de comprendre les responsabilités liées à la gestion du cycle de vie des clés. Les clés des clients sont stockées dans un [coffre Azure Key Vault](../../key-vault/general/basic-concepts.md), puis leur accès est régi par des stratégies. Il est essentiel de suivre toutes les recommandations relatives à la gestion des clés, la perte de la clé de chiffrement équivalant à la perte de l’accès aux données.

En plus des clés gérées par le client, utilisez des clés au niveau du service pour [ajouter le chiffrement double.](../concepts-infrastructure-double-encryption.md) L’implémentation de cette fonctionnalité fournira des données fortement chiffrées au repos, mais elle pénalise les performances du chiffrement. Des tests doivent être effectués.

Les données peuvent être chiffrées pendant le transit en utilisant SSL/TLS. Comme expliqué précédemment, il peut être nécessaire de [modifier vos applications](../howto-configure-ssl.md) pour prendre en charge cette modification et aussi de configurer les paramètres de validation TLS appropriés.

### <a name="firewall"></a>Pare-feu

Une fois que les utilisateurs sont configurés et que les données sont chiffrées au repos, l’équipe de migration doit passer en revue les flux de données du réseau. Azure Database pour MySQL offre plusieurs mécanismes pour sécuriser les couches réseau en limitant l’accès aux seuls utilisateurs, applications et appareils autorisés.

La première ligne de défense pour la protection de l’instance MySQL consiste à implémenter des [règles de pare-feu.](../concepts-firewall-rules.md) Les adresses IP peuvent être limitées à des emplacements valides lors de l’accès à l’instance via des adresses IP internes ou externes. Si l’instance MySQL est destinée à desservir seulement des applications internes, [restreignez l’accès public. ](../howto-deny-public-network-access.md)

Lors du déplacement d’une application sur Azure avec la charge de travail MySQL, il est probable que plusieurs réseaux virtuels seront configurés dans un modèle hub-and-spoke, nécessitant la configuration de l’[appairage de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md).

### <a name="private-link"></a>Liaison privée

Pour limiter l’accès à Azure Database pour MySQL aux ressources Azure internes, activez [Private Link.](../concepts-data-access-security-private-link.md) Private Link va garantir qu’une adresse IP privée est affectée à l’instance MySQL, et non pas une adresse IP publique.

> [!NOTE]
> Il existe de nombreuses autres [considérations de base sur les réseaux Azure](../concepts-data-access-and-security-vnet.md) qui doivent être prises en compte et qui ne sont pas abordées dans ce guide.

Passez en revue un ensemble de tâches potentielles de [la base de référence de sécurité](/azure/mysql/security-baseline) qui peuvent être implémentées dans toutes les ressources Azure. Certains des éléments décrits sur le lien vers la référence ne s’appliquent pas aux charges de travail de données et ressources Azure spécifiques.

### <a name="security-checklist"></a>Liste de contrôle de sécurité

  - Utilisez l’authentification Azure AD là où c’est possible.

  - Activez Advanced Thread Protection.

  - Activez toutes les fonctionnalités d’audit.

  - Envisagez une stratégie BYOK (Bring Your Own Key).

  - Implémentez des règles de pare-feu.

  - Utilisez des points de terminaison privés pour les charges de travail qui ne transitent pas sur Internet.  


> [!div class="nextstepaction"]
> [Résumé](./summary.md)