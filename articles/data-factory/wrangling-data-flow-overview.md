---
title: Flux de données de wrangling dans Azure Data Factory
description: Vue d’ensemble des flux de données de wrangling dans Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 7b46b1108246f0b83fcfce69844d19d01b1994c4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665648"
---
# <a name="what-are-wrangling-data-flows"></a>Que sont les flux de données de wrangling ?

Les organisations doivent procéder à la préparation et au wrangling des données pour une analyse précise des données complexes qui continuent à croître quotidiennement. La préparation des données est nécessaire pour que les organisations puissent utiliser les données dans différents processus d’entreprise et accélérer la rentabilité.

Les flux de données de wrangling dans Azure Data Factory vous permettent d’effectuer une préparation de données sans code à l’échelle du cloud de manière itérative. Les flux de données de wrangling s’intègrent à [Power Query Online](https://docs.microsoft.com/power-query/) et rendent les fonctions Power Query M disponibles pour les utilisateurs de Data Factory.

Le flux de données de wrangling convertit le M généré par l’éditeur mashup Power Query Online en code Spark pour l’exécution à l’échelle du cloud.

Les flux de données de wrangling sont particulièrement utiles pour les ingénieurs de données ou les « intégrateurs de données citoyen ».

## <a name="use-cases"></a>Cas d'utilisation

### <a name="fast-interactive-data-exploration-and-preparation"></a>Exploration et préparation rapide des données interactives

Plusieurs ingénieurs de données et intégrateurs de données citoyen peuvent explorer et préparer de manière interactive les jeux de données à l’échelle du cloud. Avec l’augmentation du volume, de la variété et de la vélocité des données dans les lacs de données, les utilisateurs ont besoin d’un moyen efficace pour explorer et préparer les jeux de données. Par exemple, vous pourriez avoir à créer un jeu de données qui contient toutes les informations démographiques des nouveaux clients depuis 2017. Vous ne mappez pas sur une cible connue. Vous explorez, effectuez le wrangling et préparez les jeux de données pour répondre à une exigence avant de les publier dans le lac. Les flux de données de wrangling sont souvent utilisés dans des scénarios analytiques moins formels. Les jeux de données préparés peuvent être utilisés pour exécuter des transformations et des opérations d’apprentissage automatique en aval.

### <a name="code-free-agile-data-preparation"></a>Préparation agile des données sans code

Les intégrateurs de données citoyen consacrent plus de 60 % de leur temps à la recherche et à la préparation des données. Ils cherchent à le faire sans code pour améliorer la productivité opérationnelle. Permettre aux intégrateurs de données citoyen d’enrichir, de mettre en forme et de publier des données à l’aide d’outils connus comme Power Query Online de façon évolutive améliore considérablement leur productivité. Un flux de données de wrangling dans Azure Data Factory permet à l’éditeur mashup Power Query Online de donner les moyens aux intégrateurs de données citoyen de corriger les erreurs rapidement, de normaliser les données et de produire des données de haute qualité pour appuyer les décisions commerciales.

### <a name="data-validation"></a>Validation des données

Analysez visuellement vos données sans code pour supprimer les valeurs hors norme et les anomalies, et les rendre aptes à une analyse rapide.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer un flux de données de wrangling](wrangling-data-flow-tutorial.md).