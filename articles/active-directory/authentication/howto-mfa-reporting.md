---
title: Rapports d’accès et d’utilisation pour Azure MFA - Azure Active Directory
description: Cette section décrit comment utiliser la fonctionnalité de rapports d’Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988e8982b6f06fb1210330c5cafdb696892794fe
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235526"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapports dans Azure Multi-Factor Authentication

Azure Multi-Factor Authentication fournit plusieurs rapports qui peuvent être utilisés par vous et votre organisation, et qui sont accessibles via le portail Azure. Le tableau ci-après répertorie les rapports disponibles :

| Rapport | Lieu | Description |
|:--- |:--- |:--- |
| Historique de l'utilisateur bloqué | Azure AD > Serveur MFA > Bloquer/débloquer des utilisateurs | Affiche l’historique des demandes de blocage et de déblocage d’utilisateurs. |
| Alertes relatives à l’utilisation et aux fraudes | Azure AD > Connexions | Fournit des informations sur l’utilisation globale, un récapitulatif par utilisateur, des informations détaillées sur les utilisateurs, ainsi que l’historique des alertes de fraude émises au cours de la plage de dates spécifiée. |
| Utilisation des composants locaux | Azure AD > Serveur MFA > Rapport d’activité | Fournit des informations sur l’utilisation globale de MFA via l’extension NPS, AD FS et le serveur MFA. |
| Historique de l'utilisateur contourné | Azure AD > Serveur MFA > Contournement à usage unique | Affiche l’historique des demandes de contournement de Multi-Factor Authentication pour un utilisateur. |
| État du serveur | Azure AD > Serveur MFA > État du serveur | Affiche l’état des serveurs Multi-Factor Authentication associés à votre compte. |

## <a name="view-mfa-reports"></a>Afficher les rapports MFA

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, sélectionnez **Azure Active Directory** > **Serveur MFA**.
3. Sélectionnez le rapport que vous souhaitez afficher.

   ![Rapport état du serveur MFA Server dans le portail Azure](./media/howto-mfa-reporting/report.png)

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

![Rapport des connexions Azure AD dans le portail Azure](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Structure du rapport de connexion

Les rapports d’activité de connexion pour l’authentification multifacteur vous permettent d’accéder aux informations suivantes :

**MFA obligatoire :** spécifie si l’authentification multifacteur est nécessaire pour la connexion ou non. L’authentification multifacteur peut être nécessaire en raison de l’authentification multifacteur par utilisateur, de l’accès conditionnel ou d’autres raisons. Les valeurs possibles sont **Oui** ou **Non**.

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

- Nom de la stratégie
- Contrôles d’octroi
- Contrôles de session
- Résultat

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Création de rapports sur les utilisateurs inscrits pour l’authentification Multifacteur de PowerShell

Tout d’abord, assurez-vous d’avoir le [module MSOnline V1 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) installé.

Identifiez les utilisateurs qui se sont inscrits auprès de MFA à l’aide du code PowerShell qui suit.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifiez les utilisateurs qui ne se sont pas inscrits auprès de MFA à l’aide du code PowerShell qui suit.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Résultats possibles dans les rapports d’activité

Le tableau suivant peut être utilisé pour résoudre les problèmes de l’authentification multifacteur à l’aide de la version téléchargée du rapport d’activité de l’authentification multifacteur. Ils n’apparaîtront pas directement dans le portail Azure.

| Résultat de l’appel | Description | Description générale |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN entré | L’utilisateur a entré un code confidentiel.  Si l’authentification a réussi, puis qu’il a entré le code confidentiel correct.  Si l’authentification est refusée, cela signifie qu’ils a entré un code confidentiel incorrect ou l’utilisateur est défini en mode Standard. |
| SUCCESS_NO_PIN | Uniquement les # entré | Si l’utilisateur est défini sur le mode PIN et l’authentification est refusée, cela signifie que l’utilisateur n’a pas entré son code PIN et qu’il a entré uniquement #.  Si l’utilisateur est défini en mode Standard et l’authentification réussit cela signifie que l’utilisateur a entré uniquement # qui est la meilleure chose à faire en mode Standard. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Non appuyé après l’entrée | L’utilisateur n’a pas envoyé de chiffres DTMF, car # n’a pas été entré.  Les autres chiffres entrés ne sont pas envoyées sauf si # est entré, indiquant l’achèvement de l’entrée. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Aucune entrée téléphone - délai dépassé | L’appel a été reçu, mais aucune réponse.  Cela indique généralement que l’appel a été récupéré par la messagerie vocale. |
| SUCCESS_PIN_EXPIRED | PIN expiré et non modifié | PIN de l’utilisateur a expiré et qu’ils ont été invités à le modifier, mais la modification de code PIN n’a pas aboutie. |
| SUCCESS_USED_CACHE | Cache utilisé | L’authentification a réussi sans appel multi-Factor Authentication dans la mesure où une authentification a abouti précédemment pour le même nom d’utilisateur s’est produite pendant la période de cache configuré. |
| SUCCESS_BYPASSED_AUTH | Authentification contournée | L’authentification a réussi à l’aide d’un contournement à usage unique initié pour l’utilisateur.  Consultez le rapport d’historique utilisateur contourné pour plus d’informations sur le contournement. |
| SUCCESS_USED_IP_BASED_CACHE | Cache basé sur IP utilisé | L’authentification a réussi sans appel multi-Factor Authentication depuis une authentification a abouti précédemment pour le même nom d’utilisateur, le type d’authentification, le nom de l’application et IP s’est produite pendant la période de cache configuré. |
| SUCCESS_USED_APP_BASED_CACHE | Cache en fonction des applications utilisé | L’authentification a réussi sans appel multi-Factor Authentication depuis une authentification a abouti précédemment pour le même nom d’utilisateur, le type d’authentification et le nom de l’application dans le délai de cache configuré. |
| SUCCESS_INVALID_INPUT | Entrée téléphone non valide | La réponse envoyée à partir du téléphone n’est pas valide.  Cela peut provenir d’un télécopieur ou modem ou l’utilisateur a peut-être entré * dans le cadre de leur code confidentiel. |
| SUCCESS_USER_BLOCKED | L’utilisateur est bloqué | Numéro de téléphone de l’utilisateur est bloqué.  Un numéro bloqué peut être lancé par l’utilisateur lors d’un appel d’authentification ou par un administrateur à l’aide du portail Azure. <br> REMARQUE :   Un numéro bloqué est également une conséquence d’une alerte de fraude. |
| SUCCESS_SMS_AUTHENTICATED | Message texte authentifié | Pour un message texte bidirectionnel, l’utilisateur a répondu correctement avec leur mot de passe à usage unique (OTP) ou secret à usage unique + PIN. |
| SUCCESS_SMS_SENT | Message texte envoyé | Pour un Message texte, le message texte contenant le code secret à usage unique (OTP) a été correctement envoyé.  L’utilisateur entre le secret à usage unique ou un OTP + un PIN dans l’application pour effectuer l’authentification. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Application mobile authentifiée | L’utilisateur est authentifié correctement via l’application mobile. |
| SUCCESS_OATH_CODE_PENDING | Code OATH en attente | L’utilisateur a été invité à entrer son code OATH, mais n’a pas répondu. |
| SUCCESS_OATH_CODE_VERIFIED | Code OATH vérifié | L’utilisateur a entré un code OATH valide lorsque vous y êtes invité. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Code OATH de secours vérifié | L’utilisateur a été refusé l’authentification à l’aide de sa principale méthode multi-Factor Authentication et puis fourni un code OATH valide de secours. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Réponses à des Questions de sécurité de secours | L’utilisateur a été refusé à l’authentification à l’aide de sa principale méthode multi-Factor Authentication, puis a répondu aux questions de sécurité correctement de secours. |
| FAILED_PHONE_BUSY | Authentification déjà en cours | Multi-Factor Authentication qu’il traite déjà une authentification pour cet utilisateur.  Cela est souvent dû à des clients RADIUS qui envoient plusieurs demandes d’authentification lors de la même l’authentification. |
| CONFIG_ISSUE | Téléphone inaccessible | Tentative d’appel a été effectuée, mais soit ne peut pas être placé ou n’était pas une réponse.  Cela inclut le signal occupé, un signal occupé rapide (déconnecté), triple-triple tonalité (numéro n’est plus en service), a dépassé le délai sonne, etc. |
| FAILED_INVALID_PHONENUMBER | Format de numéro de téléphone non valide | Le numéro de téléphone a un format non valide.  Numéros de téléphone doivent être numériques et doivent comporter 10 chiffres pour le code de pays + 1 (États-Unis et Canada). |
| FAILED_USER_HUNGUP_ON_US | Utilisateur a raccroché le téléphone | L’utilisateur a répondu au téléphone, mais raccroché sans appuyer sur des boutons. |
| FAILED_INVALID_EXTENSION | Extension non valide | L’extension contient des caractères non valides.  Seuls les chiffres, virgules, *, et # sont autorisés.  Un préfixe @ peut également être utilisé. |
| FAILED_FRAUD_CODE_ENTERED | Code fraude entré | L’utilisateur choisi pour signaler une fraude lors de l’appel, ce qui entraîne un refus d’authentification et un numéro de téléphone bloqué.| 
| FAILED_SERVER_ERROR | Impossible d’émettre l’appel | Le service multi-Factor Authentication n’a pas pu effectuer l’appel. |
| FAILED_SMS_NOT_SENT | Message texte n’a pas pu être envoyé. | Le message texte n’a pas pu être envoyé.  L’authentification est refusée. |
| FAILED_SMS_OTP_INCORRECT | Message texte OTP Incorrect | L’utilisateur a entré un code secret à usage unique incorrect (OTP) à partir du message texte qu'ils reçus.  L’authentification est refusée. |
| FAILED_SMS_OTP_PIN_INCORRECT | Texte du Message secret à usage unique + PIN Incorrect | L’utilisateur a entré un code secret à usage unique incorrect (OTP) et/ou un code confidentiel utilisateur incorrect.  L’authentification est refusée. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | A dépassé le Maximum de texte Message OTP tentatives | L’utilisateur a dépassé le nombre maximal de tentatives de code secret à usage unique (OTP). |
| FAILED_PHONE_APP_DENIED | Application mobile refusée | L’utilisateur a refusé l’authentification dans l’application mobile en appuyant sur le bouton refuser. |
| FAILED_PHONE_APP_INVALID_PIN | Code PIN d’application mobile non valide | L’utilisateur a entré un code confidentiel non valide lors de l’authentification dans l’application mobile. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Application mobile PIN ne pas changé | L’utilisateur ne pas pu terminer une modification de PIN requise dans l’application mobile. |
| FAILED_FRAUD_REPORTED | Fraude signalée | L’utilisateur a signalé une fraude dans l’application mobile. |
| FAILED_PHONE_APP_NO_RESPONSE | Application mobile aucune réponse | L’utilisateur n’a pas répondu à la demande d’authentification application mobile. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Application tous les périphériques Mobile bloqués | Les appareils de l’application mobile pour cet utilisateur ne répondent plus aux notifications et ont été bloqués. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Échoué de la Notification d’application mobile | Une erreur s’est produite lorsque vous tentez d’envoyer une notification à l’application mobile sur l’appareil de l’utilisateur. |
| FAILED_PHONE_APP_INVALID_RESULT | Résultat de l’application mobile non valide | L’application mobile a renvoyé un résultat non valide. |
| FAILED_OATH_CODE_INCORRECT | Code OATH Incorrect | L’utilisateur a entré un code OATH incorrect.  L’authentification est refusée. |
| FAILED_OATH_CODE_PIN_INCORRECT | Code OATH et PIN incorrects | L’utilisateur a entré un code OATH incorrect et/ou un code confidentiel utilisateur incorrect.  L’authentification est refusée. |
| FAILED_OATH_CODE_DUPLICATE | Dupliquer le Code OATH | L’utilisateur a entré un code OATH précédemment utilisé.  L’authentification est refusée. |
| FAILED_OATH_CODE_OLD | Code OATH expiré | L’utilisateur a entré un code OATH qui précède un code OATH précédemment utilisé.  L’authentification est refusée. |
| FAILED_OATH_TOKEN_TIMEOUT | Délai d’attente de résultat de Code OATH | L’utilisateur a pris trop de temps à entrer le code OATH et la tentative d’authentification multifacteur avait déjà expiré. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Délai de résultat de Questions de sécurité | L’utilisateur a pris trop de temps à entrer des réponses aux questions de sécurité et la tentative d’authentification multifacteur a déjà expiré. |
| FAILED_AUTH_RESULT_TIMEOUT | Délai d’expiration du résultat de l’authentification | L’utilisateur a pris trop de temps de la tentative d’authentification multifacteur. |
| FAILED_AUTHENTICATION_THROTTLED | Authentification limitée | La tentative d’authentification multifacteur a été limitée par le service. |

## <a name="next-steps"></a>Étapes suivantes

* [Pour les utilisateurs](../user-help/multi-factor-authentication-end-user.md)
* [Où déployer](concept-mfa-whichversion.md)
