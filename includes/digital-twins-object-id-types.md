---
title: Fichier Include
description: Fichier Include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 12d4278171d43fdaf8613a1c91bdbffc269adc56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949035"
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