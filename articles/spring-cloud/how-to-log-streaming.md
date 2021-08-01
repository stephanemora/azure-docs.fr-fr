---
title: Envoyer en streaming les journaux d’application Azure Spring Cloud en temps réel
description: Comment utiliser la diffusion en continu des journaux pour afficher instantanément les journaux des applications
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b87f3221e62db6999dd67f475055f699a74c4c2a
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110495157"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Envoyer en streaming les journaux d’application Azure Spring Cloud en temps réel

**Cet article s’applique à :** ✔️ Java ✔️ C#

Azure Spring Cloud active la diffusion en continu de journaux dans Azure CLI pour obtenir des journaux de console d’application en temps réel pour la résolution des problèmes. Vous pouvez également [analyser les journaux et les mesures à l’aide des paramètres de diagnostic](./diagnostic-services.md).

## <a name="prerequisites"></a>Conditions préalables requises

* Installez [extension Azure CLI](/cli/azure/install-azure-cli) pour Spring Cloud, version minimale 0.2.0.
* Une instance d’**Azure Spring Cloud** avec une application en cours d’exécution, par exemple [application Spring Cloud](./quickstart.md).

> [!NOTE]
>  L’extension de l’interface CLI ASC est mise à jour de la version 0.2.0 vers la version 0.2.1. Cette modification affecte la syntaxe de la commande pour le streaming des journaux : `az spring-cloud app log tail`, qui est remplacée par : `az spring-cloud app logs`. La commande `az spring-cloud app log tail` sera dépréciée dans une version ultérieure. Si vous utilisez la version 0.2.0, vous pouvez effectuer la mise à niveau vers la version 0.2.1. Tout d’abord, supprimez l’ancienne version à l’aide de la commande `az extension remove -n spring-cloud`.  Ensuite, installez la version 0.2.1 à l’aide de la commande `az extension add -n spring-cloud`.

## <a name="use-cli-to-tail-logs"></a>Utiliser l’interface CLI pour les dernières lignes des journaux

Pour éviter de spécifier à plusieurs reprises le nom de votre groupe de ressources et de votre instance de service, définissez le nom du groupe de ressources et le nom du cluster par défaut.
```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
Dans les exemples suivants, le groupe de ressources et le nom du service sont omis dans les commandes.

### <a name="tail-log-for-app-with-single-instance"></a>Dernières lignes du journal pour l’application avec une seule instance
Si une application nommée « auth-service » ne dispose que d’une seule instance, vous pouvez afficher le journal de l’instance de l’application à l’aide de la commande suivante :
```azurecli
az spring-cloud app logs -n auth-service
```
Des journaux seront retournés :
```output
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

```azurecli
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Avec les résultats :

```output
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Ensuite, vous pouvez diffuser en continu des journaux d’une instance d’application avec l’option `-i/--instance` :

```azurecli
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Vous pouvez également obtenir des informations sur les instances d’application à partir du portail Azure.  Après avoir sélectionné **Applications** dans le volet de navigation gauche de votre service Azure Spring Cloud, sélectionnez **instances de l’application**.

### <a name="continuously-stream-new-logs"></a>Diffuser en continu de nouveaux journaux
Par défaut, `az spring-cloud app logs` imprime uniquement les journaux existants diffusés en continu à la console d’application, puis se ferme. Si vous souhaitez diffuser de nouveaux journaux, ajoutez la lettre  f (--Follow) :  

```azurecli
az spring-cloud app logs -n auth-service -f
``` 
Pour vérifier toutes les options de journalisation prises en charge :
```azurecli
az spring-cloud app logs -h 
```

### <a name="format-json-structured-logs"></a>Formater les journaux structurés JSON

> [!NOTE]
> Requiert l’extension spring-cloud version 2.4.0 ou ultérieure.

Lorsque le [Journal des applications structurées](./structured-app-log.md) est activé pour l’application, les journaux sont imprimés au format JSON. Cela complique la lecture. L’argument `--format-json` peut être utilisé pour mettre en forme les journaux JSON dans un format lisible par l’utilisateur.

```shell
# Raw JSON log
$ az spring-cloud app logs -n auth-service
{"timestamp":"2021-05-26T03:35:27.533Z","logger":"com.netflix.discovery.DiscoveryClient","level":"INFO","thread":"main","mdc":{},"message":"Disable delta property : false"}
{"timestamp":"2021-05-26T03:35:27.533Z","logger":"com.netflix.discovery.DiscoveryClient","level":"INFO","thread":"main","mdc":{},"message":"Single vip registry refresh property : null"}

# Formatted JSON log
$ az spring-cloud app logs -n auth-service --format-json
2021-05-26T03:35:27.533Z  INFO [           main] com.netflix.discovery.DiscoveryClient   : Disable delta property : false
2021-05-26T03:35:27.533Z  INFO [           main] com.netflix.discovery.DiscoveryClient   : Single vip registry refresh property : null
```

L’argument `--format-json` prend également un format personnalisé facultatif, à l’aide de l’argument de mot clé [format string syntax](https://docs.python.org/3/library/string.html#format-string-syntax).

```shell
# Custom format
$ az spring-cloud app logs -n auth-service --format-json="{message}{n}"
Disable delta property : false
Single vip registry refresh property : null
```

> Le format par défaut utilisé est
> ```
> {timestamp} {level:>5} [{thread:>15.15}] {logger{39}:<40.40}: {message}{n}{stackTrace}
> ```

## <a name="next-steps"></a>Étapes suivantes
* [Démarrage rapide : Supervision des applications Azure Spring Cloud avec les journaux, les métriques et le suivi](./quickstart-logs-metrics-tracing.md)
* [Analyser les journaux et les indicateurs de performance avec les paramètres de diagnostic](./diagnostic-services.md)
