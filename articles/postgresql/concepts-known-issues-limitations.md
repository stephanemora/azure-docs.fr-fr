---
title: Problèmes connus et limitations – Azure Database pour PostgreSQL – Serveur unique et serveur flexible (préversion)
description: Présente les problèmes connus que les clients doivent connaître.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105405"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database pour PostgreSQL – Problèmes connus et limitations

Cette page donne la liste des problèmes connus d’Azure Database pour PostgreSQL qui peuvent avoir un impact sur votre application. Elle présente également des mesures d’atténuation et des recommandations pour contourner le problème.

## <a name="intelligent-performance---query-store"></a>Performances intelligentes – Magasin des requêtes

Applicable à Azure Database pour PostgreSQL – Serveur unique.

| Applicable | Cause | Mise à jour|
| ----- | ------ | ---- | 
| PostgreSQL 9.6, 10, 11 | L’activation du paramètre `pg_qs.replace_parameter_placeholders` peut entraîner l’arrêt du serveur dans certains scénarios rares. | Dans la section Paramètres du serveur du Portail Azure, donnez la valeur `OFF` au paramètre `pg_qs.replace_parameter_placeholders`, puis enregistrez.   | 

## <a name="server-parameters"></a>Paramètres de serveur

Applicable à Azure Database pour PostgreSQL – Serveur unique et serveur flexible.

| Applicable | Cause | Mise à jour| 
| ----- | ------ | ---- | 
| PostgreSQL 9.6, 10, 11 | Le choix d’une valeur supérieure à la valeur [recommandée](https://www.postgresql.org/docs/11/kernel-resources.html) pour le paramètre de serveur `max_locks_per_transaction` peut empêcher le serveur d’être opérationnel après un redémarrage. | Laissez la valeur par défaut (32 ou 64) ou remplacez-la par une valeur raisonnable selon la [documentation](https://www.postgresql.org/docs/11/kernel-resources.html) de PostgreSQL. <br> <br> Du côté du service, des travaux sont en cours pour limiter la valeur haute en fonction de la référence SKU.  | 

## <a name="next-steps"></a>Étapes suivantes
- Consultez les [meilleures pratiques](./concepts-query-store-best-practices.md) relatives au Magasin des requêtes
