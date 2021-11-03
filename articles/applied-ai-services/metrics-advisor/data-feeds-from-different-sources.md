---
title: Connecter différentes sources de données à Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Ajouter différents flux de données à Metrics Advisor
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: mbullwin
ms.openlocfilehash: 005886c0399a89fe2e58aa669a8f7c0d33821cd3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026635"
---
# <a name="how-to-connect-different-data-sources"></a>Guide pratique : Connecter différentes sources de données

Utilisez cet article pour rechercher les paramètres et les exigences relatifs à la connexion de différents types de sources de données à Azure Metrics Advisor. Pour en savoir plus sur l’utilisation de vos données avec Metrics Advisor, consultez [Intégrer vos données](how-tos/onboard-your-data.md). 

## <a name="supported-authentication-types"></a>Types d’authentification pris en charge

| Types d’authentification | Description |
| ---------------------|-------------|
|**De base** | Vous devez fournir des paramètres de base pour accéder aux sources de données. Par exemple, vous pouvez utiliser une chaîne de connexion ou un mot de passe. Les administrateurs du flux de données peuvent afficher ces informations d’identification. |
| **Identité managée Azure** | [Identités managées](../../active-directory/managed-identities-azure-resources/overview.md) pour les ressources Azure est une fonctionnalité d’Azure Active Directory (Azure AD). Elle fournit aux services Azure une identité managée automatiquement dans Azure AD. Vous pouvez utiliser cette identité pour authentifier tous les services qui prennent en charge l’authentification Azure AD.|
| **Chaîne de connexion Azure SQL**| Stockez votre chaîne de connexion Azure SQL en tant qu’entité d’informations d’identification dans Metrics Advisor et utilisez-la directement chaque fois que vous importez des données de mesures. Seuls les administrateurs de l’entité d’informations d’identification peuvent voir ces informations d’identification, mais les lecteurs autorisés peuvent créer des flux de données sans avoir besoin de connaître les détails des informations d’identification. |
| **Clé partagée Azure Data Lake Storage Gen2**| Stockez votre clé de compte de lac de données en tant qu’entité d’informations d’identification dans Metrics Advisor et utilisez-la directement chaque fois que vous importez des données de mesures. Seuls les administrateurs de l’entité d’informations d’identification peuvent voir ces informations d’identification, mais les lecteurs autorisés peuvent créer des flux de données sans avoir besoin de connaître les détails des informations d’identification.|
| **Principal du service**| Stockez votre [principal de service](../../active-directory/develop/app-objects-and-service-principals.md) en tant qu’entité d’informations d’identification dans Metrics Advisor, et utilisez celle-ci directement chaque fois que vous importez des données de mesures. Seuls les administrateurs de l’entité d’informations d’identification peuvent voir les informations d’identification, mais les lecteurs autorisés peuvent créer des flux de données sans avoir besoin de connaître les détails des informations d’identification.|
| **Principal de service du coffre de clés**|Stockez votre [principal de service dans coffre de clés](/azure-stack/user/azure-stack-key-vault-store-credentials) en tant qu’entité d’informations d’identification dans Metrics Advisor, et utilisez celle-ci directement chaque fois que vous importez des données de mesures. Seuls les administrateurs d’une entité d’informations d’identification peuvent voir les informations d’identification, mais les lecteurs peuvent créer des flux de données sans avoir besoin de connaître les détails des informations d’identification. |


## <a name="data-sources-and-corresponding-authentication-types"></a>Sources de données et types d’authentification correspondants

| Sources de données | Types d’authentification |
|-------------| ---------------------|
|[Application Insights](#appinsights) | De base |
|[Stockage Blob Azure (JSON)](#blob) | De base<br>Identité managée |
|[Azure Cosmos DB (SQL)](#cosmosdb) | De base |
|[Azure Data Explorer (Kusto)](#kusto) | De base<br>Identité managée<br>Principal du service<br>Principal de service du coffre de clés |
|[Azure Data Lake Storage Gen2](#adl) | De base<br>Clé partagée Data Lake Storage Gen2<br>Principal du service<br>Principal de service du coffre de clés |
|[Azure Event Hubs](#eventhubs) | De base |
|[Journaux Azure Monitor](#log) | De base<br>Principal du service<br>Principal de service du coffre de clés |
|[Azure SQL Database / SQL Server](#sql) | De base<br>Identité managée<br>Principal du service<br>Principal de service du coffre de clés<br>Chaîne de connexion Azure SQL |
|[Stockage de tables Azure](#table) | De base | 
|[InfluxDB (InfluxQL)](#influxdb) | De base |
|[MongoDB](#mongodb) | De base |
|[MySQL](#mysql) | De base |
|[PostgreSQL](#pgsql) | De base|
|[Fichiers locaux (CSV)](#csv) | De base|

Les sections suivantes spécifient les paramètres requis pour tous les types d’authentification dans différents scénarios de source de données. 

## <a name="span-idappinsightsapplication-insightsspan"></a><span id="appinsights">Application Insights</span>

* **ID d’application** : Il est utilisé pour identifier cette application lorsque vous utilisez l’API Application Insights. Pour obtenir l’ID d’application, suivez ces étapes :

   1. À partir de votre ressource Application Insights, sélectionnez **Accès API**.
   
      ![Capture d’écran montrant comment récupérer l’ID d’application à partir de votre ressource Application Insights.](media/portal-app-insights-app-id.png)

   2. Copiez l’ID d’application généré dans le champ **ID d’application** dans Metrics Advisor. 

* **Clé API** : Les clés API sont utilisées par les applications en dehors du navigateur pour accéder à cette ressource. Pour obtenir la clé API, procédez comme suit :

   1. À partir de la ressource Application Insights, sélectionnez **Accès API**.

   2. Sélectionnez **Créer une clé API**.

   3. Entrez une brève description, sélectionnez l’option **Lire les données de télémétrie**, puis sélectionnez **Générer la clé**.

      ![Capture d’écran montrant comment récupérer la clé API dans le portail Azure.](media/portal-app-insights-app-id-api-key.png)

       > [!IMPORTANT]
       > Copiez et enregistrez cette clé API. Elle ne vous sera plus jamais montrée. Si vous perdez cette clé, vous devez en créer une autre.

   4. Copiez la clé API dans le champ **Clé API** dans Metrics Advisor.

* **Requête** : Les journaux Application Insights reposent sur Azure Data Explorer ; les requêtes des journaux d’activité Azure Monitor utilisent une version du même langage de requête Kusto. La [documentation du langage de requête Kusto](/azure/data-explorer/kusto/query) doit constituer votre principale référence pour écrire une requête par rapport à Application Insights. 

    Exemple de requête :

    ``` Kusto
    [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);
    ```
    Vous pouvez également vous référer à [Tutoriel : Écrire une requête valide](tutorials/write-a-valid-query.md) pour des exemples plus spécifiques.
  
## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Stockage Blob Azure (JSON)</span>

* **Chaîne de connexion** : Il existe deux types d’authentification pour Stockage Blob Azure (JSON) :

    * **De base** : Consultez [Configurer des chaînes de connexion Stockage Azure](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account) pour plus d’informations sur la récupération de cette chaîne. Vous pouvez également vous rendre sur le portail Azure, accéder à votre ressource Stockage Blob Azure et trouver la chaîne de connexion directement dans **Paramètres** > **Clés d’accès**.
    
    * **Identité managée** : Les identités managées pour les ressources Azure peuvent autoriser l’accès aux données de blob et de file d’attente. Cette fonctionnalité utilise les informations d’identification Azure AD des applications qui s’exécutent dans les machines virtuelles Azure, les applications de fonction, les groupes de machines virtuelles identiques et d’autres services. 
    
        Vous pouvez créer une identité managée dans le portail Azure pour votre ressource Stockage Blob Azure. Dans **Contrôle d’accès (IAM)** , sélectionnez **Attributions de rôle**, puis sélectionnez **Ajouter**. Un type de rôle suggéré est : **Lecteur des données BLOB du stockage**. Pour plus d’informations, consultez [Utiliser une identité managée pour accéder à Stockage Azure](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access-1).
    
        ![Capture d’écran montrant un blob d’identité managée.](media/managed-identity-blob.png)
    

* **Conteneur** : Metrics Advisor s’attend à ce que les données de séries chronologiques soient stockées sous forme de fichiers blob (un blob par timestamp), sous un seul conteneur. Il s’agit du champ nom du conteneur.

* **Modèle de blob** : Metrics Advisor utilise un chemin d’accès pour trouver le fichier JSON dans Stockage Blob. Voici un exemple de modèle de fichier blob, qui est utilisé pour trouver le fichier JSON dans Stockage Blob :`%Y/%m/FileName_%Y-%m-%d-%h-%M.json`. `%Y/%m` est le chemin d’accès et, si vous avez `%d` dans votre chemin d’accès, vous pouvez l’ajouter après `%m`. Si votre fichier JSON est nommé par date, vous pouvez également utiliser `%Y-%m-%d-%h-%M.json`.

   Les paramètres suivants sont pris en charge :
   
   * `%Y` est l’année au format `yyyy`.
   * `%m` est le mois au format `MM`.
   * `%d` est le jour au format `dd`.
   * `%h` est l’heure au format `HH`.
   * `%M` est la minute au format `mm`.
  
   Par exemple, dans le jeu de données suivant, le modèle de blob doit être `%Y/%m/%d/00/JsonFormatV2.json`.
  
   ![Capture d’écran montrant le modèle de blob.](media/blob-template.png)
  

* **Version du format JSON** : Définit le schéma de données dans les fichiers JSON. Metrics Advisor prend en charge les versions suivantes. Vous pouvez en choisir une pour remplir le champ :
  
   * **v1** (valeur par défaut)

      Seuls *Nom* et *Valeur* des mesures sont acceptés. Exemple :
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

   * **v2**

      Les *Dimensions* et *timestamp* des mesures sont également acceptés. Exemple :
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

   Un seul timestamp est autorisé par fichier JSON. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Chaîne de connexion** : La chaîne de connexion pour accéder à votre compte Azure Cosmos DB. Elle se trouve dans la ressource Azure Cosmos DB dans le portail Azure, dans **Clés**. Pour plus d’informations, consultez [Sécuriser l’accès aux données dans Azure Cosmos DB](../../cosmos-db/secure-access-to-data.md).
* **Base de données** : La base de données à interroger. Dans le portail Azure, sous **Conteneurs**, cliquez sur **Parcourir** pour rechercher la base de données.
* **ID de la collection** : ID de collection sur lequel effectuer la requête. Dans le portail Azure, sous **Conteneurs**, cliquez sur **Parcourir** pour rechercher l’ID de collection.
* **Requête SQL** : Une requête SQL permettant d’obtenir et de formuler des données dans des données de séries chronologiques multidimensionnelles. Vous pouvez utiliser les variables `@IntervalStart` et `@IntervalEnd` dans votre requête. Elles doivent utiliser le format suivant : `yyyy-MM-ddTHH:mm:ssZ`.

    Exemple de requête :
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```

    Pour plus d’informations, référez-vous au [tutoriel sur l’écriture d’une requête valide](tutorials/write-a-valid-query.md).

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **Chaîne de connexion** : Il existe quatre types d’authentification pour Azure Data Explorer (Kusto), à savoir les types de base, principal de service, principal de service du coffre de clés et identité managée. La source de données dans la chaîne de connexion doit être au format URI (commence par « https »). Vous pouvez trouver l’URI sur le portail Azure.
    
    * **De base** : Metrics Advisor prend en charge l’accès à Azure Data Explorer (Kusto) à l’aide de l’authentification d’application Azure AD. Vous devez créer et inscrire une application Azure AD, puis l’autoriser à accéder à une base de données Azure Data Explorer. Pour plus d’informations, consultez [Créer une inscription d’application Azure AD dans Azure Data Explorer](/azure/data-explorer/provision-azure-ad-app). Voici un exemple de chaîne de connexion :
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>;AAD Federated Security=True;Application Client ID=<Application Client ID>;Application Key=<Application Key>;Authority ID=<Tenant ID>
        ```

    * **Principal de service** : Un principal de service est une instance concrète créée à partir de l’objet d’application. Le principal de service hérite de certaines propriétés de cet objet d’application. L’objet principal de service définit ce que l’application peut réellement faire dans le locataire spécifique, qui peut accéder à l’application, ainsi que les ressources auxquelles l’application peut accéder. Pour utiliser un principal de service dans Metrics Advisor :
    
        1. Créez l’inscription d’application Azure AD. Pour plus d’informations, consultez [Créer une inscription d’application Azure AD dans Azure Data Explorer](/azure/data-explorer/provision-azure-ad-app).

        1. Gérez les autorisations de base de données Azure Data Explorer. Pour plus d’informations, consultez [Gérer les autorisations de base de données d’Azure Data Explorer](/azure/data-explorer/manage-database-permissions). 

        1. Créez une entité d’informations d’identification dans Metrics Advisor. Découvrez comment [créer une entité d’informations d’identification](how-tos/credential-entity.md) dans Metrics Advisor afin de pouvoir choisir cette entité lorsque vous ajoutez un flux de données pour le type d’authentification du principal de service. 
        
        Voici un exemple de chaîne de connexion :
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **Principal de service du coffre de clés** : Azure Key Vault permet de protéger les clés de chiffrement et les valeurs secrètes utilisées par les applications et services cloud. Key Vault vous permet de chiffrer les clés et les valeurs secrètes. Vous devez d’abord créer un principal de service, puis le stocker dans le coffre de clés. Pour plus d’informations, consultez [Créer une entité d’informations d’identification pour le principal de service du coffre de clés](how-tos/credential-entity.md#sp-from-kv) pour suivre la procédure détaillée de définition du principal de service du coffre de clés. Voici un exemple de chaîne de connexion : 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **Identité managée** : L’identité managée pour les ressources Azure peut autoriser l’accès aux données de blob et de file d’attente. L’identité managée utilise les informations d’identification Azure AD des applications qui s’exécutent dans les machines virtuelles Azure, les applications de fonction, les groupes de machines virtuelles identiques et d’autres services. En utilisant une identité managée pour ressources Azure et l’authentification Azure AD, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud. Découvrez comment [autoriser avec une identité managée](../../storage/blobs/authorize-managed-identity.md#enable-managed-identities-on-a-vm). 
    
        Vous pouvez créer une identité managée dans le portail Azure pour votre instance Azure Data Explorer (Kusto). Sélectionnez **Autorisations** > **Ajouter**. Le type de rôle suggéré est : **administrateur/lecteur**.
        
        ![Capture d’écran montrant l’identité managée pour Kusto.](media/managed-identity-kusto.png)

        Voici un exemple de chaîne de connexion : 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

     
* **Requête** : Pour obtenir et formuler des données dans des données de séries chronologiques multidimensionnelles, consultez [Langage de la requête Kusto](/azure/data-explorer/kusto/query). Vous pouvez utiliser les variables `@IntervalStart` et `@IntervalEnd` dans votre requête. Elles doivent utiliser le format suivant : `yyyy-MM-ddTHH:mm:ssZ`.

  Exemple de requête :
    
  ```kusto
  [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);    
  ```

  Pour plus d’informations, référez-vous au [tutoriel sur l’écriture d’une requête valide](tutorials/write-a-valid-query.md).

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **Nom du compte** : Les types d’authentification pour Azure Data Lake Storage Gen2 sont les types de base, clé partagée Azure Data Lake Storage Gen2, principal de service et principal de service du coffre de clés.
    
    * **De base** : Le **nom de compte** de votre compte Azure Data Lake Storage Gen2. Vous pouvez le trouver dans votre ressource de compte de stockage Azure (Azure Data Lake Storage Gen2), dans **Clés d’accès**. 

    * **Clé partagée Azure Data Lake Storage Gen2** : Tout d’abord, vous spécifiez la clé de compte pour accéder à votre compte Azure Data Lake Storage Gen2 (il s’agit de la même clé de compte que celle du type d’authentification de base). Vous pouvez le trouver dans votre ressource de compte de stockage Azure (Azure Data Lake Storage Gen2), dans **Clés d’accès**. Ensuite, vous [créez une entité d’informations d’identification](how-tos/credential-entity.md) pour le type de clé partagée Azure Data Lake Storage Gen2, et vous renseignez la clé de compte. 

        Le nom du compte est le même que le type d’authentification de base.
    
    * **Principal de service** : Un *principal de service* est une instance concrète créée à partir de l’objet d’application, et il hérite de certaines propriétés de cet objet d’application. Un principal de service est créé dans chaque locataire dans lequel l’application est utilisée, et il fait référence à l’objet application global unique. L’objet principal de service définit ce que l’application peut réellement faire dans le locataire spécifique, qui peut accéder à l’application, ainsi que les ressources auxquelles l’application peut accéder.

        Le nom du compte est le même que le type d’authentification de base.
    
        **Étape 1 :** Créez et inscrivez une application Azure AD, puis autorisez-la à accéder à la base de données. Pour plus d’informations, consultez [Créer une inscription d’application Azure AD](/azure/data-explorer/provision-azure-ad-app).

        **Étape 2 :** Attribuez des rôles.
        
        1. Dans le portail Azure, accédez au service **Comptes de stockage**.
        
        2. Sélectionnez le compte Azure Data Lake Storage Gen2 à utiliser avec cette inscription d’application.

        3. Sélectionnez **Contrôle d’accès (IAM)** .

        4. Sélectionnez **+ Ajouter**, puis sélectionnez **Ajouter une attribution de rôle** à partir du menu.

        5. Définissez le champ **Sélectionner** sur le nom de l’application Azure AD, puis définissez le rôle sur **Contributeur aux données Blob du stockage**. Ensuite, sélectionnez **Enregistrer**.
        
        ![Capture d’écran montrant les étapes à suivre pour attribuer des rôles.](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

        **Étape 3 :** [Créez une entité d’informations d’identification](how-tos/credential-entity.md) dans Metrics Advisor, afin de pouvoir choisir cette entité lorsque vous ajoutez un flux de données pour le type d’authentification du principal de service. 
        
    * **Principal de service du coffre de clés** : Key Vault permet de protéger les clés de chiffrement et les valeurs secrètes utilisées par les applications et services cloud. Key Vault vous permet de chiffrer les clés et les valeurs secrètes. Créez d’abord un principal de service, puis stockez-le dans un coffre de clés. Pour plus d’informations, consultez [Créer une entité d’informations d’identification pour le principal de service du coffre de clés](how-tos/credential-entity.md#sp-from-kv). Le nom du compte est le même que le type d’authentification de base.

* **Clé de compte** (nécessaire uniquement pour le type d’authentification de base) : Spécifiez la clé de compte pour accéder à votre compte Azure Data Lake Storage Gen2. Vous pouvez le trouver dans votre ressource de compte de stockage Azure (Azure Data Lake Storage Gen2), dans **Clés d’accès**.

* **Nom du système de fichiers (conteneur)**  : Pour Metrics Advisor, vous stockez vos données de séries chronologiques sous forme de fichiers blob (un blob par timestamp) ,sous un seul conteneur. Il s’agit du champ nom du conteneur. Vous pouvez le trouver dans votre instance de compte de stockage Azure (Azure Data Lake Storage Gen2). Dans **Data Lake Storage**, sélectionnez **Conteneurs**, et vous verrez le nom du conteneur.

* **Modèle de répertoire** : Il s’agit du modèle de répertoire du fichier blob. Les paramètres suivants sont pris en charge :

   * `%Y` est l’année au format `yyyy`.
   * `%m` est le mois au format `MM`.
   * `%d` est le jour au format `dd`.
   * `%h` est l’heure au format `HH`.
   * `%M` est la minute au format `mm`.

   Exemple de requête pour une métrique quotidienne : `%Y/%m/%d`.

   Exemple de requête pour une métrique horaire : `%Y/%m/%d/%h`.

* **Modèle de fichier** : Metrics Advisor utilise un chemin d’accès pour trouver le fichier JSON dans Stockage Blob. Voici un exemple de modèle de fichier blob, qui est utilisé pour trouver le fichier JSON dans Stockage Blob :`%Y/%m/FileName_%Y-%m-%d-%h-%M.json`. `%Y/%m` est le chemin d’accès et, si vous avez `%d` dans votre chemin d’accès, vous pouvez l’ajouter après `%m`. 
   
   Les paramètres suivants sont pris en charge :
   
   * `%Y` est l’année au format `yyyy`.
   * `%m` est le mois au format `MM`.
   * `%d` est le jour au format `dd`.
   * `%h` est l’heure au format `HH`.
   * `%M` est la minute au format `mm`.

   Metrics Advisor prend en charge le schéma de données dans les fichiers JSON, comme dans l’exemple suivant :

   ``` JSON
   [
     {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
     {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
   ]
   ```

## <a name="span-ideventhubsazure-event-hubsspan"></a><span id="eventhubs">Azure Event Hubs</span>

* **Limites** : Tenez compte des limites suivantes de l’intégration.

   * L’intégration de Metrics Advisor à Event Hubs ne prend pas en charge actuellement plus de trois flux de données actifs dans une instance Metrics Advisor en préversion publique.
   * Metrics Advisor commencera toujours à consommer les messages du dernier décalage, y compris lors de la réactivation d’un flux de données interrompu.
   
      * Les messages générés pendant la période de pause du flux de données seront perdus.
      * L’heure de début de l’ingestion du flux de données est automatiquement définie sur le timestamp UTC actuel, lors de la création du flux de données. Cette heure est uniquement utilisée à titre de référence.

   * Un seul flux de données peut être utilisé par groupe de consommateurs. Pour réutiliser un groupe de consommateurs d’un autre flux de données supprimé, vous devez attendre au moins dix minutes après la suppression.
   * La chaîne de connexion et le groupe de consommateurs ne peuvent pas être modifiés après la création du flux de données.
   * Pour les messages Event Hubs, seul le format JSON est pris en charge, et les valeurs JSON ne peuvent pas être un objet JSON imbriqué. L’élément de niveau supérieur peut être un objet JSON ou un tableau JSON.
    
    Les messages valides sont les suivants :

    ``` JSON
    Single JSON object 
    {
    "metric_1": 234, 
    "metric_2": 344, 
    "dimension_1": "name_1", 
    "dimension_2": "name_2"
    }
    ```
        
    ``` JSON
    JSON array 
    [
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 12.4,
            "location": "outdoor"
        },
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 24.8,
            "location": "indoor"
        }
    ]
    ```


* **Chaîne de connexion** : Accédez à l’instance d’Event Hubs. Ajoutez ensuite une nouvelle stratégie ou choisissez une stratégie d’accès partagé existante. Copiez la chaîne de connexion dans le panneau contextuel.
    ![Capture d’écran d’Event Hubs.](media/datafeeds/entities-eventhubs.jpg)
    
    ![Capture d’écran de stratégies d’accès partagé.](media/datafeeds/shared-access-policies.jpg)

    Voici un exemple de chaîne de connexion : 
    ```
    Endpoint=<Server>;SharedAccessKeyName=<SharedAccessKeyName>;SharedAccessKey=<SharedAccess Key>;EntityPath=<EntityPath>
    ```

* **Groupe de consommateurs** : Un [groupe de consommateurs](../../event-hubs/event-hubs-features.md#consumer-groups) est une vue (état, position ou décalage) d’un Event Hub dans sa totalité.
Vous le trouvez dans le menu **Groupes de consommateurs** d’une instance d’Azure Event Hubs. Un groupe de consommateurs peut uniquement servir un flux de données. Créez un nouveau groupe de consommateurs pour chaque flux de données.
* **Timestamp** (facultatif) : Metrics Advisor utilise le timestamp d’Event Hubs comme timestamp de l’événement, si la source de données utilisateur ne contient pas de champ de timestamp. Le champ de timestamp est facultatif. Si aucune colonne de timestamp n’est sélectionnée, le service utilise l’heure de mise en file d’attente comme timestamp.

    Le champ de timestamp doit correspondre à l’un de ces deux formats :
    
    * `YYYY-MM-DDTHH:MM:SSZ`
    * Nombre de secondes ou de millisecondes à partir de l’époque `1970-01-01T00:00:00Z`.
    
    Le timestamp est aligné à gauche sur la granularité. Par exemple, si le timestamp est `2019-01-01T00:03:00Z`, la granularité est de 5 minutes, puis Metrics Advisor aligne le timestamp sur `2019-01-01T00:00:00Z`. Si le timestamp de l’événement est `2019-01-01T00:10:00Z`, Metrics Advisor utilise le timestamp directement, sans aucun alignement. 


## <a name="span-idlogazure-monitor-logsspan"></a><span id="log">Journaux Azure Monitor</span>

Les journaux d’activité Azure Monitor proposent les types d’authentification suivants : de base, principal de service et principal de service du coffre de clés.
* **De base** : Vous devez renseigner les champs **ID de locataire**, **ID client**, **Clé secrète client** et **ID d’espace de travail**.
   Pour obtenir l’**ID de locataire**, l’**ID client** et la **clé secrète client**, consultez [Inscrire une application ou une API web](../../active-directory/develop/quickstart-register-app.md). Vous trouverez l’**ID d’espace de travail** sur le portail Azure.
   
    ![Capture d’écran montrant où trouver l’ID d’espace de travail dans le portail Azure.](media/workspace-id.png)
    
* **Principal de service** : Un principal de service est une instance concrète créée à partir de l’objet d’application, et il hérite de certaines propriétés de cet objet d’application. Un principal de service est créé dans chaque locataire dans lequel l’application est utilisée, et il fait référence à l’objet application global unique. L’objet principal de service définit ce que l’application peut réellement faire dans le locataire spécifique, qui peut accéder à l’application, ainsi que les ressources auxquelles l’application peut accéder.
    
    **Étape 1 :** Créez et inscrivez une application Azure AD, puis autorisez-la à accéder à une base de données. Pour plus d’informations, consultez [Créer une inscription d’application Azure AD](/azure/data-explorer/provision-azure-ad-app).

    **Étape 2 :** Attribuez des rôles.
    1. Dans le portail Azure, accédez au service **Comptes de stockage**.
    2. Sélectionnez **Contrôle d’accès (IAM)** .
    3. Sélectionnez **+ Ajouter**, puis sélectionnez **Ajouter une attribution de rôle** à partir du menu.
    4. Définissez le champ **Sélectionner** sur le nom de l’application Azure AD, puis définissez le rôle sur **Contributeur aux données Blob du stockage**. Ensuite, sélectionnez **Enregistrer**.
    
        ![Capture d’écran montrant comment attribuer des rôles.](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

    
    **Étape 3 :** [Créez une entité d’informations d’identification](how-tos/credential-entity.md) dans Metrics Advisor, afin de pouvoir choisir cette entité lorsque vous ajoutez un flux de données pour le type d’authentification du principal de service. 
        
* **Principal de service du coffre de clés** : Key Vault permet de protéger les clés de chiffrement et les valeurs secrètes utilisées par les applications et services cloud. Key Vault vous permet de chiffrer les clés et les valeurs secrètes. Créez d’abord un principal de service, puis stockez-le dans un coffre de clés. Pour plus d’informations, consultez [Créer une entité d’informations d’identification pour le principal de service du coffre de clés](how-tos/credential-entity.md#sp-from-kv). 

* **Requête** : Spécifiez la requête. Pour plus d’informations, consultez [Requêtes de journal dans Azure Monitor](../../azure-monitor/logs/log-query-overview.md).

    Exemple de requête :

    ``` Kusto
    [TableName]
    | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd)
    | summarize [count_per_dimension]=count() by [Dimension]
    ```

    Pour plus d’informations, référez-vous au [tutoriel sur l’écriture d’une requête valide](tutorials/write-a-valid-query.md).

## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **Chaîne de connexion** : Les types d’authentification pour Azure SQL Database et SQL Server sont les types de base, identité managée, chaîne de connexion Azure SQL, principal de service et principal de service du coffre de clés.
    
    * **De base** : Metrics Advisor accepte une [chaîne de connexion de style ADO.NET](/dotnet/framework/data/adonet/connection-string-syntax) pour une source de données SQL Server.
    Voici un exemple de chaîne de connexion : 
    
        ```
        Data Source=<Server>;Initial Catalog=<db-name>;User ID=<user-name>;Password=<password>
        ```
    
    * <span id='jump'>**Identité managée**</span> : L’identité managée pour les ressources Azure peut autoriser l’accès aux données de blob et de file d’attente. Pour ce faire, elle utilise les informations d’identification Azure AD des applications qui s’exécutent dans les machines virtuelles Azure, les applications de fonction, les groupes de machines virtuelles identiques et d’autres services. En utilisant une identité managée pour ressources Azure et l’authentification Azure AD, vous pouvez éviter de stocker des informations d’identification avec les applications qui s’exécutent dans le cloud. Pour [activer votre entité managée](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md), procédez comme suit :
    1. L’activation d’une identité managée affectée par le système s’effectue en un seul clic. Dans le portail Azure, pour votre espace de travail Metrics Advisor, allez dans **Paramètres** > **Identité** > **Système attribué**. Définissez ensuite l’état comme **activé**. 
    
        ![Capture d’écran montrant comment définir l’état sur activé.](media/datafeeds/set-identity-status.png)

    1. Activez l’authentification Azure AD. Dans le portail Azure, pour votre source de données, allez dans **Paramètres** > **Administrateur Active Directory**. Sélectionnez **Définir l’administrateur**, puis sélectionnez un **compte d’utilisateur Azure AD** à transformer en administrateur du serveur. Ensuite, choisissez **Sélectionner**.
    
        ![Capture d’écran montrant comment définir l’administrateur.](media/datafeeds/set-admin.png)

    1. Activez l’identité managée dans Metrics Advisor. Vous pouvez modifier une requête dans l’outil de gestion de la base de données ou dans le portail Azure.
    
        **Outil de gestion** : Dans votre outil de gestion de la base de données, sélectionnez **Active Directory – Authentification universelle avec MFA** dans le champ d’authentification. Dans le champ **Nom d’utilisateur**, entrez le nom du compte Azure AD que vous avez défini en tant qu’administrateur du serveur à l’étape 2. Par exemple, ce peut être `test@contoso.com`.
    
        ![Capture d’écran montrant comment définir les détails de connexion.](media/datafeeds/connection-details.png)
        
        **Portail Azure** : Dans votre base de données SQL, sélectionnez **Éditeur de requête** et connectez-vous au compte administrateur.
        ![Capture d’écran montrant comment modifier votre requête dans le portail Azure.](media/datafeeds/query-editor.png)

        Ensuite, dans la fenêtre de requête, exécutez la commande suivante (notez qu’il s’agit de la même que pour la méthode utilisant l’outil de gestion) :
    
        ```
        CREATE USER [MI Name] FROM EXTERNAL PROVIDER
        ALTER ROLE db_datareader ADD MEMBER [MI Name]
        ```
    
        > [!NOTE]
        > Le `MI Name` est le nom de l’identité managée dans Metrics Advisor (pour le principal de service, il doit être remplacé par le nom de principal du service). Pour plus d’informations, consultez [Autoriser avec une identité managée](../../storage/blobs/authorize-managed-identity.md#enable-managed-identities-on-a-vm). 
            
        Voici un exemple de chaîne de connexion : 
       
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
        
    * **Chaîne de connexion Azure SQL** : 
      

        Voici un exemple de chaîne de connexion : 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>;User ID=<user-name>;Password=<password>
        ```
  

    * **Principal de service** : Un principal de service est une instance concrète créée à partir de l’objet d’application, et il hérite de certaines propriétés de cet objet d’application. Un principal de service est créé dans chaque locataire dans lequel l’application est utilisée, et il fait référence à l’objet application global unique. L’objet principal de service définit ce que l’application peut réellement faire dans le locataire spécifique, qui peut accéder à l’application, ainsi que les ressources auxquelles l’application peut accéder.
    
        **Étape 1 :** Créez et inscrivez une application Azure AD, puis autorisez-la à accéder à une base de données. Pour plus d’informations, consultez [Créer une inscription d’application Azure AD](/azure/data-explorer/provision-azure-ad-app).

        **Étape 2** : Suivez les étapes décrites précédemment, dans la section [Identité managée dans SQL Server](#jump). 

        **Étape 3 :** [Créez une entité d’informations d’identification](how-tos/credential-entity.md) dans Metrics Advisor, afin de pouvoir choisir cette entité lorsque vous ajoutez un flux de données pour le type d’authentification du principal de service. 

        Voici un exemple de chaîne de connexion : 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
  
    * **Principal de service du coffre de clés** : Key Vault permet de protéger les clés de chiffrement et les valeurs secrètes utilisées par les applications et services cloud. Key Vault vous permet de chiffrer les clés et les valeurs secrètes. Créez d’abord un principal de service, puis stockez-le dans un coffre de clés. Pour plus d’informations, consultez [Créer une entité d’informations d’identification pour le principal de service du coffre de clés](how-tos/credential-entity.md#sp-from-kv). Vous pouvez également trouver votre chaîne de connexion dans votre ressource Azure SQL Server, dans **Paramètres** > **Chaînes de connexion**.
        
        Voici un exemple de chaîne de connexion : 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```

* **Requête** : Utilisez une requête SQL pour obtenir et formuler des données dans des données de séries chronologiques multidimensionnelles. Vous pouvez utiliser `@IntervalStart` et `@IntervalEnd` dans votre requête pour vous aider à obtenir une valeur de métrique attendue dans un intervalle. Elles doivent utiliser le format suivant : `yyyy-MM-ddTHH:mm:ssZ`.


    Exemple de requête :
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```
    
## <a name="span-idtableazure-table-storagespan"></a><span id="table">Stockage de tables Azure</span>

* **Chaîne de connexion** : Créez une URL de signature d’accès partagé (SAP) et renseignez-la ici. Le moyen le plus simple de générer une URL SAP consiste à utiliser le portail Azure. Tout d’abord, sous **Paramètres**, allez sur le compte de stockage auquel vous voulez accéder. Sélectionnez ensuite **Signature d’accès partagé**. Cochez les cases **Table** et **Objet**, puis sélectionnez **Générer la signature d’accès partagé et la chaîne de connexion**. Dans l’espace de travail Metrics Advisor, copiez et collez l’**URL SAP du service de table** dans la zone de texte.

    ![Capture d’écran montrant comment générer la signature d’accès partagé dans Stockage Table Azure.](media/azure-table-generate-sas.png)

* **Nom de la table** : Spécifiez une table à interroger. Vous pouvez le trouver dans votre instance de compte de stockage Azure. Dans la section **Service de table**, sélectionnez **Tables**.

* **Requête** : Vous pouvez utiliser `@IntervalStart` et `@IntervalEnd` dans votre requête pour vous aider à obtenir une valeur de métrique attendue dans un intervalle. Elles doivent utiliser le format suivant : `yyyy-MM-ddTHH:mm:ssZ`.

    Exemple de requête :
    
    ``` mssql
    PartitionKey ge '@IntervalStart' and PartitionKey lt '@IntervalEnd'
    ```

    Pour plus d’informations, consultez le [tutoriel sur l’écriture d’une requête valide](tutorials/write-a-valid-query.md).


## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Chaîne de connexion** : La chaîne de connexion pour accéder à InfluxDB.
* **Base de données** : La base de données à interroger.
* **Requête** : Une requête SQL permettant d’obtenir et de formuler des données dans des données de séries chronologiques multidimensionnelles pour ingestion.

    Exemple de requête :

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    
Pour plus d’informations, référez-vous au [tutoriel sur l’écriture d’une requête valide](tutorials/write-a-valid-query.md).

* **Nom d’utilisateur** : Facultatif pour l’authentification. 
* **Mot de passe** : Facultatif pour l’authentification. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Chaîne de connexion** : La chaîne de connexion pour accéder à MongoDB.
* **Base de données** : La base de données à interroger.
* **Requête** : Une commande permettant d’obtenir et de formuler des données dans des données de séries chronologiques multidimensionnelles pour ingestion. Vérifiez la commande sur [db.runCommand()](https://docs.mongodb.com/manual/reference/method/db.runCommand/index.html).

    Exemple de requête :

    ``` MongoDB
    {"find": "[TableName]","filter": { [Timestamp]: { $gte: ISODate(@IntervalStart) , $lt: ISODate(@IntervalEnd) }},"singleBatch": true}
    ```
    

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Chaîne de connexion** : La chaîne de connexion pour accéder à une base de données MySQL.
* **Requête** : Une requête SQL permettant d’obtenir et de formuler des données dans des données de séries chronologiques multidimensionnelles pour ingestion.

    Exemple de requête :

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn]< @IntervalEnd
    ```

    Pour plus d’informations, référez-vous au [tutoriel sur l’écriture d’une requête valide](tutorials/write-a-valid-query.md).

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Chaîne de connexion** : La chaîne de connexion pour accéder à une base de données PostgreSQL.
* **Requête** : Une requête SQL permettant d’obtenir et de formuler des données dans des données de séries chronologiques multidimensionnelles pour ingestion.

    Exemple de requête :

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    Pour plus d’informations, référez-vous au [tutoriel sur l’écriture d’une requête valide](tutorials/write-a-valid-query.md).
    
## <a name="span-idcsvlocal-files-csvspan"></a><span id="csv">Fichiers locaux (CSV)</span>

> [!NOTE]
> Cette fonctionnalité est utilisée uniquement pour une évaluation rapide du système axée sur la détection d’anomalie. Elle n’accepte que les données statiques provenant d’un CSV local et effectue la détection d’anomalie sur des données de séries chronologiques uniques. Pour analyser des mesures multidimensionnelles, notamment l’ingestion des données en temps réel, la notification d’anomalies, l’analyse des causes racine et l’analyse des incidents intermétriques, utilisez d’autres sources de données prises en charge.

**Exigences relatives aux données de fichier CSV :**
- Le fichier doit avoir au moins une colonne, qui représente les mesures à analyser. Pour une expérience utilisateur plus rapide et de meilleure qualité, essayez un fichier CSV qui contient deux colonnes : une colonne timestamp et une colonne métrique. Le format du timestamp doit être le suivant : `2021-03-30T00:00:00Z`, et il est préférable que la partie `seconds` soit `:00Z`. La granularité temporelle entre chaque enregistrement doit être la même.
- La colonne timestamp est facultative. S’il n’y a pas de timestamp, Metrics Advisor utilisera le timestamp à partir d’aujourd’hui (`00:00:00` UTC). Le service met en correspondance chaque mesure de la ligne à un intervalle d’une heure.
- Il n’y a pas de réordonnancement ni de comblement des écarts pendant l’ingestion des données. Assurez-vous que vos données dans le fichier CSV sont ordonnées dans l’ordre **ascendant (ASC)** des timestamps.
 
## <a name="next-steps"></a>Étapes suivantes

* En attendant que vos données de mesures soient ingérées dans le système, découvrez [comment gérer des configurations de flux de données](how-tos/manage-data-feeds.md).
* Lorsque vos données de mesures sont ingérées, vous pouvez [configurer les métriques et ajuster la configuration de détection](how-tos/configure-metrics.md).