---
title: 'Importer à partir d’une URL web via HTTP : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Importer à partir d’une URL web via HTTP dans Azure Machine Learning service pour lire les données d’une page web publique à utiliser dans une expérience d’apprentissage automatique.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bff913efb38c9e5589c795386dfbbc480d799a37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411439"
---
# <a name="import-from-web-url-via-http-module"></a>Module Importer à partir d’une URL web via HTTP

Cet article décrit un module de l’interface visuelle (préversion) d’Azure Machine Learning service.

Utilisez ce module pour lire les données d’une page web publique et l’utiliser dans une expérience d’apprentissage automatique.

Les restrictions suivantes s’appliquent aux données publiées sur une page web :

- Les données doivent être dans les formats pris en charge suivants : CSV, TSV, ARFF ou SvmLight. Les autres données génèrent des erreurs.
- Aucune authentification n’est nécessaire ni prise en charge. Les données doivent être disponibles publiquement. 

Deux méthodes permettent d’obtenir des données : utilisez l’Assistant pour configurer la source de données ou configurez-la manuellement.

## <a name="use-the-data-import-wizard"></a>Utiliser l’Assistant Importation de données

1. Ajoutez le module **Importer des données** à votre expérience. Vous trouverez ce module dans la catégorie **Data Input and Output** (Entrée et sortie de données) de l’interface.

2. Cliquez sur **Launch Import Data Wizard** (Lancer l’Assistant Importation de données) et sélectionnez URL web via HTTP.

3. Collez l’URL, puis sélectionnez un format de données.

4. À l’issue de la configuration

Pour modifier une connexion de données existante, redémarrez l’Assistant. L’Assistant charge tous les détails de configuration précédents pour que vous n’ayez pas à recommencer à partir de zéro.

## <a name="manually-set-properties-in-the-import-data-module"></a>Définir manuellement les propriétés du module Importer des données

Les étapes suivantes décrivent la configuration manuelle de la source d’importation.

1. Ajoutez le module [Importer des données](import-data.md) à votre expérience. Vous trouverez ce module dans la catégorie **Data Input and Output** (Entrée et sortie de données) de l’interface.

2. Pour **Source de données**, sélectionnez **Web URL via HTTP** (URL web via HTTP).

3. Pour **URL**, tapez ou collez l’URL complète de la page qui contient les données à charger.

    L’URL doit inclure l’URL du site et le chemin d’accès complet de la page qui contient les données à charger avec l’extension et le nom de fichier.

    Par exemple, la page suivante contient le jeu de données Iris du référentiel de l’Université de Californie, Irvine, d’apprentissage automatique :

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. Pour **Format de données**, sélectionnez l’un des formats de données pris en charge dans la liste.

    Nous vous recommandons de toujours vérifier les données au préalable afin de déterminer le format. La page Irvine de l’Université de Californie utilise le format CSV. Les autres formats de données pris en charge sont les suivants : TSV, ARFF et SvmLight.

5. Si les données sont au format CSV ou TSV, utilisez l’option **Le fichier a une ligne d’en-tête**  pour indiquer si la source de données inclut une ligne d’en-tête. La ligne d’en-tête permet d’attribuer des noms de colonne.

6. Sélectionnez les options **Use cached results** (Utiliser les résultats mis en cache) si vous ne pensez pas que les données changeront beaucoup ou si vous souhaitez éviter de recharger les données à chaque exécution de l’expérience.

    Lorsque cette option est sélectionnée, l’expérience charge les données à la première exécution du module, et utilise par la suite une version mise en cache du jeu de données.

    Si vous souhaitez recharger le jeu de données sur chaque itération du jeu de données d’expérience, désélectionnez l’option **Use cached results** (Utiliser les résultats mis en cache). Les résultats sont également rechargés si des modifications sont apportées aux paramètres de l’option [Importer des données](import-data.md).

7. Exécutez l’expérience.

## <a name="results"></a>Résultats

À l’issue de l’opération, cliquez sur le jeu de données de sortie et sélectionnez **Visualiser** pour voir si les données ont bien été importées.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 