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
ms.openlocfilehash: 0f85f2770b98507618d5cbfdec1b55f397c7f514
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101749988"
---
Azure offre la possibilité d’accélérer les performances des E/S par seconde et des Mo/s du stockage sur disque : on parle alors de « bursting », aussi bien pour les machines virtuelles que pour les disques. Vous pouvez efficacement tirer parti du bursting à la fois au niveau des machines virtuelles et au niveau des disques pour obtenir de meilleures performances.

Le bursting pour les machines virtuelles Azure et le bursting pour les ressources de disque ne sont pas interdépendants. Vous n’avez pas besoin d’avoir une machine virtuelle prenant en charge le bursting pour pouvoir utiliser un disque attaché prenant en charge le bursting. De même, vous n’avez pas besoin d’avoir un disque attaché prenant en charge le bursting pour utiliser une machine virtuelle prenant en charge le bursting.