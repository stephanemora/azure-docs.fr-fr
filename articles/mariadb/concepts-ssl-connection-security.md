---
title: Connectivité SSL - Azure Database for MariaDB
description: Informations de configuration d’Azure Database for MariaDB et des applications associées afin d’utiliser correctement les connexions SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 36532575645d135a7abe7239798b6f2abc4246f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477066"
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
