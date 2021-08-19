---
title: Qu’est-ce que le streaming d’applications distantes Azure Virtual Desktop ? - Azure
description: Une présentation du streaming d’applications distantes Azure Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 88bc4c94612a69be8a9d7aad7bbad983b687235a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798785"
---
# <a name="what-is-azure-virtual-desktop-remote-app-streaming"></a>Qu’est-ce que le streaming d’applications distantes Azure Virtual Desktop ?

Azure Virtual Desktop est un service de virtualisation d’applications et de bureau qui s’exécute sur le cloud et vous permet d’accéder à votre bureau à distance à tout moment, où que vous soyez. Toutefois, saviez-vous que vous pouviez également utiliser le Azure Virtual Desktop comme plateforme en tant que service (PaaS) pour fournir des applications SaaS (Software as a service) de votre organisation à vos clients ? Avec la diffusion d’applications à distance d’Azure Virtual Desktop, vous pouvez désormais utiliser Azure Virtual Desktop pour fournir des applications à vos clients via un réseau sécurisé via des machines virtuelles.

Si vous n’êtes pas familiarisé avec Azure Virtual Desktop (ou que vous découvrez la virtualisation d’applications en général), nous avons rassemblé des ressources qui vous aideront à faire fonctionner votre déploiement.

## <a name="requirements"></a>Spécifications

Avant de commencer, nous vous recommandons de consulter la [Présentation d’Azure Virtual Desktop](../overview.md) pour une liste plus approfondie des exigences système pour exécuter Azure Virtual Desktop. Au passage, vous pouvez parcourir le reste de la documentation d’Azure Virtual Desktop si vous souhaitez avoir plus d’informations sur le service, car la plupart des articles s’appliquent également à la diffusion d’applications à distance pour Azure Virtual Desktop. Une fois que vous avez compris les principes de base, vous pouvez utiliser la documentation de streaming d’application à distance plus efficacement.

Pour configurer un déploiement Azure Virtual Desktop pour que vos applications personnalisées soient disponibles pour des clients externes à votre organisation, vous avez besoin des éléments suivants :

- Votre application personnalisée. Voir [Comment héberger des applications personnalisées avec Azure Virtual Desktop](custom-apps.md) Pour en savoir plus sur les types d’applications qu’Azure Virtual Desktop prend en charge et comment vous pouvez les servir à vos clients.

- Informations d’identification de jonction de domaine. Si vous ne disposez pas déjà d’un système de gestion des identités compatible avec Azure Virtual Desktop, vous devez configurer la gestion des identités pour votre pool hôte.

- Un abonnement Azure. Si vous n’avez pas encore d’abonnement, assurez-vous de [Créer un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="get-started"></a>Bien démarrer

Maintenant que vous êtes prêt, jetons un coup d’œil à la façon dont vous pouvez configurer votre déploiement Azure Virtual Desktop.

Vous pouvez configurer votre déploiement manuellement en suivant les didacticiels suivants :

1. [Créer un pool d’hôtes avec le portail Azure](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

2. [Gérer les groupes d’applications](../manage-app-groups.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

3. [Créer un pool d’hôtes pour valider les mises à jour de service](../create-validation-host-pool.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

4. [Configurer des alertes de service](../set-up-service-alerts.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="customize-and-manage-azure-virtual-desktop"></a>Personnaliser et gérer Azure Virtual Desktop

Une fois que vous avez configuré Azure Virtual Desktop, vous disposez de nombreuses options pour personnaliser votre déploiement en fonction des besoins de votre organisation ou de vos clients. Ces articles peuvent vous aider à démarrer :

- [Comment héberger des applications personnalisées avec Azure Virtual Desktop](custom-apps.md)
- [Inscrire votre abonnement à la tarification d’accès par utilisateur](per-user-access-pricing.md)
- [Comment utiliser Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [Utilisation de machines virtuelles Windows 10 avec Intune](/mem/intune/fundamentals/windows-10-virtual-machines)
- [Comment déployer une application à l’aide de l’attachement d’application MSIX](msix-app-attach.md)
- [Azure Monitor pour Azure Virtual Desktop permet de superviser votre déploiement](../azure-monitor.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Configurer un plan de continuité d’activité et la reprise d’activité (BCDR)](../disaster-recovery.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Procéder à la mise à l'échelle des hôtes de session à l'aide d'Azure Automation](../set-up-scaling-script.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Configurer l’impression universelle](/universal-print/fundamentals/universal-print-getting-started)

## <a name="get-to-know-your-azure-virtual-desktop-deployment"></a>Découverte de votre déploiement Azure Virtual Desktop

Lisez les articles suivants pour comprendre les concepts essentiels à la création et à la gestion des déploiements Azure Virtual Desktop :

- [Compréhension de la tarification des licences et de l’accès par utilisateur](licensing.md)
- [Instructions de sécurité pour les applications inter-organisations](security.md)
- [Bonnes pratiques pour la sécurité d’Azure Virtual Desktop](../security-guide.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Glossaire d’Azure Monitor pour Azure Virtual Desktop](../azure-monitor-glossary.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Azure Virtual Desktop pour l’entreprise](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
- [Estimer les coûts de déploiement totaux](total-costs.md)
- [Recommandations en matière d’architecture](architecture-recs.md)

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes prêt à commencer la configuration de votre déploiement manuellement, consultez le didacticiel suivant.

> [!div class="nextstepaction"]
> [Créer un pool d’hôtes avec le portail Azure](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
