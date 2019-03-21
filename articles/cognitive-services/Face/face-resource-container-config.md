---
title: Configurer des conteneurs
titlesuffix: Face - Azure Cognitive Services
description: Paramètres de configuration des conteneurs.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: diberry
ms.openlocfilehash: 4215b008af21a3473a1d2dcef5f73a1b19133215
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821557"
---
# <a name="configure-face-docker-containers"></a>Configurer des conteneurs Docker Visage

L’environnement d’exécution de conteneur **Visage** est configuré à l’aide des arguments de la commande `docker run`. Ce conteneur a plusieurs paramètres obligatoires et quelques paramètres facultatifs. Plusieurs [exemples](#example-docker-run-commands) de commande sont disponibles. Les paramètres propres aux conteneurs correspondent aux paramètres de facturation. 

Les paramètres de conteneur sont [hiérarchiques](#hierarchical-settings) et peuvent être définis avec des [variables d’environnement](#environment-variable-settings) ou des [arguments de ligne de commande](#command-line-argument-settings) docker.

## <a name="configuration-settings"></a>Paramètres de configuration

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Les paramètres [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Paramètre de configuration ApiKey

Le paramètre `ApiKey` spécifie la clé de ressource Azure utilisée pour effectuer le suivi des informations de facturation pour le conteneur. Vous devez spécifier une valeur pour ApiKey, et il doit s’agir d’une clé valide pour la ressource _Visage_ spécifiée pour le paramètre de configuration [`Billing`](#billing-configuration-setting).

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : Gestion des ressources de **Visage**, sous **Clés**

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Paramètre de configuration Billing

Le paramètre `Billing` permet de spécifier l’URI de point de terminaison de la ressource _Visage_ sur Azure servant à mesurer les informations de facturation du conteneur. Vous devez affecter à ce paramètre de configuration une valeur correspondant à un URI de point de terminaison valide pour une ressource _Visage_ sur Azure. La conteneur crée des rapports sur l'utilisation toutes les 10 à 15 minutes.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : Vue d’ensemble de **Visage**, étiqueté `Endpoint`

|Obligatoire| Nom | Type de données | Description |
|--|------|-----------|-------------|
|Oui| `Billing` | Chaîne | URI du point de terminaison de facturation<br><br>Exemple :<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Paramètres de configuration CloudAI

Les paramètres de configuration de la section `CloudAI` fournissent des options de conteneur propres à votre conteneur. Les paramètres et les objets suivants sont pris en charge pour le conteneur Visage dans la section `CloudAI`

| Nom | Type de données | Description |
|------|-----------|-------------|
| `Storage` | Object | Scénario de stockage utilisé par le conteneur Visage. Pour plus d’informations sur les scénarios de stockage et les paramètres associés pour les objets `Storage`, consultez [Paramètres de scénario de stockage](#storage-scenario-settings). |

### <a name="storage-scenario-settings"></a>Paramètres de scénario de stockage

Le conteneur Visage stocke des données d’objet blob, de cache, de métadonnées et de file d’attente, selon ce qui est stocké. Par exemple, les index et résultats d’entraînement d’un grand groupe de personnes sont stockés sous forme de données d’objet blob. Le conteneur Visage offre deux scénarios de stockage différents quand il interagit avec ces types de données et les stocke :

* Mémoire  
  Les quatre types de données sont stockés en mémoire. Ils ne sont ni distribués ni persistants. Si le conteneur Visage est arrêté ou supprimé, toutes les données contenues dans le stockage de ce conteneur sont détruites.  
  Il s’agit du scénario de stockage par défaut du conteneur Visage.
* Azure  
  Le conteneur Visage utilise Stockage Azure et Azure Cosmos DB pour répartir ces quatre types de données dans le stockage persistant. Les données d’objet blob et de file d’attente sont gérées par Stockage Azure. Les métadonnées et les données de cache sont gérées par Azure Cosmos DB. Si le conteneur Visage est arrêté ou supprimé, toutes les données se trouvant dans le stockage de ce conteneur restent stockées dans Stockage Azure et Azure Cosmos DB.  
  Les ressources utilisées par le scénario de stockage Azure imposent les conditions supplémentaires suivantes :
  * La ressource Stockage Azure doit utiliser le type de compte StorageV2.
  * La ressource Azure Cosmos DB doit utiliser l'API Azure Cosmos DB pour MongoDB

Les scénarios de stockage et les paramètres de configuration associés sont gérés par l’objet `Storage`, sous la section de configuration `CloudAI`. Les paramètres de configuration suivants sont disponibles dans l’objet `Storage` :

| Nom | Type de données | Description |
|------|-----------|-------------|
| `StorageScenario` | Chaîne | Scénario de stockage pris en charge par le conteneur. Les valeurs disponibles sont les suivantes :<br/>`Memory` - Valeur par défaut. Le conteneur utilise un stockage non persistant, non réparti et en mémoire pour une utilisation temporaire sur un nœud unique. Si le conteneur est arrêté ou supprimé, son stockage est détruit.<br/>`Azure` - Le conteneur utilise des ressources Azure pour le stockage. Si le conteneur est arrêté ou supprimé, son stockage est conservé.|
| `ConnectionStringOfAzureStorage` | Chaîne | Chaîne de connexion de la ressource Stockage Azure utilisée par le conteneur.<br/>Ce paramètre s’applique uniquement si `Azure` est spécifié pour le paramètre de configuration `StorageScenario`. |
| `ConnectionStringOfCosmosMongo` | Chaîne | Chaîne de connexion MongoDB de la ressource Azure Cosmos DB utilisée par le conteneur.<br/>Ce paramètre s’applique uniquement si `Azure` est spécifié pour le paramètre de configuration `StorageScenario`. |

Par exemple, la commande suivante spécifie le scénario de stockage Azure et fournit des exemples de chaîne de connexion pour les ressources Stockage Azure et Cosmos DB utilisées pour stocker les données du conteneur Visage.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Le scénario de stockage est géré séparément des montages d’entrée et de sortie. Vous pouvez spécifier une combinaison de ces caractéristiques pour un seul conteneur. Par exemple, la commande suivante définit un montage de liaison Docker sur le dossier `D:\Output` de la machine hôte comme montage de sortie, puis instancie un conteneur à partir de l’image conteneur Visage, les fichiers journaux étant enregistrés au format JSON sur le montage de sortie. De même, la commande spécifie le scénario de stockage Azure et fournit des exemples de chaîne de connexion pour les ressources Stockage Azure et Cosmos DB utilisées pour stocker les données du conteneur Visage.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

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

Les conteneurs Visage n’utilisent pas de montage d’entrée ou de sortie pour stocker des données d’entraînement ou de service. 

La syntaxe exacte de l’emplacement de montage d’hôte varie en fonction du système d’exploitation hôte. De plus, l’emplacement de montage de l’[ordinateur hôte](face-how-to-install-containers.md#the-host-computer) peut ne pas être accessible en raison d’un conflit entre les autorisations utilisées par le compte de service Docker et les autorisations de l’emplacement de montage de l’hôte. 

|Facultatif| Nom | Type de données | Description |
|-------|------|-----------|-------------|
|Non autorisé| `Input` | Chaîne | Les conteneurs Visage n’utilisent pas cet élément.|
|Facultatif| `Output` | Chaîne | Cible du montage de sortie. La valeur par défaut est `/output`. Il s’agit de l’emplacement des journaux. Les journaux de conteneur sont inclus. <br><br>Exemple :<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Paramètres hiérarchiques

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]

## <a name="example-docker-run-commands"></a>Exemples de commandes docker run 

Les exemples suivants utilisent les paramètres de configuration pour illustrer comment écrire et utiliser des commandes `docker run`.  Une fois en cours d’exécution, le conteneur continue à s’exécuter jusqu’à ce que vous l’[arrêtiez](face-how-to-install-containers.md#stop-the-container).

* **Caractère de continuation de ligne** : Les commandes Docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte. 
* **Ordre des arguments** : Ne changez pas l’ordre des arguments, sauf si vous avez une connaissance approfondie des conteneurs Docker.

Remplacez {_argument_name_} par vos propres valeurs :

| Placeholder | Valeur | Format ou exemple |
|-------------|-------|---|
|{BILLING_KEY} | Clé de point de terminaison de la ressource Visage. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Valeur de point de terminaison de facturation, région comprise.|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](face-how-to-install-containers.md#billing).
> La valeur ApiKey est la **Clé** présente dans la page des clés des ressources de Visage Azure. 

## <a name="face-container-docker-examples"></a>Exemples Docker de conteneurs Visage

Les exemples Docker suivants s’appliquent au conteneur Visage. 

### <a name="basic-example"></a>Exemple de base 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Exemple de journalisation avec arguments de ligne de commande

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-environment-variable"></a>Exemple de journalisation avec variable d’environnement

  ```
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY}
  ```

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Guide pratique pour installer et exécuter des conteneurs](face-how-to-install-containers.md).
