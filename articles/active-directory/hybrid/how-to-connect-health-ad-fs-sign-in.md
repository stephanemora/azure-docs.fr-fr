---
title: Connexions AD FS dans Azure AD avec Connect Health | Microsoft Docs
description: Ce document décrit comment intégrer les connexions AD FS à l’aide du rapport de connexions Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 035215bf9350243f667c4883786a923a172cb89c
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108126558"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>Connexions AD FS dans Azure AD avec Connect Health - préversion

Les connexions AD FS peuvent désormais être intégrées dans le rapport de connexions Azure Active Directory à l’aide de Connect Health. Le [rapport Connexions Azure AD](../reports-monitoring/concept-all-sign-ins.md) contient des informations sur le moment où les utilisateurs, les applications et les ressources managées se connectent à Azure AD et accèdent aux ressources. 

L’agent Connect Health pour AD FS met en corrélation plusieurs ID d’événements à partir d’AD FS, en fonction de la version du serveur, afin de fournir des informations sur la requête et les détails de l’erreur en cas d’échec de la requête. Ces informations sont corrélées avec le schéma de rapport de connexion Azure AD et affichées dans l’expérience utilisateur de rapport de connexion Azure AD. En plus du rapport, un nouveau flux Log Analytics est disponible avec les données d’AD FS et un nouveau modèle de classeur Azure Monitor. Le modèle peut être utilisé et modifié pour une analyse approfondie des scénarios tels que les verrouillages de compte AD FS, les tentatives de mot de passe incorrectes et les pics de tentatives de connexion inattendues.

## <a name="prerequisites"></a>Prérequis
* Azure AD Connect Health pour AD FS, installé et mis à niveau vers la dernière version.
* Rôle de lecteur de rapports ou d’administrateur général pour afficher les connexions Azure AD

## <a name="what-data-is-displayed-in-the-report"></a>Quelles données sont affichées dans le rapport ?
Les données disponibles reflètent les mêmes données disponibles pour les connexions Azure AD. Cinq onglets contenant des informations seront disponibles en fonction du type de connexion, Azure AD ou AD FS. Connect Health met en corrélation les événements d’AD FS, en fonction de la version du serveur, et les met en correspondance avec le schéma AD FS. 



#### <a name="user-sign-ins"></a>Connexions utilisateur 
Chaque onglet du panneau des connexions affiche les valeurs par défaut ci-dessous :
* Date de la connexion
* ID de la demande
* Nom ou ID de l’utilisateur
* État de la connexion
* Adresse IP de l’appareil utilisé pour la connexion
* Identificateur de connexion

#### <a name="authentication-method-information"></a>Informations sur la méthode d’authentification
Les valeurs suivantes peuvent être affichées dans l’onglet Authentification. La méthode d’authentification est tirée des journaux d’audit AD FS.

|Méthode d'authentification|Description|
|-----|-----|
|Formulaires|Authentification par nom d’utilisateur/mot de passe|
|Windows|Authentification Windows intégrée|
|Certificat|Authentification avec des certificats SmartCard/VirtualSmart|
|WindowsHelloForBusiness|Ce champ est destiné à l’authentification avec Windows Hello Entreprise. (Authentification Microsoft Passport)|
|Périphérique | S’affiche si l’authentification de l’appareil est sélectionnée comme l’authentification « principale » à partir de l’intranet/extranet et que l’authentification de l’appareil est effectuée.  Il n’y a pas d’authentification utilisateur distincte dans ce scénario.| 
|Adresses IP fédérées|AD FS n’a pas effectué l’authentification, mais l’a envoyée à un fournisseur d’identité tiers|
|SSO |Si un jeton d’authentification unique a été utilisé, ce champ s’affiche. Si l’authentification unique a une authentification multifacteur, elle s’affiche comme multifacteur|
|Multifacteur|Si un jeton d’authentification unique a une authentification multifacteur et qu’elle a été utilisée pour l’authentification, ce champ s’affiche en tant que multifacteur|
|Azure MFA|Azure MFA est sélectionné en tant que fournisseur d’authentification supplémentaire dans AD FS et a été utilisé pour l’authentification|
|ADFSExternalAuthenticationProvider|Ce champ est utilisé si un fournisseur d’authentification tiers a été inscrit et utilisé pour l’authentification|


#### <a name="ad-fs-additional-details"></a>Informations supplémentaires sur AD FS
Les informations suivantes sont disponibles pour les connexions AD FS :
* Nom du serveur
* Chaîne IP
* Protocol

### <a name="enabling-log-analytics-and-azure-monitor"></a>Activation de Log Analytics et d’Azure Monitor
Log Analytics peut être activé pour les connexions AD FS et être utilisé avec n’importe quel autre composant intégré à Log Analytics, comme Sentinel.

> [!NOTE] 
> Les connexions AD FS peuvent augmenter considérablement les frais de Log Analytics, selon la quantité de connexions à AD FS dans votre organisation. Pour activer et désactiver Log Analytics, sélectionnez la case à cocher du flux.

Pour activer Log Analytics pour cette fonctionnalité, accédez au panneau Log Analytics et sélectionnez le flux « ADFSSignIns ». Cette sélection permet aux connexions AD FS d’être sont acheminées vers Log Analytics.

Pour accéder au modèle de classeur Azure Monitor mis à jour, accédez à « Modèles Azure Monitor », puis sélectionnez le classeur « Connexions ».
Pour plus d’informations sur les classeurs, consultez [Classeurs Azure Monitor](https://aka.ms/adfssigninspreview).




### <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)
***Quels sont les types de connexions que je suis susceptible de voir ?***
Le rapport de connexion prend en charge les connexions via les protocoles O-Auth, WS-Fed, SAML et WS-Trust. 

***Comment les différents types de connexion sont-ils affichés dans le rapport de connexion ?***
Si une connexion unique transparente est effectuée, il y aura une ligne pour la connexion avec un ID de corrélation.
Si une authentification à facteur unique est effectuée, deux lignes sont remplies avec le même ID de corrélation, mais avec deux méthodes d’authentification différentes (par exemple formulaires, authentification unique).
Dans le cas de l’authentification multifacteur, il y aura trois lignes avec un ID de corrélation partagé et trois méthodes d’authentification correspondantes (par exemple formulaires, AzureMFA, multifacteur). Dans cet exemple particulier, le multifacteur dans ce cas indique que l’authentification unique a une authentification multifacteur.

***Quels sont les types d’erreur que je suis susceptible de voir ?***
Pour obtenir la liste complète des erreurs liées à AD FS qui sont renseignées dans le rapport et les descriptions de connexion, consultez la page [Référence de code d’erreur de l’aide d’AD FS](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference)

***Je vois « 00000000-0000-0000-0000-000000000000 » dans la section « Utilisateur » d’une connexion. Qu’est-ce que cela signifie ?***
Si la connexion a échoué et que l’UPN ne correspond pas à un UPN existant, les champs « Utilisateur », « Nom d’utilisateur » et « ID utilisateur » seront « 00000000-0000-0000-0000-000000000000 » et « Identificateur de connexion » sera renseigné avec la valeur saisie par l’utilisateur. Dans ce cas, l’utilisateur qui tente de se connecter n’existe pas.

***Comment puis-je mettre en corrélation mes événements locaux avec le rapport de connexion Azure AD ?***
L’agent Azure AD Connect Health pour AD FS met en corrélation les ID d’événement d’AD FS en fonction de la version du serveur. Les événements sont disponibles dans le journal de sécurité des serveurs AD FS. 

***Pourquoi est-ce que je vois NotSet ou NotApplicable dans l’ID/le nom de l’application pour certaines connexions AD FS ?***
Le rapport de connexion AD FS affichera les identifiants OAuth dans le champ ID d’application pour les connexions OAuth. Dans les scénarios de connexion WS-Fed, WS-Trust, l’ID d’application sera NotSet ou NotApplicable et les ID de ressource et les identificateurs de partie de confiance seront présents dans le champ ID de ressource.

***Y a-t-il d’autres problèmes connus avec le rapport dans la préversion ?***
Le rapport présente un problème connu où le champ « Condition d’authentification » de l’onglet « Informations de base » est renseigné comme une valeur d’authentification à facteur unique pour les connexions AD FS, quelle que soit la connexion. En outre, l’onglet Détails de l’authentification affiche « Principal ou secondaire » dans le champ Exigence, avec un correctif en cours pour différencier les types d’authentification principal et secondaire.


## <a name="related-links"></a>Liens connexes
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installation de l’agent Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Rapport de l’adresse IP risquée](how-to-connect-health-adfs-risky-ip.md)