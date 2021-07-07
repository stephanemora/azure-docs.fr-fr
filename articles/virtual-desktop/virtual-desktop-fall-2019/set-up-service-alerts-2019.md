---
title: Configurer des alertes de service pour Azure Virtual Desktop (classique) – Azure
description: Guide pratique pour configurer Azure Service Health afin de recevoir des notifications de service pour Azure Virtual Desktop (classique).
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 452dd8ce28589d473b730e1215e86c67fa490e48
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751736"
---
# <a name="tutorial-set-up-service-alerts-for-azure-virtual-desktop-classic"></a>Tutoriel : configurer des alertes de service pour Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop Azure Resource Manager, consultez [cet article](../set-up-service-alerts.md).

Vous pouvez utiliser Azure Service Health pour surveiller les problèmes de service et les avis d’intégrité pour Azure Virtual Desktop. Azure Service Health peut vous envoyer des notifications avec différents types d’alertes (par exemple, e-mail ou SMS), vous aider à comprendre l’effet d’un problème et vous tenir informé de son évolution. Azure Service Health peut également vous aider à réduire les temps d’arrêt, vous préparer à la maintenance planifiée et aux changements susceptibles d’affecter la disponibilité de vos ressources.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez et configurez des alertes de service.

Pour en savoir plus sur Azure Service Health, consultez la [Documentation Azure Health](../../service-health/index.yml).

## <a name="prerequisites"></a>Prérequis

- [Tutoriel : créer un locataire dans Azure Virtual Desktop](tenant-setup-azure-active-directory.md)
- [Tutoriel : Créer des principaux de service et des attributions de rôles avec PowerShell](create-service-principal-role-powershell.md)
- [Tutoriel : Créer un pool d’hôtes avec la Place de marché Azure](create-host-pools-azure-marketplace-2019.md)

## <a name="create-service-alerts"></a>Créer des alertes de service

Cette section vous montre comment configurer Azure Service Health et définir des notifications, auxquelles vous pouvez accéder sur le portail Azure. Vous pouvez définir différents types d’alertes et les planifier pour vous avertir en temps voulu.

### <a name="recommended-service-alerts"></a>Alertes de service recommandées

Nous vous recommandons de créer des alertes de service pour les types d’événements d’intégrité suivants :

- **Problème de service** : recevez des notifications en cas de problèmes majeurs ayant un impact sur la connectivité de vos utilisateurs par rapport au service ou à la capacité à gérer votre locataire Azure Virtual Desktop.
- **Avis d’intégrité :** Recevez des notifications qui nécessitent votre attention. Voici quelques exemples de ce type de notification :
    - Des machines virtuelles (VM) n’étant pas configurées de façon sûre avec le port 3389 ouvert
    - Dépréciation de fonctionnalité

### <a name="configure-service-alerts"></a>Configurer les alertes de service

Pour configurer des alertes de service :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Service Health.**
3. Suivez les instructions de [Créer des alertes de journal d’activité sur les notifications de service](../../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json) pour configurer vos alertes et notifications.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment configurer et utiliser Azure Service Health pour superviser les problèmes de service et les avis d’intégrité pour Azure Virtual Desktop. Pour en savoir plus sur la connexion à Azure Virtual Desktop, passez aux guides pratiques Se connecter à Azure Virtual Desktop.

> [!div class="nextstepaction"]
> [Se connecter au client Bureau à distance sur Windows 7 et Windows 10](connect-windows-7-10-2019.md)