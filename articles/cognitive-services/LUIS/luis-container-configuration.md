---
title: Paramètres de conteneur Docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: L’environnement d’exécution de conteneur LUIS est configuré à l’aide des arguments de la commande `docker run`. LUIS a plusieurs paramètres obligatoires et quelques paramètres facultatifs.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 98828589832d69ada11205e471314a153a566766
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080263"
---
# <a name="configure-containers"></a>Configurer des conteneurs

L’environnement d’exécution de conteneur LUIS (Language Understanding) est configuré à l’aide des arguments de la commande `docker run`. LUIS a plusieurs paramètres obligatoires et quelques paramètres facultatifs. Plusieurs [exemples](#example-docker-run-commands) de commande sont disponibles. Les paramètres propres aux conteneurs sont les [paramètres de montage](#mount-settings) d’entrée et les paramètres de facturation. 

Les paramètres de conteneur sont [hiérarchiques](#hierarchical-settings) et peuvent être définis avec des [variables d’environnement](#environment-variable-settings) ou des [arguments de ligne de commande](#command-line-argument-settings) docker.

## <a name="configuration-settings"></a>Paramètres de configuration

Ce conteneur a les paramètres de configuration suivants :

|Obligatoire|Paramètre|Objectif|
|--|--|--|
|Oui|[ApiKey](#apikey-setting)|Utilisé pour le suivi des informations de facturation.|
|Non |[ApplicationInsights](#applicationinsights-setting)|Vous permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur.|
|Oui|[Facturation](#billing-setting)|Spécifie l’URI de point de terminaison de la ressource _Language Understanding_ sur Azure.|
|Oui|[Eula](#eula-setting)| Indique que vous avez accepté la licence pour le conteneur.|
|Non |[Fluentd](#fluentd-settings)|Écrire les données des journaux et, éventuellement, des métriques, sur un serveur Fluentd.|
|Non |[Journalisation](#logging-settings)|Fournit la prise en charge de la journalisation ASP.NET Core pour votre conteneur. |
|Oui|[Mounts](#mount-settings)|Lire et écrire des données de l’[ordinateur hôte](luis-container-howto.md#the-host-computer) sur le conteneur, et du conteneur sur l’ordinateur hôte.|

> [!IMPORTANT]
> Les paramètres [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) et [`Eula`](#eula-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Paramètre ApiKey

Le paramètre `ApiKey` spécifie la clé de ressource Azure utilisée pour effectuer le suivi des informations de facturation pour le conteneur. Vous devez spécifier une valeur pour ApiKey, et il doit s’agir d’une clé valide pour la ressource _Language Understanding_ spécifiée pour le paramètre de configuration [`Billing`](#billing-setting).

Vous trouverez ce paramètre à deux emplacements :

* Portail Azure : Gestion des ressources de **Language Understanding**, sous **Clés**
* Portail LUIS : Page **Keys and Endpoint settings** (Paramètres des clés et du point de terminaison). 

N’utilisez pas la clé de démarrage ou la clé de création. 

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

Le paramètre `ApplicationInsights` vous permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur. Application Insights assure une supervision approfondie de votre conteneur. Vous pouvez facilement superviser la disponibilité, les performances et l’utilisation de votre conteneur. De plus, vous pouvez identifier et diagnostiquer rapidement les erreurs dans votre conteneur.

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `ApplicationInsights`.

|Obligatoire| NOM | Type de données | Description |
|--|------|-----------|-------------|
|Non | `InstrumentationKey` | Chaîne | Clé d’instrumentation de l’instance Application Insights à laquelle les données de télémétrie du conteneur sont envoyées. Pour plus d’informations, consultez [Application Insights pour ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exemple :<br>`InstrumentationKey=123456789`|


## <a name="billing-setting"></a>Paramètre Billing

Le paramètre `Billing` spécifie l’URI du point de terminaison de la ressource _Language Understanding_ sur Azure servant à mesurer les informations de facturation du conteneur. Vous devez attribuer une valeur à ce paramètre de configuration, et il doit s’agir d’un URI de point de terminaison valide pour une ressource _Language Understanding_ sur Azure.

Vous trouverez ce paramètre à deux emplacements :

* Portail Azure : Vue d’ensemble de **Language Understanding**, étiquetée `Endpoint`
* Portail LUIS : Page **Keys and Endpoint settings** (Paramètres des clés et du point de terminaison), dans le cadre de l’URI de point de terminaison.

|Obligatoire| NOM | Type de données | Description |
|--|------|-----------|-------------|
|Oui| `Billing` | Chaîne | URI du point de terminaison de facturation<br><br>Exemple :<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Paramètre Eula

Le paramètre `Eula` indique que vous avez accepté la licence pour le conteneur. Vous devez attribuer à ce paramètre de configuration une valeur qui doit être définie sur `accept`.

|Obligatoire| NOM | Type de données | Description |
|--|------|-----------|-------------|
|Oui| `Eula` | Chaîne | Acceptation de la licence<br><br>Exemple :<br>`Eula=accept` |

Les conteneurs Cognitive Services sont accordés sous licence selon les termes d’[un contrat](https://go.microsoft.com/fwlink/?linkid=2018657) qui régit votre utilisation d’Azure. Si vous ne disposez pas d’un contrat existant régissant votre utilisation d’Azure, vous acceptez que votre utilisation d’Azure soit régie par le [Contrat d’abonnement à Microsoft Online](https://go.microsoft.com/fwlink/?linkid=2018755), qui intègre les [conditions des services en ligne](https://go.microsoft.com/fwlink/?linkid=2018760). Pour les préversions, vous acceptez également les [conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://go.microsoft.com/fwlink/?linkid=2018815). En utilisant le conteneur, vous acceptez les termes du contrat.

## <a name="fluentd-settings"></a>Paramètres Fluentd

Fluentd est un collecteur de données open source pour la journalisation unifiée. Les paramètres `Fluentd` gèrent la connexion du conteneur à un serveur [Fluentd](https://www.fluentd.org). Le conteneur LUIS comprend un fournisseur de journalisation Fluentd qui permet à votre conteneur d’écrire des données de journaux et, éventuellement, de métriques, sur un serveur Fluentd.

Le tableau suivant décrit les paramètres de configuration pris en charge sous la section `Fluentd`.

| NOM | Type de données | Description |
|------|-----------|-------------|
| `Host` | Chaîne | Adresse IP ou nom d’hôte DNS du serveur Fluentd. |
| `Port` | Entier  | Port du serveur Fluentd.<br/> La valeur par défaut est 24224. |
| `HeartbeatMs` | Entier  | Intervalle de pulsation, en millisecondes. Si aucun trafic d’événement n’est envoyé avant l’expiration de cet intervalle, une pulsation est envoyée au serveur Fluentd. La valeur par défaut est de 60 000 millisecondes (1 minute). |
| `SendBufferSize` | Entier  | Espace de mémoire tampon réseau, en octets, alloué pour les opérations d’envoi. La valeur par défaut est de 32 768 octets (32 kilo-octets). |
| `TlsConnectionEstablishmentTimeoutMs` | Entier  | Délai d’attente, en millisecondes, pour établir une connexion SSL/TLS avec le serveur Fluentd. La valeur par défaut est de 10 000 millisecondes (10 secondes).<br/> Si `UseTLS` est défini sur false, cette valeur est ignorée. |
| `UseTLS` | Booléen | Indique si le conteneur doit utiliser SSL/TLS pour communiquer avec le serveur Fluentd. La valeur par défaut est false. |

## <a name="logging-settings"></a>Paramètres de journalisation

Les paramètres `Logging` gèrent la prise en charge de la journalisation ASP.NET Core pour votre conteneur. Vous pouvez utiliser pour votre conteneur les mêmes paramètres et valeurs de configuration que ceux d’une application ASP.NET Core. 

Le conteneur LUIS prend en charge les fournisseurs de journalisation suivants :

|Fournisseur|Objectif|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Fournisseur de journalisation `Console` ASP.NET Core. Tous les paramètres de configuration ASP.NET Core et les valeurs par défaut de ce fournisseur de journalisation sont pris en charge.|
|[Déboguer](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Fournisseur de journalisation `Debug` ASP.NET Core. Tous les paramètres de configuration ASP.NET Core et les valeurs par défaut de ce fournisseur de journalisation sont pris en charge.|
|[Disque](#disk-logging)|Fournisseur de journalisation JSON. Ce fournisseur de journalisation écrit les données de journal dans le montage de sortie.|

### <a name="disk-logging"></a>Journalisation Disk
  
Le fournisseur de journalisation `Disk` prend en charge les paramètres de configuration suivants :  

| NOM | Type de données | Description |
|------|-----------|-------------|
| `Format` | Chaîne | Format de sortie des fichiers journaux.<br/> **Remarque :** Cette valeur doit être `json` pour activer le fournisseur de journalisation. Si cette valeur est spécifiée sans que le montage de sortie soit aussi spécifié pendant l’instanciation d’un conteneur, une erreur se produit. |
| `MaxFileSize` | Entier  | Taille maximale, en mégaoctets (Mo), d’un fichier journal. Dès que la taille du fichier journal actif atteint ou dépasse cette valeur, un nouveau fichier journal est commencé par le fournisseur de journalisation. Si la valeur -1 est spécifiée, la taille du fichier journal est limitée uniquement par la taille de fichier maximale, le cas échéant, pour le montage de sortie. La valeur par défaut est 1. |

Pour plus d’informations sur la configuration de la prise en charge de la journalisation ASP.NET Core, consultez [Configuration d’un fichier de paramètres](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mount-settings"></a>Paramètres de montage

Utilisez des montages de liaisons pour lire et écrire des données vers et à partir du conteneur. Vous pouvez spécifier un montage d’entrée ou de sortie en spécifiant l’option `--mount` dans la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/). 

Le conteneur LUIS n’utilise pas de montage d’entrée ou de sortie pour stocker des données d’entraînement ou de service. 

La syntaxe exacte de l’emplacement de montage d’hôte varie en fonction du système d’exploitation hôte. De plus, l’emplacement de montage de l’[ordinateur hôte](luis-container-howto.md#the-host-computer) peut ne pas être accessible en raison d’un conflit entre les autorisations utilisées par le compte de service docker et les autorisations de l’emplacement de montage de l’hôte. 

Le tableau suivant décrit les paramètres pris en charge.

|Obligatoire| NOM | Type de données | Description |
|-------|------|-----------|-------------|
|Oui| `Input` | Chaîne | Cible du montage d’entrée. La valeur par défaut est `/input`. Il s’agit de l’emplacement des fichiers de package LUIS. <br><br>Exemple :<br>`--mount type=bind,src=c:\input,target=/input`|
|Non | `Output` | Chaîne | Cible du montage de sortie. La valeur par défaut est `/output`. Il s’agit de l’emplacement des journaux. Cela comprend les journaux de requêtes LUIS et les journaux de conteneur. <br><br>Exemple :<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Paramètres hiérarchiques

Les paramètres pour le conteneur LUIS sont hiérarchiques, et tous les conteneurs sur l’[ordinateur hôte](luis-container-howto.md#the-host-computer) utilisent une hiérarchie partagée.

Vous pouvez utiliser l’une des options suivantes pour spécifier les paramètres :

* [Variables d’environnement](#environment-variable-settings)
* [Arguments de ligne de commande](#command-line-argument-settings)

Les valeurs de variable d’environnement remplacent les valeurs d’argument de ligne de commande, qui elles-mêmes remplacent les valeurs par défaut de l’image conteneur. Si vous spécifiez des valeurs différentes dans une variable d’environnement et dans un argument de ligne de commande pour le même paramètre de configuration, la valeur de la variable d’environnement est utilisée par le conteneur instancié.

|Precedence|Emplacement du paramètre|
|--|--|
|1|Variable d’environnement| 
|2|Ligne de commande|
|3|Valeur par défaut d’image conteneur|

### <a name="environment-variable-settings"></a>Paramètres de variables d’environnement

L’utilisation de variables d’environnement offre les avantages suivants :

* Plusieurs paramètres peuvent être configurés.
* Plusieurs conteneurs peuvent utiliser les mêmes paramètres.

### <a name="command-line-argument-settings"></a>Paramètres d’arguments de ligne de commande

L’utilisation d’arguments de ligne de commande présente l’avantage de permettre à chaque conteneur d’utiliser différents paramètres.

## <a name="example-docker-run-commands"></a>Exemples de commandes docker run

Les exemples suivants utilisent les paramètres de configuration pour illustrer comment écrire et utiliser des commandes `docker run`.  Une fois en cours d’exécution, le conteneur continue à s’exécuter jusqu’à ce que vous l’[arrêtiez](luis-container-howto.md#stop-the-container).


* **Caractère de continuation de ligne** : les commandes docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte. 
* **Ordre des arguments** : ne changez pas l’ordre des arguments, sauf si vous avez une connaissance approfondie des conteneurs docker.

Remplacez {_argument_name_} par vos propres valeurs :

| Placeholder | Valeur | Format ou exemple |
|-------------|-------|---|
|{ENDPOINT_KEY} | Clé de point de terminaison de l’application LUIS entraînée. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | La valeur de point de terminaison de facturation est disponible dans la page Vue d’ensemble de Language Understanding du portail Azure.|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](luis-container-howto.md#billing).
> La valeur ApiKey est la **clé** mentionnée dans la page Keys and Endpoints dans le portail LUIS. Elle est également disponible dans la page Clés de ressources Language Understanding d’Azure. 

### <a name="basic-example"></a>Exemple de base

L’exemple suivant contient le moins d’arguments possible pour exécuter le conteneur :

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> La commande précédente utilise le répertoire du lecteur `c:` pour éviter tout conflit d’autorisation sur Windows. Si vous devez utiliser un répertoire spécifique en tant que répertoire d’entrée, vous devrez peut-être accorder au docker une autorisation de service. La commande docker précédente utilise la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences du système d’exploitation de votre [ordinateur hôte](luis-container-howto.md#the-host-computer). Ne changez pas l’ordre des arguments, sauf si vous avez une connaissance approfondie des conteneurs docker.


### <a name="applicationinsights-example"></a>Exemple ApplicationInsights

L’exemple suivant définit l’argument ApplicationInsights pour envoyer des données de télémétrie à Application Insights pendant l’exécution du conteneur :

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>Exemple de journalisation avec arguments de ligne de commande

La commande suivante définit le niveau de journalisation, `Logging:Console:LogLevel`, pour configurer le niveau de journalisation sur [`Information`](https://msdn.microsoft.com). 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>Exemple de journalisation avec variable d’environnement

Les commandes suivantes utilisent une variable d’environnement nommée `Logging:Console:LogLevel` pour configurer le niveau de journalisation sur [`Information`](https://msdn.microsoft.com). 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Guide pratique pour installer et exécuter des conteneurs](luis-container-howto.md).
* Consultez les [Questions fréquentes (FAQ)](luis-resources-faq.md) pour résoudre les problèmes liés à la fonctionnalité LUIS.