---
title: Conteneurs Docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: Le conteneur LUIS charge votre application entraînée ou publiée dans un conteneur docker et fournit l’accès aux prédictions de requête à partir des points de terminaison d’API du conteneur.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 2542364db3a895c060c752beeb0cfabf75834f7d
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970268"
---
# <a name="install-and-run-luis-docker-containers"></a>Installer et exécuter des conteneurs Docker LUIS
 
Le conteneur LUIS (Language Understanding) charge votre modèle Language Understanding entraîné ou publié, également connu sous le nom d’[application LUIS](https://www.luis.ai), dans un conteneur docker et fournit l’accès aux prédictions de requête à partir des points de terminaison d’API du conteneur. Vous pouvez collecter les journaux de requêtes du conteneur et les charger sur le modèle Azure Language Understanding pour améliorer la précision de prédiction de l’application.

La vidéo suivante illustre l’utilisation de ce conteneur.

[![Démonstration d’un conteneur pour Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour exécuter le conteneur LUIS, vous devez disposer des éléments suivants : 

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. <br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.| 
|Ressource LUIS (Language Understanding) et application associée |Pour pouvoir utiliser le conteneur, vous devez disposer des éléments suivants :<br><br>* Une [ressource Azure _Language Understanding_](luis-how-to-azure-subscription.md) ainsi que la clé de point de terminaison et l’URI de point de terminaison associés (utilisé en tant que point de terminaison de facturation).<br>* Une application entraînée ou publiée empaquetée en tant qu’entrée montée dans le conteneur avec son ID d’application associé.<br>* La clé de création pour télécharger le package d’application, si vous effectuez cette opération à partir de l’API.<br><br>Ces prérequis sont utilisés pour passer des arguments de ligne de commande aux variables suivantes :<br><br>**{AUTHORING_KEY}**  : cette clé sert à obtenir l’application empaquetée à partir du service LUIS dans le cloud et à charger les journaux de requêtes vers le cloud. Le format est `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}**  : cet ID sert à sélectionner l’application. Le format est `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{ENDPOINT_KEY}**  : cette clé sert à démarrer le conteneur. La clé de point de terminaison est disponible à deux endroits. Le premier est le portail Azure, dans la liste de clés de la ressource _Language Understanding_. Elle est également disponible dans le portail LUIS, dans la page Keys and Endpoint settings (Paramètres des clés et du point de terminaison). N’utilisez pas la clé de démarrage.<br><br>**{BILLING_ENDPOINT}**  : la valeur de point de terminaison de facturation est disponible dans la page Vue d’ensemble de Language Understanding du portail Azure. Par exemple `https://westus.api.cognitive.microsoft.com/luis/v2.0`.<br><br>La [clé de création et la clé de point de terminaison](luis-boundaries.md#key-limits) ont différentes fonctions. Ne les utilisez pas de manière interchangeable. |

### <a name="the-host-computer"></a>L’ordinateur hôte

L’**hôte** est l’ordinateur qui exécute le conteneur docker. Il peut s’agir d’un ordinateur local ou d’un service d’hébergement docker dans Azure, notamment :

* [Azure Kubernetes Service](../../aks/index.yml)
* [Azure Container Instances](../../container-instances/index.yml)
* Cluster [Kubernetes](https://kubernetes.io/) déployé sur [Azure Stack](../../azure-stack/index.yml). Pour plus d’informations, consultez [Déployer Kubernetes sur Azure Stack](../../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

### <a name="container-requirements-and-recommendations"></a>Exigences et recommandations relatives au conteneur

Ce conteneur prend en charge des valeurs minimales et recommandées pour les paramètres :

|Paramètre| Minimale | Recommandé |
|-----------|---------|-------------|
|Cœurs<BR>`--cpus`|1 cœur<BR>au moins 2,6 gigahertz (GHz) ou plus rapide|1 cœur|
|Mémoire<BR>`--memory`|2 Go|4 Go|
|Transactions par seconde<BR>(TPS)|20 TPS|40 TPS|

Les paramètres `--cpus` et `--memory` sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir du référentiel `mcr.microsoft.com/azure-cognitive-services/luis` :

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Pour obtenir une description complète des balises disponibles utilisées dans la commande précédente, telles que `latest`, consultez [LUIS](https://hub.docker.com/r/microsoft/azure-cognitive-services-luis/) sur Docker Hub.

> [!TIP]
> Vous pouvez utiliser la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/) pour lister vos images conteneurs téléchargées. Par exemple, la commande suivante liste l’ID, le référentiel et la balise de chaque image conteneur téléchargée dans un tableau :
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/luis                           latest
>  ``` 

## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

![Procédure pour l’utilisation du conteneur LUIS (Language Understanding)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportez le package](#export-packaged-app-from-luis) pour le conteneur à partir du portail LUIS ou des API LUIS.
1. Déplacez le fichier de package dans le répertoire d’**entrée** requis sur l’[ordinateur hôte](#the-host-computer). Ne renommez pas, ne modifiez pas et ne décompressez pas le fichier de package LUIS.
1. [Exécutez le conteneur](##run-the-container-with-docker-run) avec le _montage d’entrée_ et les paramètres de facturation requis. D’autres [exemples](luis-container-configuration.md#example-docker-run-commands) de commande `docker run` sont disponibles. 
1. [Interrogation du point de terminaison de prédiction du conteneur](#query-the-containers-prediction-endpoint). 
1. Quand vous en avez terminé avec le conteneur, [importez les journaux du point de terminaison](#import-the-endpoint-logs-for-active-learning) à partir du montage de sortie dans le portail LUIS et [arrêtez](#stop-the-container) le conteneur.
1. Utilisez l’[apprentissage actif](luis-how-to-review-endoint-utt.md) du portail LUIS dans la page **Review endpoint utterances** (Passer en revue les énoncés du point de terminaison) afin d’améliorer l’application.

L’application en cours d’exécution dans le conteneur ne peut pas être modifiée. Pour changer l’application dans le conteneur, vous devez changer l’application dans le service LUIS à l’aide du portail [LUIS](https://www.luis.ai) ou utiliser les [API de création](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) LUIS. Ensuite, entraînez et/ou publiez, puis téléchargez un nouveau package et réexécutez le conteneur.

L’application LUIS à l’intérieur du conteneur ne peut pas être réexportée vers le service LUIS. Seuls les journaux de requêtes peuvent être chargés. 

## <a name="export-packaged-app-from-luis"></a>Exporter l’application empaquetée à partir de LUIS

Le conteneur LUIS a besoin d’une application LUIS entraînée ou publiée pour répondre aux requêtes de prédiction des énoncés d’utilisateur. Pour accéder à l’application LUIS, utilisez l’API du package entraîné ou publié. 

L’emplacement par défaut est le sous-répertoire `input` correspondant à l’emplacement où vous exécutez la commande `docker run`.  

Placez le fichier de package dans un répertoire et référencez ce répertoire en tant que montage d’entrée quand vous exécutez le conteneur docker. 

### <a name="package-types"></a>Types de packages

Le répertoire de montage d’entrée peut contenir en même temps les versions **Production**, **Staging** et **Trained** de l’application. Tous les packages sont montés. 

|Type de package|API de point de terminaison de requête|Disponibilité des requêtes|Format du nom de fichier de package|
|--|--|--|--|
|Formé|Get, Post|Conteneur uniquement|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Staging|Get, Post|Azure et conteneur|`{APPLICATION_ID}_STAGING.gz`|
|Production|Get, Post|Azure et conteneur|`{APPLICATION_ID}_PRODUCTION.gz`|

>**Important :** Ne renommez pas, ne modifiez pas et ne décompressez pas les fichiers de packages LUIS.

### <a name="packaging-prerequisites"></a>Prérequis pour l’empaquetage

Avant d’empaqueter une application LUIS, vous devez disposer des éléments suivants :

|Conditions requises pour l’empaquetage|Détails|
|--|--|
|Instance de ressource Azure _Language Understanding_|Exemples de régions prises en charge :<br><br>USA Ouest (```westus```)<br>Europe Ouest (```westeurope```)<br>Australie Est (```australiaeast```)|
|Application LUIS entraînée ou publiée|Sans aucune [dépendance non prise en charge](#unsupported-dependencies). |
|Accès au système de fichiers de l’[ordinateur hôte](#the-host-computer) |L’ordinateur hôte doit autoriser un [montage d’entrée](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Exporter le package d’application à partir du portail LUIS

Le [portail](https://www.luis.ai) LUIS offre la possibilité d’exporter le package de l’application entraînée ou publiée. 

### <a name="export-published-apps-package-from-luis-portal"></a>Exporter le package de l’application publiée à partir du portail LUIS

Le package de l’application publiée est disponible à partir de la page de liste **My Apps**. 

1. Connectez-vous au portail [LUIS](https://www.luis.ai).
1. Cochez la case à gauche du nom de l’application dans la liste. 
1. Sélectionnez l’élément **Export** dans la barre d’outils contextuelle au-dessus de la liste.
1. Sélectionnez **Export for container (GZIP)**.
1. Sélectionnez l’environnement **Production slot** (Emplacement de production) ou **Staging slot** (Emplacement intermédiaire).
1. Le package est téléchargé à partir du navigateur.

![Exporter le package publié pour le conteneur à partir du menu Export de la page d’application](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>Exporter le package de l’application entraînée à partir du portail LUIS

Le package de l’application entraînée est disponible à partir de la page de liste **Versions**. 

1. Connectez-vous au portail [LUIS](https://www.luis.ai).
1. Sélectionnez l’application dans la liste. 
1. Sélectionnez **Manage** (Gérer) dans la barre de navigation de l’application.
1. Sélectionnez **Versions** dans la barre de navigation gauche.
1. Cochez la case à gauche du nom de la version dans la liste.
1. Sélectionnez l’élément **Export** dans la barre d’outils contextuelle au-dessus de la liste.
1. Sélectionnez **Export for container (GZIP)**.
1. Le package est téléchargé à partir du navigateur.

![Exporter le package entraîné pour le conteneur à partir du menu Export de la page Versions](./media/luis-container-how-to/export-trained-package-for-container.png)


### <a name="export-published-apps-package-from-api"></a>Exporter le package de l’application publiée à partir de l’API

Utilisez l’API REST suivante pour empaqueter une application LUIS que vous avez déjà [publiée](luis-how-to-publish-app.md). Remplacez les espaces réservés dans l’appel d’API par vos propres valeurs appropriées, en utilisant le tableau sous la spécification HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Placeholder | Valeur |
|-------------|-------|
|{APPLICATION_ID} | ID d’application de l’application LUIS publiée. |
|{APPLICATION_ENVIRONMENT} | Environnement de l’application LUIS publiée. Utilisez l’une des valeurs suivantes :<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | Clé de création du compte LUIS pour l’application LUIS publiée.<br/>Vous pouvez obtenir votre clé de création à partir de la page **User Settings** (Paramètres utilisateur) dans le portail LUIS. |
|{AZURE_REGION} | Région Azure appropriée :<br/><br/>```westus``` - USA Ouest<br/>```westeurope``` - Europe Ouest<br/>```australiaeast``` - Australie Est |

Utilisez la commande CURL suivante pour télécharger le package publié, en substituant vos propres valeurs :

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_{APPLICATION_ENVIRONMENT}.gz
```

En cas de réussite, la réponse est un fichier de package LUIS. Enregistrez le fichier à l’emplacement de stockage spécifié pour le montage d’entrée du conteneur. 

### <a name="export-trained-apps-package-from-api"></a>Exporter le package de l’application entraînée à partir de l’API

Utilisez l’API REST suivante pour empaqueter une application LUIS que vous avez déjà [entraînée](luis-how-to-train.md). Remplacez les espaces réservés dans l’appel d’API par vos propres valeurs appropriées, en utilisant le tableau sous la spécification HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Placeholder | Valeur |
|-------------|-------|
|{APPLICATION_ID} | ID d’application de l’application LUIS entraînée. |
|{APPLICATION_VERSION} | Version d’application de l’application LUIS entraînée. |
|{AUTHORING_KEY} | Clé de création du compte LUIS pour l’application LUIS publiée.<br/>Vous pouvez obtenir votre clé de création à partir de la page **User Settings** (Paramètres utilisateur) dans le portail LUIS.  |
|{AZURE_REGION} | Région Azure appropriée :<br/><br/>```westus``` - USA Ouest<br/>```westeurope``` - Europe Ouest<br/>```australiaeast``` - Australie Est |

Utilisez la commande CURL suivante pour télécharger le package entraîné :

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_v{APPLICATION_VERSION}.gz
```

En cas de réussite, la réponse est un fichier de package LUIS. Enregistrez le fichier à l’emplacement de stockage spécifié pour le montage d’entrée du conteneur. 

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter le conteneur. La commande utilise les paramètres suivants :

| Placeholder | Valeur |
|-------------|-------|
|{ENDPOINT_KEY} | cette clé sert à démarrer le conteneur. N’utilisez pas la clé de démarrage. |
|{BILLING_ENDPOINT} | la valeur de point de terminaison de facturation est disponible dans la page Vue d’ensemble de Language Understanding du portail Azure.|

Remplacez ces paramètres par vos propres valeurs dans l’exemple de commande `docker run` suivant.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> La commande précédente utilise le répertoire du lecteur `c:` pour éviter tout conflit d’autorisation sur Windows. Si vous devez utiliser un répertoire spécifique en tant que répertoire d’entrée, vous devrez peut-être accorder au docker une autorisation de service. La commande docker précédente utilise la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences du système d’exploitation de votre [ordinateur hôte](#the-host-computer). Ne changez pas l’ordre des arguments, sauf si vous avez une connaissance approfondie des conteneurs docker.


Cette commande :

* Exécute un conteneur à partir de l’image conteneur LUIS.
* Charge l’application LUIS à partir du montage d’entrée qui se trouve à c:\input sur l’hôte de conteneur.
* Alloue deux cœurs de processeur et 4 gigaoctets (Go) de mémoire.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Enregistre les journaux LUIS et de conteneur dans le montage de sortie qui se trouve à c:\output sur l’hôte de conteneur.
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte. 

D’autres [exemples](luis-container-configuration.md#example-docker-run-commands) de commande `docker run` sont disponibles. 

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).
> La valeur ApiKey est la **clé** mentionnée dans la page Keys and Endpoints dans le portail LUIS. Elle est également disponible dans la page Clés de ressources Language Understanding d’Azure.  

## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST. Les points de terminaison pour les applications publiées (intermédiaires ou production) ont une route _différente_ de celle des points de terminaison pour les applications entraînées. 

Utilisez l’hôte, https://localhost:5000, pour les API de conteneur. 

|Type de package|Méthode|Routage|Paramètres de requête|
|--|--|--|--|
|Publié|[Get](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>&staging<br>[&timezoneOffset]<br>[&verbose]<br>[&log]<br>|
|Formé|Get, Post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[&timezoneOffset]<br>[&verbose]<br>[&log]|

Les paramètres de requête configurent ce qui est retourné dans la réponse de requête, et de quelle manière :

|Paramètre de requête.|type|Objectif|
|--|--|--|
|`q`|chaîne|Énoncé de l’utilisateur.|
|`timezoneOffset`|number|timezoneOffset vous permet de [changer le fuseau horaire](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) utilisé par l’entité prédéfinie datetimeV2.|
|`verbose`|booléenne|Retourne toutes les intentions et leurs scores quand la valeur est true. La valeur par défaut est false, ce qui retourne uniquement la première intention.|
|`staging`|booléenne|Retourne une requête à partir des résultats de l’environnement intermédiaire si la valeur est true. |
|`log`|booléenne|Enregistre les requêtes, qui peuvent être utilisées ultérieurement pour l’[apprentissage actif](luis-how-to-review-endoint-utt.md). La valeur par défaut est true.|

### <a name="query-published-app"></a>Interroger une application publiée

Voici un exemple de commande CURL pour interroger le conteneur d’une application publiée :

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Pour effectuer des requêtes sur l’environnement intermédiaire (**Staging**), affectez la valeur true au paramètre de chaîne de requête **staging** : 

`staging=true`

### <a name="query-trained-app"></a>Interroger une application entraînée

Voici un exemple de commande CURL pour interroger le conteneur d’une application entraînée : 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Le nom de version a un maximum de 10 caractères et contient uniquement des caractères autorisés dans une URL. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importer les journaux de point de terminaison pour l’apprentissage actif

Si un montage de sortie est spécifié pour le conteneur LUIS, les fichiers journaux de requêtes d’application sont enregistrés dans le répertoire de sortie, où {INSTANCE_ID} est l’ID de conteneur. Le journal des requêtes d’application contient la requête, la réponse et les horodateurs de chaque requête de prédiction soumise au conteneur LUIS. 

L’emplacement suivant montre la structure de répertoires imbriqués pour les fichiers journaux du conteneur.
`
/output/luis/{INSTANCE_ID}/
`
 
À partir du portail LUIS, sélectionnez votre application, puis sélectionnez **Import endpoint logs** (Importer les journaux de point de terminaison) pour charger ces journaux. 

![Importer les fichiers journaux du conteneur pour l’apprentissage actif](./media/luis-container-how-to/upload-endpoint-log-files.png)

Une fois le journal chargé, [passez en revue les énoncés de point de terminaison](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) dans le portail LUIS.

## <a name="stop-the-container"></a>Arrêter le conteneur

Pour arrêter le conteneur, dans l’environnement de ligne de commande où le conteneur est en cours d’exécution, appuyez sur **Ctrl+C**.

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous exécutez le conteneur avec un [montage](luis-container-configuration.md#mount-settings) de sortie et la journalisation activée, il génère des fichiers journaux qui sont utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur. 

## <a name="containers-api-documentation"></a>Documentation sur l’API du conteneur

Le conteneur fournit un ensemble complet de documentation pour les points de terminaison ainsi qu’une fonctionnalité `Try it now`. Cette fonctionnalité vous permet d’entrer vos paramètres dans un formulaire HTML basé sur le web et d’effectuer la requête sans avoir à écrire du code. Une fois la requête retournée, un exemple de commande CURL est fourni pour illustrer le format du corps et des en-têtes HTTP requis. 

> [!TIP]
> Consultez la [spécification OpenAPI](https://swagger.io/docs/specification/about/), qui décrit les opérations d’API prises en charge par le conteneur, à partir de l’URI relatif `/swagger`. Par exemple : 
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>Facturation

Le conteneur LUIS envoie des informations de facturation à Azure à l’aide d’une ressource _Language Understanding_ correspondante sur votre compte Azure. 

Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas de données client (l’énoncé) à Microsoft. 

`docker run` utilise les arguments suivants lors de la facturation :

| Option | Description |
|--------|-------------|
| `ApiKey` | Clé API de la ressource _Language Understanding_ utilisée pour effectuer le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur une clé API pour la ressource LUIS Azure provisionnée, spécifiée dans `Billing`. |
| `Billing` | Point de terminaison de la ressource _Language Understanding_ utilisée pour effectuer le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur l’URI de point de terminaison d’une ressource LUIS Azure provisionnée.|
| `Eula` | Indique que vous avez accepté la licence pour le conteneur.<br/>La valeur de cette option doit être `accept`. |

> [!IMPORTANT]
> Les trois options doivent être spécifiées avec des valeurs valides ; sinon, le conteneur ne démarre pas.

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](luis-container-configuration.md).

## <a name="unsupported-dependencies"></a>Dépendances non prises en charge

Vous pouvez utiliser une application LUIS si elle **n’inclut aucune** des dépendances suivantes :

Configurations d’application non prises en charge|Détails|
|--|--|
|Cultures de conteneur non prises en charge| Allemand (de-DE)<br>Néerlandais (nl-NL)<br>Japonais (ja-JP)<br>|
|Domaines non pris en charge|Domaines prédéfinis, y compris les entités et les intentions de domaine prédéfinis|
|Entités non prises en charge pour toutes les cultures|Entité prédéfinie [KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) pour toutes les cultures|
|Entités non prises en charge pour la culture anglais (en-US)|Entités prédéfinies [GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2)|
|Préparation vocale|Les dépendances externes ne sont pas prises en charge dans le conteneur.|
|analyse de sentiments|Les dépendances externes ne sont pas prises en charge dans le conteneur.|
|Vérification orthographique Bing|Les dépendances externes ne sont pas prises en charge dans le conteneur.|

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs LUIS (Language Understanding). En résumé :

* LUIS (Language Understanding) fournit des conteneurs Linux pour Docker qui offrent des prédictions de requête de point de terminaison d’énoncés.
* Les images conteneurs sont téléchargées à partir de Microsoft Container Registry (MCR).
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST pour interroger les points de terminaison de conteneur en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas les données des clients (p. ex., l’image ou le texte analysés) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](luis-container-configuration.md).
* Consultez les [Questions fréquentes (FAQ)](luis-resources-faq.md) pour résoudre les problèmes liés à la fonctionnalité LUIS.