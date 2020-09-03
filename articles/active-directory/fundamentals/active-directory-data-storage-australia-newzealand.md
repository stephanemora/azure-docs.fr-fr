---
title: Stockage de données d’identité pour les clients australiens - Azure AD
description: Découvrez où Azure Active Directory stocke les données relatives à l’identité de ses clients australiens.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/21/2020
ms.custom: it-pro, seodec18, references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: edc53152fe532a071de744db77d5c08d799c445a
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318520"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Stockage de données client pour les clients australiens et néo-zélandais dans Azure Active Directory

Azure Active Directory (Azure AD) stocke ses données client dans un emplacement géographique qui dépend du pays indiqué lors de l’inscription à un service Microsoft Online Services. Microsoft Online Services inclut Office 365 et Azure. 

Pour plus d’informations sur l’emplacement des données Azure AD et des autres services Microsoft, consultez la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) du Centre de gestion de la confidentialité Microsoft.

À partir du 26 février 2020, Microsoft a commencé à stocker les données client Azure AD des nouveaux locataires présentant une adresse de facturation australienne ou néo-zélandaise dans les centres de données australiens. Entre le 1er mai 2020 et le 31 octobre 2020, Microsoft migrera les locataires existants disposant d’une adresse de facturation australienne ou néo-zélandaise dans les centres de données australiens sans aucune intervention du client. Le processus de migration n’impliquera pas de temps d’arrêt pour les clients et n’aura aucun impact sur les fonctionnalités des locataires.

Par ailleurs, certaines fonctionnalités Azure AD ne prennent pas encore en charge le stockage des données client en Australie. Pour obtenir des informations spécifiques sur les fonctionnalités, accédez à [Mappage de données Azure AD](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9). Par exemple, Microsoft Azure Multi-Factor Authentication stocke les données client aux États-Unis et les traite à l’échelle mondiale. Consultez [Résidence des données et données client pour Azure Multi-Factor Authentication](../authentication/concept-mfa-data-residency.md).

> [!NOTE]
> Les produits et services Microsoft ainsi que les applications tierces qui s’intègrent à Azure AD ont accès aux données client. Évaluez chacun des produits, services et applications que vous utilisez pour déterminer comment ils traitent les données client et s’ils répondent aux besoins de stockage de données de votre entreprise. Pour plus d’informations sur la résidence des données des services de Microsoft, consultez la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) du Microsoft Trust Center.