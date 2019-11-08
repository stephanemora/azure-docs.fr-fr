---
title: Utilisation du package SQL Database DAC et des tâches Stream Analytics avec Azure SQL Database Edge | Microsoft Docs
description: En savoir plus sur l’utilisation des tâches Stream Analytics dans SQL Database Edge
keywords: sql database edge, stream analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 1b6b3f4e4be9d056bc53fac2eb2f1f3fcd768085
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510946"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>Utilisation du package SQL Database DAC et des tâches Stream Analytics avec Azure SQL Database Edge

Azure SQL Database Edge en préversion est un moteur de base de données relationnelle optimisé conçu pour les déploiements IoT et Edge. Il repose sur les dernières versions du moteur de base de données Microsoft SQL Server, qui fournit des fonctionnalités de performances, de sécurité et de traitement des requêtes de pointe. Avec les fonctionnalités de gestion de base de données relationnelle de SQL Server les plus performantes du marché, Azure SQL Database Edge offre des fonctionnalités de diffusion en continu intégrées pour l’analyse en temps réel et le traitement complexe des événements.

Azure SQL Database Edge fournit également une implémentation native de SQLPackage.exe qui permet aux utilisateurs de déployer un package [DAC SQL Database](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) pendant le déploiement de SQL Database Edge.

Azure SQL Database Edge expose deux paramètres facultatifs via l’option des *propriétés souhaitées du jumeau de module* du module IoT Edge.

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
| SQLPackage | URI Stockage Blob Azure du fichier *.zip contenant le package DAC SQL Database.
| ASAJobInfo | URI Stockage Blob Azure de la tâche ASA Edge. Pour plus d’informations sur la publication de la tâche ASA Edge, consultez [Publication d’une tâche ASA Edge pour SQL Database Edge]().

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Utilisation de packages DAC SQL Database avec SQL Database Edge

Pour utiliser un package DAC SQL Database (*.dacpac) avec SQL Database Edge, suivez les étapes indiquées ci-dessous.

1. Créez ou extrayez un package DAC SQL Database. Pour générer un DacPac d’une base de données SQL Database existante, vous pouvez vous appuyer sur les concepts décrits à la rubrique [Extraire une DAC d’une base de données](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/).

2. Compressez le fichier * *. dacpac* et chargez-le dans un compte Stockage Blob Azure. Pour plus d’informations sur le chargement de fichiers vers Stockage Blob Azure, consultez [Charger, télécharger et répertorier des objets blob à l’aide du portail Microsoft Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Générez une signature SAP pour le fichier zip à l’aide du Portail Azure. Pour plus d’informations, consultez [Déléguer l’accès avec les signature d'accès partagé (SAP)](../storage/common/storage-sas-overview.md).

4. Mettez à jour la configuration du module SQL Database Edge pour inclure l’URI SAS du package DAC. Pour mettre à jour le module SQL Database Edge

    1. Dans le Portail Azure, accédez à votre déploiement IoT Hub.

    2. Dans le volet gauche, cliquez sur **IoT Edge**.

    3. Sur la page **IoT Edge**, recherchez et sélectionnez l’instance IoT Edge dans laquelle le module SQL Database Edge est déployé.

    4. Sur la page *Appareil IoT Edge*, cliquez sur **Définir des modules**. 

    5. Sur la page **Définir des modules**, cliquez sur *configure* pour le module SQL Database Edge. 

    6. Dans le volet **Modules personnalisés IoT Edge**, sélectionnez *Définir les propriétés souhaitées du jumeau de module*, puis mettez à jour les propriétés souhaitées pour inclure l’URI de l’option SQLPackage comme indiqué dans l’exemple ci-dessous. 

        > [!NOTE]
        > L’URI SAP ci-dessous est fournie à titre d’exemple. Remplacez l’URI par l’URI réel de votre déploiement.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Cliquez sur **Enregistrer**.

    8. Sur la page **Définir des modules**, cliquez sur *Suivant*.

    9. Sur la page **Définir des modules**, cliquez sur *Suivant*, puis sur **Envoyer**.

5. Après la mise à jour du module, le fichier dacpac est téléchargé, décompressé et déployé sur l’instance SQL Database Edge.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Utilisation de tâches de diffusion en continu avec SQL Database Edge

Azure SQL Database Edge possède une implémentation native du runtime Stream Analytics. Cela permet aux utilisateurs de créer une tâche Azure Stream Analytics Edge et de la déployer en tant que tâche de diffusion en continu SQL Database Edge. Pour créer une tâche Azure Stream Analytics Edge, procédez comme suit.

1. Accédez au Portail Azure à l’aide de [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true) d’aperçu. Cette URL d’aperçu permet aux utilisateurs de configurer la sortie SQL Database pour une tâche Stream Analytics Edge.

2. Créez une **tâche Azure Stream Analytics sur IoT Edge tâche** et choisissez l’environnement d’hébergement associé à **Edge**.

3. Définissez *input* et *output* pour la tâche Azure Stream Analytics. Chaque sortie SQL (configurée ci-dessous) est liée à une table unique dans la base de données. S’il est nécessaire de diffuser en continu des données vers plusieurs tables, vous devez créer plusieurs sorties SQL Database. Les sorties SQL peuvent être configurées pour pointer vers des bases de données différentes.

    *Entrée : choisissez EdgeHub comme entrée pour la tâche Edge et fournissez les informations sur la ressource.*

    *Sortie : sélectionnez SQL Database en tant que sortie, « Indiquez manuellement les paramètres SQL Database » et fournissez les informations de configuration de la base de données et de la table.*

    |Champ      | Description |
    |---------------|-------------|
    |Alias de sortie | Nom de l’Alias de sortie.|
    |Base de données | Nom du serveur SQL Database. Il doit s’agir d’un nom de base de données valide, qui existe sur l’instance SQL Database Edge.|
    |Nom du serveur | Nom (ou adresse IP) et détails du numéro de port de l’instance SQL. Pour un déploiement SQL Database Edge, vous pouvez utiliser **tcp:.,1433** comme nom de serveur.|
    |Nom d’utilisateur | Compte de connexion SQL disposant d’un lecteur de données et d’un accès en écriture à la base de données mentionnée ci-avant.|
    |Mot de passe | Mot de passe du compte de connexion SQL mentionné ci-dessus.|
    |Table | Nom de la table qui sera sortie pour la tâche de diffusion en continu.|
    |Héritage du partitionnement| Cette option de configuration de la sortie SQL permet d’hériter le schéma de partitionnement de l’entrée ou l’étape de requête précédente. L’activation de cette option contribue à améliorer les débits lors de l’écriture dans une table sur disque et l’utilisation d’une topologie de travail massivement parallèle.|
    |Taille du lot| La taille de lot correspond au nombre maximal d’enregistrements qui sont envoyés avec chaque opération d’insertion en bloc.|

    Exemple de configuration d’entrée/ sortie ci-après :

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
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

4. Définissez la requête de tâche ASA pour la tâche Edge. Cette requête doit utiliser les alias d’entrée/sortie définis comme noms d’entrée et de sortie. Pour plus d’informations, consultez [Référence sur le langage de requête Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Définissez les paramètres de compte de stockage pour le travail Edge. Le compte de stockage est utilisé comme cible de publication pour la tâche Edge.

6. Sous Configurer, sélectionnez Publier, puis cliquez sur le bouton Publier. Enregistrez l’URL SAS à utiliser avec le module SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>Déployer la tâche Edge Stream Analytics dans le module SQL Database Edge

Pour déployer la tâche de diffusion en continu dans le module SQL Database Edge, mettez à jour la configuration de celui-ci afin d’inclure l’URI SAP pour la tâche de diffusion en continu à l’étape précédente. Pour mettre à jour le module SQL Database Edge

1. Dans le Portail Azure, accédez à votre déploiement IoT Hub.

2. Dans le volet gauche, cliquez sur **IoT Edge**.

3. Sur la page **IoT Edge**, recherchez et sélectionnez l’instance IoT Edge dans laquelle le module SQL Database Edge est déployé.

4. Sur la page *Appareil IoT Edge*, cliquez sur **Définir des modules**. 

5. Sur la page **Définir des modules**, cliquez sur *configure* pour le module SQL Database Edge. 

6. Dans le volet **Modules personnalisés IoT Edge**, sélectionnez *Définir les propriétés souhaitées du jumeau de module*, puis mettez à jour les propriétés souhaitées pour inclure l’URI de l’option ASAJobInfo comme indiqué dans l’exemple ci-dessous. 

    > [!NOTE]
    > L’URI SAP ci-dessous est fournie à titre d’exemple. Remplacez l’URI par l’URI réel de votre déploiement.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Cliquez sur **Enregistrer**.

8. Sur la page **Définir des modules**, cliquez sur *Suivant*.

9. Sur la page **Définir des modules**, cliquez sur *Suivant*, puis sur **Envoyer**.

10. Après la mise à jour du module, le fichier de tâche d’analytique de flux est téléchargé, décompressé et déployé sur l’instance SQL Database Edge.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la tarification et la disponibilité, consultez [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Demandez à activer Azure SQL Database Edge pour votre abonnement.
- Pour commencer, consultez les procédures suivantes :
  - [Déployer SQL Database Edge via le Portail Azure](deploy-portal.md)
