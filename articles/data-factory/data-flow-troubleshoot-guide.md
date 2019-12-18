---
title: Résoudre les problèmes de flux de données
description: Découvrez comment résoudre les problèmes de flux de données dans Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.custom: seo-lt-2019
ms.date: 12/06/2019
ms.openlocfilehash: b972bbeac419d88afdd257a7fd19587dbaedf0d9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930172"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Résoudre les problèmes de flux de données Azure Data Factory

Cet article présente des méthodes couramment employées pour résoudre les problèmes de flux de données dans Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erreurs et messages courants

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Message d’erreur : DF-SYS-01 : shaded.databricks.org.apache.hadoop.fs.azure.AzureException: com.microsoft.azure.storage.StorageException : Le conteneur spécifié n’existe pas.

- **Symptômes** : L’exécution de l’aperçu des données, du débogage et du flux de données de pipeline échoue car le conteneur n’existe pas

- **Cause** : Se produit quand le jeu de données contient un conteneur qui n’existe pas dans le stockage

- **Résolution** : Vérifiez que le conteneur que vous référencez dans votre jeu de données existe

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Message d’erreur : DF-SYS-01: java.lang.AssertionError : Échec de l’assertion : Structures de répertoires en conflit détectées. Chemins suspects

- **Symptômes** : Lors de l’utilisation de caractères génériques dans la transformation de la source avec des fichiers Parquet

- **Cause** : Syntaxe des caractères génériques incorrecte ou non valide

- **Résolution** : Vérifiez la syntaxe des caractères génériques que vous utilisez dans vos options de transformation de la source

### <a name="error-message-df-src-002-container-container-name-is-required"></a>Message d’erreur : DF-SRC-002: « container » (nom de conteneur) est obligatoire

- **Symptômes** : L’exécution de l’aperçu des données, du débogage et du flux de données de pipeline échoue car le conteneur n’existe pas

- **Cause** : Se produit quand le jeu de données contient un conteneur qui n’existe pas dans le stockage

- **Résolution** : Vérifiez que le conteneur que vous référencez dans votre jeu de données existe

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Message d’erreur : DF-UNI-001 : PrimaryKeyValue a des types IntegerType et StringType incompatibles

- **Symptômes** : L’exécution de l’aperçu des données, du débogage et du flux de données de pipeline échoue car le conteneur n’existe pas

- **Cause** : Se produit lors d’une tentative d’insertion d’un type de clé primaire incorrect dans des récepteurs de base de données

- **Résolution** : Utilisez une colonne dérivée pour effectuer un cast de la colonne que vous utilisez pour la clé primaire dans votre flux de données afin qu’elle corresponde au type de données de votre base de données cible

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Message d’erreur : DF-SYS-01 : com.microsoft.sqlserver.jdbc.SQLServerException : Échec de la connexion TCP/IP à l’hôte xxxxx.database.windows.net, port 1433. Erreur : « xxxx.database.windows.net. Vérifiez les propriétés de connexion. Assurez-vous qu’une instance de SQL Server est en cours d’exécution sur l’hôte et accepte les connexions TCP/IP au port. Vérifiez que les connexions TCP au port ne sont pas bloquées par un pare-feu. »

- **Symptômes** : Impossible d’afficher un aperçu des données ou d’exécuter le pipeline avec la source ou le récepteur de base de données

- **Cause** : La base de données est protégée par un pare-feu

- **Résolution** : Ouvrez l’accès du pare-feu à la base de données

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Message d’erreur : DF-SYS-01 : com.microsoft.sqlserver.jdbc.SQLServerException : Il existe déjà un objet nommé « XXXXXX » dans la base de données.

- **Symptômes** : Le récepteur ne parvient pas à créer la table

- **Cause** : Une table portant le même nom que celui défini dans votre source ou dans le jeu de données existe déjà dans la base de données cible

- **Résolution** : Changez le nom de la table que vous essayez de créer

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-string-or-binary-data-would-be-truncated"></a>Message d’erreur : DF-SYS-01 : com.microsoft.sqlserver.jdbc.SQLServerException : Les données binary ou String seront tronquées. 

- **Symptômes** : Lors de l’écriture de données dans un récepteur SQL, votre flux de données échoue lors de l’exécution du pipeline avec une erreur de troncation possible.

- **Cause** : Un champ de votre flux de données mappé à une colonne de votre base de données SQL n’est pas suffisamment grand pour stocker la valeur, le pilote SQL génère alors cette erreur.

- **Résolution** : Vous pouvez réduire la longueur des données pour les colonnes de type chaîne à l’aide de ```left()``` dans une colonne dérivée ou implémenter le modèle [« ligne d’erreur ».](how-to-data-flow-error-rows.md)

### <a name="error-message-since-spark-23-the-queries-from-raw-jsoncsv-files-are-disallowed-when-the-referenced-columns-only-include-the-internal-corrupt-record-column"></a>Message d’erreur : Depuis Spark 2.3, les requêtes provenant de fichiers JSON/CSV bruts ne sont pas autorisées lorsque les colonnes référencées incluent uniquement la colonne interne d’enregistrements endommagés. 

- **Symptômes** : Échec de la lecture à partir d’une source JSON

- **Cause** : Lors de la lecture à partir d’une source JSON avec un document unique sur de nombreuses lignes imbriquées, ADF, via Spark, ne peut pas déterminer l’emplacement où un nouveau document commence et où le document précédent se termine.

- **Résolution** : Sur la transformation source qui utilise un jeu de données JSON, développez « Paramètres JSON » et activez « Un seul document ».

### <a name="error-message-duplicate-columns-found-in-join"></a>Message d’erreur : Colonnes dupliquées trouvées dans la jointure

- **Symptômes** : La transformation de jointure a généré des noms de colonnes dupliqués dans les colonnes de gauche et de droite

- **Cause** : Les flux joints portent des noms de colonnes communs

- **Résolution** : Ajoutez un transformation Select après la jointure et sélectionnez « Supprimer les colonnes dupliquées » pour l’entrée et la sortie.


## <a name="general-troubleshooting-guidance"></a>Instructions générales pour la résolution des problèmes

1. Vérifiez l’état de vos connexions de jeu de données. Dans chaque transformation de source et récepteur, visitez le service lié pour chaque jeu de données que vous utilisez et testez les connexions.
2. Vérifiez l’état de vos connexions aux fichiers et aux tables à partir du concepteur de flux de données. Activez le débogage, puis cliquez sur Aperçu des données dans vos transformations de la source pour vérifier que vous pouvez accéder à vos données.
3. Si tout semble correct dans l’aperçu des données, accédez au concepteur de pipeline et mettez votre flux de données dans une activité de pipeline. Déboguez le pipeline pour un test de bout en bout.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guide de performances du mappage de flux de données ADF](concepts-data-flow-performance.md)
