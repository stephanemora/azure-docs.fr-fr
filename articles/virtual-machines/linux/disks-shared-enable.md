---
title: Activer la fonctionnalité Disques partagés pour les disques managés Azure
description: Configurer un disque managé Azure avec la fonctionnalité Disques partagés (préversion) afin de pouvoir le partager avec plusieurs machines virtuelles
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0dc7cf6e3f72ff9fa7ef5d7cbd85c1fb93f76f9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146766"
---
# <a name="enable-shared-disk"></a>Activer la fonctionnalité Disques partagés

Cet article explique comment activer la fonctionnalité Disques partagés (préversion) pour les disques managés Azure. La fonctionnalité Disques partagés Azure (préversion) est une nouvelle fonctionnalité pour disques managés Azure qui vous permet de connecter simultanément un disque managé à plusieurs machines virtuelles. La connexion d'un disque managé à plusieurs machines virtuelles vous permet de déployer de nouvelles applications en cluster ou de migrer des applications en cluster existantes vers Azure. 

Si vous recherchez des informations conceptuelles relatives aux disques managés sur lesquels la fonctionnalité Disques partagés est activée, reportez-vous à [Disques partagés Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]