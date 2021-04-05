---
title: Cycle de vie de l’activation et de la désactivation de l’hébergement d’Azure Service Fabric
description: En savoir plus sur le cycle de vie d’une application et d’un ServicePackage sur un nœud.
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831820"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Cycle de vie de l’hébergement d’Azure Service Fabric

Cet article fournit une vue d’ensemble des événements qui se produisent dans Azure Service Fabric quand une application est activée sur un nœud. Il explique les différentes configurations de cluster qui contrôlent le comportement.

Avant de continuer, assurez-vous de bien avoir compris les concepts et relations expliqués dans [Modéliser une application dans Service Fabric][a1]. 

> [!NOTE]
> Cet article utilise certains termes de manière spécialisée. Sauf indication contraire :
>
> - Le terme *réplica* fait référence à la fois au réplica d’un service avec état et à une instance d’un service sans état.
> - Le terme *CodePackage* est traité comme étant équivalent au processus ServiceHost, qui inscrit un ServiceType. Il héberge les réplicas des services de ce ServiceType.
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>Activation d’un ApplicationPackage ou d’un ServicePackage

Pour activer un ApplicationPackage ou un ServicePackage :

1. Téléchargez l’ApplicationPackage (par exemple, *ApplicationManifest.xml*).
2. Configurez un environnement pour l’application. Vos étapes incluent, par exemple, la création d’utilisateurs.
3. Commencez le suivi de l’application pour la désactivation.
4. Téléchargez le ServicePackage (par exemple, *ServiceManifest.xml*, du code, des fichiers config et des packages de données).
5. Configurez un environnement pour le ServicePackage. Vos étapes incluent, par exemple, la configuration d’un pare-feu et l’allocation de ports pour les points de terminaison.
6. Démarrez le suivi du ServicePackage pour la désactivation.
7. Démarrez le SetupEntryPoint pour les CodePackages et attendez qu’il se termine.
8. Démarrez le MainEntryPoint pour les CodePackages.

### <a name="servicetype-blocklisting"></a>Blocage de ServiceType
`ServiceTypeDisableFailureThreshold` détermine le nombre d’échecs d’activation, de téléchargement et de CodePackage autorisés. Une fois le seuil atteint, le ServiceType est planifié pour être bloqué. Le premier échec d’activation, échec de téléchargement ou incident CodePackage planifie le blocage du ServiceType. 

La configuration de `ServiceTypeDisableGraceInterval` détermine l’intervalle de grâce avant que ServiceType soit bloqué sur le nœud. Au cours de ce processus, l’activation, le téléchargement et le redémarrage du CodePackage sont retentés en parallèle. Une nouvelle tentative signifie, par exemple, que le redémarrage du CodePackage est planifié après l’incident ou que Service Fabric réessayera de télécharger les packages.

Quand ServiceType est bloqué, une erreur d’intégrité s’affiche : `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

ServiceType est réactivé sur le nœud si l’un des événements suivants se produit :
- L’activation aboutit. Si elle échoue, elle atteint le nombre maximal `ActivationMaxFailureCount` de nouvelles tentatives.
- Le téléchargement aboutit. S’il échoue, il atteint le nombre maximal `DeploymentMaxFailureCount` de nouvelles tentatives.
- Un CodePackage qui a rencontré un incident démarre et inscrit correctement le ServiceType.

`ActivationMaxFailureCount` et `DeploymentMaxFailureCount` sont le nombre maximal de tentatives que Service Fabric effectue pour activer ou télécharger une application sur un nœud. Une fois le maximum atteint, Service Fabric active à nouveau le ServiceType pour l’activation. 

Ces seuils donnent au service une autre occasion d’être activé. Si l’activation a échoué, le problème est automatiquement corrigé. 

Un réplica nouvellement placé ou activé peut déclencher une nouvelle opération d’activation ou de téléchargement. Cette action aboutira ou déclenchera à nouveau le blocage de ServiceType.

> [!NOTE]
> Un CodePackage défaillant qui n’enregistre pas un ServiceType est sans incidence sur le ServiceType. Seul un CodePackage défaillant qui héberge un réplica affecte le ServiceType.
>

### <a name="codepackage-crash"></a>Plantage de CodePackage
Lorsqu’un CodePackage se plante, Service Fabric utilise une interruption pour le redémarrer. L’interruption est appliquée, que le CodePackage ait ou non inscrit un type avec le runtime de Service Fabric.

La valeur de l’interruption est `Min(RetryTime, ActivationMaxRetryInterval)`. La valeur est incrémentée en quantités constantes, linéaires ou exponentielles en fonction du paramètre de configuration `ActivationRetryBackoffExponentiationBase` :

- **Constante** : Si `ActivationRetryBackoffExponentiationBase == 0`, alors `RetryTime = ActivationRetryBackoffInterval`.
- **Linéaire** :  Si `ActivationRetryBackoffExponentiationBase == 0`, alors `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval`, où `ContinuousFailureCount` est le nombre de fois où un CodePackage rencontre un incident ou ne s’active pas.
- **Exponentielle** : `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)`.
    
Vous pouvez contrôler le comportement en modifiant les valeurs. Par exemple, si vous souhaitez effectuer plusieurs tentatives de redémarrage rapide, vous pouvez utiliser des quantités linéaires en définissant `ActivationRetryBackoffExponentiationBase` sur `0` et `ActivationRetryBackoffInterval` sur `10`. Par conséquent, si un CodePackage se plante, l’intervalle de début commencera après dix secondes. Si le package continue à se planter, l’interruption passe à 20 secondes, 30 secondes, 40 secondes, et ainsi de suite, jusqu’à ce que l’activation du CodePackage réussisse ou que le CodePackage soit désactivé. 
    
La durée maximale pendant laquelle Service Fabric s’interrompt (c’est-à-dire, il attend) après une défaillance est régie par `ActivationMaxRetryInterval`.
    
Si votre CodePackage rencontre un incident et se rétablit, il doit continuer de s’exécuter pendant une période de temps spécifiée par `CodePackageContinuousExitFailureResetInterval`. Après cet intervalle, Service Fabric considère que le CodePackage est sain. Service Fabric remplace alors le rapport d’intégrité d’erreur précédent par OK et réinitialise le `ContinuousFailureCount`.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage n’inscrivant pas ServiceType
Si un CodePackage reste opérationnel et qu’il est supposé inscrire un ServiceType, mais ne le fait pas, Service Fabric génère un rapport d’intégrité d’avertissement après le `ServiceTypeRegistrationTimeout`. Le rapport indique que le ServiceType n’a pas été inscrit dans le délai prévu.

### <a name="activation-failure"></a>Échec de l’activation
Lorsque Service Fabric détecte une erreur pendant l’activation, il utilise toujours une interruption linéaire, comme c’est le cas pour un incident CodePackage. L’opération d’activation abandonne après de nouvelles tentatives à ces intervalles : (0 + 10 + 20 + 30 + 40) = 100 secondes. (La première nouvelle tentative est immédiate.) Après cette séquence, l’activation n’est pas retentée.
    
L’intervalle maximal d’activation peut être `ActivationMaxRetryInterval`. La nouvelle tentative peut être `ActivationMaxFailureCount`.

### <a name="download-failure"></a>Échec du téléchargement
Service Fabric utilise toujours une interruption linéaire lorsqu’il trouve une erreur pendant un téléchargement. L’opération d’activation abandonne après de nouvelles tentatives à ces intervalles : (0 + 10 + 20 + 30 + 40) = 100 secondes. (La première nouvelle tentative est immédiate.) Après cette séquence, le téléchargement n’est pas retenté. 

L’interruption linéaire pour un téléchargement est égale à `ContinuousFailureCount` * `DeploymentRetryBackoffInterval`. L’intervalle maximal peut être `DeploymentMaxRetryInterval`. À l’instar des activations, les opérations de téléchargement peuvent réessayer jusqu’à atteindre la limite de `ActivationMaxFailureCount`.

> [!NOTE]
> Avant de modifier ces paramètres, gardez à l’esprit les exemples suivants :
>
>* Si le CodePackage continue de se planter et de s’interrompre, le ServiceType est désactivé. Cependant, si la configuration d’activation a un redémarrage rapide, le CodePackage peut se trouver opérationnel à quelques reprises avant que le ServiceType soit réellement bloqué. 
>
>    Par exemple, supposons que votre CodePackage soit opérationnel, qu’il inscrive le ServiceType auprès de Service Fabric, puis qu’il plante. Dans ce cas, une fois que l’hébergement a reçu une inscription de type, la période de `ServiceTypeDisableGraceInterval` est annulée. Ce processus peut se répéter jusqu’à ce que votre CodePackage s’interrompe à une valeur supérieure à la période de `ServiceTypeDisableGraceInterval`. Le ServiceType ne sera alors jamais bloqué sur le nœud. Le blocage du ServiceType peut prendre plus de temps que prévu.
>
>* Dans le cas d’activations, lorsqu’un système Service Fabric doit placer un réplica sur un nœud, l’agent de reconfiguration demande au sous-système d’hébergement d’activer l’application. Il réessaie la demande d’activation toutes les 15 secondes. (La durée est régie par le paramètre de configuration `RAPMessageRetryInterval`.) Service Fabric ne peut pas savoir que le ServiceType a été bloqué, sauf si l’opération d’activation de l’hébergement est active pendant une période plus longue que l’intervalle de nouvelle tentative et le `ServiceTypeDisableGraceInterval`. 
>
>    Par exemple, supposons que le `ActivationMaxFailureCount` du cluster est défini sur 5 et que `ActivationRetryBackoffInterval` est défini sur 1 seconde. Dans ce cas, l’opération d’activation abandonne après des intervalles de (0 + 1 + 2 + 3 + 4) = 10 secondes. (La première nouvelle tentative est immédiate.) Après cette séquence, l’hébergement abandonne toute nouvelle tentative. L’opération d’activation se termine et ne fait pas de nouvelle tentative après 15 secondes. 
>
>    Service Fabric a épuisé toutes les nouvelles tentatives autorisées dans un délai de 15 secondes. Ainsi, chaque nouvelle tentative de l’agent de reconfiguration crée une nouvelle opération d’activation dans le sous-système d’hébergement et le modèle continue à se répéter. Par conséquent, le ServiceType n’est jamais bloqué sur le nœud. Étant donné que le ServiceType ne sera pas bloqué sur le nœud, le réplica ne sera pas déplacé et essayé sur un nœud différent.
> 

## <a name="deactivation"></a>Désactivation

Quand un ServicePackage est activé sur un nœud, il est suivi pour la désactivation. La désactivation fonctionne de deux manières :

- **Désactivation périodique** :  À chaque `DeactivationScanInterval`, le système recherche les packages de services qui n’ont *jamais* hébergé de réplica et les marque comme étant des candidats à la désactivation.
- **Désactivation ReplicaClose** : Si un réplica est fermé, la désactivation obtient un `DecrementUsageCount`. Le décompte est à 0 lorsque le ServicePackage n’héberge aucun réplica ; le ServicePackage est donc candidat à la désactivation.

Le mode d’activation détermine à quel moment les candidats sont planifiés pour la désactivation. En mode partagé, les candidats à la désactivation sont planifiés après le `DeactivationGraceInterval`. En mode exclusif, ils sont planifiés après le `ExclusiveModeDeactivationGraceInterval`. Si un nouveau placement de réplica se produit dans cet intervalle, la désactivation est annulée. 

Pour plus d’informations, consultez [Mode exclusif et mode partagé][a2].

### <a name="periodic-deactivation"></a>Désactivation périodique
Voici quelques exemples de désactivation périodique :

* **Exemple 1** : Supposons que le désactivateur démarre une analyse à l’instant T (le `DeactivationScanInterval`). Sa recherche suivante se fera à l’instant 2T. Supposons qu’une activation du ServicePackage se soit produite à T+1. Ce ServicePackage n’héberge pas de réplica. il doit donc être désactivé. 

    Pour être un candidat à la désactivation, le ServicePackage ne doit héberger aucun réplica pendant au moins un instant T. Il sera éligible pour la désactivation à 2T+1. Par conséquent, l’analyse à 2T n’identifie pas ce ServicePackage comme candidat à la désactivation. 

    Le cycle de désactivation suivant, 3T, planifie ce ServicePackage pour la désactivation, car le package est désormais dans un état sans réplica pour l’instant T.  

* **Exemple 2 :** Supposons qu’un ServicePackage soit activé à l’instant T-1 et que le désactivateur démarre une analyse à l’instant T. Le ServicePackage n’héberge pas de réplica. À la prochaine analyse, 2T, le ServicePackage sera identifié comme candidat à la désactivation, et sera donc planifié pour la désactivation.  

* **Exemple 3** : Supposons qu’un ServicePackage soit activé à T-1 et que le désactivateur démarre une analyse à l’instant T. Le ServicePackage n’héberge pas encore de réplica. Maintenant, à T+1, un réplica est placé. Autrement dit, l’hébergement obtient un `IncrementUsageCount`, ce qui signifie qu’un réplica est créé. 

    À 2T, ce ServicePackage ne sera pas planifié pour la désactivation. Étant donné que le package contient un réplica, la désactivation suivra la logique de ReplicaClose, comme décrit dans la section suivante de cet article.

* **Exemple 4** : Supposons que votre ServicePackage est de 10 Go. Étant donné que le package est volumineux, le téléchargement sur le nœud prend du temps. Quand une application est activée, le désactivateur effectue le suivi de son cycle de vie. Si le `DeactivationScanInterval` est défini sur une petite valeur, votre ServicePackage pourrait ne pas avoir le temps de s’activer sur le nœud en raison du temps qu’il a fallu pour le télécharger. Pour résoudre ce problème, vous pouvez [télécharger le ServicePackage à l’avance sur le nœud][p1] ou augmenter le `DeactivationScanInterval`. 

> [!NOTE]
> Un ServicePackage peut être désactivé n’importe où entre (`DeactivationScanInterval` à 2 * `DeactivationScanInterval`) + `DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval`. 
>

### <a name="replicaclose-deactivation"></a>Désactivation ReplicaClose

> [!NOTE]
> Cette section fait référence aux paramètres de configuration suivants :
> - **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval** : Temps accordé à un ServicePackage pour héberger un autre réplica s’il en a déjà hébergé un. 
> - **DeactivationScanInterval** : Durée minimale accordée à un ServicePackage pour héberger un réplica s’il n’a *jamais* héberger de réplica, c’est-à-dire s’il n’a pas été utilisé.
>

Le système conserve le nombre de réplicas qu’un ServicePackage contient. Si un ServicePackage contient un réplica et que le réplica est fermé ou arrêté, l’hébergement reçoit un `DecrementUsageCount`. Quand un réplica est ouvert, l’hébergement reçoit un `IncrementUsageCount`. 

La décrémentation indique que le nombre de réplicas hébergés par le ServicePackage a été réduit d’un réplica. Lorsque le décompte tombe à 0, la désactivation du ServicePackage est planifiée. Le délai après lequel il sera désactivé est `DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval`. 

Par exemple, imaginons qu’une décrémentation se produit à T, et que la désactivation du ServicePackage est planifiée à 2T + X (`DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval`). Pendant ce temps, si l’hébergement reçoit un `IncrementUsage` parce qu’un réplica est créé, la désactivation est annulée.

### <a name="ctrl--c"></a>Ctrl + C
Si un ServicePackage passe l’intervalle `DeactivationGraceInterval`/`ExclusiveModeDeactivationGraceInterval` et qu’il n’héberge toujours pas de réplica, la désactivation ne peut pas être annulée. Les CodePackages reçoivent un gestionnaire Ctrl+C. Le pipeline de désactivation doit donc maintenant se terminer pour que le processus soit arrêté. 

Pendant ce temps, si un nouveau réplica pour le même ServicePackage tente d’être placé, il échoue, car le processus ne peut pas passer de la désactivation à l’activation.

## <a name="cluster-configurations"></a>Configurations de cluster

Cette section répertorie les configurations qui ont des valeurs par défaut qui ont une incidence sur l’activation et la désactivation.

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold** : Valeur par défaut : 1. Seuil pour le nombre d’échecs ; une fois ce seuil atteint, FailoverManager reçoit une notification lui indiquant de désactiver le type de service sur le nœud et d’essayer un autre nœud pour le placement.
- **ServiceTypeDisableGraceInterval** : Valeur par défaut : 30 secondes. Intervalle de temps après lequel le type de service peut être désactivé.
- **ServiceTypeRegistrationTimeout** : Valeur par défaut : 300 secondes. Délai d’expiration pour l’inscription du ServiceType auprès de Service Fabric.

### <a name="activation"></a>Activation
- **ActivationRetryBackoffInterval** : Valeur par défaut : 10 secondes. Intervalle d’interruption pour chaque échec d’activation.
- **ActivationMaxFailureCount** : Valeur par défaut : 20. Nombre maximal de nouvelles tentatives d’activation en échec par le système avant abandon. 
- **ActivationRetryBackoffExponentiationBase** : Valeur par défaut : 1,5.
- **ActivationMaxRetryInterval** : Valeur par défaut : 3 600 secondes. Intervalle maximal d’interruption avant nouvelle tentative pour l’activation après des échecs.
- **CodePackageContinuousExitFailureResetInterval** : Valeur par défaut : 300 secondes. Intervalle de délai d’expiration pour réinitialiser le nombre d’échecs de sortie continue pour le CodePackage.

### <a name="download"></a>Téléchargement
- **DeploymentRetryBackoffInterval** : Valeur par défaut : 10. Intervalle d’interruption pour l’échec du déploiement.
- **DeploymentMaxRetryInterval** : Valeur par défaut : 3 600 secondes. Intervalle maximal d’interruption pour le déploiement après des échecs.
- **DeploymentMaxFailureCount** : Valeur par défaut : 20. Le déploiement de l’application sera retenté `DeploymentMaxFailureCount` fois avant l’échec du déploiement de cette application sur le nœud.

### <a name="deactivation"></a>Désactivation
- **DeactivationScanInterval** : Valeur par défaut : 600 secondes. Durée minimale accordée au ServicePackage pour héberger un réplica s’il n’a jamais héberger de réplica (c’est-à-dire s’il n’est pas utilisé).
- **DeactivationGraceInterval** : Valeur par défaut : 60 secondes. Dans un modèle de processus *partagé*, temps accordé à un ServicePackage pour héberger à nouveau un autre réplica après en avoir déjà hébergé un.
- **ExclusiveModeDeactivationGraceInterval** : Valeur par défaut : 1 seconde. Dans un modèle de processus *exclusif*, temps accordé à un ServicePackage pour héberger à nouveau un autre réplica après en avoir déjà hébergé un.

## <a name="next-steps"></a>Étapes suivantes

- [Empaquetez une application][a3] et préparez-la pour le déploiement.
- [Déployer et supprimer des applications][a4] dans PowerShell.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
