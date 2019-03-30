---
title: Planifier un déploiement de cluster Azure Service Fabric | Microsoft Docs
description: En savoir plus sur la planification et préparation pour un déploiement de cluster Service Fabric sur Azure de production.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo
ms.openlocfilehash: 0f3a9010805ec1a18490f6f530f60d7a3c763398
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663236"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planifier et préparer un déploiement de cluster

Planification et préparation pour un déploiement de cluster de production sont très important.  Il existe de nombreux facteurs à prendre en compte.  Cet article vous guide tout au long des étapes de préparation de votre déploiement de cluster.

## <a name="read-the-best-practices-information"></a>Lire les informations de meilleures pratiques
Pour gérer des clusters et des applications Azure Service Fabric avec succès, voici les opérations que nous recommandons vivement de que procéder pour optimiser la fiabilité de votre environnement de production.  Pour plus d’informations, consultez [application Service Fabric et le cluster des meilleures pratiques](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Sélectionnez le système d’exploitation pour le cluster
Service Fabric permet la création de clusters Service Fabric sur toute machine virtuelle ou tout ordinateur exécutant Windows Server ou Linux.  Avant de déployer votre cluster, vous devez choisir le système d’exploitation :  Windows ou Linux.  Chaque nœud (machine virtuelle) dans le cluster s’exécute le même système d’exploitation, vous ne pouvez pas mélanger des machines virtuelles Linux et Windows dans le même cluster.

## <a name="capacity-planning"></a>planification de la capacité
Pour un déploiement de production, la planification de la capacité est une étape importante. Voici quelques éléments à prendre en compte dans le cadre de ce processus.

* Le nombre initial de types de nœuds de votre cluster 
* Les propriétés de chaque type de nœud (taille, nombre d’instances, principales, nombre de machines virtuelles, etc. sur internet.)
* Caractéristiques de fiabilité et de durabilité du cluster

### <a name="select-the-initial-number-of-node-types"></a>Sélectionnez le nombre initial de types de nœuds
Vous devez d’abord déterminer l’utilisation du cluster que vous créez. Quels types d’applications planifiez-vous de déployer dans ce cluster ? Votre application inclut-elle plusieurs services ? Si oui, ces services doivent-ils être publics ou accessibles sur Internet ? Vos services (qui composent votre application) ont-ils des besoins d’infrastructure différents tels qu’une RAM plus volumineuse ou des cycles processeur plus élevés ? Un cluster Service Fabric peut se composer de plusieurs types de nœud : un type de nœud principal et un ou plusieurs types de nœud non principal. Chaque type de nœud est mappé à un groupe de machines virtuelles identiques. Chaque type de nœud peut ensuite faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité. [Propriétés de nœud et contraintes de placement] [ placementconstraints] peut être configuré pour limiter les services spécifiques aux types de nœud spécifique.  Pour plus d’informations, consultez [le nombre de types de nœud votre cluster doit démarrer avec](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Sélectionnez les propriétés de nœud pour chaque type de nœud
Types de nœuds définissent les VM SKU, les nombre et les propriétés des machines virtuelles dans le jeu de mise à l’échelle associée.

La taille minimale des machines virtuelles pour chaque type de nœud est déterminée par le [niveau de durabilité] [ durability] que vous choisissez pour le type de nœud.

Le nombre minimal de machines virtuelles pour le type de nœud principal est déterminé par le [niveau de fiabilité] [ reliability] vous choisissez.

Consultez les recommandations minimales pour [les types de nœud principal](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [des charges de travail avec état sur les types de nœud non principal](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads), et [des charges de travail sans état sur les types de nœud non principal](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads). 

Plus que le nombre minimal de nœuds doit être basée sur le nombre de réplicas des applications/services que vous souhaitez exécuter dans ce type de nœud.  [Planification de capacité pour les applications Service Fabric](service-fabric-capacity-planning.md) vous aide à estimer les ressources que vous avez besoin pour exécuter vos applications. Vous pouvez toujours monter en puissance le cluster ou vers le bas pour ajuster pour modifier la charge de travail application ultérieurement. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Sélectionnez les niveaux de durabilité et de fiabilité pour le cluster
Le niveau de durabilité est utilisé pour indiquer au système les privilèges dont disposent vos machines virtuelles avec l’infrastructure Azure sous-jacente. Dans le type de nœud principal, ce privilège permet à Service Fabric de suspendre toute demande de l’infrastructure au niveau de la machine virtuelle (par exemple, un redémarrage de la machine virtuelle, une réinitialisation de la machine virtuelle ou une migration de machine virtuelle) qui influe sur la configuration requise du quorum pour les services système et vos services avec état. Dans les types de nœuds non principaux, ce privilège permet à Service Fabric de suspendre toute demande de l’infrastructure au niveau de la machine virtuelle (comme le redémarrage de la machine virtuelle, la réinitialisation de la machine virtuelle, la migration de machine virtuelle, etc.) qui influe sur la configuration requise du quorum pour vos services avec état.  Pour les avantages des différents niveaux et des recommandations à quel niveau à utiliser et quand, consultez [les caractéristiques de durabilité du cluster][durability].

Le niveau de fiabilité est utilisé pour définir le nombre de réplicas des services système que vous voulez exécuter dans ce cluster sur le type de nœud principal. Plus le nombre de réplicas est important, plus les services système sont fiables dans votre cluster.  Pour les avantages des différents niveaux et des recommandations à quel niveau à utiliser et quand, consultez [les caractéristiques de fiabilité du cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Activer le proxy inverse et/ou DNS
Les services se connectant entre eux, en général à l’intérieur d’un cluster, peuvent accéder directement aux points de terminaison d’autres services, car les nœuds d’un cluster se trouvent sur le même réseau local. Pour faciliter la connexion entre les services, Service Fabric fournit des services supplémentaires : Un [service DNS](service-fabric-dnsservice.md) et un [service de proxy inverse](service-fabric-reverseproxy.md).  Les deux services peuvent être activées lorsque vous déployez un cluster.

Depuis de nombreux services, en particulier en conteneur services, peut avoir un nom d’URL existant, pouvoir résoudre ces à l’aide du DNS standard protocole (plutôt que le protocole de Service d’affectation de noms) est pratique, surtout dans les scénarios « lift- and -shift » d’application. C’est là précisément qu’intervient le service DNS. Il vous permet de mapper les noms DNS à un nom de service et, par conséquent, de résoudre les adresses IP des points de terminaison.

Le proxy inverse traite des services dans le cluster qui exposent des points de terminaison HTTP (HTTPS inclus). Le proxy inverse simplifie considérablement l’appel d’autres services en fournissant un format URI spécifique.  Le proxy inverse gère également la résolution, connectez-vous et recommencez les étapes requises pour un service de communiquer avec un autre.

## <a name="prepare-for-disaster-recovery"></a>Préparation à la récupération d’urgence
Pour fournir une haute disponibilité, il est essentiel que les services puissent survivre à tous les types d’échecs. Ceci est particulièrement important pour les échecs inattendus et hors de votre contrôle. [Préparer la récupération d’urgence](service-fabric-disaster-recovery.md) décrit certains modes d’échec courants qui pourraient être sinistres si pas modélisés et gérés correctement. Il aborde également les actions à entreprendre si un incident se produit et les atténuations des risques.

## <a name="production-readiness-checklist"></a>Liste de vérification de disponibilité de la production
Votre application et le cluster sont prêts à accepter le trafic de production ? Avant de déployer votre cluster en production, exécuter via le [liste de contrôle de Production readiness](service-fabric-production-readiness-checklist.md). Gardez votre application et le cluster en cours d’exécution sans heurts à travers les éléments dans cette liste de vérification. Nous recommandons vivement tous ces éléments cochés avant de passer en production.

## <a name="next-steps"></a>Étapes suivantes
* [Créer un cluster Service Fabric exécutant Windows](service-fabric-best-practices-overview.md)
* [Créer un cluster Service Fabric exécutant Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster