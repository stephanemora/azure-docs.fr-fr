---
title: Comment utiliser l’agent Java In-process d’Application Insights dans Azure Spring Cloud
description: Comment surveiller les applications et les microservices à l’aide de l’agent Java In-process d’Application Insights dans Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f6f1ed1a3e09397a720dfd4d842b79cd88aa738d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877387"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Agent In-process Java d’Application Insights dans Azure Spring Cloud (préversion)

Ce document explique comment surveiller les applications et les microservices à l’aide de l’agent Java Application Insights dans Azure Spring Cloud. 

À l’aide de cette fonctionnalité, vous pouvez :

* Lancer des recherches dans des données de suivi avec différents filtres
* Voir la carte des dépendances des microservices.
* Vérifier les performances des requêtes.
* Surveiller les métriques dynamiques en temps réel.
* Vérifier les échecs des requêtes.
* Vérifier les métriques de l’application.

Application Insights fournit de nombreuses perspectives observables, notamment :

* Mise en correspondance d’applications
* Performances
* Échecs
* Mesures
* Métriques temps réel
* Disponibilité

## <a name="enable-java-in-process-agent-for-application-insights"></a>Activation de l’agent Java In-process pour Application Insights

Activez la fonctionnalité en préversion de l’agent Java In-process à l’aide de la procédure suivante.

1. Accédez à la page de la vue d’ensemble du service de votre instance de service.
2. Cliquez sur l’entrée **Application Insights** dans le panneau de surveillance.
3. Cliquez sur le bouton **Activer Application Insights** pour activer l’intégration d’**Application Insights**.
4. Sélectionnez une instance d’Application Insights ou créez-en une.
5. Cliquez sur **Activer l’agent Java In-process** pour activer la préversion de la fonctionnalité de l’agent Java In-process. Ici, vous pouvez également personnaliser le taux d’échantillonnage de 0 à 100.
6.  Cliquez sur **Enregistrer** pour enregistrer la modification.

## <a name="portal"></a>Portail

1. Accédez à la page **Service | Vue d’ensemble** et sélectionnez **Application Insights** dans la section **Supervision**. 
2. Cliquez sur **Activer Application Insights** pour activer Application Insights dans Azure Spring Cloud.
3. Cliquez sur **Activer l’agent Java In-process** pour activer la préversion de la fonctionnalité de l’agent Java In-process. Quand la préversion de la fonctionnalité d’agent In-Process est activée, vous pouvez configurer un taux d’échantillonnage facultatif (par défaut 10,0 %).

  [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Utilisation de la fonctionnalité Application Insights

Lorsque la fonctionnalité **Application Insights** est activée, vous pouvez :

Dans le volet de navigation de gauche, cliquez sur **Application Insights** pour accéder à la page **Vue d’ensemble** d’Application Insights. 

* Cliquez sur **Plan de l’application** pour afficher l’état des appels entre les applications.

  [ ![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Cliquez sur le lien entre le service client et `petclinic` pour voir plus de détails, par exemple une requête SQL.

* Dans le volet de navigation de gauche, cliquez sur **Performances** pour voir les données de performances de toutes les opérations des applications, ainsi que les dépendances et les rôles.

  [ ![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* Dans le volet de navigation de gauche, cliquez sur **Échecs** pour voir s’il s’agit d’un événement inattendu pour vos applications.

  [ ![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* Dans le volet de navigation de gauche, cliquez sur **Métriques** et sélectionnez l’espace de noms. Vous verrez les métriques Spring Boot et les métriques personnalisées, le cas échéant.

  [ ![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* Dans le volet de navigation de gauche, cliquez sur **Métriques en temps réel** pour voir les métriques en temps réel de différentes dimensions.

  [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* Dans le volet de navigation de gauche, cliquez sur **Disponibilité** pour surveiller la disponibilité et la réactivité des applications Web en créant des [tests de disponibilité dans Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

  [ ![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>Modèle ARM
Pour utiliser le modèle Azure Resource Manager, copiez le contenu suivant sur `azuredeploy.json`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>Interface de ligne de commande
Appliquer le modèle ARM avec la commande CLI :

* Pour une instance Azure Spring Cloud existante :

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* Pour une nouvelle instance Azure Spring Cloud :

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* Pour désactiver App-Insight :

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>Voir aussi
* [Utiliser le suivi distribué avec Azure Spring Cloud](spring-cloud-howto-distributed-tracing.md)
* [Analyser les journaux et les métriques](diagnostic-services.md)
* [Effectuer le streaming des journaux en temps réel](spring-cloud-howto-log-streaming.md)