---
title: Limitations pour les pools de nœuds Windows Server dans Azure Kubernetes Service (ACS)
description: En savoir plus sur les limitations connues lors de l’exécution des charges de travail et les pools de nœuds Windows Server dans Azure Kubernetes Service (ACS)
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: twhitney
ms.openlocfilehash: 12fb9dc67e8afae3dcb9ade97dd61ab438e0fac5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475401"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitations actuelles pour les pools de nœuds Windows Server et des charges de travail dans Azure Kubernetes Service (ACS)

Dans Azure Kubernetes Service (ACS), vous pouvez créer un pool de nœud qui exécute Windows Server en tant que le système d’exploitation invité sur les nœuds. Ces nœuds peuvent exécuter des applications de conteneur Windows natives, telles que celles reposant sur le .NET Framework. Comme il existe des différences majeures de comment Linux et du système d’exploitation Windows fournit la prise en charge du conteneur, certains Kubernetes courantes et les fonctionnalités liées à pod ne sont pas actuellement disponibles pour les pools de nœuds Windows.

Cet article présente quelques-unes des limitations et des concepts de système d’exploitation pour les nœuds de Windows Server dans ACS. Pools de nœuds pour Windows Server sont actuellement en version préliminaire.

> [!IMPORTANT]
> Fonctionnalités de préversion AKS sont en libre-service, participer. Elles sont fournies pour recueillir des commentaires et des bogues à partir de notre communauté. Dans la version préliminaire, ces fonctionnalités ne sont pas destinées à des fins de production. Fonctionnalités en version préliminaire publique relèvent du « meilleur effort » la prise en charge. L’assistance des équipes de support technique AKS est disponible pendant les heures de bureau PST fuseau horaire (PST) uniquement. Pour plus d’informations, consultez les éléments suivants prennent en charge des articles :
>
> * [Stratégies de prise en charge AKS][aks-support-policies]
> * [FAQ du Support Azure][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Limitations pour Windows Server dans Kubernetes

Conteneurs Windows Server doivent s’exécuter sur un ordinateur hôte de conteneur basé sur Windows. Pour exécuter des conteneurs Windows Server dans ACS, vous pouvez [créer un pool de nœud qui exécute Windows Server] [ windows-node-cli] en tant que le système d’exploitation invité. Prise en charge de fenêtre Server nœud pool inclut certaines limitations qui font partie de Windows Server en amont dans le projet de Kubernetes. Ces limitations ne sont pas spécifiques à AKS. Pour plus d’informations sur cette prise en charge en amont pour Windows Server dans Kubernetes, consultez [conteneurs Windows Server dans les limites de Kubernetes][upstream-limitations].

Les limitations suivantes en amont pour les conteneurs Windows Server dans Kubernetes sont pertinentes pour AKS :

- Conteneurs Windows Server permet uniquement à Windows Server 2019, qui correspond au nœud Windows Server sous-jacente du système d’exploitation.
    - Images de conteneur créées à l’aide de Windows Server 2016 comme le système d’exploitation de base ne sont pas pris en charge.
- Conteneurs privilégiés ne peut pas être utilisés.
- Fonctionnalités spécifiques de Linux telles que des fonctionnalités RunAsUser, SELinux, AppArmor ou POSIX ne sont pas disponibles dans les conteneurs Windows Server.
    - Limitations de système de fichiers qui sont spécifiques à Linux telles que UUI/GUID, par des autorisations utilisateur également ne sont pas disponibles dans les conteneurs Windows Server.
- Disques Azure et les fichiers Azure sont les types de volumes pris en charge, accédés en tant que volumes NTFS dans le conteneur Windows Server.
    - Le stockage NFS / volumes ne sont pas pris en charge.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Limitations d’ACS pour les pools de nœuds Windows Server

Les limitations supplémentaires suivantes s’appliquent à la prise en charge du pool de nœuds Windows Server dans ACS :

- Un cluster AKS contient toujours un pool de nœuds Linux en tant que le premier pool de nœud. Impossible de supprimer ce premier pool de nœud basés sur Linux, sauf si le cluster AKS lui-même est supprimé.
- Actuellement, AKS prend uniquement en charge l’équilibrage de charge de base, ce qui permet uniquement pour un pool principal, le pool de nœud Linux par défaut. Par conséquent, le trafic sortant à partir de pods de Windows sera toujours [traduit en adresse gérés par Azure publique IP][azure-outbound-traffic]. Étant donné que cette adresse IP n’est pas configurable, il n’est pas possible actuellement d’une liste verte le trafic provenant de pods de Windows. 
- Les clusters AKS doivent utiliser le modèle de mise en réseau (Avancé) de Azure CNI.
    - Mise en réseau de Kubenet (basic) n’est pas pris en charge. Impossible de créer un cluster AKS qui utilise kubenet. Pour plus d’informations sur les différences dans les modèles de réseau, consultez [réseau concepts pour les applications dans ACS][azure-network-models].
    - Le modèle de réseau Azure CNI nécessite une planification supplémentaire et considérations relatives à la gestion des adresses IP. Pour plus d’informations sur la façon de planifier et implémenter Azure CNI, consultez [mise en réseau de configurer Azure CNI dans AKS][configure-azure-cni].
- Les nœuds de Windows Server dans ACS doivent être *mis à niveau* vers une dernière version de Windows Server 2019 pour maintenir le dernier correctif logiciel corrige et met à jour. Mises à jour de Windows ne sont pas activées dans l’image de nœud de base dans ACS. Selon une planification régulière autour de cycle de mise à jour de Windows et votre propre processus de validation, vous devez effectuer une mise à niveau sur l’ou les pools nœud Windows Server dans votre cluster AKS. Pour plus d’informations sur la mise à niveau d’un pool de nœud Windows Server, consultez [mise à niveau d’un pool de nœuds dans ACS][nodepool-upgrade].
    - Ces mises à niveau du nœud Windows Server consomment temporairement les adresses IP supplémentaires dans le sous-réseau de réseau virtuel comme un nouveau nœud est déployé, avant que l’ancien nœud est supprimé.
    - quotas de processeurs virtuels sont également temporairement consommées dans l’abonnement comme un nouveau nœud est déployé, l’ancien nœud supprimé.
    - Vous ne pouvez pas mettre à jour et gérer automatiquement des redémarrages à l’aide de `kured` comme avec les nœuds Linux dans ACS.
- Le cluster AKS peut avoir un maximum de huit pools de nœuds.
    - Vous pouvez avoir un maximum de 400 nœuds dans ces pools de huit nœuds.
- Le nom de pool de nœud Windows Server a une limite de 6 caractères.
- Fonctionnalités préliminaires dans ACS telles que la stratégie de réseau et cluster autoscaler, ne sont pas approuvées pour les nœuds de Windows Server.
- Les contrôleurs d’entrée doivent être planifiés uniquement sur des nœuds Linux à l’aide d’un NodeSelector.
- Les espaces de développement Azure est actuellement uniquement disponible pour les pools de nœuds basés sur Linux.
- Prise en charge (gMSA) lorsque les nœuds Windows Server ne sont pas joints à un domaine Active Directory n’est pas disponible actuellement dans ACS de comptes de service administrés de groupe.
    - L’open source, en amont [ACS-engine] [ aks-engine] projet fournit actuellement prise en charge du service administré de groupe si vous avez besoin d’utiliser cette fonctionnalité.

## <a name="os-concepts-that-are-different"></a>Concepts du système d’exploitation qui sont différents

Kubernetes est historiquement axée sur Linux. De nombreux exemples utilisés dans la source amont [Kubernetes.io] [ kubernetes] site Web sont destinées à être utilisé sur les nœuds Linux. Lorsque vous créez des déploiements qui utilisent des conteneurs Windows Server, les considérations suivantes à l’appliquer au niveau du système d’exploitation :

- **Identité** -Linux utilise le nom d’utilisateur (UID) et groupID (GID), représentée en tant que types d’entiers. Noms d’utilisateur et de groupe ne sont pas canoniques : ils sont simplement un alias dans */etc/groupes* ou */etc/passwd* vers UID + GID.
    - Windows Server utilise un identificateur de sécurité binaire supérieure (SID) qui est stocké dans la base de données Windows Security Access Manager (SAM). Cette base de données n’est pas partagé entre l’hôte et les conteneurs, ou entre les conteneurs.
- **Autorisations de fichiers** -Windows Server utilise une liste de contrôle d’accès basée sur les SID, plutôt que d’un masque de bits des autorisations et les UID + GID
- **Chemins de fichiers** -convention sur Windows Server consiste à utiliser \ au lieu de /.
    - Dans les spécifications de pod qui montent des volumes, spécifiez le chemin d’accès correctement pour les conteneurs Windows Server. Par exemple, au lieu de montage d’un point de */mnt/volume* dans un conteneur Linux, spécifiez une lettre de lecteur et emplacement tels que */K/Volume* à monter en tant que le *k :* lecteur.

## <a name="next-steps"></a>Étapes suivantes

Prise en main des conteneurs de Windows Server dans ACS, [créer un pool de nœud qui exécute Windows Server dans AKS][windows-node-cli].

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
