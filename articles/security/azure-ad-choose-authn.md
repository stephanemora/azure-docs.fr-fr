---
title: Choisir la méthode d’authentification adaptée à votre solution d’identité hybride Azure AD | Microsoft Docs
description: Ce guide s’adresse aux directeurs d’entreprise, directeurs des systèmes d’information, responsables de la sécurité des systèmes d’information, architectes en chef de la gestion des identités, architectes d’entreprise et autres décideurs dans les domaines de la sécurité et de l’informatique en charge de choisir une méthode d’authentification pour leur solution d’identité hybride Azure AD, dans les moyennes et grandes entreprises.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 102fa06be3734fa6993616f752922433ee0dee7f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Choisir la méthode d’authentification adaptée à votre solution d’identité hybride Azure Active Directory 

Cet article est le premier d’une série visant à aider les organisations à implémenter une solution d’identité hybride Azure AD complète. Une telle solution, présentée dans le cadre du « Hybrid Identity Digital Transformation Framework », traite des projections commerciales (business outcomes) et des objectifs sur lesquels les organisations doivent se concentrer pour implémenter une solution d’identité hybride fiable et sécurisée. La première projection commerciale du framework énonce la nécessité pour les organisations de sécuriser le processus d’authentification utilisé par les utilisateurs pour accéder à des applications cloud. Le premier objectif commercial de la projection d’une authentification sécurisée est la possibilité pour les utilisateurs de se connecter à des applications cloud avec leurs nom d’utilisateur et mot de passe locaux. Ce processus de connexion et la façon dont les utilisateurs s’authentifient ouvrent les portes du cloud.

La première étape pour les organisations souhaitant migrer leurs applications vers le cloud est de choisir une méthode d’authentification appropriée. Cette décision ne doit pas être prise à la légère, et ce pour les raisons suivantes :

1. Il s’agit de la première décision que doit prendre une organisation souhaitant migrer vers le cloud. 

2. La méthode d’authentification, qui contrôle l’accès à l’ensemble des données et des ressources du cloud, est un composant essentiel de la présence d’une organisation dans le cloud.

3. Elle constitue le fondement sur lequel reposent toutes les autres fonctionnalités avancées en matière de sécurité et d’expérience utilisateur dans Azure AD.

4. Il est difficile de changer de méthode d’authentification une fois celle-ci implémentée.

L’identité constituant désormais le nouveau plan de contrôle en matière de sécurité informatique, l’authentification permet aux organisations de gérer les accès au cloud. Les organisations doivent veiller à ce que le plan de contrôle d’identité renforce leur sécurité et protège leurs applications cloud contre les intrus.

### <a name="out-of-scope"></a>Hors propos

Les organisations qui ne présentent aucune empreinte d’un annuaire local existant ne sont pas concernées par cet article. En général, ces entreprises créent uniquement des identités résidant dans le cloud, ce qui n’exige pas une solution d’identité hybride. Les identités résidant uniquement dans le cloud ne sont pas associées à des identités locales correspondantes.  

## <a name="choosing-the-right-authentication-method"></a>Choix de la méthode d’authentification appropriée

Avec la solution d’identité hybride Azure AD comme nouveau plan de contrôle, l’authentification constitue la base de l’accès au cloud. Le choix de la méthode d’authentification est une première décision essentielle dans la configuration d’une solution d’identité hybride Azure AD. L’implémentation de la méthode d’authentification est configurée à l’aide d’Azure AD Connect, qui provisionne également les utilisateurs dans le cloud. 

Pour choisir une méthode d’authentification, vous devez prendre en compte l’infrastructure existante, le temps nécessaire à l’implémentation, sa complexité et les coûts associés. Ces facteurs sont différents pour chaque organisation et peuvent varier au fil du temps. 

Azure AD prend en charge les méthodes d’authentification suivantes pour les solutions d’identité hybride :

### <a name="cloud-authentication"></a>Authentification cloud
Quand vous choisissez cette méthode d’authentification, Azure AD gère le processus de connexion pour les utilisateurs. Si vous l’associez à une authentification unique (SSO) fluide, les utilisateurs peuvent se connectent aux applications cloud sans avoir à retaper leurs informations d’identification. L’authentification cloud propose deux options : 

**Synchronisation de hachage du mot de passe** : il s’agit du moyen le plus simple d’activer l’authentification pour les objets d’annuaire locaux dans Azure AD. La synchronisation de hachage du mot de passe permet aux utilisateurs d’utiliser les mêmes nom d’utilisateur et mot de passe qu’ils utilisent localement sans avoir à déployer une infrastructure supplémentaire. Certaines fonctionnalités Premium d’Azure AD, comme Identity Protection, nécessite la synchronisation de hachage du mot de passe, quelle que soit la méthode d’authentification sélectionnée.

> [!NOTE] 
> Les mots de passe ne sont jamais stockés en texte clair ni chiffrés avec un algorithme réversible dans Azure AD. Pour plus d’informations sur le processus réel de la synchronisation de hachage du mot de passe, consultez [Implémenter la synchronisation de hachage du mot de passe avec la synchronisation Azure AD Connect](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Authentification directe** : fournit une validation de mot de passe simple pour les services d’authentification Azure AD. Pour cela, un agent logiciel exécuté sur un ou plusieurs serveurs locaux valide les utilisateurs directement auprès de votre annuaire Active Directory local, ce qui garantit que la validation des mots de passe n’a pas lieu dans le cloud. Les entreprises soumises à des réglementations qui les empêchent de stocker leurs mots de passe hachés dans le cloud et qui ne peuvent donc pas utiliser la synchronisation de hachage du mot de passe utilisent cette méthode d’authentification. Pour plus d’informations sur le processus réel de l’authentification directe, consultez [Connexion de l’utilisateur avec l’authentification directe Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Authentification fédérée
Quand vous choisissez cette méthode d’authentification, Azure AD délègue le processus d’authentification à un système d’authentification approuvée distinct, par exemple les services de fédération Active Directory (AD FS), pour valider le mot de passe de l’utilisateur. Le système d’authentification peut fournir des conditions d’authentification supplémentaires, comme l’authentification par carte à puce ou une authentification multifacteur tierce. Pour plus d’informations, consultez [Déploiement des services de fédération Active Directory (AD FS)](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

L’arbre de décision présenté dans la section suivante va vous aider à déterminer la méthode d’authentification adaptée à vos besoins. Vous pourrez ainsi décider si vous devez déployer une authentification cloud ou une authentification fédérée pour votre solution d’identité hybride Azure AD.

## <a name="azure-ad-authentication-decision-tree"></a>Authentification AD Azure : arbre de décision

![image1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>Considérations détaillées sur les méthodes d’authentification

### <a name="cloud-authentication-password-hash-sync"></a>Authentification cloud : synchronisation de hachage du mot de passe 

* **Effort** : la synchronisation de hachage du mot de passe nécessite le moins d’effort en termes de déploiement, de maintenance et d’infrastructure. Elle s’adresse aux organisations qui ont seulement besoin d’autoriser leurs utilisateurs à se connecter à Office 365, à des applications SaaS et à d’autres ressources basées sur Azure AD. Une fois activée, la synchronisation de hachage du mot de passe fait partie du processus de synchronisation Azure AD Connect et s’exécute toutes les deux minutes. 

* **Expérience utilisateur** : nous recommandons aux organisations de déployer une authentification unique (SSO) fluide conjointement à la synchronisation de hachage du mot de passe pour éviter les invites inutiles une fois la connexion établie et améliorer l’expérience de connexion de l’utilisateur.

* **Scénarios avancés** : les organisations peuvent choisir d’utiliser les insights des identités avec les rapports Azure AD Identity Protection, notamment le rapport Informations d’identification ayant fuité. Windows Hello Entreprise est une autre option avec des [exigences spécifiques quand vous utilisez la synchronisation de hachage du mot de passe](https://docs.microsoft.com/en-us/windows/access-protection/hello-for-business/hello-identity-verification). Les organisations exigeant une authentification multifacteur avec la synchronisation de hachage du mot de passe doivent utiliser l’authentification multifacteur Azure AD et ne peuvent pas utiliser de méthode d’authentification multifacteur tierce ou locale.

* **Continuité d’activité** : la synchronisation de hachage du mot de passe est, par nature, un service cloud hautement disponible qui s’adapte à tous les centres de données Microsoft. Il est recommandé de déployer un deuxième serveur Azure AD Connect en mode préproduction dans une configuration de secours à des fins de reprise d’activité après sinistre.

* **Considérations** : à l’heure actuelle, la synchronisation de hachage du mot de passe n’applique pas immédiatement les changements aux états des comptes locaux. Cela signifie qu’un utilisateur a accès aux applications cloud jusqu’à ce que l’état du compte utilisateur soit synchronisé avec Azure AD. Pour passer outre cette limitation, il est recommandé aux organisations d’activer un nouveau cycle de synchronisation après les mises à jour en bloc effectuées par les administrateurs sur les états des comptes locaux (comme la désactivation de comptes). Le verrouillage des comptes est un autre état de compte d’utilisateur qui est synchronisé dans le cycle suivant. 

> [!NOTE] 
> L’état indiquant un mot de passe expiré n’est pas synchronisé avec Azure AD à l’aide d’Azure AD Connect. 

Pour obtenir les étapes de déploiement, consultez [Implémentation de la synchronisation de hachage du mot de passe](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization).

### <a name="cloud-authentication-pass-through-authentication"></a>Authentification cloud : authentification directe  

* **Effort** : l’authentification directe nécessite l’installation d’un ou plusieurs agents légers (trois sont recommandés) sur des serveurs existants ayant accès à vos services AD DS (Active Directory Domain Services) locaux et à vos contrôleurs de domaine AD locaux. Ces agents doivent disposer d’un accès sortant à Internet et avoir accès à vos contrôleurs de domaine. Le déploiement des agents dans un réseau de périmètre, qui nécessite un accès réseau sans contrainte aux contrôleurs de domaine, n’est donc pas pris en charge. Tout le trafic réseau est chiffré et limité aux demandes d’authentification. Pour plus d’informations sur ce processus, consultez [l’immersion dans la sécurité](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) sur l’authentification directe.

* **Expérience utilisateur** : il est recommandé aux organisations de déployer une authentification unique fluide avec l’authentification directe pour éviter les invites inutiles une fois la connexion établie et ainsi améliorer l’expérience de connexion de l’utilisateur.

* **Scénarios avancés** : l’authentification directe garantit que les demandes d’authentification sont immédiatement refusées quand l’état du compte d’un utilisateur local indique « désactivé », « verrouillé » ou « mot de passe expiré » ou se trouvent en dehors des heures de connexion autorisées de l’utilisateur. Les organisations exigeant une authentification multifacteur avec l’authentification directe doivent utiliser l’authentification multifacteur Azure AD et ne peuvent pas utiliser de méthode d’authentification multifacteur tierce ou locale. Les fonctionnalités avancées, comme le rapport sur la fuite des informations d’identification généré par Identity Protection, nécessite le déploiement de la synchronisation de hachage du mot de passe (que vous choisissiez l’authentification directe ou non).

* **Continuité d’activité**  : il est recommandé de déployer deux agents directs supplémentaires, en plus du premier agent sur le serveur Azure AD Connect, pour garantir la haute disponibilité des demandes d’authentification. Quand trois agents sont déployés et que l’un d’eux est hors service pour maintenance, l’échec d’un agent n’a aucune incidence. Le déploiement de la synchronisation de hachage du mot de passe présente un autre avantage. En effet, outre l’authentification directe, elle peut servir de méthode d’authentification de secours quand la méthode d’authentification principale n’est plus disponible, par exemple en cas d’indisponibilité des serveurs locaux.

* **Considérations** : si vous utilisez la synchronisation de hachage du mot de passe comme méthode d’authentification de secours pour l’authentification directe et que les agents ne peuvent pas valider les informations d’identification de l’utilisateur, le basculement vers la synchronisation de hachage du mot de passe n’est pas automatique. Vous devez alors basculer la méthode de connexion manuellement avec Azure AD Connect. L’authentification directe prend uniquement en charge les applications cloud qui utilisent une authentification moderne et des protocoles Exchange Online spécifiques comme ActiveSync, POP3 et IMAP4. Pour plus d’informations sur la prise en charge des applications Office, consultez [Microsoft Office 2013 and later supports modern authentication, but not earlier versions](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/). Pour découvrir d’autres considérations sur l’authentification directe, notamment la prise en charge d’ID de connexion de substitution, consultez le [forum aux questions](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq).

Pour obtenir les étapes de déploiement à suivre, consultez [Implémentation de l’authentification directe](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Federated Authentication

* **Effort**  : l’utilisation d’un système d’authentification fédérée s’appuie sur un système externe pour authentifier les utilisateurs. Certaines entreprises souhaitent rentabiliser leur investissement et réutiliser leur système fédéré existant avec leur solution d’identité hybride Azure AD. La maintenance et la gestion du système fédéré ne relèvent pas d’Azure AD. Il appartient à l’organisation d’utiliser le système fédéré pour vérifier qu’il est déployé de manière sécurisée et qu’il peut gérer la charge de l’authentification. 

* **Expérience utilisateur** : l’expérience utilisateur de l’authentification fédérée dépend de l’implémentation de fonctionnalités, de la topologie et de la configuration de la batterie de serveurs de fédération. Certaines organisations exigent cette souplesse pour configurer l’accès à la batterie de serveurs de fédération en fonction de leurs exigences en matière de sécurité. Par exemple, il est possible de configurer en interne des utilisateurs connectés et des appareils capables de connecter automatiquement les utilisateurs. Aucune information d’identification n’est donc demandée aux utilisateurs puisqu’ils sont déjà connectés à leur appareil. Au contraire, il est aussi possible de faire appel à certaines fonctionnalités de sécurité avancées pour compliquer le processus de connexion de l’utilisateur.

* **Scénarios avancés** : une solution d’authentification fédérée est généralement nécessaire quand les clients ont des exigences en matière d’authentification qui ne sont pas prises en charge en mode natif par Azure AD. Voici quelques exigences courantes (pour obtenir des informations détaillées, consultez [ce billet de blog](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/)) :

    * Authentification nécessitant des cartes à puce ou des certificats.
    * Utilisation d’un serveur MFA local ou d’un fournisseur d’authentification multifacteur tiers.
    * Authentification à l’aide d’une solution d’authentification tierce. Consultez la [liste de compatibilité de fédération Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Les utilisateurs doivent se connecter avec leur sAMAccountName, comme DOMAINE\nomutilisateur, et non avec un nom d’utilisateur principal (UPN) comme user@domain.com.
    * Implémentation Windows Hello Entreprise dépendante d’une infrastructure à clé publique (PKI) interne pour les certificats de confiance.

* **Continuité d’activité** : les systèmes fédérés nécessitent généralement un groupe de serveurs à charge équilibrée, également appelé « batterie de serveurs », configuré dans une topologie de réseau interne et de réseau de périmètre pour garantir la haute disponibilité pour les demandes d’authentification. La synchronisation de hachage du mot de passe peut être déployée conjointement avec l’authentification fédérée comme méthode d’authentification de secours quand la méthode d’authentification principale n’est plus disponible, par exemple en cas d’indisponibilité des serveurs locaux. Certaines grandes entreprises exigent une solution de fédération pour prendre en charge plusieurs points d’entrée Internet configurés avec géo-DNS pour les demandes d’authentification à faible latence.

* **Considérations** : les systèmes fédérés nécessitent généralement un investissement plus important dans l’infrastructure locale. La plupart des organisations choisissent cette option si elles disposent déjà d’une solution de fédération locale et qu’elles sont contraintes d’utiliser un fournisseur d’identité unique pour des raisons commerciales. La fédération est plus difficile à utiliser et à dépanner que les solutions d’authentification cloud. L’utilisation d’ID de connexion utilisateur avec un domaine non routable qui ne peut pas être vérifié dans Azure AD nécessite une configuration supplémentaire. Cette exigence est connue sous le nom de « prise en charge des ID de connexion de substitution ». Pour connaître les limitations et les exigences, consultez [Configuration d’un ID de connexion de substitution](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

Pour accéder aux étapes de déploiement, consultez [Implémentation des services de fédération](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/deploying-federation-servers).

> [!NOTE] 
> Quand vous déployez votre solution d’identité hybride Azure AD, vous devez veiller à implémenter l’une des topologies prises en charge d’Azure AD Connect. Pour découvrir les configurations prises en charge et celles qui ne le sont pas, consultez[Topologies pour Azure AD Connect](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Diagrammes architecturaux

Le diagramme suivant présente les composants architecturaux de haut niveau nécessaires pour chaque méthode d’authentification que vous pouvez utiliser avec votre solution d’identité hybride Azure AD. Il vous offre une vue d’ensemble à l’aide de laquelle vous pouvez comparer les différences entre les solutions.

Le diagramme suivant illustre la simplicité d’une solution de synchronisation de hachage du mot de passe :

![PHS](media/azure-ad/azure-ad-authn-image2.png)

Le diagramme suivant présente les exigences en matière d’agents de l’authentification directe :

![PTA](media/azure-ad/azure-ad-authn-image3.png)

Le diagramme suivant présente les composants obligatoires pour la fédération dans le réseau interne et le réseau de périmètre de votre organisation :

![ADFS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="recommendations-and-considerations-from-azure-ad"></a>Recommandations et considérations du point de vue d’Azure AD

Votre système d’identité garantit à vos utilisateurs l’accès aux applications cloud et aux applications métier que vous migrez et rendez accessibles dans le cloud. L’authentification contrôle l’accès aux applications pour assurer la productivité des utilisateurs autorisés et protéger les données sensibles de votre organisation contre les intrus. Pour cette raison, tenez compte des recommandations suivantes dans le choix de la méthode d’authentification appropriée pour votre organisation. 

Utilisez ou activez la synchronisation de hachage du mot de passe quelle que soit la méthode d’authentification choisie, et ce pour les raisons suivantes :

1. **Haute disponibilité et reprise d’activité après sinistre** : l’authentification directe et la fédération s’appuient sur une infrastructure locale. Pour l’authentification directe, cela inclut la mise en réseau et le matériel des serveurs. Il est aussi attendu que les contrôleurs de domaine répondent aux demandes d’authentification en provenance des agents d’authentification directe. Pour la fédération, l’empreinte locale est plus importante, car elle nécessite des serveurs dans votre réseau de périmètre pour rediriger par l’intermédiaire d’un proxy les demandes d’authentification et les serveurs de fédération interne. Votre organisation doit donc déployer des serveurs redondants pour éviter des points de défaillance uniques et garantir le traitement continu des requêtes d’authentification en cas d’échec d’un composant. L’intégrité de nombreux composants passe par des opérations de maintenance. Si celles-ci ne sont pas planifiées et implémentées correctement, le risque de panne est plus élevé. La synchronisation de hachage du mot de passe permet d’éviter les pannes, car le service d’authentification cloud Azure AD de Microsoft est mis à l’échelle globalement et toujours disponible.

2. **Survie à une panne locale** : les conséquences d’une panne locale à la suite d’une cyberattaque ou d’un sinistre peuvent être considérables, de l’atteinte à l’image de marque à la paralysie de l’organisation si celle-ci ne parvient pas à gérer l’attaque. Au cours de l’année écoulée, de nombreuses organisations ont été victimes d’attaques menées par des programmes malveillants, notamment des ransomwares (rançongiciels), qui ont provoqué la mise hors service de leurs serveurs locaux. Après examen de l’assistance fournie par Microsoft pour gérer ces types d’attaques, il ressort deux catégories d’organisations :

   a. Les organisations ayant activé la synchronisation de hachage du mot de passe ont pu basculer vers cette méthode d’authentification et se retrouver en ligne en quelques heures. En accédant aux e-mails par le biais d’Office 365, elles ont pu résoudre les problèmes et accéder à d’autres charges de travail sur le cloud.

   b. Les organisations n’ayant pas auparavant activé la synchronisation de hachage du mot de passe ont dû faire appel à des systèmes de messagerie externes non fiables pour la communication et la résolution des problèmes. Il leur a fallu au moins plusieurs semaines pour être à nouveau opérationnelles.

3. **Identity Protection** : Azure AD Identity Protection est l’un des meilleurs moyens de protéger les utilisateurs dans le cloud. Microsoft analyse en permanence Internet à la recherche de listes d’utilisateurs et de mots de passe vendues et mises à disposition sur le « dark web » par des utilisateurs malveillants. Azure AD peut utiliser ces informations pour vérifier si les noms d’utilisateur et les mots de passe de votre organisation sont compromis. Il est donc essentiel d’activer la synchronisation de hachage du mot de passe, et ce quelle que soit la méthode d’authentification vous utilisez (fédérée ou directe). Les informations d’identification ayant fuité sont présentées sous la forme d’un rapport et peuvent être utilisées pour bloquer un utilisateur ou le forcer à changer son mot de passe quand il tente de se connecter avec un mot de passe ayant fuité.

Enfin, selon [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), Microsoft propose l’ensemble le plus complet de fonctions de gestion de l’identité et de l’accès. Microsoft repousse sept mille milliards de cyberévénements par jour tout en authentifiant les utilisateurs autorisés auprès de milliers d’applications SaaS comme Office 365, et ce sur la quasi-totalité des appareils. 

## <a name="conclusion"></a>Conclusion

Dans cet article, vous avez découvert les différentes options d’authentification que les organisations peuvent configurer et déployer pour prendre en charge l’accès aux applications cloud. Face à des exigences professionnelles, sécuritaires et techniques variées, les organisations ont le choix entre la synchronisation de hachage du mot de passe, l’authentification directe et la fédération. Pour chaque méthode d’authentification, votre organisation peut déterminer si l’effort nécessaire pour déployer la solution et l’expérience utilisateur du processus répondent à ses propres critères. Vous devez également déterminer si votre organisation a besoin des scénarios avancés et des fonctionnalités de continuité d’activité de chaque méthode d’authentification. Enfin, vous devez évaluer les considérations relatives à chaque méthode d’authentification pour voir si l’une d’elles empêche l’implémentation de votre choix.

## <a name="next-steps"></a>Étapes suivantes

Aujourd’hui, les menaces sont présentes 24 heures sur 24 et proviennent de partout. L’implémentation d’une méthode d’authentification appropriée permet d’atténuer les risques en matière de sécurité et de protéger vos identités. 

[Démarrez](https://docs.microsoft.com/en-us/azure/active-directory/get-started-azure-ad) avec Azure AD et déployez la solution d’authentification adaptée à votre organisation.

Si vous souhaitez passer de l’authentification fédérée à l’authentification cloud, découvrez plus en détail [comment changer la méthode de connexion](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Pour vous aider à planifier et à implémenter la migration, vous pouvez utiliser [ces plans de projet](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/tree/master/Authentication).
