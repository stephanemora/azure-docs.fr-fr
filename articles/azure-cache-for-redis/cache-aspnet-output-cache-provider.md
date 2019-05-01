---
title: Fournisseur de caches de sortie ASP.NET pour le Cache Azure pour Redis
description: Découvrez comment mettre en cache une sortie de pages ASP.NET à l’aide du Cache Azure pour Redis.
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943868"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Fournisseur de caches de sortie ASP.NET pour le Cache Azure pour Redis

Le fournisseur de caches de sortie Redis est un mécanisme de stockage hors processus pour les données de cache de sortie. Ces données concernent spécialement les réponses HTTP complètes (mise en cache de la sortie de pages). Le fournisseur se connecte au nouveau point d'extension du fournisseur de caches de sortie introduit dans ASP.NET 4.

Pour utiliser le fournisseur de caches de sortie Redis, configurez d’abord votre cache, puis configurez votre application ASP.NET en utilisant le package NuGet du fournisseur de caches de sortie Redis. Cette rubrique fournit des conseils sur la configuration de votre application pour utiliser le fournisseur de caches de sortie Redis. Pour plus d’informations sur la création et la configuration d’une instance de Cache Azure pour Redis, voir [Créer un cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Stockage de la sortie de pages ASP.NET dans le cache

Pour configurer une application cliente dans Visual Studio avec le package NuGet de l’État de session du Cache Azure pour Redis, cliquez sur **Gestionnaire de package NuGet**, **Console de Service Manager** dans le menu **Outils**.

Exécutez la commande suivante depuis la fenêtre `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Le package NuGet du fournisseur de caches de sortie Redis a une dépendance sur le package StackExchange.Redis.StrongName. Le package StackExchange.Redis.StrongName est automatiquement installé s’il ne figure pas déjà dans votre projet. Pour plus d’informations sur le package NuGet du fournisseur de caches de sortie Redis, consultez la page NuGet [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/).

>[!NOTE]
>Il existe, en plus du package StackExchange.Redis.StrongName avec nom fort, une version de StackExchange.Redis sans nom fort. Si votre projet est à l’aide de la version de StackExchange.Redis sans nom fort, vous devez désinstaller Sinon, vous rencontrerez des conflits d’affectation de noms dans votre projet. Pour plus d’informations sur ces packages, consultez la section [Configuration des clients de cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Le package NuGet télécharge et ajoute les références d’assembly nécessaires et ajoute la section suivante dans votre fichier web.config. Cette section contient la configuration requise pour que votre application ASP.NET utilise le fournisseur de cache de sortie Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Configurez les attributs avec les valeurs du panneau de votre cache sur le portail Microsoft Azure et configurez les autres valeurs selon votre choix. Pour obtenir des instructions sur l’accès aux propriétés de votre cache, voir [Configurer les paramètres du Cache Azure pour Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

| Attribut | Type | Default | Description |
| --------- | ---- | ------- | ----------- |
| *host* | string | « localhost » | Le nom hôte ou adresse des IP du serveur Redis |
| *port* | entier positif | 6379 (non-SSL)<br/>6380 (SSL) | Port du serveur de redis |
| *accessKey* | string | "" | Redis mot de passe de serveur lors de l’autorisation de Redis est activée. La valeur est une chaîne vide par défaut, ce qui signifie que le fournisseur d’état de session ne sont pas utiliser n’importe quel mot de passe lors de la connexion au serveur Redis. **Si votre serveur Redis est dans un réseau accessible publiquement, telles que le Cache Redis Azure, veillez à activer l’autorisation de Redis améliorer la sécurité et fournir un mot de passe sécurisé.** |
| *ssl* | booléenne | **false** | Indique s’il faut ou non se connecter au serveur Redis par le biais de SSL. Cette valeur est **false** par défaut, car Redis ne prend pas en charge SSL prêts à l’emploi. **Si vous utilisez le Cache Redis Azure qui prend en charge SSL dès le départ, veillez à la définir à true pour améliorer la sécurité.**<br/><br/>Le port non SSL est désactivé par défaut pour les nouveaux caches. Spécifiez **true** pour ce paramètre pour utiliser le port SSL. Pour plus d’informations sur l’activation du port non SSL, consultez la section relative aux [ports d’accès](cache-configure.md#access-ports) dans la rubrique [Configuration d’un cache](cache-configure.md). |
| *databaseIdNumber* | entier positif | 0 | *Cet attribut peut être spécifié uniquement par le biais de web.config ou AppSettings.*<br/><br/>Spécifiez la base de données Redis à utiliser. |
| *connectionTimeoutInMilliseconds* | entier positif | Fourni par StackExchange.Redis | Permet de définir *ConnectTimeout* lors de la création de StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | entier positif | Fourni par StackExchange.Redis | Permet de définir *SyncTimeout* lors de la création de StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (chaîne de connexion valide StackExchange.Redis) | string | *n/a* | Soit une référence de paramètre à AppSettings ou web.config, sans quoi une chaîne de connexion valide StackExchange.Redis. Cet attribut peut fournir des valeurs pour *hôte*, *port*, *accessKey*, *ssl*et d’autres attributs de StackExchange.Redis. Pour plus en détail, consultez *connectionString*, consultez [paramètre connectionString](#setting-connectionstring) dans le [attribut notes](#attribute-notes) section. |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/a* | *Ces attributs peuvent être spécifiés uniquement par le biais de web.config ou AppSettings.*<br/><br/>Utilisez ces attributs pour fournir une chaîne de connexion. *settingsClassName* doit être un nom de classe qualifié d’assembly qui contient la méthode spécifiée par *settingsMethodName*.<br/><br/>La méthode spécifiée par *settingsMethodName* doivent être publiques, statiques et void (ne prend aucun paramètre), avec un type de retour **chaîne**. Cette méthode retourne la chaîne de connexion. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | *Ces attributs peuvent être spécifiés uniquement par le biais de web.config ou AppSettings.*<br/><br/>Utilisez ces attributs pour déboguer votre application en fournissant des journaux à partir du Cache de l’état de Session/sortie, ainsi que des journaux à partir de StackExchange.Redis. *loggingClassName* doit être un nom de classe qualifié d’assembly qui contient la méthode spécifiée par *loggingMethodName*.<br/><br/>La méthode spécifiée par *loggingMethodName* doivent être publiques, statiques et void (ne prend aucun paramètre), avec un type de retour **System.IO.TextWriter**. |
| *applicationName* | string | Le nom du module du processus en cours ou « / » | *SessionStateProvider uniquement*<br/>*Cet attribut peut être spécifié uniquement par le biais de web.config ou AppSettings.*<br/><br/>Le préfixe de nom d’application à utiliser dans le cache Redis. Le client peut utiliser le même cache Redis à des fins différentes. Pour s’assurer que les clés de session ne sont pas en conflit, il peut être préfixé avec le nom de l’application. |
| *throwOnError* | booléenne | true | *SessionStateProvider uniquement*<br/>*Cet attribut peut être spécifié uniquement par le biais de web.config ou AppSettings.*<br/><br/>Indique s’il faut ou non lever une exception lorsqu’une erreur se produit.<br/><br/>Pour plus d’informations *throwOnError*, consultez [Remarques sur la *throwOnError* ](#notes-on-throwonerror) dans le [attribut notes](#attribute-notes) section. |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | entier positif | 5 000 | *SessionStateProvider uniquement*<br/>*Cet attribut peut être spécifié uniquement par le biais de web.config ou AppSettings.*<br/><br/>Combien de nouvelle tentative en cas d’échec d’une opération. Si cette valeur est inférieure à *operationTimeoutInMilliseconds*, le fournisseur ne sera pas relancé.<br/><br/>Pour plus d’informations *retryTimeoutInMilliseconds*, consultez [Remarques sur la *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) dans le [attribut notes](#attribute-notes) section. |
| *redisSerializerType* | string | *n/a* | Spécifie le nom de type qualifié d’assembly d’une classe qui implémente Microsoft.Web.Redis. ISerializer et qui contient la logique personnalisée pour sérialiser et désérialiser les valeurs. Pour plus d’informations, consultez [sur *redisSerializerType* ](#about-redisserializertype) dans le [attribut notes](#attribute-notes) section. |
|

## <a name="attribute-notes"></a>Notes d’attribut

### <a name="setting-connectionstring"></a>Paramètre *connectionString*

La valeur de *connectionString* est utilisé comme clé pour extraire la chaîne de connexion à partir des AppSettings, si cette chaîne existe dans AppSettings. Si ce n’est pas trouvé dans AppSettings, la valeur de *connectionString* sera être utilisé comme clé pour extraire la chaîne de connexion à partir de web.config **ConnectionString** section, si cette section existe. Si n’est pas le cas de la chaîne de connexion existe dans AppSettings ou le fichier web.config **ConnectionString** section, la valeur littérale de *connectionString* sera utilisé comme la chaîne de connexion lors de la création StackExchange.Redis.ConnectionMultiplexer.

Les exemples suivants illustrent comment *connectionString* est utilisé.

#### <a name="example-1"></a>Exemple 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

Dans `web.config`, utilisez ci-dessus clé comme valeur de paramètre au lieu de la valeur réelle.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Exemple 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

Dans `web.config`, utilisez ci-dessus clé comme valeur de paramètre au lieu de la valeur réelle.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Exemple 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Remarques sur la *throwOnError*

Actuellement, si une erreur se produit pendant une opération de session, le fournisseur d’état de session lève une exception. Cette opération arrête l’application.

Ce comportement a été modifié d’une façon qui prend en charge les attentes des utilisateurs du fournisseur état de session ASP.NET existants tout en offrant également la possibilité d’agir sur les exceptions, si vous le souhaitez. Le comportement par défaut lève toujours une exception lorsqu’une erreur se produit, cohérente avec d’autres fournisseurs état de session ASP.NET ; code existant devrait se dérouler comme avant.

Si vous définissez *throwOnError* à **false**, au lieu de lever une exception lorsqu’une erreur se produit, il échouera, en mode silencieux. Pour voir si une erreur s’est produite et, dans ce cas, découvrez ce que l’exception a été, vérifiez la propriété statique *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Remarques sur la *retryTimeoutInMilliseconds*

Cela fournit une logique de nouvelle tentative pour simplifier le cas où une opération de session doit réessayer en cas d’échec en raison des éléments tels que problème de réseau, tout en permettant de contrôler le délai d’attente de nouvelle tentative ou de refuser de nouvelles tentatives entièrement.

Si vous définissez *retryTimeoutInMilliseconds* à un nombre, par exemple, 2000, puis lorsqu’une opération de session échoue, il réessaiera pour 2 000 millisecondes avant de traiter comme une erreur. Par conséquent, pour que le fournisseur d’état de session pour appliquer cette logique de nouvelle tentative, il suffit de configurer le délai d’attente. La première nouvelle tentative aura lieu après 20 millisecondes, ce qui est suffisant dans la plupart des cas, quand un problème réseau se produit. Après cela, il réessaiera chaque seconde jusqu'à ce qu’il arrive à expiration. Juste après le délai d’expiration, il réessaiera plus de temps pour vous assurer qu’il ne suffira pas désactiver le délai d’attente par une seconde (au plus).

Si vous ne pense pas que vous avez besoin de nouvelle tentative (par exemple, lorsque vous exécutez le serveur Redis sur le même ordinateur que votre application) ou si vous souhaitez gérer la logique de nouvelle tentative vous-même, définissez *retryTimeoutInMilliseconds* à 0.

### <a name="about-redisserializertype"></a>Sur *redisSerializerType*

Par défaut, la sérialisation pour stocker les valeurs sur le cache Redis est effectuée dans un format binaire fourni par le **BinaryFormatter** classe. Utilisez *redisSerializerType* pour spécifier le nom de type qualifié d’assembly d’une classe qui implémente **Microsoft.Web.Redis.ISerializer** et possède la logique personnalisée pour sérialiser et désérialiser les valeurs. Par exemple, voici une classe de sérialiseur Json à l’aide de JSON.NET :

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

En supposant que cette classe est définie dans un assembly avec nom **MyCompanyDll**, vous pouvez définir le paramètre *redisSerializerType* à utiliser :

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Directive de cache de sortie

Ajoutez une directive OutputCache à chaque page pour laquelle vous voulez mettre en cache la sortie.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Dans l’exemple précédent, les données de page mises en cache resteront dans le cache pendant 60 secondes et une version différente de la page est mise en cache pour chaque combinaison de paramètres. Pour plus d’informations sur la directive OutputCache, consultez [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837).

Une fois ces étapes effectuées, votre application est configurée pour utiliser le fournisseur de caches de sortie Redis.

## <a name="next-steps"></a>Étapes suivantes

Voir [Fournisseur d’États de session ASP.NET pour le Cache Azure pour Redis](cache-aspnet-session-state-provider.md).
