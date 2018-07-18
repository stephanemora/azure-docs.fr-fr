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
ms.openlocfilehash: 298b92205e73b3623db02fb1dd803edac8379700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664486"
---
### <a name="sql-servers"></a>Serveurs SQL

|  |  |
|---------|---------|
| [Auditer aucun administrateur Azure Active Directory](../articles/azure-policy/scripts/audit-no-aad-admin.md) | Vérifier lorsqu’il n’existe aucun administrateur Azure Active Directory affecté au serveur SQL. |
| [Auditer le paramètre de détection des menaces au niveau du serveur](../articles/azure-policy/scripts/audit-sql-ser-threat-det-setting.md) | Audite les stratégies d’alerte de sécurité de base de données SQL si ces stratégies ne sont pas définies sur un état spécifié. Spécifiez une valeur qui indique si la détection des menaces est activée ou désactivée.  |
| [Audit des paramètres d’audit de SQL Server](../articles/azure-policy/scripts/sql-server-audit.md) | Effectue l’audit de SQL Server pour déterminer si les paramètres d’audit sont activés. |
| [Auditer le paramètre d’audit au niveau du serveur SQL Server](../articles/azure-policy/scripts/audit-sql-ser-leve-audit-setting.md) | Vérifie si les paramètres d’audit du serveur SQL Server ne correspondent pas à un paramétrage spécifié. Spécifiez une valeur qui indique si les paramètres d’audit doivent être activés ou désactivés. |
| [Exiger SQL Server version 12.0](../articles/azure-policy/scripts/req-sql-12.md) | Oblige les serveurs SQL Server à utiliser la version 12.0.  |