---
title: Guide pratique pour surveiller des applications avec l’agent Java New Relic
titleSuffix: Azure Spring Cloud
description: Découvrez comment surveiller des applications Azure Spring Cloud à l’aide de l’agent Java New Relic.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-java
ms.openlocfilehash: 61796641dbba6b02e8298f084f6c36908b053669
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758075"
---
# <a name="how-to-monitor-with-new-relic-java-agent-preview"></a>Guide pratique pour surveiller des applications avec l’agent Java New Relic (préversion)

Cette fonctionnalité permet de surveiller les applications Azure Spring Cloud avec l’agent Java **New Relic**.

Avec l’agent Java **New Relic**, vous pouvez :
* Consommer l’agent Java **New Relic**.
* Configurer l’agent Java **New Relic** à l’aide de **variables d’environnement**.
* Vérifier toutes les données de surveillance à partir du tableau de bord **New Relic**.

## <a name="prerequisites"></a>Prérequis

* Compte **New Relic**.
* [Azure CLI version 2.0.67 ou ultérieure](/cli/azure/install-azure-cli).

## <a name="leverage-the-new-relic-java-in-process-agent"></a>Tirer profit de l’agent Java New Relic in-process

Utilisez la procédure suivante pour accéder à l’agent :

1. Créer une instance Azure Spring Cloud

2. Créez une application.

    ```shell
      az spring-cloud app create --name "appName" --is-public true \
      -s "resourceName" -g "resourceGroupName"
    ```

3. Créez un déploiement avec l’agent **New Relic** et des **variables d’environnement**.

    ```shell
    az spring-cloud app deploy --name "appName" --jar-path app.jar \
       -s "resourceName" -g "resourceGroupName" \
       --jvm-options="-javaagent:/opt/agents/newrelic/java/newrelic-agent.jar" \
       --env NEW_RELIC_APP_NAME=appName NEW_RELIC_LICENSE_KEY=newRelicLicenseKey
    ```

  Azure Spring Cloud préinstalle l’agent Java **New Relic** sur le chemin d’accès suivant. Les clients peuvent tirer parti de l’agent à partir des **options JVM** des applications, et configurer l’agent à l’aide des [variables d’environnement de l’agent Java New Relic](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables).

  ```shell
  /opt/agents/newrelic/java/newrelic-agent.jar
  ```

## <a name="portal"></a>Portail

Vous pouvez également tirer parti de cet agent à partir du portail avec la procédure suivante. 

1. Recherchez l’application dans **Paramètres**/**Applications** dans le volet de navigation.

   [ ![Trouver l’application à surveiller](media/new-relic-monitoring/find-app.png) ](media/new-relic-monitoring/find-app.png)

2. Cliquez sur l’application pour accéder à la page **Présentation**.

   [ ![Page Présentation](media/new-relic-monitoring/overview-page.png) ](media/new-relic-monitoring/overview-page.png)

3. Cliquez sur **Configuration** dans le volet de navigation gauche pour ajouter/mettre à jour/supprimer les **variables d’environnement** de l’application.

   [ ![Mettre à jour l’environnement](media/new-relic-monitoring/configurations-update-environment.png) ](media/new-relic-monitoring/configurations-update-environment.png)

4. Cliquez sur **Paramètres généraux** pour ajouter/mettre à jour/supprimer l’**option JVM** de l’application.

   [ ![Mettre à jour l’option JVM](media/new-relic-monitoring/update-jvm-option.png) ](media/new-relic-monitoring/update-jvm-option.png)

5. Affichez la page de **résumé** de la passerelle/API d’application à partir du tableau de bord **New Relic**.

   [ ![Page Résumé de l’application](media/new-relic-monitoring/app-summary-page.png) ](media/new-relic-monitoring/app-summary-page.png)

6. Affichez la page de **résumé** customers-service de l’application à partir du tableau de bord **New Relic**.
 
   [ ![Page Customers-service](media/new-relic-monitoring/customers-service.png) ](media/new-relic-monitoring/customers-service.png)  

7. Affichez la page **Service Map** à partir du tableau de bord **New Relic**.  

   [ ![Page Carte de service](media/new-relic-monitoring/service-map.png) ](media/new-relic-monitoring/service-map.png) 

8. Affichez les **JVM** de l’application à partir du tableau de bord **New Relic**.

   [ ![Page JVM](media/new-relic-monitoring/jvm-page.png) ](media/new-relic-monitoring/jvm-page.png) 

9. Affichez le profil d’application à partir du tableau de bord **New Relic**.

   [ ![Profil d’application](media/new-relic-monitoring/profile-app.png) ](media/new-relic-monitoring/profile-app.png) 

## <a name="new-relic-java-agent-logging"></a>Journalisation de l’agent Java New Relic

Par défaut, Azure Spring Cloud imprime les journaux de l’agent Java **New Relic** dans `STDOUT`. Ils seront combinés avec les journaux des applications. Vous pouvez récupérer la version d’agent explicite à partir des journaux des applications.

Vous pouvez également récupérer les journaux de l’agent **New Relic** à partir des composants suivants :

* Journaux Azure Spring Cloud.
* Azure Spring Cloud Application Insights.
* Azure Spring Cloud LogStream.

Vous pouvez tirer parti de certaines variables d’environnement fournies par **New Relic** pour configurer la journalisation du **nouvel agent**, comme `NEW_RELIC_LOG_LEVEL`, pour contrôler le niveau des journaux. Pour en savoir plus, consultez [Variables d’environnement New Relic](https://docs.newrelic.com/docs/agents/java-agent/configuration/java-agent-configuration-config-file/#Environment_Variables).

> [!CAUTION]
> Nous vous recommandons vivement de ne *pas* remplacer le comportement par défaut de journalisation fourni par Azure Spring Cloud pour New Relic. Sinon, les scénarios de journalisation dans les scénarios ci-dessus seront bloqués et le ou les fichiers journaux pourront être perdus. Par exemple, vous ne devez pas transmettre les variables d’environnement suivantes à vos applications. Les fichiers journaux peuvent être perdus après le redémarrage ou le redéploiement des applications.
>
> * NEW_RELIC_LOG
> * NEW_RELIC_LOG_FILE_PATH

## <a name="new-relic-java-agent-updateupgrade"></a>Mise à jour/mise à niveau de l’agent Java New Relic

L’agent Java **New Relic** met régulièrement à jour/à niveau le JDK. La mise à jour/mise à niveau de l’agent peut avoir un impact sur les scénarios suivants.

* Les applications existantes qui utilisent l’agent Java **New Relic** avant la mise à jour/mise à niveau ne seront pas modifiées.
* Les applications existantes qui utilisent l’agent Java **New Relic** avant la mise à jour/mise à niveau nécessitent un redémarrage ou un redéploiement pour impliquer la nouvelle version de l’agent Java **New Relic**.
* Les nouvelles applications créées après la mise à jour/mise à niveau utiliseront la nouvelle version de l’agent Java **New Relic**.

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>Configuration du trafic sortant de l’instance d’injection dans le réseau virtuel

Pour une instance d’injection dans le réseau virtuel Azure Spring Cloud, vous devez vous assurer que le trafic sortant est correctement configuré pour l’agent Java **New Relic**. Pour plus d’informations, consultez la rubrique [Réseaux de New Relic](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents).

## <a name="next-steps"></a>Étapes suivantes
*  [Suivi distribué et App Insights](how-to-distributed-tracing.md)