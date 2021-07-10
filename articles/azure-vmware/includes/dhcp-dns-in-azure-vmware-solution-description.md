---
title: Description de DHCP et DNS dans Azure VMware Solution
description: Solution Azure VMware - Description de DHCP et DNS.
ms.topic: include
ms.date: 05/28/2021
ms.openlocfilehash: 585f195c36dd02e28cb744142080bee7dab91f6a
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110788078"
---
<!-- Used in tutorial-network-checklist.md and configure-dhcp-azure-vmware-solution.md -->

Les applications et les charges de travail exécutées dans un environnement de cloud privé nécessitent une résolution de nom et des services DHCP pour la recherche et les affectations d’adresses IP. Une infrastructure DHCP et DNS appropriée est requise pour fournir ces services. Vous pouvez configurer une machine virtuelle pour fournir ces services dans votre environnement de cloud privé.  

Utilisez le service DHCP intégré à NSX ou un serveur DHCP local dans le cloud privé au lieu de router le trafic DHCP de diffusion sur le réseau étendu (WAN) vers l’emplacement local.
