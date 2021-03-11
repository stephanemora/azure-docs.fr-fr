---
title: Modifier les modules IoT Edge sur un appareil FPGA pour qu’ils s’exécutent sur un appareil Azure Stack Edge Pro GPU
description: Décrit les modifications nécessaires pour que des modules IoT Edge existants sur des appareils FPGA existants s’exécutent sur votre appareil Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 660fbf7cc4dd28c800d8f49fd5d990c99f97c4c8
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442993"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Exécuter des modules IoT Edge existants d’appareils Azure Stack Edge Pro FPGA sur un appareil Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Cet article décrit en détail les modifications à apporter pour qu’un module IoT Edge basé sur Docker s’exécutant sur Azure Stack Edge Pro FPGA puisse s’exécuter sur une plateforme IoT Edge basée sur Kubernetes sur un appareil Azure Stack Edge Pro GPU. 

## <a name="about-iot-edge-implementation"></a>À propos de l’implémentation d’IoT Edge 

L’implémentation d’IoT Edge sur des appareils Azure Stack Edge Pro FPGA n’est pas la même que sur des appareils Azure Stack Edge Pro GPU. Pour les appareils GPU, Kubernetes est utilisé en tant que plateforme d’hébergement pour IoT Edge. IoT Edge sur des appareils FPGA utilise une plateforme basée sur Docker. Le modèle d’application IoT Edge basée sur Docker est automatiquement traduit en modèle d’application native Kubernetes. Toutefois, certaines modifications peuvent encore s’avérer nécessaires, car seul un petit sous-ensemble du modèle d’application Kubernetes est pris en charge.

Si vous migrez vos charges de travail à partir d’un appareil FPGA vers un appareil GPU, vous devez apporter des modifications aux modules IoT Edge existants afin que ceux-ci s’exécutent correctement sur la plateforme Kubernetes. Il se peut que vous deviez spécifier différemment vos exigences en matière de stockage, de mise en réseau, d’utilisation des ressources et de proxy web. 

## <a name="storage"></a>Stockage

Lorsque vous spécifiez le stockage pour les modules IoT Edge, tenez compte des informations suivantes.

- Le stockage des conteneurs sur Kubernetes est spécifié à l’aide de montages de volumes.
- Un déploiement sur Kubernetes ne peut pas avoir de liaisons pour associer un stockage persistant ou des chemins d’accès d’ordinateurs hôtes.
    - Pour un stockage persistant, utilisez des `Mounts` de type `volume`.
    - Pour des chemins d’accès d’ordinateurs hôtes, utilisez des `Mounts` de type `bind`.
- Pour IoT Edge sur Kubernetes, une liaison via `Mounts` fonctionne uniquement pour un répertoire, pas pour un fichier.

#### <a name="example---storage-via-volume-mounts"></a>Exemple : stockage via des montages de volumes 

Pour IoT Edge sur Docker, des liaisons de chemin d’accès d’ordinateur hôte sont utilisées pour mapper les partages sur l’appareil à des chemins d’accès à l’intérieur du conteneur. Voici les options de création de conteneur utilisées sur les appareils FPGA :

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
Pour les chemins d’accès d’ordinateurs hôtes pour IoT Edge sur Kubernetes, un exemple d’utilisation de `Mounts` de type `bind` est illustré ici :
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

Pour les appareils GPU exécutant IoT Edge sur Kubernetes, des montages de volumes sont utilisés pour spécifier le stockage. Pour approvisionner le stockage à l’aide de partages, la valeur des `Mounts.Source` serait le nom du partage SMB ou NFS approvisionné sur votre appareil GPU. `/home/input` correspond au chemin permettant d’accéder au volume dans le conteneur. Voici les options de création de conteneur utilisées sur les appareils GPU :

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Réseau

Lorsque vous spécifiez la mise en réseau pour les modules IoT Edge, tenez compte des informations suivantes. 

- La spécification de `HostPort` est requise pour exposer un service tant à l’intérieur qu’à l’extérieur du cluster.
    - Options K8sExperimental pour limiter l’exposition du service au cluster.
- La communication entre modules requiert la spécification de `HostPort` et une connexion utilisant un port mappé (et non le port exposé du conteneur).
- La mise en réseau hôte fonctionne avec `dnsPolicy = ClusterFirstWithHostNet`. Ainsi, tous les conteneurs (en particulier `edgeHub`) ne doivent pas nécessairement être également sur le réseau hôte. <!--Need further clarifications on this one-->
- L’ajout de mappages de ports pour TCP, UDP dans une même demande ne fonctionne pas.

#### <a name="example---external-access-to-modules"></a>Exemple : accès externe aux modules 

Pour les modules IoT Edge qui spécifient des liaisons de port, une adresse IP est attribuée à l’aide de la plage d’adresses IP de service externe Kubernetes spécifiée dans l’interface utilisateur locale de l’appareil. Aucune modification n’est apportée aux options de création de conteneur entre IoT Edge sur Docker et IoT Edge sur Kubernetes, comme le montre l’exemple suivant.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

Toutefois, pour interroger l’adresse IP attribuée à votre module, vous pouvez utiliser le tableau de bord Kubernetes comme décrit dans [Obtenir l’adresse IP de services ou de modules](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules). 

Vous pouvez également [vous connecter à l’interface PowerShell de l’appareil](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) et utiliser la commande `iotedge` list pour répertorier tous les modules en cours d’exécution sur votre appareil. Le [sortie de la commande](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) indique également les adresses IP externes associées au module.


## <a name="resource-usage"></a>Utilisation des ressources 

Avec les configurations d’IoT Edge basées sur Kubernetes sur des appareils GPU, les ressources telles que l’accélération matérielle, la mémoire et la configuration requise du processeur sont spécifiées autrement que sur les appareils FPGA. 

#### <a name="compute-acceleration-usage"></a>Utilisation de l’accélération du calcul

Pour déployer des modules sur FPGA, utilisez les options de création de conteneur <!--with Device Bindings--> comme illustré dans la configuration suivante : <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
Pour GPU, utilisez des spécifications de demande de ressource au lieu de liaisons d’appareil, comme dans la configuration minimale suivante. Vous demandez des ressources nvidia au lieu de catapult et n’avez pas besoin de spécifier le `wireserver`. 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Utilisation de la mémoire et du processeur
 
Pour définir l’utilisation de la mémoire et du processeur, utilisez des limites de processeur pour les modules dans la section `k8s-experimental`. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
S’il n’est pas indispensable de spécifier la mémoire et le processeur, c’est généralement conseillé. Si le paramètre `requests` n’est pas spécifié, les valeurs définies dans les limites sont utilisées comme valeur minimale requise. 

L’utilisation de la mémoire partagée pour les modules requiert également une autre méthode. Par exemple, vous pouvez utiliser le mode IPC hôte pour l’accès à la mémoire partagée entre Live Video Analytics et les solutions d’inférence, comme décrit dans [Déployer Live Video Analytics sur Azure Stack Edge](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal).


## <a name="web-proxy"></a>Proxy web 

Lors de la configuration du proxy web, tenez compte des informations suivantes :

Si vous avez configuré un proxy web dans votre réseau, configurez les variables d’environnement suivantes pour le déploiement de `edgeHub` sur votre configuration IoT Edge basée sur Docker sur des appareils FPGA :

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (sauf si le proxy web autorise le trafic `Amqp`)

Pour les configurations IoT Edge basées sur Kubernetes sur des appareils GPU, vous devez configurer cette variable supplémentaire pendant le déploiement :

- `no_proxy` : localhost

- Un proxy IoT Edge sur une plateforme Kubernetes utilise les ports 35000 et 35001. Assurez-vous que votre module ne s’exécute pas sur ces ports. Autrement, il pourrait occasionner des conflits de port. 

## <a name="other-differences"></a>Autres différences

- **Stratégie de déploiement** : il se peut que vous deviez modifier le comportement de déploiement pour des mises à jour du module. Le comportement par défaut pour les modules IoT Edge est la mise à jour propagée. Ce comportement empêche le module mis à jour de redémarrer s’il utilise des ressources telles que l’accélération matérielle ou les ports réseau. Ce comportement peut avoir des effets inattendus, en particulier quand il s’agit de volumes persistants sur une plateforme Kubernetes pour les appareils GPU. Pour modifier ce comportement par défaut, vous pouvez spécifier `Recreate` dans la section `k8s-experimental` de votre module.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **Noms de modules** : les noms des modules doivent respecter les conventions d’affectation de noms de Kubernetes. Il se peut que vous deviez renommer les modules s’exécutant sur IoT Edge avec Docker quand vous les déplacez vers IoT Edge avec Kubernetes. Pour plus d’informations sur l’affectation de noms, consultez [Conventions d’affectation de noms de Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **Autres options** : 
    - Certaines options de création de Docker qui fonctionnaient sur des appareils FPGA ne fonctionneront pas dans l’environnement Kubernetes sur vos appareils GPU. Par exemple : , like – EntryPoint.<!--can we confirm what exactly is required here-->
    - Des variables d’environnement telles que `:` doivent être remplacées par `__`.
    - L’état **Container Creating** pour un pod Kubernetes se traduit par l’état **backoff** pour un module sur la ressource IoT Hub. Si un certain nombre de raisons peuvent expliquer pourquoi le pod est dans cet état, une raison courante est quand une image de grand conteneur est extraite sur une connexion à bande passante réseau faible. Quand le pod est dans cet état, l’état du module est **backoff** dans IoT Hub, même si le module est en cours de démarrage.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez en détail comment [configurer le GPU pour utiliser un module](azure-stack-edge-j-series-configure-gpu-modules.md).
