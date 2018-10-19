---
title: 'Azure AD Connect : options d’appareil | Microsoft Docs'
description: Ce document décrit en détail les options d’appareil disponibles dans Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6993895b6f2b836308b8ed7c375212c3fae12768
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46305918"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect : options d’appareil

La documentation ci-après fournit des informations sur les différentes options d’appareil disponibles dans Azure AD Connect. Vous pouvez utiliser Azure AD Connect pour configurer les deux opérations suivantes : 
* **Joindre Hybrid Azure AD** : si votre environnement comporte une empreinte locale AD et que vous souhaitez profiter d’Azure AD, vous pouvez implémenter des appareils joints Azure AD hybrides. Ces appareils sont joints à la fois à votre service Active Directory local et à Azure Active Directory.
* **Réécriture d’appareil** : la réécriture d’appareil est utilisée pour activer l’accès conditionnel basé sur l’appareil à des appareils protégés par AD FS (2012 R2 ou version ultérieure).

## <a name="configure-device-options-in-azure-ad-connect"></a>Configurer les options d’appareil dans Azure AD Connect

1.  Exécutez Azure AD Connect. Sur la page **Tâches supplémentaires**, sélectionnez **Configurer les options de l’appareil**.  Cliquez sur **Suivant**.
    ![Tâche Configurer les options de l’appareil](./media/how-to-connect-device-options/deviceoptions.png) 

    La page **Vue d’ensemble** affiche les détails.
    ![Vue d’ensemble](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > La nouvelle tâche Configurer les options de l’appareil est uniquement disponible dans la version 1.1.819.0 et dans les versions ultérieures.

2.  Après avoir entré les informations d’identification pour Azure AD, vous pouvez choisir l’opération à effectuer sur la page Options de l’appareil.
    ![Opérations sur les appareils](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des appareils joints à Azure AD hybrides](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Configurer/désactiver la réécriture d’appareil](how-to-connect-device-writeback.md)

