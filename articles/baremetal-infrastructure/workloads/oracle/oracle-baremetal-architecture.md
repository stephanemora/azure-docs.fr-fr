---
title: Architecture de BareMetal Infrastructure pour Oracle
description: Découvrez l’architecture de plusieurs configurations de BareMetal Infrastructure pour Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 1bdc32c14cfc8986c52a4ea916a6130ee29e6028
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558633"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>Architecture de BareMetal Infrastructure pour Oracle

Dans cet article, nous allons examiner les options architecturales pour BareMetal Infrastructure pour Oracle et les fonctionnalités prises en charge par chacune.

## <a name="single-instance"></a>Instance unique

Cette topologie prend en charge une seule instance d’Oracle Database avec Oracle Data Guard pour la migration vers BareMetal Infrastructure. Elle prend en charge l’utilisation d’un nœud de secours pour la haute disponibilité et les tâches de maintenance.

[![Diagramme montrant l’architecture d’une instance unique d’Oracle Database avec Oracle Data Guard.](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Oracle Real Application Clusters (RAC) One Node

Cette topologie prend en charge une configuration RAC avec stockage partagé et cluster GRID. Les instances de base de données s’exécutent sur un seul nœud (configuration actif-passif).

Voici quelques fonctionnalités :

- Actif-passif avec Oracle RAC One Node

    - Basculement automatique

    - Redémarrage rapide sur le second nœud

- Basculement en temps réel et scalabilité avec Oracle RAC

- Maintenance continue sans temps d’arrêt

[![Diagramme montrant l’architecture d’une configuration actif-passif Oracle RAC One Node.](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>Certificat RAC

Cette topologie prend en charge une configuration Oracle RAC avec stockage partagé et cluster Grid, tandis que plusieurs instances par base de données s’exécutent simultanément (configuration actif-passif).

- Les performances sont faciles à mettre à l’échelle grâce au provisionnement en ligne des serveurs ajoutés. 
-  Les utilisateurs sont actifs sur tous les serveurs, et tous les serveurs partagent l’accès à la même base de données Oracle. 
-  Tous les types de maintenance de base de données peuvent être effectués en ligne ou de manière continue, afin de limiter, voire éliminer, les temps d’arrêt. 
- Les systèmes de secours Oracle Active Data Guard (ADG) peuvent aussi facilement servir de systèmes de test. 

Cette configuration vous permet de tester toutes les modifications apportées sur une copie exacte de la base de données de production avant qu’elles soient appliquées à l’environnement de production.

> [!NOTE]
> Si vous envisagez d’utiliser Active Data Guard Far Sync (mode synchrone), vous devez prendre en compte les zones régionales dans lesquelles cette fonctionnalité est prise en charge. Pour les régions géographiques distribuées uniquement, nous vous recommandons d’utiliser Data Guard en mode asynchrone.

[![Diagramme montrant l’architecture d’une configuration actif-actif Oracle RAC.](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Découvrez le provisionnement de vos instances BareMetal pour les charges de travail Oracle.

> [!div class="nextstepaction"]
> [Provisionner BareMetal Infrastructure pour Oracle](oracle-baremetal-provision.md)

