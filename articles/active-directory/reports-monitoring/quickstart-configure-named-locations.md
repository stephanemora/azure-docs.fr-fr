---
title: Configuration d’emplacements nommés dans Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer des emplacements nommés.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71b21c4727e7679c25dc2574a6506f8d3932aec2
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434787"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Démarrage rapide : Configurer des emplacements nommés dans Azure Active Directory

Les emplacements nommés vous permettent d’étiqueter des plages d’adresses IP approuvées dans votre entreprise. Azure AD utilise des emplacements nommés pour effectuer les actions suivantes :
- Détecter les faux positifs dans les [événements à risque](concept-risk-events.md). La connexion à partir d’un emplacement approuvé réduit le risque de connexion d’un utilisateur.   
- Configurer l’[accès conditionnel en fonction des emplacements](../conditional-access/location-condition.md).

Dans ce guide de démarrage rapide, vous apprenez à configurer des emplacements nommés dans votre environnement.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

* Un locataire Azure AD. Inscrivez-vous pour obtenir un [essai gratuit](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Un utilisateur, qui est administrateur général du locataire.
* Une plage IP qui est établie et fiable dans votre organisation. La plage d’adresses IP doit être au format **CIDR** (Classless Inter-Domain Routing).

## <a name="configure-named-locations"></a>Configurer des emplacements nommés

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le volet gauche, à partir de la section **Sécurité**, sélectionnez **Azure Active Directory**, puis **Accès conditionnel**.

    ![Onglet Accès conditionnel](./media/quickstart-configure-named-locations/entrypoint.png)

3. Dans la page **Accès conditionnel**, sélectionnez **Emplacements nommés**, puis **Nouvel emplacement**.

    ![Emplacements nommés](./media/quickstart-configure-named-locations/namedlocation.png)

6. Remplissez le formulaire dans la nouvelle page. 

   * Dans la zone de texte **Nom**, tapez un nom pour votre emplacement nommé.
   * Dans la zone **Plages d’adresses IP**, tapez la plage d’adresses IP au format CIDR.  
   * Cliquez sur **Créer**.
    
     ![Panneau Nouveau](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Accès conditionnel Azure AD](../active-directory-conditional-access-azure-portal.md)
- [Conditions d’emplacement dans l’accès conditionnel Azure AD](../conditional-access/location-condition.md)
- [Rapport sur les connexions à risque](concept-risky-sign-ins.md)  
