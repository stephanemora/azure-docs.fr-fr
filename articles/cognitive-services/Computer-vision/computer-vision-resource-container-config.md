---
title: Configurer des conteneurs – Vision par ordinateur
titlesuffix: Azure Cognitive Services
description: Configurer des paramètres différents pour les conteneurs Reconnaître le texte dans Vision par ordinateur.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 90358d54077a0c320e8d3186e806b8a61d951c82
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321339"
---
# <a name="configure-recognize-text-docker-containers"></a>Configurer des conteneurs Docker Reconnaître le texte

L’environnement d’exécution de conteneur **Reconnaître le texte** est configuré à l’aide des arguments de la commande `docker run`. Ce conteneur a plusieurs paramètres obligatoires et quelques paramètres facultatifs. Plusieurs [exemples](#example-docker-run-commands) de commande sont disponibles. Les paramètres propres aux conteneurs correspondent aux paramètres de facturation. 

## <a name="configuration-settings"></a>Paramètres de configuration

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Les paramètres [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Paramètre de configuration ApiKey

Le paramètre `ApiKey` spécifie la clé de ressource Azure `Cognitive Services` utilisée pour effectuer le suivi des informations de facturation pour le conteneur. Vous devez donner une valeur à ApiKey, qui doit être une clé valide pour la ressource _Cognitive Services_ spécifiée dans le paramètre de configuration [`Billing`](#billing-configuration-setting).

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : Gestion des ressources **Cognitive Services**, sous **Clés**

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Paramètre de configuration Billing

Le paramètre `Billing` permet de spécifier l’URI de point de terminaison de la ressource _Cognitive Services_ sur Azure servant à effectuer l’analyse des informations de facturation du conteneur. Vous devez donner une valeur à ce paramètre de configuration, qui doit être un URI de point de terminaison valide pour une ressource _Cognitive Services_ dans Azure. Le conteneur crée des rapports sur l’utilisation toutes les 10 à 15 minutes.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : Vue d’ensemble **Cognitive Services**, étiquetée `Endpoint`

Pensez à ajouter le routage `vision/v1.0` à l’URI de point de terminaison, comme dans le tableau suivant. 

|Obligatoire| Nom | Type de données | Description |
|--|------|-----------|-------------|
|OUI| `Billing` | Chaîne | URI du point de terminaison de facturation<br><br>Exemple :<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

Pensez à ajouter le routage `vision/v1.0` à l’URI de point de terminaison, comme dans le tableau suivant. 

Remplacez {_argument_name_} par vos propres valeurs :

| Placeholder | Valeur | Format ou exemple |
|-------------|-------|---|
|{API_KEY} | Clé de point de terminaison de la ressource Cognitive Services. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | Valeur de point de terminaison de facturation, région comprise.|`https://westcentralus.api.cognitive.microsoft.com/vision/v1.0`|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](computer-vision-how-to-install-containers.md#billing).
> La valeur ApiKey est la **Clé** de la page Clés des ressources Azure `Cognitive Services`. 

## <a name="recognize-text-container-docker-examples"></a>Exemples de conteneur Docker Reconnaître le texte

Les exemples Docker suivants s’appliquent au conteneur Reconnaître le texte. 

### <a name="basic-example"></a>Exemple de base 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Exemple de journalisation 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Guide pratique pour installer et exécuter des conteneurs](computer-vision-how-to-install-containers.md).
