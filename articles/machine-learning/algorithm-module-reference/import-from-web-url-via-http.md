---
title: 'Importer à partir de l’URL Web via HTTP : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser l’importation à partir de l’URL Web via le module HTTP dans le service Azure Machine Learning pour lire les données à partir d’une page Web publique pour une utilisation dans une expérience d’apprentissage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f0847e9dd90267d985b75be3c3a07ce8fae98a9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029669"
---
# <a name="import-from-web-url-via-http-module"></a>Importer à partir de l’URL Web via le module HTTP

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour lire les données à partir d’une page Web publique pour une utilisation dans une expérience d’apprentissage.

Les restrictions suivantes s’appliquent aux données publiées sur une page web :

- Données doivent être l’un des formats pris en charge : CSV, TSV, ARFF ou SvmLight. Autres données génère des erreurs.
- Aucune authentification n’est requis ou pris en charge. Données doivent être disponibles publiquement. 

Il existe deux façons d’obtenir des données : utilisez l’Assistant pour configurer la source de données, ou configurer manuellement.

## <a name="use-the-data-import-wizard"></a>Utilisez l’Assistant Importation de données

1. Ajouter le **importer des données** module à votre expérience. Vous pouvez rechercher le module dans l’interface, dans le **données entrée et sortie** catégorie.

2. Cliquez sur **lancer Assistant Importer des données** et sélectionnez l’URL Web via HTTP.

3. Collez l’URL, puis sélectionnez un format de données.

4. Lorsque la configuration est terminée.

Pour modifier une connexion de données existante, démarrez à nouveau l’Assistant. L’Assistant charge tous les détails de la configuration précédente, afin que vous n’êtes pas obligé de redémarrer à partir de zéro

## <a name="manually-set-properties-in-the-import-data-module"></a>Définir manuellement les propriétés dans le module importer des données

Les étapes suivantes décrivent comment configurer manuellement la source d’importation.

1. Ajouter le [importer des données](import-data.md) module à votre expérience. Vous pouvez rechercher le module dans l’interface, dans le **données entrée et sortie** catégorie.

2. Pour **source de données**, sélectionnez **URL Web via HTTP**.

3. Pour **URL**, tapez ou collez l’URL complète de la page qui contient les données que vous souhaitez charger.

    L’URL doit inclure l’URL du site et le chemin d’accès complet, avec le nom de fichier et l’extension, à la page qui contient les données à charger.

    Par exemple, la page suivante contient le jeu de données Iris à partir du référentiel de l’Université de Californie, Irvine d’apprentissage :

    `http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. Pour **format de données**, sélectionnez des formats de données pris en charge dans la liste.

    Nous vous recommandons de toujours vérifier les données au préalable afin de déterminer le format. La page de l’UC Irvine utilise le format CSV. Autres formats de données pris en charge sont TSV, ARFF et SvmLight.

5. Si les données sont au format CSV ou TSV, utilisez le **fichier comporte une ligne d’en-tête** option pour indiquer si la source de données inclut une ligne d’en-tête. La ligne d’en-tête est utilisée pour attribuer des noms de colonne.

6. Sélectionnez le **utiliser mis en cache les résultats** options si vous ne pensez pas les données à beaucoup changé, ou si vous souhaitez éviter de recharger les données chaque fois que vous exécutez l’expérience.

    Lorsque cette option est sélectionnée, l’expérience charge l’heure de données la première, le module est exécuté et par la suite utilise une version mise en cache du jeu de données.

    Si vous souhaitez recharger le jeu de données sur chaque itération du jeu de données d’expérience, désélectionnez le **utiliser mis en cache les résultats** option. Résultats sont également rechargés s’il existe des modifications aux paramètres de [importer des données](import-data.md).

7. Exécutez l’expérience.

## <a name="results"></a>Résultats

Lorsque vous avez terminé, cliquez sur le jeu de données de sortie et sélectionnez **visualiser** pour voir si les données ont été importées avec succès.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 