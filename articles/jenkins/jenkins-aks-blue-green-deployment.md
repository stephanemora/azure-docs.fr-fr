---
title: Déployer sur Azure Kubernetes Service à l’aide de Jenkins et du modèle de déploiement bleu/vert
description: Découvrez comment déployer sur Azure Kubernetes Service (AKS) à l’aide de Jenkins et du modèle de déploiement bleu/vert.
keywords: jenkins, azure, devops, kubernetes, k8s, aks, blue green deployment, continuous delivery, cd
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 9d6551f910bd99322f844b44130ebb03732df83c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251478"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Déployer sur Azure Kubernetes Service (AKS) à l’aide de Jenkins et du modèle de déploiement bleu/vert

Azure Kubernetes Service (AKS) gère votre environnement Kubernetes hébergé, ce qui vous permet de déployer et de gérer de manière simple et rapide des applications en conteneur. Vous n’avez pas besoin de maîtriser l’orchestration de conteneurs. AKS élimine également la charge des opérations en cours et la maintenance par le provisionnement, la mise à niveau et la mise à l’échelle des ressources à la demande. Vous n’avez pas besoin de déconnecter vos applications. Pour en savoir plus sur AKS, consultez la [documentation AKS](/azure/aks/).

Le déploiement bleu/vert est un modèle de livraison continue Azure DevOps qui consiste à garder en service une version existante (bleue) pendant le déploiement d’une nouvelle version (verte). En règle générale, ce modèle fait appel à l’équilibrage de charge pour acheminer des quantités croissantes de trafic vers le déploiement vert. Si la surveillance détecte un incident, le trafic peut être redirigé vers le déploiement bleu, qui est encore en cours d’exécution. Pour en savoir plus sur la livraison continue, consultez [Présentation de la livraison continue](/azure/devops/what-is-continuous-delivery).

Dans ce tutoriel, vous allez apprendre à effectuer les tâches suivantes :

> [!div class="checklist"]
> * En savoir plus sur le modèle de déploiement bleu/vert
> * Créer un cluster Kubernetes managé
> * Exécuter un exemple de script permettant de configurer un cluster Kubernetes
> * Configurer manuellement un cluster Kubernetes
> * Créer et exécuter un travail Jenkins

## <a name="prerequisites"></a>Conditions préalables requises
- [Compte GitHub](https://github.com) : Vous avez besoin d’un compte GitHub pour cloner l’exemple de référentiel.
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) : vous utilisez Azure CLI 2.0 pour créer le cluster Kubernetes.
- [Chocolatey](https://chocolatey.org) : gestionnaire de packages que vous utilisez pour installer kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) : interface de ligne de commande que vous utilisez pour exécuter des commandes sur des clusters Kubernetes.
- [jq](https://stedolan.github.io/jq/download/) : processeur JSON léger en ligne de commande.

## <a name="clone-the-sample-app-from-github"></a>Cloner l’exemple d’application de GitHub

Sur le dépôt Microsoft de GitHub, vous trouverez un exemple d’application qui montre comment déployer AKS à l’aide de Jenkins et du modèle bleu/vert. Dans cette section, vous pourrez dupliquer et activer ce référentiel dans votre GitHub, et cloner l’application sur votre système local.

1. Accédez au référentiel GitHub pour trouver l’exemple d’application [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Capture d’écran de l’exemple d’application sur le dépôt Microsoft GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Dupliquez le référentiel en sélectionnant **Fork** en haut à droite de la page, puis en suivant les instructions permettant de dupliquer et activer le référentiel sur votre compte GitHub.

    ![Capture d’écran de l’option GitHub permettant d’effectuer une duplication (fork)](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Après la duplication (fork) du dépôt, le nom du compte change au profit de votre nom de compte et une note indique l’emplacement de duplication du dépôt (Microsoft).

    ![Capture d’écran du nom de compte et de la note dans GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Sélectionnez **Cloner ou télécharger**.

    ![Capture d’écran de l’option GitHub permettant de cloner ou télécharger un dépôt](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. Dans la fenêtre **Cloner avec HTTPS**, sélectionnez l’icône de **copie**.

    ![Capture d’écran de l’option GitHub permettant de copier l’URL à cloner dans le Presse-papiers](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Ouvrez un terminal ou une fenêtre Git Bash.

1. Modifiez les répertoires pour indiquer l’emplacement sur lequel vous souhaitez enregistrer la copie locale (clone) du référentiel.

1. À l’aide de la commande `git clone`, clonez l’URL précédemment copiée.

    ![Capture d’écran de la commande git clone Git Bash](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Appuyez sur la touche Entrée pour lancer le processus de clonage.

    ![Capture d’écran du résultat de la commande git clone Git Bash](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Modifiez les répertoires pour indiquer le nouveau répertoire contenant le clone de l’application source.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Créer et configurer un cluster Kubernetes managé

Dans cette section, vous allez effectuer les étapes suivantes :

- Créer un cluster Kubernetes managé à l’aide d’Azure CLI 2.0.
- Apprendre à configurer un cluster manuellement ou à l’aide du script d’installation.
- Créer une instance du service Azure Container Registry.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Créer un cluster Kubernetes managé à l’aide d’Azure CLI 2.0
Afin de créer un cluster Kubernetes managé avec [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), veillez à utiliser la version 2.0.25 ou ultérieure d’Azure CLI.

1. Connectez-vous à votre compte Azure. Après avoir entré la commande suivante, vous recevez des instructions qui expliquent comment effectuer la connexion. 
    
    ```azurecli
    az login
    ```

1. Pendant l’exécution de la commande `az login` à l’étape précédente, une liste de tous vos abonnements Azure apparaît (avec leur ID d’abonnement). Au cours de cette étape, vous pourrez définir l’abonnement Azure par défaut. Remplacez l’espace réservé &lt;your-subscription-id> par l’identifiant d’abonnement Azure de votre choix. 

    ```azurecli
    az account set -s <your-subscription-id>
    ```

1. Créez un groupe de ressources. Remplacez l’espace réservé &lt;your-subscription-id> par le nom de votre nouveau groupe de ressources, puis remplacez l’espace réservé &lt;your-location> par l’emplacement. La commande `az account list-locations` affiche tous les emplacements Azure. Tous les emplacements ne sont pas disponibles dans la préversion d’AKS. Si vous indiquez un emplacement non valide à ce moment-là, le message d’erreur affiche la liste des emplacements disponibles.

    ```azurecli
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Créer un cluster Kubernetes. Remplacez l’espace réservé &lt;your-resource-group-name> par le nom du groupe de ressources créé à l’étape précédente, puis remplacez l’espace réservé &lt;your-kubernetes-cluster-name> par le nom de votre nouveau cluster. (ce processus peut prendre plusieurs minutes)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Configurer le cluster Kubernetes

Vous pouvez configurer un déploiement bleu/vert dans AKS manuellement ou à l’aide d’un script de configuration fourni dans l’exemple précédemment cloné. Dans cette section, les deux méthodes vous sont présentées.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Configurer le cluster Kubernetes à l’aide de l’exemple de script de configuration.
1. Modifiez le fichier **deploy/aks/setup/setup.sh** en remplaçant les espaces réservés suivants par les valeurs qui correspondent à votre environnement : 

   - **&lt;your-resource-group-name>**
   - **&lt;your-kubernetes-cluster-name>**
   - **&lt;your-location>**
   - **&lt;your-dns-name-suffix>**

     ![Capture d’écran du script setup.sh dans bash, avec plusieurs espaces réservés mis en valeur](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Exécuter le script d’installation.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Configurer manuellement un cluster Kubernetes 
1. Télécharger la configuration de Kubernetes dans votre dossier de profil.

    ```azurecli
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Accédez au répertoire **deploy/aks/setup**. 

1. Exécutez les commandes **kubectl** suivantes afin de configurer les services pour le point d’extrémité public et les deux points de terminaison de test.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Mettre à jour le nom DNS pour le public et tester les points de terminaison. Quand vous créez un cluster Kubernetes, vous créez également un [groupe de ressources supplémentaire](https://github.com/Azure/AKS/issues/3), avec le modèle de nommage de **MC_&lt;your-resource-group-name> _&lt;your-kubernetes-cluster-name>_ &lt;your-location>** .

    Localisez les adresses IP publiques du groupe de ressources.

    ![Capture d’écran des adresses IP publiques dans le groupe de ressources](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Pour chaque service, recherchez l’adresse IP externe en exécutant la commande suivante :
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Mettez à jour le nom DNS de l’adresse IP correspondante avec la commande suivante :

    ```azurecli
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Renouvelez l’appel de `todoapp-test-blue` et `todoapp-test-green` :

    ```azurecli
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Votre abonnement ne doit comporter qu’un seul nom DNS. Pour garantir l’unicité, vous pouvez utiliser `<your-dns-name-suffix>`.

### <a name="create-an-instance-of-container-registry"></a>Créer une instance de Container Registry

1. Exécutez la commande `az acr create` pour créer une instance de Container Registry. Dans la section suivante, vous pouvez ensuite utiliser `login server` en guise d’URL du registre Docker.

    ```azurecli
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Exécutez la commande `az acr credential` pour afficher vos informations d’identification Container Registry. Notez le nom d’utilisateur et le mot de passe du registre Docker, car ils vous serviront dans la section suivante.

    ```azurecli
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Préparer le serveur Jenkins

Dans cette section, vous pourrez voir comment préparer le serveur Jenkins pour exécuter des builds, ce qui est parfait pour effectuer des tests. Toutefois, vous devez utiliser un [agent de machine virtuelle Azure](https://plugins.jenkins.io/azure-vm-agents) ou un [agent de conteneur Azure](https://plugins.jenkins.io/azure-container-agents) pour mettre en place un agent dans Azure afin d’exécuter vos builds. Pour plus d’informations, consultez l’article Jenkins sur les [implications en matière de sécurité de la génération sur le nœud master](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

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

    1. Sélectionnez **Manage Jenkins (Gérer Jenkins) > Manage Plugins (Gérer les plug-ins) > Available (Disponible)** .
    1. Recherchez et installez le plug-in Azure Container Service.

1. Ajoutez les informations d’identification pour gérer les ressources dans Azure. Si vous ne disposez pas déjà du plug-in, installez le plug-in **Informations d’identification Azure**.

1. Ajoutez vos informations d’identification Azure Service Principal avec le type **Microsoft Azure Service Principal**.

1. Ajoutez votre nom d’utilisateur et votre mot de passe de registre Azure Docker (tel qu’obtenu dans la section « Créer une instance de Container Registry ») avec le type **Username with password (Nom d’utilisateur et mot de passe)** .

## <a name="edit-the-jenkinsfile"></a>Modifier le fichier Jenkins

1. Dans votre propre dépôt, accédez à `/deploy/aks/`, puis ouvrez `Jenkinsfile`.

2. Mettez le fichier à jour comme suit :

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Mettez à jour l’ID des informations d’identification Container Registry :
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Créer le travail
1. Ajouter un nouveau travail avec le type **Pipeline**.

1. Sélectionnez **Pipeline** > **Définition** > **Script de pipeline à partir de SCM**.

1. Entrez l’URL du dépôt SCM avec votre &lt;your-forked-repo>.

1. Saisissez le chemin du script en tant que `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Exécuter le travail

1. Vérifiez que vous pouvez exécuter votre projet dans votre environnement local. Voici comment : [Exécuter le projet sur l’ordinateur local](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Exécuter le travail Jenkins. La première fois que vous exécutez le travail, Jenkins déploie l’application todo sur l’environnement bleu, qui constitue l’environnement inactif par défaut. 

1. Pour vérifier que le travail s’est exécuté, accédez à ces URL :
    - Point de terminaison public :`http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Point de terminaison bleu : `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Point de terminaison vert : `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Les points de terminaison de test public et bleu reçoivent la même mise à jour tandis que le point de terminaison vert montre l’image tomcat par défaut. 

Si vous exécutez la build plusieurs fois, elle passe par les déploiements bleu et vert. En d’autres termes, si l’environnement actuel est bleu, le travail effectue le déploiement et les tests sur l’environnement vert. Ensuite, si les tests sont corrects, le travail met à jour le point de terminaison public de l’application pour acheminer le trafic vers l’environnement vert.

## <a name="additional-information"></a>Informations supplémentaires

Pour en savoir plus sur le déploiement sans interruption de service, consultez ce [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin des ressources que vous avez créées dans ce tutoriel, vous pouvez les supprimer.

```azurecli
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Dépannage

Si vous constatez des bogues dans les plug-ins Jenkins, enregistrez un problème dans le [Jenkins JIRA](https://issues.jenkins-ci.org/) du composant en question.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer un déploiement sur AKS à l’aide de Jenkins et du modèle de déploiement bleu/vert. Pour en savoir plus sur le fournisseur Azure Jenkins, consultez le site Jenkins sur Azure.

> [!div class="nextstepaction"]
> [Jenkins sur Azure](/azure/jenkins/)