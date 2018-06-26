---
title: Installer Azure Data Lake Tools pour Visual Studio
description: Cet article explique comment installer Azure Data Lake Tools pour Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 05/03/2018
ms.openlocfilehash: c520c437212c23cc9dc8327c95b9f2a77b08e1ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622868"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Installation de Data Lake Tools pour Visual Studio

Découvrez comment utiliser Visual Studio pour créer des comptes Azure Data Lake Analytics, définir des travaux dans [U-SQL](data-lake-analytics-u-sql-get-started.md) et envoyer des travaux au service Data Lake Analytics. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Prérequis

* **Visual Studio** : toutes les éditions sauf Express sont prises en charge.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Kit SDK Microsoft Azure pour .NET** version 2.7.1 ou ultérieure.  Installez-le à l’aide de [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
* Un compte**Data Lake Analytics**. Pour créer un compte, consultez [Prise en main d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Installer Azure Data Lake Tools pour Visual Studio 2017

Azure Data Lake Tools pour Visual Studio est pris en charge dans Visual Studio 2017 15.3 ou version supérieure. L’outil fait partie des charges de travail **Développement Azure** et **Stockage et traitement des données** dans Visual Studio Installer. Activez l’une de ces deux charges de travail dans le cadre de votre installation de Visual Studio.  

Activez la charge de travail **Stockage et traitement des données** comme indiqué : ![Activer la charge de travail Stockage et traitement des données](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Activez la charge de travail **Développement Azure** comme indiqué : ![Activer la charge de travail Développement Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Installer Azure Data Lake Tools pour Visual Studio 2013 et 2015

Téléchargez et installez Azure Data Lake Tools pour Visual Studio [à partir du centre de téléchargement](http://aka.ms/adltoolsvs). Après l’installation, notez les points suivants :
* Le nœud **Server Explorer** > **Azure** contient un nœud **Data Lake Analytics**. 
* Le menu **Outils** a un élément **Data Lake**.


## <a name="next-steps"></a>Étapes suivantes
* Pour consigner les informations de diagnostic, consultez [Accès aux journaux de diagnostic d’Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour utiliser la vue d’exécution du vertex, voir [Utiliser la vue d’exécution du vertex dans Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
