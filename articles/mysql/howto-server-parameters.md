---
title: Guide pratique pour configurer les paramètres de serveur dans Azure Database pour MySQL
description: Cet article décrit comment configurer les paramètres de serveur MySQL dans Azure Database pour MySQL à l’aide du portail Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 103e09a0e2b9dd409fa2ddaff1c5311ef9936d22
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538497"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Guide pratique pour configurer des paramètres de serveur dans Azure Database pour MySQL à l’aide du portail Azure

Azure Database pour MySQL prend en charge la configuration de certains paramètres de serveur. Cet article décrit comment configurer ces paramètres à l’aide du portail Azure. Les paramètres du serveur ne sont pas tous modifiables.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Accéder à Paramètres du serveur sur le portail Azure

1. Connectez-vous au portail Azure, puis recherchez votre serveur Azure Database pour MySQL.
2. Dans la section **PARAMÈTRES**, cliquez sur **Paramètres du serveur** pour ouvrir la page de paramètres du serveur Azure Database pour MySQL.
![Page Paramètres du serveur du portail Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Recherchez les paramètres que vous devez ajuster. Examinez la colonne **Description** pour comprendre la fonction et les valeurs autorisées.
![Bouton déroulant Énumérer](./media/howto-server-parameters/3-toggle_parameter.png)
4. Cliquez sur **Enregistrer** pour enregistrer vos modifications.
![Enregistrer ou annuler les modifications](./media/howto-server-parameters/4-save_parameters.png)
5. Si vous avez enregistré de nouvelles valeurs pour les paramètres, vous pouvez toujours rétablir toutes les valeurs par défaut en sélectionnant **Rétablir toutes les valeurs par défaut**.
![Rétablir toutes les valeurs par défaut](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Liste des paramètres de serveur configurables

La liste des paramètres de serveur pris en charge s’allonge en permanence. Utilisez l’onglet Paramètres du serveur dans le portail Azure pour obtenir la définition et configurer les paramètres du serveur en fonction des besoins de votre application.

## <a name="non-configurable-server-parameters"></a>Paramètres de serveur non configurables

Le pool de mémoires tampons InnoDB et le nombre maximal de connexions ne sont pas configurables et dépendent de votre [niveau tarifaire](concepts-service-tiers.md).

|**Niveau tarifaire**| **Génération de calcul**|**vCore(s)**|**Pool de mémoires tampons InnoDB (Mo)**| **Nombre maximal de connexions**|
|---|---|---|---|--|
|De base| Gen 4| 1| 960| 50|
|De base| Gen 4| 2| 2560| 100|
|De base| Gen 5| 1| 960| 50|
|De base| Gen 5| 2| 2560| 100|
|Usage général| Gen 4| 2| 3584| 300|
|Usage général| Gen 4| 4| 7680| 625|
|Usage général| Gen 4| 8| 15360| 1250|
|Usage général| Gen 4| 16| 31232| 2 500|
|Usage général| Gen 4| 32| 62976| 5 000|
|Usage général| Gen 5| 2| 3584| 300|
|Usage général| Gen 5| 4| 7680| 625|
|Usage général| Gen 5| 8| 15360| 1250|
|Usage général| Gen 5| 16| 31232| 2 500|
|Usage général| Gen 5| 32| 62976| 5 000|
|Usage général| Gen 5| 64| 125952| 10000|
|Mémoire optimisée| Gen 5| 2| 7168| 600|
|Mémoire optimisée| Gen 5| 4| 15360| 1250|
|Mémoire optimisée| Gen 5| 8| 30720| 2 500|
|Mémoire optimisée| Gen 5| 16| 62464| 5 000|
|Mémoire optimisée| Gen 5| 32| 125952| 10000|

Ces paramètres de serveur ne sont pas configurables dans le système :

|**Paramètre**|**Valeur fixe**|
| :------------------------ | :-------- |
|innodb_file_per_table dans le niveau de base|ÉTEINT|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 Mo|

Tous les autres paramètres de serveur sont définis sur leurs valeurs MySQL par défaut pour les versions [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) et [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Utilisation avec le paramètre de fuseau horaire

### <a name="populating-the-time-zone-tables"></a>Remplissage des tables de fuseaux horaires

Les tables de fuseaux horaires sur votre serveur peuvent être remplies en appelant la procédure stockée `az_load_timezone` à partir d’un outil tel que la ligne de commande MySQL ou MySQL Workbench.

> [!NOTE]
> Si vous exécutez la commande `az_load_timezone` à partir de MySQL Workbench, vous devrez peut-être d’abord désactiver le mode de mise à jour sécurisée à l’aide de `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Pour afficher les valeurs de fuseau horaire disponibles, exécutez la commande suivante :

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Définition du fuseau horaire global

Le fuseau horaire global peut être défini dans la page **Paramètres du serveur** sur le portail Azure. L’exemple ci-dessous définit le fuseau horaire global sur la valeur « US/Pacific ».

![Définir le paramètre de fuseau horaire](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Définition du fuseau horaire de la session

Le fuseau horaire de la session peut être défini en exécutant la commande `SET time_zone` à partir d’un outil tel que la ligne de commande MySQL ou MySQL Workbench. L’exemple ci-dessous définit le fuseau horaire **US/Pacific**.

```sql
SET time_zone = 'US/Pacific';
```

Reportez-vous à la documentation de MySQL sur les [fonction de date et heure](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Étapes suivantes

- [Bibliothèques de connexions pour Azure Database pour MySQL](concepts-connection-libraries.md).
