---
title: Utilisation de travaux à la périphérie Azure Stream Analytics avec Azure SQL Edge (préversion)
description: En savoir plus sur l’utilisation de travaux Stream Analytics dans Azure SQL Edge (préversion)
keywords: SQL Edge, Stream Analytics,
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b57cb8cae80381a6c2cd88358dd9284ba56c919
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594518"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>Utilisation de travaux Azure Stream Analytics avec SQL Edge

Azure SQL Edge (préversion) est un moteur de base de données relationnelle optimisé et conçu pour les déploiements IoT et Edge. Il repose sur les dernières versions du moteur de base de données Microsoft SQL Server, qui fournit des fonctionnalités de performances, de sécurité et de traitement des requêtes de pointe. Avec les fonctionnalités de gestion de base de données relationnelle de SQL Server les plus performantes du marché, Azure SQL Edge offre des fonctionnalités de diffusion en continu intégrées pour l’analyse en temps réel et le traitement des événements complexes.

Azure SQL Edge possède une implémentation native du runtime Stream Analytics. Cette implémentation vous permet de créer une tâche Azure Stream Analytics Edge et de la déployer en tant que tâche de diffusion en continu SQL Edge. Vous pouvez déployer des travaux Azure Stream Analytics sur SQL Edge à l’aide du paramètre ASAJobInfo exposé via l’option `module twin's desired properties` du module SQL Edge :

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
| ASAJobInfo | URI de Stockage Blob Azure pour la tâche ASA Edge.

## <a name="create-an-azure-stream-analytics-edge-job"></a>Créer un travail à la périphérie Azure Stream Analytics

1. Accédez au portail Azure.

2. Créez une tâche **Azure Stream Analytics sur IoT Edge**. Choisissez l’environnement d’hébergement qui cible **Edge**.

3. Définissez l’entrée et la sortie de la tâche Azure Stream Analytics. Chaque sortie SQL, que vous allez configurer ici, est liée à une seule table dans la base de données. Si vous avez besoin de transmettre en continu des données vers plusieurs tables, vous devez créer plusieurs sorties SQL Database. Vous pouvez configurer les sorties SQL pour qu’elles pointent vers différentes bases de données.

    **Entrée**. Choisissez EdgeHub comme entrée de la tâche Edge et fournissez des informations sur la ressource.

    **Sortie**. Sélectionnez SQL Database comme sortie. Sélectionnez **Fournir les paramètres de base de données SQL manuellement**. Fournissez les détails de configuration pour la base de données et la table.

    |Champ      | Description |
    |---------------|-------------|
    |Alias de sortie | Nom de l’alias de sortie.|
    |Base de données | Nom de la base de données SQL. Il doit s’agir d’un nom valide d’une base de données qui existe dans l’instance SQL Edge.|
    |Nom du serveur | Nom (ou adresse IP) et détails du numéro de port de l’instance SQL. Pour un déploiement SQL Edge, vous pouvez utiliser **tcp:.,1433** comme nom de serveur.|
    |Nom d’utilisateur | Compte de connexion SQL disposant d’un accès en lecture et en écriture aux données de la base de données que vous avez spécifiée précédemment.|
    |Mot de passe | Mot de passe du compte de connexion SQL que vous avez spécifié précédemment.|
    |Table de charge de travail | Nom de la table qui sera générée pour la tâche de streaming.|
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

6. Sous **Configurer**, sélectionnez **Publier**, puis cliquez sur le bouton **Publier**. Enregistrez l’URI SAP à utiliser avec le module SQL Edge.

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>Déployer un travail à la périphérie Azure Stream Analytics sur SQL Edge

Pour déployer le travail de diffusion en continu vers le module SQL Edge, mettez à jour la configuration de celui-ci afin d’inclure l’URI SAP pour le travail de diffusion en continu de l’étape précédente. Pour mettre à jour le module SQL Edge :

1. Sur le portail Azure, accédez à votre déploiement IoT Hub.

2. Dans le volet gauche, sélectionnez **IoT Edge**.

3. Dans la page **IoT Edge**, recherchez et sélectionnez l’IoT Edge dans lequel le module SQL Edge est déployé.

4. Dans la page **Appareil IoT Edge**, sélectionnez **Définir des modules**.

5. Dans la page **Définir des modules**, sélectionnez **Configurer** pour le module SQL Edge.

6. Dans le volet **Modules personnalisés IoT Edge**, sélectionnez **Set module twin's desired properties** (Définir les propriétés souhaitées du jumeau de module). Mettez à jour les propriétés souhaitées de façon à inclure l’URI pour l’option `ASAJobInfo`, comme dans l’exemple suivant.

    > [!NOTE]
    > L’URI SAS figurant dans le code JSON ci-dessous n’est qu’un exemple. Remplacez l’URI par l’URI réel de votre déploiement.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. Sélectionnez **Enregistrer**.

8. Dans la page **Définir des modules**, sélectionnez **Suivant**.

9. Dans la page **Définir des modules**, sélectionnez **Suivant**, puis **Envoyer**.

10. Après la mise à jour du module, le fichier de travail Stream Analytics est téléchargé, décompressé et déployé pour l’instance SQL Edge.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer SQL Edge via le portail Azure](deploy-portal.md)

- [Transmettre des données en continu](stream-data.md)

- [Machine Learning et intelligence artificielle avec ONNX dans SQL Edge (préversion)](onnx-overview.md)
