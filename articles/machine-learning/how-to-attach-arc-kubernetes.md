---
title: Machine Learning avec Azure Arc (préversion)
description: Configurez un cluster Kubernetes avec Azure Arc pour effectuer l’apprentissage des modèles Machine Learning dans Azure Machine Learning.
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 06/18/2021
ms.topic: how-to
ms.openlocfilehash: 4a588c2668fee342da074c911ce99fe29a7aa06a
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606178"
---
# <a name="configure-azure-arc-enabled-machine-learning-preview"></a>Configuration du Machine Learning avec Azure Arc (préversion)

Découvrez comment configurer le Machine Learning avec Azure Arc pour l’apprentissage.

## <a name="what-is-azure-arc-enabled-machine-learning"></a>Présentation du Machine Learning avec Azure Arc

Azure Arc permet d’exécuter des services Azure dans n’importe quel environnement Kubernetes, qu’il soit local, multicloud ou situé à la périphérie.

Le Machine Learning avec Azure Arc vous permet de configurer et d’utiliser des clusters Kubernetes avec Azure Arc pour effectuer l’apprentissage des modèles Machine Learning et les gérer dans Azure Machine Learning.

Le Machine Learning avec Azure Arc prend en charge les scénarios d’apprentissage suivants :

* Apprentissage de modèles avec l’interface CLI (v2)
  * Entraînement distribué
  * Balayage d’hyperparamètres
* Apprentissage de modèles avec le kit SDK Python Azure Machine Learning
  * Optimisation des hyperparamètres
* Création et utilisation de pipelines Machine Learning
* Apprentissage d’un modèle local avec un serveur proxy sortant
* Apprentissage d’un modèle local avec un magasin de données NFS

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’en possédez pas, [créez un compte gratuit](https://azure.microsoft.com/free) avant de commencer.
* Un cluster Kubernetes avec Azure Arc. Pour plus d’informations, consultez [Guide de démarrage de la connexion d’un cluster Kubernetes existant à Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md).
* Respectez les [Prérequis des extensions de cluster Kubernetes avec Azure Arc](../azure-arc/kubernetes/extensions.md#prerequisites).
  * Azure CLI version 2.24.0 ou ultérieure
  * Extension k8s-extension d’Azure CLI version 0.4.3 ou ultérieure
* Un espace de travail Azure Machine Learning. [Créez un espace de travail](how-to-manage-workspace.md?tabs=python) avant de commencer si vous n’en possédez pas.
  * Kit SDK Python Azure Machine Learning version 1.30 ou ultérieure

## <a name="deploy-azure-machine-learning-extension"></a>Déploiement de l’extension Azure Machine Learning

Kubernetes avec Azure Arc comprend une fonctionnalité d’extension de cluster permettant d’installer divers agents, notamment les définitions Azure Policy, le monitoring et le Machine Learning. Azure Machine Learning impose d’utiliser l’extension de cluster *Microsoft.AzureML.Kubernetes* pour déployer l’agent Azure Machine Learning sur le cluster Kubernetes. Une fois l’extension Azure Machine Learning installée, vous pouvez attacher le cluster à un espace de travail Azure Machine Learning et l’utiliser pour l’apprentissage.

Utilisez l’extension Azure CLI `k8s-extension` pour déployer l’extension Azure Machine Learning sur votre cluster Kubernetes avec Azure Arc.

1. Connexion à Azure
    
    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

1. Déploiement de l’extension Azure Machine Learning

    ```azurecli
    az k8s-extension create --name amlarc-compute --extension-type Microsoft.AzureML.Kubernetes --configuration-settings enableTraining=True  --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster
    ```

    >[!IMPORTANT]
    > Pour activer l’apprentissage sur le cluster avec Azure Arc, vous devez définir `enableTraining` sur **True**. Cette commande crée une ressource Azure Service Bus et une ressource Azure Relay sous le même groupe de ressources que le cluster Arc. Ces ressources servent à communiquer avec le cluster. Toute modification apportée à ces ressources a pour effet d’arrêter les clusters attachés utilisés comme cibles de calcul d’apprentissage.

    Vous pouvez également configurer les paramètres suivants lorsque vous déployez l’extension Azure Machine Learning pour l’apprentissage de modèles :

    |Nom de la clé du paramètre de configuration  |Description  |
    |--|--|
    | ```enableTraining``` | Par défaut, `False`. Définissez-le sur `True` pour créer une instance d’extension servant à l’apprentissage des modèles Machine Learning.  |
    |```logAnalyticsWS```  | Par défaut, `False`. L’extension Azure Machine Learning s’intègre à l’espace de travail Azure Log Analytics. Définissez-le sur `True` pour fournir des fonctionnalités de visionnage et d’analyse des journaux dans l’espace de travail Log Analytics. Des coûts associés à l’espace de travail Log Analytics peuvent s’appliquer.   |
    |```installNvidiaDevicePlugin```  | Par défaut, `True`. Le plug-in d’appareil NVIDIA est requis pour l’apprentissage sur du matériel GPU NVIDIA. L’extension Azure Machine Learning installe ce plug-in par défaut lors de la création de l’instance Azure Machine Learning, que le cluster Kubernetes dispose ou non de matériel GPU. Définissez-le sur `False` si vous ne prévoyez pas d’utiliser de GPU pour l’apprentissage ou si le plug-in d’appareil NVIDIA est déjà installé.  |
    |```installBlobfuseSysctl```  | Par défaut, `True` si « enableTraining=True ». Blobfuse 1.3.7 est requis pour l’apprentissage. Azure Machine Learning installe Blobfuse par défaut lors de la création de l’instance d’extension. Définissez ce paramètre de configuration sur `False` si Blobfuse 1.37 est déjà installé sur votre cluster Kubernetes.   |
    |```installBlobfuseFlexvol```  | Par défaut, `True` si « enableTraining=True ». Blobfuse Flexvolume est requis pour l’apprentissage. Azure Machine Learning installe Blobfuse Flexvolume par défaut sur le chemin par défaut. Définissez ce paramètre de configuration sur `False` si Blobfuse Flexvolume est déjà installé sur votre cluster Kubernetes.   |
    |```volumePluginDir```  | Chemin d’ordinateur hôte pour l’installation de Blobfuse Flexvolume. Applicable uniquement si « enableTraining=True ». Par défaut, Azure Machine Learning installe Blobfuse Flexvolume sous le chemin par défaut */etc/kubernetes/volumeplugins*. Spécifiez un emplacement d’installation personnalisé en indiquant ce paramètre de configuration.   |

    > [!WARNING]
    > Si vous réinstallez le plug-in d’appareil NVIDIA, Blobfuse et Blobfuse Flexvolume alors qu’ils sont déjà installés dans votre cluster, il peut se produire une erreur d’installation de l’extension. Définissez `installNvidiaDevicePlugin`, `installBlobfuseSysctl` et `installBlobfuseFlexvol` sur `False` pour éviter toute erreur d’installation.

1. Vérifiez le déploiement de votre extension AzureML.

    ```azurecli
    az k8s-extension show --name amlarc-compute --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group>
    ```

    Dans la réponse, recherchez `"extensionType": "amlarc-compute"` et `"installState": "Installed"`. Il est à noter qu’elle peut afficher `"installState": "Pending"` les premières minutes.

    Lorsque `installState` indique **Installé**, exécutez la commande suivante sur votre ordinateur en faisant pointer le fichier kubeconfig sur votre cluster pour vérifier que tous les pods situés sous l’espace de noms *azureml* se trouvent à l’état *En cours d’exécution* :

   ```bash
    kubectl get pods -n azureml
   ```

## <a name="attach-arc-cluster-studio"></a>Attachement d’un cluster Arc (studio)

Le fait d’attacher un cluster Kubernetes avec Azure Arc permet de le rendre accessible à l’espace de travail pour l’apprentissage.

1. Accédez à [Azure Machine Learning Studio](https://ml.azure.com).
1. Sous **Gérer**, sélectionnez **Calcul**.
1. Sélectionnez l’onglet **Calculs attachés**.
1. Sélectionnez **+Nouveau > Kubernetes (préversion)** .

   ![Attachement d’un cluster Kubernetes](./media/how-to-attach-arc-kubernetes/attach-kubernetes-cluster.png)

1. Entrez un nom de calcul et sélectionnez votre cluster Kubernetes avec Azure Arc dans la liste déroulante.

   ![Configuration du cluster Kubernetes](./media/how-to-attach-arc-kubernetes/configure-kubernetes-cluster.png)

1. (Facultatif) Pour les scénarios avancés, parcourez et chargez un fichier de configuration.

   ![Chargement d’un fichier de configuration](./media/how-to-attach-arc-kubernetes/upload-configuration-file.png)

1. Sélectionnez **Attacher**.

    Dans l’onglet Calculs attachés, l’état initial du cluster est *En cours de création*. Si l’attachement du cluster aboutit, l’état passe à *Réussi*. Sinon, il devient *Échec*.

    ![Provisionner les ressources](./media/how-to-attach-arc-kubernetes/provision-resources.png)

### <a name="advanced-attach-scenario"></a>Scénario d’attachement avancé

Utilisez un fichier de configuration JSON pour configurer des fonctionnalités de cible de calcul avancées sur des clusters Kubernetes avec Azure Arc.

Voici un exemple de fichier de configuration :

```json
{
   "namespace": "amlarc-testing",
   "defaultInstanceType": "gpu_instance",
   "instanceTypes": {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
         }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         },
         "resources": {
            "requests": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            },
            "limits": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            }
         }
      }
   }
}
```

Voici les propriétés de cible de calcul personnalisées qui peuvent être configurées avec un fichier de configuration :

* `namespace` : par défaut, l’espace de noms `default`. Il s’agit de l’espace de noms dans lequel les travaux et les pods s’exécutent. Il est à noter que, si l’espace de noms défini est différent de l’espace de noms par défaut, il doit déjà exister. La création d’espaces de noms requiert des privilèges d’administrateur de cluster.

* `defaultInstanceType` : type d’instance sur lequel les travaux d’apprentissage s’exécutent par défaut. Obligatoire si la propriété `instanceTypes` est spécifiée. La valeur de `defaultInstanceType` doit correspondre à l’une des valeurs définies dans la propriété `instanceTypes`.

    > [!IMPORTANT]
    > Actuellement, seuls les envois de tâches utilisant le nom de cible de calcul sont pris en charge. Par conséquent, la configuration est toujours définie par défaut sur defaultInstanceType.

* `instanceTypes` : liste des types d’instances utilisés pour les travaux d’apprentissage. Chaque type d’instance est défini par les propriétés `nodeSelector` et `resources requests/limits` :

  * `nodeSelector` : une ou plusieurs étiquettes de nœud utilisées pour identifier des nœuds dans un cluster. Des privilèges d’administrateur de cluster sont nécessaires pour créer les étiquettes des nœuds de cluster. Si cette propriété est spécifiée, les travaux d’apprentissage sont planifiés pour s’exécuter sur les nœuds qui possèdent les étiquettes spécifiées. Vous pouvez utiliser `nodeSelector` pour cibler un sous-ensemble de nœuds dans le placement de la charge de travail d’apprentissage. Cela peut être utile dans les scénarios où un cluster possède différentes références SKU ou différents types de nœuds, tels que des nœuds processeur et GPU. Par exemple, vous pouvez créer des étiquettes pour tous les nœuds GPU et définir un `instanceType` pour le pool de nœuds GPU. Vous ciblez ainsi le pool de nœuds GPU exclusivement lors de la planification des travaux d’apprentissage. 

  * `resources requests/limits` : spécifie les demandes de ressources et limite l’exécution d’un pod de travail d’apprentissage. Par défaut, 1 processeur et 4 Go de mémoire.

    >[!IMPORTANT]
    > Par défaut, une ressource de cluster est déployée avec un processeur et 4 Go de mémoire. Si un cluster est configuré avec moins de ressources, l’exécution du travail échoue. Pour garantir la réussite de l’exécution du travail, nous vous recommandons de toujours spécifier les demandes/limites des ressources en fonction des besoins du travail d’apprentissage. Voici un exemple de fichier de configuration par défaut :
    >
    > ```json
    > {
    >    "namespace": "default",
    >    "defaultInstanceType": "defaultInstanceType",
    >    "instanceTypes": {
    >       "defaultInstanceType": {
    >          "nodeSelector": null,
    >          "resources": {
    >             "requests": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             },
    >             "limits": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             }
    >          }
    >       }
    >    }
    > }
    > ```

## <a name="attach-arc-cluster-python-sdk"></a>Attachement d’un cluster Arc (kit SDK Python)

Le code Python suivant montre comment attacher un cluster Kubernetes avec Azure Arc et l’utiliser comme cible de calcul pour l’apprentissage :

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
    amlarc_compute = ws.compute_targets[amlarc_compute_name]
    if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
        print("found compute target: " + amlarc_compute_name)
else:
    print("creating new compute target...")

    amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id) 
    amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
    amlarc_compute.wait_for_completion(show_output=True)
    
     # For a more detailed view of current KubernetesCompute status, use get_status()
    print(amlarc_compute.get_status().serialize())
```

### <a name="advanced-attach-scenario"></a>Scénario d’attachement avancé

Le code suivant montre comment configurer des propriétés de cible de calcul avancées, telles que l’espace de noms, nodeSelector et les demandes/limites de ressources :

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
   amlarc_compute = ws.compute_targets[amlarc_compute_name]
   if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
      print("found compute target: " + amlarc_compute_name)
else:
   print("creating new compute target...")
   ns = "amlarc-testing"
    
   instance_types = {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
        }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         }
      }
   }

   amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns, default_instance_type="gpu_instance", instance_types = instance_types)
 
   amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
   amlarc_compute.wait_for_completion(show_output=True)
    
   # For a more detailed view of current KubernetesCompute status, use get_status()
   print(amlarc_compute.get_status().serialize())
```

## <a name="next-steps"></a>Étapes suivantes

- [Apprentissage de modèles avec l’interface CLI (v2)](how-to-train-cli.md)
- [Configurer et soumettre des exécutions d’entraînement](how-to-set-up-training-targets.md)
- [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)
- [Apprentissage d’un modèle avec Scikit-learn](how-to-train-scikit-learn.md)
- [Entraîner un modèle TensorFlow](how-to-train-tensorflow.md)
- [Apprentissage d’un modèle PyTorch](how-to-train-pytorch.md)
- [Apprentissage avec des pipelines Azure Machine Learning](how-to-create-machine-learning-pipelines.md)
- [Apprentissage d’un modèle local avec un serveur proxy sortant](../azure-arc/kubernetes/quickstart-connect-cluster.md#5-connect-using-an-outbound-proxy-server)
