---
title: Résolution des problèmes | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
manager: douge
ms.openlocfilehash: 371bb9195266f3511d115de2532e6b64f49ef26f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199294"
---
# <a name="troubleshooting-guide"></a>Guide de résolution des problèmes

Ce guide contient des informations sur les problèmes courants que vous êtes susceptible de rencontrer en utilisant Azure Dev Spaces.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erreur « erreur de connexion en amont ou déconnexion/réinitialisation avant les en-têtes »
Vous pouvez obtenir cette erreur en tentant d’accéder à votre service. Par exemple, quand vous accédez à l’URL du service dans un navigateur. 

### <a name="reason"></a>Raison 
Le port du conteneur n’est pas disponible. Cause possible : 
* Le conteneur est toujours en cours de génération et de déploiement. Cela peut être le cas si vous exécutez `azds up` ou démarrez le débogueur, puis que vous tentez d’accéder au conteneur avant que celui-ci ait été correctement déployé.
* La configuration du port n’est pas cohérente dans le fichier Dockerfile, le graphique Helm et le code serveur chargé d’ouvrir un port.

### <a name="try"></a>Essayez de procéder comme suit :
1. Si le conteneur est en cours de génération et/ou de déploiement, vous pouvez patienter 2 à 3 secondes avant d’essayer à nouveau d’accéder au service. 
1. Vérifiez la configuration du port. Les numéros de port spécifiés doivent être **identiques** dans toutes les ressources ci-dessous :
    * **Dockerfile :** spécifié par l’instruction `EXPOSE`.
    * **[Graphique Helm](https://docs.helm.sh) :** spécifié par les valeurs `externalPort` et `internalPort` d’un service (souvent situées dans un fichier `values.yml`),
    * Ports ouverts dans le code d’application, par exemple dans Node.js : `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Fichier de configuration introuvable
Vous exécutez `azds up` et obtenez l’erreur suivante : `Config file not found: .../azds.yaml`

### <a name="reason"></a>Raison
Vous devez exécuter `azds up` à partir du répertoire racine du code que vous souhaitez exécuter et initialiser le dossier du code à exécuter avec Azure Dev Spaces.

### <a name="try"></a>Essayez de procéder comme suit :
1. Remplacez le répertoire actuel par le dossier racine contenant le code de votre service. 
1. Si le dossier du code ne contient pas de fichier azds.yaml, exécutez `azds prep` pour générer des ressources Docker, Kubernetes et Azure Dev Spaces.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erreur : « Le programme canal « azds » s’est arrêté de façon inattendue avec le code 126. »
Le démarrage du débogueur VS Code peut parfois générer cette erreur. Il s'agit d'un problème connu.

### <a name="try"></a>Essayez de procéder comme suit :
1. Fermez et rouvrez VS Code.
2. Appuyez de nouveau sur F5.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Erreur de débogage « Le type de débogage configuré « coreclr » n’est pas prise en charge »
L’exécution du débogueur VS Code signale l’erreur : `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Raison
L’extension VS Code pour Azure Dev Spaces n’est pas installée sur votre ordinateur de développement.

### <a name="try"></a>Essayez de procéder comme suit :
Installez l’[extension VS Code pour Azure Dev Spaces](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Le type ou le nom d’espace de noms « MyLibrary » est introuvable

### <a name="reason"></a>Raison 
Le contexte de build étant par défaut au niveau du projet/service, le projet de bibliothèque que vous utilisez est introuvable.

### <a name="try"></a>Essayez de procéder comme suit :
Tâches à effectuer :
1. Modifiez le fichier azds.yaml pour définir le contexte de build au niveau de la solution.
2. Modifiez les fichiers Dockerfile et Dockerfile.develop de sorte qu’ils fassent correctement référence aux fichiers csproj, eu égard au nouveau contexte de build.
3. Placez un fichier .dockerignore à côté du fichier .sln et modifiez-le, si nécessaire.

Vous trouverez un exemple à l’adresse https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftconnectedenvironmentregisteraction-authorization-error"></a>Erreur d’autorisation « 'Microsoft.ConnectedEnvironment/register/action »
L’erreur suivante peut s’afficher quand vous gérez un espace Azure Dev Spaces et que vous travaillez dans un abonnement Azure pour lequel vous ne bénéficiez pas d’un accès Propriétaire ou Contributeur.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.ConnectedEnvironment/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Raison
L’abonnement Azure sélectionné n’a pas inscrit l’espace de noms Microsoft.ConnectedEnvironment.

### <a name="try"></a>Essayez de procéder comme suit :
Une personne bénéficiant d’un accès Propriétaire ou Contributeur à l’abonnement Azure peut exécuter la commande Azure CLI suivante pour inscrire manuellement l’espace de noms Microsoft.ConnectedEnvironment :

```cmd
az provider register --namespace Microsoft.ConnectedEnvironment
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces ne semble pas utiliser mon fichier Dockerfile existant pour créer un conteneur 

### <a name="reason"></a>Raison
Azure Dev Spaces peut être configuré pour pointer vers un fichier Dockerfile spécifique de votre projet. S’il s’avère qu’Azure Dev Spaces n’utilise pas le fichier Dockerfile attendu pour générer vos conteneurs, vous devrez peut-être lui indiquer explicitement son emplacement. 

### <a name="try"></a>Essayez de procéder comme suit :
Ouvrez le fichier `azds.yaml` qui a été généré par Azure Dev Spaces dans votre projet. Utilisez la directive `configurations->develop->build->dockerfile` pour pointer vers le fichier Dockerfile que vous voulez utiliser :

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```