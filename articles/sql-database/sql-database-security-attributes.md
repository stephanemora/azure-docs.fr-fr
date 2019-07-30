---
title: Attributs de sécurité pour Azure SQL Database
description: Liste de contrôle des attributs de sécurité pour l’évaluation d’Azure SQL Database
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c495456a5a3295abe5460ff6b5586e41fab2d95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001035"
---
# <a name="security-attributes-for-azure-sql-database"></a>Attributs de sécurité pour Azure SQL Database

Cet article décrit les attributs de sécurité courants intégrés dans Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Azure SQL Database inclut une [base de données unique](sql-database-single-index.yml) et une [instance gérée](sql-database-managed-instance.md). Les entrées ci-dessous s’appliquent aux deux offres, sauf mention contraire.

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Appelé « chiffrement en cours d’utilisation », comme décrit dans l’article [Always Encrypted](sql-database-always-encrypted.md). Le chiffrement côté service est du type [transparent data encryption](transparent-data-encryption-azure-sql.md) (TDE).|
| Chiffrement en transit :<ul><li>Chiffrement ExpressRoute</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Utilisation du protocole HTTPS. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Oui | Une gestion de clé par le service et une gestion de clé par le client sont proposées (cette dernière via [Azure Key Vault](../key-vault/index.yml)). |
| Chiffrement au niveau des colonnes (Azure Data Services)| Oui | Via [Always Encrypted](sql-database-always-encrypted.md). |
| Appels d’API chiffrés| Oui | À l’aide de HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui | S’applique exclusivement à une [base de données unique](sql-database-single-index.yml). |
| Prise en charge de l’injection de réseau virtuel| Oui | S’applique exclusivement à une [instance gérée](sql-database-managed-instance.md). |
| Prise en charge de l’isolement et du pare-feu réseau| Oui | Pare-feu aux niveaux base de données et serveur ; isolement réseau pour [instance gérée](sql-database-managed-instance.md) uniquement |
| Prise en charge du tunneling forcé| Oui | [instance gérée](sql-database-managed-instance.md) via un VPN [Azure ExpressRoute](../expressroute/index.yml) |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Oui | La solution SIEM tierce d’Imperva (SecureSphere) est également prise en charge avec l’intégration d’[Azure Event Hubs](../event-hubs/index.yml) via un [audit SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | Azure Active Directory |
| Authorization| Oui |  |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle| Oui | Oui pour certains événements uniquement. |
| Journalisation et audit du plan de données | Oui | Via un [audit SQL](sql-database-auditing.md). |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Non  | | 

## <a name="additional-security-attributes-for-sql-database"></a>Attributs de sécurité supplémentaires pour SQL Database

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Préventif : évaluation des vulnérabilités | OUI | Voir [Le service d’évaluation des vulnérabilités SQL vous permet d’identifier des vulnérabilités de base de données](sql-vulnerability-assessment.md). |
| Préventif : découverte et classification des données  | Oui | Voir [Découverte et classification des données pour Azure SQL Database et SQL Data Warehouse](sql-database-data-discovery-and-classification.md) |
| Détection : détection des menaces | Oui | Voir [Advanced Threat Protection pour Azure SQL Database](sql-database-threat-detection-overview.md). |
