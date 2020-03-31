---
title: Fichier Include
description: Fichier Include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 893beb0800af0eece4d69e727e427c3e92b79121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044922"
---
Le contrôle d’accès en fonction du rôle est une stratégie de sécurité basée sur l’héritage permettant de gérer les accès, les autorisations et les rôles. Les rôles descendants héritent des autorisations des rôles parents. Les autorisations peuvent également être attribuées sans être héritées d’un rôle parent. Elle peuvent aussi être attribuées pour personnaliser un rôle, si besoin.

Par exemple, l’administrateur de l’espace peut avoir besoin d’un accès global pour exécuter toutes les opérations de cet espace. L’accès comprend tous les nœuds en dessous de l’espace ou dans l’espace. Le programme d’installation de l’appareil peut nécessiter uniquement les autorisations *lire* et *mettre à jour* pour les appareils et les capteurs.

Dans tous les cas, les rôles doivent être octroyés *avec précision et sans aller au-delà de l’accès nécessaire* pour remplir leurs tâches, selon le principe des privilèges minimum. Conformément à ce principe, l’identité est accordée *uniquement* :

* L’accès nécessaire pour effectuer son travail.
* Un rôle approprié et limité à son travail.

>[!IMPORTANT]
> Suivez toujours le principe des privilèges minimum.

Deux autres pratiques importantes à suivre avec le contrôle d’accès en fonction du rôle :

> [!div class="checklist"]
> * Auditez régulièrement les attributions de rôles pour vérifier que chaque rôle dispose des autorisations appropriées.
> * Les attributions et les rôles doivent être modifiés lorsque les utilisateurs en changent.