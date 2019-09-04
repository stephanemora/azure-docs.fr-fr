---
title: Outils de développement de la machine virtuelle DSVM - Azure | Microsoft Docs
description: Découvrez les outils et les environnements de développement intégrés qui sont préinstallés sur Data Science Virtual Machine.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 8f9dad0fb007945b69b75daadfdb12f61dc4defb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074299"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Outils de développement sur la machine virtuelle DSVM

Data Science Virtual Machine (DSVM) regroupe plusieurs outils populaires dans un environnement de développement intégré (IDE) très productif. Voici quelques-uns des outils disponibles sur la machine virtuelle DSVM.

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE à usage général      |
| Versions DSVM prises en charge      | Windows      |
| Utilisations classiques      | Développement de logiciels    |
| Comment est-il configuré et installé sur la machine virtuelle DSVM ?      | Charge de travail Science des données (outils Python et R), charge de travail Azure (Hadoop, Data Lake), Node.js, outils SQL Server, [Azure Machine Learning pour Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Comment l’utiliser et l’exécuter ?      | Raccourci sur le Bureau (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`)    |
| Outils associés sur la machine virtuelle DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE à usage général      |
| Versions DSVM prises en charge      | Windows, Linux     |
| Utilisations classiques      | Éditeur de code et Intégration de Git   |
| Comment l’utiliser et l’exécuter ?      | Raccourci sur le Bureau (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) dans Windows, raccourci sur le Bureau ou terminal (`code`) dans Linux    |
| Outils associés sur la machine virtuelle DSVM      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE client pour le langage R   |
| Versions DSVM prises en charge      | Windows, Linux      |
| Utilisations classiques      |  Développement R     |
| Comment l’utiliser et l’exécuter ?      | Raccourci sur le Bureau (`C:\Program Files\RStudio\bin\rstudio.exe`) sur Windows, raccourci sur le Bureau (`/usr/bin/rstudio`) sur Linux      |
| Outils associés sur la machine virtuelle DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio Server 

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE client pour le langage R   |
| Qu’est-ce que c’est ?   | IDE basé sur le web pour R    |
| Versions DSVM prises en charge      | Linux      |
| Utilisations classiques      |  Développement R     |
| Comment l’utiliser et l’exécuter ?      | Activez le service avec _systemctl enable rstudio-server_, puis démarrez-le avec _systemctl start rstudio-server_. Connectez-vous ensuite à RStudio Server sur http:\//adresse_IP_de_votre_machine_virtuelle:8787.       |
| Outils associés sur la machine virtuelle DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE client pour le langage Julia   |
| Versions DSVM prises en charge      | Windows, Linux      |
| Utilisations classiques      |  Développement Julia     |
| Comment l’utiliser et l’exécuter ?      | Raccourci sur le Bureau (`C:\JuliaPro-0.5.1.1\Juno.bat`) sur Windows, raccourci sur le Bureau (`/opt/JuliaPro-VERSION/Juno`) sur Linux      |
| Outils associés sur la machine virtuelle DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE client pour le langage Python    |
| Versions DSVM prises en charge      | Linux      |
| Utilisations classiques      |  Développement Python     |
| Comment l’utiliser et l’exécuter ?      | Raccourci sur le Bureau (`/usr/bin/pycharm`) sur Linux      |
| Outils associés sur la machine virtuelle DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Outil décisionnel et de visualisation interactive des données    |
| Versions DSVM prises en charge      | Windows  |
| Utilisations classiques      |  Visualisation des données et création de tableaux de bord   |
| Comment l’utiliser et l’exécuter ?      | Raccourci sur le Bureau (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Outils associés sur la machine virtuelle DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

