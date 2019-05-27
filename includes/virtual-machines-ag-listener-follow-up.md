---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165411"
---
Une fois que vous avez créé l’écouteur de groupe de disponibilité, il peut être nécessaire d’ajuster les paramètres de cluster RegisterAllProvidersIP et HostRecordTTL pour la ressource de l’écouteur. Ces paramètres peuvent réduire le délai de reconnexion après un basculement, ce qui peut empêcher des délais d’expiration de la connexion. Pour plus d’informations sur ces paramètres et voir des exemples de code, consultez la rubrique [Créer ou configurer un écouteur de groupe de disponibilité](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

