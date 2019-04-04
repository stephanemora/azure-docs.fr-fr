---
title: Configurer des conteneurs
titlesuffix: Text Analytics - Azure Cognitive Services
description: Analyse de texte fournit pour chaque conteneur une infrastructure de configuration commune qui vous permettre de configurer et de gérer facilement les paramètres de stockage, de journalisation, de télémétrie et de sécurité de vos conteneurs.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 3cb6f4563cf45b9ccd377dec3db4ebab095c8a09
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885432"
---
# <a name="configure-text-analytics-docker-containers"></a>Configurer les conteneurs Docker Analyse de texte

Analyse de texte fournit pour chaque conteneur une infrastructure de configuration commune qui vous permettre de configurer et de gérer facilement les paramètres de stockage, de journalisation, de télémétrie et de sécurité de vos conteneurs.

## <a name="configuration-settings"></a>Paramètres de configuration

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Les paramètres [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Paramètre de configuration ApiKey

Le paramètre `ApiKey` spécifie la clé de ressource Azure utilisée pour effectuer le suivi des informations de facturation pour le conteneur. Vous devez spécifier une valeur pour ApiKey, et il doit s’agir d’une clé valide pour la ressource _Analyse de texte_ spécifiée pour le paramètre de configuration [`Billing`](#billing-configuration-setting).

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : Gestion des ressources de l’**Analyse de texte**, sous **Clés**

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Paramètre de configuration Billing

Le paramètre `Billing` permet de spécifier l’URI du point de terminaison de la ressource _Analyse de texte_ sur Azure servant à contrôler les informations de facturation du conteneur. Vous devez attribuer une valeur à ce paramètre de configuration, et cette valeur doit être un URI de point de terminaison valide pour une ressource __Analyse de texte_ sur Azure. La conteneur crée des rapports sur l'utilisation toutes les 10 à 15 minutes.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : **Du texte Analytique** vue d’ensemble, étiqueté `Endpoint`

|Obligatoire| Nom | Type de données | Description |
|--|------|-----------|-------------|
|Oui| `Billing` | Chaîne | URI du point de terminaison de facturation<br><br>Exemple :<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0` |

## <a name="eula-setting"></a>Paramètre Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Paramètres Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Paramètres des informations d'identification du proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Paramètres de journalisation
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Paramètres de montage

Utilisez des montages de liaisons pour lire et écrire des données vers et à partir du conteneur. Vous pouvez spécifier un montage d’entrée ou de sortie en spécifiant l’option `--mount` dans la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Les conteneurs Analyse de texte n’utilisent pas de montage d’entrée ou de sortie pour stocker des données d’entraînement ou de service. 

La syntaxe exacte de l’emplacement de montage d’hôte varie en fonction du système d’exploitation hôte. De plus, l’emplacement de montage de l’[ordinateur hôte](how-tos/text-analytics-how-to-install-containers.md#the-host-computer) peut ne pas être accessible en raison d’un conflit entre les autorisations utilisées par le compte de service docker et les autorisations de l’emplacement de montage de l’hôte. 

|Facultatif| Nom | Type de données | Description |
|-------|------|-----------|-------------|
|Non autorisé| `Input` | Chaîne | Les conteneurs Analyse de texte n’utilisent pas cet élément.|
|Facultatif| `Output` | Chaîne | Cible du montage de sortie. La valeur par défaut est `/output`. Il s’agit de l’emplacement des journaux. Les journaux de conteneur sont inclus. <br><br>Exemple :<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemples de commandes docker run 

Les exemples suivants utilisent les paramètres de configuration pour illustrer comment écrire et utiliser des commandes `docker run`.  Une fois en cours d’exécution, le conteneur continue à s’exécuter jusqu’à ce que vous l’[arrêtiez](how-tos/text-analytics-how-to-install-containers.md#stop-the-container).

* **Caractère de continuation de ligne** : les commandes docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte. 
* **Ordre des arguments** : ne changez pas l’ordre des arguments, sauf si vous avez une connaissance approfondie des conteneurs docker.

Remplacez {_argument_name_} par vos propres valeurs :

| Placeholder | Valeur | Format ou exemple |
|-------------|-------|---|
|{BILLING_KEY} | La clé de point de terminaison de la ressource Analyse de texte disponible dans la page Clés Analyse de texte du portail Azure. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | La valeur de point de terminaison de facturation est disponible dans la page Vue d’ensemble d’Analyse de texte du portail Azure.|`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](how-tos/text-analytics-how-to-install-containers.md#billing).
> La valeur ApiKey est la **Clé** présente dans la page des clés des ressources d’Analyse de texte Azure. 

## <a name="keyphrase-extraction-container-docker-examples"></a>Exemples Docker pour le conteneur Extraction de phrases clés

Les exemples Docker suivants s’appliquent au conteneur Extraction de phrases clés. 

### <a name="basic-example"></a>Exemple de base 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Exemple de journalisation 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="language-detection-container-docker-examples"></a>Exemples Docker pour le conteneur Détection de langue

Les exemples Docker suivants s’appliquent au conteneur Détection de langue. 

### <a name="basic-example"></a>Exemple de base

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Exemple de journalisation

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```
 
## <a name="sentiment-analysis-container-docker-examples"></a>Exemples Docker pour le conteneur Analyse des sentiments

Les exemples Docker suivants s’appliquent au conteneur Analyse des sentiments. 

### <a name="basic-example"></a>Exemple de base

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Exemple de journalisation

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Guide pratique pour installer et exécuter des conteneurs](how-tos/text-analytics-how-to-install-containers.md).
* Utiliser plus de [conteneurs Cognitive Services](../cognitive-services-container-support.md)
