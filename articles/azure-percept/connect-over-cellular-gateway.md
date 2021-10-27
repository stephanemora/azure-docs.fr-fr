---
title: Établir une connexion 5G ou LTE avec Azure Percept avec une passerelle
description: Cet article explique comment connecter Azure Percept DK via des réseaux 5G ou LTE avec une passerelle mobile.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 00596a23e086f801b152e1a3129ecaf7ef44a0df
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007398"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks-using-a-gateway"></a>Établir une connexion 5G ou LTE avec Azure Percept avec une passerelle
Une passerelle qui se connecte à Internet via un réseau 5G ou LTE et fournit des ports Ethernet est un moyen simple de connecter Azure Percept à Internet. Dans ce cas, Azure Percept ne tient pas compte du fait qu’il est connecté en 5G ou LTE : il sait que son port Ethernet dispose d’une connectivité et achemine tout le trafic via ce port.  


## <a name="5glte-gateway-topology-overview"></a>Présentation de la topologie de la passerelle 5G/LTE
Vous pouvez voir ci-dessous comment une passerelle 5G/LTE peut facilement être appairée avec Azure Percept DK.

:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="Ce diagramme montre comment Azure Percept DK se connecte à une passerelle 5G/LTE via Ethernet." lightbox="media/connect-over-cellular/topology-expanded.png":::

## <a name="considerations-when-connecting-to-a-5g-or-lte-gateway"></a>Éléments à prendre en compte lors d’une connexion à une passerelle 5G ou LTE
Voici quelques points importants à prendre en compte lorsque vous connectez Azure Percept DK à une passerelle 5G/LTE.
- Configurez d’abord la passerelle, puis vérifiez que celle-ci reçoit bien une connexion via la carte SIM. Il sera alors plus facile de résoudre les éventuels problèmes détectés lors de la connexion d’Azure Percept DK.
- Vérifiez que les deux extrémités du câble Ethernet sont fermement connectées à la passerelle et à Azure Percept DK.
- Suivez les [instructions par défaut](./how-to-connect-over-ethernet.md) pour la connexion d’Azure Percept DK via Ethernet.
- Si votre plan de téléphonie 5G/LTE a un quota, il est recommandé d’optimiser la quantité de données que vos modèles Azure Percept DK envoient au cloud.
- Veillez à disposer d’un [pare-feu correctement configuré](./concept-security-configuration.md) qui bloque le trafic entrant externe.

## <a name="considerations-when-doing-ssh-to-the-devkit"></a>Éléments à prendre en compte lors de l’utilisation du protocole SSH avec le kit de développement
Pour établir une connexion SSH au devkit via une passerelle Ethernet 5G/LTE, vous disposez des options suivantes :
- **Utilisation du point d’accès Wi-Fi du devkit**. Si vous avez désactivé le Wi-Fi, vous pouvez le réactiver en redémarrant votre devkit. À partir de là, vous pouvez vous connecter au point d’accès Wi-Fi du kit de développement et suivre les instructions sur [l’utilisation du protocole SSH avec Azure Percept DK](./how-to-ssh-into-percept-dk.md).
- **Utilisation d’une connexion Ethernet vers un réseau local (LAN)** . Avec cette option, vous allez débrancher votre devkit de la passerelle 5G/LTE et la brancher au routeur LAN. Pour plus d’informations, consultez [Comment se connecter via Ethernet](./how-to-connect-over-ethernet.md). 
- **Utilisation des fonctionnalités d’accès à distance de la passerelle**. De nombreuses passerelles 5G/LTE incluent des gestionnaires d’accès à distance qui peuvent être utilisés pour se connecter à des appareils du réseau via une connexion SSH. Contactez le fabricant de votre passerelle 5G/LTE pour savoir si celle-ci dispose de cette fonctionnalité. Voici un exemple de gestionnaire d’accès à distance pour les [passerelles 5G/LTE CradlePoint](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager).
- **Utilisation du port série du devkit**. Azure Percept DK comprend un port de connexion série qui peut être utilisé pour se connecter directement à l’appareil. Pour obtenir des instructions détaillées, consultez [Établir une connexion série à Azure Percept DK](./how-to-connect-to-percept-dk-over-serial.md).

## <a name="next-steps"></a>Étapes suivantes
Selon l’appareil mobile auquel vous avez accès, vous souhaiterez peut-être vous connecter via un mode USB :

[Établir une connexion avec un modem USB](./connect-over-cellular-usb.md).

Retour à l’article principal sur 5G ou LTE :

[Établir une connexion 5G ou LTE](./connect-over-cellular.md).