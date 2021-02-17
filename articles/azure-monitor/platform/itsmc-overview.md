---
title: Vue d’ensemble du Connecteur de gestion des services informatiques
description: Cet article fournit une vue d’ensemble du Connecteur de gestion des services informatiques (Connecteur ITSM).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: d22bb05ad6db3630e9b0242e098fd81f65e34b05
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007310"
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
> À compter du 1er octobre 2020, les intégrations ITSM de Cherwell et Provance à Azure Alert ne sont plus disponibles pour les nouveaux clients. Les nouvelles connexions ITSM ne seront pas prises en charge.
> Les connexions ITSM existantes continueront d’être prises en charge.

Avec ITSMC, vous pouvez :

-  Créer des éléments de travail dans votre outil ITSM, en fonction de vos alertes Azure (alertes de métriques, alertes de journal d’activité et alertes Log Analytics).
-  Si vous le souhaitez, vous pouvez synchroniser les données d’incidents et de demandes de modification de vos outils ITSM vers un espace de travail Azure Log Analytics.

Pour plus d’informations sur les conditions légales et la politique de confidentialité, consultez [Déclaration de confidentialité Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Vous pouvez commencer à utiliser ITSMC en effectuant les étapes suivantes :

1. [Configurer votre environnement ITSM pour accepter les alertes d’Azure.](./itsmc-connections.md)
1. [Configurer une solution ITSM Azure](./itsmc-definition.md#add-it-service-management-connector)
1. [Configurer le Connecteur ITSM Azure pour votre environnement ITSM.](./itsmc-definition.md#create-an-itsm-connection)
1. [Configurer un groupe d’actions pour tirer parti du Connecteur ITSM.](./itsmc-definition.md#define-a-template)

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes dans le connecteur ITSM](./itsmc-resync-servicenow.md)
