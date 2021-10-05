---
title: Guide de migration des charges de travail de calcul GPU dans Azure
description: Guide de migration des séries NC, ND et NC v2.
author: iafinder
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 08/15/2020
ms.author: iafinder
ms.openlocfilehash: 574118d9bd8c400eccb48ed551d6059c0dfc7bf2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551678"
---
# <a name="migration-guide-for-gpu-compute-workloads-in-azure"></a>Guide de migration des charges de travail de calcul GPU dans Azure

Étant donné que des GPU plus puissants apparaissent sur la Place de marché et dans les centres de données Microsoft Azure, nous vous recommandons de réévaluer les performances de vos charges de travail et de migrer éventuellement vers des GPU plus récents.

Pour la raison qui vient d’être évoquée ainsi que pour maintenir une offre de service fiable et de haute qualité, Azure met régulièrement hors service le matériel des anciennes tailles de machines virtuelles. Le premier groupe de GPU dont la mise hors service est prévue au sein d’Azure est celui des machines virtuelles d’origine, séries NC, NC v2 et ND avec accélérateurs GPU NVIDIA Tesla K80, P100 et P40, respectivement, pour centre de données. Ces produits seront mis hors service le 31 août 2022, tout comme les plus anciennes machines virtuelles de cette série lancées en 2016.

Depuis, les GPU ont fait des progrès incroyables parallèlement à l’ensemble du secteur du deep learning et du HPC, en dépassant généralement le double des performances d’une génération à l’autre. Depuis le lancement des GPU NVIDIA K80, P40 et P100, Azure a fourni plusieurs nouvelles générations et catégories de machines virtuelles axées sur le calcul accéléré par GPU et par IA. Ces machines virtuelles reposent sur des GPU NVIDIA T4, V100 et A100, qui se différencient par des fonctionnalités facultatives telles que les structures fabric d’interconnexion InfiniBand. Nous encourageons les clients à explorer ces options en tant que chemins de migration.

Dans la plupart des cas, l’augmentation considérable des performances offertes par les nouvelles générations de GPU abaisse le coût TCO global en réduisant la durée d’exécution des travaux (pour les travaux burstables), ou en réduisant la quantité globale de machines virtuelles avec GPU nécessaires pour répondre à une demande de ressources de calcul de taille fixe, même si les coûts par heure de GPU peuvent varier. En plus de ces avantages, les clients peuvent améliorer le délai d’obtention d’une solution en utilisant des machines virtuelles plus performantes. Ils peuvent également améliorer l’intégrité et la prise en charge de leur solution en adoptant des versions plus récentes des logiciels, du runtime CUDA et des pilotes.

## <a name="migration-vs-optimization"></a>Migration ou optimisation

Azure reconnaît que les clients ont une multitude d’impératifs qui peuvent dicter la sélection d’un produit spécifique parmi les machines virtuelles avec GPU. Il s’agit notamment de considérations relatives à l’architecture du GPU, aux interconnexions, au coût TCO, au délai d’obtention d’une solution et à la disponibilité régionale en fonction des impératifs de conformité ou de latence, dans la mesure où certains de ces aspects changent avec le temps.

Dans le même temps, l’accélération GPU est un nouveau domaine dont l’évolution est rapide.

Par conséquent, il n'existe pas d'orientation unique pour ce domaine de produits, et une migration est le moment idéal pour réévaluer les changements potentiellement spectaculaires apportés à une charge de travail - comme le passage d'un modèle de déploiement en grappe à une seule grande VM à 8 GPU ou vice versa, l'exploitation de types de données de précision réduite, l'adoption de fonctionnalités telles que le GPU multi-instance, et bien plus encore.

Les considérations de ce genre, qui sont effectuées dans le contexte d’améliorations déjà considérables des performances des GPU par génération, et où une fonctionnalité telle que l’ajout de cœurs Tensor peut accroître les performances selon un ordre de grandeur, dépendent fortement de la charge de travail.

Combiner la migration au changement d’architecture des applications peut produire une valeur considérable tout en améliorant les coûts et le délai d’obtention d’une solution.

Toutefois, ce genre d’amélioration dépasse le cadre de ce document. En effet, celui-ci se concentre sur les classes d’équivalence directe des charges de travail généralisées pouvant être exécutées aujourd’hui par les clients, afin d’identifier les options de machine virtuelle les plus similaires au niveau du prix *et* des performances de GPU par rapport aux familles de machines virtuelles existantes mises hors service.

Ainsi, ce document part du principe que l’utilisateur n’a peut-être aucun insight ou contrôle sur les propriétés spécifiques à la charge de travail, par exemple le nombre nécessaire d’instances de machine virtuelle, de GPU, d’interconnexions, etc.

## <a name="recommended-upgrade-paths"></a>Chemins de mise à niveau recommandés

### <a name="nc-series-vms-featuring-nvidia-k80-gpus"></a>Machines virtuelles série NC avec GPU NVIDIA K80

Les machines virtuelles de la [série NC (v1)](./nc-series.md) représentent le type le plus ancien de machine virtuelle de calcul avec accélération GPU dans Azure. Elles reposent sur une technologie qui associe un, deux, trois ou quatre accélérateurs GPU NVIDIA Tesla K80 pour centre de données à des processeurs Intel Xeon E5-2690 v3 (Haswell). Elles ont été autrefois un type de machine virtuelle phare pour les applications exigeantes en IA, machine learning et HPC. Elles sont restées longtemps un choix populaire dans le cycle de vie du produit (en particulier via les tarifs promotionnels de la série NC) pour les utilisateurs qui préféraient avoir un coût absolu très faible par heure de GPU par rapport aux GPU ayant un débit par dollar plus élevé.

Aujourd’hui, compte tenu des performances de calcul relativement faibles de la plateforme vieillissante du GPU NVIDIA K80, par rapport à la série de machines virtuelles dotées de GPU plus récents, les charges de travail d’inférence et d’analytique en temps réel représentent un cas d’usage populaire de la série NC, où une machine virtuelle avec accélération doit être disponible dans un état stable pour répondre aux requêtes des applications au fur et à mesure de leur arrivée. Dans les cas de ce genre, le volume ou la taille de lot des requêtes est parfois insuffisant pour pouvoir bénéficier de GPU plus performants. Les machines virtuelles NC sont également populaires auprès des développeurs et des étudiants, qui découvrent, développent ou testent l’accélération GPU, qui ont besoin d’une cible de déploiement CUDA dans le cloud, peu coûteuse et sur laquelle itérer sans qu’il soit nécessaire d’effectuer un passage en production.

En règle générale, les clients des séries NC doivent passer directement des tailles NC aux tailles [NC T4 v3](./nct4-v3-series.md), la nouvelle plateforme d’Azure utilisant l’accélération GPU pour les charges de travail légères avec GPU NVIDIA Tesla T4. Toutefois, d’autres références SKU de machines virtuelles doivent être prises en compte pour les charges de travail s’exécutant sur les tailles de série NC compatibles avec InfiniBand.

| Taille actuelle de machine virtuelle | Taille de machine virtuelle cible | Différence de spécification | 
|---|---|---|
Standard_NC6 <br> Standard_NC6_Promo | Standard_NC4as_T4_v3 <br>ou<br>Standard_NC8as_T4 | Processeur : Intel Haswell comparé à AMD Rome<br>Nombre de GPU : 1 (identique)< br>Génération du GPU : NVIDIA Keppler comparé à Turing (+2 générations, environ 2 fois plus de FLOPS FP32)<br>Mémoire du GPU (Gio par GPU) : 16 (+4)<br>Processeurs virtuels : 4 (-2) ou 8 (+2)<br>Mémoire (Gio) : 16 (-40) ou 56 (identique)<br>Stockage temporaire (SSD) en Gio : 180 (-160) ou 360 (+20)<br>Nombre maximal de disques de données : 8 (-4) ou 16 (+4)<br>Accélération réseau : Oui (+)<br>Stockage Premium : Oui (+)| 
| Standard_NC24<br>Standard_NC24_Promo | Standard_NC64as_T4_v3* | Processeur : Intel Haswell comparé à AMD Rome<br>Nombre de GPU : 4 (identique)<br>Génération du GPU : NVIDIA Keppler comparé à Turing (+2 générations, environ 2 fois plus de FLOPS FP32)<br>Mémoire du GPU (Gio par GPU) : 16 (+4)<br>Processeurs virtuels : 64 (+40)<br>Mémoire (Gio) : 440 (+216)<br>Stockage temporaire (SSD) en Gio : 2 880 (+1 440)<br>Nombre maximal de disques de données : 32 (-32)<br>Accélération réseau : Oui (+)<br>Stockage Premium : Oui (+) | 
|Standard_NC24r<br>Standard_NC24r_Promo<br><br>(Tailles prenant en charge le clustering InfiniBand) | Standard_NC24rs_v3* | Processeur : Intel Haswell comparé à Intel Broadwell<br>Nombre de GPU : 4 (identique)<br>Génération du GPU : NVIDIA Keppler comparé à Volta (+2 générations)<br>Mémoire du GPU (Gio par GPU) : 16 (+4)<br>Processeurs virtuels : 24 (+0)<br>Mémoire (Gio) : 448 (+224)<br>Stockage temporaire (SSD) en Gio : 2 948 (+1 440)<br>Nombre maximal de disques de données : 32 (identique)<br>Accélération réseau : Non (identique)<br>Stockage Premium : Oui (+)<br>Interconnexion InfiniBand : Oui | 


### <a name="nd-series-vms-featuring-nvidia-tesla-p40-gpus"></a>Machines virtuelles série ND avec GPU NVIDIA Tesla P40

Les machines virtuelles de la série ND correspondent à une plateforme de milieu de gamme conçue à l’origine pour les charges de travail d’IA et de deep learning. Elles offrent d’excellentes performances pour l’inférence par lots via des opérations à virgule flottante simple précision améliorées par rapport à leurs prédécesseurs. Elles fonctionnent avec des GPU NVIDIA Tesla P40 et des processeurs Intel Xeon E5-2690 v4 (Broadwell). À l’instar des séries NC et NC v2, la série ND offre une configuration avec un réseau secondaire à faible latence et à haut débit via un accès RDMA ainsi qu’une connectivité InfiniBand, ce qui vous permet d’exécuter des travaux d’entraînement à grande échelle sur de nombreux GPU.

| Taille actuelle de machine virtuelle | Taille de machine virtuelle cible | Différence de spécification | 
|---|---|---|
|Standard_ND6 | Standard_NC4as_T4_v3<br>ou<br>Standard_NC8as_T4 | Processeur : Intel Broadwell comparé à AMD Rome<br>Nombre de GPU : 1 (identique)<br>Génération du GPU : NVIDIA Pascal comparé à Turing (+1 génération)<br>Mémoire du GPU (Gio par GPU) : 16 (-8)<br>Processeurs virtuels : 4 (-2) ou 8 (+2)<br>Mémoire (Gio) : 16 (-40) ou 56 (-56)<br>Stockage temporaire (SSD) en Gio : 180 (-552) ou 360 (-372)<br>Nombre maximal de disques de données : 8 (-4) ou 16 (+4)<br>Accélération réseau : Oui (+)<br>Stockage Premium : Oui (+) | 
| Standard_ND12 | Standard_NC16as_T4_v3 | Processeur : Intel Broadwell comparé à AMD Rome<br>Nombre de GPU : 1 (-1)<br>Génération du GPU : NVIDIA Pascal comparé à Turing (+1 génération)<br>Mémoire du GPU (Gio par GPU) : 16 (-8)<br>Processeurs virtuels : 16 (+4)<br>Mémoire (Gio) : 110 (-114)<br>Stockage temporaire (SSD) en Gio : 360 (-1 114)<br>Nombre maximal de disques de données : 48 (+16)<br>Accélération réseau : Oui (+)<br>Stockage Premium : Oui (+) | 
| Standard_ND24 | Standard_NC64as_T4_v3* | Processeur : Intel Broadwell comparé à AMD Rome<br>Nombre de GPU : 4 (identique)<br>Génération du GPU : NVIDIA Pascal comparé à Turing (+1 génération)<br>Mémoire du GPU (Gio par GPU) : 16 (-8)<br>Processeurs virtuels : 64 (+40)<br>Mémoire (Gio) : 440 (identique)<br>Stockage temporaire (SSD) en Gio : 2 880 (identique)<br>Nombre maximal de disques de données : 32 (identique)<br>Accélération réseau : Oui (+)<br>Stockage Premium : Oui (+) | 
| Standard_ND24r | Standard_NC24rs_v3* | Processeur : Intel Broadwell (identique)<br>Nombre de GPU : 4 (identique)<br>Génération du GPU : NVIDIA Pascal comparé à Volta (+1 génération)<br>Mémoire du GPU (Gio par GPU) : 16 (-8)<br>Processeurs virtuels : 24 (+0)<br>Mémoire (Gio) : 448 (identique)<br>Stockage temporaire (SSD) en Gio : 2 948 (identique)<br>Nombre maximal de disques de données : 32 (identique)<br>Accélération réseau : Non (identique)<br>Stockage Premium : Oui (+)<br>Interconnexion InfiniBand : Oui (identique) | 

### <a name="nc-v2-series-vms-featuring-nvidia-tesla-p100-gpus"></a>Machines virtuelles série NC v2 avec GPU NVIDIA Tesla P100

Les machines virtuelles de la série NC v2 correspondent à une plateforme phare conçue à l’origine pour les charges de travail d’IA et de deep learning. Elles offrent d’excellentes performances pour l’entraînement du deep learning avec des performances par GPU environ 2 fois supérieures à celles de la série NC d’origine. Elles fonctionnent avec des GPU NVIDIA Tesla P100 et des processeurs Intel Xeon E5-2690 v4 (Broadwell). À l’instar des séries NC et ND, la série NC v2 offre une configuration avec un réseau secondaire à faible latence et à haut débit via un accès RDMA ainsi qu’une connectivité InfiniBand, ce qui vous permet d’exécuter des travaux d’entraînement à grande échelle sur de nombreux GPU.

| Taille actuelle de machine virtuelle | Taille de machine virtuelle cible | Différence de spécification | 
|---|---|---|
| Standard_NC6s_v2 | Standard_NC6s_v3 | Processeur : Intel Broadwell (identique)<br>Nombre de GPU : 1 (identique)<br>Génération du GPU : NVIDIA Pascal comparé à Volta (+1 génération)<br>Mémoire du GPU (Gio par GPU) : 16 (identique)<br>Processeurs virtuels : 6 (identique)<br>Mémoire (Gio) : 112 (identique)<br>Stockage temporaire (SSD) en Gio : 736 (identique)<br>Nombre maximal de disques de données : 12 (identique)<br>Accélération réseau : Non (identique)<br>Stockage Premium : Oui (+) | 
| Standard_NC12s_v2 | Standard_NC12s_v3 | Processeur : Intel Broadwell (identique)<br>Nombre de GPU : 2 (identique)<br>Génération du GPU : NVIDIA Pascal comparé à Volta (+1 génération)<br>Mémoire du GPU (Gio par GPU) : 16 (identique)<br>Processeurs virtuels : 12 (identique)<br>Mémoire (Gio) : 112 (identique)<br>Stockage temporaire (SSD) en Gio : 1 474 (identique)<br>Nombre maximal de disques de données : 24 (identique)<br>Accélération réseau : Non (identique)<br>Stockage Premium : Oui (+) | 
| Standard_NC24s_v2 | Standard_NC24s_v3 | Processeur : Intel Broadwell (identique)<br>Nombre de GPU : 4 (identique)<br>Génération du GPU : NVIDIA Pascal comparé à Volta (+1 génération)<br>Mémoire du GPU (Gio par GPU) : 16 (identique)<br>Processeurs virtuels : 24 (identique)<br>Mémoire (Gio) : 448 (identique)<br>Stockage temporaire (SSD) en Gio : 2 948 (identique)<br>Nombre maximal de disques de données : 32 (identique)<br>Accélération réseau : Non (identique)<br>Stockage Premium : Oui (+) | 
| Standard_NC24rs_v2 | Standard_NC24rs_v3* | Processeur : Intel Broadwell (identique)<br>Nombre de GPU : 4 (identique)<br>Génération du GPU : NVIDIA Pascal comparé à Volta (+1 génération)<br>Mémoire du GPU (Gio par GPU) : 16 (identique)<br>Processeurs virtuels : 24 (identique)<br>Mémoire (Gio) : 448 (identique)<br>Stockage temporaire (SSD) en Gio : 2 948 (identique)<br>Nombre maximal de disques de données : 32 (identique)<br>Accélération réseau : Non (identique)<br>Stockage Premium : Oui (+)<br>Interconnexion InfiniBand : Oui (identique)| 


## <a name="migration-steps"></a>Étapes de la migration

### <a name="general-changes"></a>Modifications générales

1.  Choisissez une série et une taille pour la migration. Tirez profit de la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour obtenir des insights supplémentaires.

2.  Obtenez le quota de la série de machines virtuelles cible

3.  Redimensionnez la taille actuelle de machine virtuelle de la série N\* en fonction de la taille cible. Cela peut également être le bon moment pour mettre à jour le système d’exploitation utilisé par votre image de machine virtuelle, ou pour adopter l’une des images HPC avec des pilotes préinstallés en tant que point de départ.

    > [!IMPORTANT]
    > Votre image de machine virtuelle a peut-être été produite avec une ancienne version du runtime CUDA, du pilote NVIDIA et (le cas échéant, pour les tailles compatibles avec l’accès RDMA uniquement) des pilotes Mellanox OFED nécessaires à votre nouvelle série de machines virtuelles avec GPU. Vous pouvez les mettre à jour ici [en suivant les instructions de la documentation Azure.](./sizes-gpu.md)

### <a name="breaking-changes"></a>Dernières modifications

#### <a name="select-target-size-for-migration"></a>Sélectionner la taille cible pour la migration

Après avoir évalué votre utilisation actuelle, déterminez le type de machine virtuelle avec GPU dont vous avez besoin. En fonction des impératifs liés à la charge de travail, vous avez différents choix.

> [!NOTE]
> Parmi les bonnes pratiques, il est recommandé de sélectionner une taille de machine virtuelle en fonction du coût et des performances. Les recommandations de ce guide sont basées sur une comparaison individuelle universelle des métriques de performances ainsi que sur la correspondance la plus proche dans une autre série de machines virtuelles. Avant de choisir la taille appropriée, effectuez une comparaison des coûts à l’aide de la calculatrice de prix Azure.

> [!IMPORTANT]
> Toutes les tailles héritées des séries NC, NC v2 et ND sont disponibles en tailles multiGPU, notamment les tailles à 4 GPU avec et sans interconnexion InfiniBand pour les charges de travail étroitement liées à un scale-out, qui nécessitent plus de puissance de calcul qu’une seule machine virtuelle à 4 GPU, ou un seul GPU K80, P40 ou P100 peut fournir. Bien que les recommandations ci-dessus offrent un chemin direct, les utilisateurs de ces tailles doivent cibler leurs objectifs de performances avec des machines virtuelles plus puissantes basées sur des GPU NVIDIA V100, par exemple les séries [NC v3](./ncv3-series.md) et [ND v2](./ndv2-series.md), qui permettent généralement d’obtenir le même niveau de performance de charge de travail à des coûts inférieurs et avec une facilité de gestion améliorée. Les performances obtenues sont considérablement supérieures par GPU et par machine virtuelle sans qu’il soit nécessaire de recourir à des configurations multiGPU et multinœud.
<br>

#### <a name="get-quota-for-the-target-vm-family"></a>Obtenez le quota de la famille de machines virtuelles cible

Suivez le guide permettant de [demander une augmentation du quota de processeurs virtuels par famille de machines virtuelles.](../azure-portal/supportability/per-vm-quota-requests.md) Sélectionnez la taille de machine virtuelle cible que vous avez choisie pour la migration.

#### <a name="resize-the-current-virtual-machine"></a>Redimensionner la machine virtuelle actuelle

Vous pouvez [redimensionner la machine virtuelle](resize-vm.md). 

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste complète des tailles de machines virtuelles avec GPU, consultez [GPU - Vue d’ensemble du calcul accéléré](sizes-gpu.md)