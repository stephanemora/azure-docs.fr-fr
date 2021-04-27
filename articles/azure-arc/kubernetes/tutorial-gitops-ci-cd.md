---
title: 'Tutoriel : Implémenter une CI/CD avec GitOps à l’aide de clusters Kubernetes avec Azure Arc'
description: Ce tutoriel vous guide dans la configuration d’une solution CI/CD à l’aide de GitOps avec des clusters Kubernetes avec Azure Arc. Pour une interprétation conceptuelle de ce workflow, consultez l’article Workflow CI/CD à l’aide de GitOps – Kubernetes avec Azure Arc.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 9a228ce6f8b18afb77b656765abbad0bb4ae877f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589140"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Tutoriel : Implémenter une CI/CD avec GitOps à l’aide de clusters Kubernetes avec Azure Arc


Dans ce tutoriel, vous allez configurer une solution CI/CD à l’aide de GitOps avec des clusters Kubernetes avec Azure Arc. À l’aide de l’exemple d’application Azure Vote, vous allez :

> [!div class="checklist"]
> * Créer un cluster Kubernetes avec Azure Arc.
> * Connecter votre application et les référentiels GitOps à Azure Repos.
> * Importer des pipelines CI/CD.
> * Connecter votre instance d’Azure Container Registry (ACR) à Azure DevOps et Kubernetes.
> * Créer des groupes de variables d’environnement.
> * Déployer les environnements `dev` et `stage`.
> * Tester les environnements d’application.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Avant de commencer

Ce tutoriel suppose que vous maîtrisez Azure DevOps, Azure Repos et Pipelines, ainsi qu’Azure CLI.

* Connectez-vous à [Azure DevOps Services](https://dev.azure.com/).
* Suivez le [tutoriel précédent](./tutorial-use-gitops-connected-cluster.md) pour apprendre à déployer GitOps pour votre environnement CI/CD.
* Connaissez [les avantages et l’architecture](./conceptual-configurations.md) de cette fonctionnalité.
* Vérifiez que vous disposez des éléments suivants :
  * Un [cluster Kubernetes avec Azure Arc](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster) nommé **arc-cicd-cluster**.
  * Une instance d’Azure Container Registry connectée (ACR) connectée avec une [intégration AKS](../../aks/cluster-container-registry-integration.md) ou une [authentification de cluster non AKS](../../container-registry/container-registry-auth-kubernetes.md).
  * Des autorisations « Administrateur de build » et « Administrateur de projet » pour [Azure Repos](/azure/devops/repos/get-started/what-is-repos) et [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started).
* Installez les extensions CLI de Kubernetes avec Azure Arc versions 1.0.0 et ultérieures :

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8sconfiguration
  ```
  * Pour mettre à jour ces extensions vers la dernière version, exécutez les commandes suivantes :

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8sconfiguration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Importer des applications et des référentiels GitOps dans Azure Repos

Importez un [référentiel d’application](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#application-repo) et un [référentiel GitOps](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#gitops-repo) dans Azure Repos. Pour ce tutoriel, utilisez les exemples de référentiels suivants :

* Référentiel d’application **arc-cicd-demo-src**
   * URL : https://github.com/Azure/arc-cicd-demo-src
   * Contient l’exemple d’application Azure Vote que vous allez déployer à l’aide de GitOps.
* Référentiel GitOps **arc-cicd-demo-gitops**
   * URL : https://github.com/Azure/arc-cicd-demo-gitops
   * Fonctionne comme base pour les ressources de cluster qui hébergent l’application Azure Vote.

En savoir plus sur l’[importation de référentiels Git](/azure/devops/repos/git/import-git-repository).

>[!NOTE]
> L’importation et l’utilisation de deux référentiels distincts pour le référentiel d’application et celui GitOps peuvent améliorer la sécurité et la simplicité. Les autorisations et la visibilité du référentiel d’application et celui GitOps peuvent être paramétrées individuellement.
> Par exemple, l’administrateur de cluster peut considérer que les modifications du code d’application ne sont pas pertinentes pour l’état souhaité du cluster. À l’inverse, un développeur d’applications n’a pas besoin de connaître les paramètres spécifiques de chaque environnement : un ensemble de valeurs de test qui couvrent les paramètres peut suffire.

## <a name="connect-the-gitops-repo"></a>Connecter le référentiel GitOps

Pour déployer votre application en continu, connectez le référentiel d’application à votre cluster à l’aide de GitOps. Votre référentiel GitOps **arc-cicd-demo-gitops** contient les ressources de base permettant de faire fonctionner votre application sur votre cluster **arc-cicd-cluster**.

Le référentiel GitOps initial contient uniquement un [manifeste](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) qui crée les espaces de noms **dev** et **stage** correspondant aux environnements de déploiement.

La connexion GitOps que vous créez effectuera automatiquement les actions suivantes :
* Synchroniser les manifestes dans le répertoire de manifestes.
* Mettre à jour l’état du cluster.

Le workflow CI/CD remplit le répertoire de manifestes avec des manifestes supplémentaires pour déployer l’application.


1. [Créez une nouvelle connexion GitOps](./tutorial-use-gitops-connected-cluster.md) à votre référentiel **arc-cicd-demo-gitops** récemment importé dans Azure Repos.

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Assurez-vous que le flux utilise *uniquement* le répertoire `arc-cicd-cluster/manifests` comme chemin de base. Définissez le chemin d’accès à l’aide du paramètre d’opérateur suivant :

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Si vous utilisez une chaîne de connexion HTTPS et que vous rencontrez des problèmes de connexion, veillez à omettre le préfixe du nom d’utilisateur dans l’URL. Par exemple, pour `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops`, `alice@` doit être été supprimé. `--https-user` spécifie l’utilisateur à la place, par exemple `--https-user alice`.

1. Vérifiez l’état du déploiement dans Portail Azure.
   * Si le déploiement est réussi, vous verrez les espaces de noms `dev` et `stage` créés dans votre cluster.

## <a name="import-the-cicd-pipelines"></a>Importer des pipelines CI/CD

Maintenant que vous avez synchronisé une connexion GitOps, vous devez importer les pipelines CI/CD qui créent les manifestes.

Le référentiel d’application contient un dossier `.pipeline` avec les pipelines que vous utiliserez pour les demandes de tirage (PR, pull request), l’intégration continue et le déploiement continu. Importez et renommez les trois pipelines fournis dans l’exemple de référentiel :

| Nom du fichier de pipeline | Description |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | Pipeline PR de l’application, nommé **arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | Pipeline CI de l’application, nommé **arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | Pipeline CD de l’application, nommé **arc-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>Connecter votre ACR
Vos pipelines et votre cluster utiliseront ACR pour stocker et récupérer les images Docker.

### <a name="connect-acr-to-azure-devops"></a>Connecter ACR à Azure DevOps
Pendant le processus d’intégration continue, vous allez déployer vos conteneurs d’application dans un registre. Commencez par créer une connexion au service Azure :

1. Dans Azure DevOps, ouvrez la page **Connexions de service** à partir de la page des paramètres du projet. Dans TFS, ouvrez la page **Services** à partir de l’icône **Paramètres** dans la barre de menu supérieure.
2. Choisissez **+ Nouvelle connexion de service**, puis sélectionnez le type de connexion de service dont vous avez besoin.
3. Renseignez les paramètres de la connexion de service. Pour ce didacticiel :
   * Nommez la connexion de service **arc-demo-acr**. 
   * Sélectionnez **myResourceGroup** comme groupe de ressources.
4. Sélectionnez **Accorder une autorisation d’accès à tous les pipelines**. 
   * Cette option autorise les fichiers de pipeline YAML pour les connexions de service. 
5. Choisissez **OK** pour créer la connexion.

### <a name="connect-acr-to-kubernetes"></a>Connecter ACR à Kubernetes
Autorisez votre cluster Kubernetes à extraire des images de votre ACR. S’il est privé, l’authentification est requise.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Connecter ACR à des clusters AKS existants

Intégrez un ACR existant à des clusters AKS existants à l’aide de la commande suivante :

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Créer un secret d’extraction d’image

Pour connecter des clusters non AKS et locaux à votre ACR, créez un secret d’extraction d’image. Kubernetes utilise un secret d’extraction d’image pour stocker les informations nécessaires pour s’authentifier auprès de votre registre.

Créez un secret d’extraction d’image avec la commande `kubectl` suivante. Répétez l’opération pour les espaces de noms `dev` et `stage`.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

Pour éviter d’avoir à définir un imagePullSecret pour chaque pod, envisagez d’ajouter l’imagePullSecret au compte de service dans les espaces de noms `dev` et `stage`. Pour plus d’informations, consultez le [tutoriel Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account).

## <a name="create-environment-variable-groups"></a>Créer des groupes de variables d’environnement

### <a name="app-repo-variable-group"></a>Groupe de variables du référentiel d’application
[Créez un groupe de variables](/azure/devops/pipelines/library/variable-groups) nommé **az-vote-app-dev**. Définissez les valeurs suivantes :

| Variable | Valeur |
| -------- | ----- |
| AZ_ACR_NAME | (votre instance ACR, par exemple : azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (votre connexion au service Azure, qui doit être **arc-demo-acr** comme indiqué plus haut dans le tutoriel) |
| AZURE_VOTE_IMAGE_REPO | Le chemin d’accès complet au référentiel d’application Azure Vote, par exemple azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Dev |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | Chaîne de connexion Git pour votre référentiel GitOps |
| Jeton d’accès personnel | Un [jeton d’accès personnel créé](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) avec des autorisations sources en lecture/écriture. Enregistrez-le pour l’utiliser ultérieurement lors de la création du groupe de variables `stage` |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Marquez votre jeton d’accès personnel comme type de secret. Dans vos applications, pensez à lier les secrets d’un [coffre de clés Azure](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault).
>
### <a name="stage-environment-variable-group"></a>Groupe de variables d’environnement stage

1. Clonez le groupe de variables **az-vote-app-dev**.
1. Remplacez le nom par **az-vote-app-stage**.
1. Vérifiez les valeurs suivantes pour les variables correspondantes :

| Variable | Valeur |
| -------- | ----- |
| ENVIRONMENT_NAME | Étape |
| TARGET_NAMESPACE | `stage` |

Vous êtes maintenant prêt à déployer sur les environnements `dev` et `stage`.

## <a name="deploy-the-dev-environment-for-the-first-time"></a>Déployer l’environnement dev pour la première fois
Une fois les pipelines CI et CD créés, exécutez le pipeline CI pour déployer l’application pour la première fois.

### <a name="ci-pipeline"></a>Pipeline d’intégration continue

Lors de l’exécution initiale du pipeline CI, vous pouvez obtenir une erreur d’autorisation de ressources lors de la lecture du nom de la connexion de service.
1. Vérifiez que la variable à laquelle vous accédez est AZURE_SUBSCRIPTION.
1. Autorisez l’utilisation.
1. Exécutez à nouveau le pipeline.

Le pipeline CI :
* S’assure que la modification de l’application réussit tous les contrôles de qualité automatisés pour le déploiement.
* Effectue toute validation supplémentaire qui n’a pas pu être réalisée dans le pipeline PR.
    * Spécifiquement pour GitOps, le pipeline publie également les artefacts pour la validation qui sera déployée par le pipeline CD.
* Vérifie que l’image Docker a changé et que la nouvelle image est envoyée (push).

### <a name="cd-pipeline"></a>Pipeline de déploiement continu
L’exécution réussie du pipeline CI déclenche le pipeline CD pour terminer le processus de déploiement. Le déploiement dans chaque environnement se fera de manière incrémentielle.

> [!TIP]
> Si le pipeline CD ne se déclenche pas automatiquement :
> 1. Vérifiez que le nom correspond au déclencheur de branche dans [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml).
>    * Elle doit avoir la valeur `arc-cicd-demo-src CI`.
> 1. Relancez le pipeline CI.

Une fois que les modifications du modèle et du manifeste du repo GitOps ont été générées, le pipeline de CD crée une validation, l’envoie (push) et crée une PR pour approbation.
1. Ouvrez le lien PR fourni dans la sortie de la tâche `Create PR`.
1. Vérifiez les modifications apportées au référentiel GitOps. Ce qui suit doit s’afficher :
   * Des modifications de haut niveau du modèle Helm.
   * Des manifestes Kubernetes de bas niveau qui montrent les modifications sous-jacentes à l’état souhaité. Flux déploie ces manifestes.
1. Si tout semble correct, approuvez et terminez la PR.

1. Après quelques minutes, Flux prend en compte la modification et commence le déploiement.
1. Transférez le port localement en utilisant `kubectl` et assurez-vous que l’application fonctionne correctement à l’aide de la commande suivante :

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Affichez l’application Azure Vote dans votre navigateur à l’adresse `http://localhost:8080/`.

1. Votez pour vos favoris et préparez-vous à apporter des modifications à l’application.

## <a name="set-up-environment-approvals"></a>Configurer les approbations d’environnement
Lors du déploiement d’une application, vous pouvez non seulement apporter des modifications au code ou aux modèles, mais vous pouvez également mettre involontairement le cluster dans un état incorrect.

Si l’environnement dev révèle un arrêt après le déploiement, empêchez sa progression vers les environnements ultérieurs à l’aide d’approbations d’environnement.

1. Dans votre projet Azure DevOps, accédez à l’environnement qui doit être protégé.
1. Accédez à **Approbations et vérifications** pour la ressource.
1. Sélectionnez **Create** (Créer).
1. Indiquez les approbateurs et un message facultatif.
1. Sélectionnez à nouveau **Créer** pour terminer l’ajout de la vérification d’approbation manuelle.

Pour plus d’informations, consultez le tutoriel [Définir l’approbation et les vérifications](/azure/devops/pipelines/process/approvals).

La prochaine fois que le pipeline CD s’exécutera, il sera suspendu après la création de la demande de tirage (pull request) de GitOps. Vérifiez que la modification a été correctement synchronisée et qu’elle transmet les fonctionnalités de base. Approuvez la vérification du pipeline pour permettre le passage de la modification à l’environnement suivant.

## <a name="make-an-application-change"></a>Effectuer un changement d’application

Avec cet ensemble de ligne de base de modèles et de manifestes représentant l’état du cluster, vous allez apporter une petite modification à l’application.

1. Dans le référentiel **arc-cicd-demo-src**, modifiez le fichier [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg).

2. Puisque « Cats vs Dogs » ne reçoit pas suffisamment de votes, remplacez-le par « Tabs vs Spaces » pour augmenter le nombre de votes.

3. Validez la modification dans une nouvelle branche, envoyez-la (push) et créez une demande de tirage (pull request).
   * Il s’agit du processus de développement classique qui démarrera le cycle de vie CI/CD.

## <a name="pr-validation-pipeline"></a>Pipeline de validation de la demande de tirage (pull request)

Le pipeline PR est la première ligne de défense contre une modification défectueuse. Les vérifications habituelles de la qualité du code d’application comprennent le linting et l’analyse statique. Du point de vue de GitOps, vous devez également garantir la même qualité pour l’infrastructure résultante à déployer.

Le Dockerfile et les charts Helm de l’application peuvent utiliser le linting de la même façon que l’application.

Les erreurs trouvées pendant le linting vont de :
* de fichiers YAML incorrectement formatés à
* des suggestions de meilleures pratiques, telles que la définition des limites d’UC et de mémoire pour votre application.

> [!NOTE]
> Pour tirer le meilleur parti du linting Helm dans une application réelle, vous devez substituer des valeurs qui sont raisonnablement similaires à celles utilisées dans un environnement réel.

Les erreurs détectées pendant l’exécution du pipeline apparaissent dans la section des résultats du test. Vous pouvez ici :
* Suivre les statistiques utiles relatives aux types d’erreur.
* Rechercher la première validation sur laquelle elles ont été détectées.
* Créer un rapport des appels de procédure des liens de style vers les sections de code qui ont provoqué l’erreur.

Une fois l’exécution du pipeline terminée, vous êtes assuré de la qualité du code de l’application et du modèle qui le déploiera. Vous pouvez maintenant approuver et terminer la PR. Le pipeline CI s’exécutera à nouveau, régénérant les modèles et les manifestes, avant de déclencher le pipeline CD.

> [!TIP]
> Dans un environnement réel, n’oubliez pas de définir des stratégies de branche pour garantir que la PR réussit vos vérifications de la qualité. Pour plus d’informations, consultez l’article [Définir des stratégies de branche](/azure/devops/repos/git/branch-policies).

## <a name="cd-process-approvals"></a>Approbations du processus de déploiement continu

Une exécution réussie du pipeline CI déclenche le pipeline CD pour terminer le processus de déploiement. À l’instar de la première fois où vous pouvez utiliser le pipeline CD, le déploiement dans chaque environnement se fera de manière incrémentielle. Cette fois-ci, le pipeline vous demande d’approuver chaque environnement de déploiement.

1. Approuvez le déploiement dans l’environnement `dev`.
1. Une fois que les modifications du modèle et du manifeste du repo GitOps ont été générées, le pipeline de CD crée une validation, l’envoie (push) et crée une PR pour approbation.
1. Ouvrez le lien PR fourni dans la tâche.
1. Vérifiez les modifications apportées au référentiel GitOps. Ce qui suit doit s’afficher :
   * Des modifications de haut niveau du modèle Helm.
   * Des manifestes Kubernetes de bas niveau qui montrent les modifications sous-jacentes à l’état souhaité.
1. Si tout semble correct, approuvez et terminez la PR.
1. Attendez la fin du déploiement.
1. En guise de test de vérification de build basique, accédez à la page de l’application et vérifiez que l’application de vote affiche désormais Tabs vs Spaces.
   * Transférez le port localement en utilisant `kubectl` et assurez-vous que l’application fonctionne correctement à l’aide de la commande suivante : `kubectl port-forward -n dev svc/azure-vote-front 8080:80`.
   * Affichez l’application Azure Vote dans votre navigateur à l’adresse http://localhost:8080/ et vérifiez que les options de vote sont remplacées par Tabs vs Spaces. 
1. Répétez les étapes 1 à 7 pour l’environnement `stage`.

Votre déploiement est maintenant terminé. Ceci termine le workflow CI/CD.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources en procédant comme suit :

1. Supprimez la connexion de configuration d’Azure Arc GitOps :
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Supprimez l’espace de noms `dev` :
   * `kubectl delete namespace dev`

3. Supprimez l’espace de noms `stage` :
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré un workflow CI/CD complet qui implémente DevOps du développement de l’application au déploiement. Les modifications apportées à l’application déclenchent automatiquement la validation et le déploiement, avec des approbations manuelles.

Passez à notre article conceptuel pour en savoir plus sur GitOps et les configurations avec Kubernetes avec Azure Arc.

> [!div class="nextstepaction"]
> [Workflow CI/CD avec GitOps – Kubernetes avec Azure Arc](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd)
