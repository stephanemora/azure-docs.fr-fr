---
title: Accès conditionnel - Informations de sécurité combinées - Azure Active Directory
description: Créer une stratégie d’accès conditionnel personnalisée pour exiger un emplacement approuvé pour l’inscription des informations de sécurité
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
ms.openlocfilehash: 08c1db1a8e377edd1bf9b8f1b942e65b970edf56
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803676"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>Accès conditionnel : Exiger un emplacement approuvé pour l’inscription MFA

La sécurisation de l’inscription des utilisateurs pour l’authentification multifacteur et la réinitialisation de mot de passe en libre-service est désormais possible avec les actions de l’utilisateur dans la stratégie d’accès conditionnel. Cette fonctionnalité en préversion est à la disposition des organisations qui ont activé l’[inscription combinée](../authentication/concept-registration-mfa-sspr-combined.md). Cette fonctionnalité peut être activée dans les organisations qui souhaitent que les utilisateurs s’inscrivent à l’authentification multifacteur et à la réinitialisation de mot de passe en libre-service depuis un emplacement central, tel qu’un emplacement réseau approuvé pendant l’intégration de ressources humaines. Pour plus d’informations sur la création d’emplacements approuvés dans l’accès conditionnel, consultez l’article [Qu’est-ce que la condition d’emplacement de l’accès conditionnel Azure Active Directory ?](../conditional-access/location-condition.md#named-locations)

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Créer une stratégie pour exiger l’inscription à partir d’un emplacement approuvé

La stratégie suivante s’applique à tous les utilisateurs sélectionnés, qui tentent de s’inscrire à l’aide de l’expérience d’inscription combinée et bloque l’accès, sauf si ces derniers se connectent à partir d’un emplacement réseau approuvé.

1. Dans le portail **Azure**, accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel**.
1. Sélectionnez **Nouvelle stratégie**.
1. Sous Nom, entrez un nom pour cette stratégie. Par exemple, **Inscription d’informations de sécurité combinée sur les réseaux approuvés**.
1. Sous **Affectations**, cliquez sur **Utilisateurs et groupes**, puis sélectionnez les utilisateurs et les groupes auxquels vous souhaitez appliquer cette stratégie.

   > [!WARNING]
   > Les utilisateurs doivent être activés pour l’[inscription combinée en préversion](../authentication/howto-registration-mfa-sspr-combined.md).

1. Sous **Applications cloud ou actions**, sélectionnez **Actions utilisateur**, cochez **Inscrire des informations de sécurité (préversion)** .
1. Sous **Conditions** > **Emplacements**.
   1. Configurez **Oui**.
   1. Incluez **N’importe quel emplacement**.
   1. Excluez **Tous les emplacements approuvés**.
   1. Cliquez sur **Terminé** dans le panneau Emplacements.
   1. Cliquez sur **Terminé** dans le panneau des conditions.
1. Sous **Contrôles d’accès** > **Octroyer**.
   1. Cliquez sur **Bloquer l’accès**.
   1. Puis cliquez sur **Sélectionner**.
1. Définissez l’option **Appliquer la stratégie** sur **Activé**.
1. Cliquez ensuite sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

[Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel](troubleshoot-conditional-access-what-if.md)
