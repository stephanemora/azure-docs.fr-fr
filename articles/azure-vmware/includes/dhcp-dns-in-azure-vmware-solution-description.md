---
title: Description de DHCP et DNS dans Azure VMware Solution
description: Solution Azure VMware - Description de DHCP et DNS.
ms.topic: include
ms.date: 05/28/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 98f2fd71cc4657a3837278c3b7a761678b8a1005
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750107"
---
<!-- Used in tutorial-network-checklist.md and configure-dhcp-azure-vmware-solution.md -->

Les applications et les charges de travail exécutées dans un environnement de cloud privé nécessitent une résolution de nom et des services DHCP pour la recherche et les affectations d’adresses IP. Une infrastructure DHCP et DNS appropriée est requise pour fournir ces services. Vous pouvez configurer une machine virtuelle pour fournir ces services dans votre environnement de cloud privé.  

Utilisez le service DHCP intégré à NSX ou un serveur DHCP local dans le cloud privé au lieu de router le trafic DHCP de diffusion sur le réseau étendu (WAN) vers l’emplacement local.
