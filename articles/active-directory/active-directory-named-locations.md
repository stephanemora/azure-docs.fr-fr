---
title: Configuration d’emplacements nommés dans Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer des emplacements nommés.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.component: protection
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f739917b201d5255716d22930d7c4bd9e6602f37
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224515"
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Configurer des emplacements nommés dans Azure Active Directory

Les emplacements nommés vous permettent d’étiqueter des plages d’adresses IP approuvées dans votre entreprise. Azure Active Directory utilise des emplacements nommés dans le contexte suivant :

- La détection [d’événements à risque](active-directory-reporting-risk-events.md) pour réduire le nombre de faux positifs reportés.  

- [Accès conditionnel en fonction des emplacements](active-directory-conditional-access-locations.md).


Cet article explique comment configurer des emplacements nommés dans votre environnement.


## <a name="entry-points"></a>Points d’entrée

Vous pouvez accéder à la page de configuration des emplacements nommés dans la section **Sécurité** de la page Azure Active Directory en cliquant sur :

![Points d’entrée](./media/active-directory-named-locations/34.png)

- **Accès conditionnel :**

    - Dans la section **Gérer**, cliquez sur **Emplacements nommés**.
    
        ![Commande Emplacements nommés](./media/active-directory-named-locations/06.png)

- **Connexions risquées :**

    - Dans la barre d’outils supérieure, cliquez sur **Ajouter des plages d’adresses IP connues**.

       ![Commande Emplacements nommés](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Exemple de configuration

**Pour configurer un emplacement nommé :**

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général.

2. Dans le volet gauche, cliquez sur **Azure Active Directory**.

    ![Lien Azure Active Directory dans le volet gauche](./media/active-directory-named-locations/01.png)

3. Dans la page **Azure Active Directory**, dans la section **Sécurité**, cliquez sur **Accès conditionnel**.

    ![Commande Accès conditionnel](./media/active-directory-named-locations/05.png)


4. Dans la page **Accès conditionnel**, dans la section **Gérer**, cliquez sur **Emplacements nommés**.

    ![Commande Emplacements nommés](./media/active-directory-named-locations/06.png)


5. Dans la page **Emplacements nommés**, cliquez sur **Nouvel emplacement**.

    ![Commande Nouvel emplacement](./media/active-directory-named-locations/07.png)


6. Dans la page **Nouveau**, procédez comme suit :

    ![Panneau Nouveau](./media/active-directory-named-locations/61.png)

    a. Dans la zone de texte **Nom**, tapez un nom pour votre emplacement nommé.

    b. Dans la zone de texte **Plage d’adresses IP**, tapez une plage d’adresses IP. La plage d’adresses IP doit être au format *CIDR* (Classless Inter-Domain Routing).  

    c. Cliquez sur **Créer**.



## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- [Conditions d’emplacement dans l’accès conditionnel Azure Active Directory](active-directory-conditional-access-locations.md)

- [Événements à risque dans Azure Active Directory](active-directory-reporting-risk-events.md).

- [Rapports sur les connexions risquées dans le portail Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md).  
