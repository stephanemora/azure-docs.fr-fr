---
title: Calculer le nombre et la taille des blobs à l’aide de l’inventaire de Stockage Azure
description: Apprenez à calculer le nombre et la taille totale des blobs par conteneur.
services: storage
author: normesta
ms.author: normesta
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 8365c4873165b5a8040bc3def5743eca435cb7e9
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110653934"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Calculer le nombre et la taille totale des blobs par conteneur à l’aide de l’inventaire de Stockage Azure

Cet article utilise la fonctionnalité d’inventaire de Stockage Blob Azure et Azure Synapse pour calculer le nombre de blobs et la taille totale des blobs par conteneur. Ces valeurs sont utiles lors de l’optimisation de l’utilisation des blobs par conteneur.

Les métadonnées des blobs ne sont pas incluses dans cette méthode. La fonctionnalité d’inventaire de Stockage Blob Azure utilise l’API REST [List Blobs](/rest/api/storageservices/list-blobs) avec les paramètres par défaut. Ainsi, l’exemple ne prend pas en charge les instantanés, les conteneurs « $ », etc.

> [!IMPORTANT]
> L’inventaire des blobs est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure disponibles en version bêta, en préversion ou qui ne sont pas encore en phase de disponibilité générale.

## <a name="enable-inventory-reports"></a>Activation des rapports d’inventaire

La première étape de cette méthode consiste à [activer les rapports d’inventaire](blob-inventory.md#enable-inventory-reports) sur votre compte de stockage. Vous devrez peut-être attendre jusqu’à 24 heures après avoir activé les rapports d’inventaire pour que votre premier rapport soit généré.

Lorsque vous avez un rapport d’inventaire à analyser, accordez-vous un accès en lecture blob sur le conteneur où se trouve le fichier CSV du rapport.

1. Accédez au conteneur à l’aide du fichier de rapport CSV de l’inventaire.
1. Sélectionnez **Contrôle d’accès (IAM)** , puis **Ajouter des attributions de rôle**.

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="Sélectionner Ajouter des attributions de rôle":::

1. Sélectionnez **Lecteur des données Blob du stockage** dans la liste déroulante **Rôle**.

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="Ajouter le rôle Lecteur des données Blob du stockage à partir de la liste déroulante":::.

1. Dans le champ **Sélectionner**, entrez l’adresse e-mail du compte que vous utilisez pour exécuter le rapport.

## <a name="create-an-azure-synapse-workspace"></a>Créer un espace de travail Azure Synapse

Ensuite, [créez un espace de travail Azure Synapse](../../synapse-analytics/get-started-create-workspace.md) dans lequel vous allez exécuter une requête SQL pour signaler les résultats de l’inventaire.

## <a name="create-the-sql-query"></a>Créer la requête SQL

Une fois que vous avez créé votre espace de travail Azure Synapse, procédez comme suit.

1. Accédez à [https://web.azuresynapse.net](https://web.azuresynapse.net).
1. Sélectionnez l’onglet **Développer** sur le bord gauche.
1. Sélectionnez le grand signe plus (+) pour ajouter un élément.
1. Sélectionnez **Script SQL**.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="Sélectionner Script SQL pour créer une nouvelle requête":::

## <a name="run-the-sql-query"></a>Exécuter la requête SQL

1. Ajoutez la requête SQL suivante dans votre espace de travail Azure Synapse pour [lire le fichier CSV de l’inventaire](../../synapse-analytics/sql/query-single-csv-file.md#read-a-csv-file).

    Pour le paramètre `bulk`, utilisez l’URL du fichier CSV du rapport d’inventaire que vous souhaitez analyser.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. Nommez votre requête SQL dans le volet Propriétés à droite.

1. Publiez votre requête SQL en appuyant sur CTRL+S ou en sélectionnant le bouton **Publier tout**.

1. Sélectionnez le bouton **Exécuter** pour exécuter la requête SQL. Le nombre et la taille totale des blobs par conteneur sont indiqués dans le volet **Résultats**.

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="Sortie de l’exécution du script pour calculer le nombre et la taille totale des blobs.":::

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des données d’objets blob avec l’inventaire des objets blob de Stockage Azure](blob-inventory.md)
- [Calculer la taille totale de facturation d’un conteneur d’objets blob](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)