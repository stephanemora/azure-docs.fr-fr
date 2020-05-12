---
title: Utiliser Azure Automation Update Management avec les clients Configuration Manager
description: Cet article a pour but de vous aider à configurer Microsoft Endpoint Configuration Manager avec cette solution pour déployer des mises à jour logicielles sur des clients ConfigMgr.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 2dbc33aa56c7e930596ba6806ba1dd2e128e1c82
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780213"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Déployer des mises à jour sur des clients Microsoft Endpoint Configuration Manager avec Update Management

Les clients ayant investi dans Microsoft Endpoint Configuration Manager pour gérer des PC, des serveurs et d’autres appareils mobiles s’appuient aussi sur sa puissance et sa maturité pour gérer les mises à jour logicielles dans le cadre de leur cycle de gestion des mises à jour logicielles (SUM).

Vous pouvez signaler et mettre à jour des serveurs Windows gérés en créant et prédéfinissant des déploiements de mises à jour logicielles dans Configuration Manager et obtenir un état détaillé des déploiements de mises à jour terminés à l’aide d’[Update Management](automation-update-management.md). Si vous utilisez Configuration Manager pour générer des rapports de conformité de mises à jour, mais pas pour gérer les déploiements de mises à jour avec vos serveurs Windows, vous pouvez continuer d’adresser des rapports à Configuration Manager tout en gérant les mises à jour de sécurité avec Update Management.

## <a name="prerequisites"></a>Prérequis

* Vous devez avoir ajouté [Update Management](automation-update-management.md) à votre compte Automation.
* Les serveurs Windows actuellement gérés par votre environnement Configuration Manager doivent aussi adresser des rapports à l’espace de travail Log Analytics dans lequel la solution Update Management est aussi activée.
* Cette fonctionnalité est activée dans la branche actuelle de Configuration Manager, version 1606 et supérieure. Pour intégrer votre site d'administration centrale Configuration Manager ou un site principal autonome aux journaux Azure Monitor et importer des collections, consultez [Connecter Configuration Manager aux journaux Azure Monitor](../azure-monitor/platform/collect-sccm.md).  
* Les agents Windows doivent être configurés pour communiquer avec un serveur WSUS (Windows Server Update Services) ou avoir accès à Microsoft Update s’ils ne reçoivent pas de mises à jour de sécurité de Configuration Manager.

La façon dont vous gérez les clients hébergés dans Azure IaaS avec votre environnement Configuration Manager existant dépend essentiellement de la connexion dont vous disposez entre les centres de données Azure et votre infrastructure. Cette connexion a une incidence sur les changements de conception que vous pouvez être amené à apporter à votre infrastructure Configuration Manager et sur les coûts liés à la prise en charge de ces changements. Pour comprendre les considérations de planification que vous devez évaluer avant de poursuivre, consultez [Configuration Manager sur Azure - Questions fréquentes (FAQ)](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Configuration

### <a name="manage-software-updates-from-configuration-manager"></a>Gérer les mises à jour logicielles à partir de Configuration Manager

Si vous avez l’intention de continuer à gérer les déploiements de mises à jour à partir de Configuration Manager, effectuez les étapes suivantes. Azure Automation se connecte à Configuration Manager pour appliquer les mises à jour aux ordinateurs clients connectés à votre espace de travail Log Analytics. Le contenu des mises à jour est disponible dans le cache de l’ordinateur client comme si le déploiement était géré par Configuration Manager.

1. Créez un déploiement de mises à jour logicielles à partir du site de niveau supérieur dans votre hiérarchie de Configuration Manager à l’aide du processus décrit dans [Déployer des mises à jour logicielles](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). Le seul paramètre qui doit être configuré différemment par rapport à un déploiement standard est l’option **Ne pas installer les mises à jour logicielles** pour contrôler le comportement de téléchargement du package de déploiement. Ce comportement est géré dans Update Management en créant un déploiement de mises à jour planifié à l’étape suivante.

1. Dans Azure Automation, sélectionnez **Update Management**. Créez un déploiement en suivant les étapes décrites dans [Création d’un déploiement de mises à jour](automation-tutorial-update-management.md#schedule-an-update-deployment), puis sélectionnez **Groupes importés** dans la liste déroulante **Type** pour sélectionner le regroupement Configuration Manager approprié. Gardez à l’esprit les points importants suivants : a. Si une fenêtre de maintenance est définie dans le regroupement d’appareils Configuration Manager sélectionné, les membres du regroupement la respectent au détriment du paramètre **Durée** défini dans le déploiement planifié.
    b. Les membres du regroupement cible doivent disposer d’une connexion Internet (qu’elle soit directe, via un serveur proxy ou via la passerelle Log Analytics).

À l’issue du déploiement des mises à jour par le biais d’Azure Automation, les ordinateurs cibles membres du groupe d’ordinateurs sélectionné installent les mises à jour à l’heure planifiée à partir de leur cache client local. Vous pouvez [consulter l’état du déploiement des mises à jour](automation-tutorial-update-management.md#view-results-of-an-update-deployment) pour surveiller les résultats de votre déploiement.

### <a name="manage-software-updates-from-azure-automation"></a>Gérer les mises à jour logicielles à partir d’Azure Automation

Pour gérer les mises à jour de machines virtuelles Windows Server qui sont des clients Configuration Manager, vous devez configurer la stratégie de configuration des clients afin de désactiver la fonctionnalité de gestion des mises à jour logicielles pour tous les clients gérés par Update Management. Par défaut, les paramètres client ciblent tous les appareils de la hiérarchie. Pour plus d’informations sur ce paramètre de stratégie et sur la façon de le configurer, consultez [Comment configurer les paramètres client dans Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

Après avoir apporté cette modification à la configuration, vous créez un déploiement en suivant les étapes décrites dans [Création d’un déploiement de mises à jour](automation-tutorial-update-management.md#schedule-an-update-deployment), puis vous sélectionnez **Groupes importés** dans la liste déroulante **Type** pour sélectionner le regroupement Configuration Manager approprié.

## <a name="next-steps"></a>Étapes suivantes

Créez un déploiement en suivant les étapes décrites dans [Création d’un déploiement de mises à jour](automation-tutorial-update-management.md#schedule-an-update-deployment).