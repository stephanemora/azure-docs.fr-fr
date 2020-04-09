---
title: Fichier include
description: Fichier include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: be8aae6308e712449402b002576974743bc125ef
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986614"
---
Ces exemples montrent comment utiliser Azure Monitor pour créer des alertes avec des abonnements qui ont été intégrés pour la gestion des ressources déléguées Azure.

| **Modèle** | **Description** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Interroge le dernier jour d’activité dans un locataire gérant et [crée des rapports sur les délégations ajoutées ou supprimées](../articles/lighthouse/how-to/monitor-delegation-changes.md) (ou sur les tentatives ayant échoué).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Ce modèle crée une alerte Azure et se connecte à un groupe d’actions existant.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Crée plusieurs alertes de journal basées sur des requêtes Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Déploie une alerte dans un locataire lorsqu’un utilisateur délègue un abonnement à un locataire gérant.|
