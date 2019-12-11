---
title: Se connecter à IBM DB2
description: Gérer les ressources avec les API REST IBM DB2 et Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 3c2bb01254b19c42fdd704544a6812177fecf4ca
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789901"
---
# <a name="manage-ibm-db2-resources-with-azure-logic-apps"></a>Gérer les ressources IBM DB2 avec Azure Logic Apps

Avec Azure Logic Apps et le connecteur IBM DB2, vous pouvez créer des flux de travail et des tâches automatisés basés sur les ressources stockées dans votre base de données DB2. Vos flux de travail peuvent se connecter aux ressources dans votre base de données, lire et lister vos tables de base de données, ajouter des lignes, changer des lignes, supprimer des lignes, et bien plus encore. Vous pouvez inclure des actions dans vos applications logiques qui obtiennent des réponses de votre base de données et mettent la sortie à la disposition d’autres actions.

Cet article explique comment vous pouvez créer une application logique qui effectue différentes opérations de base de données. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Plateformes et versions prises en charge

Le connecteur DB2 inclut un client Microsoft qui communique avec les serveurs DB2 distants via un réseau TCP/IP. Vous pouvez utiliser ce connecteur pour accéder aux bases de données cloud telles qu’IBM DB2 pour Windows s’exécutant dans la virtualisation Azure. Vous pouvez également accéder à des bases de données DB2 locales après avoir [installé et configuré la passerelle de données locale](../logic-apps/logic-apps-gateway-connection.md).

Le connecteur IBM DB2 prend en charge les plateformes et versions IBM DB2 ci-après, ainsi que les produits IBM DB2 compatibles qui prennent en charge les versions 10 et 11 de SQL Access Manager (SQLAM) DRDA (Distributed Relational Database Architecture) :

| Plateforme | Version | 
|----------|---------|
| IBM DB2 pour z/OS | 11.1, 10.1 |
| IBM DB2 pour i | 7.3, 7.2, 7.1 |
| IBM DB2 pour LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Opérations de base de données prises en charge

Le connecteur IBM DB2 prend en charge les opérations de base de données ci-après, qui ont une action correspondante dans le connecteur :

| Opération de base de données | Action dans le connecteur |
|--------------------|------------------|
| énumération des tables de base de données ; | Obtenir des tables |
| lecture d’une ligne à l’aide de l’instruction SELECT ; | Obtenir une ligne |
| lecture de toutes les lignes à l’aide de l’instruction SELECT ; | Obtenir des lignes |
| ajout d’une ligne à l’aide de l’instruction INSERT ; | Insérer une ligne |
| Modifier une ligne à l’aide de l’instruction UPDATE | Mettre à jour une ligne |
| suppression d’une ligne à l’aide de l’instruction DELETE. | Supprimer la ligne |
|||

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* Une base de données IBM DB2, cloud ou locale

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à la base de données DB2. Ce connecteur ne fournit que des actions ; ainsi, pour démarrer votre application logique, sélectionnez un déclencheur distinct, par exemple, le déclencheur **Périodicité**.
Les exemples dans cet article utilisent le déclencheur **Périodicité**.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Ajouter une action DB2 : Obtenir les tables

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques, si elle n’est pas encore ouverte.

1. Sous le déclencheur, choisissez **Nouvelle étape**.

1. Dans la zone de recherche, entrez « db2 » comme filtre. Pour cet exemple, sous la liste des actions, sélectionnez cette action : **Obtenir les tables (préversion)**

   ![Action select](./media/connectors-create-api-db2/select-db2-action.png)

   Vous êtes maintenant invité à fournir les détails de connexion de votre base de données DB2.

1. Suivez la procédure permettant de créer des connexions pour des [bases de données cloud](#cloud-connection) ou celle permettant d’en créer pour des [bases de données locales](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Se connecter à des bases de données DB2 cloud

Pour configurer votre connexion, indiquez les détails de connexion ci-après quand vous y êtes invité, choisissez **Créer**, puis enregistrez votre application logique :

| Propriété | Obligatoire | Description |
|----------|----------|-------------|
| **Se connecter via une passerelle locale** | Non | Ne concerne que les connexions locales. |
| **Nom de connexion** | OUI | Le nom de votre connexion, par exemple, « MyLogicApp-DB2-connection » |
| **Serveur** | OUI | L’adresse ou alias (suivi d’un caractère deux-points et du numéro de port) de votre serveur DB2, par exemple, « myDB2server.cloudapp.net:50000 » <p><p>**Remarque**: Cette valeur est une chaîne qui représente une adresse TCP/IP ou un alias, au format IPv4 ou IPv6, suivi de deux-points et d'un numéro de port TCP/IP. |
| **Base de données** | OUI | Nom de votre base de données <p><p>**Remarque**: Cette valeur est une chaîne qui représente un nom de base de données relationnelle DRDA (RDBNAM) : <p>- DB2 pour z/OS accepte une chaîne de 16 octets où la propriété de base de données correspond à un emplacement « IBM DB2 pour z/OS ». <br>- DB2 pour i accepte une chaîne de 18 octets où la propriété de base de données correspond à une base de données relationnelle « IBM DB2 pour i ». <br>- DB2 pour LUW accepte une chaîne de 8 octets. |
| **Nom d’utilisateur** | OUI | Votre nom d’utilisateur pour la base de données <p><p>**Remarque**: Cette valeur est une chaîne dont la longueur est basée sur la base de données spécifique : <p><p>- DB2 pour z/OS accepte une chaîne de 8 octets. <br>- DB2 pour i accepte une chaîne de 10 octets. <br>- DB2 pour Linux ou UNIX accepte une chaîne de 8 octets. <br>- DB2 pour Windows accepte une chaîne de 30 octets. |
| **Mot de passe** | OUI | Votre mot de passe pour la base de données |
||||

Par exemple :

![Détails de connexion pour les bases de données cloud](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Se connecter à des bases de données DB2 locales

Avant de créer votre connexion, vous devez disposer de votre passerelle de données locale. Sinon, vous ne pouvez pas terminer la configuration de votre connexion. Si votre passerelle est installée, indiquez les détails de connexion ci-après, puis choisissez **Créer**.

| Propriété | Obligatoire | Description |
|----------|----------|-------------|
| **Se connecter via une passerelle locale** | OUI | S’applique quand vous souhaitez une connexion locale et affiche les propriétés de cette dernière. |
| **Nom de connexion** | OUI | Le nom de votre connexion, par exemple, « MyLogicApp-DB2-connection » | 
| **Serveur** | OUI | L’adresse ou alias (suivi d’un caractère deux-points et du numéro de port) de votre serveur DB2, par exemple, « myDB2server:50000 » <p><p>**Remarque**: Cette valeur est une chaîne qui représente une adresse TCP/IP ou un alias, au format IPv4 ou IPv6, suivi de deux-points et d'un numéro de port TCP/IP. |
| **Base de données** | OUI | Nom de votre base de données <p><p>**Remarque**: Cette valeur est une chaîne qui représente un nom de base de données relationnelle DRDA (RDBNAM) : <p>- DB2 pour z/OS accepte une chaîne de 16 octets où la propriété de base de données correspond à un emplacement « IBM DB2 pour z/OS ». <br>- DB2 pour i accepte une chaîne de 18 octets où la propriété de base de données correspond à une base de données relationnelle « IBM DB2 pour i ». <br>- DB2 pour LUW accepte une chaîne de 8 octets. |
| **Authentification** | OUI | Type d’authentification pour votre connexion, par exemple, « De base » <p><p>**Remarque**: Sélectionnez cette valeur dans la liste, qui comprend De base ou Windows (Kerberos). |
| **Nom d’utilisateur** | OUI | Votre nom d’utilisateur pour la base de données <p><p>**Remarque**: Cette valeur est une chaîne dont la longueur est basée sur la base de données spécifique : <p><p>- DB2 pour z/OS accepte une chaîne de 8 octets. <br>- DB2 pour i accepte une chaîne de 10 octets. <br>- DB2 pour Linux ou UNIX accepte une chaîne de 8 octets. <br>- DB2 pour Windows accepte une chaîne de 30 octets. |
| **Mot de passe** | OUI | Votre mot de passe pour la base de données |
| **Passerelle** | OUI | Nom de votre passerelle de données locale installée <p><p>**Remarque**: Sélectionnez cette valeur dans la liste, qui comprend toutes les passerelles de données installées dans votre abonnement Azure et votre groupe de ressources. |
||||

Par exemple :

![Détails de connexion pour les bases de données locales](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Afficher les tables de sortie

Pour exécuter votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur. Une fois que l’application logique a terminé de s’exécuter, vous pouvez voir la sortie de l’exécution.

1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

1. Sous **Résumé**, dans la section **Historique des exécutions**, sélectionnez l’exécution la plus récente, qui correspond au premier élément dans la liste.

   ![Afficher l’historique des exécutions](./media/connectors-create-api-db2/run-history.png)

1. Sous **Exécution d’application logique**, vous pouvez maintenant examiner l’état, les entrées et les sorties pour chaque étape de votre application logique.
Développez l’action **Obtenir les tables**.

   ![Développer l’action](./media/connectors-create-api-db2/expand-action-step.png)

1. Pour afficher les entrées, choisissez **Afficher les entrées brutes**.

1. Pour afficher les sorties, choisissez **Afficher les sorties brutes**.

   Les sorties incluent une liste de tables.

   ![Afficher les tables de sortie](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Obtenir une ligne

Pour extraire un seul enregistrement d’une table de base de données DB2, utilisez l’action **Obtenir la ligne** dans votre application logique. Cette action exécute une instruction `SELECT WHERE` DB2, par exemple, `SELECT FROM AREA WHERE AREAID = '99999'`.

1. Si vous n’avez jamais utilisé d’actions DB2 dans votre application logique, suivez les étapes de la section [Ajouter une action DB2 : Obtenir les tables](#add-db2-action), mais ajoutez l’action **Obtenir la ligne** à la place, puis revenez ici pour continuer.

   Une fois que vous avez ajouté l’action **Obtenir la ligne**, voici ce à quoi ressemble votre exemple d’application logique :

   ![Action Obtenir la ligne](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Spécifiez des valeurs pour toutes les propriétés obligatoires (*). Une fois que vous avez sélectionné une table, l’action affiche les propriétés spécifiques aux enregistrements de cette table.

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de la table** | OUI | Table qui contient l’enregistrement souhaité, par exemple, « AREA » (Zone) ici |
   | **ID de zone** | OUI | ID de l’enregistrement souhaité, par exemple, « 99999 » ici |
   ||||

   ![Sélectionner une table](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.

### <a name="view-output-row"></a>Afficher la ligne de sortie

Pour exécuter votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur. Une fois que l’application logique a terminé de s’exécuter, vous pouvez voir la sortie de l’exécution.

1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

1. Sous **Résumé**, dans la section **Historique des exécutions**, sélectionnez l’exécution la plus récente, qui correspond au premier élément dans la liste.

1. Sous **Exécution d’application logique**, vous pouvez maintenant examiner l’état, les entrées et les sorties pour chaque étape de votre application logique.
Développez l’action **Obtenir la ligne**.

1. Pour afficher les entrées, choisissez **Afficher les entrées brutes**.

1. Pour afficher les sorties, choisissez **Afficher les sorties brutes**.

   Les sorties incluent la ligne que vous avez spécifiée.

   ![Afficher la ligne de sortie](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Obtenir des lignes

Pour extraire tous les enregistrements d’une table de base de données DB2, utilisez l’action **Obtenir les lignes** dans votre application logique. Cette action exécute une instruction `SELECT` DB2, par exemple, `SELECT * FROM AREA`.

1. Si vous n’avez jamais utilisé d’actions DB2 dans votre application logique, suivez les étapes de la section [Ajouter une action DB2 : Obtenir les tables](#add-db2-action), mais ajoutez l’action **Obtenir les lignes** à la place, puis revenez ici pour continuer.

   Une fois que vous avez ajouté l’action **Obtenir les lignes**, voici ce à quoi ressemble votre exemple d’application logique :

   ![Action Obtenir les lignes](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Ouvrez la liste **Nom de la table**, puis sélectionnez la table souhaitée, en l’occurrence « AREA » (Zone) :

   ![Sélectionner une table](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Pour spécifier un filtre ou une requête pour les résultats, choisissez **Afficher les options avancées**.

1. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.

### <a name="view-output-rows"></a>Afficher les lignes de sortie

Pour exécuter votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur. Une fois que l’application logique a terminé de s’exécuter, vous pouvez voir la sortie de l’exécution.

1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

1. Sous **Résumé**, dans la section **Historique des exécutions**, sélectionnez l’exécution la plus récente, qui correspond au premier élément dans la liste.

1. Sous **Exécution d’application logique**, vous pouvez maintenant examiner l’état, les entrées et les sorties pour chaque étape de votre application logique.
Développez l’action **Obtenir les lignes**.

1. Pour afficher les entrées, choisissez **Afficher les entrées brutes**.

1. Pour afficher les sorties, choisissez **Afficher les sorties brutes**.

   Les sorties incluent tous les enregistrements de la table spécifiée.

   ![Afficher les lignes de sortie](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Insérer une ligne

Pour ajouter un seul enregistrement à une table de base de données DB2, utilisez l’action **Insérer une ligne** dans votre application logique. Cette action exécute une instruction `INSERT` DB2, par exemple, `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

1. Si vous n’avez jamais utilisé d’actions DB2 dans votre application logique, suivez les étapes de la section [Ajouter une action DB2 : Obtenir les tables](#add-db2-action), mais ajoutez l’action **Insérer une ligne** à la place, puis revenez ici pour continuer.

   Une fois que vous avez ajouté l’action **Insérer une ligne**, voici ce à quoi ressemble votre exemple d’application logique :

   ![Action Insérer une ligne](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Spécifiez des valeurs pour toutes les propriétés obligatoires (*). Une fois que vous avez sélectionné une table, l’action affiche les propriétés spécifiques aux enregistrements de cette table.

   Pour cet exemple, voici les propriétés :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de la table** | OUI | Table à laquelle ajouter l’enregistrement, telle que « AREA » (Zone) |
   | **ID de zone** | OUI | ID de la zone à ajouter, tel que « 99999 » |
   | **Description de la zone** | OUI | Description de la zone à ajouter, par exemple « Area 99999 » (Zone 99999) |
   | **ID de région** | OUI | ID de la région à ajouter, tel que « 102 » |
   |||| 

   Par exemple :

   ![Sélectionner une table](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.

### <a name="view-insert-row-outputs"></a>Afficher les sorties d’une insertion de ligne

Pour exécuter votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur. Une fois que l’application logique a terminé de s’exécuter, vous pouvez voir la sortie de l’exécution.

1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

1. Sous **Résumé**, dans la section **Historique des exécutions**, sélectionnez l’exécution la plus récente, qui correspond au premier élément dans la liste.

1. Sous **Exécution d’application logique**, vous pouvez maintenant examiner l’état, les entrées et les sorties pour chaque étape de votre application logique.
Développez l’action **Insérer une ligne**.

1. Pour afficher les entrées, choisissez **Afficher les entrées brutes**.

1. Pour afficher les sorties, choisissez **Afficher les sorties brutes**.

   Les sorties incluent l’enregistrement que vous avez ajouté à la table spécifiée.

   ![Afficher la sortie avec la ligne insérée](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Mettre à jour une ligne

Pour mettre à jour un seul enregistrement dans une table de base de données DB2, utilisez l’action **Mettre à jour la ligne** dans votre application logique. Cette action exécute une instruction `UPDATE` DB2, par exemple, `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`.

1. Si vous n’avez jamais utilisé d’actions DB2 dans votre application logique, suivez les étapes de la section [Ajouter une action DB2 : Obtenir les tables](#add-db2-action), mais ajoutez l’action **Mettre à jour la ligne** à la place, puis revenez ici pour continuer.

   Une fois que vous avez ajouté l’action **Mettre à jour la ligne**, voici ce à quoi ressemble votre exemple d’application logique :

   ![Action Mettre à jour la ligne](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Spécifiez des valeurs pour toutes les propriétés obligatoires (*). Une fois que vous avez sélectionné une table, l’action affiche les propriétés spécifiques aux enregistrements de cette table.

   Pour cet exemple, voici les propriétés :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de la table** | OUI | Table dans laquelle mettre à jour l’enregistrement, telle que « AREA » (Zone) |
   | **ID de ligne** | OUI | ID de l’enregistrement à mettre à jour, tel que « 99999 » |
   | **ID de zone** | OUI | Nouvel ID de zone, tel que « 99999 » |
   | **Description de la zone** | OUI | Nouvelle description de la zone, telle que « Updated 99999 » (99999 mise à jour) |
   | **ID de région** | OUI | Nouvel ID de région, tel que « 102 » |
   ||||

   Par exemple :

   ![Sélectionner une table](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.

### <a name="view-update-row-outputs"></a>Afficher les sorties d’une mise à jour de ligne

Pour exécuter votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur. Une fois que l’application logique a terminé de s’exécuter, vous pouvez voir la sortie de l’exécution.

1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

1. Sous **Résumé**, dans la section **Historique des exécutions**, sélectionnez l’exécution la plus récente, qui correspond au premier élément dans la liste.

1. Sous **Exécution d’application logique**, vous pouvez maintenant examiner l’état, les entrées et les sorties pour chaque étape de votre application logique.
Développez l’action **Mettre à jour la ligne**.

1. Pour afficher les entrées, choisissez **Afficher les entrées brutes**.

1. Pour afficher les sorties, choisissez **Afficher les sorties brutes**.

   Les sorties incluent l’enregistrement que vous avez mis à jour dans la table spécifiée.

   ![Afficher la sortie avec la ligne mise à jour](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Supprimer la ligne

Pour supprimer un seul enregistrement d’une table de base de données DB2, utilisez l’action **Supprimer la ligne** dans votre application logique. Cette action exécute une instruction `DELETE` DB2, par exemple, `DELETE FROM AREA WHERE AREAID = '99999'`.

1. Si vous n’avez jamais utilisé d’actions DB2 dans votre application logique, suivez les étapes de la section [Ajouter une action DB2 : Obtenir les tables](#add-db2-action), mais ajoutez l’action **Supprimer la ligne** à la place, puis revenez ici pour continuer.

   Une fois que vous avez ajouté l’action **Supprimer la ligne**, voici ce à quoi ressemble votre exemple d’application logique :

   ![Action Supprimer la ligne](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Spécifiez des valeurs pour toutes les propriétés obligatoires (*). Une fois que vous avez sélectionné une table, l’action affiche les propriétés spécifiques aux enregistrements de cette table.

   Pour cet exemple, voici les propriétés :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de la table** | OUI | Table de laquelle supprimer l’enregistrement, telle que « AREA » (Zone) |
   | **ID de ligne** | OUI | ID de l’enregistrement à supprimer, tel que « 99999 » |
   ||||

   Par exemple :

   ![Sélectionner une table](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.

### <a name="view-delete-row-outputs"></a>Afficher les sorties d’une suppression de ligne

Pour exécuter votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur. Une fois que l’application logique a terminé de s’exécuter, vous pouvez voir la sortie de l’exécution.

1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

1. Sous **Résumé**, dans la section **Historique des exécutions**, sélectionnez l’exécution la plus récente, qui correspond au premier élément dans la liste.

1. Sous **Exécution d’application logique**, vous pouvez maintenant examiner l’état, les entrées et les sorties pour chaque étape de votre application logique.
Développez l’action **Supprimer la ligne**.

1. Pour afficher les entrées, choisissez **Afficher les entrées brutes**.

1. Pour afficher les sorties, choisissez **Afficher les sorties brutes**.

   Les sorties n’incluent plus l’enregistrement que vous avez supprimé de la table spécifiée.

   ![Afficher la sortie sans la ligne supprimée](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, notamment sur les déclencheurs, les actions et les limites, comme décrit dans le fichier OpenAPI (anciennement Swagger) du connecteur, consultez la [page de référence du connecteur](/connectors/db2/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
