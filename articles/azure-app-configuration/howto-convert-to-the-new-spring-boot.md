---
title: Convertir vers la bibliothèque Spring Boot
titleSuffix: Azure App Configuration
description: Découvrez comment convertir la version précédente en une nouvelle bibliothèque Spring Boot App Configuration.
ms.service: azure-app-configuration
author: mrm9084
ms.author: mametcal
ms.topic: how-to
ms.date: 07/08/2021
ms.openlocfilehash: 1bff486cb226de6bb9b6c8a0f065dbca134bd684
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113553013"
---
# <a name="convert-to-new-app-configuration-spring-boot-library"></a>Convertir en nouvelle bibliothèque Spring Boot App Configuration

Une nouvelle version de la bibliothèque App Configuration pour Spring Boot est désormais disponible. La version introduit de nouvelles fonctionnalités telles que l’actualisation de transmission de type push, mais également un certain nombre de changements cassants. Ces changements ne sont pas rétro-compatibles avec les configurations qui utilisaient la version précédente de la bibliothèque. Pour les rubriques suivantes.

* ID de groupe et d’artefact
* Profils de Spring
* Chargement et rechargement de la configuration
* Chargement des indicateurs de fonctionnalités

Cet article fournit une référence sur les modifications et les actions nécessaires à la migration vers la nouvelle version de la bibliothèque.

## <a name="group-and-artifact-id-changed"></a>ID de groupe et d’artefact modifiés

Les ID de groupe et d’artefact de toutes les bibliothèques Azure Spring Boot ont été mis à jour pour correspondre à un nouveau format. Les nouveaux noms de packages sont les suivants :

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
```

## <a name="use-of-spring-profiles"></a>Utilisation de profils Spring

Dans la version précédente, les profils Spring étaient utilisés dans le cadre de la configuration afin qu’ils puissent correspondre au format des fichiers de configuration. Par exemple,

```properties
/<application name>_dev/config.message
```

Cela a été modifié afin que la ou les étiquettes par défaut dans une requête soient des profils Spring au format suivant, avec une étiquette qui correspond au profil Spring :

```properties
/application/config.message
```

 Pour effectuer la conversion vers le nouveau format, vous pouvez exécuter les commandes avec le nom de votre magasin :

```azurecli
az appconfig kv export -n your-stores-name -d file --format properties --key /application_dev* --prefix /application_dev/ --path convert.properties --skip-features --yes
az appconfig kv import -n your-stores-name -s file --format properties --label dev --prefix /application/ --path convert.properties --skip-features --yes
```

ou utiliser la fonctionnalité Import/Export dans le portail.

Lorsque vous êtes complètement déplacé vers la nouvelle version, vous pouvez supprimer les anciennes clés en exécutant :

```azurecli
az appconfig kv delete -n ConversionTest --key /application_dev/*
```

Cette commande répertorie toutes les clés que vous allez supprimer pour vous permettre de vérifier qu’aucune clé inattendue n’est supprimée. Les clés peuvent également être supprimées dans le portail.

## <a name="which-configurations-are-loaded"></a>Quelles configurations sont chargées

Le cas par défaut de chargement de la correspondance de configuration `/applicaiton/*` n’a pas changé. La modification est que `/${spring.application.name}/*` ne sera pas utilisé en supplément automatiquement, sauf s’il est défini. Au lieu d’utiliser `/${spring.application.name}/*`, vous pouvez utiliser la nouvelle configuration Sélections.

```properties
spring.cloud.azure.appconfiguration.stores[0].selects[0].key-filter=/${spring.application.name}/*
```

## <a name="configuration-reloading"></a>Recharge de la configuration

L’analyse de tous les magasins de configuration est désormais désactivée par défaut. Une nouvelle configuration a été ajoutée à la bibliothèque pour permettre l’activation de l’analyse des magasins de configuration. En outre, l’expiration du cache a été renommée en intervalle d’actualisation et a également été modifiée pour être par magasin de configuration. En outre, si l’analyse d’un magasin de configuration est activée, il est nécessaire de configurer au moins une clé surveillée, avec une étiquette facultative.

```properties
spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled
spring.cloud.azure.appconfiguration.stores[0].monitoring.refresh-interval
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].key
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].label
```

Il n’y a pas eu de modification du fonctionnement de l’intervalle d’actualisation, la modification renomme la configuration pour clarifier les fonctionnalités. La nécessité d’une clé surveillée permet de s’assurer que lors de la modification des configurations, la bibliothèque ne tente pas de charger les configurations tant que toutes les modifications n’ont pas été effectuées.

## <a name="feature-flag-loading"></a>Chargement des indicateurs de fonctionnalités

Par défaut, le chargement des indicateurs de fonctionnalités est maintenant désactivé. En outre, les indicateurs de fonctionnalité disposent désormais d’un filtre d’étiquette et d’un intervalle d’actualisation.

```properties
spring.cloud.azure.appconfiguration.stores[0].feature-flags.enable
spring.cloud.azure.appconfiguration.stores[0].feature-flags.label-filter
spring.cloud.azure.appconfiguration.stores[0].monitoring.feature-flag-refresh-interval
```
