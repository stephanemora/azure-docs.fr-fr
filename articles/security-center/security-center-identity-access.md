---
title: Surveiller l’identité et l’accès dans Azure Security Center | Microsoft Docs
description: Découvrez comment utiliser les fonctionnalités d’identité et d’accès dans Azure Security Center pour surveiller les problèmes liés à l’activité d’accès et à l’identité de vos utilisateurs.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2020
ms.author: memildin
ms.openlocfilehash: 183b81134b2fe72a539cc6460a05d828342aafbb
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086483"
---
# <a name="monitor-identity-and-access"></a>Surveiller l’identité et l’accès

> [!TIP]
> À partir de mars 2020, vous retrouverez les recommandations sur les identités et les accès d’Azure Security Center dans tous les abonnements au niveau tarifaire gratuit. Si vous avez des abonnements sur le niveau gratuit, leur degré de sécurisation est affecté, car la sécurité de leur identité et de leur accès n’a pas été évaluée. 

Lorsque Security Center identifie des failles de sécurité potentielles, il crée des suggestions qui vous guident tout au long du processus de configuration des contrôles nécessaires afin de renforcer et protéger vos ressources.

Le périmètre de sécurité a évolué d’un périmètre de réseau vers un périmètre d’identité. La sécurité se résume moins à la protection de votre réseau et plus à la défense de vos données, ainsi qu'à la gestion de la sécurité de vos applications et de vos utilisateurs. Désormais, comme de plus en plus d’applications et de données sont déplacées vers le cloud, l’identité devient le nouveau périmètre.

Le fait de surveiller vos activités d’identité vous permet de prendre des mesures avant qu’un événement ne survienne, ou des mesures réactives, pour contrer une tentative d’attaque. Voici des exemples de recommandations que vous pouvez voir dans la section sur la sécurité des ressources **Identité et accès** d’Azure Security Center :

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement
- Trois propriétaires au plus doivent être désignés pour votre abonnement
- Les comptes déconseillés doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement

Pour obtenir la liste complète des recommandations que vous pouvez y voir, consultez [Recommandations relatives à l’identité et à l’accès](recommendations-reference.md#recs-identity).

> [!NOTE]
> Si votre abonnement comporte plus de 600 comptes, Security Center n’est pas en mesure d’exécuter les recommandations d’identité dans votre abonnement. Les recommandations qui ne sont pas exécutées sont listées sous « Évaluations non disponibles » ci-dessous.
Security Center ne peut pas exécuter les recommandations d’identité sur des agents d’administration d’un partenaire fournisseur de solutions Cloud.
>


Toutes les recommandations sur les identités et les accès sont disponibles dans les deux contrôles de sécurité dans la page **Recommandations** :

- Gérer l’accès et les autorisations 
- Activer la MFA

![Les deux contrôles de sécurité avec les recommandations sur les identités et les accès](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Activer l’authentification multifacteur (MFA)

L’activation de MFA nécessite des [autorisations de locataire Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). 

- Si vous disposez d’une édition Premium d’AD, activez MFA avec l’[accès conditionnel](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

- Les utilisateurs de l’édition gratuite d’AD peuvent activer les **paramètres par défaut de sécurité** dans Azure Active Directory comme décrit dans la [documentation AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), mais la recommandation de Security Center d’activer MFA s’affiche toujours.


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les articles suivants :

- [Protection de vos machines et de vos applications dans Azure Security Center](security-center-virtual-machine-protection.md)
- [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
- [Protection de vos données et du service Azure SQL Database dans Azure Security Center](security-center-sql-service-recommendations.md)