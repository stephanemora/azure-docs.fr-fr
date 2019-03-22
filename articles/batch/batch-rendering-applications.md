---
title: Applications de rendu – Azure Batch
description: Applications de rendu Batch préinstallées
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 12/11/2018
ms.topic: conceptual
ms.openlocfilehash: 8ce430d83c52014b3d1d3d2a985f74aeb488caea
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791883"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Applications de rendu d’images de machine virtuelle préinstallées

Vous pouvez utiliser n’importe quelle application de rendu avec Azure Batch. Toutefois, les images de machine virtuelle de la Place de marché Azure sont disponibles avec des applications courantes préinstallées.

Le cas échéant, une licence de paiement à l’utilisation est disponible pour les applications de rendu préinstallées. Quand un pool Batch est créé, les applications requises peuvent être spécifiées et les frais de la machine virtuelle et des applications sont facturés à la minute. Les prix des applications sont listés dans la [page des prix Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Certaines applications prennent en charge uniquement Windows, mais la plupart sont prises en charge sur Windows et Linux.

## <a name="applications-on-centos-7-rendering-nodes"></a>Applications sur les nœuds de rendu CentOS 7

* Autodesk Maya I/O 2017 Update 5 (cut 201708032230)
* Autodesk Maya I/O 2018 Update 2 (cut 201711281015)
* Autodesk Arnold for Maya 2017 (Arnold version 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold version 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (version 3.60.04)
* Chaos Group V-Ray for Maya 2018 (version 3.60.04)
* Blender (2.68)

## <a name="applications-on-windows-server-2016-rendering-nodes"></a>Applications sur les nœuds de rendu Windows Server 2016

* Autodesk Maya I/O 2017 Update 5 (version 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (version 18.4.0.7622)  
* Autodesk 3ds Max I/O 2019 Update 1 (version 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (version 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max (Arnold version 5.0.2.4) (version 1.2.926)
* Chaos Group V-Ray for Maya (version 3.52.03)
* Chaos Group V-Ray for 3ds Max (version 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Étapes suivantes

Pour utiliser les images de machine virtuelle de rendu, celles-ci doivent être spécifiées dans la configuration du pool au moment de la création du pool. Consultez les [capacités du pool Batch pour le rendu](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).