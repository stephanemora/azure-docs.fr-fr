---
title: Connectivité SSL - Azure Database pour MySQL
description: Informations de configuration de la base de données Azure pour MySQL et des applications associées afin d’utiliser correctement les connexions SSL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 6a12ef851823ab5eff2b11905d05be1950c82ef0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474269"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Connectivité SSL dans la base de données Azure pour MySQL

La base de données Azure pour MySQL prend en charge la connexion de votre serveur de base de données aux applications clientes via SSL (Secure Sockets Layer). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application.

## <a name="ssl-default-settings"></a>Paramètres SSL par défaut

Par défaut, le service de base de données doit être configuré pour exiger des connexions SSL lors de la connexion à MySQL.  Nous vous recommandons d’éviter de désactiver l’option SSL dans la mesure du possible.

Lorsque vous approvisionnez un nouveau serveur de base de données Azure pour MySQL par le biais du Portail Azure et d’Azure CLI, l’application de connexions SSL est activée par défaut. 

Les chaînes de connexion pour les différents langages de programmation sont affichées dans le portail Azure. Ces chaînes de connexion incluent les paramètres SSL requis pour se connecter à votre base de données. Dans le portail Azure, sélectionnez votre serveur. Sous l’en-tête **Paramètres**, sélectionnez les **Chaînes de connexion**. Le paramètre SSL varie en fonction du connecteur, par exemple « ssl=true », « sslmode=require » ou « sslmode=require » et d’autres variations.

Pour découvrir comment activer ou désactiver la connexion SSL pour le développement d’applications, consultez la page [Guide pratique : configurer SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Étapes suivantes

[Bibliothèques de connexions de la base de données Azure pour MySQL](concepts-connection-libraries.md)
