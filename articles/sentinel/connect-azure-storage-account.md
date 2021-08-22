---
title: Connecter les journaux de diagnostic Stockage Azure à Azure Sentinel
description: Apprenez à connecter les journaux de diagnostic Stockage Azure à Azure Sentinel à l’aide d’Azure Policy.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: af9f5218b7f7f94ddc73fb67fe82427d1cfbfff2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524928"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>Connecter les journaux de diagnostic Stockage Azure

> [!IMPORTANT]
> Le connecteur de compte Stockage Azure est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Le compte Stockage Azure est une solution cloud pour les scénarios modernes de stockage de données. Il contient tous vos objets de données : blobs, fichiers, files d’attente, tables et disques.

Ce connecteur vous permet de diffuser en continu les journaux de diagnostic de vos comptes Stockage Azure dans Azure Sentinel, ce qui vous permet en permanence de surveiller l’activité et de détecter les menaces de sécurité dans toutes vos ressources de stockage Azure au sein de votre organisation.

En savoir plus sur la [supervision d’un compte Stockage Azure](../storage/common/storage-analytics-logging.md).

## <a name="prerequisites"></a>Prérequis

Pour ingérer les journaux de diagnostic d’un compte Stockage Azure dans Azure Sentinel :

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Pour utiliser Azure Policy afin d’appliquer une stratégie de streaming de journaux à des ressources Stockage Azure, vous devez avoir le rôle Propriétaire pour l’étendue de l’attribution de stratégie.

## <a name="connect-to-azure-storage-account"></a>Connectez-vous au compte Stockage Azure

Ce connecteur utilise Azure Policy pour appliquer une configuration unique de streaming de journaux à une collection de ressources de compte Stockage Azure, définie en tant qu’étendue. Vous pouvez voir les types de journaux et de métriques Stockage Azure disponibles sur le côté gauche de la page du connecteur, sous **Types de données**.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez **Compte Stockage Azure** dans la galerie des connecteurs de données, puis sélectionnez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Dans la section **Configuration** de la page du connecteur, développez **Diffuser en continu les journaux de diagnostic de votre compte Stockage Azure à grande échelle**.

1. Sélectionnez le bouton **Lancer l’Assistant Affectation Azure Policy**.

    L’Assistant d’attribution de stratégie s’ouvre, prêt à créer une stratégie nommée **Configurer les paramètres de diagnostic de comptes de stockage dans l’espace de travail Log Analytics**.

    1. Sous l’onglet **Informations de base**, cliquez sur le bouton avec les trois points à côté d’**Étendue** pour sélectionner votre abonnement (et éventuellement un groupe de ressources). Vous pouvez également ajouter une description.

    1. Dans l’onglet **Paramètres** :
        - Choisissez votre espace de travail Azure Sentinel dans la liste déroulante **Espace de travail Log Analytics**.
        - Dans la liste déroulante **Services de stockage à déployer**, sélectionnez les types de ressources de stockage (fichier, table, file d’attente, etc.) vers lesquels vous souhaitez déployer les paramètres de diagnostic.
        - Laissez les champs **Nom du paramètre** et **Effet** tels quels.
        - Les autres champs déroulants représentent les types de journaux de diagnostic et de métriques disponibles. Conservez la valeur « True » pour tous les types que vous souhaitez ingérer.

    1. Les étapes ci-dessus appliquent la stratégie à toutes les futures ressources de stockage. Pour appliquer la stratégie à vos ressources existantes, sélectionnez l’onglet **Correction** et cochez la case **Créer une tâche de correction**.

    1. Sous l’onglet **Vérifier + créer**, cliquez sur **Créer**. Votre stratégie est maintenant affectée à l’étendue que vous avez choisie.

> [!NOTE]
>
> Avec ce connecteur de données particulier, les indicateurs d’état de connectivité (une bande de couleur dans la galerie des connecteurs de données et des icônes de connexion à côté des noms de types de données) s’affichent comme *connectés* (en vert) uniquement si les données ont été ingérées au cours des 14 derniers jours. Après 14 jours passés sans ingestion de données, le connecteur sera déconnecté. Quand plus de données arrivent, l’état *connecté* est renvoyé.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a appris à connecter un compte Stockage Azure à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
