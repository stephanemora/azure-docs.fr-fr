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
ms.openlocfilehash: f2318d3026578aef1e0e5c08d4a816b8f95a366f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448705"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Comprendre les sorties d’Azure Stream Analytics
Cet article décrit les différents types de sorties disponibles pour un travail Azure Stream Analytics. Les sorties permettent de stocker et d’enregistrer les résultats du travail Stream Analytics. Vous pouvez utiliser ces données pour aller plus loin dans l’analyse marketing et l’entreposage de vos données.

Lorsque vous concevez votre requête Stream Analytics, faites référence au nom de la sortie à l’aide de la [clause INTO](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Vous pouvez utiliser une seule sortie par travail, ou si nécessaire plusieurs sorties par travail de diffusion en continu en ajoutant plusieurs clauses INTO dans la requête.

Pour créer, modifier et tester le travail d’Analytique de Stream génère, vous pouvez utiliser la [Azure portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), et [Visual Studio](stream-analytics-quick-create-vs.md).

Certains types de sorties prennent en charge le [partitionnement](#partitioning), et les [tailles de lots de sortie](#output-batch-size) varient afin d’optimiser le débit.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics prend en charge [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store est un référentiel d'entreprise à très grande échelle pour les charges de travail d'analyse du Big Data. Data Lake Store vous permet de stocker des données de toute taille, de tout type et de toute vitesse d’ingestion en vue d’une analyse opérationnelle et exploratoire. Stream Analytics doit être autorisé à accéder à Data Lake Store.

La sortie Azure Data Lake Store de Stream Analytics n’est pas disponible dans les régions Azure - Chine (21Vianet) et Azure - Allemagne (T-Systems International).

### <a name="authorize-an-azure-data-lake-store-account"></a>Autoriser un compte Azure Data Lake Store

1. Lorsque Data Lake Storage est sélectionné en tant que sortie dans le portail Azure, vous êtes invité à autoriser une connexion à un Data Lake Store existant.

   ![Autoriser la connexion à Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Si vous avez déjà accès à Data Lake Store, sélectionnez **Autoriser maintenant**. Une page s’affiche avec le message suivant : **Redirection vers l’autorisation en cours**. Une fois l’autorisation accordée, une page s’affiche pour vous permettre de configurer la sortie Data Lake Store.

3. Une fois le compte Data Lake Store authentifié, vous pouvez configurer les propriétés de votre sortie Data Lake Store. Le tableau ci-dessous répertorie les noms de propriétés et leur description pour configurer votre sortie Data Lake Store.

   ![Définir Data Lake Store en tant que sortie de Stream Analytics](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de la requête vers Data Lake Store. |
| Nom du compte | Nom du compte de stockage Data Lake Storage où vous envoyez votre sortie. Vous accédez à la liste déroulante des comptes Data Lake Store disponibles dans votre abonnement. |
| Modèle de préfixe de chemin d’accès | Chemin de fichier utilisé pour écrire vos fichiers dans le compte Data Lake Store spécifié. Vous pouvez spécifier une ou plusieurs instances des variables {date} et {time}.<br /><ul><li>Exemple 1 : dossier1/journaux/{date}/{heure}</li><li>Exemple 2 : dossier1/journaux/{date}</li></ul><br />L’horodatage de la structure de dossiers créée suit l’heure UTC et pas l’heure locale.<br /><br />Si le modèle de chemin d’accès du fichier ne se termine pas par le caractère « / », le dernier modèle du chemin d’accès du fichier est traité comme préfixe de nom de fichier. <br /><br />De nouveaux fichiers sont créés dans les cas de figure suivants :<ul><li>modification du schéma de sortie ;</li><li>redémarrage externe ou interne d’un travail.</li></ul> |
| Format de la date | facultatif. Si le jeton de la date est utilisé dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés. Exemple : AAAA/MM/JJ |
|Format de l’heure | facultatif. Si le jeton de l’heure est utilisé dans le chemin d’accès du préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisés. Actuellement, la seule valeur possible est HH. |
| Format de sérialisation de l’événement | Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge.|
| Encodage | Si vous utilisez le format CSV ou JSON, vous devez spécifier un encodage. UTF-8 est le seul format de codage actuellement pris en charge.|
| Délimiteur | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale.|
| Format | Applicable uniquement pour la sérialisation JSON. L’expression « Séparé par une ligne » indique que la sortie sera mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme « Tableau » indique que la sortie sera mise en forme en tant que tableau d’objets JSON. Ce tableau se ferme uniquement lorsque le travail s’arrête ou que Stream Analytics est passé à la période suivante. En règle générale, il est préférable d’utiliser du code JSON séparé par des lignes, car il ne requiert aucun traitement spécial pendant que le fichier de sortie est écrit.|

### <a name="renew-data-lake-store-authorization"></a>Renouveler une autorisation Data Lake Store
Vous devez authentifier de nouveau votre compte Data Lake Store si son mot de passe a été modifié depuis la création ou la dernière authentification de votre travail. Si vous ne réauthentifiez pas votre compte, votre travail ne produira pas de résultats en sortie et affichera une erreur indiquant qu’une nouvelle autorisation est nécessaire dans les journaux d’activité des opérations. Il existe actuellement une limitation selon laquelle le jeton d’authentification doit être actualisé manuellement tous les 90 jours pour tous les travaux impliquant une sortie Data Lake Store. Toutefois, vous pouvez contourner cette difficulté en [authentifie à l’aide de managed identités (version préliminaire)](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls).

Pour renouveler l’autorisation, **Arrêtez** votre travail > accédez à votre sortie Data Lake Store > cliquez sur le lien **Renouveler l’autorisation**. Pendant un bref instant, une page s’affiche avec le message suivant : **Redirection vers l’autorisation en cours...**. Elle se ferme automatiquement et indique le message suivant : **L’autorisation a été renouvelée** si l’opération a réussi. Vous devez ensuite cliquer sur **Enregistrer** en bas de la page, puis redémarrer votre travail à partir de **l’heure du dernier arrêt** pour éviter une perte de données.

![Renouveler une autorisation Data Lake Store dans la sortie](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>Base de données SQL
[base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) comme sortie pour les données relationnelles ou pour les applications qui dépendent de contenus hébergés dans une base de données relationnelle. Les travaux Stream Analytics écrivent les données dans une table existante d’une base de données Azure SQL Database. Notez que le schéma de table doit correspondre exactement aux champs et aux types de sortie de votre travail. [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) peut également être spécifié en tant que sortie via l’option de sortie SQL Database. Pour découvrir les moyens d’améliorer le débit d’écriture, consultez l’article [Sortie Stream Analytics dans Azure SQL DB](stream-analytics-sql-output-perf.md). Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de base de données SQL.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette base de données. |
| Base de données | Nom de la base de données où vous envoyez votre sortie. |
| Nom du serveur | Nom du serveur SQL Database. |
| Nom d’utilisateur | Nom de l’utilisateur qui a accès en écriture à la base de données. Stream Analytics prend uniquement en charge l’authentification SQL. |
| Mot de passe | Mot de passe de connexion à la base de données. |
| Table | Nom de la table dans laquelle la sortie sera écrite. Le nom de la table est sensible à la casse et le schéma de cette table doit correspondre exactement au nombre de champs et aux types générés par la sortie de votre travail. |
|Hériter du schéma de partition| Cette propriété vous permet d'hériter du schéma de partition de l'étape de requête précédente afin d'obtenir une topologie entièrement parallèle avec plusieurs rédacteurs dans la table. Pour plus d'informations, consultez [Sortie d'Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Correspondance du nombre de lots| Limite supérieure recommandée pour le nombre d'enregistrements envoyés avec chaque transaction d'insertion en bloc.|

> [!NOTE]
> L’offre de base de données SQL Azure est actuellement prise en charge pour une sortie de tâche dans Stream Analytics. Toutefois, une machine virtuelle de Azure exécutant SQL Server avec une base de données associée n’est pas pris en charge. Cela est susceptible de changer dans des futures versions.
>

## <a name="blob-storage"></a>Stockage d'objets blob
Le stockage d’objets blob offre une solution peu coûteuse et évolutive pour stocker de grandes quantités de données non structurées dans le cloud. Pour une présentation du stockage d’objets blob Azure et de son utilisation, consultez [Utilisation du stockage d’objets blob](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie d’objets blob.

| Nom de la propriété       | Description                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de sortie        | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce stockage d’objets blob. |
| Compte de stockage     | Nom du compte de stockage où vous envoyez votre sortie.               |
| Clé du compte de stockage | Clé secrète associée au compte de stockage.                              |
| Conteneur de stockage   | Les conteneurs fournissent un regroupement logique des objets blob stockés dans le service d’objets blob Microsoft Azure. Lorsque vous téléchargez un objet blob dans le service d'objets Blob, vous devez spécifier un conteneur pour cet objet blob. |
| Modèle de chemin d'accès | facultatif. Modèle de chemin d’accès au fichier utilisé pour écrire vos blobs dans le conteneur spécifié. <br /><br /> Dans le modèle de chemin d’accès, vous pouvez choisir d’utiliser une ou plusieurs instances des variables Date/Heure pour spécifier la fréquence d’écriture des objets blob : <br /> {date}, {time} <br /><br />Vous pouvez utiliser le partitionnement d’objet blob personnalisé afin de spécifier un nom personnalisé {field} de vos données d’événement aux objets blob de partition. Le nom du champ est alphanumérique et peut inclure des espaces, des traits d’union et des traits de soulignement. Voici les restrictions qui s’appliquent aux champs personnalisés : <ul><li>Non-respect de la casse (pas de différence entre « ID » et « id » de colonne)</li><li>impossibilité d’utiliser des champs imbriqués (utiliser un alias dans la requête du travail pour « aplatir » le champ) ;</li><li>Des expressions ne peuvent pas être utilisées comme nom de champ.</li></ul> <br /><br /> Cette fonctionnalité permet également d’utiliser des configurations de spécificateur de format de date/heure personnalisé dans le chemin d’accès. Les formats de date et d’heure personnalisés doivent être spécifiés un par un et délimités par le mot clé {DateHeure :\<spécificateur >}. Les entrées autorisées \<spécificateur > sont yyyy, MM, M, dd, d, HH, H, mm, m, ss ou s. Le mot clé {DateHeure :\<spécificateur >} peut être utilisé plusieurs fois dans le chemin d’accès pour former des configurations de date/d’heure personnalisées. <br /><br />Exemples : <ul><li>Exemple 1 : cluster1/logs/{date}/{time}</li><li>Exemple 2 : cluster1/logs/{date}</li><li>Exemple 3 : cluster1/{client_id}/{date}/{time}</li><li>Exemple 4 : cluster1/{datetime:ss}/{myField}, où la requête est : SELECT data.myField AS myField FROM Input;</li><li>Exemple 5 : cluster1/année = {datetime:yyyy} / mois = {datetime:MM} / jour = {datetime:dd}</ul><br /><br />L’horodatage de la structure de dossiers créée suit l’heure UTC et pas l’heure locale.<br /><br />La procédure d’affection de noms respecte la convention suivante : <br /><br />{Modèle de préfixe de chemin d’accès}/Code_hachage_schéma_Numéro_Guid.extension<br /><br />Exemples de fichier de sortie :<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br /><br /> Pour plus d’informations sur cette fonctionnalité, consultez [Partitionnement de sortie Blob dans Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Format de la date | facultatif. Si le jeton de la date est utilisé dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés. Exemple : AAAA/MM/JJ |
| Format de l’heure | facultatif. Si le jeton de l’heure est utilisé dans le chemin d’accès du préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisés. Actuellement, la seule valeur possible est HH. |
| Format de sérialisation de l’événement | Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage    | Si vous utilisez le format CSV ou JSON, vous devez spécifier un encodage. UTF-8 est le seul format de codage actuellement pris en charge. |
| Délimiteur   | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format      | Applicable uniquement pour la sérialisation JSON. L’expression « Séparé par une ligne » indique que la sortie sera mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme « Tableau » indique que la sortie sera mise en forme en tant que tableau d’objets JSON. Ce tableau se ferme uniquement lorsque le travail s’arrête ou que Stream Analytics est passé à la période suivante. En règle générale, il est préférable d’utiliser du code JSON séparé par des lignes, car il ne requiert aucun traitement spécial pendant que le fichier de sortie est écrit. |

Lorsque vous utilisez le stockage d’objets blob en tant que sortie, un fichier est créé dans l’objet blob dans les cas suivants :

* si le fichier actuel dépasse le nombre maximal de blocs autorisés (50 000 actuellement) (Il est possible d’atteindre cette limite sans atteindre la taille maximale d’objet blob autorisée. Par exemple, si le taux de sortie est élevé, vous voyez apparaître davantage d’octets par bloc, et la taille de fichier est supérieure. S’il est faible, chaque bloc comporte moins de données et cette taille est inférieure.) ;
* si le schéma est modifié dans la sortie, et que le format de cette dernière requiert un schéma fixe (CSV et Avro) ;
* si un travail est relancé, de façon externe par un utilisateur ou en interne à des fins de maintenance système ou de récupération sur erreur ;
* si la requête est entièrement partitionnée (un fichier est créé pour chaque partition de sortie) ;
* si un fichier ou un conteneur du compte de stockage est supprimé par l’utilisateur ;
* si la sortie fait l’objet d’une segmentation temporelle via le modèle de préfixe de chemin d’accès, un nouvel objet blob est utilisé lorsque la requête passe à l’heure suivante.
* si la sortie est partitionnée par un champ personnalisée, un nouvel objet blob est créé par clé de partition s’il n’y en a pas ;
* si la sortie est partitionnée par un champ personnalisé et que la cardinalité des clés de partition est supérieure à 8 000, un nouvel objet blob est créé par clé de partition.

## <a name="event-hub"></a>Event Hub
Le service [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) est un ingesteur d’événements de publication/abonnement hautement évolutif. Il peut collecter des millions d’événements par seconde. Un hub d’événements peut être utilisé en tant que sortie lorsque la sortie d’un travail Stream Analytics correspond à l’entrée d’un autre travail de diffusion.

Quelques paramètres sont requis pour configurer les flux de données Event Hub en tant que sortie.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie | Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cet Event Hub. |
| Espace de noms Event Hub |Conteneur pour un ensemble d’entités de messagerie. En créant un hub d’événements, vous avez également créé un espace de noms Event Hub. |
| Nom de l’Event Hub | Nom de votre sortie Event Hub. |
| Nom de la stratégie du hub d’événements | Stratégie d’accès partagé, qui peut être créée dans l’onglet Configuration du hub d’événements. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de stratégie Event Hub | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Event Hub. |
| Colonne de clé de partition [facultatif] | Cette colonne contient la clé de partition pour la sortie du hub d’événements. |
| Format de sérialisation de l’événement | Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage | Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge. |
| Délimiteur | Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format | Applicable uniquement pour la sérialisation JSON. L’expression « Séparé par une ligne » indique que la sortie sera mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme « Tableau » indique que la sortie sera mise en forme en tant que tableau d’objets JSON. Ce tableau se ferme uniquement lorsque le travail s’arrête ou que Stream Analytics est passé à la période suivante. En règle générale, il est préférable d’utiliser du code JSON séparé par des lignes, car il ne requiert aucun traitement spécial pendant que le fichier de sortie est écrit. |
| Colonnes de propriété [facultatifs] | Les colonnes qui doivent être attachés en tant que propriétés de l’utilisateur d’un message sortant au lieu de la charge utile séparées par des virgules. Plus d’informations sur cette fonctionnalité dans la section « Propriétés de métadonnées personnalisées pour la sortie » |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) peut être utilisé comme sortie d’une tâche Stream Analytics pour fournir une expérience de visualisation riche des résultats d’analyse. Cette fonctionnalité peut être utilisée pour les tableaux de bord opérationnels, la génération de rapports et la création de rapports pilotés par des métriques.

La sortie Power BI de Stream Analytics n’est pas disponible dans les régions Azure - Chine (21Vianet) et Azure - Allemagne (T-Systems International).

### <a name="authorize-a-power-bi-account"></a>Autorisation d’un compte Power BI
1. Lorsque Power BI est sélectionné en tant que sortie dans le portail Azure, vous êtes invité à autoriser un utilisateur Power BI existant ou à créer un compte Power BI.
   
   ![Autoriser un utilisateur de Power BI à configurer la sortie](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Créez un compte si vous n’en avez pas déjà un, puis cliquez sur Autoriser maintenant. La page suivante s’affiche :
   
   ![S’authentifier auprès de Power BI à partir d’un compte Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. Dans cette étape, indiquez le compte professionnel ou scolaire servant à autoriser la sortie Power BI. Si vous n’êtes pas déjà inscrit à Power BI, choisissez S’inscrire maintenant. Le compte professionnel ou scolaire que vous utilisez pour Power BI peut être différent du compte d’abonnement Azure avec lequel vous êtes actuellement connecté.

### <a name="configure-the-power-bi-output-properties"></a>Configuration des propriétés de sortie Power BI
Une fois le compte Power BI authentifié, vous pouvez configurer les propriétés pour votre sortie Power BI. Le tableau ci-dessous répertorie les noms de propriétés et leur description pour configurer votre sortie Power BI.

| Nom de la propriété | description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette sortie Power BI. |
| Espace de travail de groupe |Pour permettre le partage de données avec d’autres utilisateurs de Power BI, vous pouvez sélectionner des groupes à l’intérieur de votre compte Power BI ou choisir « Mon espace de travail » si vous ne souhaitez pas écrire dans un groupe. La mise à jour d’un groupe existant nécessite le renouvellement de l’authentification Power BI. |
| Nom du jeu de données |Fournissez un nom de jeu de données qui doit être utilisé pour la sortie Power BI |
| Nom de la table |Fournissez un nom de table sous le jeu de données de la sortie Power BI. Actuellement, la sortie Power BI des tâches Stream Analytics peut avoir une table uniquement dans un jeu de données. |

Pour une vue d’ensemble de la configuration d’un tableau de bord et d’une sortie Power BI, consultez l’article [Azure Stream Analytics & Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Ne créez pas explicitement le jeu de données et la table dans le tableau de bord Power BI. Le jeu de données et la table seront automatiquement remplis au démarrage du travail et lorsque celui-ci commence à extraire des sorties dans Power BI. Notez que si la requête de travail ne génère aucun résultat, le jeu de données et la table ne seront pas créés. De même, si Power BI dispose déjà d’un jeu de données et d’une table portant le même nom que celui saisi dans le travail Stream Analytics, alors ces données sont écrasées.
>

### <a name="schema-creation"></a>Création d’un schéma
Azure Stream Analytics crée un jeu de données et une table Power BI au nom de l’utilisateur s’il n’en existe pas encore. Dans tous les autres cas, la table est mise à jour de façon à inclure les nouvelles valeurs. Actuellement, il existe une limitation : une seule table peut exister dans un jeu de données. Power BI utilise la stratégie de conservation FIFO. Quand elle est activée, les données sont collectées dans une table jusqu’à ce qu’elle atteigne 200 000 lignes.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Conversion de types de données Stream Analytics vers Power BI
Azure Stream Analytics met à jour le modèle de données dynamiquement lors de l’exécution si le schéma de sortie est modifié. L’intégralité des modifications de nom de colonne, modifications de type de colonne et ajouts ou suppressions de colonnes sont suivis.

Ce tableau décrit les conversions de types de données des [types de données Steam Analytics](https://msdn.microsoft.com/library/azure/dn835065.aspx) vers les [types Entity Data Model (EDM)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) de Power Bi si un jeu de données et une table POWER BI n’existent pas.

De Stream Analytics | Vers Power BI
-----|-----
bigint | Int64
nvarchar(max) | Chaîne
Datetime | DateTime
float | Double
Tableau d’enregistrements | Type chaîne, valeur constante « IRecord » ou « IArray »

### <a name="schema-update"></a>Mise à jour d’un schéma
Steam Analytics déduit le schéma de modèle de données sur la base du premier ensemble d’événements de la sortie. Plus tard, si nécessaire, le schéma de modèle de données est mis à jour pour prendre en compte les événements entrants qui ne correspondent pas au schéma d’origine.

La requête `SELECT *` doit être évitée pour empêcher la mise à jour dynamique du schéma entre les lignes. En plus de l’impact potentiel sur les performances, le temps nécessaire pour la création des résultats devient impossible à déterminer. Les champs exacts qui doivent être présentés dans le tableau de bord Power BI doivent être sélectionnés. En outre, les valeurs de données doivent être compatibles avec le type de données choisi.


Précédent/Actuel | Int64 | Chaîne | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Chaîne | Chaîne | Double
Double | Double | Chaîne | Chaîne | Double
Chaîne | Chaîne | Chaîne | Chaîne | Chaîne 
DateTime | Chaîne | Chaîne |  DateTime | Chaîne


### <a name="renew-power-bi-authorization"></a>Renouvellement de l’autorisation Power BI
Si le mot de passe de votre compte Power BI est modifié après la création ou la dernière authentification de votre travail Stream Analytics, vous devrez effectuer une nouvelle authentification. Par ailleurs, si l’authentification MFA (Multi-Factor Authentication) est configurée sur votre client Azure Active Directory (AAD), vous devrez renouveler l’autorisation Power BI toutes les deux semaines. Un symptôme de ce problème est l’absence de sortie de la tâche et une « erreur d’authentification de l’utilisateur » dans les journaux d’activité des opérations :

  ![Authentifier une erreur de l’utilisateur Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Pour résoudre ce problème, arrêtez votre tâche en cours d'exécution et accédez à votre sortie Power BI. Cliquez sur le lien **Renouveler l’autorisation**, puis redémarrez votre travail à partir de **l’Heure du dernier arrêt** pour éviter de perdre des données.

  ![Renouveler une autorisation Power BI pour la sortie](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Stockage Table
[stockage de tables Azure](../storage/common/storage-introduction.md) offre un stockage hautement disponible et massivement évolutif, afin qu'une application puisse être mise à l'échelle automatiquement pour répondre à la demande des utilisateurs. Le stockage de tables correspond au magasin de clés/attributs NoSQL de Microsoft, qui peut être utilisé pour les données structurées, en présentant moins de contraintes au niveau du schéma. Le stockage des données sur les tables Azure permet d’assurer leur persistance et une récupération efficace.

Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de table.

| Nom de la propriété | description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce stockage de tables. |
| Compte de stockage |Nom du compte de stockage où vous envoyez votre sortie. |
| Clé du compte de stockage |Clé d’accès associée au compte de stockage. |
| Nom de la table |Nom de la table. La table est créée, si elle n’existe pas. |
| Clé de partition |Nom de la colonne de sortie contenant la clé de partition. La clé de partition est un identificateur unique pour la partition dans une table donnée qui constitue la première partie de la clé primaire d’une entité. C’est une valeur de chaîne qui peut atteindre 1 Ko. |
| Clé de ligne |Nom de la colonne de sortie contenant la clé de ligne. La clé de ligne est un identificateur unique pour une entité dans une partition donnée. Elle constitue la deuxième partie de la clé primaire d’une entité. La clé de ligne est une valeur de chaîne qui peut atteindre 1 Ko. |
| Taille du lot |Nombre d’enregistrements d’une opération par lot. La valeur par défaut (100) est suffisante pour la plupart des travaux. Pour plus d’informations sur la modification de ce paramètre, voir [Spécifications des opérations par lot sur les tables](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx). |

## <a name="service-bus-queues"></a>Files d'attente Service Bus
[Files d'attente Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) permettent de livrer des messages selon le principe du premier entré, premier sorti (FIFO) à un ou plusieurs destinataires concurrents. En général, les messages sont reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente ; chaque message est reçu et traité par un seul consommateur de message uniquement.

La table ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de file d’attente.

| Nom de la propriété | description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette file d’attente Service Bus. |
| Espace de noms Service Bus |Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. |
| Nom de la file d'attente |Nom de la file d’attente Service Bus. |
| Nom de la stratégie de file d’attente |Lorsque vous créez une file d’attente, vous pouvez également créer des stratégies d’accès partagé sur l’onglet Configurer de la file d’attente. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie de file d’attente |Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus |
| Format de sérialisation de l’événement |Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage |Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge |
| Délimiteur |Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format |Applicable uniquement pour le type JSON. L’expression « Séparé par une ligne » indique que la sortie sera mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme « Tableau » indique que la sortie sera mise en forme en tant que tableau d’objets JSON. |
| Colonnes de propriété [facultatifs] | Les colonnes qui doivent être attachés en tant que propriétés de l’utilisateur d’un message sortant au lieu de la charge utile séparées par des virgules. Plus d’informations sur cette fonctionnalité dans la section « Propriétés de métadonnées personnalisées pour la sortie » |

Le nombre de partitions est [basé sur la référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.

## <a name="service-bus-topics"></a>Rubriques de Service Bus
Les files d'attente Service Bus offrent une communication de type un-à-un entre l'expéditeur et le destinataire, alors que les [rubriques Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrent une communication de type un-à-plusieurs.

Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de table.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette rubrique Service Bus. |
| Espace de noms Service Bus |Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. En créant un Event Hub, vous avez également créé un espace de noms Service Bus |
| Nom de la rubrique |Les rubriques sont des entités de messagerie qui sont similaires aux files d’attente et aux hubs d’événements. Il est conçu pour recevoir des flux d’événements à partir d’appareils et de services divers. Quand une rubrique est créée, elle reçoit également un nom. Les messages envoyés à une rubrique ne sont disponibles que si un abonnement est créé. Assurez-vous donc que la rubrique comprend un ou plusieurs abonnements. |
| Nom de la stratégie de rubrique |Lorsque vous créez une rubrique, vous pouvez également créer des stratégies d’accès partagé sur l’onglet Configurer de la rubrique. Chaque stratégie d’accès partagé présente un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie de rubrique |Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus |
| Format de sérialisation de l’événement |Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge. |
| Encodage |Si vous utilisez le format CSV ou JSON, vous devez spécifier un encodage. UTF-8 est le seul format d’encodage actuellement pris en charge |
| Délimiteur |Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Colonnes de propriété [facultatifs] | [Facultatif] Les colonnes qui doivent être attachés en tant que propriétés de l’utilisateur d’un message sortant au lieu de la charge utile séparées par des virgules. Plus d’informations sur cette fonctionnalité dans la section « Propriétés de métadonnées personnalisées pour la sortie » |

Le nombre de partitions est [basé sur la référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) est un service de base de données multimodèle distribué à l’échelle mondiale, qui offre une mise à l’échelle élastique et sans limite dans le monde entier, des requêtes enrichies et une indexation automatique sur les modèles de données indépendants des schémas, la garantie d’une latence faible et des contrats de niveau de service complets de haut niveau. Pour en savoir plus sur les options de collection Cosmos DB pour Stream Analytics, consultez l’article [Stream Analytics avec une sortie Cosmos DB](stream-analytics-documentdb-output.md).

La sortie Azure Cosmos DB de Stream Analytics n’est pas disponible dans les régions Azure - Chine (21Vianet) et Azure - Allemagne (T-Systems International).

> [!Note]
> Pour le moment, Azure Stream Analytics prend uniquement en charge la connexion à CosmosDB à l’aide de **l’API SQL**.
> Les autres API Azure Cosmos DB ne sont pas encore prises en charge. Si vous pointez Azure Stream Analytics vers les comptes Azure Cosmos DB créés avec d'autres API, les données risquent de ne pas être correctement stockées.

Le tableau suivant décrit les propriétés de création d’une sortie Azure Cosmos DB.

| Nom de la propriété | description |
| --- | --- |
| Alias de sortie | Alias référençant cette sortie dans votre requête Stream Analytics. |
| Récepteur | Cosmos DB |
| Option d’importation | Choisissez « Sélectionner Cosmos DB dans votre abonnement » ou « Fournir manuellement les paramètres Cosmos DB ».
| ID de compte | Nom ou URI de point de terminaison du compte Cosmos DB. |
| Clé de compte | Clé d’accès partagé du compte Cosmos DB. |
| Base de données | Nom de la base de données Cosmos DB. |
| Modèle de nom de collection | Nom ou modèle des collections à utiliser. <br />Le format de nom de collection peut être construit à l’aide du jeton facultatif {partition}, où les partitions commencent à 0. Deux exemples :  <br />1. _MyCollection_ : Une collection nommée « MyCollection » doit exister.  <br />2. _MyCollection{partition}_ : Basé sur la colonne de partitionnement. <br />Les collections de colonne de partitionnement doivent exister, par exemple, « MyCollection0 », « MyCollection1 », « MyCollection2 », etc. |
| Partition Key | facultatif. Nécessaire uniquement si vous utilisez un jeton {partition} dans votre modèle de nom de collection.<br /> La clé de partition est le nom de champ dans les événements de sortie utilisé pour spécifier la clé de partitionnement de sortie sur les collections.<br /> Pour une sortie de collection unique, n’importe quelle colonne de sortie arbitraire peut être utilisée (par exemple, PartitionId). |
| ID du document |facultatif. Nom du champ dans les événements de sortie utilisé pour spécifier la clé primaire sur laquelle sont basées les opérations d’insertion ou de mise à jour.

## <a name="azure-functions"></a>Azure Functions
Azure Functions est un service de calcul sans serveur qui vous permet d’exécuter du code à la demande sans explicitement configurer ou gérer l’infrastructure. Grâce à ce service, vous pouvez implémenter le code qui est déclenché par les événements qui se produisent dans Azure ou des services tiers. Comme Azure Functions peut répondre à des déclencheurs, il constitue l’outil de sortie logique pour Azure Stream Analytics. Cet adaptateur de sortie permet aux utilisateurs de connecter Stream Analytics à Azure Functions et d’exécuter un script ou un fragment de code en réaction à différents événements.

La sortie Azure Functions de Stream Analytics n’est pas disponible dans les régions Azure - Chine (21Vianet) et Azure - Allemagne (T-Systems International).

Azure Stream Analytics appelle Azure Functions via des déclencheurs HTTP. Le nouvel adaptateur de sortie Azure Stream Analytics est disponible, avec les propriétés configurables suivantes :

| Nom de la propriété | description |
| --- | --- |
| Conteneur de fonctions |Nom de votre application Azure Function App |
| Fonction |Nom de la fonction dans votre application Azure Function App |
| Clé |Si vous souhaitez utiliser une fonction Azure à partir d’un autre abonnement, vous pouvez le faire en fournissant la clé permettant d’accéder à votre fonction. |
| Taille de lot maximale |Cette propriété peut être utilisée pour définir la taille maximale de chaque lot de sortie qui est envoyé à votre application Azure Functions. L’unité d’entrée est exprimée en octets. Par défaut, cette valeur est définie sur 262 144 Ko (256 Ko) |
| Nombre maximal de lots  |Comme son nom l’indique, cette propriété vous permet de spécifier le nombre maximal d’événements dans chaque lot envoyé à Azure Functions. Par défaut, cette valeur est de 100. |

Lorsque Azure Stream Analytics reçoit une exception 413 (l’entité de requête HTTP est trop volumineuse) de la part d’une fonction Azure, il réduit la taille des lots envoyés à Azure Functions. Dans le code de votre fonction Azure, utilisez cette exception pour vous assurer qu’Azure Stream Analytics n’envoie pas de lots trop volumineux. Vérifiez également que les valeurs de taille et de nombre de lots maximum utilisées dans la fonction correspondent à celles qui ont été saisies dans le portail Stream Analytics.

De plus, si aucun événement n’est signalé dans le temps imparti, aucune sortie n’est générée. De ce fait, la fonction computeResult n’est pas appelée. Ce comportement est cohérent avec les fonctions d’agrégation fenêtrées intégrées.

## <a name="custom-metadata-properties-for-output"></a>Propriétés de métadonnées personnalisées pour la sortie 

Cette fonctionnalité permet l’attachement des colonnes de la requête en tant que propriétés de l’utilisateur aux messages sortants. Ces colonnes n’entrent pas dans la charge utile. Ces propriétés sont présentes sous la forme d’un dictionnaire sur le message de sortie. Clé est le nom de colonne et la valeur est la valeur de colonne dans le dictionnaire de propriétés. Tous les types de données Analytique de Stream sont pris en charge à l’exception d’enregistrement et de tableau.  

Sorties prises en charge : 
* Files d'attente Service Bus 
* Rubriques de Service Bus 
* Event Hub 

Exemple : Dans l’exemple suivant, nous allons ajouter les 2 champs DeviceId et DeviceStatus aux métadonnées. 
* Requête : `select *, DeviceId, DeviceStatus from iotHubInput` .
* Configuration de sortie : `DeviceId,DeviceStatus`.

![Colonnes de propriété](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Propriétés du Message inspectées EventHub à l’aide de la sortie [Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer).

   ![Propriétés de l’événement personnalisées](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Partitionnement

Le tableau suivant récapitule la prise en charge de la partition et le nombre de générateurs de sortie pour chaque type de sortie :

| Type de sortie | Prise en charge du partitionnement | Clé de partition  | Nombre de générateurs de sortie |
| --- | --- | --- | --- |
| Azure Data Lake Store | Oui | Utilisez les jetons {date} et {time} dans le modèle de préfixe du chemin. Choisissez le format de date, par exemple, AAAA/MM/JJ, JJ/MM/AAAA, MM-JJ-AAAA. HH est utilisé pour le format de l’heure. | Suit le partitionnement d’entrée des [requêtes entièrement parallélisables](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Oui | Basée sur la clause PARTITION BY dans la requête | Suit le partitionnement d’entrée des [requêtes entièrement parallélisables](stream-analytics-scale-jobs.md). Pour améliorer vos performances de débit d'écriture lorsque vous chargez des données dans Azure SQL Database, consultez l'article [Sortie Azure Stream Analytics vers Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Stockage d'objets blob Azure | Oui | Utilisez les jetons {date} et {time} de vos champs d’événement dans le modèle de chemin. Choisissez le format de date, par exemple, AAAA/MM/JJ, JJ/MM/AAAA, MM-JJ-AAAA. HH est utilisé pour le format de l’heure. La sortie d’objet blob peut être partitionnée par un seul attribut d’événement personnalisé {fieldname} ou par {datetime:\<specifier>}. | Suit le partitionnement d’entrée des [requêtes entièrement parallélisables](stream-analytics-scale-jobs.md). |
| Azure Event Hub | Oui | Oui | Dépend de l’alignement des partitions.<br /> Lorsque la clé de partition Event Hub de sortie s’aligne parfaitement avec l’étape de requête (précédente) en amont, le nombre d’enregistreurs est égal au nombre de partitions Event Hub de sortie. Chaque enregistreur utilise la [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) d’EventHub pour envoyer des événements à la partition concernée. <br /> Lorsque la clé de partition Event Hub de sortie ne s’aligne pas avec l’étape de requête (précédente) en amont, le nombre d’enregistreurs est égal au nombre de partitions de cette précédente étape. Chaque enregistreur utilise la [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) d’EventHubClient pour envoyer des événements à toutes les partitions de sortie. |
| Power BI | Non  | Aucun | Non applicable. |
| Stockage de tables Azure | Oui | N’importe quelle colonne de sortie.  | Suit le partitionnement d’entrée des [requêtes entièrement mises en parallèle ](stream-analytics-scale-jobs.md). |
| Rubrique Azure Service Bus | Oui | Choisi automatiquement. Le nombre de partitions est basé sur la [référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.| Égal au nombre de partitions de la rubrique de sortie.  |
| File d’attente Azure Service Bus | Oui | Choisi automatiquement. Le nombre de partitions est basé sur la [référence Service Bus et sa taille](../service-bus-messaging/service-bus-partitioning.md). La clé de partition est une valeur entière unique pour chaque partition.| Égal au nombre de partitions de la file d’attente de sortie. |
| Azure Cosmos DB | Oui | Utilisez le jeton {partition} dans le modèle de nom de collection. La valeur de {partition} est basée sur la clause PARTITION BY dans la requête. | Suit le partitionnement d’entrée des [requêtes entièrement mises en parallèle ](stream-analytics-scale-jobs.md). |
| Azure Functions | Non  | Aucun | Non applicable. |

Si votre adaptateur de sortie n'est pas partitionné, l'absence de données dans une partition d'entrée entraînera un retard pouvant aller jusqu'au délai d'arrivée tardive.  Dans ce cas, la sortie est fusionnée dans un seul writer, ce qui peut entraîner des goulots d'étranglement dans votre pipeline. Pour en savoir plus sur la stratégie d'arrivée tardive, consultez l'article [Considérations relatives à l’ordre des événements Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Taille de lot de sortie
Azure Stream Analytics utilise des lots de taille variable pour traiter les événements et générer les sorties. En général, le moteur n’écrit pas les messages un par un ; il utilise des lots pour plus d’efficacité. Lorsque le taux d’événements entrants et le taux d’événements sortants sont tous deux élevés, il utilise de plus gros lots. Lorsque le taux de sortie est faible, il utilise des lots plus petits pour maintenir une faible latence.

Le tableau suivant expose certaines considérations relatives au traitement par lots de la sortie :

| Type de sortie | Taille de message maximale | Optimisation de la taille des lots |
| :--- | :--- | :--- |
| Azure Data Lake Store | Voir [Limites de stockage de Data Lake](../azure-subscription-service-limits.md#data-lake-store-limits) | Jusqu’à 4 Mo par opération d’écriture. |
| Azure SQL Database | 10 000 lignes maximum par insertion en bloc<br />100 lignes minimum par insertion en bloc <br />Voir aussi [Limites d’Azure SQL](../sql-database/sql-database-resource-limits.md) |  Chaque lot est initialement inséré en bloc avec la taille de lot maximale et peut être fractionné en deux moitiés (jusqu’à la taille de lot minimale) en fonction des erreurs récupérables provenant de SQL. |
| Stockage d'objets blob Azure | Voir [Limites du Stockage Azure](../azure-subscription-service-limits.md#storage-limits) | Taille maximale de bloc blob : 4 Mo.<br />Nombre maximal de blocs blob : 50 000. |
| Azure Event Hub   | 256 Ko par message <br />Voir aussi [Limites d’Event Hubs](../event-hubs/event-hubs-quotas.md) |   Lorsque le partitionnement Entrée/Sortie ne s’aligne pas, chaque événement est empaqueté individuellement dans un EventData et envoyé dans un lot dans la limite de la taille de message maximale (1 Mo pour la référence SKU Premium). <br /><br />  Lorsque le partitionnement Entrée/Sortie est aligné, plusieurs événements sont empaquetés dans un seul EventData, dans la limite de la taille de message maximale, et envoyés.  |
| Power BI | Voir [Limites de l’API REST Power BI](https://msdn.microsoft.com/library/dn950053.aspx) |
| Stockage de tables Azure | Voir [Limites du Stockage Azure](../azure-subscription-service-limits.md#storage-limits) | Par défaut, 100 entités par transaction ; configurable sur une valeur inférieure si besoin. |
| File d’attente Azure Service Bus   | 256 Ko par message<br /> Voir aussi [Limites de Service Bus](../service-bus-messaging/service-bus-quotas.md) | Un événement unique par message. |
| Rubrique Azure Service Bus | 256 Ko par message<br /> Voir aussi [Limites de Service Bus](../service-bus-messaging/service-bus-quotas.md) | Un événement unique par message. |
| Azure Cosmos DB   | Voir [Limites d’Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits) | La taille des lots et la fréquence d’écriture sont ajustées dynamiquement en fonction des réponses CosmosDB. <br /> Stream Analytics n’impose aucune limite prédéterminée. |
| Azure Functions   | | Taille de lot par défaut : 262 144 Ko (256 Ko). <br /> Nombre d’événements par défaut par lot : 100. <br /> La taille de lot, configurable, peut être augmentée ou diminuée dans les [options de sortie](#azure-functions) de Stream Analytics.

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
