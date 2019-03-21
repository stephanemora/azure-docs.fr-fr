---
title: Compatibilité des outils de base de données Azure pour la gestion et de pilotes MySQL
description: Cet article décrit les pilotes MySQL et les outils de gestion compatibles avec Azure Database pour MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 05f48145973777052590f8d10e1a2ce1fd22ec7a
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258103"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Pilotes MySQL et outils de gestion compatibles avec Azure Database pour MySQL
Cet article décrit les pilotes et outils de gestion compatibles avec Azure Database pour MySQL.

## <a name="mysql-drivers"></a>Pilotes MySQL
En termes de base de données MySQL, Azure Database pour MySQL utilise la version community edition la plus populaire au monde. Par conséquent, elle est compatible avec un large éventail de langages de programmation et de pilotes. L’objectif est de prendre en charge les trois versions les plus récentes de pilotes MySQL, et de poursuivre les efforts avec les auteurs de la Communauté open source, afin d’améliorer en permanence les fonctionnalités et la facilité d’utilisation des pilotes MySQL. Une liste de pilotes testés et détectés compatibles avec Azure Database pour MySQL 5.6 et 5.7 est fournie dans le tableau suivant :

| **Pilote** | **Liens** | **Versions compatibles** | **Versions incompatibles** | **Remarques** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Pour la connexion PHP 7.0 à SSL MySQLi, ajoutez MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT dans la chaîne de connexion. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Ensemble PDO : option ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` définie sur false.|
| .NET | [MySqlConnector sur GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Package d’installation à partir de Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 et versions ultérieures | 0.26.5 et versions antérieures | |
| Connecteur MySQL/NET | [Connecteur MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Un bogue d’encodage peut provoquer l’échec des connexions sur certains systèmes Windows non -UTF8. |
| Nodejs |  [MySQLjs sur GitHub](https://github.com/mysqljs/mysql/) <br> Package d’installation à partir de NPM :<br> Exécuter `npm install mysql` à partir de NPM | 2.15 | 2.14.1 et versions antérieures | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 et versions antérieures | Utilisez `allowNativePasswords=true` dans la chaîne de connexion pour la version 1.3. Version 1.4 contient un correctif et `allowNativePasswords=true` n’est plus nécessaire. |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 et versions antérieures | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 et versions antérieures | |

## <a name="management-tools"></a>Outils de gestion
L’avantage de compatibilité s’étend également aux outils de gestion de base de données. Vos outils existants doivent continuer de fonctionner avec Azure Database pour MySQL, tant que la manipulation de base de données s’effectue dans les limites des autorisations utilisateur. Trois outils de gestion de base de données courants, qui ont été testés et détectés compatibles avec Azure Database pour MySQL 5.6 et 5.7, sont répertoriés dans le tableau suivant :

|                                     | **MySQL Workbench 6.x et supérieur** | **Navicat 12** | **PHPMyAdmin 4.x et supérieur** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Création, mise à jour, lecture, écriture, suppression | X | X | X |
| Connexion SSL | X | X | X |
| Saisie semi-automatique de la requête SQL | X | X |  |
| Importation et exportation de données | X | X | X |
| Exportation dans plusieurs formats | X | X | X |
| Sauvegarde et restauration |  | X |  |
| Affichage des paramètres du serveur | X | X | X |
| Affichage des connexions client | X | X | X |

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de connexion à la base de données Azure Database pour MySQL](howto-troubleshoot-common-connection-issues.md)