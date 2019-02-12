---
title: Planification de votre système Avere vFXT - Azure
description: Explique la planification à effectuer avant de déployer Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: a097110bac7dad630f9a85dd8b20678db0c739cf
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744654"
---
# <a name="plan-your-avere-vfxt-system"></a>Planifier votre système Avere vFXT

Cet article explique comment planifier un nouveau cluster vFXT Avere pour Azure dont la position et la taille répondent à vos besoins. 

Avant d’accéder à la Place de marché Azure ou de créer des machines virtuelles, vous devez réfléchir à la façon dont le cluster va interagir avec les autres éléments dans Azure. Vous devez prévoir l’emplacement des ressources de cluster dans votre réseau privé et vos sous-réseaux, ainsi que celui de votre stockage back-end. Vérifiez que les nœuds de cluster que vous créez sont suffisamment puissants pour prendre en charge votre flux de travail. 

Lisez la suite pour en savoir plus.

## <a name="resource-group-and-network-infrastructure"></a>Groupe de ressources et infrastructure réseau

Pensez à l’emplacement des éléments de votre déploiement vFXT Avere pour Azure. Le diagramme suivant montre une disposition possible pour les composants Avere vFXT pour Azure :

![Diagramme montrant le contrôleur de cluster et les machines virtuelles de cluster dans un sous-réseau Les limites du sous-réseau sont entourées des limites d’un réseau virtuel. Dans le réseau virtuel, un hexagone représente le point de terminaison du service de stockage. Celui-ci est relié par une flèche pointillée à un stockage Blob situé en dehors du réseau virtuel.](media/avere-vfxt-components-option.png)

Suivez ces instructions pour planifier l’infrastructure réseau de votre système Avere vFXT :

* Tous les éléments doivent être gérés avec un nouvel abonnement créé pour le déploiement d’Avere vFXT. Voici les avantages : 
  * Simplification du suivi des coûts : visualisez et auditez tous les coûts des ressources, de l’infrastructure et des cycles de calcul dans un seul abonnement.
  * Simplification du nettoyage : vous pouvez supprimer l’ensemble de l’abonnement une fois que vous en avez terminé avec le projet.
  * Partitionnement des quotas de ressources : protégez d’autres charges de travail critiques contre une éventuelle limitation des ressources quand vous importez le grand nombre de clients utilisés pour votre flux de travail informatique à hautes performances en isolant le cluster et les clients Avere vFXT dans un abonnement unique.

* Recherchez les systèmes de calcul clients qui sont proches du cluster vFXT. Le stockage back-end, quant à lui, peut être plus distant.  

* Par souci de simplicité, placez le cluster vFXT et la machine virtuelle du contrôleur de cluster dans le même réseau virtuel et dans le même groupe de ressources. Ils doivent également utiliser le même compte de stockage. (Le contrôleur de cluster crée le cluster et peut aussi être utilisé pour la gestion du cluster par le biais de la ligne de commande.)  

  > [!NOTE] 
  > Le modèle de création de cluster peut créer un nouveau groupe de ressources et un nouveau compte de stockage pour le cluster. Vous pouvez spécifier un groupe de ressources existant à condition qu’il soit vide.

* Le cluster doit se trouver dans son propre sous-réseau afin d’éviter tout conflit d’adresses IP avec des clients ou des ressources de calcul. 

## <a name="ip-address-requirements"></a>Exigences relatives aux adresses IP 

Vérifiez que le sous-réseau de votre cluster dispose d’une plage d’adresses IP suffisamment étendue pour prendre en charge le cluster. 

Le cluster Avere vFXT utilise les adresses IP suivantes :

* Une adresse IP de gestion de cluster. Cette adresse peut se déplacer d’un nœud à un autre dans le cluster. Toutefois, elle est toujours disponible pour que vous puissiez vous connecter à l’outil de configuration du panneau de configuration Avere.
* Pour chaque nœud du cluster :
  * Au moins une adresse IP exposée aux clients (toutes les adresses exposées aux clients sont gérées par le *vserver* du cluster, qui peut les déplacer d’un nœud à l’autre en fonction des besoins).
  * Une adresse IP pour la communication de cluster
  * Une adresse IP d’instance (affectée à la machine virtuelle)

Si vous utilisez le stockage Blob Azure, les adresses IP du réseau virtuel de votre cluster peuvent également être nécessaires :  

* Un compte de stockage Blob Azure nécessite au moins cinq adresses IP. Gardez cette nécessité à l’esprit si vous placez le stockage Blob dans le même réseau virtuel que votre cluster.
* Si vous utilisez le stockage Blob Azure situé en dehors du réseau virtuel pour votre cluster, vous devez créer un point de terminaison de service de stockage à l’intérieur du réseau virtuel. Ce point de terminaison n’utilise pas d’adresse IP.

Vous avez la possibilité de placer les ressources réseau et le stockage Blob (si utilisé) dans des groupes de ressources différents du cluster.

## <a name="vfxt-node-sizes"></a>Tailles des nœuds vFXT 

Les machines virtuelles qui servent de nœuds de cluster déterminent le débit des requêtes et la capacité de stockage de votre cache. Vous pouvez choisir entre deux types d’instances, avec différentes caractéristiques de mémoire, de processeur et de stockage local. 

Chaque nœud vFXT est identique. Autrement dit, si vous créez un cluster à trois nœuds, vous aurez trois machines virtuelles de même type et de même taille. 

| Type d’instance | Processeurs virtuels | Mémoire  | Stockage SSD local  | Disques de données max. | Débit du disque non mis en cache | Cartes réseau (nombre) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D16s_v3 | 16  | 64 Gio  | 128 Go  | 32 | 25 600 IOPS <br/> 384 Mbits/s | 8 000 Mbits/s (8) |
| Standard_E32s_v3 | 32  | 256 Gio | 512 Go  | 32 | 51 200 IOPS <br/> 768 Mbits/s | 16 000 Mbits/s (8)  |

La taille du cache de disque par nœud est configurable et peut aller de 1 000 Go à 8 000 Go. La taille de cache recommandée pour chaque nœud Standard_D16s_v3 est de 1 To, et elle est de 4 To pour chaque nœud Standard_E32s_v3.

Pour plus d’informations sur ces machines virtuelles, lisez les documents Microsoft Azure suivants :

* [Tailles de machine virtuelle à usage général](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general)
* [Tailles de machine virtuelle à mémoire optimisée](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Quota des comptes

Vérifiez que votre abonnement a la capacité nécessaire pour exécuter le cluster Avere vFXT, ainsi que tous les systèmes clients et systèmes de calcul utilisés. Pour plus d’informations, lisez [Quota pour le cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster).

## <a name="back-end-data-storage"></a>Stockage de données back-end

Où le cluster Avere vFXT doit-il stocker vos données quand elles ne sont pas dans le cache ? Décidez si votre plage de travail sera stockée à long terme dans un nouveau conteneur d’objets blob ou dans un système de stockage matériel ou cloud existant. 

Si vous souhaitez utiliser le stockage Blob Azure pour le back-end, vous devez créer un conteneur dans le cadre de la création du cluster vFXT. Cette option crée et configure le nouveau conteneur pour qu’il soit prêt à être utilisé dès que le cluster est prêt. 

Pour plus d’informations, consultez [Créer le déploiement Avere vFXT pour Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure).

> [!NOTE]
> Seuls des conteneurs de stockage Blob vides peuvent être utilisés en tant que systèmes de stockage principaux (core filer) pour le système Avere vFXT. vFXT doit pouvoir gérer son magasin d’objets sans avoir à préserver les données existantes. 
>
> Pour savoir comment copier des données vers le conteneur du cluster à l’aide des ordinateurs clients et du cache Avere vFXT, lisez [Déplacement de données vers le cluster vFXT](avere-vfxt-data-ingest.md).

Si vous souhaitez utiliser un système de stockage local existant, vous devez l’ajouter au cluster vFXT après sa création. Pour obtenir des instructions détaillées sur l’ajout d’un système de stockage existant au cluster vFXT Avere, lisez [Configurer le stockage](avere-vfxt-add-storage.md).

## <a name="cluster-access"></a>Accès au cluster 

Le cluster Avere vFXT pour Azure se trouve sur un sous-réseau privé et n’a pas d’adresse IP publique. Vous devez disposer d’une méthode d’accès au sous-réseau privé pour l’administration du cluster et les connexions clientes. 

Les options d’accès disponibles sont les suivantes :

* Hôte de saut : affectez une adresse IP publique à une machine virtuelle distincte sur le réseau privé, et utilisez-la pour créer un tunnel SSL vers les nœuds de cluster. 

  > [!TIP]
  > Si vous définissez une adresse IP publique sur le contrôleur de cluster, vous pouvez l’utiliser en tant qu’hôte de saut. Pour plus d’informations, consultez [Contrôleur de cluster en tant qu’hôte de saut](#cluster-controller-as-jump-host).

* Réseau privé virtuel (VPN) : configurez un VPN point à site ou site à site vers votre réseau privé.

* Azure ExpressRoute : configurez une connexion privée par le biais d’un partenaire ExpressRoute. 

Pour plus d’informations sur ces options, lisez la [documentation des réseaux virtuels Azure sur la communication Internet](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Contrôleur de cluster en tant qu’hôte de saut

Si vous définissez une adresse IP publique sur le contrôleur de cluster, vous pouvez l’utiliser en tant qu’hôte de saut (« jump host ») pour contacter le cluster Avere vFXT à partir d’un emplacement en dehors du sous-réseau privé. Toutefois, ce contrôleur disposant de privilèges d’accès pour modifier des nœuds de cluster, cela crée un faible risque de sécurité.  

Pour améliorer la sécurité avec une adresse IP publique, utilisez un groupe de sécurité réseau afin d’autoriser l’accès entrant uniquement par le biais du port 22. Le cas échéant, vous pouvez renforcer la protection du système en verrouillant l’accès à votre plage d’adresses IP sources, c’est-à-dire autoriser uniquement les connexions des machines que vous avez l’intention d’utiliser pour l’accès au cluster.

Quand vous créez le cluster, vous pouvez choisir s’il faut créer une adresse IP publique sur le contrôleur de cluster. 

* Si vous créez un réseau virtuel ou un sous-réseau, une adresse IP publique est affectée au contrôleur de cluster.
* Si vous sélectionnez un réseau virtuel et un sous-réseau existants, le contrôleur de cluster aura uniquement des adresses IP privées. 

## <a name="next-step-understand-the-deployment-process"></a>Étape suivante : Comprendre le processus de déploiement

La [vue d’ensemble du déploiement](avere-vfxt-deploy-overview.md) donne une idée globale de toutes les étapes nécessaires à la création d’un système Avere vFXT pour système Azure, ainsi qu’à sa préparation en vue de fournir des données.  