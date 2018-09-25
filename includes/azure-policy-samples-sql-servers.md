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
ms.openlocfilehash: 76e161be1adca4aa2ec7b682a13b0a42e4b79412
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003632"
---
### <a name="sql-servers"></a>Serveurs SQL

|  |  |
|---------|---------|
| [Auditer aucun administrateur Azure Active Directory](../articles/governance/policy/samples/audit-no-aad-admin.md) | Vérifier lorsqu’il n’existe aucun administrateur Azure Active Directory affecté au serveur SQL. |
| [Auditer le paramètre de détection des menaces au niveau du serveur](../articles/governance/policy/samples/audit-sql-ser-threat-det-setting.md) | Audite les stratégies d’alerte de sécurité de base de données SQL si ces stratégies ne sont pas définies sur un état spécifié. Spécifiez une valeur qui indique si la détection des menaces est activée ou désactivée.  |
| [Audit des paramètres d’audit de SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Effectue l’audit de SQL Server pour déterminer si les paramètres d’audit sont activés. |
| [Auditer le paramètre d’audit au niveau du serveur SQL Server](../articles/governance/policy/samples/audit-sql-ser-leve-audit-setting.md) | Vérifie si les paramètres d’audit du serveur SQL Server ne correspondent pas à un paramétrage spécifié. Spécifiez une valeur qui indique si les paramètres d’audit doivent être activés ou désactivés. |
| [Exiger SQL Server version 12.0](../articles/governance/policy/samples/req-sql-12.md) | Oblige les serveurs SQL Server à utiliser la version 12.0.  |