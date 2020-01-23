---
title: Résoudre les problèmes de réplication des machines virtuelles Azure avec Azure Site Recovery
description: Résoudre les problèmes de réplication dans le cadre de la reprise d’activité d’une machine virtuelle Azure avec Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: b8afdd0f2dd98260a628116fa7402e05cd39e06b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965859"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Résoudre les problèmes de réplication dans le cadre de la reprise d’activité d’une machine virtuelle Azure

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

* **Exclure le disque, ce qui provoque un taux de changement de données élevé** : Vous pouvez exclure le disque à l’aide de [PowerShell](./azure-to-azure-exclude-disks.md). Pour exclure le disque, vous devez d’abord désactiver la réplication.
* **Modifier le niveau du disque de stockage après sinistre** : Cette option est possible seulement si le débit de données du disque est inférieur à 20 Mo/s. Supposons qu’une machine virtuelle avec un disque P10 disque a un taux d’activité des données supérieur à 8 Mo/s, mais inférieur à 10 Mo/s. Si le client peut utiliser le disque P30 pour le stockage cible pendant la protection, le problème peut être résolu. Notez que cette solution est possible seulement pour les machines qui utilisent des disques managés Premium. Effectuez les étapes suivantes :
    - Accédez au panneau Disques de la machine répliquée concernée et copiez le nom du disque de réplica.
    - Accédez à ce disque managé de réplica.
    - Vous pouvez voir une bannière dans le panneau Vue d’ensemble indiquant qu’une URL de signature d’accès partagé a été générée. Cliquez sur cette bannière et annulez l’exportation. Ignorez cette étape si vous ne voyez pas la bannière.
    - Dès que l’URL de la signature d’accès partagé est révoquée, accédez au panneau Configuration du disque managé et augmentez la taille de manière à ce que Site Recovery prenne en charge le débit observé sur le disque source.

## <a name="Network-connectivity-problem"></a>Problèmes de connectivité réseau

### <a name="network-latency-to-a-cache-storage-account"></a>Latence du réseau pour le compte de stockage de cache
Site Recovery envoie les données répliquées vers le compte de stockage de cache. Vous pouvez constater une latence du réseau si le chargement des données entre une machine virtuelle et le compte de stockage de cache est inférieur à 4 Mo en 3 secondes.

Pour vérifier si un problème est lié à la latence, utilisez [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) pour charger des données de la machine virtuelle dans le compte de stockage de cache. Si la latence est élevée, vérifiez si vous utilisez une appliance virtuelle réseau pour contrôler le trafic réseau sortant à partir des machines virtuelles. L’appliance peut être limitée si tout le trafic de réplication passe par elle.

Nous vous recommandons de créer un point de terminaison de service réseau dans votre réseau virtuel pour « Stockage » afin que le trafic de réplication n’atteigne pas l’appliance virtuelle réseau. Pour plus d'informations, consultez [Configuration des appliances virtuelles réseau](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Connectivité réseau
Pour que la réplication Site Recovery fonctionne, une connectivité sortante vers des URL ou des plages d’adresses IP spécifiques est nécessaire à partir de la machine virtuelle. Si votre machine virtuelle se trouve derrière un pare-feu ou utilise des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, vous pouvez rencontrer l’un des problèmes ci-après. Pour vous assurer que toutes les URL sont connectées, consultez [Connectivité sortante pour les URL Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID d’erreur 153006 : aucun point de récupération cohérent avec l’application disponible pour la machine virtuelle pendant les « XXX » dernières minutes.

Certains des problèmes les plus courants sont répertoriés ci-dessous

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Cause 1 : Problème connu dans SQL Server 2008/2008 R2
**Procédure de résolution** : Il existe un problème connu dans SQL Server 2008/2008 R2. Référez-vous à cet article de la base de connaissances : [Azure Site Recovery Agent or other non-component VSS backup fails for a server hosting SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2) (L’agent Azure Site Recovery ou une autre sauvegarde VSS sans composant échoue sur un serveur hébergeant une instance SQL Server 2008 R2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Cause 2 : Les tâches Azure Site Recovery échouent lorsque des serveurs hébergent les instances de n’importe quelle version de SQL Server avec des bases de données AUTO_CLOSE
**Procédure de résolution** : Référez-vous à cet [article](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) de la base de connaissances


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Cause 3 : Problème connu dans SQL Server 2016 et 2017
**Procédure de résolution** : Référez-vous à cet [article](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) de la base de connaissances

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>Cause 4 : Vous utilisez la configuration directe des espaces de stockage
**Procédure de résolution** : Azure Site Recovery ne peut pas créer de point de récupération cohérent avec l’application pour la configuration directe des espaces de stockage. Consultez l’article pour [configurer la stratégie de réplication](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms) correctement.

### <a name="more-causes-due-to-vss-related-issues"></a>Autres causes provoquées par des problèmes liés à VSS :

Pour mieux résoudre le problème, vérifiez les fichiers sur la machine source pour obtenir le code d’erreur exact de l’échec :

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Comment localiser les erreurs dans le fichier ?
Recherchez la chaîne « vacpError » en ouvrant le fichier vacp.log dans un éditeur

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Dans l’exemple ci-dessus, **2147754994** est le code d’erreur qui vous informe de l’échec, comme indiqué ci-dessous

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>L’enregistreur VSS n’est pas installé - erreur 2147221164

*Procédure de résolution* : Pour générer une balise de cohérence d’application, Azure Site Recovery utilise le service VSS (cliché instantané de volume) de Microsoft. Il installe un fournisseur VSS pour que l’opération prenne des clichés instantanés de la cohérence d’application. Ce fournisseur VSS est installé en tant que service. Si le service de fournisseur VSS n’est pas installé, la création de clichés instantanés de la cohérence d’application échoue et l’ID d’erreur 0x80040154 « Classe non inscrite » s’affiche. </br>
Consultez [l’article relatif au dépannage de l’installation de l’enregistreur VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>L’enregistreur VSS est désactivé - erreur 2147943458

**Procédure de résolution** : Pour générer une balise de cohérence d’application, Azure Site Recovery utilise le service VSS (cliché instantané de volume) de Microsoft. Il installe un fournisseur VSS pour que l’opération prenne des clichés instantanés de la cohérence d’application. Ce fournisseur VSS est installé en tant que service. Si le service de fournisseur VSS est désactivé, la création de clichés instantanés de la cohérence d’application échoue et l’ID d’erreur « Le service spécifié est désactivé et ne peut pas être démarré (0x80070422) » s’affiche. </br>

- Si le service VSS est désactivé :
    - Vérifiez que le type de démarrage du service fournisseur VSS est défini sur **Automatique**.
    - Redémarrez les services suivants :
        - Service VSS
        - Fournisseur VSS d’Azure Site Recovery
        - Service VDS (Virtual Disk Service)

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - erreur 2147754756

**Procédure de résolution** : Pour générer une balise de cohérence d’application, Azure Site Recovery utilise le service VSS (cliché instantané de volume) de Microsoft.
Vérifiez si le service de fournisseur VSS d’Azure Site Recovery est installé. </br>

- Réessayez d’installer le fournisseur à l’aide des commandes suivantes :
- Désinstallez le fournisseur existant : C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Réinstallez : C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Vérifiez que le type de démarrage du service fournisseur VSS est défini sur **Automatique**.
    - Redémarrez les services suivants :
        - Service VSS
        - Fournisseur VSS d’Azure Site Recovery
        - Service VDS (Virtual Disk Service)
