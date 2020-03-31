---
title: Bonnes pratiques d’évaluation dans Azure Migrate Server Assessment
description: Conseils pour créer des évaluations avec Azure Migrate Server Assessment.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: de6953b6648613595bc9975b17941b3a453a6d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185977"
---
# <a name="best-practices-for-creating-assessments"></a>Meilleures pratiques pour la création d’évaluations

[Azure Migrate](migrate-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres d’ISV (fournisseurs de logiciels indépendants) tiers.

Cet article résume les meilleures pratiques lors de la création d’évaluations à l’aide de l’outil Azure Migrate Server Assessment.

## <a name="about-assessments"></a>À propos des évaluations

Les évaluations que vous créez avec Azure Migrate Server Assessment sont une capture instantanée de données à un moment donné. Il y a deux types d’évaluation dans d’Azure Migrate.

**Type d’évaluation** | **Détails** | **Données**
--- | --- | ---
**Basée sur les performances** | Évaluations qui donnent des recommandations en fonction des données de performances collectées | Les recommandations concernant les machines virtuelles sont fonction des données d’utilisation du processeur et de la mémoire.<br/><br/> Les recommandations concernant le type de disque (HDD/SSD ou disque managé premium) sont fonction de l’IOPS et du débit des disques locaux.
**Telle quelle locale** | Évaluations qui n’utilisent pas de données de performances pour formuler des recommandations. | Les recommandations concernant la taille des machines virtuelles sont basées sur la taille de la machines virtuelle locale.<br/><br> Le type de disque recommandé est basé sur ce que vous sélectionnez dans le paramètre type de stockage pour l’évaluation.

### <a name="example"></a>Exemple
Par exemple, si vous avez une machine virtuelle locale avec quatre cœurs utilisée à 20 % et une mémoire de 8 Go utilisée à 10 %, les évaluations seront les suivantes :

- **Évaluation basée sur les performances** :
    - Identifie les cœurs et la mémoire effectifs sur la base des cœurs (4 x 0,20 = 0,8) et de l’utilisation de la mémoire (8 Go x 0,10 = 0,8).
    - Applique le facteur de confort spécifié dans les propriétés d’évaluation (par exemple, 1.3x) pour obtenir les valeurs à utiliser pour le dimensionnement. 
    - Recommande la taille de machine virtuelle la plus proche dans Azure pouvant prendre en charge environ 1,04 cœur (0,8 x 1,3) et environ 1,04 Go (0,8 x 1,3) de mémoire.

- **Évaluation telle quelle (locale)**  :
    -  Recommande une machine virtuelle à quatre cœurs ; 8 Go de mémoire.

## <a name="best-practices-for-creating-assessments"></a>Meilleures pratiques pour la création d’évaluations

L’appliance Azure Migrate profile en permanence votre environnement local et envoie des métadonnées et des données de performances à Azure. Suivez ces meilleures pratiques pour évaluer les serveurs découverts à l’aide d’une appliance :

- **Créer des évaluations telles quelles** : Vous pouvez créer des évaluations telles quelles dès que vos machines s’affichent dans le portail Azure Migrate.
- **Créer une évaluation basée sur les performances** : Après la configuration de la détection, nous vous recommandons d’attendre au moins une journée avant d’exécuter une évaluation basée sur les performances :
    - La collecte des données de performances prend du temps. En attendant au moins une journée, vous pouvez être certain qu’il y a suffisamment de points de données de performances avant d’exécuter l’évaluation.
    - Lorsque vous exécutez des évaluations basées sur les performances, veillez à profiler votre environnement pour la durée de l’évaluation. Par exemple, si vous créez une évaluation avec une durée des performances définie sur une semaine, vous devez attendre au moins une semaine après le démarrage de la détection pour que tous les points de données soient collectés. Sinon, l’évaluation n’aura pas cinq étoiles.
- **Recalculer les évaluations** : Étant donné que les évaluations sont des instantanés à un moment donné, elles ne sont pas automatiquement mises à jour avec les données les plus récentes. Pour mettre à jour une évaluation avec les données les plus récentes, vous devez la recalculer.

Suivez ces meilleures pratiques pour évaluer les serveurs importés dans Azure Migrate via. le fichier .CSV :

- **Créer des évaluations telles quelles** : Vous pouvez créer des évaluations telles quelles dès que vos machines s’affichent dans le portail Azure Migrate.
- **Créer une évaluation basée sur les performances** : Cela permet d’obtenir une meilleure estimation des coûts, surtout si vous avez surapprovisionné la capacité du serveur localement. Toutefois, la précision de l’évaluation basée sur les performances dépend des données de performances que vous avez spécifiées pour les serveurs. 
- **Recalculer les évaluations** : Étant donné que les évaluations sont des instantanés à un moment donné, elles ne sont pas automatiquement mises à jour avec les données les plus récentes. Pour mettre à jour une évaluation avec les dernières données importées, vous devez la recalculer.

## <a name="best-practices-for-confidence-ratings"></a>Meilleures pratiques pour les niveaux de confiance

Quand vous effectuez des évaluations basées sur les performances, une note de confiance des évaluations allant de 1 étoile (le plus faible) à 5 étoiles (le plus élevé) est attribuée. Pour utiliser efficacement les notes de confiance des évaluations :
- Azure Migrate Server Assessment a besoin des données d’utilisation du processeur/de la mémoire de la machine virtuelle.
- Pour chaque disque attaché à la machine virtuelle locale, les données IOPS/de débit en lecture/écriture sont nécessaires.
- Pour chaque carte réseau attachée à la machine virtuelle, des données en entrée/sortie du réseau sont nécessaires.

Selon le pourcentage de points de données disponibles pour la durée sélectionnée, la note de confiance d’une évaluation est fournie comme le résume le tableau ci-dessous.

   **Disponibilité des points de données** | **Niveau de confiance**
   --- | ---
   0 %-20 % | 1 étoile
   21 %-40 % | 2 étoiles
   41 %-60 % | 3 étoiles
   61 %-80 % | 4 étoiles
   81 %-100 % | 5 étoiles


## <a name="common-assessment-issues"></a>Problèmes d'évaluation courants

Voici comment résoudre certains problèmes d’environnement courants qui affectent les évaluations.

###  <a name="out-of-sync-assessments"></a>Évaluations non synchronisées

Si vous ajoutez ou supprimez des machines d’un groupe après avoir créé une évaluation, l’évaluation que vous avez créée sera marquée comme étant **non synchronisée**. Réexécutez l’évaluation (**Recalculer**) pour refléter les modifications du groupe.

### <a name="outdated-assessments"></a>Évaluations obsolètes

En cas de modification locale des machines virtuelles appartenant à un groupe qui a été évalué, l’évaluation est marquée comme **obsolète**. Pour refléter les modifications, réexécutez l’évaluation.

### <a name="low-confidence-rating"></a>Faible niveau de confiance

Il peut arriver qu’une évaluation n’ait pas tous les points de données pour diverses raisons :

- Vous n’avez pas profilé votre environnement pour la durée pour laquelle vous créez l’évaluation. Par exemple, si vous créez une *évaluation basée sur les performances* avec une durée des performances définie sur une semaine, vous devez attendre au moins une semaine après le démarrage de la découverte pour que tous les points de données soient recueillis. Vous pouvez toujours cliquer sur **Recalculer** pour voir la note de confiance applicable la plus récente. Notez que la note de confiance est applicable uniquement quand vous créez une évaluation *basée sur les performances*.

- Plusieurs machines virtuelles ont été arrêtées pendant la période de calcul de l’évaluation. Si certaines machines virtuelles ont été mises hors tension pendant un certain temps, l’outil Évaluation de serveur ne peut pas recueillir les données de performances pour cette période.

- Peu de machines virtuelles ont été créées après que la découverte dans Évaluation de serveur a commencé. Par exemple, si vous créez une évaluation de l’historique des performances du mois dernier, mais si la création de quelques machines virtuelles dans l’environnement ne remonte qu’à une semaine. Dans ce cas, les données de performances pour les nouvelles machines virtuelles ne seront pas disponibles pour toute la durée et la note de confiance sera faible.


## <a name="next-steps"></a>Étapes suivantes

- [Découvrez](concepts-assessment-calculation.md) le mode de calcul des évaluations.
- [Découvrez](how-to-modify-assessment.md) comment personnaliser une évaluation.
