---
title: Expériences de la gestion multilocataire
description: La gestion des ressources déléguées Azure offre une expérience de gestion inter-locataires.
ms.date: 01/06/2020
ms.topic: conceptual
ms.openlocfilehash: 661ada4e5d620d4977290986f5649b91363202fe
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75706986"
---
# <a name="cross-tenant-management-experiences"></a>Expériences de la gestion multilocataire

En tant que fournisseur de services, vous pouvez utiliser la [gestion des ressources déléguées Azure](../concepts/azure-delegated-resource-management.md) pour gérer des ressources Azure pour plusieurs clients à partir de votre propre locataire dans le [portail Azure](https://portal.azure.com). La plupart des tâches et des services peuvent être exécutés sur des ressources Azure déléguées sur des locataires gérés. Cet article décrit quelques-uns des scénarios améliorés dans lesquels la gestion des ressources déléguées Azure peut être efficace.

> [!NOTE]
> La gestion des ressources déléguées Azure peut également être utilisée [au sein d’une entreprise qui dispose de plusieurs locataires Azure AD](enterprise.md) pour simplifier l’administration entre locataires.

## <a name="understanding-customer-tenants"></a>Compréhension des locataires du client

Dans Azure Active Directory (Azure AD), un locataire est une représentation d’une organisation. Il s’agit d’une instance dédiée d’Azure AD qu’une organisation reçoit quand elle crée une relation avec Microsoft en s’inscrivant à Azure, à Microsoft 365 ou à d’autres services. Chaque locataire Azure AD est distinct et indépendant des autres, et a son propre ID de locataire (GUID). Pour plus d’informations, voir [Qu’est-ce qu’Azure Active Directory ?](../../active-directory/fundamentals/active-directory-whatis.md)

En règle générale, pour gérer des ressources Azure pour un client, les fournisseurs de services doivent se connecter au portail Azure à l’aide d’un compte associé au locataire de ce client, ce qui requiert qu’un administrateur dans le locataire du client crée et gère des comptes d’utilisateur pour le fournisseur de services.

Avec la gestion des ressources déléguées Azure, le processus d’intégration spécifie les utilisateurs au sein du locataire du fournisseur de services qui pourront accéder aux abonnements, aux groupes de ressources et aux ressources dans le locataire du client, ainsi que les gérer. Ces utilisateurs peuvent ensuite se connecter au portail Azure en utilisant leurs propres informations d’identification. Dans le portail Azure, ils peuvent gérer les ressources appartenant à tous les clients auxquels ils ont accès. Pour ce faire, ils peuvent accéder à la page [Mes clients](../how-to/view-manage-customers.md) du portail Azure ou travailler directement dans le contexte de l’abonnement de ce client, soit sur le portail Azure ou via des API.

La gestion des ressources déléguées Azure offre davantage de flexibilité pour gérer les ressources de plusieurs clients sans devoir se connecter aux différents comptes dans les différents locataires. Par exemple, un fournisseur de services peut avoir trois clients, avec des responsabilités et des niveaux d’accès différents, comme illustré ici :

![Trois locataires du client présentant les responsabilités du fournisseur de services](../media/azure-delegated-resource-management-customer-tenants.jpg)

À l’aide de la gestion des ressources déléguées Azure, les utilisateurs autorisés peuvent se connecter au locataire du fournisseur de services pour accéder à ces ressources, comme illustré ici :

![Ressources du client gérées via un locataire du fournisseur de services](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Prise en charge des API et de l’outil de gestion

Vous pouvez effectuer des tâches de gestion sur les ressources déléguées directement sur le portail ou à l’aide d’API et d’outils de gestion (tels que Azure CLI et Azure PowerShell). Toutes les API existantes peuvent être utilisées lorsque vous travaillez avec des ressources déléguées, tant que la fonctionnalité est prise en charge pour la gestion entre inter-locataires et que l’utilisateur dispose des autorisations appropriées.

Nous fournissons également des API pour effectuer des tâches de gestion des ressources déléguées Azure. Pour plus d’informations, voir la section **Référence**.

## <a name="enhanced-services-and-scenarios"></a>Services et scénarios améliorés

La plupart des tâches et des services peuvent être exécutés sur des ressources déléguées sur des locataires gérés. Voici quelques-uns des principaux scénarios où la gestion inter-locataires peut être efficace.

[Azure Arc pour les serveurs (préversion)](../../azure-arc/servers/overview.md) :

- [Connecter des machines Windows Server ou Linux en dehors d’Azure](../../azure-arc/servers/quickstart-onboard-portal.md) à des abonnements et/ou à des groupes de ressources délégués dans Azure
- Gérer des machines connectées à l’aide de constructions Azure, comme Azure Policy et le marquage

[Azure Automation](../../automation/index.yml) :

- Utiliser des comptes Automation pour accéder à des ressources du client déléguées et les utiliser

[Sauvegarde Azure](../../backup/index.yml) :

- Sauvegarder et restaurer des données client dans des locataires du client

[Azure Kubernetes Service (AKS)](../../aks/index.yml) :

- Gérer des environnements Kubernetes hébergés, ainsi que déployer et gérer des applications en conteneur au sein des locataires du client

[Azure Monitor](../../azure-monitor/index.yml) :

- Afficher les alertes pour les abonnements délégués, avec la possibilité d’afficher des alertes à travers tous les abonnements
- Afficher les détails du journal d’activité pour des abonnements délégués
- Log Analytics : interroger des données à partir d’espaces de travail du clients distants dans plusieurs locataires
- Créer des alertes dans les locataires du client qui déclenchent une automatisation, par exemple des runbooks Azure Automation ou des fonctions Azure, dans le locataire du fournisseur de services par le biais de webhooks

[Azure Policy](../../governance/policy/index.yml) :

- Les instantanés de conformité affichent les détails sur les stratégies attribuées au sein d’abonnements délégués
- Créer et modifier des définitions de stratégie au sein d’un abonnement délégué
- Affecter des définitions de stratégie définies par le client au sein de l’abonnement délégué
- Les clients voient les stratégies créées par le fournisseur de services en même temps que les stratégies qu’ils ont créées eux-mêmes
- Peut [corriger deployIfNotExists ou modifier des affectations au sein du locataire du client](../how-to/deploy-policy-remediation.md)

[Azure Resource Graph](../../governance/resource-graph/index.yml) :

- Inclut désormais l’ID de locataire dans les résultats de requête renvoyés, ce qui vous permet d’identifier si un abonnement appartient au locataire du client ou au locataire du fournisseur de services

[Azure Security Center](../../security-center/index.yml) :

- Visibilité inter-locataire
  - Surveiller la conformité aux stratégies de sécurité et garantir la couverture de sécurité des ressources de tous les locataires
  - Surveillance continue de la conformité réglementaire de plusieurs clients dans une seule vue
  - Surveiller, trier et hiérarchiser les recommandations de sécurité actionnables avec un calcul du degré de sécurisation
- Gestion d’état de la sécurité inter-locataire
  - Gérer les stratégies de sécurité
  - Agir sur des ressources non conformes à l’aide de recommandations de sécurité actionnables
  - Collecter et stocker des données liées à la sécurité
- Détection et traitement des menaces inter-locataires
  - Détecter des menaces pesant sur des ressources inter-locataires
  - Appliquer des contrôles de protection avancée contre les menaces tels que l’accès à la machine virtuelle juste-à-temps (JIT)
  - Renforcer la configuration du groupe de sécurité réseau avec le renforcement du réseau adaptatif
  - S’assurer que les serveurs exécutent uniquement les applications et processus qu’ils doivent exécuter avec des contrôles d’application adaptatifs
  - Surveiller les modifications apportées aux fichiers et aux entrées de Registre importants avec le monitoring d’intégrité de fichier

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md) :

- Gérer les ressources Azure Sentinel [dans les locataires clients](../../sentinel/multiple-tenants-service-providers.md)
- [Suivre les attaques et afficher les alertes de sécurité sur plusieurs locataires clients](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Azure Service Health](../../service-health/index.yml) :

- Surveiller l’intégrité des ressources du client avec Azure Resource Health
- Suivre l’intégrité des services Azure utilisés par les clients

[Azure Site Recovery](../../site-recovery/index.yml) :

- Gérer les options de récupération d’urgence pour les machines virtuelles Azure dans les locataires du client (notez que vous ne pouvez pas utiliser de comptes d’identification pour copier des extensions de machine virtuelle)

[Machines virtuelles Azure](../../virtual-machines/index.yml) :

- Utiliser des extensions de machine virtuelle pour accomplir des tâches d’automatisation et de configuration après déploiement sur des machines virtuelles Azure dans les locataires du client
- Utiliser des diagnostics de démarrage pour résoudre des problèmes de machines virtuelles Azure dans les locataires du client
- Accéder aux machines virtuelles avec une console série dans les locataires du client
- Notez que vous ne pouvez pas utiliser Azure Active Directory pour une ouverture de session à distance sur une machine virtuelle, ou intégrer une machine virtuelle avec Key Vault pour les mots de passe, les secrets ou les clés de chiffrement du disque.

[Réseau virtuel Azure](../../virtual-network/index.yml) :

- Déployer et gérer des réseaux virtuels et des cartes d’interface réseau virtuelles (cartes réseau virtuelles) au sein des locataires du client

Demandes de support :

- Ouvrir des demandes de support pour des ressources déléguées à partir du panneau **Aide + Support** dans le portail Azure (en sélectionnant le plan de support disponible pour l’étendue déléguée)

## <a name="current-limitations"></a>Limites actuelles
Dans tous les scénarios, gardez à l’esprit les limitations actuelles suivantes :

- Les demandes traitées par Azure Resource Manager peuvent être effectuées à l’aide de la gestion des ressources déléguées Azure. Les URI d’opération pour ces demandes commencent par `https://management.azure.com`. Toutefois, les demandes qui sont gérées par une instance d’un type de ressource (par exemple, accès aux secrets du coffre de clés ou accès aux données de stockage) ne sont pas prises en charge avec la gestion des ressources déléguées Azure. Les URI d’opération pour ces demandes commencent généralement par une adresse propre à votre instance, telle que `https://myaccount.blob.core.windows.net` ou `https://mykeyvault.vault.azure.net/`. Ces dernières sont également des opérations sur les données plutôt que des opérations de gestion. 
- Les attributions de rôles doivent utiliser des [rôles intégrés](../../role-based-access-control/built-in-roles.md) de contrôle d’accès en fonction du rôle (RBAC). Tous les rôles intégrés sont actuellement pris en charge avec la gestion des ressources déléguées Azure, à l’exception du propriétaire et des rôles intégrés avec l’autorisation [DataActions](../../role-based-access-control/role-definitions.md#dataactions). Le rôle Administrateur de l’accès utilisateur est pris en charge uniquement pour une utilisation limitée dans [l’affectation de rôles à des identités gérées](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Les rôles personnalisés et les [Rôles Administrateur classique de l’abonnement](../../role-based-access-control/classic-administrators.md) ne sont pas pris en charge.
- Actuellement, vous ne pouvez pas intégrer un abonnement (ou un groupe de ressources au sein d’un abonnement) pour la gestion des ressources déléguées Azure si l’abonnement utilise Azure Databricks. De même, si un abonnement a été inscrit pour une intégration avec le fournisseur de ressources **Microsoft.ManagedServices**, vous ne pouvez pas actuellement créer d’espace de travail Databricks pour cet abonnement.
- Bien que vous puissiez intégrer des abonnements et des groupes de ressources pour la gestion des ressources déléguées Azure qui ont des verrous de ressources, ces verrous n’empêchent pas les actions d’être effectuées par les utilisateurs dans le locataire gestionnaire. Les [affectations de refus](../../role-based-access-control/deny-assignments.md), qui protègent les ressources managées par le système, telles que celles créées par les applications managées Azure ou Azure Blueprints (affectations de refus émises par le système), empêchent les utilisateurs du locataire gestionnaire d’agir sur ces ressources. Toutefois, à ce moment-là, les utilisateurs du locataire client ne peuvent pas créer leurs propres affectations de refus (affectations de refus émises par l’utilisateur).

## <a name="next-steps"></a>Étapes suivantes

- Intégrez vos clients à la gestion des ressources déléguées Azure [en utilisant des modèles Azure Resource Manager](../how-to/onboard-customer.md) ou [en publiant une offre de services managés privés ou publics sur la Place de marché Azure](../how-to/publish-managed-services-offers.md).
- [Affichez et gérez les clients](../how-to/view-manage-customers.md) en accédant à **Mes clients** sur le portail Azure.
