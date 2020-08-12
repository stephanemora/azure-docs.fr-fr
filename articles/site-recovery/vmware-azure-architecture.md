---
title: Architecture de la récupération d’urgence des machines virtuelles VMware dans Azure Site Recovery
description: Cet article offre une vue d’ensemble de l’architecture et des composants utilisés pour configurer la reprise d’activité de machines virtuelles VMware locales sur Azure avec Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 4b1b8a0cfa98d48d7cb92474c1572f17c79ffd0d
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498950"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Architecture pour la reprise d’activité de VMware sur Azure

Cet article décrit l’architecture et les processus utilisés quand vous déployez la réplication et le basculement pour la reprise d’activité de machines virtuelles VMware entre un site VMware local et Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Composants architecturaux

Le tableau et le graphique suivants présentent une vue générale des composants utilisés pour la reprise d’activité de VMware sur Azure.

**Composant** | **Prérequis** | **Détails**
--- | --- | ---
**Microsoft Azure** | Un abonnement Azure, un compte de Stockage Azure pour le cache, un disque managé et un réseau Azure. | Les données répliquées à partir de machines virtuelles locales sont stockées dans Stockage Azure. Les machines virtuelles Azure sont créées avec les données répliquées quand vous exécutez un basculement depuis le site local vers Azure. Les machines virtuelles Azure se connectent au réseau virtuel Azure lors de leur création.
**Machine du serveur de configuration** | Une seule machine locale. Nous vous recommandons de l’exécuter en tant que machine virtuelle VMware pouvant être déployée à partir d’un modèle OVF téléchargé.<br/><br/> La machine exécute tous les composants locaux de Site Recovery, y compris le serveur de configuration, le serveur de processus et le serveur cible maître. | **Serveur de configuration** : coordonne la communication entre les ordinateurs locaux et Azure, et gère la réplication des données.<br/><br/> **Serveur de traitement**: Installé par défaut sur le serveur de configuration. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement, et les envoie vers le stockage Azure. De plus, le serveur de processus installe le service Mobilité d’Azure Site Recovery sur les machines virtuelles que vous voulez répliquer, et effectue une découverte automatique sur les machines locales. À mesure que s’étend votre déploiement, vous pouvez ajouter des serveurs de traitement distincts afin de gérer de plus grands volumes de trafic de réplication.<br/><br/> **Serveur cible maître** : Installé par défaut sur le serveur de configuration. Il gère les données de réplication pendant la restauration automatique à partir d’Azure. Pour les déploiements à grande échelle, vous pouvez ajouter un serveur cible maître distinct à des fins de restauration automatique.
**Serveurs VMware** | Les machines virtuelles VMware sont hébergées sur des serveurs vSphere ESXi locaux. Nous recommandons d’utiliser un serveur vCenter pour gérer les hôtes. | Pendant le déploiement de Site Recovery, vous ajoutez des serveurs VMware au coffre Recovery Services.
**Machines répliquées** | Le service Mobilité est installé sur chaque machine virtuelle VMware que vous répliquez. | Nous vous recommandons d’autoriser l’installation automatique à partir du serveur de processus. Vous pouvez également installer le service manuellement, ou utiliser une méthode de déploiement automatisée, telle que Configuration Manager.

![Diagramme montrant les relations architecturales de la réplication VMware vers Azure.](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Configurer la connectivité réseau sortante

Pour que Site Recovery fonctionne comme prévu, vous devez modifier la connectivité réseau sortante pour permettre au réseau d’effectuer la réplication.

> [!NOTE]
> Site Recovery ne prend pas en charge l’utilisation d’un proxy d’authentification pour contrôler la connectivité réseau.

### <a name="outbound-connectivity-for-urls"></a>Connectivité sortante pour les URL

Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, autorisez l’accès à ces URL :

| **Nom**                  | **Commercial**                               | **Secteur public**                                 | **Description** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Stockage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Permet d’écrire les données dans le compte de stockage de cache dans la région source à partir de la machine virtuelle. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fournit l’autorisation et l’authentification aux URL du service Site Recovery. |
| Réplication               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permet à la machine virtuelle de communiquer avec le service Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permet à la machine virtuelle d’écrire des données de surveillance et de diagnostic Site Recovery. |

## <a name="replication-process"></a>Processus de réplication

1. Lorsque vous activez la réplication pour une machine virtuelle, la réplication initiale vers Stockage Azure commence, conformément à la stratégie de réplication spécifiée. Notez les points suivants :
    - Pour les machines virtuelles VMware, la réplication se fait au niveau du bloc, presque en continu, à l’aide de l’agent du service Mobilité en cours d’exécution sur la machine virtuelle.
    - Tous les paramètres de la stratégie de réplication sont appliqués :
        - **Seuil d’objectif de point de récupération**. Ce paramètre n’affecte pas la réplication. Il aide à la supervision. Un événement est déclenché, et un e-mail peut être envoyé, si le RPO en cours dépasse le seuil spécifié.
        - **Rétention des points de récupération**. Ce paramètre spécifie le moment auquel vous souhaitez revenir lors d’une interruption. La durée maximale de rétention sur le stockage premium est de 24 heures. Sur le stockage standard, elle est de 72 heures. 
        - **Instantanés de cohérence d’application**. Un instantané de cohérence des applications peut être pris toutes les 1 à 12 heures, en fonction de vos besoins en applications. Les instantanés sont des instantanés d’objet blob Azure standard. L’agent Mobilité en cours d’exécution sur une machine virtuelle demande un instantané VSS conformément à ce paramètre, et insère un signet à ce point dans le temps pour en faire un point de cohérence avec l’application dans le flux de réplication.

2. Le trafic est répliqué sur des points de terminaison publics de stockage Azure via Internet. L’autre solution consiste à utiliser Azure ExpressRoute avec [peering Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). La réplication du trafic à partir d’un site local vers Azure via un réseau VPN de site à site n’est pas prise en charge.
3. L’opération de réplication initiale garantit que toutes les données présentes sur l’ordinateur au moment de l’activation de la réplication sont envoyées à Azure. La réplication des modifications delta dans Azure commence à l’issue de la réplication initiale. Le suivi des modifications d’une machine est envoyé au serveur de traitement.
4. La communication s’effectue comme suit :

    - Les machines virtuelles communiquent avec le serveur de configuration local sur le port HTTPS 443 entrant, pour la gestion de la réplication.
    - Le serveur de configuration orchestre la réplication avec Azure sur le port HTTPS 443 sortant.
    - Les machines virtuelles envoient des données de réplication au serveur de traitement (s’exécutant sur l’ordinateur du serveur de configuration) sur le port HTTPS 9443 entrant. Ce port peut être modifié.
    - Le serveur de traitement reçoit les données de réplication, les optimise et les chiffre, puis les envoie au stockage Azure via le port 443 sortant.
5. Les données de réplication se trouvent tout d’abord dans un compte de stockage de cache dans Azure. Ces journaux sont traités et les données sont stockées dans un disque managé Azure (appelé disque seed asr). Les points de récupération sont créés sur ce disque.

![Diagramme montrant le processus de réplication VMware vers Azure.](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Processus de resynchronisation

1. Parfois, pendant la réplication initiale ou lors du transfert de changements d’ordre différentiel, il peut y avoir des problèmes de connectivité réseau entre la machine source et le serveur de traitement ou entre le serveur de traitement et Azure. L’un ou l’autre de ces problèmes peut momentanément entraîner des échecs de transfert de données vers Azure.
2. Pour éviter les problèmes d’intégrité des données et réduire les coûts de transfert de données, Site Recovery marque un ordinateur pour la resynchronisation.
3. Une machine peut également être marquée pour la resynchronisation dans des situations comme celles qui suivent pour maintenir la cohérence entre la machine source et les données stockées dans Azure :
    - Si une machine subit un arrêt forcé
    - Si une machine subit des changements de configuration comme le redimensionnement de disque (en modifiant la taille du disque de 2 à 4 To)
4. La resynchronisation envoie uniquement les données différentielles à Azure. Le transfert de données entre un site local et Azure est réduit en calculant les sommes de contrôle de données entre la machine source et les données stockées dans Azure.
5. Par défaut, la resynchronisation est planifiée pour s’exécuter automatiquement en dehors des heures de bureau. Si vous ne souhaitez pas attendre la resynchronisation par défaut en dehors des heures de bureau, vous pouvez resynchroniser une machine virtuelle manuellement, Pour ce faire, accédez au portail Azure, sélectionnez la machine virtuelle > **Resynchroniser**.
6. Si la resynchronisation par défaut échoue en dehors des heures de bureau et qu’une intervention manuelle est nécessaire, une erreur est générée sur la machine spécifique dans le portail Azure. Vous pouvez résoudre l’erreur et déclencher la resynchronisation manuellement.
7. Une fois la resynchronisation terminée, la réplication des modifications différentielles reprend.

## <a name="failover-and-failback-process"></a>Processus de basculement et de restauration automatique

Après avoir configuré la réplication et exécuté une simulation de reprise après sinistre (test de basculement) pour vérifier que tout fonctionne comme prévu, vous pouvez exécuter un basculement et une restauration automatique en fonction de vos besoins.

1. Vous pouvez basculer un seul ordinateur ou créer des plans de récupération pour basculer plusieurs machines virtuelles en même temps. Utiliser un plan de récupération plutôt que de basculer un seul ordinateur présente les avantages suivants :
    - Vous pouvez modéliser des dépendances d’application en incluant toutes les machines virtuelles sur l’application dans un seul plan de récupération.
    - Vous pouvez ajouter des scripts, des runbooks Azure et mettre en pause pour effectuer des actions manuelles.
2. Après avoir déclenché le basculement initial, validez-le pour accéder à la charge de travail à partir de la machine virtuelle Azure.
3. Lorsque votre site local principal est à nouveau disponible, vous pouvez vous préparer pour la restauration automatique. Pour effectuer la restauration automatique, vous devez configurer une infrastructure de restauration automatique, notamment les éléments suivants :

    * **Serveur de traitement temporaire dans Azure** : pour effectuer une restauration automatique à partir d’Azure, configurez une machine virtuelle Azure faisant office de serveur de traitement pour gérer la réplication à partir d’Azure. Vous pourrez supprimer cette machine virtuelle une fois la restauration terminée.
    * **Connexion VPN** : pour la restauration automatique, vous avez besoin d’une connexion VPN (ou ExpressRoute) du réseau Azure vers le site local.
    * **Serveur cible maître distinct** : par défaut, le serveur cible maître local qui a été installé avec le serveur de configuration, sur la machine virtuelle VMware locale, gère la restauration automatique. Si vous avez besoin de restaurer automatiquement de grands volumes de trafic, configurez un autre serveur cible maître local dédié.
    * **Stratégie de restauration automatique** : pour répliquer vers votre site local, vous avez besoin d’une stratégie de restauration automatique. Cette stratégie est automatiquement créée en même temps que la stratégie de réplication depuis le site local vers Azure.
4. Une fois les composants en place, la restauration automatique s’effectue en trois actions :

    - Étape 1 : Reprotégez les machines virtuelles Azure afin qu’elles répliquent à partir d’Azure vers les machines virtuelles VMware locales.
    -  Étape 2 : Exécutez un basculement vers le site local.
    - Étape 3 : Une fois les charges de travail automatiquement restaurées, vous réactivez la réplication pour les machines virtuelles locales.
    
 

![Diagramme montrant la restauration automatique VMware à partir d’Azure.](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Étapes suivantes

Suivez [ce didacticiel](vmware-azure-tutorial.md) pour savoir comment activer la réplication VMware vers Azure.
