---
title: Installer et exécuter des conteneurs
titlesuffix: Face - Azure Cognitive Services
description: Ce tutoriel pas à pas décrit comment télécharger, installer et exécuter des conteneurs pour Visage.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 310311c22be25960b15f20d573624f50b0f618b1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474810"
---
# <a name="install-and-run-containers"></a>Install and run containers

La mise en conteneur est une méthode de distribution de logiciels dans laquelle une application ou un service est packagé en tant qu’image conteneur. La configuration et les dépendances pour l’application ou le service sont inclus dans l’image conteneur. L’image conteneur peut ensuite être déployée sur un hôte conteneur avec peu ou pas de modifications. Les conteneurs sont isolés les uns des autres et du système d’exploitation sous-jacent, avec une empreinte inférieure à celle d’une machine virtuelle. Vous pouvez instancier les conteneurs à partir d’images conteneur pour les tâches à court terme et les supprimer quand vous n’avez plus besoin.

Visage fournit un conteneur Linux standardisé pour Docker, nommé Visage, qui détecte les visages humains sur des images et identifie les attributs, y compris les caractéristiques (par exemple, le nez et les yeux), le sexe, l’âge et d’autres caractéristiques du visage prévues par la machine. En plus de la détection, Visage peut vérifier si deux visages sur la même image ou sur des images différentes sont identiques à l’aide d’un score de confiance, ou de comparer des visages par rapport à une base de données pour voir si un visage similaire ou identique existe déjà. Il peut également regrouper des visages similaires à l’aide de caractéristiques visuelles partagées.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="preparation"></a>Préparation

Vous devez respecter les prérequis suivants avant d’effectuer le conteneur Visage :

**Moteur Docker** : le moteur Docker doit être installé localement. Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) et [Windows](https://docs.docker.com/docker-for-windows/). Sur Windows, vous devez configurer Docker pour prendre en charge les conteneurs Linux. Les conteneurs Docker peuvent également être déployés directement sur [Azure Kubernetes Service](../../aks/index.yml), sur [Azure Container Instances](../../container-instances/index.yml) ou sur un cluster [Kubernetes](https://kubernetes.io/) déployé sur [Azure Stack](../../azure-stack/index.yml). Pour plus d’informations sur le déploiement de Kubernetes sur Azure Stack, consultez [Déployer Kubernetes sur Azure Stack](../../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation.

**Connaissance de Microsoft Container Registry et de Docker** : vous devez avoir une compréhension élémentaire des concepts de Microsoft Container Registry et de Docker, notamment les registres, dépôts, conteneurs et images conteneur, ainsi qu’une maîtrise des commandes `docker` de base.  

Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).

### <a name="container-requirements-and-recommendations"></a>Exigences et recommandations relatives au conteneur

Le conteneur Visage nécessite au minimum 1 cœur de processeur d’au moins 2,6 gigahertz (GHz) et 4 gigaoctets (Go) de mémoire allouée, mais nous recommandons d’utiliser au moins 2 cœurs de processeur et 6 Go de mémoire allouée.

## <a name="request-access-to-the-private-container-registry"></a>Demander l’accès au registre de conteneurs privé

Vous devez tout d’abord remplir et envoyer la [formulaire de demande de conteneurs Vision Cognitive Services ](https://aka.ms/VisionContainersPreview) pour demander l’accès au conteneur Visage. Le formulaire demande des informations sur vous, votre société et le scénario utilisateur pour lequel vous allez utiliser le conteneur. À la réception du formulaire, l’équipe Azure Cognitive Services vérifie que vous remplissez bien les critères d’accès au registre de conteneurs privé.

> [!IMPORTANT]
> Vous devez utiliser une adresse e-mail associée à un compte Microsoft (MSA) ou à un compte Azure Active Directory (Azure AD) dans le formulaire.

Si votre demande est approuvée, vous recevez un e-mail contenant des instructions relatives à l’obtention de vos informations d'identification et à l’accès au registre de conteneurs privé.

## <a name="create-a-face-resource-on-azure"></a>Créer une ressource Visage sur Azure

Si vous souhaitez utiliser le conteneur Visage, vous devez créer une ressource Visage sur Azure. Après avoir créé la ressource, vous utilisez la clé de l’abonnement et l’URL de point de terminaison de la ressource pour instancier le conteneur. Pour plus d’informations sur l’instanciation d’un conteneur, consultez [Instancier un conteneur à partir d’une image conteneur téléchargée](#instantiate-a-container-from-a-downloaded-container-image).

Procédez comme suit pour créer et récupérer des informations à partir d’une ressource Visage :

1. Créer une ressource Visage dans le portail Azure.  
   Si vous souhaitez utiliser le conteneur Visage, vous devez d’abord créer une ressource Visage correspondante dans le portail Azure. Pour plus d’informations, consultez [Démarrage rapide : Créer un compte Cognitive Services dans le portail Azure.](../cognitive-services-apis-create-account.md)

1. Obtenez l’URL de point de terminaison et la clé d’abonnement pour la ressource Azure.  
   Une fois la ressource Azure créée, vous devez utiliser l’URL de point de terminaison et la clé de l’abonnement de cette ressource pour instancier le conteneur Visage correspondant. Vous pouvez copier l’URL du point de terminaison et la clé de l’abonnement, respectivement sur les pages Démarrage rapide et Clés de la ressource Visage sur le portail Azure.

## <a name="log-in-to-the-private-container-registry"></a>Connectez-vous au registre de conteneurs privé

Il y a plusieurs façons de s’authentifier auprès du registre de conteneurs privé pour les conteneurs Cognitive Services, mais la méthode recommandée à partir de la ligne de commande est l’utilisation de la [CLI Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Utilisez la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/), comme indiqué dans l’exemple suivant, pour vous connecter à `containerpreview.azurecr.io`, le registre de conteneurs privé pour les conteneurs Cognitive Services. Remplacez *\<username\>* par le nom de l’utilisateur et *\<password\>* par le mot de passe fourni dans les informations d’identification envoyées par l’équipe Azure Cognitive Services.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Si vous avez sécurisé vos informations d’identification dans un fichier texte, vous pouvez utiliser la commande `cat` pour concaténer son contenu à la commande `docker login`, comme indiqué dans l’exemple suivant. Remplacez *\<passwordFile\>* par le chemin et le nom du fichier texte contenant le mot de passe, et *\<username\>* par le nom d’utilisateur fourni dans vos informations d’identification.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>Télécharger des images de conteneur à partir du registre de conteneurs privé

L’image de conteneur pour le conteneur Visage est disponible à partir d’un Registre de conteneur Docker privé, nommé `containerpreview.azurecr.io`, dans Azure Container Registry. L’image de conteneur pour le conteneur Visage doit être téléchargée à partir du référentiel pour exécuter le conteneur localement.

Utilisez la commande [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image de conteneur à partir du référentiel. Par exemple, pour télécharger la dernière image de conteneur Visage à partir du référentiel, utilisez la commande suivante :

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

Pour obtenir une description complète des balises disponibles pour le conteneur Visage, consultez [Reconnaître le texte](https://go.microsoft.com/fwlink/?linkid=2018655) sur Docker Hub.

> [!TIP]
> Vous pouvez utiliser la commande [images Docker](https://docs.docker.com/engine/reference/commandline/images/) pour répertorier vos images de conteneur téléchargées. Par exemple, la commande suivante liste l’ID, le référentiel et la balise de chaque image conteneur téléchargée dans un tableau :
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Instancier un conteneur à partir d’une image conteneur téléchargée

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour instancier un conteneur à partir d’une image conteneur téléchargée. Par exemple, la commande suivante :

* Instancie un conteneur à partir de l’image de conteneur Visage
* Alloue deux cœurs de processeur et 6 gigaoctets (Go) de mémoire
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture

```Docker
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
```

> [!IMPORTANT]
> Les options `Eula`, `Billing` et `ApiKey` doivent être spécifiées pour instancier le conteneur ; sinon, le conteneur ne démarre pas.  Pour plus d'informations, consultez [Facturation](#billing).

Une fois l’instanciation effectuée, vous pouvez appeler des opérations à partir du conteneur à l’aide de l’URI hôte du conteneur. Par exemple, l’URI hôte suivant représente le conteneur Visage qui a été instancié dans l’exemple précédent :

```http
http://localhost:5000/
```

> [!TIP]
> Vous pouvez accéder à la [spécification OpenAPI](https://swagger.io/docs/specification/about/) (ancienne spécification Swagger), qui décrit les opérations prises en charge par un conteneur instancié, à partir de l’URI relatif `/swagger` pour ce conteneur. Par exemple, l’URI suivant permet d’accéder à la spécification OpenAPI pour le conteneur Visage instancié dans l’exemple précédent :
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Vous pouvez soit [appeler les opérations d’API REST](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/howtodetectfacesinimage) disponibles à partir de votre conteneur, soit utiliser la bibliothèque de client [Azure Cognitive Services Face Client Library](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/) pour appeler ces opérations.  
> [!IMPORTANT]
> Vous devez disposer d’Azure Cognitive Services Face Client Library, version 2.0 ou ultérieure, si vous souhaitez utiliser la bibliothèque de client avec votre conteneur.

La seule différence entre l’appel d’une opération donnée à partir de votre conteneur et l’appel de cette même opération à partir d’un service correspondant sur Azure est que vous allez utiliser l’URI hôte de votre conteneur au lieu de l’URI hôte d’une région Azure, de l’hôte pour appeler l’opération. Par exemple, si vous souhaitez utiliser une instance Visage en cours d’exécution dans la région Azure USA Ouest pour détecter des visages, vous appelez l’opération d’API REST suivante :

```http
POST https://westus.api.cognitive.microsoft.com/face/v1.0/detect
```

Si vous souhaitez utiliser un conteneur Visage en cours d’exécution sur votre ordinateur local sous sa configuration par défaut pour détecter des visages, vous appelez l’opération d’API REST suivante :

```http
POST http://localhost:5000/face/v1.0/detect
```

### <a name="billing"></a>Facturation

Le conteneur Visage envoie des informations de facturation à Azure à l’aide d’une ressource Visage correspondante sur votre compte Azure. Les options de ligne de commande suivantes sont utilisées par le conteneur Visage à des fins de facturation :

| Option | Description |
|--------|-------------|
| `ApiKey` | La clé d’API de la ressource Visage utilisée pour effectuer le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur une clé API pour la ressource Face Azure provisionnée, spécifiée dans `Billing`. |
| `Billing` | Le point de terminaison de la ressource Visage utilisée pour effectuer le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur l’URI de point de terminaison d’une ressource Visage Azure provisionnée.|
| `Eula` | Indique que vous avez accepté la licence pour le conteneur.<br/>La valeur de cette option doit être `accept`. |

> [!IMPORTANT]
> Les trois options doivent être spécifiées avec des valeurs valides ; sinon, le conteneur ne démarre pas.

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](face-resource-container-config.md).

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Visage. En résumé :

* Visage fournit un conteneur Linux pour Docker, nommé Visage, pour détecter ou identifier des visages à l’aide d’une base de données de personnes.
* Les images de conteneur sont téléchargées à partir d’un registre de conteneurs privé dans Azure.
* Les images de conteneur s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou kit de développement logiciel (SDK) pour appeler des opérations dans des conteneurs Visage en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas les données des clients (p. ex., l’image ou le texte analysés) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](face-resource-container-config.md).
* Consulter [Vue d’ensemble de Visage](Overview.md) pour en savoir plus sur la détection et l’identification de visages  
* Reportez-vous à l’ [API Visage](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) pour plus d’informations sur les méthodes prises en charge par le conteneur.
* Reportez-vous à [Forum aux questions (FAQ)](FAQ.md) pour résoudre les problèmes liés à la fonctionnalité de Visage.
* Utiliser plus de [conteneurs Cognitive Services](../cognitive-services-container-support.md)