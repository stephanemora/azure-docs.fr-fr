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
ms.openlocfilehash: f3b9f6c27fb8d423350eac5d286c9859ad6fbd37
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70104249"
---
## <a name="about-the-user-delegation-sas-preview"></a>À propos de la SAP de délégation d’utilisateur (préversion)

Un jeton SAP pour l’accès à un conteneur ou à un objet blob peut être sécurisé à l’aide d’informations d’identification Azure AD ou d’une clé de compte. Une SAP sécurisée avec des informations d’identification Azure AD est appelée une SAP de délégation d’utilisateur, car le jeton OAuth 2.0 utilisé pour signer la SAP est demandé au nom de l’utilisateur.

Comme meilleure pratique de sécurité, Microsoft vous recommande d’utiliser si possible les informations d’identification Azure AD plutôt que d’utiliser la clé de compte qui peut être plus facilement compromise. Lorsque la conception de votre application nécessite des signatures d’accès partagé, utilisez les informations d’identification Azure AD pour créer une SAP de délégation d’utilisateur pour profiter d’une sécurité supérieure. Pour plus d’informations sur la SAP de délégation d’utilisateur, consultez [Créer une SAP de délégation d’utilisateur](/rest/api/storageservices/create-user-delegation-sas).

> [!NOTE]
> La préversion de la SAP de délégation d’utilisateur est destinée uniquement à une utilisation hors production.

> [!CAUTION]
> Tout client disposant d’une SAP valide peut accéder aux données de votre compte de stockage tel qu’autorisé par cette SAP. Il est important de protéger une SAP contre toute utilisation malveillante ou involontaire. Faites preuve de discrétion lors de la distribution d’une SAP et mettez en place un plan de révocation d’une SAS compromis.

Pour plus d’informations sur les signatures d’accès partagé, consultez [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](../articles/storage/common/storage-sas-overview.md).
