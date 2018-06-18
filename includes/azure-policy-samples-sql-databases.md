---
title: Fichier Include
description: Fichier Include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 50a6388a95bce14bc9af7e0c9a5387e148f6fa73
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664492"
---
### <a name="sql-databases"></a>BASES DE DONNÉES SQL

|  |  |
|---------|---------|
| [Références SKU de base de données SQL autorisées](../articles/azure-policy/scripts/allowed-sql-db-skus.md) | Exige que les bases de données SQL utilisent une référence SKU approuvée. Spécifiez un tableau d’identifiants de référence SKU autorisés ou un tableau de noms de référence SKU autorisés. |
| [Auditer le paramètre de détection des menaces au niveau de la base de données](../articles/azure-policy/scripts/audit-db-threat-det-setting.md) | Audite les stratégies d’alerte de sécurité de base de données SQL si ces stratégies ne sont pas définies sur un état spécifié. Spécifiez une valeur qui indique si la détection des menaces est activée ou désactivée.  |
| [Audit du chiffrement de la base de données SQL](../articles/azure-policy/scripts/sql-database-encryption-audit.md) | Vérifie si la base de données SQL n’a pas de chiffrement transparent des données activé. |
| [Auditer le paramètre d’audit au niveau de la base de données SQL](../articles/azure-policy/scripts/audit-sql-db-audit-setting.md) | Vérifie si les paramètres d’audit des bases de données SQL ne correspondent pas à un paramétrage spécifié. Spécifiez une valeur qui indique si les paramètres d’audit doivent être activés ou désactivés.  |
| [Audit l’état du chiffrement transparent des données](../articles/azure-policy/scripts/audit-trans-data-enc-status.md) | Audite le chiffrement transparent des données des bases de données SQL s’il n’est pas activé.  |