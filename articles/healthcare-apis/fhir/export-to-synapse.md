---
title: Exportation de données dans l’API Azure pour FHIR vers Azure Synapse Analytics
description: Cet article décrit le déplacement de données FHIR dans Synapse
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/13/2021
ms.author: ginle
ms.openlocfilehash: 1fc264109b0cc2d6534f0c8b213dfc250c700d15
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116779"
---
# <a name="moving-data-from-azure-api-for-fhir-to-azure-synapse-analytics"></a>Déplacement de données à partir de l’API Azure pour FHIR vers Azure Synapse Analytics

Dans cet article, vous allez découvrir quelques méthodes pour déplacer des données à partir de l’API Azure pour FHIR vers [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/), qui est un service d’analyse illimité qui regroupe l’intégration des données, l’entreposage des données d’entreprise et l’analytique des Big Data. 

Le déplacement des données du serveur FHIR vers la synapse implique l’exportation des données à l’aide de l' `$export` opération FHIR suivie d’une série d’étapes pour transformer et charger les données vers synapse. Cet article vous guide à travers deux des différentes approches, qui montrent toutes deux comment convertir des ressources FHIR en formats tabulaires tout en les déplaçant dans synapse.

* **Charger les données exportées dans Synapse à l’aide de T-SQL :** Utilisez `$export` l’opération pour déplacer des ressources FHIR dans un **stockage d’objets BLOB Azure Data Lake Gen 2 (ADL Gen 2)** au `NDJSON` format. Chargez les données à partir du stockage dans des **Pools SQL sans serveur ou dédiés** dans Synapse à l’aide de T-SQL. Convertissez ces étapes en pipeline de déplacement de données robuste à l’aide des [pipelines Synapse](../../synapse-analytics/get-started-pipelines.md).
* **Utilisez les outils du référentiel OSS FHIRs de pipelines d’analyse :** Le [pipeline FHIR Analytics](https://github.com/microsoft/FHIR-Analytics-Pipelines) référentiel contient des outils capables de créer un **pipeline d’Azure Data Factory (ADF)** pour déplacer des données FHIR dans un **dossier CDM (Common Data Model)** et du dossier CDM vers synapse.

## <a name="load-exported-data-to-synapse-using-t-sql"></a>Charger les données exportées dans Synapse à l’aide de T-SQL

### <a name="export-for-moving-fhir-data-into-azure-data-lake-gen-2-storage"></a>`$export` pour déplacer des données FHIR vers le stockage Azure Data Lake génération 2

:::image type="content" source="media/export-data/export-azure-storage-option.png" alt-text="Stockage Azure vers Synapse à l’aide de $export":::

#### <a name="configure-your-fhir-server-to-support-export"></a>Configurer votre serveur FHIR pour prendre en charge `$export`

L’API Azure pour FHIR implémente l' `$export` opération définie par la spécification FHIR pour exporter tout ou un sous-ensemble filtré de données FHIR au `NDJSON` format. En outre, il prend en charge l' [exportation désidentifiée](./de-identified-export.md) pour rendre les données FHIR anonymes au cours de l’exportation. Si vous utilisez `$export` , vous bénéficiez de la fonctionnalité de désidentification par défaut, sa capacité est déjà intégrée à `$export` .

Pour exporter des données FHIR vers le stockage d’objets BLOB Azure, vous devez d’abord configurer votre serveur FHIR pour exporter les données vers le compte de stockage. Vous devez (1) activer l’identité managée (2) accédez à Access Control dans le compte de stockage et ajoutez une attribution de rôle, (3) Sélectionnez votre compte de stockage pour `$export` . Vous trouverez plus d’instructions pas à pas [ici](./configure-export-data.md).

Vous pouvez configurer le serveur pour exporter les données vers n’importe quel type de compte de stockage Azure, mais nous vous recommandons d’exporter vers ADL Gen 2 pour un meilleur alignement avec synapse.

#### <a name="using-export-command"></a>Utilisation de la `$export` commande

Après avoir configuré votre serveur FHIR, vous pouvez suivre la [documentation](./export-data.md#using-export-command) pour exporter vos ressources FHIR au niveau du système, du patient ou du groupe. Par exemple, vous pouvez exporter toutes vos données FHIR relatives aux patients dans un `Group` à l’aide de la `$export` commande suivante, dans laquelle vous spécifiez votre nom de stockage d’objets BLOB ADL Gen 2 dans le champ `{{BlobContainer}}` :

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}  
```

Vous pouvez également utiliser `_type` le paramètre dans l' `$export` appel ci-dessus pour limiter les ressources que vous souhaitez exporter. Par exemple, l’appel suivant exporte uniquement `Patient` les `MedicationRequest` ressources, et `Observation` :

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}&
_type=Patient,MedicationRequest,Condition
```  

Pour plus d’informations sur les différents paramètres pris en charge, consultez `$export` la section de la page relative aux [paramètres](./export-data.md#settings-and-parameters)de la requête.

### <a name="create-a-synapse-workspace"></a>Créer un espace de travail Synapse

Avant d’utiliser Synapse, vous aurez besoin d’un espace de travail synapse. Vous allez créer un service Azure Synapse Analytics sur Portail Azure. Vous trouverez plus d’informations sur [ce](../../synapse-analytics/get-started-create-workspace.md)Guide pas à pas. Vous avez besoin d’un `ADLSGEN2` compte pour créer un espace de travail. Votre espace de travail Azure Synapse utilise ce compte de stockage pour stocker vos données de l’espace de travail synapse.

Après avoir créé un espace de travail, vous pouvez afficher votre espace de travail sur Synapse Studio en vous connectant à votre espace de travail https://web.azuresynapse.net ou en lançant Synapse Studio dans le portail Azure.

#### <a name="creating-a-linked-service-between-azure-storage-and-synapse"></a>Création d’un service lié entre stockage Azure et Synapse

Pour déplacer vos données vers Synapse, vous devez créer un service lié qui connecte votre compte de stockage Azure à synapse. Vous trouverez plus d’instructions pas à pas [ici](../../synapse-analytics/data-integration/data-integration-sql-pool.md#create-linked-services).

1. Sur Synapse Studio, accédez à l’onglet **gérer** , puis sous **connexions externes**, sélectionnez **services liés**.
2. Sélectionnez **nouveau** pour ajouter un nouveau service lié.
3. Sélectionnez **Azure Data Lake Storage Gen2** dans la liste et sélectionnez **Continuer**.
4. Entrez vos informations d’identification d’authentification. Lorsque vous avez terminé, sélectionnez **Créer**.

Maintenant que vous disposez d’un service lié entre votre stockage ADL Gen 2 et Synapse, vous êtes prêt à utiliser des pools SQL Synapse pour charger et analyser vos données FHIR.

### <a name="decide-between-serverless-and-dedicated-sql-pool"></a>Choisir entre un pool sans serveur et un pool SQL dédié

Azure Synapse Analytics propose deux pools SQL différents, un pool SQL sans serveur et un pool SQL dédié. Le pool SQL sans serveur offre la flexibilité de l’interrogation des données directement dans le stockage d’objets BLOB à l’aide du point de terminaison SQL sans serveur sans approvisionnement des ressources. Le pool SQL dédié offre la puissance de traitement pour des performances élevées et l’accès concurrentiel, et est recommandé pour les capacités d’entreposage de données à l’échelle de l’entreprise. Pour plus d’informations sur les deux pools SQL, consultez la [page de documentation de Synapse](../../synapse-analytics/sql/overview-architecture.md) sur l’architecture SQL.

#### <a name="using-serverless-sql-pool"></a>Utilisation d’un pool SQL sans serveur

Comme il ne s’agit pas de serveurs, il n’y a pas d’infrastructure à configurer ou de clusters à gérer. Vous pouvez commencer à interroger des données à partir de Synapse Studio dès que l’espace de travail est créé.

Par exemple, la requête suivante peut être utilisée pour transformer les champs sélectionnés `Patient.ndjson` en une structure tabulaire :

```sql
SELECT * FROM  
OPENROWSET(bulk 'https://{{youraccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson', 
FORMAT = 'csv', 
FIELDTERMINATOR ='0x0b', 
FIELDQUOTE = '0x0b')  
WITH (doc NVARCHAR(MAX)) AS rows     
CROSS APPLY OPENJSON(doc)     
WITH ( 
    ResourceId VARCHAR(64) '$.id', 
    Active VARCHAR(10) '$.active', 
    FullName VARCHAR(100) '$.name[0].text', 
    Gender VARCHAR(20) '$.gender', 
       ...
) 
```

Dans la requête ci-dessus, la `OPENROWSET` fonction accède aux fichiers dans le stockage Azure et `OPENJSON` analyse le texte JSON et retourne les propriétés d’entrée JSON sous forme de lignes et de colonnes. Chaque fois que cette requête est exécutée, le pool SQL sans serveur lit le fichier à partir du stockage d’objets BLOB, analyse le JSON et extrait les champs.

Vous pouvez également matérialiser les résultats au format parquet dans une [table externe](../../synapse-analytics/sql/develop-tables-external-tables.md) pour obtenir de meilleures performances de requête, comme indiqué ci-dessous :

```sql
-- Create External data source where the parquet file will be written 
CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH ( 
    LOCATION = 'https://{{youraccount}}.blob.core.windows.net/{{exttblcontainer}}' 
); 
GO 

-- Create External File Format 
CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH ( 
    FORMAT_TYPE = PARQUET, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec' 
); 
GO 

CREATE EXTERNAL TABLE [dbo].[Patient] WITH ( 
        LOCATION = 'PatientParquet/', 
        DATA_SOURCE = [MyDataSource], 
        FILE_FORMAT = [ParquetFF] 
) AS 
SELECT * FROM  
OPENROWSET(bulk 'https://{{youraccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson' 
-- Use rest of the SQL statement from the previous example --
```

#### <a name="using-dedicated-sql-pool"></a>Utilisation d’un pool SQL dédié

Le pool SQL dédié prend en charge les tables gérées et un cache hiérarchique pour les performances en mémoire. Vous pouvez importer des Big Data avec des requêtes T-SQL simples, puis utiliser la puissance du moteur de requête distribuée pour exécuter des analyses hautes performances.

Le moyen le plus simple et le plus rapide de charger des données à partir de votre stockage dans un pool SQL dédié consiste à utiliser la **`COPY`** commande dans T-SQL, qui peut lire des fichiers CSV, parquet et orc. Comme dans l’exemple de requête ci-dessous, utilisez la `COPY` commande pour charger les `NDJSON` lignes dans une structure tabulaire. 

```sql
-- Create table with HEAP, which is not indexed and does not have a column width limitation of NVARCHAR(4000) 
CREATE TABLE StagingPatient ( 
Resource NVARCHAR(MAX) 
) WITH (HEAP) 
COPY INTO StagingPatient 
FROM 'https://{{yourblobaccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson' 
WITH ( 
FILE_TYPE = 'CSV', 
ROWTERMINATOR='0x0a', 
FIELDQUOTE = '', 
FIELDTERMINATOR = '0x00' 
) 
GO
```

Une fois que vous avez les lignes JSON dans la `StagingPatient` table ci-dessus, vous pouvez créer différents formats tabulaires des données à l’aide de la `OPENJSON` fonction et en stockant les résultats dans des tables. Voici un exemple de requête SQL pour créer une `Patient` table en extrayant quelques champs de la `Patient` ressource :

```sql
SELECT RES.* 
INTO Patient 
FROM StagingPatient
CROSS APPLY OPENJSON(Resource)   
WITH (
    ResourceId VARCHAR(64) '$.id',
    FullName VARCHAR(100) '$.name[0].text',
    FamilyName VARCHAR(50) '$.name[0].family',
    GivenName VARCHAR(50) '$.name[0].given[0]',
    Gender VARCHAR(20) '$.gender',
    DOB DATETIME2 '$.birthDate',
    MaritalStatus VARCHAR(20) '$.maritalStatus.coding[0].display',
    LanguageOfCommunication VARCHAR(20) '$.communication[0].language.text'
) AS RES 
GO

```

## <a name="use-fhir-analytics-pipelines-oss-tools"></a>Utiliser les outils OSS des pipelines d’analyse FHIR

:::image type="content" source="media/export-data/analytics-pipeline-option.png" alt-text="Utilisation du pipeline ADF pour déplacer des données dans le dossier CDM, puis sur Synapse":::

> [!Note]
> Le [pipeline FHIR Analytics](https://github.com/microsoft/FHIR-Analytics-Pipelines) est un outil open source publié sous licence MIT et n’est pas couvert par le contrat SLA Microsoft pour les services Azure.

### <a name="adf-pipeline-for-moving-fhir-data-into-cdm-folder"></a>Pipeline ADF pour le déplacement des données FHIR vers le dossier CDM

Le dossier CDM (Common Data Model) est un dossier dans un Data Lake qui est conforme à des structures de métadonnées bien définies et standardisées et à des données auto-descriptives. Ces dossiers facilitent l’interopérabilité des métadonnées entre les producteurs de données et les consommateurs de données. Avant de déplacer des données FHIR dans un dossier CDM, vous pouvez transformer vos données dans une configuration de table.

### <a name="generating-table-configuration"></a>Génération de la configuration de table

Clone te référentiel obtient tous les scripts et le code source. Utilisez `npm install` pour installer les dépendances. Exécutez la commande suivante à partir du `Configuration-Generator` dossier pour générer un dossier de configuration de table à l’aide des instructions de format YAML :

```bash
Configuration-Generator> node .\generate_from_yaml.js -r {resource configuration file} -p {properties group file} -o {output folder}
```

Vous pouvez utiliser les exemples de `YAML` fichiers `resourcesConfig.yml` et `propertiesGroupConfig.yml` fournis dans le référentiel.

### <a name="generating-adf-pipeline"></a>Génération du pipeline ADF

Vous pouvez désormais utiliser le contenu de la configuration de table générée et quelques autres configurations pour générer un pipeline ADF. Lorsqu’il est déclenché, ce pipeline ADF exporte les données du serveur FHIR à l’aide de l' `$export` API et écrit dans un dossier CDM avec les métadonnées CDM associées.

1. Créer une application Azure Active Directory (AD) et un principal du service. Le pipeline ADF utilise un service Azure batch pour effectuer la transformation et nécessite une application Azure AD pour le service batch. Suivez [Azure ad documentation](../../active-directory/develop/howto-create-service-principal-portal.md).
2. Accordez l’accès à l’emplacement de stockage d’exportation au principal du service. Dans le `Access Control` du stockage d’exportation, attribuez `Storage Blob Data Contributor` le rôle à l’application Azure ad.
3. Déployez le pipeline de sortie. Utilisez le modèle `fhirServiceToCdm.json` pour un déploiement personnalisé sur Azure. Cette étape permet de créer les ressources Azure suivantes :
    - Un pipeline ADF portant le nom `{pipelinename}-df` .
    - Un coffre de clés avec le nom `{pipelinename}-kv` pour stocker la clé secrète client.
    - Un compte batch portant le nom `{pipelinename}batch` pour exécuter la transformation.
    - Un compte de stockage portant le nom `{pipelinename}storage` .
4. Accordez l’accès à la Azure Data Factory. Dans le panneau de contrôle d’accès de votre service FHIR, accordez `FHIR data exporter` `FHIR data reader` les rôles et à la fabrique de données, `{pipelinename}-df` .
5. Téléchargez le contenu du dossier de configuration de la table dans le conteneur de configuration.
6. Accédez à `{pipelinename}-df` et déclenchez le pipeline. Vous devez voir les données exportées dans le dossier CDM sur le compte de stockage `{pipelinename}storage` . Vous devez voir un dossier pour chaque table contenant un fichier CSV.

### <a name="from-cdm-folder-to-synapse"></a>Du dossier CDM à Synapse

Une fois que les données sont exportées au format CDM et stockées dans votre stockage ADL Gen 2, vous pouvez désormais déplacer vos données dans le dossier CDM vers synapse.

Vous pouvez créer un pipeline CDM à Synapse à l’aide d’un fichier de configuration, qui ressemble à ceci :

```json
{
  "ResourceGroup": "",
  "TemplateFilePath": "../Templates/cdmToSynapse.json",
  "TemplateParameters": {
    "DataFactoryName": "",
    "SynapseWorkspace": "",
    "DedicatedSqlPool": "",
    "AdlsAccountForCdm": "",
    "CdmRootLocation": "cdm",
    "StagingContainer": "adfstaging",
    "Entities": ["LocalPatient", "LocalPatientAddress"]
  }
}
```

Exécutez ce script avec le fichier de configuration ci-dessus :

```bash
.\DeployCdmToSynapsePipeline.ps1 -Config: config.json
```

Ajout d’une identité gérée ADF en tant qu’utilisateur SQL dans la base de données SQL. Voici un exemple de script SQL permettant de créer un utilisateur et un rôle Assign :

```sql
CREATE USER [datafactory-name] FROM EXTERNAL PROVIDER
GO
EXEC sp_addrolemember db_owner, [datafactory-name]
GO
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris deux façons de déplacer vos données FHIR vers la synapse : (1) à l’aide `$export` de pour déplacer des données dans le stockage d’objets BLOB ADL Gen 2, puis de charger les données dans des pools SQL Synapse et (2) à l’aide du pipeline ADF pour déplacer des données FHIR vers le dossier CDM, puis à synapse.

Ensuite, vous pouvez en savoir plus sur l’anonymisation de vos données FHIR tout en déplaçant les données vers Synapse pour vous assurer que vos informations de santé sont protégées :
 
>[!div class="nextstepaction"]
>[Exportation de données déidentifiées](./de-identified-export.md)








