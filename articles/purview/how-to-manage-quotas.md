---
title: Gérer les ressources et les quotas
titleSuffix: Azure Purview
description: Découvrez les quotas et les limites de ressources pour Azure Machine Learning et apprenez à demander des augmentations de quota.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 422c096533e8b470446a29b6d45f9127d963f913
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122563933"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Gestion et augmentation des quotas de ressources avec Azure Purview
 
Azure Purview est un service cloud destiné aux utilisateurs de données. Il offre la possibilité de gérer de manière centralisée la gouvernance des données sur l’ensemble du patrimoine de données, comprenant à la fois les environnements cloud et les environnements locaux. Il permet aux analystes d’entreprise de rechercher des données pertinentes en utilisant des termes métier significatifs. Pour augmenter les limites jusqu'à la limite maximale pour votre abonnement, contactez le support.
 
## <a name="azure-purview-limits"></a>Limites d’Azure Purview
 
|**Ressource**|  **Limite par défaut**  |**Limite maximale**|
|---|---|---|
|Comptes Purview par région, par locataire (tous abonnements confondus)|3|Contacter le support technique|
|vCore disponibles pour l’analyse, par compte*|160|160|
|Analyses simultanées, par compte à un moment donné (limite établie en fonction du type des sources de données analysées*)|5 | 10 |
|Durée maximale d’exécution d’une analyse|7 jours|7 jours|
|[Unité de capacité de la carte de données (CU)](concept-elastic-data-map.md) |1 CU (débit de 25 opérations/seconde et 2 Go de stockage des métadonnées) | 100 CU (contactez le support technique pour un nombre de CU plus élevé)|
|Débit des opérations de mappage de données |25 opérations/seconde pour chaque unité de capacité | 2 500 opérations/s pour 100 CU (contactez le support technique pour obtenir plus de débit)| 
|Stockage du mappage de données |2 Go pour chaque unité de capacité | 200 Go pour 100 CU (contactez le support technique pour obtenir plus de stockage) |
|Fenêtre d’élasticité du mappage de données | 1-8 CU (le mappage de données peut être mis à l’échelle automatiquement en fonction du débit dans la fenêtre d’élasticité) | Contactez le support pour accéder à la fenêtre d’élasticité supérieure |
|Taille des ressources par compte|100 millions de ressources physiques |Contacter le support technique|
|Taille maximale d’une ressource dans un catalogue|2 Mo|2 Mo|
|Longueur maximale d’un nom de ressource et d’un nom de classification|4 Ko|4 Ko|
|Longueur maximale du nom et de la valeur d’une propriété de ressource|32 Ko|32 Ko|
|Longueur maximale du nom et de la valeur d’un attribut de classification|32 Ko|32 Ko|
|Nombre maximal de termes de glossaire, par compte|100K|100K|
 
\* Les scénarios de runtime d’intégration auto-hébergés ne sont pas concernés par les limites définies dans le tableau ci-dessus. 
 
## <a name="next-steps"></a>Étapes suivantes
 
> [!div class="nextstepaction"]
>[Concept : carte de données élastique dans Azure Purview](concept-elastic-data-map.md)

> [!div class="nextstepaction"]
>[Tutoriel : Analyse des données avec Azure Purview](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Tutoriel : Parcourir la page d’accueil et rechercher une ressource](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Tutoriel : Recherche de ressources et affichage de leur traçabilité](tutorial-browse-and-view-lineage.md)
