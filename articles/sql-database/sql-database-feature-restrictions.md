---
title: Restrictions des fonctionnalités Azure SQL Database | Microsoft Docs
description: Les restrictions des fonctionnalités Azure SQL Database améliorent la sécurité de votre base de données en limitant les fonctionnalités dans votre base de données grâce auxquelles des attaquants peuvent accéder aux informations qu’elles contiennent.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: ac7a074e78def504a10b4daa07971f919f414a88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66259451"
---
# <a name="azure-sql-database-feature-restrictions"></a>Restrictions des fonctionnalités Azure SQL Database

Une source courante d’attaques de SQL Server est par le biais d’applications web qui accèdent à la base de données où diverses formes d’attaques par injection de code SQL sont utilisées pour dérober des informations sur la base de données.  Dans l’idéal, le code d’application est développé afin qu’il n’autorise pas l’injection de code SQL.  Toutefois, dans des bases de code volumineuses qui incluent du code hérité et externe, on ne peut jamais être sûr que tous les cas ont été traités. Les injections de code SQL sont donc une réalité contre laquelle nous devons nous protéger.  Les restrictions des fonctionnalités ont pour finalité d’empêcher certaines formes d’injection de code SQL de faire fuiter des informations sur la base de données, même lorsque l’injection de code SQL est réussie.

## <a name="enabling-feature-restrictions"></a>Activation des restrictions des fonctionnalités

L’activation des restrictions des fonctionnalités est effectuée à l’aide de la procédure stockée `sp_add_feature_restriction` comme suit :

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Les fonctionnalités suivantes peuvent être restreintes :

| Fonctionnalité          | Description |
|------------------|-------------|
| N'ErrorMessages' | Lorsqu’elle est restreinte, toutes les données utilisateur contenues dans le message d’erreur sont masquées. Consultez [Restriction des fonctionnalités de messages d’erreur](#error-messages-feature-restriction) |
| N'Waitfor'       | Lorsqu’elle est restreinte, la commande retourne immédiatement sans délai. Consultez [Restriction de la fonctionnalité WAITFOR](#waitfor-feature-restriction) |

La valeur de `object_class` peut être `N'User'` ou `N'Role'` pour désigner si `object_name` est un nom d’utilisateur ou un nom de rôle dans la base de données.

L’exemple suivant entraîne le masquage de tous les messages d’erreur pour l’utilisateur `MyUser` :

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Désactivation des restrictions des fonctionnalités

La désactivation des restrictions des fonctionnalités est effectuée à l’aide de la procédure stockée `sp_drop_feature_restriction` comme suit :

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

L’exemple suivant désactive le masquage du message d’erreur pour l’utilisateur `MyUser` :

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Affichage des restrictions des fonctionnalités

La vue `sys.sql_feature_restrictions` affiche toutes les restrictions des fonctionnalités actuellement définies sur la base de données. Elle comporte les colonnes suivantes :

| Nom de la colonne | Type de données | Description |
|-------------|-----------|-------------|
| class       | nvarchar(128) | Classe d’objet à laquelle la restriction s’applique |
| objet      | nvarchar(256) | Nom d’objet auquel la restriction s’applique |
| fonctionnalité     | nvarchar(128) | Fonctionnalité restreinte |

## <a name="feature-restrictions"></a>Restrictions des fonctionnalités

### <a name="error-messages-feature-restriction"></a>Restriction des fonctionnalités de messages d’erreur

Une méthode d’attaque par injection de code SQL courante consiste à injecter du code provoquant une erreur.  En examinant le message d’erreur, un attaquant peut obtenir des informations sur le système, permettant ainsi des attaques encore plus ciblées.  Cette attaque peut être particulièrement utile lorsque l’application n’affiche pas les résultats d’une requête, mais qu’elle affiche des messages d’erreur.

Prenons une application web incluant une requête sous la forme suivante :

```html
http://www.contoso.com/employee.php?id=1
```

Qui exécute la requête de base de données suivante :

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Si la valeur transmise en tant qu’argument `id` à la requête d’application web est copiée pour remplacer $EmpId dans la requête de base de données, un attaquant pourrait effectuer la requête suivante :

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

L’erreur suivante serait alors retournée, permettant ainsi à l’attaquant de connaître le nom de la base de données :

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Après l’activation de la restriction des fonctionnalités de messages d’erreur pour l’utilisateur de l’application dans la base de données, le message d’erreur retourné est masqué afin qu’aucune information interne sur la base de données ne soit divulguée :

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

De même, l’attaquant pourrait effectuer la requête suivante :

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

L’erreur suivante serait alors retournée, permettant ainsi à l’attaquant de connaître le salaire de l’employé :

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Grâce à la restriction des fonctionnalités de messages erreur, la base de données retournerait :

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>Restriction de la fonctionnalité WAITFOR

Une injection de code SQL aveugle se produit lorsqu’une application ne fournit pas à un attaquant les résultats du code SQL injecté ou un message d’erreur, mais où l’attaquant peut déduire des informations de la base de données en créant une requête conditionnelle dans laquelle les deux branches conditionnelles ont une durée d’exécution différente. En comparant le temps de réponse, l’attaquant peut savoir quelle branche a été exécutée, et ainsi obtenir des informations sur le système. La variante la plus simple de cette attaque consiste à utiliser l’instruction `WAITFOR` pour introduire le délai.

Prenons une application web incluant une requête sous la forme suivante :

```html
http://www.contoso.com/employee.php?id=1
```

qui exécute la requête de base de données suivante :

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Si la valeur transmise en tant que paramètre d’ID à la requête d’application web est copiée pour remplacer $EmpId dans la requête de base de données, un attaquant peut effectuer la requête suivante :

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

L’exécution de la requête prendrait alors 5 secondes de plus si le compte `sa` était utilisé. Si une restriction de fonctionnalité `WAITFOR` est désactivée dans la base de données, l’instruction `WAITFOR` est ignorée et aucune information n’est divulguée par le biais de cette attaque.