---
title: Utilisation des packages DAC SQL Database – Azure SQL Edge (préversion)
description: En savoir plus sur l’utilisation de packages DAC dans Azure SQL Edge (préversion)
keywords: SQL Edge, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 17d0ba4d7298e60255477c4801e0bcb9b9a4ecac
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595878"
---
# <a name="sql-database-dac-packages-in-sql-edge"></a>Packages DAC SQL Database dans SQL Edge

Azure SQL Edge (préversion) est un moteur de base de données relationnelle optimisé et conçu pour les déploiements IoT et Edge. Il repose sur les dernières versions du moteur de base de données Microsoft SQL Server, qui fournit des fonctionnalités de performances, de sécurité et de traitement des requêtes de pointe. Avec les fonctionnalités de gestion de base de données relationnelle de SQL Server les plus performantes du marché, Azure SQL Edge offre des fonctionnalités de diffusion en continu intégrées pour l’analyse en temps réel et le traitement des événements complexes.

Azure SQL Edge fournit également une implémentation native de SqlPackage.exe qui vous permet de déployer un package [DAC SQL Database](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) pendant le déploiement de SQL Edge. Vous pouvez déployer des packages DAC SQL Database sur SQL Edge en utilisant le paramètre SqlPackage exposé via l’option `module twin's desired properties` du module SQL Edge :

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

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Utiliser un package DAC SQL Database avec SQL Edge

Pour utiliser un package DAC SQL Database (*.dacpac) avec SQL Edge, procédez comme suit :

1. Créez ou extrayez un package DAC SQL Database. Pour savoir comment générer un package DAC pour une base de données SQL Server existante, consultez [Extraire une package DAC d’une base de données](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/).

2. Compressez le fichier *.dacpac et chargez-le dans un compte Stockage Blob Azure. Pour plus d’informations sur le chargement de fichiers dans Stockage Blob Azure, consultez [Charger, télécharger et lister les objets blob avec le portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Générez une signature d’accès partagé pour le fichier zip à partir du portail Azure. Pour plus d’informations, consultez [Déléguer l’accès avec des signature d’accès partagé (SAP)](../storage/common/storage-sas-overview.md).

4. Mettez à jour la configuration du module SQL Edge pour inclure l’URI d’accès partagé du package DAC. Pour mettre à jour le module SQL Edge, procédez comme suit :

    1. Sur le portail Azure, accédez à votre déploiement IoT Hub.

    2. Dans le volet gauche, sélectionnez **IoT Edge**.

    3. Dans la page **IoT Edge**, recherchez et sélectionnez l’IoT Edge dans lequel le module SQL Edge est déployé.

    4. Dans la page **Appareil IoT Edge**, sélectionnez **Définir des modules**.

    5. Dans la page **Définir des modules**, sélectionnez **Configurer** pour le module SQL Edge.

    6. Dans le volet **Modules personnalisés IoT Edge**, sélectionnez **Set module twin's desired properties** (Définir les propriétés souhaitées du jumeau de module). Mettez à jour les propriétés souhaitées de façon à inclure l’URI pour l’option `SQLPackage`, comme dans l’exemple suivant.

        > [!NOTE]
        > L’URI SAS figurant dans le code JSON ci-dessous n’est qu’un exemple. Remplacez l’URI par l’URI réel de votre déploiement.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac",
                }
            }
        ```

    7. Sélectionnez **Enregistrer**.

    8. Dans la page **Définir des modules**, sélectionnez **Suivant**.

    9. Dans la page **Définir des modules**, sélectionnez **Suivant**, puis **Envoyer**.

5. Après la mise à jour du module, le fichier de package DAC est téléchargé, décompressé et déployé pour l’instance SQL Edge.

À chaque redémarrage du conteneur Azure SQL Edge, le package de fichiers *.dacpac est téléchargé et évalué pour déterminer s’il a été modifié. Si une nouvelle version du fichier dacpac est rencontrée, les modifications sont déployées vers la base de données dans SQL Edge.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer SQL Edge via le portail Azure](deploy-portal.md)
- [Transmettre des données en continu](stream-data.md)
- [Machine Learning et intelligence artificielle avec ONNX dans SQL Edge (préversion)](onnx-overview.md)
