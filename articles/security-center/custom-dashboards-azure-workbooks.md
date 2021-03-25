---
title: Galerie de classeurs dans Azure Security Center
description: Découvrez comment créer des rapports riches et interactifs de données de votre Azure Security Center avec la galerie Classeurs Azure Monitor intégrée
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 198702f619e490e8000e4430aab23a7f6bfb6d85
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107062"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Créer des rapports riches et interactifs de données de Security Center

Les [Classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md) fournissent un canevas flexible pour l’analyse des données et la création de rapports visuels riches au sein du portail Azure. Ils vous permettent d’exploiter plusieurs sources de données à travers l’écosystème Azure et de les combiner dans des expériences interactives unifiées.

Les Classeurs fournissent un ensemble riche de fonctionnalités pour la visualisation de vos données Azure. Pour obtenir des exemples détaillés de chaque type de visualisation, consultez les [exemples de visualisations et la documentation](../azure-monitor/visualize/workbooks-text-visualizations.md). 

Dans Azure Security Center, vous pouvez accéder aux rapports intégrés pour suivre la posture de sécurité de votre organisation. Vous pouvez également créer des rapports personnalisés pour afficher un vaste éventail de données à partir de Security Center ou d’autres sources de données prises en charge.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Rapport sur le degré de sécurisation dans le temps":::

## <a name="availability"></a>Disponibilité

| Aspect                          | Détails                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| État de sortie :                  | PRÉVERSION<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                       |
| Prix :                        | Gratuit                                                                                                                                         |
| Rôles et autorisations obligatoires : | Pour enregistrer des classeurs, vous devez disposer au minimum d’autorisations de Contributeur de classeur sur le groupe de ressources cible                                      |
| Clouds :                         | ![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Oui](./media/icons/yes-icon.png) National/souverain (US Gov, Chine Gov, autres Gov) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Galerie de classeurs dans Azure Security Center

Avec la fonctionnalité Classeurs Azure intégrée, Azure Security Center facilite la création de vos propres rapports interactifs personnalisés. Security Center inclut également une bibliothèque de classeurs avec les rapports suivants prêts pour personnalisation :

- **Degré de sécurisation dans le temps** : suivez les scores de vos abonnements et les modifications apportées aux recommandations pour vos ressources
- **Mises à jour du système** : visualisez les mises à jour système manquantes par ressources, système d’exploitation, gravité, etc.
- **Résultats de l’évaluation des vulnérabilités** : visualisez les découvertes des analyses de vulnérabilités de vos ressources Azure

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Galerie de classeurs intégrés dans Azure Security Center":::

Choisissez l’un des rapports fournis ou créez le vôtre.

> [!TIP]
> Utilisez le bouton **Modifier** pour personnaliser les rapports fournis à votre convenance. Lorsque vous avez fini d’apporter des modifications, sélectionnez **Enregistrer** pour enregistrer vos modifications dans un nouveau classeur.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="Modification des classeurs fournis pour les personnaliser en fonction de vos besoins particuliers":::

### <a name="use-the-secure-score-over-time-report"></a>Utiliser le rapport « Degré de sécurisation dans le temps »

Ce rapport utilise les données de degré de sécurisation de votre espace de travail Log Analytics. Ces données doivent être exportées à partir de l’outil d’exportation continue, comme décrit dans [Configurer l’exportation continue à partir des pages du Security Center dans le portail Azure](continuous-export.md?tabs=azure-portal).

Quand vous configurez l’exportation continue, définissez la fréquence d’exportation sur **diffusion en continu des mises à jour** et **captures instantanées**.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="Pour le classeur de degré de sécurisation dans le temps, vous devez sélectionner les deux options dans les paramètres de fréquence d’exportation de votre configuration d’exportation continue.":::

> [!NOTE]
> Les captures instantanées étant exportées chaque semaine, vous devez attendre au moins une semaine que la première capture instantanée soit exportée avant de pouvoir consulter les données de ce rapport.

> [!TIP]
> Pour configurer l’exportation continue au sein de votre organisation, utilisez les stratégies « DeployIfNotExist » d’Azure Policy fournies décrites dans [Configurer l’exportation continue à grande échelle](continuous-export.md?tabs=azure-policy).

Le rapport du degré de sécurisation dans le temps comporte cinq graphiques pour la création de rapports sur les abonnements pour les espaces de travail sélectionnés :


|Graph  |Exemple  |
|---------|---------|
|**Évaluer les tendances de la dernière semaine et du mois**<br>Utilisez cette section pour surveiller le score actuel et les tendances générales des scores de vos abonnements.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="Tendances du degré de sécurisation sur le rapport intégré":::|
|**Score agrégé pour tous les abonnements sélectionnés**<br>Pointez votre souris sur un point quelconque de la courbe de tendance afin de voir le score agrégé à n’importe quelle date dans l’intervalle de temps sélectionné.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Score agrégé pour tous les abonnements sélectionnés":::|
|**Recommandations avec le plus de ressources non saines**<br>Ce tableau vous aide à trier les recommandations qui comptent le plus de ressources devenues non saines au cours de la période sélectionnée.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="Recommandations avec le plus de ressources non saines":::|
|**Scores pour des contrôles de sécurité spécifiques**<br>Les contrôles de sécurité du Security Center sont des regroupements logiques de recommandations. Ce graphique vous permet de voir en un clin d’œil les scores hebdomadaires de tous vos contrôles.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="Scores pour vos contrôles de sécurité sur la période sélectionnée":::|
|**Modifications de ressources**<br>Les recommandations comptant le plus de ressources ayant changé d’état (sain, non sain ou non applicable) pendant la période sélectionnée sont répertoriées ici. Sélectionnez une recommandation dans la liste pour ouvrir une nouvelle table répertoriant les ressources spécifiques.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="Recommandations comptant le plus de ressources ayant changé d’état d’intégrité":::|

### <a name="use-the-system-updates-report"></a>Utiliser le rapport « Mises à jour système »

Ce rapport est basé sur la recommandation de sécurité « Les mises à jour système doivent être installées sur vos machines ».

Le rapport vous aide à identifier les machines avec des mises à jour en suspens.

Vous pouvez afficher la situation des abonnements sélectionnés en fonction des éléments suivants :

- Liste des ressources avec des mises à jour en suspens
- Liste des mises à jour manquantes dans vos ressources

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="Rapport sur les mises à jour système du Security Center, basé sur la recommandation de sécurité des mises à jour manquantes":::

### <a name="use-the-vulnerability-assessment-findings-report"></a>Utiliser le rapport « Résultats de l’évaluation des vulnérabilités »

Le Security Center inclut des scanneurs de vulnérabilité pour vos machines, des conteneurs dans des registres de conteneurs, et des serveurs SQL.

Apprenez-en davantage sur l’utilisation de ces scanneurs :

- [Analyser vos machines avec le scanneur d’évaluation des vulnérabilités intégré](deploy-vulnerability-assessment-vm.md)
- [Analyser les images de votre registre à la recherche de vulnérabilités](defender-for-container-registries-usage.md)
- [Analyser vos ressources SQL à la recherche de vulnérabilités](defender-for-sql-on-machines-vulnerability-assessment.md)

Les résultats de chacun de ces scanneurs sont signalés dans des recommandations distinctes :

- Les vulnérabilités de vos machines virtuelles doivent être corrigées
- Les vulnérabilités dans les images Azure Container Registry doivent être corrigées (avec Qualys)
- Les résultats de l’évaluation des vulnérabilités sur vos bases de données SQL doivent être corrigés
- Les résultats de l’évaluation des vulnérabilités sur vos serveurs SQL sur des machines doivent être corrigés

Ce rapport rassemble ces résultats et les organise par gravité, type de ressource et catégorie.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Rapport des résultats de l’évaluation des vulnérabilités de Security Center":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Importer des classeurs à partir d’autres galeries de classeurs

Si vous avez créé des classeurs dans d’autres services Azure et souhaitez les déplacer vers la galerie de classeurs de votre Azure Security Center :

1. Ouvrez le classeur cible.

1. Dans la barre d’outils, sélectionnez **Modifier**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Modification d’un classeur Azure Monitor":::

1. À partir de la barre d’outils, sélectionnez **</>** pour accéder à l’Éditeur avancé.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="Lancement de l’éditeur avancé pour obtenir le code JSON du modèle de galerie":::

1. Copiez le code JSON du modèle de galerie du classeur.

1. Ouvrez la galerie de classeurs dans Security Center, puis dans la barre de menus, sélectionnez **Nouveau**.
1. Sélectionnez **</>** pour accéder à l’Éditeur avancé.
1. Collez l’intégralité du code JSON du modèle de galerie.
1. Sélectionnez **Appliquer**.
1. Dans la barre d’outils, sélectionnez **Enregistrer sous**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Enregistrement du classeur dans la galerie du Security Center":::

1. Entrez les informations requises pour l’enregistrement du classeur :
   1. nom du classeur ;
   2. région souhaitée ;
   3. abonnement, groupe de ressources et partage appropriés.

Vous trouverez votre classeur enregistré dans la catégorie **Classeurs récemment modifiés** .


## <a name="next-steps"></a>Étapes suivantes

Cet article a décrit la page Classeurs Azure Monitor intégrée du Security Center avec des rapports intégrés et l’option permettant de créer vos propres rapports personnalisés et interactifs.

- En savoir plus sur les [Classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md)
- Les rapports intégrés extraient leurs données des recommandations du Security Center. Apprenez-en davantage sur les nombreuses recommandations de sécurité dans [Recommandations de sécurité – Guide de référence](recommendations-reference.md)