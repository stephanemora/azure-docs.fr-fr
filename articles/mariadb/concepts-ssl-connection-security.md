---
title: Connectivité SSL pour Azure Database for MariaDB
description: Informations de configuration d’Azure Database for MariaDB et des applications associées afin d’utiliser correctement les connexions SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 15bb28846b3409dd31bcdf8d42990facc94fd06d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60332684"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Connectivité SSL dans Azure Database for MariaDB
Azure Database for MariaDB prend en charge la connexion de votre serveur de base de données aux applications clientes via SSL (Secure Sockets Layer). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application.

## <a name="default-settings"></a>Paramètres par défaut
Par défaut, le service de base de données doit être configuré pour exiger des connexions SSL lors de la connexion à MariaDB.  Nous vous recommandons d’éviter de désactiver l’option SSL dans la mesure du possible.

Quand vous provisionnez un nouveau serveur Azure Database for MariaDB par le biais du portail Azure et de l’interface CLI, l’application de connexions SSL est activée par défaut.

Les chaînes de connexion pour les différents langages de programmation sont affichées dans le portail Azure. Ces chaînes de connexion incluent les paramètres SSL requis pour se connecter à votre base de données. Dans le portail Azure, sélectionnez votre serveur. Sous l’en-tête **Paramètres**, sélectionnez les **Chaînes de connexion**. Le paramètre SSL varie en fonction du connecteur, par exemple « ssl=true », « sslmode=require » ou « sslmode=require » et d’autres variations.

Pour découvrir comment activer ou désactiver la connexion SSL pour le développement d’applications, consultez la page [Guide pratique : configurer SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [règles de pare-feu du serveur](concepts-firewall-rules.md)
- Découvrez comment [configurer SSL](howto-configure-ssl.md).
