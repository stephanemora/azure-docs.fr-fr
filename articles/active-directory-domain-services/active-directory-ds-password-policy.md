---
title: 'Azure Active Directory Domain Services : Stratégie de mot de passe | Microsoft Docs'
description: Comprendre les stratégies de mot de passe sur les domaines managés
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: ergreenl
ms.openlocfilehash: 513cccb8b83eb4a69df1bc6172f1f02485215e35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60417003"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Stratégies de mot de passe et de verrouillage de compte sur les domaines managés
Cet article explique les stratégies de mot de passe par défaut sur un domaine managé. Il décrit également comment configurer ces stratégies.

## <a name="fine-grained-password-policies-fgpp"></a>Stratégies de mot de passe affinée (SMPA)
Utilisez des stratégies de mot de passe affinées pour spécifier plusieurs stratégies de mot de passe dans un seul domaine. Les SMPA vous permettent d’appliquer différentes restrictions des stratégies de mot de passe et de verrouillage de compte à différents groupes d’utilisateurs dans un domaine. Par exemple, vous pouvez appliquer des paramètres de mot de passe stricts aux comptes privilégiés.

Vous pouvez configurer les paramètres de mot de passe suivants à l’aide des SMPA :
* Longueur minimale du mot de passe
* Historique du mot de passe
* Les mots de passe doivent répondre aux exigences de complexité :
* Âge minimum du mot de passe
* Âge maximum du mot de passe
* Stratégie de verrouillage de compte
    * Durée de verrouillage de compte
    * Nombre d’échecs d’ouverture de session autorisés
    * Réinitialisation le nombre d’échecs d’ouverture de session après


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Paramètres par défaut de la stratégie de mot de passe affinée sur un domaine managé
La capture d’écran suivante illustre la stratégie de mot de passe affinée par défaut configurée sur un domaine Azure AD Domain Services managé.

![Stratégie de mot de passe affinée par défaut](./media/how-to/default-fgpp.png)

> [!NOTE]
> Vous ne pouvez ni modifier ni supprimer la stratégie de mot de passe affinée prédéfinie par défaut. Les membres du groupe « AAD DC Administrators » peuvent créer une SMPA personnalisée et la configurer pour qu’elle remplace la SMPA prédéfinie par défaut.
>
>

## <a name="password-policy-settings"></a>Paramètres de la stratégie de mot de passe
Sur un domaine managé, les stratégies de mot de passe suivantes sont configurées par défaut :
* Longueur minimale du mot de passe (caractères) : 7
* Âge minimum du mot de passe (durée de vie) : 90 jours
* Les mots de passe doivent répondre aux exigences de complexité :

### <a name="account-lockout-settings"></a>Paramètres de verrouillage de compte
Sur un domaine managé, les stratégies de verrouillage de compte suivantes sont configurées par défaut :
* Durée de verrouillage de compte : 30
* Nombre d’échecs d’ouverture de session autorisés : 5.
* Réinitialisation le nombre d’échecs d’ouverture de session après : 30 minutes

Effectivement, les comptes d’utilisateur sont verrouillés pendant 30 minutes si cinq mots de passe incorrects sont utilisés en l’espace de 2 minutes. Les comptes sont déverrouillés automatiquement après 30 minutes.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Créer une stratégie de mot de passe affichée (SMPA) personnalisée sur un domaine managé
Vous pouvez créer une SMPA personnalisée et l’appliquer à des groupes spécifiques dans votre domaine managé. Cette configuration remplace effectivement la SMPA par défaut configurée pour le domaine managé.

> [!TIP]
> Seuls les membres du groupe **AAD DC Administrators** dispose des autorisations pour créer des stratégies de mot de passe affinées personnalisées.
>
>

Vous pouvez également créer des stratégies de mot de passe affinées personnalisées et les appliquer aux unités d’organisation personnalisées que vous créez dans le domaine managé.

Vous pouvez configurer une SMPA personnalisée pour les raisons suivantes :
* Pour définir une stratégie de verrouillage de compte différente.
* Pour configurer un paramètre de durée de vie de mot de passe par défaut sur le domaine managé.

Pour créer une SMPA personnalisée sur votre domaine géré :
1. Connectez-vous à la machine virtuelle Windows que vous utilisez pour administrer votre domaine managé. Si vous n’en avez pas, suivez les instructions pour [administrer un domaine managé](active-directory-ds-admin-guide-administer-domain.md).
2. Lancez le **Centre d’administration Active Directory** sur la machine virtuelle.
3. Cliquez sur le nom de domaine (par exemple, « contoso100.com »).
4. Double-cliquez sur **Système** pour ouvrir le conteneur Système.
5. Double-cliquez sur **Password Settings Container (Conteneur des paramètres système)**.
6. La SMPA prédéfinie par défaut du domaine managé appelé **AADDSSTFPSO** s’affiche. Vous ne pouvez pas modifier cette SMPA prédéfinie. Mais vous pouvez créer une SMPA personnalisée qui remplace la SMPA par défaut.
7. Dans le panneau **Tâches** à droite, cliquez sur **Nouveau** et cliquez sur **Password Settings (Paramètres de mot de passe)**.
8. Dans la boîte de dialogue **Create Password Settings (Créer des paramètres de mot de passe)**, spécifiez les paramètres de mot de passe personnalisés à appliquer dans le cadre de la SMPA personnalisée. N’oubliez pas de définir la priorité de manière appropriée pour remplacer la SMPA par défaut.

   ![Créer une SMPA personnalisée](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **Pensez à décocher la case Protect from accidental deletion (Protéger contre la suppression accidentelle).** Si cette option est sélectionnée, la SMPA ne peut pas être enregistrée.
   >
   >

9. Dans **Directly Applies To (S’applique directement à)**, cliquez sur le bouton **Ajouter**. Dans la boîte de dialogue **Select Users or Groups (Sélectionner des utilisateurs ou des groupes)**, cliquez sur le bouton **Emplacements**.

   ![Sélection de l’option Utilisateurs et groupes](./media/how-to/fgpp-applies-to.png)

10. Dans la boîte de dialogue **Emplacements**, développez le nom de domaine et cliquez sur **AADDC Users (Utilisateurs AADDC)**. Vous pouvez maintenant sélectionner un groupe dans l’unité d’organisation prédéfinie des utilisateurs, auquel appliquer la SMPA.

    ![Sélectionner l’unité d’organisation à laquelle ce groupe appartient](./media/how-to/fgpp-container.png)

11. Tapez le nom du groupe et cliquez sur le bouton **Vérifier les noms** pour vérifier que le groupe existe.

    ![Sélectionner le groupe auquel appliquer la SMPA](./media/how-to/fgpp-apply-group.png)

12. Le nom du groupe s’affiche dans la section **Directly Applies To (S’applique directement à)**. Cliquez sur le bouton **OK** pour enregistrer ces modifications.

    ![SMPA appliquée](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Pour appliquer des stratégies de mot de passe personnalisées pour des comptes utilisateur dans une unité organisationnelle personnalisée :** Des stratégies de mot de passe affinées peuvent être appliquées uniquement aux groupes. Pour ne configurer une stratégie de mot de passe personnalisée que pour les utilisateurs d’une unité d’organisation personnalisée, créez un groupe incluant les utilisateurs de cette unité d’organisation.
>
>

## <a name="next-steps"></a>Étapes suivantes
* [Learn about Active Directory fine grained password policies (En savoir plus sur les stratégies de mot de passe affinées Active Directory)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configurer des stratégies de mot de passe affinées à l’aide du Centre d’administration Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
