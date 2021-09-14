---
title: Recommandations sur l’architecture Azure Virtual Desktop - Azure
description: Recommandations concernant l’architecture d’Azure Virtual Desktop pour les développeurs d’applications.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0b398fe41ec1cda06fce1cf9141ec6bd5d6bcbfb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798795"
---
# <a name="architecture-recommendations"></a>Suggestions en matière d’architecture

Les déploiements Azure Virtual Desktop se présentent sous de nombreuses formes et tailles, en fonction de nombreux facteurs, comme les besoins des utilisateurs finaux, l’infrastructure existante de l’organisation qui déploie le service, etc. Comment vérifier que vous utilisez l’architecture appropriée qui répond aux besoins de votre organisation ?

Cet article fournit des conseils pour votre structure de déploiement Azure Virtual Desktop. Les exemples présentés dans cet article ne sont pas les seules manières possibles de déployer Azure Virtual Desktop. Toutefois, nous abordons deux des types de déploiements les plus élémentaires pour les utilisateurs internes et externes à votre organisation.

## <a name="deploying-azure-virtual-desktop-for-users-within-your-organization"></a>Déploiement d’Azure Virtual Desktop pour les utilisateurs internes à votre organisation

Si vous effectuez un déploiement Azure Virtual Desktop pour les utilisateurs de votre organisation, vous pouvez héberger l’ensemble de vos utilisateurs et ressources dans le même locataire Azure. Vous pouvez également utiliser les méthodes de gestion des identités actuellement prises en charge d’Azure Virtual Desktop pour assurer la sécurité de vos utilisateurs.

Les conditions requises les plus basiques pour un déploiement Azure Virtual Desktop qui peut répondre aux besoins des applications distantes et des bureaux à distance pour les utilisateurs de votre organisation sont les suivantes :

- Un pool d’hôtes pour héberger les sessions utilisateur
- Un abonnement Azure pour héberger le pool d’hôtes
- Un locataire Azure destiné à être le locataire propriétaire pour la gestion des abonnements et des identités

Toutefois, vous pouvez également créer un déploiement comprenant plusieurs pools d’hôtes qui proposent différentes applications à différents groupes d’utilisateurs.

Certains clients choisissent de créer des abonnements Azure distincts dans lesquels stocker chaque déploiement Azure Virtual Desktop. Cette pratique vous permet de dissocier le coût de chaque déploiement, en fonction des sous-organisations auxquelles ils fournissent des ressources. D’autres choisissent d’utiliser des étendues de facturation Azure pour dissocier les coûts à un niveau plus précis. Pour plus d’informations, consultez [Comprendre et utiliser des étendues](../../cost-management-billing/costs/understand-work-scopes.md).

## <a name="deploying-azure-virtual-desktop-for-users-outside-your-organization"></a>Déploiement d’Azure Virtual Desktop pour les utilisateurs externes à votre organisation

Si votre déploiement Azure Virtual Desktop répond aux besoins d’utilisateurs finaux externes à votre organisation, en particulier des utilisateurs qui n’utilisent généralement pas Windows ou qui n’ont pas accès à ses ressources internes, vous devez prendre en compte d’autres recommandations de sécurité.

Azure Virtual Desktop ne prend actuellement pas en charge les identités externes, notamment les utilisateurs B2B (Business-to-Business) ou B2C (Business-to-Client). Vous devez créer et gérer ces identités manuellement, et fournir vous-même les informations d’identification à vos utilisateurs. Les utilisateurs utilisent ensuite ces identités pour accéder aux ressources dans Azure Virtual Desktop.

Pour fournir une solution sécurisée à vos clients, Microsoft recommande vivement de créer un locataire et un abonnement Azure Active Directory (AD) pour chaque client avec sa propre instance Active Directory dédiée. Cette distinction signifie que vous allez devoir créer, pour chaque organisation, un déploiement Azure Virtual Desktop séparé qui est totalement isolé des autres déploiements et de leurs ressources. Les machines virtuelles utilisées par chaque organisation ne doivent pas pouvoir accéder aux ressources d’autres sociétés pour assurer la sécurité des informations. Vous pouvez configurer ces déploiements distincts à l’aide d’une combinaison d’Active Directory Domain Services (AD DS) et d’Azure AD Connect, ou en utilisant Azure AD Domain Services.
