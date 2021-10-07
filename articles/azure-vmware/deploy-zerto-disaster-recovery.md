---
title: Déployer Zerto Disaster Recovery sur Azure VMware Solution (disponibilité initiale)
description: Découvrez comment implémenter Zerto Disaster Recovery pour des machines virtuelles VMware locales ou Azure VMware Solution.
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 9e919f8fa22965bdd7a170db2c19bd921de030cd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475290"
---
# <a name="deploy-zerto-disaster-recovery-on-azure-vmware-solution-initial-availability"></a>Déployer Zerto Disaster Recovery sur Azure VMware Solution (disponibilité initiale)

Cet article explique comment implémenter une récupération d’urgence pour des machines virtuelles VMware locales ou Azure VMware Solution. La solution décrite dans cet article utilise [Zerto Disaster Recovery](https://www.zerto.com/solutions/use-cases/disaster-recovery/). Les instances de Zerto sont déployées tant sur le site protégé que sur le site de récupération. 

Zerto est conçu pour réduire le temps d’arrêt des machines virtuelles en cas de sinistre. Grâce à la plateforme de Zerto reposant sur la protection continue des données, la perte de données est minimale, voire nulle. Elle fournit le niveau de protection souhaité pour de nombreuses applications d’entreprise stratégiques et critiques pour l’entreprise. Par ailleurs, Zerto automatise et orchestre le basculement et la restauration automatique, garantissant ainsi un temps d’arrêt minimal en cas de sinistre. En général, Zerto simplifie la gestion grâce à l’automatisation, et veille à ce que les temps de récupération soient brefs et hautement prévisibles.


## <a name="core-components-of-the-zerto-platform"></a>Composants principaux de la plateforme Zerto

| Composant | Description |
| --- | --- |
| **Zerto Virtual Manager (ZVM)**   | Application de gestion pour Zerto, implémentée en tant que service Windows installé sur une machine virtuelle Windows. L’administrateur du cloud privé installe et gère la machine virtuelle Windows. Le ZVM permet de configurer la récupération d’urgence du jour 0 et du jour 2. Cela inclut, par exemple, la configuration des sites principal et de récupération d’urgence, la protection des machines virtuelles, la récupération des machines virtuelles, etc. En revanche, ZVM ne gère pas les données de réplication des machines virtuelles clientes protégées.     |
| **Virtual Replication appliance (vRA)**   | Machine virtuelle Linux pour gérer la réplication des données de la source à la cible de réplication. Une instance de vRA est installée par hôte ESXi, offrant une architecture à une échelle véritable qui augmente et diminue avec les hôtes du cloud privé. La VRA gère la réplication des données sur les machines virtuelles protégées, et depuis celles-ci vers la cible locale ou distante, en stockant les données dans le journal.    |
| **Pilote d’hôte ESXi de Zerto**   | Installé sur chaque hôte ESXi de machine virtuelle VMware configuré pour la récupération d’urgence Zerto. Le pilote d’hôte intercepte les E/S d’une machine virtuelle vSphere, et envoie les données de réplication à la vRA choisie pour cet hôte. La vRA assure ensuite la réplication des données de la machine virtuelle sur une ou plusieurs cibles de récupération d’urgence.    |
| **Zerto Cloud Appliance (ZCA)**   | Machine virtuelle Windows servant uniquement quand la plateforme Zerto est utilisée pour récupérer des machines virtuelles vSphere en tant que machines virtuelles IaaS natives Azure. La ZCA comprend les composants suivants :<ul><li>**ZVM :** service Windows hébergeant l’interface utilisateur et s’intégrant avec les API natives d’Azure pour la gestion et l’orchestration.</li><li>**VRA :** service Windows répliquant les données depuis ou vers Azure.</li></ul>La ZCA s’intégrant en mode natif avec la plateforme sur laquelle elle est déployée, elle vous permet d’utiliser un stockage Blob Azure au sein d’un compte de stockage sur Microsoft Azure. Par conséquent, elle garantit le déploiement le plus rentable sur chacune de ces plateformes.   |
| **Virtual Protection Group (VPG)**   | Groupe logique de machines virtuelles créé sur le ZVM. La plateforme Zerto permet de configurer des stratégies de récupération d’urgence, de sauvegarde et de mobilité sur un VPG. Ce mécanisme permet d’appliquer un ensemble cohérent de stratégies à un groupe de machines virtuelles.  |


Pour en savoir plus sur l’architecture de la plateforme Zerto, consultez le [Guide d’architecture de la plateforme Zerto](https://www.zerto.com/wp-content/uploads/2021/07/Zerto-Platform-Architecture-Guide.pdf). 


## <a name="supported-zerto-scenarios"></a>Scénarios de Zerto pris en charge

Vous pouvez utiliser la plateforme Zerto avec Azure VMware Solution pour les trois scénarios suivants. 

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>Scénario 1 : récupération d’urgence de machine virtuelle VMware locale sur Azure VMware Solution

Dans ce scénario, le site principal est un environnement local basé sur vSphere. Et le site de récupération d’urgence est un cloud privé Azure VMware Solution. 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-1.png" alt-text="Diagramme montrant le scénario 1 pour la solution de récupération d’urgence Zerto sur Azure VMware Solution." border="false":::



### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-cloud-dr"></a>Scénario 2 : récupération d’urgence de cloud Azure VMware Solution sur Azure VMware Solution

Dans ce scénario, le site principal est un cloud privé Azure VMware Solution situé dans une région Azure. Et le site de récupération d’urgence est un cloud privé Azure VMware Solution situé dans une autre région Azure.

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2.png" alt-text="Diagramme montrant le scénario 2 pour la solution de récupération d’urgence Zerto sur Azure VMware Solution." border="false":::


### <a name="scenario-3-azure-vmware-solution-to-iaas-vms-cloud-dr"></a>Scénario 3 : récupération d’urgence de cloud Azure VMware Solution sur machines virtuelles IaaS

Dans ce scénario, le site principal est un cloud privé Azure VMware Solution situé dans une région Azure. Des blobs Azure et des machines virtuelles IaaS Azure (basées sur Hyper-V) sont utilisés en cas de sinistre.

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-3.png" alt-text="Diagramme montrant le scénario 3 pour la solution de récupération d’urgence Zerto sur Azure VMware Solution." border="false":::



## <a name="prerequisites"></a>Prérequis

### <a name="on-premises-vmware-to-azure-vmware-solution-dr"></a>Récupération d’urgence de machine virtuelle VMware locale sur Azure VMware Solution

- Cloud privé de Azure VMware Solution déployé en tant que région secondaire.

- Connectivité VPN ou ExpressRoute entre une installation locale et Azure VMware Solution.



### <a name="azure-vmware-solution-to-azure-vmware-solution-cloud-dr"></a>Récupération d’urgence de cloud Azure VMware Solution sur Azure VMware Solution

- Le cloud privé de Azure VMware Solution doit être déployé dans la région primaire et la région secondaire.

   :::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2a-prerequisite.png" alt-text="Diagramme montrant le premier prérequis pour le scénario 2 de la solution de récupération d’urgence Zerto sur Azure VMware Solution.":::
 
- La connectivité, comme ExpressRoute Global Reach, entre le cloud privé de Azure VMware Solution source et cible.



### <a name="azure-vmware-solution-iaas-vms-cloud-dr"></a>Récupération d’urgence de cloud Azure VMware Solution sur machines virtuelles IaaS Azure

- Connectivité réseau, basée sur ExpressRoute, du cloud Azure VMware Solution au réseau virtuel utilisé pour la récupération d’urgence.   

- Suivez les [instructions d’Azure Enterprise pour la réplication virtuelle Zerto](http://s3.amazonaws.com/zertodownload_docs/Latest/Zerto%20Virtual%20Replication%20Azure%20Enterprise%20Guidelines.pdf) et les autres prérequis.



## <a name="install-zerto-on-azure-vmware-solution"></a>Installer Zerto sur Azure VMware Solution

Actuellement, la récupération d’urgence Zerto sur Azure VMware Solution est en phase de disponibilité initiale (IA). Durant la phase IA, vous devez contacter Microsoft afin de demander un support pour la phase IA et justifier de votre éligibilité pour bénéficier de celui-ci.

Pour solliciter un support pour la phase IA en lien avec la récupération d’urgence Zerto sur Azure VMware Solution, adressez une demande par e-mail à zertoonavs@microsoft.com . Durant la phase IA, Azure VMware Solution prend uniquement en charge l’installation et l’intégration manuelles de Zerto. Toutefois, Microsoft collaborera avec vous pour s’assurer que vous puissiez installer manuellement Zerto sur votre cloud privé.

>[!NOTE]
>La disponibilité générale (GA) d’Azure VMware Solution permettra l’installation en libre-service et les opérations du jour 2 de Zerto sur Azure VMware Solution.


## <a name="configure-zerto-for-disaster-recovery"></a>Configurer Zerto pour la récupération d’urgence

Pour configurer Zerto pour les scénarios de récupération d’urgence de machine virtuelle VMware locale sur Azure VMware Solution et de cloud Azure VMware Solution sur Azure VMware Solution, consultez le [guide d’administration de Zerto Virtual Manager dans un environnement vSphere](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/Zerto%20Virtual%20Manager%20vSphere%20Administration%20Guide.pdf?cb=1629311409).


Pour plus d’informations, consultez la [documentation technique de Zerto](https://www.zerto.com/myzerto/technical-documentation/). Vous pouvez également télécharger tous les guides Zerto regroupés dans le [corpus de documentation PDF de l’outil de recherche v 8.5 pour les logiciels Zerto](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/SEARCH_TOOL.zip?cb=1629311409).



## <a name="ongoing-management-of-zerto"></a>Gestion continue de Zerto

- Lorsque vous mettez à l’échelle vos opérations de cloud privé Azure VMware Solution, il se peut que vous deviez ajouter de nouveaux hôtes Azure VMware Solution pour la protection Zerto, ou configurer la récupération d’urgence Zerto sur de nouveaux clusters vSphere Azure VMware Solution. Dans ces deux scénarios, afin d’ajouter des vRA Zerto pour vos nouveaux hôtes, vous devez ouvrir une demande de support auprès de l’équipe Azure VMware Solution au cours de la phase de disponibilité initiale. Vous pouvez ouvrir la [demande de support](https://rc.portal.azure.com/#create/Microsoft.Support) pour ces configurations du jour 2 à partir du portail Azure.   

   :::image type="content" source="media/zerto-disaster-recovery/support-request-zerto-disaster-recovery.png" alt-text="Capture d’écran montrant la demande de support pour les configurations de récupération d’urgence Zerto du jour 2.":::

- En raison de la nature de l’intégration Zerto-vSphere (utilisant l’affinité forte entre vSphere et le service de réplication de données), les opérations de réduction d’échelle (scale-down) du cluster sont supposées échouer. Les équipes SRE d’Azure VMware Solution remédieront à ces défaillances hors connexion au cours de la phase IA.

- Durant la phase GA, toutes les opérations ci-dessus seront activées en mode libre-service automatisé.


## <a name="faqs"></a>FAQ

### <a name="can-i-use-a-pre-existing-zerto-product-license-on-azure-vmware-solution"></a>Puis-je utiliser une licence de produit Zerto pré-existante sur Azure VMware Solution ?

Vous pouvez réutiliser des licences de produit Zerto préexistantes pour des environnements Azure VMware Solution. Si vous avez besoin de nouvelles licences Zerto, envoyez un e-mail à Zerto à l’adresse **sales@zerto.com** pour en acquérir. 

### <a name="how-is-zerto-supported"></a>Comment fonctionne le support de Zerto ?

Zerto Disaster Recovery est une solution vendue Zerto qui en assure le support. Pour tout problème de support lié à la solution Zerto, contactez toujours le [support Zerto](https://www.zerto.com/company/support-and-service/support/).

Les équipes de support de Zerto et de Microsoft se concerteront le cas échéant pour résoudre des problèmes liés à Zerto sur Azure VMware Solution.

