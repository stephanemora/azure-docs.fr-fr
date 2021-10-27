---
title: Connecter Azure Percept sur 5G ou LTE avec un modem USB Vodafone
description: Cet article explique comment connecter Azure Percept DK via des réseaux 5G ou LTE avec un modem USB Vodafone.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 6264bd855cdc4ff186f51748bf426573e6316e5c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007351"
---
# <a name="connect-azure-percept-over-lte-with-usb-modem-vodafone-usb-connect-4g"></a>Connecter Azure Percept sur LTE avec un modem Vodafone USB Connect 4G
Voici les étapes à suivre pour connecter votre site Azure Percept à l’aide d’un modem Vodafone USB Connect 4G v2.
Vous trouverez plus d’informations sur ce modem via Vodafone sur cette page : https://www.vodafone.com/business/iot/iot-devices/integrated-terminals

## <a name="using-the-modem-to-connect"></a>Utilisation du modem pour se connecter
Assurez-vous que vous avez effectué les préparatifs d’Azure Percept DK dans [Établir une connexion avec un modem USB](./connect-over-cellular-usb.md). Aucune préparation n’est nécessaire pour le modem USB lui-même.   

**1. Branchez une carte SIM dans le modem Vodafone**

**2. Branchez le modem Vodafone dans le port USB A d’Azure Percept**

**3. Lancez Azure Percept**

**4. Connectez-vous en SSH à Azure Percept DK**

**5. Vérifiez que ModemManager est en cours d’exécution**

Écrivez la commande suivante dans votre invite SSH :
```
systemctl status ModemManager
```
Si tout est correct, vous obtiendrez un résultat similaire à ce qui suit :

*ModemManager.service - Modem Manager*
*Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)*
*Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago*

**6. Listez les modems actifs**

Nous allons ensuite vérifier que ModemManager peut reconnaître votre modem.
```
mmcli --list-modems
```
Vous obtiendrez un résultat similaire à celui-ci, où l’ID de modem est `0`, mais il peut s’agir d’un autre nom :

 */org/freedesktop/ModemManager1/Modem/0 [Alcatel] Mobilebroadband*

**7. Obtenez les détails du modem**

Pour afficher les détails de l’état du modem, vous pouvez utiliser la commande et l’ID de modem `0`
```
mmcli --modem 0
```
Par défaut, le modem est désactivé (`Status -> state: disabled`)
```
  --------------------------------
  General  |                 path: /org/freedesktop/ModemManager1/Modem/0
           |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
  --------------------------------
  Hardware |         manufacturer: Alcatel
           |                model: Mobilebroadband
           |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
           |       carrier config: default
           |         h/w revision: 0
           |            supported: gsm-umts, lte
           |              current: gsm-umts, lte
           |         equipment id: xxx
  --------------------------------
  System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
           |              drivers: option, cdc_mbim
           |               plugin: generic
           |         primary port: cdc-wdm0
           |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
           |                       ttyUSB2 (at), wwan0 (net)
  --------------------------------
  Status   |       unlock retries: sim-pin2 (3)
           |                state: disabled
           |          power state: on
           |       signal quality: 0% (cached)
  --------------------------------
  Modes    |            supported: allowed: 2g; preferred: none
           |                       allowed: 3g; preferred: none
           |                       allowed: 4g; preferred: none
           |                       allowed: 2g, 3g; preferred: 3g
           |                       allowed: 2g, 3g; preferred: 2g
           |                       allowed: 2g, 4g; preferred: 4g
           |                       allowed: 2g, 4g; preferred: 2g
           |                       allowed: 3g, 4g; preferred: 4g
           |                       allowed: 3g, 4g; preferred: 3g
           |                       allowed: 2g, 3g, 4g; preferred: 4g
           |                       allowed: 2g, 3g, 4g; preferred: 3g
           |                       allowed: 2g, 3g, 4g; preferred: 2g
           |              current: allowed: 2g, 3g, 4g; preferred: 2g
  --------------------------------
  Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
           |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
           |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
           |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
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
Nous vous recommandons de commencer par le paramètre par défaut : `Modes: current: allowed: 2g, 3g, 4g; preferred: 2g`, qui peut être défini, si n’utilise pas déjà `mmcli --modem 0 --set-allowed-modes='2g|3g|4g' --set-preferred-mode='2g'`.

**8. Activez le modem**

Avant d’établir une connexion, nous devons allumer la ou les radios du modem.
```
mmcli --modem 0 --enable
```
Et vous devriez obtenir une réponse de ce type : *Activation du modem réussie*

Après un certain temps, le modem doit s’inscrire sur une antenne et vous devez voir dans l’état du modem : `Status -> state: registered`, si vous relancez :
```
mmcli --modem 0
```

**9. Connectez-vous à l’aide des informations APN**

Le nom du point d’accès (APN) est fourni par votre opérateur de téléphone portable, comme ici pour Vodafone :
```
mmcli --modem 0 --simple-connect="apn=internet4gd.gdsp"  
```
et si tout est OK, vous aurez bien *connecté au modem*

**10. Récupérez l’état du modem**

À présent, vous devez voir `Status -> state: connected` et une nouvelle catégorie `Bearer` à la fin du message d’état.
```
mmcli --modem 0
```
```
  --------------------------------
  General  |                 path: /org/freedesktop/ModemManager1/Modem/0-mobile.
           |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
  --------------------------------
  Hardware |         manufacturer: Alcatel
           |                model: Mobilebroadband
           |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
           |       carrier config: default
           |         h/w revision: 0
           |            supported: gsm-umts, lte
           |              current: gsm-umts, lte
           |         equipment id: xxx
  --------------------------------
  System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
           |              drivers: option, cdc_mbim
           |               plugin: generic
           |         primary port: cdc-wdm0
           |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
           |                       ttyUSB2 (at), wwan0 (net)
  --------------------------------
  Numbers  |                  own: xxx
  --------------------------------
  Status   |       unlock retries: sim-pin2 (10)
           |                state: connected
           |          power state: on
           |          access tech: lte
           |       signal quality: 19% (recent)
  --------------------------------
  Modes    |            supported: allowed: 2g; preferred: none
           |                       allowed: 3g; preferred: none
           |                       allowed: 4g; preferred: none
           |                       allowed: 2g, 3g; preferred: 3g
           |                       allowed: 2g, 3g; preferred: 2g
           |                       allowed: 2g, 4g; preferred: 4g
           |                       allowed: 2g, 4g; preferred: 2g
           |                       allowed: 3g, 4g; preferred: 4g
           |                       allowed: 3g, 4g; preferred: 3g
           |                       allowed: 2g, 3g, 4g; preferred: 4g
           |                       allowed: 2g, 3g, 4g; preferred: 3g
           |                       allowed: 2g, 3g, 4g; preferred: 2g
           |              current: allowed: 2g, 3g, 4g; preferred: 2g
  --------------------------------
  Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
           |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
           |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
           |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
  --------------------------------
  IP       |            supported: ipv4, ipv6, ipv4v6
  --------------------------------
  3GPP     |                 imei: xxxxxxxxxxxxxxx
           |        enabled locks: fixed-dialing
           |          operator id: 302220
           |        operator name: TELUS
           |         registration: roaming
  --------------------------------
  3GPP EPS | ue mode of operation: csps-2
  --------------------------------
  SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
  --------------------------------
  Bearer   |                paths: /org/freedesktop/ModemManager1/Bearer/0
```

**11. Procurez-vous les détails du porteur**

Les détails du porteur sont nécessaires pour connecter le système d’exploitation à la connexion de données de paquets que le modem a établie avec le réseau cellulaire. À ce stade, le modem dispose d’une connexion IP, mais le système d’exploitation n’est pas encore configuré pour l’utiliser.

```
mmcli --bearer 0
```
Détails du porteur :
```
  --------------------------------
  General            |       path: /org/freedesktop/ModemManager1/Bearer/0
                     |       type: default
  --------------------------------
  Status             |  connected: yes
                     |  suspended: no
                     |  interface: wwan0
                     | ip timeout: 20
  --------------------------------
  Properties         |        apn: internet4gd.gdsp
                     |    roaming: allowed
  --------------------------------
  IPv4 configuration |     method: static
                     |    address: 162.177.2.0
                     |     prefix: 22
                     |    gateway: 162.177.2.1
                     |        dns: 10.177.0.34, 10.177.0.210
                     |        mtu: 1500
  --------------------------------
  Statistics         |   attempts: 1
```

**12. Ouvrez l’interface réseau**

```
sudo ip link set dev wwan0 up
```

**13. Configurez l’interface réseau**

À l’aide des informations fournies par les détails du porteur, remplacez l’adresse IP (ici 162.177.2.0/22) par celle de votre porteur :
```
sudo ip address add 162.177.2.0/22 dev wwan0
```

**14. Vérifiez les informations IP**

La configuration IP de cette interface doit correspondre aux détails du porteur ModemManager.
```
sudo ip address show dev wwan0
```
et vérifiez que l’adresse IP de vptre porteur est indiquée ci-dessous :
```
wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether c2:12:44:c4:27:3c brd ff:ff:ff:ff:ff:ff
    inet 162.177.2.0/22 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::c012:44ff:fec4:273c/64 scope link 
       valid_lft forever preferred_lft forever
```

**15. Définissez l’itinéraire par défaut**

À l’aide des informations fournies par le porteur et en utilisant la passerelle du modem (remplacez 162.177.2.1) comme destination par défaut pour les paquets réseau :
```
sudo ip route add default via 162.177.2.1 dev wwan0
```
Vous devez maintenant avoir activé Azure Percept pour qu’il se connecte à Azure à l’aide du modem LTE.


**16. Testez la connectivité**

Nous exécutons une requête `ping` par le biais de l’interface `wwan0`. Toutefois, vous pouvez également utiliser Azure Percept Studio et vérifier si les messages de télémétrie arrivent (assurez-vous que vous n’avez pas de connexion Ethernet ou Wi-Fi activée afin d’être certain d’utiliser la connexion LTE).
```
ping -I wwan0 8.8.8.8
```
et vous devriez obtenir
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
En général, consultez [Établir une connexion avec un modem USB](./connect-over-cellular-usb.md).
   
### <a name="vodafone-modem-rules-to-mitigate-enumeration-issues"></a>Règles de modem Vodafone pour résoudre les problèmes d’énumération

Afin d’empêcher le modem d’effectuer une énumération dans un mode non pris en charge, nous vous suggérons d’utiliser les règles UDEV ci-dessous pour que ModemManager ignore les interfaces indésirables.

Fichier à créer : `/usr/lib/udev/rules.d/77-mm-vodafone-port-types.rules`, avec le contenu sous la forme :
```
ACTION!="add|change|move|bind", GOTO="mm_vodafone_port_types_end"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="1bbb", GOTO="mm_vodafone_generic_vendorcheck"
GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_generic_vendorcheck"
SUBSYSTEMS=="usb", ATTRS{bInterfaceNumber}=="?*", ENV{.MM_USBIFNUM}="$attr{bInterfaceNumber}"

# Interface 1 is QDCM (ignored) and interfaces 3 and 4 are MBIM Control and Data.
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="00", ENV{ID_MM_PORT_TYPE_AT_PRIMARY}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="01", ENV{ID_MM_PORT_IGNORE}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="02", ENV{ID_MM_PORT_AT_SECONDARY}="1"

GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_port_types_end"
```
Une fois l’installation terminée, rechargez les règles UDEV et redémarrez ModemManager :
```
udevadm control -R
systemctl restart ModemManager
```
## <a name="next-steps"></a>Étapes suivantes
[Établir une connexion avec un modem USB](./connect-over-cellular-usb.md).

Retour à l’article principal sur 5G ou LTE :

[Établir une connexion 5G ou LTE](./connect-over-cellular.md).
