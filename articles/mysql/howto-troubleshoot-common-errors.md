---
title: Résoudre les erreurs courantes – Azure Database pour MySQL
description: Découvrez comment résoudre les erreurs courantes de migration rencontrées par les utilisateurs qui débutent avec le service Azure Database pour MySQL
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 5/21/2021
ms.openlocfilehash: d095eddf150990ac5ab76f0753cae2bc44537c88
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110471807"
---
# <a name="commonly-encountered-errors-during-or-post-migration-to-azure-database-for-mysql"></a>Erreurs généralement rencontrées pendant ou après la migration vers Azure Database pour MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Azure Database pour MySQL est un service complètement managé, reposant sur MySQL Community Edition. L’expérience MySQL dans un environnement de service managé peut différer de l’exécution de MySQL dans votre environnement spécifique. Dans cet article, vous verrez certaines des erreurs courantes que les utilisateurs peuvent rencontrer en migrant vers Azure Database pour MySQL ou en y développant pour la première fois.

## <a name="common-connection-errors"></a>Erreurs de connexion courantes

#### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>ERREUR 1184 (08S01) : La connexion 22 à la base de connaissances a été abandonnée : 'db-name' user: 'user' host: 'hostIP' (échec de la commande init_connect)

L’erreur ci-dessus se produit après la réussite de la connexion mais avant l’exécution de toute commande quand la session est établie. Le message ci-dessus indique que vous avez affecté une valeur incorrecte au paramètre de serveur `init_connect`, qui provoque l’échec de l’initialisation de la session.

Certains paramètres de serveur comme `require_secure_transport` n’étant pas pris en charge au niveau de la session, toute tentative de changement des valeurs de ces paramètres avec `init_connect` peut provoquer l’erreur 1184 lors de la connexion au serveur MySQL, comme indiqué ci-dessous :

mysql> show databases; ERREUR 2006 (HY000) : Le serveur MySQL a disparu. Aucune connexion. Tentative de reconnexion... ID de connexion :    64897 Base de données active : *** AUCUNE**_ ERREUR 1184 (08S01) : La connexion 22 à la base de connaissances a été abandonnée : 'db-name' user: 'user' host: 'hostIP' (échec de la commande init_connect)

**Résolution** : Réinitialisez la valeur `init_connect` sous l’onglet Paramètres du serveur dans le portail Azure, et définissez uniquement les paramètres de serveur pris en charge à l’aide du paramètre init_connect.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Erreurs dues à l’absence du SUPER privilège et au rôle DBA

Le SUPER privilège et le rôle DBA ne sont pas pris en charge sur ce service. Par conséquent, vous pouvez rencontrer certaines erreurs courantes listées ci-dessous :

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ERREUR 1419 : Vous n’avez pas le SUPER privilège et la journalisation binaire est activée (vous *pouvez* utiliser la variable log_bin_trust_function_creators moins sécurisée)

L’erreur ci-dessus peut se produire lors de la création d’une fonction, d’un déclencheur comme ci-dessous ou de l’importation d’un schéma. Les instructions DDL telles que CREATE FUNCTION ou CREATE TRIGGER sont écrites dans le journal binaire, de sorte que le réplica secondaire peut les exécuter. Le thread SQL de réplica a des privilèges complets, qui peuvent être exploités pour élever les privilèges. Pour vous protéger contre ce danger sur les serveurs où la journalisation binaire est activée, le moteur MySQL exige que les créateurs de fonction stockée aient le privilège SUPER en plus du privilège CREATE ROUTINE habituel.

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Résolution** : Pour résoudre cette erreur, affectez la valeur 1 à `log_bin_trust_function_creators` dans le panneau des [paramètres du serveur](howto-server-parameters.md) dans le portail, exécutez les instructions DDL ou importez le schéma pour créer les objets souhaités. Vous pouvez continuer à maintenir `log_bin_trust_function_creators` sur 1 pour votre serveur afin d’éviter l’erreur à l’avenir. Nous vous recommandons de définir `log_bin_trust_function_creators`, car le risque de sécurité mis en évidence dans la [documentation de la communauté MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) est minimal dans Azure Database pour MySQL puisque le journal des emplacements n’est exposé à aucune menace.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>ERREUR 1227 (42000) à la ligne 101 : Accès refusé ; vous avez au minimum besoin d’un des SUPER privilèges pour cette opération. Échec de l’opération avec code de sortie 1

L’erreur ci-dessus peut se produire lors de l’importation d’un fichier dump ou de la création d’une procédure contenant des éléments [definer](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html).

**Résolution** :  Pour résoudre cette erreur, l’utilisateur administrateur peut accorder des privilèges permettant de créer ou d’exécuter des procédures en exécutant la commande GRANT, comme dans les exemples suivants :

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```

Vous pouvez également remplacer les éléments definer par le nom de l’utilisateur administrateur qui exécute le processus d’importation, comme indiqué ci-dessous.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>ERREUR 1227 (42000) à la ligne 295 : Accès refusé ; vous avez au minimum besoin d’un des privilèges SUPER ou SET_USER_ID pour cette opération.

L’erreur ci-dessus peut se produire lors de l’exécution de CREATE VIEW avec des instructions DEFINER dans le cadre de l’importation d’un fichier dump ou de l’exécution d’un script. Azure Database pour MySQL n’autorise ni les privilèges SUPER ni le privilège SET_USER_ID pour un utilisateur.

**Résolution** :

* Utilisez l’utilisateur definer pour exécuter CREATE VIEW, si possible. Il est probable qu’il existe beaucoup d’affichages avec des utilisateurs definer différents, dotés d’autres autorisations, ce qui peut rendre la chose irréalisable.  OR
* Modifiez le fichier dump ou le script CREATE VIEW, puis supprimez l’instruction DEFINER= du fichier dump OU 
* Modifiez le fichier dump ou le script CREATE VIEW et remplacez les valeurs de definer par celles de l’utilisateur disposant des autorisations d’administrateur et effectuant l’importation ou l’exécution du fichier de script.

> [!Tip]
> Utilisez sed ou perl pour modifier un fichier dump ou un script SQL et remplacer l’instruction DEFINER= statement

#### <a name="error-1227-42000-at-line-18-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation"></a>ERREUR 1227 (42000) à la ligne 18 : Accès refusé ; vous avez au minimum besoin d’un des SUPER privilèges pour cette opération

L’erreur ci-dessus peut se produire si vous tentez d’importer le fichier dump à partir du serveur MySQL avec GTID activé sur le serveur Azure Database pour MySQL cible. Mysqldump ajoute l’instruction SET @@SESSION.sql_log_bin=0 à un fichier dump à partir d’un serveur sur lequel des GTID sont en cours d’utilisation, ce qui désactive la journalisation binaire pendant le rechargement du fichier dump.

**Résolution** : Pour résoudre cette erreur lors de l’importation, supprimez ou commentez les lignes ci-dessous dans votre fichier mysqldump, puis réexécutez l’importation pour vérifier qu’elle est correcte.

SET @MYSQLDUMP_TEMP_LOG_BIN = @@SESSION.SQL_LOG_BIN ; SET @@SESSION.SQL_LOG_BIN= 0 ; SET @@GLOBAL.GTID_PURGED='' ; SET @@SESSION.SQL_LOG_BIN = @MYSQLDUMP_TEMP_LOG_BIN ;

## <a name="common-connection-errors-for-server-admin-sign-in"></a>Erreurs de connexion courantes liées à la connexion de l’administrateur du serveur

Quand un serveur Azure Database pour MySQL est créé, une connexion d’administrateur de serveur est fournie par l’utilisateur final lors de la création du serveur. La connexion d’administrateur du serveur vous permet de créer des bases de données, d’ajouter de nouveaux utilisateurs et d’accorder des autorisations. Si la connexion d’administrateur du serveur est supprimée, ses autorisations sont révoquées ou son mot de passe est changé, vous pouvez commencer à voir des erreurs de connexion dans votre application pendant les connexions. Voici quelques erreurs parmi les plus courantes :

#### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>ERREUR 1045 (28000) : Accès refusé pour l’utilisateur 'nom_utilisateur'@'adresse IP' (utilisant le mot de passe : OUI)

L’erreur ci-dessus se produit dans les cas suivants :

* Le nom d’utilisateur n’existe pas.
* Le nom d’utilisateur de l’utilisateur a été supprimé.
* Le mot de passe associé a été changé ou réinitialisé

**Résolution** :

* Vérifiez que le « nom_utilisateur » existe en tant qu’utilisateur valide sur le serveur, ou qu’il a été supprimé par inadvertance. Vous pouvez exécuter la requête suivante en vous connectant à l’utilisateur Azure Database pour MySQL :

  ```sql
  select user from mysql.user;
  ```

* Si vous ne pouvez pas vous connecter à l’instance MySQL pour exécuter la requête elle-même ci-dessus, nous vous recommandons de [réinitialiser le mot de passe d’administrateur avec le portail Azure](howto-create-manage-server-portal.md). L’option de réinitialisation du mot de passe à partir du portail Azure permet de recréer l’utilisateur, de réinitialiser le mot de passe et de restaurer les autorisations d’administrateur, ce qui vous permet de vous connecter par l’intermédiaire de l’administrateur de serveur et d’effectuer d’autres opérations.

## <a name="next-steps"></a>Étapes suivantes

Si vous n’avez pas trouvé la réponse que vous recherchez, envisagez les options suivantes :

* Publiez votre question sur la [page de questions Microsoft Q&A](/answers/topics/azure-database-mysql.html) ou sur [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
* Envoyez un e-mail à l’équipe Azure Database pour MySQL [@Ask Azure Database pour MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Cette adresse e-mail n’est pas un alias du support technique.
* Contactez le support Azure et [créez un ticket à partir du portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
* Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
