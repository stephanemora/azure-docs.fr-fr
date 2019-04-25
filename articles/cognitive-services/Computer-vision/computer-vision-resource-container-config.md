---
title: Configurer des conteneurs – Vision par ordinateur
titlesuffix: Azure Cognitive Services
description: Configurer des paramètres différents pour les conteneurs Reconnaître le texte dans Vision par ordinateur.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 42c08864c6908e92a7ecea336f8b1bd0606760db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203481"
---
# <a name="configure-recognize-text-docker-containers"></a>Configurer des conteneurs Docker Reconnaître le texte

L’environnement d’exécution de conteneur **Reconnaître le texte** est configuré à l’aide des arguments de la commande `docker run`. Ce conteneur a plusieurs paramètres obligatoires et quelques paramètres facultatifs. Plusieurs [exemples](#example-docker-run-commands) de commande sont disponibles. Les paramètres propres aux conteneurs correspondent aux paramètres de facturation. 

## <a name="configuration-settings"></a>Paramètres de configuration

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Les paramètres [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Paramètre de configuration ApiKey

Le `ApiKey` paramètre spécifie le Azure `Cognitive Services` utilisé pour effectuer le suivi des informations de facturation pour le conteneur de clé de ressource. Vous devez spécifier une valeur pour la clé API et la valeur doit être une clé valide pour le _Cognitive Services_ ressource spécifiée pour le [ `Billing` ](#billing-configuration-setting) paramètre de configuration.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : **COGNITIVE Services** gestion des ressources, sous **clés**

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Paramètre de configuration Billing

Le `Billing` paramètre spécifie l’URI de point de terminaison de la _Cognitive Services_ ressources sur Azure permet de contrôler les informations de facturation pour le conteneur. Vous devez spécifier une valeur pour ce paramètre de configuration, et la valeur doit être un URI de point de terminaison valide pour un _Cognitive Services_ ressources sur Azure. La conteneur crée des rapports sur l'utilisation toutes les 10 à 15 minutes.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : **COGNITIVE Services** vue d’ensemble, étiqueté `Endpoint`

N’oubliez pas d’ajouter le `vision/v1.0` routage vers l’URI de point de terminaison, comme indiqué dans le tableau suivant. 

|Obligatoire| Nom | Type de données | Description |
|--|------|-----------|-------------|
|Oui| `Billing` | Chaîne | URI du point de terminaison de facturation<br><br>Exemple :<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

Les conteneurs Vision par ordinateur n’utilisent pas de montage d’entrée ou de sortie pour stocker des données d’entraînement ou de service. 

La syntaxe exacte de l’emplacement de montage d’hôte varie en fonction du système d’exploitation hôte. De plus, l’emplacement de montage de l’[ordinateur hôte](computer-vision-how-to-install-containers.md#the-host-computer) peut ne pas être accessible en raison d’un conflit entre les autorisations utilisées par le compte de service Docker et les autorisations de l’emplacement de montage de l’hôte. 

|Facultatif| Nom | Type de données | Description |
|-------|------|-----------|-------------|
|Non autorisé| `Input` | Chaîne | Les conteneurs Vision par ordinateur n’utilisent pas cet élément.|
|Facultatif| `Output` | Chaîne | Cible du montage de sortie. La valeur par défaut est `/output`. Il s’agit de l’emplacement des journaux d’activité. Les journaux d’activité de conteneur sont inclus. <br><br>Exemple :<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemples de commandes docker run 

Les exemples suivants utilisent les paramètres de configuration pour illustrer comment écrire et utiliser des commandes `docker run`.  Une fois en cours d’exécution, le conteneur continue à s’exécuter jusqu’à ce que vous l’[arrêtiez](computer-vision-how-to-install-containers.md#stop-the-container).

* **Caractère de continuation de ligne** : Les commandes Docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte. 
* **Ordre des arguments** : Ne changez pas l’ordre des arguments, sauf si vous avez une connaissance approfondie des conteneurs Docker.

N’oubliez pas d’ajouter le `vision/v1.0` routage vers l’URI de point de terminaison, comme indiqué dans le tableau suivant. 

Remplacez {_argument_name_} par vos propres valeurs :

| Placeholder | Valeur | Format ou exemple |
|-------------|-------|---|
|{BILLING_KEY} | La clé de point de terminaison de la ressource Cognitive Services. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Valeur de point de terminaison de facturation, région comprise.|`https://westcentralus.api.cognitive.microsoft.com/vision/v1.0`|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](computer-vision-how-to-install-containers.md#billing).
> La valeur de clé API est la **clé** à partir d’Azure `Cognitive Services` page clés de ressources. 

## <a name="recognize-text-container-docker-examples"></a>Exemples de conteneur Docker Reconnaître le texte

Les exemples Docker suivants s’appliquent au conteneur Reconnaître le texte. 

### <a name="basic-example"></a>Exemple de base 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Exemple de journalisation 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Guide pratique pour installer et exécuter des conteneurs](computer-vision-how-to-install-containers.md).
