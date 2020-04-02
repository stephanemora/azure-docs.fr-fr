---
title: Stockage de données d’identité pour les clients australiens - Azure AD
description: Découvrez où Azure Active Directory stocke les données relatives à l’identité de ses clients australiens.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460532"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Stockage de données d’identité pour les clients australiens et néo-zélandais dans Azure Active Directory

Les données d’identité sont stockées par Azure AD dans un emplacement géographique en fonction de l’adresse fournie par votre organisation lors de l’abonnement à un service Microsoft Online tel qu’Office 365 ou Azure. Pour obtenir des informations sur le lieu de stockage de vos données d’identité client, vous pouvez utiliser la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) du Centre de gestion de la confidentialité Microsoft.

> [!NOTE]
> Les services et applications qui s’intègrent à Azure AD ont accès aux données d’identité client. Évaluez chaque service et application que vous utilisez pour déterminer comment ils traitent les données d’identité client, et s’ils répondent aux besoins de stockage de données de votre entreprise. Pour plus d’informations sur la résidence des données des services Microsoft, consultez la section « Où se trouvent vos données ? » du Centre de gestion de la confidentialité Microsoft.

Pour les clients qui ont fourni une adresse en Australie et Nouvelle-Zélande, et qui utilisent l’édition gratuite du service Azure AD, celui-ci conserve les données relatives aux informations d’identification personnelle (IIP) au repos au sein de centres de données australiens. 

Tous les autres services Azure AD Premium stockent les données client dans des centres de données globaux. Pour localiser le centre de données d’un service, consultez [Azure Active Directory - Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure Multi-Factor Authentication (MFA)

Le service MFA dans Azure AD stocke les données d’identité client dans des centres de données globaux au repos. Pour en savoir plus sur les informations utilisateur collectées et stockées par les serveurs Azure MFA et Azure MFA basés sur le cloud, consultez [Collecte de données utilisateur avec Microsoft Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency). Si les clients utilisent MFA, leurs données sont stockées en dehors des centres de données de l’Australie au repos. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les fonctions et fonctionnalités décrites ci-dessus, consultez les articles suivants :
- [Présentation d’authentification multifacteur](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
