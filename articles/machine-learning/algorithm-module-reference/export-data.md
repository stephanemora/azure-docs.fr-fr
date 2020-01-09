---
title: 'Exporter des données : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Exporter les données dans Azure Machine Learning pour enregistrer les résultats, les données intermédiaires et les données de travail de vos pipelines dans les destinations de stockage cloud en dehors d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 9544d086eb9535af779bf2febe0cc63c180f7fd3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529590"
---
# <a name="export-data-module"></a>Module Exporter les données

Cet article décrit un module dans le concepteur Azure Machine Learning (version préliminaire).

Utilisez ce module pour enregistrer les résultats, les données intermédiaires et les données de travail de vos pipelines dans les destinations de stockage cloud en dehors d’Azure Machine Learning. 

Ce module prend en charge l’exportation de vos données dans les services de données cloud suivants :

- Conteneur d’objets blob Azure
- Partage de fichiers Azure
- Azure Data Lake
- Azure Data Lake Gen2

Avant d’exporter vos données, vous devez d’abord inscrire un magasin de données dans votre espace de travail Azure Machine Learning. Pour plus d’informations, consultez la page [Accès aux données](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Configuration de l’exportation des données

1. Ajoutez le module **Exporter des données** à votre pipeline dans le concepteur. Ce module figure dans la catégorie **Entrée et sortie**.

1. Connectez **Exporter des données** au module qui contient les données à exporter.

1. Sélectionnez **Exporter les données** pour ouvrir le volet **Propriétés**.

1. Pour **Magasin de données**, sélectionnez un magasin de données existant dans la liste déroulante. Vous pouvez également créer un magasin de données. Découvrez comment faire sur la page [how-to-access-data](../how-to-access-data.md)

1. Définissez le chemin d’accès du magasin de données vers lequel écrire les données. 


1. Pour **Format de fichier**, sélectionnez le format dans lequel les données doivent être stockées.
 
1. Exécuter le pipeline.

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 