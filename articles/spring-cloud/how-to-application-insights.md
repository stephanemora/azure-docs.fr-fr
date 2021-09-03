---
title: Comment utiliser l’agent Java In-process d’Application Insights dans Azure Spring Cloud
description: Comment surveiller les applications et les microservices à l’aide de l’agent Java In-process d’Application Insights dans Azure Spring Cloud.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1505837a316943c2d22f82a0107bb7a1990e0e83
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525455"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Agent In-process Java d’Application Insights dans Azure Spring Cloud (préversion)

Cet article explique comment surveiller les applications et les microservices en utilisant l’agent Java d’Application Insights dans Azure Spring Cloud.

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

> [!NOTE]
> Cette fonctionnalité d’évaluation n’est pas prise en charge dans Mooncake et dans les nouvelles régions telles que l’UAE.

## <a name="enable-java-in-process-agent-for-application-insights"></a>Activation de l’agent Java In-process pour Application Insights

Activez la fonctionnalité en préversion de l’agent Java In-process à l’aide de la procédure suivante.

1. Accédez à la page de la vue d’ensemble du service de votre instance de service.
2. Sélectionnez l’entrée **Application Insights** dans le panneau de surveillance.
3. Sélectionnez le bouton **Activer Application Insights** pour activer l’intégration d’**Application Insights**.
4. Sélectionnez une instance d’Application Insights ou créez-en une.
5. Sélectionnez **Activer l’agent Java In-process** pour activer la préversion de la fonctionnalité de l’agent Java In-process. Ici, vous pouvez également personnaliser le taux d’échantillonnage de 0 à 100.
6. Sélectionnez **Enregistrer** pour enregistrer la modification.

## <a name="portal"></a>Portail

1. Accédez à la page **Service | Vue d’ensemble** et sélectionnez **Application Insights** dans la section **Supervision**.
2. Sélectionnez **Activer Application Insights** pour activer Application Insights dans Azure Spring Cloud.
3. Sélectionnez **Activer l’agent Java In-process** pour activer la fonctionnalité d’évaluation Java IPA. Quand la préversion de la fonctionnalité d’agent In-Process est activée, vous pouvez configurer un taux d’échantillonnage facultatif (par défaut 10,0 %).

   [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Utilisation de la fonctionnalité Application Insights

Lorsque la fonctionnalité **Application Insights** est activée, vous pouvez :

Dans le volet de navigation de gauche, sélectionnez **Application Insights** pour accéder à la page **Vue d’ensemble** d’Application Insights.

* Sélectionnez **Plan de l’application** pour afficher l’état des appels entre les applications.

   [ ![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Sélectionnez le lien entre le service client et `petclinic` pour voir plus de détails, par exemple une requête SQL.

* Dans le volet de navigation de gauche, sélectionnez **Performances** pour voir les données de performances de toutes les opérations des applications, ainsi que les dépendances et les rôles.

   [ ![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* Dans le volet de navigation de gauche, sélectionnez **Échecs** pour voir s’il s’agit d’un événement inattendu pour vos applications.

   [ ![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* Dans le volet de navigation de gauche, sélectionnez **Métriques** et sélectionnez l’espace de noms. Vous verrez les métriques Spring Boot et les métriques personnalisées, le cas échéant.

   [ ![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* Dans le volet de navigation de gauche, sélectionnez **Métriques en temps réel** pour voir les métriques en temps réel de différentes dimensions.

   [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* Dans le volet de navigation de gauche, sélectionnez **Disponibilité** pour surveiller la disponibilité et la réactivité des applications Web en créant des [tests de disponibilité dans Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

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
   az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate]    "samplingRate" --name "assignedName" --resource-group "resourceGroupName"
   ```

* Pour une nouvelle instance Azure Spring Cloud :

   ```azurecli
   az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName"    --disable-app-insights false --enable-java-agent true --name "assignedName" --resource-group    "resourceGroupName"
   ```

* Pour désactiver App-Insight :

   ```azurecli
   az spring-cloud app-insights update --disable --name "assignedName" --resource-group "resourceGroupName"
   ```

## <a name="java-agent-updateupgrade"></a>Mise à jour/mise à niveau de l’agent Java

L’agent Java est régulièrement mis à jour/mis à niveau avec le JDK, ce qui peut affecter les scénarios suivants.

> [!Note]
> La version du JDK est mise à jour ou à niveau tous les trimestres.

* Les applications existantes qui utilisent l’agent Java avant la mise à jour/mise à niveau ne sont pas affectées.
* Les applications créées après la mise à jour/mise à niveau tirent profit de la nouvelle version de l’agent Java.
* Les applications existantes qui n’ont pas utilisé l’agent Java auparavant nécessitent un redémarrage ou un redéploiement pour pouvoir profiter de la nouvelle version de l’agent Java.

## <a name="java-agent-configuration-hot-loading"></a>Chargement à chaud de la configuration de l’agent Java

Azure Spring Cloud a activé un mécanisme de chargement à chaud pour ajuster les paramètres de configuration de l’agent sans redémarrer les applications.

> [!Note]
> Le mécanisme de chargement à chaud a un retard en minutes.

* Lorsque l’agent Java a été activé précédemment, les modifications apportées à l’instance Application Insights et/ou à SamplingRate ne nécessitent PAS le redémarrage des applications.
* Si vous activez l’agent Java, vous devez redémarrer les applications.
* Lorsque vous désactivez l’agent Java, les applications arrêtent d’envoyer toutes les données d’analyse après un retard en minutes. Vous pouvez redémarrer les applications pour supprimer l’agent de l’environnement de runtime Java.

## <a name="concept-matching-between-azure-spring-cloud-and-application-insights"></a>Correspondance du Concept entre Azure Spring Cloud et Application Insights

| Azure Spring Cloud | Application Insights                                         |
| ------------------ | ------------------------------------------------------------ |
| `App`              | * __Cartographie d’application__/Rôle<br />* __Métriques temps réel__/Rôle<br />* __Échecs__/Rôles/Cloud Rôle<br />* __Performances__/Rôles/Rôle Cloud |
| `App Instance`     | * __Cartographie d’application__/Instance de rôle<br />* __Métriques temps réel__/Nom du service<br />* __Échecs__/Rôles/Instance Cloud<br />* __Performances__/Rôles/Instance Cloud |

Le nom `App Instance` d’Azure Spring Cloud sera modifié ou généré dans les scénarios suivants :

* Vous créez une application.
* Vous déployez un fichier JAR ou un code source dans une application existante.
* Vous lancez un déploiement bleu/vert.
* Vous redémarrez l'application.
* Vous arrêtez le déploiement d’une application, puis vous la redémarrez.

Lorsque les données sont stockées dans Application Insights, elles contiennent l’historique des instances d’application Azure Spring Cloud créées ou déployées depuis l’activation de l’agent Java. Cela signifie que, dans le portail Application Insights, vous pouvez voir les données d’Application créées hier, puis supprimées dans un intervalle de temps spécifique, comme les dernières 24 heures. Les scénarios suivants illustrent son fonctionnement :

* Vous avez créé une application autour de 8:00 aujourd’hui à partir d’Azure Spring Cloud avec l’agent Java activé, puis vous avez déployé un fichier JAR dans cette application autour de 8:10 aujourd’hui. Après quelques tests, vous modifiez le code et déployez un nouveau fichier JAR dans cette application à 8:30 aujourd’hui. Ensuite, vous prenez un arrêt et, lorsque vous revenez à 11:00, vous vérifiez certaines données de Application Insights. Voici ce que vous verrez :
  * Trois instances dans la cartographie d’application avec des intervalles de temps au cours des dernières 24 heures, ainsi que les défaillances, les performances et les métriques.
  * Une instance dans la cartographie d’application mappage d’application avec un intervalle de temps au cours de la dernière heure, ainsi que les défaillances, les performances et les métriques.
  * Une instance dans les métriques en temps réel.
* Vous avez créé une application autour de 8:00 aujourd’hui à partir d’Azure Spring Cloud avec l’agent Java activé, puis vous avez déployé un fichier JAR dans cette application autour de 8:10 aujourd’hui. Environ 8:30, vous essayez d’effectuer un déploiement bleu/vert avec un autre fichier JAR. Actuellement, vous avez deux déploiements pour cette application. Après une interruption d’environ 11:00, vous souhaitez vérifier certaines données de Application Insights. Voici ce que vous verrez :
  * Trois instances dans la cartographie d’application avec des intervalles de temps au cours des dernières 24 heures, ainsi que les défaillances, les performances et les métriques.
  * Deux instances dans la cartographie d’application avec des intervalles de temps au cours de la dernière heure, ainsi que les défaillances, les performances et les métriques.
  * Deux instances de métriques en temps réel.

## <a name="see-also"></a>Voir aussi

* [Utiliser le suivi distribué avec Azure Spring Cloud](./how-to-distributed-tracing.md)
* [Analyser les journaux et les métriques](diagnostic-services.md)
* [Effectuer le streaming des journaux en temps réel](./how-to-log-streaming.md)
