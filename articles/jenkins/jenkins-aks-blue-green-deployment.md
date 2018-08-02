---
title: Déployer sur Azure Kubernetes Service (AKS) à l’aide de Jenkins et d’un modèle de déploiement bleu/vert
description: Apprendre comment déployer sur Azure Kubernetes Service (AKS) à l’aide de Jenkins et d’un modèle de déploiement bleu/vert
services: app-service\web
documentationcenter: ''
author: tomarcher
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 07/23/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 472622f78303593b7a4d5d5136aa47f34a1f44b1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227822"
---
# <a name="deploy-to-azure-kubernetes-service-aks-using-jenkins-and-bluegreen-deployment-pattern"></a>Déployer sur Azure Kubernetes Service (AKS) à l’aide de Jenkins et d’un modèle de déploiement bleu/vert

Azure Kubernetes Service (AKS) gère votre environnement Kubernetes hébergé, accélérant et facilitant ainsi le déploiement et la gestion des applications conteneurisées sans expertise d’orchestration de conteneur. AKS élimine également la charge des opérations en cours et la maintenance par configuration, la mise à niveau et la mise à l’échelle des ressources à la demande, sans déconnecter vos applications. Pour en savoir plus sur AKS, consultez la [documentation AKS](/azure/aks/).

Le déploiement bleu/vert est un modèle de livraison continue (CD) DevOps qui consiste à garder en service une version existante (bleue) pendant le déploiement d’une nouvelle version (verte). En règle générale, ce modèle fait appel à l’équilibrage de charge pour acheminer des quantités croissantes de trafic vers le déploiement vert. Si la surveillance détecte un incident, le trafic peut être redirigé vers le déploiement bleu encore en cours d’exécution. Pour en savoir plus sur la livraison continue, consultez l’article intitulé [What is Continuous Delivery](/azure/devops/what-is-continuous-delivery).

Dans ce tutoriel, vous apprendrez à effectuer les tâches suivantes en découvrant comment déployer AKS à l’aide de Jenkins et du modèle de déploiement bleu/vert :

> [!div class="checklist"]
> * En savoir plus sur le modèle de déploiement bleu/vert
> * Créer un cluster Kubernetes managé.
> * Exécuter un exemple de script permettant de configurer un cluster Kubernetes
> * Configurer manuellement un cluster Kubernetes
> * Créer et exécuter un travail Jenkins

## <a name="prerequisites"></a>Prérequis
- [Compte GitHub](https://github.com) : Vous avez besoin d’un compte GitHub pour cloner l’exemple de référentiel.
- [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) : Azure CLI 2.0 sert à créer le cluster Kubernetes.
- [Chocolatey](https://chocolatey.org) : Gestionnaire de packages utilisé pour installer kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) : Interface en ligne de commande permettant d’exécuter des commandes sur des clusters Kubernetes.
- [jq](https://stedolan.github.io/jq/download/) : Processeur JSON léger en ligne de commande.

## <a name="clone-the-sample-app-from-github"></a>Cloner l’exemple d’application de GitHub

Le référentiel Microsoft de GitHub contient un exemple d’application qui montre comment déployer AKS à l’aide de Jenkins et du modèle bleu/vert. Dans cette section, vous pourrez dupliquer et activer ce référentiel dans votre GitHub, et cloner l’application sur votre système local.

1. Accédez au référentiel GitHub pour trouver l’exemple d’application [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Exemple d’application sur le référentiel Microsoft GitHub.](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Dupliquez le référentiel en sélectionnant **Fork** en haut à droite de la page, puis en suivant les instructions permettant de dupliquer et activer le référentiel sur votre compte GitHub.

    ![Dupliquez et activez l’exemple d’application sur votre compte GitHub.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Après la duplication et activation du référentiel, le nom du compte change et une note indique l’emplacement de duplication du référentiel (Microsoft).

    ![Exemple d’application après duplication et activation sur un autre compte GitHub.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Sélectionner **Cloner ou télécharger**.

    ![GitHub permet de cloner ou de télécharger rapidement un référentiel.](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. Dans la fenêtre **Cloner avec HTTPS**, sélectionnez l’icône de copie.

    ![Copiez l’URL de clonage dans le Presse-papiers.](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Ouvrez un terminal ou une fenêtre Bash.

1. Modifiez les répertoires pour indiquer l’emplacement sur lequel vous souhaitez enregistrer la copie locale (clone) du référentiel.

1. À l’aide de la commande `git clone`, clonez l’URL précédemment copiée.

    ![Saisissez « git clone » et l’URL du clone afin de créer un clone du référentiel.](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Appuyez sur la touche &lt;Entrée> pour lancer le processus de clonage.

    ![La commande « git clone » crée une copie personnelle du référentiel dans laquelle vous pouvez effectuer des tests.](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Modifiez les répertoires pour indiquer le nouveau répertoire contenant le clone de l’application source.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Créer et configurer un cluster Kubernetes managé

Dans cette section, vous allez effectuer les étapes suivantes :

- Créer un cluster Kubernetes managé à l’aide d’Azure CLI 2.0.
- Apprenez à configurer un cluster manuellement ou à l’aide du script d’installation.
- Créer le registre de conteneurs Azure.

> [!NOTE]   
> AKS se trouve actuellement en mode préliminaire. En savoir plus sur l’activation de l’aperçu avec votre abonnement Azure. Pour en savoir plus, consultez le [Guide de démarrage rapide : déployer un cluster Azure Kubernetes Service (AKS)](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription).

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Créer un cluster Kubernetes managé à l’aide d’Azure CLI 2.0
Afin de créer un cluster Kubernetes managé avec [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), assurez-vous d’utiliser la version 2.0.25 ou une version ultérieure d’Azure CLI.

1. Connectez-vous à votre compte Azure. Après avoir saisi la commande `az login` suivante, des instructions expliquent comment remplir le formulaire de connexion. 
    
    ```bash
    az login
    ```

1. Pendant l’exécution de la commande `az login` à l’étape précédente, une liste de tous vos abonnements Azure s’affiche (avec leur ID d’abonnement). Au cours de cette étape, vous pourrez définir l’abonnement Azure par défaut. Remplacez l’espace réservé &lt;your-subscription-id> par l’identifiant d’abonnement Azure de votre choix. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Créez un groupe de ressources. Remplacez l’espace réservé &lt;your-subscription-id> par le nom de votre nouveau groupe de ressources, puis remplacez l’espace réservé &lt;your-location> par l’emplacement. La commande `az account list-locations` affiche tous les emplacements Azure. Tous les emplacements ne sont pas disponibles dans la préversion d’AKS. Si vous indiquez un emplacement non valide à ce moment-là, le message d’erreur affichera la liste des emplacements disponibles.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Créer un cluster Kubernetes. Remplacez l’espace réservé &lt;your-resource-group-name> par le nom du groupe de ressources créé à l’étape précédente, puis remplacez l’espace réservé &lt;you-kubernetes-cluster-name> par le nom de votre nouveau cluster. (ce processus peut prendre plusieurs minutes)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Configurer le cluster Kubernetes

La configuration d’un déploiement bleu/vert dans AKS peut s’effectuer manuellement ou à l’aide d’un script de configuration fourni dans l’exemple précédemment cloné. Dans cette section, les deux méthodes vous sont présentées.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Configurer le cluster Kubernetes à l’aide de l’exemple de script de configuration.
1. Modifiez le fichier **deploy/aks/setup/setup.sh** en remplaçant les espaces réservés suivants par les valeurs qui correspondent à votre environnement : 

    - **&lt;your-resource-group-name>**
    - **&lt;your-kubernetes-cluster-name>**
    - **&lt;your-location>**
    - **&lt;your-dns-name-suffix>**

    ![Le script setup.sh contient plusieurs espaces réservés pouvant être modifiés en fonction de votre environnement.](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Exécuter le script d’installation.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Configurer manuellement un cluster Kubernetes 
1. Télécharger la configuration de Kubernetes dans votre dossier de profil.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Passer le répertoire à **deploy/aks/setup**. 

1. Exécutez les commandes **kubectl** suivantes afin de configurer les services pour le point d’extrémité public et les deux points de terminaison de test.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Mettre à jour le nom DNS pour le public et tester les points de terminaison. Lors de la création d’un cluster Kubernetes, un [groupe de ressources supplémentaire](https://github.com/Azure/AKS/issues/3) est créé avec le modèle de nommage de **MC_&lt;your-resource-group-name>_&lt;your-kubernetes-cluster-name>_&lt;your-location>**.

    Localiser les adresses IP publiques du groupe de ressources

    ![Adresse IP publique du groupe de ressources](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Pour chaque service, recherchez l’adresse IP externe en exécutant la commande suivante :
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Mettez à jour le nom DNS de l’adresse IP correspondante avec la commande suivante :

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Renouvelez l’appel de `todoapp-test-blue` et `todoapp-test-green` :

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Votre abonnement ne doit comporter qu’un seul nom DNS. Il est possible d’utiliser `<your-dns-name-suffix>` pour s’assurer qu’il est unique.

### <a name="create-azure-container-registry"></a>Créer un registre de conteneurs Azure

1. Exécutez la commande `az acr create` pour créer un registre de conteneurs Azure. Après avoir créé le registre de conteneurs Azure, utilisez `login server` comme URL du registre Docker dans la section suivante.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Exécutez la commande `az acr credential` pour afficher vos informations d’identification du registre de conteneurs Azure. Notez le nom d’utilisateur et le mot de passe du registre Docker, car ils serviront dans la section suivante.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Préparer le serveur Jenkins

Dans cette section, vous pourrez voir comment préparer le serveur Jenkins pour exécuter des builds, ce qui est parfait pour effectuer des tests. Cependant, comme expliqué dans l’article Jenkins sur les [conséquences sécuritaires de la compilation sur maître](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master), il est conseillé d’utiliser un [agent Azure VM](https://plugins.jenkins.io/azure-vm-agents) ou un agent [Azure Container](https://plugins.jenkins.io/azure-container-agents) permettant de faire fonctionner un agent dans Azure pour exécuter vos builds. 

1. Déployer un [maître Jenkins sur Azure](https://aka.ms/jenkins-on-azure).

1. Connectez-vous au serveur via le protocole SSH et installez les outils de compilation sur le serveur sur lequel vous exécutez votre compilation.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Installation de Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Assurez-vous que l’utilisateur `jenkins` a la permission d’exécuter les commandes `docker`.

1. [Installer kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Télécharger jq](https://stedolan.github.io/jq/download/).

1. Installez jq avec la commande suivante :

   ```bash
   sudo apt-get install jq
   ```
   
1. Installez les plugins dans Jenkins en suivant les étapes suivantes dans le tableau de bord Jenkins :

    1. Sélectionnez **Manage Jenkins (Gérer Jenkins) > Manage Plugins (Gérer les plug-ins) > Available (Disponible)**.
    1. Recherchez et installez le plug-in Azure Container Service.

1. Vous devez indiquer les informations d’identification qui serviront à la gestion des ressources dans Azure. Si vous ne disposez pas déjà du plug-in, installez le plug-in **Azure Credential (Informations d’identification)**.

1. Ajoutez vos informations d’identification Azure Service Principal avec le type **Microsoft Azure Service Principal**.

1. Ajoutez votre nom d’utilisateur et votre mot de passe Azure Docker Registry (tel qu’obtenu dans la section **Créer un registre de conteneurs Azure**) avec le type **Username with password (Nom d’utilisateur et mot de passe)**.

## <a name="edit-the-jenkinsfile"></a>Modifier le fichier Jenkins

1. Dans votre propre référentiel, accédez à `/deploy/aks/` et ouvrez `Jenkinsfile`.

2. Mettez à jour le fichier comme suit :

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Mettez à jour l’ID des informations d’identification de l’ACR :
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Créer le travail
1. Ajouter un nouveau travail avec le type **Pipeline**.

1. Sélectionnez **Pipeline > Définition > Script de pipeline à partir de SCM**.

1. Saisissez l’url du référentiel SCM avec votre &lt;your-forked-repo>

1. Saisissez le chemin du script en tant que `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Exécution de la tâche

1. Vérifiez que vous pouvez exécuter votre projet dans votre environnement local. [Exécuter le projet sur l’ordinateur local](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it)

1. Exécuter le travail Jenkins. La première fois que le travail Jenkins est exécuté, l’application todo est déployée dans l’environnement bleu qui constitue l’environnement inactif par défaut. 

1. Pour vérifier que le travail s’est bien exécuté, accédez aux urls :
    - Point de terminaison public :`http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Point de terminaison bleu : `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Point de terminaison vert : `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Les points de terminaison de test public et bleu reçoivent la même mise à jour tandis que le point de terminaison vert montre l’image tomcat par défaut. 

Si vous exécutez le build plusieurs fois, il passe par les déploiements bleu et vert. En d’autres termes, si l’environnement actuel est bleu, le travail déploiera/testera vers l’environnement vert, puis mettra à jour le point de terminaison public de l’application en vue d’acheminer le trafic vers l’environnement vert si les tests sont positifs.

## <a name="additional-information"></a>Informations supplémentaires

Pour en savoir plus sur le déploiement sans interruption de service, consultez ce [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources Azure créées au cours de ce tutoriel.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous constatez des bogues dans les plug-ins Jenkins, enregistrez un problème dans le [Jenkins JIRA](https://issues.jenkins-ci.org/) du composant en question.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris comment effectuer un déploiement vers Azure Kubernetes Service (AKS) à l’aide de Jenkins et d’un modèle de déploiement bleu/vert. Pour en savoir plus sur le fournisseur Azure Jenkins, consultez le site Jenkins sur Azure.

> [!div class="nextstepaction"]
> [Jenkins sur Azure](/azure/jenkins/)