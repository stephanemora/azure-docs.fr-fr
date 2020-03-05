---
title: 'Tutoriel : Effectuer un déploiement sur Azure Kubernetes Service (AKS) à partir de GitHub avec Jenkins'
description: Configurer Jenkins pour l’intégration continue (CI) dans GitHub et le déploiement continu (CD) sur Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: eb48a8558aab6c7a832efe45650686d9df0d7426
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624504"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Tutoriel : Effectuer un déploiement sur Azure Kubernetes Service (AKS) à partir de GitHub avec l’intégration continue et le déploiement continu Jenkins

Ce tutoriel explique comment déployer un exemple d’application de GitHub sur un cluster [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) en configurant l’intégration continue (CI) et le déploiement continu (CD) dans Jenkins. De cette façon, quand vous mettez à jour l’application en envoyant (push) des validations à GitHub, Jenkins exécute automatiquement un nouveau build de conteneur, envoie (push) les images conteneurs à Azure Container Registry (ACR), puis exécute votre application dans AKS. 

Dans ce tutoriel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Déployer un exemple d’application de vote Azure sur un cluster AKS
> * Créer un projet de base Jenkins
> * Configurer les informations d’identification pour Jenkins afin d’interagir avec ACR
> * Créer un travail de build Jenkins et un webhook GitHub pour les builds automatisées
> * Tester le pipeline CI/CD pour mettre à jour une application dans AKS en fonction des validations de code GitHub

## <a name="prerequisites"></a>Prérequis

Pour les besoins de ce tutoriel, vous devez disposer des éléments suivants :

- Notions de base de Kubernetes, Git, CI/CD et images conteneur

- Un [cluster AKS](../aks/kubernetes-walkthrough.md) et `kubectl` configuré avec les [informations d’identification du cluster AKS](/cli/azure/aks#az-aks-get-credentials).

- Un [registre Azure Container Registry (ACR)](../container-registry/container-registry-get-started-azure-cli.md), le nom du serveur de connexion ACR et le cluster AKS configuré pour [s’authentifier auprès du registre ACR](../aks/cluster-container-registry-integration.md).

- Azure CLI version 2.0.46 ou ultérieure installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI](/cli/azure/install-azure-cli).

- [Docker installé](https://docs.docker.com/install/) sur votre système de développement

- Un compte GitHub, un [jeton d’accès personnel GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) et un client Git installé sur votre système de développement

- Si vous fournissez votre propre instance Jenkins à la place de cet exemple de façon scriptée de déployer Jenkins, votre instance Jenkins a besoin de [Docker installé et configuré](https://docs.docker.com/install/) et de [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## <a name="prepare-your-app"></a>Préparation de votre application

Dans cet article, vous utilisez un exemple d’application de vote Azure qui contient une interface web hébergée dans un pod ou plus, un second pod qui héberge Redis pour du stockage de données temporaires. Avant d’intégrer Jenkins et AKS pour les déploiements automatisés, préparez et déployez d’abord manuellement l’application de vote Azure sur votre cluster AKS. Ce déploiement manuel est la version une de l’application, et vous permet de voir l’application en action.

> [!NOTE]
> L’exemple d’application de vote Azure utilise un pod Linux programmé pour s’exécuter sur un nœud Linux. Le flux décrit dans cet article fonctionne également pour un pod Windows Server programmé sur un nœud Windows Server.

Dupliquez le référentiel GitHub suivant pour l’exemple d’application : [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Pour répliquer le référentiel dans votre propre compte GitHub, cliquez sur le bouton **Bifurcation** en haut à droite.

Clonez la duplication sur votre système de développement. Assurez-vous d’utiliser l’URL de votre branche lorsque vous clonez le référentiel :

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Accédez au répertoire de votre duplication clonée :

```console
cd azure-voting-app-redis
```

Pour créer les images conteneur nécessaires à l’exemple d’application, utilisez le fichier *docker-compose.yaml* avec `docker-compose` :

```console
docker-compose up -d
```

Les images de base nécessaires sont extraites et les conteneurs d’applications sont générés. Vous pouvez ensuite utiliser la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/) pour afficher l’image créée. Trois images ont été téléchargées ou créées. L’image `azure-vote-front` contient l’application et utilise l’image `nginx-flask` comme base. L’image `redis` est utilisée pour démarrer une instance Redis :

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Avant d’envoyer l’image conteneur *azure-vote-front* à ACR, obtenez votre serveur de connexion ACR avec la commande [az acr list](/cli/azure/acr#az-acr-list). L’exemple suivant obtient l’adresse du serveur de connexion ACR pour un registre dans le groupe de ressources nommé *myResourceGroup* :

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilisez la commande [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) pour baliser l’image avec le nom du serveur de connexion ACR et le nombre de version `v1`. Fournissez votre propre nom `<acrLoginServer>` obtenu à l’étape précédente :

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Enfin, envoyez l’image *azure-vote-front* à votre registre ACR. Là encore, remplacez `<acrLoginServer>` par le nom du serveur de connexion de votre propre registre ACR, tel que `myacrregistry.azurecr.io` :

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Déployer l’exemple d’application sur AKS

Pour déployer l’exemple d’application sur votre cluster AKS, vous pouvez utiliser le fichier manifeste Kubernetes à la racine du référentiel de vote Azure. Ouvrez le fichier manifeste *azure-vote-all-in-one-redis.yaml* avec un éditeur tel que `vi`. Remplacez `microsoft` par le nom de votre serveur de connexion ACR. Cette valeur se trouve sur la ligne **47** du fichier manifeste :

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Utilisez ensuite la commande [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) pour déployer l’application sur votre cluster AKS :

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Un service d’équilibreur de charge Kubernetes est créé pour exposer l’application sur Internet. Ce processus peut prendre plusieurs minutes. Pour surveiller la progression du déploiement de l’équilibreur de charge, utilisez la commande [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) avec l’argument `--watch`. Une fois que l’adresse *EXTERNAL-IP* est passée du statut *En attente* à *Adresse IP*, utilisez `Control + C` pour arrêter le processus de surveillance kubectl.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Pour voir l’application en action, ouvrez un navigateur web en utilisant l’adresse IP externe de votre service. L’application de vote Azure apparaît telle que dans l’exemple suivant :

![Exemple d’application de vote Azure en cours d’exécution dans AKS](media/jenkins-continuous-deployment/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Déployer Jenkins sur une machine virtuelle Azure

Pour déployer rapidement Jenkins à utiliser dans cet article, vous pouvez utiliser le script suivant pour déployer une machine virtuelle Azure, configurer l’accès réseau et effectuer une installation de base de Jenkins. Pour l’authentification entre Jenkins et le cluster AKS, le script copie votre fichier de configuration Kubernetes de votre système de développement dans le système Jenkins.

> [!WARNING]
> Cet exemple de script est fourni a des fins de démonstration afin d’approvisionner rapidement un environnement Jenkins qui s’exécute sur une machine virtuelle Azure. Il utilise l’extension de script personnalisé Azure pour configurer une machine virtuelle, puis affiche les informations d’identification requises. Votre *~/.kube/config* est copié vers la machine virtuelle Jenkins.

Exécutez les commandes suivantes pour télécharger et exécuter le script. Vous devez vérifier le contenu d’un script avant de l’exécuter : [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

La création de la machine virtuelle et le déploiement des composants requis pour Docker et Jenkins ne prennent que quelques minutes. Une fois le script terminé, il renvoie une adresse pour le serveur Jenkins et une clé pour déverrouiller le tableau de bord, comme indiqué dans l’exemple de sortie suivant :

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Ouvrez un navigateur web à l’URL affichée et entrez la clé de déverrouillage. Suivez les invites à l’écran pour terminer la configuration de Jenkins :

- Choisissez **Install suggested plugins** (Installer les plug-ins suggérés)
- Créez le premier utilisateur administrateur. Entrez un nom d’utilisateur, tel que *azureuser*, puis fournissez votre propre mot de passe sécurisé. Enfin, tapez un nom complet et une adresse e-mail.
- Sélectionnez **Save and Finish** (Enregistrer et terminer).
- Une fois que Jenkins est prêt, sélectionnez **Start using Jenkins** (Commencer à utiliser Jenkins).
    - Si votre navigateur web affiche une page vierge lorsque vous commencez à utiliser Jenkins, redémarrez le service Jenkins. Pour redémarrer le service, activez SSH sur l’adresse IP publique de votre instance Jenkins et tapez `sudo service jenkins restart`. Une fois que le service a redémarré, actualisez votre navigateur web.
- Connectez-vous à Jenkins avec le nom d’utilisateur et le mot de passe créés dans le processus d’installation.

## <a name="create-a-jenkins-environment-variable"></a>Créer une variable d’environnement Jenkins

Une variable d’environnement Jenkins est utilisée pour contenir le nom du serveur de connexion ACR. Cette variable est référencée lors du travail de build Jenkins. Pour créer cette variable d’environnement, procédez comme suit :

- À gauche du portail Jenkins, sélectionnez **Gérer Jenkins** > **Configurer le système**
- Sous **Propriétés globales**, sélectionnez **Variables d’environnement**. Ajoutez une variable nommée `ACR_LOGINSERVER` et la valeur de votre serveur de connexion ACR.

    ![Variables d’environnement Jenkins](media/jenkins-continuous-deployment/env-variables.png)

- Lorsque vous avez terminé, cliquez sur **Enregistrer** au bas de la page de configuration Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Créer des informations d’identification Jenkins pour ACR

Pour autoriser Jenkins à générer puis envoyer les images conteneur mise à jour à ACR, vous devez spécifier les informations d’identification pour ACR. Cette authentification peut utiliser des principaux du service Azure Active Directory. Dans les prérequis, vous avez configuré le principal du service pour votre cluster AKS avec des autorisations *Lecteur* sur votre registre ACR. Ces autorisations permettent au cluster AKS d’*extraire* des images du registre ACR. Pendant le processus CI/CD, Jenkins génère de nouvelles images conteneur basées sur les mises à jour de l’application. Il doit ensuite *envoyer* ces images au registre ACR. Pour la séparation des rôles et des autorisations, configurez maintenant un principal du service pour Jenkins avec des autorisations *Contributeur* sur votre registre ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Créer un principal du service pour que Jenkins puisse utiliser ACR

Créez tout d’abord un principal du service à l’aide de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) :

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Prenez note des valeurs de *appId* et de *password* indiquées dans la sortie. Ces valeurs sont utilisées dans les étapes suivantes pour configurer la ressource d’informations d’identification dans Jenkins.

Obtenez l’ID de ressource de votre registre ACR à l’aide de la commande [az acr show](/cli/azure/acr#az-acr-show) et stockez-le en tant que variable. Indiquez le nom de votre groupe de ressources et le nom de l’ACR :

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Créez maintenant une attribution de rôle pour assigner au principal du service des droits *Contributeur* sur le registre ACR. Dans l’exemple suivant, fournissez votre propre *appId* indiqué dans la sortie d’une commande précédente pour créer le principal du service :

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Créer une ressource d’informations d’identification dans Jenkins pour le principal du service ACR

Avec l’attribution de rôle créée dans Azure, stockez maintenant vos informations d’identification ACR dans un objet d’identification Jenkins. Ces informations d’identification sont référencées lors de la tâche de génération Jenkins.

De retour à gauche du portail Jenkins, cliquez sur **Informations d’identification** > **Jenkins** > **Informations d’identification globales (sans restriction)**  > **Ajouter des informations d’identification**

Assurez-vous que le genre d’informations d’identification correspond à **Mot de passe avec nom d’utilisateur** puis entrez les informations suivantes :

- **Nom d’utilisateur** : *appId* du principal de service créé pour s’authentifier auprès de votre registre ACR.
- **Mot de passe** : *password* du principal de service créé pour s’authentifier auprès de votre registre ACR.
- **ID** : identificateur d’informations d’identification, tel que *acr-credentials*

Lorsque vous avez terminé, le formulaire d’informations d’identification ressemble à l’exemple suivant :

![Créer un objet d’informations d’identification Jenkins avec les informations du principal du service](media/jenkins-continuous-deployment/acr-credentials.png)

Cliquez sur **OK** et retournez sur le portail Jenkins.

## <a name="create-a-jenkins-project"></a>Créer un projet Jenkins

Dans la page d’accueil de votre portail Jenkins, sélectionnez **Nouvel élément** à gauche :

1. Entrez *azure-vote* comme nom du travail. Choisissez **Projet libre**, puis sélectionnez **OK**
1. Dans la section **Général**, sélectionnez **GitHub project** (Projet GitHub), puis entrez l’URL de votre référentiel dupliqué, par exemple *https:\//github.com/\<your-github-account\>/azure-voting-app-redis*
1. Dans la section **Gestion du code source**, sélectionnez **Git**, puis entrez l’URL *.git* de votre référentiel dupliqué, par exemple *https:\//github.com/\<your-github-account\>/azure-voting-app-redis.git*

1. Dans la section **Déclencheurs de génération**, sélectionnez **Déclencher un hook GitHub pour l’interrogation GITScm**
1. Sous **Environnement de build**, sélectionnez **Utiliser des textes ou fichiers secrets**
1. Sous **Liaisons**, sélectionnez **Ajouter** > **Nom d’utilisateur et mot de passe (distincts)**
   - Entrez `ACR_ID` comme **Variable de nom d’utilisateur**, et `ACR_PASSWORD` comme **Variable de mot de passe**

     ![Liaisons Jenkins](media/jenkins-continuous-deployment/bindings.png)

1. Choisissez d’ajouter une **Étape de génération** de type **Exécuter un script shell** et utilisez le texte suivant. Ce script génère une image conteneur et l’envoie dans votre registre ACR.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Ajoutez une autre **Étape de génération** de type **Exécuter un script shell** et utilisez le texte suivant. Ce script met à jour le déploiement d’application dans AKS avec la nouvelle image conteneur d’ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Une fois terminé, cliquez sur **Enregistrer**.

## <a name="test-the-jenkins-build"></a>Tester la build Jenkins

Avant d’automatiser le travail en fonction de validations GitHub, testez d’abord manuellement la build Jenkins. Cette build manuelle confirme que le travail a été correctement configuré, que le bon fichier d’authentification Kubernetes est en place et que l’authentification auprès d’ACR fonctionne.

Dans le menu de gauche du projet, sélectionnez **Générer maintenant**.

![Build de test Jenkins](media/jenkins-continuous-deployment/test-build.png)

La première build prend une minute ou deux car les couches d’image Docker sont extraites dans le serveur Jenkins. Les builds suivantes peuvent utiliser les couches d’image mises en cache pour améliorer les temps de génération.

Lors du processus de génération, le référentiel GitHub est cloné dans le serveur de génération Jenkins. Une image conteneur est générée et envoyée dans le registre ACR. Enfin, l’application de vote Azure exécutée sur le cluster AKS est mise à jour afin d’utiliser la nouvelle image. L’application n’est pas différente car aucun changement n’a été effectué au niveau du code de l’application si vous affichez l’exemple d’application dans un navigateur web.

Une fois le travail de build terminé, cliquez sur **build #1** sous l’historique de générations. Sélectionnez **Sortie de console** et affichez la sortie du processus de génération. La dernière ligne doit indiquer que la génération a été un succès.

## <a name="create-a-github-webhook"></a>Créer un webhook GitHub

Lorsque la build manuelle est terminée, intégrez GitHub à la build Jenkins. Un webhook peut être utilisé pour exécuter le travail de build Jenkins chaque fois qu’une validation de code est effectuée dans GitHub. Pour créer le webhook GitHub, procédez comme suit :

1. Accédez à votre référentiel GitHub dupliqué dans un navigateur web.
1. Sélectionnez **Paramètres**, puis **Webhooks** sur le côté gauche.
1. Choisissez d’**Ajouter un webhook**. Pour la *Charge utile de l’URL*, entrez `http://<publicIp:8080>/github-webhook/`, où `<publicIp>` correspond à l’adresse IP du serveur Jenkins. Assurez-vous d’inclure la barre oblique (/) à la fin. Laissez les autres paramètres par défaut pour le type de contenu et pour le déclencheur d'événements *Push*.
1. Sélectionnez **Ajouter un webhook**.

    ![Créer un webhook GitHub pour Jenkins](media/jenkins-continuous-deployment/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Test l’ensemble du pipeline CI/CD

Vous pouvez maintenant tester l’ensemble du pipeline CI/CD. Lorsque vous envoyez une validation de code à GitHub, les étapes suivantes se produisent :

1. Le webhook GitHub contacte Jenkins.
1. Jenkins démarre le travail de build et extrait la dernière validation de code de GitHub.
1. Une build Docker est démarrée en utilisant le code mis à jour, et la nouvelle image conteneur est marquée avec le dernier numéro de build.
1. Cette nouvelle image conteneur est envoyée à Azure Container Registry.
1. Votre application déployée sur Azure Kubernetes Service est mise à jour avec la dernière image conteneur du registre Azure Container Registry.

Sur votre machine de développement, ouvrez l’application clonée avec un éditeur de code. Sous le répertoire */azure-vote/azure-vote*, ouvrez le fichier nommé **config_file.cfg**. Mettez à jour les valeurs de vote dans ce fichier en quelque chose différent de chats et chiens, comme indiqué dans l’exemple suivant :

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Une fois la mise à jour terminée, enregistrez le fichier, validez les modifications et envoyez-les à la duplication de votre référentiel GitHub. Le webhook GitHub déclenche un nouveau travail de build dans Jenkins. Dans le tableau de bord Jenkins, surveillez le processus de génération. Il ne faut que quelques secondes pour extraire le dernier code, créer et envoyer l’image mise à jour, et pour déployer l’application mise à jour sur AKS.

Une fois la build terminée, actualisez votre navigateur web de l’exemple d’application de vote Azure. Vos modifications apparaissent, comme dans l’exemple suivant :

![Exemple de vote Azure dans AKS mis à jour par le travail de build Jenkins](media/jenkins-continuous-deployment/azure-vote-updated.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Jenkins sur Azure](/azure/jenkins)