---
title: Azure VMware Solutions (AVS) - Élever les privilèges AVS
description: Décrit comment élever les autorisations AVS pour effectuer des fonctions d’administration dans le vCenter de cloud privé AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 90dd61fc9856978bab0b68de19d48493a8e0c5fd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025331"
---
# <a name="escalate-avs-privileges-to-perform-administrative-functions-in-avs-private-cloud-vcenter"></a>Élever les privilèges AVS pour effectuer des fonctions d’administration dans le vCenter de cloud privé AVS

L’approche des privilèges AVS est conçue pour accorder aux utilisateurs vCenter les privilèges dont ils ont besoin pour effectuer des opérations normales. Dans certains cas, un utilisateur peut avoir besoin de privilèges supplémentaires pour effectuer une tâche particulière. Vous pouvez élever les privilèges d’un utilisateur vCenter SSO pour une période limitée.

Les privilèges peuvent être élevés pour les raison suivantes :

* Configuration des sources d’identité
* User Management
* Suppression de groupe de ports distribués
* Installation de solutions vCenter (telles que les applications de sauvegarde)
* Création de comptes de service

> [!WARNING]
> Les actions effectuées lorsque les privilèges sont élevés peuvent nuire à votre système ou le rendre indisponible. Effectuez uniquement les actions nécessaires pendant la période d’escalade.

À partir du portail AVS, [élevez les privilèges](escalate-private-cloud-privileges.md) de l’utilisateur local CloudOwner sur le SSO vCenter. Vous pouvez élever les privilèges de l’utilisateur distant uniquement si un fournisseur d’identité supplémentaire est configuré sur vCenter. L’escalade des privilèges implique l’ajout de l’utilisateur sélectionné au groupe Administrateurs intégré vSphere. Un seul utilisateur peut avoir des privilèges élevés. Si vous avez besoin d’élever les privilèges d’un autre utilisateur, annulez d’abord les privilèges des utilisateurs actuels.

Les utilisateurs de sources d’identité supplémentaires doivent être ajoutés en tant que membres du groupe CloudOwner.

> [!CAUTION]
> Les nouveaux utilisateurs doivent être ajoutés uniquement à *Cloud-Owner-Group*, à *Cloud-Global-Cluster-Admin-Group*, à *Cloud-Global-Storage-Admin-Group*, à *Cloud-Global-Network-Admin-Group* ou à *Cloud-Global-VM-Admin-Group*.  Les utilisateurs ajoutés au groupe *Administrateurs* seront automatiquement supprimés.  Seuls les comptes de service doivent être ajoutés au groupe *Administrateurs* et les comptes de service ne doivent pas être utilisés pour se connecter à l’interface utilisateur web de vSphere.
Pendant la période d’escalade, AVS utilise la supervision automatisée avec les notifications d’alerte associées pour détecter toute modification accidentelle de l’environnement.
