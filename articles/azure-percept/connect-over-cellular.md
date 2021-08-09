---
title: Connexion d’Azure Percept via des réseaux de téléphonie mobile
description: Cet article explique comment connecter Azure Percept DK via des réseaux de téléphonie mobile.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 05/20/2021
ms.custom: template-concept
ms.openlocfilehash: 93a2ed0c7f800461f7de6c895b9be1508603fd82
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111441913"
---
# <a name="connect-the-azure-percept-dk-over-cellular-networks"></a>Connecter Azure Percept DK via des réseaux de téléphonie mobile

La connexion d’appareils IA Edge via des réseaux mobiles (LTE et 5G) présente de nombreux avantages. L’IA Edge est la plus efficace là où la connexion Wi-Fi ou LAN est limitée, comme les villes intelligentes, les véhicules autonomes et l’agriculture. En outre, les réseaux de téléphonie mobile offrent une meilleure sécurité que le Wi-Fi. Enfin, l’utilisation d’appareils IoT qui exécutent l’IA à la périphérie permet d’optimiser la bande passante sur les réseaux de téléphonie mobile, car seules les informations nécessaires sont envoyées au cloud, alors que la plupart des données sont traitées sur l’appareil. Aujourd’hui, Azure Percept DK ne peut pas se connecter directement aux réseaux de téléphonie mobile. Toutefois, ceux-ci peuvent se connecter à des passerelles mobiles à l’aide des fonctionnalités Ethernet et Wi-Fi intégrées. Cet article explique comment tout cela fonctionne.

## <a name="options-for-connecting-the-azure-percept-dk-over-cellular-networks"></a>Options pour connecter Azure Percept DK via des réseaux de téléphonie mobile
Avec du matériel supplémentaire, vous pouvez connecter Azure Percept DK à l’aide d’une connexion mobile comme le LTE ou la 5G. Il existe actuellement deux options principales :
- **Appareil de point d’accès mobile Wi-Fi** : où le devkit est connecté au réseau Wi-Fi fourni par le point d’accès Wi-Fi. Dans ce cas, le devkit se connecte au réseau comme n’importe quel autre réseau Wi-Fi. Pour plus d’instructions, suivez le [Guide d’installation d’Azure Percept DK](./quickstart-percept-dk-set-up.md) et sélectionnez le réseau Wi-Fi mobile fourni par le point d’accès.
- **Appareil de passerelle Ethernet mobile** : ici, le devkit est connecté à la passerelle mobile via Ethernet, tirant ainsi parti d’une plus grande sécurité qu’avec les connexions Wi-Fi. Le reste de cet article explique en détail comment un réseau tel que celui-ci est configuré.

## <a name="cellular-gateway-topology"></a>Topologie d’une passerelle mobile
:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="Ce diagramme montre comment Azure Percept DK se connecte à une passerelle mobile via Ethernet.":::

Dans le diagramme ci-dessus, vous pouvez voir comment une passerelle mobile peut facilement être appairée avec Azure Percept DK.

## <a name="considerations-when-connecting-to-a-cellular-gateway"></a>Éléments à prendre en compte lors d’une connexion à une passerelle mobile
Voici quelques points importants à prendre en compte lorsque vous connectez Azure Percept DK à une passerelle mobile.
- Configurez d’abord la passerelle, puis vérifiez que celle-ci reçoit bien une connexion via la carte SIM. Il sera alors plus facile de résoudre les éventuels problèmes détectés lors de la connexion d’Azure Percept DK.
- Vérifiez que les deux extrémités du câble Ethernet sont fermement connectées à la passerelle et à Azure Percept DK.
- Suivez les [instructions par défaut](./how-to-connect-over-ethernet.md) pour la connexion d’Azure Percept DK via Ethernet.
- Si votre plan de téléphonie mobile a un quota, il est recommandé d’optimiser la quantité de données que vos modèles Azure Percept DK envoient au cloud.
- Veillez à disposer d’un [pare-feu correctement configuré](./concept-security-configuration.md) qui bloque le trafic entrant externe.

## <a name="ssh-over-a-cellular-network"></a>Connexion SSH via un réseau mobile
Pour établir une connexion SSH au devkit via une passerelle Ethernet mobile, vous disposez des options suivantes :
- **Utilisation du point d’accès Wi-Fi du devkit**. Si vous avez désactivé le Wi-Fi, vous pouvez le réactiver en redémarrant votre devkit. À partir de là, vous pouvez vous connecter au point d’accès Wi-Fi du devkit et suivre [ces procédures SSH](./how-to-ssh-into-percept-dk.md).
- **Utilisation d’une connexion Ethernet vers un réseau local (LAN)** . Avec cette option, vous allez débrancher votre devkit de la passerelle mobile et la brancher au routeur LAN. Pour plus d’informations, consultez [Comment se connecter via Ethernet](./how-to-connect-over-ethernet.md). 
- **Utilisation des fonctionnalités d’accès à distance de la passerelle**. De nombreuses passerelles mobiles incluent des gestionnaires d’accès à distance qui peuvent être utilisés pour se connecter à des appareils du réseau via une connexion SSH. Contactez le fabricant de votre passerelle mobile pour savoir si celle-ci dispose de cette fonctionnalité. Voici un exemple de gestionnaire d’accès à distance pour les [passerelles mobiles CradlePoint](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager).
- **Utilisation du port série du devkit**. Azure Percept DK comprend un port de connexion série qui peut être utilisé pour se connecter directement à l’appareil. Pour obtenir des instructions détaillées, consultez [Établir une connexion série à Azure Percept DK](./how-to-connect-to-percept-dk-over-serial.md).

## <a name="considerations-when-selecting-a-cellular-gateway-device"></a>Éléments à prendre en compte lors de la sélection d’un appareil de passerelle mobile
Les passerelles mobiles prennent en charge différentes technologies qui ont un impact sur le débit de données pris en charge pour les téléchargements et les chargements. Les débits de données indiqués aident à la prise de décision, cependant, il est rare d’atteindre leur plafond. Voici quelques conseils pour sélectionner la passerelle adaptée à vos besoins.
 
- **LTE CAT-1** fournit jusqu’à 10 Mbits/s pour le téléchargement et 5 Mbits/s pour le chargement. Cela est suffisant pour les fonctionnalités par défaut d’Azure Percept DK, telles que la détection d’objet et la création d’un Assistant vocal. Toutefois, cela risque de ne pas suffire pour les solutions qui nécessitent d’envoyer des données en streaming vidéo vers le cloud.
- **LTE CAT-3 and 4** fournit jusqu’à 100 Mbits/s pour le téléchargement et 50 Mbit/s pour le chargement, ce qui est suffisant pour le streaming vidéo vers le cloud. Toutefois, cela ne suffit pas pour diffuser en streaming des vidéos de qualité Full HD.
- **LTE CAT-5 et versions ultérieures** fournit des débits de données suffisamment élevés pour la diffusion en streaming de vidéos HD sur un seul appareil. Si vous devez connecter plusieurs appareils à une même passerelle, nous vous conseillons d’utiliser la 5G.
- À l’avenir, les passerelles **5G** seront les mieux adaptées. En effet, elles fournissent des débits de données et une bande passante qui sont suffisants pour un streaming sur plusieurs appareils à la fois. En outre, elles fournissent également une latence plus faible pour le transfert de données.


## <a name="next-steps"></a>Étapes suivantes
Si vous disposez d’une passerelle mobile et que vous souhaitez y connecter Azure Percept DK, suivez les étapes ci-dessous.
- [Comment connecter Azure Percept DK via Ethernet](./how-to-connect-over-ethernet.md)
