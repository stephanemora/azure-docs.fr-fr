---
title: Paramètres de cookies du proxy d'application - Azure Active Directory  | Microsoft Docs
description: Azure Active Directory (Azure AD) contient des cookies d'accès et de session pour accéder aux applications locales via le proxy d'application. Dans cet article, vous allez apprendre à utiliser et configurer les paramètres de cookies.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06fd83ee815e9e207c1fa5a1c6767280122c4d0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440528"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Paramètres de cookies pour l'accès aux applications locales dans Azure Active Directory

Azure Active Directory (Azure AD) contient des cookies d'accès et de session pour accéder aux applications locales via le proxy d'application. Apprenez à utiliser les paramètres de cookies du proxy d'application. 

## <a name="what-are-the-cookie-settings"></a>Que sont les paramètres de cookies ?

Le [proxy d'application](application-proxy.md) utilise les paramètres de cookies d'accès et de session suivants.

| Paramètre de cookie | Default | Description | Recommandations |
| -------------- | ------- | ----------- | --------------- |
| Utiliser un cookie HTTPOnly | **Non** | **Oui** permet au proxy d'application d'inclure l'indicateur HTTPOnly dans les en-têtes de réponse HTTP. Cet indicateur offre des avantages supplémentaires en matière de sécurité. Par exemple, il empêche les scripts côté client (CSS) de copier ou de modifier les cookies.<br></br><br></br>Auparavant, lorsque le paramètre HTTPOnly n'était pas pris en charge, le proxy d'application chiffrait et transmettait les cookies sur un canal SSL sécurisé pour les protéger des modifications. | Utilisez **Oui** pour bénéficier d'avantages supplémentaires en matière de sécurité.<br></br><br></br>Utilisez **Non** pour les clients ou agents utilisateurs nécessitant un accès au cookie de session. Par exemple, utilisez **Non** pour un client RDP ou MTSC qui se connecte à un serveur de passerelle Bureau à distance via le proxy d'application.|
| Utiliser un cookie sécurisé | **Non** | **Oui** permet au proxy d'application d'inclure l'indicateur Secure dans les en-têtes de réponse HTTP. Les cookies sécurisés renforcent la sécurité en transmettant les cookies sur un canal TLS sécurisé tel que HTTPS. Cela empêche l'observation des cookies par des tiers non autorisés en raison de la transmission du cookie en texte clair. | Utilisez **Oui** pour bénéficier d'avantages supplémentaires en matière de sécurité.|
| Utiliser un cookie persistant | **Non** | **Oui** permet au proxy d'application de configurer ses cookies d'accès pour qu'ils n'expirent pas lorsque le navigateur web est fermé. La persistance dure jusqu'à l'expiration du jeton d'accès ou jusqu'à ce que l'utilisateur supprime manuellement les cookies persistants. | Utilisez **Non** en raison du risque de sécurité associé au maintien de l'authentification des utilisateurs.<br></br><br></br>Nous vous conseillons de réserver le paramètre **Oui** aux applications les plus anciennes qui ne peuvent pas partager les cookies entre les processus. Pour gérer le partage des cookies entre les processus, il est préférable de mettre votre application à jour plutôt que d'utiliser des cookies persistants. Par exemple, vous aurez peut-être besoin de cookies persistants pour permettre à un utilisateur d'ouvrir des documents Office en mode Explorateur à partir d'un site SharePoint. Sans les cookies persistants, cette opération peut échouer si les cookies d'accès ne sont pas partagés entre le navigateur, le processus de l'explorateur et le processus Office. |

## <a name="set-the-cookie-settings---azure-portal"></a>Définir les paramètres de cookies - Portail Azure
Pour définir les paramètres de cookies à l'aide du portail Azure :

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
2. Accédez à **Azure Active Directory** > **Applications d'entreprise** > **Toutes les applications**.
3. Sélectionnez l'application pour laquelle vous souhaitez activer un paramètre de cookie.
4. Cliquez sur **Proxy d'application**.
5. Sous **Paramètres supplémentaires**, définissez le paramètre de cookie sur **Oui** ou **Non**.
6. Cliquez sur **Enregistrer** pour enregistrer les changements. 

## <a name="view-current-cookie-settings---powershell"></a>Afficher les paramètres de cookies en cours - PowerShell

Pour afficher les paramètres actuels de cookie pour l’application, utilisez cette commande PowerShell :  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Définir les paramètres de cookies - PowerShell

Dans les commandes PowerShell suivantes, ```<ObjectId>``` est l’ObjectId de l’application. 

**Cookie HTTP uniquement** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Cookie sécurisé**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Cookies persistants**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
