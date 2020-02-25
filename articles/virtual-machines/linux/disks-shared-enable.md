---
title: Activer la fonctionnalité Disques partagés pour les disques managés Azure
description: Configurer un disque managé Azure avec la fonctionnalité Disques partagés (préversion) afin de pouvoir le partager avec plusieurs machines virtuelles
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4562567aea667dd7efadbcfb033e782ec9418e3f
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472058"
---
# <a name="enable-shared-disk"></a>Activer la fonctionnalité Disques partagés

Cet article explique comment activer la fonctionnalité Disques partagés (préversion) pour les disques managés Azure. La fonctionnalité Disques partagés Azure (préversion) est une nouvelle fonctionnalité pour disques managés Azure qui vous permet de connecter simultanément un disque managé à plusieurs machines virtuelles. La connexion d'un disque managé à plusieurs machines virtuelles vous permet de déployer de nouvelles applications en cluster ou de migrer des applications en cluster existantes vers Azure. 

Si vous recherchez des informations conceptuelles sur les disques managés sur lesquels la fonctionnalité Disques partagés est activée, reportez-vous à [Disques partagés Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]