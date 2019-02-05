---
title: Liste de contrôle de l’état préparation à la production d’Azure Service Fabric | Microsoft Docs
description: Préparez votre application Service Fabric et votre cluster de production en suivant les meilleures pratiques.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/10/2018
ms.author: aljo-microsoft
ms.openlocfilehash: 02ad880f3c4a4f5812b60887090c29a0a39f6742
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206967"
---
# <a name="production-readiness-checklist"></a>Liste de vérification de disponibilité de la production

Votre application et le cluster sont prêts à accepter le trafic de production ? Le fait que vous exécutiez et testiez votre application et votre cluster ne signifie pas nécessairement qu’il sont prêts à passer en production. Continuez à exécuter votre application et votre cluster en douceur tout en passant en revue la liste de contrôle suivante. Nous recommandons vivement de vérifier tous ces points. Bien sûr, vous pouvez choisir d’utiliser d’autres solutions pour une ligne (par exemple, vos propres infrastructures de diagnostic).


## <a name="pre-requisites-for-production"></a>Conditions préalables à la production
1. Les [bonnes pratiques en matière de sécurité Azure Service Fabric](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices) sont les suivantes : 
* Utiliser des certificats X.509
* Configurer des stratégies de sécurité
* Configurer SSL pour Azure Service Fabric
* Utiliser la sécurité et l’isolement réseau avec Azure Service Fabric
* Configurer Azure Key Vault pour la sécurité
* Microsoft.Network/loadBalancers - Affecter des utilisateurs aux rôles
* Implémenter la configuration de sécurité de Reliable Actors si vous utilisez le modèle de programmation Actors
2. Pour les clusters comprenant plus de 20 cœurs ou 10 nœuds, créez un type de nœud principal dédié aux services système. Ajoutez des [contraintes de placement](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) pour réserver le type de nœud principal aux services système.
3. Utilisez une référence (SKU) D2v2 ou supérieure pour le type de nœud principal. Il est recommandé de choisir une référence (SKU) avec une capacité de disque dur d’au moins 50 Go.
4. Les clusters de production doivent être [sécurisés](service-fabric-cluster-security.md). Pour un exemple de configuration de cluster sécurisé, regardez ce [modèle de cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Utilisez des noms communs pour les certificats, et évitez d’utiliser des certificats auto-signés.
5. Ajoutez [des contraintes de ressources sur les conteneurs et les services](service-fabric-resource-governance.md), afin qu’ils ne consomment pas plus de 75 % des ressources du nœud. 
6. Comprenez et définissez le [niveau de durabilité](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Le niveau de durabilité Silver ou un niveau supérieur sont recommandés pour les types de nœuds exécutant des charges de travail avec état. Le type de nœud principal doit avoir le niveau de durabilité Silver ou un niveau supérieur.
7. Comprenez et choisissez le [niveau de fiabilité](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) du type de nœud. Une fiabilité de niveau Silver ou d’un niveau supérieur est recommandée.
8. Chargez vos charges de travail et testez-les à l’échelle afin d’identifier les [besoins en capacité](service-fabric-cluster-capacity.md) pour votre cluster. 
9. Vos services et applications sont surveillés, et les journaux des applications sont générés et stockés avec génération d’alertes. Consultez, par exemple, [Ajouter la journalisation à votre application Service Fabric](service-fabric-how-to-diagnostics-log.md) et [Surveiller les conteneurs avec Log Analytics](service-fabric-diagnostics-oms-containers.md).
10. Le cluster est surveillé avec un dispositif de génération d’alertes (par exemple, [Log Analytics](service-fabric-diagnostics-event-analysis-oms.md)). 
11. L’infrastructure de groupe de machines virtuelles identiques sous-jacente est surveillée avec un dispositif de génération d’alertes (par exemple, [Log Analytics](service-fabric-diagnostics-oms-agent.md)).
12. Le cluster a toujours des [certificats primaire et secondaire](service-fabric-cluster-security-update-certs-azure.md) (de sorte que vous n’êtes verrouillé).
13. Conservez des clusters distincts pour le développement, la préproduction et la production. 
14. Les [mises à niveau d’application](service-fabric-application-upgrade.md) et les [mises à niveau de cluster](service-fabric-tutorial-upgrade-cluster.md) sont testées d’abord dans des clusters de développement et de préproduction. 
15. Désactivez les mises à niveau automatiques dans les clusters de production, et activez-les pour les clusters de développement et de préproduction (restaurez au besoin). 
16. Fixez un objectif de point de récupération (RPO) pour votre service, configurez un [processus de récupération d’urgence](service-fabric-disaster-recovery.md), puis testez-le.
17. Planifiez la [mise à l’échelle](service-fabric-cluster-scaling.md) de votre cluster manuellement ou par programmation.
18. Planifiez la [mise à jour corrective](service-fabric-patch-orchestration-application.md) de vos nœuds de cluster. 
19. Établissez un pipeline d’intégration/livraison continue afin que vos dernières modifications soient testées en permanence. Par exemple, en utilisant [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) ou [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
20. Testez vos clusters de développement et de préproduction sous charge avec le [service d’analyse des défaillances](service-fabric-testability-overview.md), et provoquez un [chaos](service-fabric-controlled-chaos.md) contrôlé. 
21. Planifiez la [mise à l’échelle](service-fabric-concepts-scalability.md) de vos applications. 


Si vous utilisez le modèle de programmation Reliable Services ou Reliable Actors de Service Fabric, vous devez cocher les éléments suivants :
22. Mettez à niveau les applications pendant le développement local pour vérifier que votre code de service honore le jeton d’annulation dans la méthode `RunAsync` et ferme les écouteurs de communication personnalisés.
23. Évitez les [pièges courants](service-fabric-work-with-reliable-collections.md) lors de l’utilisation de Collections fiables.
24. Surveillez les compteurs de performances de mémoire CLR .NET lors de l’exécution des tests de charge, et vérifiez la présence de taux élevés de nettoyage de la mémoire ou de croissance de pile d’échappement.
25. Conservez une sauvegarde hors connexion de [Reliable Services et de Reliable Actors](service-fabric-reliable-services-backup-restore.md), puis testez le processus de restauration.
26. Idéalement, le nombre d'instances de machines virtuelles NodeType principales doit être égal au niveau de fiabilité minimum des clusters ; conditions nécessaires pour dépasser le niveau minimum : temporairement, lors de la mise à l'échelle verticale de la référence SKU du groupe principal de machines virtuelles identiques NodeType.

## <a name="optional-best-practices"></a>Meilleures pratiques facultatives

Si les listes ci-dessus sont des conditions préalables à la mise en production, les éléments suivants doivent également être pris en considération :
26. Connectez-vous au [modèle d’intégrité Service Fabric](service-fabric-health-introduction.md) pour étendre l’évaluation d’intégrité et les rapports intégrés.
27. Déployez une surveillance personnalisée qui analyse votre application et rende compte de la [charge](service-fabric-cluster-resource-manager-metrics.md) pour [l’équilibrage des ressources](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Étapes suivantes
* [Déployez un cluster Windows Service Fabric](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Déployez un cluster Linux Service Fabric](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* En savoir plus sur le [cycle de vie des applications](service-fabric-application-lifecycle.md) Service Fabric.
