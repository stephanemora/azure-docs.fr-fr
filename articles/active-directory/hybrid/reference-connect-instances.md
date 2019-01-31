---
title: 'Azure AD Connect : Instances de service Sync | Microsoft Docs'
description: Cette page décrit des considérations spéciales relatives aux instances d’Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 95d959bb61ece7456aff2e7ecb1cf1bc0e54acb1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164450"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect : Considérations spécifiques concernant les instances
Azure AD Connect est couramment utilisé avec l’instance mondiale d’Azure AD et Office 365. Mais il existe également d’autres instances, qui ont des exigences différentes en matière d’URL et autres considérations spéciales.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Allemagne
[Microsoft Cloud Allemagne](https://www.microsoft.de/cloud-deutschland) est un cloud souverain géré par un client allemand approuvé dans le domaine des données.

| URL à ouvrir dans le serveur proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Listes de révocation de certificat |

Quand vous vous connectez à votre locataire Azure AD, vous devez utiliser un compte du domaine onmicrosoft.de.

Fonctionnalités actuellement absentes de Microsoft Cloud Allemagne :

* L’**écriture différée de mot de passe** est disponible en préversion avec Azure AD Connect version 1.1.570.0 et ultérieures.
* Les autres services Azure AD Premium ne sont pas disponibles.

## <a name="microsoft-azure-government-cloud"></a>Cloud Microsoft Azure Government
Le [cloud Microsoft Azure Government](https://azure.microsoft.com/features/gov/) est un cloud du gouvernement des États-Unis.

Ce cloud a été pris en charge par des versions antérieures de DirSync. À partir de la build 1.1.180 d’Azure AD Connect, la nouvelle génération du cloud est prise en charge. Cette génération utilise des points de terminaison États-Unis uniquement et a sa propre liste d’URL à ouvrir dans votre serveur proxy.

| URL à ouvrir dans le serveur proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (requis pour la détection automatique d'un locataire de gouvernement Azure AD) |
| \*.gov.us.microsoftonline.com |
| + Listes de révocation de certificat |

> [!NOTE]
> À compter de la version 1.1.647.0 d'AAD Connect, la définition de la valeur AzureInstance dans le Registre n’est plus nécessaire, à condition que *.windows.net soit ouvert sur vos serveurs proxy.

Fonctionnalités actuellement absentes du cloud Microsoft Azure Government :

* L’**écriture différée de mot de passe** est disponible en préversion avec Azure AD Connect version 1.1.570.0 et ultérieures.
* Les autres services Azure AD Premium ne sont pas disponibles.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](whatis-hybrid-identity.md).
