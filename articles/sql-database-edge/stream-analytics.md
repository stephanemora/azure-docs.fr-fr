---
title: Utilisation de packages DAC SQL Database et de tâches Stream Analytics avec Azure SQL Database Edge | Microsoft Docs
description: En savoir plus sur l’utilisation des tâches Stream Analytics dans SQL Database Edge
keywords: sql database edge, stream analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384160"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Utilisation de packages DAC SQL Database et de tâches Stream Analytics avec Azure SQL Database Edge

Azure SQL Database Edge en préversion est un moteur de base de données relationnelle optimisé conçu pour les déploiements IoT et Edge. Il repose sur les dernières versions du moteur de base de données Microsoft SQL Server, qui fournit des fonctionnalités de performances, de sécurité et de traitement des requêtes de pointe. Avec les fonctionnalités de gestion de base de données relationnelle de SQL Server les plus performantes du marché, Azure SQL Database Edge offre des fonctionnalités de diffusion en continu intégrées pour l’analyse en temps réel et le traitement complexe des événements.

Azure SQL Database Edge fournit également une implémentation native de SqlPackage.exe qui vous permet de déployer un package [DAC SQL Database](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) pendant le déploiement de SQL Database Edge.

Azure SQL Database Edge expose deux paramètres facultatifs via l’option `module twin's desired properties` du module IoT Edge :

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Champ | Description |
|------|-------------|
| SqlPackage | URI de Stockage Blob Azure pour le fichier *.zip qui contient le package DAC SQL Database.
| ASAJobInfo | URI de Stockage Blob Azure pour la tâche ASA Edge. Pour plus d’informations, consultez [Publication d’une tâche ASA Edge pour SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Utilisation de packages DAC SQL Database avec SQL Database Edge

Pour utiliser un package DAC SQL Database (*.dacpac) avec SQL Database Edge, effectuez les étapes suivantes :

1. Créez ou extrayez un package DAC SQL Database. Pour savoir comment générer un package DAC pour une base de données SQL Server existante, consultez [Extraire une package DAC d’une base de données](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/).

2. Compressez le fichier *.dacpac et chargez-le dans un compte Stockage Blob Azure. Pour plus d’informations sur le chargement de fichiers dans Stockage Blob Azure, consultez [Charger, télécharger et lister les objets blob avec le portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Générez une signature d’accès partagé pour le fichier zip à partir du portail Azure. Pour plus d’informations, consultez [Déléguer l’accès avec des signature d’accès partagé (SAP)](../storage/common/storage-sas-overview.md).

4. Mettez à jour la configuration du module SQL Database Edge pour inclure l’URI d’accès partagé du package DAC. Pour mettre à jour le module SQL Database Edge, effectuez les étapes suivantes :

    1. Sur le portail Azure, accédez à votre déploiement IoT Hub.

    2. Dans le volet gauche, sélectionnez **IoT Edge**.

    3. Dans la page **IoT Edge**, recherchez et sélectionnez l’instance IoT Edge dans laquelle le module SQL Database Edge est déployé.

    4. Dans la page **Appareil IoT Edge**, sélectionnez **Définir des modules**.

    5. Dans la page **Définir des modules**, sélectionnez **Configurer** pour le module SQL Database Edge.

    6. Dans le volet **Modules personnalisés IoT Edge**, sélectionnez **Set module twin's desired properties** (Définir les propriétés souhaitées du jumeau de module). Mettez à jour les propriétés souhaitées pour inclure l’URI de l’option `SQLPackage`, comme dans l’exemple suivant.

        > [!NOTE]
        > L’URI SAS figurant dans le code JSON ci-dessous n’est qu’un exemple. Remplacez l’URI par l’URI réel de votre déploiement.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Sélectionnez **Enregistrer**.

    8. Dans la page **Définir des modules**, sélectionnez **Suivant**.

    9. Dans la page **Définir des modules**, sélectionnez **Suivant**, puis **Envoyer**.

5. Après la mise à jour du module, le fichier de package DAC est téléchargé, décompressé et déployé pour l’instance SQL Database Edge.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Utilisation de tâches de diffusion en continu avec SQL Database Edge

Azure SQL Database Edge possède une implémentation native du runtime Stream Analytics. Cette implémentation vous permet de créer une tâche Azure Stream Analytics Edge et de la déployer en tant que tâche de streaming SQL Database Edge. Pour créer une tâche Stream Analytics Edge, effectuez les étapes suivantes :

1. Accédez au portail Azure en utilisant l’[URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true) d’aperçu. Cette URL d’aperçu permet aux utilisateurs de configurer la sortie SQL Database pour une tâche Stream Analytics Edge.

2. Créez une tâche **Azure Stream Analytics sur IoT Edge**. Choisissez l’environnement d’hébergement qui cible **Edge**.

3. Définissez l’entrée et la sortie de la tâche Azure Stream Analytics. Chaque sortie SQL, que vous allez configurer ici, est liée à une seule table dans la base de données. Si vous avez besoin de transmettre en continu des données vers plusieurs tables, vous devez créer plusieurs sorties SQL Database. Vous pouvez configurer les sorties SQL pour qu’elles pointent vers différentes bases de données.

    **Entrée**. Choisissez EdgeHub comme entrée de la tâche Edge et fournissez des informations sur la ressource.

    **Sortie**. Sélectionnez SQL Database comme sortie. Sélectionnez **Fournir les paramètres de base de données SQL manuellement**. Fournissez les détails de configuration pour la base de données et la table.

    |Champ      | Description |
    |---------------|-------------|
    |Alias de sortie | Nom de l’alias de sortie.|
    |Base de données | Nom de la base de données SQL. Il doit s’agir d’un nom valide d’une base de données qui existe dans l’instance SQL Database Edge.|
    |Nom du serveur | Nom (ou adresse IP) et détails du numéro de port de l’instance SQL. Pour un déploiement SQL Database Edge, vous pouvez utiliser **tcp:.,1433** comme nom de serveur.|
    |Nom d’utilisateur | Compte de connexion SQL disposant d’un accès en lecture et en écriture aux données de la base de données que vous avez spécifiée précédemment.|
    |Mot de passe | Mot de passe du compte de connexion SQL que vous avez spécifié précédemment.|
    |Table | Nom de la table qui sera générée pour la tâche de streaming.|
    |Héritage du partitionnement| Permet d’hériter du schéma de partitionnement de l’entrée ou l’étape de requête précédente. Quand cette option est activée, vous pouvez tabler sur un meilleur débit d’écriture dans une table sur disque et bénéficier d’une topologie entièrement parallèle pour votre tâche.|
    |Taille du lot| Nombre maximal d’enregistrements envoyés à chaque opération d’insertion en bloc.|

    Voici un exemple de configuration d’entrée/sortie :

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Pour plus d’informations sur l’adaptateur de sortie SQL pour Azure Stream Analytics, consultez [Sortie d’Azure Stream Analytics dans Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Définissez la requête de tâche ASA pour la tâche Edge. Cette requête doit utiliser les alias d’entrée/sortie définis comme noms d’entrée et de sortie. Pour plus d’informations, consultez [Informations de référence sur le langage de requête Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Définissez les paramètres de compte de stockage pour la tâche Edge. Le compte de stockage est utilisé comme cible de publication pour la tâche Edge.

6. Sous **Configurer**, sélectionnez **Publier**, puis cliquez sur le bouton **Publier**. Enregistrez l’URI SAS à utiliser avec le module SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Déployer la tâche Stream Analytics Edge dans SQL Database Edge

Pour déployer la tâche de streaming dans le module SQL Database Edge, mettez à jour la configuration de celui-ci afin d’inclure l’URI SAP pour la tâche de streaming de l’étape précédente. Pour mettre à jour le module SQL Database Edge :

1. Sur le portail Azure, accédez à votre déploiement IoT Hub.

2. Dans le volet gauche, sélectionnez **IoT Edge**.

3. Dans la page **IoT Edge**, recherchez et sélectionnez l’instance IoT Edge dans laquelle le module SQL Database Edge est déployé.

4. Dans la page **Appareil IoT Edge**, sélectionnez **Définir des modules**.

5. Dans la page **Définir des modules**, sélectionnez **Configurer** pour le module SQL Database Edge.

6. Dans le volet **Modules personnalisés IoT Edge**, sélectionnez **Set module twin's desired properties** (Définir les propriétés souhaitées du jumeau de module). Mettez à jour les propriétés souhaitées de façon à inclure l’URI pour l’option `ASAJobInfo`, comme dans l’exemple suivant.

    > [!NOTE]
    > L’URI SAS figurant dans le code JSON ci-dessous n’est qu’un exemple. Remplacez l’URI par l’URI réel de votre déploiement.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Sélectionnez **Enregistrer**.

8. Dans la page **Définir des modules**, sélectionnez **Suivant**.

9. Dans la page **Définir des modules**, sélectionnez **Suivant**, puis **Envoyer**.

10. Après la mise à jour du module, le fichier de tâche Stream Analytics est téléchargé, décompressé et déployé pour l’instance SQL Database Edge.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les tarifs et la disponibilité, consultez [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Demandez à activer Azure SQL Database Edge pour votre abonnement.
- Pour bien démarrer, consultez [Déployer SQL Database Edge via le portail Azure](deploy-portal.md).
