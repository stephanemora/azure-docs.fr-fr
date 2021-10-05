---
title: Création visuelle
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment utiliser la création visuelle dans Azure Data Factory et Synapse Analytics
ms.service: data-factory
ms.subservice: authoring
ms.custom: synapse
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.date: 09/09/2021
ms.openlocfilehash: a9ee177015896e314d140166c2477e92fa9edf8a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788058"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Création visuelle dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L’interface utilisateur (IU) Azure Data Factory et Synapse Analytics vous permet de créer et de déployer visuellement des ressources pour vos pipelines Azure Data Factory ou Synapse Analytics sans avoir à écrire de code. Vous pouvez glisser-déposer des activités sur le canevas d’un pipeline, effectuer des séries de tests, déboguer de manière itérative, mais aussi déployer et surveiller les exécutions de votre pipeline.

Actuellement, l’IU est uniquement prise en charge dans Microsoft Edge et Google Chrome.

## <a name="authoring-canvas"></a>Zone de travail de création

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).
Pour ouvrir la **zone de travail de création**, cliquez sur l’icône de crayon. 

:::image type="content" source="media/author-visually/authoring-canvas.png" alt-text="Zone de travail de création":::

Ici, vous créez les pipelines, les activités, les jeux de données et les flux de données qui composent votre fabrique. De même, les services liés, les déclencheurs et les runtimes d’intégration peuvent être modifiés sous l’[onglet Gérer](#management-hub). Pour commencer à créer un pipeline à l’aide de la zone de travail de création, consultez [Copier des données à l’aide de l’activité de copie](tutorial-copy-data-portal.md). 

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
Pour ouvrir le **canevas de création**, cliquez sur l’icône de pipeline/d’intégration. 

:::image type="content" source="media/author-visually/authoring-canvas-synapse.png" alt-text="Zone de travail de création":::

Ici, vous créez les pipelines et activités dans Synapse. De même, les flux de données utilisés par vos pipelines peuvent être modifiés sous l’onglet Développer, et les services liés, les déclencheurs et les runtime d’intégration associés sous l’[onglet Gérer](#management-hub). Pour commencer à créer un pipeline à l’aide de la zone de travail de création, consultez [Copier des données à l’aide de l’activité de copie](tutorial-copy-data-portal.md). 

---

L’expérience de création visuelle par défaut fonctionne directement avec le service. L’intégration GitHub ou Azure Repos Git est également prise en charge afin d’autoriser le contrôle du code source et la collaboration pour les utiliser sur vos pipelines. Pour en savoir plus sur les différences entre ces expériences de création, consultez [Contrôle de code source](source-control.md).

### <a name="properties-pane"></a>Propriétés, volet

Pour des ressources de niveau supérieur, telles que des pipelines, des jeux de données et des flux de données, les propriétés de haut niveau sont modifiables dans le volet Propriétés sur le côté droit de la zone de dessin. Le volet Propriétés contient des propriétés telles que le nom, la description, les annotations et d’autres propriétés de haut niveau. Les sous-ressources telles que les activités de pipeline et les transformations de flux de données sont modifiées via le volet en bas de la zone de dessin. 

:::image type="content" source="media/author-visually/properties-pane.png" alt-text="Propriétés, volet":::

Par défaut, le volet des propriétés s’ouvre uniquement lors de la création de ressource. Pour le modifier, cliquez sur l’icône du volet de propriétés située dans l’angle supérieur droit de la zone de dessin.

### <a name="related-resources"></a>Ressources associées

Dans le volet Propriétés, vous pouvez voir quelles sont les ressources qui dépendent de la ressource sélectionnée en sélectionnant l’onglet **Associées**. Toutes les ressources qui font référence à la ressource actuelle seront répertoriées ici.

:::image type="content" source="media/author-visually/related-resources.png" alt-text="Ressources associées":::

Par exemple, dans l’image ci-dessus, un pipeline et deux flux de données utilisent le jeu de données actuellement sélectionné.

## <a name="management-hub"></a>Hub de gestion

Le hub de gestion, accessible via l’onglet *Gérer* de l’interface utilisateur, est un portail qui héberge des actions de gestion globale pour le service. Ici, vous pouvez gérer vos connexions aux banques de données, aux calculs externes, à la configuration du contrôle de code source et aux paramètres de déclencheur. Pour plus d’informations, consultez les capacités du [hub de gestion](author-management-hub.md).

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="Gérer des services liés":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="Gérer des services liés":::

---

## <a name="expressions-and-functions"></a>Expressions et fonctions

Les expressions et les fonctions peuvent être utilisées à la place des valeurs statiques pour spécifier de nombreuses propriétés dans le service.

Pour spécifier une expression pour une valeur de propriété, sélectionnez **Ajouter du contenu dynamique** ou cliquez sur **Alt+P** tout en vous concentrant sur le champ.

:::image type="content" source="media/author-visually/dynamic-content-1.png" alt-text="Ajouter du contenu dynamique":::

Vous ouvrez ainsi le **Générateur d’expressions**, où vous pouvez générer des expressions à partir des variables système prises en charge, de la sortie de l’activité, des fonctions et des variables ou paramètres spécifiés par l’utilisateur. 

:::image type="content" source="media/author-visually/dynamic-content-2.png" alt-text="Générateur d’expressions":::

Pour plus d’informations sur le langage d’expression, consultez [Expressions et fonctions](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Fournir des commentaires

Sélectionnez **Feedback** (Commentaire) pour donner votre avis sur les fonctionnalités ou informer Microsoft de problèmes avec l’outil :

:::image type="content" source="media/author-visually/provide-feedback.png" alt-text="Commentaires":::

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la surveillance et la gestion des pipelines, consultez [Surveiller et gérer les pipelines par programmation](monitor-programmatically.md).
