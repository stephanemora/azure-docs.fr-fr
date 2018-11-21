---
title: Configurer des conteneurs
titlesuffix: Computer Vision - Cognitive Services - Azure
description: Paramètres de configuration des conteneurs dans Vision par ordinateur.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 8df293878486a9cd4ded6e899871c30498c4b68f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634924"
---
# <a name="configure-containers"></a>Configurer des conteneurs

Vision par ordinateur fournit le conteneur Reconnaître le texte avec un framework de configuration commun pour vous permettre de configurer et de gérer facilement les paramètres de stockage, de journalisation, de télémétrie et de sécurité pour vos conteneurs.

## <a name="configuration-settings"></a>Paramètres de configuration

Les paramètres de configuration des conteneurs Vision par ordinateur sont hiérarchiques, et tous les conteneurs utilisent une hiérarchie partagée qui est basée sur la structure de niveau supérieur suivante :

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Authentification](#authentication-configuration-settings)
* [Facturation](#billing-configuration-setting)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Journalisation](#logging-configuration-settings)
* [Mounts](#mounts-configuration-settings)

Vous pouvez utiliser des [variables d’environnement](#configuration-settings-as-environment-variables) ou des [arguments de ligne de commande](#configuration-settings-as-command-line-arguments) pour spécifier des paramètres de configuration lors de l’instanciation d’un conteneur à partir des conteneurs Vision par ordinateur.

Les valeurs de variable d’environnement remplacent les valeurs d’argument de ligne de commande, qui elles-mêmes remplacent les valeurs par défaut de l’image conteneur. En d’autres termes, si vous spécifiez une valeur différente dans une variable d’environnement et dans un argument de ligne de commande pour un même paramètre de configuration, comme `Logging:Disk:LogLevel`, puis que vous instanciez un conteneur, la valeur de la variable d’environnement est utilisée par le conteneur instancié.

### <a name="configuration-settings-as-environment-variables"></a>Paramètres de configuration comme variables d’environnement

Vous pouvez utiliser la [syntaxe de variable d’environnement ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) pour spécifier des paramètres de configuration.

Le conteneur lit les variables d’environnement utilisateur quand le conteneur est instancié. S’il existe une variable d’environnement, sa valeur remplace la valeur par défaut du paramètre de configuration spécifié. L’avantage d’utiliser des variables d’environnement est que cela permet de définir plusieurs paramètres de configuration avant d’instancier des conteneurs, et le même ensemble de paramètres de configuration peut être utilisé automatiquement par plusieurs conteneurs.

Par exemple, les commandes suivantes utilisent une variable d’environnement pour configurer le niveau de journalisation de console avec [LogLevel.Information](https://msdn.microsoft.com), puis instancient un conteneur à partir de l’image conteneur Reconnaître le texte. La valeur de la variable d’environnement remplace le paramètre de configuration par défaut.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Paramètres de configuration comme arguments de ligne de commande

Vous pouvez utiliser la [syntaxe d’argument de ligne de commande ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) pour spécifier des paramètres de configuration.

Vous pouvez spécifier des paramètres de configuration dans le paramètre facultatif `ARGS` de la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) utilisée pour instancier un conteneur à partir d’une image conteneur téléchargée. L’avantage d’utiliser des arguments de ligne de commande est que chaque conteneur peut utiliser un ensemble de paramètres de configuration différent et personnalisé.

Par exemple, la commande suivante instancie un conteneur à partir de l’image du conteneur Reconnaître le texte et configure le niveau de journalisation de console avec LogLevel.Information, remplaçant ainsi le paramètre de configuration par défaut.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Paramètre de configuration ApiKey

Le paramètre de configuration `ApiKey` permet de spécifier la clé de configuration de la ressource Vision par ordinateur sur Azure servant à faire le suivi des informations de facturation du conteneur. Vous devez affecter à ce paramètre de configuration une valeur qui doit être une clé de configuration valide pour la ressource Vision par ordinateur spécifiée pour le paramètre de configuration [`Billing`](#billing-configuration-setting).

> [!IMPORTANT]
> Les paramètres de configuration [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-configuration-setting) sont utilisés ensemble, et vous devez fournir des valeurs valides pour les trois ; sinon, votre conteneur ne démarrera pas. Pour plus d’informations sur l’utilisation de ces paramètres de configuration pour instancier un conteneur, consultez [Billing](computer-vision-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Paramètres de configuration Application Insights

Les paramètres de configuration de la section `ApplicationInsights` vous permettent d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur. Application Insights fournit une supervision approfondie de votre conteneur jusqu’au niveau du code. Vous pouvez facilement superviser la disponibilité, les performances et l’utilisation de votre conteneur. De plus, vous pouvez identifier et diagnostiquer rapidement les erreurs dans votre conteneur sans attendre qu’un utilisateur les signale.

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `ApplicationInsights`.

| NOM | Type de données | Description |
|------|-----------|-------------|
| `InstrumentationKey` | Chaîne | Clé d’instrumentation de l’instance Application Insights à laquelle les données de télémétrie du conteneur sont envoyées. Pour plus d’informations, consultez [Application Insights pour ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Paramètres de configuration Authentication

Les paramètres de configuration `Authentication` fournissent des options de sécurité Azure pour votre conteneur. Bien que les paramètres de configuration de cette section soient disponibles, le conteneur Reconnaître le texte n’utilise pas cette section.

## <a name="billing-configuration-setting"></a>Paramètre de configuration Billing

Le paramètre de configuration `Billing` permet de spécifier l’URI de point de terminaison de la ressource Vision par ordinateur sur Azure servant à faire le suivi des informations de facturation du conteneur. Vous devez affecter à ce paramètre de configuration une valeur qui doit être un URI de point de terminaison valide pour une ressource Vision par ordinateur sur Azure.

> [!IMPORTANT]
> Les paramètres de configuration [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-configuration-setting) sont utilisés ensemble, et vous devez fournir des valeurs valides pour les trois ; sinon, votre conteneur ne démarrera pas. Pour plus d’informations sur l’utilisation de ces paramètres de configuration pour instancier un conteneur, consultez [Billing](computer-vision-how-to-install-containers.md#billing).

## <a name="eula-configuration-setting"></a>Paramètre de configuration Eula

Le paramètre de configuration `Eula` indique que vous avez accepté la licence pour le conteneur. Vous devez affecter à ce paramètre de configuration une valeur qui doit être `accept`.

> [!IMPORTANT]
> Les paramètres de configuration [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-configuration-setting) sont utilisés ensemble, et vous devez fournir des valeurs valides pour les trois ; sinon, votre conteneur ne démarrera pas. Pour plus d’informations sur l’utilisation de ces paramètres de configuration pour instancier un conteneur, consultez [Billing](computer-vision-how-to-install-containers.md#billing).

## <a name="fluentd-configuration-settings"></a>Paramètres de configuration Fluentd

La section `Fluentd` gère les paramètres de configuration de [Fluentd](https://www.fluentd.org), collecteur de données open source pour la journalisation unifiée. Les conteneurs Vision par ordinateur comprennent un fournisseur de journalisation Fluentd qui permet à votre conteneur d’écrire des journaux et, éventuellement, des données de métriques sur un serveur Fluentd.

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `Fluentd`.

| NOM | Type de données | Description |
|------|-----------|-------------|
| `Host` | Chaîne | Adresse IP ou nom d’hôte DNS du serveur Fluentd. |
| `Port` | Entier  | Port du serveur Fluentd.<br/> La valeur par défaut est 24224. |
| `HeartbeatMs` | Entier  | Intervalle de pulsation, en millisecondes. Si aucun trafic d’événement n’a été envoyé avant l’expiration de cet intervalle, une pulsation est envoyée au serveur Fluentd. La valeur par défaut est de 60 000 millisecondes (1 minute). |
| `SendBufferSize` | Entier  | Espace de mémoire tampon réseau, en octets, alloué pour les opérations d’envoi. La valeur par défaut est de 32 768 octets (32 kilo-octets). |
| `TlsConnectionEstablishmentTimeoutMs` | Entier  | Délai d’attente, en millisecondes, pour établir une connexion SSL/TLS avec le serveur Fluentd. La valeur par défaut est de 10 000 millisecondes (10 secondes).<br/> Si `UseTLS` est false, cette valeur est ignorée. |
| `UseTLS` | Booléen | Indique si le conteneur doit utiliser SSL/TLS pour communiquer avec le serveur Fluentd. La valeur par défaut est false. |

## <a name="logging-configuration-settings"></a>Paramètres de configuration Logging

Les paramètres de configuration `Logging` gèrent la prise en charge de la journalisation ASP.NET Core pour votre conteneur. Vous pouvez utiliser les mêmes paramètres de configuration et valeurs pour votre conteneur que ceux d’une application ASP.NET Core. Les fournisseurs de journalisation suivants sont pris en charge par les conteneurs Vision par ordinateur :

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  Fournisseur de journalisation `Console` ASP.NET Core. Tous les paramètres de configuration ASP.NET Core et les valeurs par défaut de ce fournisseur de journalisation sont pris en charge.
* [Déboguer](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  Fournisseur de journalisation `Debug` ASP.NET Core. Tous les paramètres de configuration ASP.NET Core et les valeurs par défaut de ce fournisseur de journalisation sont pris en charge.
* Disque  
  Fournisseur de journalisation JSON. Ce fournisseur de journalisation écrit les données de journal dans le montage de sortie.  
  Le fournisseur de journalisation `Disk` prend en charge les paramètres de configuration suivants :  

  | NOM | Type de données | Description |
  |------|-----------|-------------|
  | `Format` | Chaîne | Format de sortie des fichiers journaux.<br/> **Remarque :** Cette valeur doit être `json` pour activer le fournisseur de journalisation. Si cette valeur est spécifiée mais que le montage de sortie ne l’est pas durant l’instanciation d’un conteneur, une erreur se produit. |
  | `MaxFileSize` | Entier  | Taille maximale, en mégaoctets (Mo), d’un fichier journal. Quand la taille du fichier journal actif atteint ou dépasse cette valeur, un nouveau fichier journal est démarré par le fournisseur de journalisation. Si la valeur -1 est spécifiée, la taille du fichier journal est limitée uniquement par la taille de fichier maximale, le cas échéant, pour le montage de sortie. La valeur par défaut est 1. |

Pour plus d’informations sur la configuration de la prise en charge de la journalisation ASP.NET Core, consultez [Configuration d’un fichier de paramètres](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mounts-configuration-settings"></a>Paramètres de configuration Mounts

Les conteneurs Docker fournis par Vision par ordinateur sont conçus pour être sans état et immuables. En d’autres termes, les fichiers créés à l’intérieur d’un conteneur sont stockés dans une couche conteneur accessible en écriture, qui persiste uniquement le temps de l’exécution du conteneur et qui n’est pas facilement accessible. Si ce conteneur est arrêté ou supprimé, les fichiers créés à l’intérieur de ce conteneur sont détruits avec lui.

Cependant, comme il s’agit de conteneurs Docker, vous pouvez utiliser des options de stockage Docker, comme des volumes et des montages de liaison, pour lire et écrire des données persistantes en dehors du conteneur, si le conteneur le permet. Pour plus d’informations sur la définition et la gestion des options de stockage Docker, consultez [Manage data in Docker](https://docs.docker.com/storage/).

> [!NOTE]
> En principe, vous n’aurez pas besoin de modifier les valeurs de ces paramètres de configuration. En revanche, vous utiliserez les valeurs spécifiées dans ces paramètres de configuration en tant que destinations au moment de spécifier les montages d’entrée et de sortie pour votre conteneur. Pour plus d’informations sur la spécification des montages d’entrée et de sortie, consultez [Montages d’entrée et de sortie](#input-and-output-mounts).

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `Mounts`.

| NOM | Type de données | Description |
|------|-----------|-------------|
| `Input` | Chaîne | Cible du montage d’entrée. La valeur par défaut est `/input`. |
| `Output` | Chaîne | Cible du montage de sortie. La valeur par défaut est `/output`. |

### <a name="input-and-output-mounts"></a>Montages d’entrée et de sortie

Par défaut, chaque conteneur peut prendre en charge un *montage d’entrée*, à partir duquel le conteneur peut lire des données, et un *montage de sortie*, dans lequel le conteneur peut écrire des données. Cependant, les conteneurs ne prennent pas forcément en charge les montages d’entrée ou de sortie, et chaque conteneur peut utiliser à la fois des montages d’entrée et de sortie pour ses propres besoins en plus des options de journalisation prises en charge par les conteneurs Vision par ordinateur.

Le conteneur Reconnaître le texte ne prend pas en charge les montages d’entrée, mais il prend en charge de façon facultative les montages de sortie.

Vous pouvez spécifier un montage d’entrée ou de sortie en spécifiant l’option`--mount` dans la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) utilisée pour instancier un conteneur à partir d’une image conteneur téléchargée. Par défaut, le montage d’entrée utilise `/input` comme destination, et le montage de sortie `/output`. Toute option de stockage Docker disponible pour l’hôte de conteneur Docker peut être spécifiée dans l’option `--mount`.

Par exemple, la commande suivante définit un montage de liaison Docker dans le dossier `D:\Output` de la machine hôte comme montage de sortie, puis instancie un conteneur à partir de l’image conteneur Reconnaître le texte, enregistrant les fichiers journaux au format JSON dans le montage de sortie.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
