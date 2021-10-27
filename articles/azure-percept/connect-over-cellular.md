---
title: Établir une connexion 5G ou LTE avec Azure Percept DK
description: Cet article explique comment connecter Azure Percept DK via des réseaux 5G ou LTE.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 0df673937ebd195c250a0f0a8880ecf89d42c844
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997228"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks"></a>Établir une connexion 5G ou LTE avec Azure Percept DK

La connexion d’appareils IA Edge via des réseaux LTE et 5G présente de nombreux avantages. L’IA Edge est la plus efficace là où la connexion Wi-Fi ou LAN est limitée, comme les villes intelligentes, les véhicules autonomes et l’agriculture. En outre, les réseaux 5G/LTE offrent une meilleure sécurité que le Wi-Fi. Enfin, l’utilisation d’appareils IoT qui exécutent l’IA à la périphérie permet d’optimiser la bande passante sur les réseaux 5G/LTE, car seules les informations nécessaires sont envoyées au cloud, alors que la plupart des données sont traitées sur l’appareil. Aujourd’hui, Azure Percept DK prend même en charge la connexion directe aux réseaux 5G/LTE à l’aide d’un simple modem USB. Plus d’informations sont disponibles ci-dessous sur les différentes options.

## <a name="options-for-connecting-azure-percept-dk-over-5g-or-lte-networks"></a>Options pour connecter Azure Percept DK via des réseaux 5G ou LTE
Avec du matériel supplémentaire, vous pouvez connecter Azure Percept DK à l’aide d’une connexion LTE/5G. Trois options sont actuellement prises en charge. Vous trouverez un lien permettant d’accéder à plus de détails pour chaque option :
- **Modem USB 5G/LTE** : Nous avons maintenant publié une nouvelle image de logiciel qui prend en charge le logiciel open source ModemManager, lequel ajoute la prise en charge du modem USB à notre système d’exploitation Linux. Cela vous permet de connecter votre Azure Percept sur des réseaux LTE ou 5G en utilisant divers modems USB souvent peu coûteux. Plus d’informations ici : [Connexion à l’aide d’un modem USB](./connect-over-cellular-usb.md).   
- **Passerelle Ethernet 5G/LTE** : Ici, Azure Percept est connecté à la passerelle 5G/LTE par Ethernet. Plus d’informations ici : [Établir une connexion avec une passerelle cellulaire](./connect-over-cellular-gateway.md).
- **Appareil de point d’accès 5G/LTE Wi-Fi** : Azure Percept est connecté au réseau Wi-Fi fourni par le point d’accès Wi-Fi. Dans ce cas, le devkit se connecte au réseau comme n’importe quel autre réseau Wi-Fi. Pour plus d’instructions, suivez le [Guide d’installation d’Azure Percept DK](./quickstart-percept-dk-set-up.md) et sélectionnez le réseau Wi-Fi 5G/LTE fourni par le point d’accès.


## <a name="considerations-when-selecting-a-5g-or-lte-device-in-general"></a>Éléments à prendre en compte lors de la sélection d’un appareil 5G ou LTE en général
Les appareils 5G/LTE, qu’il s’agisse de modems USB ou de passerelles Ethernet, prennent en charge différentes technologies qui ont un impact sur le débit maximal de données pour les téléchargements et les chargements. Les débits de données indiqués aident à la prise de décision, mais sont rarement atteints dans le monde réel. Voici quelques conseils pour sélectionner l’appareil adapté à vos besoins.
 
- **LTE CAT-1** fournit jusqu’à 10 Mbits/s pour le téléchargement et 5 Mbits/s pour le chargement. Cela est suffisant pour les fonctionnalités par défaut d’Azure Percept DK, telles que la détection d’objet et la création d’un Assistant vocal. Toutefois, cela risque de ne pas suffire pour les solutions qui nécessitent d’envoyer des données en streaming vidéo vers le cloud.
- **LTE CAT-3 and 4** fournit jusqu’à 100 Mbits/s pour le téléchargement et 50 Mbit/s pour le chargement, ce qui est suffisant pour le streaming vidéo vers le cloud. Toutefois, cela ne suffit pas pour diffuser en streaming des vidéos de qualité Full HD.
- **LTE CAT-5 et versions ultérieures** fournit des débits de données suffisamment élevés pour la diffusion en streaming de vidéos HD sur un seul appareil. Si vous devez connecter plusieurs appareils à une même passerelle, nous vous conseillons d’utiliser la 5G.
- À l’avenir, les passerelles **5G** seront les mieux adaptées. En effet, elles fournissent des débits de données et une bande passante qui sont suffisants pour un streaming sur plusieurs appareils à la fois. En outre, elles fournissent également une latence plus faible pour le transfert de données.


## <a name="next-steps"></a>Étapes suivantes
Selon l’appareil cellulaire auquel vous avez accès, suivez ces liens pour connecter votre kit de développement Azure Percept :

[Établir une connexion avec une passerelle cellulaire](./connect-over-cellular-gateway.md).

[Établir une connexion avec un modem USB](./connect-over-cellular-usb.md).
