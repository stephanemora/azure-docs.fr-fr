---
title: Fichier Include
description: Fichier Include
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 94baeb3d459b700cc95d88fb82f995957640aab6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012123"
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