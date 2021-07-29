---
title: 'Concepts : Accès et identité dans Azure Kubernetes Service (AKS)'
description: Découvrez l’accès et l’identité dans Azure Kubernetes Service (AKS), notamment l’intégration d’Azure Active Directory, le contrôle d’accès en fonction du rôle Kubernetes (RBAC Kubernetes) ainsi que les rôles et les liaisons.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 77b35d776b8fcd71f26278a6fda8a102113bd570
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109844967"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Options d’accès et d’identité pour Azure Kubernetes Service (AKS)

Vous pouvez authentifier, autoriser, sécuriser et contrôler l’accès aux clusters Kubernetes de différentes manières. 
* Avec le contrôle d’accès en fonction du rôle Kubernetes (RBAC Kubernetes), vous pouvez accorder à des utilisateurs, groupes et comptes de service l’accès aux seules ressources dont ils ont besoin. 
* À l’aide d’Azure Kubernetes Service (AKS), vous pouvez améliorer la structure de sécurité et d’autorisations en utilisant Azure Active Directory et Azure RBAC. 

Kubernetes RBAC et AKS vous aident à sécuriser l’accès à votre cluster et fournissent uniquement les autorisations minimales requises aux développeurs et aux opérateurs.

Cet article présente les principaux concepts permettant de vous authentifier et d’affecter des autorisations dans AKS.

## <a name="aks-service-permissions"></a>Autorisations de service AKS

Lors de la création d’un cluster, AKS génère ou modifie les ressources dont il a besoin (telles que des machines virtuelles et des cartes réseau) pour créer et exécuter le cluster pour le compte de l’utilisateur. Cette identité est distincte de l’autorisation d’identité du cluster, qui est créée à la création du cluster.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Autorisations de l’identité qui crée et exploite le cluster

Les autorisations suivantes sont requises par l’identité qui crée et exécute le cluster.

| Autorisation | Motif |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Requise pour lire l’ID du jeu de chiffrement de disque. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Requise pour mettre à jour les groupes de placement de proximité. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Requises pour configurer des passerelles d’application et joindre le sous-réseau. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Requise pour configurer le groupe de sécurité réseau pour le sous-réseau lors de l’utilisation d’un réseau virtuel personnalisé.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Requises pour configurer les adresses IP publiques sortantes sur l’équilibreur de charge standard. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Requises pour créer et mettre à jour des espaces de travail Log Analytics et la supervision Azure pour les conteneurs. |

### <a name="aks-cluster-identity-permissions"></a>Autorisations de l’identité du cluster AKS

Les autorisations suivantes sont utilisées par l’identité du cluster AKS, qui est créée et associée au cluster AKS. Chaque autorisation est utilisée pour les raisons suivantes :

| Autorisation | Motif |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Obligatoire pour la création d’utilisateurs et le fonctionnement du cluster
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Requises pour configurer l’équilibreur de charge pour un service LoadBalancer. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Requises pour rechercher et configurer des IP publiques pour un service LoadBalancer. |
| `Microsoft.Network/publicIPAddresses/join/action` | Requise pour configurer des IP publiques pour un service LoadBalancer. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Requises pour créer ou supprimer des règles de sécurité pour un service LoadBalancer. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Requises pour configurer AzureDisks. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Requises pour configurer les comptes de stockage pour AzureFile ou AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Requises pour configurer des tables de routes et des routes pour les nœuds. |
| `Microsoft.Compute/virtualMachines/read` | Requise pour rechercher des informations pour les machines virtuelles dans un VMAS, telles que les zones, le domaine d’erreur, la taille et les disques de données. |
| `Microsoft.Compute/virtualMachines/write` | Requise pour attacher AzureDisks à une machine virtuelle dans un VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Requises pour rechercher des informations pour les machines virtuelles dans un groupe de machines virtuelles identiques, telles que les zones, le domaine d’erreur, la taille et les disques de données. |
| `Microsoft.Network/networkInterfaces/write` | Requise pour ajouter une machine virtuelle dans un VMAS à un pool d’adresses de back-ends d’équilibreur de charge. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Requise pour ajouter un groupe de machines virtuelles identiques à des pools d’adresses de back-ends d’un équilibreur de charge et effectuer un scale-out des nœuds dans un groupe de machines virtuelles identiques. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Requise pour attacher AzureDisks et ajouter une machine virtuelle à partir d’un groupe de machines virtuelles identiques à l’équilibreur de charge. |
| `Microsoft.Network/networkInterfaces/read` | Requise pour rechercher des IP internes et des pools d’adresses de back-ends d’équilibreur de charge pour les machines virtuelles dans un VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Requise pour rechercher des IP internes et des pools d’adresses de back-ends d’équilibreur de charge pour une machine virtuelle dans un groupe de machines virtuelles identiques. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Requise pour rechercher des IP publiques pour une machine virtuelle dans un groupe de machines virtuelles identiques. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Requises pour vérifier si un sous-réseau existe pour l’équilibreur de charge interne dans un autre groupe de ressources. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Requises pour configurer des instantanés pour AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Requises pour rechercher les tailles des machines virtuelles pour la recherche des limites de volume AzureDisk. |

### <a name="additional-cluster-identity-permissions"></a>Autorisations supplémentaires de l’identité du cluster

Lors de la création d’un cluster avec des attributs spécifiques, vous avez besoin des autorisations supplémentaires suivantes pour l’identité du cluster. Ces autorisations n’étant pas attribuées automatiquement, vous devez les ajouter à l’identité du cluster après sa création.

| Autorisation | Motif |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Requises si vous utilisez un groupe de sécurité réseau dans un autre groupe de ressources. Requises pour configurer des règles de sécurité pour un service LoadBalancer. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Requises si vous utilisez un sous-réseau dans un autre groupe de ressources, tel qu’un réseau virtuel personnalisé. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Requises si vous utilisez un sous-réseau associé à une table de routes dans un autre groupe de ressources, tel qu’un réseau virtuel personnalisé avec une table de routes personnalisée. Requises pour vérifier si un sous-réseau existe déjà pour le sous-réseau dans l’autre groupe de ressources. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Requise si vous utilisez un équilibreur de charge interne dans un autre groupe de ressources. Requise pour vérifier si un sous-réseau existe déjà pour l’équilibreur de charge interne dans le groupe de ressources. |
| `Microsoft.Network/privatednszones/*` | Requise si vous utilisez une zone DNS privée dans un autre groupe de ressources, tel qu’une privateDNSZone personnalisée. |

## <a name="kubernetes-rbac"></a>Contrôle RBAC Kubernetes

Le contrôle RBAC Kubernetes assure un filtrage granulaire des actions de l’utilisateur. Avec ce mécanisme de contrôle :
* Vous affectez à des utilisateurs ou à des groupes d’utilisateurs l’autorisation de créer et de modifier des ressources, ou d’afficher des journaux à partir de charges de travail d’applications en cours d’exécution. 
* Vous pouvez limiter la portée de ces autorisations à un seul espace de noms, ou les accorder à l’ensemble du cluster AKS. 
* Vous créez des *rôles* pour définir des autorisations, puis affectez ces rôles aux utilisateurs avec les *liaisons de rôle*.

Pour plus d’informations, consultez [Utilisation de l’autorisation RBAC Kubernetes][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Rôles et ClusterRoles

#### <a name="roles"></a>Rôles
Avant d’attribuer des autorisations aux utilisateurs avec RBAC Kubernetes, vous devez définir ces autorisations en tant que *rôle*. Accordez des autorisations dans un espace de noms à l’aide de rôles. 

> [!NOTE]
> Les rôles Kubernetes *accordent* des autorisations ; ils ne les *refusent* pas.

Pour accorder des autorisations sur l’ensemble du cluster ou sur des ressources de cluster en dehors d’un espace de noms donné, vous pouvez utiliser *ClusterRoles* à la place.

#### <a name="clusterroles"></a>ClusterRoles

Un ClusterRole accorde et applique des autorisations aux ressources sur l’ensemble du cluster, et non à un espace de noms spécifique.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings et ClusterRoleBindings

Une fois que vous avez défini les rôles pour accorder des autorisations aux ressources, vous affectez ces autorisations RBAC Kubernetes au moyen d’un *RoleBinding*. Si votre cluster AKS [s’intègre à Azure Active Directory (Azure AD)](#azure-ad-integration), les RoleBindings accordent des autorisations aux utilisateurs Azure AD pour effectuer des actions au sein du cluster. Découvrez comment en consultant [Contrôler l’accès aux ressources de cluster à l’aide du contrôle d’accès en fonction du rôle Kubernetes et des identités Azure Active Directory](azure-ad-rbac.md).

#### <a name="rolebindings"></a>RoleBindings

Assignez des rôles aux utilisateurs pour un espace de noms donné à l’aide de RoleBindings. Avec RoleBindings, vous pouvez séparer logiquement un cluster AKS unique, en autorisant uniquement les utilisateurs à accéder aux ressources d’application dans leur espace de noms attribué. 

Pour lier des rôles sur l’ensemble du cluster, ou sur des ressources de cluster en dehors d’un espace de noms donné, vous pouvez utiliser *ClusterRoleBindings* à la place.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

Avec un ClusterRoleBinding, vous liez des rôles à des utilisateurs et vous les appliquez aux ressources sur l’ensemble du cluster, et non à un espace de noms spécifique. Ce moyen vous permet d’accorder aux administrateurs ou aux techniciens du support technique l’accès à toutes les ressources dans le cluster AKS.


> [!NOTE]
> Microsoft/AKS prend toute mesure relative au cluster avec le consentement de l’utilisateur sous un rôle Kubernetes intégré `aks-service` et une liaison de rôle intégrée `aks-service-rolebinding`. 
> 
> Ce rôle permet à AKS de dépanner et de diagnostiquer les problèmes de cluster, mais ne peut pas modifier les autorisations, ni créer des rôles ou des liaisons de rôle, ni d’autres actions à privilèges élevés. L’accès aux rôles est activé uniquement sous les tickets de support actifs avec l’accès juste-à-temps (JIT). En savoir plus sur les stratégies de prise en charge [AKS](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Comptes de service Kubernetes

Les *comptes de service* sont un des principaux types d’utilisateur dans Kubernetes. L’API Kubernetes contient et gère les comptes de service. Les informations d’identification des comptes de service sont stockées en tant que secrets Kubernetes, ce qui leur permet d’être utilisées par les pods autorisés à communiquer avec le serveur d’API. La plupart des requêtes d’API fournissent un jeton d’authentification pour un compte de service ou un compte d’utilisateur normal.

Ces comptes d’utilisateur autorisent un accès plus classique pour les développeurs et les administrateurs humains, ils se ne limitent pas seulement aux services et aux processus. Bien que Kubernetes ne fournisse pas de solution de gestion des identités pour stocker les comptes et mots de passe des utilisateurs réguliers, vous pouvez intégrer des solutions d’identité externes à Kubernetes. Pour les clusters AKS, cette solution d’identité intégrée est Azure AD.

Pour plus d’informations sur les options d’identité dans Kubernetes, consultez [Authentification Kubernetes][kubernetes-authentication].

## <a name="azure-ad-integration"></a>Intégration Azure AD

Améliorez la sécurité de votre cluster AKS avec l’intégration d’Azure AD. S’appuyant sur des décennies de gestion des identités d’entreprise, Azure AD est un service multilocataire, basé sur le cloud pour la gestion des identités et des annuaires, qui combine les principaux services d’annuaire, la gestion des accès aux applications et la protection des identités. Avec Azure AD, vous pouvez intégrer des identités locales dans les clusters AKS pour fournir une source unique de sécurité et de gestion des comptes.

![Intégration d’Azure Active Directory aux clusters AKS](media/concepts-identity/aad-integration.png)

Avec les clusters AKS intégrés Azure AD, vous pouvez accorder aux utilisateurs ou aux groupes l’accès aux ressources Kubernetes dans un espace de noms ou au sein du cluster. 

1. Pour obtenir un contexte de configuration `kubectl`, un utilisateur exécute la commande [az aks get-credentials][az-aks-get-credentials]. 
1. Lorsqu’un utilisateur interagit ensuite avec le cluster AKS par le biais de `kubectl`, il est invité à se connecter avec ses informations d’identification Azure AD. 

Cette solution fournit une source unique pour les informations d’identification de mots de passe et de gestion des comptes utilisateur. L’utilisateur peut uniquement accéder aux ressources, tel que défini par l’administrateur du cluster.

L’authentification Azure AD est fournie aux clusters AKS à l’aide d’OpenID Connect. OpenID Connect est une couche d’identité basée sur le protocole OAuth 2.0. Pour plus d’informations sur OpenID Connect, voir la [documentation sur Open ID Connect][openid-connect]. Depuis le cluster Kubernetes, [l’authentification par jeton de Webhook][webhook-token-docs] est utilisée pour vérifier les jetons d’authentification. L’authentification par jeton de Webhook est configurée et gérée en tant que partie du cluster AKS.

### <a name="webhook-and-api-server"></a>Webhook et serveur API

![Webhook et le flux d’authentification du serveur API](media/concepts-identity/auth-flow.png)

Comme indiqué dans le graphique ci-dessus, le serveur API appelle le serveur webhook AKS et effectue les étapes suivantes :

1. `kubectl` utilise l’application cliente Azure AD pour connecter des utilisateurs avec le [flux d’octroi d’autorisation d’appareil OAuth 2.0](../active-directory/develop/v2-oauth2-device-code.md).
2. Azure AD fournit un access_token, id_token et un refresh_token.
3. L’utilisateur envoie une requête à `kubectl` avec un access_token à partir de `kubeconfig`.
4. `kubectl` envoie le jeton access_token au serveur d’API.
5. Le serveur API est configuré avec le serveur webhook d’authentification pour effectuer la validation.
6. Le serveur webhook d’authentification confirme que la signature JSON Web Token est valide en vérifiant la clé de signature publique Azure AD.
7. L’application serveur utilise les informations d’identification fournies par l’utilisateur pour interroger les appartenances aux groupes de l’utilisateur connecté à partir de l’API Graph MS.
8. Une réponse est envoyée au serveur d’API avec des informations utilisateur, telles que la revendication de nom d’utilisateur principal (UPN) du jeton d’accès et l’appartenance au groupe de l’utilisateur en fonction de l’ID d’objet.
9. L’API effectue une décision d’autorisation basée sur le rôle Kubernetes/RoleBinding.
10. Une fois l’autorisation accordée, le serveur d’API renvoie une réponse à `kubectl`.
11. `kubectl` envoie les commentaires à l’utilisateur.
 
Découvrez comment intégrer AKS à Azure AD avec notre [Guide pratique d’intégration d’Azure AD géré par AKS](managed-aad.md).

## <a name="azure-role-based-access-control"></a>Contrôle d'accès en fonction du rôle Azure

Le contrôle d’accès en fonction du rôle (RBAC) Azure est un système d’autorisation basé sur [Azure Resource Manager](../azure-resource-manager/management/overview.md), qui permet une gestion précise des accès des ressources Azure.

| Système RBAC | Description |
|---|---|
| Contrôle RBAC Kubernetes | Conçu pour fonctionner sur des ressources Kubernetes dans votre cluster AKS. |
| Azure RBAC | Conçu pour fonctionner sur des ressources dans votre abonnement Azure. |

Avec le contrôle RBAC Azure, vous créez une *définition de rôle* qui décrit les autorisations à appliquer. Vous attribuez ensuite à un utilisateur ou à un groupe cette définition de rôle par le biais d’une *attribution de rôle* pour une *étendue* spécifique. L’étendue peut être une ressource spécifique, un groupe de ressources ou l’ensemble de l’abonnement.

Pour plus d’informations, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (Azure RBAC) ?][azure-rbac]

Il existe deux niveaux d’accès nécessaires pour pleinement utiliser un cluster AKS : 
* [Accédez à la ressource AKS dans votre abonnement Azure](#azure-rbac-to-authorize-access-to-the-aks-resource). 
  * Contrôlez la mise à l'échelle ou la mise à niveau de votre cluster à l'aide des API AKS.
  * Extrayez votre fichier `kubeconfig`.
* Accédez à l’API Kubernetes. Cet accès est contrôlé par :
  * le [RBAC Kubernetes](#kubernetes-rbac) (en général) ; ou
  * [en intégrant Azure RBAC à AKS pour l'autorisation Kubernetes](#azure-rbac-for-kubernetes-authorization).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC pour autoriser l’accès à la ressource AKS

Avec RBAC Azure, vous pouvez fournir à vos utilisateurs (ou identités) un accès granulaire aux ressources AKS dans un ou plusieurs abonnements. Par exemple, vous pouvez utiliser le [rôle de contributeur d’Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) pour mettre à l’échelle et mettre à niveau votre cluster. Pendant ce temps, un autre utilisateur avec le [rôle d’administrateur de cluster d’Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) a uniquement l’autorisation d’extraire le `kubeconfig` d’administration.

Vous pouvez également attribuer à votre utilisateur le rôle de [contributeur](../role-based-access-control/built-in-roles.md#contributor) général. Avec le rôle de contributeur général, les utilisateurs peuvent effectuer les autorisations ci-dessus et toutes les actions possibles sur la ressource AKS, à l’exception de la gestion des autorisations.

[Utilisez Azure RBAC pour définir l'accès au fichier de configuration Kubernetes dans AKS](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization"></a>Azure RBAC pour autorisation Kubernetes

Avec l’intégration Azure RBAC, AKS utilise un serveur webhook d’autorisation Kubernetes pour vous permettre de gérer les autorisations et les attributions de ressources de cluster Kubernetes intégrées à Azure AD à l’aide de la définition de rôle Azure et des attributions de rôles.

![Azure RBAC pour le flux d’autorisation Kubernetes](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Comme indiqué dans le diagramme ci-dessus, lorsque vous utilisez l’intégration Azure RBAC, toutes les requêtes envoyées à l’API Kubernetes suivent le même processus d’authentification, comme expliqué dans la [section Intégration d’Azure Active Directory](#azure-ad-integration). 

Si l’identité à l’origine de la demande existe dans Azure AD, Azure collaborera avec Kubernetes RBAC pour autoriser la requête. Si l’identité existe en dehors d’Azure AD (par exemple, un compte de service Kubernetes), l’autorisation empêchera le RBAC Kubernetes normal.

Dans ce scénario, vous utilisez des mécanismes et API Azure RBAC pour affecter des rôles intégrés aux utilisateurs ou créer des rôles personnalisés, comme vous le feriez avec des rôles Kubernetes. 

Grâce à cette fonctionnalité, vous accordez non seulement aux utilisateurs des autorisations pour la ressource AKS sur l’ensemble des abonnements, mais vous configurez également le rôle et les autorisations pour chacun de ces clusters contrôlant l’accès à l’API Kubernetes. Par exemple, vous pouvez accorder le rôle `Azure Kubernetes Service RBAC Viewer` sur l’étendue de l’abonnement. Le destinataire du rôle sera en mesure de répertorier et d’extraire tous les objets Kubernetes de tous les clusters sans les modifier.

> [!IMPORTANT]
> Vous devez activer l’autorisation Azure RBAC pour Kubernetes avant de pouvoir utiliser cette fonctionnalité. Pour obtenir plus d’informations et des instructions détaillées, consultez notre guide pratique sur l’[utilisation d’Azure RBAC pour l’autorisation Kubernetes](manage-azure-rbac.md).

#### <a name="built-in-roles"></a>Rôles intégrés

AKS propose quatre rôles intégrés. Ils sont similaires aux [rôles intégrés Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) avec quelques différences, comme la prise en charge des CRD. Consultez la liste complète des actions autorisées par chaque [rôle intégré Azure](../role-based-access-control/built-in-roles.md).

| Role                                | Description  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC Viewer  | Autorise l’accès en lecture seule pour voir la plupart des objets dans un espace de noms. <br> N’autorise pas l’affichage des rôles ou des liaisons de rôles.<br> N’autorise pas l’affichage des `Secrets`. La lecture du contenu `Secrets` permet d’accéder aux informations d’identification `ServiceAccount` dans l’espace de noms, ce qui permet l’accès aux API comme n’importe quel `ServiceAccount` dans l’espace de noms (une forme d’élévation de privilèges).  |
| Azure  Kubernetes Service RBAC Writer | Autorise l’accès en lecture/écriture pour la plupart des objets dans un espace de noms. <br> N’autorise pas l’affichage ni la modification des rôles et des liaisons de rôles. <br> Permet d’accéder à `Secrets` et aux pods en cours d’exécution comme n’importe quel ServiceAccount dans l’espace de noms. Il peut donc être utilisé pour obtenir les niveaux d’accès API de n’importe quel ServiceAccount dans l’espace de noms. |
| Azure Kubernetes Service RBAC Admin  | Autorise l’accès administrateur, normalement accordé au sein d’un espace de noms. <br> Autorise l’accès en lecture/écriture à la plupart des ressources dans un espace de noms (ou dans l’étendue du cluster), y compris la possibilité de créer des rôles et des liaisons de rôles dans l’espace de noms. <br> N’autorise pas l’accès en écriture au quota de ressources ou à l’espace de noms proprement dit. |
| Azure Kubernetes Service RBAC Cluster Admin  | Autorise l’accès de super utilisateur qui permet d’effectuer n’importe quelle action sur toutes les ressources. <br> Donne un contrôle total sur l’ensemble des ressources dans le cluster et dans tous les espaces de noms. |


## <a name="summary"></a>Résumé

Consultez le tableau pour un résumé rapide de la manière dont les utilisateurs peuvent s’authentifier auprès de Kubernetes lorsque l’intégration Azure AD est activée. Dans tous les cas, la séquence de commandes de l’utilisateur est la suivante :
1. Exécuter `az login` pour s’authentifier sur Azure.
1. Exécuter `az aks get-credentials` pour télécharger les informations d’identification du cluster dans `.kube/config`.
1. Exécutez les commandes `kubectl`. 
   * La première commande peut déclencher l’authentification basée sur le navigateur pour s’authentifier auprès du cluster, comme décrit dans le tableau suivant.

Dans le portail Azure, vous pouvez trouver :
* L’*octroi de rôle* (octroi de rôle Azure RBAC) dont il est question dans la deuxième colonne est affiché sous l’onglet **Contrôle d’accès**. 
* Le groupe Cluster Admin Azure AD est affiché sous l’onglet **Configuration**.
  * Également affiché avec le nom de paramètre `--aad-admin-group-object-ids` dans l’interface de ligne de commande Azure.


| Description        | Octroi de rôle requis| Groupe(s) Azure AD administrateur de cluster | Quand l’utiliser |
| -------------------|------------|----------------------------|-------------|
| Connexion administrateur héritée à l’aide d’un certificat client| **Rôle administrateur Azure Kubernetes**. Ce rôle permet l’utilisation de `az aks get-credentials` avec l’indicateur `--admin`, ce qui télécharge un [certificat administrateur de cluster hérité (non-Azure AD)](control-kubeconfig-access.md) dans le `.kube/config` de l’utilisateur. Il s’agit du seul objectif du rôle administrateur Azure Kubernetes.|n/a|Si vous êtes bloqué et n’avez pas accès à un groupe Azure AD valide avec accès à votre cluster.| 
| Azure AD avec liaisons (cluster)RoleBindings manuelles| **Rôle d’utilisateur Azure Kubernetes**. Le rôle d’utilisateur permet l’utilisation de `az aks get-credentials` sans indicateur `--admin`. (Il s’agit de l’unique objectif du rôle d’utilisateur Azure Kubernetes.) Ainsi, un cluster Azure AD peut télécharger [une entrée vide](control-kubeconfig-access.md) dans `.kube/config`, ce qui déclenche l’authentification basée sur le navigateur lorsqu’il est utilisé pour la première fois par `kubectl`.| L’utilisateur ne se trouve dans aucun de ces groupes. L’utilisateur ne se trouvant dans aucun groupe d’administration de cluster, ses droits sont entièrement contrôlés par n’importe quelle liaison RoleBindings ou ClusterRoleBindings configurée par les administrateurs de cluster. Les liaisons (cluster)RoleBindings [désignent les utilisateurs ou groupes Azure AD](azure-ad-rbac.md) en tant que `subjects`. Si aucune de ces liaisons n’a été configurée, l’utilisateur ne sera pas en mesure d’exécuter de commandes `kubectl`.|Si vous souhaitez un contrôle d’accès affiné et que vous n’utilisez pas Azure RBAC pour l’autorisation Kubernetes. Notez que l’utilisateur qui configure les liaisons doit se connecter à l’aide d’une des autres méthodes répertoriées dans ce tableau.|
| Azure AD par membre du groupe d’administration| Identique à ce qui précède|L’utilisateur est membre d’un des groupes répertoriés ici. AKS génère automatiquement une liaison ClusterRoleBinding qui lie tous les groupes répertoriés au rôle de Kubernetes `cluster-admin`. Ainsi, les utilisateurs de ces groupes peuvent exécuter toutes les commandes `kubectl` comme `cluster-admin`.|Si vous souhaitez accorder aux utilisateurs des droits d’administrateur complets et que vous n’utilisez _pas_ Azure RBAC pour l’autorisation Kubernetes.|
| Azure AD avec Azure RBAC pour l’autorisation Kubernetes|Deux rôles : <br> Premièrement, **rôle d’utilisateur Azure Kubernetes** (comme ci-dessus). <br> Deuxièmement, l’un des rôles «Azure Kubernetes Service **RBAC**... » répertoriés ci-dessus, ou votre propre alternative personnalisée.|Le champ des rôles d’administrateur de l’onglet Configuration n’est pas pertinent lorsqu’Azure RBAC pour l’autorisation Kubernetes est activé.|Vous utilisez Azure RBAC pour l’autorisation Kubernetes. Cette approche vous offre un contrôle affiné, sans avoir à configurer de liaisons RoleBindings ou ClusterRoleBindings.|

## <a name="next-steps"></a>Étapes suivantes

- Pour vous familiariser avec RBAC Azure AD et Kubernetes, consultez [Intégrer Azure Active Directory à AKS][aks-aad].
- Pour plus d'informations sur les bonnes pratiques, consultez [Bonnes pratiques relatives à l'authentification et à l'autorisation dans AKS][operator-best-practices-identity].
- Pour vous familiariser avec l’autorisation Azure RBAC pour Kubernetes, consultez [utilisez Azure RBAC pour autoriser l’accès au sein du cluster Azure Kubernetes Service (AKS)](manage-azure-rbac.md).
- Pour commencer à sécuriser votre fichier `kubeconfig`, consultez [Limiter l’accès au fichier de configuration de cluster](control-kubeconfig-access.md)

Pour plus d’informations sur les concepts fondamentaux de Kubernetes et d’AKS, consultez les articles suivants :

- [Clusters et charges de travail Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Sécurité Kubernetes/AKS][aks-concepts-security]
- [Réseaux virtuels Kubernetes/AKS][aks-concepts-network]
- [Stockage Kubernetes/AKS][aks-concepts-storage]
- [Mise à l’échelle Kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
