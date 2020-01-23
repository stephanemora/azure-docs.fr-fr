---
title: Supprimer les protocoles TLS 1.0 et 1.1 de l’utilisation avec Azure Cache pour Redis
description: Découvrez comment supprimer TLS 1.0 et 1.1 de votre application lors de la communication avec Azure Cache pour Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 2f6203deb5e06ba69a3b4d06297d5e702992c79d
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708054"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Supprimer les protocoles TLS 1.0 et 1.1 de l’utilisation avec Azure Cache pour Redis

L’utilisation exclusive de TLS (Transport Layer Security) version 1.2 ou ultérieure est une initiative à l’échelle du secteur. Les versions TLS 1.0 et 1.1 sont connues pour être sujettes à des attaques telles que BEAST et POODLE, et pour avoir d’autres failles et menaces courantes (CVE). Elles ne prennent pas non plus en charge les méthodes de chiffrement modernes et les suites de chiffrement recommandées par les normes de conformité PCI. Ce [blog sur la sécurité de TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) explique plus en détail certaines de ces vulnérabilités.

Dans le cadre de cet effort, nous apportons les modifications suivantes dans le cache Azure pour Redis :

* À compter du 13 janvier 2020, nous allons configurer la version TLS minimale par défaut sur 1,2 pour les instances de cache nouvellement créées.  Les instances de cache existantes ne seront pas mises à jour à ce stade.  Vous êtes autorisé à [modifier la version minimale du protocole TLS](cache-configure.md#access-ports) à 1,0 ou 1,1 pour des raisons de compatibilité descendante, si nécessaire.  Cette modification peut être effectuée via le Portail Azure ou d’autres API de gestion.
* À compter du 31 mars 2020, nous arrêterons de prendre en charge les versions 1,0 et 1,1 de TLS. Après cette modification, votre application doit utiliser TLS 1.2 ou une version ultérieure pour communiquer avec votre cache.

En outre, dans le cadre de cette modification, nous allons supprimer la prise en charge des suites de chiffrement plus anciennes et non sécurisées.  Nos suites de chiffrement prises en charge sont limitées aux éléments suivants lorsque le cache est configuré avec une version TLS minimale de 1.2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

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
