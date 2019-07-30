---
title: Résolution de problèmes
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s '
ms.openlocfilehash: 2434507ac89d631bb96ae9633403075801879a37
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277396"
---
# <a name="troubleshooting-guide"></a>Guide de résolution des problèmes

Ce guide contient des informations sur les problèmes courants que vous êtes susceptible de rencontrer en utilisant Azure Dev Spaces.

Si vous rencontrez un problème avec Azure Dev Spaces, créez un [problème dans le dépôt GitHub Azure Dev Spaces](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Activation de la journalisation détaillée

Pour résoudre les problèmes plus efficacement, vous pouvez créer des journaux d’activité plus détaillés.

Pour l’extension Visual Studio, affectez 1 à la variable d’environnement `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED`. N’oubliez pas de redémarrer Visual Studio pour appliquer la variable d’environnement. Une fois activés, les journaux d’activité détaillés sont écrits dans votre répertoire `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Dans l’interface CLI, vous pouvez générer plus d’informations pendant l’exécution de la commande en utilisant le commutateur `--verbose`. Vous pouvez également parcourir des journaux d’activité plus détaillés dans `%TEMP%\Azure Dev Spaces`. Sur un Mac, vous pouvez trouver le répertoire TEMP en exécutant `echo $TMPDIR` à partir d’une fenêtre de terminal. Sur un ordinateur Linux, le répertoire TEMP est généralement `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Débogage de services avec plusieurs instances

Actuellement, Azure Dev Spaces fonctionne mieux lors du débogage d’une seule instance ou d’un seul pod. Le fichier azds.yaml contient un paramètre, *replicaCount*, qui indique le nombre de pods que Kubernetes exécute pour votre service. Si vous changez le paramètre replicaCount pour configurer votre application afin d’exécuter plusieurs pods pour un service donné, le débogueur s’attache au premier pod, quand l’ordre est alphabétique. Le débogueur s’attache à un autre pod une fois que le pod d’origine est recyclé, ce qui peut provoquer un comportement inattendu.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erreur « Échec de la création du contrôleur Azure Dev Spaces »

### <a name="reason"></a>Motif
Vous pouvez voir cette erreur lorsqu’il y a un problème de création du contrôleur. Dans le cas d’une erreur temporaire, supprimez et recréez le contrôleur pour la corriger.

### <a name="try"></a>Essai

Supprimez le contrôleur :

```bash
azds remove -g <resource group name> -n <cluster name>
```

Vous devez utiliser l’interface CLI Azure Dev Spaces pour supprimer un contrôleur. La suppression d’un contrôleur n’est pas possible à partir de Visual Studio. Vous ne pouvez pas non plus supprimer un contrôleur à partir d’Azure Cloud Shell étant donné que vous ne pouvez pas installer la CLI Azure Dev Spaces dans Azure Cloud Shell.

Si vous n’avez pas installé la CLI Azure Dev Spaces, installez-la à l’aide de la commande suivante avant de supprimer le contrôleur :

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

La recréation du contrôleur est possible à partir de CLI ou de Visual Studio. Pour obtenir des exemples, consultez les guides de démarrage rapide [Développement en équipe](quickstart-team-development.md) ou [Développer avec .NET Core](quickstart-netcore-visualstudio.md).

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
    
### <a name="multi-stage-dockerfiles"></a>Dockerfiles à plusieurs étapes :
Vous recevez un message d’erreur *Impossible de démarrer le service* lors de l’utilisation d’un fichier Dockerfile multiphase. Dans ce cas, la sortie détaillée contient le texte suivant :

```cmd
$ azds up -v
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

Cette erreur est due au fait que les nœuds AKS exécutent une version antérieure de Docker qui ne prend pas en charge les builds multiphases. Pour éviter les builds multiphases, réécrivez votre fichier Dockerfile.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Réexécution d’un service après la recréation d’un contrôleur
Vous recevez un message d’erreur *Impossible de démarrer le service*  lorsque vous essayez de réexécuter un service après avoir supprimé, puis recréé le contrôleur Azure Dev Spaces associé à ce cluster. Dans ce cas, la sortie détaillée contient le texte suivant :

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Cette erreur est due au fait que la suppression du contrôleur Dev Spaces ne supprime pas les services précédemment installés par ce contrôleur. La recréation du contrôleur suivie de la tentative d’exécution des services à l’aide du nouveau contrôleur se solde par un échec car les anciens services sont toujours en place.

Pour remédier à ce problème, utilisez la commande `kubectl delete` afin de supprimer manuellement les anciens services de votre cluster, puis réexécutez Dev Spaces pour installer les nouveaux services.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Échec de la résolution de nom DNS pour une URL publique associée à un service Dev Spaces

Vous pouvez configurer un point de terminaison d’URL publique pour votre service en spécifiant le commutateur `--public` dans la commande `azds prep` ou en cochant la case `Publicly Accessible` dans Visual Studio. Le nom DNS public est automatiquement inscrit lorsque vous exécutez votre service dans Dev Spaces. Si ce nom DNS n’est pas inscrit, vous voyez un message d’erreur *Impossible d’afficher la page* ou *Impossible d’accéder au site* dans votre navigateur web lors de la connexion à l’URL publique.

### <a name="try"></a>Essayez de procéder comme suit :

Vous pouvez utiliser la commande suivante pour répertorier toutes les URL associées à vos services Dev Spaces :

```cmd
azds list-uris
```

Si une URL est à l’état *En attente*, cela signifie que Dev Spaces attend toujours que l’inscription DNS se termine. L’inscription peut parfois prendre quelques minutes. Dev Spaces ouvre également un tunnel localhost pour chaque service, vous pouvez utiliser pendant l’attente d’inscription DNS.

Si une URL reste à l’état *En attente* pendant plus de 5 minutes, cela peut indiquer un problème lié au pod DNS externe créant le point de terminaison public ou au pod du contrôleur d’entrée nginx assurant l’acquisition du point de terminaison public. Vous pouvez utiliser les commandes suivantes pour supprimer ces pods. AKS recrée automatiquement les pods supprimés.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Erreur « Les outils et configurations nécessaires sont manquants »

Cette erreur peut se produire lors du lancement de VS Code : « [Azure Dev Spaces] Les outils et configurations nécessaires pour générer et déboguer '[nom du projet]' sont manquants ».
L’erreur signifie que azds.exe ne se trouve pas dans la variable d’environnement PATH, comme indiqué dans VS Code.

### <a name="try"></a>Essayez de procéder comme suit :

Lancez VS Code à partir d’une invite de commandes dans laquelle la variable d’environnement PATH est correctement définie.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Message d’erreur « Required tools to build and debug ’nom_projet’ are out of date » (« Les outils nécessaires à la compilation et au débogage de ’nom_projet’ ne sont plus à jour »).

Cette erreur se produit dans Visual Studio Code si vous avez une version plus récente de l’extension VS Code pour Azure Dev Spaces et une version plus ancienne de l’interface de ligne de commande Azure Dev Spaces.

### <a name="try"></a>Essai

Téléchargez et installez la dernière version de l’interface CLI Azure Dev Spaces :

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>L’erreur « azds » n’est pas reconnue en tant que programme exécutable, fichier de commandes ou commande interne ou externe
 
Vous risquez de voir cette erreur si azds.exe n’est pas installé ou configuré correctement.

### <a name="try"></a>Essayez de procéder comme suit :

1. Vérifiez l’emplacement %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI pour azds.exe. S’il y figure, ajoutez cet emplacement à la variable d’environnement PATH.
2. Si azds.exe n’est pas installé, exécutez la commande suivante :

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Avertissement « Impossible de générer Dockerfile en raison d’un langage non pris en charge »
Azure Dev Spaces fournit une prise en charge native de C# et Node.js. Quand vous exécutez *azds prep* dans un répertoire contenant du code écrit dans l’un de ces langages, Azure Dev Spaces crée automatiquement le fichier Dockerfile approprié pour vous.

Vous pouvez toujours utiliser Azure Dev Spaces avec du code écrit dans d’autres langages, mais vous devez créer manuellement le fichier Dockerfile avant d’exécuter *azds up* pour la première fois.

### <a name="try"></a>Essayez de procéder comme suit :
Si votre application est écrite dans un langage qu’Azure Dev Spaces ne prend pas nativement en charge, vous devez fournir un fichier Dockerfile approprié pour générer une image conteneur qui exécute votre code. Docker fournit la [liste des bonnes pratiques pour écrire des fichiers Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) et des [informations de référence sur Dockerfile](https://docs.docker.com/engine/reference/builder/) qui peuvent vous aider à écrire un fichier Dockerfile qui répond à vos besoins.

Une fois que vous avez le fichier Dockerfile approprié, vous pouvez exécuter *azds up* pour exécuter votre application dans Azure Dev Spaces.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erreur « erreur de connexion en amont ou déconnexion/réinitialisation avant les en-têtes »
Vous pouvez obtenir cette erreur en tentant d’accéder à votre service. Par exemple, quand vous accédez à l’URL du service dans un navigateur. 

### <a name="reason"></a>Motif 
Le port du conteneur n’est pas disponible. Ce problème peut se produire, car : 
* Le conteneur est toujours en cours de génération et de déploiement. Ce problème peut survenir si vous exécutez `azds up` ou démarrez le débogueur, puis que vous tentez d’accéder au conteneur avant que celui-ci ait été correctement déployé.
* La configuration du port n’est pas cohérente dans le fichier _Dockerfile_, le chart Helm et le code serveur chargé d’ouvrir un port.

### <a name="try"></a>Essayez de procéder comme suit :
1. Si le conteneur est en cours de génération et/ou de déploiement, vous pouvez patienter 2 à 3 secondes avant d’essayer à nouveau d’accéder au service. 
1. Vérifiez la configuration du port. Les numéros de port spécifiés doivent être **identiques** dans toutes les ressources suivantes :
    * **Dockerfile :** Spécifié par l’instruction `EXPOSE`.
    * **[Graphique Helm](https://docs.helm.sh) :** Spécifié par les valeurs `externalPort` et `internalPort` d’un service (souvent situées dans un fichier `values.yml`),
    * Ports ouverts dans le code d’application, par exemple dans Node.js : `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Fichier de configuration introuvable
Vous exécutez `azds up` et obtenez l’erreur suivante : `Config file not found: .../azds.yaml`

### <a name="reason"></a>Motif
Vous devez exécuter `azds up` à partir du répertoire racine du code que vous souhaitez exécuter et initialiser le dossier du code à exécuter avec Azure Dev Spaces.

### <a name="try"></a>Essayez de procéder comme suit :
1. Remplacez le répertoire actuel par le dossier racine contenant le code de votre service. 
1. Si le dossier du code ne contient pas de fichier _azds.yaml_, exécutez `azds prep` pour générer des ressources Docker, Kubernetes et Azure Dev Spaces.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Error: « Le programme canal « azds » s’est arrêté de façon inattendue avec le code 126. »
Le démarrage du débogueur VS Code peut parfois générer cette erreur.

### <a name="try"></a>Essayez de procéder comme suit :
1. Fermez et rouvrez VS Code.
2. Appuyez de nouveau sur F5.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Erreur de débogage « Failed to find debugger extension for type:coreclr » (Impossible de trouver l’extension du débogueur pour type: coreclr)
L’exécution du débogueur VS Code signale l’erreur : `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Motif
L’extension VS Code pour C# n’est pas installée sur votre ordinateur de développement. L’extension C# inclut la prise en charge du débogage pour .NET Core (CoreCLR).

### <a name="try"></a>Essayez de procéder comme suit :
Installez [l’extension VS Code pour C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Erreur de débogage « Le type de débogage configuré « coreclr » n’est pas prise en charge »
L’exécution du débogueur VS Code signale l’erreur : `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Motif
L’extension VS Code pour Azure Dev Spaces n’est pas installée sur votre ordinateur de développement.

### <a name="try"></a>Essayez de procéder comme suit :
Installez l’[extension VS Code pour Azure Dev Spaces](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Erreur de débogage « ’valeur ’cwd’ ’/src’. Le système ne peut pas trouver le fichier spécifié. » ou «launch: program ’/src/ [chemin d’accès au fichier binaire du projet]’ n’existe pas »
L’exécution du débogueur VS Code signale l’erreur : `Invalid 'cwd' value '/src'. The system cannot find the file specified.` et/ou `launch: program '/src/[path to project executable]' does not exist`

### <a name="reason"></a>Motif
Par défaut, l’extension VS Code utilise `src` comme répertoire de travail du projet sur le conteneur. Si vous avez mis à jour votre `Dockerfile` pour spécifier un répertoire de travail différent, vous pouvez voir cette erreur.

### <a name="try"></a>Essayez de procéder comme suit :
Mettez à jour le fichier `launch.json` dans le sous-répertoire `.vscode` de votre dossier de projet. Changez la directive `configurations->cwd` pour pointer vers le même répertoire que le `WORKDIR` défini dans le `Dockerfile` de votre projet. Vous devrez peut-être aussi mettre à jour la directive `configurations->program`.

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
Vous avez besoin d’un accès *Propriétaire* ou *Contributeur* dans votre abonnement Azure pour gérer Azure Dev Spaces. Vous pouvez voir cette erreur si vous tentez de gérer Dev Spaces alors que vous n’avez pas d’accès *Propriétaire* ou *Contributeur* à l’abonnement Azure associé.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Motif
L’abonnement Azure sélectionné n’a pas inscrit l’espace de noms `Microsoft.DevSpaces`.

### <a name="try"></a>Essayez de procéder comme suit :
Une personne bénéficiant d’un accès Propriétaire ou Contributeur à l’abonnement Azure peut exécuter la commande Azure CLI suivante pour inscrire manuellement l’espace de noms `Microsoft.DevSpaces` :

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Dev Spaces expire à l’étape *En attente de la build de l’image conteneur...* avec des nœuds virtuels AKS

### <a name="reason"></a>Motif
Ce délai d’attente se produit quand vous essayez d’utiliser Dev Spaces afin d’exécuter un service configuré pour s’exécuter sur un [nœud virtuel AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Actuellement, Dev Spaces ne gère pas les services de build ou de débogage sur des nœuds virtuels.

Si vous exécutez `azds up` avec le commutateur `--verbose` ou si vous activez la journalisation détaillée dans Visual Studio, vous obtenez des informations supplémentaires :

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

La commande ci-dessus indique que le pod du service a été assigné à *virtual-node-aci-linux*, qui est un nœud virtuel.

### <a name="try"></a>Essayez de procéder comme suit :
Mettez à jour le graphique Helm du service pour supprimer toutes les valeurs *nodeSelector* et/ou *tolerations* qui permettent au service de s’exécuter sur un nœud virtuel. Ces valeurs sont généralement définies dans le fichier `values.yaml` du graphique.

Vous pouvez quand même utiliser un cluster AKS avec la fonctionnalité des nœuds virtuels activée, si le service que vous voulez générer/déboguer par le biais de Dev Spaces s’exécute sur un nœud de machine virtuelle. Il s’agit de la configuration par défaut.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>« Error: could not find a ready tiller pod» (Erreur : Aucun pod Tiller prêt n’a été trouvé) lors du lancement de Dev Spaces

### <a name="reason"></a>Motif
Cette erreur se produit lorsque le client Helm ne peut plus communiquer avec le pod Tiller exécuté dans le cluster.

### <a name="try"></a>Essayez de procéder comme suit :
Le fait de redémarrer les nœuds d’agent de votre cluster permet généralement de résoudre ce problème.

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>« Erreur : échec de la mise en production du service azds-\<identificateur\>-\<nomespace\>-\<nomservice\> : '\<nomservice\>' existe déjà » ou « Refus de l’accès pull pour \<nomservice\> ; le référentiel n’existe pas ou nécessite 'docker login' »

### <a name="reason"></a>Motif
Ces erreurs peuvent se produire si vous exécutez une combinaison de commandes Helm directes (comme `helm install`, `helm upgrade` ou `helm delete`) et de commandes Dev Spaces (comme `azds up` et `azds down`) dans le même espace de développement. Ces erreurs sont dues au fait que Dev Spaces utilise sa propre instance Tiller, qui est en conflit avec votre instance Tiller exécutée dans le même espace de développement.

### <a name="try"></a>Essayez de procéder comme suit :
Il est possible d’utiliser à la fois des commandes Helm et des commandes Dev Spaces dans le même cluster AKS, mais chaque espace de noms où Dev Spaces est activé doit utiliser l’une ou l’autre de ces commandes.

Par exemple, supposons que vous utilisez une commande Helm pour exécuter votre application entière dans un espace de développement parent. Vous pouvez créer des espaces de développement enfants à partir de ce parent, utiliser ensuite Dev Spaces pour exécuter individuellement les services dans ces espaces de développement enfants, puis tester tous les services ensemble. Quand vous êtes prêt à enregistrer vos modifications, utilisez une commande Helm pour déployer le code mis à jour dans l’espace de développement parent. N’utilisez pas `azds up` pour exécuter le service mis à jour dans l’espace de développement parent, car cela provoquerait un conflit avec le service initialement exécuté à l’aide d’une commande Helm.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Le proxy Azure Dev Spaces peut interférer avec d'autres pods en cours d’exécution dans un espace de développement

### <a name="reason"></a>Motif
Lorsque vous activez Dev Spaces sur un espace de noms de votre cluster AKS, un conteneur supplémentaire appelé _mindaro-proxy_ est installé dans chaque pod en cours d'exécution dans cet espace de noms. Ce conteneur intercepte les appels aux services dans le pod, ce qui fait partie intégrante des fonctionnalités de développement en équipe de Dev Spaces. Toutefois, il peut interférer avec certains services en cours d’exécution dans ces pods. Il est connu pour interférer avec les pods exécutant Cache Azure pour Redis, ce qui provoque des erreurs de connexion et des échecs dans la communication principale/secondaire.

### <a name="try"></a>Essayez de procéder comme suit :
Vous pouvez déplacer les pods concernés vers un espace de noms situé à l’intérieur du cluster dans lequel Dev Spaces n’est _pas_ activé. Le reste de votre application peut continuer à s’exécuter à l’intérieur d’un espace de noms où Dev Spaces est activé. Dev Spaces n’installe pas le conteneur _mindaro-proxy_ dans les espaces de noms qui ne le prennent pas en charge.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces ne semble pas utiliser mon fichier Dockerfile existant pour créer un conteneur

### <a name="reason"></a>Motif
Azure Dev Spaces peut être configuré pour pointer vers un fichier _Dockerfile_ spécifique de votre projet. S’il s’avère qu’Azure Dev Spaces n’utilise pas le fichier _Dockerfile_ attendu pour générer vos conteneurs, vous devrez peut-être indiquer explicitement à Azure Dev Spaces quel fichier Dockerfile utiliser. 

### <a name="try"></a>Essayez de procéder comme suit :
Ouvrez le fichier _azds.yaml_ qui a été généré par Azure Dev Spaces dans votre projet. Utilisez la directive *configurations->develop->build->dockerfile* pour pointer vers le fichier Dockerfile à utiliser :

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erreur « Échec de la surveillance interne : regardez ENOSPC » lors de l’attachement d’un débogueur à une application Node.js

### <a name="reason"></a>Motif

Le nœud qui exécute le pod avec l’application Node.js à laquelle vous tentez d’attacher un débogueur a dépassé la valeur *fs.inotify.max_user_watches*. Dans certains cas, [la valeur par défaut *fs.inotify.max_user_watches* est trop petite pour gérer l’attachement direct d’un débogueur à un pod](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Essai
Une solution de contournement temporaire pour ce problème consiste à augmenter la valeur de *fs.inotify.max_user_watches* sur chaque nœud du cluster, puis à redémarrer ce nœud pour appliquer les changements.

## <a name="new-pods-are-not-starting"></a>Les nouveaux pods ne démarrent pas

### <a name="reason"></a>Motif

L’initialiseur Kubernetes ne peut pas appliquer le PodSpec pour les nouveaux pods en raison des changements d’autorisation RBAC apportés au rôle *administrateur de cluster* dans le cluster. Le nouveau pod a peut-être également un PodSpec non valide ; par exemple, le compte de service associé au pod n’existe plus. Pour voir les pods qui sont *En attente* en raison du problème d’initialiseur, utilisez la commande `kubectl get pods` :

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Ce problème peut impacter les pods dans *tous les espaces de noms* sur le cluster, y compris les espaces de noms où Azure Dev Spaces n’est pas activé.

### <a name="try"></a>Essai

[Mettez à jour la CLI Dev Spaces avec la dernière version](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools), puis supprimez *azds InitializerConfiguration* dans le contrôleur Azure Dev Spaces :

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Une fois que vous avez supprimé *azds InitializerConfiguration* du contrôleur Azure Dev Spaces, utilisez `kubectl delete` pour supprimer tous les pods ayant l’état *En attente*. Après cela, redéployez vos pods.

Si les nouveaux pods sont toujours bloqués dans l’état *En attente* après un redéploiement, utilisez `kubectl delete` pour supprimer tous les pods *En attente*. Quand vous avez supprimé tous les pods en attente, supprimez le contrôleur dans le cluster, puis réinstallez-le :

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Après avoir réinstallé votre contrôleur, redéployez vos pods.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Autorisations RBAC incorrectes pour appeler les API et le contrôleur Dev Spaces

### <a name="reason"></a>Motif
L’utilisateur qui tente d’accéder au contrôleur Azure Dev Spaces doit avoir un accès en lecture sur le *kubeconfig* administrateur dans le cluster AKS. Par exemple, cette autorisation est disponible dans le [rôle administrateur intégré de cluster du service Azure Kubernetes](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Pour accéder au contrôleur Azure Dev Spaces, l’utilisateur doit également avoir le rôle RBAC *Contributeur* ou *Propriétaire* sur le contrôleur.

### <a name="try"></a>Essai
Pour plus d’informations sur la mise à jour des autorisations utilisateur dans un cluster AKS, consultez [cet article](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Pour mettre à jour le rôle RBAC d’un utilisateur sur le contrôleur :

1. Connectez-vous au portail Azure sur https://portal.azure.com.
1. Accédez au groupe de ressources contenant le contrôleur, qui est généralement le même que votre cluster AKS.
1. Cochez la case *Afficher les types masqués*.
1. Cliquez sur le contrôleur.
1. Ouvrez le volet *Contrôle d’accès (IAM)* .
1. Cliquez sur l’onglet *Attributions de rôles*.
1. Cliquez sur *Ajouter*, puis sur *Ajouter une attribution de rôle*.
    * Dans *Rôle*, sélectionnez *Contributeur* ou *Propriétaire*.
    * Dans *Attribuer l’accès à*, sélectionnez *Utilisateur, groupe ou principal du service Azure AD*.
    * Dans *Sélectionner*, recherchez l’utilisateur auquel vous souhaitez accorder des autorisations.
1. Cliquez sur *Enregistrer*.

## <a name="controller-create-failing-due-to-controller-name-length"></a>Échec de la création du contrôleur en raison de la longueur du nom de contrôleur

### <a name="reason"></a>Motif
Le nom d’un contrôleur Azure Dev Spaces ne peut pas comporter plus de 31 caractères. Si le nom de votre contrôleur dépasse cette limite de caractères, quand vous activez Dev Spaces sur un cluster AKS ou créez un contrôleur, vous recevez une erreur similaire à celle-ci :

*Échec de la création du contrôleur Dev Spaces 'nom-de-contrôleur-qui-est-trop-long-aks-usa-est' dans le cluster : Le nom de contrôleur Azure Dev Spaces 'nom-de-contrôleur-qui-est-trop-long-aks-usa-est' n’est pas valide. Violation de contrainte(s) : Les noms de contrôleur Azure Dev Spaces peuvent contenir 31 caractères au maximum*

### <a name="try"></a>Essai

Créez un contrôleur en choisissant un autre nom :

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Échec de l’activation de Dev Spaces quand des pools de nœuds Windows sont ajoutés à un cluster AKS

### <a name="reason"></a>Motif
Actuellement, Azure Dev Spaces peut uniquement être exécuté sur des pods et nœuds Linux. Lorsque vous disposez d’un cluster AKS avec un pool de nœuds Windows, vous devez vous assurer que les pods Azure Dev Spaces sont planifiés uniquement sur des nœuds Linux. Si un pod Azure Dev Spaces est planifié pour s’exécuter sur un nœud Windows, ce pod ne démarre pas et l’activation de Dev Spaces échoue.

### <a name="try"></a>Essai
[Ajoutez une teinte](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) à votre cluster AKS pour vous assurer que les pods Linux ne sont pas planifiés pour être exécutés sur un nœud Windows.

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Erreur « Aucun nœud Linux non teinté trouvé à l’état Prêt sur le cluster. Il doit exister au moins un nœud Linux non teinté à l’état Prêt pour déployer des pods dans l’espace de noms « azds ». »

### <a name="reason"></a>Motif

Azure Dev Spaces ne peut pas créer de contrôleur sur votre cluster AKS, car il n’a pas trouvé de nœud non teinté à l’état *Prêt* sur lequel planifier des pods. Azure Dev Spaces requiert au moins un nœud Linux à l’état *Prêt* permettant la planification des pods sans spécifier de tolérances.

### <a name="try"></a>Essai
[Mettez à jour la configuration de teinte](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) sur votre cluster AKS pour vous assurer qu’au moins un nœud Linux permet la planification de pods sans spécifier de tolérances. Assurez-vous qu’au moins un nœud Linux permettant la planification des pods sans spécifier de tolérances est à l’état *Prêt*. Si votre nœud prend beaucoup de temps à atteindre l’état *Prêt*, vous pouvez essayer de redémarrer le nœud.

## <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Erreur « Interface CLI Azure Dev Spaces pas correctement installée » lors de l’exécution de `az aks use-dev-spaces`

### <a name="reason"></a>Motif
Une mise à jour de l’interface CLI Azure Dev Spaces a modifié le chemin d’accès de l’installation. Si vous utilisez une version de l’interface Azure CLI antérieure à 2.0.63, cette erreur peut s’afficher. Pour afficher votre version de l’interface CLI, utilisez `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

L’installation peut réussir malgré le message d’erreur lors de l’exécution de `az aks use-dev-spaces` avec une version d’Azure CLI antérieure à 2.0.63. Vous pouvez continuer à utiliser `azds` sans aucun problème.

### <a name="try"></a>Essai
Mettez à jour votre installation de l’interface [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vers 2.0.63 ou une version ultérieure. Cela permettra de résoudre le message d’erreur que vous recevez lors de l’exécution de `az aks use-dev-spaces`. Vous pouvez également continuer à utiliser votre version actuelle de l’interface Azure CLI et de l’interface CLI Azure Dev Spaces.


## <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>La mise à l’échelle automatique du pod horizontal ne fonctionne pas dans un espace de développement

### <a name="reason"></a>Motif

Lorsque vous exécutez un service dans un espace de développement, le pod de ce service est [injecté avec des conteneurs supplémentaires pour l’instrumentation](how-dev-spaces-works.md#prepare-your-aks-cluster). Ces conteneurs n’ont pas de requêtes ou de limites de ressources définies, ce qui entraîne la désactivation de la mise à l’échelle automatique du pod horizontal.

### <a name="try"></a>Essai
Exécutez le module de mise à l’échelle automatique du pod horizontal dans un espace de noms pour lequel les espaces de développement ne sont pas activés.
