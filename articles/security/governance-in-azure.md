---
title: Gouvernance dans Azure | Microsoft Docs
description: Découvrez les services informatiques cloud qui peuvent évoluer pour répondre aux besoins de votre application ou de votre entreprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 579e900ee6616af8fd197e501364acd8e18d3e37
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970521"
---
# <a name="governance-in-azure"></a>Gouvernance dans Azure

Azure offre de nombreuses options de sécurité, ainsi que la possibilité de contrôler ces options pour vous permettre de répondre aux exigences uniques des déploiements de votre organisation.

La gouvernance cloud Azure fait référence aux processus de prise de décisions, aux critères et aux stratégies impliqués dans la planification, l’architecture, l’acquisition, le déploiement, le fonctionnement et la gestion du cloud computing. La gouvernance cloud Azure fournit une approche d’audit et de conseils intégrée afin de passer en revue l’utilisation de la plateforme Azure par les organisations et de les conseiller dans ce domaine. 

Pour créer un plan pour la gouvernance du cloud Azure, vous devez examiner de manière approfondie les personnes, les processus et les technologies actuellement en place. Ensuite, vous pouvez créer des frameworks qui facilitent la prise en charge continue des besoins de l’entreprise tout en offrant aux utilisateurs la flexibilité nécessaire pour utiliser les fonctionnalités d’Azure.

## <a name="implementation-of-policies-processes-and-standards"></a>Implémentation de stratégies, de processus et de normes 

Les responsables ont défini des rôles et des responsabilités pour surveiller la mise en œuvre des stratégies de sécurité des informations et de la continuité opérationnelle dans Azure. La gestion Azure a pour mission de superviser les pratiques de sécurité et de continuité des activités au sein de ses équipes respectives (y compris les tierces parties). Elle facilite également la conformité avec les stratégies de sécurité, les processus et les normes.

### <a name="account-provisioning"></a>Provisionnement des comptes

Définir la hiérarchie des comptes est une étape importante pour utiliser et structurer les services Azure au sein d’une entreprise. C’est la base de la structure de gouvernance. Les clients qui ont un Accord Entreprise peuvent subdiviser l’environnement en services, comptes et abonnements.

![Provisionnement des comptes](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Si vous ne disposez pas d’un Accord Entreprise, pensez à utiliser des [balises Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) au niveau de l’abonnement pour définir la hiérarchie. Un abonnement Azure est l’unité de base qui contient toutes les ressources. Il définit également plusieurs limites au sein d’Azure, telles que le nombre de cœurs et de ressources. Les abonnements peuvent contenir des [groupes de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), qui à leur tour peuvent contenir des ressources. Les principes du [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) s’appliquent à ces trois niveaux.

Chaque entreprise est différente. Pour celles n’ayant pas d’Accord Entreprise, la hiérarchie avec balises Azure offre une flexibilité dans la façon dont Azure est organisé. Avant de déployer des ressources dans Azure, vous devez modéliser une hiérarchie et en comprendre l’impact sur la facturation, l’accès aux ressources et la complexité.

### <a name="subscription-controls"></a>Contrôles de l’abonnement

Les abonnements déterminent la manière dont l’utilisation des ressources est signalée et facturée. Vous pouvez configurer des abonnements pour obtenir une facturation et un paiement séparés. Un compte Azure peut avoir plusieurs abonnements. Les abonnements peuvent être utilisés pour déterminer l’utilisation des ressources Azure par plusieurs services au sein d’une entreprise.

Par exemple, imaginez qu’une entreprise a un service informatique, un service RH et un service marketing, et que ces services mènent différents projets. L’entreprise peut baser sa facturation sur l’utilisation des ressources Azure, comme les machines virtuelles, par chaque service. Elle peut ensuite contrôler les finances de chaque service.

Les abonnements Azure établissent trois paramètres :

- Un ID d’abonné unique

- Un emplacement de facturation

- Un ensemble de ressources disponibles

Pour une personne, ces paramètres incluent un ID de compte Microsoft, un numéro de carte de crédit et la suite complète de services Azure. Microsoft impose des limites de consommation, en fonction du type d’abonnement.

Les hiérarchies d’inscription Azure définissent la structure des services dans un Accord Entreprise. Le portail Accord Entreprise permet aux clients de diviser l’accès aux ressources Azure associées à un Accord Entreprise en fonction de hiérarchies flexibles personnalisables d’après les besoins d’une organisation. Le modèle de hiérarchie doit correspondre à la structure géographique et de gestion d’une organisation afin de prendre en compte la facturation et l’accès aux ressources associés.

Les trois principaux modèles de hiérarchie sont les modèles fonctionnel, d’unité commerciale et géographique. Les services sont une construction administrative pour les regroupements de comptes. Au sein de chaque service, des abonnements peuvent être affectés aux comptes, ce qui crée des silos pour la facturation et plusieurs limites clés dans Azure (par exemple, nombre de machines virtuelles et comptes de stockage).

![Contrôles de l’abonnement](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Pour les organisations disposant d’un Accord Entreprise, les abonnements Azure respectent une hiérarchie à quatre niveaux :

1. Administrateur d’inscription d’entreprise

2. Administrateur de service

3. Propriétaire du compte

4. Administrateur de services fédérés

Cette hiérarchie régit les éléments suivants :

- Relation de facturation

- Administration des comptes

- Accès aux ressources au moyen de RBAC

- Limites :

  - Utilisation et facturation (carte de tarifs basée sur le numéro de l’offre)

  - Limites

  - Réseau virtuel

- Attachement à Azure Active Directory (Azure AD). Une instance d’Azure AD peut être associée à plusieurs abonnements.

- Association à un compte d’inscription d’entreprise

### <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

[RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) autorise une gestion détaillée des accès aux ressources dans Azure. L’utilisation de RBAC vous permet de n’accorder que les droits d’accès dont les utilisateurs ont besoin pour effectuer leur travail. Les entreprises doivent s’efforcer de donner aux employés les autorisations exactes dont ils ont besoin. Un trop grand nombre d’autorisations expose un compte aux attaques. Si le nombre d’autorisations est trop faible, les employés ne peuvent pas effectuer leur travail efficacement. 

Plutôt que de donner à tous des autorisations illimitées au sein de votre abonnement ou de vos ressources Azure, vous pouvez autoriser uniquement certaines actions. Par exemple, vous pouvez utiliser RBAC pour permettre à un employé donné de gérer les machines virtuelles dans un abonnement, tandis qu’un autre gère les bases de données SQL au sein du même abonnement.

Pour accorder l’accès, vous attribuez des rôles aux utilisateurs, aux groupes ou aux applications dans une étendue donnée. L’étendue d’une attribution de rôle peut être une seule ressource, un groupe de ressources ou un abonnement. Un rôle attribué à une étendue parent accorde également l’accès aux enfants qu’elle contient. Par exemple, un utilisateur ayant accès à un groupe de ressources peut gérer toutes les ressources qu’il contient, telles que les sites web, les machines virtuelles et les sous-réseaux. Dans chaque abonnement, vous pouvez créer jusqu’à 2000 attributions de rôles.

Un rôle est une collection d’autorisations, et RBAC a plusieurs rôles intégrés. Les rôles intégrés suivants s’appliquent à tous les types de ressources :

- **Propriétaire** dispose d’un accès total à toutes les ressources, ainsi que le droit de déléguer l’accès à d’autres personnes.

- **Contributeur** peut créer et gérer tous les types de ressources Azure, mais ne peut pas accorder l’accès à d’autres personnes.

- **Lecteur** peut consulter toutes les ressources Azure.

Les autres rôles intégrés dans Azure permettent de gérer des ressources Azure spécifiques. Par exemple, le rôle de contributeur de machine virtuelle permet à l’utilisateur de créer et gérer des machines virtuelles. Pour obtenir la liste de tous les rôles intégrés et leurs opérations, consultez [Rôles intégrés dans Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

RBAC prend en charge les opérations de gestion des ressources Azure dans le portail Azure et les API Azure Resource Manager. Dans la plupart des cas, RBAC ne peut pas autoriser les opérations au niveau des données pour les ressources Azure. Pour plus d’informations sur l’extension de RBAC aux opérations de données, consultez [Comprendre les définitions de rôles](https://docs.microsoft.com/azure/role-based-access-control/role-definitions).

Si les rôles intégrés ne répondent pas à vos besoins d’accès, vous pouvez [créer un rôle personnalisé](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). À l’instar des rôles intégrés, les rôles personnalisés peuvent être affectés à des utilisateurs, des groupes et des applications dans l’étendue des abonnements, des groupes de ressources et des ressources. Vous pouvez créer des rôles personnalisés à l’aide d’[Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), d’[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) et de l’[API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

### <a name="resource-management"></a>Gestion des ressources

Azure fournit deux modèles de déploiement : [classic](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) et Azure Resource Manager.

Dans le modèle classic, chaque ressource existe indépendamment. Il n’existe aucun moyen de regrouper des ressources associées. Vous devez suivre manuellement les ressources qui composent la solution ou l’application, et veiller à les gérer selon une approche coordonnée. L’unité de base de gestion est l’abonnement. Il est difficile de répartir les ressources au sein d’un abonnement, ce qui entraîne la création d’un grand nombre d’abonnements.

Le modèle de déploiement Resource Manager inclut le concept de [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Un groupe de ressources est un conteneur de ressources qui partagent un cycle de vie commun. Il peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation.

Le modèle de déploiement Resource Manager présente plusieurs avantages :

- Vous pouvez déployer, gérer et surveiller tous les services de votre solution comme un groupe, plutôt que de gérer ces services individuellement.

- Vous pouvez déployer votre solution à plusieurs reprises tout au long de son cycle de vie et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.

- Vous pouvez appliquer le contrôle d’accès à toutes les ressources dans votre groupe de ressources. Ces stratégies sont appliquées automatiquement quand de nouvelles ressources sont ajoutées au groupe de ressources.

- Vous pouvez appliquer des balises aux ressources pour organiser logiquement toutes les ressources de votre abonnement.

- Vous pouvez utiliser JavaScript Objet Notation (JSON) pour définir l’infrastructure de votre solution. Le fichier JSON est connu sous le nom de modèle Resource Manager.

- Vous pouvez définir les dépendances entre les ressources afin de les déployer dans le bon ordre.

![Gestionnaire de ressources](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Pour des recommandations sur les modèles, voir [Bonnes pratiques relatives à la création de modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

Azure Resource Manager analyse les dépendances pour aider à s’assurer que les ressources sont créées dans le bon ordre. Si une ressource dépend d’une valeur d’une autre ressource (par exemple, une machine virtuelle ayant besoin d’un compte de stockage pour les disques), vous devez [définir une dépendance](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) dans le modèle.

Vous pouvez également utiliser le modèle pour les mises à jour de l’infrastructure. Par exemple, vous pouvez ajouter une ressource à votre solution et ajouter des règles de configuration pour les ressources qui sont déjà déployées. Si le modèle spécifie de créer une ressource, mais que cette ressource existe déjà, Resource Manager effectue une mise à jour au lieu de créer une autre ressource. Resource Manager met à jour l’actif existant vers l’état qu’il présenterait s’il s’agissait d’une nouvelle ressource.

Resource Manager fournit des extensions pour les cas qui nécessitent davantage d’opérations, comme l’installation d’un logiciel non inclus dans le programme d’installation.

### <a name="resource-tracking"></a>Suivi des ressources

À mesure que les utilisateurs de votre organisation ajoutent des ressources à l’abonnement, il devient de plus en plus important d’associer des ressources au service, au client et à l’environnement appropriés. Vous pouvez attacher des métadonnées aux ressources par le biais de [balises](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags). Vous utilisez des balises pour fournir des informations sur la ressource ou le propriétaire. Les balises vous permettent non seulement d’agréger et de regrouper les ressources de différentes façons, mais également d’utiliser ces données pour les besoins de facturation interne.

Utilisez des balises quand vous disposez d’un ensemble complexe de groupes de ressources et de ressources, et que vous souhaitez visualiser ces actifs de la façon qui vous semble la plus logique. Par exemple, vous pouvez baliser des ressources qui jouent un rôle similaire dans votre organisation ou qui appartiennent au même service.

Sans balises, les utilisateurs de votre organisation peuvent créer plusieurs ressources qui peuvent s’avérer difficiles à identifier et à gérer ultérieurement. Par exemple, vous pourriez souhaiter supprimer toutes les ressources d’un projet. Si ces ressources ne sont pas balisées pour le projet, vous devez les rechercher manuellement. Le balisage constitue un levier important pour réduire les coûts inutiles dans votre abonnement.

Les ressources ne doivent pas nécessairement appartenir au même groupe de ressources pour partager une balise. Vous pouvez créer votre propre taxonomie de balises pour vous assurer que tous les utilisateurs de votre organisation utiliseront des balises communes plutôt que d’appliquer par inadvertance des balises légèrement différentes (telles que « dépt » au lieu de « département »).

Les stratégies de ressources vous permettent de créer des règles standard pour votre organisation. Vous pouvez créer des stratégies pour garantir que les ressources sont balisées avec les valeurs appropriées.

Vous avez également la possibilité de consulter les ressources balisées via le portail Azure. Le [rapport d’utilisation](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) de votre abonnement inclut les noms et valeurs de balises, afin de vous permettre de répartir les coûts en fonction des balises.

Pour plus d’informations sur les balises, consultez [Initiative de la stratégie de facturation en fonction de balises](../azure-policy/scripts/billing-tags-policy-init.md).

Les limites suivantes s’appliquent aux balises :

- Chaque ressource ou groupe de ressources peut inclure un maximum de 15 paires clé/valeur de balise. Cette limitation s’applique uniquement aux balises directement appliquées au groupe de ressources ou à la ressource. Un groupe de ressources peut contenir de nombreuses ressources qui ont chacune 15 paires clé/valeur de balise.

- Le nom de la balise est limité à 512 caractères.

- La valeur de la balise est limitée à 256 caractères.

- Les ressources d’un groupe de ressources n’héritent pas des balises appliquées à ce groupe de ressources.

Si vous devez associer plus de 15 valeurs à une ressource, utilisez une chaîne JSON pour la valeur de balise. La chaîne JSON peut contenir plusieurs valeurs appliquées à une seule clé de balise.

#### <a name="tags-for-billing"></a>Balises pour la facturation

Les balises vous permettent de regrouper les données de facturation. Par exemple, si vous exécutez plusieurs machines virtuelles pour différentes organisations, vous pouvez recourir aux balises pour regrouper l’utilisation par centre de coûts. Vous pouvez également utiliser des balises pour catégoriser les coûts par environnement d’exécution ; par exemple, l’utilisation de la facturation pour les machines virtuelles en cours d’exécution dans l’environnement de production.

Vous pouvez récupérer des informations sur les balises par le biais des [API Resource Usage et RateCard](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) ou du fichier de valeurs séparées par des virgules (CSV). Vous téléchargez le fichier d’utilisation à partir du [portail des comptes Azure](https://account.windowsazure.com/) ou du [portail Accord Entreprise](https://ea.azure.com/).

Pour plus d’informations sur l’accès par programme aux informations de facturation, consultez [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Pour plus d’informations sur les opérations de l’API REST, consultez [Informations de référence sur l’API REST Azure Billing](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Lorsque vous téléchargez le fichier CSV d’utilisation pour les services qui prennent en charge les balises avec la facturation, les balises s’affichent dans la colonne Balises.

### <a name="critical-resource-controls"></a>Contrôles des ressources critiques

À mesure que votre organisation ajoute des services principaux à l’abonnement, il devient de plus en plus important de s’assurer que ces services sont disponibles afin d’éviter une interruption de service. Les [verrous de ressource](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) vous permettent de limiter les opérations sur les ressources de grande valeur si leur suppression ou leur modification pourraient avoir un impact significatif sur vos applications ou votre infrastructure de cloud. Vous pouvez appliquer des verrous à un abonnement, un groupe de ressources ou une ressource. En règle générale, vous appliquez des verrous à des ressources fondamentales telles que les réseaux virtuels, les passerelles et les comptes de stockage.

Les verrous de ressources prennent actuellement en charge deux valeurs : **CanNotDelete** et **ReadOnly**. **CanNotDelete** signifie que les utilisateurs (disposant des droits appropriés) peuvent toujours lire ou modifier une ressource, mais pas la supprimer. **ReadOnly** signifie que les utilisateurs autorisés ne peuvent pas supprimer ou modifier une ressource.

Les verrous de ressources s’appliquent uniquement aux opérations qui se produisent dans le plan de gestion, c’est-à-dire les opérations envoyées à <https://management.azure.com>. Les verrous ne limitent pas la manière dont les ressources exécutent leurs propres fonctions. Les modifications des ressources sont limitées, mais pas les opérations sur les ressources. Par exemple, un verrou **ReadOnly** sur une base de données SQL vous empêche de supprimer ou de modifier cette base de données, mais il ne vous empêche pas de créer, mettre à jour ou supprimer les données qu’elle contient.

L’application de **ReadOnly** peut produire des résultats inattendus, car certaines opérations qui ressemblent à des opérations de lecture nécessitent des actions supplémentaires. Par exemple, le placement d’un verrou **ReadOnly** sur un compte de stockage empêche tous les utilisateurs de répertorier les clés. L’opération de listage de clés est gérée par le biais d’une requête POST, car les clés retournées sont disponibles pour les opérations d’écriture.

![Contrôles des ressources critiques](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Autre exemple : le placement d’un verrou **ReadOnly** sur une ressource Azure App Service empêche l’Explorateur de serveurs Visual Studio d’afficher les fichiers de la ressource, car cette interaction nécessite un accès en écriture.

Contrairement au contrôle d'accès basé sur les rôles, vous utilisez des verrous de gestion pour appliquer une restriction à tous les utilisateurs et rôles. Pour en savoir plus sur la définition des autorisations, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Lorsque vous appliquez un verrou à une étendue parente, toutes les ressources de cette étendue héritent du même verrou. Même les ressources que vous ajoutez par la suite héritent du verrou du parent. Le verrou le plus restrictif de l’héritage est prioritaire.

Pour créer ou supprimer des verrous de gestion, vous devez avoir accès aux actions Microsoft.Authorization/ ou Microsoft.Authorization/locks/. Parmi les rôles prédéfinis, seuls les rôles Propriétaire et Administrateur de l'accès utilisateur peuvent effectuer ces actions.

### <a name="api-access-to-billing-information"></a>Accès par API aux informations de facturation

Utilisez les API de facturation Azure pour extraire les données d’utilisation et de ressources dans vos outils d’analyse de données préférés. Les API d’utilisation des ressources Azure et RateCard peuvent vous aider à prévoir vos coûts avec précision et à les gérer. Les API sont implémentées en tant que fournisseur de ressources et font partie intégrante de la famille d’API exposées par Azure Resource Manager.

#### <a name="resource-usage-api-preview"></a>API Resource Usage (préversion)

Utiliser l’[API Azure Resource Usage](https://msdn.microsoft.com/library/azure/mt219003) pour obtenir une estimation de vos données de consommation Azure. L’API comprend :

- **RBAC** : configurez les stratégies d’accès dans le [portail Azure](https://portal.azure.com/) ou par le biais des [applets de commande Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) pour spécifier les utilisateurs ou les applications qui peuvent accéder aux données d’utilisation de l’abonnement. Les appelants doivent utiliser les jetons Azure Active Directory standard pour l’authentification. Ajoutez l’appelant au rôle Lecteur de facture, Lecteur, Propriétaire ou Collaborateur pour être en mesure d’accéder aux données d’utilisation relatives à un abonnement Azure spécifique.

- **Agrégations horaires ou quotidiennes** : les appelants peuvent indiquer s’ils souhaitent visualiser leurs données d’utilisation Azure par incréments horaires ou quotidiens. Par défaut, les données sont présentées par jour.

- **Métadonnées d’instance (balises de ressource incluses)**  : obtenez des détails au niveau de l’instance, comme l’URI de ressource complet (/subscriptions/{subscription-id} /..), des informations sur les groupes de ressources et les balises de ressources. Ces métadonnées vous aideront à allouer de façon déterministe et par programmation l’utilisation par les balises, pour les cas d’utilisation comme la facturation interne.

- **Métadonnées de ressources** : des détails sur les ressources, comme le nom du compteur, la catégorie du compteur, la sous-catégorie du compteur, l’unité et la région permettent à l’appelant de mieux comprendre les ressources consommées. Nous nous efforçons également d’aligner la terminologie des métadonnées de ressources dans l’ensemble du portail Azure, des fichiers CSV sur l’utilisation d’Azure et sur la facturation Accord Entreprise, ainsi que dans toutes les autres expériences destinées au public, afin de vous aider à mettre en corrélation les données des différentes expériences.

- **Utilisation pour tous les types d’offre** : les données d’utilisation sont accessibles pour tous les types d’offre, notamment Paiement à l’utilisation, MSDN, Engagement financier, Crédit financier et Accord Entreprise.

#### <a name="resource-ratecard-api"></a>API Resource RateCard

Utilisez l’API Azure Resource RateCard pour obtenir la liste des ressources Azure disponibles et une estimation des informations de prix pour chacune de ces ressources. L’API comprend :

- **RBAC** : configurez vos stratégies d’accès dans le portail Azure ou par le biais des applets de commande Azure PowerShell pour spécifier les utilisateurs ou les applications qui peuvent accéder aux données de RateCard. Les appelants doivent utiliser les jetons Azure Active Directory standard pour l’authentification. Ajoutez l’appelant au rôle Lecteur, Propriétaire ou Collaborateur pour être en mesure d’accéder aux données d’utilisation relatives à un abonnement Azure particulier.

- **Prise en charge des offres Paiement à l’utilisation, MSDN, Engagement financier et Crédit financier (mais pas Accord Entreprise)**  : cette API fournit des informations de tarif au niveau des offres Azure. L’appelant de cette API doit transmettre les informations d’offre pour obtenir les détails et les tarifs des ressources. Accord Entreprise n’est pas pris en charge actuellement, car les offres Accord Entreprise présentent des tarifs personnalisés par inscription. 

#### <a name="scenarios"></a>Scénarios

La combinaison des API Usage et RateCard rend ces scénarios possibles :

- **Compréhension des dépenses Azure au cours du mois** : utilisez la combinaison des API Usage et RateCard pour obtenir une meilleure idée de vos dépenses de cloud au cours du mois. Vous pouvez analyser les estimations d’utilisation et de facturation horaires et quotidiennes.

- **Configurer des alertes** : utilisez les API Usage et RateCard pour obtenir une estimation de la consommation et de la facturation, et pour configurer des alertes en fonction des ressources ou des valeurs monétaires.

- **Prédiction de facture** : obtenez une estimation de votre consommation et de vos dépenses cloud, et appliquez des algorithmes d’apprentissage automatique pour prédire le montant de la facture à la fin du cycle de facturation.

- **Effectuez une analyse des coûts avant consommation** : utilisez l’API RateCard afin de prédire le montant de votre facture pour votre utilisation prévue quand vous déplacez vos charges de travail vers Azure. Si vous disposez de charges de travail existantes dans d’autres clouds publics ou privés, vous pouvez également mapper votre utilisation sur les tarifs Azure afin d’obtenir une meilleure estimation de vos dépenses Azure. Cette estimation vous donne la possibilité d’ajouter un tableau croisé dynamique sur l’offre et de comparer les différents types d’offre au-delà du paiement à l’utilisation, notamment
Engagement financier et Crédit financier.

- **Effectuez des analyses de scénarios** : vous pouvez déterminer s’il est plus rentable d’exécuter les charges de travail dans une autre région ou dans une autre configuration de la ressource Azure. Les coûts des ressources Azure peuvent varier en fonction de la région Azure que vous utilisez. Vous pouvez également déterminer si un autre type d’offre Azure propose un meilleur tarif pour une ressource Azure.

### <a name="networking-controls"></a>Contrôles de mise en réseau

L’accès aux ressources peut être interne (dans le réseau de l’entreprise) ou externe (par le biais d’internet). Il est facile pour les utilisateurs de votre organisation de placer par inadvertance des ressources au mauvais endroit et de potentiellement les exposer à un accès malveillant. Comme pour les appareils locaux, les entreprises doivent ajouter des contrôles appropriés pour s’assurer que les utilisateurs d’Azure prennent les bonnes décisions.

![Contrôles de mise en réseau](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Pour la gouvernance de l’abonnement, les ressources principales suivantes fournissent un contrôle de base de l’accès.

#### <a name="network-connectivity"></a>Connectivité réseau

Les [réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sont des objets Conteneur de sous-réseaux. Bien que cela ne soit pas strictement nécessaire, un réseau virtuel est souvent utilisé pour la connexion des applications aux ressources d’entreprise internes. Le service Azure Virtual Network vous permet de connecter en toute sécurité les ressources Azure entre elles à l’aide de réseaux virtuels.

Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Il s’agit d’une isolation logique du cloud Azure dédié à votre abonnement. Vous pouvez également connecter des réseaux virtuels à votre réseau local.

Voici les fonctionnalités des réseaux virtuels Azure :

- **Isolation** : les réseaux virtuels sont isolés les uns des autres. Vous pouvez créer des réseaux virtuels distincts à des fins de développement, de test et de production qui utilisent les mêmes blocs d’adresses CIDR. À l’inverse, vous pouvez créer plusieurs réseaux virtuels qui utilisent différents blocs d’adresses CIDR et connecter les réseaux entre eux. Vous pouvez segmenter un réseau virtuel en plusieurs sous-réseaux. Azure assure la résolution de noms interne pour les machines virtuelles et instances de rôle Azure Cloud Services connectées à un réseau virtuel. Vous pouvez également configurer un réseau virtuel afin d’utiliser vos propres serveurs DNS au lieu d’utiliser la résolution de noms interne Azure.

- **Connectivité Internet** : toutes les machines virtuelles Azure et instances de rôle de services cloud connectées à un réseau virtuel ont accès à Internet, par défaut. Vous pouvez également activer l’accès entrant à des ressources spécifiques, en fonction de vos besoins.

- **Connectivité des ressources Azure** : vous pouvez connecter des ressources Azure, telles que des services cloud et des machines virtuelles, au même réseau virtuel. Les ressources peuvent se connecter entre elles à l’aide d’adresses IP privées, même si elles se trouvent sur des sous-réseaux différents. Azure fournit un routage par défaut entre les sous-réseaux, les réseaux virtuels et les réseaux locaux, sans que vous ayez à configurer et à gérer ces itinéraires.

- **Connectivité de réseaux virtuels** : vous pouvez connecter des réseaux virtuels entre eux. Les ressources connectées à un réseau virtuel peuvent ensuite communiquer avec n’importe quelle ressource sur n’importe quel autre réseau virtuel.

- **Connectivité locale** : vous pouvez connecter des réseaux virtuels à des réseaux locaux par le biais de connexions de réseau privé entre votre réseau et Azure, ou par le biais d’une connexion VPN de site à site sur Internet.

- **Filtrage du trafic** : vous pouvez filtrer le trafic réseau (entrant et sortant) pour les machines virtuelles et les services cloud par adresse IP source et port, par adresse IP de destination et port, et par protocole.

- **Routage** : vous pouvez également remplacer le routage Azure par défaut en configurant votre propre routage ou en utilisant des itinéraires BGP par le biais d’une passerelle réseau.

#### <a name="network-access-controls"></a>Contrôles d’accès réseau

Les [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) sont similaires à un pare-feu et fournissent des règles pour la manière dont une ressource peut « communiquer » par le biais du réseau. Ils permettent de contrôler la manière dont un sous-réseau (ou une machine virtuelle) peut se connecter à Internet ou à d’autres sous-réseaux sur le même réseau virtuel.

Un groupe de sécurité réseau contient la liste des règles de sécurité qui autorisent ou rejettent le trafic réseau vers les ressources connectées aux réseaux virtuels Azure. Les groupes de sécurité réseau peuvent être associés à des sous-réseaux, à des machines virtuelles spécifiques (Classic) ou à des interfaces réseau individuelles attachées à des machines virtuelles (Resource Manager).

Quand un groupe de sécurité réseau est associé à un sous-réseau, les règles s’appliquent à toutes les ressources connectées au sous-réseau. Vous pouvez restreindre davantage le trafic en associant également un groupe de sécurité réseau à une machine virtuelle ou à une carte réseau.

## <a name="security-and-compliance-with-organizational-standards"></a>Sécurité et conformité aux normes de l’organisation

Chaque entreprise a des besoins différents et tire des avantages différents des solutions cloud. Mais les clients ont tous les mêmes préoccupations de base concernant la migration vers le cloud. Voici ce que les clients veulent obtenir des fournisseurs de solutions cloud :

- **Sécuriser les données** : les responsables informatiques reconnaissent que le cloud peut accroître la sécurité des données et le contrôle administratif, mais ils ont encore des doutes et pensent que la migration vers le cloud les rendra plus vulnérables aux attaques des pirates que leurs solutions internes actuelles.

- **Maintenir la confidentialité des données** : les services cloud représentent des défis uniques en termes de confidentialité. À mesure que les entreprises se tournent vers le cloud pour réduire les coûts d’infrastructure et améliorer leur flexibilité, elles s’inquiètent également de perdre le contrôle sur l’emplacement de stockage de leurs données, à savoir qui y a accès et comment elles sont utilisées.

- **Conserver le contrôle** : alors même qu’elles tirent parti du cloud pour déployer des solutions plus innovantes, les entreprises s’inquiètent de perdre le contrôle de leurs données. Suite aux révélations récentes sur des agences gouvernementales accédant à des données des clients, par des moyens légaux et illégaux, les directeurs informatiques s’inquiètent de stocker leurs données dans le cloud.

- **Promouvoir la transparence** : les décisionnaires comprennent l’importance de la sécurité, de la confidentialité et du contrôle. Mais ils veulent également pouvoir vérifier indépendamment comment leurs données sont stockées, utilisées et sécurisées.

- **Respecter la conformité** : à mesure que les entreprises étendent leur utilisation des technologies cloud, la complexité et la portée des normes et des réglementations ne cessent d’évoluer. Les entreprises ont besoin de savoir que leurs normes de conformité seront respectées.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>Configuration de la sécurité pour la surveillance, la journalisation et l’audit

Les abonnés Azure peuvent gérer leurs environnements cloud à partir de plusieurs appareils. Ces appareils peuvent inclure des stations de travail de gestion, des PC de développeurs et même des appareils d’utilisateurs finaux disposant de privilèges et d’autorisations propres à des tâches. 

Dans certains cas, les fonctions d’administration sont effectuées par le biais de consoles web, comme le portail Azure. Des connexions directes peuvent aussi être établies avec Azure à partir de systèmes locaux sur des VPN, Terminal Services, des protocoles d’application cliente ou l’API de gestion des services Azure (SMAPI) (par programmation). Par ailleurs, les points de terminaison de client peuvent être joints au domaine ou isolés et non gérés, comme les tablettes ou les smartphones.

Cette variabilité peut ajouter un risque significatif à un déploiement cloud. La gestion, le suivi et l’audit peuvent être plus complexes pour les actions d’administration. Des menaces de sécurité peuvent également survenir avec l’accès non réglementé aux points de terminaison de client utilisés pour la gestion des services cloud. Les stations de travail personnelles ou communes destinées au développement et à la gestion de l’infrastructure introduisent des menaces imprévisibles, notamment avec la navigation Web (par exemple, lors d’attaques de point d’eau) ou la messagerie électronique (par exemple, ingénierie sociale et hameçonnage).

La surveillance, la journalisation et l’audit fournissent une base pour suivre et comprendre les activités administratives. L’audit détaillé de toutes les actions peut ne pas toujours être possible en raison du volume de données généré. Il est toutefois recommandé d’auditer l’efficacité des stratégies de gestion.

La gouvernance de sécurité Azure à partir d’objets de stratégie de groupe AD DS (Azure Active Directory Domain Services) peut vous aider à contrôler toutes les interfaces Windows de l’administrateur, comme le partage de fichiers. Incluez les stations de travail de gestion dans la surveillance, la journalisation et l’audit. Effectuez le suivi des accès et de l’utilisation au niveau administrateur et développeur.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) offre une vue centralisée de l’état de la sécurité des ressources dans les abonnements. Il fournit des recommandations qui aident à empêcher la compromission des ressources. Il peut autoriser des stratégies plus détaillées (par exemple l’application de stratégies à des groupes de ressources spécifiques qui permettent à l’entreprise d’adapter sa position en fonction des risques auxquels elle est confrontée).

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Security Center fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité. Une fois que vous avez activé les [stratégies de sécurité](https://docs.microsoft.com/azure/security-center/security-center-policies) pour les ressources d’un abonnement, Azure Security Center analyse la sécurité de vos ressources afin d’identifier les vulnérabilités potentielles. Les informations sur la configuration du réseau sont instantanément disponibles.

Azure Security Center constitue une combinaison des meilleures pratiques en matière d’analyse et de gestion de la stratégie de sécurité pour toutes les ressources dans un abonnement Azure. Il permet aux équipes de sécurité et aux responsables de la gestion des risques d’éviter les menaces de sécurité, de les détecter et d’y répondre, car il collecte et analyse automatiquement les données de sécurité à partir de vos ressources Azure, du réseau et des solutions de partenaires, comme les logiciels anti-programmes malveillants et les pare-feu.

En outre, Azure Security Center applique des analyses avancées, notamment l’apprentissage automatique et des études du comportement. Il utilise des informations globales concernant des menaces provenant de produits et services Microsoft, de Microsoft Digital Crimes Unit (DCU), de Microsoft Security Response Center (MSRC) et de sources externes. Vous pouvez appliquer la [gouvernance de sécurité](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) largement au niveau de l’abonnement. Vous pouvez aussi la limiter à des exigences spécifiques et l’appliquer à des ressources individuelles par le biais de la définition de stratégie.

Pour finir, Azure Security Center analyse l’intégrité de sécurité des ressources en fonction de ces stratégies, et utilise ces informations pour présenter des tableaux de bord informatifs et générer des alertes pour des événements tels que la détection de programmes malveillants ou des tentatives de connexions à partir d’adresses IP malveillantes. Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Azure Security Center surveille les ressources Azure suivantes :

- Machines virtuelles (notamment les services cloud)

- Réseaux virtuels

- Bases de données SQL

- Solutions de partenaires intégrées à votre abonnement Azure, par exemple pare-feu d’applications web sur les machines virtuelles et sur [App Service Environment](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

Lorsque vous accédez à Security Center pour la première fois, la collecte des données est activée sur toutes les machines virtuelles de votre abonnement. Nous vous recommandons de laisser la collecte de données activée, mais vous pouvez [la désactiver](https://docs.microsoft.com/azure/security-center/security-center-faq) dans la stratégie Security Center.

### <a name="log-analytics"></a>Log Analytics

Le [programme de gouvernance](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) et de sécurité des informations de l’équipe de service et de développement du logiciel Azure Log Analytics prend en charge ses besoins métier. Il est conforme aux lois et aux réglementations décrits dans [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) et [Conformité pour les services cloud Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Ces sites web expliquent également la façon dont Log Analytics fixe les exigences de sécurité, identifie les contrôles de sécurité, et gère et analyse les risques. Une fois par an, l’équipe passe en revue les stratégies, normes, procédures et directives.

Chaque membre de l’équipe de développement de Log Analytics reçoit une formation formelle en matière de sécurité des applications. Un système de gestion de versions vous aide à protéger chaque projet logiciel en cours de développement.

Microsoft dispose d’une équipe dédiée à la sécurité et à la conformité, qui surveille et évalue tous les services en son sein. Les responsables de la sécurité des informations qui constituent l’équipe ne sont pas associés aux services d’ingénierie qui développent Log Analytics. Les responsables de la sécurité ont leur propre chaîne de gestion. Ils effectuent des évaluations indépendantes des produits et services afin de garantir la sécurité et la conformité.

Les fonctionnalités fondamentales de Log Analytics sont fournies par un ensemble de services qui s’exécutent dans Azure. Chaque service assure une fonction de gestion spécifique, et vous pouvez combiner plusieurs services pour mettre en œuvre différents scénarios de gestion.

![Services Azure pour la gestion](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Ces services de gestion ont été conçus dans le cloud. Étant entièrement hébergés dans Azure, ils n’impliquent aucun déploiement ni gestion de ressources locales. La configuration est minimale, et vous pouvez être opérationnel en quelques minutes seulement.

Placez un agent sur n’importe quel ordinateur Windows ou Linux dans votre centre de données, et il enverra des données à Log Analytics. Là, elles peuvent être analysées, en même temps que toutes les autres données collectées à partir des services cloud ou locaux. Utilisez Sauvegarde Azure et Azure Site Recovery pour tirer profit des capacités du cloud en matière de sauvegarde et de haute disponibilité pour vos ressources locales.

![Services de gestion dans le tableau de bord Azure](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Les runbooks figurant dans le cloud ne peuvent généralement pas accéder à vos ressources locales, mais vous pouvez installer un agent sur un ou plusieurs ordinateurs qui hébergeront des runbooks dans votre centre de données. Quand vous démarrez un runbook, vous devez indiquer si vous souhaitez l’exécuter dans le cloud ou sur un Worker local.

Microsoft et ses partenaires mettent à votre disposition différentes solutions que vous pouvez ajouter à votre abonnement Azure pour augmenter la valeur de votre investissement dans Log Analytics. Par exemple, Azure propose des [solutions de gestion](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions), qui sont des ensembles de logiques prépackagés qui implémentent un scénario de gestion à l’aide d’un ou plusieurs services de gestion.

![Galerie de solutions de gestion dans Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

En tant que partenaire, vous pouvez créer vos propres solutions pour prendre en charge vos applications et services et les fournir aux utilisateurs par le biais de la plateforme Place de Marché Azure ou des modèles de démarrage rapide.

## <a name="performance-alerting-and-monitoring"></a>Alertes et monitoring relatifs aux performances

### <a name="alerting"></a>Génération d’alertes

Les alertes sont une méthode de surveillance des métriques de ressources, événements ou journaux Azure. Elles vous avertissent quand une condition que vous avez spécifiée est remplie.

Les alertes sont disponibles dans différents services, notamment :

- **Azure Application Insights** : prend en charge les tests web et les alertes de métriques. Pour plus d’informations, consultez [Configuration d’alertes dans Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) et [Analyse de la disponibilité et de la réactivité d’un site Web](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- **Log Analytics** : prend en charge le routage des journaux de diagnostic et d’activité vers Log Analytics. Il autorise les alertes de métriques, de journaux et autres types d’alertes. Pour plus d’informations, consultez [Alertes dans Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- **Azure Monitor** : prend en charge les alertes sur la base de valeurs de mesures et d’événements de journal d’activité. Vous pouvez utiliser [l’API REST Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx) pour gérer les alertes. Pour plus d’informations, consultez [Utilisation du portail Azure, de PowerShell ou de l’interface de ligne de commande pour créer des alertes](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Surveillance

Les problèmes de performances dans votre application cloud peuvent affecter votre entreprise. Avec plusieurs composants interconnectés et de nouvelles versions fréquentes, des dégradations peuvent se produire à tout moment. Et si vous développez une application, vos utilisateurs découvrent généralement des problèmes que vous n’avez pas identifiés pendant les tests. Vous devez prendre connaissance de ces problèmes immédiatement et disposer d’outils pour les diagnostiquer et les résoudre.

Il existe une gamme d’outils pour la surveillance des services et applications Azure. Certaines de leurs fonctionnalités se chevauchent. C’est en partie dû au flou entre le développement et le fonctionnement d’une application.

Voici les outils principaux :

- **Azure Monitor** est un outil de base pour la surveillance des services s’exécutant sur Azure. Il vous fournit des données au niveau de l’infrastructure sur le débit d’un service et l’environnement. Si vous gérez toutes vos applications dans Azure et décidez d’augmenter ou de diminuer les ressources, Azure Monitor peut vous aider à démarrer.

- **Application Insights** peut être utilisé pour le développement et comme solution de surveillance de la production. Il fonctionne en installant un package dans votre application et vous donne donc une vue plus interne de ce qui se passe. Ses données comprennent les temps de réponse des dépendances, les traces des exceptions, le débogage des captures instantanées et les profils d’exécution. Il offre des outils pour analyser toutes ces données de télémétrie afin de vous aider à déboguer une application et vous aider à comprendre ce que les utilisateurs font avec. Vous pouvez savoir si un pic dans les temps de réponse est dû à un élément dans une application ou à un problème de ressource externe. Si vous utilisez Visual Studio et que l’application est en cause, vous accédez directement à la ligne de code problématique pour la corriger.

- **Log Analytics** est destiné à ceux qui souhaitent optimiser les performances et planifier la maintenance des applications exécutées en production. Il collecte et agrège les données provenant de nombreuses sources, avec un délai de 10 à 15 minutes. Il fournit une solution de gestion informatique globale holistique pour Azure, en local et dans les infrastructures cloud tierces (par exemple, Amazon Web Services). Il fournit des outils pour analyser les données de plusieurs sources, permet d’exécuter des requêtes complexes sur tous les journaux et peut générer des alertes de façon proactive pour des conditions spécifiées. Vous pouvez même collecter des données personnalisées dans son référentiel central, puis interroger et visualiser ces données.

- **System Center Operations Manager** pour gérer et surveiller les installations cloud à grande échelle. Vous le connaissez peut-être déjà comme outil de gestion local pour Windows Server en local et les solutions cloud basées sur Hyper-V, mais il peut également s’intégrer et gérer des applications Azure. Entre autres choses, il peut installer Application Insights sur des applications en direct existantes. Si une application tombe en panne, Operations Manager vous le signale au bout de quelques secondes.


## <a name="next-steps"></a>Étapes suivantes

- [Meilleures pratiques relatives à la création de modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Exemples d’implémentation de la gouvernance des abonnements Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/)
