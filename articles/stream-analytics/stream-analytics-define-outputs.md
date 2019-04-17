---
title: Comprendre les sorties d’Azure Stream Analytics
description: Cet article décrit les options de sortie de données disponibles dans Azure Stream Analytics, notamment Power BI pour les résultats d’analyse.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/25/2019
ms.custom: seodec18
ms.openlocfilehash: 03871c3f3627e85cc2af2f05a5fba38bd8069a15
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609487"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Comprendre les sorties d’Azure Stream Analytics
Cet article décrit les types de sorties disponibles pour un travail Azure Stream Analytique. Les sorties permettent de stocker et d’enregistrer les résultats du travail Stream Analytics. En utilisant les données de sortie, vous pouvez faire encore plus analytique d’entreprise et d’entreposage de données de vos données.

Lorsque vous concevez votre requête Analytique de Stream, faire référence au nom de la sortie à l’aide de la [clause INTO](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Vous pouvez utiliser une seule sortie par travail, ou plusieurs sorties par la tâche de streaming (si vous en avez besoin) en fournissant plusieurs clauses INTO dans la requête.

Pour créer, modifier et tester le travail d’Analytique de Stream génère, vous pouvez utiliser la [Azure portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), et [Visual Studio](stream-analytics-quick-create-vs.md).

Une prise en charge des types de sorties [partitionnement](#partitioning). [Tailles de lot de sortie](#output-batch-size) varient pour optimiser le débit.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics prend en charge [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store est un référentiel de très grande échelle de toute l’entreprise pour les charges de travail analytique de big data. Vous pouvez utiliser Data Lake Store pour stocker les données de toute taille, le type et la vitesse d’ingestion pour l’analytique opérationnelle et exploratoire. Stream Analytique doit être autorisé à accéder à Data Lake Store.

La sortie Azure Data Lake Store de Stream Analytics n’est pas disponible dans les régions Azure - Chine (21Vianet) et Azure - Allemagne (T-Systems International).

### <a name="authorize-an-azure-data-lake-store-account"></a>Autoriser un compte Azure Data Lake Store

1. Lorsque vous sélectionnez Data Lake Store en tant que sortie dans le portail Azure, vous êtes invité à autoriser une connexion à une instance existante de Data Lake Store.

   ![Autoriser une connexion à Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Si vous avez déjà accès à Data Lake Store, sélectionnez **autoriser maintenant**. Une page s’affiche et indique **redirection vers l’autorisation**. Après que l’autorisation a réussi, vous voyez la page qui vous permet de configurer la sortie Data Lake Store.

3. Après avoir configuré le compte Data Lake Store authentifié, vous pouvez configurer les propriétés pour votre sortie Data Lake Store.

   ![Définir Data Lake Store en tant que sortie de Stream Analytics](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

Le tableau suivant répertorie les noms de propriétés et leurs descriptions pour configurer votre sortie Data Lake Store.   

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie | Un nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers Data Lake Store. |
| Nom du compte | Le nom du compte Data Lake Store où que vous envoyez votre sortie. Vous voyez une liste déroulante des comptes Data Lake Store qui sont disponibles dans votre abonnement. |
| Modèle de préfixe de chemin d’accès | Le chemin d’accès de fichier qui est utilisé pour écrire vos fichiers dans le compte Data Lake Store spécifié. Vous pouvez spécifier une ou plusieurs instances de {date} et {time} variables :<br /><ul><li>Exemple 1 : dossier1/journaux/{date}/{heure}</li><li>Exemple 2 : dossier1/journaux/{date}</li></ul><br />L’horodatage de la structure de dossiers créée suit l’heure UTC et pas l’heure locale.<br /><br />Si le modèle de chemin d’accès de fichier ne contient pas une barre oblique (/), le dernier modèle dans le chemin d’accès de fichier est traité comme un préfixe de nom de fichier. <br /><br />De nouveaux fichiers sont créés dans les cas de figure suivants :<ul><li>modification du schéma de sortie ;</li><li>Redémarrage externe ou interne d’un travail</li></ul> |
| Format de la date | facultatif. Si le jeton de la date est utilisé dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés. Exemple : AAAA/MM/JJ |
|Format de l’heure | facultatif. Si le jeton de l’heure est utilisé dans le chemin d’accès du préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisés. Actuellement, la seule valeur possible est HH. |
| Format de sérialisation de l’événement | Le format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge.|
| Encodage | Si vous utilisez le format CSV ou JSON, un encodage doit être spécifié. UTF-8 est le seul format de codage actuellement pris en charge.|
| Délimiteur | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale.|
| Format | Applicable uniquement pour la sérialisation JSON. **Séparé par une ligne** Spécifie que la sortie est mise en forme avec chaque objet JSON séparé par une nouvelle ligne. **Tableau** Spécifie que la sortie est mise en forme en tant que tableau d’objets JSON. Ce tableau se ferme uniquement lorsque le travail s’arrête ou que Stream Analytics est passé à la période suivante. En règle générale, il est préférable d’utiliser JSON séparée par des lignes, car elle ne requiert aucun traitement spécial pendant que le fichier de sortie est toujours en cours d’écriture à.|

### <a name="renew-data-lake-store-authorization"></a>Renouveler une autorisation Data Lake Store
Vous devez authentifier de nouveau votre compte Data Lake Store si son mot de passe a été modifié depuis la création ou la dernière authentification de votre travail. Si vous ne pas s’authentifier de nouveau, votre travail ne génère pas de résultats de sortie et affiche une erreur qui indique la nécessité d’un renouvellement d’autorisation dans les journaux des opérations. 

Actuellement, le jeton d’authentification doit être actualisé manuellement tous les 90 jours pour tous les travaux avec une sortie de Data Lake Store. Vous pouvez passer outre cette limitation en [authentifient via gérés identités (version préliminaire)](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls).

Pour renouveler l’autorisation :

1. Sélectionnez **arrêter** pour arrêter votre travail.
1. Accédez à votre Store lac de données de sortie et sélectionnez le **renouveler l’autorisation** lien.

   Pendant un bref instant, une page contextuelle indique **redirection vers l’autorisation**. Si l’autorisation est accordée, la page indique **l’autorisation a été correctement renouvelée** puis se ferme automatiquement. 
   
1. Sélectionnez **enregistrer** en bas de la page. Vous pouvez ensuite redémarrer votre travail à partir de la **heure du dernier arrêt** afin d’éviter la perte de données.

![Renouveler une autorisation Data Lake Store dans la sortie](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>Base de données SQL
Vous pouvez utiliser [base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) en tant que sortie de données relationnelles ou pour les applications qui dépendent de contenus hébergés dans une base de données relationnelle. Travaux d’Analytique Stream écrire dans une table existante dans la base de données SQL. Le schéma de table doit correspondre exactement les champs et leurs types de sortie de votre travail. Vous pouvez également spécifier [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) option de sortie en tant que sortie par le biais de la base de données SQL. Pour en savoir plus sur les moyens d’améliorer le débit d’écriture, consultez le [Analytique Stream avec Azure SQL Database en tant que sortie](stream-analytics-sql-output-perf.md) article. 

Le tableau suivant répertorie les noms des propriétés et leur description pour la création d’une sortie de la base de données SQL.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette base de données. |
| Base de données | Le nom de la base de données où que vous envoyez votre sortie. |
| Nom du serveur | Nom du serveur SQL Database. |
| Nom d’utilisateur | Le nom d’utilisateur qui a accès en écriture à la base de données. Analytique de Stream prend en charge uniquement l’authentification SQL. |
| Mot de passe | Mot de passe de connexion à la base de données. |
| Table | Nom de la table dans laquelle la sortie sera écrite. Le nom de table respecte la casse. Le schéma de cette table doit correspondre exactement le nombre de champs et leurs types de sortie de votre travail génère. |
|Hériter du schéma de partition| Une option pour hériter le schéma de partitionnement de votre étape de requête précédente, pour activer la topologie entièrement parallèle avec plusieurs rédacteurs à la table. Pour plus d'informations, consultez [Sortie d'Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Correspondance du nombre de lots| La limite recommandée sur le nombre d’enregistrements envoyés avec chaque bloc insérer une transaction.|

> [!NOTE]
> L’offre de base de données SQL Azure est actuellement prise en charge pour une sortie de tâche dans Stream Analytics. Une machine virtuelle Azure exécutant SQL Server avec une base de données attachée n’est pas pris en charge. Cela est susceptible de changer dans des futures versions.
>

## <a name="blob-storage"></a>Stockage d'objets blob
Stockage d’objets Blob Azure offre une solution économique et évolutive pour stocker de grandes quantités de données non structurées dans le cloud. Pour une introduction sur le stockage d’objets Blob et de son utilisation, consultez [comment utiliser des objets BLOB](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

Le tableau suivant répertorie les noms des propriétés et leurs descriptions pour la création d’une sortie d’objets blob.

| Nom de la propriété       | Description                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de sortie        | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce stockage d’objets blob. |
| Compte de stockage     | Le nom du compte de stockage où vous envoyez votre sortie.               |
| Clé du compte de stockage | Clé secrète associée au compte de stockage.                              |
| Conteneur de stockage   | Un regroupement logique des objets BLOB stockés dans le service Blob Azure. Lorsque vous téléchargez un objet blob dans le service d'objets Blob, vous devez spécifier un conteneur pour cet objet blob. |
| Modèle de chemin d’accès | facultatif. Le modèle de chemin d’accès de fichier qui est utilisé pour écrire vos objets BLOB dans le conteneur spécifié. <br /><br /> Dans le modèle de chemin d’accès, vous pouvez choisir d’utiliser une ou plusieurs instances des variables de date et heure pour spécifier la fréquence à laquelle l’écriture des objets BLOB : <br /> {date}, {time} <br /><br />Vous pouvez utiliser le partitionnement d’objet blob personnalisé afin de spécifier un nom personnalisé {field} de vos données d’événement aux objets blob de partition. Le nom du champ est alphanumérique et peut inclure des espaces, des traits d’union et des traits de soulignement. Voici les restrictions qui s’appliquent aux champs personnalisés : <ul><li>Noms de champs ne sont pas la casse. Par exemple, le service ne peut pas faire la distinction entre la colonne « ID » et la colonne « id ».</li><li>Les champs imbriqués ne sont pas autorisées. Au lieu de cela, utilisez un alias dans la requête de travail pour le champ « aplatir ».</li><li>Expressions ne peut pas être utilisées comme nom de champ.</li></ul> <br />Cette fonctionnalité permet également d’utiliser des configurations de spécificateur de format de date/heure personnalisé dans le chemin d’accès. Les formats de date et d’heure personnalisés doivent être spécifiés un par un et délimités par le mot clé {DateHeure :\<spécificateur >}. Les entrées autorisées pour \<spécificateur > sont AAAA, MM, M, jj, d, HH, H, mm, m, ss ou s. Le {date/heure :\<spécificateur >} mot clé peut être utilisée plusieurs fois dans le chemin d’accès pour former des configurations de date/heure personnalisé. <br /><br />Exemples : <ul><li>Exemple 1 : cluster1/logs/{date}/{time}</li><li>Exemple 2 : cluster1/logs/{date}</li><li>Exemple 3 : cluster1/{client_id}/{date}/{time}</li><li>Exemple 4 : cluster1/{datetime:ss}/{myField}, où la requête est : SELECT data.myField AS myField FROM Input;</li><li>Exemple 5 : cluster1/année = {datetime:yyyy} / mois = {datetime:MM} / jour = {datetime:dd}</ul><br />L’horodatage de la structure de dossiers créée suit l’heure UTC et pas l’heure locale.<br /><br />Procédure d’affection utilise la convention suivante : <br /><br />{Modèle de préfixe de chemin d’accès}/Code_hachage_schéma_Numéro_Guid.extension<br /><br />Exemples de fichier de sortie :<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Pour plus d’informations sur cette fonctionnalité, consultez [partitionnement de la sortie d’objets blob Azure Stream Analytique personnalisée](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Format de la date | facultatif. Si le jeton de la date est utilisé dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés. Exemple : AAAA/MM/JJ |
| Format de l’heure | facultatif. Si le jeton de l’heure est utilisé dans le chemin d’accès du préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisés. Actuellement, la seule valeur possible est HH. |
| Format de sérialisation de l’événement | Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage    | Si vous utilisez le format CSV ou JSON, un encodage doit être spécifié. UTF-8 est le seul format de codage actuellement pris en charge. |
| Délimiteur   | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format      | Applicable uniquement pour la sérialisation JSON. **Séparé par une ligne** Spécifie que la sortie est mise en forme avec chaque objet JSON séparé par une nouvelle ligne. **Tableau** Spécifie que la sortie est mise en forme en tant que tableau d’objets JSON. Ce tableau se ferme uniquement lorsque le travail s’arrête ou que Stream Analytics est passé à la période suivante. En règle générale, il est préférable d’utiliser JSON séparée par des lignes, car elle ne requiert aucun traitement spécial pendant que le fichier de sortie est toujours en cours d’écriture à. |

Lorsque vous utilisez le stockage d’objets Blob en tant que sortie, un nouveau fichier est créé dans l’objet blob dans les cas suivants :

* si le fichier actuel dépasse le nombre maximal de blocs autorisés (50 000 actuellement) Vous pouvez atteindre le nombre maximal autorisé de blocs sans atteindre la taille maximale autorisée pour les blobs. Par exemple, si le taux de sortie est élevé, vous voyez apparaître davantage d’octets par bloc, et la taille de fichier est supérieure. S’il est faible, chaque bloc comporte moins de données et cette taille est inférieure.) ;
* S’il existe une modification de schéma dans la sortie et le format de sortie requiert un schéma fixe (CSV et Avro).
* si un travail est relancé, de façon externe par un utilisateur ou en interne à des fins de maintenance système ou de récupération sur erreur ;
* Si la requête est entièrement partitionnée, ainsi qu’un nouveau fichier est créé pour chaque partition de sortie.
* Si l’utilisateur supprime un fichier ou un conteneur du compte de stockage.
* Si la sortie est partitionnée en utilisant le modèle de préfixe de chemin d’accès de temps, et un nouvel objet blob est utilisé lorsque la requête passe à l’heure suivante.
* Si la sortie est partitionnée par un champ personnalisé, et un nouvel objet blob est créé par la clé si de partition, il n’existe pas.
* Si la sortie est partitionnée par un champ personnalisé où la partition la cardinalité de clé dépasse 8 000, et un nouvel objet blob est créé par la clé de partition.

## <a name="event-hubs"></a>Event Hubs
Le service [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) est un ingesteur d’événements de publication/abonnement hautement évolutif. Il peut collecter des millions d’événements par seconde. Une utilisation d’un concentrateur d’événements en tant que sortie est lors de la sortie d’un travail Stream Analytique devient l’entrée d’une autre tâche de diffusion en continu.

Vous avez besoin de quelques paramètres pour configurer des flux de données à partir de concentrateurs d’événements en tant que sortie.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie | Un nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cet event hub. |
| Espace de noms du hub d’événements |Un conteneur pour un ensemble d’entités de messagerie. Lorsque vous avez créé un hub d’événements, vous avez également créé un espace de noms event hub. |
| Nom du hub d’événements | Le nom de votre sortie de hub d’événements. |
| Nom de la stratégie du hub d’événements | La stratégie d’accès partagé, vous pouvez créer sur le concentrateur d’événements **configurer** onglet. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie du Event Hub | La clé d’accès partagé qui est utilisée pour authentifier l’accès à l’espace de noms event hub. |
| Colonne de clé de partition | facultatif. Une colonne qui contient la clé de partition pour la sortie de hub d’événements. |
| Format de sérialisation de l’événement | Le format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage | Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge. |
| Délimiteur | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format | Applicable uniquement pour la sérialisation JSON. **Séparé par une ligne** Spécifie que la sortie est mise en forme avec chaque objet JSON séparé par une nouvelle ligne. **Tableau** Spécifie que la sortie est mise en forme en tant que tableau d’objets JSON. Ce tableau se ferme uniquement lorsque le travail s’arrête ou que Stream Analytics est passé à la période suivante. En règle générale, il est préférable d’utiliser JSON séparée par des lignes, car elle ne requiert aucun traitement spécial pendant que le fichier de sortie est toujours en cours d’écriture à. |
| Colonnes de propriété | facultatif. Colonnes séparées par des virgules qui doivent être attachés en tant que propriétés de l’utilisateur du message sortant au lieu de la charge utile. Plus d’informations sur cette fonctionnalité est dans la section [propriétés de métadonnées personnalisées pour la sortie](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI
Vous pouvez utiliser [Power BI](https://powerbi.microsoft.com/) en tant que sortie pour un travail d’Analytique de Stream fournir une expérience de visualisation riche des résultats d’analyse. Vous pouvez utiliser cette fonctionnalité pour les tableaux de bord opérationnels, la génération de rapports et la création de rapports pilotés par des mesures.

La sortie Power BI de Stream Analytics n’est pas disponible dans les régions Azure - Chine (21Vianet) et Azure - Allemagne (T-Systems International).

### <a name="authorize-a-power-bi-account"></a>Autorisation d’un compte Power BI
1. Lorsque Power BI est sélectionné en tant que sortie dans le portail Azure, vous êtes invité à autoriser un utilisateur de Power BI existant ou pour créer un nouveau compte Power BI.
   
   ![Autoriser un utilisateur de Power BI pour configurer la sortie](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Créer un nouveau compte si vous n’avez pas encore, puis sélectionnez **autoriser maintenant**. La page suivante apparaît :
   
   ![Authentifiez-vous sur Power BI à partir du compte Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. Spécifiez un compte professionnel ou scolaire pour autoriser la sortie Power BI. Si vous n’êtes pas déjà inscrit à Power BI, sélectionnez **s’inscrire maintenant**. Le compte professionnel ou scolaire que vous utilisez pour Power BI peut être différent du compte d’abonnement Azure que vous êtes maintenant connecté à l’aide.

### <a name="configure-the-power-bi-output-properties"></a>Configuration des propriétés de sortie Power BI
Après avoir configuré le compte Power BI authentifié, vous pouvez configurer les propriétés pour votre sortie Power BI. Le tableau suivant répertorie les noms de propriétés et leurs descriptions pour configurer votre sortie Power BI.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Fournissez un nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette sortie Power BI. |
| Espace de travail de groupe |Pour activer le partage de données avec d’autres utilisateurs de Power BI, vous pouvez sélectionner des groupes à l’intérieur de votre compte Power BI ou choisir **mon espace de travail** si vous ne souhaitez pas écrire dans un groupe. La mise à jour d’un groupe existant nécessite le renouvellement de l’authentification Power BI. |
| Nom du jeu de données |Fournissez un nom de jeu de données que vous souhaitez que la sortie de Power BI à utiliser. |
| Nom de la table |Fournissez un nom de table sous le jeu de données de la sortie Power BI. Actuellement, une sortie Power BI des travaux Stream Analytics ne peut avoir qu’une table dans un jeu de données. |

Pour une procédure pas à pas de configuration d’une sortie Power BI et un tableau de bord, consultez le [Power BI et Azure Stream Analytique](stream-analytics-power-bi-dashboard.md) article.

> [!NOTE]
> Ne créez pas explicitement le jeu de données et la table dans le tableau de bord Power BI. Le jeu de données et la table sont automatiquement remplis lorsque le travail est démarré et le travail démarre le pompage de sortie dans Power BI. Si la requête de tâche ne génère aucun résultat, le jeu de données et la table ne sont pas créés. Si Power BI dispose déjà d’un jeu de données et une table portant le même nom que celui fourni dans ce travail Analytique de Stream, les données existantes sont remplacées.
>

### <a name="create-a-schema"></a>Créer un schéma
Azure Stream Analytique crée un schéma de jeu de données et de table Power BI pour l’utilisateur si elles n’existent pas déjà. Dans tous les autres cas, la table est mise à jour de façon à inclure les nouvelles valeurs. Actuellement, seule une table peut exister dans un jeu de données. 

Power BI utilise la stratégie de rétention premier entré, premier sorti (FIFO). Collecte les données dans une table jusqu'à atteindre 200 000 lignes.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Convertir un type de données à partir de Stream Analytique pour Power BI
Azure Stream Analytics met à jour le modèle de données dynamiquement lors de l’exécution si le schéma de sortie est modifié. L’intégralité des modifications de nom de colonne, modifications de type de colonne et ajouts ou suppressions de colonnes sont suivis.

Ce tableau décrit les conversions de type de données à partir de [les types de données Analytique de Stream](https://msdn.microsoft.com/library/azure/dn835065.aspx) à Power BI [types du modèle EDM (Entity Data Model)](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/entity-data-model), si un jeu de données Power BI et une table n’existent pas.

De Stream Analytics | Vers Power BI
-----|-----
bigint | Int64
nvarchar(max) | Chaîne
Datetime | DateTime
float | Double
Tableau d’enregistrements | Chaîne de type, la valeur de constante « IRecord » ou « IArray »

### <a name="update-the-schema"></a>Mettre à jour le schéma
Steam Analytics déduit le schéma de modèle de données sur la base du premier ensemble d’événements de la sortie. Ultérieurement, si nécessaire, le schéma de modèle de données est mis à jour pour prendre en compte les événements entrants qui ne peuvent pas tenir dans le schéma d’origine.

Éviter le `SELECT *` requête afin d’éviter la mise à jour de schéma dynamique entre les lignes. En plus de l’impact potentiel sur les performances, elle peut entraîner à incertitude le temps nécessaire pour les résultats. Sélectionnez les champs exacts qui doivent être présentés dans le tableau de bord Power BI. En outre, les valeurs de données doivent être compatibles avec le type de données choisi.


Précédent/actuel | Int64 | Chaîne | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Chaîne | Chaîne | Double
Double | Double | Chaîne | Chaîne | Double
Chaîne | Chaîne | Chaîne | Chaîne | Chaîne 
DateTime | Chaîne | Chaîne |  DateTime | Chaîne


### <a name="renew-power-bi-authorization"></a>Renouvellement de l’autorisation Power BI
Si votre mot de passe du compte Power BI est modifié après la création ou de la dernière authentification de votre travail d’Analytique de Stream, vous devez réauthentifier l’Analytique de Stream. Si Azure Multi-Factor Authentication est configuré sur votre client Azure Active Directory (Azure AD), vous devez également renouveler l’autorisation Power BI toutes les deux semaines. Un symptôme de ce problème est l’absence de sortie de la tâche et une « erreur d’authentification de l’utilisateur » dans les journaux d’activité des opérations :

  ![Authentifier une erreur de l’utilisateur Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Pour résoudre ce problème, arrêtez votre tâche en cours d'exécution et accédez à votre sortie Power BI. Cliquez sur le lien **Renouveler l’autorisation**, puis redémarrez votre travail à partir de **l’Heure du dernier arrêt** pour éviter de perdre des données.

  ![Renouveler une autorisation Power BI pour la sortie](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Stockage de tables
[stockage de tables Azure](../storage/common/storage-introduction.md) offre un stockage hautement disponible et massivement évolutif, afin qu'une application puisse être mise à l'échelle automatiquement pour répondre à la demande des utilisateurs. Stockage de table est un magasin de clés/attributs NoSQL de Microsoft, que vous pouvez utiliser pour les données structurées avec moins de contraintes sur le schéma. Le stockage des données sur les tables Azure permet d’assurer leur persistance et une récupération efficace.

Le tableau suivant répertorie les noms des propriétés et leurs descriptions pour la création d’une sortie de table.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce stockage de tables. |
| Compte de stockage |Le nom du compte de stockage où vous envoyez votre sortie. |
| Clé du compte de stockage |Clé d’accès associée au compte de stockage. |
| Nom de la table |Nom de la table. La table est créée si elle n’existe pas. |
| Clé de partition |Le nom de la colonne de sortie qui contient la clé de partition. La clé de partition est un identificateur unique pour la partition dans une table qui constitue la première partie de la clé primaire d’une entité. C’est une valeur de chaîne qui peut être jusqu'à 1 Ko. |
| Clé de ligne |Le nom de la colonne de sortie qui contient la clé de ligne. La clé de ligne est un identificateur unique pour une entité dans une partition. Elle constitue la deuxième partie de la clé primaire d’une entité. La clé de ligne est une valeur de chaîne qui peut être jusqu'à 1 Ko. |
| Taille du lot |Nombre d’enregistrements d’une opération par lot. La valeur par défaut (100) est suffisante pour la plupart des travaux. Consultez le [spécifications des opérations de traitement par lots de Table](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) pour plus d’informations sur la modification de ce paramètre. |

## <a name="service-bus-queues"></a>Files d’attente Service Bus
[Files d’attente Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrent une remise de messages FIFO à un ou plusieurs destinataires concurrents. En règle générale, les messages sont reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente. Chaque message est reçu et traité par un consommateur d’un seul message.

Le tableau suivant répertorie les noms des propriétés et leurs descriptions pour la création d’une sortie de file d’attente.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Un nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette file d’attente Service Bus. |
| Espace de noms Service Bus |Un conteneur pour un ensemble d’entités de messagerie. |
| Nom de la file d'attente |Le nom de la file d’attente Service Bus. |
| Nom de la stratégie de file d’attente |Lorsque vous créez une file d’attente, vous pouvez également créer des stratégies d’accès partagé sur la file d’attente **configurer** onglet. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie de file d’attente |Clé d’accès partagé qui permet d’authentifier l’accès à l’espace de noms Service Bus. |
| Format de sérialisation de l’événement |Le format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage |Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge. |
| Délimiteur |Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format |Applicable uniquement pour le type JSON. **Séparé par une ligne** Spécifie que la sortie est mise en forme avec chaque objet JSON séparé par une nouvelle ligne. **Tableau** Spécifie que la sortie est mise en forme en tant que tableau d’objets JSON. |
| Colonnes de propriété | facultatif. Colonnes séparées par des virgules qui doivent être attachés en tant que propriétés de l’utilisateur du message sortant au lieu de la charge utile. Plus d’informations sur cette fonctionnalité est dans la section [propriétés de métadonnées personnalisées pour la sortie](#custom-metadata-properties-for-output). |

Le nombre de partitions est [basé sur la référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.

## <a name="service-bus-topics"></a>Rubriques Service Bus
Files d’attente Service Bus offrent une méthode de communication d’un à un expéditeur au destinataire. [Rubriques Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) fournissent une forme de type un-à-plusieurs de communication.

Le tableau suivant répertorie les noms des propriétés et leurs descriptions pour la création d’une sortie de la rubrique.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Un nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette rubrique Service Bus. |
| Espace de noms Service Bus |Un conteneur pour un ensemble d’entités de messagerie. En créant un Event Hub, vous avez créé un espace de noms Service Bus. |
| Nom de la rubrique |Les rubriques sont des entités de messagerie qui sont similaires aux files d’attente et aux hubs d’événements. Ils sont conçus pour collecter des flux d’événements à partir de périphériques et services. Lorsqu’une rubrique est créée, elle a également donné un nom spécifique. Les messages envoyés à une rubrique ne sont pas disponibles, sauf si un abonnement est créé, assurez-vous donc il existe un ou plusieurs abonnements sous la rubrique. |
| Nom de la stratégie de rubrique |Lorsque vous créez une rubrique, vous pouvez également créer des stratégies d’accès partagé sur le sujet **configurer** onglet. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie de rubrique |Clé d’accès partagé qui permet d’authentifier l’accès à l’espace de noms Service Bus. |
| Format de sérialisation de l’événement |Le format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage |Si vous utilisez le format CSV ou JSON, un encodage doit être spécifié. UTF-8 est le seul format de codage actuellement pris en charge. |
| Délimiteur |Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Colonnes de propriété | facultatif. Colonnes séparées par des virgules qui doivent être attachés en tant que propriétés de l’utilisateur du message sortant au lieu de la charge utile. Plus d’informations sur cette fonctionnalité est dans la section [propriétés de métadonnées personnalisées pour la sortie](#custom-metadata-properties-for-output). |

Le nombre de partitions est [basé sur la référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) est un service de base de données globalement distribuée qui offre la SCALABILITÉ élastique autour du globe, des requêtes enrichies et l’indexation automatique sur les modèles de données indépendants des schémas. Pour en savoir plus sur les options de collection Azure Cosmos DB pour l’Analytique de Stream, consultez le [Analytique Stream avec Azure Cosmos DB en tant que sortie](stream-analytics-documentdb-output.md) article.

La sortie Azure Cosmos DB de Stream Analytics n’est pas disponible dans les régions Azure - Chine (21Vianet) et Azure - Allemagne (T-Systems International).

> [!Note]
> À ce stade, Azure Stream Analytique prend uniquement en charge la connexion à Azure Cosmos DB à l’aide de l’API SQL.
> Les autres API Azure Cosmos DB ne sont pas encore prises en charge. Si vous pointez Azure Stream Analytics vers les comptes Azure Cosmos DB créés avec d'autres API, les données risquent de ne pas être correctement stockées.

Le tableau suivant décrit les propriétés de création d’une sortie Azure Cosmos DB.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie | Alias référençant cette sortie dans votre requête Stream Analytics. |
| Récepteur | Azure Cosmos DB. |
| Option d’importation | Choisissez **sélectionner Cosmos DB à partir de votre abonnement** ou **paramètres fournissent Cosmos DB manuellement**.
| ID de compte | Nom ou URI de point de terminaison du compte Azure Cosmos DB. |
| Clé de compte | Clé d’accès partagé du compte Azure Cosmos DB. |
| Base de données | Nom de la base de données Azure Cosmos DB. |
| Modèle de nom de collection | Le nom du regroupement ou le modèle pour les collections à utiliser. <br />Vous pouvez construire le format de nom de collection à l’aide du jeton facultatif {partition}, où les partitions commencent à 0. Deux exemples :  <br /><ul><li> _MyCollection_: Une collection nommée « MyCollection » doit exister.</li>  <li> _MyCollection {partition}_: Selon la colonne de partitionnement.</li></ul> Les collections de colonnes de partitionnement doivent être réunies : « MyCollection0 », « MyCollection1 », « MyCollection2 », et ainsi de suite. |
| Clé de partition | facultatif. Cela est utile uniquement si vous utilisez un jeton {partition} dans votre modèle de nom de collection.<br /> La clé de partition est le nom du champ dans les événements de sortie qui est utilisé pour spécifier la clé de partitionnement de sortie sur les collections.<br /> Pour la sortie de collection unique, vous pouvez utiliser n’importe quelle colonne de sortie arbitraire. ID de partition est un exemple. |
| ID du document |facultatif. Le nom du champ dans les événements de sortie qui est utilisé pour spécifier la clé primaire sur l’insérer ou mettre à jour les opérations sont basées.

## <a name="azure-functions"></a>Azure Functions
Azure Functions est un service de calcul sans serveur que vous pouvez utiliser pour exécuter du code à la demande sans explicitement configurer ou gérer l’infrastructure. Il vous permet d’implémenter le code qui est déclenchée par les événements qui se produisent dans les services Azure ou partenaire. Cette capacité d’Azure Functions à répondre aux déclencheurs rend une sortie naturelle pour Azure Stream Analytique. Cet adaptateur de sortie permet aux utilisateurs de se connecter Stream Analytique pour Azure Functions et exécuter un script ou une partie du code en réponse à un grand nombre d’événements.

La sortie Azure Functions de Stream Analytics n’est pas disponible dans les régions Azure - Chine (21Vianet) et Azure - Allemagne (T-Systems International).

Azure Stream Analytics appelle Azure Functions via des déclencheurs HTTP. L’adaptateur de sortie Azure Functions est disponible avec les propriétés configurables suivantes :

| Nom de la propriété | Description |
| --- | --- |
| Conteneur de fonctions |Le nom de votre application Azure Functions. |
| Fonction |Le nom de la fonction dans votre application Azure Functions. |
| Clé |Si vous souhaitez utiliser une fonction Azure à partir d’un autre abonnement, vous pouvez le faire en fournissant la clé pour accéder à votre fonction. |
| Taille de lot maximale |Une propriété qui vous permet de définie la taille maximale de chaque lot de sortie qui est envoyé à votre fonction Azure. L’unité d’entrée est exprimée en octets. Par défaut, cette valeur est de 262 144 octets (256 Ko). |
| Nombre maximal de lots  |Une propriété qui vous permet de spécifier le nombre maximal d’événements dans chaque lot est envoyé à Azure Functions. La valeur par défaut est 100. |

Quand Azure Stream Analytique reçoit un 413 (« http Request Entity Too Large ») exception à partir d’une fonction Azure, elle réduit la taille des lots qu’il envoie à Azure Functions. Dans le code de votre fonction Azure, utilisez cette exception pour vous assurer qu’Azure Stream Analytics n’envoie pas de lots trop volumineux. En outre, assurez-vous que les valeurs de nombre et taille de lot maximale utilisées dans la fonction sont cohérents avec les valeurs entrées dans le portail d’Analytique de Stream.

En outre, dans une situation où il n’existe aucun événement de lancement dans une fenêtre de temps, aucune sortie n’est générée. Par conséquent, le **computeResult** fonction n’est pas appelée. Ce comportement est cohérent avec les fonctions d’agrégation fenêtrées intégrées.

## <a name="custom-metadata-properties-for-output"></a>Propriétés de métadonnées personnalisées pour la sortie 

Vous pouvez attacher des colonnes de la requête en tant que propriétés de l’utilisateur aux messages sortants. Ces colonnes ne passent pas dans la charge utile. Les propriétés sont présentes sous la forme d’un dictionnaire sur le message de sortie. *Clé* est le nom de colonne et *valeur* est la valeur de colonne dans le dictionnaire de propriétés. Tous les types de données Analytique de Stream sont pris en charge à l’exception d’enregistrement et de tableau.  

Sorties prises en charge : 
* File d’attente Service Bus 
* Rubrique Service Bus 
* Event Hub 

Dans l’exemple suivant, nous ajoutons les deux champs `DeviceId` et `DeviceStatus` aux métadonnées. 
* Requête : `select *, DeviceId, DeviceStatus from iotHubInput`
* Configuration de sortie : `DeviceId,DeviceStatus`

![Colonnes de propriété](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

La capture d’écran suivante montre les inspecter dans EventHub par le biais des propriétés de message de sortie [Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

![Propriétés de l’événement personnalisées](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Partitionnement

Le tableau suivant récapitule la prise en charge de la partition et le nombre de générateurs de sortie pour chaque type de sortie :

| Type de sortie | Prise en charge du partitionnement | Clé de partition  | Nombre de générateurs de sortie |
| --- | --- | --- | --- |
| Azure Data Lake Store | Oui | Utilisez {date} {time} les jetons et dans le modèle de préfixe de chemin d’accès. Choisissez le format de date, telles qu’AAAA/MM/JJ, jj/MM/AAAA ou MM-jj-aaaa. HH est utilisé pour le format d’heure. | Suit le partitionnement d’entrée des [requêtes entièrement parallélisables](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Oui | Basée sur la clause PARTITION BY dans la requête. | Suit le partitionnement d’entrée des [requêtes entièrement parallélisables](stream-analytics-scale-jobs.md). Pour en savoir plus sur la réalisation de meilleures performances en écriture débit lorsque vous chargez des données dans la base de données SQL Azure, consultez [sortie Azure Stream Analytique pour Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Stockage d'objets blob Azure | Oui | Utilisez {date} {time} les jetons et à partir de vos champs d’événements dans le modèle de chemin d’accès. Choisissez le format de date, telles qu’AAAA/MM/JJ, jj/MM/AAAA ou MM-jj-aaaa. HH est utilisé pour le format d’heure. La sortie d’objet blob peut être partitionnée par un seul attribut d’événement personnalisé {fieldname} ou par {datetime:\<specifier>}. | Suit le partitionnement d’entrée des [requêtes entièrement parallélisables](stream-analytics-scale-jobs.md). |
| Hubs d'événements Azure | Oui | Oui | Dépend de l’alignement des partitions.<br /> Lorsque la clé de partition pour la sortie de hub d’événements est également alignée avec l’étape de requête en amont (précédent), le nombre d’enregistreurs est le même que le nombre de partitions dans la sortie de hub d’événements. Chaque enregistreur utilise la [EventHubSender classe](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) pour envoyer des événements à la partition spécifique. <br /> Lorsque la clé de partition pour la sortie de hub d’événements n’est pas alignée sur l’étape de requête en amont (précédent), le nombre d’enregistreurs est le même que le nombre de partitions dans cette étape précédente. Chaque enregistreur utilise la [SendBatchAsync classe](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) dans **EventHubClient** pour envoyer des événements à toutes les partitions de sortie. |
| Power BI | Non  | Aucun | Non applicable. |
| Stockage de tables Azure | Oui | N’importe quelle colonne de sortie.  | Suit le partitionnement d’entrée des [requêtes entièrement mises en parallèle ](stream-analytics-scale-jobs.md). |
| Rubrique Azure Service Bus | Oui | Choisi automatiquement. Le nombre de partitions est basé sur la [référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.| Égal au nombre de partitions de la rubrique de sortie.  |
| File d’attente Azure Service Bus | Oui | Choisi automatiquement. Le nombre de partitions est basé sur la [référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.| Égal au nombre de partitions de la file d’attente de sortie. |
| Azure Cosmos DB | Oui | Utilisez le jeton {partition} dans le modèle de nom de collection. La valeur {partition} est basée sur la clause PARTITION BY dans la requête. | Suit le partitionnement d’entrée des [requêtes entièrement mises en parallèle ](stream-analytics-scale-jobs.md). |
| Azure Functions | Non  | Aucun | Non applicable. |

Si votre adaptateur de sortie n'est pas partitionné, l'absence de données dans une partition d'entrée entraînera un retard pouvant aller jusqu'au délai d'arrivée tardive. Dans ce cas, la sortie est fusionnée dans un writer unique, ce qui peut provoquer des goulots d’étranglement dans votre pipeline. Pour en savoir plus sur la stratégie d’arrivée tardive, consultez [considérations relatives au classement de Azure Stream Analytique événement](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Taille de lot de sortie
Azure Stream Analytique utilise des lots de taille variable pour traiter les événements et écrire vers les sorties. En général, le moteur Analytique de Stream n’écrit pas un message à la fois et utilise des lots pour plus d’efficacité. Lorsque le taux de deux événements entrants et sortants est élevé, Stream Analytique utilise des lots plus volumineux. Lorsque le taux de sortie est faible, il utilise des lots plus petits pour maintenir une faible latence.

Le tableau suivant décrit certaines des considérations pour le traitement par lot de sortie :

| Type de sortie | Taille de message maximale | Optimisation de la taille des lots |
| :--- | :--- | :--- |
| Azure Data Lake Store | Consultez [limite de stockage Data Lake](../azure-subscription-service-limits.md#data-lake-store-limits). | Utiliser jusqu'à 4 Mo par opération d’écriture. |
| Azure SQL Database | 10 000 lignes maximum par insertion en bloc unique.<br />100 lignes minimales par insertion en bloc unique. <br />Consultez [SQL Azure limite](../sql-database/sql-database-resource-limits.md). |  Chaque lot est initialement en bloc inséré avec la taille de lot maximale. Vous pouvez fractionner le lot dans la moitié (jusqu'à ce que la taille de lot minimale) basé sur les erreurs renouvelables à partir de SQL. |
| Stockage d'objets blob Azure | Consultez [limites d’Azure Storage](../azure-subscription-service-limits.md#storage-limits). | La taille de bloc maximale d’objet blob est de 4 Mo.<br />Le nombre de bock maximale d’objet blob est de 50 000. |
| Hubs d'événements Azure  | 256 Ko par message. <br />Consultez [Event Hubs limite](../event-hubs/event-hubs-quotas.md). |  Lorsque le partitionnement d’entrée/sortie n’est pas aligné, chaque événement est compressé individuellement dans **EventData** et envoyé dans un lot de jusqu'à la taille maximale des messages (1 Mo pour la référence SKU Premium). <br /><br />  Lorsque le partitionnement d’entrée/sortie est aligné, plusieurs événements sont rassemblés dans un seul **EventData** instance jusqu'à la taille maximale des messages et envoyées.  |
| Power BI | Consultez [limite de l’API Rest Power BI](https://msdn.microsoft.com/library/dn950053.aspx). |
| Stockage de tables Azure | Consultez [limites d’Azure Storage](../azure-subscription-service-limits.md#storage-limits). | La valeur par défaut est 100 entités par une transaction unique. Vous pouvez le configurer sur une valeur inférieure, en fonction des besoins. |
| File d’attente Azure Service Bus   | 256 Ko par message.<br /> Consultez [Service Bus limite](../service-bus-messaging/service-bus-quotas.md). | Utiliser un seul événement par message. |
| Rubrique Azure Service Bus | 256 Ko par message.<br /> Consultez [Service Bus limite](../service-bus-messaging/service-bus-quotas.md). | Utiliser un seul événement par message. |
| Azure Cosmos DB   | Consultez [Azure Cosmos DB limite](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Taille du lot et d’écriture de fréquence sont ajustées de manière dynamique en fonction des réponses d’Azure Cosmos DB. <br /> Il n’existe aucune limitation prédéterminée d’Analytique de Stream. |
| Azure Functions   | | La taille de lot par défaut est de 262 144 octets (256 Ko). <br /> Le nombre d’événements par lot par défaut est 100. <br /> La taille de lot, configurable, peut être augmentée ou diminuée dans les [options de sortie](#azure-functions) de Stream Analytics.

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
