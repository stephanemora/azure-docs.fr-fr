---
title: 'Azure AD Connect : Considérations sur les identités hybrides pour Azure Government'
description: Considérations spéciales relatives au déploiement d’Azure AD Connect avec le cloud pour le secteur public.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81377554"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Considérations sur les identités hybrides pour Azure Government 
Le document suivant décrit les considérations relatives à l’implémentation d’un environnement hybride avec le cloud Azure Government.  Ces informations sont fournies à titre de référence pour les administrateurs et les architectes qui travaillent avec le cloud Azure Government.  
> [!NOTE] 
> Pour intégrer un environnement AD local au cloud Azure Government, vous devez effectuer une mise à niveau vers la dernière version de [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> Pour obtenir la liste complète des points de terminaison du DoD de l’État fédéral des USA, reportez-vous à la [documentation](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Authentification directe 
Les informations suivantes sont fournies pour l’implémentation de l’authentification directe (PTA) et du cloud Azure Government.

### <a name="allow-access-to-urls"></a>Autoriser l'accès à des URL  
Avant de déployer l’agent d’authentification directe, vérifiez s’il y a un pare-feu entre vos serveurs et Azure AD. Si votre pare-feu ou votre proxy autorise la liste verte DNS, ajoutez les connexions suivantes : 
> [!NOTE]
> Les instructions suivantes s’appliquent également à l’installation du [connecteur de proxy d’application](https://aka.ms/whyappproxy) pour les environnements Azure Government.

|URL |Utilisation|
|-----|-----| 
|*.msappproxy.us *.servicebus.usgovcloudapi.net|Communication entre l’agent et le service cloud Azure AD |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| Azure utilise ces URL pour vérifier les certificats.| 
|login.windows.us secure.aadcdn.microsoftonline-p.com *.microsoftonline.us </br>* .microsoftonline-p.us </br>*.msauth.net </br>* .msauthimages.net </br>*.msecnd.net</br>* .msftauth.net </br>*.msftauthimages.net</br>* .phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| L’agent utilise ces URL lors du processus d’inscription.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Installer l’agent pour le cloud Azure Government 
Pour installer l’agent pour le cloud Azure Government, vous devez suivre ces étapes spécifiques : Dans le terminal de ligne de commande, accédez au dossier où se trouve l’exécutable pour l’installation de l’agent. Exécutez la commande suivante, qui spécifie que l’installation est pour Azure Government. 

Pour l’authentification directe : 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Pour le proxy d’application :
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Authentification unique 
Configurez votre serveur Azure AD Connect : Si vous utilisez l’authentification directe comme méthode de connexion utilisateur, aucune vérification des prérequis n’est nécessaire. Si vous utilisez la synchronisation de hachage de mot de passe comme méthode de connexion, et s’il existe un pare-feu entre Azure AD Connect et Azure AD, vérifiez les points suivants :
- Vous utilisez Azure AD Connect 1.1.644.0 ou version ultérieure. 
- Si votre pare-feu ou votre proxy autorisent la mise en liste verte DNS, ajoutez les connexions aux URL *.msapproxy.us sur le port 443. Dans le cas contraire, autorisez l’accès aux plages d’adresses IP du centre de données Azure, qui sont mises à jour chaque semaine. Cette condition préalable est applicable uniquement lorsque vous activez la fonctionnalité. Elle n'est pas requise pour les connexions d'utilisateur réelles. 

### <a name="rolling-out-seamless-sso"></a>Déploiement homogène de l’authentification unique 
Vous pouvez déployer progressivement l’authentification unique fluide pour vos utilisateurs en suivant les instructions fournies ci-dessous. Vous commencez par ajouter l’URL Azure AD ci-après aux paramètres de la zone Intranet de tous les utilisateurs ou des utilisateurs sélectionnés en utilisant la stratégie de groupe dans Active Directory : https://autologon.microsoft.us 

En outre, vous devez activer un paramètre de stratégie de zone intranet appelé Autoriser les mises à jour de la barre d’état via le script avec la stratégie de groupe. Considérations sur le navigateur Mozilla Firefox (toutes les plateformes) : Mozilla Firefox n’utilise pas automatiquement l’authentification Kerberos. Chaque utilisateur doit ajouter manuellement l’URL Azure AD à ses paramètres Firefox comme suit : 
1. Exécutez Firefox et entrez about:config dans la barre d’adresse. Ignorez les notifications que vous voyez. 
2. Recherchez la préférence network.negotiate-auth.trusted-uris. Cette préférence répertorie les sites de confiance de Firefox pour l’authentification Kerberos. 
3. Cliquez avec le bouton droit et sélectionnez Modifier. 
4. Entrez https://autologon.microsoft.us dans le champ.
5. Sélectionnez OK, puis rouvrez le navigateur. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge basé sur Chromium (toutes les plateformes) 
Si vous avez remplacé les paramètres de stratégie  `AuthNegotiateDelegateAllowlist` ou  `AuthServerAllowlist` dans votre environnement, veillez à y ajouter également l’URL d’Azure AD (https://autologon.microsoft.us) ). 

### <a name="google-chrome-all-platforms"></a>Google Chrome (toutes les plateformes) 
Si vous avez remplacé les paramètres de stratégie  `AuthNegotiateDelegateWhitelist` ou  `AuthServerWhitelist` dans votre environnement, veillez à y ajouter également l’URL d’Azure AD (https://autologon.microsoft.us) ). 

## <a name="next-steps"></a>Étapes suivantes
[Authentification directe](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[Authentification unique](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
