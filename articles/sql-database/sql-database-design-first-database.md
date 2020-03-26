---
title: 'Tutoriel : Concevoir votre première base de données relationnelle avec SSMS'
description: Apprenez à concevoir votre première base de données relationnelle dans une base de données unique dans Azure SQL Database à l’aide de SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.openlocfilehash: 9764c4bc794eb8d133270b762fa2bca30a056fea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75459627"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Tutoriel : Concevoir une base de données relationnelle dans une base de données unique au sein d’Azure SQL Database avec SSMS

Azure SQL Database est une solution DBaaS relationnelle dans Microsoft Cloud (Azure). Dans ce didacticiel, vous allez apprendre à utiliser le portail Azure et [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) pour :

> [!div class="checklist"]
> - Créer une base de données unique à l’aide du portail Azure*
> - Configurer une règle de pare-feu IP au niveau du serveur à l’aide du portail Azure
> - Se connecter à la base de données avec SSMS
> - Créer des tables avec SSMS
> - Charger en masse des données avec BCP
> - Interroger des données avec SSMS

*Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

> [!TIP]
> Le module Microsoft Learn suivant vous aide à apprendre gratuitement comment [développer et configurer une application ASP.Net qui interroge une base de données Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), ce qui comprend la création d’une base de données simple.
> [!NOTE]
> Pour les besoins de ce tutoriel, nous utilisons une base de données unique. Vous pouvez également utiliser une base de données mise en pool élastique ou une base de données d’instance située dans une instance managée. Pour la connexion à une instance gérée, consultez ces guides de démarrage rapide des instances gérées : [Démarrage rapide : Configurer la machine virtuelle Azure pour qu’elle se connecte à Azure SQL Database Managed Instance](sql-database-managed-instance-configure-vm.md) et [Démarrage rapide : Configurer une connexion point à site sur Azure SQL Database Managed Instance à partir d’un emplacement local](sql-database-managed-instance-configure-p2s.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vérifiez que les éléments suivants sont installés :

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (dernière version)
- [BCP et SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (dernière version)

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-blank-single-database"></a>Créer une base de données unique vide.

Une base de données unique dans Azure SQL Database est créée avec un ensemble défini de ressources de calcul et de stockage. Cette base de données est créée dans un [groupe de ressources Azure](../azure-resource-manager/management/overview.md) et elle est gérée à l’aide d’un [serveur de base de données](sql-database-servers.md).

Suivez ces étapes pour créer une base de données unique vide.

1. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.
2. Dans la page **Nouveau**, sélectionnez **Bases de données** dans la section Place de marché Azure, puis cliquez sur **SQL Database** dans la section **Sélection**.

   ![créer une base de données vide](./media/sql-database-design-first-database/create-empty-database.png)

3. Remplissez le formulaire **Base de données SQL** avec les informations suivantes, comme indiqué dans l’illustration précédente :

    | Paramètre       | Valeur suggérée | Description |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nom de la base de données** | *yourDatabase* | Pour connaître les noms de bases de données valides, consultez [Identificateurs de base de données](/sql/relational-databases/databases/database-identifiers). |
    | **Abonnement** | *yourSubscription*  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
    | **Groupe de ressources** | *yourResourceGroup* | Pour les noms de groupe de ressources valides, consultez [Naming conventions](/azure/architecture/best-practices/resource-naming) (Conventions d’affectation de nom). |
    | **Sélectionner une source** | Base de données vide | Indique qu’une base de données vide doit être créée. |

4. Cliquez sur **Serveur** pour utiliser un serveur de base de données existant ou créez et configurez-en un. Sélectionnez un serveur existant ou cliquez sur **Créer un serveur** et remplissez le formulaire **Nouveau serveur** avec les informations suivantes :

    | Paramètre       | Valeur suggérée | Description |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nom du serveur** | Nom globalement unique | Pour les noms de serveur valides, consultez [Naming conventions](/azure/architecture/best-practices/resource-naming) (Conventions d’affectation de nom). |
    | **Connexion d’administrateur du serveur** | Nom valide | Pour connaître les noms de connexions valides, consultez [Identificateurs de base de données](/sql/relational-databases/databases/database-identifiers). |
    | **Mot de passe** | Mot de passe valide | Votre mot de passe doit comporter au moins huit caractères et contenir des caractères appartenant à trois des catégories suivantes : majuscules, minuscules, chiffres et caractères non alphanumériques. |
    | **Lieu** | Emplacement valide | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. Cliquez sur **Sélectionner**.
6. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service, le nombre de DTU ou de vCore et la quantité de stockage. Vous pouvez explorer les options concernant le nombre de DTU/vCore et le stockage disponible pour chaque niveau de service.

    Après avoir sélectionné le niveau de service, le nombre de DTU ou de vCore et la quantité de stockage, cliquez sur **Appliquer**.

7. Entrez un **Classement** pour la base de données vide (pour ce tutoriel, utilisez la valeur par défaut). Pour en savoir plus sur les classements, voir [Classements](/sql/t-sql/statements/collations)

8. Maintenant que vous avez rempli le formulaire **SQL Database**, cliquez sur **Créer** pour provisionner la base de données unique. Cette étape peut prendre quelques minutes.

9. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

   ![notification](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Créer une règle de pare-feu IP au niveau du serveur

Le service SQL Database crée un pare-feu IP au niveau du serveur. Ce pare-feu empêche les applications et outils externes de se connecter au serveur et à toutes les bases de données sur ce serveur, sauf si une règle de pare-feu autorise leur adresse IP à traverser le pare-feu. Pour activer la connectivité externe à votre base de données unique, vous devez d’abord ajouter une règle de pare-feu IP pour votre adresse IP (ou plage d’adresses IP). Suivez ces étapes pour créer une [règle de pare-feu IP au niveau du serveur SQL Database](sql-database-firewall-configure.md).

> [!IMPORTANT]
> Le service SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à ce service à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre base de données unique, sauf si votre administrateur ouvre le port 1433.

1. Une fois le déploiement terminé, sélectionnez **Bases de données SQL** dans le menu Portail Azure ou recherchez et sélectionnez *Bases de données SQL* à partir de n’importe quelle page.  

1. Sélectionnez *YourDatabase* dans la page **Bases de données SQL**. La page d’aperçu de votre base de données s’ouvre. Elle affiche le **Nom du serveur** complet (par exemple `contosodatabaseserver01.database.windows.net`) et fournit des options pour poursuivre la configuration.

   ![nom du serveur](./media/sql-database-design-first-database/server-name.png)

1. Copiez le nom complet du serveur pour vous connecter à votre serveur et aux bases de données à partir de SQL Server Management Studio.

1. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils. La page **Paramètres de pare-feu** du serveur SQL Database s’ouvre.

   ![règle de pare-feu IP au niveau du serveur](./media/sql-database-design-first-database/server-firewall-rule.png)

1. Dans la barre d’outils, cliquez sur **Ajouter une adresse IP cliente** afin d’ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu IP. Une règle de pare-feu IP peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

1. Cliquez sur **Enregistrer**. Une règle de pare-feu IP au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur SQL Database.

1. Cliquez sur **OK**, puis fermez la page **Paramètres de pare-feu**.

Votre adresse IP peut désormais traverser le pare-feu IP. Vous pouvez maintenant vous connecter à votre base de données unique à l’aide de SQL Server Management Studio ou tout autre outil de votre choix. Veillez à utiliser le compte d’administrateur de serveur que vous avez créé précédemment.

> [!IMPORTANT]
> Par défaut, l’accès par le biais du pare-feu IP SQL Database est activé pour tous les services Azure. Cliquez sur **ÉTEINT** sur cette page pour le désactiver pour tous les services Azure.

## <a name="connect-to-the-database"></a>Se connecter à la base de données

Utilisez [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) pour établir une connexion à votre base de données unique.

1. Ouvrez SQL Server Management Studio.
2. Dans la fenêtre **Se connecter au serveur**, entrez les valeurs suivantes :

   | Paramètre       | Valeur suggérée | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Type de serveur** | Moteur de base de données | Cette valeur est requise. |
   | **Nom du serveur** | Nom complet du serveur | Par exemple, *yourserver.database.windows.net*. |
   | **Authentification** | l’authentification SQL Server | L’authentification SQL est le seul type d’authentification que nous avons configuré dans ce tutoriel. |
   | **Connexion** | Compte d’administrateur de serveur | Le compte que vous avez spécifié lorsque vous avez créé le serveur. |
   | **Mot de passe** | Mot de passe de votre compte d’administrateur de serveur | Il s’agit du mot de passe que vous avez spécifié quand vous avez créé le serveur. |

   ![connect to server](./media/sql-database-design-first-database/connect.png)

3. Cliquez sur **Options** dans la boîte de dialogue **Se connecter au serveur**. Dans la section **Se connecter à la base de données**, entrez *yourDatabase* pour vous connecter à cette base de données.

    ![connexion à la base de données sur le serveur](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. Cliquez sur **Connecter**. La fenêtre **Explorateur d’objets** s’ouvre dans SSMS.

5. Dans l’**Explorateur d’objets**, développez **Bases de données**, puis *yourDatabase* pour afficher les objets dans l’exemple de base de données.

   ![objets de base de données](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Créer des tables dans votre base de données

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

2. Dans la fenêtre de requête, exécutez la requête suivante pour créer quatre tables dans votre base de données :

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

   ![Créer des tables](./media/sql-database-design-first-database/create-tables.png)

3. Développez le nœud **Tables** sous *yourDatabase* dans l’**Explorateur d’objets** pour afficher les tables que vous avez créées.

   ![tables ssms-créées](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Charger des données dans les tables

1. Créez un dossier nommé *sampleData* dans le dossier Téléchargements pour y stocker les exemples de données pour votre base de données.

2. Cliquez avec le bouton droit sur les liens suivants et enregistrez-les dans le dossier *sampleData*.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Ouvrez une fenêtre d’invite de commandes et accédez au dossier *sampleData*.

4. Exécutez les commandes suivantes pour insérer des exemples de données dans les tables, en remplaçant les valeurs de *server*, *database*, *user* et *password* par les valeurs correspondant à votre environnement.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Vous avez maintenant chargé des exemples de données dans les tables que vous avez créées précédemment.

## <a name="query-data"></a>Interroger des données

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

2. Dans la fenêtre de requête, exécutez la requête suivante :

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

Dans ce tutoriel, vous avez découvert de nombreuses tâches de base de données élémentaires. Vous avez appris à :

> [!div class="checklist"]
> - Créer une base de données unique
> - Configurer une règle de pare-feu IP au niveau du serveur
> - Vous connecter à la base de données avec [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)
> - Créer des tables
> - Charger des données en bloc
> - Interroger ces données

Passez au didacticiel suivant pour en savoir plus sur la conception d’une base de données à l’aide de Visual Studio et C#.

> [!div class="nextstepaction"]
> [Concevoir une base de données relationnelle dans une base de données unique au sein d’Azure SQL Database en C# avec ADO.NET](sql-database-design-first-database-csharp.md)
