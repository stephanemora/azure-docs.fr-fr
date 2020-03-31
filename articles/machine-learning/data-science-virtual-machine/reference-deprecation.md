---
title: 'Référence : Dépréciation des images Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Détails sur la dépréciation affectant Azure Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525785"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Référence : Dépréciation des images DSVM

Vous trouverez ci-dessous des suggestions pour traiter les dépréciations à venir sur Data Science Virtual Machine Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012 : Migration des disques de données

Nous cesserons de prendre en charge l’image DSVM Windows 2012 le 31 décembre 2019. Pour migrer un disque de données de votre machine virtuelle DSVM Windows 2012 vers une machine virtuelle DSVM Windows 2016, procédez comme suit :

1. Créez une nouvelle machine virtuelle DSVM Windows 2016, en suivant les instructions indiquées [ici](./provision-vm.md#create-your-dsvm).
1. Détachez les disques de données existants de votre image Windows 2012 en suivant [ces instructions](../../virtual-machines/windows/detach-disk.md).
1. Attachez le disque de l’étape précédente à votre image Windows 2016 en suivant [ces instructions](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
