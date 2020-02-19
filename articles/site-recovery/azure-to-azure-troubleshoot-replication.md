---
title: Résoudre les problèmes de réplication des machines virtuelles Azure avec Azure Site Recovery
description: Résoudre les problèmes de réplication dans le cadre de la reprise d’activité d’une machine virtuelle Azure avec Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: e5e52c6e8560c7369054cfc9fcf2ba4c405671e0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190808"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Résoudre les problèmes de réplication dans le cadre de la reprise d’activité d’une machine virtuelle Azure

Cet article décrit les problèmes courants dans Azure Site Recovery lorsque vous répliquez et récupérez des machines virtuelles d’une région vers une autre. Il explique également comment détecter les problèmes courants. Pour plus d’informations sur les configurations prises en charge, consultez la [matrice de prise en charge pour la réplication des machines virtuelles Azure](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery réplique des données de manière cohérente de la région source vers la région de récupération d’urgence. Il crée également un point de récupération cohérent en cas d’incident toutes les 5 minutes. Si Site Recovery ne peut pas créer de points de récupération pendant 60 minutes, il vous en informe et vous communique les informations suivantes :

Message d’erreur : « Aucun point de récupération d’incident cohérent disponible pour la machine virtuelle pendant les 60 dernières minutes ».</br>
ID d'erreur : 153007

Les sections suivantes décrivent les causes et solutions.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Taux élevé de changement de données sur la machine virtuelle source

Azure Site Recovery crée un événement si le taux de modification des données sur la machine virtuelle source dépasse les limites prises en charge. Pour voir si le problème est dû à un taux d’évolution élevé, accédez à **Éléments répliqués** > **Machine virtuelle** > **Événements – dernières 72 heures**.
Vous devez voir l’événement « Taux de modification des données au-delà des limites prises en charge » :

![Page Azure Site Recovery qui affiche un taux de modification des données trop élevé](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Si vous sélectionnez l’événement, vous pouvez consulter les informations relatives au disque :

![Page qui affiche les détails de l’événement Taux de modification des données](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Limites Azure Site Recovery

Le tableau suivant présente les limites d’Azure Site Recovery. Ces limites sont basées sur nos tests, mais ne peuvent pas couvrir toutes les combinaisons possibles d’entrée/sortie (E/S) d’application. Les résultats réels varient en fonction de la combinaison d’E/S de votre application.

Deux limites sont à prendre en compte : le taux d’évolution des données par disque et le taux d’évolution des données par machine virtuelle. Par exemple, examinons le disque P20 Premium dans le tableau suivant. Pour une machine virtuelle unique, Site Recovery peut gérer 5 Mo/s d’évolution par disque, avec un maximum de 5 disques de ce type. Site Recovery a une limite de 25 Mo/s de l’évolution totale par machine virtuelle.

**Stockage de réplication cible** | **Taille moyenne des E/S du disque source** |**Activité moyenne des données du disque source** | **Activité totale des données par jour du disque de données source**
---|---|---|---
Stockage Standard | 8 Ko | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 8 Ko  | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 16 Ko | 4 Mo/s |  336 Go par disque
Disque Premium P10 ou P15 | 32 Ko ou plus | 8 Mo/s | 672 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 8 Ko    | 5 Mo/s | 421 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 16 Ko ou plus |10 Mo/s | 842 Go par disque

### <a name="solution"></a>Solution

Les limites des taux de modification des données d’Azure Site Recovery dépendent du type de disque. Pour voir si ce problème est récurrent ou momentané, trouvez le taux de modification des données de la machine virtuelle concernée. Accédez à la machine virtuelle source, recherchez les mesures sous **Surveillance** et ajoutez les mesures comme illustré sur cette capture d'écran :

![Page qui montre le processus en trois étapes permettant de déterminer le taux de modification des données](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Sélectionnez **Ajouter une mesure**, puis ajoutez **Octets écrits/s sur disque de système d’exploitation** et **Octets écrits/s sur disque de données**.
1. Surveiller le pic comme indiqué dans la capture d’écran.
1. Affichez le total des opérations en écriture sur le disque du système d’exploitation et sur tous les disques de données combinés. Ces mesures peuvent ne pas vous renseigner sur le niveau par disque, mais indiquent le modèle total d'activité des données.

Un pic dans le taux de modification des données peut provenir d’une rafale occasionnelle de données. Si le taux de modification des données est supérieur à 10 Mo/s (pour Premium) ou 2 Mo/s (pour Standard) puis diminue, la réplication rattrapera son retard. Si l’évolution est la plupart du temps bien supérieure à la limite prise en charge, envisagez l’une des options suivantes :

- Exclure le disque à l’origine d’un taux élevé de modification des données : Tout d’abord, désactivez la réplication. Vous pouvez ensuite exclure le disque à l’aide de [PowerShell](./azure-to-azure-exclude-disks.md).
- Modifier le niveau de service du disque de stockage dédié à la récupération d’urgence : Cette option est possible seulement si le débit de données du disque est inférieur à 20 Mo/s. Supposons qu’une machine virtuelle dotée d’un disque P10 présente un taux d’évolution des données supérieur à 8 Mo/s, mais inférieur à 10 Mo/s. Si le client peut utiliser le disque P30 pour le stockage cible pendant la protection, le problème peut être résolu. Cette solution est possible seulement pour les machines qui utilisent des Disques managés Premium. Procédez comme suit :

    1. Accédez au panneau **Disques** de la machine répliquée concernée et copiez le nom du disque de réplica.
    1. Accédez à ce réplica du disque managé.
    1. Vous pouvez voir une bannière dans **vue d’ensemble** qui indique qu’une URL SAS a été générée. Cliquez sur cette bannière et annulez l’exportation. Ignorez cette étape si vous ne voyez pas la bannière.
    1. Dès que l’URL SAS est révoquée, accédez à **Configuration** pour le disque managé. Augmentez la taille afin que Site Recovery prenne en charge le taux d’évolution observé sur le disque source.

## <a name="Network-connectivity-problem"></a>Problèmes de connectivité réseau

### <a name="network-latency-to-a-cache-storage-account"></a>Latence du réseau pour le compte de stockage de cache

Site Recovery envoie les données répliquées vers le compte de stockage de cache. Vous pouvez rencontrer une latence du réseau si le chargement des données entre une machine virtuelle et le compte de stockage de cache est inférieur à 4 Mo en 3 secondes.

Pour vérifier s’il existe un problème lié à la latence, utilisez [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy). Vous pouvez utiliser cet utilitaire de ligne de commande pour charger des données de la machine virtuelle vers le compte de stockage de cache. Si la latence est élevée, vérifiez si vous utilisez une appliance virtuelle réseau (NVA) pour contrôler le trafic réseau sortant des machines virtuelles. L’appliance peut être limitée si tout le trafic de réplication passe par elle.

Nous vous recommandons de créer un point de terminaison de service réseau dans votre réseau virtuel pour « Stockage » afin que le trafic de réplication n’atteigne pas l’appliance virtuelle réseau. Pour plus d'informations, consultez [Configuration des appliances virtuelles réseau](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Connectivité réseau

Pour que la réplication Site Recovery fonctionne, la machine virtuelle doit fournir une connectivité sortante vers des URL ou des plages d’adresses IP spécifiques. Votre machine virtuelle peut se trouver derrière un pare-feu ou utiliser des règles de groupe de sécurité réseau (NSG) pour contrôler la connectivité sortante. Si c’est le cas, vous pouvez rencontrer des problèmes. Pour vous assurer que toutes les URL sont connectées, consultez [Connectivité sortante pour les URL Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>ID d’erreur 153006 : aucun point de récupération cohérent avec l’application disponible pour la machine virtuelle pendant les « X » dernières minutes

Voici quelques-uns des problèmes les plus courants.

#### <a name="known-issue-in-sql-server-20082008-r2"></a>Problème connu dans SQL Server 2008/2008 R2

**Procédure de résolution** : Il existe un problème connu avec SQL Server 2008/2008 R2. Référez-vous à l’article [Azure Site Recovery Agent or other non-component VSS backup fails for a server hosting SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2) (L’agent Azure Site Recovery ou une autre sauvegarde VSS sans composant échoue sur un serveur hébergeant une instance SQL Server 2008 R2).

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Les tâches Azure Site Recovery échouent lorsque des serveurs hébergent les instances de n’importe quelle version de SQL Server avec des bases de données AUTO_CLOSE

**Procédure de résolution** : Reportez-vous à l’article [Non-component VSS backups such as Azure Site Recovery jobs fail on servers hosting SQL Server instances with AUTO_CLOSE DBs](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) (Des sauvegardes VSS sans composant telles que des tâches Azure Site Recovery échouent sur des serveurs hébergeant des instances SQL Server avec des bases de données AUTO_CLOSE).


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>Problème connu dans SQL Server 2016 et 2017

**Procédure de résolution** : Reportez-vous à l’article [Error occurs when you back up a virtual machine with non-component based backup in SQL Server 2016 and 2017](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) (Une erreur se produit lorsque vous sauvegardez une machine virtuelle avec une sauvegarde non basée sur des composants dans SQL Server 2016 et 2017).

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Vous utilisez la configuration des espaces de stockage direct Azure

**Procédure de résolution** : Azure Site Recovery ne peut pas créer de point de récupération cohérent avec l’application pour la configuration des espaces de stockage direct. [Configurez la stratégie de réplication](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms).

### <a name="more-causes-because-of-vss-related-issues"></a>Autres causes dues à des problèmes liés à VSS :

Pour mieux résoudre le problème, vérifiez les fichiers sur la machine source pour obtenir le code d’erreur exact de l’échec :

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Pour localiser les erreurs dans le fichier, recherchez la chaîne « vacpError » en ouvrant le fichier vacp.log dans un éditeur.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Dans l’exemple précédent, **2147754994** est le code d’erreur qui vous informe de l’échec à la suite de cette phrase.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>L’enregistreur VSS n’est pas installé - erreur 2147221164

**Procédure de résolution** : Pour générer une balise de cohérence d’application, Azure Site Recovery utilise le Service VSS. Site Recovery installe un fournisseur VSS pour que l’opération prenne des clichés instantanés de la cohérence d’application. Azure Site Recovery installe ce fournisseur VSS en tant que service. Si le fournisseur VSS n’est pas installé, la création de clichés instantanés de la cohérence d’application échoue. Elle indique l’ID d’erreur 0x80040154 « Classe non inscrite ». Consultez l’article relatif à la [résolution des problèmes lors de l’installation de l’enregistreur VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>L’enregistreur VSS est désactivé - erreur 2147943458

**Procédure de résolution** : Pour générer la balise de cohérence d’application, Azure Site Recovery utilise le Service VSS. Site Recovery installe un fournisseur VSS pour que l’opération prenne des clichés instantanés de la cohérence d’application. Ce fournisseur VSS est installé en tant que service. Si le service de fournisseur VSS n’est pas activé, la création de clichés instantanés de la cohérence d’application échoue. Elle affiche l’erreur « Le service spécifié est désactivé et ne peut pas être démarré (0x80070422). »

Si VSS est désactivé :

- Vérifiez que le type de démarrage du service fournisseur VSS est défini sur **Automatique**.
- Redémarrez les services suivants :
   - Service VSS
   - Fournisseur VSS d’Azure Site Recovery
   - Service VDS (Virtual Disk Service)

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - erreur 2147754756

**Procédure de résolution** : Pour générer la balise de cohérence d’application, Azure Site Recovery utilise le Service VSS. Vérifiez si le service de fournisseur VSS d’Azure Site Recovery est installé.

Utilisez les commandes suivantes pour réinstaller le fournisseur VSS :
1. Désinstallez le fournisseur existant : C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
1. Réinstallez le fournisseur VSS : C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Vérifiez que le type de démarrage du service fournisseur VSS est défini sur **Automatique**.

Redémarrez les services suivants :
- Service VSS
- Fournisseur VSS d’Azure Site Recovery
- Service VDS (Virtual Disk Service)
