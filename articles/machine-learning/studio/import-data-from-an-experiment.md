---
title: Importer des données dans Machine Learning Studio à partir d’une autre expérience | Microsoft Docs
description: Comment enregistrer des données d’apprentissage dans Azure Machine Learning Studio et les utiliser dans une autre expérience
keywords: importer des données,données,sources de données,données d’apprentissage
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 7da9dcec-5693-4bb6-8166-15904e7f75c3
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 5007657da11cb45c6511780398c5425d14dca900
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246570"
---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Importer vos données dans Azure Machine Learning Studio à partir d’une autre expérience
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Il se peut que vous ayez parfois besoin d’obtenir un résultat intermédiaire à partir d’une expérience et de l’utiliser dans le cadre d’une autre expérience. Pour ce faire, vous enregistrez le module en tant que jeu de données :

1. Cliquez sur la sortie du module que vous souhaitez enregistrer en tant que jeu de données.
2. Cliquez sur **Enregistrer comme jeu de données**.
3. Lorsque vous y êtes invité, saisissez un nom et une description qui vous permet d'identifier facilement le jeu de données.
4. Cliquez sur la coche **OK** .

Lorsque l'enregistrement est terminé, le jeu de données sera disponible pour être utilisé dans n'importe quelle expérience dans votre espace de travail. Vous pouvez le trouver dans la liste **Jeux de données enregistrés** dans la palette des modules.

