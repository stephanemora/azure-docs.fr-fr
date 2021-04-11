---
author: v-amallick
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: v-amallick
ms.openlocfilehash: d20ed4d39921f8000f77f947c4372bd8b10ca642
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294145"
---
Vous pouvez désormais utiliser des [points de terminaison privés](../articles/private-link/private-endpoint-overview.md) pour sauvegarder en toute sécurité les données des serveurs de votre réseau virtuel vers votre coffre Recovery Services. Le point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre coffre. Le trafic réseau entre vos ressources dans le réseau virtuel et le coffre transite via votre réseau virtuel et une liaison privée sur le réseau principal de Microsoft. Cela élimine l’exposition de l’Internet public. Les points de terminaison privés permettent de sauvegarder et de restaurer vos bases de données SQL et SAP HANA actives à l’intérieur de vos machines virtuelles Azure. Ils sont également utilisables pour vos serveurs locaux à l’aide de l’agent MARS.

La sauvegarde de machine virtuelle Azure ne nécessite pas de connexion Internet, et donc pas de points de terminaison privés, pour permettre l’isolement réseau.

Pour en savoir plus sur les points de terminaison privés pour Sauvegarde Azure, cliquez [ici](../articles/backup/private-endpoints.md).