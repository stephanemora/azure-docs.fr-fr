---
title: Migrer Amazon RDS pour MySQL vers Azure Database pour MySQL à l’aide de MySQL Workbench
description: Cet article explique comment migrer Amazon RDS pour MySQL vers Azure Database pour MySQL à l’aide de l’Assistant migration de MySQL Workbench.
author: HJToland3
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 05/21/2021
ms.openlocfilehash: 18697f2150cdb8a6ab4eeb12334553d32b2f984d
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110471969"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-mysql-workbench"></a>Migrer Amazon RDS pour MySQL vers Azure Database pour MySQL à l’aide de MySQL Workbench

Vous pouvez utiliser différents utilitaires, tels que l’exportation/importation de MySQL Workbench, Azure Database Migration Service (DMS), et la sauvegarde et restauration MySQL, pour migrer Amazon RDS pour MySQL vers Azure Database pour MySQL. Toutefois, l’utilisation de l’Assistant migration de MySQL Workbench offre un moyen simple et pratique de déplacer vos bases de données Amazon RDS pour MySQL vers Azure Database pour MySQL.

L’Assistant migration vous permet de sélectionner facilement les schémas et les objets à migrer. Il vous permet également de consulter les journaux du serveur pour identifier les erreurs et les goulots d’étranglement en temps réel. Par conséquent, vous pouvez éditer et modifier les tables, les structures de base de données et les objets pendant le processus de migration lorsqu’une erreur est détectée, puis reprendre la migration sans avoir à redémarrer à partir de zéro.

> [!NOTE]
> Vous pouvez également utiliser l’Assistant migration pour migrer d’autres sources, telles que Microsoft SQL Server, Oracle, PostgreSQL, MariaDB, etc., qui n’entrent pas dans le cadre de cet article.

## <a name="prerequisites"></a>Prérequis

Avant de commencer le processus de migration, il est recommandé de s’assurer que plusieurs paramètres et fonctionnalités sont configurés correctement, comme décrit ci-dessous.

- Assurez-vous que le jeu de caractères des bases de données source et cible est identique.
- Définissez le délai d’attente sur une valeur raisonnable en fonction de la quantité de données ou de la charge de travail que vous souhaitez importer ou migrer.
- Définissez le `max_allowed_packet parameter` sur une valeur raisonnable en fonction de la taille de la base de données que vous souhaitez importer ou migrer.
- Vérifiez que toutes vos tables utilisent InnoDB, car le serveur Azure Database pour MySQL ne prend en charge que le moteur de stockage InnoDB.
- Supprimez, remplacez ou modifiez tous les déclencheurs, toutes les procédures stockées et autres fonctions contenant l’utilisateur racine ou les definers de super utilisateur (Azure Database pour MySQL ne prend pas en charge le privilège de super utilisateur). Vous pouvez également remplacer les éléments definer par le nom de l’utilisateur administrateur qui exécute le processus d’importation, comme indiqué ci-dessous :

  ```
  DELIMITER; ;/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
  DELIMITER;
  /* Modified to */
  DELIMITER;
  /*!50003 CREATE*//*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
  DELIMITER;

  ```

- Si les fonctions définies par l’utilisateur (UDF) sont exécutées sur votre serveur de base de données, vous devez supprimer le privilège pour la base de données MySQL. Pour déterminer si des fonctions définies par l’utilisateur sont en cours d’exécution sur votre serveur, utilisez la requête suivante :

  ```
  SELECT * FROM mysql.func;
  ```

  Si vous découvrez que les fonctions définies par l’utilisateur sont en cours d’exécution, vous pouvez supprimer les fonctions définies par l’utilisateur à l’aide de la requête suivante :

  ```
  DROP FUNCTION your_UDFunction;
  ```

- Assurez-vous que le serveur sur lequel l’outil est exécuté et l’emplacement d’exportation disposent de suffisamment d’espace disque et de puissance de calcul (vCores, UC et mémoire) pour effectuer l’opération d’exportation, en particulier lors de l’exportation d’une base de données très volumineuse.
- Créez un chemin d’accès entre l’instance locale ou AWS et la base de données Azure pour MySQL si la charge de travail se trouve derrière des pare-feu ou d’autres couches de sécurité réseau.

## <a name="begin-the-migration-process"></a>Commencer le processus de migration

1. Pour démarrer le processus de migration, connectez-vous à MySQL Workbench, puis sélectionnez l’icône Accueil.
2. Dans la barre de navigation de gauche, sélectionnez l’icône de l’Assistant migration, comme indiqué dans la capture d’écran ci-dessous.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/begin-the-migration.png" alt-text="Écran d’accueil de MySQL Workbench":::

   La page **vue d’ensemble** de l’Assistant migration s’affiche, comme montré ci-dessous.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-wizard-welcome.png" alt-text="Page d’accueil de l’Assistant migration de MySQL Workbench":::

3. Déterminez si un pilote ODBC pour MySQL Server est installé en sélectionnant **ouvrir l’administrateur ODBC**.

   Dans notre cas, sous l’onglet **pilotes** , vous remarquerez qu’il y a déjà deux pilotes ODBC MySQL Server installés.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/obdc-administrator-page.png" alt-text="Page Administrateur de sources de données ODBC":::

   Si un pilote ODBC MySQL n’est pas installé, utilisez le programme d’installation de MySQL que vous avez utilisé pour installer MySQL Workbench afin d’installer le pilote. Pour plus d’informations sur l’installation du pilote ODBC MySQL, consultez les ressources suivantes :

   - [MySQL :: MySQL Connector/ODBC Developer Guide :: 4,1 installation de Connector/ODBC sur Windows](https://dev.mysql.com/doc/connector-odbc/en/connector-odbc-installation-binary-windows.html)
   - [Pilote ODBC pour MySQL : comment installer et configurer la connexion (pas à pas) – {coding}Sight (codingsight.com)](https://codingsight.com/install-and-configure-odbc-drivers-for-mysql/)

4. Fermez la boîte de dialogue **administrateur de la source de données ODBC**, puis poursuivez le processus de migration.

## <a name="configure-source-database-server-connection-parameters"></a>Configurer les paramètres de connexion du serveur de base de données source

1. Sur la page **Vue d’ensemble**, sélectionnez **Démarrer la migration**.

   La page **sélection de la source** s’affiche. Utilisez cette page pour fournir des informations sur le SGBDR à partir duquel vous effectuez la migration et sur les paramètres de la connexion.

2. Dans le champ **système de base de données**, sélectionnez **MySQL**.
3. Dans le champ **connexion stockée**, sélectionnez l’un des paramètres de connexion enregistrés pour ce SGBDR.

   Vous pouvez enregistrer les connexions en activant la case à cocher en bas de la page et en spécifiant le nom de votre préférence.

4. Dans le champ **méthode de connexion**, sélectionnez **TCP/IP standard**.
5. Dans le champ **nom d'hôte**, spécifiez le nom de votre serveur de base de données source.
6. Dans le champ **port**, spécifiez **3306**, puis entrez le nom d’utilisateur et le mot de passe pour la connexion au serveur.
7. Dans le champ **base de données**, entrez le nom de la base de données que vous souhaitez migrer si vous le connaissez. Sinon, laissez ce champ vide.
8. Sélectionnez **tester la connexion** pour vérifier la connexion à votre instance MySQL Server.

   Si vous avez entré les paramètres corrects, un message s’affiche pour indiquer la réussite de la tentative de connexion.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-connection-parameters.png" alt-text="Les paramètres de connexion de la base de données ne sont pas valides":::

9. Sélectionnez **Suivant**.

## <a name="configure-target-database-server-connection-parameters"></a>Configurer les paramètres de connexion du serveur de base de données source

1. Sur la page **sélection de la cible**, définissez les paramètres pour vous connecter à votre instance de serveur MySQL cible à l’aide d’un processus similaire à celui utilisé pour configurer la connexion au serveur source.
2. Pour vérifier qu’une connexion est réussie, sélectionnez **tester la connexion**.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/target-connection-parameters.png" alt-text="Les paramètres de connexion de la base de données ne sont pas valides":::

3. Sélectionnez **Suivant**.

## <a name="select-the-schemas-to-migrate"></a>Sélectionnez les schémas à migrer

L’Assistant migration va communiquer avec votre instance de MySQL Server et récupérer une liste de schémas à partir du serveur source.

1. Sélectionnez **Afficher les journaux** pour afficher cette opération.

   La capture d’écran ci-dessous montre comment les schémas sont récupérés à partir du serveur de base de données source.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/retrieve-schemas.png" alt-text="Extraire la page de liste des schémas":::

2. Sélectionnez **Suivant** pour vérifier que tous les schémas ont été extraits avec succès.

   La capture d’écran ci-dessous montre la liste des schémas extraits.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/schemas-selection.png" alt-text="Page de sélection des schémas":::

   Vous pouvez uniquement migrer les schémas qui s’affichent dans cette liste.

3. Sélectionnez les schémas que vous voulez migrer et sélectionnez **Suivant**.

## <a name="object-migration"></a>Migration d'objet

Ensuite, spécifiez le ou les objets que vous souhaitez migrer.

1. Sélectionnez **Afficher la sélection**, puis, sous **Objets disponibles**, sélectionnez et ajoutez les objets que vous souhaitez migrer.

   Une fois que vous avez ajouté les objets, ils s’affichent sous **Objets à migrer**, comme montré dans la capture d’écran ci-dessous.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-objects.png" alt-text="Page Sélection des objets sources":::

   Dans ce scénario, nous avons sélectionné tous les objets de table.

2. Sélectionnez **Suivant**.

## <a name="edit-data"></a>Modifier des données

Dans cette section, vous avez la possibilité de modifier les objets que vous souhaitez migrer.

1. Dans la page **Modification manuelle**, remarquez le menu déroulant **Affichage** dans le coin supérieur droit.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/manual-editing.png" alt-text="Page Sélection de la modification manuelle":::

   La zone de liste déroulante **Affichage** comprend trois éléments :

   - **Tous les objets** : affiche tous les objets. Avec cette option, vous pouvez modifier manuellement le SQL généré avant de l’appliquer au serveur de base de données cible. Pour ce faire, sélectionnez l’objet et sélectionnez Afficher le code et les messages. Vous pouvez voir (et modifier) le code MySQL généré qui correspond à l’objet sélectionné.
   - **Problèmes de migration** : affiche les problèmes qui se sont produits pendant la migration, que vous pouvez examiner et vérifier.
   - **Mappage de colonnes** : affiche les informations de mappage de colonne. Vous pouvez utiliser cette vue pour modifier le nom et la colonne de modification de l’objet cible.

2. Sélectionnez **Suivant**.

## <a name="create-the-target-database"></a>Créer la base de données cible

1. Activez la case à cocher **Créer un schéma dans le SGBDR cible** .

   Vous pouvez également choisir de conserver les schémas déjà existants, afin qu’ils ne soient pas modifiés ou mis à jour.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-database.png" alt-text="Page Options de création cible":::

   Dans cet article, nous avons choisi de créer le schéma dans le SGBDR cible, mais vous pouvez également activer la case à cocher **Créer un fichier de script SQL** pour enregistrer le fichier sur votre ordinateur local ou à d’autres fins.

2. Sélectionnez **Suivant**.

## <a name="run-the-mysql-script-to-create-the-database-objects"></a>Exécuter le script MySQL pour créer les objets de base de données

Étant donné que nous avons choisi de créer un schéma dans le SGBDR cible, le script SQL migré sera exécuté dans le serveur MySQL cible. Vous pouvez afficher sa progression comme indiqué dans la capture d’écran ci-dessous :

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-schemas.png" alt-text="Page Créer des schémas":::

1. Une fois la création des schémas et de leurs objets terminée, sélectionnez **Suivant**.

   Dans la page **Créer des résultats cibles**, vous voyez une liste des objets créés et une notification des erreurs rencontrées lors de leur création, comme illustré dans la capture d’écran suivante.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-results.png" alt-text="Page Créer des résultats cibles":::

2. Passez en revue les détails sur cette page pour vérifier que tout s’est terminé comme prévu.

   Pour cet article, nous n’avons aucune erreur. Si vous n’avez pas besoin de traiter les messages d’erreur, vous pouvez modifier le script de migration.

3. Dans la zone **Objet**, sélectionnez l’objet que vous souhaitez modifier.
4. Sous **script SQL CREATE pour l’objet sélectionné**, modifiez votre script SQL, puis sélectionnez **Appliquer** pour enregistrer les modifications.
5. Sélectionnez **Recréer les objets** pour exécuter le script, y compris vos modifications.

   Si le script échoue, vous devrez peut-être modifier le script généré. Vous pouvez ensuite corriger manuellement le script SQL et tout exécuter à nouveau. Dans cet article, nous ne changeons rien, donc nous laissons le script tel quel.

6. Sélectionnez **Suivant**.

## <a name="transfer-data"></a>Transfert de données

Cette partie du processus déplace les données de l’instance de base de données du serveur MySQL source vers l’instance de base de données MySQL cible que vous venez de créer. Utilisez la page **Configuration du transfert des données** pour configurer ce processus.

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/data-transfer-setup.png" alt-text="Page Configuration du transfert des données":::

Cette page fournit des options pour configurer le transfert de données. Pour les besoins de cet article, nous accepterons les valeurs par défaut.

1. Pour commencer le processus de transfert des données, sélectionnez **Suivant**.

   La progression du processus de transfert de données s’affiche comme indiqué dans la capture d’écran suivante.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/bulk-data-transfer.png" alt-text="Page Transfert des données en bloc":::

   > [!NOTE]
   > La durée du processus de transfert des données est directement liée à la taille de la base de données que vous migrez. Plus la base de données source est volumineuse, plus le processus prendra de temps, mais il peut arriver à quelques heures pour les bases de données plus volumineuses.

2. Une fois le transfert terminé, sélectionnez **Suivant**.

   La page **Rapport de migration** s’affiche et fournit un rapport résumant l’ensemble du processus, comme indiqué dans la capture d’écran ci-dessous :

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-report.png" alt-text="Page Rapport de progression de la migration":::

3. Sélectionnez **Terminer** pour fermer l’assistant migration.

   La migration a été effectuée avec succès.

## <a name="verify-consistency-of-the-migrated-schemas-and-tables"></a>Vérifier la cohérence des tables et des schémas migrés

1. Ensuite, connectez-vous à votre instance de base de données cible MySQL pour vérifier que les tables et les schémas migrés sont cohérents avec votre base de données source MySQL.

   Dans le cas présent, vous pouvez voir que tous les schémas (sakila, modale, Items, customer, clothes, world, et world_x) de la base de données Amazon RDS pour MySQL: **MyjolieDB** ont été migrés avec succès vers l’instance de base de données Azure pour MySQL: **azmysql**.

2. Pour vérifier le nombre de tables et de lignes, exécutez la requête suivante sur les deux instances :

      `SELECT COUNT (*) FROM sakila.actor;`

   Vous pouvez voir dans la capture d’écran ci-dessous que le nombre de lignes pour Amazon RDS MySQL est 200, ce qui correspond à l’instance de base de données Azure pour MySQL.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-source.png" alt-text="Base de données source de taille de table et de ligne":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-target.png" alt-text="Base de données cible de taille de table et de ligne":::

   Vous pouvez exécuter la requête ci-dessus sur chaque schéma et chaque table, ce qui constitue un peu de travail si vous travaillez avec des centaines de milliers, voire des millions de tables. Vous pouvez utiliser les requêtes ci-dessous pour vérifier le schéma (base de données) et la taille de la table à la place.

3. Pour vérifier la taille de la base de données, exécutez la requête suivante :

    ```
   SELECT table_schema AS "Database", 
   ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS "Size (MB)" 
   FROM information_schema.TABLES 
   GROUP BY table_schema;
   ```

4. Pour vérifier la taille de la table, exécutez la requête suivante :

   ```
   SELECT table_name AS "Table",
   ROUND(((data_length + index_length) / 1024 / 1024), 2) AS "Size (MB)"
   FROM information_schema.TABLES
   WHERE table_schema = "database_name"
   ORDER BY (data_length + index_length) DESC;
   ```

   Vous voyez dans les captures d’écran ci-dessous que la taille de schéma (base de données) de l’instance Amazon RDS MySQL source est la même que celle de l’instance de base de données Azure pour MySQL cible.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-source.png" alt-text="Base de données source de taille de base de données":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-target.png" alt-text="Base de données cible de taille de base de données":::

   Étant donné que les tailles de schéma (base de données) sont les mêmes dans les deux instances, il n’est pas vraiment nécessaire de vérifier les tailles de tables individuelles. Dans tous les cas, vous pouvez toujours utiliser la requête ci-dessus pour vérifier la taille de vos tables, si nécessaire.

   Vous avez maintenant confirmé que votre migration a été effectuée avec succès.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la migration de bases de données vers Azure Database pour MySQL, consultez le [Guide de migration des bases de données](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Regardez la vidéo [Easily migrate MySQL/PostgreSQL apps to Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) (Migration facile des applications MySQL/PostgreSQL vers un service géré Azure) qui comprend une démonstration sur la migration des applications MySQL vers Azure Database pour MySQL.
