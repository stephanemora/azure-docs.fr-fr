---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137022"
---
Vous pouvez désormais utiliser des [points de terminaison privés](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) pour sauvegarder en toute sécurité les données des serveurs de votre réseau virtuel vers votre coffre Recovery Services. Le point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre coffre. Le trafic réseau entre vos ressources dans le réseau virtuel et le coffre transite via votre réseau virtuel et une liaison privée sur le réseau principal de Microsoft. Cela élimine l’exposition de l’Internet public. Les points de terminaison privés permettent de sauvegarder et de restaurer vos bases de données SQL et SAP HANA actives à l’intérieur de vos machines virtuelles Azure. Ils sont également utilisables pour vos serveurs locaux à l’aide de l’agent MARS.

La sauvegarde de machine virtuelle Azure ne nécessite pas de connexion Internet, et donc pas de points de terminaison privés, pour permettre l’isolement réseau.

>[!NOTE]
> Cette fonctionnalité est actuellement en utilisation précoce. Répondez à [cette enquête](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) et envoyez-nous un e-mail à l’adresse azbackupnetsec@microsoft.com si vous souhaitez utiliser des points de terminaison privés pour Sauvegarde Azure. La possibilité d’utiliser cette fonctionnalité est soumise à l’approbation du service Sauvegarde Azure.
