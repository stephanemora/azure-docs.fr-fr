---
title: Prolongez votre expérience avec R
titleSuffix: ML Studio (classic) - Azure
description: Comment étendre les fonctionnalités d’Azure Machine Learning Studio (classique) en utilisant le langage R et le module Exécuter le script R.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218032"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (classique) : Prolongez votre expérience avec R 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Vous pouvez étendre les fonctionnalités d’Azure Machine Learning Studio (classique) en utilisant le langage R et le module [Exécuter le script R][execute-r-script].

Ce module accepte plusieurs jeux de données d’entrée et génère un jeu de données unique en sortie. Vous pouvez taper un script R dans le paramètre **Script R** du module [Exécuter le script R][execute-r-script].

Pour accéder à chaque port d'entrée du module, utilisez un code semblable au suivant :

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Liste de tous les packages actuellement installés
La liste des packages installés peut changer. Vous trouverez une liste des packages actuellement installés dans [Packages R pris en charge par Azure Machine Learning Studio (classique)](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Vous pouvez également obtenir la liste complète et à jour des packages installés en entrant le code suivant dans le module [Exécuter le Script R][execute-r-script] :

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

La liste des packages est envoyée au port de sortie du module [Exécuter le Script R][execute-r-script].
Pour afficher la liste des packages, connectez un module de conversion comme [Convertir en CSV][convert-to-csv] à la sortie gauche du module [Exécuter le script R][execute-r-script] puis exécutez le test. Cliquez ensuite sur la sortie du module de conversion et sélectionnez **Télécharger**. 

![Télécharger la sortie du module « Convertir en CSV »](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importation de packages
Vous pouvez importer des packages qui ne sont pas encore installés en utilisant les commandes suivantes dans le module [Exécuter le Script R][execute-r-script] :

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

où le fichier `my_favorite_package.zip` contient votre package.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
