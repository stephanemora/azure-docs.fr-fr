---
title: Connecter Azure Percept sur LTE avec un modem USB MultiTech Multiconnect
description: Cet article explique comment connecter Azure Percept DK via des réseaux 5G ou LTE avec un modem USB MultiTech.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 336e67de6d178dcff1b57fe75d57f2272386d2c5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007376"
---
# <a name="connect-azure-percept-over-lte-with-usb-modem-multitech-multiconnect"></a>Connecter Azure Percept sur LTE avec un modem USB MultiTech Multiconnect 
Voici les étapes à suivre pour connecter Azure Percept à l’aide d’un modem USB MultiTech Multiconnect (MTCM-LNA3-B03). 

> [!Note]
> Il existe plusieurs modèles, et nous avons utilisé le LNA3 qui fonctionne au moins avec les cartes SIM de Verizon et Vodafone. Nous n’avons pas pu nous connecter à un réseau AT&T, mais nous étudions cela et le modifierons cette page si nous trouvons la cause racine. Vous trouverez plus d’informations sur le matériel de ce modem particulier sur cette page : https://www.multitech.com/brands/multiconnect-microcell.

## <a name="preparation"></a>Préparation
Assurez-vous d’avoir effectué la connexion à Azure Percept à l’aide d’un [modem USB](./connect-over-cellular-usb.md) et noté les commentaires sur les câbles USB à utiliser. 

### <a name="preparation-of-the-modem"></a>Préparation du modem
Pour commencer, nous devons utiliser le modem en mode MBIM. La procédure à suivre est disponible dans le guide de référence des commandes AT ici : https://www.telit.com/wp-content/uploads/2018/01/Telit-LE910-V2-Modules-AT-Commands-Reference-Guide-r3.pdf.

Nous utilisons la commande AT `AT#USBCFG=<mode>` pour configurer le mode USB approprié afin d’activer l’interface MBIM.

Le guide de référence des commandes AT répertorie tous les modes possibles, mais nous sommes intéressés par le mode `3`, dont la valeur par défaut est `0`.

Le moyen le plus simple de configurer le mode consiste à connecter le modem MultiTech à un PC et à utiliser un logiciel de terminal comme TeraTerm ou le logiciel PC Putty. En utilisant Gestionnaire d’appareils Windows, vous pouvez voir quel port USB est attribué au modem ; vous devrez peut-être tester lequel répond aux commandes AT, s’il y en a plusieurs. Les paramètres du port COM doivent être les suivants : Débit en baud : 9600 (ou 115200) Arrêt : 1 Parité : aucun Taille en octets : 8 Contrôle de flux : aucun flux de contrôle

Voici les commandes AT : Pour vérifier le mode USB actuel de l’appareil MultiTech :
```
AT#USBCFG?
```
Passez au mode 3 :
```
AT#USBCFG=3
```
Si vous vérifiez à nouveau à l’aide de la première commande at, vous devez obtenir : `#USBCFG: 3`.

Une fois que vous avez défini le mode USB correct, vous devez émettre une réinitialisation avec :
```
AT#REBOOT
```
À ce stade, le modem doit se déconnecter et se reconnecter ultérieurement au port USB en utilisant le mode défini ci-dessus.

## <a name="using-the-modem-to-connect"></a>Utilisation du modem pour se connecter
Assurez-vous que vous avez effectué les préparatifs d’Azure Percept décrits dans [Établir une connexion avec un modem USB](./connect-over-cellular-usb.md).   

**1. Insérez une carte SIM dans le modem MultiTech**.

**2. Branchez le modem MultiTech dans le port USB A d’Azure Percept**.

**3. Mettez Azure Percept sous tension**.

**4. Connectez-vous en SSH à Azure Percept DK**.

**5. Vérifiez que ModemManager est en cours d’exécution**.

Écrivez la commande suivante dans votre invite SSH :
```
systemctl status ModemManager
```
Si tout est correct, vous obtiendrez un résultat similaire à ce qui suit :

*ModemManager.service - Modem Manager*
*Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)* 
*Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago*

**6. Listez les modems actifs**.

Dans ce cas, vous devriez voir que le modèle FIH7160 a été reconnu par ModemManager.
```
mmcli --list-modems
```
Vous obtiendrez un résultat semblable à ceci : */org/freedesktop/ModemManager1/Modem/0 [Telit] FIH7160*.

**7. Obtenez les détails du modem**.

L’ID de modem est ici `0`, ce qui peut être différent dans votre cas. L’ID de modem (`--modem 0`) est utilisé dans les commandes de ModemManager comme suit :
```
mmcli --modem 0
```
Par défaut, le modem est désactivé (`Status -> state: disabled`).
```
 --------------------------------
  General  |                 path: /org/freedesktop/ModemManager1/Modem/0
           |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
  --------------------------------
  Hardware |         manufacturer: Telit
           |                model: FIH7160
           |    firmware revision: 20.00.525
           |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
           |            supported: gsm-umts, lte
           |              current: gsm-umts, lte
           |         equipment id: xxxx
  --------------------------------
  System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
           |              drivers: cdc_acm, cdc_mbim
           |               plugin: telit
           |         primary port: cdc-wdm0
           |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
           |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
           |                       wwan0 (net)
  --------------------------------
  Status   |       unlock retries: sim-pin2 (3)
           |                state: disabled
           |          power state: on
           |       signal quality: 0% (cached)
  --------------------------------
  Modes    |            supported: allowed: 3g; preferred: none
           |                       allowed: 4g; preferred: none
           |                       allowed: 3g, 4g; preferred: none
           |              current: allowed: 3g, 4g; preferred: none
  --------------------------------
  Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
           |                       eutran-13, eutran-17
           |              current: utran-2, eutran-2
  --------------------------------
  IP       |            supported: ipv4, ipv6, ipv4v6
  --------------------------------
  3GPP     |                 imei: xxxxxxxxxxxxxxx
           |        enabled locks: fixed-dialing
  --------------------------------
  3GPP EPS | ue mode of operation: csps-2
  --------------------------------
  SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
```

**8. Activez le modem**.

Avant d’établir une connexion, nous devons allumer la ou les radios du modem.
```
mmcli --modem 0 --enable
```
Et vous devriez obtenir une réponse de ce type : *Activation réussie du modem*.

Après un certain temps, le modem doit s’inscrire auprès d’une antenne, et vous devez voir dans l’état du modem : `Status -> state: registered`, si vous exécutez à nouveau :
```
mmcli --modem 0
```

**9. Connectez-vous à l’aide des informations APN**.

Le nom du point d’accès (APN) est fourni par votre opérateur de téléphone portable, comme ici pour Verizon :
```
mmcli --modem 0 --simple-connect="apn=vzwinternet"  
```
Et si tout est OK, vous aurez bien le message *connecté au modem*.

**10. Récupérez l’état du modem**.

À présent, vous devez voir `Status -> state: connected` et une nouvelle catégorie `Bearer` à la fin du message d’état.
```
mmcli --modem 0
```
```
 --------------------------------
  General  |                 path: /org/freedesktop/ModemManager1/Modem/0
           |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
  --------------------------------
  Hardware |         manufacturer: Telit
           |                model: FIH7160
           |    firmware revision: 20.00.525
           |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
           |            supported: gsm-umts, lte
           |              current: gsm-umts, lte
           |         equipment id: xxxx
  --------------------------------
  System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
           |              drivers: cdc_acm, cdc_mbim
           |               plugin: telit
           |         primary port: cdc-wdm0
           |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
           |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
           |                       wwan0 (net)
  --------------------------------
  Numbers  |                  own: +1xxxxxxxx
  --------------------------------
  Status   |       unlock retries: sim-pin2 (3)
           |                state: connected
           |          power state: on
           |          access tech: lte
           |       signal quality: 16% (recent)
  --------------------------------
  Modes    |            supported: allowed: 3g; preferred: none
           |                       allowed: 4g; preferred: none
           |                       allowed: 3g, 4g; preferred: none
           |              current: allowed: 3g, 4g; preferred: none
  --------------------------------
  Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
           |                       eutran-13, eutran-17
           |              current: utran-2, eutran-2
  --------------------------------
  IP       |            supported: ipv4, ipv6, ipv4v6
  --------------------------------
  3GPP     |                 imei: xxxxxxxxxxxxxxx
           |        enabled locks: fixed-dialing
           |          operator id: 311480
           |        operator name: Verizon
           |         registration: home
  --------------------------------
  3GPP EPS | ue mode of operation: csps-2
  --------------------------------
  SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
  --------------------------------
  Bearer   |                paths: /org/freedesktop/ModemManager1/Bearer/0
```

**11. Procurez-vous les détails du porteur**.

Les détails du porteur sont nécessaires pour connecter le système d’exploitation à la connexion de données de paquets que le modem a établie avec le réseau cellulaire. À ce stade, le modem dispose d’une connexion IP, mais le système d’exploitation n’est pas encore configuré pour l’utiliser.
```
mmcli --bearer 0
```
Détails du porteur :
```
 ------------------------------------
  General            |           path: /org/freedesktop/ModemManager1/Bearer/0
                     |           type: default
  ------------------------------------
  Status             |      connected: yes
                     |      suspended: no
                     |      interface: wwan0
                     |     ip timeout: 20
  ------------------------------------
  Properties         |            apn: vzwinternet
                     |        roaming: allowed
  ------------------------------------
  IPv4 configuration |         method: static
                     |        address: 100.112.107.46
                     |         prefix: 24
                     |        gateway: 100.112.107.1
                     |            dns: 198.224.166.135, 198.224.167.135
  ------------------------------------
  Statistics         |       duration: 119
                     |       attempts: 1
                     | total-duration: 119
```

**12. Ouvrez l’interface réseau**.

```
sudo ip link set dev wwan0 up
```

**13. Configurez l’interface réseau**.

À l’aide des informations fournies par le porteur, remplacez l’adresse IP (ici 100.112.107.46/24) par celle de votre porteur :
```
sudo ip address add 100.112.107.46/24 dev wwan0
```

**14. Vérifiez les informations IP**.

La configuration IP de cette interface doit correspondre aux détails du porteur ModemManager.
```
sudo ip address show dev wwan0
```
et vérifiez que l’adresse IP de votre porteur est indiquée ci-dessous :
```
6: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1428 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether 1e:fb:08:e9:2a:25 brd ff:ff:ff:ff:ff:ff
    inet 100.112.107.46/24 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::1cfb:8ff:fee9:2a25/64 scope link
       valid_lft forever preferred_lft forever
```

**15. Définissez l’itinéraire par défaut**.

Utilisez à nouveau les informations fournies par le porteur et utilisez la passerelle du modem (remplacez 100.112.107.1) comme destination par défaut des paquets réseau :
```
sudo ip route add default via 100.112.107.1 dev wwan0
```
Désormais, Azure Percept dispose d’une connexion qui utilise le modem USB.

**16. Testez la connectivité**.

Nous exécutons une requête `ping` par le biais de l’interface `wwan0`. Toutefois, vous pouvez également utiliser Azure Percept Studio et vérifier si les messages de télémétrie arrivent (assurez-vous que vous n’avez pas de connexion Ethernet ou Wi-Fi activée afin d’être certain d’utiliser la connexion LTE).
```
ping -I wwan0 8.8.8.8
```
Vous devriez obtenir :
```
PING 8.8.8.8 (8.8.8.8) from 162.177.2.0 wwan0: 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=111 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=92.0 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=88.8 ms
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 4ms
rtt min/avg/max/mdev = 88.779/97.254/110.964/9.787 ms
```


## <a name="debugging"></a>Débogage
De manière générale, consultez [Établir une connexion avec un modem USB](./connect-over-cellular-usb.md).

## <a name="next-steps"></a>Étapes suivantes
[Établir une connexion avec un modem USB](./connect-over-cellular-usb.md).

Retour à l’article principal sur le réseau 5G ou LTE :

[Établir une connexion 5G ou LTE](./connect-over-cellular.md).
   

