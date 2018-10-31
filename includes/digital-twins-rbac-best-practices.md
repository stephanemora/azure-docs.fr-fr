---
title: Fichier Include
description: Fichier Include
services: azure-digital-twins
author: adamgerard
ms.service: azure-digital-twins
ms.topic: include
ms.date: 09/27/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 075587df445b46c8b03c5448cebf8cd8b12f1179
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323718"
---
Le contrôle d’accès en fonction du rôle est une stratégie de sécurité basée sur l’héritage permettant de gérer les accès, les autorisations et les rôles. Les rôles descendants héritent des autorisations des rôles parents. Des autorisations peuvent également être attribuées sans être héritées d’un rôle parent. Elle peuvent aussi être attribuées pour personnaliser un rôle, si besoin.

Par exemple, un **administrateur d’espace** risque d’avoir besoin d’un accès global pour exécuter toutes les opérations pour un espace spécifié (y compris tous les nœuds sous ou dans l’espace), tandis qu’un **programme d’installation d’appareil** aura seulement besoin d’autorisations en *lecture* et *mise à jour* sur les appareils et capteurs.

Dans tous les cas, les rôles doivent être octroyés **avec précision et sans dépasser l’accès nécessaire** pour remplir leurs tâches, selon le **principe des privilèges minimum** qui accorde à une identité *uniquement* :

* L’accès nécessaire pour effectuer son travail.
* Un rôle approprié et limité à son travail.

>[!IMPORTANT]
> **Suivez toujours le principe des privilèges minimum**.

Deux autres pratiques importantes à suivre avec le contrôle d’accès en fonction du rôle :

> [!div class="checklist"]
> * Auditez régulièrement les attributions de rôles pour vérifier que chaque rôle dispose des autorisations appropriées.
> * Les attributions et les rôles doivent être supprimés lorsque les utilisateurs en changent.