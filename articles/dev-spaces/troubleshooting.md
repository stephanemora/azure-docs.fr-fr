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
ms.openlocfilehash: b2ef450a429b26843cf770a6243c6f4de932de43
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247318"
---
# <a name="troubleshooting-guide"></a>Guide de résolution des problèmes

Ce guide contient des informations sur les problèmes courants que vous êtes susceptible de rencontrer en utilisant Azure Dev Spaces.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erreur « Échec de la création du contrôleur Azure Dev Spaces »

Vous pouvez voir cette erreur lorsqu’il y a un problème de création du contrôleur. Dans le cas d’une erreur temporaire, la suppression puis la recréation du contrôleur suffisent pour la corriger.

### <a name="try"></a>Essayez de procéder comme suit :

Pour supprimer le contrôleur, utilisez Azure Dev Spaces CLI. Il n’est pas possible de le faire dans Visual Studio ou dans Cloud Shell. Pour installer AZDS CLI, installez tout d’abord Azure CLI, puis exécutez cette commande :

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Exécutez ensuite cette commande pour supprimer le contrôleur :

```cmd
azds remove -g <resource group name> -n <cluster name>
```

La recréation du contrôleur est possible à partir de CLI ou de Visual Studio. Suivez les instructions dans les tutoriels, comme si vous démarriez pour la première fois.


## <a name="error-service-cannot-be-started"></a>Erreur « Impossible de démarrer le service »

Vous pouvez voir cette erreur lorsque votre code de service ne parvient pas à démarrer. La cause se trouve souvent dans le code utilisateur. Pour obtenir plus d’informations de diagnostic, apportez les modifications suivantes à vos commandes et paramètres :

### <a name="try"></a>Essayez de procéder comme suit :

Sur la ligne de commande :

Lorsque vous vous servez de _azds.exe_, utilisez l’option de ligne de commande --verbose, et l’option de ligne de commande --output pour spécifier le format de sortie.
 
    ```cmd
    azds up --verbose --output json
    ```

Dans Visual Studio :

1. Ouvrez **Outils > Options** et, sous **Projets et solutions**, choisissez de **Générer et exécuter**.
2. Remplacez les paramètres de **Commentaires relatifs à la sortie de build du projet MSBuild** par **Détails** ou **Diagnostic**.

    ![Capture d’écran de la boîte de dialogue Outils Options](media/common/VerbositySetting.PNG)

## <a name="error-required-tools-and-configurations-are-missing"></a>Erreur « Les outils et configurations nécessaires sont manquants »

Cette erreur peut se produire lors du lancement de VS Code : « [Azure Dev Spaces] Les outils et configurations nécessaires pour générer et déboguer '[nom du projet]' sont manquants ».
L’erreur signifie que azds.exe ne se trouve pas dans la variable d’environnement PATH, comme indiqué dans VS Code.

### <a name="try"></a>Essayez de procéder comme suit :

Lancez VS Code à partir d’une invite de commandes dans laquelle la variable d’environnement PATH est correctement définie.

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>L’erreur « azds » n’est pas reconnue en tant que programme exécutable, fichier de commandes ou commande interne ou externe
 
Vous risquez de voir cette erreur si azds.exe n’est pas installé ou configuré correctement.

### <a name="try"></a>Essayez de procéder comme suit :

1. Vérifiez l’emplacement %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (Preview) pour azds.exe. S’il y figure, ajoutez cet emplacement à la variable d’environnement PATH.
2. Si azds.exe n’est pas installé, exécutez la commande suivante :

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erreur « erreur de connexion en amont ou déconnexion/réinitialisation avant les en-têtes »
Vous pouvez obtenir cette erreur en tentant d’accéder à votre service. Par exemple, quand vous accédez à l’URL du service dans un navigateur. 

### <a name="reason"></a>Motif 
Le port du conteneur n’est pas disponible. Ce problème peut se produire, car : 
* Le conteneur est toujours en cours de génération et de déploiement. Ce problème peut survenir si vous exécutez `azds up` ou démarrez le débogueur, puis que vous tentez d’accéder au conteneur avant que celui-ci ait été correctement déployé.
* La configuration du port n’est pas cohérente dans le fichier _Dockerfile_, le chart Helm et le code serveur chargé d’ouvrir un port.

### <a name="try"></a>Essayez de procéder comme suit :
1. Si le conteneur est en cours de génération et/ou de déploiement, vous pouvez patienter 2 à 3 secondes avant d’essayer à nouveau d’accéder au service. 
1. Vérifiez la configuration du port. Les numéros de port spécifiés doivent être **identiques** dans toutes les ressources ci-dessous :
    * **Dockerfile :** spécifié par l’instruction `EXPOSE`.
    * **[Graphique Helm](https://docs.helm.sh) :** spécifié par les valeurs `externalPort` et `internalPort` d’un service (souvent situées dans un fichier `values.yml`),
    * Ports ouverts dans le code d’application, par exemple dans Node.js : `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Fichier de configuration introuvable
Vous exécutez `azds up` et obtenez l’erreur suivante : `Config file not found: .../azds.yaml`

### <a name="reason"></a>Motif
Vous devez exécuter `azds up` à partir du répertoire racine du code que vous souhaitez exécuter et initialiser le dossier du code à exécuter avec Azure Dev Spaces.

### <a name="try"></a>Essayez de procéder comme suit :
1. Remplacez le répertoire actuel par le dossier racine contenant le code de votre service. 
1. Si le dossier du code ne contient pas de fichier _azds.yaml_, exécutez `azds prep` pour générer des ressources Docker, Kubernetes et Azure Dev Spaces.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erreur : « Le programme canal « azds » s’est arrêté de façon inattendue avec le code 126. »
Le démarrage du débogueur VS Code peut parfois générer cette erreur. Il s'agit d'un problème connu.

### <a name="try"></a>Essayez de procéder comme suit :
1. Fermez et rouvrez VS Code.
2. Appuyez de nouveau sur F5.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Erreur de débogage « Le type de débogage configuré « coreclr » n’est pas prise en charge »
L’exécution du débogueur VS Code signale l’erreur : `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Motif
L’extension VS Code pour Azure Dev Spaces n’est pas installée sur votre ordinateur de développement.

### <a name="try"></a>Essayez de procéder comme suit :
Installez l’[extension VS Code pour Azure Dev Spaces](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Le type ou le nom d’espace de noms « MyLibrary » est introuvable

### <a name="reason"></a>Motif 
Le contexte de build étant par défaut au niveau du projet/service, le projet de bibliothèque que vous utilisez est introuvable.

### <a name="try"></a>Essayez de procéder comme suit :
Tâches à effectuer :
1. Modifiez le fichier _azds.yaml_ pour définir le contexte de build au niveau de la solution.
2. Modifiez les fichiers _Dockerfile_ et _Dockerfile.develop_ pour qu’ils fassent correctement référence aux fichiers (_csproj_) du projet, en tenant compte du nouveau contexte de build.
3. Placez un fichier _.dockerignore_ à côté du fichier .sln et modifiez-le, si nécessaire.

Vous trouverez un exemple à l’adresse https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Erreur d’autorisation « Microsoft.DevSpaces/register/action »
L’erreur suivante peut s’afficher quand vous gérez un espace Azure Dev Spaces et que vous travaillez dans un abonnement Azure pour lequel vous ne bénéficiez pas d’un accès Propriétaire ou Contributeur.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Motif
L’abonnement Azure sélectionné n’a pas inscrit l’espace de noms `Microsoft.DevSpaces`.

### <a name="try"></a>Essayez de procéder comme suit :
Une personne bénéficiant d’un accès Propriétaire ou Contributeur à l’abonnement Azure peut exécuter la commande Azure CLI suivante pour inscrire manuellement l’espace de noms `Microsoft.DevSpaces` :

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces ne semble pas utiliser mon fichier Dockerfile existant pour créer un conteneur 

### <a name="reason"></a>Motif
Azure Dev Spaces peut être configuré pour pointer vers un fichier _Dockerfile_ spécifique de votre projet. S’il s’avère qu’Azure Dev Spaces n’utilise pas le fichier _Dockerfile_ attendu pour générer vos conteneurs, vous devrez peut-être lui indiquer explicitement son emplacement. 

### <a name="try"></a>Essayez de procéder comme suit :
Ouvrez le fichier _azds.yaml_ qui a été généré par Azure Dev Spaces dans votre projet. Utilisez la directive `configurations->develop->build->dockerfile` pour pointer vers le fichier Dockerfile que vous voulez utiliser :

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```