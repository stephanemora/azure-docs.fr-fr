---
title: Limitations pour les pools de nœuds Windows Server dans Azure Kubernetes Service (AKS)
description: En savoir plus sur les limitations connues lors de l’exécution de charges de travail d’application et de pools de nœuds Windows Server dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 157f890c65efd0de9fa7d8d7aa5cb43b4a902dfa
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615640"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitations actuelles pour les pools de nœuds Windows Server et les charges de travail d’application dans Azure Kubernetes Service (AKS)

Dans Azure Kubernetes Service (AKS), vous pouvez créer un pool de nœuds qui exécute Windows Server en tant que système d’exploitation invité sur les nœuds. Ces nœuds peuvent exécuter des applications de conteneur Windows natives, comme celles reposant sur .NET Framework. Comme il existe des différences majeures dans la façon dont Linux et Windows fournissent la prise en charge des conteneurs, certaines fonctionnalités liées aux pods et Kubernetes courantes ne sont actuellement pas disponibles pour les pools de nœuds Windows.

Cet article présente certaines des limitations et certains concepts de système d’exploitation pour les nœuds Windows Server dans AKS. Les pools de nœuds pour Windows Server sont actuellement en préversion.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont en libre-service et font l’objet d’un abonnement. Les versions préliminaires sont fournies « en l’état », « avec toutes les erreurs » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service (sla) et de la garantie limitée. Les versions préliminaires AKS sont partiellement couvertes par le service clientèle sur la base du meilleur effort. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production. Pour obtenir des informations supplémentaires, veuillez lire les articles de support suivants :
>
> * [Stratégies de support AKS][aks-support-policies]
> * [FAQ du support Azure][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Quels sont les systèmes d’exploitation Windows pris en charge ?

AKS utilise Windows Server 2019 comme version de système d’exploitation hôte et prend en charge uniquement l’isolation des processus. Les images de conteneur générées à l’aide d’autres versions de Windows Server ne sont pas prises en charge. [Compatibilité des versions avec les conteneurs Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Kubernetes est-il différent sur Windows et Linux ?

La prise en charge des pools de nœuds Windows Server comprend certaines limitations qui font partie du projet Windows Server dans Kubernetes en amont. Ces limitations ne sont pas spécifiques à AKS. Pour plus d’informations sur cette prise en charge en amont de Windows Server dans Kubernetes, voir la section [Fonctionnalités et limitations prises en charge][upstream-limitations] du document [Introduction au support Windows dans Kubernetes][intro-windows], dans le projet Kubernetes.

Kubernetes est historiquement axé sur Linux. De nombreux exemples utilisés sur le site web [Kubernetes.io][kubernetes] en amont sont destinés à être utilisés sur des nœuds Linux. Lorsque vous créez des déploiements qui utilisent des conteneurs Windows Server, les considérations suivantes au niveau du système d’exploitation s’appliquent :

- **Identité** - Linux identifie un utilisateur par un identificateur d’utilisateur entier (UID). Un utilisateur dispose également d’un nom d’utilisateur alphanumérique pour se connecter, que Linux convertit en UID de l’utilisateur. De même, Linux identifie un groupe d’utilisateurs par un identificateur de groupe entier (GID) et convertit un nom de groupe en son GID correspondant.
    - Windows Server utilise un identificateur de sécurité binaire (SID) plus long qui est stocké dans la base de données Windows Security Access Manager (SAM). Cette base de données n’est pas partagée entre l’hôte et les conteneurs, ou entre conteneurs.
- **Autorisations de fichiers** - Windows Server utilise une liste de contrôle d’accès basée sur les SID, plutôt qu’un masque de bits d’autorisations et les UID et GID
- **Chemins de fichiers** - La convention sur Windows Server consiste à utiliser \ au lieu de /.
    - Dans les spécifications de pod qui montent des volumes, spécifiez le chemin d’accès correctement pour les conteneurs Windows Server. Par exemple, au lieu d’un point de montage de */mnt/volume* dans un conteneur Linux, spécifiez une lettre de lecteur et un emplacement comme */K/Volume* à monter en tant que lecteur *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Quels sont les types de disques pris en charge pour Windows ?

Les disques Azure et Azure Files sont les types de volumes pris en charge, accédés en tant que volumes NTFS dans le conteneur Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Puis-je exécuter des clusters exclusivement Windows dans AKS ?

Les nœuds principaux (plan de contrôle) dans un cluster AKS étant hébergés par le service AKS, vous ne serez pas exposé au système d’exploitation des nœuds hébergeant les composants principaux. Tous les clusters AKS sont créés avec un premier pool de nœuds par défaut qui est basé sur Linux. Ce pool de nœuds contient les services système nécessaires au fonctionnement du cluster. Il est recommandé d’exécuter au moins deux nœuds dans le premier pool pour garantir la fiabilité de votre cluster et la possibilité d’effectuer des opérations de cluster. Il est impossible de supprimer le premier pool de nœuds Linux, sauf en cas de suppression du cluster AKS.

## <a name="what-network-plug-ins-are-supported"></a>Quels sont les plug-ins réseau pris en charge ?

Les clusters AKS comportant des pools de nœuds doivent utiliser le modèle de mise en réseau (avancé) d’Azure CNI. La mise en réseau Kubenet (de base) n’est pas prise en charge. Pour plus d’informations sur les différences dans les modèles de réseau, consultez [Concepts réseau pour les applications dans AKS][azure-network-models]. - Le modèle de réseau Azure CNI nécessite une planification supplémentaire et considérations relatives à la gestion des adresses IP. Pour plus d’informations sur la façon de planifier et implémenter Azure CNI, consultez [Configurer le réseau Azure CNI dans AKS][configure-azure-cni].

## <a name="can-i-change-the-max--of-pods-per-node"></a>Puis-je modifier le nombre maximal de pods par nœud ?

Il est actuellement nécessaire de définir au maximum 30 pods pour garantir la fiabilité de vos clusters.

## <a name="how-do-patch-my-windows-nodes"></a>Comment corriger mes nœuds Windows ?

Les nœuds Windows Server dans AKS doivent être *mis à niveau* pour obtenir les correctifs et mises à jour les plus récents. Les mises à jour Windows ne sont pas activées sur les nœuds dans AKS. AKS met en production de nouvelles images de pool de nœuds dès que des correctifs sont disponibles. Il incombe aux clients de mettre à niveau les pools de nœuds pour rester à jour en matière de correctifs. Cela est également vrai pour la version de Kubernetes utilisée. Les notes de publication d’AKS indiquent quand de nouvelles versions sont disponibles. Pour plus d’informations sur la mise à niveau d’un pool de nœuds Windows Server, consultez [Upgrade a node pool][nodepool-upgrade] (Mettre à niveau un pool de nœuds).

> [!NOTE]
> L’image Windows Server mise à jour n’est utilisée que si une mise à niveau de cluster (mise à niveau de plan de contrôle) a été effectuée avant la mise à niveau du pool de nœuds.
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Comment faire pivoter le principal du service pour mon pool de nœuds Windows ?

Pendant la préversion, les pools de nœuds Windows ne prennent pas en charge la rotation du principal de service comme limite de la préversion. Pour mettre à jour le principal du service, créez un pool de nœuds Windows, puis migrez vos pods de l’ancien pool vers le nouveau. Une fois cette opération terminée, supprimez l’ancien pool de nœuds.

## <a name="how-many-node-pools-can-i-create"></a>Combien de pools de nœuds puis-je créer ?

Le cluster AKS peut comprendre au maximum huit (8) pools de nœuds. Vous pouvez avoir au maximum 400 nœuds dans ces pools de nœuds. [Limitations de pool de nœuds][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Comment puis-je nommer mes pools de nœuds Windows ?

Le nom ne peut pas compter plus de 6 (six) caractères. Il s’agit d’une limitation actuelle d’AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Toutes les fonctionnalités sont-elles prises en charge avec les nœuds Windows ?

Les stratégies réseau et Kubenet ne sont actuellement pas pris en charge avec des nœuds Windows. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Puis-je exécuter des contrôleurs d’entrée sur des nœuds Windows ?

Oui, un contrôleur d’entrée prenant en charge les conteneurs Windows Server peut s’exécuter sur des nœuds Windows dans AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Puis-je utiliser Azure Dev Spaces avec des nœuds Windows ?

Azure Dev Spaces est actuellement uniquement disponible pour les pools de nœuds Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Mes conteneurs Windows Server peuvent-ils utiliser gMSA ?

Les comptes de service gérés de groupe (gMSA) ne sont actuellement pas pris en charge dans AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Puis-je utiliser Azure Monitor pour des conteneurs avec des nœuds et conteneurs Windows ?

Oui. Toutefois Azure Monitor ne collecte pas de journaux (stdout) de conteneurs Windows. Vous pouvez toujours établir un attachement au flux en direct des journaux stdout à partir d’un conteneur Windows.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Que se passe-t-il si j’ai besoin d’une fonctionnalité qui n’est pas prise en charge ?

Nous nous efforçons d’introduire toutes les fonctionnalités dont vous avez besoin pour Windows dans AKS mais, si vous rencontrez des lacunes, le projet open source [aks-engine][aks-engine] en amont offre un moyen simple et entièrement personnalisable d’exécuter Kubernetes dans Azure, et inclut le support pour Windows. Ne manquez pas de consulter notre [Feuille de route AKS][aks-roadmap] qui présente les fonctionnalités à venir.

## <a name="next-steps"></a>Étapes suivantes

Pour vous lancer avec des conteneurs Windows Server dans AKS, [créez un pool de nœuds qui exécutent Windows Server dans AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
