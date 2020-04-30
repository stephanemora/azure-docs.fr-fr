---
title: Rapports d’accès et d’utilisation pour Azure MFA - Azure Active Directory
description: Cette section décrit comment utiliser la fonctionnalité de rapports d’Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df562d65ad064efb1be337e0b68cb8638536981
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112760"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapports dans Azure Multi-Factor Authentication

Azure Multi-Factor Authentication fournit plusieurs rapports qui peuvent être utilisés par vous et votre organisation, et qui sont accessibles via le portail Azure. Le tableau ci-après répertorie les rapports disponibles :

| Rapport | Emplacement | Description |
|:--- |:--- |:--- |
| Historique de l'utilisateur bloqué | Azure AD > Sécurité > MFA > Bloquer/débloquer des utilisateurs | Affiche l’historique des demandes de blocage et de déblocage d’utilisateurs. |
| Alertes relatives à l’utilisation et aux fraudes | Azure AD > Connexions | Fournit des informations sur l’utilisation globale, un récapitulatif par utilisateur, des informations détaillées sur les utilisateurs, ainsi que l’historique des alertes de fraude émises au cours de la plage de dates spécifiée. |
| Utilisation des composants locaux | Azure AD > Sécurité > MFA > Rapport d’activité | Fournit des informations sur l’utilisation globale de MFA via l’extension NPS, AD FS et le serveur MFA. |
| Historique de l'utilisateur contourné | Azure AD > Security > MFA > Contournement à usage unique | Affiche l’historique des demandes de contournement de Multi-Factor Authentication pour un utilisateur. |
| État du serveur | Azure AD > Sécurité > MFA > État du serveur | Affiche l’état des serveurs Multi-Factor Authentication associés à votre compte. |

## <a name="view-mfa-reports"></a>Afficher les rapports MFA

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. À gauche, sélectionnez **Azure Active Directory** > **Sécurité** > **MFA**.
3. Sélectionnez le rapport que vous souhaitez afficher.

   ![Rapport d’état du serveur MFA Server dans le portail Microsoft Azure](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Rapport de connexion Azure AD

Le **rapport d’activité de connexion** du [portail Azure](https://portal.azure.com) vous permet d’obtenir les informations dont vous avez besoin pour évaluer l’état de votre environnement.

Le rapport d’activité de connexion peut vous fournir des informations sur l’utilisation des applications gérées et des activités de connexion des utilisateurs, y compris des informations sur l’utilisation de l’authentification multifacteur (MFA). Les données d’authentification multifacteur vous donnent un aperçu du fonctionnement de l’authentification multifacteur dans votre organisation. Elles vous permettent de répondre aux questions telles que :

- La connexion a-t-elle été protégée avec l’authentification multifacteur ?
- Comment l’utilisateur a-t-il effectué l’authentification multifacteur ?
- Pourquoi l’utilisateur était-il incapable d’effectuer l’authentification multifacteur ?
- Combien d’utilisateurs sont-ils invités à utiliser l’authentification multifacteur ?
- Combien d’utilisateurs n’ont pas pu résoudre le défi de l’authentification multifacteur ?
- Quels sont les problèmes courants d’authentication multifacteur auxquels les utilisateurs sont confrontés ?

Ces données sont disponibles via le [portail Azure](https://portal.azure.com) et [l’API de génération de rapports](../reports-monitoring/concept-reporting-api.md).

![Rapports sur les connexions Azure AD dans le portail Microsoft Azure](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Structure du rapport de connexion

Les rapports d’activité de connexion pour l’authentification multifacteur vous permettent d’accéder aux informations suivantes :

**MFA obligatoire :** spécifie si l’authentification multifacteur est nécessaire pour la connexion ou non. MFA peut être nécessaire en raison de MFA par utilisateur, de l’accès conditionnel ou d’autres raisons. Les valeurs possibles sont **Oui** ou **Non**.

**Résultat MFA :** plus d’informations indiquant si l’authentification multifacteur a été réussie ou refusée :

- Si l’authentification multifacteur a été réussie, cette colonne fournit plus d’informations sur la façon dont l’authentification multifacteur a été satisfaite.
   - Azure Multi-Factor Authentication
      - achevée dans le cloud
      - a expiré en raison des politiques configurées pour l’abonné
      - inscription demandée
      - satisfaite par une revendication dans le jeton
      - satisfaite par une revendication fournie par un fournisseur externe
      - satisfaite par l’authentification forte
      - ignorée, car le flux testé était un flux d’ouverture de session de répartiteur de service Windows
      - ignorée en raison de mot de passe
      - ignorée en raison d’emplacement
      - ignorée en raison de l’appareil enregistré
      - ignorée en raison de l’appareil mémorisé
      - réussie
   - Redirigé vers un fournisseur externe pour l’authentification multifacteur

- Si l’authentification multifacteur a été refusée, cette colonne fournit la raison de refus.
   - Azure Multi-Factor Authentication refusée ;
      - authentification en cours
      - double tentative d’authentification
      - code erroné entré beaucoup de fois
      - authentification invalide
      - code de vérification de l’application mobile incorrect
      - configuration incorrecte
      - appel téléphonique passé à la messagerie vocale
      - numéro de téléphone de format invalide
      - erreur de service
      - téléphone de l’utilisateur injoignable
      - Impossible d’envoyer la notification d’application mobile à l’appareil
      - Impossible d’envoyer la notification d’application mobile
      - refus de l’authentification par l’utilisateur
      - l’utilisateur n’a pas répondu à la notification d’application mobile
      - l’utilisateur ne dispose pas de toutes les méthodes de vérification enregistrés
      - l’utilisateur a entré un code erroné
      - l’utilisateur a entré un code PIN erroné
      - l’utilisateur a raccroché l’appel téléphonique sans réussir l’authentification
      - l’utilisateur est bloqué
      - l’utilisateur n’a jamais entré le code de vérification
      - utilisateur non trouvé
      - code de vérification déjà utilisé une fois

**Méthode d’authentification MFA :** la méthode d’authentification que l’utilisateur a utilisé pour effectuer l’authentification multifacteur. Les valeurs possibles incluent :

- SMS
- Notification sur l’application mobile
- Appel téléphonique (téléphone d’authentification)
- Code de vérification de l’application mobile
- Appel téléphonique (téléphone du bureau)
- Appel téléphonique (téléphone d’authentification alternatif)

**Détail d’authentification MFA :** version nettoyée du numéro de téléphone, par exemple : + X XXXXXXXX64.

**Accès conditionnel :** permet de trouver des informations sur les stratégies d’accès conditionnel qui ont affecté la tentative de connexion, notamment :

- Nom de stratégie
- Contrôles d’octroi
- Contrôles de session
- Résultats

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Création de rapports PowerShell sur les utilisateurs inscrits pour MFA

Tout d’abord, assurez-vous d’avoir le [module MSOnline V1 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) installé.

Identifiez les utilisateurs qui se sont inscrits auprès de MFA à l’aide du code PowerShell qui suit. Cet ensemble de commandes exclut les utilisateurs désactivés, car ces comptes ne peuvent pas s’authentifier auprès d’Azure AD.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifiez les utilisateurs qui ne se sont pas inscrits auprès de MFA à l’aide du code PowerShell qui suit. Cet ensemble de commandes exclut les utilisateurs désactivés, car ces comptes ne peuvent pas s’authentifier auprès d’Azure AD.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifiez les méthodes de sortie et les utilisateurs inscrits. 

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Résultats possibles dans les rapports d’activité

Le tableau suivant peut être utilisé pour résoudre les problèmes de l’authentification multifacteur à l’aide de la version téléchargée du rapport d’activité de l’authentification multifacteur. Ils n’apparaîtront pas directement dans le portail Microsoft Azure.

| Résultat de l’appel | Description | Description générale |
| --- | --- | --- |
| SUCCESS_WITH_PIN | Code PIN entré | L’utilisateur a entré un code PIN.  Si l’authentification a réussi, alors le code PIN entré est correct.  Si l’authentification est refusée, cela signifie que l’utilisateur a entré un code PIN ou que l’utilisateur est défini en mode Standard. |
| SUCCESS_NO_PIN | Uniquement # entré | Si l’utilisateur est défini en mode Code PIN et que l’authentification est refusée, cela signifie que l’utilisateur n’a pas entré son code PIN et qu’il a uniquement entré #.  Si l’utilisateur est défini en mode Standard et que l’authentification réussit, cela signifie que l’utilisateur a uniquement entré #, ce qu’il faut exactement faire en mode Standard. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # non appuyé après l’entrée | L’utilisateur n’a pas envoyé de chiffres DTMF, car # n’a pas été entré.  Les autres chiffres entrés ne sont pas envoyés, sauf si # est entré, indiquant la validation de l’entrée. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Aucune entrée téléphone - expiration du délai d’attente | L’appel a été reçu, mais aucune réponse.  Cela indique généralement que l’appel a été récupéré par la messagerie vocale. |
| SUCCESS_PIN_EXPIRED | Code PIN expiré et non modifié | Le code PIN de l’utilisateur a expiré et il a été invité à le modifier, mais la modification du code PIN n’a pas totalement abouti. |
| SUCCESS_USED_CACHE | Cache utilisé | L’authentification a réussi sans appel Multi-Factor Authentication, car une authentification ayant abouti précédemment pour le même nom d’utilisateur s’est produite pendant la plage de temps du cache configurée. |
| SUCCESS_BYPASSED_AUTH | Authentification contournée | L’authentification a réussi à l’aide d’un contournement à usage unique initié pour l’utilisateur.  Consultez le rapport de l’historique de l’utilisateur contourné pour plus d’informations sur le contournement. |
| SUCCESS_USED_IP_BASED_CACHE | Cache basé sur l’adresse IP utilisée | L’authentification a réussi sans appel Multi-Factor Authentication, car une authentification ayant abouti précédemment pour le même nom d’utilisateur, type d’authentification, nom d’application et adresse IP s’est produite pendant la plage de temps du cache configurée. |
| SUCCESS_USED_APP_BASED_CACHE | Cache basé sur l’application utilisée | L’authentification a réussi sans appel Multi-Factor Authentication, car une authentification ayant abouti précédemment pour le même nom d’utilisateur, type d’authentification et nom d’application s’est produite pendant la plage de temps du cache configurée. |
| SUCCESS_INVALID_INPUT | Entrée téléphone non valide | La réponse envoyée à partir du téléphone n’est pas valide.  Cela peut provenir d’un télécopieur ou d’un modem, ou s’expliquer par le fait que l’utilisateur a entré * dans son code PIN. |
| SUCCESS_USER_BLOCKED | L’utilisateur est bloqué | Le numéro de téléphone de l’utilisateur est bloqué.  Un numéro bloqué peut être initié par l’utilisateur lors d’un appel d’authentification ou par un administrateur utilisant le portail Microsoft Azure. <br> REMARQUE :   Un numéro bloqué est également une conséquence d’une alerte de fraude. |
| SUCCESS_SMS_AUTHENTICATED | SMS authentifié | Pour un SMS bidirectionnel, l’utilisateur a répondu correctement avec son code secret à usage unique ou son code secret à usage unique + son code PIN. |
| SUCCESS_SMS_SENT | SMS envoyé | Le SMS contenant le code secret à usage unique a été correctement envoyé.  L’utilisateur entre le code secret à usage unique ou le code secret à usage unique + le code PIN dans l’application pour effectuer l’authentification. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Application mobile authentifiée | L’utilisateur est authentifié correctement via l’application mobile. |
| SUCCESS_OATH_CODE_PENDING | Code OATH en attente | L’utilisateur a été invité à entrer son code OATH, mais n’a pas répondu. |
| SUCCESS_OATH_CODE_VERIFIED | Code OATH vérifié | L’utilisateur a entré un code OATH valide lorsqu’il y a été invité. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Code OATH de secours vérifié | L’authentification de l’utilisateur a été refusée à l’aide de sa principale méthode Multi-Factor Authentication, puis un code OATH valide de secours a été fourni. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Les questions de sécurité de secours ont obtenu des réponses | L’authentification de l’utilisateur a été refusée à l’aide de sa principale méthode Multi-Factor Authentication, puis ses questions de sécurité de secours ont obtenu une réponse. |
| FAILED_PHONE_BUSY | Authentification déjà en cours | Multi-Factor Authentication traite déjà une authentification pour cet utilisateur.  Cela est souvent dû à des clients RADIUS qui envoient plusieurs demandes d’authentification lorsqu’ils sont connectés à la même session. |
| CONFIG_ISSUE | Téléphone inaccessible | Une tentative d’appel a eu lieu, mais il n’a pas pu être passé ou n’a pas reçu de réponse.  Cela inclut le signal occupé, le signal occupé rapide (déconnecté), la triple tonalité (le numéro n’est plus en service), l’expiration du délai de sonnerie, etc. |
| FAILED_INVALID_PHONENUMBER | Format de numéro de téléphone non valide | Le numéro de téléphone est dans un format non valide.  Les numéros de téléphone doivent être numériques et comporter 10 chiffres pour le code de pays + 1 (États-Unis et Canada). |
| FAILED_USER_HUNGUP_ON_US | L’utilisateur a raccroché le téléphone | L’utilisateur a répondu au téléphone, mais a raccroché sans appuyer sur un bouton. |
| FAILED_INVALID_EXTENSION | Extension non valide | L’extension contient des caractères non valides.  Seuls les chiffres, les virgules, * et # sont autorisés.  Un préfixe @ peut également être utilisé. |
| FAILED_FRAUD_CODE_ENTERED | Code fraude entré | L’utilisateur a choisi de signaler une fraude lors de l’appel, ce qui entraîne un refus d’authentification et le blocage du numéro de téléphone.| 
| FAILED_SERVER_ERROR | Impossible de passer un appel | Le service Multi-Factor Authentication n’a pas pu effectuer l’appel. |
| FAILED_SMS_NOT_SENT | Le SMS n’a pas pu être envoyé | Le SMS n’a pas pu être envoyé.  L’authentification est refusée. |
| FAILED_SMS_OTP_INCORRECT | Le code secret à usage unique du SMS est incorrect | L’utilisateur a entré un code secret à usage unique incorrect à partir du message texte qu’il a reçu.  L’authentification est refusée. |
| FAILED_SMS_OTP_PIN_INCORRECT | Le code secret à usage unique du SMS + le code PIN sont incorrects | L’utilisateur a entré un code secret à usage unique incorrect et/ou un code PIN utilisateur incorrect.  L’authentification est refusée. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | A dépassé les tentatives de saisie maximum de code secret à usage unique de SMS | L’utilisateur a dépassé le nombre maximal de tentatives de saisie de code secret à usage unique. |
| FAILED_PHONE_APP_DENIED | Application mobile refusée | L’utilisateur a refusé l’authentification dans l’application mobile en appuyant sur le bouton Refuser. |
| FAILED_PHONE_APP_INVALID_PIN | Code PIN d’application mobile non valide | L’utilisateur a entré un code PIN non valide lors de l’authentification dans l’application mobile. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Code PIN d’application mobile non modifié | L’utilisateur n’a pas effectué correctement la modification du code PIN nécessaire dans l’application mobile. |
| FAILED_FRAUD_REPORTED | Fraude signalée | L’utilisateur a signalé une fraude dans l’application mobile. |
| FAILED_PHONE_APP_NO_RESPONSE | Aucune réponse de l’application mobile | L’utilisateur n’a pas répondu à la demande d’authentification de l’application mobile. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Tous les appareils de l’application mobile sont bloqués | Les appareils de l’application mobile de cet utilisateur ne répondent plus aux notifications et ont été bloqués. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Échec des notifications sur l’application mobile | Une erreur s’est produite lors de la tentative d’envoi d’une notification vers l’application mobile sur l’appareil de l’utilisateur. |
| FAILED_PHONE_APP_INVALID_RESULT | Résultat non valide de l’application mobile | L’application mobile a renvoyé un résultat non valide. |
| FAILED_OATH_CODE_INCORRECT | Code OATH incorrect | L’utilisateur a entré un code OATH incorrect.  L’authentification est refusée. |
| FAILED_OATH_CODE_PIN_INCORRECT | Code OATH + code PIN incorrects | L’utilisateur a entré un code OATH incorrect et/ou un code PIN utilisateur incorrect.  L’authentification est refusée. |
| FAILED_OATH_CODE_DUPLICATE | Code OATH dupliqué | L’utilisateur a entré un code OATH précédemment utilisé.  L’authentification est refusée. |
| FAILED_OATH_CODE_OLD | Code OATH ayant expiré | L’utilisateur a entré un code OATH qui précède un code OATH précédemment utilisé.  L’authentification est refusée. |
| FAILED_OATH_TOKEN_TIMEOUT | Délai d’attente de résultat de code OATH | L’utilisateur a mis trop de temps à entrer le code OATH et la tentative de Multi-Factor Authentication avait déjà expiré. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Délai d’expiration de résultat des questions de sécurité | L’utilisateur a mis trop de temps à entrer les réponses aux questions de sécurité et la tentative de Multi-Factor Authentication avait déjà expiré. |
| FAILED_AUTH_RESULT_TIMEOUT | Délai d’expiration du résultat de l’authentification | L’utilisateur a mis trop de temps pour traiter la tentative de Multi-Factor Authentication. |
| FAILED_AUTHENTICATION_THROTTLED | Authentification limitée | La tentative de Multi-Factor Authentication a été limitée par le service. |

## <a name="next-steps"></a>Étapes suivantes

* [Rapport d’utilisation et d’insights SSPR et MFA](howto-authentication-methods-usage-insights.md)
* [Pour les utilisateurs](../user-help/multi-factor-authentication-end-user.md)
* [Où déployer](concept-mfa-whichversion.md)
