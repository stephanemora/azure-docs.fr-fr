---
title: Envoyer en streaming les journaux d’application Azure Spring Cloud en temps réel
description: Comment utiliser la diffusion en continu des journaux pour afficher instantanément les journaux des applications
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: 27978d367ded7a31d73949cd675ae9e6f8cb887c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263997"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Envoyer en streaming les journaux d’application Azure Spring Cloud en temps réel
Azure Spring Cloud active la diffusion en continu de journaux dans Azure CLI pour obtenir des journaux de console d’application en temps réel pour la résolution des problèmes. Vous pouvez également [analyser les journaux et les mesures à l’aide des paramètres de diagnostic](./diagnostic-services.md).

## <a name="prerequisites"></a>Conditions préalables requises

* Installez [extension Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) pour Spring Cloud, version minimale 0.2.0.
* Une instance d’**Azure Spring Cloud** avec une application en cours d’exécution, par exemple [application Spring Cloud](./spring-cloud-quickstart-launch-app-cli.md).

## <a name="use-cli-to-tail-logs"></a>Utiliser l’interface CLI pour les dernières lignes des journaux

Pour éviter de spécifier à plusieurs reprises le nom de votre groupe de ressources et de votre instance de service, définissez le nom du groupe de ressources et le nom du cluster par défaut.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
Dans les exemples suivants, le groupe de ressources et le nom du service sont omis dans les commandes.

### <a name="tail-log-for-app-with-single-instance"></a>Dernières lignes du journal pour l’application avec une seule instance
Si une application nommée « auth-service » ne dispose que d’une seule instance, vous pouvez afficher le journal de l’instance de l’application à l’aide de la commande suivante :
```
az spring-cloud app log tail -n auth-service
```
Des journaux seront retournés :
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Dernières lignes du journal pour l’application avec une seule instance
Si plusieurs instances existent pour l’application nommée `auth-service`, vous pouvez afficher le journal des instances à l’aide de l’option `-i/--instance`. 

Tout d’abord, vous pouvez récupérer les noms des instances de l’application à l’aide de la commande suivante.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Avec les résultats :

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Ensuite, vous pouvez diffuser en continu des journaux d’une instance d’application avec l’option `-i/--instance` :

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Vous pouvez également obtenir des informations sur les instances d’application à partir du portail Azure.  Après avoir sélectionné **Applications** dans le volet de navigation gauche de votre service Azure Spring Cloud, sélectionnez **instances de l’application**.

### <a name="continuously-stream-new-logs"></a>Diffuser en continu de nouveaux journaux
Par défaut, `az spring-cloud ap log tail` imprime uniquement les journaux existants diffusés en continu à la console d’application, puis se ferme. Si vous souhaitez diffuser de nouveaux journaux, ajoutez la lettre  f (--Follow) :  

```
az spring-cloud app log tail -n auth-service -f
``` 
Pour vérifier toutes les options de journalisation prises en charge :
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>Étapes suivantes

* [Analyser les journaux et les indicateurs de performance avec les paramètres de diagnostic](./diagnostic-services.md)

 





