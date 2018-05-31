---
title: 'Azure AD Connect : options d’appareil | Microsoft Docs'
description: Ce document décrit en détail les options d’appareil disponibles dans Azure AD Connect
services: active-directory
documentationcenter: ''
author: anandy
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: anandy
ms.openlocfilehash: d592cbb55643099b6b628a44f182d0a079a6c71e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354510"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect : options d’appareil

La documentation ci-après fournit des informations sur les différentes options d’appareil disponibles dans Azure AD Connect. Vous pouvez utiliser Azure AD Connect pour configurer les deux opérations suivantes : 
* **Joindre Hybrid Azure AD** : si votre environnement comporte une empreinte locale AD et que vous souhaitez également profiter des fonctionnalités proposées par Azure Active Directory, vous pouvez implémenter des appareils joints Azure AD hybrides. Il s’agit d’appareils qui sont à la fois, joints à votre service Active Directory local et à Azure Active Directory.
* **Réécriture d’appareil** : la réécriture d’appareil est utilisée pour activer l’accès conditionnel basé sur l’appareil à des appareils protégés par AD FS (2012 R2 ou version ultérieure).

## <a name="configure-device-options-in-azure-ad-connect"></a>Configurer les options d’appareil dans Azure AD Connect

1.  Exécutez Azure AD Connect. Sur la page **Tâches supplémentaires**, sélectionnez **Configurer les options de l’appareil**.
    ![Tâche Configurer les options de l’appareil](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    Lorsque vous cliquez sur Suivant, une page **Vue d’ensemble** décrit en détail les opérations que vous pouvez effectuer.
    ![Vue d’ensemble](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > La nouvelle tâche Configurer les options de l’appareil est uniquement disponible dans la version 1.1.819.0 et dans les versions ultérieures.

2.  Après avoir entré les informations d’identification pour Azure AD, vous pouvez choisir l’opération à effectuer sur la page Options de l’appareil.
    ![Opérations sur les appareils](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des appareils joints à Azure AD hybrides](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Configurer/désactiver la réécriture d’appareil](./active-directory-aadconnect-feature-device-writeback.md)

