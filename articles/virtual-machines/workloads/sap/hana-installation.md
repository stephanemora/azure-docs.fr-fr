---
title: Installation de SAP HANA sur SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Comment installer SAP HANA sur SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dad088138fea2dd4fadc0cc9eed71245c32a8e0b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162654"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Guide pratique d’installation et de configuration de SAP HANA (grandes instances) sur Azure

Avant de lire cet article, familiarisez-vous avec les [termes courants concernant les grandes Instances HANA](hana-know-terms.md) et les [références SKU des grandes instances HANA](hana-available-skus.md).

L’installation de SAP HANA relève de votre responsabilité et vous pouvez démarrer l’activité après la remise d’un nouveau SAP HANA sur un serveur Azure (grandes instances). Et après que la connexion entre vos réseaux virtuels Azure et les unités de Grande instance HANA est établie. 

> [!Note]
> En vertu de la stratégie de SAP, l’installation de SAP HANA doit être effectuée par une personne certifiée pour l’effectuer, à savoir une personne ayant passé l’examen Certified SAP Technology Associate et l’examen de certification SAP HANA Installation, ou par un intégrateur de systèmes (SI) certifié SAP.

Effectuez une nouvelle vérification, en particulier si vous vous préparez à installer HANA 2.0 ([Note de support #2235581 relative à SAP HANA : systèmes d’exploitation pris en charge](https://launchpad.support.sap.com/#/notes/2235581/E)) afin de vous assurer que le système d’exploitation utilisé est pris en charge par la dernière version de SAP HANA que souhaitez installer. Vous comprendrez alors que le nombre de systèmes d’exploitation pris en charge par HANA 2.0 est plus limité que celui des systèmes pris en charge par HANA 1.0. 

> [!IMPORTANT] 
> Pour les unités de Type II, seule la version SLES 12 SP2 du système d’exploitation est actuellement prise en charge. 

Avant de commencer l’installation de HANA, vous devez valider les éléments suivants :
- [Valider les unités HLI](#validate-the-hana-large-instance-units)
- [Configuration du système d’exploitation](#operating-system)
- [Configuration réseau](#networking)
- [Configuration du stockage](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Valider les unités de grandes instances HANA (HLI)

Après avoir reçu l’unité de grande Instance HANA de Microsoft, validez les paramètres suivants, puis effectuez les ajustements nécessaire.

Après avoir reçu l’unité de grandes instances HANA, puis établi l’accès et la connectivité aux instances, la **première étape** consiste à enregistrer le système d’exploitation de l’instance auprès de votre fournisseur de système d’exploitation. Cela inclut l’inscription de votre système d’exploitation SUSE Linux dans une instance de SUSE SMT que vous devez avoir déployée sur une machine virtuelle dans Azure. L’unité de grande Instance HANA peut se connecter à cette instance SMT (voir [Comment configurer un serveur SMT pour SUSE Linux](hana-setup-smt.md)). Ou bien l’enregistrement de votre système d’exploitation Red Hat doit se faire avec le Gestionnaire d’abonnements Red Hat auquel vous devez vous connecter. Consultez également la section Remarques de ce [document](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cette étape est également nécessaire si vous souhaitez pouvoir appliquer des correctifs au système d’exploitation. Tâche qui incombe au client. Pour SUSE, la documentation permettant d’installer et de configurer SMT se trouve [ici](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

La **deuxième étape** consiste à rechercher les nouveaux correctifs applicables à la version spécifique du système d’exploitation. Vérifiez que le niveau de correctif logiciel des grandes instances HANA est à jour. En fonction de la date de publication des correctifs/versions du système d’exploitation et des modifications de l’image que Microsoft peut déployer, il peut arriver que les correctifs les plus récents ne soient pas inclus. Par conséquent, cette étape est obligatoire après réception d’une unité de grande instance HANA afin de vérifier si des correctifs indispensables pour la sécurité, le fonctionnement, la disponibilité et les performances ont été publiés entretemps par le fournisseur Linux concerné, et s’ils doivent être appliqués.

La **troisième étape** consiste à consulter les notes de support SAP relatives à l’installation et à la configuration de SAP HANA sur la version spécifique du système d’exploitation. En raison des recommandations variables ou des modifications apportées aux notes de support SAP ou des configurations qui dépendent de scénarios d’installation uniques, Microsoft ne sera pas toujours en mesure de fournir une unité de grandes instances HANA configurée à la perfection. Par conséquent, vous êtes tenu, en tant que client, de lire les Notes de SAP liées à SAP HANA sur votre version de Linux. Vérifiez également les configurations de la publication/version de système d’exploitation nécessaires, et appliquez les paramètres de configuration si ce n’est déjà fait.

Vérifiez en particulier les paramètres ci-dessous et effectuez les ajustements éventuels nécessaires :

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Sous SLES12 SP1 et RHEL 7.2, ces paramètres doivent être définis dans un fichier de configuration dans le répertoire /etc/sysctl.d. Il faut par exemple créer un fichier de configuration nommé 91-NetApp-HANA.conf. Pour les versions plus anciennes de SLES et RHEL, ces paramètres doivent être définis dans /etc/sysctl.conf.

Pour toutes les versions de RHEL et à partir de SLES12, le paramètre 
- sunrpc.tcp_slot_table_entries = 128

doit être défini dans /etc/modprobe.d/sunrpc-local.conf. Si le fichier n’existe pas, il doit tout d’abord être créé en ajoutant l’entrée suivante : 
- options sunrpc tcp_max_slot_table_entries=128

La **quatrième étape** consiste à vérifier l’heure système de votre unité de grandes instances HANA. Les instances sont déployées sur la base d’un fuseau horaire système qui représente l’emplacement de la région Azure dans laquelle se trouve le tampon de grande instance HANA. Vous êtes libre de modifier l’heure système ou le fuseau horaire des instances que vous possédez. Dans ce cas, si vous commandez d’autres instances dans votre locataire, vous devrez probablement modifier le fuseau horaire des nouvelles instances. Microsoft Operations n’a aucune visibilité sur le fuseau horaire système défini pour vos instances après leur réception. C’est pourquoi les instances nouvellement déployées peuvent ne pas être définies sur le même fuseau horaire que celui choisi précédemment. Il vous incombe donc, en tant que client, de vérifier le fuseau horaire des instances remises et de les modifier si nécessaire. 

La **cinquième étape** consiste à vérifier le fichier etc/hosts. À la réception des unités, des adresses IP spécifiques sont affectées à différentes fonctions (voir la section suivante). Vérifiez le fichiers etc/hosts. Si vous ajoutez des unités à un locataire existant, le fichier etc/hosts des systèmes nouvellement déployés ne contiendra pas les adresses IP des systèmes reçus précédemment. Il vous incombe donc, en tant que client, de définir les paramètres appropriés pour que l’instance nouvellement déployée puisse interagir et résoudre les noms des unités précédemment déployées dans votre locataire. 

## <a name="operating-system"></a>Système d’exploitation

> [!IMPORTANT] 
> Pour les unités de Type II, seule la version SLES 12 SP2 du système d’exploitation est actuellement prise en charge. 

La taille de l’espace d’échange de l’image du système d’exploitation livré est définie sur 2 Go conformément à la [note de support #1999997 relative à la FAQ : mémoire SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Tout autre paramètre souhaité doit être défini par vous-même, en tant que client.

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) est la distribution de Linux installée pour SAP HANA sur Azure (grandes instances). Cette distribution spéciale fournit des fonctionnalités spécifiques à SAP &quot;prêtes à l’emploi&quot; (y compris des paramètres prédéfinis pour une exécution efficace de SAP sur SLES).

Consultez [la bibliothèque de ressources/les livres blancs](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) sur le site Web SUSE et [SAP sur SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) sur SCN (SAP Community Network) pour bénéficier de ressources utiles relatives au déploiement de SAP HANA sur SLES (notamment sur la configuration de la haute disponibilité, le renforcement de la sécurité spécifique aux opérations SAP, etc.).

Autres liens utilies concernant SAP sur SUSE :

- [SAP HANA sur le site SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113) (Meilleure pratique pour SAP : Réplication de la file d’attente – SAP NetWeaver sur Linux Enterprise 12).
- [ClamSAP - SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (ClamSAP – Protection antivirus SLES pour SAP, y compris SLES 12 for les applications SAP).

Notes de support SAP applicables à l’implémentation de SAP HANA sur SLES 12 :

- [Note de support SAP #1944799 relative aux instructions SAP HANA pour l’installation du système d’exploitation SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [Note de support SAP #2205917 relative aux paramètres de système d’exploitation SAP HANA DB recommandés pour SLES 12 ainsi que les application SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
- [Note de support SAP #1984787 relative à SUSE Linux Enterprise Server 12 : notes d’installation](https://launchpad.support.sap.com/#/notes/1984787).
- [Note de support SAP #171356 relative aux logiciels SAP sur Linux : informations générales](https://launchpad.support.sap.com/#/notes/1984787).
- [Note de support SAP #1391070 relative aux solutions Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux pour SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) est une autre offre permettant l’exécution de SAP HANA sur les grandes instances HANA. RHEL 6.7 et 7.2 sont disponibles. Veuillez noter que contrairement aux machines virtuelles Azure natives où seuls RHEL 7.2 et les versions plus récentes sont pris en charge, les grandes instances HANA prennent également en charge RHEL 6.7. Toutefois, nous vous recommandons d’utiliser une version 7.x de RHEL.

Autres liens SAP utiles relatifs à Red Hat :
- [Site de SAP HANA sur Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Notes de support SAP applicables à l’implémentation de SAP HANA sur Red Hat :

- [Note de support SAP #2009879 relative aux instructions SAP HANA pour les systèmes d’exploitation Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E).
- [Note de support SAP #2292690 relative à SAP HANA DB : paramètres de système d’exploitation recommandés pour RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [Note de support SAP #2247020 relative à SAP HANA DB : paramètres de système d’exploitation recommandés pour RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [Note de support SAP #1391070 relative aux solutions Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).
- [Note de support SAP #2228351 relative à Linux : SAP HANA Database SPS 11 révision 110 (ou plus) sur RHEL 6 ou SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [Note de support SAP #2397039 relative à la FAQ : SAP sur RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [Note de support SAP #1496410 relative à Red Hat Enterprise Linux 6.x : installation et mise à niveau](https://launchpad.support.sap.com/#/notes/1496410).
- [Note de support SAP #2002167 relative à Red Hat Enterprise Linux 7.x : installation et mise à niveau](https://launchpad.support.sap.com/#/notes/2002167).

### <a name="time-synchronization"></a>Synchronisation temporelle

Les applications SAP basées sur l’architecture de SAP NetWeaver sont sensibles aux différences d’heure des différents composants qui constituent le système SAP. Vous connaissez certainement les vidages courts SAP ABAP avec le titre d’erreur ZDATE\_LARGE\_TIME\_DIFF, dans la mesure où ces ceux-ci surviennent lorsque l’heure système des différents serveurs ou machines virtuelles s’écarte trop.

Pour SAP HANA sur Azure (grandes instances), la synchronisation de l’heure effectuée dans Azure ne s’applique pas aux unités de calcul dans les tampons de grandes instances. Cette synchronisation ne concerne pas les applications SAP qui s’exécutent sur des machines virtuelles Azure natives, car Azure garantit la bonne synchronisation de l’heure des systèmes. Par conséquent, un serveur temporel distinct doit être configuré pour être utilisé par les serveurs d’applications SAP s’exécutant sur des machines virtuelles Azure et les instances de base de données SAP HANA s’exécutant sur des grandes instances HANA. L’heure de l’infrastructure de stockage dans les tampons de grandes instances est synchronisée avec les serveurs NTP.


## <a name="networking"></a>Mise en réseau
Nous partons du principe que vous avez suivi les recommandations données dans les documents ci-après pour concevoir vos réseaux virtuels Azure et connecter ceux-ci aux grandes instances HANA :

- [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastructure et connectivité à SAP HANA (grandes instances) sur Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Certains détails concernant la mise en réseau d’unités individuelles valent la peine d’être soulignés. Chaque unité de grandes instances HANA est fournie avec deux ou trois adresses IP affectées à deux ou trois de ses ports de carte réseau. Trois adresses IP sont utilisées dans les configurations de montée en charge HANA et dans le scénario de réplication système HANA. Une des adresses IP affectées à la carte réseau de l’unité se situe hors du pool d’adresses IP du serveur décrit à la page [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Consultez [Scénarios HLI pris en charge](hana-supported-scenario.md) pour connaître les détails Ethernet de votre architecture.

## <a name="storage"></a>Stockage

La disposition du stockage de SAP HANA sur Azure (grandes instances) est configurée par SAP HANA sur Azure Service Management en suivant les directives recommandées par SAP dans le livre blanc [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Conditions de stockage SAP HANA). La taille approximative des différents volumes avec les différentes références SKU de grandes instances HANA est documentée dans [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Les conventions d’affectation de noms des volumes de stockage sont répertoriées dans le tableau suivant :

| Utilisation du stockage | Nom du montage | Nom du volume | 
| --- | --- | ---|
| Données HANA | /hana/data/SID/mnt0000<m> | Adresse IP du stockage:/hana_data_SID_mnt00001_tenant_vol |
| Journal HANA | /Hana/log/SID/mnt0000<m> | Adresse IP du stockage:/hana_log_SID_mnt00001_tenant_vol |
| Sauvegarde de fichier journal HANA | HANA/log/backups | Adresse IP du stockage:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA partagé | /Hana/Shared/SID | Adresse IP du stockage:/hana_shared_SID_mnt00001_tenant_vol/shared |
| /usr/sap | /usr/SAP/SID | Adresse IP du stockage:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Où SID est l’ID système de l’instance HANA 

Et tenant est une énumération interne des opérations lors du déploiement d’un client.

Comme vous pouvez le voir, HANA partagé et usr/sap partagent le même volume. La nomenclature des points de montage n’inclut ni l’ID système des instances HANA, ni le nombre de montages. Dans les déploiements avec montée en puissance parallèle, il n’existe qu’un seul montage, par exemple mnt00001. Dans un déploiement avec montée en puissance parallèle vous devriez voir un nombre de montages équivalent au nombre de nœuds worker et principaux. Pour l’environnement avec montée en puissance parallèle, les données, les journaux et les volumes de sauvegarde de fichier journal sont partagés et associés à chaque nœud dans la configuration de la montée en puissance parallèle. Pour les configurations exécutant plusieurs instances SAP, un autre jeu de volumes est créé et attaché à l’unité de grande instance HANA. Consultez [Scénarios HLI pris en charge](hana-supported-scenario.md) pour connaître les détails de la disposition du stockage de votre scénario.

À la lecture de ce document, et en regardant une unité de plus près, vous pouvez vous rendre compte de la générosité du volume disque des unités de grande instance HANA attribué à HANA/data, et remarquer la présence d’un volume HANA/log/backup. Si HANA/data est aussi volumineux, c’est parce que les captures instantanées du stockage que nous vous proposons l’exigent. Cela signifie que, plus vous prenez d’instantanés de stockage, plus les instantanés occupent d’espace dans les volumes de stockage qui vous sont assignés. Le volume HANA/log/backup n’est pas conçu pour être le volume sur lequel placer des sauvegardes de base de données. Il est dimensionné pour être utilisé en tant que volume de sauvegarde pour les sauvegardes des journaux de transactions HANA. Pour plus de détails, voir [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

En plus de la capacité de stockage fournie, les clients peuvent acheter de l’espace de stockage supplémentaire par incréments de 1 To. Cet espace supplémentaire peut être ajouté en tant que nouveaux volumes à des grandes instances HANA.

Pendant l’intégration avec SAP HANA sur Azure Service Management, le client spécifie un ID d’utilisateur et un ID de groupe pour l’utilisateur sidadm et le groupe sapsys (p. ex. : 1000,500). Ces mêmes valeurs doivent être utilisées lors de l’installation du système SAP HANA. Comme vous souhaitez déployer plusieurs instances HANA sur une unité, vous obtenez plusieurs ensembles de volumes (un ensemble pour chaque instance). Par conséquent, au moment du déploiement, vous devez définir :

- Le SID des différentes instances HANA (sidadm en est dérivé).
- Tailles de mémoire des différentes instances HANA. En effet, la taille de mémoire par instance définit la taille des volumes dans chaque jeu de volumes.

Selon les recommandations du fournisseur de stockage, les options de montage suivantes sont configurées pour tous les volumes montés (exclut le LUN de démarrage) :

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Ces points de montage sont configurés dans/etc/fstab, comme indiqué dans les graphiques suivants :

![fstab des volumes montés dans l’unité de grande instance HANA](./media/hana-installation/image1_fstab.PNG)

La sortie de la commande df -h sur une unité de grande instance HANA S72m ressemblerait à :

![fstab des volumes montés dans l’unité de grande instance HANA](./media/hana-installation/image2_df_output.PNG)


Le contrôleur de stockage et les nœuds dans les tampons de grandes instances sont synchronisés avec les serveurs NTP. Dans la mesure où vous synchronisez les unités SAP HANA sur Azure (grandes instances) et les machines virtuelles Azure avec un serveur NTP, vous ne devriez pas rencontrer d’écart de temps entre l’infrastructure et les unités de calcul dans Azure ou les tampons de grandes instances.

Pour optimiser SAP HANA pour le stockage sous-jacent, définissez également les paramètres de configuration SAP HANA suivants :

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Pour les versions de SAP HANA 1.0 jusqu’à SPS12, ces paramètres peuvent être définis au cours de l’installation de la base de données SAP HANA, comme décrit dans la [Note de support SAP #2267798 - Configuration of the SAP HANA Database](https://launchpad.support.sap.com/#/notes/2267798) (Configuration de la base de données SAP HANA)

Vous pouvez également configurer les paramètres après l’installation de la base de données SAP HANA via le framework hdbparam. 

L’utilisation du framework hdbparam est déconseillée avec SAP HANA 2.0. Il faut donc définir les paramètres à l’aide de commandes SQL. Pour plus d’informations, consultez la [Note de support SAP #2399079 : Elimination of hdbparam in HANA 2](https://launchpad.support.sap.com/#/notes/2399079) (Suppression de hbdparam dans HANA 2).

Consultez [Scénarios HLI pris en charge](hana-supported-scenario.md) pour connaître la disposition du stockage de votre architecture.


**Étapes suivantes**

- Voir [Installation de HANA sur HLI](hana-example-installation.md).










































 







 




