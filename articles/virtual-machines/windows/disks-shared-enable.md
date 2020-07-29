---
title: Activer la fonctionnalité Disques partagés pour les disques managés Azure
description: Configurer un disque managé Azure avec la fonctionnalité Disques partagés afin de pouvoir le partager avec plusieurs machines virtuelles
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/16/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e36c539cc1143490aeb4862c928589db5c502656
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500591"
---
# <a name="enable-shared-disk"></a>Activer la fonctionnalité Disques partagés

Cet article explique comment activer la fonctionnalité Disques partagés pour les disques managés Azure. La fonctionnalité Disques partagés Azure est une nouvelle fonctionnalité pour disques managés Azure qui vous permet de connecter simultanément un disque managé à plusieurs machines virtuelles. La connexion d'un disque managé à plusieurs machines virtuelles vous permet de déployer de nouvelles applications en cluster ou de migrer des applications en cluster existantes vers Azure. 

Si vous recherchez des informations conceptuelles relatives aux disques managés sur lesquels la fonctionnalité Disques partagés est activée, reportez-vous à [Disques partagés Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]