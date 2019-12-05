---
title: Accès conditionnel - Bloquer l’authentification héritée - Azure Active Directory
description: Créer une stratégie d’accès conditionnel personnalisée pour bloquer les protocoles d’authentification héritée
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b4627080879c9e7d2635b950bb7f31b7d23581
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803629"
---
# <a name="conditional-access-block-legacy-authentication"></a>Accès conditionnel : Bloquer l’authentification héritée

En raison des risques accrus associés aux protocoles d’authentification hérités, Microsoft recommande aux organisations de bloquer les demandes d’authentification utilisant ces protocoles et d’exiger une authentification moderne.

## <a name="create-a-conditional-access-policy"></a>Créer une stratégie d’accès conditionnel

Les étapes suivantes vous aideront à créer une stratégie d’accès conditionnel pour bloquer les requêtes d’authentification héritées.

1. Connectez-vous au **portail Microsoft Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs**.
   1. Sous **Exclure**, sélectionnez **Utilisateurs et groupes**, puis choisissez les comptes qui doivent conserver la possibilité d’utiliser l’authentification héritée. 
   1. Sélectionnez **Terminé**.
1. Sous **Conditions** > **Applications clientes (préversion)** , réglez **Configurer** sur **Oui**.
   1. Cochez uniquement les cases **Applications mobiles et clients de bureau** > **Autres clients**.
   2. Sélectionnez **Terminé**.
1. Sous **Contrôles d'accès** > **Accorder**, sélectionnez **Bloquer l’accès**.
   1. Sélectionnez **Sélectionner**.
1. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer votre stratégie.

## <a name="next-steps"></a>Étapes suivantes

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

[Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel](troubleshoot-conditional-access-what-if.md)
