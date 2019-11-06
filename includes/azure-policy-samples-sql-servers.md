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
ms.openlocfilehash: dabadf3a4175947f09ba20b0e644f1d2b485ee38
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73590708"
---
### <a name="sql-servers"></a>Serveurs SQL

|  |  |
|---------|---------|
| [Auditer aucun administrateur Azure Active Directory](../articles/governance/policy/samples/audit-no-aad-admin.md) | Vérifier lorsqu’il n’existe aucun administrateur Azure Active Directory affecté au serveur SQL. |
| [Auditer le paramètre de détection des menaces au niveau du serveur](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Audite les stratégies d’alerte de sécurité de base de données SQL si ces stratégies ne sont pas définies sur un état spécifié. Spécifiez une valeur qui indique si la détection des menaces est activée ou désactivée.  |
| [Audit des paramètres d’audit de SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Effectue l’audit de SQL Server pour déterminer si les paramètres d’audit sont activés. |
| [Auditer le paramètre d’audit au niveau du serveur SQL Server](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Vérifie si les paramètres d’audit du serveur SQL Server ne correspondent pas à un paramétrage spécifié. Spécifiez une valeur qui indique si les paramètres d’audit doivent être activés ou désactivés. |