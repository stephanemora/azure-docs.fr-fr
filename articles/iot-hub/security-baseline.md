---
title: Base de référence de sécurité Azure pour Azure IoT Hub
description: La base de référence de sécurité Azure IoT Hub fournit des instructions et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans le benchmark de sécurité Azure.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a21e0ae235d5b5c514f3d82b76b4d17394035872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576887"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Base de référence de sécurité Azure pour Azure IoT Hub

Cette base de référence de sécurité applique les conseils [Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) à Microsoft Azure IoT Hub. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure.
Le contenu est regroupé selon les **contrôles de sécurité** définis par le Benchmark de sécurité Azure et les instructions d’aide associées applicables à Azure IoT Hub. Les **contrôles** non applicables à Azure IoT Hub ont été exclus.

 
Pour voir la correspondance complète entre Azure IoT Hub et le Benchmark de sécurité Azure, consultez le [fichier complet de mise en correspondance de la base de référence de sécurité Azure IoT Hub](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Conseils** : IoT Hub étant une plateforme PaaS (platform as a service) multilocataire, différents clients partagent donc le même pool de ressources matérielles de calcul, de réseau et de stockage. Les noms d’hôtes d’IoT Hub sont mappés à un point de terminaison public avec une adresse IP de routage public sur Internet. Différents clients partagent ce point de terminaison public IoT Hub, et tous les appareils IoT qui se trouvent sur des réseaux étendus et sur des réseaux locaux peuvent y accéder. Microsoft a conçu le service pour isoler complètement les données de chaque locataire et fonctionne en permanence pour garantir ce résultat.

Les fonctionnalités IoT Hub, notamment le routage de messages, le chargement de fichiers et l'importation/exportation d’appareils en bloc ont également besoin d’une connectivité IoT Hub vers une ressource Azure appartenant au client via son point de terminaison public. L’ensemble de ces chemins de connectivité constitue le trafic de sortie entre IoT Hub et les ressources du client.

Recommandez la restriction de la connectivité à vos ressources Azure (y compris Azure IoT Hub) via un réseau virtuel dont vous êtes propriétaire et fonctionnant pour réduire l’exposition de la connectivité dans un réseau isolé, et activer la connectivité réseau locale directement sur le réseau principal Azure. Dans la mesure du possible, utilisez Azure Private Link et Azure Private Endpoint pour activer l’accès privé à vos services à partir d’autres réseaux virtuels. 

Une fois l’accès privé établi, désactivez l’accès au réseau public pour IoT Hub pour renforcer la sécurité. Ce contrôle au niveau du réseau est appliqué à une ressource d’hub IoT spécifique, garantissant ainsi l’isolation. Afin que le service demeure actif pour d’autres ressources client à l’aide du chemin public, son point de terminaison public peut toujours être résolu, les adresses IP sont détectables et les ports restent ouverts. Cela n’est pas une source de préoccupation, car Microsoft intègre plusieurs couches de sécurité pour garantir un isolement complet entre les locataires.

Maintenez le nombre de ports physiques ouverts au strict minimum sur vos appareils pour éviter les accès indésirables. De plus, générez des mécanismes pour prévenir ou détecter toute falsification physique de l’appareil.

- [Prise en charge des réseaux virtuels IoT](virtual-network-support.md)

- [Gérer l’accès au réseau public pour le hub IoT](iot-hub-public-network-access.md)

- [Isolation des locataires dans Azure](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices#tenant-level-isolation)

- [Meilleure pratique de mise en réseau IoT](https://docs.microsoft.com/azure/iot-fundamentals/security-recommendations#networking)

- [Vue d’ensemble d’Azure Private Link](../private-link/private-link-overview.md)

- [Groupe de sécurité réseau Azure](../virtual-network/network-security-groups-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des cartes réseau

**Conseils** : Utilisez Azure Security Center et suivez les recommandations de protection du réseau pour sécuriser vos ressources réseau Azure. Activez les journaux de flux de groupe de sécurité réseau et envoyez ceux-ci à un compte Stockage Azure à des fins d’audit. Vous pouvez aussi envoyer les journaux de flux à un espace de travail Log Analytics, puis utiliser Traffic Analytics pour obtenir des insights sur les modèles de trafic au sein de votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau, d’identifier les zones réactives et des menaces de sécurité, de comprendre les modèles de flux de trafic, ainsi que d’épingler de mauvaises configurations du réseau.
 
- [Tutoriel : journaliser le trafic réseau à destination et en provenance d’une machine virtuelle à l’aide du portail Azure](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics](../network-watcher/traffic-analytics.md)
 
- [Protéger vos ressources réseau](../security-center/security-center-network-recommendations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="13-protect-critical-web-applications"></a>1.3 : Protéger les applications web critiques

**Conseils** : Non applicable. Cette recommandation a trait aux applications web s’exécutant sur Azure App Service ou des ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications présentant des adresses IP connues comme étant malveillantes

**Conseils** : Bloquez les adresses IP malveillantes connues avec les règles de filtre IP IoT Hub. Les tentatives malveillantes sont également enregistrées et font l’objet d’alertes via Azure Security Center pour IoT.

Azure DDoS Protection Basic est déjà activé et disponible sans frais supplémentaires dans le cadre de IoT Hub. La supervision permanente du trafic et l’atténuation en temps réel des attaques courantes au niveau du réseau fournissent les mêmes défenses que celles utilisées par les services en ligne de Microsoft.  La distribution et l’atténuation du trafic d’attaque peuvent être réalisées entre différentes régions à l’échelle du réseau global d’Azure.

- [Filtre IP IoT Hub](iot-hub-ip-filtering.md)

- [Communication d’adresses IP suspectes - Azure Security Center pour IoT](/azure/asc-for-iot/concept-security-alerts)

- [Gérer Azure DDoS Protection Basic](../ddos-protection/ddos-protection-overview.md)

- [Protection contre les menaces dans Azure Security Center](../security-center/azure-defender.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Aide** : Non applicable ; cette recommandation est destinée aux offres qui produisent des paquets réseau susceptibles d’être enregistrés et consultés par les clients. IoT Hub ne produit pas de paquets réseau destinés aux clients, et n’est pas conçu pour être déployé directement dans les réseaux virtuels Azure.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions basés sur le réseau (IDS/IPS)

**Aide** : Sélectionnez une offre de la place de marché Azure qui prend en charge les fonctionnalités IDS/IPS avec des fonctionnalités d’inspection de charge utile.  Lorsque l’inspection de la charge utile n’est pas obligatoire, vous pouvez utiliser le renseignement sur les menaces du Pare-feu Azure. Le filtrage basé sur le renseignement sur les menaces du Pare-feu Azure génère des alertes et bloque le trafic en provenance ou à destination d’adresses IP et de domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence.

Déployez la solution de pare-feu de votre choix dans les limites réseau de votre organisation pour détecter et/ou bloquer le trafic malveillant. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Guide pratique pour déployer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Guide pratique pour configurer des alertes avec le Pare-feu Azure](../firewall/threat-intel.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="17-manage-traffic-to-web-applications"></a>1.7 : Gérer le trafic à destination des applications web

**Conseils** : Non applicable. Cette recommandation a trait aux applications web s’exécutant sur Azure App Service ou des ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Aide** : Pour les ressources qui doivent accéder à votre instance Azure IoT Hub, utilisez des étiquettes de service de réseau virtuel afin de définir des contrôles d’accès réseau sur des groupes de sécurité réseau ou le Pare-feu Azure. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de l’étiquette de service (par exemple, AzureIoTHub) dans le champ Source ou Destination approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

- [Utiliser des étiquettes de service pour Azure IoT](iot-hub-understand-ip-address.md)
- [Pour plus d’informations sur l’utilisation des étiquettes de service](../virtual-network/service-tags-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Aide** : Définissez et implémentez des configurations de sécurité standard pour les ressources réseau associées à vos espaces de noms Azure IoT Hub à l’aide d’Azure Policy. Utilisez des alias Azure Policy dans les espaces de noms « Microsoft.Devices » et « Microsoft.Network » afin de créer des stratégies personnalisées d’audit ou d’application de la configuration réseau de vos espaces de noms Azure Machine Learning. 

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Aide** : Utilisez des balises pour les ressources réseau associées à votre déploiement Azure IoT Hub afin de les organiser logiquement dans une taxonomie.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Aide** : Utilisez le journal d’activité Azure pour superviser les configurations des ressources réseau et détecter les changements des ressources réseau associées à Azure IoT Hub. Créez des alertes dans Azure Monitor, qui se déclenchent lors de la modification de ressources réseau critiques.

- [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Guide pratique pour créer des alertes dans Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : Ingérer les journaux via Azure Monitor pour agréger les données de sécurité générées par Azure IoT Hub. Dans Azure Monitor, utilisez des espaces de travail Log Analytics pour interroger et effectuer l’analytique, et utilisez des comptes de stockage pour le stockage à long terme/d’archivage. Vous pouvez également activer et intégrer des données dans Azure Sentinel ou une solution SIEM (Security Information and Event Management) tierce.

- [Configurer les journaux Azure IoT](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Conseils** : activez les paramètres de diagnostic Azure IoT sur les ressources Azure pour accéder aux journaux d’audit, de sécurité et de ressources. Les journaux d’activité, automatiquement disponibles, incluent la source de l’événement, la date, l’utilisateur, l’horodatage, les adresses sources, les adresses de destination et d’autres éléments utiles.

- [Configurer les journaux Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub-reference#resource-logs)

- [Guide pratique pour collecter des journaux et des métriques de plateforme avec Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Présentation de la journalisation et des différents types de journaux dans Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : le [Benchmark de sécurité Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) est l’initiative de stratégie par défaut pour Security Center et constitue la base des [recommandations de Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Les définitions Azure Policy associées à ce contrôle sont activées automatiquement par Security Center. Un plan [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) pour les services associés peut être nécessaire pour les alertes liées à ce contrôle.

**Définitions intégrées à Azure Policy - Microsoft.Devices** :

[!INCLUDE [Resource Policy for Microsoft.Devices 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.devices-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4 : Collecter les journaux de sécurité des systèmes d’exploitation

**Conseils** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Aide** : Dans Azure Monitor, définissez la période de rétention des journaux pour les espaces de travail Log Analytics associés à vos instances Azure IoT Hub conformément aux réglementations de conformité de votre organisation.

- [Guide pratique pour définir les paramètres de conservation des journaux](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Aide** : Analysez et supervisez les journaux afin de détecter tout comportement anormal, et examinez régulièrement les résultats pour votre solution Azure IoT Hub. Utilisez Azure Monitor et un espace de travail Log Analytics pour examiner les journaux et effectuer des requêtes sur leurs données.

Vous pouvez également activer et intégrer les données dans Azure Sentinel ou une solution SIEM tierce. 

- [Surveiller l’intégrité d’Azure IoT](monitor-iot-hub.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Bien démarrer avec les requêtes Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
   
- [Effectuer des requêtes personnalisées dans Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Aide** : Utilisez Azure Security Center pour IoT avec un espace de travail Log Analytics pour superviser les activités anormales détectées dans les journaux de sécurité et les événements et générer des alertes s’y rapportant. Vous pouvez également activer et intégrer les données dans Azure Sentinel. Vous pouvez également définir des alertes opérationnelles avec Azure Monitor susceptibles d’avoir des implications en matière de sécurité, par exemple lorsque le trafic s’interrompt de façon inattendue.

- [Surveiller l’intégrité d’Azure IoT Hub](monitor-iot-hub.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Alertes Azure Security Center pour IoT](/azure/asc-for-iot/concept-security-alerts)

- [Guide pratique pour générer une alerte sur des données de journal Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="28-centralize-anti-malware-logging"></a>2.8 : Centraliser la journalisation anti-programme malveillant

**Aide** : Non applicable ; Azure IoT Hub ne traite pas ni ne produit de journaux liés aux logiciels anti-programmes malveillants.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="29-enable-dns-query-logging"></a>2.9 : Activer la journalisation des requêtes DNS

**Conseils** : Non applicable ; Azure IoT Hub ne traite pas ni ne produit de journaux liés au DNS.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="210-enable-command-line-audit-logging"></a>2.10 : Activer l’enregistrement d’audit en ligne de commande

**Conseils** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Conseils** : Le contrôle d’accès en fonction du rôle Azure (Azure RBAC) vous permet de gérer l’accès à Azure IoT Hub par le biais d’attributions de rôles. Vous pouvez affecter ces rôles à des utilisateurs et regrouper des principaux de service et des identités managées. Les rôles intégrés prédéfinis pour certaines ressources peuvent être inventoriés ou interrogés par le biais d’outils tels qu’Azure CLI, Azure PowerShell ou le portail Azure.

- [Guide pratique pour obtenir un rôle d’annuaire dans Azure Active Directory (Azure AD) avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Guide pratique pour obtenir les membres d’un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Aide** : La gestion de l’accès aux ressources Azure IoT Hub est contrôlée via Azure Active Directory (Azure AD). Azure AD n’intègre pas le concept des mots de passe par défaut.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseils** : Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés.

Vous pouvez également activer l’accès juste-à-temps aux comptes d’administration en utilisant Azure Active Directory (Azure AD) Privileged Identity Management et Azure Resource Manager.

- [En savoir plus sur Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4 : Utiliser l’authentification unique (SSO) avec Azure Active Directory

**Conseils** : Pour les utilisateurs qui accèdent à IoT Hub, utilisez Azure Active Directory (Azure AD) SSO. Suivez les suggestions d’Azure Security Center liées à la gestion des identités et des accès.

- [Présentation de l’authentification SSO avec Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseils** : Activez l’authentification multifacteur Azure Active Directory (Azure AD) pour protéger votre locataire Azure global, en bénéficiant de tous les services. Le service IoT Hub ne prend pas en charge l’authentification multifacteur.

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Conseils** : Utilisez une station de travail à accès privilégié sécurisée pour les tâches administratives qui requièrent des privilèges élevés.

- [Comprendre les stations de travail d’accès privilégié sécurisées](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Guide pratique pour activer l’authentification multifacteur Azure Active Directory (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration

**Conseils** : Utilisez les rapports de sécurité Azure Active Directory (Azure AD) et la supervision pour détecter les activités suspectes ou potentiellement dangereuses qui se produisent dans l’environnement. Utiliser Azure Security Center pour superviser les activités liées aux identités et aux accès.

- [Guide pratique pour identifier les utilisateurs Azure AD pour lesquels une activité à risque a été signalée](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour superviser l’activité liée aux identités et aux accès des utilisateurs dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8 : Gérer les ressources Azure uniquement à partir d’emplacements approuvés

**Conseils** : Pour les utilisateurs qui accèdent à IoT Hub, l’accès conditionnel n’est pas pris en charge. Pour l’atténuer, utilisez les emplacements nommés Azure Active Directory (Azure AD) afin d’autoriser uniquement l’accès à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions pour l’ensemble de votre locataire Azure, en bénéficiant de tous les services, y compris IoT Hub.

- [Procédure de configuration des emplacements nommés Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Pour l’accès utilisateur à IoT Hub, utilisez Azure Active Directory (Azure AD) en tant que système d’authentification et d’autorisation central. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

Pour l’accès aux appareils et aux services, IoT Hub utilise des jetons de sécurité et des jetons de signature d’accès partagé (SAP) afin d’authentifier les appareils et les services et éviter d’envoyer des clés sur le réseau. 

- [Création et configuration d’une instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [Jetons de sécurité IoT Hub](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Azure Active Directory (Azure AD) fournit des journaux pour vous aider à découvrir les comptes obsolètes. Par ailleurs, utilisez les révisions d’accès et des identités Azure AD pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seuls les utilisateurs appropriés continuent de bénéficier d’un accès.

Utilisez Azure AD Privileged Identity Management (PIM) pour générer des journaux et des alertes quand des activités suspectes ou potentiellement dangereuses se produisent dans l’environnement.

- [Présentation des rapports Azure AD](/azure/active-directory/reports-monitoring/)

- [Utilisation des révisions d’accès et des identités Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Déployer Azure AD Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Conseil** : vous avez accès aux sources des journaux Activité de connexion, Audit et Événement à risque Azure Active Directory (Azure AD), qui permettent l’intégration à n’importe quel outil SIEM/de supervision.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure AD et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Vous pouvez configurer les alertes souhaitées dans un espace de travail Log Analytics.

Utilisez les journaux de ressources Azure Monitor pour surveiller les tentatives de connexion non autorisées dans la catégorie Connexions.

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Configurer les journaux de ressources pour IoT Hub](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12 : Alerte en cas d’écart de comportement de connexion à un compte

**Aide** : Utilisez les fonctionnalités d’Azure Active Directory (Azure AD) Identity Protection pour configurer des réponses automatisées aux actions suspectes détectées, liées aux identités d’utilisateur. Vous pouvez également ingérer des données dans Azure Sentinel pour approfondir votre examen.

- [Guide pratique pour afficher les connexions risquées Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Guide pratique pour intégrer Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 : Fournir à Microsoft un accès aux données client pertinentes pendant les scénarios de support

**Aide** : Dans les scénarios de support impliquant que Microsoft accède aux données client, il sera demandé directement au client.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Conseils** : Utilisez des étiquettes pour faciliter le suivi des ressources Azure qui stockent ou traitent des informations sensibles.
 
- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

**Aide** : Implémentez une isolation en utilisant des abonnements et groupes d’administration distincts selon les domaines de sécurité (par exemple, le type d’environnement et le niveau de confidentialité des données). Vous pouvez limiter le niveau d’accès à vos ressources Azure demandées par vos applications et environnements d’entreprise. Vous pouvez contrôler l’accès aux ressources Azure via Azure RBAC.
  
- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

**Conseils** : utilisez une solution tierce de la Place de marché Azure dans les périmètres du réseau, qui surveille et bloque les transferts non autorisés d’informations sensibles tout en alertant les professionnels de la sécurité des informations.

Pour la plateforme sous-jacente gérée par Microsoft, Microsoft traite tout le contenu client en tant que contenu sensible, et assure une protection contre la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

**Conseils** : IoT Hub utilise le protocole TLS (Transport Layer Security) pour sécuriser les connexions provenant d’appareils et de services IoT. Trois versions du protocole TLS sont actuellement prises en charge, à savoir les versions 1.0, 1.1 et 1.2. Il vous est fortement recommandé d’utiliser TLS 1.2 comme version TLS par défaut lors de la connexion à IoT Hub.

Suivez les recommandations d’Azure Security Center relatives au chiffrement au repos et au chiffrement en transit, le cas échéant.

- [Prise en charge du protocole TLS dans IoT Hub](iot-hub-tls-support.md)
- [Présentation du chiffrement en transit avec Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

**Aide** : Les fonctionnalités d’identification, de classification et de protection contre la perte des données ne sont pas encore disponibles pour Azure IoT Hub. Si nécessaire, implémentez une solution tierce à des fins de conformité.

Pour la plateforme Azure sous-jacente gérée par Microsoft, Microsoft traite tout le contenu client comme sensible et déploie d'importants efforts pour vous protéger contre la perte et l’exposition des données client. Pour garantir la sécurité des données client dans Azure, Microsoft a implémenté et tient à jour une suite de contrôles et de fonctionnalités de protection des données robustes.

- [Présentation de la protection des données client dans Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6 : Utiliser Azure RBAC pour contrôler l’accès aux ressources

**Conseils** : Pour l’accès utilisateur de plan de contrôle à IoT Hub, utilisez Azure RBAC afin de contrôler l’accès. Pour l’accès de plan de données à IoT Hub, utilisez des stratégies d’accès partagé pour IoT Hub.

- [Comment configurer Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Contrôler l’accès à IoT Hub](iot-hub-devguide-security.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Aide** : Utilisez Azure Monitor avec le journal d’activité Azure pour créer des alertes en cas de modifications sur des instances de production Azure IoT Hub et autres ressources critiques ou associées.

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="vulnerability-management"></a>Gestion des vulnérabilités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des vulnérabilités](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3 : Déployer une solution de gestion automatisée des correctifs de logiciels tiers

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 : Comparer les analyses de vulnérabilités dos à dos

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 : Utilisez un processus de classement des risques pour classer par ordre de priorité la correction des vulnérabilités découvertes.

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Aide** : Appliquez des balises aux ressources Azure (la plupart des ressources prennent en charge les balises) afin de les organiser logiquement dans une taxonomie.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Aide** : Le cas échéant, utilisez des étiquettes, des groupes d’administration et des abonnements séparés pour organiser et suivre les ressources. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.
  
- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4 : Dresser et tenir un inventaire des ressources Azure approuvées

**Conseils** : Créez un inventaire des ressources Azure et logiciels approuvés pour les ressources de calcul en fonction des besoins de votre organisation.

Chaque IoT Hub dispose d’un registre des identités qui peut être utilisé pour créer des ressources par appareil dans le service. Des identités d’appareil peuvent être ajoutées à une liste verte ou une liste rouge, individuellement ou en groupe, permettant un contrôle total de l’accès à l’appareil.

- [Registre des identités IoT Hub](iot-hub-devguide-identity-registry.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Aide** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans vos abonnements. 

Utilisez Azure Resource Graph pour interroger et découvrir des ressources dans leurs abonnements.  Vérifiez que toutes les ressources Azure présentes dans l’environnement sont approuvées. 

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Procédure pour créer des requêtes avec l’Explorateur Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6 : Analyser les applications logicielles non approuvées dans des ressources de calcul

**Conseils** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 : Supprimer des ressources et applications logicielles Azure non approuvées

**Conseils** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="68-use-only-approved-applications"></a>6.8 : Utiliser des applications approuvées uniquement

**Conseils** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Conseils** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans les abonnements clients selon les définitions de stratégies intégrées suivantes :

* Types de ressources non autorisés
* Types de ressources autorisés

Utilisez également Azure Resource Graph pour interroger/découvrir des ressources dans les abonnements.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Guide pratique pour créer des requêtes avec Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10 : Tenir un inventaire des titres de logiciels approuvés

**Conseils** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Conseils** : Utilisez l’accès conditionnel Azure Active Directory (Azure AD) pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant « Bloquer l’accès » pour l’application « Gestion Microsoft Azure ».

- [Configurer l’accès conditionnel pour bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : aucune

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12 : Limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul

**Conseils** : Non applicable. Cette recommandation a trait aux ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13 : Séparer physiquement ou logiquement des applications à risque élevé

**Conseils** : Non applicable. Cette recommandation a trait aux applications web s’exécutant sur Azure App Service ou des ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Aide** : Définissez et implémentez des configurations de sécurité standard pour votre service Azure IoT Hub avec Azure Policy. Utilisez des alias Azure Policy dans les espaces de noms « Microsoft.Devices » pour créer des stratégies personnalisées d’audit ou d’application de la configuration de vos applications Azure IoT Hub.

Azure Resource Manager est capable d’exporter le modèle au format JSON (JavaScript Object Notation), qui doit être examiné pour vérifier que les configurations répondent aux exigences de sécurité de votre organisation.

Vous pouvez aussi utiliser les recommandations d’Azure Security Center comme ligne de base de configuration sécurisée pour vos ressources Azure.

- [Affichage des alias Azure Policy disponibles](/powershell/module/az.resources/get-azpolicyalias)

- [Tutoriel : Créer et gérer des stratégies pour assurer la conformité](../governance/policy/tutorials/create-and-manage.md)

- [Exportation monoressource ou multiressource vers un modèle sur le portail Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recommandations de sécurité - Guide de référence](../security-center/recommendations-reference.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="72-establish-secure-operating-system-configurations"></a>7.2 : Établir des configurations sécurisées du système d’exploitation

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Aide** : Utilisez les stratégies Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure. En outre, vous pouvez utiliser des modèles Azure Resource Manager pour appliquer la configuration de sécurité des ressources Azure requise par votre organisation.  
 
- [Comprendre les effets d'Azure Policy](../governance/policy/concepts/effects.md)
- [Créer et gérer des stratégies pour assurer la conformité](../governance/policy/tutorials/create-and-manage.md)
- [Présentation des modèles Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4 : Préserver la sécurité des configurations du système d'exploitation

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Si vous utilisez des définitions Azure Policy personnalisées pour vos ressources Azure IoT Hub ou associées, utilisez Azure Repos pour stocker et gérer votre code de manière sécurisée.

- [Stocker du code dans Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentation Azure Repos](/azure/devops/repos)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="76-securely-store-custom-operating-system-images"></a>7.6 Stocker en toute sécurité des images de système d’exploitation personnalisées

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Conseils** : Utilisez des alias Azure Policy dans l’espace de noms « Microsoft.Devices » pour créer des stratégies personnalisées d’alerte, d’audit ou d’application de configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Utiliser des alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8 : Déployer des outils de gestion de la configuration pour les systèmes d'exploitation

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Aide** : Utiliser Azure Security Center pour effectuer des analyses de ligne de base pour vos ressources Azure. En outre, utilisez Azure Policy pour alerter et auditer les configurations des ressources Azure. 
 
- [Guide pratique pour Corriger les recommandations dans Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 : Implémenter la surveillance de la configuration automatique pour les systèmes d’exploitation

**Aide** : Non applicable. Ces conseils concernent les ressources de calcul.

**Responsabilité** : Non applicable

**Supervision Azure Security Center** : aucune

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Conseils** : IoT Hub utilise des jetons de sécurité et des jetons de signature d’accès partagé (SAP) pour authentifier les appareils et les services afin d’éviter d’envoyer des clés sur le réseau. 

Utilisez les identités managées conjointement avec Azure Key Vault afin de simplifier la gestion des secrets pour vos applications cloud.

- [Jetons de sécurité IoT Hub](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Utiliser des identités managées pour IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

- [Créer un coffre de clés](../key-vault/general/quick-create-portal.md)

- [Fournir une authentification Key Vault avec une identité managée](../key-vault/general/assign-access-policy-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Aide** : IoT Hub utilise des jetons de sécurité et des jetons de signature d’accès partagé (SAP) pour authentifier les appareils et les services afin d’éviter d’envoyer des clés sur le réseau.

Utilisez des identités managées pour fournir aux services Azure une identité managée automatiquement dans Azure Active Directory (Azure AD). Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

- [Jetons de sécurité IoT Hub](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-deployment#iot-hub-security-tokens)

- [Configurer des identités managées pour IoT Hub](https://docs.microsoft.com/azure/iot-hub/virtual-network-support#turn-on-managed-identity-for-iot-hub)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault. 
 
- [Configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Défense contre les programmes malveillants](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Aide** : Microsoft Antimalware est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple, Azure IoT Hub), mais il ne s’exécute pas sur du contenu client.

Il vous incombe de pré-analyser tout contenu chargé vers des ressources Azure autres que de calcul. Microsoft ne peut pas accéder aux données client et ne peut donc pas effectuer d’analyses anti-programme malveillant du contenu client en votre nom.

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : garantir des sauvegardes automatisées régulières

**Aide** : Le service Azure IoT Hub fournit des méthodes et une infrastructure pour rendre vos services IoT Hub hautement disponibles et récupérables en cas de sinistres selon des objectifs métier spécifiques. 

- [Haute disponibilité et récupération d’urgence IoT Hub :](iot-hub-ha-dr.md)

- [Cloner IoT Hub](iot-hub-how-to-clone.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés gérées par le client

**Aide** : En ce qui concerne le hub IoT secondaire, Azure IoT Hub recommande qu’il contienne toutes les identités des appareils pouvant se connecter à la solution. La solution doit conserver des sauvegardes géo-répliquées d’identités d’appareils et les télécharger dans le hub IoT secondaire avant de basculer le point de terminaison actif des appareils. La fonctionnalité d’exportation des identités d’appareil d’IoT Hub est utile dans ce contexte.

- [Haute disponibilité et récupération d’urgence IoT Hub :](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [Exportation des identités des appareils IoT Hub](iot-hub-bulk-identity-mgmt.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Aide** : En ce qui concerne le hub IoT secondaire, Azure IoT Hub recommande qu’il contienne toutes les identités des appareils pouvant se connecter à la solution. La solution doit conserver des sauvegardes géo-répliquées d’identités d’appareils et les télécharger dans le hub IoT secondaire avant de basculer le point de terminaison actif des appareils. La fonctionnalité d’exportation des identités d’appareil d’IoT Hub est utile dans ce contexte.

Effectuez régulièrement une restauration des données dans la sauvegarde. Assurez-vous que vous pouvez restaurer les clés gérées par le client sauvegardées.

- [Haute disponibilité et récupération d’urgence IoT Hub :](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr#achieve-cross-region-ha)

- [Exportation des identités des appareils IoT Hub](iot-hub-bulk-identity-mgmt.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés gérées par le client

**Aide** : Activez la suppression réversible et la protection contre la purge dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante. Si le Stockage Azure est utilisé pour stocker les sauvegardes, la suppression réversible vous permet d’enregistrer et de récupérer vos données en cas de suppression d’objets blob ou d’instantanés d’objets blob.

 
- [Présentation d’Azure RBAC](../role-based-access-control/overview.md)

- [Suppression réversible pour le service Stockage Blob Azure](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="incident-response"></a>Réponse aux incidents

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : réponse aux incidents](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

**Aide** : Développez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé. 
  
- [Aide sur la création de votre propre processus de réponse aux incidents de sécurité](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [Utiliser le guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de votre propre plan de réponse aux incidents](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

**Aide** : Azure Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou à l’analytique utilisées pour émettre l’alerte, ainsi qu’à l’intention malveillante estimée de l’activité à l’origine du déclenchement de l’alerte.

  
 En outre, marquez les abonnements à l’aide d’étiquettes et créez un système de nommage pour identifier et classer les ressources Azure, en particulier celles qui traitent des données sensibles. Il vous incombe de hiérarchiser le traitement des alertes en fonction de la criticité des ressources et de l’environnement Azure où l’incident s’est produit.
  
- [Alertes de sécurité dans Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [Utiliser des étiquettes pour organiser vos ressources Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Conseils** : Effectuez des exercices pour tester les capacités de réponse aux incidents de vos systèmes à intervalles réguliers, afin de protéger vos ressources Azure. Identifiez les points faibles et les lacunes, puis révisez votre plan de réponse en fonction des besoins.
  
- [Publication du NIST – Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide de test, d’entraînement et d’utilisation des programmes destinés aux plans et fonctionnalités informatiques)](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 : Fournir des informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

**Instructions** : Microsoft utilisera les informations de contact pour le signalement d’incidents de sécurité pour vous contacter si le Microsoft Security Response Center (MSRC) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé. Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus.
  
- [Guide pratique pour définir le contact de sécurité d’Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Conseils** : Exportez vos alertes et recommandations Azure Security Center en utilisant la fonctionnalité d’exportation continue pour identifier les risques pesant sur les ressources Azure. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser en continu les alertes vers Azure Sentinel.
  
- [Configurer l’exportation continue](../security-center/continuous-export.md)
 
- [Transmettre en continu des alertes à Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Conseils** : Utilisez la fonctionnalité d’automatisation de workflow d’Azure Security Center pour déclencher automatiquement des réponses aux alertes et aux recommandations de sécurité afin de protéger vos ressources Azure.
  
- [Configurer l’automatisation du workflow dans Security Center](../security-center/workflow-automation.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="penetration-tests-and-red-team-exercises"></a>Tests d’intrusion et exercices Red Team

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : tests d’intrusion et exercices Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1 : Procéder régulièrement à des tests d’intrusion des ressources Azure et veiller à corriger tous les problèmes de sécurité critiques détectés

**Aide** : Suivez les règles d’engagement de pénétration du cloud Microsoft pour vous assurer que vos tests d’intrusion sont conformes aux stratégies de Microsoft. Utilisez la stratégie et l’exécution de Red Teaming de Microsoft ainsi que les tests d’intrusion de site actif sur l’infrastructure cloud, les services et les applications gérés par Microsoft.

- [Règles d’engagement des tests d’intrusion](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- En savoir plus sur les [bases de référence de la sécurité Azure](/azure/security/benchmarks/security-baselines-overview)
