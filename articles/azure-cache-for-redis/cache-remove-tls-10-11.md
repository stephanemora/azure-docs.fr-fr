---
title: Supprimer les protocoles TLS 1.0 et 1.1 de l’utilisation avec Azure Cache pour Redis
description: Découvrez comment supprimer TLS 1.0 et 1.1 de votre application lors de la communication avec Azure Cache pour Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 69df5a65df99a7497099e71e9f41701458370c87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84423919"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Supprimer les protocoles TLS 1.0 et 1.1 de l’utilisation avec Azure Cache pour Redis

L’utilisation exclusive de TLS (Transport Layer Security) version 1.2 ou ultérieure est une initiative à l’échelle du secteur. Les versions TLS 1.0 et 1.1 sont connues pour être sujettes à des attaques telles que BEAST et POODLE, et pour avoir d’autres failles et menaces courantes (CVE). Elles ne prennent pas non plus en charge les méthodes de chiffrement modernes et les suites de chiffrement recommandées par les normes de conformité PCI. Ce [blog sur la sécurité de TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) explique plus en détail certaines de ces vulnérabilités.

Dans le cadre de cet effort, nous apportons les modifications suivantes dans le cache Azure pour Redis :

* **Phase 1 :** Nous configurerons la version TLS minimale par défaut sur la version 1.2 pour les instances de cache nouvellement créées (anciennement TLS 1.0).  Les instances de cache existantes ne seront pas mises à jour à ce stade. Vous êtes autorisé à [modifier la version minimale du protocole TLS](cache-configure.md#access-ports) à 1,0 ou 1,1 pour des raisons de compatibilité descendante, si nécessaire. Cette modification peut être effectuée via le Portail Azure ou d’autres API de gestion.
* **Phase 2 :** Nous mettrons fin à la prise en charge des versions 1.0 et 1.1 de TLS. Après cette modification, votre application doit utiliser TLS 1.2 ou une version ultérieure pour communiquer avec votre cache.

En outre, dans le cadre de cette modification, nous allons supprimer la prise en charge des suites de chiffrement plus anciennes et non sécurisées.  Nos suites de chiffrement prises en charge sont limitées aux éléments suivants lorsque le cache est configuré avec une version TLS minimale de 1.2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Cet article fournit des instructions générales sur la façon de détecter les dépendances sur ces versions antérieures de TLS et de les supprimer de votre application.

Les dates d'entrée en vigueur de ces modifications sont les suivantes :

| Cloud                | Date de début de la phase 1 | Date de début de la phase 2         |
|----------------------|--------------------|----------------------------|
| Azure (global)       |  13 janvier 2020  | Différée en raison de la COVID 19  |
| Azure Government     |  13 mars 2020    | Différée en raison de la COVID 19  |
| Azure Germany        |  13 mars 2020    | Différée en raison de la COVID 19  |
| Azure China 21Vianet |  13 mars 2020    | Différée en raison de la COVID 19  |

REMARQUE :  Nouvelle date de la phase 2 non encore déterminée

## <a name="check-whether-your-application-is-already-compliant"></a>Vérifier si votre application est déjà conforme

Le moyen le plus simple de déterminer si votre application peut fonctionner avec le protocole TLS 1.2 consiste à définir la valeur de **Version TLS minimale** sur TLS 1.2 sur un cache de test ou intermédiaire, puis d’effectuer des tests. Le paramètre **Version TLS minimale** se trouve dans les [Paramètres avancés](cache-configure.md#advanced-settings) de votre instance de cache dans le portail Azure.  Si l’application continue à fonctionner comme prévu après cette modification, elle est probablement conforme. Il se peut que vous deviez configurer la bibliothèque cliente Redis que votre application utilise pour activer le protocole TLS 1.2 afin de vous connecter à Azure Cache pour Redis.

## <a name="configure-your-application-to-use-tls-12"></a>Configurer votre application pour utiliser TLS 1.2

La plupart des applications utilisent des bibliothèques clientes Redis pour gérer la communication avec leurs caches. Voici des instructions pour configurer certaines des bibliothèques clientes populaires, dans différents langages de programmation et infrastructures, afin qu’elles utilisent TLS 1.2.

### <a name="net-framework"></a>.NET Framework

Les clients .NET Redis utilisent la version TLS la plus ancienne par défaut sur .NET Framework 4.5.2 ou version antérieure, et la dernière version de TLS sur .NET Framework 4.6 ou version ultérieure. Si vous utilisez une version antérieure de .NET Framework, vous pouvez activer TLS 1.2 manuellement :

* **StackExchange.Redis :** Définissez `ssl=true` et `sslprotocols=tls12` dans la chaîne de connexion.
* **ServiceStack.Redis :** Suivez les instructions [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) avec ServiceStack.Redis v5.6 au minimum.

### <a name="net-core"></a>.NET Core

Les clients .NET Core Redis utilisent par défaut la version TLS du système d’exploitation, qui dépend évidemment du système d’exploitation lui-même. 

En fonction de la version du système d’exploitation et des correctifs qui ont été appliqués, la version par défaut effective du protocole TLS peut varier. Bien qu’il y ait une source d’informations à ce sujet, [voici](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) un article pour Windows. 

Toutefois, si vous utilisez un ancien système d’exploitation ou souhaitez simplement être rassuré, nous vous recommandons de configurer manuellement la version du protocole TLS de votre choix via le client.


### <a name="java"></a>Java

Les clients Java Redis utilisent TLS 1.0 sur Java version 6 ou antérieure. Jedis, Lettuce et Redisson ne peuvent pas se connecter à Azure Cache for Redis si TLS 1.0 est désactivé sur le cache. Mettez à niveau votre infrastructure Java pour utiliser les nouvelles versions du protocole TLS.

Pour Java 7, les clients Redis n’utilisent pas TLS 1.2 par défaut, mais peuvent être configurés pour cela. Jedis vous permet de spécifier les paramètres TLS sous-jacents avec l’extrait de code suivant :

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Les clients Lettuce et Redisson ne prenant pas encore en charge la spécification de la version TLS, de sorte qu’ils seront rompus si le cache n’accepte que les connexions TLS 1.2. Les correctifs pour ces clients étant en cours de révision, vérifiez auprès de ces packages qu’une version mise à jour est prise en charge.

Dans Java 8, TLS 1.2 est utilisé par défaut et ne doit pas nécessiter de mise à jour de la configuration de votre client dans la plupart des cas. Par précaution, testez votre application.

### <a name="nodejs"></a>Node.js

Node Redis et IORedis utilisent TLS 1.2 par défaut.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Versions antérieures à PHP 7 : Predis prend uniquement en charge TLS 1.0. Ces versions ne fonctionnent pas avec TLS 1.2 ; vous devez effectuer la mise à niveau pour utiliser TLS 1.2.
 
* PHP 7.0 à PHP 7.2.1 : Predis utilise uniquement TLS 1.0 ou 1.1 par défaut. Vous pouvez utiliser la solution de contournement suivante pour utiliser TLS 1.2. Spécifiez TLS 1.2 quand vous créez l’instance cliente :

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7.3 et versions ultérieures : Predis utilise la toute dernière version de TLS.

#### <a name="phpredis"></a>PhpRedis

PhpRedis ne prend en charge TLS sur aucune version de PHP.

### <a name="python"></a>Python

Redims-py utilise TLS 1.2 par défaut.

### <a name="go"></a>GO

Redigo utilise TLS 1.2 par défaut.

## <a name="additional-information"></a>Informations supplémentaires

- [Configuration du cache Azure pour Redis](cache-configure.md)
