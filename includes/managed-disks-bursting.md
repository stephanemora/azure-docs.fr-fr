---
title: Fichier include
description: Fichier include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 2af8b429b6addf6da32b34773525c51a36624e78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242135"
---
Sur Azure, nous offrons la possibilité d’optimiser les performances des E/S par seconde et des Mo/s du stockage sur disque, appelée « mode rafale » aussi bien sur les machines virtuelles que sur les disques. Le mode rafale est utile dans de nombreux scénarios, comme la gestion du trafic de disque inattendu ou les tâches de traitement par lots. Vous pouvez exploiter efficacement le mode rafale au niveau des machines virtuelles et au niveau des disques. Cela vous permet d’obtenir des performances de base de référence et de mode rafale exceptionnelles à la fois sur votre machine virtuelle et sur votre disque. 

Notez que le mode rafale sur les disques et les machines virtuelles sont indépendants l’un de l’autre. Si vous disposez d’un disque en mode rafale, vous n’avez pas besoin d’une machine virtuelle en mode rafale pour permettre la rafale de votre disque. Si vous disposez d’une machine virtuelle en mode rafale, vous n’avez pas besoin d’un disque en mode rafale pour permettre la rafale de votre machine virtuelle. 