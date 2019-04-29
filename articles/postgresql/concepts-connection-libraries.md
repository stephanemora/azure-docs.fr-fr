---
title: Bibliothèques de connexions de la base de données Azure pour PostgreSQL
description: Cet article décrit plusieurs bibliothèques et pilotes que les développeurs peuvent utiliser lorsqu’ils codent des applications pour se connecter à une base de données Azure pour PostgreSQL et l’interroger.
services: postgresql
author: WenJason
ms.author: v-jay
manager: digimobile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
origin.date: 02/28/2018
ms.date: 12/03/2018
ms.openlocfilehash: 0e762a2d7cf82e2957fb276fcea0a20553f719e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559770"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliothèques de connexions de la base de données Azure pour PostgreSQL
Cet article liste les bibliothèques et les pilotes qui sont à la disposition des développeurs pour développer des applications qui se connectent à Azure Database pour PostgreSQL et l’interrogent.

## <a name="client-interfaces"></a>Interfaces client
La plupart des bibliothèques clientes de langages qui permettent de se connecter au serveur PostgreSQL sont des projets externes, distribués de manière indépendante. Les bibliothèques répertoriées sont prises en charge sur les plateformes Windows, Linux et Mac, pour la connexion à Azure Database pour PostgreSQL. Plusieurs exemples de démarrage rapide sont regroupés à la section Étapes suivantes.

| **Langage** | **Interface client** | **Informations supplémentaires** | **Télécharger** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Compatible DB API 2.0 | [Télécharger](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Extension de base de données | [Installer](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Package pg npm](https://www.npmjs.com/package/pg) | Client non bloquant JavaScript pur | [Installer](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Pilote JDBC de type 4 | [Télécharger](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Interface Ruby | [Télécharger](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | Pilote Go Postgres pur | [Installer](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Fournisseur de données ADO.NET | [Télécharger](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Pilote ODBC | [Télécharger](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface principale du langage C | Inclus |
| C++ | [libpqxx](http://pqxx.org/) | Interface C++ remaniée | [Télécharger](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Étapes suivantes
Lisez ces guides de démarrage rapide pour savoir comment se connecter à Azure Database for PostgreSQL et l’interroger dans le langage de votre choix :

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
