---
title: Base de sécurité Azure pour Azure DevTest Labs
description: La base de référence de sécurité Azure DevTest Labs fournit des instructions et des ressources pour la mise en œuvre des recommandations de sécurité spécifiées dans le benchmark de sécurité Azure.
author: msmbaldwin
ms.service: devtest-lab
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 27a0d5b809480b2ce4aff36c5acd43c149ed5bb3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562832"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Base de sécurité Azure pour Azure DevTest Labs

Cette base de référence de sécurité applique les instructions du [Benchmark de sécurité Azure version 1.0](../security/benchmarks/overview-v1.md) à Azure DevTest Labs. Le benchmark de sécurité Azure fournit des recommandations sur la façon dont vous pouvez sécuriser vos solutions cloud sur Azure. Le contenu est regroupé par les **contrôles de sécurité** définis par le Benchmark de sécurité Azure et les conseils associés applicables à Azure DevTest Labs. **Les contrôles** non applicables à Azure DevTest Labs ont été exclus.

Pour voir comment Azure DevTest Labs est entièrement mappé au Benchmark de sécurité Azure, consultez le [fichier de mappage complet de la base de référence de sécurité Azure DevTest Labs](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sécurité réseau

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : sécurité réseau](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1 : Protéger les ressources Azure au sein des réseaux virtuels

**Conseils** : Lorsque vous déployez des ressources Azure DevTest Labs, vous devez créer ou utiliser un réseau virtuel existant. Assurez-vous que le réseau virtuel choisi dispose d’un groupe de sécurité réseau appliqué à ses sous-réseaux et des contrôles d’accès réseau configurés spécifiques aux ports et sources de confiance de votre application. Lorsque des ressources de laboratoire sont configurées avec un réseau virtuel, elles ne sont pas adressables publiquement et sont accessibles uniquement depuis le réseau virtuel. Vous pouvez également choisir de créer un laboratoire isolé du réseau, dans lequel les environnements et les ressources, par exemple un compte de stockage et des coffres de clés, seront également complètement isolés et accessibles uniquement par le biais de points de terminaison spécifiés. 

En fonction des besoins de votre organisation, utilisez le service Pare-feu Azure pour créer, appliquer et journaliser de manière centralisée les stratégies de connectivité réseau et d’application entre les abonnements et les réseaux virtuels.

- [Guide pratique pour configurer un réseau virtuel pour Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Guide pratique pour créer une instance DevTest Labs isolée du réseau](network-isolation.md)

- [Comment créer un groupe de sécurité réseau avec des règles de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

- [Guide pratique pour déployer et configurer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2 : Superviser et journaliser la configuration et le trafic des réseaux virtuels, des sous-réseaux et des cartes réseau

**Conseils** : Déployez un groupe de sécurité réseau sur le réseau sur lequel vos ressources Azure DevTest Labs sont déployées. Activez les journaux de flux du groupe de sécurité réseau sur vos groupes de sécurité réseau pour l’audit du trafic.

Vous pouvez aussi envoyer ces journaux dans un espace de travail Log Analytics et utiliser Traffic Analytics pour obtenir des insights sur le flux de trafic dans votre cloud Azure. Parmi les avantages de Traffic Analytics figure la possibilité de visualiser l’activité réseau et d’identifier les zones réactives, d’identifier les menaces de sécurité, de comprendre les modèles de flux de trafic et de repérer les mauvaises configurations du réseau.

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Présentation de la sécurité réseau assurée par Azure Security Center](../security-center/security-center-network-recommendations.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="13-protect-critical-web-applications"></a>1.3 : Protéger les applications web critiques

**Conseils** : Déployez un pare-feu d'applications web (WAF) Azure devant les applications web critiques déployées à l’aide de modèles Resource Manager pour bénéficier d’un contrôle supplémentaire du trafic entrant. Activez le paramètre de diagnostic du WAF et ingérez les journaux dans un compte de stockage, un hub d'événements ou un espace de travail Log Analytics.

- [Guide pratique pour déployer Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 : Refuser les communications présentant des adresses IP connues comme étant malveillantes

**Conseils** : Le déploiement d’un réseau virtuel Azure (VNet) pour un laboratoire améliore la sécurité et l’isolation de votre laboratoire. Les sous-réseaux, les stratégies de contrôle d’accès et d’autres fonctionnalités facilitent également la restriction de l’accès. Lors du déploiement dans un réseau virtuel, Azure DevTest Labs n’est pas adressable publiquement et est accessible uniquement à partir de machines virtuelles et d’applications au sein du réseau virtuel.

Activez la protection DDoS Standard sur vos réseaux virtuels Azure pour vous protéger des attaques DDoS. Utilisez la fonctionnalité de renseignement sur les menaces intégrée à Azure Security Center pour refuser les communications présentant des adresses IP connues comme étant malveillantes.

Déployez le Pare-feu Azure à chaque extrémité du réseau de l'organisation en activant la fonctionnalité de renseignement sur les menaces et en la configurant sur « Alerter et refuser » pour vous protéger de tout trafic réseau malveillant. Utilisez la fonctionnalité d'accès réseau juste-à-temps d'Azure Security Center pour configurer les groupes de sécurité réseau (NSG) et limiter l'exposition des points de terminaison aux adresses IP approuvées pendant une période limitée. Utilisez la fonctionnalité de renforcement du réseau adaptatif d'Azure Security Center pour recommander des configurations NSG qui limitent les ports et les adresses IP sources en fonction du trafic réel et du renseignement sur les menaces.

- [Guide pratique pour configurer un réseau virtuel pour Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Guide pratique pour configurer la protection DDoS](../ddos-protection/manage-ddos-protection.md)

- [Guide pratique pour déployer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Présentation de la fonctionnalité Threat Intelligence intégrée à Azure Security Center](../security-center/azure-defender.md)

- [Présentation de la fonctionnalité de renforcement du réseau adaptatif d’Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Présentation de la fonctionnalité de contrôle d’accès réseau juste-à-temps d’Azure Security Center](../security-center/security-center-just-in-time.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="15-record-network-packets"></a>1.5 : Enregistrer les paquets réseau

**Conseils** : Activez la capture de paquets Network Watcher sur le réseau virtuel de votre laboratoire pour examiner les activités anormales. 

- [Guide pratique pour activer les journaux de flux NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Guide pratique pour activer et utiliser Traffic Analytics](../network-watcher/traffic-analytics.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 : Déployer des systèmes de détection et de prévention des intrusions (IDS/IPS) basés sur le réseau

**Conseils** : Utilisez un pare-feu Azure déployé sur votre réseau virtuel avec le renseignement sur les menaces activé. Le filtrage basé sur le renseignement sur les menaces du Pare-feu Azure peut générer des alertes et refuser le trafic depuis ou vers des adresses IP et des domaines malveillants connus. Ces adresses IP et domaines proviennent du flux Microsoft Threat Intelligence. Si votre organisation nécessite des fonctionnalités supplémentaires par rapport à celles fournies par le Pare-feu Azure, sélectionnez une offre appropriée sur la Place de marché Azure, qui prend en charge les fonctionnalités IDS/IPS avec des fonctionnalités d’inspection de la charge utile.

Déployez la solution de pare-feu de votre choix dans les limites réseau de votre organisation pour détecter et/ou refuser le trafic malveillant.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)
 

- [Guide pratique pour déployer le Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md)
 

- [Guide pratique pour configurer des alertes avec le Pare-feu Azure](../firewall/threat-intel.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="17-manage-traffic-to-web-applications"></a>1.7 : Gérer le trafic à destination des applications web

**Conseils** : Lorsque vous utilisez des environnements DevTest Labs Azure Resource Manager incluant des applications Web, déployez une passerelle Azure Application Gateway avec le protocole HTTPS/TLS activé pour les certificats approuvés.

- [Guide pratique pour déployer Application Gateway](../application-gateway/quick-create-portal.md)

- [Guide pratique pour configurer Application Gateway de façon à utiliser le protocole HTTPS](../application-gateway/create-ssl-portal.md)

- [Présentation de l’équilibrage de charge de niveau 7 avec les passerelles d’applications web Azure](../application-gateway/overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 : Réduire la complexité et les frais administratifs liés aux règles de sécurité réseau

**Conseils** : Utilisez les étiquettes de service Réseau virtuel Azure pour définir des contrôles d’accès réseau sur les groupes de sécurité réseau ou le Pare-feu Azure configurés pour vos ressources DevTest Labs. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. En spécifiant le nom de la balise de service (par exemple, ApiManagement) dans le champ Source ou Destination approprié d'une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

Vous pouvez également utiliser des groupes de sécurité d’application pour simplifier les configurations de sécurité complexes. Les groupes de sécurité d’application permettent de configurer la sécurité réseau comme un prolongement naturel de la structure de l’application, et donc de regrouper les machines virtuelles et définir des stratégies de sécurité réseau basés sur ces groupes.

- [Présentation et utilisation des balises de service](../virtual-network/service-tags-overview.md)

- [Présentation et utilisation des groupes de sécurité d’application](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 : Gérer les configurations de sécurité standard pour les périphériques réseau

**Aide** : Définissez et implémentez des configurations de sécurité standard pour les ressources réseau à l'aide d'Azure Policy.

Vous pouvez également utiliser Azure Blueprints pour simplifier les déploiements Azure à grande échelle en regroupant les principaux artefacts d’environnement (par exemple, les modèles Azure Resource Manager, les contrôles RBAC et les stratégies) au sein d’une seule définition de blueprint. Vous pouvez appliquer le blueprint aux nouveaux abonnements et ajuster le contrôle et la gestion grâce au contrôle de version.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemples Azure Policy pour le réseau](../governance/policy/samples/built-in-policies.md#network)

- [Guide pratique pour créer un blueprint Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="110-document-traffic-configuration-rules"></a>1.10 : Règles de configuration du trafic de documents

**Conseils** : Utilisez des balises pour les ressources réseau associées à votre déploiement Azure DevTest Labs afin de les organiser logiquement dans une taxonomie. Concernant les règles NSG individuelles, utilisez le champ « Description » afin de spécifier le besoin métier et/ou la durée (etc.) pour toutes les règles qui autorisent le trafic vers/depuis un réseau.

Utilisez l’une des définitions de stratégie Azure intégrée en lien avec l’étiquetage comme « Exiger une étiquette et sa valeur » pour vous assurer que toutes les ressources créées sont étiquetées et être informé de l’existence de ressources non étiquetées.

Vous pouvez utiliser Azure PowerShell ou Azure CLI pour rechercher des ressources ou effectuer des actions sur des ressources en fonction de leurs étiquettes.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

- [Guide pratique pour créer un réseau virtuel](../virtual-network/quick-create-portal.md)

- [Guide pratique pour créer un groupe NSG avec une configuration de sécurité](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 : Utiliser des outils automatisés pour superviser les configurations des ressources réseau et détecter les modifications

**Conseils** : Utilisez le journal d’activité Azure pour effectuer le monitoring des configurations de ressources et détecter les changements apportés à vos ressources Azure. Créez des alertes dans Azure Monitor, qui se déclenchent en cas de modification des ressources critiques.

- [Guide pratique pour consulter et récupérer les événements du journal d’activité Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Guide pratique pour créer des alertes dans Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="logging-and-monitoring"></a>Journalisation et supervision

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : journalisation et supervision](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1 : Utiliser des sources de synchronisation date/heure approuvées

**Conseils** : Microsoft gère les sources de temps pour les ressources Azure. Toutefois, vous pouvez gérer les paramètres de synchronisation temporelle de vos ressources de calcul.

- [Synchronisation de l’heure pour les machines virtuelles Windows dans Azure](../virtual-machines/windows/time-sync.md)

**Responsabilité** : Partagé

**Supervision d’Azure Security Center** : Aucune

### <a name="22-configure-central-security-log-management"></a>2.2 : Configurer la gestion des journaux de sécurité centrale

**Aide** : activez les paramètres de diagnostic des journaux d’activité Azure et envoyez les journaux vers un espace de travail Log Analytics, Event Hub ou un compte de stockage Azure pour archivage. Les journaux d’activité fournissent des informations concernant les opérations effectuées sur vos instances Azure DevTest Labs au niveau du plan de gestion. À l’aide des données du journal d’activité Azure, vous pouvez déterminer le « quoi, qui et quand » pour toutes les opérations d’écriture (PUT, POST, DELETE) effectuées au niveau du plan de gestion pour vos instances DevTest Labs.

- [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](../azure-monitor/essentials/diagnostic-settings.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 : Activer la journalisation d’audit pour les ressources Azure

**Aide** : activez les paramètres de diagnostic des journaux d’activité Azure et envoyez les journaux vers un espace de travail Log Analytics, Event Hub ou un compte de stockage Azure pour archivage. Les journaux d’activité fournissent des informations concernant les opérations effectuées sur vos instances Azure DevTest Labs au niveau du plan de gestion. À l’aide des données du journal d’activité Azure, vous pouvez déterminer le « quoi, qui et quand » pour toutes les opérations d’écriture (PUT, POST, DELETE) effectuées au niveau du plan de gestion pour vos instances DevTest Labs.

- [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](../azure-monitor/essentials/diagnostic-settings.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4 : Collecter les journaux de sécurité des systèmes d’exploitation

**Conseils** : Les machines virtuelles Azure DevTest Labs sont créées et détenues par le client. Il incombe donc à l’organisation de les surveiller. Vous pouvez utiliser Azure Security Center pour surveiller le système d’exploitation. Les données collectées par Security Center auprès du système d’exploitation incluent le type et la version du système d’exploitation, le système d’exploitation (journaux des événements Windows), les processus en cours d’exécution, le nom de l’ordinateur, les adresses IP et l’utilisateur connecté. L’agent Log Analytics collecte également les fichiers de vidage sur incident.

Pour plus d’informations, consultez les articles suivants :

- [Guide pratique pour collecter les journaux des hôtes internes des machines virtuelles Azure avec Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Présentation de la collecte de données Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="25-configure-security-log-storage-retention"></a>2.5 : Configurer la conservation du stockage des journaux de sécurité

**Conseils** : Dans Azure Monitor, définissez la période de rétention des journaux pour les espaces de travail Log Analytics associés à vos instances Azure DevTest Labs conformément aux réglementations de conformité de votre organisation.

- [Pour plus d’informations, consultez l’article suivant](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="26-monitor-and-review-logs"></a>2.6 : Superviser et examiner les journaux

**Aide** : Activez les paramètres de diagnostic des journaux d’activité Azure et envoyez les journaux à un espace de travail Log Analytics. Exécutez des requêtes dans Log Analytics pour rechercher des termes, identifier des tendances, analyser des modèles et fournir de nombreuses autres informations basées sur les données du journal d’activité qui ont pu être collectées pour Azure DevTest Labs.

Pour plus d’informations, consultez les articles suivants :

- [Guide pratique pour activer les paramètres de diagnostic du journal d’activité Azure](../azure-monitor/essentials/diagnostic-settings.md)

- [Collecte et analyse des journaux d’activité Azure dans l’espace de travail Log Analytics dans Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7 : Activer les alertes d’activité anormale

**Conseils** : Utilisez l’espace de travail Azure Log Analytics pour surveiller les activités anormales dans les événements et les journaux de sécurité liés à votre Azure DevTest Labs, et alerter à leur sujet.

- [Guide pratique pour générer une alerte sur des données de journal Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="identity-and-access-control"></a>Contrôle des accès et des identités

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : contrôle des accès et des identités](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 : Tenir un inventaire des comptes d’administration

**Aide** : Azure Active Directory (Azure AD) comporte des rôles intégrés qui doivent être explicitement attribués et qui peuvent être interrogés. Utilisez le module Azure AD PowerShell pour exécuter des requêtes ad hoc afin de découvrir les comptes membres de groupes d’administration.

- [Guide pratique pour obtenir un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Guide pratique pour obtenir les membres d’un rôle d’annuaire dans Azure AD avec PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Rôles Azure DevTest Labs](devtest-lab-add-devtest-user.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="32-change-default-passwords-where-applicable"></a>3.2 : Modifier les mots de passe par défaut lorsque cela est possible

**Conseils** : Azure Active Directory (Azure AD) n’intègre pas le concept des mots de passe par défaut. Selon le service, d’autres ressources Azure qui exigent un mot de passe forcent la création d’un mot de passe conforme à des exigences de complexité et d’une longueur minimale. Vous êtes responsable des applications tierces et des services de la Place de marché susceptibles d’utiliser des mots de passe par défaut.

DevTest Labs n’intègre pas le concept des mots de passe par défaut.

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 : Utiliser des comptes d’administration dédiés

**Conseils** : Créez des procédures standard autour de l’utilisation de comptes d’administration dédiés. Utilisez la gestion des identités et des accès dans Azure Security Center pour superviser le nombre de comptes d’administration.

En outre, pour vous aider à suivre les comptes d’administration dédiés, vous pouvez utiliser des recommandations d’Azure Security Center ou des stratégies Azure intégrées, telles que les suivantes :

- Plusieurs propriétaires doivent être affectés à votre abonnement

- Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement

- [Utilisation d’Azure Security Center pour superviser l’identité et l’accès (préversion)](../security-center/security-center-identity-access.md)

- [Utilisation d’Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Rôles Azure DevTest Labs](devtest-lab-add-devtest-user.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4 : Utiliser l’authentification unique (SSO) avec Azure Active Directory

**Conseils**  : DevTest Labs utilise le service Azure Active Directory (Azure AD) pour la gestion des identités. Tenez compte de ces deux aspects clés lorsque vous autorisez les utilisateurs à accéder à un environnement basé sur DevTest Labs :
- Gestion des ressources : l’accès au portail Azure permet d’effectuer les opérations de gestion des ressources (créer des machines virtuelles, créer des environnements, démarrer, arrêter, redémarrer, supprimer, appliquer des artefacts, etc.). La gestion des ressources s’effectue dans Azure en utilisant le contrôle d’accès en fonction du rôle Azure (Azure RBAC). Vous affectez des rôles aux utilisateurs et définissez les autorisations au niveau des ressources et de l’accès.
- Machines virtuelles (au niveau du réseau) : dans la configuration par défaut, les machines virtuelles utilisent un compte d’administrateur local. S’il existe un domaine disponible (Azure Active Directory Domain Services (Azure AD DS), un domaine local ou un domaine cloud), il est possible de lui adjoindre des machines. Les utilisateurs peuvent ensuite utiliser leurs identités basées sur le domaine en utilisant l’artefact de jonction de domaine pour se connecter aux machines.

- [Architecture de référence pour DevTest Labs](./devtest-lab-reference-architecture.md#architecture)

- [Présentation de l’authentification SSO avec Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 : Utiliser l’authentification multifacteur pour tous les accès basés sur Azure Active Directory

**Conseil** : activez l’authentification multifacteur Azure Active Directory (Azure AD), puis suivez les recommandations relatives à la gestion des identités et des accès Azure Security Center.

- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Guide pratique pour superviser les identités et les accès dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 : Utiliser des ordinateurs dédiés (stations de travail avec accès privilégié) pour toutes les tâches administratives

**Conseils** : Utilisez des stations de travail disposant d’un accès privilégié avec l’authentification multifacteur configurée pour vous connecter aux ressources Azure et les configurer. 

- [En savoir plus sur les stations de travail à accès privilégié](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 
- [Guide pratique pour activer l’authentification multifacteur dans Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7 : Journaliser et générer des alertes en cas d’activités suspectes sur des comptes d’administration

**Aide** : Utilisez les rapports de sécurité Azure Active Directory (Azure AD) pour générer des journaux et des alertes quand une activité suspecte ou potentiellement dangereuse se produit dans l’environnement. Utiliser Azure Security Center pour superviser les activités liées aux identités et aux accès.

- [Guide pratique pour identifier les utilisateurs Azure AD pour lesquels une activité à risque a été signalée](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour superviser l’activité liée aux identités et aux accès des utilisateurs dans Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8 : Gérer les ressources Azure uniquement à partir d’emplacements approuvés

**Conseils** : Utilisez des emplacements nommés à accès conditionnel pour autoriser l’accès uniquement à partir de regroupements logiques spécifiques de plages d’adresses IP ou de pays/régions.

- [Guide pratique pour configurer des emplacements nommés dans Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="39-use-azure-active-directory"></a>3.9 : Utiliser Azure Active Directory

**Aide** : Utiliser Azure Active Directory (Azure AD) comme système d’authentification et d’autorisation central. Azure AD protège les données en utilisant un chiffrement fort pour les données au repos et en transit. De plus, AAD sale, hache et stocke de manière sécurisée les informations d’identification utilisateur.

- [Création et configuration d’une instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 : Examiner et rapprocher régulièrement l’accès utilisateur

**Aide** : Azure Active Directory (Azure AD) fournit des journaux pour vous aider à découvrir les comptes obsolètes. Par ailleurs, utilisez les révisions d’accès des identités Azure pour gérer efficacement les appartenances aux groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seules les utilisateurs appropriés continuent de bénéficier d’un accès.

- [Présentation des rapports Azure AD](../active-directory/reports-monitoring/overview-reports.md)

- [Comment utiliser les révisions d’accès des identités Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11 : Superviser les tentatives d’accès à des informations d’identification désactivées

**Conseils** : Vous pouvez accéder aux sources de journal de l’activité de connexion, de l’audit et des événements à risque d’Azure Active Directory (Azure AD), ce qui vous permet d’intégrer n’importe quel outil SIEM (Security Information and Event Management) ou de supervision.

Vous pouvez simplifier ce processus en créant des paramètres de diagnostic pour les comptes d’utilisateur Azure AD et en envoyant les journaux d’audit et les journaux de connexion à un espace de travail Log Analytics. Vous pouvez configurer des alertes dans un espace de travail Log Analytics.

- [Guide pratique pour intégrer des journaux d’activité Azure dans Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12 : Alerte en cas d’écart de comportement de connexion à un compte

**Aide** : Utilisez les détections de risque et la fonctionnalité Risk and Identity Protection d’Azure Active Directory (Azure AD) pour configurer des réponses automatiques aux actions suspectes détectées liées aux identités d’utilisateur.

- [Guide pratique pour afficher les connexions risquées Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Guide pratique pour configurer et activer des stratégies de risque Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="data-protection"></a>Protection des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : protection des données](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

**Conseils** : Utilisez des étiquettes pour faciliter le suivi des ressources Azure qui stockent ou traitent des informations sensibles.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6 : Utiliser Azure RBAC pour contrôler l’accès aux ressources

**Conseils** : Utilisez le contrôle d’accès en fonction du rôle Azure (Azure RBAC) pour contrôler l’accès aux labos dans Azure DevTest Labs.

- [Comment configurer Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Comprendre les rôles dans DevTest Labs](devtest-lab-add-devtest-user.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

**Conseils** : Utilisez Azure Monitor avec le journal d’activité Azure pour créer des alertes quand des modifications sont apportées aux instances DevTest Labs ainsi qu’à d’autres ressources critiques ou connexes.

- [Guide pratique pour créer des alertes sur les événements du journal d’activité Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Guide pratique pour créer des alertes sur les événements du journal d’activité DevTest](create-alerts.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="inventory-and-asset-management"></a>Gestion des stocks et des ressources

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Gestion des stocks et des ressources](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1 : Utiliser la solution de détection automatisée des ressources

**Conseils** : Utilisez Azure Resource Graph pour interroger et découvrir toutes les ressources (y compris les ressources DevTest Labs) au sein de vos abonnements. Vérifiez que vous disposez des autorisations (en lecture) appropriées dans votre locataire et pouvez répertorier tous les abonnements Azure ainsi que les ressources qu’ils contiennent.

- [Guide pratique pour créer des requêtes avec Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Guide pratique pour afficher ses abonnements Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Présentation d’Azure RBAC](../role-based-access-control/overview.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

**Aide** : Appliquez des balises aux ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories.

- [Guide pratique pour créer et utiliser des étiquettes](../azure-resource-manager/management/tag-resources.md)

- [Configurer des balises pour DevTest Labs](devtest-lab-add-tag.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

**Conseils** : Utilisez un balisage, des groupes d’administration et des abonnements distincts, ainsi que des laboratoires distincts le cas échéant, pour organiser et suivre les laboratoires et les ressources associées. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement rapidement.

- [Guide pratique pour créer des abonnements Azure supplémentaires](../cost-management-billing/manage/create-subscription.md)

- [Guide pratique pour créer des groupes d’administration](../governance/management-groups/create-management-group-portal.md)

- [Guide pratique pour créer un laboratoire à l’aide de DevTest Labs](devtest-lab-create-lab.md)

- [Guide pratique pour créer et utiliser des étiquettes](devtest-lab-add-tag.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4 : Dresser et tenir un inventaire des ressources Azure approuvées

**Conseils** :Créez un inventaire des ressources Azure et logiciels approuvés pour les ressources de calcul en fonction des besoins de votre organisation. En tant qu’administrateur d’abonnements, vous pouvez également utiliser les contrôles d’application adaptatifs, une fonctionnalité d’Azure Security Center, pour définir un ensemble d’applications autorisées à s’exécuter sur des groupes configurés de machines de labo. Cette fonctionnalité est disponible pour les machines et les serveurs Azure et non-Azure Windows (toutes versions, classique ou Azure Resource Manager), mais aussi Linux.

- [Guide pratique pour activer les contrôles d’application adaptatifs](../security-center/security-center-adaptive-application.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

**Conseils** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans les abonnements clients en fonction des définitions de stratégies intégrées suivantes :

- Types de ressources non autorisés

- Types de ressources autorisés

Par ailleurs, utilisez Azure Resource Graph pour interroger/découvrir des ressources dans les abonnements. Cela peut être utile dans les environnements de haute sécurité, tels que ceux dotés de comptes de stockage.

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour créer des requêtes avec Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6 : Analyser les applications logicielles non approuvées dans des ressources de calcul

**Aide** : Azure Automation permet de contrôler totalement le déploiement, les opérations et la désaffectation des charges de travail et des ressources. En tant qu’administrateur d’abonnements, vous pouvez tirer parti de l’inventaire des machines virtuelles Azure pour automatiser la collecte d’informations sur tous les logiciels installés sur les machines virtuelles DevTest Labs dans votre abonnement. Les propriétés de nom, de version, d’éditeur et d’heure d’actualisation du logiciel sont disponibles sur le portail Azure. Pour avoir accès à la date d’installation et à d’autres informations, le client a demandé d’activer les diagnostics au niveau de l’invité et de rassembler les journaux des événements Windows dans un espace de travail Log Analytics.

Outre l’utilisation de Change Tracking pour la surveillance d’applications logicielles, les contrôles d’application adaptatifs d’Azure Security Center utilisent l’apprentissage automatique pour analyser les applications en cours d’exécution sur vos machines et créer une liste verte à partir de cette intelligence. Cette capacité simplifie considérablement le processus de configuration et de gestion des stratégies de mise en liste verte des applications, ce qui vous permet d’éviter l’utilisation de logiciels indésirables dans votre environnement. Vous pouvez configurer le mode audit ou le mode application. Le mode audit vérifie uniquement l’activité sur les machines virtuelles protégées. Le mode application applique les règles, puis s’assure que les applications n’ayant pas d’autorisation sont bloquées. 

- [Présentation d’Azure Automation](../automation/automation-intro.md)

- [Guide pratique pour activer l’inventaire des machines virtuelles Azure](../automation/automation-tutorial-installed-software.md)

- [Présentation des contrôles d’application adaptatifs](../security-center/security-center-adaptive-application.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 : Supprimer des ressources et applications logicielles Azure non approuvées

**Aide** : Azure Automation permet de contrôler totalement le déploiement, les opérations et la désaffectation des charges de travail et des ressources. En tant qu’administrateur d’abonnements, vous pouvez utiliser Change Tracking pour identifier tous les logiciels installés sur les machines virtuelles hébergées dans DevTest Labs. Vous pouvez implémenter votre propre processus ou utiliser la configuration de l’état Azure Automation pour supprimer les logiciels non autorisés.

- [Présentation d’Azure Automation](../automation/automation-intro.md)

- [Suivre les changements dans votre environnement avec la solution Change Tracking](../automation/change-tracking/overview.md)

- [Vue d’ensemble d’Azure Automation State Configuration](../automation/automation-dsc-overview.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="68-use-only-approved-applications"></a>6.8 : Utiliser des applications approuvées uniquement

**Conseils** : En tant qu’administrateur d’abonnements, vous avez également la possibilité d’exploiter les contrôles d’application adaptatifs Azure Security Center pour faire en sorte que seuls les logiciels autorisés s’exécutent et que l’exécution de tous les logiciels non autorisés soit bloquée sur les machines virtuelles Azure hébergées dans DevTest Labs.

- [Guide pratique pour utiliser les contrôles d’application adaptatifs Azure Security Center](../security-center/security-center-adaptive-application.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

**Conseils** : Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans les abonnements clients en fonction des définitions de stratégies intégrées suivantes :
- Types de ressources non autorisés
- Types de ressources autorisés

Documents de référence :

- [Guide pratique pour configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Guide pratique pour refuser un type de ressource spécifique avec Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10 : Tenir un inventaire des titres de logiciels approuvés

**Conseils** : Le contrôle d’application adaptatif est une solution de bout en bout intelligente et automatisée d’Azure Security Center qui vous permet de contrôler les applications qui peuvent s’exécuter sur vos machines Azure et non-Azure (Windows et Linux) hébergées dans DevTest Labs. Notez que vous devez être administrateur des abonnements pour pouvoir configurer ce paramètre pour les ressources de calcul sous-jacentes hébergées dans DevTest Labs. Implémentez une solution tierce si ce paramètre ne répond pas aux exigences de votre organisation.

- [Guide pratique pour utiliser les contrôles d’application adaptatifs Azure Security Center](../security-center/security-center-adaptive-application.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager

**Conseils** : Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant **Bloquer l’accès** pour l’application **Gestion Microsoft Azure**.

- [Configuration de l’accès conditionnel pour bloquer l’accès à Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12 : Limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul

**Conseils** : Selon le type de script, vous pouvez utiliser des configurations de système d’exploitation spécifiques ou des ressources tierces pour limiter la capacité des utilisateurs à exécuter des scripts dans les machines virtuelles hébergées dans DevTest Labs. Vous avez également la possibilité d’utiliser les contrôles d’application adaptatifs Azure Security Center pour faire en sorte que seuls les logiciels autorisés s’exécutent et que l’exécution de tous les logiciels non autorisés soit bloquée sur les machines virtuelles Azure sous-jacentes.

- [Guide pratique pour contrôler l’exécution des scripts PowerShell dans des environnements Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?preserve-view=true&view=powershell-7)

- [Guide pratique pour utiliser les contrôles d’application adaptatifs Azure Security Center](../security-center/security-center-adaptive-application.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13 : Séparer physiquement ou logiquement des applications à risque élevé

**Conseils** : Les applications à risque élevé déployées dans votre environnement Azure peuvent être isolées à l’aide d’un réseau virtuel, d’un sous-réseau, d’abonnements, de groupes d’administration, etc., et suffisamment sécurisées avec un pare-feu Azure, un pare-feu d’applications Web (WAF) ou un groupe de sécurité réseau (NSG).

- [Configurer un réseau virtuel pour DevTest Labs](devtest-lab-configure-vnet.md)

- [Présentation du Pare-feu Azure](../web-application-firewall/overview.md)

- [Présentation du pare-feu d’applications web](../virtual-network/network-security-groups-overview.md)

- [Vue d’ensemble de la sécurité réseau](security-baseline.md)

- [Organiser vos ressources avec des groupes d’administration Azure](../governance/management-groups/overview.md)

- [Guide de décision concernant les abonnements](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

## <a name="secure-configuration"></a>Configuration sécurisée

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Configuration sécurisée](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

**Conseils** : Utilisez des alias Azure Policy pour créer des stratégies personnalisées afin d’auditer ou d’appliquer la configuration de vos ressources Azure créées en tant que partie de DevTest Labs. Vous pouvez aussi utiliser des définitions Azure Policy intégrées.

Par ailleurs, Azure Resource Manager a la possibilité d’exporter le modèle au format JSON (JavaScript Object Notation), qui doit être examiné pour vérifier que les configurations répondent/dépassent les exigences de sécurité de votre organisation.

Vous pouvez aussi utiliser les recommandations d’Azure Security Center comme base de référence de configuration sécurisée pour vos ressources Azure.

- [Affichage des alias Azure Policy disponibles](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Tutoriel : Créer et gérer des stratégies pour assurer la conformité](../governance/policy/tutorials/create-and-manage.md)

- [Exportation monoressource ou multiressource vers un modèle sur le portail Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recommandations de sécurité - Guide de référence](../security-center/recommendations-reference.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="72-establish-secure-operating-system-configurations"></a>7.2 : Établir des configurations sécurisées du système d’exploitation

**Conseils** : Utilisez les recommandations d'Azure Security Center pour préserver les configurations de sécurité sur toutes les ressources de calcul sous-jacentes créées en tant que partie de DevTest Labs. En outre, vous pouvez utiliser des images de système d'exploitation personnalisées ou Azure Automation State Configuration pour établir la configuration de sécurité du système d'exploitation requise par votre organisation.

- [Suivi des recommandations d'Azure Security Center](../security-center/security-center-recommendations.md)

- [Recommandations de sécurité - Guide de référence](../security-center/recommendations-reference.md)

- [Vue d’ensemble d’Azure Automation State Configuration](../automation/automation-dsc-overview.md)

- [Charger un disque dur virtuel et l'utiliser pour créer des machines virtuelles Windows dans Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Créer une machine virtuelle Linux à partir d'un disque personnalisé avec Azure CLI](../virtual-machines/linux/upload-vhd.md)

- [Créer et distribuer des images personnalisées à plusieurs laboratoires DevTest](image-factory-save-distribute-custom-images.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

**Conseils** : Utilisez les commandes Azure Policy **refuser** et **déployer s’il n’existe pas** pour appliquer des paramètres sécurisés à vos ressources Azure créées en tant que partie de DevTest Labs. En outre, vous pouvez utiliser des modèles Azure Resource Manager pour appliquer la configuration de sécurité des ressources Azure requise par votre organisation.

- [Comprendre les effets d'Azure Policy](../governance/policy/concepts/effects.md)

- [Créer et gérer des stratégies pour assurer la conformité](../governance/policy/tutorials/create-and-manage.md)

- [Présentation des modèles Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4 : Préserver la sécurité des configurations du système d'exploitation

**Conseils** : Suivez les recommandations d’Azure Security Center en matière d'évaluation des vulnérabilités sur vos ressources de calcul Azure sous-jacentes créées en tant que partie d’un lab. En outre, vous pouvez utiliser des modèles Azure Resource Manager, des images de système d'exploitation personnalisées ou Azure Automation State Configuration pour appliquer la configuration de sécurité du système d'exploitation requise par votre organisation. Vous pouvez également utiliser une fabrique d’images, qui est une solution avec une configuration sous forme de code qui crée et distribue régulièrement des images, automatiquement et avec toutes les configurations nécessaires.

Notez également que les images de machines virtuelles de la Place de marché Azure publiées par Microsoft sont gérées et appliquées par Microsoft.

- [Implémenter les recommandations d'évaluation des vulnérabilités d'Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

- [Vue d’ensemble d’Azure Automation State Configuration](../automation/automation-dsc-overview.md)

- [Exemple de script permettant de charger un disque dur virtuel sur Azure et de créer une machine virtuelle](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

- [Comment créer une fabrique d’images dans DevTest Labs](image-factory-create.md)

**Responsabilité** : Partagé

**Supervision Azure Security Center** : Aucune

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

**Aide** : Utilisez Azure DevOps pour stocker et gérer en toute sécurité votre code, comme les stratégies Azure personnalisées, les modèles Azure Resource Manager et les scripts Desired State Configuration. Pour accéder aux ressources que vous gérez dans Azure DevOps, vous pouvez accorder ou refuser des autorisations à des utilisateurs spécifiques, à des groupes de sécurité intégrés ou à des groupes définis dans Azure Active Directory (Azure AD) s’ils sont intégrés à Azure DevOps.

- [Tutoriel Git Azure Repos](/azure/devops/repos/git/gitworkflow)

- [À propos des autorisations et des groupes](/azure/devops/organizations/security/about-permissions?preserve-view=true&tabs=preview-page&view=azure-devops)

- [Intégration du flux de travail d’Azure DevTest Labs et d’Azure DevOps](devtest-lab-dev-ops.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="76-securely-store-custom-operating-system-images"></a>7.6 Stocker en toute sécurité des images de système d’exploitation personnalisées

**Aide** : Si vous avez recours à des images personnalisées, utilisez le contrôle d’accès en fonction du rôle Azure (Azure RBAC) pour garantir que seuls les utilisateurs autorisés ont accès à ces images. À l’aide d’une galerie Shared Image Gallery, vous pouvez partager vos images avec des laboratoires spécifiques qui en ont besoin. Pour les images conteneur, stockez-les dans Azure Container Registry et tirez parti d’Azure RBAC pour vous assurer que seuls les utilisateurs autorisés peuvent accéder aux images.

- [Présentation d’Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Comment configurer Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Configurer une galerie Shared Image Gallery dans DevTest Labs](configure-shared-image-gallery.md)

- [Comprendre Azure RBAC pour Container Registry](../container-registry/container-registry-roles.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7 : Déployer des outils de gestion de la configuration pour les ressources Azure

**Conseils** : Définissez et implémentez des configurations de sécurité standard pour les ressources Azure à l’aide d’Azure Policy. Utilisez des alias Azure Policy pour créer des stratégies personnalisées afin d’auditer ou d’appliquer la configuration réseau de vos ressources Azure créées en tant que partie de DevTest Labs. Vous pouvez également utiliser des définitions de stratégie intégrées en lien avec vos ressources spécifiques. Par ailleurs, vous pouvez utiliser Azure Automation pour déployer les changements de configuration.

- [Configurer et gérer Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Utiliser des alias](../governance/policy/concepts/definition-structure.md#aliases)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8 : Déployer des outils de gestion de la configuration pour les systèmes d'exploitation

**Conseils** : Azure Automation State Configuration est un service de gestion de la configuration pour les nœuds DSC dans n’importe quel cloud ou centre de données local. Vous pouvez facilement intégrer des machines, leur affecter des configurations déclaratives et afficher des rapports montrant la conformité de chaque machine à l’état souhaité que vous avez spécifié. Vous pouvez également écrire un artefact personnalisé qui peut être installé sur chaque machine Lab pour s’assurer qu’il respecte les stratégies organisationnelles. 

- [Intégration des machines pour la gestion avec Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

- [Créer des artefacts personnalisés pour vos machines virtuelles DevTest Labs](devtest-lab-artifact-author.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9 : Mettre en place une supervision automatisée de la configuration pour les ressources Azure

**Conseils** : Utiliser Azure Security Center pour effectuer des analyses de ligne de base pour vos ressources Azure créées sous DevTest Labs. En outre, utilisez Azure Policy pour alerter et auditer les configurations des ressources Azure.

- [Corriger les recommandations dans Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 : Implémenter la surveillance de la configuration automatique pour les systèmes d’exploitation

**Conseils** : Utilisez Azure Security Center pour effectuer des analyses de base pour les paramètres de système d’exploitation et d’ancrage des conteneurs exécutés dans votre laboratoire.

- [Comprendre les recommandations concernant les conteneurs dans Azure Security Center](../security-center/container-security.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité

**Conseils** : Utilisez Managed Service Identity conjointement avec Azure Key Vault pour simplifier et sécuriser la gestion des secrets pour vos applications Cloud.

- [Configurer l’identité managée pour déployer des environnements Azure Resource Manager dans DevTest Labs](use-managed-identities-environments.md)

- [Configurer l’identité gérée pour déployer des machines virtuelles dans DevTest Labs](enable-managed-identities-lab-vms.md)

- [Créer un coffre de clés](../key-vault/general/quick-create-portal.md)

- [Fournir une authentification Key Vault avec une identité managée](../key-vault/general/authentication.md)

- [Comment attribuer une stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

**Conseils** : Utilisez des identités managées pour fournir aux services Azure une identité gérée automatiquement dans Azure Active Directory (Azure AD). Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

- [Configurer l’identité managée pour déployer des environnements Azure Resource Manager dans DevTest Labs](use-managed-identities-environments.md)

- [Configurer l’identité gérée pour déployer des machines virtuelles dans DevTest Labs](enable-managed-identities-lab-vms.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

**Conseils** : Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault.

- [Comment configurer Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="malware-defense"></a>Défense contre les programmes malveillants

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : Défense contre les programmes malveillants](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1 : Utiliser un logiciel anti-programme malveillant géré de manière centralisée

**Conseils** : Utilisez Microsoft Antimalware pour Azure afin de superviser et défendre en continu vos ressources. Pour Linux, utilisez une solution logicielle anti-programme malveillant tierce.  Utilisez également la détection des menaces pour les services de données d’Azure Security Center afin de détecter les programmes malveillants chargés sur des comptes de stockage. 

- [Guide pratique pour configurer l’extension Microsoft Antimalware pour Azure](../security/fundamentals/antimalware.md) 

- [Protection contre les menaces dans Azure Security Center](../security-center/azure-defender.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 : Pré-analyser les fichiers à charger sur des ressources Azure non liées au calcul

**Conseils** : Microsoft Antimalware est activé sur l’hôte sous-jacent qui prend en charge les services Azure (par exemple, Azure App Service hébergé dans un labo), mais il ne s’exécute pas sur votre contenu. 

Pré-analysez les fichiers chargés sur des ressources Azure non liées au calcul, comme App Service, Data Lake Storage, Stockage Blob, etc. 

Utilisez la détection des menaces pour les services de données dans Azure Security Center pour détecter les programmes malveillants chargés sur les comptes de stockage. 

- [Présentation de Microsoft Antimalware pour Azure](../security/fundamentals/antimalware.md) 

- [Présentation de la détection des menaces pour les services de données d’Azure Security Center](../security-center/azure-defender.md)

**Responsabilité** : Customer

**Supervision d’Azure Security Center** : Aucune

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3 : Vérifier que les logiciels et signatures anti-programme malveillant sont à jour

**Aide** : Lorsqu’il est déployé, Microsoft Antimalware pour Azure installe automatiquement les dernières signatures, plateformes et mises à jour du moteur. Suivez les recommandations faites dans Azure Security Center : « Calcul &amp; applications » pour vous assurer que tous les points de terminaison des ressources de calcul sous-jacentes de DevTest Labs sont à jour avec les dernières signatures. La protection du système d’exploitation Windows peut encore être renforcée par une sécurité supplémentaire afin de limiter le risque d’attaques basées sur les virus ou les logiciels malveillants avec le service Microsoft Defender - Protection avancée contre les menaces, qui s’intègre à Azure Security Center.

- [Guide pratique pour déployer l’extension Microsoft Antimalware pour Azure](../security/fundamentals/antimalware.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

## <a name="data-recovery"></a>Récupération des données

*Pour plus d’informations, consultez [Benchmark de sécurité Azure : récupération de données](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 : garantir des sauvegardes automatisées régulières

**Conseils** : Actuellement, Azure DevTest Labs ne prend pas en charge les sauvegardes et les captures instantanées de machine virtuelle. Toutefois, vous pouvez activer et configurer le service Sauvegarde Azure sur les machines virtuelles Azure sous-jacentes hébergées dans DevTest Labs. Vous pouvez également configurer la fréquence souhaitée et la période de rétention pour les sauvegardes automatiques, à condition que vous disposiez de l’accès approprié aux ressources de calcul sous-jacentes. 

- [Vue d’ensemble de la sauvegarde de machines virtuelles Azure](../backup/backup-azure-vms-introduction.md)

- [Sauvegarder une machine virtuelle Azure à partir des paramètres de la machine virtuelle](../backup/backup-azure-vms-first-look-arm.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 : Effectuer des sauvegardes complètes du système et sauvegarder les clés managées par le client

**Conseils** : Actuellement, Azure DevTest Labs ne prend pas en charge les sauvegardes et les captures instantanées de machine virtuelle. Toutefois, vous pouvez créer des captures instantanées de vos machines virtuelles Azure sous-jacentes hébergées dans DevTest Labs, ou des disques managés attachés à ces instances à l’aide de PowerShell ou d’API REST, à condition que vous disposiez de l’accès approprié aux ressources de calcul sous-jacentes. Vous pouvez également sauvegarder toutes les clés gérées par le client dans Azure Key Vault.

Activez le service Sauvegarde Azure sur les machines virtuelles Azure cibles, et définissez la fréquence et les périodes de rétention souhaitées. Cela inclut une sauvegarde complète de l’état du système. Si vous utilisez le chiffrement de disques Azure, la sauvegarde de machine virtuelle Azure gère automatiquement la sauvegarde des clés gérées par le client.

- [Sauvegarde sur des machines virtuelles Azure utilisant le chiffrement](../backup/backup-azure-vms-encryption.md)

- [Vue d’ensemble de la sauvegarde de machines virtuelles Azure](../backup/backup-azure-vms-introduction.md)

- [Vue d’ensemble de la sauvegarde de machines virtuelles Azure](../backup/backup-azure-vms-introduction.md)

- [Guide pratique pour sauvegarder des clés de coffre de clés dans Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 : Valider toutes les sauvegardes, y compris les clés gérées par le client

**Aide** : Assurez-vous que la restauration des données du contenu s’effectue régulièrement dans Sauvegarde Azure. Si nécessaire, testez la restauration du contenu sur un réseau virtuel ou abonnement isolé. Testez également la restauration des clés gérées par le client sauvegardées.

Si vous utilisez Azure Disk Encryption, vous pouvez restaurer la machine virtuelle Azure à l’aide des clés de chiffrement du disque. Lorsque vous utilisez le chiffrement de disque, vous pouvez restaurer la machine virtuelle Azure à l’aide des clés de chiffrement du disque.

- [Sauvegarde sur des machines virtuelles Azure utilisant le chiffrement](../backup/backup-azure-vms-encryption.md)

- [Guide pratique pour récupérer des fichiers à partir d’une sauvegarde de machine virtuelle Azure](../backup/backup-azure-restore-files-from-vm.md)

- [Guide pratique pour restaurer des clés de coffre de clés dans Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [Guide pratique pour sauvegarder et restaurer une machine virtuelle chiffrée](../backup/backup-azure-vms-encryption.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 : Garantir la protection des sauvegardes et des clés managées par le client

**Conseils** : Quand vous sauvegardez des disques managés avec Sauvegarde Azure, les machines sont chiffrées au repos avec SSE (Storage Service Encryption). Sauvegarde Azure peut également sauvegarder des machines virtuelles Azure chiffrées avec Azure Disk Encryption. Azure Disk Encryption s’intègre avec les clés de chiffrement de lecteur BitLocker qui sont sauvegardées dans un coffre de clés en tant que secrets. Azure Disk Encryption s’intègre également avec les clés de chiffrement Azure Key Vault. Activez la suppression réversible dans Key Vault pour protéger les clés contre une suppression accidentelle ou malveillante.

- [Suppression réversible pour les machines virtuelles](../backup/soft-delete-virtual-machines.md)

- [Vue d’ensemble de la suppression réversible d’Azure Key Vault](../key-vault/general/soft-delete-overview.md)

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

**Aide** : Azure Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez les abonnements à l’aide d’étiquettes et créez un système de nommage pour identifier et classer les ressources Azure, en particulier celles qui traitent des données sensibles.  Il vous incombe de hiérarchiser le traitement des alertes en fonction de la criticité des ressources et de l’environnement Azure où l’incident s’est produit. 

- [Alertes de sécurité dans le Centre de sécurité Azure](../security-center/security-center-alerts-overview.md) 

- [Organisation des ressources Azure à l’aide de catégories](../azure-resource-manager/management/tag-resources.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

**Conseils** : Effectuez des exercices pour tester les capacités de réponse aux incidents de vos systèmes à intervalles réguliers, afin de protéger vos ressources Azure. Identifiez les points faibles et les lacunes, puis révisez votre plan de réponse en fonction des besoins. 

- [Publication du NIST : Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 : Fournir des informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

**Instructions** : Microsoft utilisera les informations de contact pour le signalement d’incidents de sécurité pour vous contacter si le Microsoft Security Response Center (MSRC) découvre que vos données ont été consultées de manière illégale ou par un tiers non autorisé. Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus. 

- [Comment définir le contact de sécurité d’Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

**Conseils** : Exportez vos alertes et recommandations Azure Security Center en utilisant la fonctionnalité d’exportation continue pour identifier les risques pesant sur les ressources Azure. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser en continu les alertes vers Azure Sentinel. 

- [Comment configurer l’exportation continue](../security-center/continuous-export.md) 

- [Comment envoyer des alertes à Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilité** : Customer

**Supervision Azure Security Center** : Aucune

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

**Conseils** : Utilisez la fonctionnalité d’automatisation de workflow d’Azure Security Center pour déclencher automatiquement des réponses aux alertes et aux recommandations de sécurité afin de protéger vos ressources Azure. 

- [Comment configurer l’automatisation du workflow dans Security Center](../security-center/workflow-automation.md)

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

- Consultez [Vue d’ensemble d’Azure Security Benchmark V2](../security/benchmarks/overview.md)
- En savoir plus sur les [bases de référence de la sécurité Azure](../security/benchmarks/security-baselines-overview.md)
