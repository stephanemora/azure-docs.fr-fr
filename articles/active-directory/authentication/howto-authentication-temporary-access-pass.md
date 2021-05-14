---
title: Configurer un passe d’accès temporaire dans Azure AD pour inscrire des méthodes d’authentification sans mot de passe
description: Découvrez comment configurer des utilisateurs pour leur permettre d’inscrire des méthodes d’authentification sans mot de passe à l’aide d’un passe d’accès temporaire
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/26/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f0473a266cffb083a82ffc9afb333da7eecf59c
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108015696"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Configurer un passe d’accès temporaire dans Azure AD pour inscrire des méthodes d’authentification sans mot de passe (préversion)

Les méthodes d’authentification sans mot de passe, telles que FIDO2 et la connexion par téléphone sans mot de passe par le biais de l’application Microsoft Authenticator, permettent aux utilisateurs de se connecter en toute sécurité sans mot de passe. Les utilisateurs peuvent amorcer des méthodes sans mot de passe de l’une des deux manières suivantes :

- Utilisation des méthodes Azure AD Multi-Factor Authentication existantes 
- Utilisation d’un passe d’accès temporaire 

Un passe d’accès temporaire est un code secret limité dans le temps émis par un administrateur, qui répond à des exigences d’authentification fortes et qui peut être utilisé pour intégrer d’autres méthodes d’authentification, y compris sans mot de passe. Un passe d’accès temporaire facilite également la récupération quand un utilisateur perd ou oublie son facteur d’authentification fort, comme une clé de sécurité FIDO2 ou l’application Microsoft Authenticator, mais doit se connecter pour inscrire de nouvelles méthodes d’authentification fortes.


Cet article explique comment activer et utiliser un passe d’accès temporaire dans Azure AD à l’aide du portail Azure. Vous pouvez également effectuer ces actions à l’aide des API REST. 

>[!NOTE]
>Le passe d’accès temporaire est actuellement disponible en préversion publique. Certaines fonctionnalités peuvent ne pas être prises en charge ou avoir des capacités limitées. 

## <a name="enable-the-temporary-access-pass-policy"></a>Activer la stratégie de passe d’accès temporaire

Une stratégie de passe d’accès temporaire définit des paramètres, tels que la durée de vie des passes créés dans le locataire, ou les utilisateurs et les groupes qui peuvent utiliser un passe d’accès temporaire pour se connecter. Pour que quiconque puisse se connecter avec un passe d’accès temporaire, vous devez activer la stratégie de méthode d’authentification et choisir les utilisateurs et les groupes qui peuvent se connecter à l’aide d’un passe d’accès temporaire.
Bien que vous puissiez créer un passe d’accès temporaire pour n’importe quel utilisateur, seuls ceux inclus dans la stratégie peuvent l’utiliser pour se connecter.

Les détenteurs des rôles Administrateur général et Administrateur de la stratégie de méthode d’authentification peuvent mettre à jour la stratégie de méthode d’authentification par passe d’accès temporaire.
Pour configurer la stratégie de méthode d’authentification par passe d’accès temporaire :

1. Connectez-vous au portail Azure en tant qu’administrateur général, puis cliquez sur **Azure Active Directory** > **Sécurité** > **Méthodes d’authentification** > **Passe d’accès temporaire**.
1. Cliquez sur **Oui** pour activer la stratégie, sélectionnez les utilisateurs auxquels la stratégie est appliquée, ainsi que les éventuels paramètres **généraux**.

   ![Capture d’écran de la procédure d’activation de la stratégie de méthode d’authentification par passe d’accès temporaire](./media/how-to-authentication-temporary-access-pass/policy.png)

   La valeur par défaut et la plage de valeurs autorisées sont décrites dans le tableau suivant.


   | Paramètre | Valeurs par défaut | Valeurs autorisées | Commentaires |
   |---|---|---|---|
   | Durée de vie minimale | 1 heure | 10 à 43 200 minutes (30 jours) | Nombre minimal de minutes pendant lesquelles le passe d’accès temporaire est valide. |
   | Durée de vie maximale | 24 heures | 10 à 43 200 minutes (30 jours) | Nombre maximal de minutes pendant lesquelles le passe d’accès temporaire est valide. |
   | Durée de vie par défaut | 1 heure | 10 à 43 200 minutes (30 jours) | Les valeurs par défaut peuvent être remplacées par chaque passe, dans le cadre de la durée de vie minimale et maximale configurée par la stratégie. |
   | Utilisation unique | False | True / False | Lorsque la stratégie est définie sur false, les passes du locataire peuvent être utilisés une fois ou plusieurs fois pendant leur validité (durée de vie maximale). En appliquant une utilisation ponctuelle dans la stratégie de passe d’accès temporaire, tous les passes créés dans le locataire sont à usage unique. |
   | Longueur | 8 | 8 à 48 caractères | Définit la longueur du code secret. |

## <a name="create-a-temporary-access-pass"></a>Créer un passe d’accès temporaire

Une fois que vous avez activé une stratégie, vous pouvez créer un passe d’accès temporaire pour un utilisateur dans Azure AD. Ces rôles peuvent effectuer les actions suivantes en lien avec un passe d’accès temporaire.

- Un administrateur général peut créer, supprimer et afficher le passe d’accès temporaire de tout utilisateur (sauf lui-même).
- Un administrateur d’authentification privilégié peut créer, supprimer et afficher le passe d’accès temporaire d’un administrateur et d’un membre (sauf lui-même).
- Un administrateur d’authentification peut créer, supprimer et afficher le passe d’accès temporaire d’un membre (sauf lui-même).
- Un administrateur général peut afficher les détails du passe d’accès temporaire de l’utilisateur (sans lire le code proprement dit).

1. Connectez-vous au Portail Azure en tant qu’administrateur général, administrateur d’authentification privilégié ou administrateur d’authentification. 
1. Cliquez sur **Azure Active Directory**, accédez à Utilisateurs, sélectionnez un utilisateur, par exemple *Chris Green*, puis choisissez **Méthodes d’authentification**.
1. Si nécessaire, sélectionnez l’option **Essayer la nouvelle expérience des méthodes d’authentification des utilisateurs**.
1. Sélectionnez l’option **Ajouter des méthodes d’authentification**.
1. Sous **Choisir une méthode**, cliquez sur **Passe d’accès temporaire (préversion)** .
1. Définissez une heure ou une durée d’activation personnalisée, puis cliquez sur **Ajouter**.

   ![Capture d’écran montrant comment créer une passe d’accès temporaire](./media/how-to-authentication-temporary-access-pass/create.png)

1. Une fois le passe d’accès temporaire ajouté, ses détails sont affichés. Prenez note de la valeur réelle du passe d’accès temporaire. Vous fournissez cette valeur à l’utilisateur. Vous ne pouvez pas afficher cette valeur après avoir cliqué sur **OK**.
   
   ![Capture d’écran des détails d’un passe d’accès temporaire](./media/how-to-authentication-temporary-access-pass/details.png)

Les commandes suivantes montrent comment créer et obtenir un passe d’accès temporaire avec PowerShell :

```powershell
# Create a Temporary Access Pass for a user
$properties = @{}
$properties.isUsableOnce = $True
$properties.startDateTime = '2021-03-11 06:00:00'
$propertiesJSON = $properties | ConvertTo-Json

New-MgUserAuthenticationTemporaryAccessPassMethod -UserId user2@contoso.com -BodyParameter $propertiesJSON

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM TAPRocks!

# Get a user's Temporary Access Pass
Get-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM

```

## <a name="use-a-temporary-access-pass"></a>Utiliser un passe d’accès temporaire

L’utilisation la plus courante d’un passe d’accès temporaire consiste pour un utilisateur à inscrire les détails d’authentification lors de la première connexion, sans devoir répondre à d’autres invites de sécurité. Les méthodes d’authentification sont inscrites à l’adresse [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Les utilisateurs peuvent également mettre à jour les méthodes d’authentification existantes ici.

1. Ouvrez un navigateur web à l’adresse [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Entrez l’UPN du compte pour lequel vous avez créé le passe d’accès temporaire, par exemple *tapuser@contoso.com* .
1. Si l’utilisateur est inclus dans la stratégie de passe d’accès temporaire, il voit s’afficher un écran dans lequel entrer son passe d’accès temporaire.
1. Entrez le passe d’accès temporaire qui s’est affiché dans le portail Azure.

   ![Capture d’écran montrant comment entrer un passe d’accès temporaire](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>Pour des domaines fédérés, un passe d’accès temporaire est préférable à une fédération. Un utilisateur disposant d’un passe d’accès temporaire s’authentifie dans Azure AD et n’est pas redirigé vers le fournisseur d’identité (IdP) fédéré.

L’utilisateur est maintenant connecté et peut mettre à jour ou inscrire une méthode telle que la clé de sécurité FIDO2. Les utilisateurs qui mettent à jour leurs méthodes d’authentification en raison de la perte de leurs informations d’identification ou de leur appareil doivent veiller à supprimer les anciennes méthodes d’authentification.

Les utilisateurs peuvent également se servir de leur passe d’accès temporaire afin de s’inscrire pour une connexion par téléphone sans mot de passe directement à partir de l’application Microsoft Authenticator. Pour plus d’informations, consultez [Ajouter votre compte professionnel ou scolaire dans l’application Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md).

![Capture d’écran montrant comment entrer un passe d’accès temporaire en utilisant un compte professionnel ou scolaire](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-temporary-access-pass"></a>Supprimer un passe d’accès temporaire

Un passe d’accès temporaire expiré est inutilisable. Sous les **Méthodes d’authentification** d’un utilisateur, la colonne **Détails** indique quand le passe d’accès temporaire a expiré. Vous pouvez supprimer un passe d’accès temporaire expiré en procédant comme suit :

1. Dans le portail Azure AD, accédez à **Utilisateurs**, sélectionnez un utilisateur, par exemple *Utilisateur de passe d’accès temporaire*, puis choisissez **Méthodes d’authentification**.
1. Sur le côté droit de la méthode d’authentification **Passe d’accès temporaire (préversion)** présentée dans la liste, sélectionnez **Supprimer**.

Vous pouvez également utiliser PowerShell :

```powershell
# Remove a user's Temporary Access Pass
Remove-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com -TemporaryAccessPassAuthenticationMethodId c5dbd20a-8b8f-4791-a23f-488fcbde3b38
```

## <a name="replace-a-temporary-access-pass"></a>Remplacer un passe d’accès temporaire 

- Un utilisateur ne peut avoir qu’un seul passe d’accès temporaire. Le code secret est utilisable entre les heures de début et de fin du passe d’accès temporaire.
- Si l’utilisateur a besoin d’un nouveau passe d’accès temporaire :
  - Si le passe d’accès temporaire existant est valide, l’administrateur doit le supprimer et en créer un nouveau. La suppression d’un passe d’accès temporaire valide a pour effet de révoquer les sessions de l’utilisateur. 
  - Si le passe d’accès temporaire existant a expiré, un nouveau passe remplace le passe existant sans que cela ait pour effet de révoquer les sessions de l’utilisateur.

Pour plus d’informations sur les normes NIST pour l’intégration et la récupération, consultez la [publication spéciale 800-63 du NIST](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Limites

Gardez ces limites à l’esprit :

- Lors de l’utilisation d’un passe d’accès temporaire à usage unique pour inscrire une méthode sans mot de passe telle que FIDO2 ou une connexion par téléphone, l’utilisateur doit accomplir l’inscription dans les 10 minutes suivant la connexion avec le passe. Cette limitation ne s’applique pas à un passe d’accès temporaire utilisable plusieurs fois.
- Des utilisateurs invités ne peuvent pas se connecter avec un passe d’accès temporaire.
- Le passe d’accès temporaire est en préversion publique et n’est actuellement pas disponible dans Azure pour le gouvernement des États-Unis.
- Les utilisateurs concernés par la stratégie d’inscription Réinitialisation du mot de passe en libre-service (SSPR) *ou* par la [stratégie d’inscription de l’authentification multifacteur Identity Protection](../identity-protection/howto-identity-protection-configure-mfa-policy.md)seront tenus d’inscrire des méthodes d’authentification après s’être connectés avec un passe d’accès temporaire. Les utilisateurs concernés par ces stratégies seront redirigés vers le [mode d’interruption de l’inscription combinée](concept-registration-mfa-sspr-combined.md#combined-registration-modes). Cette expérience ne prend actuellement pas en charge l’inscription de FIDO2 et de la connexion par téléphone. 
- Un passe d’accès temporaire ne peut pas être utilisé avec l’extension Network Policy Server (NPS) et l’adaptateur Services de fédération Active Directory (AD FS), ou pendant l’expérience OOBE ( Setup/Out-of-Box-Experience) Windows et AutoPilot. 
- Lorsque l’authentification unique fluide est activée sur le locataire, les utilisateurs sont invités à entrer un mot de passe. Le lien **Utiliser plutôt un passe d’accès temporaire** sera disponible pour que l’utilisateur se connecte avec un passe d’accès temporaire.

  ![Capture d’écran de l’utilisation d’un passe d’accès temporaire à la place](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Dépannage    

- Si un passe d’accès temporaire n’est pas proposé à un utilisateur lors de la connexion, vérifiez les points suivants :
  - L’utilisateur est visé par la stratégie de méthode d’authentification par passe d’accès temporaire.
  - L’utilisateur dispose d’un passe d’accès temporaire valide et, si celui-ci est à usage unique, il n’a pas encore été utilisé.
- Si le message « **La connexion par passe d’accès temporaire a été bloquée en raison de la stratégie relative aux informations d’identification de l’utilisateur.**  » apparaît lors de la connexion à l’aide d’un passe d’accès temporaire, vérifiez les points suivants :
  - L’utilisateur dispose d’un passe d’accès temporaire à usages multiples, tandis que la stratégie de méthode d’authentification requiert un passe d’accès temporaire à usage unique.
  - Un passe d’accès temporaire à usage unique a déjà été utilisé.

## <a name="next-steps"></a>Étapes suivantes

- [Planifier un déploiement d’authentification sans mot de passe dans Azure Active Directory](howto-authentication-passwordless-deployment.md)

