---
title: Notes de publication pour Azure Security Center
description: Description des nouveautés et modifications apportées à Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 06/14/2021
ms.author: memildin
ms.openlocfilehash: 62fb48fb7517f25ceaf2b1e922ece83538157f90
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112238046"
---
# <a name="whats-new-in-azure-security-center"></a>Nouveautés d’Azure Security Center

Le Centre de sécurité fait l’objet d’un développement actif et bénéficie d’améliorations en permanence. Pour vous tenir au courant des développements les plus récents, cette page fournit des informations sur les nouvelles fonctionnalités, les correctifs de bogues et les fonctionnalités déconseillées.

Cette page étant mise à jour fréquemment, nous vous invitons donc à la consulter souvent. 

Pour en savoir plus sur les changements *planifiés* qui seront bientôt disponibles dans Security Center, consultez [Changements importants à venir dans Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Si vous recherchez des éléments datant de plus de six mois, vous les trouverez dans l’[Archive des nouveautés d’Azure Security Center](release-notes-archive.md).


## <a name="june-2021"></a>Juin 2021

Les mises à jour du mois de juin incluent :

- [Nouvelle alerte pour Azure Defender pour Key Vault](#new-alert-for-azure-defender-for-key-vault)
- [Recommandations pour chiffrer avec les clés gérées par le client (clés CMK) désactivées par défaut](#recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default)
- [Préfixe des alertes Kubernetes « AKS_ » remplacé par « K8S_ »](#prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_)
- [Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » dépréciées](#deprecated-two-recommendations-from-apply-system-updates-security-control)


### <a name="new-alert-for-azure-defender-for-key-vault"></a>Nouvelle alerte pour Azure Defender pour Key Vault

Pour développer les protections contre les menaces fournies par Azure Defender pour Key Vault, nous avons ajouté l’alerte suivante :

| Alerte (type d’alerte)                                                                 | Description                                                                                                                                                                                                                                                                                                                                                      | Tactique MITRE | severity |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| Accès à partir d’une adresse IP suspecte à un coffre de clés<br>(KV_SuspiciousIPAccess)  | Un coffre de clés a fait l’objet d’un accès réussi par une IP qui a été identifiée par Microsoft Threat Intelligence comme adresse IP suspecte. Cela peut indiquer que votre infrastructure a été compromise. Nous recommandons d’investiguer plus en profondeur. En savoir plus sur les [capacités de renseignement sur les menaces de Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). | Accès aux informations d’identification                            | Moyenne   |
|||

Pour plus d'informations, consultez les pages suivantes :
- [Présentation d’Azure Defender pour Key Vault](defender-for-resource-manager-introduction.md)
- [Répondre aux alertes Azure Defender pour Key Vault](defender-for-key-vault-usage.md)
- [Liste des alertes fournies par Azure Defender pour Key Vault](alerts-reference.md#alerts-azurekv)


### <a name="recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default"></a>Recommandations pour chiffrer avec les clés gérées par le client (clés CMK) désactivées par défaut

Security Center comprend plusieurs recommandations pour chiffrer les données au repos avec des clés gérées par le client, telles que :

- Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)
- Les comptes Azure Cosmos DB doivent utiliser des clés gérées par le client pour chiffrer les données au repos
- Les espaces de travail Azure Machine Learning doivent être chiffrés avec une clé gérée par le client (CMK)

Dans Azure, les données sont chiffrées automatiquement à l’aide de clés gérées par la plateforme. Par conséquent, l’utilisation de clés gérées par le client doit être appliquée uniquement lorsque cela est nécessaire pour la conformité à une stratégie spécifique que votre organisation choisit de mettre en vigueur.

Avec cette modification, les recommandations d’utilisation de clés CMK sont désormais **désactivées par défaut**. Si cela est pertinent pour votre organisation, vous pouvez les activer en remplaçant le paramètre d’*effet* de la stratégie de sécurité correspondante par **AuditIfNotExists** ou **Appliquer**. Apprenez-en davantage dans [Activer une stratégie de sécurité](tutorial-security-policy.md#enable-a-security-policy).

Cette modification est reflétée dans le nom de la recommandation avec un nouveau préfixe, **[Activer si nécessaire]** , comme indiqué dans les exemples suivants :

- [Activer si nécessaire] Les comptes de stockage doivent utiliser une clé gérée par le client pour chiffrer les données au repos
- [Activer si nécessaire] Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)
- [Activer si nécessaire] Les comptes Azure Cosmos DB doivent utiliser des clés gérées par le client pour chiffrer les données au repos

:::image type="content" source="media/upcoming-changes/customer-managed-keys-disabled.png" alt-text="Les recommandations concernant les clés CMK de Security Center seront désactivées par défaut." lightbox="media/upcoming-changes/customer-managed-keys-disabled.png":::


### <a name="prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_"></a>Préfixe des alertes Kubernetes « AKS_ » remplacé par « K8S_ »

Azure Defender pour Kubernetes s’est récemment développé pour protéger les clusters Kubernetes hébergés localement et dans plusieurs environnements multiclouds. Découvrez-en plus dans [Utiliser Azure Defender pour Kubernetes afin de protéger les déploiements Kubernetes hybrides et multiclouds (en préversion)](release-notes.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview).

Pour signifier que les alertes de sécurité fournies par Azure Defender pour Kubernetes ne sont plus limitées aux clusters sur Azure Kubernetes Service, nous avons remplacé le préfixe des types d’alertes « AKS_ » par « K8S_ ». Le cas échéant, les noms et les descriptions ont également été mis à jour. Par exemple, cette alerte :

|Alerte (type d’alerte)|Description|
|----|----|
|Détection d’un outil de test d’intrusion Kubernetes<br>(**AKS** _PenTestToolsKubeHunter)|L’analyse du journal d’audit de Kubernetes a détecté l’utilisation de l’outil de test d’intrusion Kubernetes dans le cluster **AKS**. Bien que ce comportement puisse être légitime, des attaquants peuvent utiliser ces outils publics à des fins malveillantes.
|||

a été remplacée par :

|Alerte (type d’alerte)|Description|
|----|----|
|Détection d’un outil de test d’intrusion Kubernetes<br>(**K8S** _PenTestToolsKubeHunter)|L’analyse du journal d’audit de Kubernetes a détecté l’utilisation de l’outil de test d’intrusion Kubernetes dans le cluster **Kubernetes**. Bien que ce comportement puisse être légitime, des attaquants peuvent utiliser ces outils publics à des fins malveillantes.|
|||

Toutes les règles de suppression qui font référence à des alertes commençant par « AKS_ » ont été automatiquement converties. Si vous avez configuré des exportations SIEM ou des scripts d’automatisation personnalisés qui font référence à des alertes Kubernetes par type d’alerte, vous avez besoin de les mettre à jour avec les nouveaux types d’alerte.

Pour obtenir la liste complète des alertes Kubernetes, consultez [Alertes pour les clusters Kubernetes](alerts-reference.md#alerts-k8scluster).

### <a name="deprecated-two-recommendations-from-apply-system-updates-security-control"></a>Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » dépréciées

Les deux recommandations suivantes ont été dépréciées :

- **La version du système d'exploitation (SE) doit être mise à jour pour vos rôles service cloud**. Par défaut, Azure met régulièrement à jour votre SE invité vers la dernière image prise en charge d’un produit de la famille de SE que vous avez spécifiée dans votre configuration de service (.cscfg), tel que Windows Server 2016.
- **Les services Kubernetes doivent être mis à niveau vers une version Kubernetes non vulnérable**. Les évaluations de cette recommandation ne sont pas aussi étendues que nous le voulions. Nous prévoyons de remplacer la recommandation par une version améliorée qui est mieux adaptée à vos besoins en matière de sécurité.


## <a name="may-2021"></a>Mai 2021

Les mises à jour du mois de mai incluent :

- [Azure Defender pour DNS et Azure Defender pour Resource Manager en disponibilité générale](#azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga)
- [Azure Defender pour les bases de données relationnelles open source en disponibilité générale](#azure-defender-for-open-source-relational-databases-released-for-general-availability-ga)
- [Nouvelles alertes pour Azure Defender pour Resource Manager](#new-alerts-for-azure-defender-for-resource-manager)
- [Analyse des vulnérabilités CI/CD des images conteneur avec les workflows GitHub et Azure Defender (préversion)](#cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview)
- [Plus de requêtes Resource Graph disponibles pour certaines recommandations](#more-resource-graph-queries-available-for-some-recommendations)
- [Changement de la gravité de la recommandation de classification des données SQL](#sql-data-classification-recommendation-severity-changed)
- [Nouvelles recommandations pour activer les fonctionnalités de lancement fiable (en préversion)](#new-recommendations-to-enable-trusted-launch-capabilities-in-preview)
- [Nouvelles recommandations pour renforcer les clusters Kubernetes (en préversion)](#new-recommendations-for-hardening-kubernetes-clusters-in-preview)
- [Développement de l’API Évaluations avec deux nouveaux champs](#assessments-api-expanded-with-two-new-fields)
- [L’inventaire des ressources obtient un filtre d’environnement cloud](#asset-inventory-gets-a-cloud-environment-filter)


### <a name="azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga"></a>Azure Defender pour DNS et Azure Defender pour Resource Manager en disponibilité générale

Ces deux plans de protection contre les menaces au niveau cloud natif sont désormais en disponibilité générale.

Ces nouvelles protections améliorent nettement votre résilience face aux attaques des acteurs des menaces et accroissent considérablement le nombre de ressources Azure protégées par Azure Defender.

- **Azure Defender pour Ressource Manager** – Supervise automatiquement toutes les opérations de gestion de ressources effectuées dans votre organisation. Pour plus d'informations, consultez les pages suivantes :
    - [Présentation d’Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md)
    - [Répondre aux alertes Azure Defender pour Resource Manager](defender-for-resource-manager-usage.md)
    - [Liste des alertes fournies par Azure Defender pour Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender pour DNS** – Supervise en continu toutes les requêtes DNS émanant de vos ressources Azure. Pour plus d'informations, consultez les pages suivantes :
    - [Présentation d’Azure Defender pour DNS](defender-for-dns-introduction.md)
    - [Répondre aux alertes Azure Defender pour DNS](defender-for-dns-usage.md)
    - [Liste des alertes fournies par Azure Defender pour DNS](alerts-reference.md#alerts-dns)

Pour simplifier le processus d’activation de ces plans, utilisez les recommandations suivantes :

- **Azure Defender pour Resource Manager doit être activé**
- **Azure Defender pour DNS doit être activé**

> [!NOTE]
> L’activation des plans Azure Defender engendre des frais. Découvrez-en plus sur les détails des prix par région dans la page des tarifs de Security Center : https://aka.ms/pricing-security-center.


### <a name="azure-defender-for-open-source-relational-databases-released-for-general-availability-ga"></a>Azure Defender pour les bases de données relationnelles open source en disponibilité générale

Azure Security Center étend son offre de protection SQL avec un nouveau bundle pour couvrir vos bases de données relationnelles open source :

- **Azure Defender pour serveurs de base de données Azure SQL** : défend vos serveurs Azure SQL natifs
- **Azure Defender pour les serveurs SQL sur machines** : étend les mêmes protections à vos serveurs SQL dans les environnements hybrides, multicloud et locaux
- **Azure Defender pour les bases de données relationnelles open source** : protège vos serveurs uniques Azure Database pour MySQL, pour PostgreSQL et for MariaDB

Azure Defender pour les bases de données relationnelles open source supervise en permanence vos serveurs à la recherche de menaces de sécurité et détecte les activités anormales de base de données indiquant des menaces potentielles envers Azure Database pour MySQL, pour PostgreSQL et for MariaDB. Quelques exemples :

- **Détection précise des attaques par force brute** : Azure Defender pour les bases de données relationnelles open source fournit des informations détaillées sur les attaques par force brute tentées et réussies. Cela vous permet d’investiguer et de répondre avec une meilleure compréhension de la nature et de l’état de l’attaque sur votre environnement.
- **Détection des alertes comportementales** : Azure Defender pour les bases de données relationnelles open source vous avertit des comportements suspects et inattendus sur vos serveurs, tels que les modifications apportées au modèle d’accès à votre base de données.
- **Détection basée sur le renseignement sur les menaces** : Azure Defender tire parti du renseignement sur les menaces de Microsoft et de la vaste base de connaissances pour exposer les alertes de menace afin que vous puissiez agir sur ces dernières.

Pour en savoir plus, consultez [Présentation d’Azure Defender pour les bases de données relationnelles open source](defender-for-databases-introduction.md).

### <a name="new-alerts-for-azure-defender-for-resource-manager"></a>Nouvelles alertes pour Azure Defender pour Resource Manager

Pour développer les protections contre les menaces fournies par Azure Defender pour Resource Manager, nous avons ajouté les alertes suivantes :

| Alerte (type d’alerte)                                                                                                                                                | Description                                                                                                                                                                                                                                                                                                                                                                                                                              | Tactiques MITRE | severity |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------:|----------|
|**Autorisations accordées à un rôle RBAC de manière inhabituelle pour votre environnement Azure (préversion)**<br>(ARM_AnomalousRBACRoleAssignment)|Azure Defender pour Resource Manager a détecté une attribution de rôle RBAC qui est inhabituelle par rapport à d’autres attributions effectuées par le même attributeur / pour le même attributaire / dans votre locataire en raison des anomalies suivantes : heure d’attribution, adresse de l’attributeur, attributeur, méthode d’authentification, entités attribuées, logiciel client utilisé, extension d’attribution. Cette opération a probablement été effectuée par un utilisateur légitime de votre organisation. Elle peut également indiquer qu’un compte de votre organisation a été violé et que l’acteur de menaces tente d’accorder des autorisations à un autre compte d’utilisateur dont il est propriétaire.|Mouvement latéral, évasion de la défense|Moyenne|
|**Rôle personnalisé privilégié créé pour votre abonnement de façon suspecte (préversion)**<br>(ARM_PrivilegedRoleDefinitionCreation)|Azure Defender pour Resource Manager a détecté une création suspecte de définition de rôle personnalisé privilégié dans votre abonnement. Cette opération a probablement été effectuée par un utilisateur légitime de votre organisation. Elle peut également indiquer qu’un compte de votre organisation a été violé et que l’acteur de menaces tente de créer un rôle privilégié qu’il utilisera pour s’évader de la détection.|Mouvement latéral, évasion de la défense|Faible|
|**Opération Azure Resource Manager depuis une adresse IP suspecte (préversion)**<br>(ARM_OperationFromSuspiciousIP)|Azure Defender pour Resource Manager a détecté une opération depuis une adresse IP qui a été marquée comme suspecte dans les flux de renseignement sur les menaces.|Exécution|Moyenne|
|**Opération Azure Resource Manager depuis une adresse IP proxy suspecte (préversion)**<br>(ARM_OperationFromSuspiciousProxyIP)|Azure Defender pour Resource Manager a détecté une opération de gestion des ressources depuis une adresse IP associée à des services proxy comme TOR. Bien que ce comportement puisse être légitime, il est souvent observé dans le cadre d’activités malveillantes, lorsque les acteurs de menaces tentent de dissimuler leur IP source.|Évasion de défense|Moyenne|
||||

Pour plus d'informations, consultez les pages suivantes :
- [Présentation d’Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md)
- [Répondre aux alertes Azure Defender pour Resource Manager](defender-for-resource-manager-usage.md)
- [Liste des alertes fournies par Azure Defender pour Resource Manager](alerts-reference.md#alerts-resourcemanager)


### <a name="cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview"></a>Analyse des vulnérabilités CI/CD des images conteneur avec les workflows GitHub et Azure Defender (préversion)

Azure Defender pour les registres de conteneurs offre désormais aux équipes DevSecOps une observabilité des workflows d’action GitHub.

La nouvelle fonctionnalité d’analyse des vulnérabilités pour les images conteneur, utilisant Trivy, aide vos développeurs à rechercher les vulnérabilités courantes dans leurs images conteneur *avant* d’envoyer les images vers des registres de conteneurs.

Les rapports d’analyse de conteneur sont résumés dans Azure Security Center, ce qui offre aux équipes de sécurité un meilleur insight et une meilleure compréhension de la source des images conteneur vulnérables ainsi que des workflows et dépôts desquels elles proviennent.

Apprenez-en davantage dans [Identifier les images conteneur vulnérables dans vos workflows CI/CD](defender-for-container-registries-cicd.md).

### <a name="more-resource-graph-queries-available-for-some-recommendations"></a>Plus de requêtes Resource Graph disponibles pour certaines recommandations

Toutes les recommandations de Security Center permettent d’afficher les informations relatives à l’état des ressources affectées en utilisant Azure Resource Graph à partir du bouton **Ouvrir une requête**. Pour obtenir tous les détails sur cette fonctionnalité puissante, consultez [Examiner les données de recommandation dans l’Explorateur Azure Resource Graph (ARG)](security-center-recommendations.md#review-recommendation-data-in-azure-resource-graph-explorer-arg).

Security Center comporte des analyseurs de vulnérabilités intégrés pour analyser vos machines virtuelles, vos serveurs SQL et leurs hôtes ainsi que les registres de conteneurs pour les vulnérabilités de sécurité. Les résultats sont retournés sous forme de recommandations avec toutes les découvertes individuelles pour chaque type de ressource rassemblées dans une vue unique. Les recommandations sont les suivantes :

- Les vulnérabilités dans les images Azure Container Registry doivent être corrigées (avec Qualys)
- Les vulnérabilités de vos machines virtuelles doivent être corrigées
- Les résultats des vulnérabilités des bases de données SQL doivent être résolus
- Les résultats des vulnérabilités des serveurs SQL Server sur les machines doivent être résolus

Une fois cette modification apportée, vous pouvez aussi utiliser le bouton **Ouvrir une requête** pour ouvrir la requête qui affiche les résultats de la sécurité.

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="Le bouton Ouvrir une requête propose désormais des options pour une requête plus détaillée présentant les résultats de la sécurité pour les recommandations relatives à l’analyseur de vulnérabilités.":::

Le bouton **Ouvrir une requête** offre aussi des options supplémentaires pour d’autres recommandations, le cas échéant.

Découvrez plus d’informations sur les analyseurs de vulnérabilités de Security Center :

- [Analyseur d’évaluation des vulnérabilités intégré d’Azure Defender pour les machines Azure et hybrides](deploy-vulnerability-assessment-vm.md)
- [Analyseur d’évaluation des vulnérabilités intégré d’Azure Defender pour les serveurs SQL](defender-for-sql-on-machines-vulnerability-assessment.md)
- [Analyseur d’évaluation des vulnérabilités intégré d’Azure Defender pour les registres de conteneurs](defender-for-container-registries-usage.md)

### <a name="sql-data-classification-recommendation-severity-changed"></a>Changement de la gravité de la recommandation de classification des données SQL

La gravité de la recommandation **Les données sensibles de vos bases de données SQL doivent être classifiées** est passée d’un niveau **élevé** à un niveau **faible**.

Ce changement s’inscrit dans le cadre des changements apportés à cette recommandation annoncés dans [Améliorations apportées à la recommandation de classification des données SQL](upcoming-changes.md#enhancements-to-sql-data-classification-recommendation). 


### <a name="new-recommendations-to-enable-trusted-launch-capabilities-in-preview"></a>Nouvelles recommandations pour activer les fonctionnalités de lancement fiable (en préversion)

Azure propose le lancement fiable pour améliorer de manière fluide la sécurité des machines virtuelles de [2e génération](../virtual-machines/generation-2.md). Le lancement fiable protège contre les techniques d’attaque avancées et persistantes. Le lancement fiable se compose de plusieurs technologies d’infrastructure coordonnées qui peuvent être activées indépendamment. Chaque technologie offre une couche de défense supplémentaire contre les menaces sophistiquées. Apprenez-en davantage dans [Lancement fiable pour les machines virtuelles Azure](../virtual-machines/trusted-launch.md).

> [!IMPORTANT]
> Le lancement fiable requiert la création de nouvelles machines virtuelles. Vous ne pouvez pas activer le lancement fiable sur les machines virtuelles existantes qui ont été créées initialement sans cette fonctionnalité.
> 
> Le lancement fiable est actuellement disponible en préversion publique. La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

La recommandation de Security Center, **vTPM doit être activé sur les machines virtuelles prises en charge**, garantit que vos machines virtuelles Azure utilisent un vTPM. Cette version virtualisée d’un module de plateforme sécurisée matériel permet d’effectuer l’attestation en mesurant la chaîne de démarrage complète de votre machine virtuelle (UEFI, système d’exploitation, système et pilotes).

Lorsque le vTPM est activé, l’**extension Attestation d’invité** peut valider à distance le démarrage sécurisé. Les recommandations suivantes garantissent le déploiement de cette extension :

- **Le démarrage sécurisé doit être activé sur les machines virtuelles Windows prises en charge**
- **L’extension Attestation d’invité doit être installée sur les machines virtuelles Windows prises en charge**
- **L’extension Attestation d’invité doit être installée sur les groupes de machines virtuelles identiques Windows pris en charge**
- **L’extension Attestation d’invité doit être installée sur les machines virtuelles Linux prises en charge**
- **L’extension Attestation d’invité doit être installée sur les groupes de machines virtuelles identiques Linux pris en charge**

Apprenez-en davantage dans [Lancement fiable pour les machines virtuelles Azure](../virtual-machines/trusted-launch.md). 

### <a name="new-recommendations-for-hardening-kubernetes-clusters-in-preview"></a>Nouvelles recommandations pour renforcer les clusters Kubernetes (en préversion)

Les recommandations suivantes vous permettent de renforcer davantage vos clusters Kubernetes

- **Les clusters Kubernetes ne doivent pas utiliser l’espace de noms par défaut** : pour éviter tout accès non autorisé aux types de ressources ConfigMap, Pod, Secret, Service et ServiceAccount, empêchez l’utilisation de l’espace de noms par défaut dans les clusters Kubernetes.
- **Les clusters Kubernetes doivent désactiver le montage automatique des informations d’identification d’API** : pour empêcher une ressource Pod potentiellement compromise d’exécuter des commandes API sur des clusters Kubernetes, désactivez le montage automatique des informations d’identification de l’API.
- **Les clusters Kubernetes ne doivent pas octroyer de fonctionnalités de sécurité CAPSYSADMIN**

Découvrez comment Security Center peut protéger vos environnements conteneurisés dans [Sécurité des conteneurs dans Security Center](container-security.md).

### <a name="assessments-api-expanded-with-two-new-fields"></a>Développement de l’API Évaluations avec deux nouveaux champs

Nous avons ajouté les deux champs suivants à l’[API REST Évaluations](/rest/api/securitycenter/assessments) :

- **FirstEvaluationDate** : Heure à laquelle la recommandation a été créée et évaluée pour la première fois. Retournée sous forme de temps universel coordonné (UTC) au format ISO 8601.
- **StatusChangeDate** : Heure de la dernière modification de l’état de la recommandation. Retournée sous forme de temps universel coordonné (UTC) au format ISO 8601.

La valeur initiale par défaut de ces champs (pour toutes les recommandations) est `2021-03-14T00:00:00+0000000Z`.

Pour accéder à ces informations, vous pouvez utiliser l’une des méthodes indiquées dans le tableau ci-dessous.

| Outil                 | Détails                                                                                                                                                                |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Appel d’API REST        | GET https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/providers/Microsoft.Security/assessments?api-version=2019-01-01-preview& $expand=statusEvaluationDates |
| Azure Resource Graph | `securityresources`<br>`where type == "microsoft.security/assessments"`                                                                                                |
| Exportation continue    | Les deux champs dédiés seront disponibles dans les données de l’espace de travail Log Analytics                                                                                            |
| [Exportation CSV](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations) | Les deux champs sont inclus dans les fichiers CSV.                                                        |
|                      |                                                                                                                                                                        |


Découvrez-en plus sur l’[API REST Évaluations](/rest/api/securitycenter/assessments).


### <a name="asset-inventory-gets-a-cloud-environment-filter"></a>L’inventaire des ressources obtient un filtre d’environnement cloud

La page d’inventaire des ressources de Security Center propose un certain nombre de filtres pour affiner rapidement la liste des ressources affichées. Pour en savoir plus, consultez [Explorer et gérer vos ressources avec l’inventaire des ressources](asset-inventory.md).

Un nouveau filtre offre la possibilité d’affiner la liste en fonction des comptes cloud que vous avez connectés avec les fonctionnalités multiclouds de Security Center :

:::image type="content" source="media/asset-inventory/filter-environment.png" alt-text="Filtre d’environnement de l’inventaire":::

Découvrez plus d’informations sur les fonctionnalités multiclouds :

- [Connecter vos comptes AWS à Azure Security Center](quickstart-onboard-aws.md)
- [Connectez vos comptes GCP à Azure Security Center](quickstart-onboard-gcp.md)


## <a name="april-2021"></a>Avril 2021

Les mises à jour du mois d’avril incluent :
- [Actualisation de la page d’intégrité des ressources (en préversion)](#refreshed-resource-health-page-in-preview)
- [Les images de registre de conteneurs qui ont été récemment extraites sont à présent réanalysées chaque semaine (en disponibilité générale)](#container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga)
- [Utiliser Azure Defender pour Kubernetes afin de protéger les déploiements Kubernetes hybrides et multiclouds (en préversion)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)
- [L’intégration de Microsoft Defender pour point de terminaison à Azure Defender prend désormais en charge Windows Server 2019 et Windows 10 Virtual Desktop (WVD) publié pour la disponibilité générale](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga)
- [Recommandations concernant l’activation d’Azure Defender pour DNS et de Resource Manager (en préversion)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview)
- [Ajout de trois normes de conformité réglementaire : Azure CIS 1.3.0, CMMC niveau 3 et New Zealand ISM Restricted](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [Quatre nouvelles recommandations relatives à la configuration des invités (en préversion)](#four-new-recommendations-related-to-guest-configuration-in-preview)
- [Recommandations CMK déplacées dans le contrôle de sécurité des bonnes pratiques](#cmk-recommendations-moved-to-best-practices-security-control)
- [Onze alertes Azure Defender déconseillées](#11-azure-defender-alerts-deprecated)
- [Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » sont désormais déconseillées](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [Vignette « Azure Defender pour SQL sur des machines » supprimée du tableau de bord Azure Defender](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)
- [21 recommandations déplacées entre les contrôles de sécurité](#21-recommendations-moved-between-security-controls)

### <a name="refreshed-resource-health-page-in-preview"></a>Actualisation de la page d’intégrité des ressources (en préversion)

L’intégrité des ressources de Security Center a été développée, améliorée et perfectionnée pour fournir une vue instantanée de l’état d’intégrité global d’une ressource unique. 

Vous pouvez consulter des informations détaillées sur la ressource et toutes les recommandations qui s’y appliquent. De plus, si vous utilisez [Azure Defender](azure-defender.md), les alertes de sécurité en suspens pour cette ressource spécifique s’affichent également.

Pour ouvrir la page d’intégrité des ressources pour une ressource, sélectionnez n’importe quelle ressource dans la [page d’inventaire des ressources](asset-inventory.md).

Cette page d’aperçu dans les pages du portail de Security Center affiche les éléments suivants :

1. **Informations sur la ressource** : groupe de ressources et abonnement auxquels elle est associée, emplacement géographique, etc.
1. **Fonctionnalité de sécurité appliquée** : indique si Azure Defender est activé pour la ressource.
1. **Nombre de recommandations en suspens et d’alertes** : nombre de recommandations de sécurité en suspens et d’alertes Azure Defender.
1. **Recommandations et alertes actionnables** : deux onglets listent les recommandations et les alertes qui s’appliquent à la ressource.

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="Page Intégrité des ressources d’Azure Security Center présentant des informations d’intégrité d’une machine virtuelle":::

Découvrez-en plus dans le [Tutoriel : Examiner l’intégrité de vos ressources](investigate-resource-health.md).


### <a name="container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga"></a>Les images de registre de conteneurs qui ont été récemment extraites sont à présent réanalysées chaque semaine (en disponibilité générale)

Azure Defender pour les registres de conteneurs comprend un analyseur de vulnérabilité intégré. Cet analyseur analyse immédiatement toute image que vous envoyez à votre registre et toute image extraite au cours des 30 derniers jours.

De nouvelles vulnérabilités sont découvertes tous les jours. Avec cette mise à jour, les images conteneur qui ont été extraites de vos registres au cours des 30 derniers jours sont **réanalysées** chaque semaine. Procéder ainsi permet de s’assurer que les vulnérabilités nouvellement découvertes sont identifiées dans vos images.

L’analyse est facturée par image, aucuns frais supplémentaires ne sont donc facturés pour ces analyses.

Apprenez-en davantage sur cet analyseur en consultant [Analyse des vulnérabilités dans les images avec Azure Defender pour les registres de conteneurs](defender-for-container-registries-usage.md).


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview"></a>Utiliser Azure Defender pour Kubernetes afin de protéger les déploiements Kubernetes hybrides et multiclouds (en préversion)

Azure Defender pour Kubernetes étend ses fonctionnalités de protection contre les menaces afin de protéger vos clusters où qu'ils soient déployés. L’intégration de [Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md) et de ses nouvelles [fonctionnalités d’extensions](../azure-arc/kubernetes/extensions.md) a rendu cela possible. 

Lorsque vous avez activé Azure Arc sur vos clusters Kubernetes non Azure, une nouvelle recommandation d'Azure Security Center propose d'y déployer l'extension Azure Defender en quelques clics seulement.

Utilisez la recommandation (**L'extension d'Azure Defender doit être installée sur les clusters Kubernetes avec Azure Arc**) et l'extension pour protéger les clusters Kubernetes déployés via d'autres fournisseurs de cloud, mais pas sur leurs services Kubernetes managés.

Cette intégration entre Azure Security Center, Azure Defender et Kubernetes avec Azure Arc offre les avantages suivants :

- Approvisionnement aisé de l'extension Azure Defender sur les clusters Kubernetes avec Azure Arc non protégés (manuellement et à grande échelle)
- Surveillance de l'extension Azure Defender et de son état d'approvisionnement à partir du portail Azure Arc
- Les recommandations de sécurité de Security Center sont signalées sur la nouvelle page Sécurité du portail Azure Arc
- Les menaces de sécurité identifiées par Azure Defender sont signalées sur la nouvelle page Sécurité du portail Azure Arc
- Les clusters Kubernetes avec Azure Arc sont intégrés à la plateforme et à l'expérience Azure Security Center

Pour en savoir plus, consultez [Utiliser Azure Defender pour Kubernetes avec vos clusters Kubernetes locaux et multicloud](defender-for-kubernetes-azure-arc.md).

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Recommandation d’Azure Security Center relative au déploiement de l’extension Azure Defender pour les clusters Kubernetes avec Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga"></a>L’intégration de Microsoft Defender pour point de terminaison à Azure Defender prend désormais en charge Windows Server 2019 et Windows 10 Virtual Desktop (WVD) publié en disponibilité générale

Microsoft Defender for Endpoint est une solution holistique de sécurité des points de terminaison dans le cloud. Il fournit une gestion et une évaluation des vulnérabilités basées sur les risques ainsi que la détection et la réponse des points de terminaison (EDR). Pour obtenir la liste complète des avantages de l’utilisation de Defender pour point de terminaison avec Azure Security Center, consultez [Protéger vos points de terminaison avec la solution EDR intégrée de Security Center : Microsoft Defender pour point de terminaison](security-center-wdatp.md).

Quand vous activez Azure Defender pour les serveurs sur un serveur Windows, une licence pour Defender pour point de terminaison est incluse dans le plan. Si vous avez déjà activé Azure Defender pour les serveurs et que vous avez des serveurs Windows 2019 dans votre abonnement, ils recevront automatiquement Defender pour point de terminaison avec cette mise à jour. Aucune action manuelle n’est nécessaire. 

La prise en charge a été étendue de façon à inclure Windows Server 2019 et [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md).

> [!NOTE]
> Si vous activez Defender pour point de terminaison sur une machine Windows Server 2019, vérifiez qu’elle répond aux prérequis décrits dans [Activer l’intégration de Microsoft Defender pour point de terminaison](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration).


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview"></a>Recommandations concernant l’activation d’Azure Defender pour DNS et de Resource Manager (en préversion)

Deux nouvelles recommandations ont été ajoutées afin de simplifier le processus d’activation d’[Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md) et d’[Azure Defender pour DNS](defender-for-dns-introduction.md):

- **Azure Defender pour Resource Manager doit être activé** - Defender pour Resource Manager supervise automatiquement toutes les opérations de gestion de ressources dans votre organisation. Azure Defender détecte les menaces et vous alerte sur les activités suspectes.
- **Azure Defender pour DNS doit être activé** - Defender pour DNS fournit une couche supplémentaire de protection pour vos ressources cloud en supervisant en continu toutes les requêtes DNS émises par vos ressources Azure. Azure Defender vous avertit des activités suspectes au niveau de la couche DNS.

L’activation des plans Azure Defender engendre des frais. Découvrez-en plus sur les détails des prix par région dans la page des tarifs de Security Center : https://aka.ms/pricing-security-center.

> [!TIP]
> Les recommandations en préversion ne rendent pas une ressource non saine et ne sont pas incluses dans les calculs de votre degré de sécurisation. Corrigez-les là où c’est possible, de sorte que quand la période de préversion se termine, elles soient prises en compte dans le calcul de votre degré de sécurisation. Découvrez comment répondre à ces recommandations dans [Corriger les recommandations dans Azure Security Center](security-center-remediate-recommendations.md).


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>Ajout de trois normes de conformité réglementaire : Azure CIS 1.3.0, CMMC niveau 3 et New Zealand ISM Restricted

Nous avons ajouté trois normes à utiliser avec Azure Security Center. À l’aide du tableau de bord de conformité réglementaire, vous pouvez désormais suivre votre conformité avec :

- [CIS Microsoft Azure Foundations Benchmark 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC niveau 3](../governance/policy/samples/cmmc-l3.md)
- [New Zealand ISM Restricted](../governance/policy/samples/new-zealand-ism.md)

Vous pouvez les attribuer à vos abonnements comme décrit dans [Personnaliser l’ensemble de normes du tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="Ajout de trois normes à utiliser avec le tableau de bord de conformité réglementaire d’Azure Security Center." lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

Pour en savoir plus :
- [Personnaliser l’ensemble de normes du tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md)
- [Tutoriel : Améliorer votre conformité aux normes](security-center-compliance-dashboard.md)
- [Questions fréquentes (FAQ) - Tableau de bord de conformité réglementaire](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-in-preview"></a>Quatre nouvelles recommandations relatives à la configuration des invités (en préversion)

L'[extension Configuration des invités](../governance/policy/concepts/guest-configuration.md) d'Azure effectue des signalements auprès de Security Center pour renforcer les paramètres relatifs aux invités de vos machines virtuelles. L'extension n'est pas nécessaire pour les serveurs avec Arc car elle est incluse dans Azure Connected Machine Agent. L'extension nécessite une identité gérée par le système sur la machine.

Nous avons ajouté quatre nouvelles recommandations à Security Center pour tirer le meilleur parti de cette extension.

- Deux recommandations vous invitent à installer l'extension et l'identité gérée par le système correspondante :
    - **L’extension Guest Configuration doit être installée sur vos machines**
    - **L’extension Guest Configuration des machines virtuelles doit être déployée avec une identité managée attribuée par le système**

- Une fois l'extension installée et exécutée, elle commence l'audit de vos machines et vous êtes invité à renforcer les paramètres tels que la configuration du système d'exploitation et les paramètres d'environnement. Les deux recommandations suivantes vous inviteront à renforcer vos machines Windows et Linux comme décrit :
    - **Windows Defender Exploit Guard doit être activé sur vos machines**
    - **L’authentification auprès des machines Linux doit exiger des clés SSH**

Pour en savoir plus, consultez [Présentation de la fonctionnalité de configuration des invités d'Azure Policy](../governance/policy/concepts/guest-configuration.md).

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>Les recommandations CMK ont été déplacées dans le contrôle de sécurité des bonnes pratiques

Le programme de sécurité de chaque organisation comprend des exigences en matière de chiffrement de données. Par défaut, les données des clients Azure sont chiffrées au repos avec des clés gérées par le service. Toutefois, les clés gérées par le client (CMK) sont généralement nécessaires pour répondre aux normes de conformité réglementaire. Les CMK vous permettent de chiffrer vos données avec une clé [Azure Key Vault](../key-vault/general/overview.md) que vous avez créée et dont vous êtes le propriétaire. Ainsi, le contrôle total et la responsabilité du cycle de vie des clés, notamment leur permutation et leur gestion, vous sont donnés.

Les contrôles de sécurité d’Azure Security Center sont des groupes logiques de recommandations de sécurité associées, qui reflètent vos surfaces d’attaque vulnérables. Chaque contrôle comprend un nombre maximal de points que vous pouvez ajouter à votre degré de sécurisation si vous appliquez toutes les recommandations indiquées dans le contrôle, pour toutes vos ressources. Le contrôle de sécurité **Implémenter les bonnes pratiques de sécurité** vaut zéro point. Les recommandations de ce contrôle n’ont donc pas d’incidence sur votre degré de sécurisation.

Les recommandations listées ci-dessous sont en cours de déplacement vers le contrôle de sécurité **Implémenter les bonnes pratiques de sécurité** pour mieux refléter leur nature facultative. Ce déplacement garantit que ces recommandations se trouvent dans le contrôle le plus approprié pour atteindre leur objectif.

- Les comptes Azure Cosmos DB doivent utiliser des clés gérées par le client pour chiffrer les données au repos
- Les espaces de travail Azure Machine Learning doivent être chiffrés avec une clé gérée par le client (CMK)
- Les comptes Cognitive Services doivent activer le chiffrement des données avec une clé gérée par le client (CMK)
- Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)
- Les instances gérées SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos
- Les serveurs SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos
- Les comptes de stockage doivent utiliser une clé gérée par le client (CMK) pour le chiffrement

Découvrez les recommandations de chaque contrôle de sécurité dans [Contrôles de sécurité et leurs recommandations](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="11-azure-defender-alerts-deprecated"></a>Onze alertes Azure Defender déconseillées

Les onze alertes Azure Defender répertoriées ci-dessous sont désormais déconseillées.

- Ces deux alertes seront remplacées par de nouvelles alertes offrant une meilleure couverture :

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PRÉVERSION - Détection de l’exécution de la fonction « Get-AzureDomainInfo » du kit de ressources MicroBurst |
    | ARM_MicroBurstRunbook    | PRÉVERSION - Détection de l’exécution de la fonction « Get-AzurePasswords » du kit de ressources MicroBurst  |
    |                          |                                                                          |

- Les neuf alertes suivantes concernent un connecteur IPC (Azure Active Directory Identity Protection) déjà déconseillé :

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Propriétés de connexion inhabituelles |
    | AnonymousLogin      | Adresse IP anonyme          |
    | InfectedDeviceLogin | Adresse IP liée à un programme malveillant     |
    | ImpossibleTravel    | Voyage inhabituel               |
    | MaliciousIP         | Adresse IP malveillante          |
    | LeakedCredentials   | Informations d’identification divulguées            |
    | PasswordSpray       | Pulvérisation de mots de passe                |
    | LeakedCredentials   | Azure AD Threat Intelligence  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > Ces neuf alertes IPC n'ont jamais été des alertes Security Center. Elles font partie du connecteur IPC (Identity Protection Connector) AAD (Azure Active Directory) qui les envoyait à Security Center. Ces deux dernières années, les seuls clients qui ont vu ces alertes sont les organisations qui avaient configuré l'exportation (du connecteur vers ASC) en 2019 ou avant. Le connecteur IPC AAD a continué à les afficher dans ses propres systèmes d'alertes et elles sont restées disponibles dans Azure Sentinel. Le seul changement est qu'elles n'apparaissent plus dans Security Center.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>Deux recommandations du contrôle de sécurité « Appliquer les mises à jour système » sont désormais déconseillées 

Les deux recommandations suivantes sont désormais déconseillées et les changements peuvent avoir un léger impact sur votre niveau de sécurité :

- **Vos machines doivent être redémarrées pour appliquer les mises à jour système**
- **L’agent de surveillance doit être installé sur vos machines**. Cette recommandation se réfère uniquement aux machines locales, et une partie de sa logique sera transférée vers une autre recommandation : **Les problèmes d'intégrité de l'agent Log Analytics doivent être résolus sur vos machines**.

Nous vous recommandons de vérifier vos configurations d’exportation continue et d’automatisation du workflow pour voir si ces recommandations y sont incluses. En outre, tous les tableaux de bord et autres outils de supervision susceptibles de les utiliser doivent être mis à jour en conséquence.

Pour plus d’informations sur ces recommandations, consultez la [page de référence sur les recommandations de sécurité](recommendations-reference.md).

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>Vignette « Azure Defender pour SQL sur des machines » supprimée du tableau de bord Azure Defender

La zone de couverture du tableau de bord Azure Defender comprend des vignettes correspondant aux plans Azure Defender concernés de votre environnement. En raison d’un problème lié au signalement du nombre de ressources protégées et non protégées, nous avons décidé de supprimer temporairement l’état de couverture des ressources pour **Azure Defender pour SQL sur des machines** jusqu’à ce que le problème soit résolu.


### <a name="21-recommendations-moved-between-security-controls"></a>21 recommandations déplacées entre les contrôles de sécurité 

Les recommandations suivantes ont été déplacées vers d’autres contrôles de sécurité. Les contrôles de sécurité sont des groupes logiques de recommandations de sécurité associées, qui reflète les surfaces d’attaque vulnérables. Ce déplacement garantit que toutes ces recommandations sont dans le contrôle le plus approprié pour atteindre son objectif.

Découvrez les recommandations de chaque contrôle de sécurité dans [Contrôles de sécurité et leurs recommandations](secure-score-security-controls.md#security-controls-and-their-recommendations).

|Recommandation |Modification et impact  |
|---------|---------|
|L’évaluation des vulnérabilités doit être activée sur vos serveurs SQL<br>L’évaluation des vulnérabilités doit être activée sur vos instances managées SQL<br>Les vulnérabilités de vos bases de données SQL doivent être corrigées<br>Les vulnérabilités sur vos bases de données SQL dans les machines virtuelles doivent être corrigées     |Déplacement de Corriger les vulnérabilités (6 points)<br>vers Corriger les configurations de sécurité (4 points).<br>Ces recommandations ont un impact réduit sur votre score, en fonction de votre environnement.|
|Plusieurs propriétaires doivent être affectés à votre abonnement<br>Les variables de compte Automation doivent être chiffrées<br> Appareils IoT – Le processus audité a arrêté d’envoyer des événements<br> Appareils IoT – Échec de la validation de la base du système d’exploitation<br> Appareils IoT – Mise à niveau de la suite de chiffrement TLS requise<br> Appareils IoT – Ports ouverts sur l’appareil<br> Appareils IoT – Stratégie de pare-feu permissive trouvée dans l’une des chaînes<br> Appareils IoT – Règle de pare-feu permissive trouvée dans la chaîne d’entrée<br> Appareils IoT – Règle de pare-feu permissive trouvée dans la chaîne de sortie<br>Les journaux de diagnostic dans IoT Hub doivent être activés<br> Appareils IoT – Agent envoyant des messages sous-exploités<br>Appareils IoT : la stratégie de filtre IP par défaut devrait être refusée<br>Appareils IoT : plage d’adresses IP large pour la règle de filtre IP<br>Appareils IoT : les intervalles et la taille des messages des agents doivent être ajustés<br>Appareils IoT : informations d’identification et d’authentification identiques<br>Appareils IoT : le processus audité a arrêté d’envoyer des événements<br>Appareils IoT : la configuration de ligne de base du système d’exploitation doit être corrigée|Déplacement vers **Implémenter les meilleures pratiques de sécurité**.<br>Lorsqu’une recommandation se déplace vers le contrôle de sécurité Implémenter les bonnes pratiques de sécurité, ce qui n’est pas du tout judicieux, la recommandation n’affecte plus votre score sécurisé.|
|||


## <a name="march-2021"></a>Mars 2021

Les mises à jour du mois de mars incluent :

- [Gestion du Pare-feu Azure intégrée à Security Center](#azure-firewall-management-integrated-into-security-center)
- [L’évaluation des vulnérabilités SQL comprend désormais l’expérience « Désactiver la règle » (préversion)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Classeurs Azure Monitor intégrés dans Security Center et trois modèles fournis](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Le tableau de bord de conformité réglementaire comprend désormais les rapports d’audit Azure (préversion)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Les données de recommandation peuvent être consultées dans Azure Resource Graph avec « Explorer dans ARG »](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Mises à jour des stratégies pour le déploiement de l’automatisation des workflows](#updates-to-the-policies-for-deploying-workflow-automation)
- [Deux recommandations héritées n’écrivent plus de données directement dans le journal d’activité Azure](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Améliorations de la page Suggestions](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Gestion du Pare-feu Azure intégrée à Security Center

Lorsque vous ouvrez Azure Security Center, la première page qui s’affiche est la page de présentation. 

Ce tableau de bord interactif fournit une vue unifiée de la posture de sécurité de vos charges de travail cloud hybrides. En outre, elle affiche des alertes de sécurité, des informations de couverture, etc.

Pour vous aider à visualiser l’état de votre sécurité avec une expérience centralisée, nous avons intégré Azure Firewall Manager à ce tableau de bord. Vous pouvez désormais vérifier l’état de couverture du Pare-feu sur tous les réseaux et gérer de façon centralisée les stratégies de pare-feu Azure à partir de Security Center.

En savoir plus sur ce tableau de bord dans la [page Vue d’ensemble d’Azure Security Center](overview-page.md).

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Tableau de bord de vue d’ensemble de Security Center avec une vignette pour Pare-feu Azure":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>L’évaluation des vulnérabilités SQL comprend désormais l’expérience « Désactiver la règle » (préversion)

Security Center comprend un analyseur de vulnérabilités intégré pour vous aider à découvrir, suivre et corriger les vulnérabilités potentielles des bases de données. Les résultats de vos analyses d’évaluation fournissent une vue d’ensemble de l’état de sécurité de vos machines SQL ainsi que des détails sur les éventuels problèmes de sécurité découverts.

Si votre organisation préfère ignorer un résultat, plutôt que de le corriger, vous pouvez éventuellement désactiver cette fonction. Les résultats désactivés n’ont pas d’impact sur votre Niveau de sécurité ni ne génèrent de bruit indésirable.

Pour plus d’informations, consultez [Désactiver des découvertes spécifiques](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview).



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Classeurs Azure Monitor intégrés dans Security Center et trois modèles fournis

Dans le cadre d’Ignite Spring 2021, nous avons annoncé une expérience intégrée des classeurs Azure Monitor dans Security Center.

Vous pouvez tirer parti de la nouvelle intégration pour commencer à utiliser les modèles prêts à l’emploi de la galerie de Security Center. En utilisant des modèles de classeurs, vous pouvez accéder à des rapports dynamiques et visuels et en créer pour suivre la posture de sécurité de votre organisation. Vous pouvez aussi créer des classeurs basés sur des données Security Center ou tout autre type de données pris en charge, et déployer rapidement des classeurs de communauté de la communauté GitHub de Security Center.

Trois modèles de rapport sont fournis :

- **Évolution du degré de sécurisation** : suivez les scores de vos abonnements et les modifications apportées aux recommandations pour vos ressources
- **Mises à jour du système** : visualisez les mises à jour système manquantes par ressources, système d’exploitation, gravité, etc.
- **Résultats de l’évaluation des vulnérabilités** : visualisez les découvertes des analyses de vulnérabilités de vos ressources Azure

En savoir plus sur l’utilisation de ces rapports ou sur la création de vos propres rapports : [Créer des rapports interactifs et riches de données Security Center](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Rapport sur le degré de sécurisation dans le temps.":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Le tableau de bord de conformité réglementaire comprend désormais les rapports d’audit Azure (préversion)

À partir de la barre d’outils du tableau de bord de conformité réglementaire, vous pouvez désormais télécharger les rapports de certification Azure et Dynamics. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Barre d’outils du tableau de bord de conformité réglementaire":::

Vous pouvez sélectionner l’onglet pour les types de rapports appropriés (PCI, SOC, ISO et autres) et utiliser des filtres pour rechercher les rapports spécifiques dont vous avez besoin.

En savoir plus sur la [Gestion des normes dans votre tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Filtrage de la liste des rapports d’audit Azure disponibles.":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Les données de recommandation peuvent être consultées dans Azure Resource Graph avec « Explorer dans ARG »

Les pages de détails de recommandation incluent désormais le bouton de barre d’outils « Explorer dans ARG ». Utilisez ce bouton pour ouvrir une requête Azure Resource Graph et explorer, exporter et partager les données de la recommandation.

Azure Resource Graph (ARG) fournit un accès instantané aux informations relatives aux ressources de vos environnements cloud avec des fonctionnalités robustes de filtrage, de regroupement et de tri. Il s’agit d’un moyen rapide et efficace de demander des informations dans les abonnements Azure par programmation ou depuis le Portail Azure.

Apprenez-en davantage sur [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml).

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Explorez les données de recommandation dans Azure Resource Graph.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Mises à jour des stratégies pour le déploiement de l’automatisation des workflows

L’automatisation des processus d’analyse et de réponse aux incidents de votre organisation peut réduire de manière significative le temps requis pour examiner et atténuer les incidents de sécurité.

Nous fournissons trois stratégies Azure Policy « DeployIfNotExist » qui créent et configurent des procédures d’automatisation des workflows pour vous permettre de déployer vos automatisations dans votre organisation :

|Objectif  |Policy  |ID de stratégie  |
|---------|---------|---------|
|Automatisation du flux de travail pour les alertes de sécurité|[Déployer l’automatisation de workflow pour les alertes Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatisation du flux de travail pour les recommandations de sécurité|[Déployer l’automatisation de workflow pour les recommandations Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Automatisation des workflows pour les modifications de conformité réglementaire|[Déployer l’automatisation des workflows pour la conformité réglementaire Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Il existe deux mises à jour des fonctionnalités de ces stratégies :

- Quand elles sont affectées, leur activation reste appliquée.
- Vous pouvez maintenant personnaliser ces stratégies et mettre à jour les paramètres, même s’ils ont déjà été déployés. Par exemple, si un utilisateur veut ajouter une autre clé d’évaluation ou modifier une clé d’évaluation existante, il peut le faire.

Prise en main des [modèles d’automatisation de flux de travail](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

En savoir plus sur la façon d’[Automatiser les réponses aux déclencheurs Security Center](workflow-automation.md).


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Deux recommandations héritées n’écrivent plus de données directement dans le journal d’activité Azure 

Security Center transmet les données pour presque toutes les recommandations de sécurité à Azure Advisor qui, à son tour, les écrit dans le [Journal d’activité Azure](../azure-monitor/essentials/activity-log.md).

Pour deux recommandations, les données sont écrites simultanément et directement dans le journal d’activité Azure. Avec cette modification, Security Center cesse d’écrire des données pour ces recommandations de sécurité héritées directement dans le journal d’activité. Au lieu de cela, nous exportons les données vers Azure Advisor, comme c’est le cas pour toutes les autres recommandations.

Les deux recommandations héritées sont les suivantes :
-  Les problèmes d’intégrité de la protection du point de terminaison doivent être résolus sur vos machines
- Les vulnérabilités de la configuration de sécurité sur vos machines doivent être corrigées

Si vous accédiez à des informations pour ces deux recommandations dans la catégorie « Recommandation de type TaskDiscovery » du journal d’activité, ces informations ne sont plus disponibles.


### <a name="recommendations-page-enhancements"></a>Améliorations de la page Suggestions 

Nous avons publié une version améliorée de la liste des suggestions pour présenter plus d’informations en un clin d’œil.

Sur cette page, vous verrez désormais :

1. Score maximal et score actuel pour chaque contrôle de sécurité.
1. Icônes qui remplaçant des balises telles que **Corriger** et **Version préliminaire**.
1. Une nouvelle colonne présentant l'[initiative de stratégie](security-policy-concept.md) associée à chaque suggestion ; visible lorsque « Regrouper par contrôles » est désactivé.

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Améliorations apportées à la page Suggestions d’Azure Security Center - mars 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Améliorations apportées à la liste « plate » Suggestions d’Azure Security Center - mars 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

Apprenez-en davantage dans [Recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).


## <a name="february-2021"></a>Février 2021

Les mises à jour de février sont les suivantes :

- [Nouvelle page d’alertes de sécurité dans le portail Azure en disponibilité générale (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Les recommandations en matière de protection des charges de travail Kubernetes sont en disponibilité générale](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [L’intégration de Microsoft Defender pour point de terminaison à Azure Defender prend désormais en charge Windows Server 2019 et Windows 10 Virtual Desktop (WVD) (en préversion)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Lien direct vers la stratégie dans la page des détails de la recommandation](#direct-link-to-policy-from-recommendation-details-page)
- [La recommandation de classification des données SQL n’a plus d’incidence sur votre niveau de sécurité](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Les automatisations de workflow peuvent être déclenchées par des modifications apportées aux évaluations de conformité réglementaire (en préversion)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Améliorations de la page d’inventaire des ressources](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Nouvelle page d’alertes de sécurité dans le portail Azure en disponibilité générale (GA)

La page des alertes de sécurité d’Azure Security Center a été repensée pour fournir les éléments suivants :

- **Expérience de triage améliorée des alertes** – favorise la réduction d’un trop grand nombre d’alertes et permet de se concentrer plus facilement sur les menaces les plus pertinentes. La liste comprend des filtres personnalisables et des options de regroupement.
- **Plus d’informations dans la liste des alertes** – telles que les tactiques MITRE ATT&ACK.
- **Bouton pour créer des exemples d’alerte** - pour évaluer les fonctionnalités d’Azure Defender et tester la configuration de vos alertes (pour l’intégration SIEM, les notifications par e-mail et les automatisations de workflow), vous pouvez créer des exemples d’alertes à partir de tous les plans Azure Defender.
- **Alignement avec l’expérience d’incident d’Azure Sentinel** – pour les clients qui utilisent les deux produits, il est maintenant plus simple de passer de l’un à l’autre et il est facile d’apprendre l’un de l’autre.
- **Amélioration des performances** pour les grandes listes d’alertes.
- **Navigation au clavier** dans la liste des alertes.
- **Alertes à partir d’Azure Resource Graph** – vous pouvez effectuer des requêtes sur des alertes dans Azure Resource Graph, l’API de type Kusto pour toutes vos ressources. Cela est également utile si vous créez vos propres tableaux de bord d’alertes. [Apprenez-en davantage sur Azure Resource Graph](../governance/resource-graph/index.yml).
- **Créer une fonctionnalité d’exemples d’alerte** - Pour créer des exemples d’alertes à partir de la nouvelle expérience Alertes, consultez [Générer des exemples d’alertes Azure Defender](security-center-alert-validation.md#generate-sample-azure-defender-alerts).

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Liste d’alertes de sécurité d’Azure Security Center":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Les recommandations en matière de protection des charges de travail Kubernetes sont en disponibilité générale

Nous sommes heureux d’annoncer la disponibilité générale de l’ensemble des recommandations prévues pour la protection des charges de travail Kubernetes.

Afin de garantir la sécurisation par défaut des charges de travail Kubernetes, Security Center a ajouté des recommandations de renforcement au niveau de Kubernetes, y compris des options de mise en œuvre avec le contrôle d’admission Kubernetes.

Lorsque le module complémentaire Azure Policy pour Kubernetes est installé sur votre cluster AKS (Azure Kubernetes Service), toutes les requêtes adressées au serveur d’API Kubernetes sont analysées par rapport à l’ensemble prédéfini de bonnes pratiques (affiché sous la forme de 13 recommandations de sécurité) avant d’être conservées sur le cluster. Vous pouvez ensuite configurer la mise en œuvre des meilleures pratiques et les imposer aux charges de travail futures.

Par exemple, vous pouvez interdire la création de conteneurs privilégiés, et faire en sorte que toutes les demandes ultérieures soient bloquées.

Consultez [Meilleures pratiques de protection de charge de travail à l’aide du contrôle d’admission Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control) pour en savoir plus.

> [!NOTE]
> Lorsque les recommandations étaient en préversion, elles ne portaient pas atteinte à l’intégrité des ressources de cluster AKS, et n’étaient pas incluses dans les calculs de votre niveau de sécurité. Avec l’annonce de cette disponibilité générale, elles seront désormais intégrées dans le calcul du niveau. Si vous ne les avez pas encore corrigé, cela peut avoir un léger impact sur votre niveau de sécurité. Mettez-les en place dans la mesure du possible, comme indiqué dans [Appliquer les recommandations d’Azure Security Center](security-center-remediate-recommendations.md).


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>L’intégration de Microsoft Defender pour point de terminaison à Azure Defender prend désormais en charge Windows Server 2019 et Windows 10 Virtual Desktop (WVD) (en préversion)

Microsoft Defender for Endpoint est une solution holistique de sécurité des points de terminaison dans le cloud. Il fournit une gestion et une évaluation des vulnérabilités basées sur les risques ainsi que la détection et la réponse des points de terminaison (EDR). Pour obtenir la liste complète des avantages de l’utilisation de Defender pour point de terminaison avec Azure Security Center, consultez [Protéger vos points de terminaison avec la solution EDR intégrée de Security Center : Microsoft Defender pour point de terminaison](security-center-wdatp.md).

Quand vous activez Azure Defender pour les serveurs sur un serveur Windows, une licence pour Defender pour point de terminaison est incluse dans le plan. Si vous avez déjà activé Azure Defender pour les serveurs et que vous avez des serveurs Windows 2019 dans votre abonnement, ils recevront automatiquement Defender pour point de terminaison avec cette mise à jour. Aucune action manuelle n’est nécessaire. 

La prise en charge a été étendue de façon à inclure Windows Server 2019 et [Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md).

> [!NOTE]
> Si vous activez Defender pour point de terminaison sur une machine Windows Server 2019, vérifiez qu’elle répond aux prérequis décrits dans [Activer l’intégration de Microsoft Defender pour point de terminaison](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration).

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Lien direct vers la stratégie dans la page des détails de la recommandation

Lorsque vous examinez les détails d’une recommandation, il est souvent utile de pouvoir consulter la stratégie sous-jacente. Pour chaque recommandation prise en charge par une stratégie, il existe un nouveau lien dans la page des détails de la recommandation :

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Lien vers la page Azure Policy de la stratégie spécifique prenant en charge une recommandation.":::

Utilisez ce lien pour afficher la définition de stratégie et passer en revue la logique d’évaluation. 

Si vous examinez la liste des recommandations de notre [Guide de référence des recommandations de sécurité](recommendations-reference.md), vous remarquerez également des liens vers les pages de définition de stratégie :

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Accès à la page Azure Policy d’une stratégie particulière, directement à partir de la page de référence des recommandations d’Azure Security Center." lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>La recommandation de classification des données SQL n’a plus d’incidence sur votre niveau de sécurité
La recommandation **Les données sensibles de vos bases de données SQL doivent être classifiées** n’a plus d’incidence sur votre niveau de sécurité. S’agissant de la seule recommandation présente dans le contrôle de sécurité **Appliquer la classification des données**, le contrôle affiche désormais 0 comme valeur de niveau de sécurité.

Pour obtenir la liste complète de tous les contrôles de sécurité dans Security Center ainsi que leurs scores et la liste des recommandations de chacune d’eux, consultez [Contrôles de sécurité et leurs recommandations](secure-score-security-controls.md#security-controls-and-their-recommendations).

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>Les automatisations de workflow peuvent être déclenchées par des modifications apportées aux évaluations de conformité réglementaire (en préversion)
Nous avons ajouté un troisième type de données aux options de déclencheur de vos automatisations de workflow : les modifications apportées aux évaluations de conformité réglementaire.

Découvrez comment utiliser les outils d’automatisation des workflows dans [Automatiser les réponses aux déclencheurs Security Center](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Utilisation des modifications apportées aux évaluations de conformité réglementaire pour déclencher une automatisation de workflow." lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Améliorations de la page d’inventaire des ressources
La page inventaire d’inventaire des ressources de Security Center a été améliorée comme suit :

- Les récapitulatifs en haut de la page incluent désormais les **abonnements non inscrits**, montrant le nombre d’abonnements pour lesquels Security Center n’est pas activé.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Nombre d’abonnements non inscrits dans les récapitulatifs en haut de la page d’inventaire des ressources.":::

- Des filtres ont été développés et améliorés pour inclure les éléments suivants :
    - **Totaux** - Chaque filtre présente le nombre de ressources qui répondent aux critères de chaque catégorie

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Totaux dans les filtres de la page d’inventaire des ressources d’Azure Security Center.":::

    - **Contient des filtres d’exemptions** (facultatif) - limitez les résultats aux ressources qui ont ou non des exemptions. Ce filtre n’apparaît pas par défaut, mais il est accessible à partir du bouton **Ajouter un filtre**.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Ajout du filtre « Contient des exemptions » dans la page d’inventaire des ressources d’Azure Security Center":::

En savoir plus sur la façon d’[Explorer et gérer vos ressources avec l’inventaire des ressources](asset-inventory.md).

## <a name="january-2021"></a>Janvier 2021

Les mises à jour de janvier sont les suivantes :

- [Le benchmark de sécurité Azure est désormais l’initiative de stratégie par défaut d’Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [L’évaluation des vulnérabilités pour les machines locales et multicloud est en disponibilité générale](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Le degré de sécurisation pour les groupes d’administration est désormais disponible en préversion](#secure-score-for-management-groups-is-now-available-in-preview)
- [L’API Degré de sécurisation est en disponibilité générale](#secure-score-api-is-released-for-general-availability-ga)
- [Protections DNS non résolues ajoutées à Azure Defender pour App Service](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Les connecteurs multiclouds sont en disponibilité générale](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Exempter des recommandations entières de votre degré de sécurisation pour les abonnements et les groupes d’administration](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Les utilisateurs peuvent désormais faire une demande de visibilité à l’échelle du locataire auprès de leur administrateur général](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [Ajout de 35 recommandations (préversion) pour mieux détailler le benchmark de sécurité Azure](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Exportation CSV de la liste filtrée de recommandations](#csv-export-of-filtered-list-of-recommendations)
- [Ressources « non applicables » désormais signalées comme « Conformes » dans les évaluations Azure Policy](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Exporter des captures instantanées hebdomadaires de données sur le niveau de sécurité et la conformité réglementaire avec l’exportation continue (préversion)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Le benchmark de sécurité Azure est désormais l’initiative de stratégie par défaut d’Azure Security Center

Le Benchmark de sécurité Azure est l’ensemble des directives propres à Azure et créées par Microsoft contenant les bonnes pratiques de sécurité et de conformité basées sur les infrastructures de conformité courantes. Ce benchmark, largement respecté et centré sur le cloud, est basé sur les contrôles du [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) et du [NIST (National Institute of Standards and Technology)](https://www.nist.gov/).

Au cours des derniers mois, la liste des recommandations de sécurité intégrée Security Center a été considérablement allongée en vue d’étendre la couverture de ce benchmark.

À compter de cette version, ce benchmark constitue la base des recommandations Security Center. En outre, il est entièrement intégré en tant qu’initiative de stratégie par défaut. 

La documentation de chacun des services Azure comprend une page consacrée à la base de référence de sécurité. Par exemple, [voici une base de référence Security Center](security-baseline.md). Ces bases de référence s’appuient sur le benchmark de sécurité Azure.

Si vous utilisez le tableau de bord de conformité réglementaire de Security Center, vous verrez deux instances du benchmark pendant une période de transition :

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Tableau de bord de conformité réglementaire Azure Security Center affichant le benchmark Azure Security":::

Les recommandations existantes ne sont pas affectées, et les modifications sont automatiquement reflétées dans Security Center à mesure que le benchmark se développe. 

Pour en savoir plus, consultez les pages suivantes :

- [En savoir plus sur le Benchmark de sécurité Azure](/security/benchmark/azure/introduction)
- [Personnaliser l’ensemble de normes du tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>L’évaluation des vulnérabilités pour les machines locales et multicloud est en disponibilité générale

En octobre, nous avions annoncé la préversion de l’analyse des serveurs Azure Arc à l’aide de l’analyseur d’évaluation des vulnérabilités intégré à [Azure Defender pour les serveurs](defender-for-servers-introduction.md) (avec Qualys).

Celle-ci est maintenant en disponibilité générale.

Une fois que vous avez activé Azure Arc sur vos machines non-Azure, Security Center propose de déployer l’analyseur de vulnérabilité intégré dessus, manuellement et à grande échelle.

Avec cette mise à jour, vous pouvez libérer la puissance d’**Azure Defender pour les serveurs** afin de consolider votre programme de gestion des vulnérabilités dans l’ensemble de vos ressources Azure et non-Azure.

Principales fonctionnalités :

- Supervision de l’état de provisionnement de l’analyseur d’évaluation des vulnérabilités sur les machines Azure Arc
- Provisionnement de l’agent d’évaluation des vulnérabilités intégré sur des machines Windows Azure Arc Windows et Linux non protégées (manuellement et à grande échelle)
- Réception et analyse des vulnérabilités détectées par les agents déployés (manuellement et à grande échelle)
- Expérience unifiée pour les machines virtuelles Azure et Azure Arc

[Découvrez-en plus sur le déploiement de l’analyseur de vulnérabilité intégré sur vos machines hybrides](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Découvrez-en plus sur les serveurs avec Azure Arc](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Le degré de sécurisation pour les groupes d’administration est maintenant disponible en préversion

La page Degré de sécurisation affiche désormais les degrés de sécurisation agrégés de vos groupes d’administration, en plus du niveau d’abonnement. Vous pouvez maintenant voir la liste des groupes d’administration de votre organisation, ainsi que le degré de sécurisation de chaque groupe d’administration.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Affichage des degrés de sécurisation de vos groupes d’administration.":::

En savoir plus sur [le degré de sécurisation et les contrôles de sécurité dans Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>L’API Degré de sécurisation est en disponibilité générale

Vous pouvez désormais accéder à votre degré de sécurisation par le biais de l’[API Degré de sécurisation](/rest/api/securitycenter/securescores/). Les méthodes de l’API offrent la flexibilité nécessaire pour interroger les données et créer votre propre mécanisme de création de rapports sur vos degrés de sécurisation au fil du temps. Par exemple :

- Utiliser l’API **Niveau de sécurité** pour obtenir le niveau d’un abonnement spécifique.
- Utiliser l’API **Contrôles du niveau de sécurité** pour lister les contrôles de sécurité et le niveau actuel de vos abonnements.

Découvrez des outils externes accessibles avec l’API Niveau de sécurité dans [la zone consacrée au niveau de sécurité de notre communauté GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

En savoir plus sur [le degré de sécurisation et les contrôles de sécurité dans Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Protections DNS non résolues ajoutées à Azure Defender pour App Service

La prise de contrôle des sous-domaines constitue une menace grave et courante pour les organisations. Une prise de contrôle de sous-domaine peut se produire quand un enregistrement DNS pointe vers un site web déprovisionné. Ces enregistrements DNS sont également appelés entrées « DNS non résolues ». Les enregistrements CNAME sont particulièrement vulnérables à cette menace. 

Les prises de contrôle de sous-domaines permettent aux acteurs des menaces de rediriger le trafic destiné au domaine d’une organisation vers un site effectuant des activités malveillantes.

Azure Defender pour App Service détecte désormais les entrées DNS non résolues lorsqu’un site web App Service est désactivé. C’est à ce moment-là que l’entrée DNS pointe vers une ressource inexistante et que votre site web devient vulnérable à la prise de contrôle de sous-domaine. Ces protections sont disponibles pour les domaines gérés par Azure DNS et pour ceux gérés par un bureau d’enregistrement de domaines externes. En outre, elles s’appliquent aussi bien à App Service sur Windows qu’à App Service sur Linux.

En savoir plus :

- [Table de référence des alertes App Service](alerts-reference.md#alerts-azureappserv) : comprend deux nouvelles alertes Azure Defender qui se déclenchent lorsqu’une entrée DNS non résolue est détectée
- [Empêcher les entrées DNS non résolues et la prise de contrôle des sous-domaines](../security/fundamentals/subdomain-takeover.md) : découvrez la menace que constitue la prise de sous-domaine et les entrées DNS non résolues
- [Présentation d’Azure Defender pour App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Les connecteurs multiclouds sont en disponibilité générale

Les charges de travail cloud couvrant généralement plusieurs plates-formes cloud, les services de sécurité cloud se doivent d’en faire de même.

Azure Security Center protège les charges de travail dans Azure, Amazon Web Services (AWS) et Google Cloud Platform (GCP).

Lorsque vous connectez vos comptes AWS ou GCP, leurs outils de sécurité natifs comme AWS Security Hub ou GCP Security Command Center sont intégrés à Azure Security Center.

Cette fonctionnalité Security Center fournit une visibilité et une protection pour l’ensemble des environnements cloud principaux. Voici quelques-uns des avantages de cette intégration :

- Provisionnement automatique des agents - Security Center utilise Azure Arc pour déployer l’agent Log Analytics sur vos instances AWS
- Gestion des stratégies
- Gestion des vulnérabilités
- Détection de point de terminaison et réponse incorporée (EDR)
- Détection des erreurs de configuration de sécurité
- Vue montrant les recommandations de sécurité de tous les fournisseurs de cloud
- Incorporer toutes vos ressources dans les calculs du degré de sécurisation de Security Center
- Évaluations de conformité réglementaire de vos ressources AWS et GCP

Dans le menu Security Center, sélectionnez **Connecteurs multiclouds** pour afficher les options permettant de créer des connecteurs :

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Ajouter un compte AWS sur la page connecteurs multicloud de Security Center":::

Pour en savoir plus :
- [Connecter vos comptes AWS à Azure Security Center](quickstart-onboard-aws.md)
- [Connectez vos comptes GCP à Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Exempter des recommandations entières de votre degré de sécurisation pour les abonnements et les groupes d’administration

Nous sommes en train d’étendre la fonctionnalité d’exemption afin de pouvoir y inclure des recommandations entières. Pour cela, nous ajoutons des options permettant d’affiner les recommandations de sécurité que Security Center fournit concernant vos abonnements, vos groupes d’administration ou vos ressources.

Il arrive parfois qu’une ressource soit signalée comme non saine alors que le problème a été résolu par un outil tiers, ce que Security Center n’a pas détecté. D’autres fois, il arrive qu’une recommandation s’affiche dans une étendue à laquelle elle n’est pas censée appartenir. La recommandation peut ne pas convenir à un abonnement. Ou encore, il est possible que votre organisation ait décidé d’accepter les risques liés à une ressource ou à une recommandation précise.

Avec cette fonctionnalité en préversion, vous pouvez désormais créer une exemption pour une recommandation dans les buts suivants :

- **Exempter une ressource** pour qu’elle ne soit plus listée parmi les ressources non saines et n’impacte pas votre degré de sécurisation. La ressource sera listée comme non applicable, avec le motif « exemptée » et la justification de votre choix.

- **Exempter un abonnement ou un groupe d’administration** pour que la recommandation n’impacte pas votre degré de sécurisation et ne s’affiche plus pour l’abonnement ou le groupe d’administration. Cela concerne les ressources existantes et futures. La recommandation sera signalée avec la justification que vous avez choisie pour l’étendue sélectionnée.

Pour plus d’informations, consultez [Exempter une ressource des recommandations et du degré de sécurisation](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Les utilisateurs peuvent désormais faire une demande de visibilité à l’échelle du locataire auprès de leur administrateur général

Si un utilisateur ne dispose pas des autorisations pour voir les données Security Center, il verra un lien lui permettant de demander des autorisations à l’administrateur général de son organisation. Sa demande doit comprendre le rôle dont il souhaite les autorisations, ainsi que la raison pour laquelle il en a besoin.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Bannière informant l’utilisateur qu’il peut demander des autorisations à l’échelle du locataire.":::

Pour plus d’informations, consultez [Demander des autorisations à l’échelle du locataire quand les vôtres sont insuffisantes](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient).


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Ajout de 35 recommandations (préversion) pour mieux détailler le benchmark de sécurité Azure

Le [Benchmark de sécurité Azure](/security/benchmark/azure/introduction) est désormais l’initiative de stratégie par défaut d’Azure Security Center. 

Pour étendre la couverture de ce benchmark, les 35 recommandations suivantes (en préversion) ont été ajoutées à Security Center.

> [!TIP]
> Les recommandations en préversion ne rendent pas une ressource non saine et ne sont pas incluses dans les calculs de votre degré de sécurisation. Corrigez-les là où c’est possible, de sorte que quand la période de préversion se termine, elles soient prises en compte dans le calcul de votre degré de sécurisation. Découvrez comment répondre à ces recommandations dans [Corriger les recommandations dans Azure Security Center](security-center-remediate-recommendations.md).

| Contrôle de sécurité                     | Nouvelles recommandations                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Activer le chiffrement des données au repos            | - Les comptes Azure Cosmos DB doivent utiliser des clés gérées par le client pour chiffrer les données au repos<br>- Les espaces de travail Azure Machine Learning doivent être chiffrés avec une clé gérée par le client (CMK)<br>- La protection des données BYOK (Bring Your Own Key) doit être activée pour les serveurs MySQL<br>- La protection des données BYOK (Bring Your Own Key) doit être activée pour les serveurs PostgreSQL<br>- Les comptes Cognitive Services doivent activer le chiffrement des données avec une clé gérée par le client (CMK)<br>- Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)<br>- Les instances managées SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos<br>- Les serveurs SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos<br>- Les comptes de stockage doivent utiliser une clé gérée par le client (CMK) pour le chiffrement                                                                                                                                                              |
| Implémenter les bonnes pratiques de sécurité    | - Les abonnements doivent avoir l’adresse e-mail d’un contact pour le signalement des problèmes de sécurité<br> - Le provisionnement automatique de l’agent Log Analytics doit être activé sur votre abonnement<br> - La notification par e-mail pour les alertes à gravité élevée doit être activée<br> - La notification par e-mail au propriétaire de l’abonnement pour les alertes à gravité élevée doit être activée<br> - La protection contre la suppression définitive doit être activée sur les coffres de clés<br> - La suppression réversible doit être activée sur les coffres de clés |
| Gérer l’accès et les autorisations        | - Les applications de fonction doivent avoir l’option « Certificats clients (certificats clients entrants) » activée |
| Protéger les applications contre les attaques DDoS | - Le pare-feu d’applications web (WAF) doit être activé pour Application Gateway<br> - Web Application Firewall (WAF) doit être activé pour le service Azure Front Door Service |
| Restreindre l’accès réseau non autorisé | - Le pare-feu doit être activé sur Key Vault<br> - Le point de terminaison privé doit être configuré pour Key Vault<br> - App Configuration doit utiliser une liaison privée<br> - Azure Cache pour Redis doit se trouver dans un réseau virtuel<br> - Les domaines Azure Event Grid doivent utiliser une liaison privée<br> - Les rubriques Azure Event Grid doivent utiliser une liaison privée<br> - Les espaces de travail Azure Machine Learning doivent utiliser une liaison privée<br> - Azure SignalR Service doit utiliser une liaison privée<br> - Azure Spring Cloud doit utiliser l’injection de réseau<br> - Les registres de conteneurs ne doivent pas autoriser un accès réseau sans restriction<br> - Les registres de conteneurs doivent utiliser une liaison privée<br> - L’accès au réseau public doit être désactivé pour les serveurs MariaDB<br> - L’accès au réseau public doit être désactivé pour les serveurs MySQL<br> - L’accès au réseau public doit être désactivé pour les serveurs PostgreSQL<br> - Le compte de stockage doit utiliser une connexion de liaison privée<br> - Les comptes de stockage doivent utiliser des règles de réseau virtuel pour restreindre l’accès au réseau<br> - Les modèles VM Image Builder doivent utiliser une liaison privée|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Liens connexes :

- [En savoir plus sur le Benchmark de sécurité Azure](/security/benchmark/azure/introduction)
- [En savoir plus sur Azure Database for MariaDB](../mariadb/overview.md)
- [En savoir plus sur Azure Database pour MySQL](../mysql/overview.md)
- [En savoir plus sur Azure Database pour PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>Exportation CSV de la liste filtrée de recommandations 

En novembre 2020, nous avons ajouté des filtres à la page Recommandations ([La liste des recommandations comprend désormais des filtres](release-notes-archive.md#recommendations-list-now-includes-filters)). En décembre, nous avons étendu ces filtres ([La page Recommandations contient de nouveaux filtres pour l’environnement, la gravité et les réponses disponibles](release-notes-archive.md#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Avec cette annonce, nous changeons le comportement du bouton **Télécharger au format CSV** afin que l’exportation CSV inclue uniquement les recommandations actuellement affichées dans la liste filtrée. 

Par exemple, dans l’image ci-dessous vous pouvez constater que la liste a été filtrée sur deux recommandations. Le fichier CSV généré comprend les détails de l’état de chaque ressource affectée par ces deux recommandations.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Exportation de recommandations filtrées vers un fichier CSV.":::

Apprenez-en davantage dans [Recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Ressources « non applicables » désormais signalées comme « Conformes » dans les évaluations Azure Policy

Avant, les ressources qui étaient évaluées pour une recommandation et considérées comme **non applicables** apparaissaient dans Azure Policy comme étant « Non conformes ». Aucune action de l’utilisateur ne pouvait les faire passer à l’état « Conforme ». Depuis ce changement, ces ressources s’affichent comme étant « Conformes » par souci de clarté.

La seule incidence sera observée dans Azure Policy, où le nombre de ressources compatibles augmentera. Il n’y aura aucune incidence sur votre degré de sécurisation dans Azure Security Center.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Exporter des captures instantanées hebdomadaires de données sur le niveau de sécurité et la conformité réglementaire avec l’exportation continue (préversion)

Nous avons ajouté une nouvelle fonctionnalité en préversion aux outils d’[exportation continue](continuous-export.md) pour l’exportation des captures instantanées hebdomadaires de données sur le niveau de sécurité et la conformité réglementaire.

Quand vous définissez une exportation continue, définissez la fréquence d’exportation :

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Choix de la fréquence de l’exportation continue.":::

- **Streaming** : les évaluations sont envoyées quand l’état d’intégrité d’une ressource est mis à jour (si aucune mise à jour n’est effectuée, aucune donnée n’est envoyée).
- **Captures instantanées** : une capture instantanée de l’état actuel de toutes les évaluations de conformité réglementaire est envoyée chaque semaine (il s’agit d’une fonctionnalité en préversion pour les captures instantanées hebdomadaires de données sur le niveau de sécurité et la conformité réglementaire).

Pour plus d’informations sur toutes les possibilités de cette fonctionnalité, consultez [Exporter en continu des données Security Center](continuous-export.md).