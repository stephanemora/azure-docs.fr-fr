---
title: Accès conditionnel pour les utilisateurs de B2B Collaboration - Azure AD
description: Découvrez comment appliquer des stratégies d’authentification multifacteur pour les utilisateurs d’Azure Active Directory B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40be574b048575a1cf056fcdcd97bc09d4f21098
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113729611"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Accès conditionnel pour les utilisateurs de B2B Collaboration

Cet article explique comment les organisations peuvent étendre des stratégies d’accès conditionnel pour permettre à des utilisateurs invités B2B d’accéder à leurs ressources.
>[!NOTE]
>Ce flux d’authentification ou d’autorisation diffère légèrement pour les utilisateurs invités par rapport à celui des utilisateurs existants de ce fournisseur d’identité (IdP).

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Flux d’authentification pour les utilisateurs invités B2B en provenance d’un annuaire externe

Le diagramme suivant illustre le flux : ![image montrant le flux d’authentification pour les utilisateurs invités B2B provenant d’un annuaire externe](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Étape | Description |
|--------------|-----------------------|
| 1. | L’utilisateur invité B2B demande l’accès à une ressource. La ressource redirige l’utilisateur vers son locataire, un IdP approuvé.|
| 2. | Le locataire de la ressource identifie l’utilisateur comme étant externe, puis le redirige vers l’idP de cet utilisateur invité B2B. L’utilisateur procède à l’authentification principale auprès de l’IdP.
| 3. | L’IdP de l’utilisateur invité B2B émet un jeton pour l’utilisateur. L’utilisateur est redirigé vers le locataire de la ressource avec le jeton. Le locataire de la ressource valide le jeton, puis évalue l’utilisateur au regard de ses stratégies d’accès conditionnel. Par exemple, le locataire de la ressource peut demander à l’utilisateur d’effectuer une authentification multifacteur Azure Active Directory (AD).
| 4. | Une fois que toutes les stratégies d’accès conditionnel du locataire de la ressource sont satisfaites, le locataire émet son propre jeton et redirige l’utilisateur vers sa ressource.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Flux d’authentification pour les utilisateurs invités B2B dotés d’un code secret à usage unique

Le diagramme suivant illustre le flux : ![image montrant le flux d’authentification pour les utilisateurs invités B2B dotés d’un code secret à usage unique](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Étape | Description |
|--------------|-----------------------|
| 1. |L’utilisateur demande l’accès à une ressource située dans un autre locataire. La ressource redirige l’utilisateur vers son locataire, un IdP approuvé.|
| 2. | Le locataire de la ressource identifie l’utilisateur en tant qu’[utilisateur doté d’un mot de passe à usage unique par e-mail externe](./one-time-passcode.md) et envoie un e-mail contenant le mot de passe à usage unique à l’utilisateur.|
| 3. | L’utilisateur récupère le mot de passe à usage unique et soumet le code. Le locataire de la ressource évalue l’utilisateur par rapport à ses stratégies d’accès conditionnel.
| 4. | Une fois que toutes les stratégies d’accès conditionnel sont satisfaites, le locataire émet un jeton et redirige l’utilisateur vers sa ressource. |

>[!NOTE]
>Si l’utilisateur vient d’un locataire d’une ressource externe, il n’est pas possible d’évaluer également les stratégies d’accès conditionnel de l’idP de l’utilisateur invité B2B. À l’heure actuelle, seules les stratégies d’accès conditionnel du locataire de la ressource s’appliquent à ses invités.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Authentification multifacteur Azure AD pour les utilisateurs B2B

Les organisations peuvent appliquer plusieurs stratégies d’authentification multifacteur Azure AD pour leurs utilisateurs invités B2B. Ces stratégies peuvent être appliquées au niveau du locataire, de l’application ou de l’utilisateur individuel, de la même façon qu’elles peuvent être activées pour les employés à plein temps et les membres de l’organisation.
Le locataire de la ressource est toujours responsable de l’authentification multifacteur Azure AD pour les utilisateurs, même si l’organisation de l’utilisateur invité offre des fonctionnalités d’authentification multifacteur. Voici un exemple.

1. Un administrateur ou un professionnel de l’information dans une société nommée Fabrikam invite l’utilisateur d’une autre société nommée Contoso à utiliser son application Woodgrove.

2. L’application Woodgrove de Fabrikam est configurée pour exiger une authentification multifacteur Azure AD lors de l’accès.

3. Quand l’utilisateur invité B2B de Contoso essaie d’accéder à Woodgrove dans le locataire Fabrikam, il est invité à procéder à l’authentification multifacteur Azure AD.

4. L’utilisateur invité peut alors configurer son authentification multifacteur Azure AD auprès de Fabrikam et sélectionner les options.

5. Ce scénario fonctionne pour toute identité : Azure AD ou compte Microsoft personnel (MSA). Par exemple, il fonctionne si l’utilisateur de Contoso s’authentifie avec un ID social.

6. Fabrikam doit disposer de suffisamment de licences Azure AD Premium prenant en charge l’authentification multifacteur Azure AD. L’utilisateur de Contoso consomme alors cette licence de Fabrikam. Pour plus d’informations sur les licences B2B, consultez le [modèle de facturation pour les identités externes Azure AD](./external-identities-pricing.md).

>[!NOTE]
>L’authentification multifacteur Azure AD est effectuée au niveau du locataire de la ressource pour garantir la prévisibilité. Lorsque l’utilisateur invité se connecte, il voit la page de connexion du locataire de ressources affichée en arrière-plan, ainsi que sa propre page de connexion et son logo de société au premier plan.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Configurer l’authentification multifacteur Azure AD pour les utilisateurs B2B

Pour configurer l’authentification multifacteur Azure AD pour les utilisateurs B2B Collaboration, regardez cette vidéo :

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>Authentification multifacteur Azure AD des utilisateurs B2B pour l’utilisation de l’offre

Pour en savoir plus sur l’expérience d’utilisation de l’authentification multifacteur Azure AD, regardez cette vidéo :

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Réinitialisation de l’authentification multifacteur Azure AD pour les utilisateurs B2B

À présent, les applets de commande PowerShell suivantes sont disponibles pour valider des utilisateurs invités B2B :

1. Se connecter à Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Obtenir tous les utilisateurs avec des méthodes d'authentification

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Voici un exemple :

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Réinitialisez la méthode d’authentification multifacteur Azure AD pour qu’un utilisateur spécifique oblige l’utilisateur B2B Collaboration à définir de nouveau des méthodes de validation. 
   Voici un exemple :

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Accès conditionnel pour les utilisateurs B2B

Divers facteurs influencent les stratégies d’accès conditionnel pour les utilisateurs invités B2B.

### <a name="device-based-conditional-access"></a>Accès conditionnel basé sur les appareils

En matière d’accès conditionnel, il existe une option pour exiger que [l’appareil d’un utilisateur soit conforme ou joint à Azure AD Hybride](../conditional-access/concept-conditional-access-conditions.md#device-state-preview). Les utilisateurs invités B2B peuvent uniquement satisfaire à la conformité si le locataire de la ressource peut gérer leur appareil. Les appareils ne peuvent pas être gérés par plusieurs organisations à la fois. Les utilisateurs invités B2B ne peuvent pas effectuer la jonction Azure AD Hybride, car ils n’ont pas de compte AD local. 

>[!Note]
>Il n’est pas recommandé d’exiger un appareil géré pour les utilisateurs externes.

### <a name="mobile-application-management-policies"></a>Stratégies de gestion des applications mobiles

Les contrôles d’octroi d’accès conditionnel comme **Exiger des applications client approuvées** et **Exiger des stratégies de protection d’application** nécessitent que l’appareil soit inscrit dans le locataire. Ces contrôles peuvent uniquement être appliqués à des [appareils iOS et Android](../conditional-access/concept-conditional-access-conditions.md#device-platforms). En revanche, aucun de ces contrôles ne peut être appliqué à des utilisateurs invités B2B si l’appareil de l’utilisateur est déjà géré par une autre organisation. Un appareil mobile ne peut pas être inscrit dans plusieurs locataires à la fois. Si l’appareil mobile est géré par une autre organisation, l’utilisateur est bloqué. 

>[!NOTE]
>Il n’est pas recommandé d’exiger une stratégie de protection d’application pour les utilisateurs externes.

### <a name="location-based-conditional-access"></a>Accès conditionnel en fonction des emplacements

La [stratégie en fonction des emplacements](../conditional-access/concept-conditional-access-conditions.md#locations) basée sur des plages d’adresses IP peut être appliquée si l’organisation qui invite peut créer une plage d’adresses IP approuvées qui définit ses organisations partenaires.

Des stratégies peuvent également être appliquées en fonction des **localisations géographiques**.

### <a name="risk-based-conditional-access"></a>Accès conditionnel en fonction du risque

La [stratégie de connexion à risque](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) est appliquée si l’utilisateur invité B2B satisfait au contrôle d’octroi. Par exemple, une organisation peut exiger une authentification multifacteur Azure AD pour une connexion à risque moyen ou élevé. En revanche, si un utilisateur ne s’est pas préalablement inscrit à l’authentification multifacteur Azure AD auprès du locataire de la ressource, il est bloqué. Cela permet d’empêcher les utilisateurs malveillants d’inscrire leurs propres informations d’identification pour une authentification multifacteur Azure AD s’ils parviennent à compromettre le mot de passe d’un utilisateur légitime.

La [stratégie d’utilisateur à risque](../conditional-access/concept-conditional-access-conditions.md#user-risk) ne peut pas être résolue dans le locataire de la ressource. Par exemple, si vous exigez un changement de mot de passe pour les utilisateurs invités à haut risque, ceux-ci sont bloqués en raison de l’impossibilité de réinitialiser des mots de passe dans le répertoire des ressources.

### <a name="conditional-access-client-apps-condition"></a>Conditions des applications clientes d’accès conditionnel

Les [conditions des applications clientes](../conditional-access/concept-conditional-access-conditions.md#client-apps) se comportent de la même façon pour les utilisateurs invités B2B que pour tout autre type d’utilisateur. Par exemple, vous pouvez empêcher des utilisateurs invités d’utiliser des protocoles d’authentification hérités.

### <a name="conditional-access-session-controls"></a>Contrôles de session d’accès conditionnel

Les [contrôles de session](../conditional-access/concept-conditional-access-session.md) se comportent de la même façon pour les utilisateurs invités B2B que pour tout autre type d’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants sur Azure AD B2B Collaboration :

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](./what-is-b2b.md)
- [Utilisateurs Identity Protection et B2B](../identity-protection/concept-identity-protection-b2b.md)
- [Prix des identités externes](https://azure.microsoft.com/pricing/details/active-directory/external-identities/)
- [Questions fréquentes (FAQ)](./faq.yml)
