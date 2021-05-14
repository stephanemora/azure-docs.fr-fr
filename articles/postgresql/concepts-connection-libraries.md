---
title: Bibliothèques de connexions - Azure Database pour PostgreSQL - Serveur unique
description: Cet article décrit plusieurs bibliothèques et pilotes que vous pouvez utiliser lors du codage d’applications pour vous connecter à Azure Database pour PostgreSQL - Serveur unique et l’interroger.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: b8a1526605195b5eb24d8044f42b70ca5336bf7c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878305"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Bibliothèques de connexions d’Azure Database pour PostgreSQL - Serveur unique
Cet article liste les bibliothèques et les pilotes qui sont à la disposition des développeurs pour développer des applications qui se connectent à Azure Database pour PostgreSQL et l’interrogent.

## <a name="client-interfaces"></a>Interfaces client
La plupart des bibliothèques clientes de langages qui permettent de se connecter au serveur PostgreSQL sont des projets externes, distribués de manière indépendante. Les bibliothèques répertoriées sont prises en charge sur les plateformes Windows, Linux et Mac, pour la connexion à Azure Database pour PostgreSQL. Plusieurs exemples de démarrage rapide sont regroupés à la section Étapes suivantes.

| **Langage** | **Interface client** | **Informations supplémentaires** | **Télécharger** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Compatible DB API 2.0 | [Télécharger](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Extension de base de données | [Installer](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Package pg npm](https://www.npmjs.com/package/pg) | Client non bloquant JavaScript pur | [Installer](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Pilote JDBC de type 4 | [Télécharger](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Interface Ruby | [Télécharger](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | Pilote Go Postgres pur | [Installer](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Fournisseur de données ADO.NET | [Télécharger](https://dotnet.microsoft.com/download) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Pilote ODBC | [Télécharger](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface principale du langage C | Inclus |
| C++ | [libpqxx](http://pqxx.org/) | Interface C++ remaniée | [Télécharger](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Étapes suivantes
Lisez ces guides de démarrage rapide pour savoir comment se connecter à Azure Database for PostgreSQL et l’interroger dans le langage de votre choix :

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
