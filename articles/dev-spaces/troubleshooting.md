---
title: Résolution des problèmes | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 09/11/2018
ms.topic: article
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
manager: douge
ms.openlocfilehash: 3f30a62a2f351aecabc37206607c3e28ec5e3ab5
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353356"
---
# <a name="troubleshooting-guide"></a>Guide de résolution des problèmes

Ce guide contient des informations sur les problèmes courants que vous êtes susceptible de rencontrer en utilisant Azure Dev Spaces.

## <a name="enabling-detailed-logging"></a>Activation de la journalisation détaillée

Pour résoudre les problèmes plus efficacement, vous pouvez créer des journaux plus détaillés.

Pour l’extension Visual Studio, affectez 1 à la variable d’environnement `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED`. N’oubliez pas de redémarrer Visual Studio pour appliquer la variable d’environnement. Une fois activés, les journaux détaillés sont écrits dans votre répertoire `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Dans l’interface CLI, vous pouvez générer plus d’informations pendant l’exécution de la commande en utilisant le commutateur `--verbose`. Vous pouvez également parcourir des journaux plus détaillés dans `%TEMP%\Azure Dev Spaces`. Sur un Mac, vous pouvez trouver le répertoire TEMP en exécutant `echo $TMPDIR` à partir d’une fenêtre de terminal. Sur un ordinateur Linux, le répertoire TEMP est généralement `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Débogage de services avec plusieurs instances

À ce stade, Azure Dev Spaces fonctionne mieux lors du débogage d’une seule instance (pod). Le fichier azds.yaml contient un paramètre, replicaCount, qui indique le nombre de pods qui seront exécutés pour votre service. Si vous modifiez le paramètre replicaCount pour configurer votre application afin d’exécuter plusieurs pods pour un service donné, le débogueur s’attache au premier pod (quand l’ordre est alphabétique). Si ce pod est recyclé pour une raison quelconque, le débogueur s’attache à un pod différent, ce qui peut provoquer un comportement inattendu.

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

1. Ouvrez **Outils > Options** et, sous **Projets et solutions**, choisissez **Générer et exécuter**.
2. Remplacez les paramètres de **Commentaires relatifs à la sortie de build du projet MSBuild** par **Détails** ou **Diagnostic**.

    ![Capture d’écran de la boîte de dialogue Outils Options](media/common/VerbositySetting.PNG)
    
Cette erreur peut s’afficher si vous tentez d’utiliser un fichier Dockerfile à plusieurs étapes. La sortie détaillée se présente ainsi :

```cmd
$ azds up
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Ceci est dû au fait que les nœuds AKS exécutent une version antérieure de Docker qui ne prend pas en charge les builds à plusieurs étapes. Vous devrez réécrire votre fichier Dockerfile pour éviter les builds à plusieurs étapes.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Échec de la résolution de nom DNS pour une URL publique associée à un service Dev Spaces

En cas d’échec de la résolution de nom DNS, vous pouvez voir une erreur « Impossible d’afficher cette page » ou « Impossible d’atteindre le site » dans votre navigateur web lors d’une tentative de connexion à l’URL publique associée à un service Dev Spaces.

### <a name="try"></a>Essayez de procéder comme suit :

Vous pouvez utiliser la commande suivante pour répertorier toutes les URL associées à vos services Dev Spaces :

```cmd
azds list-uris
```

Si une URL est à l’état *En attente*, cela signifie que Dev Spaces attend toujours que l’inscription DNS se termine. L’inscription peut parfois prendre quelques minutes. Dev Spaces ouvre également un tunnel localhost pour chaque service, vous pouvez utiliser pendant l’attente d’inscription DNS.

Si une URL reste à l’état *En attente* pendant plus de 5 minutes, cela peut indiquer un problème lié au pod DNS externe créant le point de terminaison public et/ou au pod du contrôleur d’entrée nginx assurant l’acquisition du point de terminaison public. Vous pouvez utiliser les commandes suivantes pour supprimer ces pods. Ils seront automatiquement recréés.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

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

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Avertissement « Impossible de générer Dockerfile en raison d’un langage non pris en charge »
Azure Dev Spaces fournit une prise en charge native de C# et Node.js. Quand vous exécutez *azds prep* dans un répertoire contenant du code écrit dans l’un de ces langages, Azure Dev Spaces crée automatiquement le fichier Dockerfile approprié pour vous.

Vous pouvez toujours utiliser Azure Dev Spaces avec du code écrit dans d’autres langages, mais vous devez créer le fichier Dockerfile vous-même avant d’exécuter *azds up* pour la première fois.

### <a name="try"></a>Essayez de procéder comme suit :
Si votre application est écrite dans un langage qu’Azure Dev Spaces ne prend pas nativement en charge, vous devez fournir un fichier Dockerfile approprié pour générer une image conteneur qui exécute votre code. Docker fournit la [liste des bonnes pratiques pour écrire des fichiers Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) ainsi que des [informations de référence sur Dockerfile](https://docs.docker.com/engine/reference/builder/) qui peuvent vous aider à écrire un fichier Dockerfile qui répond à vos besoins.

Une fois que vous avez le fichier Dockerfile approprié, vous pouvez exécuter *azds up* pour exécuter votre application dans Azure Dev Spaces.

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
Le démarrage du débogueur VS Code peut parfois générer cette erreur.

### <a name="try"></a>Essayez de procéder comme suit :
1. Fermez et rouvrez VS Code.
2. Appuyez de nouveau sur F5.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Erreur de débogage « Failed to find debugger extension for type:coreclr » (Impossible de trouver l’extension du débogueur pour type: coreclr)
L’exécution du débogueur VS Code signale l’erreur : `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Motif
L’extension VS Code pour C# n’est pas installée sur votre ordinateur de développement. L’extension C# inclut la prise en charge du débogage pour .Net Core (CoreCLR).

### <a name="try"></a>Essayez de procéder comme suit :
Installez [l’extension VS Code pour C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

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

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>« Error: could not find a ready tiller pod» (Erreur : Aucun pod Tiller prêt n’a été trouvé) lors du lancement de Dev Spaces

### <a name="reason"></a>Motif
Cette erreur se produit lorsque le client Helm ne peut plus communiquer avec le pod Tiller exécuté dans le cluster.

### <a name="try"></a>Essayez de procéder comme suit :
Le fait de redémarrer les nœuds d’agent de votre cluster permet généralement de résoudre ce problème.

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
