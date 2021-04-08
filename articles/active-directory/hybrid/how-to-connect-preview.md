---
title: 'Azure AD Connect : Fonctionnalités en préversion | Microsoft Docs'
description: Cette rubrique décrit en détail les fonctionnalités disponibles en version préliminaire dans Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d48cd8f95ecaf3e537a5221d766af150a51aa31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517682"
---
# <a name="more-details-about-features-in-preview"></a>Plus de détails sur les fonctionnalités de la version préliminaire
Cette rubrique décrit l’utilisation des fonctionnalités disponibles dans la version préliminaire.

## <a name="azure-ad-connect-sync-v2-endpoint-api"></a>API de point de terminaison V2 pour la synchronisation d’Azure AD Connect

Nous avons déployé un nouveau point de terminaison (API) pour Azure AD Connect, qui améliore les performances des opérations des services de synchronisation pour Azure Active Directory. En utilisant le nouveau point de terminaison V2, vous constaterez des gains de performances perceptibles lors de l’exportation et de l’importation vers Azure AD. Ce nouveau point de terminaison prend également en charge la synchronisation de groupes comptant jusqu’à 250 000 membres. L’utilisation de ce point de terminaison vous permet également de mettre à jour des groupes unifiés Microsoft 365, sans limite d’appartenance maximale, sur votre Active Directory local, quand l’écriture différée de groupe est activée. Pour plus d’informations, consultez [API du point de terminaison V2 pour la synchronisation Azure AD Connect](how-to-connect-sync-endpoint-api-v2.md).

## <a name="user-writeback"></a>Écriture différée de l’utilisateur
> [!IMPORTANT]
> La fonctionnalité d’écriture différée utilisateur en version préliminaire a été supprimée lors de la mise à jour d’Azure AD Connect en août 2015. Si vous l'avez activée, vous devez désactiver cette fonctionnalité.
>
>

## <a name="next-steps"></a>Étapes suivantes
Poursuivez votre [installation personnalisée d’Azure AD Connect](how-to-connect-install-custom.md).

En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](whatis-hybrid-identity.md).
