---
title: Héberger des applications personnalisées avec Azure Virtual Desktop - Azure
description: Comment traiter les applications personnalisées avec Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 35e07fad22760e6c8c87e60f77cd6d98e5db42a2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798794"
---
# <a name="how-to-host-custom-apps-with-azure-virtual-desktop"></a>Comment héberger des applications personnalisées avec Azure Virtual Desktop

Azure Virtual Desktop peut héberger plusieurs types d’applications Windows. Nous vous recommandons de préparer vos applications en fonction du type de packages d’application avec lequel vous envisagez de déployer vos applications. Dans cet article, nous allons vous expliquer ce que vous devez faire pour chaque type de package d’application. 

>[!NOTE]
>Nous vous recommandons d’héberger vos applications sur un hôte multisession. Nous vous recommandons également de tester vos applications pour garantir qu’elles se comportent comme prévu lors de l’exécution sur votre hôte multisession. Par exemple, exécutez un test pour voir si deux utilisateurs ou plus sur le même hôte de session peuvent exécuter correctement l’application en même temps.

## <a name="msix"></a>MSIX

MSIX est le type de package recommandé pour les applications personnalisées dans Azure Virtual Desktop, car il peut tirer parti de la [fonctionnalité intégrée d’attachement d’application MSIX](../app-attach-glossary.md) du service. Pour savoir comment réempaqueter les applications Win32 existantes au format MSIX, consultez [Réempaqueter vos applications Win32 existantes au format MSIX](/windows/application-management/msix-app-packaging-tool).

Une fois que vous avez empaqueté votre application au format MSIX, vous pouvez utiliser la fonctionnalité d’attachement d’application MSIX d’Azure Virtual Desktop pour fournir vos applications à vos clients. Découvrez comment utiliser l’attachement d’application MSIX pour vos applications dans [Déployer des applications avec l’attachement d’application MSIX](msix-app-attach.md).

## <a name="other-options-for-win32-applications"></a>Autres options pour les applications Win32

Vous pouvez également proposer des applications Win32 à vos utilisateurs sans les réempaqueter au format MSIX à l’aide des options suivantes.

### <a name="include-the-application-manually-on-session-hosts"></a>Inclure l’application manuellement sur des hôtes de session

Suivez les instructions mentionnées dans la rubrique [Préparer et personnaliser une image de disque dur virtuel principale](../set-up-customize-master-image.md) pour inclure une application dans le cadre de l’image Windows que vous utilisez pour vos machines virtuelles. Plus précisément, suivez les instructions de la section [Configuration du Registre et d’autres applications](../set-up-customize-master-image.md#other-applications-and-registry-configuration) pour tous les utilisateurs.

### <a name="use-microsoft-endpoint-manager-to-deploy-the-application-at-scale"></a>Utiliser Microsoft Endpoint Manager pour déployer l’application à grande échelle

Si vous utilisez Microsoft Endpoint Manager pour gérer vos hôtes de session, vous pouvez déployer des applications en suivant les instructions fournies dans [Installation d’applications sur des appareils Windows 10](/mem/intune/apps/apps-windows-10-app-deploy#install-apps-on-windows-10-devices). Veillez à déployer votre application en mode « Contexte d’appareil » sur tous les hôtes de session pour garantir que tous les utilisateurs de votre déploiement peuvent accéder à l’application.

### <a name="manual-installation"></a>Installation manuelle

Nous vous déconseillons d’installer les applications manuellement, car cela nécessite de répéter le processus pour chaque hôte de session. Cette méthode est plus souvent utilisée par les professionnels de l’informatique à des fins de test.

Si vous devez installer vos applications manuellement, vous devez vous connecter à distance à votre hôte de session avec un compte d’administrateur après avoir configuré votre pool d’hôtes Azure Virtual Desktop. Après cela, installez l’application comme vous le feriez sur un ordinateur physique. Vous devez répéter ce processus pour installer l’application sur chaque hôte de session de votre pool d’hôtes.

>[!NOTE]
>Si le processus d’installation vous donne la possibilité d’installer l’application pour tous les utilisateurs, sélectionnez cette option.

## <a name="microsoft-store-applications"></a>Applications Microsoft Store

Pour le moment, nous vous déconseillons d’utiliser des applications Microsoft Store pour diffuser en streaming des applications distantes dans Azure Virtual Desktop.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment empaqueter et déployer des applications à l’aide de l’attachement d’application MSIX, consultez [Déployer des applications avec l’attachement d’application MSIX](msix-app-attach.md).