---
title: Usages courants de l’analyse des coûts dans Azure Cost Management
description: Cet article explique comment obtenir des résultats des tâches courantes d’analyse des coûts dans Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: ''
ms.openlocfilehash: 756e2f275a92a31a99604aaf7ef880bfdce8149b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75984585"
---
# <a name="common-cost-analysis-uses"></a>Usages courants de l’analyse des coûts

Souvent, les utilisateurs d’Azure Cost Management souhaitent obtenir des réponses aux questions que beaucoup d’autres se posent. Cet article vous explique, pas à pas, comment obtenir des résultats des tâches courantes d’analyse des coûts dans Cost Management.

## <a name="view-cost-breakdown-by-azure-service"></a>Voir la décomposition des coûts par service Azure

La visualisation des coûts par service Azure peut vous aider à mieux comprendre les parties les plus coûteuses de votre infrastructure. Par exemple, si les coûts de calcul des machines virtuelles peuvent être faibles, vous pouvez cumuler des coûts de réseau importants en raison de la quantité d’informations qu’elles émettent. Il est essentiel de comprendre les principaux facteurs de coût de vos services Azure. Vous pourrez ainsi ajuster l’utilisation des services selon vos besoins.

1. Dans le portail Azure, accédez à l’analyse des coûts pour votre étendue. Par exemple :  **Gestion des coûts + facturation** > **Gestion des coûts** > **Analyse du coût**.
1. Sélectionnez **Coût par service**, puis, groupez les coûts selon le critère **Niveau de service**.
1. Choisissez la vue **Tableau**.

![Décomposition des coûts par service Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Voir la décomposition des coûts par ressource Azure

Vos services s’articulent autour de ressources Azure. L’examen des coûts en fonction des ressources vous permet d’identifier rapidement les principaux facteurs de coût. Si un service comprend des ressources trop coûteuses, envisagez des modifications pour réduire vos coûts.

1. Dans le portail Azure, accédez à l’analyse des coûts pour votre étendue. Par exemple :  **Gestion des coûts + facturation** > **Gestion des coûts** > **Analyse du coût**.
1. Sélectionnez **Coût par ressource**.
1. Choisissez la vue **Tableau**.

![Voir la décomposition des coûts par ressource Azure](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Voir la décomposition des coûts selon certaines dimensions

Les dimensions vous permettent d’organiser les coûts en fonction de différentes valeurs de métadonnées apparaissant dans vos frais. Par exemple, vous pouvez grouper vos coûts par emplacement.

1. Dans le portail Azure, accédez à l’analyse des coûts pour votre étendue. Par exemple :  **Gestion des coûts + facturation** > **Gestion des coûts** > **Analyse du coût**.
1. Sélectionnez le filtre **Grouper par**.  
    ![Sélectionner un critère de groupement](./media/cost-analysis-common-uses/group-by.png)
1. Vous pouvez enregistrer la vue pour une utilisation ultérieure si vous le souhaitez.
1. Cliquez sur un graphique à secteurs sous le graphe pour afficher des données plus détaillées.  
    ![Voir la décomposition des coûts selon certaines dimensions](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Voir les coûts par jour ou par mois

La consultation des coûts par jour et par mois peut vous aider à mieux comprendre si vos coûts sont plus élevés à certaines périodes de la semaine ou de l’année. Un trafic client plus élevé sur une période de vacances entraîne-t-il une augmentation de vos coûts Azure ? Les coûts sont-ils plus élevés le vendredi que le lundi ?

1. Dans le portail Azure, accédez à l’analyse des coûts pour votre étendue. Par exemple :  **Gestion des coûts + facturation** > **Gestion des coûts** > **Analyse du coût**.
1. Sous **Granularité**, sélectionnez **Mensuelle** ou **Quotidienne**.

![Voir les coûts par jour](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Voir les coûts pour une étiquette spécifique

De nombreux utilisateurs d’Azure appliquent des étiquettes à leurs ressources pour mieux catégoriser les frais. Il peut s’agir, par exemple, d’un centre de coûts ou d’un environnement de développement (de production ou de test). Les étiquettes apparaissent en tant que dimension dans l’analyse des coûts. Vous pouvez utiliser la dimension pour obtenir des insights sur vos catégories personnalisées de ressources étiquetées.

La prise en charge des étiquettes s’applique à l’utilisation signalée *après* l’application de l’étiquette à la ressource. Les étiquettes ne sont pas appliquées rétroactivement pour les cumuls de coûts.

1. Dans le portail Azure, accédez à l’analyse des coûts pour votre étendue. Par exemple :  **Gestion des coûts + facturation** > **Gestion des coûts** > **Analyse du coût**.
1. Sélectionnez l’option d’étiquette sous **Grouper par**.

![Voir les coûts pour une étiquette spécifique](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Télécharger vos détails d’utilisation

Le fichier de rapport des détails d’utilisation, au format CSV, fournit une décomposition de l’ensemble des frais cumulés sur une facture. Vous pouvez comparer la facture avec le rapport et ainsi mieux la comprendre. Tous les frais facturés correspondent à des frais décomposés dans le rapport d’utilisation.

1. Dans le portail Azure, accédez à l’onglet **Utilisation et frais** pour un abonnement ou un compte de facturation. Par exemple :  **Gestion des coûts + facturation** > **Facturation** > **Utilisation + frais**.
1. Sélectionnez la ligne pour laquelle vous souhaitez télécharger les informations, puis cliquez sur le symbole de téléchargement.  
    ![Télécharger les données sur l’utilisation et les frais](./media/cost-analysis-common-uses/download1.png)
1.  Sélectionnez le fichier d’utilisation à télécharger.  
    ![Choisir un fichier d’utilisation à télécharger](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Voir la décomposition des coûts d’EA

Votre inscription EA entraîne des coûts pour l’ensemble de votre organisation. Comprendre la façon dont ces coûts s’accumulent et sont facturés au fil du temps vous aide à impliquer les parties prenantes concernées. Vous pouvez ainsi vous assurer que les coûts sont gérés de manière responsable.

1. Dans le portail Azure, accédez à **Gestion des coûts + facturation** > **Vue d’ensemble**.
1. Cliquez sur **Décomposition** pour le mois en cours afin de visualiser la ventilation de votre engagement financier.  
    ![Vue d’ensemble des coûts d’EA - décomposition récapitulative](./media/cost-analysis-common-uses/breakdown1.png)
1.  Cliquez sur l’onglet **Utilisation et frais** pour voir la décomposition pour le mois précédent sur la période choisie.  
    ![Onglet Utilisation et frais](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Voir le coût mensuel de l’inscription par période

Utilisez une vue graphique des coûts mensuels de votre inscription pour comprendre les tendances de coût et les montants facturés sur une période donnée.

1. Dans le portail Azure, accédez à l’analyse des coûts pour votre étendue. Par exemple :  **Gestion des coûts + facturation** > **Gestion des coûts** > **Analyse du coût**.
1. Sélectionnez votre inscription et définissez la période d’inscription.
1. Choisissez une granularité mensuelle, puis définissez la vue sur **Histogramme (empilé)** .

Vous pouvez grouper et filtrer vos données pour obtenir une analyse plus détaillée.

![Coût mensuel de l’inscription par période](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Voir les coûts cumulés de l’inscription EA

Pour comprendre les dépenses globales de votre organisation sur une période donnée, vous pouvez visualiser les frais nets cumulés au fil du temps.

1. Dans le portail Azure, accédez à l’analyse des coûts pour votre étendue. Par exemple :  **Gestion des coûts + facturation** > **Gestion des coûts** > **Analyse du coût**.
1. Sélectionnez votre inscription pour voir les coûts cumulés à ce jour.

![Coûts cumulés de l’inscription](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Étapes suivantes
- Si vous n’avez pas encore effectué le premier guide de démarrage rapide relatif à Cost Management, lisez-le à partir de [Démarrer l’analyse des coûts](quick-acm-cost-analysis.md).
- Lisez la [documentation sur Azure Cost Management](../index.yml).
