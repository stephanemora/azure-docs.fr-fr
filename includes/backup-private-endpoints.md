---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: b8941bb4fa77cb0008cb7271681e972bc21d6588
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659520"
---
Vous pouvez désormais utiliser des [points de terminaison privés](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) pour sauvegarder en toute sécurité les données des serveurs de votre réseau virtuel vers votre coffre Recovery Services. Le point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre coffre. Le trafic réseau entre vos ressources dans le réseau virtuel et le coffre transite via votre réseau virtuel et une liaison privée sur le réseau principal de Microsoft. Cela élimine l’exposition de l’Internet public. Les points de terminaison privés permettent de sauvegarder et de restaurer vos bases de données SQL et SAP HANA actives à l’intérieur de vos machines virtuelles Azure. Ils sont également utilisables pour vos serveurs locaux à l’aide de l’agent MARS.

La sauvegarde de machine virtuelle Azure ne nécessite pas de connexion Internet, et donc pas de points de terminaison privés, pour permettre l’isolement réseau.

Pour en savoir plus sur les points de terminaison privés pour Sauvegarde Azure, cliquez [ici](https://docs.microsoft.com/azure/backup/private-endpoints).
