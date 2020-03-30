---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74005588"
---
Cet article fournit des instructions pour la création d’applications hautes performances avec Azure Premium Storage. Vous pouvez utiliser les instructions fournies dans ce document parallèlement aux bonnes pratiques de performances applicables aux technologies utilisées par votre application. Pour illustrer les instructions, nous avons utilisé comme exemple un SQL Server exécuté sur Premium Storage.

Bien que cet article couvre plusieurs scénarios de performances au niveau de la couche de stockage, vous devrez optimiser la couche applicative. Par exemple, si vous hébergez une batterie de serveurs SharePoint sur Azure Premium Storage, vous pouvez utiliser les exemples SQL Server de cet article pour optimiser le serveur de base de données. Vous devez également optimiser le serveur web et le serveur d’applications de la batterie de serveurs SharePoint pour obtenir de meilleures performances.

Cet article vous aidera à répondre à certaines questions courantes relatives à l’optimisation des performances applicatives sur Azure Premium Storage :

* Comment mesurer les performances de votre application ?  
* Pourquoi n’obtenez-vous pas les hautes performances attendues ?  
* Quels sont les facteurs qui influencent les performances de votre application sur Premium Storage ?  
* Comment ces facteurs influencent-ils les performances de votre application sur Premium Storage ?  
* Comment optimiser les performances d’E/S par seconde, de bande passante et de latence ?  

Ces instructions vous sont spécifiquement fournies pour Premium Storage, car les charges de travail exécutées sur Premium Storage sont extrêmement sensibles aux performances. Nous vous proposons des exemples lorsque cela s’y prête. Vous pouvez également appliquer certaines de ces instructions aux applications qui s’exécutent sur des machines virtuelles IaaS avec des disques de stockage Standard.