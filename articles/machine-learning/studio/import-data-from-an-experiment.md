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
ms.openlocfilehash: 922619f09ffc72a79c5ff6dac1bbc6807c38467c
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344516"
---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Importer vos données dans Azure Machine Learning Studio à partir d’une autre expérience

Il se peut que vous ayez parfois besoin d’obtenir un résultat intermédiaire à partir d’une expérience et de l’utiliser dans le cadre d’une autre expérience. Pour ce faire, vous enregistrez le module en tant que jeu de données :

1. Cliquez sur la sortie du module que vous souhaitez enregistrer en tant que jeu de données.
2. Cliquez sur **Enregistrer comme jeu de données**.
3. Lorsque vous y êtes invité, saisissez un nom et une description qui vous permet d'identifier facilement le jeu de données.
4. Cliquez sur la coche **OK** .

Lorsque l'enregistrement est terminé, le jeu de données sera disponible pour être utilisé dans n'importe quelle expérience dans votre espace de travail. Vous pouvez le trouver dans la liste **Jeux de données enregistrés** dans la palette des modules.

