---
title: Limitations pour les pools de nœuds Windows Server dans Azure Kubernetes Service (AKS)
description: En savoir plus sur les limitations connues lors de l’exécution de charges de travail d’application et de pools de nœuds Windows Server dans Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 501aeb60eba1d94b4c5882a7c6cbfa8d0359e44d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033911"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitations actuelles pour les pools de nœuds Windows Server et les charges de travail d’application dans Azure Kubernetes Service (AKS)

Dans Azure Kubernetes Service (AKS), vous pouvez créer un pool de nœuds qui exécute Windows Server en tant que système d’exploitation invité sur les nœuds. Ces nœuds peuvent exécuter des applications de conteneur Windows natives, comme celles reposant sur .NET Framework. Comme il existe des différences majeures dans la façon dont Linux et Windows fournissent la prise en charge des conteneurs, certaines fonctionnalités liées aux pods et Kubernetes courantes ne sont actuellement pas disponibles pour les pools de nœuds Windows.

Cet article présente certaines des limitations et certains concepts de système d’exploitation pour les nœuds Windows Server dans AKS. Les pools de nœuds pour Windows Server sont actuellement en préversion.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont en libre-service et font l’objet d’un abonnement. Les versions préliminaires sont fournies « en l’état », « avec toutes les erreurs » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service (sla) et de la garantie limitée. Les versions préliminaires AKS sont partiellement couvertes par le service clientèle sur la base du meilleur effort. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour obtenir des informations supplémentaires, veuillez lire les articles de support suivants :
>
> * [Stratégies de support AKS][aks-support-policies]
> * [FAQ du support Azure][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Limitations pour Windows Server dans Kubernetes

Les conteneurs Windows Server doivent s’exécuter sur un hôte conteneur basé sur Windows. Pour exécuter des conteneurs Windows Server dans AKS, vous pouvez [créer un pool de nœuds qui exécute Windows Server][windows-node-cli] en tant que système d’exploitation invité. La prise en charge des pools de nœuds Windows Server comprend certaines limitations qui font partie du projet Windows Server dans Kubernetes en amont. Ces limitations ne sont pas spécifiques à AKS. Pour plus d’informations sur cette prise en charge en amont de Windows Server dans Kubernetes, consultez [Limitations des conteneurs Windows Server dans Kubernetes](https://docs.microsoft.com/azure/aks/windows-node-limitations).

Les limitations suivantes en amont pour les conteneurs Windows Server dans Kubernetes sont pertinentes pour AKS :

- Les conteneurs Windows Server peuvent utiliser uniquement Windows Server 2019, qui correspond au nœud système d’exploitation du nœud Windows Server sous-jacent.
    - Les images de conteneur créées à l’aide de Windows Server 2016 comme système d’exploitation de base ne sont pas prises en charge.
- Les conteneurs privilégiés ne peuvent pas être utilisés.
- Les fonctionnalités spécifiques à Linux telles que RunAsUser, SELinux, AppArmor ou POSIX ne sont pas disponibles dans les conteneurs Windows Server.
    - Les limitations de système de fichiers qui sont spécifiques à Linux, telles que les autorisations par utilisateur et UUI/GUID ne sont également pas disponibles dans les conteneurs Windows Server.
- Les disques Azure et Azure Files sont les types de volumes pris en charge, accédés en tant que volumes NTFS dans le conteneur Windows Server.
    - Le stockage/les volumes basés sur NFS ne sont pas pris en charge.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Limitations d’AKS pour les pools de nœuds Windows Server

Les limitations supplémentaires suivantes s’appliquent à la prise en charge du pool de nœuds Windows Server dans AKS :

- Un cluster AKS contient toujours un pool de nœuds Linux en tant que premier pool de nœuds. Il est impossible de supprimer ce premier pool de nœuds Linux, sauf si le cluster AKS lui-même est supprimé.
- Les clusters AKS doivent utiliser le modèle de mise en réseau Azure CNI (avancée).
    - La mise en réseau Kubenet (de base) n’est pas prise en charge. Il est impossible de créer un cluster AKS qui utilise kubenet. Pour plus d’informations sur les différences dans les modèles de réseau, consultez [Concepts réseau pour les applications dans AKS][azure-network-models].
    - Le modèle de réseau Azure CNI nécessite une planification supplémentaire et considérations relatives à la gestion des adresses IP. Pour plus d’informations sur la façon de planifier et implémenter Azure CNI, consultez [Configurer le réseau Azure CNI dans AKS][configure-azure-cni].
- Les nœuds Windows Server dans AKS doivent être *mis à niveau* vers la dernière version de Windows Server 2019 pour maintenir les dernières versions des correctifs et mises à jour. Les mises à jour de Windows ne sont pas activées dans l’image de nœud de base dans AKS. Suivez une planification régulière basée sur le cycle de mise à jour de Windows et votre propre processus de validation pour effectuer une mise à niveau sur le ou les pools de nœuds Windows Server dans votre cluster AKS. Pour plus d’informations sur la mise à niveau d’un pool de nœuds Windows Server, consultez [Mettre à niveau un pool de nœuds dans AKS][nodepool-upgrade].
    - Ces mises à niveau de nœuds Windows Server consomment temporairement des adresses IP supplémentaires dans le sous-réseau virtuel lorsqu’un nouveau nœud est déployé, avant que l’ancien nœud soit supprimé.
    - Les quotas de processeurs virtuels sont également temporairement consommés dans l’abonnement lorsqu’un nouveau nœud est déployé, et avant que l’ancien nœud soit supprimé.
    - Vous ne pouvez pas mettre à jour et gérer automatiquement les redémarrages à l’aide de `kured` comme avec les nœuds Linux dans AKS.
- Le cluster AKS peut avoir un maximum de huit pools de nœuds.
    - Vous pouvez avoir un maximum de 400 nœuds dans ces huit pools de nœuds.
- Le nom de pool de nœud Windows Server a une limite de 6 caractères.
- Les fonctionnalités en préversion dans AKS, telles que la stratégie réseau et l’autoscaler de cluster ne sont pas conseillées pour les nœuds Windows Server.
- Les contrôleurs d’entrée doivent être planifiés uniquement sur les nœuds Linux à l’aide d’un NodeSelector.
- Azure Dev Spaces est actuellement uniquement disponible pour les pools de nœuds Linux.
- La prise en charge des comptes de service administré de groupe (gMSA) lorsque les nœuds Windows Server ne sont pas joints à un domaine Active Directory n’est pas disponible actuellement dans AKS.
    - Le projet [aks-engine][aks-engine] open source en amont fournit actuellement la prise en charge de gMSA si vous avez besoin d’utiliser cette fonctionnalité.

## <a name="os-concepts-that-are-different"></a>Différences de concept entre les systèmes d’exploitation

Kubernetes est historiquement axé sur Linux. De nombreux exemples utilisés sur le site web [Kubernetes.io][kubernetes] en amont sont destinés à être utilisés sur des nœuds Linux. Lorsque vous créez des déploiements qui utilisent des conteneurs Windows Server, les considérations suivantes au niveau du système d’exploitation s’appliquent :

- **Identité** - Linux utilise l’userID (UID) et le groupID (GID), représentés en tant que types entiers. Les noms d’utilisateur et de groupe ne sont pas canoniques : ce sont simplement des alias dans */etc/groupes* ou */etc/passwd* qui désignent l’UID et le GID.
    - Windows Server utilise un identificateur de sécurité binaire (SID) plus long qui est stocké dans la base de données Windows Security Access Manager (SAM). Cette base de données n’est pas partagée entre l’hôte et les conteneurs, ou entre conteneurs.
- **Autorisations de fichiers** - Windows Server utilise une liste de contrôle d’accès basée sur les SID, plutôt qu’un masque de bits d’autorisations et les UID et GID
- **Chemins de fichiers** - La convention sur Windows Server consiste à utiliser \ au lieu de /.
    - Dans les spécifications de pod qui montent des volumes, spécifiez le chemin d’accès correctement pour les conteneurs Windows Server. Par exemple, au lieu d’un point de montage de */mnt/volume* dans un conteneur Linux, spécifiez une lettre de lecteur et un emplacement comme */K/Volume* à monter en tant que lecteur *K:* .

## <a name="next-steps"></a>Étapes suivantes

Pour vous lancer avec des conteneurs Windows Server dans AKS, [créez un pool de nœuds qui exécutent Windows Server dans AKS][windows-node-cli].

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
