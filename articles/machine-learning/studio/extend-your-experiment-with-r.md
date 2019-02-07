---
title: Prolongez votre expérience avec R
titleSuffix: Azure Machine Learning Studio
description: Comment étendre les fonctionnalités d'Azure Machine Learning Studio en utilisant le langage R et le module Exécuter le script R.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 98f5922cc329aedc8deeffbcb042bddf90405883
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497151"
---
# <a name="azure-machine-learning-studio-extend-your-experiment-with-r"></a>Azure Machine Learning Studio : Prolongez votre expérience avec R 
Comment étendre les fonctionnalités d’Azure Machine Learning Studio en utilisant le langage R et le module [Exécuter le script R][execute-r-script].

Ce module accepte plusieurs jeux de données d’entrée et génère un jeu de données unique en sortie. Vous pouvez taper un script R dans le paramètre **Script R** du module [Exécuter le script R][execute-r-script].

Pour accéder à chaque port d'entrée du module, utilisez un code semblable au suivant :

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Liste de tous les packages actuellement installés
La liste des packages installés peut changer. Vous trouverez une liste des packages actuellement installés dans [R Packages Supported by Azure Machine Learning Studio](https://msdn.microsoft.com/library/azure/mt741980.aspx) (Packages R pris en charge par Azure Machine Learning Studio).

Vous pouvez également obtenir la liste complète et à jour des packages installés en entrant le code suivant dans le module [Exécuter le Script R][execute-r-script] :

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

La liste des packages est envoyée au port de sortie du module [Exécuter le Script R][execute-r-script].
Pour afficher la liste des packages, connectez un module de conversion comme [Convertir en CSV][convert-to-csv] à la sortie gauche du module [Exécuter le script R][execute-r-script] puis exécutez le test. Cliquez ensuite sur la sortie du module de conversion et sélectionnez **Télécharger**. 

![Télécharger la sortie du module « Convertir en CSV »](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importation de packages
Vous pouvez importer des packages qui ne sont pas encore installés en utilisant les commandes suivantes dans le module [Exécuter le Script R][execute-r-script] :

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

où le fichier `my_favorite_package.zip` contient votre package.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
