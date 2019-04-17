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
ms.openlocfilehash: 4617e878f2af446608ede4e0aed644848564a074
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609073"
---
# <a name="troubleshooting-guide"></a>Guide de résolution des problèmes

Ce guide contient des informations sur les problèmes courants que vous êtes susceptible de rencontrer en utilisant Azure Dev Spaces.

Si vous avez un problème lors de l’utilisation des espaces de développement Azure, créez un [problème dans le référentiel GitHub d’espaces Azure Dev](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Activation de la journalisation détaillée

Pour résoudre les problèmes plus efficacement, vous pouvez créer des journaux d’activité plus détaillés.

Pour l’extension Visual Studio, affectez 1 à la variable d’environnement `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED`. N’oubliez pas de redémarrer Visual Studio pour appliquer la variable d’environnement. Une fois activés, les journaux d’activité détaillés sont écrits dans votre répertoire `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Dans l’interface CLI, vous pouvez générer plus d’informations pendant l’exécution de la commande en utilisant le commutateur `--verbose`. Vous pouvez également parcourir des journaux d’activité plus détaillés dans `%TEMP%\Azure Dev Spaces`. Sur un Mac, vous pouvez trouver le répertoire TEMP en exécutant `echo $TMPDIR` à partir d’une fenêtre de terminal. Sur un ordinateur Linux, le répertoire TEMP est généralement `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Débogage de services avec plusieurs instances

Actuellement, Azure Dev Spaces fonctionne mieux lors du débogage d’une seule instance ou d’un seul pod. Le fichier azds.yaml contient un paramètre, *replicaCount*, qui indique le nombre de pods que Kubernetes exécute pour votre service. Si vous changez le paramètre replicaCount pour configurer votre application afin d’exécuter plusieurs pods pour un service donné, le débogueur s’attache au premier pod, quand l’ordre est alphabétique. Le débogueur s’attache à un autre pod une fois que le pod d’origine est recyclé, ce qui peut provoquer un comportement inattendu.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erreur « Échec de la création du contrôleur Azure Dev Spaces »

Vous pouvez voir cette erreur lorsqu’il y a un problème de création du contrôleur. Dans le cas d’une erreur temporaire, supprimez et recréez le contrôleur pour la corriger.

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

1. Vérifiez l’emplacement %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (Preview) pour azds.exe. S’il y figure, ajoutez cet emplacement à la variable d’environnement PATH.
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
L’extension VS Code pour C# n’est pas installée sur votre ordinateur de développement. Le C# extension inclut la prise en charge de .NET Core (CoreCLR) de débogage.

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
Ce délai d’attente se produit lorsque vous essayez d’utiliser des espaces de développement pour exécuter un service qui est configuré pour s’exécuter un [nœud virtuel AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Actuellement, Dev Spaces ne gère pas les services de build ou de débogage sur des nœuds virtuels.

Si vous exécutez `azds up` avec le commutateur `--verbose` ou si vous activez la journalisation détaillée dans Visual Studio, vous obtenez des informations supplémentaires :

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

La commande ci-dessus indique que les PODS du service a été assigné à *virtuel-nœud-aci-linux*, qui est un nœud virtuel.

### <a name="try"></a>Essayez de procéder comme suit :
Mettez à jour le graphique Helm du service pour supprimer toutes les valeurs *nodeSelector* et/ou *tolerations* qui permettent au service de s’exécuter sur un nœud virtuel. Ces valeurs sont généralement définies dans le fichier `values.yaml` du graphique.

Vous pouvez quand même utiliser un cluster AKS avec la fonctionnalité des nœuds virtuels activée, si le service que vous voulez générer/déboguer par le biais de Dev Spaces s’exécute sur un nœud de machine virtuelle. Il s’agit de la configuration par défaut.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>« Error: could not find a ready tiller pod» (Erreur : Aucun pod Tiller prêt n’a été trouvé) lors du lancement de Dev Spaces

### <a name="reason"></a>Motif
Cette erreur se produit lorsque le client Helm ne peut plus communiquer avec le pod Tiller exécuté dans le cluster.

### <a name="try"></a>Essayez de procéder comme suit :
Le fait de redémarrer les nœuds d’agent de votre cluster permet généralement de résoudre ce problème.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Le proxy Azure Dev Spaces peut interférer avec d'autres pods en cours d’exécution dans un espace de développement

### <a name="reason"></a>Motif
Lorsque vous activez Dev Spaces sur un espace de noms de votre cluster AKS, un conteneur supplémentaire appelé _mindaro-proxy_ est installé dans chaque pod en cours d'exécution dans cet espace de noms. Ce conteneur intercepte les appels aux services dans le pod, ce qui fait partie intégrante des fonctionnalités de développement en équipe de Dev Spaces. Toutefois, il peut interférer avec certains services en cours d’exécution dans ces pods. Il est connu pour interférer avec les pods exécutant Cache Azure pour Redis, ce qui crée des erreurs de connexion et des échecs en termes de communication maître/esclave.

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

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erreur « watch interne a échoué : regardez ENOSPC » lors de l’attachement du débogage pour une application Node.js

### <a name="reason"></a>Motif

Le nœud qui exécute le pod avec l’application Node.js que vous tentez d’attacher à un débogueur a dépassé le *fs.inotify.max_user_watches* valeur. Dans certains cas, [la valeur par défaut *fs.inotify.max_user_watches* est peut-être trop petite pour gérer l’attachement d’un débogueur directement à un pod](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Essai
Une solution de contournement temporaire pour ce problème consiste à augmenter la valeur de *fs.inotify.max_user_watches* sur chaque nœud du cluster et redémarrer ce nœud pour que les modifications entrent en vigueur.

## <a name="new-pods-are-not-starting"></a>Nouveaux pods ne démarrent pas

### <a name="reason"></a>Motif

L’initialiseur de Kubernetes ne peut pas appliquer le PodSpec pour les nouveaux pods en raison de modifications de l’autorisation RBAC pour le *administrateur de cluster* rôle dans le cluster. Le nouveau module peut-être également avoir un PodSpec non valide, par exemple le compte de service associé avec le pod n’existe plus. Pour voir les pods qui se trouvent dans un *en attente* état en raison du problème d’initialiseur, utilisez le `kubectl get pods` commande :

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Ce problème peut affecter les pods dans *tous les espaces de noms* dans le cluster, y compris les espaces de noms où les espaces de développement Azure n’est pas activé.

### <a name="try"></a>Essai

[La mise à jour de l’interface CLI espaces de développement vers la dernière version](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) et en supprimant le *azds InitializerConfiguration* à partir du contrôleur d’espaces de développement Azure :

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Une fois que vous avez supprimé le *azds InitializerConfiguration* à partir du contrôleur d’espaces de développement Azure, utilisez `kubectl delete` à supprimer n’importe quel nombre de pods dans un *en attente* état. Après tout, en attente de pods ont été supprimés, redéployer vos PODS sera supprimé.

Si les nouveaux pods sont toujours bloqués dans une *en attente* état après un redéploiement, utilisez `kubectl delete` à supprimer n’importe quel nombre de pods dans un *en attente* état. Après tout en attente de pods ont été supprimés, supprimer le contrôleur à partir du cluster et le réinstaller :

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Une fois que votre contrôleur est réinstallé, redéployez vos PODS sera supprimé.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Autorisations RBAC incorrectes pour l’appel d’API et contrôleur d’espaces de développement

### <a name="reason"></a>Motif
L’utilisateur l’accès au contrôleur d’espaces de développement Azure doit avoir accès en lecture de l’administrateur *kubeconfig* sur le cluster AKS. Par exemple, cette autorisation est disponible dans le [rôle d’administrateur intégré Azure Kubernetes Service Cluster](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). L’utilisateur l’accès au contrôleur d’espaces de développement Azure doit également avoir le *contributeur* ou *propriétaire* rôle RBAC pour le contrôleur.

### <a name="try"></a>Essai
Plus de détails sur la mise à jour des autorisations d’un utilisateur pour un cluster AKS [ici](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user).

Mettre à jour le rôle RBAC de l’utilisateur pour le contrôleur :

1. Connectez-vous au portail Azure sur https://portal.azure.com.
1. Accédez au groupe de ressources qui contient le contrôleur, qui est généralement le même que votre cluster AKS.
1. Activer la *afficher les types masqués* case à cocher.
1. Cliquez sur le contrôleur.
1. Ouvrez le *contrôle d’accès (IAM)* volet.
1. Cliquez sur le *attributions de rôles* onglet.
1. Cliquez sur *ajouter* puis *ajouter une attribution de rôle*.
    * Pour *rôle* sélectionnez *contributeur* ou *propriétaire*.
    * Pour *attribuer l’accès à* sélectionnez *utilisateur, groupe ou principal du service Azure AD*.
    * Pour *sélectionnez* recherche pour l’utilisateur que vous souhaitez accorder des autorisations.
1. Cliquez sur *Enregistrer*.
