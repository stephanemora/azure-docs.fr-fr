---
title: "Mise à niveau d'une application : paramètres de mise à niveau | Microsoft Docs"
description: Décrit les paramètres relatifs à la mise à niveau d'une application Service Fabric, y compris les vérifications d'intégrité à effectuer et les stratégies pour annuler automatiquement la mise à niveau.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/17/2018
ms.author: subramar
ms.openlocfilehash: 3f321775ba112471760e627e6b43ed17ff8c5b6b
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182873"
---
# <a name="application-upgrade-parameters"></a>Paramètres de mise à niveau d'application
Cet article décrit les différents paramètres qui s’appliquent pendant la mise à niveau d’une application Azure Service Fabric. Les paramètres de mise à niveau d’application permettent de contrôler les délais d’attente et les vérifications d’intégrité appliqués au cours de la mise à niveau et de spécifier les stratégies à appliquer quand une mise à niveau échoue.

Les paramètres d’application s’appliquent aux mises à niveau avec PowerShell ou Visual Studio. Les paramètres obligatoires et facultatifs applicables à PowerShell et/ou Visual Studio sont décrits dans les tableaux Paramètres obligatoires et Paramètres facultatifs comme suit.

Les mises à niveau d’application sont lancées via un des trois modes de mise à niveau sélectionnables par l’utilisateur. Chaque mode a son propre ensemble de paramètres d’application :
- Surveillé
- Automatique non surveillé
- Manuel non surveillé

Les mises à niveau d’application Service Fabric avec PowerShell utilisent la commande [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade). Le mode de mise à niveau est sélectionné en passant le paramètre **Monitored**, **UnmonitoredAuto** ou **UnmonitoredManual** à [ Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

Les paramètres de mise à niveau d’application Service Fabric de Visual Studio sont définis via la boîte de dialogue Paramètres de mise à niveau de Visual Studio. Le mode de mise à niveau de Visual Studio est sélectionné via la zone de liste déroulante **Mode de mise à niveau** et il peut être **Monitored**, **UnmonitoredAuto** ou **UnmonitoredManual** . Pour plus d’informations, consultez [Configurer la mise à niveau d’une application Service Fabric](service-fabric-visualstudio-configure-upgrade.md).

## <a name="required-parameters"></a>Paramètres obligatoires
(PS=PowerShell, VS=Visual Studio)

| Paramètre | S'applique à | Description |
| --- | --- | --- |
ApplicationName |PS| Nom de l'application en cours de mise à niveau. Exemples : fabric:/VisualObjects, fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|Version du type d'application ciblée par la mise à niveau. |
FailureAction |PS, VS|Les valeurs autorisées sont **Rollback**, **Manual** et **Invalid**. Action de compensation à effectuer quand une mise à niveau de type *Surveillé* détecte des violations de stratégie de surveillance ou de stratégie d’intégrité. <br>**Rollback** spécifie que la mise à niveau est annulée et revient automatiquement à la version d’avant la mise à niveau. <br>**Manual** indique que la mise à niveau passera au mode de mise à niveau *UnmonitoredManual*. <br>**Invalid** indique que l’action en cas d’échec n’est pas valide.|
Surveillé |PS|Indique que le mode de mise à niveau est surveillé. Une fois que l’applet de commande termine une mise à niveau pour un domaine de mise à niveau, si l’intégrité du domaine de mise à niveau et le cluster sont conformes aux stratégies de contrôle d’intégrité que vous définissez, Service Fabric met à niveau le domaine de mise à niveau suivant. Si le domaine de mise à niveau ou le cluster ne répond pas aux stratégies d’intégrité, la mise à niveau échoue, et Service Fabric annule la mise à niveau pour le domaine de mise à niveau ou revient au mode manuel, selon la stratégie spécifiée. Il s’agit du mode recommandé pour les mises à niveau d’application dans un environnement de production. |
UpgradeMode | VS | Les valeurs autorisées sont **Monitored** (valeur par défaut), **UnmonitoredAuto** ou **UnmonitoredManual**. Pour plus d’informations, reportez-vous aux paramètres de PowerShell pour chaque mode dans cet article. |
UnmonitoredAuto | PS | Indique que le mode de mise à niveau est Automatique non surveillé. Une fois que Service Fabric a mis à niveau un domaine de mise à niveau, il met à niveau le domaine de mise à niveau suivant, quel que soit l’état d’intégrité de l’application. Ce mode n’est pas recommandé pour la production, et il est pratique seulement pendant le développement d’une application. |
UnmonitoredManual | PS | Indique que le mode de mise à niveau est Manuel non surveillé. Une fois que Service Fabric a mis à niveau un domaine de mise à niveau, il attend que vous mettiez à niveau le domaine de mise à niveau suivant avec l’applet de commande *Resume-ServiceFabricApplicationUpgrade*. |

## <a name="optional-parameters"></a>Paramètres facultatifs

Les paramètres d’évaluation de l’intégrité sont facultatifs. Si les critères d’évaluation d’intégrité ne sont pas spécifiés au démarrage d’une mise à niveau, Service Fabric utilise les stratégies de vérification d’intégrité d’application définies dans le fichier ApplicationManifest.xml de l’instance d’application.

Utilisez la barre de défilement horizontal en bas du tableau pour voir le champ de description complet.

(PS=PowerShell, VS=Visual Studio)

| Paramètre | S'applique à | Description |
| --- | --- | --- |
| ApplicationParameter |PS, VS| Spécifie les remplacements pour les paramètres d’application.<br>Les paramètres d’applications de PowerShell sont spécifiés sous forme de paires nom/valeur d’une table de hachage. Par exemple : @{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" }.<br>Les paramètres d’application de Visual Studio peuvent être spécifiés dans la boîte de dialogue Publier une application Service Fabric dans le champ **Fichier de paramètres d’application**.
| Confirmer |PS| Les valeurs autorisées sont **True** et **False**. Demande confirmation avant d’exécuter l’applet de commande. |
| ConsiderWarningAsError |PS, VS |Les valeurs autorisées sont **True** et **False**. La valeur par défaut est **False**. Traiter les événements d’avertissement d’intégrité de l’application comme des erreurs pendant l’évaluation de l’intégrité de l’application au cours de la mise à niveau. Par défaut, Service Fabric ne considère pas les événements d’avertissement d’intégrité comme des échecs (erreurs) ; la mise à niveau peut donc continuer même si des événements d’avertissement se produisent. |
| DefaultServiceTypeHealthPolicy | PS, VS |Spécifie la stratégie d’intégrité pour le type de service par défaut à utiliser pour la mise à niveau surveillée dans le format MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Par exemple, « 5,10,15 » indique les valeurs suivantes : MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
| Force | PS, VS | Les valeurs autorisées sont **True** et **False**. Indique que le processus de mise à niveau ignore le message d’avertissement et force la mise à niveau, même si le numéro de version n’a pas changé. Ceci est pratique pour un test local, mais n’est pas recommandé pour une utilisation dans un environnement de production car il nécessite la suppression du déploiement existant, ce qui un temps d’arrêt et une perte de données de temps potentielle. |
| ForceRestart |PS, VS |Si vous mettez à jour un package de configuration ou de données sans mettre à jour le code du service, le service est redémarré seulement si la propriété ForceRestart est définie sur **True**. Quand la mise à jour est terminée, Service Fabric indique au service qu'un nouveau package de configuration ou de données est disponible. Le service est chargé d'appliquer les modifications. Si nécessaire, le service peut redémarrer automatiquement. |
| HealthCheckRetryTimeoutSec |PS, VS |Durée (en secondes) pendant laquelle Service Fabric continue d'évaluer l'intégrité avant de déclarer la mise à niveau comme ayant échoué. La valeur par défaut est 600 secondes. Cette durée commence une fois que *HealthCheckWaitDuration* est atteinte. Dans le délai imparti par *HealthCheckRetryTimeout*, Service Fabric peut effectuer plusieurs vérifications d’intégrité de l’application. La valeur par défaut est 10 minutes, mais vous devez l’adapter à votre application. |
| HealthCheckStableDurationSec |PS, VS |Durée (en secondes) nécessaire pour vérifier que l’application est stable avant de passer au domaine de mise à niveau suivant ou de terminer la mise à niveau. Cette durée d'attente facilite la détection des modifications d'intégrité qui pourraient avoir lieu juste après la fin de la vérification d'intégrité. La valeur par défaut est 120 secondes, mais vous devez l’adapter à votre application. |
| HealthCheckWaitDurationSec |PS, VS | Délai d’attente (en secondes) après la fin de la mise à niveau sur le domaine de mise à niveau au terme duquel Service Fabric évalue l’intégrité de l’application. Cette durée peut également être considérée comme le temps pendant lequel une application doit s'exécuter pour être estimée saine. Si la vérification d'intégrité réussit, le processus de mise à niveau se poursuit sur le domaine de mise à niveau suivant.  Si la vérification d’intégrité échoue, Service Fabric attend pendant un intervalle de temps [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) avant de réessayer la vérification, jusqu’à ce que délai imparti par *HealthCheckRetryTimeout* soit atteint. La valeur par défaut et recommandée est 0 seconde. |
| MaxPercentUnhealthyDeployedApplications|PS, VS |La valeur par défaut et recommandée est 0. Nombre maximal d’applications déployées (voir la [section Intégrité](service-fabric-health-introduction.md)) pouvant être défectueuses avant que l’application ne soit considérée comme défectueuse et que sa mise à niveau échoue. Ce paramètre définit l’intégrité de l’application sur le nœud et vous aide à détecter les problèmes lors de la mise à niveau. En général, les réplicas de l’application font l’objet d’un équilibrage de la charge sur l’autre nœud, ce qui rend l’application intègre et permet de poursuivre la mise à niveau. En spécifiant une intégrité *MaxPercentUnhealthyDeployedApplications* stricte, Service Fabric peut détecter rapidement un problème avec le package d’application et effectuer une mise à niveau en mode Fail-fast. |
| MaxPercentUnhealthyServices |PS, VS |Un paramètre pour *DefaultServiceTypeHealthPolicy* et *ServiceTypeHealthPolicyMap*. La valeur par défaut et recommandée est 0. Nombre maximal de services dans l’instance d’application pouvant être défectueux avant que l’application ne soit considérée comme défectueuse et que sa mise à niveau échoue. |
| MaxPercentUnhealthyPartitionsPerService|PS, VS |La valeur par défaut et recommandée est 0. Nombre maximal de partitions dans un service pouvant être défectueuses avant que le service ne soit considéré comme défectueux. |
| MaxPercentUnhealthyReplicasPerPartition|PS, VS |La valeur par défaut et recommandée est 0. Nombre maximal de réplicas dans une partition pouvant être défectueux avant que la partition ne soit considérée comme défectueuse. |
| ServiceTypeHealthPolicyMap | PS, VS | Représente la stratégie de contrôle d’intégrité utilisée pour évaluer l’intégrité des services appartenant à un type de service. Prend une entrée de table de hachage au format suivant : @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} Par exemple : @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
| TimeoutSec | PS, VS | Spécifie le délai d’attente en secondes pour l’opération. |
| UpgradeDomainTimeoutSec |PS, VS |Durée maximale (en secondes) pour mettre à niveau un seul domaine de mise à niveau. Si ce délai est atteint, la mise à niveau s’arrête et effectue l’action spécifiée par *FailureAction*. La valeur par défaut est « never » (Infinie), mais vous devez l’adapter à votre application. |
| UpgradeReplicaSetCheckTimeoutSec |PS, VS |**Service sans état**: dans un même domaine de mise à niveau, Service Fabric tente de s’assurer que des instances supplémentaires du service sont disponibles. Si le nombre d’instances cibles est supérieur à un, Service Fabric attend que plusieurs instances soient disponibles, jusqu’à ce que la valeur de délai maximal soit atteinte. Ce délai d’attente est spécifié avec la propriété *UpgradeReplicaSetCheckTimeout*. Si le délai expire, Service Fabric poursuit la mise à niveau, quel que soit le nombre d’instances de service. Si le nombre d'instances cibles est un, Service Fabric n'attend pas et procède immédiatement à la mise à niveau.<br><br>**Service avec état**: dans un même domaine de mise à niveau, Service Fabric tente de s’assurer que le jeu de réplicas a un quorum. Service Fabric attend qu’un quorum soit disponible, jusqu’à ce que soit atteinte la valeur de délai maximal (spécifiée par la propriété *UpgradeReplicaSetCheckTimeoutSec*). Si le délai expire, Service Fabric poursuit la mise à niveau, indépendamment du quorum. Ce paramètre est défini sur « never » (infini) pour une restauration par progression, et sur 1 200 secondes pour une restauration. |
| UpgradeTimeoutSec |PS, VS |Délai (en secondes) qui s’applique à l’ensemble de la mise à niveau. Si ce délai d’attente est atteint, la mise à niveau s’arrête et *FailureAction* est déclenchée. La valeur par défaut est « never » (Infinie), mais vous devez l’adapter à votre application. |
| WhatIf | PS | Les valeurs autorisées sont **True** et **False**. Montre ce qui se passe en cas d’exécution de l’applet de commande. L’applet de commande n’est pas exécutée. |

Les critères *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService* et *MaxPercentUnhealthyReplicasPerPartition* peuvent être spécifiés par type de service pour une instance d’application. La configuration de ces paramètres par service permet à une application de contenir différents types de services avec des stratégies d’évaluation différentes. Par exemple, pour une instance d’application donnée, un type de service de passerelle sans état et un type de service de moteur avec état peuvent avoir des valeurs différentes pour *MaxPercentUnhealthyPartitionsPerService*.

## <a name="next-steps"></a>Étapes suivantes
[mise à niveau de votre application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l’application à l’aide de Visual Studio.

[mise à niveau de votre application à l’aide de PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide à travers une mise à niveau de l’application à l’aide de PowerShell.

[Mise à niveau de votre application en utilisant l’interface CLI Service Fabric sur Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) vous guide tout au long des étapes de mise à niveau de l’application avec l’interface CLI Service Fabric.

[Mise à niveau de votre application avec le plug-in Eclipse Service Fabric](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Rendez les mises à niveau de votre application compatibles en apprenant à utilisez la [Sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Apprenez à utiliser les fonctionnalités avancées lors de la mise à niveau de votre application en consultant les [Rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résolvez les problèmes courants de mise à niveau de l’application en vous reportant aux étapes de [Résolution des problèmes de mise à niveau des applications](service-fabric-application-upgrade-troubleshooting.md).
