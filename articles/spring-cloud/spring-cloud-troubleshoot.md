---
title: Guide de dépannage pour Azure Spring Cloud | Microsoft Docs
description: Guide de dépannage pour Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: ebb960085691206b096090813636ef56366e6536
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038358"
---
# <a name="troubleshooting-guide-for-common-problems"></a>Guide de dépannage pour les problèmes courants

Cet article détaille certains problèmes courants et les étapes de dépannage à suivre pour les développeurs qui travaillent dans Azure Spring Cloud. Nous recommandons également la lecture de notre [article de questions fréquentes](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problèmes liés à la disponibilité, aux performances et aux applications

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>Mon application ne démarre pas (par exemple, le point de terminaison ne peut pas être connecté ou retourne l’erreur 502 après quelques tentatives)

Exportez les journaux dans _Azure Log Analytics_. La table des journaux des applications Spring est nommée `AppPlatformLogsforSpring`. Pour en savoir plus, consultez [Analyser les journaux et les métriques avec les paramètres de diagnostic](diagnostic-services.md).

La présence de l’erreur suivante dans vos journaux indique vraisemblablement l’un des deux problèmes suivants :

`org.springframework.context.ApplicationContextException: Unable to start web server`

* L’un des beans ou l’une de ses dépendances est manquante.
* L’une des propriétés du bean est manquante ou non valide. Dans ce cas, vous verrez probablement `java.lang.IllegalArgumentException`.

Les liaisons de service peuvent également provoquer des échecs de démarrage de l’application. Utilisez les mots clés associés aux services liés pour interroger les journaux.  Par exemple, supposons que votre application ait une liaison à une instance MySQL définie avec l’heure système locale. Si l’application ne parvient pas à démarrer, vous pouvez trouver l’erreur suivante dans le journal :

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

Pour résoudre cette erreur, accédez à la section `server parameters` de votre instance MySQL et, pour `time_zone`, remplacez `SYSTEM` par `+0:00`.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Mon application se bloque ou déclenche une erreur inattendue

Quand le débogage d’une application plante, commencez par vérifier l’état d’exécution et l’état de découverte de l’application. Accédez à _Gestion des applications_ dans le Portail Azure pour vérifier que toutes les applications sont _En cours d’exécution_ et _En service_.

* Si l’état est _En cours d’exécution_ mais que l’état de découverte n’est pas _En service_, accédez à [Mon application ne peut pas être inscrite](#my-application-cannot-be-registered).

* Si l’état de découverte est _En service_, accédez à _Métriques_ pour vérifier l’intégrité de l’application. Inspectez les métriques suivantes :


  - `TomcatErrorCount` (_tomcat.global.error_) : Toutes les exceptions d’application Spring seront comptées ici. Si ce nombre est important, accédez à _Azure Log Analytics_ pour inspecter vos journaux d’applications.

  - `AppMemoryMax` (_jvm.memory.max_) : Quantité maximale de mémoire disponible pour l’application. Elle peut être indéfinie ou changer au fil du temps si elle est définie. La quantité de mémoire utilisée et validée sera toujours inférieure ou égale à Max si elle est définie. Toutefois, une allocation de mémoire peut échouer avec `OutOfMemoryError` si elle tente d’augmenter la mémoire utilisée de telle sorte que used > committed même si used <= max serait toujours vrai. Dans ce cas, essayez d’augmenter la taille maximale du segment de mémoire à l’aide du paramètre `-Xmx`.

  - `AppMemoryUsed` (_jvm.memory.used_) : Quantité de mémoire en octets actuellement utilisée par l’application. Pour une application Java à charge normale, cette série de métriques se présente sous la forme d’un modèle « en dents de scie », dans lequel l’utilisation de la mémoire augmente et diminue régulièrement par petits incréments et chute soudainement, et ce modèle se répète. Cela est dû au nettoyage de la mémoire (garbage collection) à l’intérieur de la machine virtuelle Java, où les actions de collection représentent les points bas dans les « dents de scie ».
    Cette métrique est importante pour identifier les problèmes de mémoire, tels que l’explosion de la mémoire au début, l’allocation de mémoire en surtension pour un chemin logique spécifique et les fuites de mémoire progressives.

  Pour plus d’informations, consultez [Métriques](spring-cloud-concept-metrics.md).

Consultez [cet article](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) pour bien démarrer avec _Azure Log Analytics_.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Mon application connaît une utilisation élevée du processeur ou une utilisation élevée de la mémoire

Si votre application connaît une utilisation élevée du processeur/de la mémoire, de deux choses l’une :
* Soit toutes les instances d’application connaissent une utilisation élevée du processeur/de la mémoire,
* Soit certaines instances d’application connaissent une utilisation élevée du processeur/de la mémoire.

Pour savoir de quelle situation il est question :

1. Accédez à _Métriques_, sélectionnez `Service CPU Usage Percentage` ou `Service Memory Used`,
2. Ajoutez un filtre `App=` pour spécifier l’application que vous souhaitez analyser, puis
3. Répartissez les métriques par `Instance`.

Si toutes les instances connaissent une utilisation élevée du processeur/de la mémoire, vous devez effectuer un scale-out de l’application ou un scale-up du processeur/de la mémoire. Pour plus d’informations, visitez [Mettre à l’échelle des applications](spring-cloud-tutorial-scale-manual.md).

Si certaines instances connaissent une utilisation élevée du processeur/de la mémoire, vérifiez l’état de l’instance et son état de découverte.

Pour plus d’informations, consultez [Métriques](spring-cloud-concept-metrics.md).

Si toutes les instances sont opérationnelles, accédez à _Azure Log Analytics_ pour interroger vos journaux d’application et passez en revue vos logiques de code pour déterminer si l’une d’entre elles peut avoir un impact sur le partitionnement de l’échelle. Pour plus de détails, consultez [Analyser les journaux et les métriques avec les paramètres de diagnostic](diagnostic-services.md).

Consultez [cet article](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) pour bien démarrer avec _Azure Log Analytics_. Interrogez les journaux à l’aide du [langage de requête Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Check-list avant l’intégration de votre application Spring à Azure Spring Cloud

* L’application peut être exécutée localement avec la version de runtime Java spécifiée.
* La configuration de l’environnement (Processeur/RAM/instances) répond à la configuration minimale définie par le fournisseur d’applications.
* Les éléments de configuration ont des valeurs attendues. Pour plus d’informations, consultez [Serveur de configuration](spring-cloud-tutorial-config-server.md).
* Les variables d’environnement ont des valeurs attendues.
* Les paramètres JVM ont des valeurs attendues.
* Nous vous recommandons de désactiver/supprimer les services incorporés _Config Server_ et _Spring Service Registry_ du package d’application.
* Si des ressources Azure doivent être liées via la _liaison de service_, assurez-vous que les ressources cibles sont en cours d’exécution.

## <a name="configuration-and-management"></a>Configuration et gestion

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>J’ai rencontré un problème lors de la création d’une instance du service Azure Spring Cloud

Quand vous tentez de provisionner une instance du service _Azure Spring Cloud_ par le biais du portail, Azure Spring Cloud effectue la validation pour vous.

Toutefois, si vous tentez de provisionner l’instance de service _Azure Spring Cloud_ par le biais d’[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) ou d’un [modèle Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), vérifiez les points suivants :

* L’abonnement est actif.
* L’emplacement est [pris en charge](spring-cloud-faq.md) par _Azure Spring Cloud_.
* Le groupe de ressources pour l’instance est créé.
* Le nom de la ressource est conforme à la règle de nommage. (Il ne peut contenir que des lettres minuscules, des chiffres et des traits d’union. Le premier caractère doit être une lettre. Le dernier caractère doit être une lettre ou un chiffre. Sa longueur doit être comprise entre 2 et 32 caractères.)

Si vous tentez de provisionner l’instance du service _Azure Spring Cloud_ par le biais du modèle Resource Manager, consultez https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates pour vérifier la syntaxe du modèle.

Le nom de l’instance du service _Azure Spring Cloud_ est utilisé pour demander un nom de sous-domaine sous `azureapps.io`, de sorte que le provisionnement échoue si le nom est en conflit avec un nom existant. Vous pouvez trouver plus de détails dans les journaux d’activité.

### <a name="i-cannot-deploy-a-jar-package"></a>Je ne peux pas déployer un package JAR

Vous ne pouvez pas charger le package JAR/source par le biais du portail ou du modèle Resource Manager.

Quand vous déployez votre package d’application à l’aide d’[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), le processus interroge régulièrement l’avancement du déploiement, puis, à la fin, montre son résultat.

Si l’interrogation est interrompue, vous pouvez toujours utiliser la commande suivante pour extraire les journaux de déploiement :

`az spring-cloud app show-deploy-log -n <app-name>`

Vérifiez que votre application est empaquetée dans le bon [format jar exécutable](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Si ce n’est pas le cas, une erreur semblable à la suivante s’affiche :

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>Je ne peux pas déployer un package source

Vous ne pouvez pas charger le package JAR/source par le biais du portail ou du modèle Resource Manager.

Lorsque vous déployez votre package d’application via [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), le processus interroge régulièrement l’avancement du déploiement, puis, à la fin, affiche son résultat.

Si l’interrogation est interrompue, vous pouvez toujours utiliser la commande suivante pour extraire les journaux de compilation et de déploiement :

`az spring-cloud app show-deploy-log -n <app-name>`

Toutefois, notez qu’une instance du service _Azure Spring Cloud_ ne peut déclencher qu’une seule tâche de génération pour un package source à la fois. Pour plus d’informations, consultez [Déployer une application](spring-cloud-quickstart-launch-app-portal.md) et le [guide des environnements de préproduction](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cannot-be-registered"></a>Impossible d’inscrire mon application

Dans la plupart des cas, cela se produit quand les dépendances requises et la découverte de services requis ne sont pas correctement configurés dans votre fichier POM. Une fois configuré, le point de terminaison de serveur Service Registry intégré est injecté en tant que variable d’environnement avec votre application. Les applications peuvent alors s’inscrire automatiquement auprès du serveur Service Registry et découvrir d’autres microservices dépendants.

Attendez au moins 2 minutes qu’une instance nouvellement inscrite commence à recevoir du trafic.

Si vous migrez une solution existante basée sur Spring Cloud vers Azure, vérifiez que vos instances ad hoc de _Service Registry_ et de _Config Server_ sont supprimées (ou désactivées) pour éviter tout conflit avec les instances gérées fournies par _Azure Spring Cloud_.

Vous pouvez également consulter les journaux du client _Service Registry_ dans _Azure Log Analytics_. Pour plus de détails, consultez [Analyser les journaux et les métriques avec les paramètres de diagnostic](diagnostic-services.md).

Consultez [cet article](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) pour bien démarrer avec _Azure Log Analytics_. Interrogez les journaux à l’aide du [langage de requête Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>Impossible de trouver des métriques ou des journaux pour mon application

Accédez à _Gestion des applications_ pour vérifier que l’application est _En cours d’exécution_ et _En service_.

Si vous pouvez voir les métriques à partir de _JVM_ mais aucune métrique de _Tomcat_, vérifiez si la dépendance `spring-boot-actuator` est activée dans votre package d’application et qu’elle démarre correctement.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Si vos journaux d’applications peuvent être archivés dans un compte de stockage, mais ne sont pas envoyés à _Azure Log Analytics_, vérifiez si vous avez correctement [configuré votre espace de travail](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Si vous utilisez un niveau gratuit d’_Azure Log Analytics_, notez que [le niveau gratuit ne fournit pas de contrat de niveau de service](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).