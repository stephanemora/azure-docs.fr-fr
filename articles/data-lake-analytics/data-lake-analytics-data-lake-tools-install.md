---
title: Installer Azure Data Lake Tools pour Visual Studio
description: Cet article explique comment installer Azure Data Lake Tools pour Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: df3bf715f8571659d0e622729562055428ab5c97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87125936"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Installation de Data Lake Tools pour Visual Studio

Découvrez comment utiliser Visual Studio pour créer des comptes Azure Data Lake Analytics. Vous pouvez définir des tâches dans [U-SQL](data-lake-analytics-u-sql-get-started.md), et soumettre des tâches au service Data Lake Analytics. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Conditions préalables requises

* **Visual Studio** : toutes les éditions sauf Express sont prises en charge.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Kit SDK Microsoft Azure pour .NET** version 2.7.1 ou ultérieure. Installez-le à l’aide de [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
* Un compte**Data Lake Analytics**. Pour créer un compte, consultez [Prise en main d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Installer Azure Data Lake Tools pour Visual Studio 2017 ou Visual Studio 2019

Azure Data Lake Tools pour Visual Studio est pris en charge dans Visual Studio 2017 15.3 ou version ultérieure. L’outil fait partie des charges de travail **Développement Azure** et **Stockage et traitement des données**. Activez l’une de ces deux charges de travail dans le cadre de votre installation de Visual Studio.

Activez la charge de travail **Stockage et traitement des données** comme indiqué :

![Activer la charge de travail de stockage et traitement des données](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Activez la charge de travail de **développement Azure** comme indiqué :

![Sélectionner la charge de travail de développement Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Installer Azure Data Lake Tools pour Visual Studio 2013 et 2015

Téléchargez et installez [Microsoft Azure Data Lake et Stream Analytics Tools pour Visual Studio](https://aka.ms/adltoolsvs). Après l'installation, Visual Studio affiche les modifications suivantes :

* Le nœud **Server Explorer** > **Azure** contient un nœud **Data Lake Analytics**.
* Le menu **Outils** a un élément **Data Lake**.

## <a name="next-steps"></a>Étapes suivantes

* Pour consigner les informations de diagnostic, voir [Accès aux journaux de diagnostic d’Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pour voir une requête plus complexe, consultez [Analyse de journaux d’activité des sites web à l’aide d’Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour utiliser la vue d’exécution du vertex, voir [Utilisation de la vue d’exécution du vertex dans Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
