---
title: Utilisez les restrictions du client pour gérer l’accès à SaaS cloud des applications - Azure | Microsoft Docs
description: Comment utiliser les restrictions du client pour gérer les utilisateurs peuvent accéder à des applications en fonction de leur locataire Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: celested
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b78897e2e03085a20f07ce8724226f0e0171861e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291232"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Utiliser les restrictions du client pour gérer l’accès aux applications cloud SaaS

Les organisations de grande taille qui se préoccupent de la sécurité veulent adopter les services cloud, comme Office 365, tout en s’assurant que leurs utilisateurs ne pourront accéder qu’à des ressources approuvées. En général, pour gérer l’accès, les entreprises limitent les noms de domaine ou les adresses IP. Cette approche ne fonctionne pas dans un monde où les applications software as a service (ou SaaS) sont hébergées dans un cloud public, en cours d’exécution sur les noms de domaine partagé comme [outlook.office.com](https://outlook.office.com/) et [login.microsoftonline.com](https://login.microsoftonline.com/). Le fait de bloquer ces adresses empêche les utilisateurs d’accéder à Outlook sur le web entièrement, au lieu de restreindre simplement leur accès à des identités et des ressources approuvées.

La solution Azure Active Directory (Azure AD) à ce défi est une fonctionnalité appelée restrictions du client. Avec les restrictions du client, les organisations peuvent contrôler l’accès aux applications cloud SaaS, avec le locataire Azure AD, que les applications utilisent pour l’authentification unique. Par exemple, vous souhaitez peut-être autoriser l’accès aux applications Office 365 de votre organisation, tout en empêchant l’accès aux instances d’autres organisations de ces mêmes applications.  

Avec les restrictions du client, les organisations peuvent spécifier la liste des clients auxquels leurs utilisateurs sont autorisés à accéder. Dans ce cas, Azure AD accorde uniquement l’accès à ces clients autorisés.

Cet article se concentre sur les restrictions du client pour Office 365, mais cette fonctionnalité doit fonctionner avec n’importe quelle application cloud SaaS qui utilise des protocoles d’authentification moderne avec Azure AD pour l’authentification unique. Si vous utilisez des applications SaaS avec un client Azure AD différent du client utilisé par Office 365, assurez-vous que tous les clients nécessaires sont autorisés. Pour plus d’informations sur les applications cloud SaaS, consultez [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Fonctionnement

La solution comprend les composants suivants :

1. **Azure AD** : Si le `Restrict-Access-To-Tenants: <permitted tenant list>` est présents, Azure AD uniquement les problèmes des jetons de sécurité pour les clients autorisés.

2. **Infrastructure de serveur proxy local**: Cette infrastructure est un appareil proxy capable d’inspection de Secure Sockets Layer (SSL). Vous devez configurer le serveur proxy pour insérer l’en-tête contenant la liste des clients autorisés dans le trafic destiné à Azure AD.

3. **Logiciel client**: Pour prendre en charge les restrictions du client, le logiciel client doit demander des jetons directement à partir d’Azure AD, afin que l’infrastructure du proxy peut intercepter le trafic. Basée sur navigateur les applications Office 365 prennent actuellement en charge les restrictions du client, comme les clients Office qui utilisent l’authentification moderne (comme OAuth 2.0).

4. **L’authentification moderne**: Services cloud doivent utiliser l’authentification moderne pour utiliser les restrictions du client et bloquer l’accès à tous les clients non autorisés. Vous devez configurer les services de cloud Office 365 pour utiliser les protocoles d’authentification moderne par défaut. Pour plus d’informations sur la prise en charge de l’authentification moderne par Office 365, consultez [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Authentification moderne Office 365 mise à jour).

Le schéma suivant illustre le flux de trafic de niveau supérieur. Restrictions du client nécessite une inspection SSL uniquement sur le trafic vers Azure AD, pas pour les services de cloud Office 365. Cette distinction est importante, car le volume de trafic pour l’authentification à Azure AD est généralement beaucoup plus faible volume de trafic pour les applications SaaS comme Exchange Online et SharePoint Online.

![Flux de trafic des restrictions du client - diagramme](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurer les restrictions du client

Il existe deux étapes pour bien démarrer avec les restrictions du client. Tout d’abord, assurez-vous que vos clients peuvent se connecter aux adresses correctes. Ensuite, configurez votre infrastructure du proxy.

### <a name="urls-and-ip-addresses"></a>URL et adresses IP

Pour utiliser les restrictions du client, vos clients doivent être en mesure de se connecter aux URL AD Azure suivantes pour authentifier : [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/), et [ Login.Windows.NET](https://login.windows.net/). En outre, pour accéder à Office 365, vos clients doivent également être en mesure de se connecter pour les noms de domaine complet (FQDN), URL, et les adresses IP définies dans [plages d’adresses IP et Office 365 URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configuration du proxy et conditions requises

La configuration suivante est nécessaire pour activer les restrictions du client via votre infrastructure de proxy. Ce guide est générique, donc consultez la documentation du fabricant de votre proxy pour obtenir les étapes d’implémentation spécifiques.

#### <a name="prerequisites"></a>Prérequis

- Le proxy doit être en mesure d’effectuer l’interception SSL, l’insertion d’en-tête HTTP et de filtrer les destinations à l’aide des noms de domaine complets/URL.

- Les clients doivent approuver la chaîne de certificat présentée par le proxy pour les communications SSL. Par exemple, si des certificats à partir d’une commande interne [infrastructure à clé publique (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) sont utilisés, le certificat d’autorité de certificat de racine émetteur interne doit être approuvé.

- Cette fonctionnalité est incluse dans les abonnements Office 365, mais si vous souhaitez utiliser les restrictions du client pour contrôler l’accès à d’autres applications SaaS, les licences Azure AD Premium 1 sont requis.

#### <a name="configuration"></a>Configuration

Pour chaque demande entrante sur login.microsoftonline.com, login.microsoft.com et login.windows.net, insérez deux en-têtes HTTP : *Restrict-Access-To-Tenants* et *Restrict-Access-Context*.

Les éléments suivants doivent être inclus dans les en-têtes :

- Pour *Restrict-Access-To-Tenants*, utilisez la valeur \<autorisé liste locataire\>, qui est une liste séparée par des virgules des clients auxquels vous souhaitez autoriser les utilisateurs à accéder. N’importe quel domaine qui est inscrit auprès d’un client peut être utilisé pour identifier le client dans cette liste. Par exemple, pour autoriser l’accès aux clients Contoso et Fabrikam, la paire nom/valeur ressemble à :  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Pour *Restrict-Access-Context*, utilisez une valeur d’un ID de répertoire unique, déclarant quel client consiste à définir le locataire restrictions. Par exemple, pour déclarer Contoso en tant que client qui définit la stratégie restrictions du client, la paire nom/valeur ressemble à : `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Vous pouvez trouver votre ID de répertoire dans le [portail Azure Active Directory](https://aad.portal.azure.com/). Connectez-vous en tant qu’administrateur, sélectionnez **Azure Active Directory**, puis sélectionnez **Propriétés**.

Pour empêcher les utilisateurs d’insérer leur propre en-tête HTTP avec des clients non approuvés, le proxy doit remplacer le *Restrict-Access-To-Tenants* en-tête si elle est déjà présent dans la demande entrante.

Les clients doivent être forcés à utiliser le proxy pour toutes les demandes à login.microsoftonline.com, login.microsoft.com et login.windows.net. Par exemple, si des fichiers PAC sont utilisés pour diriger les clients d’utiliser le proxy, les utilisateurs finaux ne doivent pas être en mesure de modifier ou de désactiver les fichiers PAC.

## <a name="the-user-experience"></a>Expérience utilisateur

Cette section décrit l’expérience des utilisateurs finaux et administrateurs.

### <a name="end-user-experience"></a>Expérience de l’utilisateur final

Par exemple, un utilisateur sur le réseau Contoso tente d’accéder à l’instance Fabrikam d’une application SaaS partagée comme Outlook Online. Si Fabrikam est un client non autorisé pour l’instance Contoso, l’utilisateur voit un message de refus d’accès, qui indique que vous tentez d’accéder à une ressource qui appartient à une organisation non approuvée par votre service informatique.

### <a name="admin-experience"></a>Expérience administrateur

Bien que la configuration de restrictions du client est effectuée sur l’infrastructure du proxy d’entreprise, administrateurs peuvent accéder directement dans le portail Azure, les rapports de restrictions du client. Pour afficher les rapports :

1. Se connecter à la [portail Azure Active Directory](https://aad.portal.azure.com/). Le **centre d’administration Azure Active Directory** tableau de bord s’affiche.

2. Sélectionnez **Azure Active Directory** dans le volet de gauche. La page de vue d’ensemble d’Azure Active Directory s’affiche.

3. Dans le **autres fonctionnalités** titre, sélectionnez **restrictions du client**.

L’administrateur du client spécifié dans le client Restricted-Access-Context peut utiliser ce rapport pour afficher les connexions bloquées en raison de la stratégie restrictions du client, y compris l’identité utilisée et le répertoire cible ID. Les connexions sont incluses si le client définissant la restriction est le client de l’utilisateur, ou le client de la ressource pour la connexion.

Comme pour les autres rapports dans le portail Azure, vous pouvez utiliser des filtres pour spécifier l’étendue de votre rapport. Vous pouvez filtrer sur un intervalle de temps spécifique, un utilisateur, un application, un client ou un état. Si vous sélectionnez le **colonnes** bouton, que vous pouvez choisir d’afficher des données avec n’importe quelle combinaison des champs suivants :

- **Utilisateur**
- **Application**
- **État**
- **Date**
- **Date (UTC)** (où UTC est le temps universel coordonné)
- **Méthode d’authentification MFA** (méthode d’authentification multifacteur)
- **Détails de l’authentification MFA** (détail de l’authentification multifacteur)
- **Résultat MFA**
- **Adresse IP**
- **Client**
- **Nom d’utilisateur**
- **Lieu**
- **ID de locataire cible**

## <a name="office-365-support"></a>Prise en charge d’Office 365

Les applications Office 365 doivent répondre aux deux critères pour prendre entièrement en charge les restrictions du client :

1. Le client utilisé prend en charge l’authentification moderne.
2. L’authentification moderne est activée comme protocole d’authentification par défaut pour le service cloud.

Consultez [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Authentification moderne Office 365 mise à jour) pour plus d’informations sur les clients Office qui prennent actuellement en charge l’authentification moderne. Cette page inclut également des liens vers des instructions relatives à l’activation de l’authentification moderne sur des clients Exchange Online et Skype Entreprise Online spécifiques. SharePoint Online permet déjà de l’authentification moderne par défaut.

Les applications Web Office 365 (sites portail Office, Yammer, SharePoint, Outlook sur le Web et bien plus encore) prend actuellement en charge les restrictions du client. Clients lourds (Outlook, Skype pour entreprise, Word, Excel, PowerPoint, etc.) peuvent appliquer des restrictions du client uniquement lorsque vous utilisez l’authentification moderne.  

Outlook et Skype pour les clients d’entreprise qui prennent en charge l’authentification moderne peuvent continuer à utiliser les protocoles hérités sur les clients où l’authentification moderne n’est pas activée, contournant ainsi les restrictions du client. Restrictions du client peuvent empêcher les applications qui utilisent les protocoles hérités si elles contactent login.microsoftonline.com, login.microsoft.com ou login.windows.net lors de l’authentification.

Pour Outlook sur Windows, les clients peuvent choisir d’implémenter des restrictions qui empêchent les utilisateurs finaux d’ajouter des comptes de messagerie non approuvés à leurs profils. Par exemple, consultez le paramètre de stratégie de groupe [Empêcher l’ajout de comptes Exchange personnalisés](https://gpsearch.azurewebsites.net/default.aspx?ref=1).

## <a name="testing"></a>Test

Si vous souhaitez essayer de restrictions du client avant de mettre en œuvre pour toute l’organisation, vous avez deux options : une approche basée sur hôte à l’aide d’un outil comme Fiddler ou un déploiement par étapes des paramètres de proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler pour une approche basée sur hôte

Fiddler est un proxy de débogage web gratuit qui peut être utilisé pour capturer et modifier le trafic HTTP/HTTPS, notamment l’insertion d’en-têtes HTTP. Pour configurer Fiddler afin de tester les restrictions du client, procédez comme suit :

1. [Téléchargez et installez Fiddler](https://www.telerik.com/fiddler).

2. Configurez Fiddler pour déchiffrer le trafic HTTPS, conformément à [la documentation d’aide de Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configurez Fiddler pour insérer les en-têtes *Restrict-Access-To-Tenants* et *Restrict-Access-Context* à l’aide de règles personnalisées :

   1. Dans l’outil débogueur web Fiddler, sélectionnez le menu **Règles** et sélectionnez **Personnaliser les règles...** pour ouvrir le fichier CustomRules.

   2. Ajoutez les lignes suivantes au début de la `OnBeforeRequest` (fonction). Remplacez \<domaine du locataire\> avec un domaine enregistré auprès de votre client (par exemple, `contoso.onmicrosoft.com`). Remplacez \<l’ID de répertoire\> par l’identificateur GUID Azure AD de votre client.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Si vous avez besoin d’autoriser plusieurs clients, utilisez une virgule pour séparer les noms des clients. Par exemple : 

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Enregistrez et fermez le fichier CustomRules.

Après avoir configuré Fiddler, vous pouvez capturer le trafic en accédant au menu **Fichier** et en sélectionnant **Capturer le trafic**.

### <a name="staged-rollout-of-proxy-settings"></a>Déploiement par étapes des paramètres du proxy

En fonction des capacités de votre infrastructure de proxy, vous pourriez être en mesure d’étalonner le déploiement des paramètres pour vos utilisateurs. Voici deux options principales à prendre en compte :

1. Utilisez des fichiers PAC pour pointer les utilisateurs test vers une infrastructure de proxy test, tandis que les utilisateurs normaux continuent à utiliser l’infrastructure du proxy de production.
2. Certains serveurs proxy peuvent prendre en charge des configurations différentes à l’aide de groupes.

Pour plus d’informations spécifiques, consultez la documentation de votre serveur proxy.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’authentification moderne Office 365 mise à jour](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)
- Consultez les [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
