---
title: Visualiser les données avec les classeurs Azure Monitor dans Azure Sentinel | Microsoft Docs
description: Suivez ce tutoriel pour apprendre à visualiser vos données avec des classeurs dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2021
ms.author: yelevin
ms.openlocfilehash: c26d86c98c83d9762acb8a75bba8fe464cc2a58e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491463"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Tutoriel : Visualiser et superviser vos données

Après avoir [connecté vos sources de données](quickstart-onboard.md) à Azure Sentinel, vous pouvez visualiser et superviser les données grâce à l’adoption par Azure Sentinel de classeurs Azure Monitor, ce qui vous offre une grande souplesse pour créer des tableaux de bord personnalisés. Même si les classeurs s’affichent différemment dans Azure Sentinel, il peut être utile de savoir [créer des rapports interactifs avec les classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md). Azure Sentinel vous permet de créer des classeurs personnalisés sur l’ensemble de vos données. Il est également fourni avec des modèles de classeurs intégrés avec lesquels vous obtenez rapidement des insights sur vos données dès que vous connectez une source de données.

Ce tutoriel vous aide à visualiser vos données dans Azure Sentinel.
> [!div class="checklist"]
> * Utiliser des classeurs intégrés
> * Créer des classeurs

## <a name="prerequisites"></a>Prérequis

Vous devez disposer au minimum des autorisations de **lecteur de classeur** ou de **contributeur de classeur** sur le groupe de ressources de l’espace de travail Azure Sentinel.

> [!NOTE]
> Les classeurs que vous voyez dans Azure Sentinel sont enregistrés dans le groupe de ressources de l’espace de travail Azure Sentinel et sont étiquetés par l’espace de travail dans lequel ils ont été créés.

## <a name="use-built-in-workbooks"></a>Utiliser des classeurs intégrés

1. Accédez à **Classeurs**, puis sélectionnez **Modèles** pour afficher la liste complète des classeurs intégrés à Azure Sentinel. 

    Pour savoir quels classeurs correspondent aux types de vos données connectées, le champ **Types de données requis** dans chaque classeur indique le type de données à côté d’une coche verte si vous envoyez déjà des données de ce type vers Azure Sentinel.

    [ ![Accédez aux classeurs.](media/tutorial-monitor-data/access-workbooks.png) ](media/tutorial-monitor-data/access-workbooks.png#lightbox)

1. Sélectionnez **Afficher le modèle** pour voir le modèle rempli avec vos données.

1. Pour modifier le classeur, sélectionnez **Enregistrer**, puis sélectionnez l’emplacement où vous souhaitez enregistrer le fichier JSON du modèle.

   > [!NOTE]
   > Cette opération crée une ressource Azure basée sur le modèle associé et enregistre le fichier JSON du modèle sans les données.


1. Sélectionnez **Afficher le classeur enregistré**. 

    [ ![Affichez les classeurs.](media/tutorial-monitor-data/workbook-graph.png) ](media/tutorial-monitor-data/workbook-graph.png#lightbox)

    Sélectionnez le bouton **Modifier** de la barre d’outils du classeur pour personnaliser ce dernier en fonction de vos besoins. Lorsque vous avez terminé, sélectionnez **Enregistrer** pour enregistrer vos paramètres.

    Pour plus d’informations, consultez [Créer des rapports interactifs avec les classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md).

> [!TIP]
> Pour cloner votre classeur, sélectionnez **Modifier**, puis sélectionnez **Enregistrer sous**, en veillant à enregistrer le classeur sous un autre nom, dans le même abonnement et le même groupe de ressources.
> Les classeurs clonés sont affichés sous l’onglet **Mes classeurs**.
>
## <a name="create-new-workbook"></a>Créer un classeur

1. Accédez à **Classeurs**, puis sélectionnez **Ajouter un classeur** pour créer un classeur entièrement nouveau.

    [ ![Nouveau classeur.](media/tutorial-monitor-data/create-workbook.png) ](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. Pour modifier le classeur, sélectionnez **Modifier**, puis ajoutez du texte, des requêtes et des paramètres selon vos besoins. Pour plus d’informations sur la personnalisation du classeur, consultez [Créer des rapports interactifs avec les classeurs Azure Monitor](../azure-monitor/visualize/workbooks-overview.md). 

1. Quand vous créez une requête, assurez-vous que l’option **Source de données** est définie sur **Journaux** et **Type de ressource** sur **Log Analytics**, puis choisissez le ou les espaces de travail appropriés. 

1. Une fois que vous avez créé votre classeur, enregistrez-le dans l’abonnement et le groupe de ressources de votre espace de travail Azure Sentinel.

1. Si vous voulez autoriser d’autres personnes de votre organisation à utiliser le classeur, sous **Enregistrer dans**, sélectionnez **Rapports partagés**. Si vous souhaitez restreindre l’usage de ce classeur à vous-seul, sélectionnez **Mes rapports**.

1. Pour passer d’un classeur à un autre dans votre espace de travail, sélectionnez **Ouvrir** ![Icône d’ouverture d’un classeur.](./media/tutorial-monitor-data/switch.png) dans la barre d’outils de n’importe quel classeur. L’écran bascule vers une liste de classeurs vers lesquels vous pouvez basculer.

    Sélectionnez le classeur que vous souhaitez ouvrir :

    [ ![Basculez entre les classeurs.](media/tutorial-monitor-data/switch-workbooks.png) ](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>Actualiser les données de votre classeur

Actualisez votre classeur pour afficher les données mises à jour. Dans la barre d’outils, sélectionnez l’une des options suivantes :

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false":::  **Actualiser**, pour actualiser manuellement les données de votre classeur.

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false":::  **Actualisation automatique**, pour définir l’actualisation automatique de votre classeur à un intervalle configuré.

    - Les intervalles d’actualisation automatique s’échelonnent entre **5 minutes** et **1 jour**.

    - L’actualisation automatique est suspendue lorsque vous modifiez un classeur, et les intervalles sont redémarrés chaque fois que vous revenez en mode d’affichage à partir du mode d’édition.

    - Les intervalles d’actualisation automatique sont également redémarrés si vous actualisez manuellement vos données.

    > [!TIP]
    > Par défaut, l’actualisation automatique est désactivée. Pour optimiser les performances, l’actualisation automatique est également désactivée chaque fois que vous fermez un classeur, et elle ne s’exécute pas en arrière-plan. Activez l’actualisation automatique en fonction de vos besoins la prochaine fois que vous ouvrirez le classeur.
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>Imprimer un classeur ou enregistrer au format PDF

Pour imprimer un classeur ou l’enregistrer au format PDF, utilisez le menu Options à droite du titre du classeur.

1. Sélectionnez Options > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Imprimer le contenu**. 
2. Dans l’écran d’impression, ajustez vos paramètres d’impression si nécessaire ou sélectionnez **Enregistrer au format PDF** pour l’enregistrer localement.

Exemple :

[ ![Imprimez votre classeur ou enregistrez-le au format PDF.](media/whats-new/print-workbook.png) ](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>Comment supprimer des classeurs

Pour supprimer un classeur enregistré (soit un modèle enregistré ou un classeur personnalisé), dans la page Classeurs, sélectionnez le classeur enregistré à supprimer, puis sélectionnez **Supprimer**. Cette opération supprime le classeur enregistré.

> [!NOTE]
> Elle supprime le classeur, mais aussi toutes les modifications que vous avez apportées au modèle. Le modèle d’origine reste disponible.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à visualiser vos données dans Azure Sentinel à l’aide de Workbooks Azure.

Pour découvrir comment automatiser vos réponses aux menaces, consultez [Configurer des réponses automatisées aux menaces dans Azure Sentinel](tutorial-respond-threats-playbook.md).
