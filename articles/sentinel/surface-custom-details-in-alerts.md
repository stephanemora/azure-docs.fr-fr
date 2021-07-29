---
title: Faire apparaître des détails personnalisés dans les alertes Azure Sentinel | Microsoft Docs
description: Extrayez et faites apparaître des détails d'événements personnalisés dans les alertes à l'aide des règles analytiques d'Azure Sentinel afin de bénéficier d'informations plus pertinentes et plus complètes sur les incidents
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 97cd4d51da70b9b1cca501c7f04a3b0ccfc51305
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112059672"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>Faire apparaître des détails d'événements personnalisés dans les alertes d'Azure Sentinel 

> [!IMPORTANT]
>
> - La fonctionnalité de détails personnalisés est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="introduction"></a>Introduction

Les [règles analytiques de requête planifiée](tutorial-detect-threats-custom.md) analysent les **événements** issus des sources de données connectées à Azure Sentinel, et produisent des **alertes** lorsque le contenu de ces événements est significatif du point de vue de la sécurité. Ces alertes sont ensuite analysées, regroupées et filtrées par les différents moteurs d'Azure Sentinel et transformées en **incidents** qui nécessitent l'attention d'un analyste du centre des opérations de sécurité (SOC). Mais lorsque l'analyste visualise l'incident, seules les propriétés des alertes de composant proprement dites sont immédiatement visibles. Pour accéder au contenu réel (informations contenues dans les événements), il faut creuser un peu.

À l'aide de la fonctionnalité **Détails personnalisés** de l'**Assistant de règle analytique**, vous pouvez faire apparaître des données d'événements dans les alertes générées à partir de ces événements, en intégrant ces données aux propriétés des alertes. Vous bénéficiez ainsi d'une visibilité immédiate sur le contenu des événements au sein de vos incidents, ce qui vous permet de trier, d'examiner, de tirer des conclusions et de réagir avec beaucoup plus de rapidité et d'efficacité.

La procédure détaillée ci-dessous fait partie de l'Assistant Création de règles analytiques. Elle est traitée ici de manière indépendante afin de prendre en charge le scénario d'ajout ou de modification de détails personnalisés dans une règle analytique existante.

## <a name="how-to-surface-custom-event-details"></a>Pour faire apparaître des détails d'événements personnalisés

1. Dans le menu de navigation d'Azure Sentinel, sélectionnez **Analytique**.

1. Sélectionnez une règle de requête planifiée, puis cliquez sur **Modifier**. Ou créez une règle en cliquant sur **Créer > Règle de requête planifiée** en haut de l’écran.

1. Cliquez sur l’onglet **Définir la logique de la règle**.

1. Dans la section **Enrichissement des alertes (préversion)** , développez **Détails personnalisés**.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enrichment.png" alt-text="Rechercher et sélectionner des détails personnalisés":::

1. Dans la section **Détails personnalisés** désormais développée, ajoutez les paires clé-valeur correspondant aux détails que vous souhaitez faire apparaître :

    1. Dans le champ **Clé**, entrez le nom de votre choix ; il s'agit du nom de champ qui apparaîtra dans les alertes.

    1. Dans le champ **Valeur**, accédez à la liste déroulante et choisissez le paramètre d'événement que vous souhaitez faire apparaître dans les alertes. Cette liste contiendra les valeurs correspondant aux champs des tables qui font l'objet de la requête de règle.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="Ajouter des détails personnalisés":::

1. Cliquez sur **Ajouter plus** afin de faire apparaître plus de détails, en répétant les dernières étapes pour définir les paires clé-valeur. 

    Si vous changez d'avis, ou si vous avez fait une erreur, vous pouvez supprimer un détail personnalisé en cliquant sur l'icône de la Corbeille en regard de la liste déroulante **Valeur** de ce détail.

1. Après avoir défini les détails personnalisés, cliquez sur l'onglet **Examiner et créer**. Une fois la règle validée, cliquez sur **Enregistrer**.

    > [!NOTE]
    > 
    > **Limites du service**
    > - Vous pouvez définir **jusqu'à 20 détails personnalisés** au sein d'une même règle analytique.
    >
    > - La taille maximale de l'ensemble des détails personnalisés est de **2 Ko**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à faire apparaître des détails personnalisés dans les alertes à l'aide des règles analytiques d'Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Obtenez une vue d’ensemble complète des [règles analytiques de requête planifiée](tutorial-detect-threats-custom.md).
- Apprenez-en davantage sur les [entités d'Azure Sentinel](entities-in-azure-sentinel.md).
