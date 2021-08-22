---
title: Problèmes connus et limitations – Azure Database pour PostgreSQL – Serveur unique et serveur flexible (préversion)
description: Présente les problèmes connus que les clients doivent connaître.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: 0806ef1a191759e769271a97fb1809298cde10db
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531501"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database pour PostgreSQL – Problèmes connus et limitations

Cette page donne la liste des problèmes connus d’Azure Database pour PostgreSQL qui peuvent avoir un impact sur votre application. Elle présente également des mesures d’atténuation et des recommandations pour contourner le problème.

## <a name="intelligent-performance---query-store"></a>Performances intelligentes – Magasin des requêtes

Applicable à Azure Database pour PostgreSQL – Serveur unique.

| Applicable | Cause | Mise à jour|
| ----- | ------ | ---- | 
| PostgreSQL 9.6, 10, 11 | L’activation du paramètre `pg_qs.replace_parameter_placeholders` peut entraîner l’arrêt du serveur dans certains scénarios rares. | Dans la section Paramètres du serveur du Portail Azure, donnez la valeur `OFF` au paramètre `pg_qs.replace_parameter_placeholders`, puis enregistrez.   | 


## <a name="next-steps"></a>Étapes suivantes
- Consultez les [meilleures pratiques](./concepts-query-store-best-practices.md) relatives au Magasin des requêtes
