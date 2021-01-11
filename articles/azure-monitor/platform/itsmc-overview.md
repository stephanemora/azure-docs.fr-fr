---
title: Vue d’ensemble du Connecteur de gestion des services informatiques
description: Cet article fournit une vue d’ensemble du Connecteur de gestion des services informatiques (Connecteur ITSM).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93b6160b495b609e23e10b3f709d130792067423
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803780"
---
# <a name="it-service-management-connector-overview"></a>Vue d’ensemble du Connecteur de gestion des services informatiques

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Le connecteur de gestion des services informatiques (ITSMC) vous permet de connecter Azure à un produit ou service Gestion des services informatiques (ITSM) pris en charge.

Des services Azure comme Azure Log Analytics et Azure Monitor fournissent des outils pour détecter, analyser et résoudre les problèmes liés à vos ressources Azure et non Azure. Mais les éléments de travail liés à un problème résident généralement dans un service ou produit ITSM. ITSMC fournit une connexion bidirectionnelle entre Azure et les outils ITSM pour vous permettre de résoudre les problèmes plus rapidement.

## <a name="configuration-steps"></a>Configuration

ITSMC prend en charge les connexions avec les outils ITSM suivants :

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Nous proposons à nos clients Cherwell et Provance d’utiliser une [action webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) pour point de terminaison Cherwell et Provance en guise d’autre solution pour l’intégration.

Avec ITSMC, vous pouvez :

-  Créer des éléments de travail dans votre outil ITSM, en fonction de vos alertes Azure (alertes de métriques, alertes de journal d’activité et alertes Log Analytics).
-  Si vous le souhaitez, vous pouvez synchroniser les données d’incidents et de demandes de modification de vos outils ITSM vers un espace de travail Azure Log Analytics.

Pour plus d’informations sur les conditions légales et la politique de confidentialité, consultez [Déclaration de confidentialité Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Vous pouvez commencer à utiliser ITSMC en effectuant les étapes suivantes :

1. [Configurer votre environnement ITSM pour accepter les alertes d’Azure.](./itsmc-connections.md)
1. [Configurer une solution ITSM Azure](./itsmc-definition.md#add-it-service-management-connector)
1. [Configurer le Connecteur ITSM Azure pour votre environnement ITSM.](./itsmc-definition.md#create-an-itsm-connection)
1. [Configurer un groupe d’actions pour tirer parti du Connecteur ITSM.](./itsmc-definition.md#use-itsmc)

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes liés au connecteur ITSM](./itsmc-resync-servicenow.md)