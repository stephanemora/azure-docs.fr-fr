---
title: Solution Azure VMware de CloudSimple – Élever les privilèges CloudSimple
description: Indique comment élever les autorisations CloudSimple pour effectuer des fonctions d’administration dans le vCenter de cloud privé
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 524772578ad724e969bbeab0be0a3edcf32a845f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619482"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Élevez les privilèges CloudSimple pour effectuer des fonctions d’administration dans le cloud privé vCenter

L’approche des privilèges CloudSimple est conçue pour accorder aux utilisateurs vCenter les privilèges dont ils ont besoin pour effectuer des opérations normales. Dans certains cas, un utilisateur peut avoir besoin de privilèges supplémentaires pour effectuer une tâche particulière.  Vous pouvez élever les privilèges d’un utilisateur vCenter SSO pour une période limitée.

Les privilèges peuvent être élevés pour les raison suivantes :

* Configuration des sources d’identité
* User Management
* Suppression de groupe de ports distribués
* Installation de solutions vCenter (telles que les applications de sauvegarde)
* Création de comptes de service

> [!WARNING]
> Les actions effectuées lorsque les privilèges sont élevés peuvent nuire à votre système ou le rendre indisponible. Effectuez uniquement les actions nécessaires pendant la période d’escalade.

À partir du portail CloudSimple, [élevez les privilèges](escalate-private-cloud-privileges.md) de l’utilisateur local CloudOwner sur le SSO vCenter.  Vous pouvez élever les privilèges de l’utilisateur distant uniquement si un fournisseur d’identité supplémentaire est configuré sur vCenter.  L’escalade des privilèges implique l’ajout de l’utilisateur sélectionné au groupe Administrateurs intégré vSphere.  Un seul utilisateur peut avoir des privilèges élevés.  Si vous avez besoin d’élever les privilèges d’un autre utilisateur, annulez d’abord les privilèges des utilisateurs actuels.

Les utilisateurs de sources d’identité supplémentaires doivent être ajoutés en tant que membres du groupe CloudOwner.

Pendant la période d’escalade, CloudSimple utilise la surveillance automatisée avec les notifications d’alerte associées pour détecter toute modification accidentelle de l’environnement.
