---
title: Stocker des charts Helm
description: Découvrez comment stocker des graphiques Helm pour vos applications Kubernetes en utilisant des référentiels dans Azure Container Registry
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 3f1a68258b758380a66b63e3c3137f1d460d288c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399389"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Envoyer (push) et tirer (pull) des graphiques Helm vers un registre de conteneurs Azure

Pour gérer et déployer rapidement des applications pour Kubernetes, vous pouvez utiliser le [Gestionnaire de package Helm open source][helm]. Avec Helm, les packages d’applications sont définis en tant que [graphiques](https://helm.sh/docs/topics/charts/), lesquels sont collectés et stockés dans un [référentiel de graphiques Helm](https://helm.sh/docs/topics/chart_repository/).

Cet article explique comment stocker des dépôts de graphiques Helm dans un registre de conteneurs Azure en utilisant Helm 3 ou Helm 2. Dans beaucoup de scénarios, vous devez générer et charger vos propres graphiques pour les applications que vous développez. Pour plus d’informations sur la génération de vos propres graphiques Helm, consultez le [Guide pour les développeurs de graphiques Helm][develop-helm-charts]. Vous pouvez également stocker un graphique Helm existant à partir d’un autre dépôt Helm.

> [!IMPORTANT]
> La prise en charge des graphiques Helm dans Azure Container Registry est actuellement disponible en préversion. Les préversions sont mises à votre disposition après votre accord explicite des [conditions d’utilisation][terms-of-use] supplémentaires. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="helm-3-or-helm-2"></a>Helm 3 ou Helm 2 ?

Pour stocker, gérer et installer des graphiques Helm, vous utilisez un client Helm et la CLI Helm. Les versions majeures du client Helm incluent Helm 3 et Helm 2. Helm 3 prend en charge un nouveau format de graphique et n’installe plus le composant côté serveur Tiller. Pour plus d’informations sur les différences entre les versions, consultez la [FAQ sur les versions](https://helm.sh/docs/faq/). Si vous avez déjà déployé des graphiques Helm 2, consultez cette page sur la [migration de Helm 2 vers Helm 3](https://helm.sh/docs/topics/v2_v3_migration/).

Vous pouvez utiliser Helm 3 ou Helm 2 pour stocker des graphiques Helm dans Azure Container Registry, en suivant le workflow propre à chaque version :

* [Client Helm 3](#use-the-helm-3-client) : utilisez les commandes `helm chart` dans l’interface CLI Helm pour gérer les graphiques dans votre registre en tant qu’[artefacts OCI](container-registry-image-formats.md#oci-artifacts)
* [Client Helm 2](#use-the-helm-2-client) : utilisez les commandes [az acr helm][az-acr-helm] dans Azure CLI pour ajouter et gérer votre registre de conteneurs comme un référentiel de graphiques Helm

### <a name="additional-information"></a>Informations supplémentaires

* Pour la plupart des scénarios, nous vous recommandons d’utiliser le workflow Helm 3 avec les commandes `helm chart` natives pour gérer les graphiques en tant qu’artefacts OCI.
* Vous pouvez utiliser les commandes [az acr helm][az-acr-helm] héritées d’Azure CLI avec le workflow du client et des graphiques Helm 3. Sachez toutefois que certaines commandes comme `az acr helm list` ne sont pas compatibles avec les graphiques Helm 3.
* Dans Helm 3, les commandes [az acr helm][az-acr-helm] sont prises en charge dans le but principal d’assurer la compatibilité avec le client Helm 2 et son format de graphiques. Le développement ultérieur de ces commandes n’est pas prévu pour le moment.

## <a name="use-the-helm-3-client"></a>Utiliser le client Helm 3

### <a name="prerequisites"></a>Prérequis

- **Un registre de conteneurs Azure** dans votre abonnement Azure. Si nécessaire, créez un registre en utilisant le [portail Azure](container-registry-get-started-portal.md) ou [Azure CLI](container-registry-get-started-azure-cli.md).
- **Client Helm version 3.1.0 ou ultérieure** : exécutez `helm version` pour connaître votre version actuelle. Pour plus d’informations sur l’installation et la mise à niveau de Helm, consultez [Installation de Helm][helm-install].
- **Un cluster Kubernetes** où vous allez installer un graphique Helm. Si nécessaire, créez un [cluster Azure Kubernetes Service][aks-quickstart]. 
- **Azure CLI version 2.0.71 ou ultérieure** : exécutez `az --version` pour connaître la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Workflow général

Avec **Helm 3**, vous pouvez :

* Créer un ou plusieurs référentiels Helm dans un registre de conteneurs Azure.
* Stocker les graphiques Helm 3 dans un registre en tant qu’[artefacts OCI](container-registry-image-formats.md#oci-artifacts). La prise en charge de Helm 3 pour OCI est *expérimentale*.
* Authentifiez-vous auprès du registre à l’aide de la commande `helm registry login`.
* Utilisez les commandes `helm chart` dans la CLI Helm pour envoyer (push), tirer (pull) et gérer des graphiques Helm dans un registre.
* Utiliser `helm install` pour installer des graphiques sur un cluster Kubernetes à partir d’un cache de référentiel local.

Pour obtenir des exemples, consultez les sections suivantes.

### <a name="enable-oci-support"></a>Activer la prise en charge d’OCI

Définissez la variable d’environnement suivante pour activer la prise en charge d’OCI dans le client Helm 3. Actuellement, cette prise en charge est expérimentale. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Créer un exemple de graphique

Créez un graphique de test à l’aide des commandes suivantes :

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

En guise d’exemple de base, accédez au dossier `templates` et supprimez son contenu :

```console
cd hello-world/templates
rm -rf *
```

Dans le dossier `templates`, créez un fichier nommé `configmap.yaml` avec le contenu suivant :

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Pour plus d’informations sur la création et l’exécution de cet exemple, consultez [Getting Started](https://helm.sh/docs/chart_template_guide/getting_started/) dans la documentation Helm.

### <a name="save-chart-to-local-registry-cache"></a>Enregistrer le graphique dans le cache de registre local

Accédez au sous-répertoire `hello-world`. Ensuite, exécutez `helm chart save` pour enregistrer une copie du graphique localement et pour créer un alias composé du nom complet du registre (tout en minuscules), du dépôt cible et de l’étiquette cible. 

Dans l’exemple suivant, le nom du registre est *mycontainerregistry*, le dépôt cible est *hello-world* et l’étiquette du graphique cible est *v1*. Remplacez ces valeurs par les valeurs appropriées à votre environnement :

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Exécutez `helm chart list` pour vérifier que les graphiques ont bien été enregistrés dans le cache de registre local. Le résultat se présente ainsi :

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>S’authentifier auprès du registre

Exécutez la commande `helm registry login` dans l’interface CLI Helm 3 pour [vous authentifier auprès du registre](container-registry-authentication.md) à l’aide des informations d’identification appropriées pour votre scénario.

Par exemple, créez un [principal de service Azure Active Directory avec des autorisations d’extraction et de transmission](container-registry-auth-service-principal.md#create-a-service-principal) (rôle AcrPush) dans le registre. Fournissez ensuite les informations d’identification du principal de service à `helm registry login`. L’exemple suivant fournit le mot de passe à l’aide d’une variable d’environnement :

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Envoyer (push) le graphique vers Azure Container Registry

Exécutez la commande `helm chart push` dans l’interface CLI Helm 3 pour envoyer (push) le graphique vers le dépôt cible (spécifié avec son nom complet) :

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Après une opération push réussie, la sortie ressemble à ceci :

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Répertorier les graphiques dans le référentiel

Comme avec les images stockées dans un registre de conteneurs Azure, vous pouvez utiliser les commandes [az acr repository][az-acr-repository] pour lister les référentiels hébergeant vos graphiques ainsi que les étiquettes et les manifestes des graphiques. 

Par exemple, exécutez [az acr repository show][az-acr-repository-show] pour voir les propriétés du référentiel que vous avez créé à l’étape précédente :

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

Le résultat se présente ainsi :

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Exécutez la commande [az acr repository show-manifests][az-acr-repository-show-manifests] pour voir les détails du graphique stocké dans le référentiel. Par exemple :

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

La sortie, abrégée dans cet exemple, montre le paramètre `configMediaType` défini à `application/vnd.cncf.helm.config.v1+json` :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

### <a name="pull-chart-to-local-cache"></a>Tirer (pull) le graphique vers le cache local

Pour installer un graphique Helm sur Kubernetes, le graphique doit se trouver dans le cache local. Dans cet exemple, commencez par exécuter `helm chart remove` pour supprimer le graphique local existant nommé `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Exécutez `helm chart pull` pour télécharger le graphique du registre de conteneurs Azure dans votre cache local :

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Exporter le graphique Helm

Pour pouvoir travailler plus en détail sur le graphique, exportez-le dans un répertoire local à l’aide de la commande `helm chart export`. Par exemple, exportez le graphique que vous avez tiré dans le répertoire `install` :

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Pour afficher les informations relatives au graphique exporté dans le référentiel, exécutez la commande `helm show chart` dans le répertoire de l’exportation.

```console
cd install
helm show chart hello-world
```

Helm retourne des informations détaillées sur la dernière version de votre graphique, comme indiqué dans l’exemple de sortie suivant :

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Installer le graphique Helm

Exécutez `helm install` pour installer le graphique Helm que vous avez tiré dans le cache local, puis exporté. Spécifiez un nom de version, par exemple *myhelmtest*, ou passez le paramètre `--generate-name`. Par exemple :

```console
helm install myhelmtest ./hello-world
```

La sortie après l’installation réussie du graphique est semblable à celle-ci :

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Pour vérifier l’installation, exécutez la commande `helm get manifest`. La commande retourne les données YAML dans votre fichier de modèle `configmap.yaml`.

Exécutez `helm uninstall` pour désinstaller la version du graphique sur votre cluster :

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Supprimer un graphique Helm du référentiel

Pour supprimer un graphique du référentiel, utilisez la commande [az acr repository delete][az-acr-repository-delete]. Exécutez la commande suivante, puis confirmez l’opération lorsque vous y êtes invité :

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Utiliser le client Helm 2

### <a name="prerequisites"></a>Prérequis

- **Un registre de conteneurs Azure** dans votre abonnement Azure. Si nécessaire, créez un registre en utilisant le [portail Azure](container-registry-get-started-portal.md) ou [Azure CLI](container-registry-get-started-azure-cli.md).
- **Client Helm version 2.11.0 (pas une version RC) ou ultérieure** : exécutez `helm version` pour trouver votre version actuelle. Vous avez également besoin d’un serveur Helm (Tiller) initialisé dans un cluster Kubernetes. Si nécessaire, créez un [cluster Azure Kubernetes Service][aks-quickstart]. Pour plus d’informations sur l’installation et la mise à niveau de Helm, consultez [Installation de Helm][helm-install-v2].
- **Azure CLI version 2.0.46 ou ultérieure** : exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Workflow général

Avec **Helm 2**, vous pouvez :

* Configurer votre registre de conteneurs Azure comme référentiel de graphiques Helm *unique*. Azure Container Registry gère la définition d’index quand vous ajoutez et supprimez des graphiques dans le référentiel.
* Vous authentifier auprès de votre registre de conteneurs Azure par le biais d’Azure CLI, qui met ensuite à jour automatiquement votre client Helm avec l’URI et les informations d’identification du registre. Comme vous n’avez pas besoin de spécifier manuellement les détails de ce registre, les informations d’identification ne sont pas exposées dans l’historique des commandes.
* Utiliser les commandes [az acr helm][az-acr-helm] dans Azure CLI pour ajouter votre registre de conteneurs Azure comme référentiel de graphiques Helm, et pour envoyer (push) et gérer les graphiques. Ces commandes d’Azure CLI wrappent les commandes du client Helm 2.
* Ajouter le dépôt des graphiques de votre registre de conteneurs Azure à votre index de dépôt Helm local, avec prise en charge de la recherche dans les graphiques.
* Utiliser `helm install` pour installer des graphiques sur un cluster Kubernetes à partir d’un cache de référentiel local.

Pour obtenir des exemples, consultez les sections suivantes.

### <a name="add-repository-to-helm-client"></a>Ajouter le référentiel au client Helm

Ajoutez le référentiel de graphiques Helm de votre registre Azure Container Registry à votre client Helm à l’aide de la commande [az acr helm repo add][az-acr-helm-repo-add]. Cette commande obtient un jeton d’authentification pour votre registre Azure Container Registry qui est utilisé par le client Helm. Le jeton d’authentification reste valide pendant 3 heures. Comme pour `docker login`, vous pouvez exécuter cette commande dans les futures sessions CLI pour authentifier votre client Helm auprès de votre référentiel de graphiques Helm Azure Container Registry :

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Ajouter un exemple de graphique au dépôt

Créez d’abord un répertoire local sur *~/acr-helm*, puis téléchargez le graphique *stable/wordpress* existant :

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Tapez `ls` pour lister le graphique téléchargé et notez la version de Wordpress indiquée dans le nom de fichier. Comme la commande `helm fetch stable/wordpress` n’a pas spécifié de version particulière, la *dernière* version a été extraite. Dans l’exemple de sortie ci-dessous, la version du graphique Wordpress est *8.1.0* :

```output
wordpress-8.1.0.tgz
```

Envoyez le graphique vers votre référentiel de graphiques Helm dans Azure Container Registry en exécutant la commande [az acr helm push][az-acr-helm-push] dans Azure CLI. Spécifiez le nom de votre graphique Helm téléchargé à l’étape précédente, par exemple *wordpress-8.1.0.tgz* :

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Après quelques instants, Azure CLI signale que votre graphique est enregistré, comme dans l’exemple de sortie suivant :

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Répertorier les graphiques dans le référentiel

Pour utiliser le graphique chargé à l’étape précédente, vous devez mettre à jour l’index de référentiel Helm local. Vous pouvez réindexer les référentiels dans le client Helm, ou utiliser l’interface de ligne de commande Azure pour mettre à jour l’index de référentiel. Chaque fois que vous ajoutez un graphique à votre référentiel, cette étape doit être effectuée :

```azurecli
az acr helm repo add --name mycontainerregistry
```

Avec un graphique stocké dans votre référentiel et l’index mis à jour disponible localement, vous pouvez utiliser les commandes du client Helm standard pour la recherche ou l’installation. Pour lister tous les graphiques stockés dans votre référentiel, utilisez la commande `helm search`, en spécifiant le nom de votre propre registre de conteneurs Azure :

```console
helm search mycontainerregistry
```

Le graphique Wordpress envoyé à l’étape précédente est répertorié, comme illustré dans l’exemple de sortie suivant :

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

Vous pouvez également répertorier les graphiques avec l’interface de ligne de commande Azure, en utilisant [az acr helm list][az-acr-helm-list] :

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Afficher des informations pour un graphique Helm

Pour afficher les informations relatives à un graphique spécifique dans le référentiel, utilisez la commande `helm inspect`.

```console
helm inspect mycontainerregistry/wordpress
```

Quand aucun numéro de version n’est fourni, la *dernière* version est utilisée. Helm retourne des informations détaillées sur votre graphique, comme indiqué dans l’exemple de sortie condensé suivant :

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

Vous pouvez également afficher les informations pour un graphique à l’aide de la commande [az acr helm show][az-acr-helm-show] de l’interface de ligne de commande Azure. Là encore, la *dernière* version d’un graphique est retournée par défaut. Vous pouvez ajouter `--version` pour lister une version spécifique d’un graphique, par exemple *8.1.0* :

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Installer un graphique Helm à partir du référentiel

Vous installez le graphique Helm dans votre référentiel en spécifiant le nom du référentiel et le nom du graphique. Utilisez le client Helm pour installer le graphique Wordpress :

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Si vous procédez à l’envoi à votre référentiel de graphiques Helm Azure Container Registry et retournez ultérieurement dans une nouvelle session CLI, votre client Helm local a besoin d’un jeton d’authentification mis à jour. Pour obtenir un nouveau jeton d’authentification, utilisez la commande [az acr helm repo add][az-acr-helm-repo-add].

Les étapes suivantes sont effectuées pendant le processus d’installation :

- Le client Helm recherche dans l’index de référentiel local.
- Le graphique correspondant est téléchargé à partir du référentiel Azure Container Registry.
- Le graphique est déployé à l’aide de Tiller dans votre cluster Kubernetes.

À mesure que l’installation se poursuit, suivez les instructions fournies dans la sortie de commande pour afficher les URL et les informations d’identification WordPress. Vous pouvez également exécuter la commande `kubectl get pods` pour voir les ressources Kubernetes déployées par le biais du graphique Helm :

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Supprimer un graphique Helm du référentiel

Pour supprimer un graphique du référentiel, utilisez la commande [az acr helm delete][az-acr-helm-delete]. Spécifiez le nom du graphique (*wordpress* ici) et la version à supprimer (*8.1.0*).

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Si vous souhaitez supprimer toutes les versions du graphique nommé, omettez le paramètre `--version`.

Le graphique continue d’être retourné quand vous exécutez `helm search`. Là encore, le client Helm ne met pas automatiquement à jour la liste des graphiques disponibles dans un référentiel. Pour mettre à jour l’index de référentiel du client Helm, utilisez à nouveau la commande [az acr helm repo add][az-acr-helm-repo-add] :

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la création et le déploiement de graphiques Helm, consultez [Développement de graphiques Helm][develop-helm-charts].
* Découvrez-en plus sur l’installation d’applications avec Helm dans [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Les graphiques Helm peuvent être utilisés dans le cadre du processus de génération de conteneur. Pour plus d’informations, consultez [Utiliser des tâches Azure Container Registry][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
