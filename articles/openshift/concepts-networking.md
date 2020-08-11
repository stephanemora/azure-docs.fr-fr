---
title: Concepts – Schéma de mise en réseau pour Azure Red Hat sur OpenShift 4
description: Schéma et vue d’ensemble de mise en réseau pour Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: fb81405e85d6e2653e0cf6c007c363493992161a
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419969"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>Mise en réseau dans Azure Red Hat sur OpenShift 4

Ce guide propose une vue d’ensemble de la mise en réseau dans des clusters Azure Red Hat sur OpenShift 4, ainsi qu’un schéma et une liste des points de terminaison importants.

Pour plus d’informations sur les principaux concepts des réseaux OpenShift, consultez la [documentation sur les réseaux Azure Red Hat OpenShift 4](https://docs.openshift.com/aro/4/networking/understanding-networking.html).

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Concepts de mise en réseau dans Azure Red Hat OpenShift

![Schéma de mise en réseau Azure Red Hat OpenShift 4](./media/concepts-networking/aro4-networking-diagram.png)

Lorsque vous déployez Azure Red Hat sur OpenShift 4, l’ensemble de votre cluster est contenu dans un réseau virtuel. Dans ce réseau virtuel, vos nœuds principaux et les nœuds Worker résident tous dans leur propre sous-réseau. Chaque sous-réseau utilise un équilibreur de charge public et interne.

## <a name="explanation-of-endpoints"></a>Explication des points de terminaison

La liste suivante couvre les points de terminaison importants d’un cluster Azure Red Hat OpenShift.

* **aro-pls**
    * Il s’agit d’un point de terminaison Azure Private Link utilisé par les ingénieurs de fiabilité des sites Microsoft et Red Hat pour faciliter la gestion du cluster.
* **aro-internal-lb**
    * Ce point de terminaison équilibre le trafic vers le serveur d’API. Pour cet équilibreur de charge, les nœuds principaux se trouvent dans le pool de back-ends.
* **aro-public-lb**
    * Si l’API est publique, ce point de terminaison route et équilibre le trafic vers le serveur d’API. Ce point de terminaison assigne une adresse IP sortante publique afin que les maîtres puissent accéder à Azure Resource Manager et rapporter l’intégrité du cluster.
* **aro-internal**
    * Ce point de terminaison équilibre le trafic interne du service. Pour cet équilibreur de charge, les nœuds Worker se trouvent dans le pool de back-ends.
    * Cet équilibreur de charge n’est pas créé par défaut. Cet équilibreur de charge est créé une fois que vous avez créé un service de type LoadBalancer avec les annotations correctes. Par exemple : service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **Stratégies réseau (entrée)**
    * Prises en charge dans le cadre d’OpenShift SDN
    * Activées par défaut, mise en œuvre effectuée par les clients
    * Conformes à V1 NetworkPolicy, mais les types « Sortie et IPBlock » ne sont pas encore pris en charge
    * **aro**
    * Ce point de terminaison équilibre le trafic vers le serveur d’API. Pour cet équilibreur de charge, les nœuds principaux se trouvent dans le pool de back-ends.
  * **aro-internal-lb**
    * Ce point de terminaison est utilisé pour tout trafic public. Lorsque vous créez une application et une route, il s’agit du chemin pour le trafic d’entrée.
    * Cet équilibreur de charge couvre également la connectivité Internet sortante à partir de tout pod s’exécutant dans les nœuds Worker via les règles de trafic sortant Azure Load Balancer.
        * Actuellement, les règles de trafic sortant ne sont pas configurables. Elles allouent 1 024 ports TCP à chaque nœud.
        * DisableOutboundSnat n’est pas configuré dans les règles d’équilibreur de charge. Par conséquent, les pods peuvent obtenir comme adresse IP de sortie toute adresse IP publique configurée dans cet équilibreur de charge Azure.
        * En conséquence des deux points précédents, le seul moyen d’ajouter des ports SNAT éphémères consiste à ajouter des services publics de type LoadBalancer à ARO.
* **Stratégies réseau (sortie)**
    * Les stratégies de sortie sont prises en charge à l’aide de la fonctionnalité de pare-feu de sortie dans OpenShift.
    * Une seule par espace de noms/projet.
    * Les stratégies de sortie ne sont pas prises en charge sur l’espace de noms « par défaut ».
    * Les règles de stratégie de sortie sont évaluées dans l’ordre (de la première à la dernière).
    * **aro-outbound-pip**
        * Ce point de terminaison sert d’adresse IP publique (PIP) pour les nœuds Worker.
        * Ce point de terminaison permet aux services d’ajouter une adresse IP spécifique provenant d’un cluster Azure Red Hat OpenShift à une liste verte.
* **aro-node-nsg**
    * Lorsque vous exposez un service, l’API crée une règle dans ce groupe de sécurité réseau afin que le trafic le traverse et atteigne les nœuds.
    * Par défaut, ce groupe de sécurité réseau autorise tout le trafic sortant. Actuellement, le trafic sortant peut être limité uniquement au plan de contrôle Azure Red Hat OpenShift.
* **aro-controlplane-nsg**
    * Ce point de terminaison autorise uniquement le trafic à entrer via le port 6443 pour les nœuds principaux.
* **Azure Container Registry**
    * Il s’agit d’un registre de conteneurs fourni et utilisé en interne par Microsoft.
        * Ce registre fournit des images de plateforme hôte et des composants de cluster. Par exemple, des conteneurs de surveillance ou de journalisation.
        * Non destiné à être utilisé par les clients Azure Red Hat OpenShift.  
        * Lecture seule.
        * Les connexions à ce registre se produisent sur le point de terminaison de service (connectivité interne entre les services Azure).
        * Ce registre interne n’est pas disponible en dehors du cluster par défaut.
* **Liaison privée**
    * Autorise la connectivité réseau à partir du plan de gestion dans un cluster pour la gestion du cluster.
    * Ingénieurs de fiabilité des sites Microsoft et Red Hat permettant de faciliter la gestion de votre cluster.

## <a name="networking-basics-in-openshift"></a>Notions de base des réseaux dans OpenShift

OpenShift Software Defined Networking (SDN) est utilisé pour configurer un réseau de superposition à l’aide d’Open vSwitch (OVS), une implémentation OpenFlow basée sur la spécification CNI (Container Network Interface). SDN prend en charge différents plug-ins et Stratégie réseau est le plug-in utilisé dans Azure Red Hat sur OpenShift 4. Toutes les communications réseau sont gérées par SDN, de sorte qu’aucune route supplémentaire n’est nécessaire sur vos réseaux virtuels pour mettre en place une communication de pod à pod.

## <a name="azure-red-hat-openshift-networking-specifics"></a>Spécificités des réseaux Azure Red Hat OpenShift

Les fonctionnalités suivantes sont spécifiques à Azure Red Hat OpenShift :
* L’apport de votre propre réseau virtuel est pris en charge.
* Les CIDR des réseaux de service et de pod sont configurables.
* Les nœuds et les maîtres figurent dans des sous-réseaux différents.
* Les sous-réseaux de réseau virtuel des nœuds et maîtres doivent être /27 au minimum.
* Le CIDR de pod doit avoir une taille minimale de /18 (le réseau de pod est constitué d’adresses IP non routables et n’est utilisé qu’à l’intérieur d’OpenShift SDN).
* Chaque nœud se voit allouer un sous-réseau /23 (512 adresses IP) pour ses pods. Cette valeur ne peut pas être modifiée.
* Vous ne pouvez pas attacher un pod à plusieurs réseaux.
* Vous ne pouvez pas configurer une adresse IP statique de sortie. (Ceci est une fonctionnalité OpenShift. Pour obtenir des informations, consultez la documentation sur la [configuration des adresses IP de sortie](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html)).

## <a name="network-settings"></a>Paramètres réseau

Les paramètres réseau suivants sont disponibles dans Azure Red Hat OpenShift 4 :

* **Visibilité de l’API** – Définissez la visibilité de l’API lors de l’exécution de la [commande az aro create](tutorial-create-cluster.md#create-the-cluster).
    * « Publique » – Le serveur d’API est accessible par les réseaux externes.
    * « Privée » – Le serveur d’API a affecté une adresse IP privée à partir du sous-réseau des maîtres, accessible uniquement à l’aide des réseaux connectés (réseaux virtuels appairés, autres sous-réseaux du cluster). Une zone DNS privée est créée au nom du client.
* **Visibilité des entrées** – Définissez la visibilité de l’API lors de l’exécution de la [commande az aro create](tutorial-create-cluster.md#create-the-cluster).
    * Les routes « publiques » sont définies par défaut sur l’équilibreur de charge oublic Azure Standard Load Balancer (vous pouvez modifier cela).
    * Les routes « privées » sont définies par défaut sur l’équilibreur de charge interne (vous pouvez modifier cela).

## <a name="network-security-groups"></a>Groupes de sécurité réseau
Les groupes de sécurité réseau sont créés dans le groupe de ressources du nœud, qui est verrouillé. Les groupes de sécurité réseau sont attribués directement aux sous-réseaux et non aux cartes réseau du nœud. Les groupes de sécurité réseau sont immuables, ce qui signifie que vous n’avez pas l’autorisation de les modifier. 

Toutefois, avec un serveur d’API publiquement visible, vous ne pouvez pas créer les groupes de sécurité réseau et les affecter aux cartes réseau.

## <a name="domain-forwarding"></a>Transfert de domaine
Azure Red Hat OpenShift utilise CoreDNS. Le transfert de domaine peut être configuré (consultez la documentation sur l’[utilisation du transfert DNS](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator) pour plus d’informations).

Actuellement, vous ne pouvez pas apporter votre propre DNS dans vos réseaux virtuels.


Pour plus d’informations sur le trafic sortant et sur ce qu’Azure Red Hat OpenShift prend en charge pour la sortie, consultez la documentation sur les [stratégies de prise en charge](support-policies-v4.md).
