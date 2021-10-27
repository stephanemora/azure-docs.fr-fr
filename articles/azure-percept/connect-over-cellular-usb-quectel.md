---
title: Connecter Azure Percept sur 5G ou LTE avec un modem USB Quectel RM500
description: Cet article explique comment connecter Azure Percept DK via des réseaux 5G ou LTE avec un modem USB Quectel.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 52914140f80bad9c8bd8bb57bec0938211c25e71
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007354"
---
# <a name="connect-azure-percept-over-5g-or-lte-with-usb-modem-quectel-5g-rm500-gl"></a>Connecter Azure Percept sur 5G ou LTE avec un modem USB Quectel 5G RM500-GL 

Voici les étapes à suivre pour connecter votre Azure Percept à l’aide d’un modem 5G Quectel RM500-GL. Vous pouvez contacter l’équipe de vente locale Quectel pour plus d’informations sur ce kit de développeur de modem 5G :

northamerica-sales@quectel.com – pour les clients NA

sales@quectel.com – pour les clients mondiaux

> [!Note] 
> à propos des modems 5G et des câbles USB Cela s’applique uniquement aux modems 5G. Les modems 5G requièrent plus d’énergie que les modems LTE. En outre, le câble USB peut devenir un goulot d’étranglement pour les meilleurs taux de données 5G. Pour fournir une alimentation suffisante et cohérente à un modem 5G, vous devez vous assurer que le câble USB n’est pas trop long et qu’il peut supporter au moins un courant de 3 A. Pour un débit maximal, vous devez utiliser des câbles USB 3.1 Gen2 et vérifier qu’il existe un logo USB indiquant la certification. Donc, en résumé : **Pour Power :**
> - L’intensité maximale doit être supérieure ou égale à 3 A
> - Moins de 1 m de long
> - Lors de l’utilisation de modems 5G, un seul port USB sur Azure Percept DK doit être actif
>  
> **Pour le débit :**
> - USB 3.1 Gen2
> - Certifié USB-IF

## <a name="preparation"></a>Préparation
Assurez-vous d’avoir effectué la connexion à Azure Percept à l’aide d’un [modem USB](./connect-over-cellular-usb.md) et noté les commentaires sur les câbles USB à utiliser. 

### <a name="preparation-of-the-modem"></a>Préparation du modem
Pour commencer, nous devons utiliser le modem en mode MBIM. Pour ce faire, vous pouvez utiliser une commande non documentée mais standard Quectel AT : `AT+QCFG="usbnet"`.

La propriété `usbnet` peut être définie sur quatre valeurs différentes, de `0` à `3` :
- `0` pour le **mode NDIS/PPP/QMI** (pris en charge par le pilote `qmi_wwan`, activé avec `CONFIG_USB_NET_QMI_WWAN=y|m` )
- `1` pour le **mode Ethernet CDC** (pris en charge dans Linux lorsque `CONFIG_USB_NET_CDCETHER=y|m`)
- `2` pour le **mode MBIM** (pris en charge dans Linux lorsque `CONFIG_USB_NET_CDC_MBIM=y|m`)
- `3` pour le **mode RNDIS**

Le moyen le plus simple de configurer le mode consiste à connecter le modem Quectel 5G à un PC et à utiliser des logiciels terminal tels que TeraTerm ou Quectel de PC, comme QCOM. À l’aide du Gestionnaire de périphériques Windows, vous pouvez voir le port USB affecté aux commandes AT et l’utiliser. Les paramètres du port COM doivent être les suivantes : Débit en baud : 115200 Arrêt : 1 Parité : aucun Taille en octets : 8 Contrôle de flux : aucun flux de contrôle

Et voici les commandes AT : pour vérifier l’appareil Quectel en mode USB :
```
AT+QCFG="usbnet"
```
Passez au mode 2 :
```
AT+QCFG="usbnet",2
```
Si vous vérifiez à nouveau à l’aide de la première commande at, vous devez obtenir : "+QCFG: "usbnet",2"

Une fois que vous avez défini le mode USB correct, vous devez émettre une réinitialisation matérielle avec :
```
AT+CFUN=1,1
```
À ce stade, le modem doit se déconnecter et se reconnecter ultérieurement au port USB.


## <a name="using-the-modem-to-connect"></a>Utilisation du modem pour se connecter

**1. Placez une carte SIM dans le modem Quectel**

**2. Branchez le modem Quectel sur le port USB Azure Percept. N’oubliez pas d’utiliser un câble USB correct.**

**3. Mettez Azure Percept sous tension**

**4. Vérifiez que ModemManager est en cours d’exécution**

```
systemctl status ModemManager
```
Vous devez obtenir un résultat similaire à ce qui suit :
```
* ModemManager.service - Modem Manager
   Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor pre set: enabled)
   Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23s ago
[...]
```
Si ce n’est pas le cas, vous devez vous assurer que vous avez bien copié l’image adéquate sur votre Azure Percept (fonctionnalité 5G activée).

**5. Dressez la liste des modems actifs**

Lorsque vous répertoriez les modems, vous devez voir que le modem Quectel a été reconnu et qu’il est maintenant géré par ModemManager.
```
mmcli --list-modems
```
Vous obtenez une liste qui ressemble à ceci :
```
 /org/freedesktop/ModemManager1/Modem/0 [Quectel] RM500Q-GL
```
L’ID de modem ici `0` est utilisé dans les commandes suivantes à des fins de résolution (c’est-à-dire `--modem 0`).

**6. Obtenez les informations détaillées sur le modem**

Par défaut, le modem est désactivé (`Status -> state: disabled`), voir état :
```
mmcli --modem 0
```
Vous devez obtenir un résultat similaire à ce qui suit :
```
General  |                    path: /org/freedesktop/ModemManager1/Modem/0
           |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
  -----------------------------------
  Hardware |            manufacturer: Quectel
           |                   model: RM500Q-GL
           |       firmware revision: RM500QGLABR11A04M4G
           |          carrier config: CDMAless-Verizon
           | carrier config revision: 0A010126
           |            h/w revision: RM500Q-GL
           |               supported: gsm-umts, lte, 5gnr
           |                 current: gsm-umts, lte, 5gnr
           |            equipment id: xxxx
  -----------------------------------
  System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
           |                 drivers: option, cdc_mbim
           |                  plugin: quectel
           |            primary port: cdc-wdm0
           |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
           |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
  -----------------------------------
  Numbers  |                     own: +1xxxx
  -----------------------------------
  Status   |          unlock retries: sim-pin2 (3)
           |                   state: disabled
           |             power state: on
           |          signal quality: 0% (cached)
  -----------------------------------
  Modes    |               supported: allowed: 3g; preferred: none
           |                          allowed: 4g; preferred: none
           |                          allowed: 3g, 4g; preferred: 4g
           |                          allowed: 3g, 4g; preferred: 3g
           |                          allowed: 5g; preferred: none
           |                          allowed: 3g, 5g; preferred: 5g
           |                          allowed: 3g, 5g; preferred: 3g
           |                          allowed: 4g, 5g; preferred: 5g
           |                          allowed: 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 5g
           |                          allowed: 3g, 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 3g
           |                 current: allowed: 3g, 4g, 5g; preferred: 5g
  -----------------------------------
  Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
           |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
  -----------------------------------
  IP       |               supported: ipv4, ipv6, ipv4v6
  -----------------------------------
  3GPP     |                    imei: xxxxxxxxxxxxxxx
           |           enabled locks: fixed-dialing
  -----------------------------------
  3GPP EPS |    ue mode of operation: csps-1
           |      initial bearer apn: ims
           |  initial bearer ip type: ipv4v6
  -----------------------------------
  SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
```

**7. Activez le modem**

Avant d’établir une connexion, nous devons allumer la ou les radios du modem.
```
mmcli --modem 0 --enable
```
Voici ce que vous devez obtenir :
```
successfully enabled the modem
```
Après un certain temps, le modem doit s’inscrire sur une tour de cellule et vous devez voir dans l’état du modem : `Status -> state: registered`. Vous pouvez le vérifier à nouveau avec :
```
mmcli --modem 0
```

**8. Connectez-vous à l’aide des informations APN**

En règle générale, les modems indiquent le nom du point d’accès à utiliser (voir les informations `3GPP EPS -> initial bearer APN`) afin que vous puissiez l’utiliser pour établir une connexion. Dans le cas contraire, contactez votre fournisseur de téléphone portable pour que l’APN l’utilise. Voici la commande ModemManager pour la connexion utilisant, par exemple, l’APN Verizon `APN=vzwinternet`
```
mmcli --modem 0 --simple-connect="apn=vzwinternet"
```
Et là encore, vous devez obtenir ceci :
```
successfully connected the modem
```

**9. Récupérez l’état du modem**

À présent, vous devez voir `Status -> state: connected` et une nouvelle catégorie `Bearer` à la fin du message d’état.
```
mmcli -m 0
```
```
-----------------------------------
  General  |                    path: /org/freedesktop/ModemManager1/Modem/0
           |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
  -----------------------------------
  Hardware |            manufacturer: Quectel
           |                   model: RM500Q-GL
           |       firmware revision: RM500QGLABR11A04M4G
           |          carrier config: CDMAless-Verizon
           | carrier config revision: 0A010126
           |            h/w revision: RM500Q-GL
           |               supported: gsm-umts, lte, 5gnr
           |                 current: gsm-umts, lte, 5gnr
           |            equipment id: xxx
  -----------------------------------
  System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
           |                 drivers: option, cdc_mbim
           |                  plugin: quectel
           |            primary port: cdc-wdm0
           |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
           |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
  -----------------------------------
  Numbers  |                     own: +1xxxx
  -----------------------------------
  Status   |          unlock retries: sim-pin2 (3)
           |                   state: connected
           |             power state: on
           |             access tech: lte
           |          signal quality: 12% (recent)
  -----------------------------------
  Modes    |               supported: allowed: 3g; preferred: none
           |                          allowed: 4g; preferred: none
           |                          allowed: 3g, 4g; preferred: 4g
           |                          allowed: 3g, 4g; preferred: 3g
           |                          allowed: 5g; preferred: none
           |                          allowed: 3g, 5g; preferred: 5g
           |                          allowed: 3g, 5g; preferred: 3g
           |                          allowed: 4g, 5g; preferred: 5g
           |                          allowed: 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 5g
           |                          allowed: 3g, 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 3g
           |                 current: allowed: 3g, 4g, 5g; preferred: 5g
  -----------------------------------
  Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
           |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
  -----------------------------------
  IP       |               supported: ipv4, ipv6, ipv4v6
  -----------------------------------
  3GPP     |                    imei: xxxxxxxxxxxxxxx
           |           enabled locks: fixed-dialing
           |             operator id: 311480
           |           operator name: Verizon
           |            registration: home
           |                     pco: 0: (partial) '27058000FF0100'

  -----------------------------------
  3GPP EPS |    ue mode of operation: csps-1
           |     initial bearer path: /org/freedesktop/ModemManager1/Bearer/0
           |      initial bearer apn: ims
           |  initial bearer ip type: ipv4v6
  -----------------------------------
  SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
  -----------------------------------
  Bearer   |                   paths: /org/freedesktop/ModemManager1/Bearer/1

```

**10. Procurez-vous les détails du porteur**

Le porteur résultant du `--simple-connect` ci-dessus se situe à l’emplacement suivant : `/org/freedesktop/ModemManager1/Bearer/1`.
Il s’agit de celui que nous interrogeons sur les informations de modem relatives à la connexion active. Le porteur initial n’est pas attaché à une connexion active et ne contiendra donc pas d’informations IP.
```
mmcli --bearer 1
```
```
  --------------------------------
  General            |       path: /org/freedesktop/ModemManager1/Bearer/1
                     |       type: default
  --------------------------------
  Status             |  connected: yes
                     |  suspended: no
                     |  interface: wwan0
                     | ip timeout: 20
  --------------------------------
  Properties         |        apn: fast.t-mobile.com
                     |    roaming: allowed
  --------------------------------
  IPv4 configuration |     method: static
                     |    address: 25.21.113.165
                     |     prefix: 30
                     |    gateway: 25.21.113.166
                     |        dns: 10.177.0.34, 10.177.0.210
                     |        mtu: 1500
  --------------------------------
  Statistics         |   attempts: 1
```
Informations détaillées :
- `Status -> interface: wwan0` - répertorie l’interface réseau Linux qui correspond à ce modem.
- `IPv4 configuration` - fournit la configuration IP de l’interface ci-dessus pour qu’elle soit utilisable.

**11. Vérifiez l’état de l’interface réseau du modem**

L’interface réseau doit être `DOWN` par défaut.
```
ip link show dev wwan0
```
Retourne comme résultat :
```
4: wwan0: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
```

**12. Affichez l’interface**

```
sudo ip link set dev wwan0 up
```

**13. Vérifiez les informations IP**

Vous devez voir l’interface en tant que `UP,LOWER_UP` sans informations IP par défaut.
```
sudo ip address show dev wwan0
```
```
4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
       valid_lft forever preferred_lft forever
```

**14. Émettez une requête DHCP**

Il s’agit d’une fonctionnalité spécifique du module Quectel, mais qui ne se limite pas à celui-ci. En règle générale, les informations IP doivent être définies manuellement sur l’interface ou via un démon Network Manager prenant en charge ModemManager (par exemple, NetworkManager.), mais ici, nous pouvons simplement utiliser dhclient sur le modem Quectel :
```
sudo dhclient wwan0
```

**15. Vérifiez les informations IP**

La configuration IP de cette interface doit correspondre aux détails du porteur ModemManager.
```
sudo ip address show dev wwan0
```
Et les résultats :
```
4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
    inet 25.21.113.165/30 brd 25.21.113.167 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
       valid_lft forever preferred_lft forever
```

**16. Vérifiez les itinéraires de l’interface**

Notez que le client DHCP définit également un itinéraire par défaut pour que les paquets passent par l'interface `wwan0`.
```
ip route show dev wwan0
```
comme ceci :
```
default via 25.21.113.166 
25.21.113.164/30 proto kernel scope link src 25.21.113.165
```
Vous avez établi une connexion à Azure à l’aide du modem Quectel !


**17. Testez la connectivité**

Nous exécutons une requête `ping` par le biais de l'interface `wwan0`.
```
ping -I wwan0 8.8.8.8
```
```
PING 8.8.8.8 (8.8.8.8) from 25.21.113.165 wwan0: 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=137 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=114 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 2ms
rtt min/avg/max/mdev = 113.899/125.530/137.162/11.636 ms
```

## <a name="debugging"></a>Débogage
Consultez [Établir une connexion avec un modem USB](./connect-over-cellular-usb.md).

## <a name="next-steps"></a>Étapes suivantes
[Établir une connexion avec un modem USB](./connect-over-cellular-usb.md).

Retour à l’article principal sur 5G ou LTE :

[Établir une connexion 5G ou LTE](./connect-over-cellular.md).  