---
title: Vue d’ensemble du stockage sur disque
description: Vue d’ensemble des disques managés Azure, qui gèrent les comptes de stockage en cas d’utilisation de machines virtuelles
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d3932047422571399ad0562b2f9f2d19c3e6799b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145296"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduction aux disques managés Azure

Les disques managés Azure sont des volumes de stockage de niveau bloc qui sont gérés par Azure et utilisés avec des machines virtuelles Azure. Les disques managés sont comme un disque physique sur un serveur local, mais virtualisé. Avec les disques managés, il vous suffit de spécifier la taille de disque ainsi que le type de disque et de provisionner le disque. Une fois que vous avez provisionné le disque, Azure s’occupe du reste.

Les types de disques disponibles sont les disques Ultra, les disques SSD Premium, les disques SSD Standard et les disques durs standard. Pour plus d’informations sur chaque type de disque individuel, consultez [Sélectionner un type de disque pour les machines virtuelles IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Sélectionner un type de disque pour les machines virtuelles IaaS](disks-types.md)