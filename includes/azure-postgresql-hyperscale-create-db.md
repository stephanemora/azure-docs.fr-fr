---
title: Fichier Include
description: Fichier Include
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 05/14/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c07e352288d7dc1d0bf198fd74c8baaded3a2d23
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177306"
---
Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Créer un serveur Azure Database pour PostgreSQL - Hyperscale (Citus)

Pour créer un serveur de base de données Azure pour PostgreSQL, suivez les étapes ci-après :
1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Bases de données** dans la page **Nouveau**, puis **Base de données Azure pour PostgreSQL** dans la page **Bases de données**.
3. Pour l’option de déploiement, cliquez sur le bouton **Créer** sous **Groupe de serveurs Hyperscale (Citus) - PRÉVERSION.**
4. Remplissez le formulaire de détails du nouveau serveur avec les informations suivantes :
   - Groupe de ressources : cliquez sur le lien **Créer** sous la zone de texte pour ce champ. Entrez un nom tel que **myresourcegroup**.
   - Nom du groupe de serveurs : entrez un nom unique pour le nouveau groupe de serveurs, qui sera également utilisé pour un sous-domaine de serveur.
   - Nom de l'utilisateur administrateur : valeur **citus** actuellement requise ; ne peut pas être modifiée.
   - Mot de passe : doit contenir au moins huit caractères appartenant à trois des catégories suivantes : lettres majuscules non accentuées, lettres minuscules non accentuées, chiffres (de 0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).
   - Emplacement : utilisez l’emplacement le plus proche de vos utilisateurs pour leur donner l’accès le plus rapide possible aux données.

   > [!IMPORTANT]
   > Le mot de passe d'administration du serveur que vous spécifiez ici est requis pour se connecter au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.

5. Cliquez sur **Configurer le groupe de serveurs**. Laissez inchangés les paramètres de cette section et cliquez sur **Enregistrer**.
6. Cliquez sur **Vérifier + créer**, puis sur **Créer** pour provisionner le serveur. Le provisionnement prend quelques minutes.
7. La page redirige vers la supervision du déploiement. Quand l’état passe de **Votre déploiement est en cours** à **Votre déploiement a été effectué**, cliquez sur l’élément de menu **Sorties** à gauche de la page.
8. La page des sorties contient un nom d’hôte de coordinateur avec un bouton en regard de celui-ci pour copier la valeur dans le Presse-papiers. Prenez note de ces informations pour une utilisation ultérieure.

## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur

Le service Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion) utilise un pare-feu au niveau du serveur. Par défaut, le pare-feu empêche les outils et les applications externes de se connecter au nœud coordinateur et aux bases de données qui s’y trouvent. Nous devons ajouter une règle pour ouvrir le pare-feu pour une plage d’adresses IP spécifiques.

1. Dans la section **Sorties** où vous avez précédemment copié le nom d’hôte du nœud coordinateur, recliquez sur l’élément de menu **Vue d’ensemble**.

2. Recherchez le nom du groupe de serveurs de votre déploiement et cliquez dessus. (Le nom du groupe de serveurs ne comportera *pas* de suffixe. Les éléments dont le nom se termine par exemple par « -c », « -w0 », ou « -w1 » ne correspondent pas au groupe de serveurs.)

3. Cliquez sur **Pare-feu** sous **Sécurité** dans le menu de gauche.

4. Cliquez sur le lien **+ Ajouter une règle de pare-feu pour l’adresse IP du client**.

5. Enfin, cliquez sur le bouton **Enregistrer**.

   > [!NOTE]
   > Le serveur Azure PostgreSQL communique sur le port 5432. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 5432 peut être bloqué par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 5432.
   >

## <a name="connect-to-the-database-using-psql"></a>Se connecter à la base de données à l'aide de psql

Lorsque vous créez votre serveur Azure Database pour PostgreSQL, une base de données par défaut nommée **citus** est créée. Pour vous connecter à votre serveur de base de données, vous devez disposer d'une chaîne de connexion et du mot de passe d'administration.

1. Récupérez la chaîne de connexion. Sur la page du groupe de serveurs, cliquez sur l'élément de menu **Chaînes de connexion**. (Celui-ci se trouve sous **Paramètres**.) Recherchez la chaîne marquée **C++ (libpq)** . Celle-ci se présente sous la forme suivante :

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Copiez la chaîne. Vous devez remplacer « {votre\_mot de passe} » par le mot de passe d'administration que vous avez choisi précédemment. Comme le système ne stocke pas votre mot de passe en clair, il ne peut pas l'afficher pour vous dans la chaîne de connexion.

2. Ouvrez une fenêtre de terminal sur votre ordinateur local.

3. À l'invite, connectez-vous à votre serveur Azure Database pour PostgreSQL à l'aide de l'utilitaire [psql](https://www.postgresql.org/docs/current/app-psql.html). Passez votre chaîne de connexion entre guillemets, en vous assurant qu'elle contient bien votre mot de passe :
   ```bash
   psql "{connection_string}"
   ```

   Par exemple, la commande suivante vous connecte au nœud coordinateur du groupe de serveurs **mydemoserver** :

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
