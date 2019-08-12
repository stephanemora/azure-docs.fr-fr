---
title: Administration à distance sécurisée de votre passerelle dans Azure Australie
description: Conseils sur la configuration de l’administration à distance sécurisée dans les régions australiennes pour satisfaire aux exigences spécifiques des réglementations, de la législation et de la politique du secteur public.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 827dffc1c7544d9373b5f8d4426ea8c448fa25ab
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571161"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Administration à distance sécurisée de votre passerelle dans Azure Australie

Il est essentiel pour la disponibilité et l’intégrité de tout système que les activités d’administration soient menées en toute sécurité et contrôlées. Les activités d’administration doivent être effectuées à partir d’un appareil sécurisé, via une connexion sécurisée, et être sauvegardées par des processus d’authentification et d’autorisation forts. L’administration à distance sécurisée garantit que seules les actions autorisées sont effectuées, et ce uniquement par les administrateurs autorisés.

Cet article fournit des informations sur l’implémentation d’une fonctionnalité d’administration à distance sécurisée pour un système accessible sur Internet hébergé dans Azure, qui s’aligne sur les préconisations à l’adresse du grand public de l’ACSC (Australian Cyber Security Center, ou « Centre de cyber-sécurité d’Australie ») et les objectifs de l’ISM (Information Security Manual, ou « Manuel sur la sécurité des informations ») de l’ACSC.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Exigences de l’ACSC (Australian Cyber Security Centre)

Les exigences de sécurité globales pour les systèmes du Commonwealth sont définies dans l’ISM. Pour aider les entités du Commonwealth à fournir une administration sécurisée, l’ACSC a publié [ACSC Protect: Secure Administration](https://www.acsc.gov.au/publications/protect/secure-administration.htm) (Protection de l’ACSC : sécuriser l’administration)

Ce document expose l’importance de l’administration sécurisée et suggère une méthode d’implémentation d’un environnement d’administration sécurisé. Ce document décrit les éléments d’une solution d’administration sécurisée comme suit :

|Élément   |Description   |
|---|---|
|Contrôle d’accès privilégié   |Le contrôle de l’accès aux comptes privilégiés est un contrôle de sécurité fondamental qui protège les comptes privilégiés contre toute utilisation incorrecte. La méthodologie de contrôle d’accès englobe les concepts de « privilèges minimum » et de « besoins », ainsi que les processus et les procédures de gestion des comptes de service et des mouvements du personnel.   |
|Authentification multifacteur   |L’implémentation de facteurs d’authentification supplémentaires au-delà des noms d’utilisateur et des phrases secrètes, tels que les jetons physiques ou les cartes à puces, peut contribuer à protéger les ressources critiques. Si un adversaire compromet des informations d’identification de comptes privilégiés, étant donné que toutes les actions d’administration doivent d’abord passer par une certaine forme d’authentification multifacteur, les conséquences seront probablement minimes.|
|Stations de travail privilégiées|L’utilisation d’un environnement sécurisé connu pour les tâches administratives peut réduire les risques de compromission du réseau en raison de l’implémentation de contrôles de sécurité supplémentaires.|
|Journalisation et audit   |La génération, la collecte et l’analyse automatisées des événements liés à la sécurité et à l’administration à partir de stations de travail, de serveurs, de périphériques réseau et de serveurs de rebond permettront la détection des compromissions et des tentatives de compromission. L’automatisation permet aux organisations de répondre plus rapidement, ce qui réduit la nécessité d’un compromis.|
|Segmentation et séparation du réseau|La segmentation d’un réseau en zones logiques, telles que des domaines de sécurité différents et la séparation supplémentaire de ces réseaux logiques par la restriction des types de données qui circulent d’une zone à l’autre, limite le mouvement latéral. La segmentation empêche un adversaire d’accéder à des ressources supplémentaires.|
|Serveurs de rebond|Un serveur de rebond est un serveur d’accès à distance renforcé, qui utilise généralement le logiciel Services Bureau à distance ou Secure Shell (SSH) de Microsoft. Les serveurs de rebond agissent comme un point d’exécution pas à pas pour les administrateurs accédant à des systèmes critiques en effectuant toutes les actions administratives depuis de l’hôte dédié.|
|

Cet article fournit une architecture de référence sur la façon dont les éléments ci-dessus peuvent être utilisés pour l’administration sécurisée des systèmes déployés dans Azure.

## <a name="architecture"></a>Architecture

Le fait de disposer d’une fonctionnalité d’administration sécurisée nécessite plusieurs composants qui fonctionnent ensemble pour former une solution cohérente. Dans l’architecture de référence fournie, les composants sont mappés aux éléments décrits dans [ACSC Protect: Secure Administration](https://www.acsc.gov.au/publications/protect/secure-administration.htm) (Protection de l’ACSC : sécuriser l’administration)

![Architecture d’administration à distance sécurisée Azure](media/remote-admin.png)

## <a name="components"></a>Composants

L’architecture est conçue pour s’assurer qu’un compte privilégié ne dispose que des autorisations nécessaires, qu’il est identifié en toute sécurité et qu’il n’a accès qu’aux interfaces d’administration à partir d’un appareil autorisé et par le biais de mécanismes de communications sécurisés qui sont contrôlés et audités.

|Solution| Composants|Éléments|
|---|---|---|
|Appareils sécurisés |<ul><li>Station de travail privilégiée</li><li>Appareil mobile</li><li>Microsoft Intune</li><li>Stratégie de groupe</li><li>Serveur de rebond/hôte bastion</li><li>Administration juste-à-temps (JAT)</li></ul> |<ul><li>Stations de travail privilégiées</li><li>Serveurs de rebond</li></ul>|
|Communication sécurisée |<ul><li>Portail Azure</li><li>Passerelle VPN Azure</li><li>Passerelle Bureau à distance</li><li>Groupes de sécurité réseau (NSG)</li></ul> |<ul><li>Segmentation et séparation du réseau</li></ul>|
|Authentification forte |<ul><li>Contrôleur de domaine</li><li>Azure Active Directory (Azure AD)</li><li>Serveur NPS (Network Policy Server)</li><li>Azure MFA</li></ul> |<ul><li>Authentification multifacteur</li></ul> |
|Autorisation forte |<ul><li>Identity and Access Management (IAM)</li><li>Privileged Identity Management (PIM)</li><li>Accès conditionnel</li></ul>|<ul><li>Contrôle d’accès privilégié</li></ul>|
|||

>[!NOTE]
>Pour plus d’informations sur l’élément de journalisation et d’audit, veuillez consulter l’article [Journalisation, audit et visibilité de la passerelle](gateway-log-audit-visibility.md).

## <a name="administration-workflow"></a>Flux de travail d’administration

L’administration des systèmes déployés dans Azure est divisée en deux catégories : l’administration de la configuration Azure et l’administration des charges de travail déployées dans Azure. La configuration d’Azure est effectuée via la Portail Azure et l’administration de la charge de travail s’effectue via des mécanismes d’administration tels que le protocole RDP (Remote Desktop Protocol) (RDP), Secure Shell (SSH) ou, dans le cas des fonctionnalités PaaS, à l’aide d’outils tels que SQL Management Studio.

L’obtention de l’accès à l’administration est un processus à plusieurs étapes impliquant les composants listés dans l’architecture et nécessitant l’accès au Portail Microsoft Azure et à la configuration d’Azure avant de pouvoir accéder aux charges de travail Azure.

>[!NOTE]
> Les étapes décrites ici constituent le processus général qui utilise les composants de l’interface utilisateur graphique (GUI) d’Azure. Ces étapes peuvent également être effectuées à l’aide d’autres interfaces, telles que PowerShell.

### <a name="azure-configuration-and-azure-portal-access"></a>Configuration d’Azure et accès au Portail Microsoft Azure

|Étape |Description |
|---|---|
|Connexion à une station de travail privilégiée |L’administrateur se connecte à une station de travail privilégiée en utilisant ses informations d’identification d’administration. Les contrôles de stratégie de groupe empêchent les comptes non administratifs de s’authentifier auprès de la station de travail privilégiée et les comptes d’administration de s’authentifier sur des stations de travail non privilégiées. Microsoft Intune gère la compatibilité de la station de travail privilégiée pour s’assurer qu’elle est à jour avec les correctifs logiciels, les logiciels anti-programme malveillant et d’autres exigences de conformité. |
|Connexion au Portail Microsoft Azure |L’administrateur ouvre un navigateur web pour accéder au Portail Microsoft Azure. La connexion est chiffrée à l’aide de TLS (Transport Layer Security). Puis l’administrateur se connecte à l’aide des informations d’identification d’administration. La demande d’authentification est traitée directement via Azure Active Directory ou via des mécanismes d’authentification tels que Services ADFS (AD FS) ou une authentification directe. |
|Azure MFA |Azure MFA envoie une demande d’authentification à l’appareil mobile inscrit du compte privilégié. L’appareil mobile est géré par Intune pour garantir la conformité aux exigences de sécurité. L’administrateur doit d’abord s’authentifier sur le périphérique mobile, puis sur l’application Microsoft Authenticator à l’aide d’un code confidentiel ou d’un système biométrique avant que la tentative d’authentification ne soit autorisée pour Azure MFA. |
|Accès conditionnel |Les stratégies d’accès conditionnel vérifient la tentative d’authentification pour s’assurer qu’elle répond aux exigences requises, telles que l’adresse IP à partir de laquelle la connexion provient, l’appartenance au groupe pour le compte privilégié et l’état de gestion et de conformité de la station de travail privilégiée telle qu’elle est signalée par Intune. |
|Privileged Identity Management (PIM) |Le Portail Microsoft Azure permet désormais à l’administrateur d’activer ou demander l’activation des rôles privilégiés pour lesquels il dispose d’autorisations via PIM. PIM garantit que les comptes privilégiés n’ont pas de privilèges d’administration permanents et que toutes les demandes d’accès privilégiées durent uniquement le temps nécessaire à l’exécution des tâches d’administration. PIM permet également la journalisation de toutes les demandes et activations à des fins d’audit. |
|Gestion de l’identité et de l’accès|Une fois que le compte privilégié a été identifié de manière sécurisée et que les rôles sont activés, l’administrateur peut accéder aux abonnements Azure et aux ressources pour lesquelles il a reçu des autorisations via la gestion des identités et des accès.|
|

Une fois que le compte privilégié a effectué les étapes pour obtenir un accès administratif au Portail Microsoft Azure, l’accès aux charges de travail peut être configuré et des connexions d’administration peuvent être établies.

### <a name="azure-workload-administration"></a>Administration de la charge de travail Azure

|Étape |Description|
|---|---|
|Accès juste-à-temps (JAT)|Pour obtenir l’accès aux machines virtuelles, l’administrateur utilise le JAT pour demander au protocole RDP l’accès au serveur de rebond à partir de l’adresse IP de la passerelle des services Bureau à distance et pour demander au protocole RDP ou SSH l’accès à partir du serveur de rebond vers les machines virtuelles de charge de travail appropriées.|
|Passerelle VPN Azure|L’administrateur établit à présent une connexion VPN IPSec point à site à partir de sa station de travail privilégiée vers la passerelle VPN Azure, qui effectue l’authentification par certificat pour établir la connexion.|
|Passerelle des services Bureau à distance|L’administrateur tente à présent une connexion RDP vers le serveur de rebond tandis que la passerelle des services Bureau à distance est spécifiée dans la configuration de Connexion Bureau à distance. La passerelle des services Bureau à distance dispose d’une adresse IP privée accessible via la connexion vers la passerelle VPN Azure. Les stratégies appliquées à la passerelle des services Bureau à distance déterminent si le compte privilégié est autorisé à accéder au serveur de rebond demandé. La passerelle des services Bureau à distance invite l’administrateur à saisir ses informations d’identification, puis transfère la demande d’authentification au serveur NPS (Network Policy Server).|
|Serveur NPS (Network Policy Server)|Le serveur NPS reçoit la demande d’authentification de la passerelle des services Bureau à distance et valide le nom d’utilisateur et le mot de passe sur Active Directory avant d’envoyer une demande à Azure Active Directory pour déclencher une demande d’authentification Azure MFA.|
|Azure MFA|Azure MFA envoie une demande d’authentification à l’appareil mobile inscrit du compte privilégié. L’appareil mobile est géré par Intune pour garantir la conformité aux exigences de sécurité. L’administrateur doit d’abord s’authentifier sur le périphérique mobile, puis sur l’application Microsoft Authenticator à l’aide d’un code confidentiel ou d’un système biométrique avant que la tentative d’authentification ne soit autorisée pour Azure MFA.|
|Serveur de rebond|Une fois authentifié, la connexion RDP est chiffrée à l’aide du protocole TLS (Transport Layer Security), puis envoyée via le tunnel IPSec chiffré vers la passerelle VPN Azure, via la passerelle des services Bureau à distance et le serveur de rebond. À partir du serveur de rebond, l’administrateur peut désormais établir une connexion RDP ou SSH aux machines virtuelles de charge de travail, comme spécifié dans la demande JAT.|
|

## <a name="general-guidance"></a>Règle générale

Lors de l’implémentation des composants énumérés dans cet article, les recommandations générales suivantes s’appliquent :

* Validez la disponibilité de la région des services, en veillant à ce que toutes les données restent dans les emplacements autorisés et soient déployées vers Australie Centre ou Australie Centre 2 comme première préférence pour les charges de travail PROTÉGÉES

* Consultez la publication *Azure – Rapport de certification ACSC – Protégé 2018* pour connaître l’état de certification des services individuels et effectuer des auto-évaluations sur les composants pertinents non inclus dans le rapport conformément au *GUIDE DU CONSOMMATEUR DE L’ACSC – Microsoft Azure catégorie PROTECTED*

* Vérifiez la connectivité réseau et toute configuration de proxy nécessaire pour accéder aux composants d’authentification nécessaires, tels que Azure AD, ADFS et PTA

* Utiliser Azure Policy pour surveiller et faire et assurer la conformité des exigences

* Assurez-vous que les machines virtuelles, en particulier les contrôleurs de domaine Active Directory, sont stockées dans des comptes de stockage chiffrés et utilisent Azure Disk Encryption

* Créez et gérez des processus de gestion et une gouvernance des privilèges d’administration et d’identité robustes pour étayer les contrôles techniques figurant dans cet article.

|Ressource|URL|
|---|---|
|Documents de conformité à la réglementation et aux politiques australiennes|[Documents de conformité à la réglementation et aux politiques australiennes](https://aka.ms/au-irap)|
|Produits Azure – Régions australiennes et non régionales|[Produits Azure – Régions australiennes et non régionales](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Stratégies pour réduire les risques d’incidents de sécurité informatique|[Stratégies pour réduire les risques d’incidents de sécurité informatique](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC Protect: Secure Administration (Protection de l’ACSC : sécuriser l’administration)|[ACSC Protect: Secure Administration](https://acsc.gov.au/publications/protect/secure-administration.htm) (Protection de l’ACSC : sécuriser l’administration)|
|Procédure : Intégrez votre infrastructure de passerelle des services Bureau à distance à l’aide de l’extension du serveur NPS (Network Policy Server) et Azure AD|[Intégrer la passerelle des services Bureau à distance avec NPS et Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-rdg)|

## <a name="component-guidance"></a>Guide sur les composants

Cette section fournit des informations sur la finalité de chaque composant et sur son rôle dans l’architecture d’administration à distance sécurisée des passerelles. Des liens supplémentaires permettent d’accéder à des ressources utiles, telles que la documentation de référence, des guides et des didacticiels.

## <a name="secure-devices"></a>Appareils sécurisés

Les appareils physiques utilisés par des utilisateurs privilégiés pour exécuter des fonctions d’administration sont des cibles privilégiées pour les acteurs malveillants. Pour fournir une fonctionnalité d’administration à distance sécurisée, vous devez assurer la sécurité et l’intégrité des appareils physiques, ainsi que leur protection contre les logiciels malveillants et les compromissions. Cela implique une configuration de sécurité à priorité élevée telle que spécifiée dans les huit stratégies essentielles de ACSC pour atténuer les incidents de sécurité informatiques tels que la mise en liste verte d’applications, la mise à jour corrective des applications, le renforcement des applications et la mise à jour corrective des systèmes d’exploitation. Ces fonctionnalités doivent être installées, configurées, vérifiées, validées et signalées pour garantir que l’état d’un appareil est conforme aux exigences de l’organisation.

### <a name="privileged-workstation"></a>Station de travail privilégiée

La station de travail privilégiée est une machine renforcée qui peut être utilisée pour effectuer des tâches d’administration et n’est accessible qu’aux comptes d’administration. La station de travail privilégiée doit être dotée d’une stratégie et d’une configuration prévues pour restreindre les exécutions de logiciels, leurs accès aux ressources réseau et Internet et protéger les informations d’identification en cas de vol ou de compromission de l’appareil.

|Ressources|Lien|
|---|---|
|Vue d’ensemble de l’architecture des stations de travail avec accès privilégié|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)|
|Sécurisation des documents de référence d’accès privilégié|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|
|

### <a name="mobile-device"></a>Appareil mobile

Un appareil mobile présente un risque de perte ou de vol accidentel en raison de sa portabilité et de sa taille. Il doit donc être sécurisé de manière appropriée. L’appareil mobile fournit un facteur supplémentaire important pour l’authentification, étant donné sa capacité à renforcer l’authentification pour l’accès à l’appareil, la traçabilité grâce aux services de localisation, les fonctions de chiffrement et la possibilité d’être effacé à distance. Lorsque vous utilisez un appareil mobile comme facteur d’authentification supplémentaire pour Azure, l’appareil doit être configuré pour utiliser l’application Microsoft Authenticator avec l’authentification par code confidentiel ou biométrique, et non par des appels téléphoniques ou des messages texte.

|Ressources|Lien|
|---|---|
|Méthodes Azure AD Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)|
|Utilisation de l’application Microsoft Authenticator|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|
|

### <a name="microsoft-intune"></a>Microsoft Intune

Intune est le composant de Enterprise Mobility + Security qui gère les applications et les appareils mobiles. Il s’intègre étroitement avec d’autres composants comme Azure Active Directory pour l’identité et le contrôle d’accès, et Azure Information Protection pour la protection des données. Intune fournit des stratégies pour les stations de travail et les appareils mobiles afin de définir les exigences de conformité pour l’accès aux ressources et fournit des fonctionnalités de création de rapports et d’audit pour obtenir des informations sur l’état des appareils administratifs.

|Ressources|Lien|
|---|---|
|Documentation Microsoft Intune|[https://docs.microsoft.com/intune/](https://docs.microsoft.com/intune/)|
|Prise en main de la conformité des appareils dans Intune|[https://docs.microsoft.com/intune/device-compliance-get-started](https://docs.microsoft.com/intune/device-compliance-get-started)|
|

### <a name="group-policy"></a>Stratégie de groupe

La stratégie de groupe est utilisée pour contrôler la configuration des systèmes d’exploitation et des applications. Les stratégies de sécurité contrôlent les paramètres d’authentification, d’autorisation et d’audit d’un système. La stratégie de groupe est utilisée pour renforcer la station de travail privilégiée, protéger les informations d’identification d’administration et limiter les comptes non privilégiés à l’accès aux appareils privilégiés.

|Ressources|Lien|
|---|---|
|Paramètre de stratégie de sécurité Permettre l’ouverture d’une session locale|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|
|

### <a name="jump-server--bastion-host"></a>Serveur de rebond/hôte Azure Bastion

Le serveur de rebond/hôte Azure Bastion est un point central pour l’administration. Il dispose des outils nécessaires pour effectuer des tâches d’administration, mais également de l’accès réseau nécessaire pour se connecter aux ressources sur les ports d’administration. Dans cet article, le serveur de rebond est considéré comme un point central pour l’administration des charges de travail des machines virtuelles, mais il peut également être configuré comme point autorisé pour l’administration des fonctionnalités PaaS (Platform as a service) telles que SQL. L’accès aux fonctionnalités PaaS peut être restreint selon les services à l’aide de contrôles d’identité et de réseau.

|Ressources|Lien|
|---|---|
|Implémentation d’hôtes d’administration sécurisés|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|
|

### <a name="just-in-time-jit-access"></a>Accès juste-à-temps (JAT)

JAT est une fonctionnalité Azure Security Center qui utilise des groupes de sécurité réseau (NSG) pour bloquer l’accès aux protocoles d’administration tels que RDP et SSH sur les machines virtuelles. Les applications hébergées sur des machines virtuelles continuent de fonctionner normalement, mais pour que l’accès administratif puisse être obtenu, il doit être demandé et ne peut être accordé que pour une période définie. Toutes les demandes sont journalisées à des fins d’audit.

|Ressources |Lien |
|---|---|
|Gérer l’accès juste-à-temps (JAT)|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|
|Automatisation de l’accès juste à temps pour une machine virtuelle Azure|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access)|
|

## <a name="secure-communication"></a>Sécuriser les communications

Le trafic des communications pour les activités d’administration peut contenir des informations très sensibles, telles que des informations d’identification administratives. Il doit donc être géré et protégé en conséquence. La communication sécurisée implique des fonctionnalités de chiffrement fiables pour empêcher l’écoute clandestine et la segmentation de réseau, ainsi que des restrictions qui limitent le trafic administratif à des points de terminaison autorisés et contrôlent le mouvement latéral si un système est compromis.

### <a name="azure-portal"></a>Portail Azure

Les communications avec le Portail Microsoft Azure sont chiffrées à l’aide du protocole TLS (Transport Layer Security) et l’utilisation du Portail Microsoft Azure a été certifiée par ACSC. Les entités du Commonwealth doivent suivre les recommandations de l’*ACSC Consumer Guide* (Guide du consommateur ACSC) et configurer leurs navigateurs web pour s’assurer qu’ils utilisent la dernière version de TLS et des algorithmes de chiffrement pris en charge.

|Ressources |Lien |
|---|---|
|Vue d’ensemble du chiffrement Azure : Chiffrement en transit|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit)|
|

### <a name="azure-vpn-gateway"></a>Passerelle VPN Azure

La passerelle VPN Azure fournit une connexion chiffrée sécurisée entre la station de travail privilégiée et Azure. La passerelle VPN Azure a été certifiée par l’ACSC pour fournir une communication IPSec sécurisée. Les entités du Commonwealth doivent configurer la passerelle VPN Azure conformément à l’ACSC Consumer Guide (Guide du consommateur ACSC), à l’ACSC Certification Report (Rapport de certification ACSC) et à d’autres instructions et réglementations spécifiques.

|Ressources |Lien |
|---|---|
|À propos des connexions de point à site|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about)|
|Détails des chiffrements de la passerelle VPN Azure|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto)|
|Configuration de la passerelle VPN Azure|[https://aka.ms/AzGovAUSecurity](https://aka.ms/AzGovAUSecurity)|
|

### <a name="remote-desktop-rd-gateway"></a>Passerelle Bureau à distance

La passerelle des services Bureau à distance est un mécanisme sécurisé pour le contrôle et l’autorisation des connexions RDP aux systèmes. Elle fonctionne en encapsulant le trafic RDP dans le protocole HTTPS (HyperText Transfer Protocol Secure) et le trafic chiffré à l’aide de TLS. Le protocole TLS fournit une couche supplémentaire de sécurité pour le trafic administratif.

|Ressources |Lien |
|---|---|
|Architecture des Services Bureau à distance|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|
|

### <a name="network-security-groups-nsgs"></a>Groupes de sécurité réseau (NSG)

Les groupes de sécurité réseau (NSG) fonctionnent comme des listes de contrôle d’accès (ACL) pour le trafic réseau entrant ou sortant des sous-réseaux ou des machines virtuelles. Les groupes fournissent une segmentation de réseau et un mécanisme pour contrôler et limiter les flux des communications autorisés entre les systèmes. Les groupes sont un composant fondamental de l’administration juste-à-temps (JAT) qui permet d’autoriser ou de refuser l’accès aux protocoles administratifs.

|Ressources |Lien |
|---|---|
|Vue d’ensemble des groupes de sécurité Azure|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Activation Planifier des réseaux virtuels|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
|

## <a name="strong-authentication"></a>Authentification forte

L’identification sécurisée des utilisateurs privilégiés avant l’octroi de l’accès aux systèmes est un composant fondamental de l’administration sécurisée. Les mécanismes doivent être en place pour protéger les informations d’identification associées à un compte privilégié et empêcher les acteurs malveillants d’accéder aux systèmes via l’usurpation d’identité ou le vol d’informations d’identification.

### <a name="domain-controller-dc"></a>Contrôleur de domaine

De façon générale, un contrôleur de domaine héberge une copie de la base de données Active Directory, qui contient tous les utilisateurs, ordinateurs et groupes au sein d’un domaine. Les contrôleurs de domaine effectuent l’authentification pour les utilisateurs et les ordinateurs. Les contrôleurs de service de cette architecture sont hébergés en tant qu’ordinateurs virtuels dans Azure et fournissent des services d’authentification pour les comptes privilégiés qui se connectent aux serveurs de rebond et aux machines virtuelles de charge de travail.

|Ressources |Lien |
|---|---|
|Présentation des services de domaine Active Directory|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|
|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Azure AD est le service d’authentification d’Azure. Il contient le cloud

les identités, et fournit l’authentification et l’autorisation pour un environnement Azure. Azure AD peut être synchronisé avec Active Directory via Azure AD Connect et peut fournir une authentification fédérée via les services de fédération Active Directory (AD FS) et Azure AD Connect. Azure AD est un composant fondamental de l’administration sécurisée.

|Ressources |Lien |
|---|---|
|Documentation Azure Active Directory|[https://docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)|
|Documentation des identités hybrides|[https://docs.microsoft.com/azure/active-directory/hybrid](https://docs.microsoft.com/azure/active-directory/hybrid)|
|

### <a name="network-policy-server-nps"></a>Serveur NPS (Network Policy Server)

Un serveur NPS est un serveur d’authentification et de stratégie qui fournit des processus d’authentification et d’autorisation avancés. Le serveur NPS dans cette architecture est fourni pour intégrer l’authentification MFA Azure avec les demandes d’authentification de la passerelle des services Bureau à distance. Le serveur NPS dispose d’un plug-in spécifique pour prendre en charge l’intégration avec Azure MFA dans Azure AD.

|Ressources |Lien |
|---|---|
|Documentation du serveur NPS (Network Policy Server)|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)|
|

### <a name="azure-mfa"></a>Azure MFA

Azure MFA est un service d’authentification fourni dans Azure Active Directory pour activer les demandes d’authentification au-delà d’un nom d’utilisateur et d’un mot de passe pour accéder aux ressources cloud telles que le Portail Azure. Azure MFA prend en charge un éventail de méthodes d’authentification et cette architecture utilise l’application Microsoft Authenticator pour renforcer la sécurité et l’intégration avec le serveur NPS.

|Ressources |Lien |
|---|---|
|Fonctionnement : Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)|
|Activation Déploiement basé sur le cloud Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)|
|

## <a name="strong-authorisation"></a>Autorisation forte

Une fois qu’un compte privilégié a été identifié de manière sécurisée, il peut être autorisé à accéder aux ressources. L’autorisation contrôle et gère les privilèges attribués à un compte spécifique. Les processus d’autorisation renforcés s’alignent sur les huit stratégies essentielles de ACSC pour atténuer les incidents de sécurité informatiques liés à la restriction des privilèges d’administrateur.

### <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

L’accès pour effectuer des actions privilégiées dans Azure est basé sur les rôles attribués à ce compte. Azure comprend une gamme de rôles complète et granulaire disposant d’autorisations spécifiques pour effectuer des tâches spécifiques. Ces rôles peuvent être accordés à plusieurs niveaux, tels qu’un abonnement ou un groupe de ressources. L’attribution et la gestion des autorisations des rôles sont basées sur des comptes et des groupes dans Azure Active Directory et sont gérées via Access Control (IAM) dans Azure.

|Ressources |Lien |
|---|---|
|Contrôle d’accès en fonction du rôle Azure|[https://docs.microsoft.com/azure/role-based-access-control](https://docs.microsoft.com/azure/role-based-access-control)|
|Comprendre les définitions de rôles|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)|
|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

PIM est un composant Azure Active Directory qui contrôle l’accès aux rôles privilégiés. Les comptes privilégiés n’ont pas besoin d’un accès privilégié permanent ou par défaut, mais peuvent demander un accès privilégié pendant une période donnée pour effectuer des activités privilégiées. PIM fournit des contrôles supplémentaires pour la maintenance et la restriction de l’accès privilégié, ainsi que la journalisation et l’audit pour suivre les instances d’utilisation des privilèges.

|Ressources |Lien |
|---|---|
|Documentation d’Azure Privileged Identity Management (PIM)|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)|
|Commencer à utiliser PIM|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started)|
|

### <a name="conditional-access"></a>Accès conditionnel

L’accès conditionnel est un composant de Azure Active Directory qui autorise ou refuse l’accès aux ressources en fonction de conditions. Ces conditions peuvent être basées sur l’emplacement réseau, le type d’appareil, l’état de conformité, l’appartenance au groupe et bien plus encore. L’accès conditionnel est utilisé pour appliquer l’authentification MFA, la gestion des appareils et la conformité via Intune et l’appartenance aux groupes des comptes d’administration.

|Ressources |Lien |
|---|---|
|Documentation relative à l’accès conditionnel|[https://docs.microsoft.com/azure/active-directory/conditional-access](https://docs.microsoft.com/azure/active-directory/conditional-access)|
|Activation Exiger des appareils gérés pour accéder aux applications cloud avec l’accès conditionnel|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|
|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le contrôle des flux de trafic via vos composants de passerelle dans Azure, veuillez consulter la section [Gestion et contrôle du trafic d’entrée dans Azure](gateway-ingress-traffic.md).
