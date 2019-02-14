---
title: Rapports d’accès et d’utilisation pour Azure MFA | Microsoft Docs
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
ms.openlocfilehash: 04ee892c85d2795f4ec11d47d861af3007c46e96
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169324"
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

   <center>![Cloud](./media/howto-mfa-reporting/report.png)</center>

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

![Cloud](./media/howto-mfa-reporting/sign-in-report.png)

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

## <a name="powershell-reporting"></a>Génération de rapports PowerShell

Identifiez les utilisateurs qui se sont inscrits auprès de MFA à l’aide du code PowerShell qui suit.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifiez les utilisateurs qui ne se sont pas inscrits auprès de MFA à l’aide du code PowerShell qui suit.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Étapes suivantes

* [Pour les utilisateurs](../user-help/multi-factor-authentication-end-user.md)
* [Où déployer](concept-mfa-whichversion.md)
