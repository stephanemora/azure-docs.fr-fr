---
title: Comprendre les sorties d’Azure Stream Analytics
description: Cet article décrit les options de sortie de données disponibles dans Azure Stream Analytics, notamment Power BI pour les résultats d’analyse.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/18/2010
ms.openlocfilehash: 9fae39f8c03206a7a63338890101b820a585595b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190621"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Comprendre les sorties d’Azure Stream Analytics

Cet article décrit les types de sorties disponibles pour un travail Azure Stream Analytics. Les sorties permettent de stocker et d’enregistrer les résultats du travail Stream Analytics. Vous pouvez utiliser ces données pour aller plus loin dans les analyses métier et l’entreposage de vos données.

Lorsque vous concevez votre requête Stream Analytics, faites référence au nom de la sortie à l’aide de la [clause INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Vous pouvez utiliser une seule sortie par travail ou, si nécessaire, plusieurs sorties par travail de diffusion en continu en ajoutant plusieurs clauses INTO dans la requête.

Pour créer, modifier et tester des sorties de travaux Stream Analytics, vous pouvez utiliser le [portail Microsoft Azure](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [l’API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [l’API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output) et [Visual Studio](stream-analytics-quick-create-vs.md).

Certains types de sorties prennent en charge le [partitionnement](#partitioning). Les [tailles de lots de sorties](#output-batch-size) varient pour optimiser le débit.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics prend en charge [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage est un référentiel d’entreprise à très grande échelle pour les charges de travail d’analytique du Big Data. Vous pouvez utiliser Data Lake Storage pour stocker des données de toute taille, de tout type et de toute vitesse d’ingestion en vue d’une analyse opérationnelle et exploratoire. Stream Analytics doit être autorisé à accéder à Data Lake Storage.

La sortie Azure Data Lake Storage de Stream Analytics n’est actuellement pas disponible dans les régions Azure - Chine 21Vianet et Azure - Allemagne (T-Systems International).

Le tableau suivant répertorie les noms de propriétés et leur description pour configurer votre sortie Data Lake Storage Gen 1.   

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de la requête vers Data Lake Store. |
| Abonnement | Abonnement qui contient votre compte Azure Data Lake Storage. |
| Nom du compte | Nom du compte Data Lake Store où vous envoyez votre sortie. Vous accédez à la liste déroulante des comptes Data Lake Store disponibles dans votre abonnement. |
| Modèle de préfixe de chemin d’accès | Chemin de fichier utilisé pour écrire vos fichiers dans le compte Data Lake Store spécifié. Vous pouvez spécifier une ou plusieurs instances des variables {date} et {time} :<br /><ul><li>Exemple 1 : dossier1/journaux/{date}/{heure}</li><li>Exemple 2 : dossier1/journaux/{date}</li></ul><br />L’horodatage de la structure de dossiers créée suit l’heure UTC et pas l’heure locale.<br /><br />Si le modèle de chemin d’accès du fichier ne se termine pas par le caractère (/), le dernier modèle du chemin d’accès du fichier est traité comme préfixe de nom de fichier. <br /><br />De nouveaux fichiers sont créés dans les cas de figure suivants :<ul><li>modification du schéma de sortie ;</li><li>redémarrage externe ou interne d’un travail.</li></ul> |
| Format de la date | facultatif. Si le jeton de la date est utilisé dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés. Exemple : AAAA/MM/JJ |
|Format de l’heure | facultatif. Si le jeton de l’heure est utilisé dans le chemin d’accès du préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisés. Actuellement, la seule valeur possible est HH. |
| Format de sérialisation de l’événement | Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge.|
| Encodage | Si vous utilisez le format CSV ou JSON, vous devez spécifier un encodage. UTF-8 est le seul format de codage actuellement pris en charge.|
| Délimiteur | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale.|
| Format | Applicable uniquement pour la sérialisation JSON. L’expression **Séparé par une ligne** indique que la sortie est mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme **Tableau** indique que la sortie est mise en forme en tant que tableau d’objets JSON. Ce tableau se ferme uniquement lorsque le travail s’arrête ou que Stream Analytics est passé à la période suivante. En règle générale, il est préférable d’utiliser du code JSON séparé par des lignes, car il ne requiert aucun traitement spécial pendant que le fichier de sortie est écrit.|
| Mode d'authentification | Vous pouvez autoriser l’accès à votre compte Data Lake Storage en utilisant une [identité managée](stream-analytics-managed-identities-adls.md) ou un jeton d’utilisateur. Une fois l’accès accordé, vous pouvez le révoquer en modifiant le mot de passe du compte d’utilisateur, en supprimant la sortie Data Lake Storage de ce travail ou en effaçant le travail Stream Analytics. |

## <a name="sql-database"></a>SQL Database

Vous pouvez utiliser [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) comme sortie pour les données relationnelles ou pour les applications qui dépendent de contenus hébergés dans une base de données relationnelle. Les travaux Stream Analytics écrivent les données dans une table existante dans SQL Database. Le schéma de table doit correspondre exactement aux champs et aux types dans la sortie de votre travail. Vous pouvez également spécifier [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) en tant que sortie via l’option de sortie SQL Database. Pour découvrir les moyens d’améliorer le débit d’écriture, consultez l’article [Sortie d’Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md).

Vous pouvez aussi utiliser [Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) comme sortie. Vous devez [configurer un point de terminaison public dans Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure), puis configurer manuellement les paramètres suivants dans Azure Stream Analytics. Une machine virtuelle Azure exécutant SQL Server auquel une base de données est attachée est également prise en charge en configurant manuellement les paramètres ci-dessous.

Le tableau suivant répertorie les noms de propriétés et leur description pour la création d’une sortie SQL Database.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette base de données. |
| Base de données | Nom de la base de données où vous envoyez votre sortie. |
| Nom du serveur | Nom du serveur SQL Database. Pour Azure SQL Database Managed Instance, il est nécessaire de spécifier le port 3342. Par exemple, *sampleserver.public.database.windows.net,3342* |
| Nom d’utilisateur | Nom de l’utilisateur qui a accès en écriture à la base de données. Stream Analytics prend uniquement en charge l’authentification SQL. |
| Mot de passe | Mot de passe de connexion à la base de données. |
| Table de charge de travail | Nom de la table dans laquelle la sortie sera écrite. Le nom de la table respecte la casse. Le schéma de cette table doit correspondre exactement au nombre de champs et aux types que votre sortie de travail génère. |
|Hériter du schéma de partition| Option qui permet d’hériter du schéma de partition de l’étape de requête précédente afin d’obtenir une topologie entièrement parallèle avec plusieurs rédacteurs dans la table. Pour plus d'informations, consultez [Sortie d'Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Nombre maximal de lots| Limite supérieure recommandée pour le nombre d’enregistrements envoyés avec chaque transaction d’insertion en bloc.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Stockage d’objets BLOB et Azure Data Lake Gen2

Data Lake Storage Gen2 fait du stockage Azure la base pour créer des dépôts Data Lake d’entreprise sur Azure. Conçu dès le départ pour traiter plusieurs téraoctets d’informations tout en assurant des centaines de gigaoctets de débit, Data Lake Storage Gen2 vous permet de facilement gérer d’importants volumes de données. Une caractéristique fondamentale de Data Lake Storage Gen2 est l’ajout d’un espace de noms hiérarchique au stockage Blob.

Le stockage d’objets blob Azure offre une solution peu coûteuse et évolutive pour stocker de grandes quantités de données non structurées dans le cloud. Pour une introduction au stockage d’objets blob et à son utilisation, consultez [Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide du portail Microsoft Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

Le tableau suivant répertorie les noms de propriétés et leur description pour la création d’une sortie d’objet blob ou ADLS Gen2.

| Nom de la propriété       | Description                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de sortie        | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce stockage d’objets blob. |
| Compte de stockage     | Nom du compte de stockage où vous envoyez votre sortie.               |
| Clé du compte de stockage | Clé secrète associée au compte de stockage.                              |
| Conteneur de stockage   | Regroupement logique des objets blob stockés dans le service BLOB Azure. Lorsque vous téléchargez un objet blob dans le service d'objets Blob, vous devez spécifier un conteneur pour cet objet blob. |
| Modèle de chemin d’accès | facultatif. Modèle de chemin d’accès au fichier utilisé pour écrire vos objets blob dans le conteneur spécifié. <br /><br /> Dans le modèle de chemin d’accès, vous pouvez choisir d’utiliser une ou plusieurs instances des variables de date et d’heure pour spécifier la fréquence d’écriture des objets blob : <br /> {date}, {time} <br /><br />Vous pouvez utiliser le partitionnement d’objet blob personnalisé afin de spécifier un nom personnalisé {field} de vos données d’événement aux objets blob de partition. Le nom du champ est alphanumérique et peut inclure des espaces, des traits d’union et des traits de soulignement. Voici les restrictions qui s’appliquent aux champs personnalisés : <ul><li>Les noms de champs ne sont pas sensibles à la casse. Par exemple, le service ne peut pas faire la différence entre la colonne « ID » et la colonne « id ».</li><li>Les champs imbriqués ne sont pas autorisés. Utilisez plutôt un alias dans la requête du travail pour « aplatir » le champ.</li><li>Des expressions ne peuvent pas être utilisées comme nom de champ.</li></ul> <br />Cette fonctionnalité permet également d’utiliser des configurations de spécificateur de format de date/heure personnalisé dans le chemin d’accès. Les formats de date et d’heure personnalisés doivent être spécifiés un par un et délimités par le mot clé {DateHeure :\<spécificateur >}. Les entrées autorisées pour \<specifier> sont yyyy, MM, M, dd, d, HH, H, mm, m, ss ou s. Le mot-clé {datetime:\<specifier>} peut être utilisé plusieurs fois dans le chemin d’accès pour former des configurations de date/d’heure personnalisées. <br /><br />Exemples : <ul><li>Exemple 1 : cluster1/logs/{date}/{time}</li><li>Exemple 2 : cluster1/logs/{date}</li><li>Exemple 3 : cluster1/{client_id}/{date}/{time}</li><li>Exemple 4 : cluster1/{datetime:ss}/{myField}, où la requête est : SELECT data.myField AS myField FROM Input;</li><li>Exemple 5 : cluster1/année = {datetime:yyyy} / mois = {datetime:MM} / jour = {datetime:dd}</ul><br />L’horodatage de la structure de dossiers créée suit l’heure UTC et pas l’heure locale.<br /><br />La convention suivante est utilisée pour l’attribution de noms : <br /><br />{Modèle de préfixe de chemin d’accès}/Code_hachage_schéma_Numéro_Guid.extension<br /><br />Exemples de fichier de sortie :<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Pour plus d’informations sur cette fonctionnalité, consultez [Partitionnement personnalisé de sortie BLOB dans Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Format de la date | facultatif. Si le jeton de la date est utilisé dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés. Exemple : AAAA/MM/JJ |
| Format de l’heure | facultatif. Si le jeton de l’heure est utilisé dans le chemin d’accès du préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisés. Actuellement, la seule valeur possible est HH. |
| Format de sérialisation de l’événement | Format de sérialisation pour les données de sortie. JSON, CSV, Avro et Parquet sont pris en charge. |
|Lignes minimum (Parquet uniquement)|Le nombre minimum de lignes par lot. Pour Parquet, chaque lot crée un nouveau fichier. La valeur par défaut actuelle est de 2 000 lignes et la valeur maximum autorisée est de 10 000 lignes.|
|Durée maximum (Parquet uniquement)|Le délai d’attente maximum par lot. À l’issue de cette période, le lot est écrit dans la sortie même si l’exigence de lignes minimum n’est pas remplie. La valeur par défaut actuelle est de 1 minute et la valeur maximum autorisée est de 2 heures. Si la sortie de votre objet BLOB a une fréquence de modèle de chemin d’accès, le délai d’attente ne peut pas être supérieur à l’intervalle de temps de la partition.|
| Encodage    | Si vous utilisez le format CSV ou JSON, vous devez spécifier un encodage. UTF-8 est le seul format de codage actuellement pris en charge. |
| Délimiteur   | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format      | Applicable uniquement pour la sérialisation JSON. L’expression **Séparé par une ligne** indique que la sortie est mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme **Tableau** indique que la sortie est mise en forme en tant que tableau d’objets JSON. Ce tableau se ferme uniquement lorsque le travail s’arrête ou que Stream Analytics est passé à la période suivante. En règle générale, il est préférable d’utiliser du code JSON séparé par des lignes, car il ne requiert aucun traitement spécial pendant que le fichier de sortie est écrit. |

Lorsque vous utilisez le stockage d’objets blob en tant que sortie, un fichier est créé dans l’objet blob dans les cas suivants :

* si le fichier actuel dépasse le nombre maximal de blocs autorisés (50 000 actuellement) Vous pouvez atteindre cette limite sans atteindre la taille maximale d’objet blob autorisée. Par exemple, si le taux de sortie est élevé, vous voyez apparaître davantage d’octets par bloc, et la taille de fichier est supérieure. S’il est faible, chaque bloc comporte moins de données et cette taille est inférieure.) ;
* si le schéma est modifié dans la sortie, et que le format de cette dernière requiert un schéma fixe (CSV et Avro) ;
* si un travail est relancé, de façon externe par un utilisateur ou en interne à des fins de maintenance système ou de récupération sur erreur ;
* si la requête est entièrement partitionnée et qu’un fichier est créé pour chaque partition de sortie ;
* si un fichier ou un conteneur du compte de stockage est supprimé par l’utilisateur ;
* si la sortie fait l’objet d’un partitionnement temporel via le modèle de préfixe de chemin d’accès et qu’un nouvel objet blob est utilisé lorsque la requête passe à l’heure suivante ;
* si la sortie est partitionnée par un champ personnalisé et qu’un nouvel objet blob est créé par clé de partition s’il n’y en a pas ;
* si la sortie est partitionnée par un champ personnalisé où la cardinalité des clés de partition est supérieure à 8 000 et qu’un nouvel objet blob est créé par clé de partition.

## <a name="event-hubs"></a>Event Hubs

Le service [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) est un ingesteur d’événements de publication/abonnement hautement évolutif. Il peut collecter des millions d’événements par seconde. Un hub d’événements peut être utilisé en tant que sortie lorsque la sortie d’un travail Stream Analytics correspond à l’entrée d’un autre travail de diffusion en continu. Pour obtenir des informations sur la taille de message maximale et sur l’optimisation de la taille de lot, consultez la section [taille de lot de sortie](#output-batch-size).

Vous avez besoin de quelques paramètres pour configurer des flux de données à partir d’Event Hubs en tant que sortie.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cet Event Hub. |
| Espace de noms du hub d’événements | Conteneur pour un ensemble d’entités de messagerie. En créant un Event Hub, vous avez également créé un espace de noms Event Hub. |
| Nom du hub d’événements | Nom de votre sortie Event Hub. |
| Nom de la stratégie du hub d’événements | Stratégie d’accès partagé que vous pouvez créer dans l’onglet **Configurer** de l’Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie du Event Hub | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Event Hub. |
| Colonne de clé de partition | facultatif. Colonne qui contient la clé de partition pour la sortie Event Hub. |
| Format de sérialisation de l’événement | Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage | Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge. |
| Délimiteur | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format | Applicable uniquement pour la sérialisation JSON. L’expression **Séparé par une ligne** indique que la sortie est mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme **Tableau** indique que la sortie est mise en forme en tant que tableau d’objets JSON.  |
| Colonnes de propriété | facultatif. Colonnes séparées par des virgules qui doivent être jointes en tant que propriétés de l’utilisateur du message sortant au lieu de la charge utile. Vous trouverez plus d’informations sur cette fonctionnalité dans la section [Propriétés de métadonnées personnalisées pour la sortie](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

Vous pouvez utiliser [Power BI](https://powerbi.microsoft.com/) comme sortie d’une tâche Stream Analytics pour fournir une expérience de visualisation riche des résultats d’analyse. Vous pouvez utiliser cette fonctionnalité pour les tableaux de bord opérationnels, la génération de rapports et la création de rapports pilotés par des métriques.

La sortie Power BI de Stream Analytics n’est pas disponible dans les régions Azure - Chine 21Vianet et Azure - Allemagne (T-Systems International).

Le tableau suivant répertorie les noms de propriétés et leur description pour configurer votre sortie Power BI.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Fournit un nom convivial qui est utilisé dans les requêtes pour diriger la sortie de requête vers cette sortie Power BI. |
| Espace de travail de groupe |Pour permettre le partage de données avec d’autres utilisateurs de Power BI, vous pouvez sélectionner des groupes à l’intérieur de votre compte Power BI ou choisir **Mon espace de travail** si vous ne souhaitez pas écrire dans un groupe. La mise à jour d’un groupe existant nécessite le renouvellement de l’authentification Power BI. |
| Nom du jeu de données |Fournissez un nom de jeu de données qui doit être utilisé pour la sortie Power BI. |
| Nom de la table |Fournissez un nom de table sous le jeu de données de la sortie Power BI. Actuellement, une sortie Power BI des travaux Stream Analytics ne peut avoir qu’une table dans un jeu de données. |
| Autoriser la connexion | Vous devez autoriser la connexion avec Power BI pour configurer vos paramètres de sortie. Après avoir accordé à cette sortie l’accès à votre tableau de bord Power BI, vous pouvez le révoquer en modifiant le mot de passe du compte d’utilisateur, en supprimant la sortie du travail ou en effaçant le travail Stream Analytics. | 

Pour une vue d’ensemble de la configuration d’un tableau de bord et d’une sortie Power BI, consultez le didacticiel sur [Azure Stream Analytics et Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Ne créez pas explicitement le jeu de données et la table dans le tableau de bord Power BI. Le jeu de données et la table seront automatiquement remplis au démarrage du travail et lorsque celui-ci commence à extraire des sorties dans Power BI. Si la requête de travail ne génère aucun résultat, le jeu de données et la table ne sont pas créés. De même, si Power BI dispose déjà d’un jeu de données et d’une table portant le même nom que celui saisi dans le travail Stream Analytics, alors ces données sont écrasées.
>

### <a name="create-a-schema"></a>Créer un schéma
Azure Stream Analytics crée un jeu de données et un schéma de table Power BI pour l’utilisateur s’il n’en existe pas encore. Dans tous les autres cas, la table est mise à jour de façon à inclure les nouvelles valeurs. Actuellement, seule une table peut exister dans un jeu de données. 

Power BI utilise la stratégie de rétention FIFO (premier entré, premier sorti). Les données sont collectées dans une table jusqu’à ce qu’elle atteigne 200 000 lignes.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Convertir un type de données Stream Analytics vers Power BI
Azure Stream Analytics met à jour le modèle de données dynamiquement lors de l’exécution si le schéma de sortie est modifié. L’intégralité des modifications de nom de colonne, modifications de type de colonne et ajouts ou suppressions de colonnes sont suivis.

Ce tableau décrit les conversions des [types de données Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) vers les [types Entity Data Model (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) de Power BI si un jeu de données et une table Power BI n’existent pas.

De Stream Analytics | Vers Power BI
-----|-----
bigint | Int64
nvarchar(max) | String
DATETIME | Datetime
float | Double
Tableau d’enregistrements | Type chaîne, valeur constante « IRecord » ou « IArray »

### <a name="update-the-schema"></a>Mettre à jour le schéma
Steam Analytics déduit le schéma de modèle de données sur la base du premier ensemble d’événements de la sortie. Plus tard, si nécessaire, le schéma de modèle de données est mis à jour pour prendre en compte les événements entrants qui peuvent ne pas correspondre au schéma d’origine.

Évitez d’utiliser la requête `SELECT *` pour empêcher la mise à jour dynamique du schéma entre les lignes. En plus de l’impact potentiel sur les performances, le temps nécessaire pour la création des résultats devient impossible à déterminer. Sélectionnez les champs exacts qui doivent être présentés dans le tableau de bord Power BI. En outre, les valeurs de données doivent être compatibles avec le type de données choisi.


Précédent/actuel | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datetime | String | String |  Datetime | String

## <a name="table-storage"></a>Stockage de tables

[stockage de tables Azure](../storage/common/storage-introduction.md) offre un stockage hautement disponible et massivement évolutif, afin qu'une application puisse être mise à l'échelle automatiquement pour répondre à la demande des utilisateurs. Le stockage de tables correspond au magasin de clés/d’attributs NoSQL de Microsoft, que vous pouvez utiliser pour les données structurées, en présentant moins de contraintes au niveau du schéma. Le stockage des données sur les tables Azure permet d’assurer leur persistance et une récupération efficace.

Le tableau suivant répertorie les noms de propriétés et leur description pour la création d’une sortie de table.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce stockage de tables. |
| Compte de stockage |Nom du compte de stockage où vous envoyez votre sortie. |
| Clé du compte de stockage |Clé d’accès associée au compte de stockage. |
| Nom de la table |Nom de la table. La table est créée si elle n’existe pas. |
| Clé de partition |Nom de la colonne de sortie contenant la clé de partition. La clé de partition est un identificateur unique pour la partition dans une table qui constitue la première partie de la clé primaire d’une entité. C’est une valeur de chaîne qui peut atteindre 1 Ko. |
| Clé de ligne |Nom de la colonne de sortie contenant la clé de ligne. La clé de ligne est un identificateur unique pour une entité dans une partition. Elle constitue la deuxième partie de la clé primaire d’une entité. La clé de ligne est une valeur de chaîne qui peut atteindre 1 Ko. |
| Taille du lot |Nombre d’enregistrements d’une opération par lot. La valeur par défaut (100) est suffisante pour la plupart des travaux. Pour plus d’informations sur la modification de ce paramètre, consultez la spécification [TableBatchOperation Class](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) (Classe TableBatchOperation). |

## <a name="service-bus-queues"></a>Files d’attente Service Bus

Les [files d’attente Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) permettent de livrer des messages FIFO à un ou plusieurs destinataires concurrents. En règle générale, les messages sont reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente. Chaque message est reçu et traité par un seul destinataire du message.

Le tableau suivant répertorie les noms de propriétés et leur description pour la création d’une sortie de file d’attente.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette file d’attente Service Bus. |
| Espace de noms Service Bus |Conteneur pour un ensemble d’entités de messagerie. |
| Nom de la file d'attente |Nom de la file d’attente Service Bus. |
| Nom de la stratégie de file d’attente |Lorsque vous créez une file d’attente, vous pouvez également créer des stratégies d’accès partagé dans l’onglet **Configurer** de la file d’attente. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie de file d’attente |Clé d’accès partagé qui permet d’authentifier l’accès à l’espace de noms Service Bus. |
| Format de sérialisation de l’événement |Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage |Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge. |
| Délimiteur |Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format |Applicable uniquement pour le type JSON. L’expression **Séparé par une ligne** indique que la sortie est mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme **Tableau** indique que la sortie est mise en forme en tant que tableau d’objets JSON. |
| Colonnes de propriété | facultatif. Colonnes séparées par des virgules qui doivent être jointes en tant que propriétés de l’utilisateur du message sortant au lieu de la charge utile. Vous trouverez plus d’informations sur cette fonctionnalité dans la section [Propriétés de métadonnées personnalisées pour la sortie](#custom-metadata-properties-for-output). |
| Colonnes de propriétés système | facultatif. Paires clé/valeur de propriétés système et noms de colonnes correspondants qui doivent être attachés au message sortant au lieu de la charge utile. Pour plus d’informations sur cette fonctionnalité, consultez la section [Propriétés système pour les sorties de rubrique et de file d’attente Service Bus](#system-properties-for-service-bus-queue-and-topic-outputs).  |

Le nombre de partitions est [basé sur la référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.

## <a name="service-bus-topics"></a>Rubriques de Service Bus
Les files d’attente Service Bus offrent une méthode de communication biunivoque entre l’expéditeur et le destinataire. Les [rubriques Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) fournissent une forme de communication de type un-à-plusieurs.

Le tableau suivant répertorie les noms de propriétés et leur description pour la création d’une sortie de rubrique Service Bus.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette rubrique Service Bus. |
| Espace de noms Service Bus |Conteneur pour un ensemble d’entités de messagerie. En créant un Event Hub, vous avez créé un espace de noms Service Bus. |
| Nom de la rubrique |Les rubriques sont des entités de messagerie qui sont similaires aux files d’attente et aux hubs d’événements. Elles sont conçues pour collecter des flux d’événements à partir d’appareils et de services. Quand une rubrique est créée, elle reçoit également un nom. Les messages envoyés à une rubrique ne sont disponibles que si un abonnement est créé. Assurez-vous donc que la rubrique comprend un ou plusieurs abonnements. |
| Nom de la stratégie de rubrique |Lorsque vous créez une rubrique Service Bus, vous pouvez également créer des stratégies d’accès partagé dans l’onglet **Configurer** de la rubrique. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie de rubrique |Clé d’accès partagé qui permet d’authentifier l’accès à l’espace de noms Service Bus. |
| Format de sérialisation de l’événement |Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage |Si vous utilisez le format CSV ou JSON, vous devez spécifier un encodage. UTF-8 est le seul format de codage actuellement pris en charge. |
| Délimiteur |Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Colonnes de propriété | facultatif. Colonnes séparées par des virgules qui doivent être jointes en tant que propriétés de l’utilisateur du message sortant au lieu de la charge utile. Vous trouverez plus d’informations sur cette fonctionnalité dans la section [Propriétés de métadonnées personnalisées pour la sortie](#custom-metadata-properties-for-output). |
| Colonnes de propriétés système | facultatif. Paires clé/valeur de propriétés système et noms de colonnes correspondants qui doivent être attachés au message sortant au lieu de la charge utile. Pour plus d’informations sur cette fonctionnalité, consultez la section [Propriétés système pour les sorties de rubrique et de file d’attente Service Bus](#system-properties-for-service-bus-queue-and-topic-outputs). |

Le nombre de partitions est [basé sur la référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) est un service de base de données distribuée à l’échelle mondiale, qui offre une mise à l’échelle élastique et sans limite dans le monde entier, des requêtes enrichies et une indexation automatique sur les modèles de données indépendants des schémas. Pour en savoir plus sur les options de conteneur Azure Cosmos DB pour Stream Analytics, consultez l’article [Sortie Azure Stream Analytics dans Azure Cosmos DB](stream-analytics-documentdb-output.md).

La sortie Azure Cosmos DB de Stream Analytics n’est pas disponible dans les régions Azure - Chine 21Vianet et Azure - Allemagne (T-Systems International).

> [!Note]
> Pour le moment, Azure Stream Analytics prend uniquement en charge la connexion à Azure Cosmos DB à l’aide de l’API SQL.
> Les autres API Azure Cosmos DB ne sont pas encore prises en charge. Si vous pointez Azure Stream Analytics vers les comptes Azure Cosmos DB créés avec d'autres API, les données risquent de ne pas être correctement stockées.

Le tableau suivant décrit les propriétés de création d’une sortie Azure Cosmos DB.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie | Alias référençant cette sortie dans votre requête Stream Analytics. |
| Récepteur | Azure Cosmos DB. |
| Option d’importation | Choisissez **Sélectionner Cosmos DB dans votre abonnement** ou **Fournir manuellement les paramètres Cosmos DB**.
| ID de compte | Nom ou URI de point de terminaison du compte Azure Cosmos DB. |
| Clé de compte | Clé d’accès partagé du compte Azure Cosmos DB. |
| Base de données | Nom de la base de données Azure Cosmos DB. |
| Nom du conteneur | Nom du conteneur à utiliser, qui doit se trouver dans Cosmos DB. Exemple :  <br /><ul><li> _MyContainer_ : Un conteneur nommé « MyContainer » doit exister.</li>|
| ID du document |facultatif. Nom du champ dans les événements de sortie utilisé pour spécifier la clé primaire sur laquelle sont basées les opérations d’insertion ou de mise à jour.

## <a name="azure-functions"></a>Azure Functions
Azure Functions est un service de calcul serverless que vous pouvez utiliser pour exécuter du code à la demande sans configurer ou gérer l’infrastructure explicitement. Grâce à ce service, vous pouvez implémenter le code qui est déclenché par les événements qui se produisent dans Azure ou des services partenaires. Comme Azure Functions peut répondre à des déclencheurs, il constitue l’outil de sortie logique pour Azure Stream Analytics. Cet adaptateur de sortie permet aux utilisateurs de connecter Stream Analytics à Azure Functions et d’exécuter un script ou un fragment de code en réaction à différents événements.

La sortie Azure Functions de Stream Analytics n’est pas disponible dans les régions Azure - Chine 21Vianet et Azure - Allemagne (T-Systems International).

Azure Stream Analytics appelle Azure Functions via des déclencheurs HTTP. L’adaptateur de sortie Azure Functions est disponible, avec les propriétés configurables suivantes :

| Nom de la propriété | Description |
| --- | --- |
| Conteneur de fonctions |Nom de votre application Azure Functions. |
| Fonction |Nom de la fonction dans votre application Azure Functions. |
| Clé |Si vous souhaitez utiliser une fonction Azure à partir d’un autre abonnement, vous pouvez le faire en fournissant la clé permettant d’accéder à votre fonction. |
| Taille de lot maximale |Propriété qui peut être utilisée pour définir la taille maximale de chaque lot de sortie qui est envoyé à votre fonction Azure. L’unité d’entrée est exprimée en octets. Par défaut, cette valeur est définie sur 262 144 Ko (256 Ko). |
| Nombre maximal de lots  |Propriété qui vous permet de spécifier le nombre maximal d’événements dans chaque lot envoyé à Azure Functions. La valeur par défaut est 100. |

Azure Stream Analytics attend l’état HTTP 200 de l’application Functions pour les lots traités avec succès.

Lorsqu’Azure Stream Analytics reçoit une erreur 413 (qui indique que l’entité de requête HTTP est trop volumineuse) de la part d’une fonction Azure, il réduit la taille des lots envoyés à Azure Functions. Dans le code de votre fonction Azure, utilisez cette exception pour vous assurer qu’Azure Stream Analytics n’envoie pas de lots trop volumineux. Vérifiez également que les valeurs de taille et de nombre de lots maximum utilisées dans la fonction correspondent à celles qui ont été saisies dans le portail Stream Analytics.

> [!NOTE]
> Lors du test de la connexion, Stream Analytics envoie un lot vide à Azure Functions pour s’assurer que la connexion entre les deux fonctionne. Assurez-vous que votre Function App gère les demandes de lots vides pour vérifier si la connexion de test est bien établie.

De plus, si aucun événement n’est signalé dans le temps imparti, aucune sortie n’est générée. De ce fait, la fonction **computeResult** n’est pas appelée. Ce comportement est cohérent avec les fonctions d’agrégation fenêtrées intégrées.

## <a name="custom-metadata-properties-for-output"></a>Propriétés de métadonnées personnalisées pour la sortie 

Vous pouvez joindre des colonnes de requête en tant que propriétés de l’utilisateur aux messages sortants. Ces colonnes ne font pas partie de la charge utile. Les propriétés sont présentes sous la forme d’un dictionnaire sur le message de sortie. *Clé* est le nom de colonne et *valeur* est la valeur de la colonne dans le dictionnaire de propriétés. Tous les types de données Stream Analytics sont pris en charge à l’exception des enregistrements et des tableaux.  

Sorties prises en charge : 
* File d’attente Service Bus 
* Rubrique Service Bus 
* Event Hub 

Dans l’exemple suivant, nous ajoutons les deux champs `DeviceId` et `DeviceStatus` aux métadonnées. 
* Requête : `select *, DeviceId, DeviceStatus from iotHubInput`
* Configuration de la sortie : `DeviceId,DeviceStatus`

![Colonnes de propriété](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

La capture d’écran suivante montre les propriétés du message de sortie inspectées dans EventHub via [l’Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

![Propriétés personnalisées d’événement](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Propriétés système pour les sorties de rubrique et de file d’attente Service Bus 
Vous pouvez attacher des colonnes de requête en tant que [propriétés système](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) à vos messages sortants de file d’attente ou de rubrique Service Bus. Ces colonnes ne sont pas placées dans la charge utile ; au lieu de cela, la [propriété système](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) BrokeredMessage correspondante est renseignée avec les valeurs de la colonne de requête.
Les propriétés système suivantes sont prises en charge : `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.
Les valeurs de chaîne de ces colonnes sont analysées en tant que type de valeur de propriété système correspondante et les échecs d’analyse sont traités comme des erreurs de données.
Ce champ est fourni sous la forme d’un format d’objet JSON. Les détails relatifs à ce format sont les suivants :
* Entouré par des accolades {}.
* Écrit dans des paires clé-valeur.
* Les clés et les valeurs doivent être des chaînes.
* La clé est le nom de la propriété système et la valeur est le nom de la colonne de requête.
* Les clés et les valeurs sont séparées par un signe deux-points.
* Chaque paire clé-valeur est séparée par une virgule.

Voici comment utiliser cette propriété :

* Requête : `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Colonnes de propriétés système : `{ "MessageId": "column1", "PartitionKey": "column2"}`

Cela définit le `MessageId` sur les messages de la file d’attente Service bus avec les valeurs de `column1`, et PartitionKey est défini avec les valeurs de `column2`.

## <a name="partitioning"></a>Partitionnement

Le tableau suivant récapitule la prise en charge de la partition et le nombre de générateurs de sortie pour chaque type de sortie :

| Type de sortie | Prise en charge du partitionnement | Clé de partition  | Nombre de générateurs de sortie |
| --- | --- | --- | --- |
| Azure Data Lake Store | Oui | Utilisez les jetons {date} et {time} dans le modèle de préfixe du chemin d’accès. Choisissez le format de date, par exemple, YYYY/MM/DD, DD/MM/YYYY ou MM-DD-YYYY. HH est utilisé pour le format de l’heure. | Suit le partitionnement d’entrée des [requêtes entièrement parallélisables](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Oui, doit être activé. | Basée sur la clause PARTITION BY dans la requête. | Lorsque l’option d’héritage du partitionnement est activée, elle suit le partitionnement d’entrée pour les [requêtes entièrement parallélisables](stream-analytics-scale-jobs.md). Pour améliorer vos performances de débit d’écriture lorsque vous chargez des données dans Azure SQL Database, consultez [Sortie d’Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Stockage Blob Azure | Oui | Utilisez les jetons {date} et {time} de vos champs d’événement dans le modèle de chemin d’accès. Choisissez le format de date, par exemple, YYYY/MM/DD, DD/MM/YYYY ou MM-DD-YYYY. HH est utilisé pour le format de l’heure. La sortie d’objet blob peut être partitionnée par un seul attribut d’événement personnalisé {fieldname} ou par {datetime:\<specifier>}. | Suit le partitionnement d’entrée des [requêtes entièrement parallélisables](stream-analytics-scale-jobs.md). |
| Hubs d'événements Azure | Oui | Oui | Dépend de l’alignement des partitions.<br /> Lorsque la clé de partition de la sortie Event Hub s’aligne parfaitement avec l’étape de requête (précédente) en amont, le nombre d’enregistreurs est égal au nombre de partitions dans la sortie Event Hub. Chaque enregistreur utilise la [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) pour envoyer des événements à la partition concernée. <br /> Lorsque la clé de partition de la sortie Event Hub ne s’aligne pas avec l’étape de requête (précédente) en amont, le nombre d’enregistreurs est égal au nombre de partitions de cette précédente étape. Chaque enregistreur utilise la [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) dans **EventHubClient** pour envoyer des événements à toutes les partitions de sortie. |
| Power BI | Non | None | Non applicable. |
| Stockage de tables Azure | Oui | N’importe quelle colonne de sortie.  | Suit le partitionnement d’entrée des [requêtes entièrement mises en parallèle ](stream-analytics-scale-jobs.md). |
| Rubrique Azure Service Bus | Oui | Choisi automatiquement. Le nombre de partitions est basé sur la [référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.| Égal au nombre de partitions de la rubrique de sortie.  |
| File d’attente Azure Service Bus | Oui | Choisi automatiquement. Le nombre de partitions est basé sur la [référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.| Égal au nombre de partitions de la file d’attente de sortie. |
| Azure Cosmos DB | Oui | Basée sur la clause PARTITION BY dans la requête. | Suit le partitionnement d’entrée des [requêtes entièrement mises en parallèle ](stream-analytics-scale-jobs.md). |
| Azure Functions | Oui | Basée sur la clause PARTITION BY dans la requête. | Suit le partitionnement d’entrée des [requêtes entièrement mises en parallèle ](stream-analytics-scale-jobs.md). |

Le nombre d’enregistreurs de sortie peut également être contrôlé à l’aide de la clause `INTO <partition count>` (consultez [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) dans votre requête, ce qui peut être utile pour atteindre la topologie de travail souhaitée. Si votre adaptateur de sortie n'est pas partitionné, l'absence de données dans une partition d'entrée entraînera un retard pouvant aller jusqu'au délai d'arrivée tardive. Dans ce cas, la sortie est fusionnée dans un seul enregistreur, ce qui peut créer des goulots d’étranglement dans votre pipeline. Pour en savoir plus sur la stratégie d’arrivée tardive, consultez l’article [Considérations relatives à l’ordre des événements Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Taille de lot de sortie
Azure Stream Analytics utilise des lots de taille variable pour traiter les événements et générer les sorties. En général, le moteur Stream Analytics n’écrit pas les messages un par un ; il utilise des lots pour plus d’efficacité. Lorsque le taux des événements entrants et sortants est élevé, Stream Analytics utilise des lots plus volumineux. Lorsque le taux de sortie est faible, il utilise des lots plus petits pour maintenir une faible latence.

Le tableau suivant expose certaines considérations relatives au traitement par lots des sorties :

| Type de sortie | Taille de message maximale | Optimisation de la taille des lots |
| :--- | :--- | :--- |
| Azure Data Lake Store | Consultez [Data Lake Store limits](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits) (Limites de Data Lake Store). | Utilisation jusqu’à 4 Mo par opération d’écriture. |
| Azure SQL Database | Configurable à l’aide du nombre maximal de lots. 10 000 lignes maximum et 100 lignes minimum par insertion en bloc par défaut.<br />Consultez [SQL Database limits](../sql-database/sql-database-resource-limits.md) (Limites de SQL Database). |  Chaque lot est initialement inséré en bloc avec le nombre de lots maximum. Le lot est divisé de moitié (jusqu’au nombre de lots minimum) en fonction des erreurs renouvelables de SQL. |
| Stockage Blob Azure | Consultez [Storage limits](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) (Limites de stockage). | Taille maximale de bloc d’objets blob : 4 Mo.<br />Nombre maximal de blocs d’objets blob : 50 000. |
| Hubs d'événements Azure  | 256 Ko ou 1 Mo par message. <br />Consultez [Quotas et limites d’Azure Event Hubs](../event-hubs/event-hubs-quotas.md). |  Lorsque le partitionnement entrée/sortie ne s’aligne pas, chaque événement est empaqueté individuellement dans `EventData` et envoyé dans un lot dans la limite de la taille de message maximale. Cela se produit également si les [propriétés de métadonnées personnalisées](#custom-metadata-properties-for-output) sont utilisées. <br /><br />  Lorsque le partitionnement entrée/sortie est aligné, plusieurs événements sont empaquetés dans une seule instance `EventData`, dans la limite de la taille de message maximale, et envoyés. |
| Power BI | Consultez [Power BI REST API limitations](https://msdn.microsoft.com/library/dn950053.aspx) (Limites de l’API REST Power BI). |
| Stockage de tables Azure | Consultez [Storage limits](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) (Limites de stockage). | La valeur par défaut est 100 entités par transaction. Vous pouvez définir une valeur inférieure, en fonction des besoins. |
| File d’attente Azure Service Bus   | 256 Ko par message pour le niveau Standard, 1 Mo pour le niveau Premium.<br /> Consultez [Quotas Service Bus](../service-bus-messaging/service-bus-quotas.md). | Un événement unique par message est utilisé. |
| Rubrique Azure Service Bus | 256 Ko par message pour le niveau Standard, 1 Mo pour le niveau Premium.<br /> Consultez [Quotas Service Bus](../service-bus-messaging/service-bus-quotas.md). | Un événement unique par message est utilisé. |
| Azure Cosmos DB   | Consultez [Azure Cosmos DB limits](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits) (Limites d’Azure Cosmos DB). | La taille des lots et la fréquence d’écriture sont ajustées dynamiquement en fonction des réponses Azure Cosmos DB. <br /> Stream Analytics n’impose aucune limite prédéterminée. |
| Azure Functions   | | Taille de lot par défaut : 262 144 Ko (256 Ko). <br /> Nombre d’événements par défaut par lot : 100. <br /> La taille de lot, configurable, peut être augmentée ou diminuée dans les [options de sortie](#azure-functions) de Stream Analytics.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> 
> [Démarrage rapide : Créer un travail Stream Analytics à l’aide du portail Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
