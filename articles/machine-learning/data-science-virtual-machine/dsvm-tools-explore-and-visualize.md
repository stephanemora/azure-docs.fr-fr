---
title: Outils d’exploration et de visualisation de données
titleSuffix: Azure Data Science Virtual Machine
description: Outils d’exploration et de visualisation de données pour la machine virtuelle DSVM
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: a18c374df0cad7994af855089f1096d52fe30ce2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330700"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Outils d’exploration et de visualisation de données sur Azure Data Science Virtual Machine

Dans la science des données, l’essentiel est de comprendre les données. Les outils de visualisation et d’exploration de données aident à accélérer la compréhension des données. Les outils suivants, qui sont fournis sur Data Science Virtual Machine (DSVM), facilitent cette étape clé.

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Moteur de requête SQL open source sur le Big Data    |
| Versions DSVM prises en charge      | Windows, Linux  |
| Comment est-il configuré et installé sur la machine virtuelle DSVM ?      |  Installé dans `/dsvm/tools/drill*` en mode incorporé uniquement   |
| Utilisations classiques      |  Pour l’exploration de données sur place sans nécessiter une opération d’extraction, de transformation et de chargement (ETL). Interroger différents formats et sources de données, notamment CSV, JSON, les tables relationnelles et Hadoop.     |
| Comment l’utiliser et l’exécuter ?      | Raccourci sur le Bureau  <br/> [Prendre en main Drill en 10 minutes](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Outils associés sur la machine virtuelle DSVM      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Outil décisionnel et de visualisation interactive des données    |
| Versions DSVM prises en charge      | Windows  |
| Utilisations classiques      |  Visualisation des données et création de tableaux de bord   |
| Comment l’utiliser et l’exécuter ?      | Raccourci bureau (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`) ou exécutez simplement à partir du menu **Démarrer**.      |
| Outils connexes sur la machine virtuelle DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

> [!NOTE]
> Vous devez disposer d’un compte Microsoft Office 365 pour accéder à Power BI.


## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |   Une interface graphique utilisateur (GUI) pour l’exploration de données à l’aide de R   |
| Éditions DSVM prises en charge     | Windows, Linux     |
| Utilisations classiques      | Outil général d’interface graphique utilisateur d’exploration de données pour R    |
| Comment l’utiliser et l’exécuter ?      | Outil d’interface utilisateur. Sur Windows, ouvrez une invite de commandes, exécutez R, puis dans R, exécutez `rattle()`. Sur Linux, connectez-vous avec X2Go, démarrez un terminal, exécutez R, puis dans R, exécutez `rattle()`. |
| Liens vers des exemples      | [Rattle](https://togaware.com/onepager/) |
| Outils associés sur la machine virtuelle DSVM      |LightGBM, Weka, Xgboost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |  Collection d’algorithmes de Machine Learning pour les tâches d’exploration de données. Ces algorithmes peuvent être appliqués directement à un jeu de données ou être appelés à partir de votre propre code Java. Weka contient des outils pour le prétraitement de données, la classification, la régression, le clustering, les règles d’association et la visualisation. |
| Éditions DSVM prises en charge     | Windows, Linux     |
| Utilisations classiques      | Outil de Machine Learning général     |
| Comment l’utiliser et l’exécuter ?      | Sur Windows, recherchez Weka dans le menu Démarrer. Sur Linux, connectez-vous avec X2Go, puis accédez à Applications > Développement > Weka. |
| Liens vers des exemples      | [Exemples Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Outils associés sur la machine virtuelle DSVM      |LightGBM, Rattle, Xgboost   |




