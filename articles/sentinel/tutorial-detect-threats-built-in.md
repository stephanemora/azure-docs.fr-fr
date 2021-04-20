---
title: Détecter des menaces à l’aide de règles d’analytique intégrées dans Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser des règles de détection des menaces prêtes à l’emploi, basées sur des modèles prédéfinis, qui vous informent quand quelque chose de suspect se produit.
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
ms.date: 04/12/2021
ms.author: yelevin
ms.openlocfilehash: ba757474ab24006b7b8b514bda085522bd353ea8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312669"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Tutoriel : Détection des menaces prête à l’emploi

Une fois que vous avez [connecté vos sources de données](quickstart-onboard.md) à Azure Sentinel, vous souhaiterez être averti en cas d’activité suspecte. C’est pourquoi Azure Sentinel fournit des modèles intégrés et prêts à l’emploi pour vous aider à créer des règles de détection des menaces. Ces modèles ont été conçus par l’équipe d’analystes et d’experts en sécurité de Microsoft en fonction de menaces connues, de vecteurs d’attaque courants et de chaînes d’escalade d’activités suspectes. Les règles créées à partir de ces modèles rechercheront automatiquement toute activité qui semble suspecte dans votre environnement. La plupart des modèles peuvent être personnalisés pour rechercher des activités ou les filtrer, en fonction de vos besoins. Les alertes générées par ces règles vont créer des incidents que vous pouvez attribuer et examiner dans votre environnement.

Ce tutoriel vous aide à détecter les menaces avec Azure Sentinel :

> [!div class="checklist"]
> * Utiliser des détections de menaces prêtes à l’emploi
> * Automatiser les réponses aux menaces

## <a name="about-out-of-the-box-detections"></a>À propos des détections prêtes à l’emploi

Pour voir toutes les détections prêtes à l’emploi, accédez à **Analytique**, puis à **Modèles de règle**. Cet onglet contient toutes les règles intégrées Azure Sentinel.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Utiliser des détections intégrées pour identifier les menaces avec Azure Sentinel":::

Les sections suivantes décrivent les types de modèles prêts à l’emploi disponibles :

### <a name="microsoft-security"></a>Sécurité Microsoft

Les modèles de sécurité Microsoft créent automatiquement des incidents Azure Sentinel à partir des alertes générées dans d’autres solutions de sécurité Microsoft, en temps réel. Vous pouvez utiliser des règles de sécurité Microsoft comme modèle pour créer des règles ayant une logique similaire.

Pour plus d’informations sur les règles de sécurité, consultez [Créer automatiquement des incidents à partir d’alertes de sécurité Microsoft](create-incidents-from-alerts.md).

### <a name="fusion"></a>Fusion

Basée sur la technologie Fusion, la détection avancée des attaques en plusieurs étapes dans Azure Sentinel utilise des algorithmes de Machine Learning évolutifs qui peuvent mettre en corrélation un grand nombre d’alertes et d’événements basse fidélité sur plusieurs produits dans des incidents haute fidélité et exploitables. La fusion est activée par défaut. Comme la logique est cachée et donc non personnalisable, vous ne pouvez créer qu’une seule règle avec ce modèle.

> [!IMPORTANT]
> Certaines des détections dans le modèle de règle Fusion sont actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.
>
> Pour les détections en préversion, consultez [Détection avancée des attaques multiphases dans Azure Sentinel](fusion.md).

### <a name="machine-learning-behavioral-analytics"></a>Analytique comportementale du Machine Learning

Ces modèles sont basés sur des algorithmes de Machine Learning appartenant à Microsoft. Vous ne pouvez donc pas voir la logique interne de leur fonctionnement et du moment de leur exécution. Comme la logique est cachée et donc non personnalisable, vous ne pouvez créer qu’une seule règle avec chaque modèle de ce type.

> [!IMPORTANT]
> - Les modèles de règle analytique comportementale de Machine Learning sont actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.
>
> - En créant et en activant des règles basées sur des modèles d’analytique du comportement de Machine Learning, **vous autorisez Microsoft à copier les données ingérées en dehors de la zone géographique de votre espace de travail Azure Sentinel**, le cas échéant, en vue de leur traitement par des moteurs et modèles de Machine Learning.

### <a name="scheduled"></a>Planifié

Les règles d’analytique planifiées sont basées sur des requêtes intégrées écrites par des experts en sécurité de Microsoft. Vous pouvez voir la logique de la requête et y apporter des changements. Vous pouvez utiliser le modèle de règles planifiées et personnaliser la logique des requêtes et les paramètres de planification pour créer de nouvelles règles.

> [!TIP]
> Les options de planification de règle incluent la configuration de la règle pour qu’elle s’exécute chaque nombre spécifié de minutes, d’heures ou de jours, l’horloge démarrant lorsque vous activez la règle.
>
> Nous vous recommandons d’être attentif lorsque vous activez une règle d’analyse nouvelle ou modifiée pour vous assurer que les règles obtiendront la nouvelle pile d’incidents en temps opportun. Par exemple, vous pouvez exécuter une règle au moment où vos analystes du centre des opérations de sécurité commencent leur journée de travail, puis activer les règles.
>

## <a name="use-out-of-the-box-detections"></a>Utiliser des détections prêtes à l’emploi

1. Pour utiliser un modèle intégré, cliquez sur le nom du modèle, puis cliquez sur le bouton **Créer une règle** dans le volet d’informations pour créer une nouvelle règle active basée sur ce modèle. Chaque modèle possède une liste de sources de données requises. Lorsque vous ouvrez le modèle, la disponibilité des sources de données est automatiquement vérifiée. En cas de problème de disponibilité, le bouton **Créer une règle** peut être désactivé ou un avertissement à cet effet peut s’afficher.

    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Panneau d’aperçu de la règle de détection":::

1. Cliquer sur le bouton **Créer une règle** ouvre l’Assistant Création de règles en fonction du modèle sélectionné. Tous les détails sont remplis automatiquement et, grâce aux modèles **Planifié** ou **Sécurité Microsoft**, vous pouvez personnaliser la logique et les autres paramètres de règle pour mieux répondre à vos besoins spécifiques. Vous pouvez répéter ce processus pour créer des règles supplémentaires basées sur le modèle intégré. Après avoir suivi jusqu’au bout les étapes de l’Assistant Création de règles, vous aurez terminé la création d’une règle basée sur le modèle. Les nouvelles règles s’affichent dans l’onglet **Règles actives**.

    Pour plus d’informations sur la personnalisation de vos règles dans l’Assistant Création de règles, consultez [Didacticiel : Créer des règles d’analytique personnalisées pour détecter des menaces](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à prendre en main la détection des menaces en utilisant Azure Sentinel.

Pour découvrir comment automatiser vos réponses aux menaces, consultez [Configurer des réponses automatisées aux menaces dans Azure Sentinel](tutorial-respond-threats-playbook.md).
