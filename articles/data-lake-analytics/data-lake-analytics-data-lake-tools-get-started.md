---
title: Prise en main d’Azure Data Lake Analytics à l’aide de Visual Studio
description: Découvrez comment installer Data Lake Tools pour Visual Studio et développer et tester des scripts U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/13/2018
ms.openlocfilehash: b463946402eee40d0de0942eeaf37a6f9ea59990
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083060"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Développer des scripts U-SQL à l’aide des outils Data Lake pour Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Data Lake et Stream Analytics Tools incluent des fonctionnalités en rapport avec deux services Azure : Azure Data Lake Analytics et Azure Stream Analytics. Pour plus d’informations sur les scénarios Azure Stream Analytics, consultez [Azure Stream Analytics Tools pour Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Cet article explique comment utiliser Visual Studio pour créer des comptes Azure Data Lake Analytics, définir des travaux en [U-SQL](data-lake-analytics-u-sql-get-started.md) et soumettre des travaux au service Data Lake Analytics. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Microsoft recommande de mettre à niveau vers Azure Data Lake Tools pour Visual Studio version 2.3.3000.4 ou ultérieure. Notez que les versions précédentes ne peuvent plus être téléchargées et que Microsoft les déconseille. 
> 
> **Que dois-je faire ?**
> 
> 1. Veillez à utiliser une version d’Azure Data Lake Tools pour Visual Studio antérieure à 2.3.3000.4. 
> 
>    ![Vérifier la version de l’outil](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
> 
> 2. Si votre version est antérieure à 2.3.3000.4, mettez à jour Azure Data Lake Tools pour Visual Studio par le biais du centre de téléchargement : 
>    - [Pour Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Pour Visual Studio 2013 et 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)


## <a name="prerequisites"></a>Conditions préalables

* **Visual Studio**: Toutes les éditions sauf Express sont prises en charge.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Kit SDK Microsoft Azure pour .NET** version 2.7.1 ou ultérieure.  Installez-le à l’aide de [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
* Un compte**Data Lake Analytics**. Pour créer un compte, consultez [Prise en main d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Installer Azure Data Lake Tools pour Visual Studio

Ce didacticiel nécessite que Data Lake Tools pour Visual Studio soit installé. Suivez les [instructions d’installation](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Se connecter à un compte Azure Data Lake Analytics

1. Ouvrez Visual Studio.

2. Ouvrez l’Explorateur de serveurs en sélectionnant **Afficher** > **Explorateur de serveurs**.

3. Cliquez avec le bouton droit sur **Azure**. Puis, sélectionnez **Se connecter à un abonnement Microsoft Azure**, puis suivez les instructions.

4. Dans l’Explorateur de serveurs, sélectionnez **Azure** > **Data Lake Analytics**. Une liste des comptes Data Lake Analytics s’affiche.

## <a name="write-your-first-u-sql-script"></a>Rédiger son premier script U-SQL

Le texte suivant est un script U-SQL simple. Il définit un petit jeu de données et l’écrit dans le Data Lake Store par défaut sous la forme d’un fichier appelé `/data.csv`.

```
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Envoyer le travail Analytique Data Lake

1. Sélectionnez **Fichier** > **Nouveau** > **Projet**.

2. Sélectionnez le type **Projet U-SQL**, puis cliquez sur **OK**. Visual Studio crée une solution avec un fichier **Script.usql**.

3. Collez le script précédent dans la fenêtre **Script.usql**.

4. Dans le coin supérieur gauche de la fenêtre **Script.usql**, spécifiez le compte Data Lake Analytics.

    ![Soumettre un projet U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. Dans le coin supérieur gauche de la fenêtre **Script.usql**, sélectionnez **Envoyer**.

6. Une fois que le travail a été envoyé, l’onglet **Vue du travail** s’affiche pour vous présenter la progression du travail. Cliquez sur le bouton **Actualiser** pour afficher le dernier état du travail et actualiser l’écran.

    ![Graphique des performances du travail U-SQL Visual Studio Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * **Résumé du travail** montre le résumé du travail.   
   * **Graphique des travaux** permet de visualiser la progression du travail.
   * **Opérations sur les métadonnées** indique toutes les actions qui ont été effectuées sur le catalogue U-SQL.
   * **Données** indique toutes les entrées et sorties.
   * **Historique de l’état** affiche la chronologie et les détails d’état.
   * **Analyse des unités d’allocation** indique le nombre d’unités d’allocation utilisées dans le travail et explore des simulations de différentes stratégies d’allocation d’unités d’allocation.
   * **Diagnostics** fournit une analyse avancée pour l’optimisation des performances et de l’exécution des travaux.

## <a name="check-job-status"></a>Vérifier l’état du travail

1. Dans l’Explorateur de serveurs, sélectionnez **Azure** > **Data Lake Analytics**.

2. Développez le nom du compte Data Lake Analytics.

3. Double-cliquez sur **Travaux**.

4. Sélectionnez le travail que vous avez déjà envoyé.

## <a name="see-the-job-output"></a>Visualiser la sortie du travail

1. Dans l’Explorateur de serveurs, accédez au travail que vous avez envoyé.

2. Cliquez sur l'onglet **Données** .

3. Dans l’onglet **Sorties du travail**, sélectionnez le fichier `"/data.csv"`.

## <a name="next-steps"></a>Étapes suivantes

* [Run U-SQL scripts on your own workstation for testing and debugging (Exécuter des scripts U-SQL sur votre station de travail pour les tests et le débogage)](data-lake-analytics-data-lake-tools-local-run.md)
* [Débogage du code C# dans les travaux U-SQL à l’aide d’Azure Data Lake Tools pour Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Utilisation d’Azure Data Lake Tools pour Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
