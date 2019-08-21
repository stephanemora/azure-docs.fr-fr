---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3af8077627d56ce44c5e2959e2c79b967b09d9e5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011936"
---
Une signature d’accès partagé (SAP) vous permet d’accorder un accès limité aux conteneurs et aux objets blob de votre compte de stockage. Lorsque vous créez une SAP, vous spécifiez ses contraintes, notamment les ressources de Stockage Azure auxquelles un client est autorisé à accéder, les autorisations dont ils disposent sur ces ressources et la durée de validité de la SAP.

Chaque SAP est signée avec une clé. Vous pouvez signer une SAP de l’une des deux méthodes suivantes :

- Avec une clé créée à l’aide des informations d’identification d’Azure Active Directory (Azure AD). Une SAP signée avec des informations d’identification d’Azure AD est une SAP de *délégation d’utilisateur*.
- Avec la clé du compte de stockage. Une *SAP de service* et une *SAP de compte* sont signées avec la clé du compte de stockage.
