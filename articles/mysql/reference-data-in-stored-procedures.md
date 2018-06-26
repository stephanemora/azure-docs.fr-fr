---
title: Procédures stockées de réplication des données entrantes Azure Database pour MySQL
description: Cet article présente toutes les procédures stockées utilisées pour la réplication des données entrantes.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 2d62cd693d7a67faf836c645f8bd33de9afca949
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266106"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Procédures stockées de réplication des données entrantes Azure Database pour MySQL

La réplication des données entrantes permet de synchroniser les données à partir d’un serveur MySQL qui s’exécute en local, dans des machines virtuelles ou des services de base de données hébergés par d’autres fournisseurs cloud dans le service Azure Database pour MySQL.

Les procédures stockées suivantes sont utilisées pour définir ou supprimer la réplication des données entrantes entre un serveur principal et un réplica.

|**Nom de la procédure stockée**|**Paramètres d’entrée**|**Paramètres de sortie**|**Remarque sur l’utilisation**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_primary*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|Pour transférer des données en mode SSL, ajoutez le contexte du certificat d’autorité de certification dans le paramètre master_ssl_ca. </br><br>Pour transférer des données sans SSL, transmettez une chaîne vide dans le paramètre master_ssl_ca.|
|*mysql.az_replication _start*|N/A|N/A|Lance la réplication.|
|*mysql.az_replication _stop*|N/A|N/A|Arrête la réplication.|
|*mysql.az_replication _remove_primary*|N/A|N/A|Supprime la relation de réplication entre le serveur principal et le réplica.|
|*mysql.az_replication_skip_counter*|N/A|N/A|Ignore une erreur de réplication.|

Pour configurer la réplication des données entrantes entre un serveur principal et un réplica dans Azure Database pour MySQL, voir [Guide pratique pour configurer la réplication des données entrantes](howto-data-in-replication.md).