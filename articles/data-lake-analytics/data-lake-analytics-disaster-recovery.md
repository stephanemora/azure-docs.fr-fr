---
title: Conseils sur la récupération d’urgence pour Azure Data Lake Analytique
description: Découvrez comment planifier la récupération d’urgence pour vos comptes Azure Data Lake Analytique.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ea1d4020aa9be23b4839690ae0b386d35bce8a23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498888"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Conseils sur la récupération d’urgence pour Azure Data Lake Analytique

Azure Data Lake Analytics est un service de travaux d’analyse à la demande, qui simplifie les Big Data. Au lieu de déployer, de configurer et d’optimiser le matériel, vous écrivez des requêtes pour transformer vos données et en extraire des informations pertinentes. Le service d’analyse peut traiter les travaux instantanément, quelle qu’en soit l’échelle, en définissant le compteur sur la puissance requise. Vous payez les travaux uniquement lorsque ceux-ci sont exécutés, ce qui rend le service économique. Cet article fournit des conseils sur la façon de protéger vos travaux contre les rares pannes au niveau régional ou suppressions accidentelles.

## <a name="disaster-recovery-guidance"></a>Guide de récupération d’urgence

Lorsque vous utilisez Azure Data Lake Analytique, il est essentiel pour que vous prépariez votre propre plan de récupération d’urgence. Cet article vous guide pour créer un plan de récupération d’urgence. Il existe des ressources supplémentaires qui peuvent vous aider à créer votre propre plan :
- [Défaillances et reprise d’activité après sinistre pour les applications Azure](/azure/architecture/reliability/disaster-recovery)
- [Guide technique de la résilience Azure](/azure/architecture/reliability)

## <a name="best-practices-and-scenario-guidance"></a>Meilleures pratiques et conseils de scénario

Vous pouvez exécuter un travail U-SQL périodique dans un compte ADLA dans une région qui lit et écrit des tables de U-SQL, ainsi que des données non structurées.  Préparer un incident en suivant ces étapes :

1. Créer des comptes ADLA et ADLS dans la région secondaire qui sera utilisée en cas de panne.

   > [!NOTE]
   > Étant donné que les noms de compte sont globalement uniques, utiliser un schéma d’affectation de noms cohérent qui indique quel compte est secondaire.

2. Pour les données non structurées, référence [conseils sur la récupération d’urgence pour les données dans Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Pour les données structurées stockées dans les bases de données et les tables ADLA, créer des copies des artefacts de métadonnées telles que les bases de données, des tables, des fonctions table et des assemblys. Vous devez resynchroniser régulièrement ces artefacts lorsque des modifications se produisent en production. Par exemple, les nouvelles données doit être répliquées vers la région secondaire à copier les données et d’insertion dans la table secondaire.

   > [!NOTE]
   > Ces noms d’objets sont limitées au compte secondaire et ne sont pas globalement uniques, donc ils peuvent avoir les mêmes noms que ceux du compte de production principal.

En cas de panne, vous devez mettre à jour vos scripts afin que les chemins d’accès d’entrée pointent sur le point de terminaison secondaire. Les utilisateurs faire leur travail au compte ADLA dans la région secondaire. La sortie du travail sera puis être écrite dans le compte ADLA et ADLS dans la région secondaire.

## <a name="next-steps"></a>Étapes suivantes

[Conseils sur la récupération d’urgence pour les données dans Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
