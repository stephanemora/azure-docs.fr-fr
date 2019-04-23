---
title: Solutions de partenaires pour la surveillance d’Azure Service Fabric | Microsoft Docs
description: Découvrez comment surveiller Azure Service Fabric avec des solutions de surveillance proposées par des partenaires.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: c2f953c98e41291951f07556bd0cd441d2793d1d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796241"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Solutions de partenaires pour la surveillance d’Azure Service Fabric

Cet article explique comment surveiller son infrastructure, ses clusters et ses applications Service Fabric avec quelques solutions de partenaires. Nous avons collaboré avec chacun des partenaires cités ci-dessous pour créer des offres intégrées pour Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Notre intégration à Dynatrace fournit de nombreuses fonctionnalités prêtes à l’emploi pour surveiller vos clusters Service Fabric. L’installation de Dynatrace OneAgent sur vos instances VMSS vous donne accès à des compteurs de performances et vous fournit une topologie de votre déploiement Service Fabric vers le niveau application. Dynatrace constitue également un excellent choix pour la surveillance sur site. Découvrez plus de fonctionnalités répertoriées dans le [annonce](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) et [instructions](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) pour activer Dynatrace sur votre cluster. 

## <a name="datadog"></a>Datadog

Datadog compte une extension pour VMSS pour les instances Windows et Linux. À l’aide de Datadog, vous pouvez collecter les journaux des événements Windows et collecter ainsi des événements de la plateforme Service Fabric sur Windows. Consultez les instructions relatives à l’envoi de vos données de diagnostic à Datadog en cliquant [ici](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

L’intégration de Service Fabric à AppDynamics se fait au niveau application. En mettant à jour des variables d’environnement et en utilisant des packages NuGet AppDynamics, vous pouvez envoyer des données de télémétrie d’application à AppDynamics. Reportez-vous à ces [instructions](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) pour apprendre à intégrer vos applications .NET Service Fabric à AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic est un autre outil de gestion des performances d’applications qui s’intègre parfaitement aux applications Service Fabric. Vous pouvez installer les packages NuGet New Relic et ajouter des variables d’environnement spécifiques dans vos fichiers manifeste pour envoyer vos données de télémétrie d’application à New Relic. Découvrez ces [instructions](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) pour activer les données de télémétrie de New Relic pour vos applications .NET Service Fabric.

## <a name="elk"></a>ELK 

La pile ELK est une collection de technologies Open Source : Elasticsearch, Logstash et Kibana. En les utilisant combinées, vous pouvez collecter, stocker et analyser les données de surveillance et de diagnostic Service Fabric. Nous proposons [ici](service-fabric-tutorial-java-elk.md) un didacticiel pour apprendre à effectuer cette opération avec des applications Java natives de Service Fabric. 

## <a name="humio"></a>Humio

Humio est un service de collecte de journaux capable de rassembler des journaux à partir de vos applications et les événements à partir de Service Fabric dans le cloud ou en local en temps réel. En plus de l’observabilité en direct, Humio propose des fonctionnalités d’analyse et la visualisation de pointe pour l’affichage et la collecte des informations à partir de vos diagnostics. Humio a des plans de tarification économiques et est intégrée à l’échelle tout en conservant il est éclaircissement vitesse rapide. Il s’intègre directement avec les événements de la plateforme Service Fabric et la télémétrie applicative. Vous trouverez plus d’informations sur l’intégration Humio et Service Fabric [ici](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Étapes suivantes

* Obtenir une [présentation de la surveillance et des diagnostics](service-fabric-diagnostics-overview.md) dans Service Fabric
* Découvrir comment [diagnostiquer des scénarios courants](service-fabric-diagnostics-common-scenarios.md) avec nos outils internes
