---
title: Connecter des données de journal d’activité Azure à Azure Sentinel | Microsoft Docs
description: Diffusez des événements du journal d’activité Azure dans Azure Sentinel en un seul clic. Le service de journal d’activité enregistre et affiche les événements au niveau de l’abonnement dans Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2021
ms.author: yelevin
ms.openlocfilehash: 874c4d520b3d41282d74f047b34fbe7148279a65
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606735"
---
# <a name="connect-data-from-azure-activity-log"></a>Connecter des données à partir du journal d’activité Azure

Vous pouvez diffuser des événements du [service **Journal d’activité**](../azure-monitor/essentials/activity-log.md) d’Azure dans Azure Sentinel en un seul clic. Le **Journal d’activité** est un [journal de plateforme](../azure-monitor/essentials/platform-logs-overview.md) dans Azure qui fournit un aperçu des événements au niveau de l’abonnement, des données opérationnelles d’Azure Resource Manager aux mises à jour des événements d’intégrité des services. À l’aide du **journal d’activité**, vous pouvez déterminer « quoi, qui et quand » pour toutes les opérations d’écriture (PUT, POST, DELETE) effectuées sur les ressources de votre abonnement. Vous pouvez également connaître l’état de l’opération et d’autres propriétés pertinentes. Le service ne journalise pas les opérations de lecture (GET) ou les opérations pour les ressources qui n’utilisent pas le modèle Azure Resource Manager (ARM).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Configuration requise

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Log Analytics.

- Vous devez disposer des autorisations de propriétaire sur tout abonnement dont vous souhaitez démarrer ou arrêter la diffusion en continu dans Azure Sentinel.

- Pour utiliser Azure Policy afin d’appliquer une stratégie de streaming de journaux à des abonnements d’activité Azure, vous devez avoir le rôle Propriétaire pour l’étendue de l’attribution de stratégie.

## <a name="data-structure-changes"></a>Modifications de la structure de données

Ce connecteur subit une modification dans la méthode utilisée sur le back end pour la collecte des événements du journal d’activité. Il utilise désormais le pipeline **paramètres de diagnostic** utilisé par nos connecteurs les plus récents (par exemple nos connecteurs Azure Key Vault et Azure Kubernetes Service). Si vous utilisez encore l’ancienne méthode pour ce connecteur, nous vous encourageons *fortement à passer* à la nouvelle version, qui offre une meilleure fonctionnalité et une plus grande cohérence avec les journaux des ressources. Voir les instructions ci-dessous.

La méthode **Paramètres de diagnostic** envoie les mêmes données que l’ancienne méthode envoyait à partir du service de journal des activités, bien que certaines [modifications aient été apportées à la structure](../azure-monitor/essentials/activity-log.md#data-structure-changes) de la table **AzureActivity**.

Voici quelques-unes des améliorations clés résultant de la migration vers le pipeline de paramètres de diagnostic :
- Latence d’ingestion améliorée (ingestion des événements dans les 2-3 minutes à compter de l’occurrence au lieu de 15-20 minutes).
- Amélioration de la fiabilité.
- Performances améliorées.
- Prise en charge de toutes les catégories d’événements journalisés par le service de journal d’activité (le mécanisme hérité ne prend en charge qu’un sous-ensemble, par exemple, aucune prise en charge des événements Service Health).
- Gestion à l’échelle avec Azure Policy.

Consultez la [Documentation d’Azure Monitor](../azure-monitor/logs/data-platform-logs.md) pour un traitement plus approfondi du [Journal d’activité d’Azure](../azure-monitor/essentials/activity-log.md) et du [Pipeline de paramètres de diagnostic](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="set-up-the-azure-activity-log-connector"></a>Configurer le connecteur de journal d’activité

La configuration du nouveau connecteur des journaux d’activité Azure s’effectue en deux étapes :
1. Déconnectez les abonnements existants de la méthode héritée.

1. Connexion de tous les abonnements pertinents au nouveau pipeline de paramètres de diagnostic en utilisant **Azure Policy**.

### <a name="disconnect-from-old-pipeline"></a>Se déconnecter de l’ancien pipeline

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**. Dans la liste des connecteurs, sélectionnez **Azure Activity**, puis le bouton **Ouvrir la page du connecteur** dans le coin inférieur droit.

1. Sous l’onglet **Instructions**, dans la section **Configuration**, à l’étape 1, examinez la liste de vos abonnements existants qui sont connectés à l’ancienne méthode (afin de savoir lesquels ajouter à la nouvelle), et déconnectez-les tous en une seule fois en cliquant sur le bouton **Déconnecter tout** ci-dessous.

### <a name="connect-to-new-pipeline"></a>Se connecter au nouveau pipeline

1. Sous l’étape 2, sélectionnez le bouton **Lancer l’Assistant Attribution d’Azure Policy**. L’assistant d’affectation de stratégie s’ouvre, prêt à créer une nouvelle stratégie appelée **Configurer les journaux d’activité Azure de sorte qu’ils soient transmis en continu à l’espace de travail Log Analytics spécifié**.

1. Sous l’onglet **Informations de base**, sélectionnez le bouton avec les trois points sous **Étendue** pou ouvrir le panneau **Étendue** où vous pouvez sélectionner votre abonnement (et éventuellement un groupe de ressources). Vous pouvez également ajouter une description.

1. Dans l’onglet **Paramètres**, laissez les champs **Effet** et **Activer les journaux** tels quels. Choisissez votre espace de travail Azure Sentinel dans la liste déroulante **Espace de travail Log Analytics**.

1. La stratégie sera appliquée aux ressources ajoutées à l’avenir. Pour appliquer la stratégie également à vos ressources existantes, sélectionnez l’onglet **Correction** et cochez la case **Créer une tâche de correction**.

1. Sous l’onglet **Vérifier + créer**, cliquez sur **Créer**. Votre stratégie est maintenant affectée à l’étendue que vous avez choisie.

> [!NOTE]
>
> Avec ce connecteur de données particulier, les indicateurs d’état de connectivité (une bande de couleur dans la galerie des connecteurs de données et des icônes de connexion à côté des noms de types de données) s’affichent comme *connectés* (en vert) uniquement si les données ont été ingérées au cours des 14 derniers jours. Après 14 jours passés sans ingestion de données, le connecteur sera déconnecté. Quand plus de données arrivent, l’état *connecté* est renvoyé.

## <a name="find-your-data"></a>Recherche de données

Pour interroger le schéma approprié dans Log Analytics pour les alertes d’activité Azure, saisissez `AzureActivity` au sommet de la fenêtre de requête.

Consultez l’onglet **Prochaines étapes** de la page du connecteur pour obtenir des exemples de requêtes et des modèles de classeurs utiles pour vous aider à analyser et visualiser vos données.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter le journal d’activité Azure à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Commencez à détecter les menaces avec Azure Sentinel à l’aide de règles [intégrées](detect-threats-built-in.md) ou [personnalisées](detect-threats-custom.md).
