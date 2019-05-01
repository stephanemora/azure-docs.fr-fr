---
title: Applications de rendu – Azure Batch
description: Applications de rendu Batch préinstallées
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 03/26/2018
ms.topic: conceptual
ms.openlocfilehash: 84b2ca30f1ccd49e18e2f9d42133f8672d3f8ad6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776167"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Applications de rendu d’images de machine virtuelle préinstallées

Vous pouvez utiliser n’importe quelle application de rendu avec Azure Batch. Toutefois, les images de machine virtuelle de la Place de marché Azure sont disponibles avec des applications courantes préinstallées.

Le cas échéant, une licence de paiement à l’utilisation est disponible pour les applications de rendu préinstallées. Quand un pool Batch est créé, les applications requises peuvent être spécifiées et les frais de la machine virtuelle et des applications sont facturés à la minute. Les prix des applications sont listés dans la [page des prix Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Certaines applications prennent en charge uniquement Windows, mais la plupart sont prises en charge sur Windows et Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Applications sur le rendu d’images CentOS 7

* Autodesk Maya I/O 2017 Update 5 (cut 201708032230)
* Autodesk Maya I/O 2018 Update 2 (cut 201711281015)
* Autodesk Arnold for Maya 2017 (Arnold version 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold version 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (version 3.60.04)
* Chaos Group V-Ray for Maya 2018 (version 3.60.04)
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Applications sur les dernières images de rendu de Windows Server 2016

La liste suivante s’applique à Windows Server 2016, les images de rendu version 1.3.4.

* Autodesk Maya I/O 2017 Update 5 (version 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (version 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Update 1 (version 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (version 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max (Arnold version 5.0.2.4) (version 1.2.926)
* Chaos Group V-Ray pour Maya 2018 (version 3.52.03)
* Chaos Group V-Ray pour 3ds Max 2018 (version 3.60.02)
* Chaos Group V-Ray pour Maya 2019 (version 3.52.03)
* Chaos Group V-Ray pour 3ds Max 2019 (version 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray pour 3ds Max 2019 (version 4.10.01) introduit des changements majeurs de V-ray. Pour utiliser la version précédente (version 3.60.02), utilisez Windows Server 2016, nœuds de rendu version 1.3.2.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Applications sur des images de rendu précédentes de Windows Server 2016

La liste suivante s’applique à Windows Server 2016, les images de rendu version 1.3.2.

* Autodesk Maya I/O 2017 Update 5 (version 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (version 18.4.0.7622)  
* Autodesk 3ds Max I/O 2019 Update 1 (version 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (version 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max (Arnold version 5.0.2.4) (version 1.2.926)
* Chaos Group V-Ray pour Maya 2019 (version 3.52.03)
* Chaos Group V-Ray pour 3ds Max 2018 (version 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Étapes suivantes

Pour utiliser les images de machine virtuelle de rendu, celles-ci doivent être spécifiées dans la configuration du pool au moment de la création du pool. Consultez les [capacités du pool Batch pour le rendu](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).