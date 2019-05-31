---
title: Les restrictions de fonctionnalité de base de données SQL Azure | Microsoft Docs
description: Les restrictions de fonctionnalité de base de données SQL Azure améliore la sécurité de votre base de données en limitant les fonctionnalités dans votre base de données qui peuvent être par des attaquants pour accéder aux informations qu’ils contiennent.
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
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259451"
---
# <a name="azure-sql-database-feature-restrictions"></a>Restrictions de fonctionnalité de base de données SQL Azure

Une source courante d’attaques de SQL Server est via des applications web qui accèdent à la base de données où les diverses formes d’attaques par injection SQL sont utilisées pour glaner des informations sur la base de données.  Dans l’idéal, le code d’application est développé afin qu’il n’autorise pas l’injection SQL.  Toutefois, dans Grand-bases de code qui incluent du code hérité et externe, un peut jamais être sûr que tous les cas ont été traités, par conséquent, les injections SQL sont une réalité que nous devons protéger contre.  Des restrictions des fonctionnalités vise à empêcher certaines formes d’injection SQL à partir de la fuite des informations sur la base de données, même lorsque l’injection SQL est réussie.

## <a name="enabling-feature-restrictions"></a>Activation des Restrictions de fonctionnalité

L’activation des restrictions liées aux fonctionnalités est effectuée à l’aide de la `sp_add_feature_restriction` procédure stockée comme suit :

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Les fonctionnalités suivantes peuvent être limitées :

| Fonctionnalité          | Description |
|------------------|-------------|
| N'ErrorMessages' | Lorsque la limitée, toutes les données utilisateur dans le message d’erreur seront masquées. Consultez [Restriction des fonctionnalités de Messages d’erreur](#error-messages-feature-restriction) |
| N'Waitfor'       | Lors de la restriction, la commande retournera immédiatement sans délai. Consultez [Restriction de fonctionnalité WAITFOR](#waitfor-feature-restriction) |

La valeur de `object_class` peut être `N'User'` ou `N'Role'` pour désigner si `object_name` est un nom d’utilisateur ou un nom de rôle dans la base de données.

L’exemple suivant entraîne tous les messages d’erreur pour l’utilisateur `MyUser` à masquer :

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>La désactivation des restrictions liées aux fonctionnalités

La désactivation des restrictions liées aux fonctionnalités s’effectue à l’aide de la `sp_drop_feature_restriction` procédure stockée comme suit :

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

L’exemple suivant désactive le masquage de message d’erreur pour l’utilisateur `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Restrictions liées aux fonctionnalités de visualisation

Le `sys.sql_feature_restrictions` vue affiche toutes les restrictions de la fonctionnalité actuellement définies sur la base de données. Il comporte les colonnes suivantes :

| Nom de la colonne | Type de données | Description |
|-------------|-----------|-------------|
| class       | nvarchar(128) | Classe d’objet auquel s’applique la restriction |
| objet      | nvarchar(256) | Nom de l’objet auquel s’applique la restriction |
| Fonctionnalité     | nvarchar(128) | Fonctionnalité limitée |

## <a name="feature-restrictions"></a>Restrictions liées aux fonctionnalités

### <a name="error-messages-feature-restriction"></a>Restriction de fonctionnalité de Messages erreur

Une méthode attaque d’injection SQL courantes consiste à injecter du code qui provoque une erreur.  En examinant le message d’erreur, un attaquant peut obtenir des informations sur le système, l’activation des attaques plus ciblées supplémentaires.  Cette attaque peut être particulièrement utile lorsque l’application n’affiche pas les résultats d’une requête, mais n’affiche pas les messages d’erreur.

Considérez une application web qui a une demande de la forme de :

```html
http://www.contoso.com/employee.php?id=1
```

Qui exécute la requête de base de données suivante :

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Si la valeur passée en tant que le `id` paramètre à la demande d’application web est copiée pour remplacer $EmpId dans la requête de base de données, un attaquant pourrait effectuer la requête suivante :

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

Et l’erreur suivante est retournée, ce qui permet l’attaquant de connaître le nom de la base de données :

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Une fois que les messages d’erreur l’activation des fonctionnalités restriction de l’utilisateur de l’application dans la base de données, le message d’erreur retourné est masqué afin qu’aucune information interne sur la base de données n’est divulguée :

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

De même, l’attaquant peut faire la demande suivante :

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

Et l’erreur suivante est retournée, l’attaquant pour en savoir plus de salaire de l’employé :

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

À l’aide de la restriction de fonctionnalité de messages erreur, la base de données retournerait :

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>Restriction de fonctionnalité WAITFOR

Une injection de code SQL aveugle est lorsqu’une application ne fournit pas d’un attaquant avec les résultats de l’instruction SQL injecté ou avec un message d’erreur, mais l’attaquant peut déduire des informations à partir de la base de données en créant une requête conditionnelle dans laquelle les deux branches conditionnelles prendre un temps d’exécution différents. En comparant le temps de réponse, l’attaquant peut savoir quelle branche a été exécutée et ainsi obtenir des informations sur le système. À l’aide de la variante la plus simple de cette attaque la `WAITFOR` instruction pour introduire le délai.

Considérez une application web qui a une demande de la forme de :

```html
http://www.contoso.com/employee.php?id=1
```

qui exécute la requête de base de données suivante :

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Si la valeur passée comme paramètre id pour les demandes d’application web est copiée pour remplacer $EmpId dans la requête de base de données, une personne malveillante peut exécuter la requête suivante :

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

Et la requête prendrait un 5 secondes supplémentaires si le `sa` compte a été utilisé. Si `WAITFOR` restriction de fonctionnalité est désactivée dans la base de données, la `WAITFOR` instruction sera ignorée et pas les informations sont perdues à l’aide de cette attaque.