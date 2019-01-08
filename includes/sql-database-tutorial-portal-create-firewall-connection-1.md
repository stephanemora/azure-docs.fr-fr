---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: ab31ee82e8035fe888fa70b5796aef2c2b2939b2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728539"
---
## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/)

## <a name="create-a-blank-sql-database"></a>Créer une base de données SQL vide

Une base de données Azure SQL Database existe dans un ensemble défini de [ressources de calcul et de stockage](../articles/sql-database/sql-database-service-tiers-dtu.md). La base de données fonctionne sous un [groupe de ressources Azure](../articles/azure-resource-manager/resource-group-overview.md) et un [serveur logique de base de données Azure SQL Database](../articles/sql-database/sql-database-features.md).

Pour créer une base de données SQL vide, suivez la procédure suivante.

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

1. Dans la page **Nouveau**, sélectionnez **Bases de données** > **Base de données SQL**.

   ![créer une base de données vide](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

1. Dans le volet **Base de données SQL**, tapez ou sélectionnez les valeurs suivantes :

   | Paramètre       | Valeur suggérée | DESCRIPTION |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nom de la base de données** | *yourDatabase* | Pour connaître les noms de bases de données valides, consultez [Identificateurs de base de données](/sql/relational-databases/databases/database-identifiers). |
   | **Abonnement** | *yourSubscription*  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources** | *yourResourceGroup* | Pour les noms de groupe de ressources valides, consultez [Naming conventions](/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Sélectionner une source** | Base de données vide | Indique qu’une base de données vide doit être créée. |

   ![créer une base de données](../articles/sql-database/media/sql-database-design-first-database/create-database.png)

   1. Sélectionnez **Serveur** pour configurer un serveur destiné à votre nouvelle base de données. Ensuite, tapez ou sélectionnez les valeurs suivantes :

      | Paramètre       | Valeur suggérée | DESCRIPTION |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Nom du serveur** | Nom globalement unique | Pour les noms de serveur valides, consultez [Naming conventions](/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
      | **Connexion d’administrateur du serveur** | Nom valide | Pour connaître les noms de connexions valides, consultez [Identificateurs de base de données](/sql/relational-databases/databases/database-identifiers).|
      | **Mot de passe** | Mot de passe valide | Votre mot de passe doit comporter au moins 8 caractères et utiliser des caractères appartenant à trois des catégories suivantes : caractères en majuscules, caractères en minuscules, chiffres et caractères non alphanumériques. |
      | **Lieu** | Emplacement valide | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |

      Choisissez **Select**.

      ![create database-server](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

   1. Sélectionnez **Niveau tarifaire** pour spécifier le niveau de service, le nombre de DTU et la quantité de stockage. Explorez les options concernant les DTU et le stockage disponible pour chaque niveau de service.

      Après avoir sélectionné le niveau du serveur, le nombre de DTU et la quantité de stockage, sélectionnez **Appliquer**.

   1. Entrez un **Classement** pour la base de données vide (pour ce tutoriel, utilisez la valeur par défaut). Pour en savoir plus sur les classements, voir [Classements](/sql/t-sql/statements/collations).

1. Maintenant que vous avez rempli le formulaire **Base de données SQL**, sélectionnez **Créer** pour créer la base de données. Cette étape peut prendre une minute et demie.

1. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

     ![notification](../articles/sql-database/media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Créer une règle de pare-feu

Le service de base de données SQL crée un pare-feu au niveau du serveur pour empêcher les applications et outils externes de se connecter au serveur ou à toute base de données sur le serveur. Suivez ces étapes pour créer une [règle de pare-feu au niveau du serveur de base de données SQL](../articles/sql-database/sql-database-firewall-configure.md) destinée à l’adresse IP de votre client. Ce processus n’offre une connectivité externe via le pare-feu de base de données SQL que pour votre adresse IP.

> [!NOTE]
> La base de données SQL communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter au serveur de base de données Azure SQL, à moins que votre administrateur ouvre le port 1433.

1. Une fois le déploiement terminé, choisissez **Bases de données SQL** dans le menu de gauche, puis sélectionnez *yourDatabase* dans la page **Bases de données SQL**. La page **Vue d’ensemble** de votre base de données s’ouvre. Elle affiche le nom complet du serveur (par exemple, *yourserver.database.windows.net*) et fournit des options pour poursuivre la configuration.

1. Copiez le nom complet du serveur pour vous connecter à votre serveur et à ses bases de données dans les étapes suivantes.

   ![nom du serveur](../articles/sql-database/media/sql-database-design-first-database/server-name.png)

1. Sélectionnez **Définir le pare-feu du serveur** dans la barre d’outils. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre.

   ![règle de pare-feu de serveur](../articles/sql-database/media/sql-database-design-first-database/server-firewall-rule.png)

   1. Dans la barre d’outils, sélectionnez **Ajouter une adresse IP de client** pour ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu. Une règle de pare-feu peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

   1. Choisissez **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur logique.

   1. Sélectionnez **OK**, puis fermez la page **Paramètres de pare-feu**.

Votre adresse IP peut désormais traverser le pare-feu et vous pouvez vous connecter au serveur de base de données SQL et à ses bases de données, avec SSMS ou tout autre outil de votre choix. Veillez à utiliser le compte Administrateur du serveur que vous avez créé précédemment.

> [!IMPORTANT]
> Par défaut, l’accès par le biais du pare-feu de base de données SQL est activé pour tous les services Azure. Sélectionnez **INACTIF** dans cette page pour le désactiver pour tous les services Azure.
