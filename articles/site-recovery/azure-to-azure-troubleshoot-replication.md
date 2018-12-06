---
title: Dépannage d’Azure Site Recovery en cas de problème ou d’erreur de réplication Azure vers Azure | Microsoft Docs
description: Dépannage des erreurs et des problèmes lors de la réplication de machines virtuelles Azure pour la récupération d’urgence
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: asgang
ms.openlocfilehash: 22ea3d955fe2910dc99ab4015165008da899d48e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312848"
---
# <a name="troubleshoot-azure-to-azure-vm-ongoing-replication-issues"></a>Résoudre les problèmes de réplication de machine virtuelle Azure vers Azure en cours

Cet article décrit les problèmes courants dans Azure Site Recovery lors de la réplication et de la récupération de machines virtuelles Azure d’une région vers une autre, et explique comment les résoudre. Pour plus d’informations sur les configurations prises en charge, consultez la [matrice de prise en charge pour la réplication des machines virtuelles Azure](site-recovery-support-matrix-azure-to-azure.md).


## <a name="recovery-points-not-getting-generated"></a>Les points de récupération ne sont pas générés

MESSAGE D’ERREUR : Aucun point de récupération d’incident cohérent disponible pour la machine virtuelle pendant les 60 dernières minutes.</br>
ID D’ERREUR : 153007 </br>

Azure Site Recovery réplique des données de manière cohérente de la région source vers la région de récupération d’urgence et crée un point d’incident cohérent toutes les 5 minutes. Si Site Recovery ne peut pas créer de points de récupération pendant 60 minutes, il avertit l’utilisateur. Voici des causes possibles de cette erreur :

**Cause 1 : [taux élevé de changement de données sur la machine virtuelle source](#high-data-change-rate-on-the-source-virtal-machine)**    
**Cause 2 : [Problème de connectivité réseau](#Network-connectivity-issue)**

## <a name="causes-and-solutions"></a>Causes et solutions

### <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Taux élevé de changement de données sur la machine virtuelle source
Azure Site Recovery déclenche un événement si le taux de changement de données sur la machine virtuelle source dépasse les limites prises en charge. Pour vérifier si le problème est dû à un taux d’activité de données élevé, accédez à Éléments répliqués > Machine virtuelle > et cliquez sur « Événements-dernières 72 heures ».
Vous devez voir l’événement « Taux de changement de données au-delà des limites prises en charge », comme le montre la capture d’écran ci-dessous

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Si vous cliquez sur l’événement, vous devez voir les informations du disque exactement comme indiqué dans la capture d’écran ci-dessous

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


#### <a name="azure-site-recovery-limits"></a>Limites Azure Site Recovery
Le tableau suivant présente les limites d’Azure Site Recovery. Les limites sont basées sur nos tests, mais ne peuvent pas couvrir toutes les combinaisons d’E/S d’application possibles. Les résultats réels varient en fonction de la combinaison d’E/S de votre application. Il faut également noter qu'il y a deux limites à prendre en compte, le taux d’activité de données par disque et le taux d’activité de données par machine virtuelle.
Par exemple, si nous examinons le disque Premium P20 dans le tableau ci-dessous, Site Recovery peut gérer un taux d’activité de données de 5 Mo/s et par disque pour cinq disques maximum par machine virtuelle en raison de la limite de taux d’activité total de données de 25 Mo/s par machine virtuelle.

**Stockage de réplication cible** | **Taille d’E/S moyenne de disque source** |**Activité des données moyenne de disque source** | **Total de l’activité des données de disque source par jour**
---|---|---|---
Stockage Standard | 8 Ko | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 8 Ko  | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 16 Ko | 4 Mo/s |  336 Go par disque
Disque Premium P10 ou P15 | 32 Ko ou plus | 8 Mo/s | 672 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 8 Ko    | 5 Mo/s | 421 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 16 Ko ou plus |10 Mo/s | 842 Go par disque

### <a name="solution"></a>Solution
Il faut savoir que les limites du taux de changement de données d’Azure Site Recovery dépendent du type de disque. Pour savoir si ce problème est récurrent ou momentané, il est important de trouver le modèle du taux de changement de données de la machine virtuelle concernée.
Pour trouver le taux de changement de données de la machine virtuelle concernée. Accédez à la machine virtuelle source > mesures sous surveillance et ajoutez les mesures comme indiqué ci-dessous.

![high_data_change_rate](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Cliquez sur « Ajouter une mesure » et ajoutez « Octets écrits/s sur disque de système d’exploitation » et « Octets écrits/s sur disque de données ».
2. Surveiller le pic comme indiqué dans la capture d’écran.
3. Il indique le total des opérations en écriture sur le disque du système d’exploitation et sur tous les disques de données combinés. Ces mesures ne peuvent pas vous indiquer les informations au niveau de chaque disque, mais elles sont un bon indicateur du modèle d’attrition totale des données.

Dans de tels cas, s’il s’agit d’une rafale de données occasionnelle, que le taux de changement de données est supérieur à 10 Mbits/s (pour Premium) et à 2 Mbits/s (pour Standard) pendant un certain laps de temps et qu’il redescend par la suite, la réplication rattrape le retard. Toutefois si le taux d’activité de données est bien au-delà de la limite prise en charge la plupart du temps, vous devez, si possible, envisager l’une des options ci-dessous :

**Option 1 :** exclure le disque, ce qui provoque un taux de changement de données élevé : </br>
Vous pouvez actuellement exclure le disque avec [Site Recovery Powershell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)

**Option 2 :** modifier le niveau du disque de stockage après sinistre : </br>
Cette option n’est possible que si l’activité des données de disque est inférieure à 10 Mo/s. Supposons qu’une machine virtuelle avec un disque P10 disque a un taux d’activité des données supérieur à 8 Mo/s, mais inférieur à 10 Mo/s. Si le client peut utiliser le disque P30 pour le stockage cible pendant la protection, le problème peut être résolu.

### <a name="Network-connectivity-issue"></a>Problème de connectivité réseau

#### <a name="network-latency-to-cache-storage-account-"></a>Latence du réseau pour le compte de stockage de cache :
 Site Recovery envoie les données répliquées au compte de stockage de cache et le problème peut se produire si le chargement des données à partir de la machine virtuelle dans le compte de stockage de cache est plus lent que 4 Mo en 3 secondes. Pour vérifier si des problèmes sont liés à la latence, utilisez [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) pour charger des données de la machine virtuelle dans le compte de stockage de cache.<br>
Si la latence est élevée, vérifiez si vous utilisez une appliance virtuelle réseau pour contrôler le trafic réseau sortant à partir des machines virtuelles. L’appliance peut être limitée si tout le trafic de réplication passe par elle. Nous vous recommandons de créer un point de terminaison de service réseau dans votre réseau virtuel pour « Stockage » afin que le trafic de réplication n’atteigne pas l’appliance virtuelle réseau. Reportez-vous à la [configuration des appliances virtuelles réseau](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)

#### <a name="network-connectivity"></a>Connectivité réseau
Pour que la réplication Site Recovery fonctionne, une connectivité sortante vers des URL ou des plages d’adresses IP spécifiques est nécessaire à partir de la machine virtuelle. Si votre machine virtuelle se trouve derrière un pare-feu ou utilise des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, vous pouvez rencontrer l’un des problèmes ci-après.</br>
Reportez-vous à [Connectivité sortante pour les URL Site Recovery](https://docs.microsoft.com/en-us/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) pour vous assurer que toutes les URL sont connectées 