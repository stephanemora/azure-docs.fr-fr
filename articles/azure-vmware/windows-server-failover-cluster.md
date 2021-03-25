---
title: Windows Server Failover Cluster sur Azure VMware Solution vSAN avec disques partagés natifs
description: Configurez Windows Server Failover Cluster (WSFC) sur Azure VMware Solution et tirez parti de solutions pour lesquelles la fonctionnalité WSFC est requise.
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: d667eef00fcad0e3f5243c6ab580e2e8371c6793
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518991"
---
# <a name="windows-server-failover-cluster-on-azure-vmware-solution-vsan-with-native-shared-disks"></a>Windows Server Failover Cluster sur Azure VMware Solution vSAN avec disques partagés natifs

Cet article est consacré à la configuration de Windows Server Failover Cluster sur Azure VMware Solution. L'implémentation présentée dans cet article est fournie à des fins de preuve de concept et en tant que projet pilote. Nous vous recommandons d'utiliser une configuration Cluster-in-a-Box (CIB) jusqu'à ce que les stratégies de positionnement soient disponibles.

Windows Server Failover Cluster (WSFC), anciennement connu sous le nom de Microsoft Service Cluster Service (MSCS), est une fonctionnalité du système d'exploitation (SE) Windows Server. WSFC est une fonctionnalité vitale pour l'entreprise, et elle est nécessaire pour de nombreuses applications. Par exemple, WSFC est requis pour les configurations suivantes :

- SQL Server configuré en tant que :
  - Instance de cluster de basculement (FCI) AlwaysOn, pour la haute disponibilité au niveau de l'instance
  - Groupe de disponibilité (AG) AlwaysOn, pour la haute disponibilité au niveau de la base de données
- Services de fichiers Windows :
  - Partage de fichiers générique exécuté sur le nœud de cluster actif
  - Serveur de fichiers avec montée en puissance parallèle (SOFS), qui stocke les fichiers dans des volumes partagés de cluster (CSV)
  - Espaces de stockage direct (S2D) ; disques locaux utilisés pour créer des pools de stockage sur différents nœuds de cluster

Vous pouvez héberger le cluster WSFC sur différentes instances d'Azure VMware Solution, appelées Cluster-Across-Box (CAB). Vous pouvez également placer le cluster WSFC sur un nœud Azure VMware Solution unique. Cette configuration est connue sous le nom de Cluster-in-a-Box (CIB). Nous vous déconseillons d'utiliser une solution CIB dans le cadre d'une implémentation de production. En cas de défaillance du nœud Azure VMware Solution unique, tous les nœuds de cluster WSFC sont mis hors tension et l'application subit un temps d'arrêt. Dans un cluster de cloud privé, Azure VMware Solution requiert au moins trois nœuds.

Il est important de déployer une configuration WSFC prise en charge. Votre solution devra être prise en charge sur vSphere et avec Azure VMware Solution. VMware fournit un document détaillé consacré à WSFC sur vSphere 6.7, [Configuration du clustering de basculement et du service de cluster Microsoft](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf).

Cet article se concentre sur WSFC sous Windows Server 2016 et Windows Server 2019. Les versions antérieures de Windows Server n'entrent pas dans le cadre du [support standard](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) et ne sont donc pas prises en compte ici.

Vous devez d'abord [créer un cluster WSFC](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster). Pour plus d'informations sur WSFC, consultez [Clustering de basculement dans Windows Server](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview). Utilisez les informations fournies dans cet article pour en savoir plus sur les spécificités d'un déploiement WSFC sur Azure VMware Solution.

## <a name="prerequisites"></a>Prérequis

- Environnement Azure VMware Solution
- Support d'installation du système d'exploitation Microsoft Windows Server

## <a name="reference-architecture"></a>Architecture de référence

Azure VMware Solution offre une prise en charge native de la fonctionnalité WSFC virtualisée. La solution prend en charge les réservations persistantes SCSI-3 (SCSI3PR) au niveau du disque virtuel. Cette prise en charge est nécessaire pour permettre à WSFC d'arbitrer l'accès à un disque partagé entre les nœuds. La prise en charge des réservations SCSI3PR permet de configurer WSFC avec une ressource de disque partagée par les différentes machines virtuelles en mode natif sur les magasins de données vSAN.

Le diagramme suivant illustre l'architecture des nœuds virtuels WSFC sur un cloud privé Azure VMware Solution. Il montre où réside Azure VMware Solution, serveurs virtuels WSFC (encadré rouge) compris, par rapport à la plateforme Azure au sens large. Ce diagramme illustre une architecture hub-and-spoke classique, mais une configuration similaire est possible en utilisant Azure Virtual WAN. Ces deux configurations sont équivalentes en termes de valeur ajoutée apportée par les autres services Azure.

[![Diagramme illustrant l'architecture des nœuds virtuels WSFC sur un cloud privé Azure VMware Solution](media/windows-server-failover-cluster/windows-server-failover-architecture.png)](media/windows-server-failover-cluster/windows-server-failover-architecture.png#lightbox)

## <a name="supported-configurations"></a>Configurations prises en charge

Les configurations suivantes sont actuellement prises en charge :

- Microsoft Windows Server 2012 ou version ultérieure
- Jusqu'à cinq nœuds de clustering de basculement par cluster
- Jusqu'à quatre adaptateurs PVSCSI par machine virtuelle
- Jusqu'à 64 disques par adaptateur PVSCSI

## <a name="virtual-machine-configuration-requirements"></a>Configuration requise pour les machines virtuelles

### <a name="wsfc-node-configuration-parameters"></a>Paramètres de configuration des nœuds WSFC

- Installez les outils VMware les plus récents sur chaque nœud WSFC.
- La combinaison de disques non partagés et partagés sur un même adaptateur SCSI virtuelle n'est pas prise en charge. Par exemple, si le disque système (lecteur C :) est attaché à SCSI0:0, le premier disque partagé sera attaché à SCSI1:0. Comme sur une machine virtuelle ordinaire, le nombre de contrôleurs SCSI virtuels d'un nœud de machine virtuelle de cluster WSFC est limité à quatre (4).
- Les identifiants SCSI des disques virtuels doivent être cohérents sur toutes les machines virtuelles hébergeant les nœuds du même cluster WSFC.

| **Composant** | **Configuration requise** |
| --- | --- |
| Version matérielle de la machine virtuelle | 11 ou version ultérieure pour prendre en charge Live vMotion. |
| Carte d'interface réseau virtuelle | Carte d'interface réseau paravirtualisée VMXNET3 ; activez la  fonctionnalité RSS (Receive Side Scaling) de Windows sur la carte d'interface réseau virtuelle. |
| Mémoire | Utilisez la totalité de la mémoire de réservation des machines virtuelles pour les nœuds du cluster WSFC. |
| Augmentez le délai d'expiration E/S de chaque nœud WSFC. | Remplacez HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet par 60 secondes ou plus. (Si vous recréez le cluster, cette valeur peut être réinitialisée sur sa valeur par défaut ; par conséquent, vous devez à nouveau la modifier). |
| Analyse du fonctionnement du cluster Windows | La valeur du paramètre SameSubnetThreshold de l'analyse du fonctionnement du cluster Windows doit être modifiée pour autoriser au moins 10 pulsations manquées. Il s'agit de [la valeur par défaut dans Windows Server 2016](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834). Cette recommandation s'applique à toutes les applications qui utilisent WSFC, y compris aux disques partagés et non partagés. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>Nœud WSFC - Paramètres de configuration des disques de démarrage


| **Composant** | **Configuration requise** |
| --- | --- |
| Type de contrôleur SCSI | SAS LSI Logic |
| Mode Disque | Les machines |
| Partage de bus SCSI | Aucun |
| Modifiez les paramètres avancés pour utiliser un contrôleur SCSI virtuel hébergeant le périphérique de démarrage. | Ajoutez les paramètres avancés suivants sur chaque nœud WSFC :<br /> scsiX.returnNoConnectDuringAPD = "TRUE"<br />scsiX.returnBusyOnNoConnectStatus = "FALSE"<br />Où X est le numéro d'identification du contrôleur de bus SCSI du périphérique de démarrage. Par défaut, X est défini sur 0. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>Nœud WSFC - Paramètres de configuration des disques partagés


| **Composant** | **Configuration requise** |
| --- | --- |
| Type de contrôleur SCSI | VMware Paravirtualize (PVSCSI) |
| Mode Disque | Indépendant - Persistant (étape 2 dans l'illustration ci-dessous). Avec ce paramètre, tous les disques seront exclus des instantanés. Les instantanés ne sont pas pris en charge pour les machines virtuelles basées sur WSFC. |
| Partage de bus SCSI | Physique (étape 1 dans l'illustration ci-dessous) |
| Indicateur « Multi-writer » | Non utilisé |
| Format de disque | Allocation statique. (EZT (Eager Zeroed Thick) non requis avec vSAN). |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="Capture d'écran de la page Modifier les paramètres du matériel virtuel":::

## <a name="non-supported-scenarios"></a>Scénario non pris en charge

Les fonctionnalités suivantes ne sont pas prises en charge pour WSFC sur Azure VMware Solution :

- Magasins de données NFS
- Espaces de stockage
- vSAN utilisant le service iSCSI
- Cluster étendu vSAN
- EVC (Enhanced vMotion Compatibility)
- vSphere Fault Tolerance (FT)
- Instantanés
- Stockage Live (en ligne) vMotion
- NPIV (N_Port ID Virtualization)

Les modifications à chaud apportées au matériel des machines virtuelles peuvent perturber les pulsations entre les nœuds WSFC.

Les activités suivantes ne sont pas prises en charge et peuvent entraîner un basculement du nœud WSFC :

- Ajout de mémoire à chaud
- Ajout d'UC à chaud
- Utilisation d'instantanés
- Augmentation de la taille d'un disque partagé
- Interruption et reprise de l'état de la machine virtuelle
- Sollicitation excessive de la mémoire entraînant un remplacement d'ESXi ou à une augmentation de la mémoire de la machine virtuelle
- Extension à chaud du fichier VMDK local, même s'il n'est pas associé au contrôleur de partage de bus SCSI

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Configurer WSFC avec des disques partagés sur Azure VMware Solution vSAN

1. Assurez-vous qu'un environnement Active Directory est disponible.
2. Créez des machines virtuelles sur le magasin de données vSAN.
3. Mettez toutes les machines virtuelles sous tension, configurez le nom d'hôte et les adresses IP, joignez toutes les machines virtuelles à un domaine Active Directory, et installez les dernières mises à jour disponibles du système d'exploitation.
4. Installez les outils VMware les plus récents.
5. Activez et configurez la fonctionnalité Windows Server Failover Cluster sur chaque machine virtuelle.
6. Configurez un témoin de cluster pour le quorum (un témoin de partage de fichiers fonctionne très bien).
7. Mettez tous les nœuds du cluster WSFC hors tension.
8. Ajoutez un ou plusieurs contrôleurs SCSI paravirtuels (quatre maximum) à chaque machine virtuelle du cluster WSFC. Utilisez les paramètres fournis aux paragraphes précédents.
9. Sur le premier nœud de cluster, ajoutez tous les disques partagés nécessaires via **Ajouter un périphérique** > **Disque dur**. Le paramètre Partage de disque doit rester défini sur **Non spécifié** (par défaut) et le paramètre Mode Disque sur **Indépendant - Persistant**. Attachez-le au(x) contrôleur(s) créé(s) précédemment.
10. Passez ensuite aux nœuds WSFC restants. Ajoutez les disques créés à l'étape précédente en sélectionnant **Ajouter un périphérique** > **Disque dur existant**. Conservez les mêmes identifiants SCSI de disque sur tous les nœuds WSFC.
11. Mettez le premier nœud WSFC sous tension ; connectez-vous et ouvrez la console de gestion des disques (MMC). Vérifiez que les disques partagés ajoutés peuvent être gérés par le système d'exploitation et qu'ils sont initialisés. Formatez les disques et attribuez-leur une lettre de lecteur.
12. Mettez les autres nœuds WSFC sous tension.
13. Ajoutez le disque au cluster WSFC via l'**Assistant Ajouter un disque**, puis ajoutez-le à un volume partagé de cluster.
14. Testez un basculement à l'aide de l'**Assistant Déplacer un disque** et assurez-vous que le cluster WSFC contenant les disques partagés fonctionne correctement.
15. Exécutez l'**Assistant Validation du cluster** pour vérifier que le cluster et ses nœuds fonctionnent correctement.

    Gardez bien à l'esprit les éléments spécifiques suivants du test de validation du cluster :

       - **Valider la réservation persistante des espaces de stockage**. Si vous n'utilisez pas d'espaces de stockage avec votre cluster (par exemple, sur Azure VMware Solution vSAN), ce test n'est pas applicable. Vous pouvez ignorer tous les résultats du test Valider la réservation persistante des espaces de stockage, y compris cet avertissement. Pour éviter les avertissements, vous pouvez exclure ce test.
        
      - **Valider la communication réseau**. Le test Validation du cluster génère un avertissement indiquant qu'une seule interface réseau est disponible par nœud de cluster. Vous pouvez ignorer cet avertissement. Azure VMware Solution fournit la disponibilité et les performances requises, car les nœuds sont connectés à l'un des segments NSX-T. Toutefois, conservez cet élément dans le cadre du test de validation du cluster, car il validera d'autres aspects de la communication réseau.

16. Créez une règle DRS (Data Replication Service) pour placer les machines virtuelles WSFC sur les mêmes nœuds Azure VMware Solution. Pour ce faire, une règle d'affinité est nécessaire entre l'hôte et la machine virtuelle. Les nœuds de cluster s'exécuteront ainsi sur le même hôte Azure VMware Solution. Pour rappel, cette configuration est fournie en tant que projet pilote jusqu'à ce que les stratégies de positionnement soient disponibles.

    >[!NOTE]
    > Pour cela, vous devez créer un ticket de demande de support. Le support Azure sera en mesure de vous aider.

## <a name="related-information"></a>Informations connexes

- [Clustering de basculement dans Windows Server](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Instructions pour le clustering Microsoft sur vSphere (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [À propos de la configuration du clustering de basculement et du service de cluster Microsoft (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [vSAN 6.7 U3 - WSFC avec disques partagés &amp; Réservations persistantes SCSI-3 (VMware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Limites d’Azure VMware Solution](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez configurer un cluster WSFC dans Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants :

- Configuration de votre nouveau cluster WSFC en ajoutant d'autres applications pour lesquelles la fonctionnalité WSFC est requise. Par exemple, SQL Server et SAP ASCS.
- Configuration d'une solution de sauvegarde.
  - [Configuration d'un serveur de sauvegarde Azure pour Azure VMware Solution](https://docs.microsoft.com/azure/azure-vmware/set-up-backup-server-for-azure-vmware-solution)
  - [Solutions de sauvegarde pour les machines virtuelles Azure VMware Solution](https://docs.microsoft.com/azure/azure-vmware/ecosystem-back-up-vms)
