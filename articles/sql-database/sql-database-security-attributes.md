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
ms.openlocfilehash: 1318b3e433224b009b76458b12e82c9bcf94bb7a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444389"
---
# <a name="security-attributes-for-azure-sql-database"></a>Attributs de sécurité pour Azure SQL Database

Cet article décrit les attributs de sécurité qui sont intégrés dans Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

SQL Database inclut une [base de données unique](sql-database-single-index.yml) et une [instance managée](sql-database-managed-instance.md). Les entrées suivantes s’appliquent aux deux offres, sauf mention contraire.

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement, comme Côté client ou Always Encrypted</ul>| OUI | Appelé « chiffrement en cours d’utilisation », comme décrit dans l’article [Always Encrypted](sql-database-always-encrypted.md). Le chiffrement côté serveur utilise le [chiffrement transparent des données](transparent-data-encryption-azure-sql.md).|
| Chiffrement en transit :<ul><li>Chiffrement Azure ExpressRoute</li><li>Chiffrement dans un réseau virtuel</li><li>Chiffrement entre réseaux virtuels</ul>| OUI | Utilisation du protocole HTTPS. |
| Gestion des clés de chiffrement, comme CMK ou BYOK| OUI | Une gestion de clé par le service et une gestion de clé par le client sont proposées. Cette dernière est proposée par [Azure Key Vault](../key-vault/index.yml). |
| Chiffrement au niveau des colonnes fourni par les services de données Azure| OUI | Via [Always Encrypted](sql-database-always-encrypted.md). |
| Appels d’API chiffrés| OUI | À l’aide de HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI | S’applique exclusivement à une [base de données unique](sql-database-single-index.yml). |
| Prise en charge de l’injection par Azure Virtual Network| OUI | S’applique exclusivement à une [instance managée](sql-database-managed-instance.md). |
| Prise en charge de l’isolement et du pare-feu réseau| OUI | Pare-feu au niveau de la base de données et du serveur. L’isolement réseau est destiné seulement à une [instance managée](sql-database-managed-instance.md). |
| Prise en charge du tunneling forcé| OUI | [Instance managée](sql-database-managed-instance.md) via un VPN [Azure ExpressRoute](../expressroute/index.yml). |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure, comme Log Analytics ou Application Insights| OUI | SecureSphere, la solution SIEM d’Imperva, est également prise en charge à travers l’intégration d’[Azure Event Hubs](../event-hubs/index.yml) via l’[audit SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Azure Active Directory (Azure AD) |
| Authorization| OUI | Aucun |

## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de contrôle et de gestion| OUI | Oui, seulement pour certains événements |
| Journalisation et audit du plan de données | OUI | Via l’[audit SQL](sql-database-auditing.md). |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion des configurations, comme la gestion des versions des configurations| Non  | Aucun |

## <a name="additional-security-attributes-for-sql-database"></a>Attributs de sécurité supplémentaires pour SQL Database

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Préventif : évaluation des vulnérabilités | OUI | Voir [Le service d’évaluation des vulnérabilités SQL vous permet d’identifier des vulnérabilités de base de données](sql-vulnerability-assessment.md). |
| Préventif : découverte et classification des données  | OUI | Voir [Découverte et classification des données pour Azure SQL Database et SQL Data Warehouse](sql-database-data-discovery-and-classification.md) |
| Détection : détection des menaces | OUI | Voir [Advanced Threat Protection pour Azure SQL Database](sql-database-threat-detection-overview.md). |
