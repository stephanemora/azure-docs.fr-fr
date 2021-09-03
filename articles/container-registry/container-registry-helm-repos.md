---
title: Stocker des charts Helm
description: Découvrez comment stocker des graphiques Helm pour vos applications Kubernetes en utilisant des référentiels dans Azure Container Registry
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: cdc4b0c6fb5aabdb96597cfbbe151598b16a2cc0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438905"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Envoyer (push) et tirer (pull) des graphiques Helm vers un registre de conteneurs Azure

Pour gérer et déployer rapidement des applications pour Kubernetes, vous pouvez utiliser le [Gestionnaire de package Helm open source][helm]. Avec Helm, les packages d’applications sont définis en tant que [graphiques](https://helm.sh/docs/topics/charts/), lesquels sont collectés et stockés dans un [référentiel de graphiques Helm](https://helm.sh/docs/topics/chart_repository/).

Cet article vous montre la procédure à suivre pour stocker des référentiels de graphiques Helm hébergés dans le registre de conteneurs Azure, en utilisant les 3 commandes Helm et les graphiques de stockage en tant qu’[artefacts OCI](container-registry-image-formats.md#oci-artifacts). Dans beaucoup de scénarios, vous devez générer et charger vos propres graphiques pour les applications que vous développez. Pour plus d’informations sur la génération de vos propres graphiques Helm, consultez le [Guide pour les développeurs de graphiques Helm][develop-helm-charts]. Vous pouvez également stocker un graphique Helm existant à partir d’un autre dépôt Helm.

## <a name="helm-3-or-helm-2"></a>Helm 3 ou Helm 2 ?

Pour stocker, gérer et installer des graphiques Helm, vous utilisez des commandes dans l’interface CLI Helm. Les principales versions Helm incluent Helm 3 et Helm 2. Pour plus d’informations sur les différences entre les versions, consultez la [FAQ sur les versions](https://helm.sh/docs/faq/). 

Helm 3 doit être utilisé pour le stockage des graphiques Helm dans Azure Container Registry. Avec Helm 3, vous pouvez :

* stocker et gérer des graphiques Helm dans des référentiels dans le registre de conteneurs Azure
* stocker les graphiques Helm dans un registre en tant qu’[artefacts OCI](container-registry-image-formats.md#oci-artifacts). Azure Container Registry assure la prise en charge de la disponibilité générale pour les artefacts OCI, y compris les graphiques Helm.
* Authentifiez-vous auprès du registre à l’aide de la commande `helm registry login` ou `az acr login`.
* Utilisez les commandes `helm chart` pour envoyer (push), tirer (pull) et gérer des graphiques Helm dans un registre
* Utiliser `helm install` pour installer des graphiques sur un cluster Kubernetes à partir d’un cache de référentiel local.

### <a name="feature-support"></a>Prise en charge des fonctionnalités

Azure Container Registry prend en charge des fonctionnalités de gestion de graphiques Helm spécifiques selon que vous utilisez Helm 3 (actuel) ou Helm 2 (déconseillé).

| Fonctionnalité | Helm 2 | Helm 3 |
| ---- | ---- | ---- |
| Gérer des graphiques à l’aide des commandes `az acr helm` | :heavy_check_mark: | |
| Stocker des graphiques en tant qu’artefacts OCI | | :heavy_check_mark:  |
| Gérer des graphiques à l’aide des commandes `az acr repository` et du panneau **Référentiels** dans le Portail Azure| | :heavy_check_mark:  |


> [!NOTE]
> Tout comme pour Helm 3, l’utilisation des commandes [az acr helm][az-acr-helm] avec le client Helm 2 est déconseillée. Un préavis d’au moins 3 mois sera envoyé avant la suppression de la commande.

### <a name="chart-version-compatibility"></a>Compatibilité des versions de graphiques

Les [versions de graphiques](https://helm.sh/docs/topics/charts/#the-apiversion-field) Helm suivantes peuvent être stockées dans Azure Container Registry et peuvent être installées par les clients Helm 2 et Helm 3. 

| Version | Helm 2 | Helm 3 |
| ---- | ---- | ---- |
| apiVersion v1 | :heavy_check_mark: | :heavy_check_mark: |
| apiVersion v2 | | :heavy_check_mark: |

### <a name="migrate-from-helm-2-to-helm-3"></a>Migrer de Helm 2 à Helm 3

Si vous avez déjà stocké et déployé des graphiques à l’aide de Helm 2 et Azure Container Registry, nous vous recommandons de migrer vers Helm 3. Consultez l'article :

* [Migration de Helm 2 vers 3](https://helm.sh/docs/topics/v2_v3_migration/) dans la documentation de Helm.
* [Migrer votre registre pour stocker les artefacts Helm OCI](#migrate-your-registry-to-store-helm-oci-artifacts), plus loin dans cet article

## <a name="prerequisites"></a>Prérequis

Les ressources suivantes sont nécessaires pour le scénario décrit dans cet article :

- **Un registre de conteneurs Azure** dans votre abonnement Azure. Si nécessaire, créez un registre en utilisant le [portail Azure](container-registry-get-started-portal.md) ou [Azure CLI](container-registry-get-started-azure-cli.md).
- **Client Helm version 3.1.0 ou ultérieure** : exécutez `helm version` pour connaître votre version actuelle. Pour plus d’informations sur l’installation et la mise à niveau de Helm, consultez [Installation de Helm][helm-install].
- **Un cluster Kubernetes** où vous allez installer un graphique Helm. Si nécessaire, créez un [cluster Azure Kubernetes Service][aks-quickstart]. 
- **Azure CLI version 2.0.71 ou ultérieure** : exécutez `az --version` pour connaître la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

## <a name="enable-oci-support"></a>Activer la prise en charge d’OCI

Utiliser la commande `helm version` pour vérifier que vous avez installé Helm 3 :

```console
helm version
```

Définissez la variable d’environnement suivante pour activer la prise en charge d’OCI dans le client Helm 3. Actuellement, cette prise en charge est expérimentale et sujette à modification. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Créer un exemple de graphique

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

Dans le dossier `templates`, créez un fichier nommé `configmap.yaml` en exécutant la commande suivante :

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

## <a name="save-chart-to-local-registry-cache"></a>Enregistrer le graphique dans le cache de registre local

Accédez au sous-répertoire `hello-world`. Ensuite, exécutez `helm chart save` pour enregistrer une copie du graphique localement et pour créer un alias composé du nom complet du registre (tout en minuscules), du dépôt cible et de l’étiquette cible. 

Dans l’exemple suivant, le nom du registre est *mycontainerregistry*, le référentiel cible est *helm/hello-world* et l’étiquette du graphique cible est *0.1.0*. Pour que l’extraction des dépendances réussisse, le nom et l’étiquette de l’image du graphique cible doivent correspondre au nom et à la version figurant dans `Chart.yaml`.

```console
cd ..
helm chart save . hello-world:0.1.0
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

Exécutez `helm chart list` pour vérifier que les graphiques ont bien été enregistrés dans le cache de registre local. Le résultat se présente ainsi :

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:0.1.0                                        hello-world      0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:0.1.0    hello-world      0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>S’authentifier auprès du registre

Exécuter `helm registry login` pour s’authentifier auprès du registre. Vous pouvez transmettre les [informations d’identification de Registre](container-registry-authentication.md) appropriées pour votre scénario, telles que les informations d’identification du principal de service ou un jeton d’étendue de référentiel.

Par exemple, créez un [principal de service Azure Active Directory avec des autorisations d’extraction et de transmission](container-registry-auth-service-principal.md#create-a-service-principal) (rôle AcrPush) dans le registre. Fournissez ensuite les informations d’identification du principal de service à `helm registry login`. L’exemple suivant fournit le mot de passe à l’aide d’une variable d’environnement :

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

> [!TIP]
> Vous pouvez aussi vous connecter au registre avec votre [identité Azure AD individuelle](container-registry-authentication.md?tabs=azure-cli#individual-login-with-azure-ad) pour envoyer (push) et tirer (pull) les graphiques Helm.

## <a name="push-chart-to-registry"></a>Envoyer le graphique dans le registre

Exécutez la commande `helm chart push` dans l’interface CLI Helm 3 pour envoyer (push) le graphique vers le dépôt cible (spécifié avec son nom complet) :

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

Après une opération push réussie, la sortie ressemble à ceci :

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Répertorier les graphiques dans le référentiel

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
      "0.1.0"
    ]
```

## <a name="pull-chart-to-local-cache"></a>Tirer (pull) le graphique vers le cache local

Pour installer un graphique Helm sur Kubernetes, le graphique doit se trouver dans le cache local. Dans cet exemple, commencez par exécuter `helm chart remove` pour supprimer le graphique local existant nommé `mycontainerregistry.azurecr.io/helm/hello-world:0.1.0` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

Exécutez `helm chart pull` pour télécharger le graphique du registre de conteneurs Azure dans votre cache local :

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

## <a name="export-helm-chart"></a>Exporter le graphique Helm

Pour pouvoir travailler plus en détail sur le graphique, exportez-le dans un répertoire local à l’aide de la commande `helm chart export`. Par exemple, exportez le graphique que vous avez tiré dans le répertoire `install` :

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:0.1.0 \
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

## <a name="install-helm-chart"></a>Installer le graphique Helm

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

Pour vérifier l’installation, exécutez la commande `helm get manifest`. 

```console
helm get manifest myhelmtest
```

La commande retourne les données YAML dans votre fichier de modèle `configmap.yaml`.

Exécutez `helm uninstall` pour désinstaller la version du graphique sur votre cluster :

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Supprimer le graphique du registre

Pour supprimer un graphique du registre de conteneurs, utilisez la commande [az acr repository delete][az-acr-repository-delete]. Exécutez la commande suivante, puis confirmez l’opération lorsque vous y êtes invité :

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:0.1.0
```

## <a name="migrate-your-registry-to-store-helm-oci-artifacts"></a>Migrer votre registre pour stocker les artefacts Helm OCI

Si vous configurez préalablement votre registre de conteneurs Azure en tant que référentiel de graphiques à l’aide de Helm 2 et des commandes `az acr helm`, nous vous recommandons de [mettre à niveau][helm-install] vers le client Helm 3. Ensuite, procédez comme suit pour stocker les graphiques en tant qu’artefacts OCI dans votre registre. 

> [!IMPORTANT]
> * Une fois que vous avez terminé la migration à partir d’un référentiel de graphiques de style Helm 2 (basé sur index.yaml) vers des référentiels d’artefacts OCI, utilisez l’interface de ligne de commande Helm et les commandes `az acr repository` pour gérer les graphiques. Consultez les sections précédentes de cet article. 
> * Les référentiels d’artefacts OCI Helm ne sont pas détectables à l’aide des commandes Helm telles que `helm search` et `helm repo list`. Pour plus d’informations sur les commandes Helm utilisées pour stocker des graphiques en tant qu’artefacts OCI, consultez la [documentation Helm](https://helm.sh/docs/topics/registries/).

### <a name="enable-oci-support"></a>Activer la prise en charge d’OCI

Vérifiez que vous utilisez le client Helm 3 :

```console
helm version
```

Activez le support OCI dans le client Helm 3. Actuellement, cette prise en charge est expérimentale et sujette à modification.

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="list-current-charts"></a>Répertorier les graphiques actuels

Répertoriez les graphiques actuellement stockés dans le registre, ici nommé *myregistry* :

```console
helm search repo myregistry
```

La sortie affiche les graphiques et les versions de graphiques :

```
NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
myregistry/ingress-nginx        3.20.1          0.43.0          Ingress controller for Kubernetes...
myregistry/wordpress            9.0.3           5.3.2           Web publishing platform for building...
[...]
```

### <a name="save-charts-as-oci-artifacts"></a>Enregistrer des graphiques en tant qu’artefacts OCI

Pour chaque graphique du référentiel, extrayez le graphique localement, puis enregistrez-le en tant qu’artefact OCI. Exemple :

```console 
helm pull myregisry/ingress-nginx --untar
cd ingress-nginx
helm chart save . myregistry.azurecr.io/ingress-nginx:3.20.1
```

### <a name="push-charts-to-registry"></a>Envoyer des graphiques dans le registre

Se connecter au registre :

```azurecli
az acr login --name myregistry
```

Envoyer (push) chaque graphique au registre :

```console
helm chart push myregistry.azurecr.io/ingress-nginx:3.20.1
```

Après avoir envoyé un graphique, confirmez qu’il est stocké dans le registre :

```azurecli
az acr repository list --name myregistry
```

Après avoir envoyé tous les graphiques, supprimez éventuellement le référentiel de graphiques de type Helm 2 du registre. Cela réduit le stockage dans votre registre :

```console
helm repo remove myregistry
```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la création et le déploiement de graphiques Helm, consultez [Développement de graphiques Helm][develop-helm-charts].
* Découvrez-en plus sur l’installation d’applications avec Helm dans [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Les graphiques Helm peuvent être utilisés dans le cadre du processus de génération de conteneur. Pour plus d’informations, consultez [Utiliser des tâches Azure Container Registry][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[acr-tasks]: container-registry-tasks-overview.md
