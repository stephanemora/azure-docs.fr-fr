---
title: Encourager les utilisateurs à installer l’application Microsoft Authenticator (préversion) - Azure Active Directory
description: Découvrez comment encourager votre organisation à adopter l’application Microsoft Authenticator à la place de méthodes d’authentification moins sûres.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/01/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d8599554918af41ead7c862ee2c84f2afd4b18a
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110786236"
---
# <a name="how-to-nudge-users-to-set-up-microsoft-authenticator-preview---microsoft-authenticator-app"></a>Encourager les utilisateurs à installer Microsoft Authenticator (préversion) - Application Microsoft Authenticator

Vous pouvez encourager les utilisateurs à installer Microsoft Authenticator lorsqu’ils se connectent. Les utilisateurs effectuent les étapes habituelles de connexion et d’authentification multifacteur, après quoi ils sont invités à installer Microsoft Authenticator. Vous pouvez inclure ou exclure des utilisateurs ou groupes pour contrôler qui peut recevoir une invitation à installer l’application. Cela vous permet de mener des campagnes ciblées en vue de faire passer les utilisateurs de méthodes d’authentification moins sécurisées à Microsoft Authenticator.  

En plus de cibler les personnes éligibles, vous pouvez définir le nombre de jours pendant lesquels un utilisateur a la possibilité de reporter, ou « répéter », la notification d’encouragement. Si un utilisateur clique sur **Plus tard** pour répéter l’invite les encourageant à installer l’application, il sera de nouveau encouragé à le faire à sa prochaine tentative d’authentification multifacteur (MFA) au terme de la durée de répétition. 

## <a name="prerequisites"></a>Prérequis 

- Votre organisation doit avoir activé Azure MFA. 
- L’utilisateur ne doit pas avoir déjà installé Microsoft Authenticator pour recevoir les notifications Push sur son compte. 
- Les administrateurs doivent autoriser les utilisateurs à installer Microsoft Authenticator au moyen de l’une des stratégies suivantes :  
  - Stratégie d’inscription MFA : la **notification via une application mobile** doit être activée pour les utilisateurs concernés.  
  - Stratégie de méthodes d’authentification : les utilisateurs doivent être autorisés à installer Microsoft Authenticator, et le mode d’authentification doit être défini sur **Quelconque** ou **Push**. Si la stratégie est définie sur **Sans mot de passe**, l’utilisateur ne recevra pas de notification d’encouragement. 

## <a name="user-experience"></a>Expérience utilisateur

1. L’utilisateur réussit l’authentification MFA avec Azure MFA. 

1. L’utilisateur voit une invite l’encourageant à installer l’application Microsoft Authenticator pour améliorer son expérience de connexion. Remarque : l’invite est envoyée uniquement aux utilisateurs pour lesquels les notifications Push Microsoft Authenticator sont activées et qui n’ont pas déjà fait l’installation. 

   ![L’utilisateur effectue une authentification multifacteur](./media/how-to-nudge-authenticator-app/user-mfa.png)

1. L’utilisateur appuie sur **Suivant** et effectue les différentes étapes d’installation de Microsoft Authenticator. 
   1. Commencer par télécharger l'application.  

      ![L’utilisateur télécharge Microsoft Authenticator](./media/how-to-nudge-authenticator-app/download.png)

   1. Lire les instructions d’installation de Microsoft Authenticator. 
   
      ![L’utilisateur installe Microsoft Authenticator](./media/how-to-nudge-authenticator-app/setup.png)

   1. Numériser le code QR. 

      ![L’utilisateur numérise le code QR](./media/how-to-nudge-authenticator-app/scan.png)

   1. Approuver la notification de test.

      ![L’utilisateur approuve la notification de test](./media/how-to-nudge-authenticator-app/test.png)

   1. Notification approuvée.

      ![Confirmation de l’approbation](./media/how-to-nudge-authenticator-app/approved.png)

   1. L’application Authenticator est maintenant installée en tant que méthode de connexion par défaut de l’utilisateur.

      ![Installation terminée](./media/how-to-nudge-authenticator-app/finish.png)

1. Si un utilisateur ne souhaite pas installer l’application Authenticator, il peut cliquer sur **Plus tard** pour répéter la notification pendant un certain nombre de jours (cette durée est configurable par un administrateur). 
 
   ![Répéter la notification d’installation](./media/how-to-nudge-authenticator-app/snooze.png)

## <a name="enable-the-nudge-policy"></a>Activer la stratégie d’encouragement

Pour activer la fonctionnalité d’encouragement, vous devez appliquer la stratégie des méthodes d’authentification à l’aide des API Graph ou des commandes PowerShell. Les **administrateurs généraux** et les **administrateurs de stratégie de méthode d’authentification** peuvent modifier la stratégie. 

Pour configurer la stratégie à l’aide de l’Afficheur Graph :

1. Connectez-vous à l’Afficheur Graph et vérifiez que vous avez accepté les autorisations **Policy.Read.All** et **Policy.ReadWrite.AuthenticationMethod**.

   Pour ouvrir le panneau Autorisations :

   ![Capture d’écran de l’Afficheur Graph](./media/how-to-nudge-authenticator-app/permissions.png)

1. Récupérez la stratégie des méthodes d’authentification : `GET https://graph.microsoft.com/beta/policies/authenticationmethodspolicy`

1. Mettez à jour la section registrationEnforcement et authenticationMethodsRegistrationCampaign de la stratégie pour activer la fonctionnalité d’encouragement sur un utilisateur ou un groupe.

   ![Section Campagne](./media/how-to-nudge-authenticator-app/campaign.png)

Pour modifier la stratégie, effectuez un PATCH sur la stratégie des méthodes d’authentification avec uniquement la section registrationEnforcement modifiée : `PATCH https://graph.microsoft.com/beta/policies/authenticationmethodspolicy`

Le tableau suivant liste les propriétés de la section **authenticationMethodsRegistrationCampaign**.

| Nom | Valeurs possibles | Description |
|------|-----------------|-------------|
| state |   "enabled" (activé)<br>"disabled" (désactivé)<br>« valeur par défaut » | Vous permet d’activer ou de désactiver la fonctionnalité.<br>La valeur par défaut est utilisée si la configuration n’a pas été explicitement définie ; la valeur par défaut Azure AD est utilisée pour ce paramètre. Correspond actuellement à "disabled".<br>Change les états à "enabled" ou "disabled" selon le cas.  |
| snoozeDurationInDays | Plage : 0 – 14 | Définit le nombre de jours après lequel l’utilisateur recevra une nouvelle notification d’encouragement.<br>Si la valeur est 0, l’utilisateur est encouragé à chacune de ses tentatives d’authentification MFA.<br>Par défaut : 1 jour |
| includeTargets | N/A | Vous permet d’inclure les différents utilisateurs et groupes pour lesquels vous souhaitez activer la fonctionnalité. |
| excludeTargets | N/A | Vous permet d’exclure les différents utilisateurs et groupes pour lesquels vous ne souhaitez pas activer la fonctionnalité. Si un utilisateur est membre à la fois d’un groupe qui est exclu et d’un groupe qui est inclus, il est exclu de la fonctionnalité.|

Le tableau suivant liste les propriétés **includeTargets**.

| Nom | Valeurs possibles | Description |
|------|-----------------|-------------|
| targetType| "user" (utilisateur)<br>"groupe" | Type d’entité ciblée. |
| Id | Identificateur GUID | ID de l’utilisateur ou du groupe ciblé. |
| targetedAuthenticationMethod | "microsoftAuthenticator" | L’utilisateur de la méthode d’authentification est invité à s’inscrire. La seule valeur autorisée est "microsoftAuthenticator". |

Le tableau suivant liste les propriétés **excludeTargets**.

| Nom       | Valeurs possibles   | Description                           |
|------------|-------------------|---------------------------------------|
| targetType | "user" (utilisateur)<br>"groupe" | Type d’entité ciblée.          |
| Id         | Chaîne          | ID de l’utilisateur ou du groupe ciblé. |

### <a name="examples"></a>Exemples

Voici quelques exemples JSON utiles pour commencer. 

- inclure tous les utilisateurs 
  
  Si vous souhaitez inclure TOUS les utilisateurs de votre locataire, [téléchargez ce fichier JSON](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/All%20Users%20Enabled.json), collez-le dans l’Afficheur Graph, puis exécutez `PATCH` sur le point de terminaison. 

  ```json
  {
  "registrationEnforcement": {
          "authenticationMethodsRegistrationCampaign": {
              "snoozeDurationInDays": 0,
              "state": "enabled",
              "excludeTargets": [],
              "includeTargets": [
                  {
                      "id": "all_users",
                      "targetType": "group",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  }
              ]
          }
      }
  }
  ```

- Inclure des utilisateurs ou groupes d’utilisateurs spécifiques

  Si vous souhaitez inclure certains utilisateurs ou groupes de votre locataire, [téléchargez ce fichier JSON](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/Multiple%20Includes.json) et modifiez-le pour y ajouter les GUID associés aux utilisateurs et groupes ciblés. Collez ensuite le code JSON dans l’Afficheur Graph, puis exécutez `PATCH` sur le point de terminaison. 

  ```json
  {
  "registrationEnforcement": {
        "authenticationMethodsRegistrationCampaign": {
            "snoozeDurationInDays": 0,
            "state": "enabled",
            "excludeTargets": [],
            "includeTargets": [
                {
                    "id": "*********PLEASE ENTER GUID***********",
                    "targetType": "group",
                    "targetedAuthenticationMethod": "microsoftAuthenticator"
                },
        {
                    "id": "*********PLEASE ENTER GUID***********",
                    "targetType": "user",
                    "targetedAuthenticationMethod": "microsoftAuthenticator"
                }
            ]
        }
    }
  ```

- Inclure et exclure des utilisateurs/groupes d’utilisateurs spécifiques

  Si vous souhaitez inclure ET exclure certains utilisateurs/groupes d’utilisateurs de votre locataire, [téléchargez ce fichier JSON](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/Multiple%20Includes%20and%20Excludes.json), collez-le dans l’Afficheur Graph, puis exécutez `PATCH` sur le point de terminaison. Entrez les GUID associés aux utilisateurs et groupes ciblés.

  ```json
  {
  "registrationEnforcement": {
          "authenticationMethodsRegistrationCampaign": {
              "snoozeDurationInDays": 0,
              "state": "enabled",
              "excludeTargets": [
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "group"
                  },
        {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "user"
                  }
              ],
              "includeTargets": [
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "group",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  },
        {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "user",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  }
              ]
          }
      }
  }
  ```

### <a name="identify-the-guids-of-users-to-insert-in-the-jsons"></a>Identifier les GUID des utilisateurs à ajouter dans le code JSON

1. Accédez au portail Azure.
1. Appuyez sur **Azure Active Directory**.
1. Dans le panneau **Gérer**, appuyez sur **Utilisateurs**.
1. Dans la page **Utilisateurs**, identifiez l’utilisateur spécifique que vous souhaitez cibler.
1. Lorsque vous appuyez sur l’utilisateur à cibler, vous voyez son **ID d’objet**, qui est le GUID de l’utilisateur.

   ![ID de l’objet utilisateur](./media/how-to-nudge-authenticator-app/object-id.png)

### <a name="identify-the-guids-of-groups-to-insert-in-the-jsons"></a>Identifier les GUID des groupes à ajouter dans le code JSON

1. Accédez au portail Azure.
1. Appuyez sur **Azure Active Directory**.
1. Dans le panneau **Gérer**, appuyez sur **Groupes**.
1. Dans la page **Groupes**, identifiez le groupe spécifique que vous souhaitez cibler.
1. Appuyez sur le groupe et récupérez l’**ID d’objet** associé.

   ![Encourager un groupe](./media/how-to-nudge-authenticator-app/group.png)

<!---comment out PS until ready>

### PowerShell

1. Install the module.
1. Ensure you pass the right roles:
   
   ```powershell
   Connect-MgGraph -Scopes "User.Read.All","Group.ReadWrite.All"
   ```

1. Select the beta profile.
1. Call `Update-MgPolicyAuthenticationMethod`.

<---->

## <a name="limitations"></a>Limites

La notification d’encouragement ne s’affiche pas sur les appareils mobiles Android ou iOS.

## <a name="frequently-asked-questions"></a>Forum aux questions

**Cette fonctionnalité sera-t-elle disponible pour le serveur MFA ?** Non. Cette fonctionnalité sera disponible uniquement pour les utilisateurs d’Azure MFA. 

**Pendant combien de temps la campagne restera-t-elle activée ?** Vous pouvez utiliser les API pour activer la campagne aussi longtemps que vous le souhaitez. Quand vous souhaitez arrêter la campagne, désactivez-la simplement à l’aide des API.  
 
**La durée de répétition définie peut-elle être différente pour chaque groupe d’utilisateurs ?** Non. La durée de répétition de l’invite est un paramètre au niveau du locataire qui s’applique à tous les groupes de l’étendue. 

**Les utilisateurs peuvent-ils être encouragés à installer une connexion par téléphone sans mot de passe ?** Cette fonctionnalité vise à permettre aux administrateurs d’amener les utilisateurs à installer MFA avec l’application Authenticator et non la connexion par téléphone sans mot de passe.  

**Un utilisateur ayant déjà installé une application d’authentification tierce voit-il la notification d’encouragement ?** Si cet utilisateur n’a pas l’application Microsoft Authenticator activée pour les notifications Push mais que celles-ci sont activées par stratégie, il verra la notification d’encouragement. 

**Un utilisateur ayant une application Microsoft Authenticator activée uniquement pour les codes TOTP voit-il la notification d’encouragement ?** Oui. Si cet utilisateur n’a pas l’application Microsoft Authenticator activée pour les notifications Push mais que celles-ci sont activées par stratégie, il verra la notification d’encouragement.

**Si un utilisateur vient de terminer son inscription MFA, verra-t-il la notification d’encouragement dans la même session de connexion ?** Non. Pour offrir une expérience utilisateur optimale, les utilisateurs ne recevront pas d’encouragement à installer Authenticator durant la même session que celle où ils ont inscrit d’autres méthodes d’authentification.  

**Puis-je encourager des utilisateurs à inscrire une autre méthode d’authentification ?** Non. La fonctionnalité actuelle vise à encourager les utilisateurs à installer l’application Microsoft Authenticator uniquement. 

**Y a-t-il un moyen de masquer l’option de répétition et de forcer les utilisateurs à installer l’application Authenticator ?**  
Il n’existe aucun moyen de masquer l’option de répétition de la fonctionnalité. Vous pouvez définir la propriété snoozeDuration à la valeur 0 pour vous assurer que les utilisateurs verront la notification d’encouragement à chacune de leurs tentatives d’authentification MFA.  

**Pourrai-je encourager les utilisateurs si je n’utilise pas Azure MFA ?** Non. La fonctionnalité d’encouragement peut être activée uniquement pour les utilisateurs qui effectuent l’authentification MFA à l’aide du service Azure MFA. 

**Les utilisateurs invités/B2B de mon locataire seront-ils encouragés ?** Oui. Ils recevront une notification d’encouragement s’ils ont été inclus à l’aide de la stratégie. 

**Que se passe-t-il si l’utilisateur ferme le navigateur ?** Cette action a le même effet que la répétition.


## <a name="next-steps"></a>Étapes suivantes

[Activer la connexion sans mot de passe avec l’application Microsoft Authenticator](howto-authentication-passwordless-phone.md)