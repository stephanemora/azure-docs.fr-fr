---
title: Chaînes de connexion - Azure Database for MariaDB
description: Ce document répertorie les chaînes de connexion actuellement prises en charge pour la connexion des applications à Azure Database for MariaDB, notamment ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python et Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 1b598385d533b3fc157a7a90ecc34c3cb18df4ac
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767277"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Guide pratique pour connecter des applications à Azure Database for MariaDB
Cette rubrique répertorie les types de chaînes de connexion pris en charge par Azure Database for MariaDB, ainsi que des modèles et des exemples. Vous pouvez avoir des paramètres et des réglages différents dans votre chaîne de connexion.

- Pour obtenir le certificat, consultez [Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à la base de données Azure pour MySQL](./howto-configure-ssl.md).
- {votre_hôte} = [nomserveur].mariadb.database.azure.com
- {votre_utilisateur}@{nomserveur} = format ID utilisateur pour une authentification correcte.  Si vous utilisez uniquement l’ID utilisateur, l’authentification échoue.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

Dans cet exemple, le nom du serveur est `mydemoserver`, le nom de la base de données est `wpdb`, le nom d’utilisateur est `WPAdmin` et le mot de passe est `mypassword!2`. Donc, la chaîne de connexion devrait être :

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Obtenir les détails de la chaîne de connexion dans le portail Azure
Dans le [portail Azure](https://portal.azure.com), accédez à votre serveur Azure Database for MariaDB, puis cliquez sur **Chaînes de connexion** pour obtenir la liste des chaînes de votre instance : ![Le panneau Chaînes de connexion dans le portail Azure](./media/howto-connection-strings/connection-strings-on-portal.png)

La chaîne fournit des détails tels que le pilote, le serveur et d’autres paramètres de connexion de base de données. Modifiez ces exemples pour utiliser vos propres paramètres, tels que le nom de la base de données, le mot de passe, etc. Vous pouvez ensuite utiliser cette chaîne pour vous connecter au serveur à partir de votre code et de vos applications.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
