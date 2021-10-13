---
title: Microsoft Endpoint Configuration Manager pour Azure Virtual Desktop
description: Méthodes recommandées pour gérer votre environnement Azure Virtual Desktop.
author: heidilohr
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1c44b679daa000602aad83d98a04894c5cc267b3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359107"
---
# <a name="microsoft-endpoint-manager-and-intune-for-azure-virtual-desktop"></a>Microsoft Endpoint Manager et Intune pour Azure Virtual Desktop

Nous vous recommandons d’utiliser [Microsoft Endpoint Manager](https://www.microsoft.com/endpointmanager) pour gérer votre environnement Azure Virtual Desktop après le déploiement. Microsoft Endpoint Manager est une plateforme de gestion unifiée qui comprend Microsoft Endpoint Configuration Manager et Microsoft Intune.

## <a name="microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager

Les versions 1906 et ultérieures de Microsoft Endpoint Configuration Manager peuvent gérer vos appareils Azure Virtual Desktop. Pour plus d’informations, consultez les [versions d’OS prises en charge pour les clients et les appareils avec Configuration Manager](/mem/configmgr/core/plan-design/configs/supported-operating-systems-for-clients-and-devices#windows-virtual-desktop).

## <a name="microsoft-intune"></a>Microsoft Intune

Intune prend en charge les machines virtuelles Windows 10 Entreprise pour Azure Virtual Desktop. Pour plus d’informations sur la prise en charge, consultez [Utilisation de Windows 10 Entreprise avec Intune](/mem/intune/fundamentals/windows-virtual-desktop).

La prise en charge d’Intune pour les machines virtuelles multisession Windows 10 Entreprise sur Azure Virtual Desktop est en préversion publique. Pour voir ce que la préversion publique prend en charge, consultez [Utilisation de la fonctionnalité multisession de Windows 10 Entreprise avec Intune](/mem/intune/fundamentals/windows-virtual-desktop-multi-session).

## <a name="licensing"></a>Licence

Les [licences Microsoft Endpoint Configuration Manager et Microsoft Intune](https://microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) sont incluses dans la plupart des abonnements Microsoft 365. 

Découvrez plus en détail les impératifs spécifiques aux licences en consultant les ressources suivantes :

- [Foire aux questions sur les branches et la gestion des licences de Configuration Manager](/mem/configmgr/core/understand/product-and-licensing-faq#bkmk_equiv-sub) 
- [Gestion des licences Microsoft Intune](/mem/intune/fundamentals/licenses)
