---
title: Fichier Include
description: Fichier Include
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: include
ms.date: 01/12/2021
ms.author: wellee
ms.custom: include file
ms.openlocfilehash: 67a996dbe1eb7090ea5f9ee9f0880698f4ba74a3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919063"
---
Cette section comprend des instructions, ainsi que les prérequis relatifs à l’allocation d’espaces d’adressage client dans lesquels l’unité d’échelle de la passerelle VPN point à site du hub Virtual WAN est supérieure ou égale à 40.

### <a name="background"></a>Arrière-plan

Les passerelles VPN point à site du hub Virtual WAN sont déployées avec plusieurs instances. Chaque instance d’une passerelle VPN point à site peut prendre en charge jusqu’à 10 000 connexions utilisateur point à site simultanées. Par conséquent, pour les unités d’échelle supérieures à 40, Virtual WAN doit déployer une capacité supplémentaire, ce qui nécessite qu’un nombre minimal de pools d’adresses soit alloué pour les différentes unités d’échelle.

Par exemple, si une unité d’échelle de 100 est choisie, 5 instances sont déployées pour la passerelle VPN point à site dans le hub virtuel. Ce déploiement peut prendre en charge jusqu’à 50 000 connexions simultanées, et **au moins** 5 pools d’adresses distincts.

**Unités d’échelle disponibles**

| Unité de mise à l'échelle | Nombre maximal de clients pris en charge | Nombre minimal de pools d’adresses |
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

### <a name="specifying-address-pools"></a>Spécification de pools d’adresses

Voici quelques instructions pour choisir les pools d’adresses. Notez que les attributions de pool d’adresses VPN point à site sont effectuées automatiquement par Virtual WAN.

1. Une instance de passerelle autorise un maximum de 10 000 connexions simultanées. Par conséquent, chaque pool d’adresses doit contenir au moins 10 000 adresses IP RFC1918 uniques.
1. Plusieurs plages de pools d’adresses sont automatiquement combinées et attribuées à une instance de passerelle **unique**. Ce processus est effectué en mode tourniquet (round robin) pour toutes les instances de passerelle qui ont moins de 10 000 adresses IP. Par exemple, un pool comportant 5 000 adresses peut être combiné automatiquement par Virtual WAN à un autre pool de 8 000 adresses, et être affecté à une instance de passerelle unique.
1. Un même pool d’adresses n’est attribué qu’à une seule instance de passerelle par Virtual WAN.
1. Les pools d’adresses doivent être distincts. Il ne peut y avoir aucun chevauchement entre les pools d’adresses.

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

**Recommandation n°1 : Vérifiez que le pool d’adresses n°2 comprend au moins 10 000 adresses IP distinctes (par exemple : 10.13.0.0/15)**

**Recommandation n°2 : Ajoutez un autre pool d’adresses (par exemple : Pool d’adresses n°4 10.15.0.0/21 avec 2 048 adresses). Les pools d’adresses n°2 et n°4 seront automatiquement combinés et permettront à cette instance de passerelle de prendre en charge 10 000 connexions simultanées.**
