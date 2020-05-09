---
title: Classement
description: Types de classements pris en charge dans Azure Synapse SQL.
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 2b10aea962a31ba600deca866a8d9f7ab3b81ea8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133674"
---
# <a name="database-collation-support-for-synapse-sql"></a>Prise en charge des classements de base de données pour SQL Synapse

Les classements fournissent les règles de paramètres régionaux, de page de codes, d’ordre de tri et de respect des caractères pour les types de données basés sur des caractères. Une fois que vous avez effectué votre sélection, toutes les colonnes et expressions nécessitant des informations de classement héritent du classement choisi dans le paramètre de base de données. L’héritage par défaut peut être substitué en déclarant explicitement un classement différent pour un type de données basé sur des caractères.

Vous pouvez modifier le classement par défaut de base de données du portail Azure lorsque vous créez une base de données de pool SQL. Grâce à cette fonctionnalité, il est encore plus facile de créer une base de données à l’aide de l’un des 3800 classements de base de données pris en charge.

Vous pouvez spécifier le classement par défaut de la base de données SQL Synapse à la demande au moment de sa création à l’aide de l’instruction CREATE DATABASE.

## <a name="changing-collation"></a>Modification du classement
Pour modifier le classement par défaut de la base de données de pool SQL, vous devez simplement mettre à jour le champ Classement dans l’expérience de provisionnement. Par exemple, si vous souhaitez modifier le classement par défaut en respectant la casse, vous devez simplement renommer le classement SQL_Latin1_General_CP1_CI_AS en SQL_Latin1_General_CP1_CS_AS. 

Pour modifier le classement par défaut de la base de données SQL à la demande, vous pouvez utiliser l’instruction ALTER DATABASE.

## <a name="list-of-unsupported-collation-types"></a>Liste des types de classement pris en charge
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

En outre, le pool SQL ne prend pas en charge les types de classements suivants :

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="checking-the-current-collation"></a>Vérification du classement actuel
Pour vérifier le classement actuel de la base de données, vous pouvez exécuter l’extrait de code T-SQL suivant :
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Quand « Classement » est transmis en tant que paramètre de propriété, la fonction DatabasePropertyEx retourne le classement actuel de la base de données spécifiée. Vous pouvez en savoir plus sur la fonction DatabasePropertyEx sur MSDN.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les bonnes pratiques concernant le pool SQL et SQL à la demande, consultez les articles suivants :

- [Bonnes pratiques relatives aux pools SQL](best-practices-sql-pool.md)
- [Bonnes pratiques relatives à SQL à la demande](best-practices-sql-on-demand.md)


