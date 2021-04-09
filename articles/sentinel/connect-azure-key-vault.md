---
title: Connecter les journaux de diagnostic Azure Key Vault à Azure Sentinel
description: Apprenez à connecter les journaux de diagnostic Azure Key Vault à Azure Sentinel à l'aide d'Azure Policy.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: 00e634ec62d1fda4b1f2763a8c69b8e950d54beb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505186"
---
# <a name="connect-azure-key-vault-diagnostics-logs"></a>Connecter les journaux de diagnostic Azure Key Vault

Azure Key Vault est un service cloud permettant de stocker les secrets et d’y accéder en toute sécurité. Un secret est un élément pour lequel vous voulez contrôler étroitement l’accès. Il peut s’agir de clés d’API, de mots de passe, de certificats ou de clés de chiffrement.

Ce connecteur vous permet de diffuser en continu vos journaux de diagnostic Azure Key Vault dans Azure Sentinel, afin de pouvoir surveiller en continu l'activité de toutes vos instances.

Apprenez-en davantage sur la [surveillance d'Azure Key Vault](../azure-monitor/insights/key-vault-insights-overview.md) et sur la [télémétrie de diagnostic d'Azure Key Vault](../key-vault/general/logging.md).

## <a name="prerequisites"></a>Prérequis

Pour ingérer les journaux Azure Key Vault dans Azure Sentinel :

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Pour utiliser Azure Policy afin d'appliquer une stratégie de streaming de journaux à des ressources Azure Key Vault, vous devez disposer du rôle de Propriétaire pour l'étendue d'attribution de la stratégie.

## <a name="connect-to-azure-key-vault"></a>Se connecter à Azure Key Vault

Ce connecteur utilise Azure Policy pour appliquer une configuration de streaming de journaux Azure Key Vault unique à une collection d'instances, définie en tant qu'étendue. Les types de journaux ingérés à partir d'Azure Key Vault sont présentés sur le côté gauche de la page du connecteur, sous **Types de données**.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez **Azure Key Vault** dans la galerie de connecteurs de données, puis sélectionnez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Dans la section **Configuration** de la page du connecteur, développez **Activer les journaux de diagnostic sur Azure Key Vault**.

1. Sélectionnez le bouton **Lancer l'Assistant Attribution d'Azure Policy**.

    L'Assistant d'attribution de stratégie s'ouvre, prêt à créer une stratégie nommée **Déployer - Configurer les paramètres de diagnostic d'Azure Key Vault dans l'espace de travail Log Analytics**.

    1. Sous l'onglet **De base**, cliquez sur le bouton affichant trois points sous **Étendue** pour sélectionner votre abonnement (et éventuellement un groupe de ressources). Vous pouvez également ajouter une description.

    1. Sous l’onglet **Paramètres**, choisissez votre espace de travail Azure Sentinel dans la liste déroulante **Espace de travail Log Analytics**. Les champs de liste déroulante restants représentent les types de journaux de diagnostic disponibles. Conservez la valeur « True » pour tous les types de journaux que vous souhaitez ingérer.

    1. Pour appliquer la stratégie à vos ressources existantes, sélectionnez l’onglet **Correction** et cochez la case **Créer une tâche de correction**.

    1. Sous l’onglet **Vérifier + créer**, cliquez sur **Créer**. Votre stratégie est maintenant affectée à l’étendue que vous avez choisie.

> [!NOTE]
>
> Avec ce connecteur de données particulier, les indicateurs d’état de connectivité (une bande de couleur dans la galerie de connecteurs de données et des icônes de connexion en regard des noms de types de données) s’affichent comme *Connectés* (en vert) uniquement si les données ont été ingérées au cours des deux dernières semaines. Après deux semaines passées sans ingestion de données, le connecteur déconnecté. Quand plus de données arrivent, l’état *connecté* est renvoyé.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a appris à connecter Azure Key Vault à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
