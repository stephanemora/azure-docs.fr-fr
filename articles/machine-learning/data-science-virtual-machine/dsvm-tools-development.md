---
title: Outils de développement
titleSuffix: Azure Data Science Virtual Machine
description: Découvrez les outils et environnements de développement intégrés disponibles sur Data Science Virtual Machine.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: efe57637347b5886827f5da443acd9240974d687
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070963"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Outils de développement sur Azure Data Science Virtual Machine

Data Science Virtual Machine (DSVM) regroupe plusieurs outils populaires dans un environnement de développement intégré (IDE) très productif. Voici quelques-uns des outils disponibles sur la machine virtuelle DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

| Category | Valeur |
|--|--|
| Qu’est-ce que c’est ? | IDE à usage général |
| Versions DSVM prises en charge | Windows Server 2019 : Visual Studio 2019 |
| Utilisations classiques | Développement de logiciels |
| Comment est-il configuré et installé sur la machine virtuelle DSVM ? | Charge de travail Science des données (outils Python et R), charge de travail Azure (Hadoop, Data Lake), Node.js, outils SQL Server, [Azure Machine Learning pour Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai) |
| Comment l’utiliser et l’exécuter ? | Raccourci sur le Bureau (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`). Visuellement, ouvrez Visual Studio à l’aide de l’icône du Bureau ou du menu **Démarrer**. Recherchez des programmes (en tapant touche Windows + S), suivi de **Visual Studio**. À partir d’ici, vous pouvez créer des projets dans des langages comme C#, Python, R et Node.js. |

> [!NOTE]
> Il est possible que vous receviez un message indiquant que votre période d’évaluation a expiré. Entrez les informations d’identification de votre compte Microsoft. Vous pouvez aussi créer un compte gratuit pour accéder à Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

| Category | Valeur |
|--|--|
| Qu’est-ce que c’est ? | IDE à usage général |
| Versions DSVM prises en charge | Windows, Linux |
| Utilisations classiques | Éditeur de code et Intégration de Git |
| Comment l’utiliser et l’exécuter ? | Raccourci sur le Bureau (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) dans Windows, raccourci sur le Bureau ou terminal (`code`) dans Linux |

## <a name="rstudio-desktop"></a>RStudio Desktop

| Category | Valeur |
|--|--|
| Qu’est-ce que c’est ? | IDE client pour le langage R |
| Versions DSVM prises en charge | Windows, Linux |
| Utilisations classiques | Développement R |
| Comment l’utiliser et l’exécuter ? | Raccourci sur le Bureau (`C:\Program Files\RStudio\bin\rstudio.exe`) sur Windows, raccourci sur le Bureau (`/usr/bin/rstudio`) sur Linux |

## <a name="pycharm"></a>PyCharm

| Category | Valeur |
|--|--|
| Qu’est-ce que c’est ? | IDE client pour le langage Python |
| Versions DSVM prises en charge | Windows 2019, Ubuntu 18.04 |
| Utilisations classiques | Développement Python |
| Comment l’utiliser et l’exécuter ? | Raccourci sur le Bureau (`C:\Program Files\tk`) sur Windows. Raccourci sur le Bureau (`/usr/bin/pycharm`) sur Linux |
