---
title: Dépannage d’Azure Site Recovery en cas de problèmes continus de réplication Azure vers Azure | Microsoft Docs
description: Dépannage des erreurs et des problèmes lors de la réplication de machines virtuelles Azure pour la récupération d’urgence
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9ff756270c368d39b7ef78d7c1046f7c91169668
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103744"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Résoudre les problèmes continus de réplication de machine virtuelle Azure vers Azure

Cet article décrit les problèmes courants dans Azure Site Recovery lorsque vous répliquez et récupérez des machines virtuelles d’une région vers une autre. Il explique également comment y remédier. Pour plus d’informations sur les configurations prises en charge, consultez la [matrice de prise en charge pour la réplication des machines virtuelles Azure](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery réplique des données de manière cohérente de la région source vers la région de récupération d’urgence et crée un point de récupération cohérent toutes les 5 minutes en cas d'incident. Si Site Recovery ne peut pas créer de points de récupération pendant 60 minutes, il vous en informe et vous communique les informations suivantes :

Message d’erreur : « Aucun point de récupération d’incident cohérent disponible pour la machine virtuelle pendant les 60 dernières minutes ».</br>
ID d'erreur : 153007 </br>

Les sections suivantes décrivent les causes et solutions.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Taux élevé de changement de données sur la machine virtuelle source
Azure Site Recovery déclenche un événement si le taux de changement de données sur la machine virtuelle source dépasse les limites prises en charge. Pour vérifier si le problème est dû à un taux d’activité de données élevé, accédez à **Éléments répliqués** > **Machine virtuelle** > **Événéments-dernières 72 heures**.
Vous devez voir l’événement « Taux de changement de données au-delà des limites prises en charge » :

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Si vous sélectionnez l’événement, vous pouvez consulter les informations relatives au disque :

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Limites Azure Site Recovery
Le tableau suivant présente les limites d’Azure Site Recovery. Ces limites sont basées sur nos tests, mais ne peuvent pas couvrir toutes les combinaisons d’E/S d’application possibles. Les résultats réels varient en fonction de la combinaison d’E/S de votre application. 

Deux limites sont à prendre en compte : le taux d’activité de données par disque et le taux d’activité de données par machine virtuelle. Examinons, par exemple, le disque Premium P20 dans le tableau suivant. Site Recovery peut gérer 5 Mo/s d'activité par disque, avec un maximum de cinq disques par machine virtuelle, en raison de la limite de 25 Mo/s d'activité totale par machine virtuelle.

**Stockage de réplication cible** | **Taille moyenne des E/S du disque source** |**Activité moyenne des données du disque source** | **Activité totale des données par jour du disque de données source**
---|---|---|---
Stockage Standard | 8 Ko | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 8 Ko  | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 16 Ko | 4 Mo/s |  336 Go par disque
Disque Premium P10 ou P15 | 32 Ko ou plus | 8 Mo/s | 672 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 8 Ko    | 5 Mo/s | 421 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 16 Ko ou plus |10 Mo/s | 842 Go par disque

### <a name="solution"></a>Solution
Les limites du taux de changement de données d’Azure Site Recovery dépendent du type de disque. Pour savoir si ce problème est récurrent ou momentané, trouvez le modèle du taux de changement de données de la machine virtuelle concernée. Accédez à la machine virtuelle source, recherchez les mesures sous **Surveillance** et ajoutez les mesures comme illustré sur cette capture d'écran :

![Processus en trois étapes pour déterminer le taux de modification des données](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Sélectionnez **Ajouter une mesure**, puis ajoutez **Octets écrits/s sur disque de système d’exploitation** et **Octets écrits/s sur disque de données**.
2. Surveiller le pic comme indiqué dans la capture d’écran.
3. Affichez le total des opérations en écriture sur le disque du système d’exploitation et sur tous les disques de données combinés. Ces mesures peuvent ne pas vous renseigner sur le niveau par disque, mais indiquent le modèle total d'activité des données.

S’il s’agit d’une rafale de données occasionnelle, que le taux de modification des données est supérieur à 10 Mo/s (pour Premium) et 2 Mo/s (pour Standard) pendant un certain laps de temps et qu’il redescend par la suite, la réplication peut rattraper le retard. Toutefois, si le taux d’activité de données est bien au-delà de la limite prise en charge la plupart du temps, envisagez, si possible, l’une des options ci-dessous :

* **Exclure le disque, ce qui provoque un taux de changement de données élevé** : Vous pouvez exclure le disque à l’aide de [PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine).
* **Modifier le niveau du disque de stockage après sinistre** : Cette option n’est possible que si l’activité des données de disque est inférieure à 10 Mo/s. Supposons qu’une machine virtuelle avec un disque P10 disque a un taux d’activité des données supérieur à 8 Mo/s, mais inférieur à 10 Mo/s. Si le client peut utiliser le disque P30 pour le stockage cible pendant la protection, le problème peut être résolu.

## <a name="Network-connectivity-problem"></a>Problèmes de connectivité réseau

### <a name="network-latency-to-a-cache-storage-account"></a>Latence du réseau pour le compte de stockage de cache
Site Recovery envoie les données répliquées vers le compte de stockage de cache. Vous pouvez constater une latence du réseau si le chargement des données entre une machine virtuelle et le compte de stockage de cache est inférieur à 4 Mo en 3 secondes. 

Pour vérifier si un problème est lié à la latence, utilisez [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) pour charger des données de la machine virtuelle dans le compte de stockage de cache. Si la latence est élevée, vérifiez si vous utilisez une appliance virtuelle réseau pour contrôler le trafic réseau sortant à partir des machines virtuelles. L’appliance peut être limitée si tout le trafic de réplication passe par elle. 

Nous vous recommandons de créer un point de terminaison de service réseau dans votre réseau virtuel pour « Stockage » afin que le trafic de réplication n’atteigne pas l’appliance virtuelle réseau. Pour plus d'informations, consultez [Configuration des appliances virtuelles réseau](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Connectivité réseau
Pour que la réplication Site Recovery fonctionne, une connectivité sortante vers des URL ou des plages d’adresses IP spécifiques est nécessaire à partir de la machine virtuelle. Si votre machine virtuelle se trouve derrière un pare-feu ou utilise des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, vous pouvez rencontrer l’un des problèmes ci-après. Pour vous assurer que toutes les URL sont connectées, consultez [Connectivité sortante pour les URL Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 