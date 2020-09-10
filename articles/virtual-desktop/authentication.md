---
title: Authentification Windows Virtual Desktop – Azure
description: Méthodes d’authentification pour Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8328fa9c9e32a22e8e5093aa343f333cd1914ddc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89026088"
---
# <a name="supported-authentication-methods"></a>Méthodes d’authentification prises en charge

Dans cet article, nous vous fournirons un aperçu des types d’authentification que vous pouvez utiliser dans Windows Virtual Desktop.

## <a name="session-host-authentication"></a>Authentification de l’hôte de session

Windows Virtual Desktop prend en charge NT LAN Manager (NTLM) et Kerberos pour l’authentification de l’hôte de session. Toutefois, pour utiliser Kerberos, le client doit obtenir des tickets de sécurité Kerberos auprès d’un service de centre de distribution de clés (KDC, Key Distribution Center) fonctionnant sur un contrôleur de domaine. Pour obtenir des tickets, le client a besoin d’une ligne de vue directe sur le contrôleur de domaine. Vous pouvez obtenir une ligne de vue directe en utilisant le réseau de votre entreprise. Vous pouvez également utiliser une connexion VPN qui vous relie au réseau de votre entreprise.

Voici les méthodes de connexion actuellement prises en charge :

- Client Windows Desktop
    - Nom d’utilisateur et mot de passe
    - Carte à puce
    - Windows Hello
- Client Windows Store
    - Nom d’utilisateur et mot de passe
- Client web
    - Nom d’utilisateur et mot de passe
- Android
    - Nom d’utilisateur et mot de passe
- iOS
    - Nom d’utilisateur et mot de passe
- macOS
    - Nom d’utilisateur et mot de passe

>[!NOTE]
>La carte à puce et Windows Hello peuvent uniquement utiliser Kerberos pour se connecter. La connexion avec Kerberos nécessite une ligne de vue sur le contrôleur de domaine.

## <a name="single-sign-on-sso"></a>Authentification unique (SSO)

Windows Virtual Desktop ne prend actuellement pas en charge les services de fédération Active Directory (AD FS) pour l’authentification ou l’authentification unique.

La seule façon de ne pas être invité à entrer vos informations d’identification pour l’hôte de la session consiste à les enregistrer dans le client. Nous vous recommandons de le faire uniquement avec des appareils sécurisés pour empêcher d’autres utilisateurs d’accéder à vos ressources.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes curieux de connaître d’autres moyens de sécuriser votre déploiement ? Consultez les [meilleures pratiques relatives à la sécurité](security-guide.md).
