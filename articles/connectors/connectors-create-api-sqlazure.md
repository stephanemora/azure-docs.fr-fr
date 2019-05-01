---
title: Se connecter à SQL Server ou Azure SQL Database - Azure Logic Apps | Microsoft Docs
description: Comment accéder à des bases de données SQL locales ou dans le cloud et les gérer en automatisant des workflows avec Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 998fcba50636cd92b14bdbe1633c2548e84a6bfc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696421"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Se connecter à SQL Server ou Azure SQL Database depuis Azure Logic Apps

Cet article explique comment accéder aux données dans votre base de données SQL depuis une application logique avec le connecteur SQL Server. Vous pouvez ainsi automatiser des tâches, des processus et des workflows qui gèrent vos données et ressources SQL en créant des applications logiques. Le connecteur fonctionne pour [SQL Server sur site](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) et [Azure SQL Database dans le cloud](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Vous pouvez créer des applications logiques qui s’exécutent lorsqu’elles sont déclenchées par des événements dans votre base de données SQL ou dans d’autres systèmes, tels que Dynamics CRM Online. Vos applications logiques peuvent également obtenir, insérer et supprimer des données, ainsi qu’exécuter des requêtes SQL et des procédures stockées. Par exemple, vous pouvez concevoir une application logique qui recherche automatiquement les nouveaux enregistrements dans Dynamics CRM Online, ajoute des éléments à votre base de données SQL en cas de nouveaux enregistrements, puis envoie des alertes par e-mail.

Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour obtenir des informations techniques spécifiques aux connecteurs, consultez la <a href="https://docs.microsoft.com/connectors/sql/" target="blank">référence du connecteur SQL Server</a>.

## <a name="prerequisites"></a>Conditions préalables

* L’application logique où vous devez avoir accès à votre base de données SQL. Pour démarrer votre application logique avec un déclencheur SQL, vous avez besoin d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Une [base de données SQL Azure](../sql-database/sql-database-get-started-portal.md) ou une [base de données SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Vos tables doivent contenir des données afin que votre application logique puisse renvoyer les résultats lors de l’appel des opérations. Si vous créez une base de données SQL Azure, vous pouvez utiliser les exemples de bases de données inclus. 

* Le nom de votre serveur SQL, le nom de la base de données, votre nom d’utilisateur et votre mot de passe. Vous avez besoin de ces informations d’identification pour autoriser votre application logique à accéder à votre serveur SQL. 

  * Pour Azure SQL Database, vous trouverez ces informations dans la chaîne de connexion, ou dans le portail Azure sous les propriétés de base de données SQL :

    "Server=tcp:<*yourServerName*>.database.windows.net,1433;Initial Catalog=<*yourDatabaseName*>;Persist Security Info=False;User ID=<*yourUserName*>;Password=<*yourPassword*>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

  * Pour SQL Server, vous trouverez ces informations dans la chaîne de connexion : 

    "Server=<*yourServerAddress*>;Database=<*yourDatabaseName*>;User Id=<*yourUserName*>;Password=<*yourPassword*>;"

* Avant de pouvoir connecter les applications logiques aux systèmes sue site, tels que SQL Server, vous devez [configurer une passerelle de données locale](../logic-apps/logic-apps-gateway-install.md). De cette façon, vous pouvez sélectionner la passerelle lorsque vous créez la connexion SQL pour votre application logique.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Ajouter un déclencheur SQL

Dans Azure Logic Apps, chaque application logique doit démarrer avec un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique et lance l’exécution du flux de travail de votre application.

1. Dans le portail Azure ou Visual Studio, créez une application logique vide, qui ouvre le Concepteur d'applications logiques. Cet exemple utilise le portail Azure.

2. Dans la zone de recherche, saisissez le filtre « sql server ». Dans la liste des déclencheurs, sélectionnez le déclencheur SQL souhaité. 

   Pour cet exemple, sélectionnez ce déclencheur : **SQL Server - quand un élément est créé.**

   ![Sélectionner le déclencheur « SQL Server - Lorsqu'un élément est créé »](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion SQL maintenant](#create-connection). 
   Ou, si votre connexion existe déjà, sélectionnez le **nom de la table** souhaité dans la liste.

   ![Sélectionner une table](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Définissez les propriétés **Intervalle** et **Fréquence** qui spécifient la fréquence à laquelle votre application logique vérifie la table.

   Cet exemple vérifie uniquement la table sélectionnée. 
   Pour aller plus loin, ajoutez des actions qui effectuent les tâches qui vous intéressent. 
   
   Par exemple, pour afficher le nouvel élément dans la table, vous pouvez ajouter d’autres actions, comme créer un fichier qui comporte des champs de la table, puis envoyer des alertes par e-mail. 
   Pour en savoir plus sur les autres actions possibles avec ce connecteur ou d’autres, consultez la section [Connecteurs d’applications logiques](../connectors/apis-list.md).

5. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**. 

   Cette étape active et publie automatiquement votre application logique dans Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Ajouter une action SQL

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action. Dans cet exemple, l’application logique commence par le [déclencheur de périodicité](../connectors/connectors-native-recurrence.md) et appelle une action qui reçoit une ligne d’une base de données SQL.

1. Dans le portail Azure ou Visual Studio, ouvrez votre application logique dans le Concepteur d’applications logiques. Cet exemple utilise le portail Azure.

2. Dans le Concepteur d’application logique, sous le déclencheur ou l’action, sélectionnez **Nouvelle étape** > **Ajouter une action**.

   ![Choisissez « Nouvelle étape », « Ajouter une action ».](./media/connectors-create-api-sqlazure/add-action.png)
   
   Pour ajouter une action entre des étapes, déplacez votre souris sur la flèche de connexion. 
   Cliquez sur le signe plus (**+**) qui s’affiche, puis choisissez **Ajouter une action**.

2. Dans la zone de recherche, saisissez le filtre « sql server ». Dans la liste des actions, sélectionnez les actions SQL souhaitées. 

   Pour cet exemple, sélectionnez cette action, qui obtient un seul enregistrement : **SQL Server - obtenir une ligne**

   ![Entrez « sql server », sélectionnez « SQL Server - Obtenir une ligne ».](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion SQL maintenant](#create-connection). 
   Ou, si vous avez établi une connexion, sélectionnez un **nom de table**, puis entrez l’**ID de ligne** correspondant à l’enregistrement souhaité.

   ![Entrez le nom de la table et l’ID de ligne.](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   Cet exemple ne renvoie qu’une seule ligne de la table sélectionnée. 
   Pour afficher les données de cette ligne, vous pouvez ajouter d’autres actions qui créent un fichier avec les champs de la ligne pour consultation ultérieure, et stocker ce fichier dans un compte de stockage cloud. Pour en savoir plus sur les autres actions possibles avec ce connecteur ou d’autres, consultez la section [Connecteurs d’applications logiques](../connectors/apis-list.md).

4. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Connectez-vous à votre base de données

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Gérer des données en bloc

Parfois, vous devrez peut-être travailler avec des jeux de résultats tellement important que le connecteur ne retourne pas tous les résultats en même temps, ou que vous souhaitez mieux contrôler la taille et la structure pour vos jeux de résultats. Voici quelques méthodes que vous pouvez gérer ces grands jeux de résultats :

* Pour vous aider à gérer les résultats sous forme de jeux plus petits, allumez *la pagination*. Pour plus d’informations, consultez [obtenir des données en bloc, les enregistrements et les éléments à l’aide de la pagination](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Créer une procédure stockée qui trie les résultats comme vous le souhaitez.

  Durant l’obtention ou insertion de plusieurs lignes, votre application logique peut parcourir ces lignes à l’aide un [ *boucle until* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dans ces [limites](../logic-apps/logic-apps-limits-and-config.md). 
  Toutefois, lorsque votre application logique doit utiliser des jeux d’enregistrements tellement important, par exemple, des milliers ou des millions de lignes, que vous souhaitez réduire les coûts résultant des appels vers la base de données.

  Pour organiser les résultats dans comme vous le souhaitez, vous pouvez créer un [ *procédure stockée* ](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) qui s’exécute dans votre instance SQL et utilise le **SELECT - clause ORDER BY** instruction. 
  Cette solution vous permet de déterminer la taille et la structure de vos résultats. 
  Votre application logique appelle la procédure stockée à l’aide de l’action **Exécuter la procédure stockée** du connecteur SQL Server.

  Pour en savoir plus sur la solution, consultez les articles suivants :

  * [Pagination de SQL pour le transfert de données en bloc avec Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - Clause ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Pour obtenir des informations techniques sur les déclencheurs, les actions et les limites de ce connecteur, consultez la section [Informations de référence du connecteur](/connectors/sql/). 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)

