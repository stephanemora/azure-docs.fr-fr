---
title: Fichier Include
description: Fichier Include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 70128735aef64d273c63236a13b0ae28edb6077d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003644"
---
### <a name="sql-databases"></a>BASES DE DONNÉES SQL

|  |  |
|---------|---------|
| [Références SKU de base de données SQL autorisées](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Exige que les bases de données SQL utilisent une référence SKU approuvée. Spécifiez un tableau d’identifiants de référence SKU autorisés ou un tableau de noms de référence SKU autorisés. |
| [Auditer le paramètre de détection des menaces au niveau de la base de données](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | Audite les stratégies d’alerte de sécurité de base de données SQL si ces stratégies ne sont pas définies sur un état spécifié. Spécifiez une valeur qui indique si la détection des menaces est activée ou désactivée.  |
| [Audit du chiffrement de la base de données SQL](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Vérifie si la base de données SQL n’a pas de chiffrement transparent des données activé. |
| [Auditer le paramètre d’audit au niveau de la base de données SQL](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Vérifie si les paramètres d’audit des bases de données SQL ne correspondent pas à un paramétrage spécifié. Spécifiez une valeur qui indique si les paramètres d’audit doivent être activés ou désactivés.  |
| [Audit l’état du chiffrement transparent des données](../articles/governance/policy/samples/audit-trans-data-enc-status.md) | Audite le chiffrement transparent des données des bases de données SQL s’il n’est pas activé.  |