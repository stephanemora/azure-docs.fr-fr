---
title: 'Référence : Dépréciation des images Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Détails sur la dépréciation affectant Azure Data Science Virtual Machine
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754771"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Référence : Dépréciation des images DSVM

Vous trouverez ci-dessous des suggestions pour traiter les dépréciations à venir sur Data Science Virtual Machine Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012 : Migration des disques de données

Nous cesserons de prendre en charge l’image DSVM Windows 2012 le 31 décembre 2019. Pour migrer un disque de données de votre machine virtuelle DSVM Windows 2012 vers une machine virtuelle DSVM Windows 2016, procédez comme suit :

1. Créez une nouvelle machine virtuelle DSVM Windows 2016, en suivant les instructions indiquées [ici](./provision-vm.md#create-your-dsvm).
1. Détachez les disques de données existants de votre image Windows 2012 en suivant [ces instructions](../../virtual-machines/windows/detach-disk.md).
1. Attachez le disque de l’étape précédente à votre image Windows 2016 en suivant [ces instructions](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Les nouveaux utilisateurs doivent utiliser les images Ubuntu ou Windows les plus récentes. CentOS restera disponible pour une utilisation avec les modèles de solution existants.