---
title: Supprimer les protocoles TLS 1.0 et 1.1 de l’utilisation avec Azure Cache pour Redis
description: Découvrez comment supprimer TLS 1.0 et 1.1 de votre application lors de la communication avec Azure Cache pour Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 74fcce412b2673a3ec9e4809cef018f1afbc3530
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812841"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Supprimer les protocoles TLS 1.0 et 1.1 de l’utilisation avec Azure Cache pour Redis

L’utilisation exclusive de TLS (Transport Layer Security) version 1.2 ou ultérieure est une initiative à l’échelle du secteur. Les versions TLS 1.0 et 1.1 sont connues pour être sujettes à des attaques telles que BEAST et POODLE, et pour avoir d’autres failles et menaces courantes (CVE). Elles ne prennent pas non plus en charge les méthodes de chiffrement modernes et les suites de chiffrement recommandées par les normes de conformité PCI. Ce [blog sur la sécurité de TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) explique plus en détail certaines de ces vulnérabilités.

Bien qu’aucune de ces considérations ne pose un problème immédiat, nous vous recommandons de cesser d’utiliser les protocoles TLS 1.0 et 1.1 rapidement. Azure Cache pour Redis ne prendra plus en charge ces versions de TLS après le 31 mars 2020. Après cette date, votre application devra utiliser TLS 1.2 ou une version ultérieure pour communiquer avec votre cache.

Cet article fournit des instructions générales sur la façon de détecter les dépendances sur ces versions antérieures de TLS et de les supprimer de votre application.

## <a name="check-whether-your-application-is-already-compliant"></a>Vérifier si votre application est déjà conforme

Le moyen le plus simple de déterminer si votre application fonctionne avec TLS 1.2 est de définir la valeur de **Version TLS minimale** sur TLS 1.2 sur un cache de test ou intermédiaire qu’elle utilise. Le paramètre **Version TLS minimale** se trouve dans les [Paramètres avancés](cache-configure.md#advanced-settings) de votre instance de cache dans le portail Azure. Si l’application continue à fonctionner comme prévu après cette modification, elle est probablement conforme. Vous devrez peut-être configurer certaines bibliothèques clientes Redis utilisées par votre application pour activer TLS 1.2, afin qu’elles puissent se connecter à Azure Cache pour Redis avec ce protocole de sécurité.

## <a name="configure-your-application-to-use-tls-12"></a>Configurer votre application pour utiliser TLS 1.2

La plupart des applications utilisent des bibliothèques clientes Redis pour gérer la communication avec leurs caches. Voici des instructions pour configurer certaines des bibliothèques clientes populaires, dans différents langages de programmation et infrastructures, afin qu’elles utilisent TLS 1.2.

### <a name="net-framework"></a>.NET Framework

Les clients .NET Redis utilisent la version TLS la plus ancienne par défaut sur .NET Framework 4.5.2 ou version antérieure, et la dernière version de TLS sur .NET Framework 4.6 ou version ultérieure. Si vous utilisez une version antérieure de .NET Framework, vous pouvez activer TLS 1.2 manuellement :

* **StackExchange.Redis :** Définissez `ssl=true` et `sslprotocols=tls12` dans la chaîne de connexion.
* **ServiceStack.Redis :** Suivez les [instructions de ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Les clients .NET Core Redis utilisent la dernière version de TLS par défaut.

### <a name="java"></a>Java

Les clients Java Redis utilisent TLS 1.0 sur Java version 6 ou antérieure. Jedis, Lettuce et Radisson ne peuvent pas se connecter à Azure Cache pour Redis si TLS 1.0 est désactivé sur le cache. Il n’existe actuellement aucune solution connue.

Sur Java 7 ou version ultérieure, les clients Redis n’utilisent pas TLS 1.2 par défaut, mais peuvent être configurés pour le faire. Lettuce et Radisson ne prennent pas en charge cette configuration pour le moment. Ils s’interrompent si le cache accepte uniquement les connexions TLS 1.2. Jedis vous permet de spécifier les paramètres TLS sous-jacents avec l’extrait de code suivant :

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

Node Redis et IORedis utilisent TLS 1.2 par défaut.

### <a name="php"></a>PHP

Predis sur PHP 7 ne fonctionne pas, car PHP 7 prend uniquement en charge TLS 1.0. Dans PHP 7.2.1 ou version antérieure, Predis utilise TLS 1.0 ou 1.1 par défaut. Vous pouvez spécifier TLS 1.2 quand vous créez l’instance cliente :

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

Sur PHP 7.3 ou version ultérieure, Predis utilise la dernière version de TLS.

PhpRedis ne prend en charge TLS sur aucune version de PHP.

### <a name="python"></a>Python

Redims-py utilise TLS 1.2 par défaut.

### <a name="go"></a>GO

Redigo utilise TLS 1.2 par défaut.

## <a name="additional-information"></a>Informations supplémentaires

- [Configuration du cache Azure pour Redis](cache-configure.md)
