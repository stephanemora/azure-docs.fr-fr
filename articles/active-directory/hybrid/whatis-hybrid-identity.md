---
title: Connectez Active Directory à Azure Active Directory. | Microsoft Docs
description: Azure AD Connect intègre vos répertoires locaux à Azure Active Directory. Cela vous permet de fournir une identité commune pour les applications Office 365, Azure et SaaS intégrées à Azure AD.
keywords: introduction à Azure AD Connect, présentation d’Azure AD Connect, qu’est-ce qu’Azure AD Connect, installation d’active directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 40ac3ca92c65607df056b883608dde60d816143e
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181772"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>Identité hybride et solutions d’identité Microsoft
Les entreprises et les organisations d’aujourd’hui utilisent de plus en plus communément une combinaison d’applications locales et cloud.  L’obligation de disposer d’applications et d’utilisateurs qui nécessitent un accès à ces applications, aussi bien en local que dans le cloud, constitue désormais un véritable défi.

Les solutions d'identité de Microsoft regroupent des fonctionnalités, locales et cloud, de création d'une identité d'utilisateur unique pour l'authentification et l'autorisation d'accès à toutes les ressources, indépendamment de l'emplacement. Nous appelons cette identité « identité hybride ».

## <a name="what-is-azure-ad-connect"></a>Qu’est-ce qu’Azure AD Connect ?

L’outil Microsoft Azure AD Connect a été conçu pour vous permettre d’atteindre et de remplir vos objectifs en matière d’identité hybride.  Cela vous permet de fournir une identité commune à vos utilisateurs pour les applications Office 365, Azure et SaaS intégrées à Azure AD.  Elle fournit les fonctionnalités suivantes :
    
- [Synchronisation](how-to-connect-sync-whatis.md) : ce composant est chargé de créer des utilisateurs, des groupes et d’autres objets. Il permet également de s’assurer que les informations d’identité relatives aux utilisateurs et aux groupes dans votre environnement local correspondent à celles qui se trouvent dans le cloud.  Ce composant assure la synchronisation des hachages de mot de passe avec Azure AD.
-   [Intégration AD FS et de fédération](how-to-connect-fed-whatis.md) : la fédération est une partie facultative d’Azure AD Connect, qui peut servir à configurer un environnement hybride à l’aide d’une infrastructure AD FS locale. Elle offre également des fonctionnalités de gestion AD FS telles que le renouvellement de certificat et les déploiements de serveurs AD FS supplémentaires.
-   [Authentification directe](how-to-connect-pta.md) : autre composant facultatif qui permet aux utilisateurs d’utiliser le même mot de passe en local et dans le cloud, mais sans nécessiter l’infrastructure supplémentaire d’un environnement fédéré.
-   [Analyse du fonctionnement](whatis-hybrid-identity-health.md) : Azure AD Connect Health peut assurer une supervision robuste et offrir un emplacement central dans le Portail Azure pour la visualisation de cette activité. 


![Qu’est-ce qu’Azure AD Connect ?](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Qu’est-ce qu’Azure AD Connect Health ?

Azure Active Directory (Azure AD) Connect Health vous permet de surveiller et d’analyser votre infrastructure d’identité locale et les services de synchronisation. Il vous permet de maintenir une connexion fiable à Office 365 et à Microsoft Online Services en fournissant des fonctionnalités de surveillance de vos composants d’identification clés tels que les serveurs Active Directory Federation Services (AD FS), les serveurs Azure AD Connect (moteur de synchronisation), les contrôleurs de domaine Active Directory, etc. En outre, les points de données clés sur ces composants sont facilement accessibles, ce qui vous permet d’obtenir des données d’utilisation et d’autres informations importantes pour prendre des décisions avisées.

Ces informations sont toutes présentées dans le [portail Azure AD Connect Health](https://aka.ms/aadconnecthealth). Dans le portail Azure AD Connect Health, vous pouvez afficher les alertes, surveiller les performances, analyser les utilisations, etc. Azure AD Connect Health prend en compte l’intégrité des composants d’identité clé, le tout à un seul endroit.

![Présentation d’Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


Au fur et à mesure que le nombre de fonctionnalités d’Azure AD Connect Health augmente, le portail fournit un tableau de bord unique en fonction de l’identité. Vous obtenez ainsi un environnement encore plus robuste, intégré et sain pour vos utilisateurs, afin d’augmenter leur capacité à effectuer des actions.


## <a name="why-use-azure-ad-connect"></a>Pourquoi utiliser Azure AD Connect ?
L’intégration de vos annuaires locaux avec Azure AD améliore la productivité de vos utilisateurs en leur fournissant une identité commune pour accéder aux ressources cloud et locales. Les utilisateurs et les organisations bénéficient des avantages suivants :

* Les utilisateurs peuvent utiliser une identité unique pour accéder aux applications locales et aux services cloud comme Office 365.
* Outil unique offrant une expérience de déploiement simple pour la synchronisation et la connexion.
* Fournit les fonctionnalités les plus récentes pour vos scénarios. Azure AD Connect remplace les versions antérieures des outils d’intégration d’identité tels que DirSync et Azure AD Sync. Pour plus d’informations, consultez [Identité hybride : Comparaison des outils d’intégration d’annuaire](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Pourquoi utiliser Azure AD Connect Health ?
Lorsque vous intégrez vos répertoires locaux avec Azure AD, vos utilisateurs gagnent en productivité car ils disposent d’une identité commune pour accéder aux ressources cloud et locales. Toutefois, cette intégration nécessite que vous vous assuriez que cet environnement est sécurisé pour que les utilisateurs puissent accéder aux ressources locales et cloud en toute sécurité depuis n’importe quel appareil. Azure AD Connect Health vous aide à surveiller et à obtenir facilement des informations sur l’infrastructure d’identité locale utilisée pour accéder à Office 365 ou à d’autres applications Azure AD. Son installation est aussi simple que celle d’un agent sur chacun de vos serveurs d’identité local.

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[Utilisation d’Azure AD Connect Health pour AD FS](how-to-connect-health-adfs.md)
Azure AD Connect Health pour AD FS prend en charge AD FS 2.0 sur Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 et Windows Server 2016. Cette prise en charge inclut également la surveillance de tous les serveurs proxy AD FS ou serveurs proxy d’application web qui prennent en charge l’authentification pour l’accès extranet. Avec une installation simple et rapide du programme Health Agent, Azure AD Connect Health pour AD FS vous offre un ensemble de fonctionnalités clés.

#### <a name="key-benefits-and-best-practices"></a>Avantages clés et meilleures pratiques

- *Sécurité améliorée*
  - [Tendances de verrouillage extranet](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [Rapport sur les échecs de connexions](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [Conformité aux réglementations en matière de confidentialité](reference-connect-health-user-privacy.md)    
- *Obtention d’alertes pour tous les [problèmes de système ADFS critiques](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)*
    - Configuration et disponibilité de serveur 
    - [Performances et connectivité](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - Maintenance régulière    
- *Facilité de déploiement et de gestion*
  - [Installation d’agent](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) rapide 
  - Mise à niveau automatique d’agent vers la dernière version 
  - Données disponibles dans le portail en quelques minutes    
- *[Métriques d’utilisation](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs) enrichis* 
  - Utilisation des principales applications
  - Emplacements réseau et connexion TCP
  - Demandes de jetons par serveur    
- *Une meilleure expérience utilisateur* 
  - Présentation sous forme de tableaux de bord du Portail Azure
  - [Alertes par e-mail](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>Fonctionnalités clés

*   Analyse des alertes pour savoir quand les serveurs AD FS et proxy AD FS ne sont pas sains
*   Notifications par courrier électronique pour les alertes critiques
*   Tendances des données de performance, utiles pour la planification des capacités d’AD FS
*   Analyse de l’utilisation des connexions AD FS avec tableaux croisés dynamiques (applications, utilisateurs, emplacement réseau, etc.)
*   Rapports AD FS comme le Top 50 des utilisateurs avec des tentatives ayant échoué en raison de noms d’utilisateur/mots de passe incorrects et leur dernière adresse IP
*   Rapport sur les adresses IP à risque pour les connexions AD FS ayant échoué

Pour en savoir plus sur l’[Utilisation d’Azure AD Connect Health avec AD FS](how-to-connect-health-adfs.md)

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[Azure AD Connect Health pour la synchronisation](how-to-connect-health-sync.md)
Azure AD Connect Health pour la synchronisation surveille et fournit des informations sur les synchronisations effectuées entre votre instance Active Directory locale et Azure AD. Azure AD Connect Health pour la synchronisation fournit les fonctionnalités clés suivantes :

* Analyse grâce à des alertes, pour savoir quand un serveur Azure AD Connect (moteur de synchronisation) n’est pas intègre
* Notifications par courrier électronique pour les alertes critiques
* Analyse opérationnelle de synchronisation, notamment les graphiques de latence pour les opérations de synchronisation et les tendances dans différentes opérations, en particulier les ajouts, les mises à jour, les suppressions
* Rapide aperçu des informations sur les propriétés de synchronisation et la dernière réussite de l’exportation vers Azure AD
* Rapports sur les erreurs de synchronisation de niveau objet \(ne nécessite pas Azure AD Premium\)

Pour en savoir plus sur l’[Utilisation d’Azure AD Connect Health pour la synchronisation](how-to-connect-health-sync.md)

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[Utilisation d’Azure AD Connect Health pour AD DS](how-to-connect-health-adds.md)
Azure AD Connect Health pour Active Directory Domain Service (AD DS) permet de surveiller les contrôleurs de domaine installés sur Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 et Windows Server 2016. L’installation de l’agent d’intégrité vous permet de surveiller votre environnement local AD DS à partir du cloud. Azure AD Connect Health pour AD DS fournit les fonctionnalités clé suivantes :

* Alertes de surveillance pour détecter quand les contrôleurs de domaine sont défectueux et notifications par e-mail pour les alertes critiques
* Tableau de bord des contrôleurs de domaine qui fournit un aperçu rapide de l’intégrité et de l’état de fonctionnement de vos contrôleurs de domaine
* Tableau de bord d’état de réplication fournissant les dernières informations de réplication, ainsi que des liens vers des guides de dépannage lorsque des erreurs sont détectées
* Accès rapide n’importe où aux graphiques de données de compteurs de performances populaires, nécessaires pour la résolution des problèmes et à des fins de surveillance

Pour en savoir plus sur l’[Utilisation d’Azure AD Connect Health avec AD DS](how-to-connect-health-adds.md)

## <a name="next-steps"></a>Étapes suivantes


- [Matériel et conditions préalables](how-to-connect-install-prerequisites.md) 
- [Paramètres Express](how-to-connect-install-express.md)
- [Paramètres personnalisés](how-to-connect-install-custom.md)
- [Synchronisation de hachage de mot de passe](how-to-connect-password-hash-synchronization.md)|
- [Authentification directe](how-to-connect-pta.md)
- [Fédération avec Azure AD Connect](how-to-connect-fed-whatis.md)
- [Installation des agents Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Synchronisation d’Azure AD Connect](how-to-connect-sync-whatis.md)
- [Historique des versions](reference-connect-version-history.md)
- [Comparaison des outils d’intégration de répertoire](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [FAQ Azure AD Connect](reference-connect-faq.md)









