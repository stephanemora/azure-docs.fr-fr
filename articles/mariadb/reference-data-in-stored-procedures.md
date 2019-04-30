---
title: Procédures stockées de réplication des données entrantes Azure Database for MariaDB
description: Cet article présente toutes les procédures stockées utilisées pour la réplication des données entrantes.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: reference
ms.date: 09/24/2018
ms.openlocfilehash: 75dc10ba3d95fd12ea99e10d321237560ee28171
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125863"
---
# <a name="azure-database-for-mariadb-data-in-replication-stored-procedures"></a>Procédures stockées de réplication des données entrantes Azure Database for MariaDB

La réplication des données entrantes permet de synchroniser les données à partir d’un serveur MariaDB qui s’exécute en local, dans des machines virtuelles ou des services de base de données hébergés par d’autres fournisseurs cloud dans le service Azure Database for MariaDB.

Les procédures stockées suivantes sont utilisées pour définir ou supprimer la réplication des données entrantes entre un serveur maître et un réplica.

|**Nom de la procédure stockée**|**Paramètres d’entrée**|**Paramètres de sortie**|**Remarque sur l’utilisation**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|Pour transférer des données en mode SSL, ajoutez le contexte du certificat d’autorité de certification dans le paramètre master_ssl_ca. </br><br>Pour transférer des données sans SSL, transmettez une chaîne vide dans le paramètre master_ssl_ca.|
|*mysql.az_replication _start*|N/A|N/A|Lance la réplication.|
|*mysql.az_replication _stop*|N/A|N/A|Arrête la réplication.|
|*mysql.az_replication _remove_master*|N/A|N/A|Supprime la relation de réplication entre le serveur maître et le réplica.|
|*mysql.az_replication_skip_counter*|N/A|N/A|Ignore une erreur de réplication.|

Pour configurer la réplication des données entrantes entre un serveur maître et un réplica dans Azure Database for MariaDB, consultez [Guide pratique pour configurer la réplication des données entrantes](howto-data-in-replication.md).
