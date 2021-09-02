---
title: Établir une connexion 5G ou LTE avec Azure Percept DK
description: Cet article explique comment connecter Azure Percept DK via des réseaux 5G ou LTE.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 202bec8182cc2ae04e9fadffe01977150ea93dda
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222318"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks"></a>Établir une connexion 5G ou LTE avec Azure Percept DK

La connexion d’appareils IA Edge via des réseaux LTE et 5G présente de nombreux avantages. L’IA Edge est la plus efficace là où la connexion Wi-Fi ou LAN est limitée, comme les villes intelligentes, les véhicules autonomes et l’agriculture. En outre, les réseaux 5G/LTE offrent une meilleure sécurité que le Wi-Fi. Enfin, l’utilisation d’appareils IoT qui exécutent l’IA à la périphérie permet d’optimiser la bande passante sur les réseaux 5G/LTE, car seules les informations nécessaires sont envoyées au cloud, alors que la plupart des données sont traitées sur l’appareil. Aujourd’hui, Azure Percept DK ne peut pas se connecter directement aux réseaux 5G/LTE. Toutefois, ceux-ci peuvent se connecter à des passerelles 5G/LTE à l’aide des fonctionnalités Ethernet et Wi-Fi intégrées. Cet article explique comment tout cela fonctionne.

## <a name="options-for-connecting-the-azure-percept-dk-over-5g-or-lte-networks"></a>Options pour connecter Azure Percept DK via des réseaux 5G ou LTE
Avec du matériel supplémentaire, vous pouvez connecter Azure Percept DK à l’aide d’une connexion LTE/5G. Il existe actuellement deux options principales :
- **Appareil de point d’accès 5G/LTE Wi-Fi** : où le devkit est connecté au réseau Wi-Fi fourni par le point d’accès Wi-Fi. Dans ce cas, le devkit se connecte au réseau comme n’importe quel autre réseau Wi-Fi. Pour plus d’instructions, suivez le [Guide d’installation d’Azure Percept DK](./quickstart-percept-dk-set-up.md) et sélectionnez le réseau Wi-Fi 5G/LTE fourni par le point d’accès.
- **Appareil de passerelle Ethernet 5G/LTE** : ici, le devkit est connecté à la passerelle 5G/LTE via Ethernet, tirant ainsi parti d’une plus grande sécurité qu’avec les connexions Wi-Fi. Le reste de cet article explique en détail comment un réseau tel que celui-ci est configuré.

## <a name="5glte-gateway-topology"></a>Topologie de la passerelle 5G/LTE
:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="Ce diagramme montre comment Azure Percept DK se connecte à une passerelle 5G/LTE via Ethernet.":::

Dans le diagramme ci-dessus, vous pouvez voir comment une passerelle 5G/LTE peut facilement être appairée avec Azure Percept DK.

## <a name="considerations-when-connecting-to-a-5g-or-lte-gateway"></a>Éléments à prendre en compte lors d’une connexion à une passerelle 5G ou LTE
Voici quelques points importants à prendre en compte lorsque vous connectez Azure Percept DK à une passerelle 5G/LTE.
- Configurez d’abord la passerelle, puis vérifiez que celle-ci reçoit bien une connexion via la carte SIM. Il sera alors plus facile de résoudre les éventuels problèmes détectés lors de la connexion d’Azure Percept DK.
- Vérifiez que les deux extrémités du câble Ethernet sont fermement connectées à la passerelle et à Azure Percept DK.
- Suivez les [instructions par défaut](./how-to-connect-over-ethernet.md) pour la connexion d’Azure Percept DK via Ethernet.
- Si votre plan de téléphonie 5G/LTE a un quota, il est recommandé d’optimiser la quantité de données que vos modèles Azure Percept DK envoient au cloud.
- Veillez à disposer d’un [pare-feu correctement configuré](./concept-security-configuration.md) qui bloque le trafic entrant externe.

## <a name="ssh-over-a-5g-or-lte-network"></a>Établir une connexion SSH sur un réseau 5G ou LTE
Pour établir une connexion SSH au devkit via une passerelle Ethernet 5G/LTE, vous disposez des options suivantes :
- **Utilisation du point d’accès Wi-Fi du devkit**. Si vous avez désactivé le Wi-Fi, vous pouvez le réactiver en redémarrant votre devkit. À partir de là, vous pouvez vous connecter au point d’accès Wi-Fi du devkit et suivre [ces procédures SSH](./how-to-ssh-into-percept-dk.md).
- **Utilisation d’une connexion Ethernet vers un réseau local (LAN)** . Avec cette option, vous allez débrancher votre devkit de la passerelle 5G/LTE et la brancher au routeur LAN. Pour plus d’informations, consultez [Comment se connecter via Ethernet](./how-to-connect-over-ethernet.md). 
- **Utilisation des fonctionnalités d’accès à distance de la passerelle**. De nombreuses passerelles 5G/LTE incluent des gestionnaires d’accès à distance qui peuvent être utilisés pour se connecter à des appareils du réseau via une connexion SSH. Contactez le fabricant de votre passerelle 5G/LTE pour savoir si celle-ci dispose de cette fonctionnalité. Voici un exemple de gestionnaire d’accès à distance pour les [passerelles 5G/LTE CradlePoint](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager).
- **Utilisation du port série du devkit**. Azure Percept DK comprend un port de connexion série qui peut être utilisé pour se connecter directement à l’appareil. Pour obtenir des instructions détaillées, consultez [Établir une connexion série à Azure Percept DK](./how-to-connect-to-percept-dk-over-serial.md).

## <a name="considerations-when-selecting-a-5g-or-lte-gateway-device"></a>Éléments à prendre en compte lors de la sélection d’un appareil de passerelle 5G ou LTE
Les passerelles 5G/LTE prennent en charge différentes technologies qui ont un impact sur le débit de données pris en charge pour les téléchargements et les chargements. Les débits de données indiqués aident à la prise de décision, cependant, il est rare d’atteindre leur plafond. Voici quelques conseils pour sélectionner la passerelle adaptée à vos besoins.
 
- **LTE CAT-1** fournit jusqu’à 10 Mbits/s pour le téléchargement et 5 Mbits/s pour le chargement. Cela est suffisant pour les fonctionnalités par défaut d’Azure Percept DK, telles que la détection d’objet et la création d’un Assistant vocal. Toutefois, cela risque de ne pas suffire pour les solutions qui nécessitent d’envoyer des données en streaming vidéo vers le cloud.
- **LTE CAT-3 and 4** fournit jusqu’à 100 Mbits/s pour le téléchargement et 50 Mbit/s pour le chargement, ce qui est suffisant pour le streaming vidéo vers le cloud. Toutefois, cela ne suffit pas pour diffuser en streaming des vidéos de qualité Full HD.
- **LTE CAT-5 et versions ultérieures** fournit des débits de données suffisamment élevés pour la diffusion en streaming de vidéos HD sur un seul appareil. Si vous devez connecter plusieurs appareils à une même passerelle, nous vous conseillons d’utiliser la 5G.
- À l’avenir, les passerelles **5G** seront les mieux adaptées. En effet, elles fournissent des débits de données et une bande passante qui sont suffisants pour un streaming sur plusieurs appareils à la fois. En outre, elles fournissent également une latence plus faible pour le transfert de données.


## <a name="next-steps"></a>Étapes suivantes
Si vous disposez d’une passerelle 5G/LTE et que vous souhaitez y connecter Azure Percept DK, suivez les étapes ci-dessous.
- [Comment connecter Azure Percept DK via Ethernet](./how-to-connect-over-ethernet.md)
