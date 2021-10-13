---
title: Description de DHCP et DNS dans Azure VMware Solution
description: Solution Azure VMware - Description de DHCP et DNS.
ms.topic: include
ms.date: 05/28/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 0e92259c2ece3b92b841d2be192684679d37a635
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638321"
---
<!-- Used in tutorial-network-checklist.md and configure-dhcp-azure-vmware-solution.md -->

Les applications et les charges de travail exécutées dans un environnement de cloud privé nécessitent une résolution de nom et des services DHCP pour la recherche et les affectations d’adresses IP. Une infrastructure DHCP et DNS appropriée est requise pour fournir ces services. Vous pouvez configurer une machine virtuelle pour fournir ces services dans votre environnement de cloud privé.  

Utilisez le service DHCP intégré à NSX ou un serveur DHCP local dans le cloud privé au lieu de router le trafic DHCP de diffusion sur le réseau étendu (WAN) vers l’emplacement local.
