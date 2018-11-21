---
title: Optimiser les délais des requêtes dans Azure Database pour serveur PostgreSQL à l’aide de la stratégie de stockage de table toast
description: Cet article décrit comment optimiser les délais des requêtes en utilisant la stratégie de stockage de table toast dans une base de données Azure pour un serveur PostgreSQL.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: dee8aaaef4b1998a7234a88d07ad5efbc79d050b
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628439"
---
# <a name="optimizing-query-time-with-toast-table-storage-strategy"></a>Optimisation des délais des requêtes en utilisant la stratégie de stockage de table TOAST 
Cet article décrit comment optimiser les délais des requêtes en utilisant la stratégie de stockage de table TOAST.

## <a name="toast-table-storage-strategies"></a>Stratégies de stockage de table TOAST
Il existe quatre stratégies de stockage des colonnes toast sur un disque. Elles reposent sur différentes méthodes allant de la compression au stockage hors ligne. La stratégie peut être définie au niveau du type de données et au niveau des colonnes.
- **Normale** empêche la compression ou le stockage hors ligne ; en outre, elle désactive l’utilisation d’en-têtes codées sur octet pour les types varlena. **Normale** est la seule stratégie possible pour les colonnes de types de données non toast.
- **Étendue** permet la compression et le stockage hors-ligne. **Étendue** est la valeur par défaut pour la plupart des types de données toast. La compression sera tentée avant le contenu hors ligne si la ligne est trop importante.
- **Externe** permet le stockage hors ligne, mais pas la compression. L’utilisation de la stratégie **Externe** accélère les opérations de sous-chaîne sur les colonnes de texte et de bytea larges, mais augmente l’espace de stockage, car ces opérations sont optimisées pour récupérer uniquement les parties requises de la valeur hors-ligne lorsqu’elle n'est pas comprimée.
- **Principale** permet la compression, mais pas le stockage hors-ligne. Le stockage hors-ligne se fera toujours pour ces colonnes, mais uniquement en dernier recours lorsqu’il n’existe aucun autre moyen pour rendre la ligne suffisamment petite pour tenir sur une page.

## <a name="using-toast-table-storage-strategies"></a>Utilisation des stratégies de stockage de table TOAST
Si vos requêtes accèdent aux types de données toast, nous vous recommandons d’utiliser la stratégie **Principale** au lieu de la stratégie par défaut **Étendue**, afin de réduire les délais des requêtes. **Principale** n’exclut pas le stockage hors-ligne. En revanche, si vos requêtes n’accèdent pas aux types de données toast, nous vous recommandons de conserver la stratégie **Étendue**. Ainsi, une plus grande partie des lignes de la table principale tiendront dans le cache partagé des tampons, améliorant ainsi les performances.

Si l’une de vos charges de travail utilise un schéma contenant de larges tableaux et un grand nombre de caractères, nous vous recommandons d’utiliser des tables TOAST PostgreSQL. Prenons un exemple de table client contenant plus de 350 colonnes dont plusieurs couvrent 255 caractères. Son délai de requête de référence est passé de 4 203 à 467 secondes, soit une réduction de 89 %, lorsque sa stratégie TOAST a été définie sur **Principale**.

## <a name="next-steps"></a>Étapes suivantes
Examinez les caractéristiques précédentes de votre charge de travail. 

Consultez la documentation suivante, consacrée à PostgreSQL : [Chapitre 68, Stockage physique de base de données](https://www.postgresql.org/docs/current/storage-toast.html) 