---
title: Segments réseau VMware HCX
description: Vous avez besoin de quatre réseaux pour VMware HCX.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: e9b37c125db82a95c137ede8d642888fba8b6c80
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622059"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Il existe différentes façons de configurer des segments réseau VMware HCX localement. L’exemple suivant présente une configuration simple qui prend en charge un cas d’usage de production pilote ou petit.  Lors de la conception de centaines ou de milliers de charges de travail, cette configuration peut être amenée à changer, en fonction des besoins de la migration.  

Pour préparer le déploiement de VMware HCX afin de prendre en charge le cas d’usage de production pilote ou petit, identifiez les éléments suivants :

- **Réseau de gestion :** quand vous déployez VMware HCX localement, vous devez définir un réseau de gestion.  En règle générale, il s’agit du même réseau de gestion que celui utilisé par votre cluster VMware local.  Au minimum, identifiez **deux** adresses IP sur ce segment réseau pour VMware HCX. Vous aurez peut-être besoin de quantités plus grandes, en fonction de l’échelle de votre déploiement au-delà du cas d’usage pilote ou petit.

> [!NOTE]
   > La méthode recommandée consiste à créer un réseau /26, car vous pouvez utiliser jusqu’à 10 maillages de service et 60 extendeurs réseau (-1 par maillage de service). Vous pouvez étirer **huit** réseaux par extendeur réseau en utilisant des clouds privés Azure VMware Solution.
   >
   
- **Réseau vMotion :** quand vous déployez VMware HCX localement, vous devez définir un réseau vMotion.  En règle générale, il s’agit du même réseau que celui utilisé pour vMotion par votre cluster VMware local.  Au minimum, identifiez **deux** adresses IP sur ce segment réseau pour VMware HCX. Vous aurez peut-être besoin de quantités plus grandes, en fonction de l’échelle de votre déploiement au-delà du cas d’usage pilote ou petit.

   Le réseau vMotion doit être exposé sur un commutateur virtuel distribué ou vSwitch0. Si ce n’est pas le cas, modifiez l’environnement en conséquence.

   > [!NOTE]
   > De nombreux environnements VMware utilisent des segments réseau non routés pour vMotion, ce qui ne pose aucun problème.

- **Réseau de liaison montante :** quand vous déployez VMware HCX localement, vous devez définir un réseau de liaison montante. Utilisez le réseau de gestion défini comme réseau de liaison montante.
   
- **Réseau de réplication :** quand vous déployez VMware HCX localement, vous devez définir un réseau de réplication. Utilisez le réseau de gestion défini comme réseau de réplication.  Si les hôtes du cluster local utilisent un réseau VMkernel de réplication dédié, réservez **deux** adresses IP dans ce segment réseau et utilisez le réseau VMkernel de réplication pour le réseau de réplication.
