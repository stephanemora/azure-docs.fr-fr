---
title: Questions fréquentes (FAQ) sur les performances SMB pour Azure NetApp Files | Microsoft Docs
description: Réponses aux questions fréquemment posées sur les performances SMB pour Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: b-juche
ms.openlocfilehash: 6391a3eeead6a52371c11011a65f4b4de7260156
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046243"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Questions fréquentes (FAQ) sur les performances SMB pour Azure NetApp Files

Cet article contient les réponses à certaines questions fréquemment posées sur les bonnes pratiques liées aux performances SMB pour Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>SMB Multichannel est-il activé dans les partages SMB ? 

Oui, SMB Multichannel est activé par défaut, changement mis en place début janvier 2020. La fonctionnalité était activée pour tous les partages SMB antérieurs aux volumes SMB existants et elle sera également activée pour tous les volumes au moment de leur création. 

Toute connexion SMB établie avant l’activation de la fonctionnalité Multichannel SMB doit être réinitialisée pour tirer parti de celle-ci. Pour effectuer la réinitialisation, vous pouvez déconnecter et reconnecter le partage SMB.

## <a name="is-rss-supported"></a>RSS est-il pris en charge ?

Oui, Azure NetApp Files prend en charge RSS (receive-side-scaling).

Quand SMB Multichannel est activé, un client SMB3 établit plusieurs connexions TCP au serveur SMB Azure NetApp Files sur une seule carte réseau qui prend en charge RSS. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Quelles versions de Windows prennent en charge SMB Multichannel ?

Windows prend en charge SMB Multichannel depuis Windows 2012 pour optimiser les performances.  Pour plus d’informations, consultez [déployer SMB Multichannel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) et [Notions de base de SMB Multichannel](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/). 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Ma machine virtuelle Azure prend-elle en charge RSS ?

Pour voir si vos cartes réseau de machines virtuelles Azure prennent en charge RSS, exécutez la commande `Get-SmbClientNetworkInterface` suivante et vérifiez le champ `RSS Capable` : 

![Prise en charge de RSS pour une machine virtuelle Azure](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files prend-il en charge SMB Direct ?

Non, Azure NetApp Files ne prend pas en charge SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Quel est l’avantage de SMB Multichannel ? 

La fonctionnalité SMB Multichannel permet à un client SMB3 d’établir un pool de connexions sur une ou plusieurs cartes d’interface réseau et de les utiliser pour envoyer des demandes pour une session SMB unique. À l’inverse, du fait de leur conception, SMB1 et SMB2 obligent le client à établir une connexion et à envoyer sur celle-ci tout le trafic SMB pour une session donnée. Cette connexion unique limite les performances globales du protocole qui peuvent être obtenues à partir d’un seul client.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Dois-je configurer plusieurs cartes réseau sur mon client pour SMB ?

Non. Le client SMB s’adapte au nombre de cartes réseau retourné par le serveur SMB.  Chaque volume de stockage est accessible à partir d’un seul point de terminaison de stockage.  Cela signifie qu’une seule carte réseau est utilisée pour une relation SMB donnée.  

Comme le montre la sortie de la commande `Get-SmbClientNetworkInterace` ci-dessous, la machine virtuelle a deux interfaces réseau : 15 et 12.  Comme indiqué ci-après sous la commande `Get-SmbMultichannelConnection`, même s’il existe deux cartes réseau qui prennent en charge RSS, seule l’interface 12 est utilisée en relation avec le partage SMB ; l’interface 15 n’est pas utilisée.

![Cartes réseau prenant en charge RSS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>L’association de cartes réseau est-elle prise en charge dans Azure ?

L’association de cartes réseau n’est pas prise en charge dans Azure. Bien que plusieurs interfaces réseau soient prises en charge sur les machines virtuelles Azure, elles représentent une construction logique plutôt qu’une construction physique. Ainsi, elles ne fournissent aucune tolérance de panne.  En outre, la bande passante disponible pour une machine virtuelle Azure est calculée pour la machine elle-même, et non pour une interface réseau individuelle.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Quelles sont les performances de SMB Multichannel ?

Les tests et les graphiques suivants illustrent la puissance de SMB Multichannel sur les charges de travail mono-instance.

### <a name="random-io"></a>E/S aléatoires  

Avec SMB Multichannel désactivé sur le client, des tests de lecture et d’écriture de 8 Kio purs ont été effectués à l’aide de l’outil FIO et d’une plage de travail 40 Gio.  Le partage SMB a été détaché entre chaque test, avec augmentation du nombre de connexions clientes SMB en fonction des paramètres d’interface réseau RSS : `1`,`4`,`8`,`16`, `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`. Les tests montrent que le paramètre par défaut de `4` est suffisant pour les charges de travail gourmandes en E/S. L’incrémentation à `8` et `16` n’a eu aucun effet. 

D’après la commande `netstat -na | findstr 445`, des connexions supplémentaires ont été établies avec des incréments de `1` à `4` à `8` et à `16`.  Quatre cœurs de processeur ont été entièrement utilisés pour SMB lors de chaque test, comme le confirme la statistique Perfmon `Per Processor Network Activity Cycles` (non incluse dans cet article).

![Tests d’E/S aléatoires](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

La machine virtuelle Azure n’affecte pas les limites d’E/S de stockage de SMB (ou NFS).  Comme indiqué ci-dessous, le type d’instance D16 a un taux limité de 32 000 pour les IOPS de stockage mises en cache et de 25 600 pour les IOPS de stockage non mises en cache.  Toutefois, le graphique ci-dessus montre beaucoup plus d’E/S sur SMB.

![Comparaison des E/S aléatoires](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>E/S séquentielles 

Des tests similaires aux tests d’E/S aléatoires décrits ci-dessus ont été réalisés avec des E/S séquentielles de 64 Kio. Bien que les augmentations du nombre de connexions clientes par interface réseau RSS au-delà de 4 n’aient eu aucun effet notable sur les E/S aléatoires, il en va différemment pour les E/S séquentielles. Comme le montre le graphique suivant, chaque augmentation est associée à une augmentation correspondante du débit de lecture. Le débit d’écriture est resté le même en raison des restrictions de bande passante du réseau imposées par Azure pour chaque type/taille d’instance. 

![Tests d’E/S séquentielles](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure impose des limites de débit réseau sur chaque type/taille de machine virtuelle. La limite du débit est imposée uniquement au trafic sortant. Le nombre de cartes réseau présentes sur une machine virtuelle n’a aucun impact sur la quantité totale de bande passante disponible pour la machine.  Par exemple, le type d’instance D16 avait une limite réseau imposée de 8 000 Mbits/s (1 000 Mio/s).  Comme le montre le graphique séquentiel ci-dessus, la limite affecte le trafic sortant (écritures), mais pas les lectures multicanal.

![Comparaison des E/S séquentielles](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-advanced-networking-recommended"></a>La mise en réseau avancée est-elle recommandée ?

Pour obtenir des performances optimales, il est recommandé de configurer la mise en réseau avancée dans la mesure du possible. Gardez à l’esprit les points suivants :  

* Le portail Azure active la mise en réseau avancée par défaut pour les machines virtuelles prenant en charge cette fonctionnalité.  Toutefois, il est possible que ce ne soit pas le cas d’autres méthodes de déploiement telles qu’Ansible et les outils de configuration similaires.  L’échec de l’activation de la mise en réseau avancée peut entraver les performances d’une machine.  
* Si la mise en réseau avancée n’est pas activée sur l’interface réseau d’une machine virtuelle en raison de l’absence de prise en charge d’un type ou d’une taille d’instance, elle reste désactivée avec les types d’instances plus grands. Dans ces cas, vous avez besoin d’une intervention manuelle.

## <a name="are-jumbo-frames-supported"></a>Les trames Jumbo sont-elles prises en charge ?

Les trames Jumbo ne sont pas prises en charge avec les machines virtuelles Azure.

## <a name="is-smb-signing-supported"></a>La signature SMB est-elle prise en charge ? 

Le protocole SMB fournit la base pour le partage de fichiers et d’imprimantes ainsi que pour d’autres opérations réseau telles que l’administration à distance de Windows. Pour empêcher les attaques de l’intercepteur qui modifient les paquets SMB en transit, le protocole SMB prend en charge la signature numérique des paquets SMB. 

La signature SMB est prise en charge pour toutes les versions de protocole SMB prises en charge par Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Quel est l’impact de la signature SMB sur les performances ?  

La signature SMB a un effet nuisible sur les performances SMB. Parmi d’autres causes potentielles de la dégradation des performances, la signature numérique de chaque paquet consomme des ressources processeur côté client supplémentaires, comme le montre la sortie Perfmon ci-dessous. Dans ce cas, le cœur 0 semble responsable de SMB, y compris de la signature SMB.  Une comparaison avec les quantités de débit de lecture séquentielle non-multicanal dans la section précédente montre que la signature SMB réduit le débit global de 875 Mio/s à environ 250 Mio/s. 

![Impact sur les performances de la signature SMB](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Étapes suivantes  

- [Questions fréquentes (FAQ) sur Azure NetApp Files](azure-netapp-files-faqs.md)
- Consultez [Azure NetApp Files: Managed Enterprise File Shares for SMB Workloads](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) pour plus d’informations sur l’utilisation de partages de fichiers SMB avec Azure NetApp Files.