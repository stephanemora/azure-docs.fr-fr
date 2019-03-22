---
title: Outils de science des données et d’apprentissage automatique - Azure | Microsoft Docs
description: Découvrez les outils et les frameworks de Machine Learning préinstallés sur Data Science Virtual Machine.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: e8876306e4ffbd0fa9a8aafc6d5d757fd3c9c614
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57847363"
---
# <a name="machine-learning-and-data-science-tools"></a>Outils de science des données et d’apprentissage automatique
La machine virtuelle DSVM (Data Science Virtual Machine) offre un ensemble complet d’outils et de bibliothèques d’apprentissage automatique (ML) disponibles dans des langages courants tels que Python, R et Julia. 

Voici quelques exemples d’outils et de bibliothèques de ML sur les machines virtuelles DSVM. 

## <a name="azure-machine-learninghttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>Kit de développement logiciel [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml)
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |   Azure Machine Learning est un service cloud que vous pouvez utiliser pour développer et déployer des modèles de ML. Vous pouvez suivre vos modèles avec le SDK Python pendant les opérations de création, d’entraînement, de scaling et de gestion que vous effectuez sur ceux-ci. Déployez des modèles en tant que conteneurs et exécutez-les dans le cloud, localement, ou sur Azure IoT Edge.   |
| Éditions prises en charge     | Windows (environnement Conda : AzureML), Linux (environnement Conda : py36)    |
| Utilisations classiques      | Plateforme de machine learning général      |
| Comment fonctionne la configuration ou l’installation ?      |  Installé avec prise en charge GPU   |
| Comment l’utiliser ou l’exécuter      | En tant que kit de développement logiciel Python et Azure CLI. Activez pour l’environnement conda `AzureML` sur l’édition Windows *ou* pour `py36` sur l’édition Linux.      |
| Liens vers des exemples      | Des exemples de blocs-notes Jupyter sont inclus dans le répertoire `AzureML` sous blocs-notes.  |
| Outils associés      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |    XGBoost est une bibliothèque Gradient Boosting (GBDT, GBRT ou GBM) rapide, portable et distribuée pour Python, R, Java, Scala, C++ et bien plus encore. S’exécute sur une seule machine, Hadoop, et Spark.    |
| Éditions prises en charge     | Windows, Linux     |
| Utilisations classiques      | Bibliothèque ML générale      |
| Comment fonctionne la configuration ou l’installation ?      |  Installé avec prise en charge GPU   |
| Comment l’utiliser ou l’exécuter      | En tant que bibliothèque Python (2.7 et 3.5), package R et sur l’outil en ligne de commande de chemin (`C:\dsvm\tools\xgboost\bin\xgboost.exe` pour Windows, `/dsvm/tools/xgboost/xgboost` pour Linux)    |
| Liens vers des exemples      | Des exemples sont fournis sur la machine virtuelle, dans `/dsvm/tools/xgboost/demo` sur Linux et `C:\dsvm\tools\xgboost\demo` sur Windows.   |
| Outils associés      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |   Vowpal Wabbit (également appelé « VW ») est une bibliothèque de système d’apprentissage Open Source rapide et hors cœur.    |
| Éditions prises en charge     | Windows, Linux     |
| Utilisations classiques      | Bibliothèque ML générale      |
| Comment fonctionne la configuration ou l’installation ?      |  Programme d’installation msi sur Windows, utilitaire apt-get sur Linux |
| Comment l’utiliser ou l’exécuter      | En tant qu’outil en ligne de commande de chemin (`C:\Program Files\VowpalWabbit\vw.exe` sur Windows, `/usr/bin/vw` sur Linux)    |
| Liens vers des exemples      | [Exemples VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Outils associés      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |  Weka est une collection d’algorithmes d’apprentissage automatique pour les tâches d’exploration de données. Les algorithmes peuvent être appliqués directement à un jeu de données ou être appelés à partir de votre propre code Java. Weka contient des outils pour le prétraitement des données, la classification, la régression, le clustering, les règles d’association et la visualisation. |
| Éditions prises en charge     | Windows, Linux     |
| Utilisations classiques      | Outil ML général     |
| Comment l’utiliser ou l’exécuter      | Sur Windows, recherchez Weka dans le menu Démarrer. Sur Linux, connectez-vous avec X2Go, puis accédez à **Applications** > **Développement** > **Weka**. |
| Liens vers des exemples      | [Exemples Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Outils associés      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   |   Rattle est une interface graphique utilisateur pour l’exploration de données à l’aide de R.   |
| Éditions prises en charge     | Windows, Linux     |
| Utilisations classiques      | Outil général d’interface graphique utilisateur d’exploration de données pour R    |
| Comment l’utiliser ou l’exécuter      | Outil d’interface utilisateur. Sur Windows, démarrez une invite de commandes, exécutez R, puis exécutez `rattle()` dans R. Sur Linux, connectez-vous avec X2Go, démarrez un terminal, exécutez R, puis exécutez `rattle()` dans R. |
| Liens vers des exemples      | [Rattle](https://togaware.com/onepager/) |
| Outils associés      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | LightGBM est une infrastructure d’amélioration progressive rapide, distribuée et hautes performances, basée sur les algorithmes d’arbre de décision. Cette infrastructure est utilisée pour le classement par ordre de priorité, la classification et bien d’autres tâches de ML.    |
| Versions prises en charge      | Windows, Linux    |
| Utilisations classiques      | Framework Gradient Boosting à usage général      |
| Comment fonctionne la configuration ou l’installation ?      | Sur Windows, LightGBM est installé en tant que package Python. Sur Linux, le fichier exécutable de ligne de commande se trouve dans `/opt/LightGBM/lightgbm`, le package R est installé et des packages Python sont installés.     |
| Liens vers des exemples      | [Guide LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Outils associés      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | H2O est une plateforme d’intelligence artificielle open source prenant en charge des fonctionnalités d’apprentissage automatique en mémoire, distribuées, rapides et scalables.  |
| Versions prises en charge      | Linux   |
| Utilisations classiques      | ML scalable, distribué et à usage général   |
| Comment fonctionne la configuration ou l’installation ?      | H2O est installé dans `/dsvm/tools/h2o`.      |
| Comment l’utiliser ou l’exécuter      | Connectez-vous à la machine virtuelle à l’aide de X2Go. Démarrez un nouveau terminal et exécutez `java -jar /dsvm/tools/h2o/current/h2o.jar`. Ensuite, démarrez un navigateur web et connectez-vous à `http://localhost:54321`.      |
| Liens vers des exemples      | Des exemples sont disponibles sur la machine virtuelle dans Jupyter sous le répertoire `h2o`.      |
| Outils associés      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Il existe plusieurs autres bibliothèques de ML sur les machines virtuelles DSVM, comme le package `scikit-learn` fourni dans le cadre de la distribution Anaconda Python installée sur les machines virtuelles DSVM. Pour consulter la liste des packages disponibles dans Julia, Python et R, exécutez les gestionnaires de package correspondants.
