---
title: 'Activation du Proxy d’application Azure AD : Historique de publication des versions | Microsoft Docs'
description: Cet article répertorie toutes les versions de Proxy d’Application Azure AD et décrit les nouvelles fonctionnalités et résolution des problèmes
services: active-directory
documentationcenter: ''
author: msmimart
manager: celested
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: manage-apps
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf9ee43c6c6b332c05286da8e330812d7e0db6c2
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578587"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Activation du Proxy d’application Azure AD : Historique de publication des versions
Cet article répertorie les versions et les fonctionnalités de Proxy d’Application Azure Active Directory (Azure AD) qui ont été publiées. L’équipe Azure AD met régulièrement à jour le Proxy d’Application avec les nouvelles fonctionnalités. Connecteurs de Proxy d’application sont automatiquement mis à jour lorsqu’une nouvelle version est publiée.

Voici une liste de ressources associées :

Ressource |  Détails
--------- | --------- |
Comment activer le Proxy d’Application | Conditions préalables pour l’activation du Proxy d’Application et l’installation et l’inscription d’un connecteur sont décrites dans ce [didacticiel](application-proxy-add-on-premises-application.md).
Présentation des connecteurs de proxy d’application Azure AD | Apprenez-en davantage sur [gestion du connecteur](application-proxy-connectors.md) et comment les connecteurs [mise à niveau automatique](application-proxy-connectors.md#automatic-updates).
Télécharger le connecteur de Proxy d’Application Azure AD |  [Télécharger la dernière version connector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>État de la version

20 septembre 2018 : publiée pour téléchargement

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

- Prise en charge de WebSocket ajoutée pour l’application QlikSense. Pour en savoir plus sur comment intégrer QlikSense avec le Proxy d’Application, consultez ce [procédure pas à pas](application-proxy-qlik.md). 
- Amélioration de l’Assistant installation pour le rendre plus facile à configurer un proxy sortant. 
- Définissez TLS 1.2 en tant que le protocole par défaut pour les connecteurs. 
- Ajouter un nouveau contrat de licence de l’utilisateur final (CLUF).  

### <a name="fixed-issues"></a>Problèmes résolus

- Correction d’un bogue qui entraînait des fuites de mémoire dans le connecteur.
- Mise à jour de la version d’Azure Service Bus, qui inclut un correctif de bogue pour les problèmes de délai d’attente du connecteur.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>État de la version

19 janvier 2018 : publiée pour téléchargement

### <a name="fixed-issues"></a>Problèmes résolus

- Prise en charge pour les domaines personnalisés nécessitant une traduction de domaine dans le cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>État de la version 

25 mai 2017 : publiée pour téléchargement 

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités 

Contrôle amélioré sur les limites de connexion sortante de connecteurs. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>État de la version

15 avril 2017 : publiée pour téléchargement

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

- Intégration simplifiée et la gestion avec moins de ports requis. Le Proxy d’application requiert désormais l’ouverture de seulement deux ports de sortie standards : 443 et 80. Le Proxy d’application continue d’utiliser des connexions sortantes uniquement, vous devez donc encore ne tous les composants dans une zone DMZ. Pour plus d’informations, consultez notre [documentation de configuration](application-proxy-add-on-premises-application.md).  
- Si la prise en charge par votre pare-feu ou proxy externe, vous pouvez maintenant ouvrir votre réseau par le système DNS au lieu de la plage d’adresses IP. Services de Proxy d’application requièrent des connexions à *. msappproxy.net et *. servicebus.windows.net uniquement.


## <a name="earlier-versions"></a>Versions antérieures

Si vous utilisez une version de connecteur de Proxy d’Application antérieure à 1.5.36.0, mettre à jour vers la dernière version pour vous assurer les dernières fonctionnalités entièrement prises en charge.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [d’accès à distance pour les applications locales via le Proxy d’Application AD Azure](application-proxy.md).
- Pour commencer à utiliser Proxy d’application, consultez [Tutoriel : Ajouter des applications locales pour un accès à distance via le service Proxy d’application](application-proxy-add-on-premises-application.md).
