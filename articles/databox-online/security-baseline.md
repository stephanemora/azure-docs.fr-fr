---
title: Base de référence de sécurité Azure pour Azure Stack Edge
description: La base de référence de sécurité Azure Stack Edge fournit des instructions et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans le Benchmark de sécurité Azure.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 460fd66ed4651248639334caa55eb8facbce866d
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452371"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Base de référence de sécurité Azure pour Azure Stack Edge

Cette base de référence de sécurité applique les instructions du [Benchmark de sécurité Azure version 2.0](../security/benchmarks/overview.md) à Microsoft Azure Stack Edge. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure. Le contenu est regroupé selon les **contrôles de sécurité** définis par le benchmark de sécurité Azure et les instructions associées applicables à Azure Stack Edge. Les **contrôles** non applicables à Azure Stack Edge ont été exclus.

Pour savoir comment s’effectue le mappage intégral d’Azure Stack Edge au benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité Azure Stack Edge](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1 : Implémenter la sécurité pour le trafic interne

**Conseils** : Les clients déploient un appareil physique Azure Stack Edge fourni par Microsoft dans leur réseau privé pour l’accès interne et disposent d’options pour le sécuriser davantage. Par exemple, l’appareil Azure Stack Edge est accessible sur le réseau interne du client et nécessite une adresse IP configurée par ce dernier. En outre, un mot de passe d’accès est choisi par le client pour accéder à l’interface utilisateur de l’appareil. 

Le trafic interne est davantage sécurisé par :

- La version 1.2 du protocole TLS est nécessaire pour le Portail Azure et la gestion des kits de développement logiciel (SDK) de l’appareil Azure Stack Edge.

- Tout accès client à l’appareil s’effectue via l’interface utilisateur Web locale à l’aide de la version 1.2 du protocole TLS par défaut.

- Seul un appareil Azure Stack Edge Pro autorisé peut rejoindre le service Azure Stack Edge créé par le client lors de sa souscription à Azure.

Des informations supplémentaires sont disponibles sur les liens référencés.
 
- [Configurer TLS 1.2 sur les clients Windows accédant à l’appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-j-series-configure-tls-settings.md)

- [Démarrage rapide - Bien démarrer avec Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-quickstart.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2 : Interconnecter des réseaux privés

**Conseils** : Les clients peuvent choisir un réseau privé virtuel (VPN) de point à site pour connecter un appareil Azure Stack Edge de leur réseau privé local au réseau Azure. Le VPN fournit une deuxième couche de chiffrement pour les flux de données via le protocole TLS entre l’appareil du client et Azure. 

Les clients peuvent configurer un VPN sur leur appareil Azure Stack Edge via le Portail Azure ou Azure PowerShell.

- [Configurer un VPN Azure via un script Azure PowerShell pour Azure Stack Edge Pro R et Azure Stack Edge Mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Configurer TLS 1.2 sur les clients Windows accédant à l’appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-j-series-configure-tls-settings.md)

- [Tutoriel : Configurer des certificats pour votre appareil Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3 : Établir un accès réseau privé aux services Azure

**Conseils** : Les clients peuvent choisir un réseau privé virtuel (VPN) de point à site pour connecter un appareil Azure Stack Edge de leur réseau privé local au réseau Azure. Le VPN fournit une deuxième couche de chiffrement pour les flux de données via le protocole TLS entre l’appareil du client et Azure. 

- [Configurer un VPN Azure via un script Azure PowerShell pour Azure Stack Edge Pro R et Azure Stack Edge Mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Configurer TLS 1.2 sur les clients Windows accédant à l’appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-j-series-configure-tls-settings.md)

- [Tutoriel : Configurer des certificats pour votre appareil Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4 : Protégez les applications et les services contre les attaques réseau externes

**Conseils** : L’appareil Azure Stack Edge intègre les fonctionnalités standard de protection du réseau Windows Server qui ne peuvent pas être configurées par les clients.

Les clients peuvent choisir de sécuriser leur réseau privé connecté à l’appareil Azure Stack Edge pour lutter contre les attaques externes à l’aide d’une appliance virtuelle réseau, telle qu’un pare-feu avec des protections avancées contre le déni de service distribué (DDoS) .

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5 : Déployer des systèmes de détection et de prévention des intrusions (IDS/IPS)

**Conseils** : Les points de terminaison qu’Azure Stack Edge utilise sont tous gérés par Microsoft. Les clients sont responsables de tous les contrôles supplémentaires qu’ils souhaitent déployer sur leurs systèmes locaux.

L’appareil Azure Stack Edge utilise des fonctionnalités intégrées de détection d’intrusion pour protéger le service. 

- [Understand Azure Stack Edge security](azure-stack-edge-security.md) (Présentation de la sécurité d’Azure Stack Edge)

- [Port information for Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md) (Informations de port pour Azure Stack Edge)

- [Get hardware and software intrusion detection logs](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs) (Accès aux journaux de détection des intrusions matérielles et logicielles)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

## <a name="identity-management"></a>Gestion des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des identités](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2 : Gérer les identités d’application de façon sécurisée et automatique

**Conseils** : Le système attribue automatiquement à tous les appareils Azure Stack Edge une identité managée dans Azure Active Directory (Azure AD). Actuellement, l’identité managée est utilisée pour la gestion Cloud des ordinateurs virtuels hébergés sur Azure Stack Edge.

Les appareils Azure Stack Edge démarrent en état verrouillé pour l’accès local. Pour le compte administrateur de l’appareil local, vous devez vous connecter à votre appareil via l’interface utilisateur Web locale ou l’interface PowerShell et définir un mot de passe fort. Stockez et gérez les informations d’identification administrateur de votre appareil dans un emplacement sécurisé, tel qu’Azure Key Vault et effectuez régulièrement la rotation du mot de passe administrateur en fonction des directives de votre organisation.

- [Protéger l’appareil Azure Stack Edge via un mot de passe](azure-stack-edge-security.md#protect-the-device-via-password)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3 : Utiliser l’authentification unique Azure AD pour l’accès aux applications

**Conseils** : L’authentification unique n’est pas prise en charge pour les appareils de point de terminaison Azure Stack Edge. Toutefois, vous pouvez choisir d’activer l’authentification unique basée sur Azure Active Directory standard (Azure AD) pour sécuriser l’accès à vos ressources cloud Azure.

- [Comprendre l’authentification unique d’application avec Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4 : Utiliser des contrôles d’authentification renforcés pour tous les accès basés sur Azure Active Directory

**Conseils** : La fonctionnalité d’authentification multifacteur est un contrôle d’authentification puissant, mais est en option pour les utilisateurs du service Azure Stack Edge. Elle peut être utilisée pour des scénarios pris en charge tels que la gestion des périphériques des appareils Azure Stack Edge sur le Portail Azure. Notez que l’accès local aux appareils Azure Stack Edge ne prend pas en charge l’authentification multifacteur.

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5 : Surveiller et alerter en cas d’anomalies de compte

**Conseils** : Activez Azure Monitor pour collecter des journaux d’appareil ou de conteneur pour votre service Azure Stack Edge. Surveiller les conteneurs, tels que ceux qui exécutent plusieurs applications de calcul sur le cluster Kubernetes de votre appareil.

En outre, un package de journaux qui comprend les journaux d’audit peut être collecté au niveau de l’interface utilisateur Web locale de l’appareil Azure Stack Edge. Notez que le package de journaux n’est pas disponible sur le Portail Azure.
 
- [Activer Azure Monitor sur votre appareil Azure Stack Edge Pro](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [Collecter un package de journaux](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6 : Restreindre l’accès aux ressources Azure en fonction des conditions

**Conseils** : L’accès conditionnel d’Azure Active Directory (Azure AD) est une fonctionnalité en option pour l’authentification au service Azure Stack Edge. Le service Azure Stack Edge est une ressource du portail Azure, qui vous permet de gérer un appareil Azure Stack Edge Pro depuis divers emplacements géographiques. 

- [Qu’est-ce que l’accès conditionnel](../active-directory/conditional-access/overview.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Suivez les meilleures pratiques pour protéger les informations d’identification, telles que :

- Clé d’activation utilisée pour activer l’appareil avec la ressource Azure Stack Edge dans Azure.
- Informations d'identification de connexion pour accéder à l’appareil Azure Stack Edge.
- Fichiers clés qui peuvent faciliter la récupération d’un appareil Azure Stack Edge.
- Clé de chiffrement du canal

Faites tourner puis synchronisez vos clés de compte de stockage régulièrement pour mieux protéger votre compte de stockage des utilisateurs non autorisés.

- [Protection des données et sécurité Azure Stack Edge Pro](azure-stack-edge-security.md)

- [Synchroniser des clés de stockage](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="privileged-access"></a>Accès privilégié

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Accès privilégié](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2 : Limiter l’accès administratif aux systèmes critiques de l’entreprise

**Conseils** : La solution Azure Stack Edge possède plusieurs composants avec des contrôles d’accès renforcés pour restreindre l’accès aux appareils critiques de l’entreprise. Votre organisation doit disposer d’un Accord Entreprise (AE), d’un fournisseur de solutions Cloud ou d’un abonnement Microsoft Azure Sponsorship pour configurer et gérer l’appareil : 

Le service Azure Stack Edge est protégé par les fonctionnalités de sécurité d’Azure en tant que service de gestion hébergé par Azure. Vous pouvez obtenir la clé de chiffrement de votre ressource dans les propriétés de l’appareil pour les opérations de gestion du kit de développement logiciel, mais vous ne pouvez afficher la clé de chiffrement que si vous disposez des autorisations appropriées pour l’API Resource Graph.

L’appareil Azure Stack Edge Pro est un appareil local qui vous permet de transformer vos données en les traitant localement avant de les envoyer à Azure. Votre appareil :

- a besoin d’une clé d’activation pour accéder au service Azure Stack Edge.
- est protégé à tout moment par un mot de passe.
- a activé le démarrage sécurisé.

- est un appareil verrouillé. Le contrôleur de gestion de la carte de base (BMC) et le BIOS sont protégés par mot de passe. Le BIOS est protégé par accès utilisateur limité.
- exécute Windows Defender Device Guard. Device Guard permet d’exécuter uniquement les applications de confiance que vous définissez dans vos stratégies d’intégrité du code.

Suivez les meilleures pratiques pour protéger l’ensemble des informations d’identification et des codes secrets utilisés pour accéder à Azure Stack Edge. 

- [Bonnes pratiques relatives aux mots de passe](azure-stack-edge-security.md#protect-the-device-via-password)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3 : Examiner et rapprocher régulièrement les accès utilisateur

**Conseils** : Azure Stack Edge dispose d’un utilisateur nommé « EdgeUser » qui peut configurer l’appareil. Il dispose également d’un utilisateur Azure Resource Manager nommé « EdgeArmUser » pour les fonctionnalités locales d’Azure Resource Manager sur l’appareil. 

Les meilleures pratiques doivent être suivies pour protéger les éléments suivants :

- Les informations d’identification utilisées pour accéder localement à l’appareil

- Les informations d’identification de partage SMB

- L’accès aux systèmes clients configurés pour utiliser les partages NFS

- Les clés de compte de stockage local utilisées pour accéder aux comptes de stockage locaux lors de l’utilisation de l’API REST du service BLOB.

Des informations supplémentaires sont disponibles dans le lien indiqué.

- [Informations sur la sécurité de vos appareils Azure Stack Edge](azure-stack-edge-security.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6 : Utiliser des stations de travail d’accès privilégié

**Conseils** : La sécurité et l’isolement des stations de travail sont très importants pour la sécurité des rôles sensibles, tels que les rôles d’administrateur, de développeur et d’opérateur de service critique. Utilisez des stations de travail utilisateur très sécurisées avec ou sans Azure Bastion, pour les tâches administratives. Utilisez Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (MDATP) et Microsoft Intune pour déployer une station de travail utilisateur sécurisée pour les tâches administratives. 

Les stations de travail sécurisées peuvent être gérées de manière centralisée pour appliquer une configuration sécurisée, notamment une authentification forte, des lignes de base logicielles et matérielles et un accès réseau et logique restreint.

- [Comprendre les stations de travail d’accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Déployer une station de travail d’accès privilégié](/security/compass/privileged-access-deployment)

**Supervision d’Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2 : Protection des données sensibles

**Conseils** : Azure Stack Edge considère toutes les données d’interaction comme des données sensibles ; seuls les utilisateurs autorisés peuvent accéder à ces données. Vous devez suivre les meilleures pratiques pour protéger les informations d’identification utilisées pour accéder au service Azure Stack Edge.

- [Protection des données et sécurité Azure Stack Edge Pro](azure-stack-edge-security.md#protect-the-device-via-password)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4 : Chiffrement des informations sensibles en transit

**Conseils** : Azure Stack Edge utilise des canaux sécurisés pour les flux de données. Ces règles sont les suivantes :

- La version 1.2 du protocole TLS standard est utilisée pour les données qui transitent entre l’appareil et Azure Cloud. Il n’existe aucun protocole de secours pour TLS 1.1 et versions antérieures. Les communications de l’agent seront bloquées si TLS 1.2 n’est pas pris en charge. La version 1.2 du protocole TLS est également requise pour la gestion du Portail Azure et du kit de développement logiciel (SDK).

- Lorsque les clients accèdent à votre appareil via l’interface utilisateur Web locale d’un navigateur, la version 1.2 du protocole TLS standard est utilisée comme protocole de sécurité par défaut.

La meilleure pratique consiste à configurer votre navigateur pour utiliser TLS 1.2. Utilisez SMB 3.0 avec chiffrement pour protéger les données lorsque vous les copiez depuis vos serveurs de données.

- [Meilleures pratiques pour protéger vos flux de données](azure-stack-edge-security.md#protect-data-in-flight)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

## <a name="asset-management"></a>Gestion des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des ressources](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1 : S’assurer que l’équipe de sécurité a une visibilité sur les risques pour les ressources

**Conseils** : Assurez-vous que les équipes de sécurité reçoivent des autorisations de lecteur de sécurité dans votre locataire et vos abonnements Azure afin qu’elles puissent surveiller les risques de sécurité à l’aide d’Azure Security Center. 

Selon la façon dont les responsabilités de l’équipe de sécurité sont structurées, la surveillance des risques de sécurité peut tenir de la responsabilité d’une équipe de sécurité centrale ou d’une équipe locale. Cela dit, les insights et les risques liés à la sécurité doivent toujours être agrégés de manière centralisée au sein d’une organisation. 

Les autorisations de lecteur de sécurité peuvent être appliquées globalement à un locataire entier (groupe d’administration racine) ou étendues à des groupes d’administration ou à des abonnements spécifiques. 

Notez que des autorisations supplémentaires peuvent être nécessaires pour obtenir une visibilité sur les charges de travail et les services. 

- [Vue d’ensemble du rôle lecteur de sécurité](../role-based-access-control/built-in-roles.md#security-reader)

- [Vue d'ensemble des groupes d'administration Azure](../governance/management-groups/overview.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Conseils** : Seuls les utilisateurs autorisés tels que « EdgeArmUser », peuvent accéder aux API d’appareil Azure Stack Edge via l’Azure Resource Manager local. Les mots de passe des comptes d’utilisateur ne peuvent être gérés que sur le Portail Azure. 

- [Définir un mot de passe Azure Resource Manager](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6 : Utiliser uniquement des applications approuvées dans les ressources de calcul

**Conseils** : Vous pouvez installer vos propres applications pour qu’elles s’exécutent sur n’importe quel ordinateur virtuel créé localement. Utilisez des scripts PowerShell pour créer des ordinateurs virtuels de calcul locaux sur votre appareil Stack Edge. Nous vous recommandons vivement de n’installer que des applications approuvées à exécuter sur les ordinateurs virtuels locaux. 

- [Guide pratique pour contrôler l’exécution des scripts PowerShell dans des environnements Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="logging-and-threat-detection"></a>Journalisation et détection des menaces

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Journalisation et détection des menaces](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1 : Activer la détection des menaces pour les ressources Azure

**Conseils** : Azure Stack Edge n’offre pas de fonctionnalités de détection des menaces. Toutefois, les clients peuvent collecter des journaux d’audit dans un package de journaux pour la détection et l’analyse hors connexion des menaces. 

- [Collecter un package de support pour l’appareil Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3 : Activer la journalisation pour les activités réseau Azure

**Conseils** : Azure Stack Edge dispose de journaux d’audit réseau activés dans le cadre du package de journaux téléchargeable. Ces journaux peuvent être analysés pour mettre en œuvre une surveillance en temps presque réel pour vos appareils Azure Stack Edge.

- [Azure Stack Edge gather a support package](azure-stack-edge-gpu-troubleshoot.md#collect-support-package) (Collecte d’un package de journaux pour Azure Stack Edge)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4 : Activer la journalisation pour les ressources Azure

**Conseils** : La surveillance en temps réel avec les journaux n’est actuellement pas prise en charge pour Azure Stack Edge. Il existe une fonctionnalité de collecte d’un package de journaux qui vous permet d’analyser les différents journaux qu’il contient, tels que le pare-feu, les logiciels, les intrusions matérielles et les journaux des événements système de votre appareil Azure Stack Edge Pro. Notez que l’intrusion logicielle ou les journaux de pare-feu par défaut sont collectés pour le trafic entrant et sortant.

- [Gather a support package for Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package) (Collecte d’un package de journaux pour Azure Stack Edge)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5 : Centraliser la gestion et l’analyse des journaux de sécurité

**Conseils** : La surveillance en temps réel avec les journaux n’est actuellement pas prise en charge pour Azure Stack Edge. Toutefois, vous pouvez collecter un package de journaux qui vous permet d’analyser les différents journaux qui s’y trouvent.  Le package de journaux est compressé et peut être téléchargé à partir du chemin d’accès de votre choix. Vous pouvez décompresser le package et afficher les fichiers journaux système qu’il contient. Vous pouvez également envoyer ces journaux dans un outil de gestion des événements d’informations de sécurité ou dans un autre emplacement de stockage centralisé à des fins d’analyse.

- [Gather a support package for Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package) (Collecte d’un package de journaux pour Azure Stack Edge)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6 : Configurer la rétention du stockage des journaux

**Conseils** : La période de rétention du stockage des journaux ne peut pas être modifiée au niveau de l’appareil Azure Stack Edge. Les anciens journaux sont supprimés définitivement si nécessaire. Vous pouvez collecter des packages de journaux à partir de l’appareil à intervalles réguliers pour tout besoin de conservation des journaux pendant une période plus longue. 

- [Gather a support package for Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package) (Collecte d’un package de journaux pour Azure Stack Edge)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7 : Utiliser des sources de synchronisation date/heure approuvées

**Conseils** : Azure Stack Edge utilise time.windows.com, un serveur de fournisseur de temps réseau de Microsoft.  Azure Stack Edge permet également au client de configurer le serveur de protocole de temps réseau (NTP) de son choix.

- [Configure Azure Stack Edge device time settings](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time) (Configurer les paramètres de temps de l’appareil Azure Stack Edge)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1 : Préparation – mettre à jour le processus de réponse aux incidents pour Azure

**Conseils** : Assurez-vous que votre plan de réponse aux incidents organisationnel comprend des processus : 

- pour répondre à des incidents de sécurité

- qui ont été mis à jour pour Azure Cloud
 
- qui sont régulièrement testés pour garantir la préparation

Il est recommandé de mettre en œuvre la sécurité à l’aide de processus standardisés de réponse aux incidents dans l’environnement d’entreprise.

- [Implémenter la sécurité dans l’environnement de l’entreprise](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guide de référence sur les réponses aux incidents](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2 : Préparation – configurer la notification d’incident

**Conseils** : Configurez les coordonnées des personnes à contacter en cas d’incident de sécurité dans Azure Security Center. Microsoft utilisera ces coordonnées afin de vous contacter si le Microsoft Security Response Center (MSRC) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé. Vous avez également la possibilité de personnaliser les alertes et les notifications d’incidents dans différents services Azure en fonction de vos besoins en matière de réponse aux incidents. 

- [Comment définir le contact de sécurité d’Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Supervision d’Azure Security Center** : Oui

**Responsabilité** : Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3 : Détection et analyse - créer des incidents en fonction d’alertes de haute qualité 

**Conseils** : Veillez à disposer d’un processus de création d’alertes de haute qualité et de mesure de leur qualité. Cela vous permet de tirer les leçons des incidents passés et de classer par ordre de priorité les alertes pour les analystes, afin qu’ils ne perdent pas de temps sur les faux positifs. Créez des alertes de haute qualité basées sur vos expériences des incidents passés, des sources validées de la communauté et des outils conçus pour générer et nettoyer les alertes en enregistrant et en mettant en corrélation diverses sources d’alerte. 

Azure Security Center fournit des alertes de haute qualité sur de nombreuses ressources Azure. Vous pouvez utiliser le connecteur de données Security Center pour diffuser en continu les alertes vers Azure Sentinel. Créez des règles d’alerte avancées avec Azure Sentinel pour générer automatiquement des incidents à des fins d’enquête. 

Exportez vos alertes et recommandations Security Center dans Azure Sentinel à l’aide de la fonctionnalité d’exportation pour identifier les risques pesant sur les ressources Azure. Il est recommandé de créer un processus automatisé d’exportation des alertes et des recommandations à des fins de sécurité continue.

- [Procédure de configuration de l’exportation](../security-center/continuous-export.md)

- [Comment envoyer des alertes à Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4 : Détection et analyse – enquêter sur un incident

**Conseils** : Veiller à ce que les analystes puissent interroger et utiliser diverses sources de données afin d’obtenir une vision complète de l’activité lorsqu’ils enquêtent sur des incidents potentiels. Différents types de journaux doivent être collectés pour suivre les activités d’un attaquant potentiel tout au long de la chaîne de destruction afin d’éviter les angles morts.  Veiller également à ce que les insights et les enseignements soient consignés à des fins de référence historique.  

Les sources de données à examiner comprennent les sources de journalisation centralisées qui sont déjà collectées auprès des services et des systèmes en fonctionnement concernés, mais elles peuvent également inclure les éléments suivants :

- Données réseau : utilisez les journaux de flux des groupes de sécurité réseau, Azure Network Watcher et Azure Monitor pour capturer des journaux de flux réseau et d’autres informations analytiques. 

- Captures instantanées des systèmes en fonctionnement : 

    - Utilisez la capacité de capture instantanée de la machine virtuelle Azure pour créer un instantané du disque du système en fonctionnement. 

    - Choisissez la fonctionnalité native de sauvegarde de la mémoire du système d’exploitation pour créer un instantané de la mémoire du système d’exploitation.

    - Utilisez la fonctionnalité de capture instantanée des services Azure ou de celle d’un logiciel tiers pour créer des instantanés des systèmes d’exploitation.

Azure Sentinel fournit des analyses de données approfondies sur pratiquement toutes les sources de journal et un portail de gestion des cas pour gérer le cycle de vie complet des incidents. Les renseignements obtenus au cours d’une enquête peuvent être associés à un incident à des fins de suivi et de rapport. 

- [Capture instantanée du disque d’un ordinateur Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Capture instantanée du disque d’un ordinateur Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Collecte de l’image mémoire et des informations de diagnostic par le support Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Examiner les incidents avec Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5 : Détection et analyse – classer par ordre de priorité les incidents

**Conseils** : Donnez aux analystes le contexte sur lequel les incidents doivent se concentrer en premier lieu en fonction de la gravité de l’alerte et de la sensibilité des ressources. Utilisez le niveau de gravité attribué à chaque alerte par Azure Security Center pour vous aider à prioriser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez les ressources à l’aide d’étiquettes et créez un système de nommage pour identifier et classer les ressources Azure, en particulier celles qui traitent des données sensibles.  Il vous incombe de hiérarchiser le traitement des alertes en fonction de la criticité des ressources et de l’environnement Azure où l’incident s’est produit.

- [Alertes de sécurité dans le Centre de sécurité Azure](../security-center/security-center-alerts-overview.md)

- [Organisation des ressources Azure à l’aide de catégories](../azure-resource-manager/management/tag-resources.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6 : Confinement, éradication et récupération – automatiser la gestion des incidents

**Conseils** : Automatisez les tâches manuelles répétitives pour accélérer le temps de réponse et réduire la charge de travail des analystes. Les tâches manuelles sont plus longues à exécuter, ce qui ralentit chaque incident et réduit le nombre d’incidents qu’un analyste peut traiter. Les tâches manuelles augmentent également la fatigue des analystes, ce qui accroît le risque d’erreur humaine entraînant des retards et dégrade la capacité des analystes à se concentrer efficacement sur des tâches complexes. Utilisez les fonctionnalités d’automatisation des workflows dans Azure Security Center et Azure Sentinel pour déclencher automatiquement des actions ou exécuter un playbook pour répondre aux alertes de sécurité entrantes. Le playbook prend des mesures, telles que l’envoi de notifications, la désactivation de comptes et l’isolement des réseaux problématiques. 

- [Configurer l’automatisation du workflow dans Security Center](../security-center/workflow-automation.md)

- [Configurer des réponses automatisées aux menaces dans Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurer des réponses automatisées aux menaces dans Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="posture-and-vulnerability-management"></a>Gestion de la posture et des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion de la posture et des vulnérabilités](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3 : Établir des configurations sécurisées pour des ressources de calcul

**Conseils** : Azure Stack Edge offre la prise en charge de la création de configurations sécurisées pour les ordinateurs virtuels locaux créés par les clients. Il est fortement recommandé de suivre les instructions de Microsoft pour établir les bases de référence de sécurité lors de la création d’ordinateurs virtuels locaux.

- [Télécharger les bases de référence de sécurité incluses dans la boîte à outils de la conformité de la sécurité](/windows/security/threat-protection/windows-security-baselines)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4 : Supporter des configurations sécurisées pour des ressources de calcul

**Conseils** : Azure Stack Edge ne propose aucune prise en charge de maintien des configurations sécurisées pour les ordinateurs virtuels locaux créés par les clients. Il est vivement recommandé aux clients d’utiliser les boîtes à outils de la conformité de la sécurité pour garantir la sécurité des configurations de leurs ressources informatiques.

Le système d’exploitation hôte et les ordinateurs virtuels gérés par Azure Stack Edge gèrent leurs configurations de sécurité. 

- [Lignes de base de la sécurité Windows](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5 : Stocker de manière sécurisée des images de système d’exploitation et des images conteneur personnalisées

**Conseils** : Les systèmes d’exploitation hôtes et les ordinateurs virtuels gérés par Azure Stack Edge sont stockés de manière sécurisée. 

Les clients peuvent importer leurs propres ordinateurs virtuels et images conteneurs et sont responsables de leur sécurité.

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7 : Corriger rapidement et automatiquement des vulnérabilités logicielles

**Conseils** : Azure Stack Edge propose régulièrement des mises à jour de patchs et avertit les clients lorsque ces mises à jour sont disponibles pour corriger les vulnérabilités. Il incombe au client de maintenir à jour leurs appareils et leurs ordinateurs virtuels (qu’ils ont créés) avec les patchs les plus récents.

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8 : Effectuer une simulation d’attaque régulière

**Conseils** : Selon les besoins, effectuez un test d’intrusion ou des activités Red Team sur vos ressources Azure et résolvez tous les problèmes de sécurité critiques détectés.
Suivez les règles d’engagement de pénétration du cloud Microsoft pour vous assurer que vos tests d’intrusion sont conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft.

- [Test d’intrusion dans Azure](../security/fundamentals/pen-testing.md)

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Partagé

## <a name="endpoint-security"></a>Sécurité des points de terminaison

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Sécurité des points de terminaison](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1 : Utiliser la détection de point de terminaison et réponse (EDR)

**Conseils** : Azure Stack Edge ne prend pas en charge directement la protection évolutive des points de terminaison (PEPT). Toutefois, vous pouvez collecter un package de journaux et récupérer les journaux d’audit. Ces journaux peuvent ensuite être analysés pour vérifier les activités anormales. 

- [Collecter un package de support pour l’appareil Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2 : Utiliser un logiciel anti-programme malveillant moderne géré de manière centralisée

**Conseils** : Azure Stack Edge utilise le contrôle d’application Windows Defender avec une stratégie stricte d’intégrité du code qui n’autorise l’exécution que d’applications et de scripts prédéterminés. Le logiciel anti-programmes malveillants de protection en temps réel Windows Defender est également activé. Le client peut choisir d’exécuter des logiciels anti-programmes malveillants dans les ordinateurs virtuels de calcul qu’il crée pour s’exécuter localement sur l’appareil Azure Stack Edge.

**Supervision Azure Security Center** : actuellement non disponible

**Responsabilité** : Customer

## <a name="backup-and-recovery"></a>Sauvegarde et récupération

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Sauvegarde et récupération](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1 : Garantir des sauvegardes automatiques régulières

**Conseils** : Il est recommandé d’effectuer régulièrement des sauvegardes de votre appareil Azure Stack Edge avec Sauvegarde Azure et d’autres solutions tierces de protection des données pour protéger les données des ordinateurs virtuels déployés sur l’appareil. 

Les solutions tierces de protection des données telles que Cohesity, CommVault et Veritas peuvent être également utilisées pour sauvegarder les données dans les partages SMB ou NFS locaux. 

Des informations supplémentaires sont disponibles sur les liens référencés.

- [Se préparer à la défaillance d’un appareil](azure-stack-edge-gpu-prepare-device-failure.md)

- [Protéger les fichiers et dossiers sur les machines virtuelles](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2 : Chiffrer les données de sauvegarde

**Conseils** : Assurez-vous que vos sauvegardes sont protégées contre les attaques. Cela doit inclure le chiffrement des sauvegardes afin de vous protéger contre la perte de confidentialité.  Pour plus d’informations, reportez-vous à la solution de sauvegarde de votre choix.

- [Protéger des données dans des partages locaux Edge](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [Protéger les fichiers et dossiers sur les machines virtuelles](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Conseils** : Effectuez régulièrement une restauration des données de vos sauvegardes. 

- [Procédures de récupération pour Azure Stack Edge](azure-stack-edge-gpu-recover-device-failure.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4 : Atténuer les risques liés aux clés perdues

**Conseils** : Assurez-vous que toutes les sauvegardes d’Azure Stack Edge, y compris les clés gérées par le client, sont protégées conformément aux meilleures pratiques de l’organisation. Votre appareil Azure Stack Edge est associé à un compte de stockage Azure, utilisé comme référentiel de destination pour vos données Azure. 

L’accès au compte de stockage Azure est contrôlé par les abonnements Azure et par deux clés d’accès au stockage de 512 bits associées à ce compte de stockage. L’une des clés d’accès est utilisée à des fins d’authentification lorsque l’appareil Azure Stack Edge accède au compte de stockage. L’autre clé est réservée à la rotation périodique des clés. Assurez-vous que les meilleures pratiques en matière de sécurité sont utilisées pour la rotation des clés.

- [Protect Azure Stack Edge device data in the Azure Storage accounts](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts) (Protéger les données de l’appareil Azure Stack Edge dans les comptes de stockage Azure)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="governance-and-strategy"></a>Gouvernance et stratégie

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gouvernance et stratégie](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1 : Définir la stratégie de gestion des ressources et de protection des données 

**Conseils** : Veillez à documenter et à communiquer une stratégie claire pour la surveillance et la protection continues des systèmes et des données. Hiérarchisez la découverte, l’évaluation, la protection et la surveillance des données et systèmes critiques de l’entreprise. 

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Norme de classification des données en fonction des risques pour l’entreprise

-   Visibilité de l’organisation de sécurité sur les risques et l’inventaire des actifs 

-   Approbation de l’organisation de sécurité pour les services Azure en vue de leur utilisation 

-   Sécurité des ressources tout au long de leur cycle de vie

-   Stratégie de contrôle d’accès requise conformément à la classification des données organisationnelles

-   Utilisation des fonctionnalités de protection des données Azure natives et de tiers

-   Exigences de chiffrement des données pour les cas d’utilisation en transit et au repos

-   Normes de chiffrement appropriées

Pour plus d’informations, consultez les références suivantes :
- [Recommandation d’architecture de sécurité Azure - Stockage, données et chiffrement](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Notions de base de la sécurité Azure - Sécurité, chiffrement et stockage des données Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Meilleures pratiques en matière de chiffrement et de sécurité des données Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Benchmark de sécurité Azure - Gestion des ressources](../security/benchmarks/security-controls-v2-asset-management.md)

- [Benchmark de sécurité Azure - Protection des données](../security/benchmarks/security-controls-v2-data-protection.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2 : Définir la stratégie de segmentation d'entreprise 

**Conseils** : Établissez une stratégie à l'échelle de l'entreprise pour segmenter l'accès aux ressources à l'aide d'une combinaison de contrôles d'identité, de réseau, d'application, d'abonnement, de groupe de gestion et autres.

Trouvez le bon équilibre entre la nécessité de séparation sur le plan de la sécurité et la nécessité d'exécuter quotidiennement les systèmes qui doivent communiquer entre eux et accéder aux données.

Veillez à ce que la stratégie de segmentation soit implémentée de manière cohérente pour tous les types de contrôle, y compris pour les modèles d'identité, d'accès et de sécurité du réseau, les modèles d'autorisation/d'accès aux applications et les contrôles des processus humains.

- [Aide relative à la stratégie de segmentation dans Azure (vidéo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Aide relative à la stratégie de segmentation dans Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Aligner la segmentation du réseau avec la stratégie de segmentation d’entreprise](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3 : Définir la stratégie de gestion de la posture de la sécurité

**Conseils** : Mesurez et atténuez en permanence les risques liés à vos ressources individuelles et à l’environnement dans lequel elles sont hébergées. Priorisez les ressources à valeur élevée et les surfaces d’attaque hautement exposées, comme les applications publiées, les points d’entrée et de sortie du réseau, les points de terminaison utilisateur et administrateur, etc.

- [Benchmark de sécurité Azure - Gestion de la posture et des vulnérabilités](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4 : Aligner les rôles et les responsabilités de l’organisation

**Conseils** : Veillez à documenter et à communiquer une stratégie claire pour les rôles et les responsabilités de votre organisation de sécurité. Veillez à définir clairement les responsabilités pour les décisions relatives à la sécurité, à former tout le monde au modèle de responsabilité partagée et à former les équipes techniques à la technologie permettant de sécuriser le cloud.

- [Meilleures pratiques pour la sécurité Azure 1 – Personnes : Former les équipes pour le parcours vers la sécurité dans le cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Meilleures pratiques pour la sécurité Azure 2 – Personnes : Former les équipes pour les technologies de sécurité dans le cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Meilleures pratiques pour la sécurité Azure 3 – Processus : Affecter les responsabilités pour les décisions de sécurité dans le cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5 : Définir la stratégie de sécurité réseau

**Conseils** : Établissez une approche de sécurité réseau Azure dans le cadre de la stratégie de contrôle d’accès de sécurité globale de votre organisation.  

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Responsabilité centralisée pour la gestion et la sécurité du réseau

-   Modèle de segmentation de réseau virtuel aligné avec la stratégie de segmentation de l’entreprise

-   Stratégie de correction dans différents scénarios de menaces et d’attaques

-   Stratégie de périphérie d’Internet et d’entrée et de sortie

-   Stratégie de cloud hybride et d’interconnexion locale

-   Artefacts de sécurité réseau à jour (par exemple diagrammes réseau, architecture de réseau de référence)

Pour plus d’informations, consultez les références suivantes :
- [Meilleures pratiques pour la sécurité Azure 11 – Architecture. Stratégie de sécurité unifiée unique](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark de sécurité Azure – Sécurité réseau](../security/benchmarks/index.yml)

- [Vue d’ensemble de la sécurité réseau d’Azure](../security/fundamentals/network-overview.md)

- [Stratégie d’architecture de réseau d’entreprise](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6 : Définir une stratégie d’accès privilégié et d’identité

**Conseils** : Établissez une approche d’identité Azure et d’accès privilégié dans le cadre de la stratégie de contrôle d’accès de sécurité globale de votre organisation.  

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Un système centralisé d’identité et d’authentification et son interconnexion avec d’autres systèmes d’identité internes et externes

-   Méthodes d’authentification fortes dans différents cas d’usage et différentes conditions

-   Protection des utilisateurs disposant de privilèges élevés

-   Surveillance et gestion des activités anormales des utilisateurs  

-   Vérification de l’identité et de l’accès des utilisateurs et processus de rapprochement

Pour plus d’informations, consultez les références suivantes :

- [Benchmark de sécurité Azure - Gestion des identités](../security/benchmarks/security-controls-v2-identity-management.md)

- [Benchmark de sécurité Azure - Accès privilégié](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Meilleures pratiques pour la sécurité Azure 11 – Architecture. Stratégie de sécurité unifiée unique](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Vue d’ensemble de la sécurité et de la gestion des identités Azure](../security/fundamentals/identity-management-overview.md)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7 : Définir la stratégie de journalisation et de réponse aux menaces

**Conseils** : Établissez une stratégie de journalisation et de réponse aux menaces pour détecter et corriger rapidement les menaces tout en répondant aux exigences de conformité. En priorité, fournissez aux analystes des alertes de bonne qualité et des expériences homogènes afin qu’ils puissent se concentrer sur les menaces plutôt que sur l’intégration et les étapes manuelles. 

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Rôle et responsabilités de l’organisation d’opérations de sécurité (SecOP) 

-   Un processus de réponse aux incidents bien défini, aligné avec NIST ou autre cadre réglementaire du secteur 

-   Capture et rétention des journaux pour prendre en charge la détection des menaces, la réponse aux incidents et les besoins de conformité

-   Visibilité centralisée des informations de corrélation sur les menaces, avec SIEM, les fonctionnalités Azure natives et d’autres sources 

-   Plan de communication et de notification avec vos clients, fournisseurs et les parties publiques pertinentes

-   Utilisation de plateformes Azure natives et tierces pour la gestion des incidents, comme la journalisation et la détection des menaces, les investigations et la correction et l’éradication des attaques

-   Processus de gestion des incidents et des activités postérieures aux incidents, comme les leçons apprises et la rétention des preuves

Pour plus d’informations, consultez les références suivantes :

- [Benchmark de sécurité Azure - Journalisation et détection des menaces](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Benchmark de sécurité Azure - Réponse aux incidents](../security/benchmarks/security-controls-v2-incident-response.md)

- [Meilleures pratiques pour la sécurité Azure 4 - Processus. Mise à jour des processus de réponse aux incidents pour le cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guide pour le cadre d’adoption d’Azure, la journalisation et la prise de décision pour les rapports](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Supervision d’Azure Security Center** : Non applicable

**Responsabilité** : Customer

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](../security/benchmarks/overview.md)
- En savoir plus sur les [bases de référence de la sécurité Azure](../security/benchmarks/security-baselines-overview.md)
