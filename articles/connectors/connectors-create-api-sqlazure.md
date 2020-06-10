---
title: Se connecter à SQL Server ou Azure SQL Database
description: Automatiser des tâches pour les bases de données SQL localement ou dans le cloud à l’aide d’Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/12/2020
tags: connectors
ms.openlocfilehash: f63553ced8484b3ce328fb9537d5831ae1e27fe8
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191490"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatiser les workflows pour SQL Server ou Azure SQL Database à l’aide d’Azure Logic Apps

Cet article explique comment accéder aux données dans votre base de données SQL depuis une application logique avec le connecteur SQL Server. Vous pouvez ainsi automatiser des tâches, des processus ou des workflows qui gèrent vos données et ressources SQL en créant des applications logiques. Le connecteur SQL Server fonctionne pour [SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation), ainsi que [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) et [Azure SQL Managed instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).

Vous pouvez créer des applications logiques qui s’exécutent lorsqu’elles sont déclenchées par des événements dans votre base de données SQL ou dans d’autres systèmes, tels que Dynamics CRM Online. Vos applications logiques peuvent également obtenir, insérer et supprimer des données, ainsi qu’exécuter des requêtes SQL et des procédures stockées. Par exemple, vous pouvez concevoir une application logique qui recherche automatiquement les nouveaux enregistrements dans Dynamics CRM Online, ajoute des éléments à votre base de données SQL en cas de nouveaux enregistrements, puis envoie des alertes sur les éléments ajoutés par e-mail.

Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour obtenir les informations techniques, les limitations et les problèmes connus spécifiques aux connecteurs, consultez la page [référence du connecteur SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).

* Une [base de données SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) ou une [base de données SQL Azure](../azure-sql/database/single-database-create-quickstart.md)

  Vos tables doivent contenir des données afin que votre application logique puisse renvoyer les résultats lors de l’appel des opérations. Si vous créez une base de données Azure SQL, vous pouvez utiliser les exemples de bases de données inclus.

* Le nom de votre serveur SQL, le nom de la base de données, votre nom d’utilisateur et votre mot de passe. Vous avez besoin de ces informations d’identification pour autoriser votre application logique à accéder à votre serveur SQL.

  * Pour SQL Server, vous trouverez ces informations dans la chaîne de connexion :

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Pour Azure SQL Database, vous trouverez ces informations dans la chaîne de connexion, ou dans le portail Azure sous les propriétés de base de données SQL :

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* La [passerelle de données locale](../logic-apps/logic-apps-gateway-install.md) installée sur un ordinateur local et une [ressource de passerelle de données Azure créée sur le Portail Azure](../logic-apps/logic-apps-gateway-connection.md) dans ces scénarios :

  * Les applications logiques ne s’exécutent pas dans un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Les applications logiques *s’exécutent* bien dans un environnement de service d’intégration, mais vous devez utiliser l’authentification Windows pour votre connexion à SQL Server. Dans ce scénario, utilisez la version non ISE du connecteur SQL Server ainsi que la passerelle de données, car la version ISE ne prend pas en charge l’authentification Windows.

* L’application logique où vous devez avoir accès à votre base de données SQL. Pour démarrer votre application logique avec un déclencheur SQL, vous avez besoin d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Ajouter un déclencheur SQL

Dans Azure Logic Apps, chaque application logique doit démarrer avec un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique et lance l’exécution du flux de travail de votre application logique.

1. Dans le portail Azure ou Visual Studio, créez une application logique vide, qui ouvre le Concepteur d'applications logiques. Cet exemple utilise le portail Azure.

1. Dans la zone de recherche du Concepteur, entrez le filtre « sql server ». Dans la liste des déclencheurs, sélectionnez le déclencheur SQL souhaité.

   Cet exemple utilise le déclencheur **Lorsqu'un élément est créé**.

   ![Sélectionner le déclencheur « Lorsqu'un élément est créé »](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Si vous êtes invité à créer une connexion, [créez votre connexion SQL maintenant](#create-connection). Si votre connexion existe, sélectionnez un **Nom de table**.

   ![Sélectionnez la table de votre choix](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Définissez les propriétés **Intervalle** et **Fréquence** qui spécifient la fréquence à laquelle votre application logique vérifie la table.

   Ce déclencheur ne renvoie qu’une seule ligne de la table sélectionnée. Pour effectuer d’autres tâches, ajoutez d’autres actions qui effectuent les tâches de votre choix. Par exemple, pour afficher les données de cette ligne, vous pouvez ajouter d’autres actions qui créent un fichier qui comprend les champs de la ligne retournée, puis envoient des alertes par e-mail. Pour en savoir plus sur les autres actions disponibles pour ce connecteur, consultez la [page de référence du connecteur](https://docs.microsoft.com/connectors/sql/).

1. Lorsque c’est chose faite, dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

   Cette étape active et publie automatiquement votre application logique dans Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Ajouter une action SQL

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action. Dans cet exemple, l’application logique commence par le [déclencheur de périodicité](../connectors/connectors-native-recurrence.md) et appelle une action qui reçoit une ligne d’une base de données SQL.

1. Dans le portail Azure ou Visual Studio, ouvrez votre application logique dans le Concepteur d’applications logiques. Cet exemple utilise le portail Azure.

1. Sous le déclencheur auquel/l’action à laquelle vous souhaitez ajouter l’action SQL, sélectionnez **Nouvelle étape**.

   ![Ajouter une nouvelle étape à une application logique](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Pour ajouter une action entre des étapes, déplacez votre souris sur la flèche de connexion. Cliquez sur le signe ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Sous **Choisir une action**, dans la zone de recherche, entrez « sql server » en guise de filtre. Dans la liste des actions, sélectionnez l’action SQL souhaitée.

   Cet exemple utilise l’action **Obtenir la ligne**, qui obtient un enregistrement unique.

   ![Rechercher et sélectionner l’action SQL « Obtenir la ligne »](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Cette action ne renvoie qu’une seule ligne de la table sélectionnée. Pour afficher les données de cette ligne, vous pouvez ajouter d’autres actions qui créent un fichier qui comprend les champs de la ligne retournée, et stocker ce fichier dans un compte de stockage cloud. Pour en savoir plus sur les autres actions disponibles pour ce connecteur, consultez la [page de référence du connecteur](https://docs.microsoft.com/connectors/sql/).

1. Si vous êtes invité à créer une connexion, [créez votre connexion SQL maintenant](#create-connection). Si vous avez établi une connexion, sélectionnez un **nom de table**, puis entrez l’**ID de ligne** correspondant à l’enregistrement souhaité.

   ![Entrez le nom de la table et l’ID de ligne.](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Lorsque c’est chose faite, dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

   Cette étape active et publie automatiquement votre application logique dans Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Connectez-vous à votre base de données

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Gérer des données en bloc

Parfois, vous manipulez des jeux de résultats tellement volumineux que le connecteur ne peut pas renvoyer tous les résultats en même temps, ou vous souhaitez mieux contrôler la taille et la structure de vos jeux de résultats. Voici quelques façons de gérer ces grands jeux de résultats :

* Pour mieux gérer les résultats sous forme de jeux plus petits, activez la *pagination*. Pour plus d’informations, voir [Obtenir des données en bloc, des enregistrements et des éléments à l’aide de la pagination](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Créez une procédure stockée qui trie les résultats comme vous le souhaitez.

  Lorsque vous procédez à l’extraction ou à l’insertion de plusieurs lignes, votre application logique peut effectuer une itération dans ces lignes en utilisant une [*boucle Until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dans ces [limites](../logic-apps/logic-apps-limits-and-config.md). Toutefois, lorsque votre application logique doit manipuler des jeux d’enregistrements si volumineux (plusieurs milliers ou millions de lignes, par exemple), vous devez réduire les coûts liés aux appels à la base de données.

  Pour organiser les résultats à votre convenance, vous pouvez créer une [*procédure stockée*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) qui s’exécute dans votre instance SQL et utilise l’instruction **SELEC  - ORDER BY**. Cette solution vous permet de déterminer la taille et la structure de vos résultats. Votre application logique appelle la procédure stockée à l’aide de l’action **Exécuter la procédure stockée** du connecteur SQL Server.

  Pour en savoir plus sur la solution, consultez les articles suivants :

  * [Pagination de SQL pour le transfert de données en bloc avec Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - Clause ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>Gérer les données en bloc dynamiques

Parfois, lorsque vous effectuez un appel à une procédure stockée dans le connecteur SQL Server, la sortie retournée est dynamique. Dans ce scénario, procédez comme suit :

1. Ouvrez **Concepteur Logic Apps**.
1. Exécutez une série de tests de votre application logique pour afficher le format de sortie. Copiez votre exemple de sortie.
1. Dans le concepteur, sous l’action où vous appelez la procédure stockée, sélectionnez **Nouvelle étape**.
1. Sous **Choisir une action**, recherchez et sélectionnez l’action [**Analyser JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).
1. Dans l’option **Analyser JSON**, sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**.
1. Dans la fenêtre **Entrer ou coller un exemple de charge utile JSON**, collez votre exemple de sortie, puis sélectionnez **Terminé**.
1. Si vous recevez une erreur indiquant que Logic Apps ne peut pas générer de schéma, vérifiez que la syntaxe de votre exemple de sortie est correctement mise en forme. Si vous ne pouvez toujours pas générer le schéma, entrez-en un manuellement dans la zone **Schéma**.
1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.
1. Pour accéder aux propriétés du contenu JSON, utilisez les jetons de données qui s’affichent dans la liste de contenu dynamique sous l’[action **Analyser JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Pour obtenir des informations techniques sur les déclencheurs, les actions et les limites de ce connecteur, consultez la page [Informations de référence du connecteur](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [tous les autres connecteurs pour Azure Logic apps](../connectors/apis-list.md)
