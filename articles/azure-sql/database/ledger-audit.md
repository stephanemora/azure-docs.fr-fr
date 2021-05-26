---
title: Événements d’audit Azure SQL Database avec des tables prenant en charge la comptabilité
description: Vue d’ensemble des fonctionnalités d’audit du Registre Azure SQL Database
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: eafca78dc22a01784eee79293fb7a2ec798818ad
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386367"
---
# <a name="azure-sql-database-audit-events-with-ledger-enabled-tables"></a>Événements d’audit du Registre Azure SQL Database avec des tables prenant en charge la comptabilité

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en **préversion publique**.

Lorsque vous effectuez des activités d’investigation avec des tables prenant en charge la comptabilité, en plus des données capturées dans la vue du registre et dans le registre des bases de données, des ID d’action supplémentaires sont ajoutés aux journaux d’audit SQL.  Le tableau suivant présente ces nouveaux événements de journalisation d’audit, ainsi que les conditions qui déclenchent les événements.

## <a name="enable-ledger"></a>Activer le registre

| Colonne | Valeur |
|--|--|
| **action_id** | ENLR |
| **name** | ACTIVER LE REGISTRE  |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Condition déclenchant l’événement** : création d’une table de registre ou conversion d’une table normale en table de registre.

## <a name="alter-ledger"></a>Modifier le registre

| Colonne | Valeur |
|--|--|
| **action_id** | ALLR |
| **name** | MODIFIER LE REGISTRE |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Condition déclenchant l’événement** : suppression ou attribution d’un nouveau nom à une table du registre, conversion d’une table du registre en table normale, ajout, suppression ou attribution d’un nouveau nom à une colonne dans une table du registre.


## <a name="generate-ledger-digest"></a>Générer un résumé du registre

| Colonne | Valeur |
|--|--|
| **action_id** | GDLR |
| **name** | GÉNÉRER UN RÉSUMÉ DU REGISTRE |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP  |
| **action_in_log** | 1 |

**Condition déclenchant l’événement** : génération d’un résumé du registre.

## <a name="verify-ledger"></a>Vérifier le registre

| Colonne | Valeur |
|--|--|
| **action_id** | VFLR |
| **name** | VÉRIFIER LE GRAND LIVRE |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**Condition déclenchant l’événement** : vérification d’un résumé du registre.

## <a name="ledger-operation-group"></a>Groupe d’opérations du registre

| Colonne | Valeur |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | DATABASE |
| **covering_action_desc** | NULL |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**Condition déclenchant l’événement** : N/A

| Colonne | Valeur |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | SERVER |
| **covering_action_desc** | NULL |
| **parent_class_desc** | NULL |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**Condition déclenchant l’événement** : N/A 

## <a name="next-steps"></a>Étapes suivantes

- [Audit pour Azure SQL Database et Azure Synapse Analytics](auditing-overview.md)
- [Vue d’ensemble du registre Azure SQL Database](ledger-overview.md)
- [Démarrage rapide : créer une base de données Azure SQL Database avec le registre activé](ledger-create-a-single-database-with-ledger-enabled.md)
