---
title: Introduire de lancement de l’intégration d’intégrité pour le Gestionnaire de déploiement Azure
description: Explique comment déployer un service sur plusieurs régions avec Azure Deployment Manager. Montre des pratiques de déploiement sécurisé consistant à vérifier la stabilité d’un déploiement avant sa propagation à toutes les régions.
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/19
ms.author: jgao
ms.openlocfilehash: 1006cc902cf4b6f763d86165a039a1fec5dc97a1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467084"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introduire de lancement de l’intégration d’intégrité Azure Deployment Manager (version préliminaire publique)

[Azure Deployment Manager](./deployment-manager-overview.md) vous permet d’effectuer les lancements intermédiaires de ressources Azure Resource Manager. Les ressources sont déployées région par région de manière ordonnée. La vérification d’intégrité intégrés de Azure Deployment Manager peut surveiller déploiements et automatiquement déploiements problématique stop, afin que vous puissiez dépanner et réduire l’échelle de l’impact. Cette fonctionnalité peut réduire l’indisponibilité du service a provoqué par des régressions dans les mises à jour.

## <a name="health-monitoring-providers"></a>Fournisseurs de contrôle d’intégrité

Afin de faciliter l’intégration de l’intégrité que possible, Microsoft a travaillé avec certains de l’intégrité de service supérieur surveillance entreprises pour vous fournir une solution simple de copier/coller à intégrer des contrôles d’intégrité à vos déploiements. Si vous n’utilisez pas déjà un analyseur d’intégrité, il s’agit des solutions intéressantes pour commencer :

| ![déploiement d’Azure manager d’intégrité analyse fournisseur datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![déploiement d’Azure manager d’intégrité analyse fournisseur site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![déploiement d’Azure manager d’intégrité analyse fournisseur wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, le début de la surveillance et plateforme d’analytique pour les environnements de cloud modernes. Consultez [comment Datadog s’intègre avec Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, solution de surveillance des services du cloud privé et public de tout-en-un. Consultez [comment Site24x7 s’intègre avec Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Wavefront, la plateforme de surveillance et d’analytique pour les environnements d’application cloud multiples. Consultez [comment Wavefront s’intègre avec Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Détermination de l’intégrité du service

[Fournisseurs de contrôle d’intégrité](#health-monitoring-providers) offrent plusieurs mécanismes pour la surveillance des services et vous alerter de tout problème d’intégrité de service. [Azure Monitor](/services/monitor/) est un exemple d’une telle offre. Azure Monitor peut être utilisé pour créer des alertes lorsque certains seuils sont dépassés. Par exemple, votre mémoire et l’utilisation du processeur grimper au-delà des niveaux attendus lorsque vous déployez une nouvelle mise à jour à votre service. Lors de la notification, vous pouvez effectuer les actions correctives.

Ces fournisseurs de contrôle d’intégrité offrent généralement les API REST afin que l’état des analyses de votre service peut être examiné par programmation. Les API REST peut revenir soit avec un signal intègres/défectueux simple (déterminé par le code de réponse HTTP), et/ou avec des informations détaillées sur les signaux qu’il reçoit.

La nouvelle *healthCheck* étape dans le Gestionnaire de déploiement Azure vous permet de déclarer les codes HTTP qui indiquent un service sain, ou pour obtenir des résultats plus complexes sur REST, vous pouvez même spécifier des expressions régulières qui, si elles correspondent, indiquent un état de fonctionnement normal réponse.

Le flux pour obtenir le programme d’installation avec les contrôles d’intégrité Azure Deployment Manager :

1. Créez vos moniteurs d’intégrité via un fournisseur de services de santé de votre choix.
1. Créer une ou plusieurs étapes healthCheck dans le cadre de votre déploiement Azure Deployment Manager. Remplissez les étapes healthCheck avec les informations suivantes :

    1. L’URI de l’API REST pour l’intégrité de votre contrôle (tel que défini par votre fournisseur de services de santé).
    1. Informations d’authentification. Actuellement uniquement l’authentification de style de clé API est prise en charge.
    1. [Codes d’état HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) ou des expressions régulières qui définissent une réponse correcte. Notez que vous pouvez fournir des expressions régulières, qui doivent tous les correspondance pour la réponse pour être considéré comme sain, ou peut fournir des expressions dont les doivent correspondre pour que la réponse être considéré comme sain. Les deux méthodes sont prises en charge.

    Le code Json suivant est un exemple :

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Appeler la procédure healthCheck au moment opportun dans votre déploiement Azure Deployment Manager. Dans l’exemple suivant, une étape de vérification d’intégrité est appelée dans **postDeploymentSteps** de **stepGroup2**.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Pour étudier un exemple, consultez [didacticiel : Utilisation de contrôle d’intégrité dans le Gestionnaire de déploiement Azure](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Phases d’un contrôle d’intégrité

À ce stade Azure Deployment Manager sait comment interroger l’intégrité de votre service et à ce que phases dans votre lancement à le faire. Toutefois, le Gestionnaire de déploiement Azure ainsi que des configuration approfondie de la synchronisation de ces vérifications. Une étape healthCheck est exécutée en 3 phases séquentiels, qui ont une durée configurable : 

1. Attendre

    1. Une fois une opération de déploiement est terminée, peuvent être le redémarrage de machines virtuelles, reconfiguration basé sur les nouvelles données, ou encore en cours de démarrage pour la première fois. Il prend également le temps aux services pour commencer à émettre des signaux d’intégrité doivent être regroupés par l’intégrité de fournisseur de surveillance en quelque chose d’utile. Pendant ce processus tumultueuse, il ne peut pas être judicieux pour vérifier l’intégrité du service dans la mesure où la mise à jour n’a pas encore atteint un état stable. En effet, le service peut être modifiée à plusieurs reprises entre les États corrects et incorrects comme régler les ressources. 
    1. Pendant la phase d’attente, l’intégrité du service n’est pas surveillé. Cela est utilisé pour autoriser les ressources déployées le temps d’intégrer l’avant de commencer le processus de vérification d’intégrité. 
1. Elastic

    1. Dans la mesure où il est impossible de savoir dans combien de cas toutes les ressources prendra de gâteaux avant qu’ils deviennent stables, permet à la phase élastique pour une période flexible entre lorsque les ressources sont potentiellement instables et s’ils sont requis pour maintenir un équilibre sain état.
    1. Au commencement de la phase élastique, Azure Deployment Manager commence à rechercher le point de terminaison REST fourni pour l’intégrité du service régulièrement. L’intervalle d’interrogation est configurable. 
    1. Si le moniteur d’intégrité est renvoyée avec les signaux indiquant que le service n’est pas intègre, ces signaux est ignorées, la phase élastique continue et continue de l’interrogation. 
    1. Dès que le moniteur d’intégrité est renvoyée avec les signaux indiquant que le service est intègre, la phase élastique se termine et la phase HealthyState commence. 
    1. Par conséquent, la durée spécifiée pour la phase élastique est la quantité maximale de temps qui peut être passé d’interrogation pour l’intégrité du service avant qu’une réponse correcte est considéré comme obligatoire. 
1. HealthyState

    1. Pendant la phase HealthyState, l’intégrité du service est interrogé en permanence à la même fréquence que la phase élastique. 
    1. Le service est prévu pour maintenir les signaux sains à partir du fournisseur de contrôle d’intégrité pendant toute la durée spécifiée. 
    1. Si à tout moment qu'une réponse défectueuse est détectée, Azure Deployment Manager arrête le déploiement entier et retourner la réponse REST portant les signaux de service défectueux.
    1. Une fois que la durée de HealthyState s’est terminée, le healthCheck est terminée, et déploiement se poursuit à l’étape suivante.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à apprendre à intégrer la surveillance de l’intégrité dans le Gestionnaire de déploiement Azure. Passez à l’article suivant pour apprendre à déployer avec Deployment Manager.

> [!div class="nextstepaction"]
> [Didacticiel : intégrer le contrôle d’intégrité dans le Gestionnaire de déploiement Azure](./deployment-manager-tutorial-health-check.md)