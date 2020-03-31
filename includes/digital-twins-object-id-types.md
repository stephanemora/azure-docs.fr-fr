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
ms.openlocfilehash: cb43c8b8c952d8db6cf450a7015c22c85e7fe4b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76268301"
---
Le `objectIdType` (ou le **type d’identificateur d’objet**) fait référence au type d’identité auquel un rôle est attribué. En dehors des types `DeviceId` et `UserDefinedFunctionId`, les types d’identificateur d’objet correspondent à des propriétés d’objets Azure Active Directory.

Le tableau suivant contient les types d’identificateurs d’objet pris en charge dans Azure Digital Twins :

| Type | Description |
| --- | --- |
| UserId | Attribue un rôle à un utilisateur. |
| deviceId | Attribue un rôle à un appareil. |
| DomainName | Attribue un rôle à un nom de domaine. Chaque utilisateur avec le nom de domaine spécifié a les droits d’accès du rôle correspondant. |
| TenantId | Attribue un rôle à un locataire. Chaque utilisateur qui appartient à l’ID de locataire Azure AD spécifié a les droits d’accès du rôle correspondant. |
| ServicePrincipalId | Attribue un rôle à un ID d’objet de principal de service. |
| UserDefinedFunctionId | Attribue un rôle à une fonction définie par l’utilisateur. |