---
title: Synchronisation de l’heure pour les machines virtuelles Linux dans Azure
description: Synchronisation de l’heure pour les machines virtuelles Linux.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/30/2021
ms.author: cynthn
ms.openlocfilehash: c50e39db804a18d50f4a6fb594209cc015515a8c
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108754736"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Synchronisation de l’heure pour les machines virtuelles Linux dans Azure

La synchronisation de l’heure est importante pour la sécurité et la corrélation des événements. Elle est parfois utilisée pour l’implémentation de transactions distribuées. La précision de l’heure entre plusieurs systèmes informatiques est obtenue via la synchronisation. La synchronisation peut être influencée par plusieurs facteurs, tels que les redémarrages et le trafic réseau entre la source de l’heure et l’ordinateur qui récupère l’heure. 

Azure est soutenu par une infrastructure exécutant Windows Server 2016. Windows Server 2016 utilise des algorithmes améliorés qui permettent de corriger l’heure et de configurer l’horloge locale pour la synchroniser avec l’heure UTC.  La fonctionnalité d’heure précise de Windows Server 2016 a considérablement amélioré la façon dont le service VMICTimeSync régit les machines virtuelles avec l’hôte pour une heure précise. Les améliorations incluent une heure initiale plus précise au démarrage ou lors de la restauration d’une machine virtuelle, ainsi que l’interruption de la correction de la latence. 

> [!NOTE]
> Pour une présentation rapide du service d’heure de Windows, regardez cette [vidéo de présentation globale](https://aka.ms/WS2016TimeVideo).
>
> Pour plus d’informations, consultez la rubrique [Précision de l’heure sur Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Vue d’ensemble

La précision de l’horloge de l’ordinateur est évaluée en fonction de la proximité de l’horloge de l’ordinateur par rapport à la norme de temps universel coordonné (UTC). L’heure UTC est définie par l’exemple multinational des horloges atomiques précises qui ne peuvent être décalées que d’une seconde en 300 ans. Toutefois, la lecture directe de l’heure UTC requiert du matériel spécialisé. Au lieu de cela, les serveurs de temps sont synchronisés à l’heure UTC et sont accessibles depuis d’autres ordinateurs dans un souci d’évolutivité et de fiabilité. Chaque ordinateur possède un service de synchronisation d’heure en cours d’exécution qui sait quels serveurs de temps utiliser et vérifie régulièrement si l’horloge de l’ordinateur doit être corrigée, et ajuste l’heure si nécessaire. 

Les hôtes Azure sont synchronisés avec les serveurs de temps internes Microsoft qui extraient l’heure depuis des appareils de couche 1 appartenant à Microsoft, grâce à des antennes GPS. Les machines virtuelles dans Azure peuvent dépendre de leur hôte pour transférer l’heure précise (*heure de l’hôte*) sur la machine virtuelle. La machine virtuelle peut également obtenir directement l’heure à partir d’un serveur de temps ou par une combinaison des deux. 

Sur un matériel autonome, le système d’exploitation Linux lit uniquement l’horloge du matériel hôte au démarrage. Ensuite, l’horloge est gérée à l’aide de la minuterie d’interruption dans le noyau Linux. Dans cette configuration, l’horloge présente un décalage au fil du temps. Dans les nouvelles distributions Linux sur Azure, les machines virtuelles peuvent utiliser le fournisseur VMICTimeSync, inclus dans les services d’intégration Linux (LIS), pour demander des mises à jour de l’horloge à l’hôte plus fréquemment.

Les interactions de la machine virtuelle avec l’hôte peuvent également influer sur l’horloge. Au cours de la [maintenance avec préservation de la mémoire](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot), les machines virtuelles sont interrompues jusqu’à 30 secondes. Par exemple, avant le début de la maintenance, l’horloge de la machine virtuelle affiche 10:00:00 pendant 28 secondes. Une fois que la machine virtuelle reprend, l’horloge affiche toujours 10:00:00 et est donc décalée de 28 secondes. Pour corriger cela, le service VMICTimeSync surveille ce qui se passe sur l’hôte et met à jour l’horloge sur les machines virtuelles Linux pour compenser.

Si la synchronisation de l’heure ne fonctionne pas, l’horloge de la machine virtuelle accumule les erreurs. S’il n’y a qu’une seule machine virtuelle, l’effet n’est pas forcément significatif, sauf si la charge de travail nécessite une synchronisation extrêmement précise de l’horloge. Mais dans la plupart des cas, nous possédons plusieurs machines virtuelles interconnectées qui utilisent l’heure pour suivre les transactions. L’heure doit donc être cohérente tout au long du déploiement. Lorsque l’heure est différente d’une machine virtuelle à l’autre, vous pouvez rencontrer les effets suivants :

- L’authentification échoue. Les protocoles de sécurité, comme Kerberos, ou les technologies dépendantes d’un certificat reposent sur la précision de l’heure entre les systèmes.
- Il est très difficile de déterminer ce qu’il peut se passer dans un système si l’heure des journaux d’activité (ou d’autres données) diffère. Un même événement peut avoir l’air de s’être produit à différents moments, rendant la corrélation difficile.
- Si l’horloge est désactivée, la facturation peut être calculée de manière incorrecte.


## <a name="configuration-options"></a>Options de configuration

La synchronisation de l’heure requiert un service de synchronisation de l’heure qui s’exécute sur la machine virtuelle Linux, ainsi qu’une source d’informations précise sur laquelle effectuer la synchronisation.
En général, ntpd ou chronyd est utilisé en tant que service de synchronisation, bien qu’il existe d’autres services de synchronisation de l’heure open source qui puissent également être utilisés.
La source d’information de l’heure peut être l’hôte Azure ou un service externe accessible via l’Internet public.
En soi, le service VMICTimeSync ne fournit pas de synchronisation continue entre l’hôte Azure et une machine virtuelle Linux, sauf après des pauses pour la maintenance de l’hôte, comme décrit ci-dessus. 

Historiquement, la plupart des images de la place de marché Azure avec Linux ont été configurées de deux façons :
- Aucun service de synchronisation d’heure n’est exécuté par défaut
- ntpd s’exécute en tant que service de synchronisation de l’heure et effectue la synchronisation avec une source NTP externe accessible sur le réseau. Par exemple, les images de la place de marché Ubuntu 18.04 LTS utilisent **ntp.ubuntu.com**.

Pour vérifier la synchronisation correcte de ntpd, exécutez la commande `ntpq -p`.

Depuis le début de l’année 2021, les images Azure Marketplace les plus récentes sont modifiées pour utiliser chronyd en tant que service de synchronisation de l’heure et chronyd est configuré pour se synchroniser sur l’hôte Azure plutôt que sur une source NTP externe. L’heure de l’hôte Azure est généralement la meilleure source de synchronisation par rapport à, car elle est gérée de façon très précise et fiable, et est accessible sans les retards réseau inhérents à l’accès à une source NTP externe sur l’Internet public.

VMICTimeSync est utilisé en parallèle et fournit deux fonctions :
- Met immédiatement à jour l’horloge de la machine virtuelle Linux après un événement de maintenance de l’ordinateur hôte
- Instancie une source d’horloge matérielle PTP (Precision Time Protocol) IEEE 1588 en tant qu’appareil/dev/ptp qui fournit l’heure à partir de l’hôte Azure.  Chronyd peut être configuré pour se synchroniser par rapport à cette source (qui est la configuration par défaut dans les images Linux les plus récentes). Les distributions Linux avec noyau version 4.11 ou ultérieure (ou version 3.10.0-693 ou version ultérieure pour RHEL/CentOS 7) prennent en charge appareil/dev/ptp.  Pour les versions antérieures du noyau qui ne prennent pas en charge/dev/ptp pour l’heure de l’hôte Azure, seule la synchronisation par rapport à une source externe est possible.

Bien entendu, la configuration par défaut peut être modifiée. Une image plus ancienne configurée pour utiliser ntpd et une source externe peut être modifiée pour utiliser chronyd et l’appareil /dev/ptp pour l’heure de l’hôte Azure.  De même, une image utilisant l’heure de l’hôte Azure via un appareil/dev/ptp peut être configurée pour utiliser une source NTP externe si votre application ou votre charge de travail l’exige.


## <a name="tools-and-resources"></a>Outils et ressources

Il existe des commandes de base pour la vérification de la configuration de synchronisation de l’heure. La documentation sur la distribution de Linux offre plus de détails sur la meilleure façon de configurer la synchronisation de l’heure pour cette distribution.

### <a name="integration-services"></a>Service d’intégration

Vérifiez si le service d’intégration (hv_utils) est chargé.

```bash
lsmod | grep hv_utils
```
Vous devez voir quelque chose de semblable à ceci :

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

### <a name="check-for-ptp-clock-source"></a>Vérifier la source de l’horloge PTP

Avec les versions plus récentes de Linux, une source d’horloge PTP correspondant à l’hôte Azure est disponible dans le cadre du fournisseur VMICTimeSync.
Dans les versions antérieures de Red Hat Enterprise Linux ou CentOS 7.x, les [service d’intégration Linux](https://github.com/LIS/lis-next) peuvent être téléchargé et utilisés pour installer le pilote mis à jour. Lorsque la source de l’horloge PTP est disponible, le périphérique Linux se présente sous la forme /dev/ptp *x*. 

Consultez les sources d’horloge PTP disponibles.

```bash
ls /sys/class/ptp
```

Dans cet exemple, la valeur renvoyée est *ptp0*, ce qui permet de vérifier le nom de l’horloge. Pour vérifier l’appareil, consultez le nom de l’horloge.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Cela doit retourner `hyperv`, c’est-à-dire l’hôte Azure.

Dans les machines virtuelles Linux sur lesquelles les performances réseau accélérées sont activées, vous pouvez voir plusieurs appareils PTP, car le pilote Mellanox mlx5 crée également un appareil/dev/ptp.
Étant donné que l’ordre d’initialisation peut être différent à chaque démarrage de Linux, l’appareil PTP correspondant à l’hôte Azure peut être/dev/ptp0 ou/dev/ptp1, ce qui rend difficile la configuration de chronyd avec la source correcte. Pour résoudre ce problème, les images Linux les plus récentes ont une règle udev qui crée le lien symbolique/dev/ptp_hyperv vers l’entrée/dev/ptp correspondant à l’hôte Azure. Chrony doit être configuré pour utiliser ce symlink au lieu de/dev/ptp0 ou/dev/ptp1.

### <a name="chrony"></a>chrony

Sur Ubuntu 19.10 et versions ultérieures, Red Hat Enterprise Linux et CentOS 8.x, [chrony](https://chrony.tuxfamily.org/) est configuré pour utiliser une horloge de source PTP. Au lieu de Chrony, les versions antérieures de Linux utilisent le démon ntpd (Network Time Protocol Daemon), qui ne prend pas en charge les sources PTP. Pour activer PTP dans ces versions, chrony doit être installé et configuré manuellement (dans chrony.conf) à l’aide de l’instruction suivante :

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```
Comme mentionné précédemment, si le lien symbolique /dev/ptp_hyperv est disponible, utilisez-le à la place de/dev/ptp0 pour éviter toute confusion avec l’appareil/dev/ptp créé par le pilote Mellanox mlx5.

Pour plus d’informations sur Ubuntu et NTP, consultez [Synchronisation temporelle](https://ubuntu.com/server/docs/network-ntp).

Pour plus d’informations sur Red Hat et NTP, consultez [Configurer NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP). 

Pour plus d’informations sur chrony, consultez [Utilisation de chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony).

Si les sources chrony et VMICTimeSync sont activées simultanément, vous pouvez en marquer une comme **favorite**, l’autre devenant alors la source de secours. Étant donné que les services NTP ne mettent pas à jour l’horloge pour les grands décalages, sauf après une longue période, la source VMICTimeSync permet de récupérer l’horloge à partir d’événements de machine virtuelle en pause beaucoup plus rapidement que les outils NTP seuls.

Par défaut, chronyd accélère ou ralentit l’horloge système pour corriger toute dérive temporelle. En cas de dérive trop importante, chrony n’est pas capable de la résoudre. Pour surmonter cela, le paramètre `makestep` dans **/etc/chrony.conf** peut être modifié pour forcer une synchronisation de l’heure si la dérive dépasse le seuil spécifié.

 ```bash
makestep 1.0 -1
```

Ici, chrony force une mise à jour temporelle si la dérive est supérieure à 1 seconde. Pour appliquer les modifications, redémarrez le service chronyd :

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

Sur les versions SUSE et Ubuntu antérieures à 19.10, la synchronisation de l’heure est configurée à l’aide de [systemd](https://www.freedesktop.org/wiki/Software/systemd/). Pour plus d’informations sur Ubuntu, consultez [Synchronisation de l’heure](https://help.ubuntu.com/lts/serverguide/NTP.html). Pour plus d’informations sur SUSE, consultez la section 4.5.8 des [notes de publication de SUSE Linux Enterprise Server 12 SP3](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez la rubrique [Précision de l’heure sur Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time).


