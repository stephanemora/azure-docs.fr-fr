---
title: Attributs de sécurité de base de données SQL Azure
description: Une liste de vérification des attributs de sécurité pour l’évaluation de base de données SQL Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: df1ffa07c9b813ee3da4952bbcc394f43c69b7ac
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204238"
---
# <a name="security-attributes-for-azure-sql-database"></a>Attributs de sécurité de base de données SQL Azure

Cet article décrit les attributs de sécurité courantes intégrées à Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Base de données SQL Azure comprend à la fois [base de données unique](sql-database-single-index.yml) et [instance managée](sql-database-managed-instance.md). Les entrées ci-dessous s’appliquent à ces deux offres, sauf mention contraire.

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Appelée « chiffrement en cours d’utilisation », comme décrit dans l’article [Always Encrypted](sql-database-always-encrypted.md). Utilise le chiffrement côté service [chiffrement transparent des données](transparent-data-encryption-azure-sql.md) (TDE).|
| Chiffrement en transit :<ul><li>Chiffrement d’ExpressRoute</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Utilisation du protocole HTTPS. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Oui | Gestion de clé à la fois géré par le service et gérée par le client sont proposées (ce dernier via [Azure Key Vault](../key-vault/index.yml). |
| Chiffrement au niveau des colonnes (Azure Data Services)| Oui | Via [Always Encrypted](sql-database-always-encrypted.md). |
| Appels d’API chiffrés| Oui | À l’aide de HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | S’applique aux [base de données unique](sql-database-single-index.yml) uniquement. |
| Prise en charge de l’injection de réseau virtuel| Oui | S’applique aux [instance managée](sql-database-managed-instance.md) uniquement. |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui | Pare-feu sur les deux de base de données - et au niveau du serveur ; isolement de réseau [instance managée](sql-database-managed-instance.md) uniquement |
| Prise en charge du tunneling forcé | Oui | [instance managée](sql-database-managed-instance.md) via [Azure ExpressRoute](../expressroute/index.yml) VPN |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | La solution SIEM tiers proposées par Imperva (securesphere et) est également pris en charge, via [Azure Event Hubs](../event-hubs/index.yml) intégration via [SQL audit](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Gestion des accès – Authentification| Oui | Azure Active Directory |
| Gestion des accès – Autorisation| Oui |  |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| Oui | Oui pour certains événements uniquement. |
| Journalisation et audit du plan de données | Oui | Via [SQL audit](sql-database-auditing.md). |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Non   | | 

## <a name="additional-security-attributes-for-sql-database"></a>Attributs de sécurité supplémentaires pour la base de données SQL

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Préventives : évaluation des vulnérabilités | Oui | Consultez [évaluation des vulnérabilités SQL service vous aide à identifier les vulnérabilités de base de données](sql-vulnerability-assessment.md). |
| Préventives : découverte de données et la classification  | Oui | Consultez [découverte de données Azure SQL Database et SQL Data Warehouse et classification](sql-database-data-discovery-and-classification.md). |
| Détection : détection des menaces | Oui | Consultez [Advanced Threat Protection pour Azure SQL Database](sql-database-threat-detection-overview.md). |
