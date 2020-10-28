---
title: Segments réseau VMware HCX
description: Vous avez besoin de quatre réseaux pour VMware HCX.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 8137b4383d2a243d53db317db6f5a78b3bc68e67
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173638"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Quatre réseaux sont nécessaires pour VMware HCX :

- **Réseau de gestion :** En règle générale, il s’agit du même réseau de gestion que celui utilisé sur le cluster vSphere. Au minimum, identifiez deux adresses IP sur ce segment réseau pour VMware HCX. (Vous devrez peut-être utiliser des nombres plus importants, en fonction de votre déploiement.)

- **Réseau vMotion :** En règle générale, il s’agit du même réseau que celui utilisé pour vMotion sur le cluster vSphere.  Au minimum, identifiez deux adresses IP sur ce segment réseau pour VMware HCX. (Vous devrez peut-être utiliser des nombres plus importants, en fonction de votre déploiement.)  

   Le réseau vMotion doit être exposé sur un commutateur virtuel distribué ou vSwitch0. Dans le cas contraire, modifiez l’environnement.

   > [!NOTE]
   > Si ce réseau n’est pas routé (privé), c’est bon.

- **Réseau de liaison montante :** Vous souhaitez créer un réseau pour la liaison montante VMware HCX et l’étendre à votre cluster vSphere via un groupe de ports. Au minimum, identifiez deux adresses IP sur ce segment réseau pour VMware HCX. (Vous devrez peut-être utiliser des nombres plus importants, en fonction de votre déploiement.)  

   > [!NOTE]
   > La méthode recommandée consiste à créer un réseau /29, mais n’importe quelle taille de réseau peut convenir.

- **Réseau de réplication :** Vous souhaitez créer un réseau pour la réplication VMware HCX et étendre ce réseau à votre cluster vSphere via un groupe de ports. Au minimum, identifiez deux adresses IP sur ce segment réseau pour VMware HCX. (Vous devrez peut-être utiliser des nombres plus importants, en fonction de votre déploiement.)

   > [!NOTE]
   > La méthode recommandée consiste à créer un réseau /29, mais n’importe quelle taille de réseau peut convenir.