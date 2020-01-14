---
title: Prérequis pour le provisionnement cloud Azure AD Connect dans Azure AD
description: Cet article décrit les conditions préalables et la configuration matérielle requise pour l’approvisionnement du cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 019c2f8a13a80de395803ca1782e578c049a7923
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549344"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Prérequis pour le provisionnement cloud Azure AD Connect
Cet article fournit des conseils sur la façon de choisir et d’utiliser l’approvisionnement cloud Azure Active Directory (Azure AD) Connect en tant que solution d’identité.



## <a name="cloud-provisioning-agent-requirements"></a>Conditions requises de l’agent de provisionnement cloud
Vous avez besoin des éléments suivants pour utiliser le provisionnement cloud Azure AD Connect :
    
- Un compte d’administrateur général pour votre locataire Azure AD.
- Un serveur local pour l’agent de provisionnement avec Windows 2012 R2 ou ultérieur.
- Des configurations de pare-feu locales.

Le reste du document fournit des instructions détaillées pour ces prérequis.

### <a name="in-the-azure-active-directory-admin-center"></a>Dans le Centre d’administration Azure Active Directory

1. Créez un compte d’administrateur général « cloud uniquement » dans votre locataire Azure AD. De cette façon, vous pouvez gérer la configuration de votre locataire si vos services locaux venaient à échouer ou ne plus être disponibles. Découvrez comment [ajouter un compte d’administrateur général de type cloud uniquement](../active-directory-users-create-azure-portal.md). Cette étape est essentielle si vous voulez éviter de vous retrouver en dehors de votre locataire.
1. Ajoutez un ou plusieurs [noms de domaine personnalisés](../active-directory-domains-add-azure-portal.md) à votre locataire Azure AD. Vos utilisateurs peuvent se connecter à l’aide de l’un de ces noms de domaine.

### <a name="in-your-on-premises-environment"></a>Dans votre environnement local

1. Identifiez un serveur hôte joint à un domaine exécutant Windows Server 2012 R2 ou ultérieur, avec au minimum 4 Go de RAM et .NET 4.7.1 + Runtime.

1. S’il existe un pare-feu entre vos serveurs et Azure AD, configurez les éléments suivants :
   - Assurez-vous que les agents peuvent effectuer des requêtes *sortantes* sur Azure AD sur les ports suivants :

        | Numéro de port | Utilisation |
        | --- | --- |
        | **80** | Télécharge les listes de révocation de certificats lors de la validation du certificat SSL.  |
        | **443** | Gère toutes les communications sortantes avec le service. |
        | **8080** (facultatif) | Les agents signalent leur état toutes les 10 minutes sur le port 8080, si le port 443 n’est pas disponible. Cet état est affiché sur le portail Azure AD. |
     
   - Si votre pare-feu applique les règles en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau.
   - Si votre pare-feu ou proxy vous permet de spécifier des suffixes approuvés, ajoutez des connexions à \*.msappproxy.net et \*.servicebus.windows.net. Dans le cas contraire, autorisez l’accès aux [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653), qui sont mises à jour chaque semaine.
   - Vos agents doivent accéder à login.windows.net et à login.microsoftonline.com pour l’inscription initiale. Par conséquent, ouvrez également votre pare-feu pour ces URL.
   - Pour valider le certificat, débloquez les URL suivantes : mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 et www\.microsoft.com:80. Ces URL sont utilisées pour la validation de certificat avec d’autres produits Microsoft : elles seront peut-être déjà débloquées.

### <a name="verify-the-port"></a>Vérifier le port
Pour vérifier que le service Azure est à l’écoute sur le port 443, et que votre agent peut communiquer avec lui, utilisez l’URL suivant :

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ce test vérifie que vos agents peuvent communiquer avec Azure via le port 443. Ouvrez un navigateur et accédez à l’URL précédents à partir du serveur sur lequel l’agent est installé.

![Vérification de l’accessibilité du port](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Autres conditions requises
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Exigences relatives à TLS

>[!NOTE]
>Le protocole TLS (Transport Layer Security) est un protocole qui fournit des communications sécurisées. La modification des paramètres TLS affecte l’ensemble de la forêt. Pour plus d’informations, consultez [Mise à jour pour activer TLS 1.1 et TLS 1.2 en tant que protocoles sécurisés par défaut dans WinHTTP sur Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

Le protocole TLS 1.2 doit être activé sur le serveur Windows qui hébergera l’agent de provisionnement cloud Azure AD Connect avant son installation.

Pour activer TLS 1.2, procédez comme suit.

1. Définissez les clés de Registre suivantes :
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Redémarrez le serveur.


## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Présentation du provisionnement cloud Azure AD Connect](what-is-cloud-provisioning.md)

