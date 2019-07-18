---
title: Erreurs de package de code de diagnostic d’Azure Service Fabric| Microsoft Docs
description: Découvrez comment résoudre les erreurs de package de code les plus courantes avec Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 235952388d2c044cc141b3020c67944c4250ea3d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276578"
---
# <a name="diagnose-common-code-package-errors-with-service-fabric"></a>Diagnostiquez les erreurs de package de code courantes avec Service Fabric

Cet article explique ce que cela signifie pour un package de code de terminer de façon inattendue, et fournit des informations sur les causes possibles des codes d’erreur courants, ainsi que les étapes de dépannage qui peuvent potentiellement vous aider à corriger le problème.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Quand est-ce qu’un processus ou conteneur s’arrête de façon inattendue ?

Lorsque Service Fabric reçoit une demande de démarrage d’un package de code, il commence la préparation de l’environnement sur le système local sur la base des options de configuration définies dans l’application et les manifestes de service. Ces préparatifs peuvent inclure la réservation de points de terminaison ou de ressources réseau, la configuration de règles de pare-feu ou la définition de contraintes de gouvernance des ressources. Une fois que l’environnement a été configuré correctement, Service Fabric tente d’apporter le package de code. Cette étape est considérée comme réussie si le système d’exploitation ou le runtime du conteneur signale que le processus ou conteneur a été activé avec succès. Si l’activation a échoué, vous devriez voir un message de contrôle d’intégrité dans SFX indiquant

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Une fois que le package de code a bien été correctement apporté, Service Fabric commence la surveillance de sa durée de vie. À ce stade, un processus ou conteneur peut se terminer à tout moment pour plusieurs raisons. L’initialisation d’une DLL peut échouer, le système d’exploitation peut ne plus avoir d’espace de tas, etc. Si votre package de code s’est arrêté, vous devriez voir un message de contrôle d’intégrité dans SFX indiquant

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Le code de sortie fourni dans ce message de contrôle d’intégrité est le seul indice fourni par le processus/conteneur quant à la raison de son arrêt, et il peut avoir été généré par n’importe quel niveau de la pile. Il est difficile de comprendre si ce code de sortie est lié, par exemple, à une erreur de système d’exploitation ou un problème avec .NET, ou s’il a été déclenché par votre code. Par conséquent, cet article peut être utilisé comme point de départ pour diagnostiquer la source des codes de sortie d’arrêt et les solutions possibles, en gardant à l’esprit que ce sont des solutions générales à des scénarios courants et qu’elles peuvent ne pas s’appliquer à l’erreur que vous voyez.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Comment puis-je savoir si Service Fabric a interrompu mon package de code ?

Service Fabric peut être responsable de l’interruption de votre package de code pour diverses raisons. Par exemple, il peut décider de placer le package de code sur un autre nœud à des fins d’équilibrage de charge. Vous pouvez déterminer que le package de code a été arrêté par Service Fabric si vous voyez un des codes de sortie du tableau suivant :

>[!NOTE]
> Si votre processus/conteneur se termine avec un code de sortie autre que ceux dans le tableau suivant, Service Fabric n’en est pas responsable.

Code de sortie | Description
--------- | -----------
7147 | Ces codes d’erreur indiquent que Service Fabric a arrêté le processus/conteneur de façon appropriée en lui envoyant un signal Ctrl + C.
7148 | Ces codes d’erreur indiquent que Service Fabric a arrêté le processus/conteneur. Parfois, ce code d’erreur peut indiquer que le processus/conteneur n’a pas répondu dans un délai raisonnable après l’envoi d’un signal Ctrl + C, et qu’il a donc dû être arrêté de façon forcée.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Autres codes d’erreur courants et corrections éventuelles

Code de sortie | Valeur hexadécimale | Description courte | Cause racine | Correctif potentiel
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Cette erreur peut potentiellement signifier que la machine était à court d’espace de tas. Cette cause est probable si vous avez un grand nombre de processus appartenant à votre application exécutée sur le nœud. | Si votre programme n’a pas été conçu pour répondre aux signaux Ctrl + C, vous pouvez activer le paramètre « EnableActivateNoWindow » dans le manifeste de cluster. L’activation de ce paramètre signifie que votre package de code ne s’exécutera pas dans une fenêtre de l’interface graphique et ne recevra pas de signaux Ctrl + C, mais cela réduit la quantité d’espace de tas que chaque processus consomme. Si votre package de code a besoin de recevoir les signaux Ctrl + C, vous pouvez augmenter la taille de tas de votre nœud.
3762504530 | 0xe0434352 | N/A | Cette valeur est le code d’erreur pour une exception non gérée dans le code managé (autrement dit, .NET). | Si vous voyez ce code de sortie, cela signifie que votre application déclenche une exception qui est restée non gérée et a terminé le processus. Le débogage des journaux et dumps de votre application doit être la première étape pour déterminer ce qui a provoqué l’erreur.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [diagnostic des autres scénarios courants](service-fabric-diagnostics-common-scenarios.md)
* Pour obtenir une présentation plus détaillée des journaux Azure Monitor et de ce qu’ils proposent, lisez [Présentation des journaux Azure Monitor](../operations-management-suite/operations-management-suite-overview.md).
* Découvrez plus en détail la [création d’alertes](../log-analytics/log-analytics-alerts.md) dans les journaux Azure Monitor pour faciliter la détection et les diagnostics.
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) proposées par les journaux Azure Monitor
