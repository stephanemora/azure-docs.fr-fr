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
ms.date: 11/02/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2aca42c23cc213d5d7e451105052d5d5d697b77d
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979469"
---
# <a name="hybrid-identity-and-microsoft-identity-solutions"></a>Identité hybride et solutions d’identité Microsoft
Les solutions d’identité hybride [Microsoft Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) permettent de synchroniser des objets de répertoire locaux avec Azure AD tout en gérant vos utilisateurs locaux. La première décision à prendre lorsque vous envisagez de synchroniser votre Active Directory Windows Server local avec Azure AD est de savoir si vous souhaitez utiliser des identités managées ou une identité fédérée. 

- **Identités managées** : comptes d’utilisateurs et groupes qui sont synchronisés à partir d’un annuaire local Active Directory, avec authentification utilisateur gérée par Azure.   
- Les **identités fédérées** permettent de mieux contrôler les utilisateurs en séparant l’authentification utilisateur d’Azure et en déléguant l’authentification à un fournisseur d’identité approuvé et local. 

Plusieurs options sont disponibles pour la configuration d’identités hybrides. Lorsque vous considérez le modèle d’identité le mieux adapté aux besoins de votre organisation, vous devez également prendre en compte les délais, l’infrastructure existante, la complexité et le coût. Ces facteurs sont différents pour chaque organisation et peuvent changer au fil du temps. Toutefois, si vos exigences changent, vous avez la possibilité de basculer vers un autre modèle d’identité.

## <a name="managed-identity"></a>Identité managée 

L’identité managée constitue la façon la plus simple de synchroniser des objets de répertoire locaux (utilisateurs et groupes) avec Azure AD. 

![Identité hybride synchronisée](./media/whatis-hybrid-identity/managed.png)

Bien que l’identité managée soit la méthode la plus rapide et la plus facile, vos utilisateurs doivent cependant conserver un mot de passe distinct pour les ressources de cloud. Pour éviter ce problème, vous pouvez également (en option) [synchroniser un hachage du mot de passe utilisateur](how-to-connect-password-hash-synchronization.md) à votre répertoire Azure AD. La synchronisation des hachages du mot de passe permet aux utilisateurs de se connecter à des ressources d’entreprise sur le cloud avec les mêmes nom d’utilisateur et mot de passe utilisés localement. Azure AD Connect contrôle régulièrement les modifications apportées à votre répertoire local pour le synchroniser dans votre répertoire Azure AD. Si un attribut utilisateur ou un mot de passe est modifié localement dans Active Directory, il est automatiquement mis à jour dans Azure AD. 

Pour la plupart des organisations qui doivent uniquement permettre à leurs utilisateurs de se connecter à Office 365, aux applications Saas et aux autres ressources Azure AD, l’option de synchronisation de hachage de mot de passe par défaut est recommandée. Si cela ne fonctionne pas pour vous, vous devrez décider entre l’authentification directe et AD FS.

> [!TIP]
> Les mots de passe utilisateur sont stockés dans Active Directory Windows Server local, sous la forme d’une valeur de hachage qui représente le mot de passe utilisateur réel. Une valeur de hachage est le résultat d’une fonction mathématique unidirectionnelle (l’algorithme de hachage). Il n’existe aucune méthode pour retrouver la version en texte brut du mot de passe à partir du résultat d’une fonction unidirectionnelle. Vous ne pouvez pas utiliser un hachage de mot de passe pour vous connecter à votre réseau local. Lorsque vous choisissez de synchroniser les mots de passe, Azure AD Connect extrait les hachages du mot de passe à partir d’Active Directory local et applique le traitement de sécurité supplémentaire au hachage du mot de passe avant sa synchronisation avec Azure AD. La synchronisation de hachage de mot de passe peut être utilisée également avec la réécriture du mot de passe pour activer la réinitialisation du mot de passe libre-service dans Azure AD. En outre, vous pouvez activer également l’authentification unique (SSO) pour des utilisateurs sur des ordinateurs joints à un domaine connectés au réseau d’entreprise. Avec l’authentification unique, les utilisateurs activés doivent seulement entrer un nom d’utilisateur pour accéder en toute sécurité aux ressources du cloud. 
>

## <a name="pass-through-authentication"></a>Authentification directe

[L’authentification directe Azure AD](how-to-connect-pta.md) fournit une solution de validation du mot de passe simple pour les services Azure AD à l’aide de votre répertoire Active Directory local. Si les stratégies de sécurité et de conformité pour votre organisation n’autorisent pas l’envoi des mots de passe utilisateurs, même dans un format haché et que vous devez uniquement prendre en charge l’authentification unique de bureau pour les appareils joints à un domaine, l’évaluation doit être effectuée via l’authentification directe. L’authentification directe ne nécessite aucun déploiement dans le réseau de périmètre, ce qui simplifie l’infrastructure de déploiement lors de la comparaison avec AD FS. Lorsque les utilisateurs se connectent à l’aide d’Azure AD, cette méthode d’authentification valide les mots de passe utilisateurs directement à partir d’Active Directory local.

![Authentification directe](./media/whatis-hybrid-identity/pass-through-authentication.png)

Avec l’authentification directe, une infrastructure réseau complexe est inutile et vous n’avez pas besoin de stocker les mots de passe locaux dans le cloud. Combiné avec l’authentification unique, l’authentification directe fournit une expérience entièrement intégrée lors de la connexion à Azure AD ou d’autres services cloud.

L’authentification directe peut être configurée par le biais d’Azure AD Connect. Elle utilise un agent local simple qui écoute les requêtes de validation du mot de passe. L’agent peut facilement être déployé sur plusieurs ordinateurs afin de fournir une haute disponibilité et un équilibrage de charge. Étant donné que toutes les communications sont uniquement sortantes, il n’est pas obligatoire d’installer le connecteur dans une zone DMZ. La configuration requise du serveur pour le connecteur est la suivante :

- Windows Server 2012 R2 ou version ultérieure
- Joint à un domaine dans la forêt dans laquelle les utilisateurs sont validés

## <a name="federated-identity-ad-fs"></a>Identité fédérée (AD FS)

Pour obtenir un meilleur contrôle sur le mode d’accès des utilisateurs à Office 365 et autres services cloud, vous pouvez configurer la synchronisation de répertoires avec authentification unique (SSO) à l’aide de [Active Directory Federation Services (AD FS)](how-to-connect-fed-whatis.md). La fédération des connexions utilisateurs avec AD FS délègue l’authentification à un serveur local qui valide les informations d’identification de l’utilisateur. Dans ce modèle, les informations d’identification Active Directory locales ne sont jamais passées à Azure AD.

![Identité fédérée](./media/whatis-hybrid-identity/federated-identity.png)

Également appelée fédération des identités, cette méthode de connexion garantit que toutes les authentifications utilisateur sont contrôlées localement et permet aux administrateurs de mettre en œuvre des niveaux de contrôle d’accès plus rigoureux. La fédération des identités avec AD FS est l’option la plus complexe qui nécessite le déploiement de serveurs supplémentaires dans votre environnement local. La fédération d’identité vous engage également à fournir une assistance 24 h/24 et 7 j/7 pour votre infrastructure Active Directory et AD FS. Ce niveau élevé d’assistance est nécessaire, car en cas de non-disponibilité de votre accès à l’Internet local, du contrôleur de domaine ou des serveurs AD FS, les utilisateurs ne peuvent pas se connecter aux services cloud.

> [!TIP]
> Si vous choisissez d’utiliser la Fédération avec Active Directory Federation Services (AD FS), vous avez la possibilité de configurer la synchronisation de hachage de mot de passe en tant que dispositif de secours en cas de défaillance de votre infrastructure AD FS.
>

## <a name="common-scenarios-and-recommendations"></a>Scénarios et recommandations courants

Voici quelques scénarios courants de gestion des identités hybrides et des accès avec des recommandations concernant l’option (ou les options) d’identité hybride la ou les mieux appropriées dans chaque cas.

|J’ai besoin de :|PHS et SSO<sup>1</sup>| PTA et SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Synchroniser de nouveaux comptes d’utilisateurs, de contacts et de groupes créés automatiquement dans mon Active Directory local vers le cloud.|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| ![Recommandé](./media/whatis-hybrid-identity/ic195031.png) |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|
|Configurer mon client pour des scénarios hybrides Office 365|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| ![Recommandé](./media/whatis-hybrid-identity/ic195031.png) |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|
|Permettre à mes utilisateurs de se connecter et d’accéder aux services cloud à l’aide de leur mot de passe local|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| ![Recommandé](./media/whatis-hybrid-identity/ic195031.png) |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|
|Implémenter l’authentification unique à l’aide des informations d’identification d’entreprise|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)| ![Recommandé](./media/whatis-hybrid-identity/ic195031.png) |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|
|M’assurer qu’aucun hachage du mot de passe n’est stocké dans le cloud| |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|
|Activer des solutions d’authentification multifacteur cloud| |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|
|Activer des solutions d’authentification multifacteur locales| | |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|
|Prendre en charge l’authentification par carte à puce pour mes utilisateurs<sup>4</sup>| | |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|
|Afficher les notifications d’expiration du mot de passe dans le portail Office et sur le bureau Windows 10| | |![Recommandé](./media/whatis-hybrid-identity/ic195031.png)|

> <sup>1</sup> Synchronisation de hachage de mot de passe avec authentification unique (SSO).
>
> <sup>2</sup> Authentification directe et authentification unique. 
>
> <sup>3</sup> Authentification unique fédérée avec AD FS.
>
> <sup>4</sup> AD FS peut être intégré à l’infrastructure de clé publique (PKI) de votre entreprise pour permettre l’authentification à l’aide de certificats. Ces certificats peuvent être des certificats logiciels déployés via des canaux d’approvisionnement approuvés tels que les certificats de gestion des périphériques mobiles (GPM), d’objet de stratégie de groupe (GPO), de carte à puce (y compris les cartes PIV/CAC) ou Hello for Business (approbation de certificat). Pour plus d’informations sur la prise en charge de l’authentification par carte à puce, consultez [ce  blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).
>

## <a name="what-is-azure-ad-connect"></a>Qu’est-ce qu’Azure AD Connect ?

L’outil Microsoft Azure AD Connect a été conçu pour vous permettre d’atteindre et de remplir vos objectifs en matière d’identité hybride.  Cela vous permet de fournir une identité commune à vos utilisateurs pour les applications Office 365, Azure et SaaS intégrées à Azure AD.  Elle fournit les fonctionnalités suivantes :
    
- [Synchronisation](how-to-connect-sync-whatis.md) : ce composant est chargé de créer des utilisateurs, des groupes et d’autres objets. Il permet également de s’assurer que les informations d’identité relatives aux utilisateurs et aux groupes dans votre environnement local correspondent à celles qui se trouvent dans le cloud.  Ce composant assure la synchronisation des hachages de mot de passe avec Azure AD.
- [Synchronisation du hachage de mot de passe](how-to-connect-password-hash-synchronization.md) : composant facultatif qui permet aux utilisateurs d’utiliser le même mot de passe en local et dans le cloud en synchronisant un hachage du mot de passe utilisateur avec Azure AD.
-   [Intégration AD FS et de fédération](how-to-connect-fed-whatis.md) : la fédération est une partie facultative d’Azure AD Connect, qui peut servir à configurer un environnement hybride à l’aide d’une infrastructure AD FS locale. Elle offre également des fonctionnalités de gestion AD FS telles que le renouvellement de certificat et les déploiements de serveurs AD FS supplémentaires.
-   [Authentification directe](how-to-connect-pta.md) : autre composant facultatif qui permet aux utilisateurs d’utiliser le même mot de passe en local et dans le cloud, mais sans nécessiter l’infrastructure supplémentaire d’un environnement fédéré.
-   [Intégration de PingFederate avec la fédération](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) : autre option de fédération qui vous permet d’utiliser PingFederate en tant que fournisseur d’identité.
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









