---
title: Démarrage rapide - Bloquer l’accès lorsqu’un risque de session est détecté avec Azure Active Directory Identity Protection | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez apprendre à configurer une stratégie d’accès conditionnel de connexion à risque Azure Active Directory (Azure AD) Identity Protection afin de bloquer les connexions en fonction des risques de session.
services: active-directory
keywords: protection des identités, accès conditionnel aux applications, accès conditionnel avec Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce2dc95aae2ba4677da81ce6af45b8a715000e10
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210337"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Démarrage rapide : Bloquer l’accès lorsqu’un risque de session est détecté avec Azure Active Directory Identity Protection  

Pour protéger votre environnement, vous devrez peut-être empêcher les utilisateurs suspects de se connecter. Azure Active Directory (Azure AD) Identity Protection analyse chaque connexion et calcule la probabilité qu’une tentative de connexion n’émane pas du propriétaire légitime du compte d’utilisateur. Cette probabilité (faible, moyenne ou élevée) est indiquée sous forme de valeurs calculées appelées niveaux de risque de connexion. En définissant la condition du risque de connexion, vous pouvez configurer une stratégie d’accès conditionnel de connexion à risque afin de répondre aux niveaux spécifiques dans ce domaine. 

Ce démarrage rapide décrit comment configurer une stratégie d’accès conditionnel de connexion à risque qui bloque une connexion lorsqu’un niveau de risque de connexion de niveau intermédiaire ou plus élevé a été détecté. 

![Créer une stratégie](./media/quickstart-sign-in-risk-policy/1004.png)


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.



## <a name="prerequisites"></a>Prérequis 

Pour suivre le scénario décrit dans ce didacticiel, vous avez besoin de ce qui suit :

- **Accès à une édition Azure AD Premium P2** : Azure AD Identity Protection est une fonctionnalité d’Azure AD Premium P2. 

- **Identity Protection** : Identity Protection doit être activé pour suivre le scénario de ce démarrage rapide. Pour en savoir plus sur l’activation d’Identity Protection dans Azure AD, consultez la page [Activer Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Navigateur Tor** : le [navigateur Tor](https://www.torproject.org/projects/torbrowser.html.en) est conçu pour vous aider à préserver votre confidentialité en ligne. Identity Protection détecte les connexions à partir du navigateur Tor en tant que **connexions depuis des adresses IP anonymes**, offrant un niveau de risque moyen. Pour plus d’informations, consultez [Événements à risque dans Azure Active Directory](../reports-monitoring/concept-risk-events.md).  

- **Un compte test nommé Alain Charon** : si vous ignorez comment créer un compte test, consultez la page [Ajouter un utilisateur](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>Tester la connexion 

L’objectif de cette étape consiste à vérifier que votre compte test peut accéder à votre locataire à l’aide du navigateur Tor.

**Pour tester la connexion :**

1. Connectez-vous à votre [portail Azure](https://portal.azure.com) en tant que **Alain Charon**.

2. Déconnectez-vous. 


## <a name="create-your-conditional-access-policy"></a>Créer votre stratégie d’accès conditionnel 

Le scénario de ce démarrage rapide utilise une connexion à partir du navigateur Tor pour générer la détection d’un événement à risque (**connexion depuis une adresse IP anonyme**). Le niveau de risque de cet événement est moyen. Pour répondre à cet événement à risque, définissez le niveau de risque de connexion sur Moyen. 

Cette section montre comment créer la stratégie d’accès conditionnel de risque de connexion requise. Dans votre stratégie, définissez :

|Paramètre |Valeur|
|---     | --- |
| Utilisateurs  | Alain Charon  |
| Conditions | Risque de connexion, moyen et supérieur |
| Commandes | Bloquer l’accès |
 

![Créer une stratégie](./media/quickstart-sign-in-risk-policy/201.png)

 


**Pour configurer votre stratégie d’accès conditionnel, procédez comme suit :**

1. Connectez-vous à votre [portail Azure](https://portal.azure.com) en tant qu’administrateur général.

2. Accédez à la [page Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
 
3. Dans la page **Azure AD Identity Protection**, dans la section **Configurer**, cliquez sur **Stratégie de connexion à risque**.
 
4. Dans la page relative à la stratégie, dans la section **Affectations**, cliquez sur **Utilisateurs**.

5. Dans la page **Utilisateurs**, cliquez sur **Sélectionner les utilisateurs**.

6. Dans la page **Sélectionner les utilisateurs**, sélectionnez **Alain Charon**, puis cliquez sur **Sélectionner**.

7. Dans la page **Utilisateurs**, cliquez sur **Terminé**. 

8. Dans la page relative à la stratégie, dans la section **Affectations**, cliquez sur **Conditions**.

9. Dans la page **Conditions**, cliquez sur **Risque de connexion**.

10. Dans la page **Risque de connexion**, sélectionnez **Medium and above** (Moyen et supérieur), puis cliquez sur **Sélectionner**. 

11. Dans la page **Conditions**, cliquez sur **Terminé**.

12. Dans la page relative à la stratégie, dans la section **Contrôles**, cliquez sur **Accès**.

13. Dans la page **Accès**, cliquez sur **Autoriser l’accès**, sélectionnez **Exiger une authentification multifacteur**, puis cliquez sur **Sélectionner**.

14. Dans la page relative à la stratégie, cliquez sur **Enregistrer**.  


## <a name="test-your-conditional-access-policy"></a>Tester votre stratégie d’accès conditionnel

Pour tester votre stratégie, essayez de vous connecter à votre [portail Azure](https://portal.azure.com) en tant que **Alain Charon** à l’aide du navigateur Tor. Votre tentative de connexion doit être bloquée par votre stratégie d’accès conditionnel.

![Authentification multifacteur](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez l’utilisateur test et le navigateur Tor, puis désactivez la stratégie d’accès conditionnel de connexion à risque :

- Si vous ignorez comment supprimer un utilisateur Azure AD, voir [Comment ajouter ou supprimer des utilisateurs](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Pour savoir comment supprimer le navigateur Tor, consultez la page de [désinstallation](https://tb-manual.torproject.org/uninstalling/).


