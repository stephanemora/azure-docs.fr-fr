---
title: Mise en réseau Kubernetes sur un GPU Azure Stack Edge Pro
description: Décrit le fonctionnement de la mise en réseau Kubernetes sur un GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 06/24/2021
ms.author: alkohli
ms.openlocfilehash: ece1d03787308613961fe56087f8b621a753c856
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111572583"
---
# <a name="kubernetes-networking-on-azure-stack-edge-pro-gpu-device"></a>Mise en réseau Kubernetes sur un appareil GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Sur votre GPU Azure Stack Edge Pro, un cluster Kubernetes est créé lorsque vous configurez le rôle de calcul. Une fois le cluster Kubernetes créé, les applications en conteneur peuvent être déployées sur le cluster Kubernetes dans des pods. Il existe des moyens distincts d’utiliser la mise en réseau pour les pods dans votre cluster Kubernetes. 

Cet article décrit la mise en réseau dans un cluster Kubernetes de manière générale et plus spécifiquement dans le contexte d’un GPU Azure Stack Edge Pro. 

## <a name="networking-requirements"></a>Configuration requise du réseau

Voici l’exemple d’une application standard à deux niveaux qui est déployée sur le cluster Kubernetes.

- L’application dispose d’un serveur web frontal et d’une application de base de données dans le serveur principal. 
- Une adresse IP est affectée à chaque pod, mais ces adresses IP peuvent changer au redémarrage et au basculement du pod. 
- Chaque application est constituée de plusieurs pods, et il doit y avoir un équilibrage de la charge du trafic sur tous les réplicas de pods. 

![Configuration réseau requise pour Kubernetes](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1-m.png)

Le scénario ci-dessus entraîne les exigences de mise en réseau suivantes :

 - L’accès à l’application externe doit être effectué par un utilisateur d’application en dehors du cluster Kubernetes via un nom ou une adresse IP. 
 - Les applications au sein du cluster Kubernetes, par exemple, les pods frontaux et de back-end doivent être en mesure de communiquer entre eux.

Pour résoudre les deux besoins, nous introduisons un service Kubernetes. 


## <a name="networking-services"></a>Services de mise en réseau

Il existe deux types de services Kubernetes : 

- **Service d’adresse IP de cluster** : ce service fournit un point de terminaison constant pour les pods d’application. Les pods associés à ces services ne sont pas accessibles à partir de l’extérieur du cluster Kubernetes. L’adresse IP utilisée avec ces services provient de l’espace d’adressage dans le réseau privé. 
    
    Pour savoir comment exposer les pods en interne dans le cluster Kubernetes pour l’accès comme les autres pods, au lieu de les exposer en externe comme service d’équilibreur de la charge, consultez [Exposer le service Kubernetes comme service IP de cluster pour la communication interne]().

- **Adresse IP de l’équilibreur de charge** : comme le service d’adresse IP du cluster, mais l’adresse IP associée provient du réseau externe et est accessible depuis l’extérieur du cluster Kubernetes.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->

## <a name="kubernetes-network-configuration"></a>Configuration réseau pour Kubernetes

Les adresses IP utilisées pour les nœuds Kubernetes et les services externes sont fournies dans la page **Calcul** de l’interface utilisateur locale de l’appareil.

![Attribution d’adresses IP Kubernetes dans l’interface utilisateur locale](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

L’attribution d’adresses IP est destinée aux :

- **Adresses IP des nœuds Kubernetes** : Cette plage d’adresses IP est utilisée pour les nœuds principaux et Worker Kubernetes. Ces adresses IP sont utilisées lorsque les nœuds Kubernetes communiquent entre eux.
- **Adresses IP des services externes Kubernetes** : Cette plage d’adresses IP est utilisée pour les services externes (également appelés services d’équilibreur de charge) qui sont exposés en dehors du cluster Kubernetes.

## <a name="kubernetes-networking-components"></a>Composants réseau pour Kubernetes

Les serveurs DNS Calico, Metallb et Core sont tous les composants qui sont installés pour la mise en réseau sur votre GPU Azure Stack Edge Pro. 

- **Calico** attribue une adresse IP provenant d’une plage d’adresses IP privées à chacun des pods et configure la mise en réseau pour ces pods afin qu’un pod sur un nœud puisse communiquer avec le pod sur un autre nœud. 
- **Metallb** s’exécute sur un pod interne au cluster et attribue une adresse IP aux services de type `load balancer`. Les adresses IP de l’équilibreur de charge sont choisies dans la plage d’adresses IP de service fournie par le biais de l’interface utilisateur locale. 
- **DNS de base** : ce module complémentaire configure le nom du service de mappage des enregistrements DNS sur l’adresse IP du cluster.

Lorsque vous vous connectez à l’interface PowerShell de votre appareil, vous voyez les composants réseau ci-dessus s’exécuter sur votre cluster Kubernetes.

## <a name="network-interfaces-switches"></a>Interfaces réseau, commutateurs 

Votre appareil est disponible en tant que configuration à un nœud qui constitue le cluster d’infrastructure. Le cluster Kubernetes est séparé du cluster d’infrastructure, et déployé par-dessus le cluster d’infrastructure. Le cluster Kubernetes a un nœud principal et un nœud worker. Ces deux nœuds Kubernetes sont des machines virtuelles qui exécutent vos workflows d’applications et cloud.

Les machines virtuelles master et worker ont chacune deux interfaces réseau : l’une se connecte au commutateur virtuel interne et l’autre se connecte au commutateur virtuel externe. 

- **Commutateur virtuel externe** : ce commutateur est créé quand vous activez un port de l’appareil pour le calcul à partir de la page **Calcul** dans l’interface utilisateur locale. Vous utilisez ce commutateur pour votre infrastructure de calcul, par exemple, sur les machines virtuelles que vous déployez sur votre appareil. 
- **Commutateur virtuel interne** : ce commutateur est créé dans le cadre de la configuration d’usine par défaut sur votre appareil. Le commutateur virtuel interne utilise la traduction d’adresses réseau (NAT) pour router le trafic via le port qui est configuré avec une passerelle par défaut. Par exemple, ce commutateur route toutes les requêtes d’exécution IoT des machines virtuelles vers le portail Azure. 

   <!--![Azure Stack Edge networking simplified diagram](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-0.png)-->

## <a name="network-routes"></a>Routes réseau 

Pour les machines virtuelles Kubernetes sur votre appareil, vous pouvez router le trafic en ajoutant une nouvelle configuration de route. Une configuration de route est une entrée de la table de routage qui comprend les champs suivants :

| Paramètre     | Description                                                                              |
|---------------|------------------------------------------------------------------------------------------|
| Destination   | Une adresse IP ou un préfixe d’adresse IP.                                            |
| Longueur de préfixe | Longueur du préfixe correspondant à l’adresse ou à la plage d’adresses dans la destination. |
| Tronçon suivant      | Adresse IP vers laquelle le paquet est transféré.                                         |
| Interface     | Interface réseau qui transfère le paquet IP.                                       |
| Métrique        | La métrique de routage détermine l’interface réseau utilisée par défaut jusqu’à la destination. |


## <a name="change-routing-on-compute-network"></a>Changer le routage sur le réseau de calcul

Utilisez l’applet de commande `Add-HcsNetRoute` pour changer le routage sur les machines virtuelles Kubernetes master et worker. Examinez la disposition dans le diagramme ci-dessous. 

![Diagramme du réseau pour Azure Stack Edge](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-1.png)

- Le port 2 est connecté à Internet ; c’est le chemin souhaité pour le trafic sortant. 
- Vous avez activé le calcul sur le port 3, ce qui a créé un commutateur virtuel externe sur cette interface réseau. 
- Le port 3 est connecté à un réseau privé comportant des caméras et d’autres capteurs qui transmettent des données brutes à l’appareil Azure Stack Edge en vue de leur traitement. 


Si une passerelle est configurée dans votre environnement dans le réseau privé, il est conseillé de définir des routes personnalisées pour que les machines virtuelles Kubernetes master et worker transfèrent uniquement le trafic pertinent via votre passerelle. Cela vous permet de contrôler quel trafic doit transiter sur le réseau de calcul et sur les autres ports que vous avez éventuellement configurés sur votre appareil Azure Stack Edge. Par exemple, vous souhaiterez peut-être que tous les autres trafics accessibles sur Internet transitent par les autres ports physiques de votre appareil. Dans ce cas, le trafic Internet peut passer par le port 2. 

Vous devez également prendre en compte les points suivants :

- Si vous avez un sous-réseau plat, vous n’avez pas besoin d’ajouter ces routes au réseau privé. Vous pouvez le faire (éventuellement) si votre réseau privé contient plusieurs sous-réseaux.
- Vous pouvez ajouter ces routes uniquement aux machines virtuelles Kubernetes master et worker, mais pas à l’appareil (hôte Windows).
- Il n’est pas obligatoire de configurer le calcul Kubernetes avant l’ajout de cette route. Vous pouvez aussi ajouter ou changer des routes après la configuration du calcul Kubernetes. 
- Vous pouvez ajouter une nouvelle configuration de route uniquement via l’interface PowerShell de l’appareil ; ce n’est pas possible par le biais de l’interface utilisateur locale.
- Vérifiez que l’interface réseau que vous allez utiliser a une configuration statique.

## <a name="add-a-route-configuration"></a>Ajouter une configuration de route

Pour ajouter une route personnalisée au réseau privé, utilisez l’applet de commande comme suit :

```powershell
Add-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP address or IP address prefix> -NextHop <IP address of next hop> -RouteMetric <Route metric number> 
```
Voici un exemple de sortie :

```output
Add-HcsNetRoute -InterfaceAlias "Port3" -DestinationPrefix "192.168.21.0/24" -NextHop "192.168.20.1" -RouteMetric 100 
```

La commande ci-dessus crée une entrée dans la table de routage qui définit un sous-réseau de destination 192.168.21.0/24, spécifie le tronçon suivant 192.168.20.1, et attribue à cette entrée de routage une métrique de routage de 100. Diminuez la métrique de routage et augmentez la priorité attribuée à la route.

## <a name="check-route-configuration"></a>Vérifier la configuration de route

Utilisez cette applet de commande pour vérifier toutes les configurations de routes personnalisées que vous avez ajoutées à votre appareil. Ces routes n’incluent pas toutes les routes système ou routes par défaut qui existent déjà sur l’appareil.

```powershell
Get-HcsNetRoute -InterfaceAlias <Port number>
```

## <a name="remove-a-route-configuration"></a>Supprimer une configuration de route

Utilisez cette applet de commande pour supprimer une configuration de route que vous avez ajoutée à votre appareil.

```powershell
Remove-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP or IP prefix>
```

## <a name="routing-with-multiple-network-interfaces"></a>Routage avec plusieurs interfaces réseau

Si plusieurs ports d’appareil sont connectés, les solutions d’association de cartes réseau standard ou SET (Switch Embedded Teaming), qui vous permettent de regrouper plusieurs cartes réseau physiques en une seule carte réseau virtuelle dans un environnement Hyper-V, ne sont pas prises en charge.


## <a name="next-steps"></a>Étapes suivantes

Pour configurer la mise en réseau Kubernetes sur votre GPU Azure Stack Edge Pro, consultez :

- [Exposer une application sans état en externe sur votre GPU Azure Stack Edge Pro via IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)

- [Exposer une application sans état en externe sur votre GPU Azure Stack Edge Pro via kuebctl](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)