---
title: 'Référence : Dépréciation des images DSVM'
description: Détails sur la dépréciation affectant Azure Data Science Virtual Machine (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 252f9c66034dbadaf7a2e9e6f78665c26d414deb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456283"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Référence : Dépréciation des images DSVM

Vous trouverez ci-dessous des suggestions pour traiter les dépréciations à venir sur Data Science Virtual Machine Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012 : Migration des disques de données

Nous cesserons de prendre en charge l’image DSVM Windows 2012 le 31 décembre 2019. Pour migrer un disque de données de votre machine virtuelle DSVM Windows 2012 vers une machine virtuelle DSVM Windows 2016, procédez comme suit :

1. Créez une nouvelle machine virtuelle DSVM Windows 2016, en suivant les instructions indiquées [ici](./provision-vm.md#create-your-dsvm).
1. Détachez les disques de données existants de votre image Windows 2012 en suivant [ces instructions](../../virtual-machines/windows/detach-disk.md).
1. Attachez le disque de l’étape précédente à votre image Windows 2016 en suivant [ces instructions](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
