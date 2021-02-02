---
title: Déployer Horizon sur Azure VMware Solution
description: Découvrez comment déployer VMware Horizon sur Azure VMware Solution.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 2cf6fc5cb7662188650365cb019774d6c778d405
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684873"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Déployer Horizon sur Azure VMware Solution 

>[!NOTE]
>Ce document se concentre sur le produit VMware Horizon, anciennement connu sous le nom d'Horizon 7. Horizon est une solution différente d’Horizon Cloud sur Azure, bien qu’il existe des composants partagés. Les principaux avantages d'Azure VMware Solution sont les suivants : méthode de dimensionnement plus simple et intégration de la gestion VMware Cloud Foundation sur le portail Azure.

[VMware Horizon](https://www.vmware.com/products/horizon.html)® est une plateforme de bureaux virtuels et d'applications qui s'exécute dans le centre de données et offre une gestion simple et centralisée. Elle fournit des bureaux virtuels et des applications sur tous types d'appareils, où qu'ils se trouvent. Horizon vous permet de créer et de répartir des connexions à des bureaux virtuels Windows et Linux, à des applications hébergées par les services Bureau à distance (RDS), à des appareils de bureau et à des ordinateurs physiques.

Ici, nous nous concentrons spécifiquement sur le déploiement d’Horizon sur Azure VMware Solution. Pour obtenir des informations générales sur VMware Horizon, reportez-vous à la documentation de production d’Horizon :

* [Qu’est-ce que VMware Horizon ?](https://www.vmware.com/products/horizon.html)

* [En savoir plus sur VMware Horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Architecture de référence Horizon](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Avec l’introduction d’Horizon sur Azure VMware Solution, il existe désormais deux solutions d’infrastructure de bureau virtuel sur la plateforme Azure. Le diagramme suivant résume les principales différences à un haut niveau.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Horizon sur Azure VMware Solution et Horizon Cloud sur Azure" border="false":::

Horizon 2006 et versions ultérieures sur la série de versions Horizon 8 prend en charge le déploiement local et le déploiement de solutions VMware Azure. Il existe quelques fonctionnalités d’Horizon qui sont prises en charge localement, mais pas sur Azure VMware Solution. D’autres produits de l’écosystème Horizon sont également pris en charge. Pour plus d’informations, consultez [Parité des fonctionnalités et interopérabilité](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Déployer Horizon dans un cloud hybride

Vous pouvez déployer Horizon dans un environnement cloud hybride lorsque vous utilisez l'architecture CPA (Cloud Pod Architecture) d'Horizon pour interconnecter des centres de données locaux et des centres de données Azure. L'architecture CPA effectue un scale-up de votre déploiement, crée un cloud hybride et assure la redondance pour la continuité d'activité et la reprise d'activité.  Pour plus d'informations, consultez [Extension des environnements Horizon 7 existants](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>La CPA n’est pas un déploiement étendu ; chaque pod est distinct, et tous les serveurs de connexion appartenant à chacun des pods individuels doivent être situés dans un emplacement unique et s’exécuter sur le même domaine de diffusion du point de vue du réseau.

À l’instar d’un centre de données local ou privé, Horizon peut être déployé dans un cloud privé Azure VMware Solution. Dans les sections suivantes, nous examinerons les principales différences entre le déploiement d'Horizon en local et sur Azure VMware Solution.

Le cloud privé Azure est identique dans le concept au SDDC (centre de données défini par logiciel) de VMware, un terme généralement utilisé dans la documentation d’Horizon. Le reste de ce document utilise les termes cloud privé Azure et SDDC VMware de façon interchangeable.

Le connecteur Horizon Cloud est requis pour Horizon sur Azure VMware Solution pour gérer les licences d’abonnement. Le connecteur cloud peut être déployé dans un réseau virtuel Azure avec des serveurs de connexion Horizon.

>[!IMPORTANT]
>La prise en charge du plan de contrôle d’Horizon sur Azure VMware Solution n’est pas encore disponible. Veillez à télécharger la version VHD du connecteur Horizon Cloud.

## <a name="vcenter-cloud-admin-role"></a>Rôle d’administrateur du cloud vCenter

Étant donné qu’Azure VMware Solution est un service SDDC et qu’Azure gère le cycle de vie de la solution SDDC sur Azure VMware, le modèle d’autorisation vCenter sur Azure VMware Solution est limité par nature.

Les clients doivent utiliser le rôle d’administrateur cloud, qui dispose d’un ensemble limité d’autorisations vCenter. Le produit Horizon a été modifié pour fonctionner avec le rôle d’administrateur cloud sur Azure VMware Solution, plus précisément :

* La configuration de clonage instantané a été modifiée pour s’exécuter sur Azure VMware Solution.

* Une stratégie vSAN spécifique (VMware_Horizon) a été créée sur Azure VMware Solution pour fonctionner avec Horizon ; elle doit être disponible et utilisée dans les SDDC déployés pour Horizon.

* Le cache de lecture basé sur le contenu (CBRC) vSphere, également connu sous le nom de View Storage Accelerator, est désactivé lors de l’exécution sur Azure VMware Solution.

>[!IMPORTANT]
>Le CBRC ne doit pas être réactivé.

>[!NOTE]
>Azure VMware Solution configure automatiquement les paramètres spécifiques à Horizon tant que vous déployez Horizon 2006 (aussi appelé Horizon 8) ou une version ultérieure sur la branche Horizon 8 et que vous sélectionnez l’option **Azure** dans le programme d’installation du serveur de connexion Horizon.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Horizon sur l’architecture de déploiement Azure VMware Solution

Une conception d’architecture d’Horizon classique utilise une stratégie de pods et de blocs. Un bloc est un vCenter unique, tandis que plusieurs blocs combinés forment un pod. Un pod Horizon est une unité d’organisation déterminée par les limites de scalabilité d’Horizon. Chaque pod Horizon est doté d'un portail de gestion distinct. Par conséquent, une pratique de conception standard consiste à réduire le nombre de pods.

Chaque cloud possède son propre schéma de connectivité réseau. Associée à la mise en réseau du SDDC VMware/NSX Edge, la connectivité réseau d’Azure VMware Solution présente des exigences uniques pour le déploiement d’Horizon, différentes de l’environnement local.

Chaque cloud privé Azure/SDDC peut gérer 4 000 sessions de bureau ou d'application, à condition que :

* Le trafic de la charge de travail soit aligné sur le profil du collaborateur associé à la tâche LoginVSI.

* Seul le trafic de protocole est pris en compte, pas les données utilisateur.

* NSX Edge est configuré pour être grand.

>[!NOTE]
>Le profil et les besoins de votre charge de travail peuvent être différents. Par conséquent, les résultats peuvent varier en fonction de votre cas d’utilisation. Les volumes de données utilisateur peuvent réduire les limites d’échelle dans le contexte de votre charge de travail. Dimensionnez et planifiez votre déploiement en conséquence. Pour plus d’informations, consultez les instructions de dimensionnement dans la section [Dimensionnement des hôtes Azure VMware Solution pour les déploiements Horizon](#size-azure-vmware-solution-hosts-for-horizon-deployments).

Compte tenu de la limite maximale du cloud privé Azure et du SDDC, nous vous recommandons d'utiliser une architecture de déploiement dans laquelle les instances d'Horizon Connection Server et de VMware Unified Access Gateway (UAG) sont exécutées au sein du Réseau virtuel Azure. Chaque cloud privé Azure et chaque SDDC seront ainsi transformés en bloc, ce qui optimisera la scalabilité d'Horizon lors de son exécution sur Azure VMware Solution.

La connexion entre le réseau virtuel Azure et les clouds privés Azure/SDDC doit être configurée avec ExpressRoute FastPath. Le diagramme suivant illustre un déploiement de pod Horizon de base.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Déploiement standard d’un pod à l’aide d’ExpressPath FastPath" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Connectivité réseau pour mettre à l’échelle Horizon sur Azure VMware Solution

Cette section présente l'architecture réseau à un niveau élevé, avec des exemples de déploiement courants, pour faciliter la mise à l'échelle d'Horizon sur Azure VMware Solution. L'accent est mis sur les éléments critiques de la mise en réseau. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Pod Horizon unique sur Azure VMware Solution

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Pod Horizon unique sur Azure VMware Solution" border="false":::

Le scénario de déploiement d'un pod Horizon unique est le plus simple, car vous ne déployez qu'un seul pod Horizon dans la région USA Est.  Comme chaque cloud privé/SDDC peut gérer 4 000 sessions de bureau, vous déployez la taille maximale du pod Horizon.  Vous pouvez au maximum planifier le déploiement de trois clouds privés/SDDC.

Avec un déploiement des machines virtuelles de l'infrastructure d'Horizon sur le Réseau virtuel Azure, vous pouvez atteindre 12 000 sessions par pod Horizon. La connexion entre chaque cloud privé/SDDC et le Réseau virtuel Azure s'effectue via ExpressRoute FastPath.  Aucun trafic est-ouest n'est nécessaire entre les clouds privés. 

Voici quelques hypothèses clés pour cet exemple de déploiement de base :

* Vous n’avez pas de module Horizon local que vous souhaitez connecter à ce nouveau pod à l’aide de l’architecture du pod cloud (CPA).

* Les utilisateurs finaux se connectent à leurs bureaux virtuels via Internet (plutôt que par le biais d'un centre de données local).

Sur le Réseau virtuel Azure, vous connectez votre contrôleur de domaine AD à votre instance d'AD locale via un réseau VPN ou un circuit ExpressRoute.

Une variante de l'exemple de base pourrait consister à prendre en charge la connectivité pour les ressources locales. Par exemple, il pourrait s'agir d'utilisateurs qui accèdent aux appareils de bureau et génèrent du trafic d'application de bureau virtuel ou se connectent à un pod local à l'aide de l'architecture CPA.

Le diagramme montre comment prendre en charge la connectivité pour les ressources locales. Pour connecter votre réseau d'entreprise au Réseau virtuel Azure, vous aurez besoin d'un circuit ExpressRoute.  Vous devrez également connecter votre réseau d'entreprise à chacun des cloud privés et des SDDC à l'aide d'ExpressRoute Global Reach.  Celui-ci permet la connectivité entre le SDDC et le circuit ExpressRoute et les ressources locales. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Connectez votre réseau d’entreprise à un réseau virtuel Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Plusieurs modules Horizon sur Azure VMware Solution dans plusieurs régions

Un autre scénario consiste à procéder à la mise à l'échelle d'Horizon sur plusieurs pods.  Dans ce scénario, vous déployez deux pods Horizon dans deux régions différentes et les fédérez à l'aide de l'architecture CPA. La configuration réseau est semblable à celle de l'exemple précédent, mais avec quelques liaisons supplémentaires entre les régions. 

Vous connecterez le Réseau virtuel Azure de chaque région aux clouds privés/SDDC de l'autre région. Cela permettra aux serveurs de connexion Horizon qui font partie de la fédération CPA de se connecter à tous les appareils de bureau sous gestion. L'ajout de clouds privés/SDDC supplémentaires à cette configuration vous permettrait de passer à 24 000 sessions au total. 

Les mêmes principes s'appliquent si vous déployez deux pods Horizon dans la même région.  Veillez à déployer le deuxième pod Horizon sur un *Réseau virtuel Azure distinct*. Comme dans l'exemple à pod unique, vous pouvez connecter votre réseau d'entreprise et votre pod local à cet exemple de pods multiples/région à l'aide d'ExpressRoute et de Global Reach. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Plusieurs modules Horizon sur Azure VMware Solution dans plusieurs régions" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Dimensionnement des hôtes Azure VMware Solution pour les déploiements Horizon 

La méthodologie de dimensionnement d'Horizon sur un hôte exécuté dans Azure VMware Solution est plus simple qu'en local.  Cela est dû au fait que l'hôte d'Azure VMware Solution est standardisé.  Le dimensionnement exact de l'hôte permet de déterminer le nombre d'hôtes nécessaires pour répondre à vos besoins en matière d'infrastructure VDI.  Il est essentiel pour déterminer le coût par appareil de bureau.

### <a name="sizing-tables"></a>Dimensionnement des tables

Les exigences spécifiques des processeurs virtuels/VRAM pour les bureaux virtuels Horizon dépendent du profil de charge de travail spécifique du client.   Collaborez avec vos équipes commerciales MSFT et VMware pour vous aider à déterminer vos exigences en matière de processeurs virtuels/VRAM pour vos bureaux virtuels. 

| Processeur virtuel par machine virtuelle | VRAM par machine virtuelle (Go) | Instance | 100 machines virtuelles | 200 machines virtuelles | 300 machines virtuelles | 400 machines virtuelles | 500 machines virtuelles | 600 machines virtuelles | 700 machines virtuelles | 800 machines virtuelles | 900 machines virtuelles | 1 000 machines virtuelles | 2 000 machines virtuelles | 3 000 machines virtuelles | 4 000 machines virtuelles | 5 000 machines virtuelles | 6 000 machines virtuelles | 6 400 machines virtuelles |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3,5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3,5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3,5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3,5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Entrées de dimensionnement d’Horizon

Voici ce que vous devrez collecter pour votre charge de travail planifiée :

* Nombre de bureaux simultanés

* Processeurs virtuels requis par bureau

* VRAM requise par bureau

* Stockage requis par bureau

En général, les déploiements VDI sont soumis à des contraintes de processeur ou de RAM, ce qui détermine la taille de l'hôte. Prenons l’exemple suivant pour un type de charge de travail Travailleur du savoir LoginVSI, validé avec les tests de performances :

* Déploiement simultané de 2 000 postes de travail

* 2 processeurs virtuels par bureau.

* 4 Go de vRAM par appareil de bureau.

* 50 Go de stockage par bureau

Pour cet exemple, le nombre total d’hôtes a pour valeur 18, ce qui donne une densité de machines virtuelles par hôte de 111.

>[!IMPORTANT]
>Les charges de travail des clients seront différentes de cet exemple de travailleur du savoir LoginVSI. Dans le cadre de la planification de votre déploiement, collaborez avec vos ingénieurs VMware EUC pour répondre à vos besoins spécifiques en matière de dimensionnement et de performances. Veillez à exécuter vos propres tests de performances sur la base de la charge de travail réelle et planifiée avant de finaliser le dimensionnement de l’hôte et de l’ajuster en conséquence.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Frais de licence Horizon sur Azure VMware Solution 

Il y a quatre composants pour les coûts globaux d’exécution d’Horizon sur Azure VMware Solution. 

### <a name="azure-vmware-solution-capacity-cost"></a>Coût de la capacité Azure VMware Solution

Pour plus d’informations sur la tarification, consultez la page [Tarification d’Azure VMware Solution](https://azure.microsoft.com/pricing/details/azure-vmware/)

### <a name="horizon-licensing-cost"></a>Coût des licences Horizon

Deux licences sont disponibles pour une utilisation avec Azure VMware Solution, avec un utilisateur simultané (CCU) ou un utilisateur nommé (NU) :

* Licence d’abonnement Horizon

* Licence d’abonnement universelle Horizon

Si vous ne prévoyez de déployer qu’Horizon sur Azure VMware Solution dans un avenir proche, utilisez la licence d’abonnement Horizon, car elle est moins coûteuse.

S'il est déployé sur Azure VMware Solution et localement, comme dans un cas d'usage de récupération d'urgence, choisissez la licence d'abonnement universelle Horizon. Celle-ci comprend une licence vSphere pour le déploiement local, ce qui se traduit par un coût plus élevé.

Collaborez avec votre équipe commerciale VMware EUC pour déterminer le coût des licences Horizon en fonction de vos besoins.

### <a name="azure-instance-types"></a>Types d’instances Azure

Pour comprendre les tailles de machine virtuelle Azure qui sont nécessaires pour l’infrastructure Horizon, reportez-vous aux instructions de VMware qui se trouvent [ici](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution).

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus concernant VMware Horizon sur Azure VMware Solution, consultez le [FAQ VMware Horizon](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf).
