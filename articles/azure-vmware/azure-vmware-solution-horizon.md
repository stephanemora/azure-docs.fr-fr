---
title: Déployer Horizon sur Azure VMware Solution
description: Découvrez comment déployer VMware Horizon sur Azure VMware Solution.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: bda4be049e360670cb7038bfbb3070c2a5f262c4
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91729047"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Déployer Horizon sur Azure VMware Solution 

>[!NOTE]
>Ce document se concentre sur le produit VMware Horizon. Il était précédemment nommé Horizon 7 avant que le nom du produit ne passe d’Horizon 7 à Horizon. Horizon est une solution différente d’Horizon Cloud sur Azure, bien qu’il existe des composants partagés. Les principaux avantages d’Azure VMware Solution incluent une méthode de dimensionnement plus simple et l’intégration de la gestion des fondements du cloud VMware dans le portail Azure.

[VMware Horizon](https://www.vmware.com/products/horizon.html)® est une plateforme de bureau virtuel et d’applications qui s’exécute dans le centre de données et fournit une gestion simple et centralisée. Elle fournit des bureaux virtuels et des applications aux utilisateurs finaux sur n’importe quel appareil, quel que soit l’endroit où ils se trouvent. Horizon vous permet de créer des connexions à des bureaux virtuels Windows, à des bureaux virtuels Linux Bureau à distance, à des applications hébergées sur un serveur (RDS), à des ordinateurs de bureau et à des ordinateurs physiques, ainsi qu’à des ordinateurs physiques.

Ici, nous nous concentrons spécifiquement sur le déploiement d’Horizon sur Azure VMware Solution. Pour obtenir des informations générales sur VMware Horizon, reportez-vous à la documentation de production d’Horizon :

* [Qu’est-ce que VMware Horizon ?](https://www.vmware.com/products/horizon.html)

* [En savoir plus sur VMware Horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Architecture de référence Horizon](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Avec l’introduction d’Horizon sur Azure VMware Solution, il existe désormais deux solutions d’infrastructure de bureau virtuel sur la plateforme Azure. Le diagramme suivant résume les principales différences à un haut niveau.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Les différences entre Horizon sur Azure VMware Solution et Horizon Cloud sur Azure" border="false":::

Horizon 2006 et versions ultérieures sur la série de versions Horizon 8 prend en charge le déploiement local et le déploiement de solutions VMware Azure. Il existe quelques fonctionnalités d’Horizon qui sont prises en charge localement, mais pas sur Azure VMware Solution. D’autres produits de l’écosystème Horizon sont également pris en charge. Pour plus d’informations, consultez [Parité des fonctionnalités et interopérabilité](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Déployer Horizon dans un cloud hybride

Vous pouvez déployer Horizon dans un environnement cloud hybride lorsque vous utilisez l’architecture de pod cloud (CPA) d’Horizon pour interconnecter des centres de données locaux et des centres de données Azure. La CPA est généralement utilisée pour mettre à l’échelle votre déploiement, créer un cloud hybride et fournir une redondance pour la continuité d'activité et la reprise d'activité. Pour une présentation détaillée des conseils en matière de continuité d’activité pour VMware Horizon, consultez [Développement des environnements Horizon 7 existants](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>La CPA n’est pas un déploiement étendu ; chaque pod est distinct, et tous les serveurs de connexion appartenant à chacun des pods individuels doivent être situés dans un emplacement unique et s’exécuter sur le même domaine de diffusion du point de vue du réseau.

À l’instar d’un centre de données local ou privé, Horizon peut être déployé dans un cloud privé Azure VMware Solution. Dans les sections suivantes, nous aborderons les principales différences en matière de déploiement d’Horizon en local et sur Azure VMware Solution.

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

Une conception d’architecture d’Horizon classique utilise une stratégie de pods et de blocs. Un bloc est un vCenter unique, tandis que plusieurs blocs combinés forment un pod. Un pod Horizon est une unité d’organisation déterminée par les limites de scalabilité d’Horizon. Chaque pod est doté d’un portail de gestion distinct. Par conséquent, une pratique de conception standard consiste à réduire le nombre de pods.

Chaque cloud possède son propre schéma de connectivité réseau. Associée à la mise en réseau du SDDC VMware/NSX Edge, la connectivité réseau d’Azure VMware Solution présente des exigences uniques pour le déploiement d’Horizon, différentes de l’environnement local.

Chaque cloud privé Azure/SDDC est en capacité de gérer 4 000 sessions de bureau ou d’application, ce qui suppose ce qui suit :

* Le trafic de charge de travail s’aligne avec celui du profil de collaborateur de tâche LoginVSI.

* Seul le trafic de protocole est pris en compte, pas les données utilisateur.

* NSX Edge est configuré pour être grand.

>[!NOTE]
>Le profil et les besoins de votre charge de travail peuvent être différents. Par conséquent, les résultats peuvent varier en fonction de votre cas d’utilisation. Les volumes de données utilisateur peuvent réduire les limites d’échelle dans le contexte de votre charge de travail. Dimensionnez et planifiez votre déploiement en conséquence. Pour plus d’informations, consultez les instructions de dimensionnement dans la section [Dimensionnement des hôtes Azure VMware Solution pour les déploiements Horizon](#size-azure-vmware-solution-hosts-for-horizon-deployments).

Compte tenu de la limite maximale du cloud privé Azure/SDDC, nous vous recommandons d’utiliser une architecture de déploiement dans laquelle les serveurs de connexion Horizon et les passerelles d’accès unifié (UAG) VMware s’exécutent dans le réseau virtuel Azure. Cela transforme de fait chaque cloud privé Azure/SDDC en bloc. Cela optimise l’évolutivité d’Horizon lorsqu’il s’exécute sur Azure VMware Solution.

La connexion entre le réseau virtuel Azure et les clouds privés Azure/SDDC doit être configurée avec ExpressRoute FastPath. Le diagramme suivant illustre un déploiement de pod Horizon de base.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Les différences entre Horizon sur Azure VMware Solution et Horizon Cloud sur Azure" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Connectivité réseau pour mettre à l’échelle Horizon sur Azure VMware Solution

Cette section présente l’architecture réseau à un niveau élevé pour la mise à l’échelle d’Horizon sur Azure VMware Solution avec des exemples de déploiement courants. Les éléments de mise en réseau critiques en particulier sont abordés ici.

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Pod Horizon unique sur Azure VMware Solution

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Les différences entre Horizon sur Azure VMware Solution et Horizon Cloud sur Azure" border="false":::

Un pod unique est le scénario de déploiement le plus simple. Dans cet exemple, vous décidez de déployer un seul pod Horizon dans la région USA Est. Étant donné que chaque cloud privé/SDDC est estimé pour gérer le trafic d’approximativement 4 000 sessions de bureau, vous pouvez planifier le déploiement de trois clouds privés/SDDC au maximum.

Ainsi, dans cet exemple, en association avec les machines virtuelles de l’infrastructure d’Horizon déployées dans le réseau virtuel Azure, vous pouvez atteindre 12 000 sessions par pod Horizon en fonction de vos besoins en matière de charge de travail et de données. La connexion entre chaque cloud privé et SDDC avec le réseau virtuel Azure est ExpressRoute FastPath, ainsi aucun trafic est-ouest n’est nécessaire entre les clouds privés.

Voici quelques hypothèses clés pour cet exemple de déploiement de base :

* Vous n’avez pas de module Horizon local que vous souhaitez connecter à ce nouveau pod à l’aide de l’architecture du pod cloud (CPA).

* Les utilisateurs finaux se connectent à leurs bureaux virtuels via Internet (plutôt que par le biais d’un centre de données local).

Dans cet exemple de base, vous pouvez connecter votre contrôleur de domaine Active Directory dans Azure Virtual Network à votre instance Active Directory locale via un réseau VPN ou un circuit ExpressRoute.

Une variante de l’exemple de base abordé peut être la prise en charge de la connectivité pour les ressources locales. Il peut s’agir d’utilisateurs qui accèdent aux bureaux et génèrent du trafic d’application de bureau virtuel ou se connectent à un pod local à l’aide de la CPA.

Le diagramme ci-dessous montre comment procéder. Pour connecter votre réseau d’entreprise au réseau virtuel Azure, vous aurez besoin d’un ExpressRoute. Vous devez également connecter votre réseau d’entreprise à chacun des clouds/SDDC privés à l’aide de Global Reach, ce qui permet la connectivité du SDDC aux ressources ExpressRoute et locales.

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Les différences entre Horizon sur Azure VMware Solution et Horizon Cloud sur Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Plusieurs modules Horizon sur Azure VMware Solution dans plusieurs régions

Pour un autre exemple de pod Horizon, examinons un exemple qui illustre la mise à l’échelle sur plusieurs pods. Dans cet exemple, vous déployez deux modules Horizon dans deux régions différentes et les fédérez à l’aide de la CPA. La configuration réseau est similaire à l’exemple précédent, avec quelques liens supplémentaires entre les régions. 

Vous devez connecter le réseau virtuel Azure de chaque région aux clouds privés/SDDC dans l’autre région, ce qui permet aux serveurs de connexion Horizon qui font partie de la fédération de la CPA de se connecter à tous les bureaux sous gestion. L’ajout de clouds privés/SDDC supplémentaires à cette configuration vous permet de mettre à l’échelle vers 24 000 sessions au total. 

Bien que cet exemple montre plusieurs régions, le même principe s’applique si vous souhaitez déployer deux pods Horizon dans la même région. Notez que vous devez vous assurer que le deuxième pod est déployé dans un *réseau virtuel Azure distinct*. Enfin, comme vous le feriez dans l’exemple à pod unique précédent, vous pouvez connecter votre réseau d’entreprise et votre pod local à cet exemple de pods multiples/région à l’aide des instances d’ExpressRoute et de Global Reach du client.

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Les différences entre Horizon sur Azure VMware Solution et Horizon Cloud sur Azure" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Dimensionnement des hôtes Azure VMware Solution pour les déploiements Horizon 

La méthodologie de dimensionnement d’Horizon sur un hôte s’exécutant dans Azure VMware Solution est plus simple que pour Horizon en local, car l’instance hôte Azure VMware Solution est standardisée. Le dimensionnement précis de l’hôte permet de déterminer le nombre d’hôtes nécessaires à la prise en charge de vos besoins en matière d’infrastructure VDI et est essentiel à la détermination du coût par bureau.

### <a name="azure-vmware-solution-host-instance"></a>Instance d’hôte Azure VMware Solution

* Serveur R640 PowerEdge : LIMITÉ PAR DSS

* 36 cœurs \@2,3 GHz

* 576 Go de RAM

* Contrôleur HBA SAS HBA330 12 Gbits/s (NON RAID)

* SSD SATA 1,92 To à usage mixte 6 Gbits/s 512 lecteur AG 2,5 po, 3 DWPD, 10512 TBW

* Intel 1,6 To, NVMe, Express Flash usage mixte, lecteur SFF 2,5 po, U.2, P4600 avec support

* 2 groupes de disques vSAN : 1,6 x 4 (1,92 To)

### <a name="horizon-sizing-inputs"></a>Entrées de dimensionnement d’Horizon

Pour connaître la charge de travail planifiée, procédez comme suit :

* Nombre de bureaux simultanés

* Processeurs virtuels requis par bureau

* VRAM requise par bureau

* Stockage requis par bureau

En général, les déploiements VDI sont limités par le processeur ou la RAM, car ces facteurs déterminent la taille de l’hôte. Prenons l’exemple suivant pour un type de charge de travail Travailleur du savoir LoginVSI, validé avec les tests de performances :

* Déploiement simultané de 2 000 postes de travail

* 2 processeurs virtuels par bureau.

* 4 Go de vRAM Go par bureau.

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

Si vous déployez à la fois Horizon sur Azure VMware Solution et localement, par exemple pour les cas d’utilisation avec récupération d’urgence, choisissez la licence d’abonnement universelle Horizon. La licence universelle a un coût plus élevé, car elle comprend une licence vSphere pour un déploiement local.

Collaborez avec votre équipe commerciale VMware EUC pour déterminer le coût des licences Horizon en fonction de vos besoins.

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Coût des machines virtuelles de l’infrastructure Horizon sur le réseau virtuel Azure

Sur la base de l’architecture de déploiement standard, les machines virtuelles de l’infrastructure Horizon sont constituées de serveurs de connexion, d’UAG et de gestionnaires de volume d’application, et sont déployées dans le réseau virtuel Azure du client. Des instances Azure natives supplémentaires sont requises pour prendre en charge les services de haute disponibilité (HA), Microsoft SQL ou Microsoft Active Directory (AD) sur Azure. La liste suivante répertorie les instances Azure basées sur un exemple de déploiement de 2 000 bureaux. 

| Composant d'infrastructure Horizon | Instance Azure | Nombre d’instances nécessaires (pour 2 000 bureaux)    | Comment  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| Serveur de connexion                | D4sv3          | 2       | *Comprend 1 instance pour la haute disponibilité*             |    
| UAG                              | F2sv2          | 2       | *Comprend 1 instance pour la haute disponibilité*             |
| Gestionnaire des volumes d’application              | D4sv3          | 2       | *Comprend 1 instance pour la haute disponibilité*             |
| Connecteur cloud                  | D4sv3          | 1       |                                          |
| Contrôleur AD                    | D4sv3          | 2       | *Option pour utiliser le service AD MSFT sur Azure* |
| Base de données MS-SQL                  | D4sv3          | 2       | *Option permettant d’utiliser le service SQL sur Azure*     |
| Partage de fichiers Windows               | D4sv3          |         | *Facultatif*                               |

Le coût de la machine virtuelle d’infrastructure est de 0,36 \$ par utilisateur et par mois pour le déploiement de 2 000 bureaux dans l’exemple ci-dessus. Notez que cet exemple utilise la tarification des instances Azure pour la région USA Est en date de juin 2020. Votre tarification peut varier en fonction de la région, des options sélectionnées et du moment applicable.
