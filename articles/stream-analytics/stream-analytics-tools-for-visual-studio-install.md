---
title: Configurer Azure Stream Analytics Tools pour Visual Studio
description: Cet article décrit les conditions requises à l’installation d’Azure Stream Analytics Tools pour Visual Studio et la façon de configurer ces outils.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 673f4935dce28b30c10e6abf4c7d22e00c1dd73a
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343908"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Installer Azure Stream Analytics Tools pour Visual Studio
Les outils Azure Stream Analytics prennent en charge Visual Studio 2017, 2015 et 2013. Cet article décrit comment installer et désinstaller ces outils.

Pour plus d’informations sur l’utilisation de ces outils, consultez [Stream Analytics Tools pour Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Installer
### <a name="recommended-visual-studio-2019-and-2017"></a>Recommandé : Visual Studio 2019 et 2017
* Téléchargez [Visual Studio 2019 (préversion 2 ou version ultérieure) et Visual Studio 2017 (15.3 ou version ultérieure)](https://www.visualstudio.com/). Les éditions Enterprise (Ultimate/Premium), Professional et Community sont prises en charge. L’édition Express n’est pas prise en charge. Visual Studio 2017 sur Mac n’est pas pris en charge. 
* Les outils Stream Analytics font partie des charges de travail de**développement Azure** et **Stockage et traitement des données** dans Visual Studio 2017. Activez l’une de ces deux charges de travail dans le cadre de votre installation de Visual Studio.

Activez la charge de travail **Stockage et traitement des données** comme indiqué :

![La charge de travail Stockage et traitement des données est sélectionnée.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Activez la charge de travail de **développement Azure** comme indiqué :

![La charge de travail Développement Azure est sélectionnée.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)

* Dans le menu Outils, choisissez **Extensions et mises à jour**. Recherchez les outils Azure Data Lake et Stream Analytics dans les extensions installées, puis cliquez sur **Mettre à jour** pour installer l’extension la plus récente. 

![Extensions et mises à jour Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-extensions-updates.png)

### <a name="visual-studio-2015-2013"></a>Visual Studio 2015, 2013
* Installez Visual Studio 2015 ou Visual Studio 2013 Update 4. Les éditions Enterprise (Ultimate/Premium), Professional et Community sont prises en charge. L’édition Express n’est pas prise en charge. 
* Installez le kit de développement logiciel Microsoft Azure pour .NET version 2.7.1 ou ultérieure à l’aide de [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
* Installez [Azure Stream Analytics Tools pour Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Mettre à jour

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 et 2017
Le rappel de l’existence d’une nouvelle version s’affiche dans la notification Visual Studio.

![Rappel de l’existence d’une nouvelle version de Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-new-version-reminder-vs-tools.png)

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 et 2013
Les outils Stream Analytics installés pour Visual Studio vérifient automatiquement les nouvelles versions. Suivez les instructions dans la fenêtre contextuelle pour installer la version la plus récente. 


## <a name="uninstall"></a>Désinstaller l’interface

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 et 2017
Double-cliquez sur le programme d’installation de Visual Studio et sélectionnez **Modifier**. Décochez la case **Azure Data Lake et Stream Analytics Tools** de la charge de travail **Stockage et traitement des données** ou **Développement Azure**.

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 et 2013
Accédez au Panneau de configuration et désinstallez **Microsoft Azure Data Lake et Stream Analytics Tools pour Visual Studio**.





