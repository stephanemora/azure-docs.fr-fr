---
title: Outils de science des données et d’apprentissage automatique - Azure | Microsoft Docs
description: Outils de science des données et d’apprentissage automatique
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 9a9dc868c4f22f95ca5027e3c95513d176c69eac
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392396"
---
# <a name="machine-learning-and-data-science-tools"></a>Outils de science des données et d’apprentissage automatique
La machine virtuelle DSVM (Data Science Virtual Machine) offre un ensemble complet d’outils et de bibliothèques d’apprentissage automatique disponibles dans des langages courants tels que Python, R et Julia. 

Voici quelques-uns des outils et bibliothèques d’apprentissage automatique disponibles sur la machine virtuelle DSVM. 

## <a name="azure-machine-learning-servicehttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>SDK du [service Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml)
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |   Le service Azure Machine Learning (préversion) est un service cloud que vous pouvez utiliser pour développer et déployer des modèles de machine learning.  Vous pouvez suivre vos modèles avec le SDK Python pendant les opérations de création, d’entraînement, de mise à l’échelle et de gestion que vous effectuez sur ceux-ci. Déployez des modèles en tant que conteneurs et exécutez-les sur le cloud, en local ou sur IoT Edge.   |
| Éditions DSVM prises en charge     | Windows (environnement Conda : AzureML), Linux (environnement Conda : py36)    |
| Utilisations classiques      | Plateforme de machine learning général      |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?      |  Installé avec prise en charge GPU   |
| Comment l’utiliser/l’exécuter ?      | Comme SDK Python et comme outil de ligne de commande Azure. Activez pour l’environnement conda `AzureML` sur l’édition Windows ou pour `py36` sur l’édition Linux.      |
| Liens vers des exemples      | Des exemples de notebooks Jupyter sont inclus dans le répertoire `AzureML` sous notebooks  |
| Outils connexes sur la machine virtuelle DSVM      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |    Bibliothèque Gradient Boosting (GBDT, GBRT ou GBM) rapide, portable et distribuée pour Python, R, Java, Scala, C++ et bien plus encore. S’exécute sur une seule machine, Hadoop, Spark    |
| Éditions DSVM prises en charge     | Windows, Linux     |
| Utilisations classiques      | Bibliothèque ML générale      |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?      |  Installé avec prise en charge GPU   |
| Comment l’utiliser/l’exécuter ?      | En tant que bibliothèque Python (2.7 et 3.5), package R et sur l’outil en ligne de commande de chemin (`C:\dsvm\tools\xgboost\bin\xgboost.exe` pour Windows, `/dsvm/tools/xgboost/xgboost` pour Linux)    |
| Liens vers des exemples      | Des exemples sont fournis sur la machine virtuelle, dans `/dsvm/tools/xgboost/demo` sur Linux et `C:\dsvm\tools\xgboost\demo` sur Windows   |
| Outils connexes sur la machine virtuelle DSVM      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |   Vowpal Wabbit (également appelé « VW ») est une bibliothèque de système d’apprentissage Open Source rapide et hors cœur    |
| Éditions DSVM prises en charge     | Windows, Linux     |
| Utilisations classiques      | Bibliothèque ML générale      |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?      |  Windows : programme d’installation msi, Linux : apt-get |
| Comment l’utiliser/l’exécuter ?      | En tant qu’outil en ligne de commande de chemin (`C:\Program Files\VowpalWabbit\vw.exe` sur Windows, `/usr/bin/vw` sur Linux)    |
| Liens vers des exemples      | [Exemples VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Outils connexes sur la machine virtuelle DSVM      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |  Weka est une collection d’algorithmes d’apprentissage automatique pour les tâches d’exploration de données. Les algorithmes peuvent être appliqués directement à un jeu de données ou être appelés à partir de votre propre code Java. Weka contient des outils pour le prétraitement des données, la classification, la régression, le clustering, les règles d’association et la visualisation. |
| Éditions DSVM prises en charge     | Windows, Linux     |
| Utilisations classiques      | Outil ML général     |
| Comment l’utiliser/l’exécuter ?      | Sur Windows, recherchez Weka dans le menu Démarrer. Sur Linux, connectez-vous avec X2Go, puis accédez à Applications -> Développement -> Weka. |
| Liens vers des exemples      | [Exemples Weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Outils connexes sur la machine virtuelle DSVM      |LightGBM, Rattle, XGBooost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |   Une interface graphique utilisateur pour l’exploration de données à l’aide de R   |
| Éditions DSVM prises en charge     | Windows, Linux     |
| Utilisations classiques      | Outil général d’interface graphique utilisateur d’exploration de données pour R    |
| Comment l’utiliser/l’exécuter ?      | Outil d’interface utilisateur. Sur Windows, démarrez une invite de commandes, exécutez R puis, dans R, exécutez `rattle()`. Sur Linux, connectez-vous avec X2Go, démarrez un terminal, exécutez R puis, dans R, exécutez `rattle()`. |
| Liens vers des exemples      | [Rattle](https://togaware.com/onepager/) |
| Outils connexes sur la machine virtuelle DSVM      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Un framework Gradient Boosting (GBDT, GBRT, GBM ou MART) rapide, distribué et à hautes performances basé sur des algorithmes d’arbre de décision et utilisé pour le classement, la classification et de nombreuses autres tâches d’apprentissage automatique.    |
| Versions DSVM prises en charge      | Windows, Linux    |
| Utilisations classiques      | Framework Gradient Boosting à usage général      |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?      | Sur Windows, LightGBM est installé en tant que package Python. Sur Linux, le fichier exécutable de ligne de commande se trouve dans `/opt/LightGBM/lightgbm`, le package R est installé et des packages Python sont installés.     |
| Liens vers des exemples      | [Guide LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Outils connexes sur la machine virtuelle DSVM      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Une plateforme d’intelligence artificielle open source prenant en charge des fonctionnalités d’apprentissage automatique en mémoire, distribuées, rapides et scalables  |
| Versions DSVM prises en charge      | Linux   |
| Utilisations classiques      | ML scalable, distribué et à usage général   |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?      | H2O est installé dans `/dsvm/tools/h2o`.      |
| Comment l’utiliser/l’exécuter ?      | Connectez-vous à la machine virtuelle à l’aide de X2Go. Démarrez un nouveau terminal et exécutez `java -jar /dsvm/tools/h2o/current/h2o.jar`. Ensuite, démarrez un navigateur web et connectez-vous à `http://localhost:54321`.      |
| Liens vers des exemples      | Des exemples sont disponibles sur la machine virtuelle dans Jupyter sous le répertoire `h2o`.      |
| Outils connexes sur la machine virtuelle DSVM      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Il existe plusieurs autres bibliothèques ML sur la machine virtuelle DSVM, comme le package `scikit-learn` fourni dans le cadre de la distribution Anaconda Python installée sur la machine virtuelle DSVM. Veillez à consulter la liste des packages disponibles dans Julia, Python et R en exécutant les gestionnaires de package correspondants. 
