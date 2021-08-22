---
title: Connecter des journaux de diagnostic AKS (Azure Kubernetes Service) à Azure Sentinel
description: Découvrez comment connecter des journaux de diagnostic Azure Kubernetes Service à Azure Sentinel avec Azure Policy.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/22/2021
ms.author: yelevin
ms.openlocfilehash: 96068491bd4be59c34d62375db30edad504823a9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524210"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Connecter des journaux de diagnostic Azure Kubernetes Service

AKS (Azure Kubernetes service) est un service d’orchestration de conteneurs open source complètement managé qui vous permet de déployer, de mettre à l’échelle et de gérer des conteneurs Docker et des applications basées sur des conteneurs dans un environnement de cluster.

Ce connecteur vous permet de streamer vos journaux de diagnostic AKS (Azure Kubernetes Service) dans Azure Sentinel, ce qui vous permet de superviser continuellement l’activité dans toutes vos instances. 

Apprenez-en davantage sur la [supervision d’Azure Kubernetes Service](../azure-monitor/containers/container-insights-overview.md) et sur la [télémétrie de diagnostic AKS](../aks/monitor-aks-reference.md#resource-logs).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Prérequis

Pour ingérer des journaux AKS dans Azure Sentinel :

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Pour utiliser Azure Policy afin d’appliquer une stratégie de streaming de journaux à des ressources AKS, vous devez avoir le rôle Propriétaire pour l’étendue de l’attribution de stratégie.

## <a name="connect-to-azure-kubernetes-service"></a>Se connecter à Azure Kubernetes Service

Ce connecteur utilise Azure Policy pour appliquer une configuration unique de streaming de journaux Azure Kubernetes Service à une collection de ressources, définie en tant qu’étendue. Vous pouvez voir les types de journaux ingérés à partir d’Azure Kubernetes Service sur le côté gauche de la page des connecteurs, sous **Types de données**.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez **Azure Kubernetes Service (AKS)** dans la galerie de connecteurs de données, puis sélectionnez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Dans la section **Configuration** de la page du connecteur, développez **Diffuser en continu les journaux de diagnostic de votre Azure Kubernetes Service (AKS) à grande échelle**.

1. Sélectionnez le bouton **Lancer l’Assistant Affectation Azure Policy**.

    L’Assistant d’affectation de stratégie s’ouvre, prêt à créer une nouvelle stratégie nommée **Déployer - Configurer les paramètres de diagnostic pour Azure Kubernetes Service vers l’espace de travail Log Analytics**.

    1. Sous l’onglet **Informations de base**, cliquez sur le bouton avec les trois points sous **Étendue** pour sélectionner votre abonnement (et éventuellement un groupe de ressources). Vous pouvez également ajouter une description.

    1. Dans l’onglet **Paramètres**, laissez les champs **Effet** et **Nom du paramètre** tels quels. Choisissez votre espace de travail Azure Sentinel dans la liste déroulante **Espace de travail Log Analytics**. Les champs de liste déroulante restants représentent les types de journaux de diagnostic disponibles. Conservez la valeur « True » pour tous les types de journaux que vous souhaitez ingérer.

    1. La stratégie sera appliquée aux ressources ajoutées à l’avenir. Pour appliquer la stratégie également à vos ressources existantes, sélectionnez l’onglet **Correction** et cochez la case **Créer une tâche de correction**.

    1. Sous l’onglet **Vérifier + créer**, cliquez sur **Créer**. Votre stratégie est maintenant affectée à l’étendue que vous avez choisie.

> [!NOTE]
>
> Avec ce connecteur de données particulier, les indicateurs d’état de connectivité (une bande de couleur dans la galerie des connecteurs de données et des icônes de connexion à côté des noms de types de données) s’affichent comme *connectés* (en vert) uniquement si les données ont été ingérées au cours des 14 derniers jours. Après 14 jours passés sans ingestion de données, le connecteur sera déconnecté. Quand plus de données arrivent, l’état *connecté* est renvoyé.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment utiliser Azure Policy pour connecter Azure Kubernetes Service à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
