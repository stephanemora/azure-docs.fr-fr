---
title: Configurer des conteneurs
titlesuffix: Text Analytics - Azure Cognitive Services
description: Analyse de texte fournit pour chaque conteneur une infrastructure de configuration commune qui vous permettre de configurer et de gérer facilement les paramètres de stockage, de journalisation, de télémétrie et de sécurité de vos conteneurs.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 7e993b9ccc57359ac64186765b7b704535eb5a57
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086672"
---
# <a name="configure-containers"></a>Configurer des conteneurs

Analyse de texte fournit pour chaque conteneur une infrastructure de configuration commune qui vous permettre de configurer et de gérer facilement les paramètres de stockage, de journalisation, de télémétrie et de sécurité de vos conteneurs.

## <a name="configuration-settings"></a>Paramètres de configuration

Les paramètres de configuration dans les conteneurs Analyse de texte sont hiérarchiques, et tous les conteneurs utilisent une hiérarchie partagée qui est basée sur la structure de niveau supérieur suivante :

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Authentification](#authentication-configuration-settings)
* [Facturation](#billing-configuration-setting)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Journalisation](#logging-configuration-settings)
* [Mounts](#mounts-configuration-settings)

Vous pouvez utiliser des [variables d'environnement](#configuration-settings-as-environment-variables) ou des [arguments de ligne de commande](#configuration-settings-as-command-line-arguments) pour spécifier les paramètres de configuration lors de l'instanciation d'un conteneur à partir de conteneurs Analyse de texte.

Les valeurs de variable d’environnement remplacent les valeurs d’argument de ligne de commande, qui elles-mêmes remplacent les valeurs par défaut de l’image conteneur. En d’autres termes, si vous spécifiez une valeur différente dans une variable d’environnement et dans un argument de ligne de commande pour un même paramètre de configuration, comme `Logging:Disk:LogLevel`, puis que vous instanciez un conteneur, la valeur de la variable d’environnement est utilisée par le conteneur instancié.

### <a name="configuration-settings-as-environment-variables"></a>Paramètres de configuration comme variables d’environnement

Vous pouvez utiliser la [syntaxe des variables d’environnement ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) pour spécifier des paramètres de configuration.

Le conteneur lit les variables d’environnement utilisateur au moment où le conteneur est instancié. S’il existe une variable d’environnement, sa valeur remplace la valeur par défaut du paramètre de configuration spécifié. L’utilisation de variables d’environnement présente l’avantage de pouvoir définir plusieurs paramètres de configuration avant d’instancier des conteneurs, et le même ensemble de paramètres de configuration peut être utilisé automatiquement par plusieurs conteneurs.

Par exemple, les commandes suivantes utilisent une variable d’environnement pour configurer le niveau de journalisation de console avec [LogLevel.Information](https://msdn.microsoft.com), puis instancient un conteneur à partir de l’image conteneur Analyse des sentiments. La valeur de la variable d’environnement remplace le paramètre de configuration par défaut.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Paramètres de configuration comme arguments de ligne de commande

Vous pouvez utiliser la [syntaxe des arguments de ligne de commande ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) pour spécifier des paramètres de configuration.

Vous pouvez spécifier des paramètres de configuration dans le paramètre facultatif `ARGS` de la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) utilisée pour instancier un conteneur à partir d’une image conteneur téléchargée. L’utilisation d’arguments de ligne de commande présente l’avantage de permettre à chaque conteneur d’utiliser un ensemble de paramètres de configuration distinct et personnalisé.

Par exemple, la commande suivante instancie un conteneur à partir de l’image de conteneur Analyse des sentiments et configure le niveau de journalisation de console sur LogLevel.Information, remplaçant ainsi le paramètre de configuration par défaut.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Paramètre de configuration ApiKey

Le paramètre de configuration `ApiKey` permet de spécifier la clé de configuration de la ressource Analyse de texte sur Azure servant à suivre les informations de facturation du conteneur. Vous devez attribuer une valeur à ce paramètre de configuration, et cette valeur doit être une clé de configuration valide pour la ressource Analyse de texte spécifiée pour le paramètre de configuration [`Billing`](#billing-configuration-setting).

> [!IMPORTANT]
> Les paramètres de configuration [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-configuration-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Paramètres de configuration ApplicationInsights

Les paramètres de configuration de la section `ApplicationInsights` vous permettent d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur. Application Insights assure une supervision approfondie de votre conteneur jusqu’au niveau du code. Vous pouvez facilement superviser la disponibilité, les performances et l’utilisation de votre conteneur. De plus, vous pouvez identifier et diagnostiquer rapidement les erreurs dans votre conteneur sans attendre qu’un utilisateur les signale.

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `ApplicationInsights`.

| NOM | Type de données | Description |
|------|-----------|-------------|
| `InstrumentationKey` | Chaîne | Clé d’instrumentation de l’instance Application Insights à laquelle les données de télémétrie du conteneur sont envoyées. Pour plus d’informations, consultez [Application Insights pour ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Paramètres de configuration Authentication

Les paramètres de configuration `Authentication` fournissent des options de sécurité Azure pour votre conteneur. Bien que les paramètres de configuration de cette section soient disponibles pour tous les conteneurs des conteneurs Analyse de texte, la manière dont les valeurs des paramètres de configuration sont utilisées est spécifique à chaque conteneur, et les conteneurs peuvent ne pas utiliser cette section du tout.

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `Authentication`.

| NOM | Type de données | Description |
|------|-----------|-------------|
| `ApiKey` | Chaîne ou tableau | Les clés d’abonnement Azure utilisées par le conteneur pour accéder aux autres ressources Azure le cas échéant.<br/> Si plusieurs clés d’abonnement sont utilisées par le conteneur, cette valeur est spécifiée en tant que tableau de chaînes ; sinon, une valeur de chaîne est utilisée pour spécifier une clé d’abonnement unique utilisée par le conteneur. |

## <a name="billing-configuration-setting"></a>Paramètre de configuration Billing

Le paramètre de configuration `Billing` permet de spécifier l’URI du point de terminaison de la ressource Analyse de texte sur Azure servant à contrôler les informations de facturation du conteneur. Vous devez attribuer une valeur à ce paramètre de configuration, et cette valeur doit être un URI de point de terminaison valide pour une ressource Analyse de texte sur Azure.

> [!IMPORTANT]
> Les paramètres de configuration [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-configuration-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="eula-configuration-setting"></a>Paramètre de configuration Eula

Le paramètre de configuration `Eula` indique que vous avez accepté la licence pour le conteneur. Vous devez attribuer à ce paramètre de configuration une valeur qui doit être définie sur `accept`.

> [!IMPORTANT]
> Les paramètres de configuration [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-configuration-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](how-tos/text-analytics-how-to-install-containers.md#billing).

Les conteneurs Cognitive Services sont accordés sous licence selon les termes d’[un contrat](https://go.microsoft.com/fwlink/?linkid=2018657) qui régit votre utilisation d’Azure. Si vous ne disposez pas d’un contrat existant régissant votre utilisation d’Azure, vous acceptez que celle-ci soit régie par le [Contrat d’abonnement à Microsoft Online](https://go.microsoft.com/fwlink/?linkid=2018755) (qui intègre les [conditions des services en ligne](https://go.microsoft.com/fwlink/?linkid=2018760)). Pour les préversions, vous acceptez également les [conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://go.microsoft.com/fwlink/?linkid=2018815). En utilisant le conteneur, vous acceptez les termes du contrat.

## <a name="fluentd-configuration-settings"></a>Paramètres de configuration Fluentd

La section `Fluentd` gère les paramètres de configuration de [Fluentd](https://www.fluentd.org), un collecteur de données open source pour la journalisation unifiée. Les conteneurs Analyse de texte comprennent un fournisseur de journalisation Fluentd qui permet à votre conteneur d’écrire des journaux et, éventuellement, des données de métriques sur un serveur Fluentd.

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `Fluentd`.

| NOM | Type de données | Description |
|------|-----------|-------------|
| `Host` | Chaîne | Adresse IP ou nom d’hôte DNS du serveur Fluentd. |
| `Port` | Entier  | Port du serveur Fluentd.<br/> La valeur par défaut est 24224. |
| `HeartbeatMs` | Entier  | Intervalle de pulsation, en millisecondes. Si aucun trafic d’événement n’est envoyé avant l’expiration de cet intervalle, une pulsation est envoyée au serveur Fluentd. La valeur par défaut est de 60 000 millisecondes (1 minute). |
| `SendBufferSize` | Entier  | Espace de mémoire tampon réseau, en octets, alloué pour les opérations d’envoi. La valeur par défaut est de 32 768 octets (32 kilo-octets). |
| `TlsConnectionEstablishmentTimeoutMs` | Entier  | Délai d’attente, en millisecondes, pour établir une connexion SSL/TLS avec le serveur Fluentd. La valeur par défaut est de 10 000 millisecondes (10 secondes).<br/> Si `UseTLS` est défini sur false, cette valeur est ignorée. |
| `UseTLS` | Booléen | Indique si le conteneur doit utiliser SSL/TLS pour communiquer avec le serveur Fluentd. La valeur par défaut est false. |

## <a name="logging-configuration-settings"></a>Paramètres de configuration Logging

Les paramètres de configuration `Logging` gèrent la prise en charge de la journalisation ASP.NET Core pour votre conteneur. Vous pouvez utiliser pour votre conteneur les mêmes paramètres et valeurs de configuration que ceux d’une application ASP.NET Core. Le conteneur Analyse de texte prend en charge les fournisseurs de journalisation suivants :

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  Fournisseur de journalisation `Console` ASP.NET Core. Tous les paramètres de configuration ASP.NET Core et les valeurs par défaut de ce fournisseur de journalisation sont pris en charge.
* [Déboguer](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  Fournisseur de journalisation `Debug` ASP.NET Core. Tous les paramètres de configuration ASP.NET Core et les valeurs par défaut de ce fournisseur de journalisation sont pris en charge.
* Disque  
  Fournisseur de journalisation JSON. Ce fournisseur de journalisation écrit les données de journal dans le montage de sortie.  
  Le fournisseur de journalisation `Disk` prend en charge les paramètres de configuration suivants :  

  | NOM | Type de données | Description |
  |------|-----------|-------------|
  | `Format` | Chaîne | Format de sortie des fichiers journaux.<br/> **Remarque :** Cette valeur doit être `json` pour activer le fournisseur de journalisation. Si cette valeur est spécifiée sans que le montage de sortie soit aussi spécifié pendant l’instanciation d’un conteneur, une erreur se produit. |
  | `MaxFileSize` | Entier  | Taille maximale, en mégaoctets (Mo), d’un fichier journal. Dès que la taille du fichier journal actif atteint ou dépasse cette valeur, un nouveau fichier journal est commencé par le fournisseur de journalisation. Si la valeur -1 est spécifiée, la taille du fichier journal est limitée uniquement par la taille de fichier maximale, le cas échéant, pour le montage de sortie. La valeur par défaut est 1. |

Pour plus d’informations sur la configuration de la prise en charge de la journalisation ASP.NET Core, consultez [Configuration d’un fichier de paramètres](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mounts-configuration-settings"></a>Paramètres de configuration Mounts

Les conteneurs Docker fournis par API Analyse de texte sont conçus pour être sans état et immuables. En d’autres termes, les fichiers créés à l’intérieur d’un conteneur sont stockés dans une couche conteneur accessible en écriture, qui persiste uniquement le temps de l’exécution du conteneur et n’est pas facilement accessible. Si ce conteneur est arrêté ou supprimé, les fichiers créés à l’intérieur de ce conteneur sont détruits avec lui.

Cependant, s’agissant de conteneurs Docker, vous pouvez utiliser les options de stockage Docker, comme les volumes et les montages de liaison, pour lire et écrire des données persistantes en dehors du conteneur, si le conteneur le permet. Pour plus d’informations sur la définition et la gestion des options de stockage Docker, consultez [Gérer les données dans Docker](https://docs.docker.com/storage/).

> [!NOTE]
> En principe, vous n’aurez pas besoin de modifier les valeurs de ces paramètres de configuration. En revanche, vous utiliserez des valeurs spécifiées dans ces paramètres de configuration comme destinations au moment de spécifier les montages d’entrée et de sortie de votre conteneur. Pour plus d’informations sur la spécification des montages d’entrée et de sortie, consultez [Montages d’entrée et de sortie](#input-and-output-mounts).

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `Mounts`.

| NOM | Type de données | Description |
|------|-----------|-------------|
| `Input` | Chaîne | Cible du montage d’entrée. La valeur par défaut est `/input`. |
| `Output` | Chaîne | Cible du montage de sortie. La valeur par défaut est `/output`. |

### <a name="input-and-output-mounts"></a>Montages d’entrée et de sortie

Par défaut, chaque conteneur peut prendre en charge un *montage d’entrée*, dans lequel le conteneur peut lire des données, et un *montage de sortie*, dans lequel le conteneur peut écrire des données. Cependant, les conteneurs ne prennent pas forcément en charge les montages d’entrée ou de sortie, et chaque conteneur peut utiliser à la fois des montages d’entrée et de sortie à des fins spécifiques de conteneur en plus des options de journalisation prises en charge par le conteneur Analyse de texte. Le tableau suivant répertorie la prise en charge de montage d'entrée et de sortie pour chaque conteneur dans les conteneurs Analyse de texte.

| Conteneur | Montage d’entrée | Montage de sortie |
|-----------|-------------|--------------|
|[Extraction d’expressions clés](#working-with-key-phrase-extraction) | Non pris en charge | Facultatif |
|[Détection de la langue](#working-with-language-detection) | Non pris en charge | Facultatif |
|[Analyse des sentiments](#working-with-sentiment-analysis) | Non pris en charge | Facultatif |

Vous pouvez spécifier un montage d’entrée et un montage de sortie en spécifiant l’option `--mount` de la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) utilisée pour instancier un conteneur à partir d’une image conteneur téléchargée. Par défaut, le montage d’entrée utilise `/input` comme destination, et le montage de sortie `/output`. Toute option de stockage Docker accessible à l’hôte de conteneur Docker peut être spécifiée dans l’option `--mount`.

Par exemple, la commande suivante définit un montage de liaison Docker sur le dossier `D:\Output` de la machine hôte comme montage de sortie, puis instancie un conteneur à partir de l’image de conteneur Analyse des sentiments, les fichiers journaux étant enregistrés au format JSON sur le montage de sortie.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
