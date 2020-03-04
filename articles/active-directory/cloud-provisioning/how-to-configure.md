---
title: Nouvelle configuration de l’agent de provisionnement cloud Azure AD Connect
description: Cet article explique comment installer le provisionnement cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620967"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Créer une configuration pour le provisionnement cloud Azure AD Connect

Une fois l’agent installé, vous devez vous connecter au portail Azure et configurer le provisionnement cloud Azure Active Directory (Azure AD) Connect. Effectuez les étapes suivantes pour activer l’agent.

## <a name="configure-provisioning"></a>Configurer le provisionnement
Pour configurer le provisionnement, effectuez les étapes suivantes.

1.  Dans le portail Azure, sélectionnez **Azure Active Directory**.
1.  Sélectionnez ensuite **Azure AD Connect**.
1.  Sélectionnez **Gérer le provisionnement (préversion)** .

    ![Gérer le provisionnement (préversion)](media/how-to-configure/manage1.png)

1.  Sélectionnez **Nouvelle configuration**.
1.  Dans l’écran de configuration, le domaine local est prérempli.
1.  Entrez une adresse **E-mail de notification**. Une notification est envoyée à cette adresse e-mail si le provisionnement n’est pas sain.
1.  Déplacez le sélecteur sur **Activer**, puis sélectionnez **Enregistrer**.

    ![Provisionnement Azure AD (préversion)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Limiter l’étendue du provisionnement à certains utilisateurs ou groupes
Vous pouvez limiter l’étendue de l’agent afin de synchroniser des utilisateurs ou des groupes à l’aide d’unités d’organisation ou de groupes Active Directory locaux. Vous ne pouvez pas configurer des groupes et des unités d’organisation au sein d’une configuration. 

1.  Dans le portail Azure, sélectionnez **Azure Active Directory**.
1.  Sélectionnez ensuite **Azure AD Connect**.
1.  Sélectionnez **Gérer le provisionnement (préversion)** .
1.  Sous **Configuration**, sélectionnez votre configuration.

    ![Section de configuration](media/how-to-configure/scope1.png)

1.  Sous **Configurer**, sélectionnez **Tous les utilisateurs** pour modifier l’étendue de la règle de configuration.

    ![Option Tous les utilisateurs](media/how-to-configure/scope2.png)

1. Sur la droite, vous pouvez modifier l’étendue pour n’y inclure que des groupes de sécurité. Entrez le nom unique du groupe, puis sélectionnez **Ajouter**.

    ![Option Groupes de sécurité sélectionnés](media/how-to-configure/scope3.png)

1.  Vous pouvez également modifier l’étendue pour n’y inclure que certaines unités d’organisation. Sélectionnez **Terminé**, puis **Enregistrer**.  
2.  Une fois que vous avez changé l’étendue, vous devez [redémarrer le provisionnement](#restart-provisioning) pour lancer une synchronisation immédiate des changements.

    ![Option Unités d’organisation sélectionnées](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Redémarrer le provisionnement 
Si vous ne souhaitez pas attendre la prochaine exécution planifiée, déclenchez l’exécution du provisionnement à l’aide du bouton **Redémarrer le provisionnement**. 
1.  Dans le portail Azure, sélectionnez **Azure Active Directory**.
1.  Sélectionnez ensuite **Azure AD Connect**.
1.  Sélectionnez **Gérer le provisionnement (préversion)** .
1.  Sous **Configuration**, sélectionnez votre configuration.

    ![Sélection de la configuration pour redémarrer le provisionnement](media/how-to-configure/scope1.png)

1.  En haut de la page, sélectionnez **Redémarrer le provisionnement**.

## <a name="remove-a-configuration"></a>Supprimer une configuration
Pour supprimer une configuration, effectuez les étapes suivantes.

1.  Dans le portail Azure, sélectionnez **Azure Active Directory**.
1.  Sélectionnez ensuite **Azure AD Connect**.
1.  Sélectionnez **Gérer le provisionnement (préversion)** .
1.  Sous **Configuration**, sélectionnez votre configuration.

    ![Sélection de la configuration pour supprimer la configuration](media/how-to-configure/scope1.png)

1.  En haut de l’écran de configuration, sélectionnez **Supprimer**.

    ![Bouton Supprimer](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Lorsque vous supprimez une configuration, aucune demande de confirmation ne s’affiche. Vous devez donc être sûr de vous avant de sélectionner **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Présentation du provisionnement cloud Azure AD Connect](what-is-cloud-provisioning.md)
