---
title: À propos des pools d’adresses client pour le VPN utilisateur P2S
titleSuffix: Azure Virtual WAN
description: En savoir plus sur les pools d’adresses client pour les P2S de VPN utilisateur.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: cherylmc
ms.openlocfilehash: 58b642eeff7b20ccb04e2b5817f8f5a8ee5bcc2c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525064"
---
# <a name="about-client-address-pools-for-point-to-site-configurations"></a>À propos des pools d’adresses client pour les configurations de point à site

Cet article décrit les instructions et la configuration exigée de Virtual WAN pour l’allocation des espaces d’adressage client lorsque les **unités d’échelle de la passerelle** point à site du hub virtuel sont supérieures ou égales à 40.

Les passerelles VPN point à site du hub Virtual WAN sont déployées avec plusieurs instances. Chaque instance d’une passerelle VPN point à site peut prendre en charge jusqu’à 10 000 connexions utilisateur point à site simultanées. Par conséquent, pour les unités d’échelle supérieures à 40, Virtual WAN doit déployer une capacité supplémentaire, ce qui nécessite qu’un nombre minimal de pools d’adresses soit alloué pour les différentes unités d’échelle.

Par exemple, si une unité d’échelle de 100 est choisie, 5 instances sont déployées pour la passerelle VPN point à site dans un hub virtuel. Ce déploiement peut prendre en charge jusqu’à 50 000 connexions simultanées, et **au moins** 5 pools d’adresses distincts.

**Unités d’échelle disponibles**

| Unité d'échelle | Nombre maximal de clients pris en charge | Nombre minimal de pools d’adresses |
|--- |--- |--- |
| 40 | 20000 | 2 |
| 60 | 30000 | 3 |
| 80 | 40000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80000 | 8 |
| 180 | 90000 | 9 |
| 200 | 100000 | 10 |

## <a name="specifying-address-pools"></a><a name="specify-address-pools"></a>Spécification de pools d’adresses

Les affectations de pools d’adresses VPN point à site sont effectuées automatiquement par Virtual WAN. Consultez les recommandations de base suivantes pour spécifier les pools d’adresses.

* Une instance de passerelle autorise un maximum de 10 000 connexions simultanées. Par conséquent, chaque pool d’adresses doit contenir au moins 10 000 adresses IP RFC1918 uniques.
* Plusieurs plages de pools d’adresses sont automatiquement combinées et attribuées à une instance de passerelle **unique**. Ce processus est effectué en mode tourniquet (round robin) pour toutes les instances de passerelle qui ont moins de 10 000 adresses IP. Par exemple, un pool comportant 5 000 adresses peut être combiné automatiquement par Virtual WAN à un autre pool de 8 000 adresses, et être affecté à une seule instance de passerelle.
* Un même pool d’adresses n’est attribué qu’à une seule instance de passerelle par Virtual WAN.
* Les pools d’adresses doivent être distincts. Il ne peut y avoir aucun chevauchement entre les pools d’adresses.

> [!NOTE] 
> Si un pool d’adresses est associé à une instance de passerelle en cours de maintenance, le pool d’adresses ne peut pas être réaffecté à une autre instance.

### <a name="example"></a>Exemple 

L’exemple suivant décrit une situation dans laquelle 60 unités d’échelle prennent en charge jusqu’à 30 000 connexions, mais moins de 30 000 connexions simultanées ont lieu dans les pools d’adresses alloués.

Dans cette configuration, le nombre total de connexions simultanées prises en charge est de 28 192. La première instance de passerelle prend en charge 10 000 adresses, la deuxième instance 8 192 connexions, et la troisième instance 10 000 adresses.

| Numéro du pool d’adresses | Pool d’adresses | Connexions prises en charge |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10000 |
| 2 | 10.13.0.0/19 | 8 192 |
| 3 | 10.14.0.0/15 | 10000|

#### <a name="recommendations"></a>Recommandations

* Vérifiez que le pool d’adresses n°2 comprend au moins 10 000 adresses IP distinctes. (Par exemple : 10.13.0.0/15)
* Ajoutez un autre pool d’adresses. (Par exemple : Pool d’adresses n°4 10.15.0.0/21 avec 2 048 adresses). Les pools d’adresses n°2 et n°4 seront automatiquement combinés et permettront à cette instance de passerelle de prendre en charge 10 000 connexions simultanées.

## <a name="configure-or-modify-this-setting"></a>Configurer ou modifier ce paramètre

Ce paramètre est configuré sur la page **point à site** lorsque vous créez votre hub virtuel. Vous pouvez le modifier ultérieurement.

Pour modifier ce paramètre :

[!INCLUDE [Modify client address pool](../../includes/virtual-wan-client-address-pool-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les étapes de configuration, consultez [Configurer un VPN utilisateur P2S](virtual-wan-point-to-site-portal.md).
