---
title: Migrer des bases de données volumineuses vers Azure Database pour MySQL avec mydumper/myloader
description: Cet article décrit deux méthodes courantes pour sauvegarder et restaurer des bases de données dans votre instance Azure Database pour MySQL en utilisant l’outil mydumper/myloader.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: e295e967b9164e9ab4744d18f407a18feb32481e
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534955"
---
# <a name="migrate-large-databases-to-azure-database-for-mysql-using-mydumpermyloader"></a>Migrer des bases de données volumineuses vers Azure Database pour MySQL avec mydumper/myloader

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database pour MySQL est un service géré qui vous permet d’exécuter, de gérer et de mettre à l’échelle des bases de données MySQL hautement disponibles dans le cloud. Pour migrer des bases de données MySQL d’une taille supérieure à 1 To vers Azure Database pour MySQL, envisagez d’utiliser des outils de la communauté comme [mydumper/myloader](https://centminmod.com/mydumper.html), qui offrent les avantages suivants :

* Le parallélisme, pour aider à réduire la durée de la migration.
* De meilleures performances, en évitant les routines de conversion des jeux de caractères consommatrices de ressources.
* Un format de sortie avec des fichiers distincts pour les tables, les métadonnées, etc. qui facilite la visualisation et l’analyse des données. La cohérence, en maintenant la capture instantanée sur tous les threads.
* Des positions précises des journaux de la base de données principale et du réplica.
* La facilité de gestion, car ils prennent en charge les expressions PCRE (Perl Compatible Regular Expression) pour spécifier les inclusions et les exclusions de la base de données et des tables.
* Le schéma et les données sont ensemble. Vous n’avez pas besoin de les gérer séparément, comme c’est le cas avec d’autres outils de migration logique.

Ce guide de démarrage rapide vous montre comment installer, sauvegarder et restaurer une base de données MySQL en utilisant mydumper/myloader.

## <a name="prerequisites"></a>Prérequis

Avant de commencer la migration de votre base de données MySQL, vous devez :

1. Créer un serveur Azure Database pour MySQL en utilisant le [portail Azure](./flexible-server/quickstart-create-server-portal.md).

2. Créer une machine virtuelle Azure exécutant Linux (de préférence Ubuntu) en utilisant le [portail Azure](../virtual-machines/linux/quick-create-portal.md).
    > [!Note]
    > Avant d’installer les outils, tenez compte des points suivants :
    >
    > * Si votre source est locale et qu’elle a une connexion à Azure avec une bande passante élevée (utilisant ExpressRoute), envisagez d’installer l’outil sur une machine virtuelle Azure.<br> 
    > * Si la bande passante entre la source et la cible est problématique, envisagez d’installer mydumper près de la source et myloader près du serveur cible. Vous pouvez utiliser les outils **[Azcopy](../storage/common/storage-use-azcopy-v10.md)** pour déplacer les données d’environnements locaux ou d’autres solutions cloud vers Azure.

3. Installez le client MySQL en effectuant les étapes suivantes :

4. 

    * Mettez à jour l’index du package sur la machine virtuelle Azure exécutant Linux avec la commande suivante :
        ```bash
        $ sudo apt update
        ```
    * Installez le package client mysql en exécutant la commande suivante :
        ```bash
        $ sudo apt install mysql-client
        ```

## <a name="install-mydumpermyloader"></a>Installer mydumper/myloader

Pour installer mydumper/myloader, effectuez les étapes suivantes.

1. En fonction de la distribution de votre système d’exploitation, téléchargez le package approprié pour mydumper/myloader en exécutant la commande suivante :
2. 
    ```bash
    $ wget https://github.com/maxbube/mydumper/releases/download/v0.10.1/mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Note]
    > $(lsb_release -cs) permet d’identifier votre distribution.

3. Pour installer le package .deb pour mydumper, exécutez la commande suivante :

    ```bash
    $ dpkg -i mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Tip]
    > La commande que vous utilisez pour installer le package varie en fonction de votre distribution Linux, car les programmes d’installation sont différents. mydumper/myloader est disponible pour les distributions suivantes : Fedora, RedHat, Ubuntu, Debian, CentOS, openSUSE et MacOSX. Pour plus d’informations, consultez **[Comment installer mydumper](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader)** .

## <a name="create-a-backup-using-mydumper"></a>Créer une sauvegarde en utilisant mydumper

* Pour créer une sauvegarde en utilisant mydumper, exécutez la commande suivante :

    ```bash
    $ mydumper --host=<servername> --user=<username> --password=<Password> --outputdir=./backup --rows=100000 --compress --build-empty-files --threads=16 --compress-protocol --trx-consistency-only --ssl  --regex '^(<Db_name>\.)' -L mydumper-logs.txt
    ```

Cet exemple utilise les variables suivantes :

* **--host :** Hôte auquel se connecter
* **--user :** Nom d’utilisateur avec les privilèges nécessaires 
* **--password :** Mot de passe utilisateur
* **--rows :** Essayer de fractionner les tables en segments de ce nombre de lignes
* **--outputdir :** Répertoire où placer les fichiers de sortie
* **--regex :** Expression régulière pour la correspondance de la base de données.
* **--trx-consistency-only :** Cohérence transactionnelle uniquement
* **--threads :** Nombre de threads à utiliser ; par défaut, 4. Il est recommandé d’utiliser une valeur égale à 2 fois le nombre de vCores de l’ordinateur.

    >[!Note] 
    >Pour plus d’informations sur les autres options que vous pouvez utiliser avec mydumper, exécutez la commande suivante : **mydumper --help**. Pour plus d’informations, consultez la [documentation de mydumper\myloader](https://centminmod.com/mydumper.html)<br>
    Pour produire une image mémoire de plusieurs bases de données en parallèle, vous pouvez modifier la variable regex comme indiqué dans l’exemple : **regex ’^(NomBD1\.|NomBD2\.)**

## <a name="restore-your-database-using-myloader"></a>Restaurer votre base de données en utilisant myloader

* Pour restaurer la base de données que vous avez sauvegardée en utilisant mydumper, exécutez la commande suivante :

    ```bash
    $ myloader --host=<servername> --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=500 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

Cet exemple utilise les variables suivantes :

* **--host :** Hôte auquel se connecter
* **--user :** Nom d’utilisateur avec les privilèges nécessaires 
* **--password :** Mot de passe utilisateur
* **--directory :** Emplacement où la sauvegarde est stockée. 
* **--queries-per-transaction :** La valeur recommandée pour ce paramètre doit être inférieure ou égale à 500
* **--threads :** Nombre de threads à utiliser ; par défaut, 4. Il est recommandé d’utiliser une valeur égale à 2 fois le nombre de vCores de l’ordinateur.

> [!Tip]
> Pour plus d’informations sur les autres options que vous pouvez utiliser avec myloader, exécutez la commande suivante : **myloader --help**

Une fois la base de données restaurée, il est toujours recommandé de vérifier la cohérence des données entre les bases de données source et cible.

> [!Note]
> Soumettez les problèmes ou un feedback concernant les outils mydumper/myloader **[ici](https://github.com/maxbube/mydumper/issues)** .

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [projet mydumper/myloader dans GitHub](https://github.com/maxbube/mydumper).
* Découvrez [Comment migrer des bases de données MySQL volumineuses](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).
* [Tutoriel : Migration d’une base de données Azure Database pour MySQL avec un temps d’arrêt minimal - Serveur unique vers Azure Database pour MySQL - Serveur flexible](howto-migrate-single-flexible-minimum-downtime.md)
* En savoir plus sur la réplication des données dans [Répliquer les données dans Azure Database pour MySQL - Serveur flexible](flexible-server/concepts-data-in-replication.md) et [Configurer la réplication de données dans Azure Database pour MySQL - Serveur flexible](./flexible-server/how-to-data-in-replication.md)
* [Erreurs de migration](./howto-troubleshoot-common-errors.md) les plus fréquentes