---
title: Nouvelle configuration de l’agent de provisionnement cloud Azure AD Connect
description: Cette rubrique décrit comment installer le provisionnement cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956962503dcb3a6355d455ff96744143c5965d1e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795468"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Nouvelle configuration du provisionnement cloud Azure AD Connect

Après avoir installé l’agent, vous devez vous connecter au portail Azure et configurer le provisionnement.  Utilisez les étapes suivantes pour activer l’agent.

## <a name="configure-provisioning"></a>Configurer le provisionnement
Pour configurer le provisionnement, suivez ces étapes :

1.  Dans le portail Azure AD, cliquez sur  **Azure Active Directory**.
2.  Cliquez sur **Azure AD Connect**.
3.  Sélectionnez **Gérer le provisionnement (préversion)** 
![](media/how-to-configure/manage1.png).

4.  Cliquez sur **Nouvelle configuration**.
5.  Dans l’écran de configuration, le domaine local est pré-rempli.
6. Entrez une adresse **E-mail de notification**. Une notification est envoyée à cette adresse e-mail 
7. lorsque le provisionnement n’est pas sain.  
8. Déplacez le sélecteur sur **Activer**, puis cliquez sur **Enregistrer**.
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>Limiter l’étendue du provisionnement à des utilisateurs et des groupes spécifiques
Si vous souhaitez limiter l’étendue de l’agent à la seule synchronisation d’utilisateurs et de groupes spécifiques, vous pouvez le faire. Utilisez les unités d’organisation ou les groupes Active Directory locaux pour définir cette étendue. Vous ne pouvez pas configurer des groupes et des unités d’organisation au sein d’une configuration. 

1.  Dans le portail Azure AD, cliquez sur  **Azure Active Directory**.
2.  Cliquez sur **Azure AD Connect**.
3.  Sélectionnez **Gérer le provisionnement (préversion)** .
4.  Sous **Configuration**, cliquez sur votre configuration.  
![](media/how-to-configure/scope1.png)

5.  Sous **Configurer**, sélectionnez **Tous les utilisateurs** pour modifier l’étendue de la règle de configuration.
![](media/how-to-configure/scope2.png)

6. Sur la droite, vous pouvez modifier l’étendue pour inclure uniquement des groupes de sécurité, en entrant le nom unique du groupe et en cliquant sur **Ajouter**.
![](media/how-to-configure/scope3.png)

7. Ou changer pour n’inclure que des unités d’organisation spécifiques. Cliquez sur **Terminé**, puis sur **Enregistrer**.
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Redémarrer le provisionnement 
Si vous ne souhaitez pas attendre la prochaine exécution planifiée, vous pouvez déclencher l’exécution du provisionnement à l’aide du bouton de redémarrage du provisionnement. 
1.  Dans le portail Azure AD, cliquez sur  **Azure Active Directory**.
2.  Cliquez sur **Azure AD Connect**.
3.  Sélectionnez **Gérer le provisionnement (préversion)** .
4.  Sous **Configuration**, cliquez sur votre configuration.  
![](media/how-to-configure/scope1.png)

5.  En haut de la page, cliquez sur **Redémarrer le provisionnement**.

## <a name="removing-a-configuration"></a>Supprimer une configuration
Si vous souhaitez supprimer une configuration, vous pouvez le faire en suivant les étapes décrites ici.

1.  Dans le portail Azure AD, cliquez sur  **Azure Active Directory**.
2.  Cliquez sur **Azure AD Connect**.
3.  Sélectionnez **Gérer le provisionnement (préversion)** .
4.  Sous **Configuration**, cliquez sur votre configuration.  
![](media/how-to-configure/scope1.png)

5.  En haut de la page, cliquez sur **Supprimer**.
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Aucune confirmation ne s’affichant avant la suppression d’une configuration, assurez-vous qu’il s’agit bien de l’action que vous souhaitez effectuer avant de cliquer sur **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Présentation du provisionnement cloud Azure AD Connect](what-is-cloud-provisioning.md)
