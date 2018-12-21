---
title: 'Tutoriel : Concevez votre première solution Azure SQL Database à l’aide de SSMS | Microsoft Docs'
description: Apprenez à concevoir votre première base de données SQL Azure avec SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 9fa36b9b87a8e9591b0c863826cd2278a29ba28e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956055"
---
# <a name="tutorial-design-your-first-azure-sql-database-using-ssms"></a>Tutoriel : Concevoir votre première base de données SQL Azure à l’aide de SSMS

Azure SQL Database est une solution DBaaS relationnelle dans Microsoft Cloud (Azure). Dans ce didacticiel, vous allez apprendre à utiliser le portail Azure et [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) pour :

> [!div class="checklist"]
> * Créer une base de données dans le portail Azure*
> * Configurer une règle de pare-feu au niveau du serveur dans le portail Azure
> * Connexion à la base de données avec SSMS
> * Créer des tables avec SSMS
> * Charger en masse des données avec BCP
> * Interroger des données avec SSMS

*Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

> [!NOTE]
> Pour les besoins de ce didacticiel, nous utilisons le [modèle d’achat DTU](sql-database-service-tiers-dtu.md), mais vous avez la possibilité de choisir le [modèle d’achat vCore](sql-database-service-tiers-vcore.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vérifiez que les éléments suivants sont installés :

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (dernière version)
- [BCP et SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (dernière version)

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Créer une base de données vide

Une base de données SQL Azure est créée avec un ensemble défini de [ressources de calcul et de stockage](sql-database-service-tiers-dtu.md). La base de données est créée dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) et sur un [serveur logique Azure SQL Database](sql-database-features.md).

Pour créer une base de données SQL vide, suivez la procédure suivante.

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

1. Dans la page **Nouveau**, sélectionnez **Bases de données** dans la section Place de marché Azure, puis cliquez sur **SQL Database** dans la section **Sélection**.

   ![créer une base de données vide](./media/sql-database-design-first-database/create-empty-database.png)

   1. Remplissez le formulaire **Base de données SQL** avec les informations suivantes, comme indiqué dans l’illustration précédente :

      | Paramètre       | Valeur suggérée | DESCRIPTION |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Nom de la base de données** | *yourDatabase* | Pour connaître les noms de bases de données valides, consultez [Identificateurs de base de données](/sql/relational-databases/databases/database-identifiers). |
      | **Abonnement** | *yourSubscription*  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
      | **Groupe de ressources** | *yourResourceGroup* | Pour les noms de groupe de ressources valides, consultez [Naming conventions](/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
      | **Sélectionner une source** | Base de données vide | Indique qu’une base de données vide doit être créée. |

   1. Cliquez sur **Serveur** pour utiliser un serveur existant ou créer et configurer un nouveau serveur pour votre base de données. Sélectionnez le serveur ou cliquez sur **Créer un serveur** et remplissez le formulaire **Nouveau serveur** avec les informations suivantes :

      | Paramètre       | Valeur suggérée | DESCRIPTION |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Nom du serveur** | Nom globalement unique | Pour les noms de serveur valides, consultez [Naming conventions](/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
      | **Connexion d’administrateur du serveur** | Nom valide | Pour connaître les noms de connexions valides, consultez [Identificateurs de base de données](/sql/relational-databases/databases/database-identifiers). |
      | **Mot de passe** | Mot de passe valide | Votre mot de passe doit comporter au moins huit caractères et contenir des caractères appartenant à trois des catégories suivantes : majuscules, minuscules, chiffres et caractères non alphanumériques. |
      | **Lieu** | Emplacement valide | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |

      ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

      Cliquez sur **Sélectionner**.

   1. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service, le nombre de DTU ou de vCore et la quantité de stockage. Vous pouvez explorer les options concernant le nombre de DTU/vCore et le stockage disponible pour chaque niveau de service. Par défaut, le [modèle d’achat DTU](sql-database-service-tiers-dtu.md) **Standard** est sélectionné, mais vous avez la possibilité de choisir le [modèle d’achat vCore](sql-database-service-tiers-vcore.md).

      > [!IMPORTANT]
      > Un espace de stockage supérieur à 1 To au niveau Premium est actuellement disponible dans les toutes régions sauf les suivantes : Royaume-Uni Nord, USA Centre-Ouest, Royaume-Uni Sud 2, Chine Est, USDoDCentral, Allemagne Centre, USDoDEast, US Gov Sud-Ouest, US Gov Centre-Sud, Allemagne Nord-Est, Chine Nord, US Gov Est. Dans les autres régions, l’espace de stockage maximal au niveau Premium est limité à 1 To. Consultez [Limitations actuelles P11-P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).

      Après avoir sélectionné le niveau de service, le nombre de DTU et la quantité de stockage, cliquez sur **Appliquer**.

   1. Entrez un **Classement** pour la base de données vide (pour ce tutoriel, utilisez la valeur par défaut). Pour en savoir plus sur les classements, voir [Classements](/sql/t-sql/statements/collations)

1. Maintenant que vous avez rempli le formulaire **SQL Database**, cliquez sur **Créer** pour provisionner la base de données. Cette étape peut prendre quelques minutes.

1. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

     ![notification](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Créer une règle de pare-feu

Le service de base de données SQL crée un pare-feu au niveau du serveur. Le pare-feu empêche les outils et les applications externes de se connecter au serveur et aux bases de données sur le serveur. Pour activer la connectivité externe à votre base de données, vous devez d’abord ajouter une règle pour votre adresse IP au pare-feu. Suivez ces étapes pour créer une [règle de pare-feu au niveau du serveur de base de données SQL](sql-database-firewall-configure.md).

> [!NOTE]
> La base de données SQL communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre administrateur ouvre le port 1433.

1. Une fois le déploiement terminé, cliquez sur **Bases de données SQL** dans le menu de gauche, puis cliquez sur *yourDatabase* dans la page **Bases de données SQL**. La page de vue d’ensemble de votre base de données s’ouvre. Elle affiche le **nom complet du serveur** (par exemple, *yourserver.database.windows.net*) et fournit des options pour poursuivre la configuration.

1. Copiez le nom complet du serveur pour vous connecter à votre serveur et aux bases de données à partir de SQL Server Management Studio.

   ![nom du serveur](./media/sql-database-design-first-database/server-name.png)

1. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre.

   ![règle de pare-feu de serveur](./media/sql-database-design-first-database/server-firewall-rule.png)

   1. Dans la barre d’outils, cliquez sur **Ajouter une adresse IP cliente** afin d’ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu. Une règle de pare-feu peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

   1. Cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur logique.

   1. Cliquez sur **OK**, puis fermez la page **Paramètres de pare-feu**.

Votre adresse IP peut désormais traverser le pare-feu. Vous pouvez maintenant vous connecter au serveur de base de données et à ses bases de données à l’aide de SQL Server Management Studio ou tout autre outil de votre choix. Veillez à utiliser le compte d’administrateur de serveur que vous avez créé précédemment.

> [!IMPORTANT]
> Par défaut, l’accès par le biais du pare-feu de base de données SQL est activé pour tous les services Azure. Cliquez sur **ÉTEINT** sur cette page pour le désactiver pour tous les services Azure.

## <a name="connect-to-the-database"></a>Se connecter à la base de données

Utilisez [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) pour établir une connexion à votre serveur de base de données Azure SQL.

1. Ouvrez SQL Server Management Studio.

1. Dans la fenêtre **Se connecter au serveur**, entrez les valeurs suivantes :

   | Paramètre       | Valeur suggérée | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Type de serveur** | Moteur de base de données | Cette valeur est obligatoire. |
   | **Nom du serveur** | Nom complet du serveur | Par exemple, *yourserver.database.windows.net*. |
   | **Authentification** | l’authentification SQL Server | L’authentification SQL est le seul type d’authentification que nous avons configuré dans ce tutoriel. |
   | **Connexion** | Compte d’administrateur de serveur | Le compte que vous avez spécifié lorsque vous avez créé le serveur. |
   | **Mot de passe** | Mot de passe de votre compte d’administrateur de serveur | Il s’agit du mot de passe que vous avez spécifié quand vous avez créé le serveur. |

   ![connect to server](./media/sql-database-design-first-database/connect.png)

   1. Cliquez sur **Options** dans la boîte de dialogue **Se connecter au serveur**. Dans la section **Se connecter à la base de données**, entrez *yourDatabase* pour vous connecter à cette base de données.

      ![connexion à la base de données sur le serveur](./media/sql-database-design-first-database/options-connect-to-db.png)  

   1. Cliquez sur **Connecter**. La fenêtre **Explorateur d’objets** s’ouvre dans SSMS.

1. Dans l’**Explorateur d’objets**, développez **Bases de données**, puis *yourDatabase* pour afficher les objets dans l’exemple de base de données.

   ![objets de base de données](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-the-database"></a>Créer des tables dans la base de données

Créez un schéma de base de données avec quatre tables qui modélisent un système de gestion des étudiants pour les universités à l’aide de [Transact-SQL](/sql/t-sql/language-reference) :

- Personne
- Cours
- Étudiant
- Crédit

Le diagramme suivant montre comment ces tables sont liées entre elles. Certaines de ces tables référencent des colonnes d’autres tables. Par exemple, la table *Student* référence la colonne *PersonId* de la table *Person*. Étudiez le diagramme pour comprendre comment les tables présentées dans ce didacticiel sont liées entre elles. Pour découvrir comment créer des tables de base de données efficaces, consultez [Créer des tables de base de données efficaces](https://msdn.microsoft.com/library/cc505842.aspx). Pour plus d’informations sur le choix des types de données, consultez [Types de données](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Vous pouvez également utiliser le [concepteur de tables dans SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) pour créer et concevoir vos tables.

![Relations entre les tables](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur *yourDatabase* et sélectionnez **Nouvelle requête**. Une fenêtre de requête vide connectée à votre base de données s’ouvre.

1. Dans la fenêtre de requête, exécutez la requête suivante pour créer quatre tables dans votre base de données :

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![créez des tables](./media/sql-database-design-first-database/create-tables.png)

1. Développez le nœud **Tables** sous *yourDatabase* dans l’**Explorateur d’objets** pour afficher les tables que vous avez créées.

   ![tables ssms-créées](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Charger des données dans les tables

1. Créez un dossier nommé *sampleData* dans le dossier Téléchargements pour y stocker les exemples de données pour votre base de données.

1. Cliquez avec le bouton droit sur les liens suivants et enregistrez-les dans le dossier *sampleData*.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

1. Ouvrez une fenêtre d’invite de commandes et accédez au dossier *sampleData*.

1. Exécutez les commandes suivantes pour insérer des exemples de données dans les tables, en remplaçant les valeurs de *server*, *database*, *user* et *password* par les valeurs correspondant à votre environnement.
  
   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Vous avez maintenant chargé des exemples de données dans les tables que vous avez créées précédemment.

## <a name="query-data"></a>Données de requête

Exécutez les requêtes suivantes pour récupérer des informations à partir des tables de base de données. Pour en savoir plus sur l’écriture des requêtes SQL, consultez [Écrire des requêtes SQL](https://technet.microsoft.com/library/bb264565.aspx). La première requête réunit les quatre tables pour rechercher tous les étudiants inscrits au cours de « Dominick Pope » ayant une note supérieure à 75 %. La deuxième requête réunit les quatre tables et recherche les cours que « Noe Coleman » a déjà suivis.

1. Dans une fenêtre de requête SQL Server Management Studio, exécutez la requête suivante :

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

1. Dans la fenêtre de requête, exécutez la requête suivante :

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert de nombreuses tâches de base de données élémentaires. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une base de données
> * Configurer une règle de pare-feu
> * Vous connecter à la base de données avec [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
> * créez des tables
> * Charger des données en bloc
> * Interroger ces données

Passez au didacticiel suivant pour en savoir plus sur la conception d’une base de données à l’aide de Visual Studio et C#.

> [!div class="nextstepaction"]
> [Concevoir une base de données SQL Azure et se connecter avec C# et ADO.NET](sql-database-design-first-database-csharp.md)
