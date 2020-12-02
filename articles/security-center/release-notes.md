---
title: Notes de publication pour Azure Security Center
description: Description des nouveautés et modifications apportées à Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2020
ms.author: memildin
ms.openlocfilehash: 9b715ea890c7c85161a9e360bc16f9a2a608d64b
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95320991"
---
# <a name="whats-new-in-azure-security-center"></a>Nouveautés d’Azure Security Center

Le Centre de sécurité fait l’objet d’un développement actif et bénéficie d’améliorations en permanence. Pour vous tenir au courant des développements les plus récents, cette page fournit des informations sur les nouvelles fonctionnalités, les correctifs de bogues et les fonctionnalités déconseillées.

Cette page étant mise à jour fréquemment, nous vous invitons donc à la consulter souvent. 

Pour en savoir plus sur les changements *planifiés* qui seront bientôt disponibles dans Security Center, consultez [Changements importants à venir dans Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Si vous recherchez des éléments datant de plus de six mois, vous les trouverez dans l’[Archive des nouveautés d’Azure Security Center](release-notes-archive.md).


## <a name="november-2020"></a>Novembre 2020

Les mises à jour en novembre sont les suivantes :

- [Ajout de 29 recommandations (préversion) pour mieux détailler le benchmark de sécurité Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Ajout de NIST SP 800 171 R2 au tableau de bord de conformité réglementaire Security Center](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [La liste des recommandations comprend désormais des filtres](#recommendations-list-now-includes-filters)
- [Amélioration et développement de l’expérience de provisionnement automatique](#auto-provisioning-experience-improved-and-expanded)
- [Le score sécurisé est désormais disponible dans l’exportation continue (préversion)](#secure-score-is-now-available-in-continuous-export-preview)
- [La recommandation « Les mises à jour système doivent être installées sur vos machines » inclut désormais des sous-recommandations](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Ajout de 29 recommandations (préversion) pour mieux détailler le benchmark de sécurité Azure

Le Benchmark de sécurité Azure est l’ensemble des directives propres à Azure et créées par Microsoft contenant les bonnes pratiques de sécurité et de conformité basées sur les infrastructures de conformité courantes. [En savoir plus sur le Benchmark de sécurité Azure](../security/benchmarks/introduction.md).

Les 29 recommandations suivantes (préversion) ont été ajoutées à Security Center pour mieux détailler ce benchmark.

Les recommandations en préversion ne rendent pas une ressource non saine et ne sont pas incluses dans les calculs de votre degré de sécurisation. Corrigez-les là où c’est possible, de sorte que quand la période de préversion se termine, elles soient prises en compte dans le calcul de votre degré de sécurisation. Découvrez comment répondre à ces recommandations dans [Corriger les recommandations dans Azure Security Center](security-center-remediate-recommendations.md).

| Contrôle de sécurité                     | Nouvelles recommandations                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Chiffrer les données en transit              | - L’application de la connexion SSL doit être activée pour les serveurs de base de données PostgreSQL<br>- L’application de la connexion SSL doit être activée pour les serveurs de base de données MySQL<br>- TLS doit être mis à jour vers la dernière version pour votre application API<br>- TLS doit être mis à jour vers la dernière version pour votre application de fonction<br>- TLS doit être mis à jour vers la dernière version pour votre application web<br>- FTPS doit être exigé dans votre application API<br>- FTPS doit être exigé dans votre application de fonction<br>- FTPS doit être exigé dans votre application web                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Gérer l’accès et les autorisations        | - Les applications web doivent exiger un certificat SSL pour toutes les demandes entrantes<br>- Une identité managée doit être utilisée dans votre application API<br>- Une identité managée doit être utilisée dans votre application de fonction<br>- Une identité managée doit être utilisée dans votre application web                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Restreindre l’accès réseau non autorisé | - Le point de terminaison privé doit être activé pour les serveurs PostgreSQL<br>- Le point de terminaison privé doit être activé pour les serveurs MariaDB<br>- Le point de terminaison privé doit être activé pour les serveurs MySQL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Activer l’audit et la journalisation          | - Les journaux de diagnostic d’App Services doivent être activés                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implémenter les bonnes pratiques de sécurité    | - La Sauvegarde Azure doit être activée pour les machines virtuelles<br>- La sauvegarde géoredondante doit être activée pour Azure Database for MariaDB<br>- La sauvegarde géoredondante doit être activée pour Azure Database pour MySQL<br>- La sauvegarde géoredondante doit être activée pour Azure Database pour PostgreSQL<br>- PHP doit être mis à jour vers la dernière version pour votre application API<br>- PHP doit être mis à jour vers la dernière version pour votre application web<br>- Java doit être mis à jour vers la dernière version pour votre application API<br>- Java doit être mis à jour vers la dernière version pour votre application de fonction<br>- Java doit être mis à jour vers la dernière version pour votre application web<br>- Python doit être mis à jour vers la dernière version pour votre application API<br>- Python doit être mis à jour vers la dernière version pour votre application de fonction<br>- Python doit être mis à jour vers la dernière version pour votre application web<br>- La conservation des audits pour les serveurs SQL Server doit être définie sur au moins 90 jours |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Liens connexes :

- [En savoir plus sur le Benchmark de sécurité Azure](../security/benchmarks/introduction.md)
- [En savoir plus sur les applications API Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [En savoir plus sur les applications de fonction Azure](../azure-functions/functions-overview.md)
- [En savoir plus sur les applications web Azure](../app-service/overview.md)
- [En savoir plus sur Azure Database for MariaDB](../mariadb/overview.md)
- [En savoir plus sur Azure Database pour MySQL](../mysql/overview.md)
- [En savoir plus sur Azure Database pour PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>Ajout de NIST SP 800 171 R2 au tableau de bord de conformité réglementaire Security Center

Le standard NIST SP 800-171 R2 est désormais disponible sous la forme d’une initiative intégrée à utiliser avec le tableau de bord de conformité réglementaire Azure Security Center. Les mappages des contrôles sont décrits dans [Détails de l’initiative intégrée de conformité réglementaire NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md). 

Pour appliquer le standard à vos abonnements et superviser votre état de conformité de façon continue, suivez les instructions fournies dans [Personnalisation de l’ensemble de normes du tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Standard NIST SP 800 171 R2 dans le tableau de bord de conformité réglementaire Security Center":::

Pour plus d’informations sur cette norme de conformité, consultez [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>La liste des recommandations comprend désormais des filtres

Vous pouvez maintenant filtrer la liste des recommandations de sécurité en fonction d’une plage de critères. Dans l’exemple suivant, la liste des recommandations a été filtrée pour afficher celles qui :

- sont **généralement disponibles** (c’est-à-dire pas en préversion)
- concernent les **comptes de stockage**
- prennent en charge la **correction rapide**

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtres pour la liste des recommandations":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Amélioration et développement de l’expérience de provisionnement automatique

La fonctionnalité de provisionnement automatique permet de réduire la surcharge de gestion en installant les extensions requises sur les machines virtuelles Azure nouvelles et existantes, afin qu’elles puissent tirer parti des protections de Security Center. 

À mesure qu’Azure Security Center se développe, davantage d’extensions ont été développées et Security Center peut superviser une liste plus importante de types de ressources. Les outils de provisionnement automatique ont été développés pour prendre en charge d’autres extensions et types de ressources en tirant parti des fonctionnalités d’Azure Policy.

Vous pouvez maintenant configurer le provisionnement automatique des éléments suivants :

- Agent Log Analytics
- (Nouveau) Module complémentaire Azure Policy pour Kubernetes
- (Nouveau) Microsoft Dependency Agent

Pour en savoir plus, consultez [Provisionnement automatique d’agents et d’extensions à partir d’Azure Security Center](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Le score sécurisé est désormais disponible dans l’exportation continue (préversion)

Avec l’exportation continue du score sécurisé, vous pouvez diffuser en streaming les modifications apportées à votre score en temps réel vers Azure Event Hubs ou un espace de travail Log Analytics. Utilisez cette fonctionnalité pour :

- effectuer le suivi de votre score sécurisé au fur et à mesure avec des rapports dynamiques
- exporter les données de score sécurisé vers Azure Sentinel (ou tout autre système SIEM)
- intégrer ces données à tous les processus que vous utilisez peut-être déjà pour surveiller le score sécurisé dans votre organisation

Apprenez-en plus sur la façon d’[exporter en continu des données Security Center](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations"></a>La recommandation « Les mises à jour système doivent être installées sur vos machines » inclut désormais des sous-recommandations

La recommandation **Les mises à jour système doivent être installées sur vos machines** a été améliorée. La nouvelle version inclut des sous-recommandations pour chaque mise à jour manquante et apporte les améliorations suivantes :

- Une expérience repensée dans les pages Azure Security Center du portail Azure. La page Détails de la recommandation pour **Les mises à jour système doivent être installées sur vos machines** comprend la liste des résultats, comme illustré ci-dessous. Lorsque vous sélectionnez une recherche unique, le volet d’informations s’ouvre avec un lien vers des informations de correction et une liste de ressources affectées.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Ouverture de l’une des sous-recommandations dans l’expérience du portail pour la recommandation mise à jour":::

- Des données enrichies pour la recommandation provenant d’Azure Resource Graph (ARG). ARG est un service Azure conçu pour fournir une exploration efficace des ressources. Vous pouvez utiliser ARG pour effectuer une requête à grande échelle sur un ensemble d’abonnements donné, et ainsi gérer de façon optimale votre environnement. 

    Pour Azure Security Center, vous pouvez utiliser ARG et le [langage de requête Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) pour interroger un large éventail de données relatives à la posture de sécurité.

    Avant, si vous interrogiez cette recommandation dans ARG, les seules informations disponibles étaient que la recommandation devait être corrigée sur une machine. La requête suivante de la version améliorée retournera toutes les mises à jour système manquantes, regroupées par machine.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```



## <a name="october-2020"></a>Octobre 2020

Les mises à jour d’octobre sont les suivantes :
- [Évaluation des vulnérabilités pour les machines locales et multi-cloud (préversion)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Ajout d’une recommandation concernant le pare-feu Azure (préversion)](#azure-firewall-recommendation-added-preview)
- [Des plages d’adresses IP autorisées doivent être définies sur les services Kubernetes - Recommandation mise à jour avec correctif rapide](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Le tableau de bord de conformité réglementaire comprend désormais une option de suppression des normes](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Suppression de la table Microsoft.Security/securityStatuses d’Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Évaluation des vulnérabilités pour les machines locales et multi-cloud (préversion)

L’analyseur d’évaluation des vulnérabilités intégré d’[Azure Defender pour les serveurs](defender-for-servers-introduction.md) (fourni par Qualys) analyse désormais les serveurs avec Azure Arc.

Une fois que vous avez activé Azure Arc sur vos machines non-Azure, Security Center propose de déployer l’analyseur de vulnérabilité intégré dessus, manuellement et à grande échelle.

Avec cette mise à jour, vous pouvez libérer la puissance d’**Azure Defender pour les serveurs** afin de consolider votre programme de gestion des vulnérabilités dans l’ensemble de vos ressources Azure et non-Azure.

Principales fonctionnalités :

- Supervision de l’état de provisionnement de l’analyseur d’évaluation des vulnérabilités sur les machines Azure Arc
- Provisionnement de l’agent d’évaluation des vulnérabilités intégré sur des machines Windows Azure Arc Windows et Linux non protégées (manuellement et à grande échelle)
- Réception et analyse des vulnérabilités détectées par les agents déployés (manuellement et à grande échelle)
- Expérience unifiée pour les machines virtuelles Azure et Azure Arc

[Découvrez-en plus sur le déploiement de l’analyseur de vulnérabilité intégré sur vos machines hybrides](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Découvrez-en plus sur les serveurs avec Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/).


### <a name="azure-firewall-recommendation-added-preview"></a>Ajout d’une recommandation concernant le pare-feu Azure (préversion)

Une nouvelle recommandation a été ajoutée pour protéger tous vos réseaux virtuels à l’aide du pare-feu Azure.

La recommandation **Les réseaux virtuels doivent être protégés par le pare-feu Azure** vous conseille de limiter l’accès à vos réseaux virtuels et d’éviter les menaces potentielles à l’aide du pare-feu Azure.

Découvrez le [Pare-feu Azure](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Des plages d’adresses IP autorisées doivent être définies sur les services Kubernetes - Recommandation mise à jour avec correctif rapide

La recommandation **Des plages d’adresses IP autorisées doivent être définies sur les services Kubernetes** a maintenant une option de correctif rapide.

Pour plus d’informations sur cette recommandation et toutes les autres recommandations Security Center, consultez [Recommandations de sécurité - Guide de référence](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Des plages d’adresses IP autorisées doivent être définies sur les services Kubernetes - Recommandation avec l’option de correctif rapide":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Le tableau de bord de conformité réglementaire comprend désormais une option de suppression des normes

Le tableau de bord de conformité réglementaire fournit des insights sur votre posture de conformité d’après la façon dont vous répondez à des exigences et contrôles de conformité spécifiques.

Le tableau de bord comprend un ensemble de normes réglementaires par défaut. Si certaines des normes fournies ne sont pas pertinentes pour votre organisation, vous pouvez maintenant les supprimer facilement de l’interface utilisateur pour un abonnement. Les normes ne peuvent être supprimées qu’au niveau de l’*abonnement*, et non à l’étendue du groupe d’administration.

Pour en savoir plus, consultez [Suppression d’une norme de votre tableau de bord](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Suppression de la table Microsoft.Security/securityStatuses d’Azure Resource Graph (ARG)

Azure Resource Graph est un service d’Azure conçu pour fournir une exploration efficace des ressources avec la possibilité de lancer des requêtes à grande échelle sur un ensemble donné d’abonnements pour vous permettre d’optimiser la gestion de votre environnement. 

Pour Azure Security Center, vous pouvez utiliser ARG et le [langage de requête Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) pour interroger un large éventail de données relatives à la posture de sécurité. Par exemple :

- L’inventaire des ressources utilise Azure Resource Graph (ARG)
- Nous avons documenté un exemple de requête ARG pour savoir comment [identifier les comptes sans authentification multifacteur (MFA) activée](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

Dans ARG, il existe des tables de données que vous pouvez utiliser dans vos requêtes.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Explorateur Azure Resource Graph et les tables disponibles":::

> [!TIP]
> La documentation ARG liste toutes les tables disponibles dans [Informations de référence sur les types de ressource et les tables Azure Resource Graph](../governance/resource-graph/reference/supported-tables-resources.md).

À compter de cette mise à jour, la table **Microsoft.Security/securityStatuses** a été supprimée. L’API securityStatuses est toujours disponible.

Le remplacement de données peut être utilisé par la table Microsoft.Security/Assessments.

La principale différence entre Microsoft.Security/securityStatuses et Microsoft.Security/Assessments est que la première montre l’agrégation des évaluations tandis que la deuxième contient un enregistrement unique pour chacune d’entre elles.

Par exemple, Microsoft. Microsoft.Security/securityStatuses retourne un résultat avec un tableau de deux valeurs policyAssessments :

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Tandis que Microsoft.Security/Assessments contient un enregistrement pour chaque évaluation de stratégie de ce type, comme suit :

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Exemple de conversion d’une requête ARG existante à l’aide de securityStatuses pour utiliser à présent la table des évaluations (Assessments) :**

Requête qui référence securityStatuses :

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Requête de remplacement pour la table Assessments :

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Pour en savoir plus, consultez les liens suivants :
- [Procédure pour créer des requêtes avec l’Explorateur Azure Resource Graph](../governance/resource-graph/first-query-portal.md)
- [Langage de requête Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>Septembre 2020

Les mises à jour en septembre sont les suivantes :
- [Security Center fait peau neuve !](#security-center-gets-a-new-look)
- [Publication d’Azure Defender](#azure-defender-released)
- [Azure Defender pour Key Vault est mis à la disposition générale](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender pour la protection du stockage des fichiers et d’ADLS Gen2 est mis à la disposition générale](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Les outils d’inventaire des ressources sont désormais mis à la disposition générale](#asset-inventory-tools-are-now-generally-available)
- [Désactiver le résultat d’une vulnérabilité précise pour les images de registres de conteneurs et les machines virtuelles](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Exempter une ressource d’une recommandation](#exempt-a-resource-from-a-recommendation)
- [Les connecteurs AWS et GCP dans Security Center apportent une expérience multicloud](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Bundle Kubernetes de recommandations sur la protection des charges de travail](#kubernetes-workload-protection-recommendation-bundle)
- [Les résultats de l’évaluation des vulnérabilités sont désormais disponibles dans l’exportation continue](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Empêcher les erreurs de configurations de sécurité en appliquant des recommandations lors de la création de nouvelles ressources](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Recommandations de groupe de sécurité réseau améliorées](#network-security-group-recommendations-improved)
- [Recommandation de la préversion AKS déconseillée : « Des stratégies de sécurité de pods doivent être définies sur les services Kubernetes »](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Notifications par e-mail améliorées dans Azure Security Center](#email-notifications-from-azure-security-center-improved)
- [Le degré de sécurisation n’inclut pas de recommandations sur la préversion](#secure-score-doesnt-include-preview-recommendations)
- [Les recommandations incluent désormais un indicateur de gravité et l’intervalle d’actualisation](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center fait peau neuve !

Nous avons publié une interface utilisateur actualisée pour les pages du portail de Security Center. Les nouvelles pages incluent une nouvelle page vue d’ensemble, ainsi que des tableaux de bord pour le degré de sécurisation, l’inventaire des ressources et Azure Defender.

La page vue d’ensemble repensée présente désormais une vignette permettant d’accéder aux tableaux de bord du degré de sécurisation, de l’inventaire des ressources et d’Azure Defender. Elle comporte également une vignette qui renvoie au tableau de bord de conformité réglementaire.

En savoir plus sur la [page vue d’ensemble](overview-page.md).


### <a name="azure-defender-released"></a>Publication d’Azure Defender

**Azure Defender** est une plate-forme de protection de la charge de travail Cloud (CWPP) intégrée à Security Center pour une protection intelligente et avancée de vos charges de travail Azure et hybrides. Elle remplace l’option de niveau tarifaire standard de Security Center. 

Lorsque vous activez Azure Defender à partir de la zone **Tarification et paramètres** d’Azure Security Center, les plans Defender suivants sont tous activés simultanément et fournissent des défenses complètes pour les couches de calcul, de données et de service de votre environnement :

- [Azure Defender pour les serveurs](defender-for-servers-introduction.md)
- [Azure Defender pour App Service](defender-for-app-service-introduction.md)
- [Azure Defender pour Stockage](defender-for-storage-introduction.md)
- [Azure Defender pour SQL](defender-for-sql-introduction.md)
- [Azure Defender pour Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender pour Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender pour les registres de conteneurs](defender-for-container-registries-introduction.md)

Chacun de ces plans est expliqué séparément dans la documentation relative à Security Center.

Avec ses tableaux de bord dédiés, Azure Defender propose des alertes de sécurité ainsi que la protection avancée contre les menaces pour les machines virtuelles, les bases de données SQL, les conteneurs, les applications web, votre réseau, et bien plus encore.

[En savoir plus sur Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender pour Key Vault est mis à la disposition générale

Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement comme les certificats, chaînes de connexion et mots de passe. 

**Azure Defender pour Key Vault** fournit une protection native Azure avancée contre les menaces pour Azure Key Vault, apportant une couche supplémentaire de renseignements de sécurité. Par extension, Azure Defender pour Key Vault protège de nombreuses ressources dépendantes de vos comptes de Key Vault.

Le plan facultatif est désormais en disponibilité générale. Dans la préversion, cette fonctionnalité s’appelait « protection avancée contre les menaces pour Azure Key Vault ».

En outre, les pages de Key Vault dans le portail Azure comprennent désormais une page dédiée à la **Sécurité** pour gérer les recommandations et les alertes de **Security Center**.

Consultez [Azure Defender pour Key Vault](defender-for-key-vault-introduction.md) pour en savoir plus.


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender pour la protection du stockage des fichiers et d’ADLS Gen2 est mis à la disposition générale 

**Azure Defender pour Stockage** détecte les activités potentiellement dangereuses sur vos comptes de stockage Azure. Vos données peuvent être protégées, qu’elles soient stockées en tant que conteneurs blob, de partages de fichiers ou de lacs de données.

Le support d’[Azure Files](../storage/files/storage-files-introduction.md) et d’[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) est désormais mis à la disposition générale.

À partir du 1er octobre 2020, nous commencerons à facturer la protection des ressources sur ces services.

Consultez [Azure Defender pour Stockage](defender-for-storage-introduction.md) pour en savoir plus.


### <a name="asset-inventory-tools-are-now-generally-available"></a>Les outils d’inventaire des ressources sont désormais mis à la disposition générale

La page d’inventaire des ressources d’Azure Security Center fournit une page unique pour visualiser la posture de sécurité des ressources que vous avez connectées à Security Center.

Security Center analyse périodiquement l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités de sécurité potentielles. Il fournit ensuite des recommandations sur la façon de corriger ces vulnérabilités.

Lorsqu’une ressource contient des recommandations en suspens, celles-ci apparaissent dans l’inventaire.

Consultez [Explorez et gérez vos ressources à l’aide des outils d’inventaire et de gestion des ressources](asset-inventory.md) pour en savoir plus.



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Désactiver le résultat d’une vulnérabilité précise pour les images de registres de conteneurs et les machines virtuelles

Azure Defender comprend des analyseurs de vulnérabilités pour examiner les images dans votre Azure Container Registry et vos machines virtuelles.

Si votre organisation préfère ignorer un résultat, plutôt que de le corriger, vous pouvez éventuellement désactiver cette fonction. Les résultats désactivés n’ont pas d’impact sur votre Niveau de sécurité ni ne génèrent de bruit indésirable.

Lorsqu’un résultat correspondra aux critères que vous avez définis dans vos règles de désactivation, il n’apparaîtra plus dans la liste des résultats.

Cette option est disponible dans les pages « Détails des recommandations » pour :

- **Les vulnérabilités dans les images Azure Container Registry doivent être corrigées**
- **Les vulnérabilités de vos machines virtuelles doivent être corrigées**

Pour plus d’informations, consultez [Désactiver des résultats spécifiques pour vos images de conteneur](defender-for-container-registries-usage.md#disable-specific-findings-preview) et [Désactiver des résultats spécifiques pour vos machines virtuelles](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Exempter une ressource d’une recommandation

Il peut arriver qu’une ressource soit signalée comme non saine en ce qui concerne une recommandation spécifique (faisant baisser par conséquent le degré de sécurisation), même si vous estimez qu’elle ne devrait pas l’être. Elle a peut-être été corrigée par un processus non suivi par Security Center. Ou bien votre organisation a décidé d’accepter le risque pour cette ressource spécifique. 

Dans ce cas, vous pouvez créer une règle d’exemption et veiller à ce que la ressource ne figure plus parmi les ressources non saines à l’avenir. Ces règles peuvent contenir des justifications documentées comme décrit ci-dessous.

Pour plus d’informations, consultez [Exempter une ressource des recommandations et du degré de sécurisation](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Les connecteurs AWS et GCP dans Security Center apportent une expérience multicloud

Les charges de travail cloud couvrant généralement plusieurs plates-formes cloud, les services de sécurité cloud se doivent d’en faire de même.

Azure Security Center protège désormais les charges de travail dans Azure, Amazon Web Services (AWS) et Google Cloud Platform (GCP).

L’intégration de vos comptes AWS et GCP dans Security Center intègre Azure Security Hub, GCP Security Command et Azure Security Center. 

Pour plus d’informations, consultez [Connecter vos comptes AWS à Azure Security Center](quickstart-onboard-aws.md) et [Connecter vos comptes GCP à Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Bundle Kubernetes de recommandations sur la protection des charges de travail

Pour vous assurer que les charges de travail Kubernetes sont sécurisées par défaut, Security Center ajoute des recommandations de renforcement au niveau de Kubernetes, y compris des options de mise en œuvre avec le contrôle d’admission Kubernetes.

Lorsque vous avez installé le module complémentaire Azure Policy pour Kubernetes sur votre cluster AKS, toutes les requêtes adressées au serveur d’API Kubernetes sont analysées par rapport à l’ensemble prédéfini de meilleures pratiques avant d’être conservées sur le cluster. Vous pouvez ensuite configurer la mise en œuvre des meilleures pratiques et les imposer aux charges de travail futures.

Par exemple, vous pouvez interdire la création de conteneurs privilégiés, et faire en sorte que toutes les demandes ultérieures soient bloquées.

Consultez [Meilleures pratiques de protection de charge de travail à l’aide du contrôle d’admission Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control) pour en savoir plus.


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Les résultats de l’évaluation des vulnérabilités sont désormais disponibles dans l’exportation continue

Utilisez l’exportation continue pour diffuser vos alertes et vos recommandations en temps réel vers Azure Event Hubs, les espaces de travail Log Analytics ou Azure Monitor. À partir de là, vous pouvez intégrer ces données à des informations de sécurité et gestion d’événements (par exemple, Azure Sentinel, Power BI, Azure Data Explorer et bien plus.)

Les outils d’évaluation des vulnérabilités intégrés à Security Center renvoient des résultats sur vos ressources comme recommandations exploitables dans le cadre d’une recommandation « parent », telle que « les vulnérabilités de vos machines virtuelles doivent être corrigées ». 

Les résultats de sécurité sont désormais disponibles pour l’exportation via l’exportation continue lorsque vous sélectionnez recommandations et activez l’option **Inclure les résultats de sécurité**.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Intégrer les résultats de sécurité dans la configuration de l’exportation continue" :::

Pages connexes :

- [Solution intégrée d’évaluation des vulnérabilités de Security Center pour les machines virtuelles Azure](deploy-vulnerability-assessment-vm.md)
- [Solution d’évaluation des vulnérabilités intégrée à Security Center pour les images Azure Container Registry](defender-for-container-registries-usage.md)
- [Exportation continue](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Empêcher les erreurs de configurations de sécurité en appliquant des recommandations lors de la création de nouvelles ressources

Les erreurs de configuration de la sécurité sont à l’origine de la plupart des incidents. Security Center vous permet désormais de *prévenir* les erreurs de configurations des nouvelles ressources en ce qui concerne les recommandations spécifiques. 

Cette fonctionnalité peut vous aider à sécuriser vos charges de travail et à stabiliser votre degré de sécurisation.

La mise en œuvre d’une configuration sécurisée, basée sur une recommandation spécifique, est proposée en deux modes :

- En utilisant l’effet **Refuser** d’Azure Policy, vous pouvez empêcher la création de ressources non saines

- À l’aide de l’option **Appliquer** , vous pouvez tirer parti de l’effet **DeployIfNotExist** de la stratégie Azure et corriger automatiquement les ressources non saines lors de leur création
 
Cette option est disponible pour les recommandations de sécurité sélectionnées et se trouve en haut de la page Détails de la ressource.

Pour plus d’informations, consultez [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Recommandations de groupe de sécurité réseau améliorées

Les recommandations de sécurité suivantes relatives aux groupes de sécurité réseau ont été améliorées pour réduire certaines instances de faux positifs.

- Tous les ports réseau doivent être limités sur le groupe de sécurité réseau associé à votre machine virtuelle
- Les ports de gestion doivent être fermés sur vos machines virtuelles
- Les machines virtuelles accessibles à partir d’Internet doivent être protégées avec des groupes de sécurité réseau
- Les sous-réseaux doivent être associés à un groupe de sécurité réseau


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Recommandation de la préversion AKS déconseillée : « Des stratégies de sécurité de pods doivent être définies sur les services Kubernetes »

La recommandation de préversion « Des stratégies de sécurité de pods doivent être définies sur les services Kubernetes » est déconseillée comme décrit dans la documentation [Azure Kubernetes Service](../aks/use-pod-security-policies.md).

La fonctionnalité « stratégie de sécurité des pods (préversion) », sera bientôt déconseillée et ne sera plus disponible après le 15 octobre 2020 pour laisser place à Azure Policy pour AKS.

Une fois que la stratégie de sécurité des pods (préversion) sera déconseillée, vous devrez désactiver la fonctionnalité sur tous les clusters existants à l’aide de la fonctionnalité déconseillée pour effectuer les futures mises à niveau de cluster et continuer à bénéficier du support Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Notifications par e-mail améliorées dans Azure Security Center

Les zones suivantes des e-mails concernant les alertes de sécurité ont été améliorées : 

- ajout de la possibilité d’envoyer des notifications par e-mail concernant les alertes pour tous les niveaux de gravité
- La possibilité d’informer les utilisateurs avec différents rôles Azure sur l’abonnement a été ajoutée
- Nous avertissons de manière proactive les propriétaires d’abonnements par défaut sur les alertes de gravité élevée (qui ont une probabilité élevée d’être des violations authentiques)
- Nous avons supprimé le champ du numéro de téléphone de la page de configuration des notifications par e-mail

Consultez [Configurer les notifications par e-mail pour les alertes de sécurité](security-center-provide-security-contact-details.md) pour en savoir plus.


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Le degré de sécurisation n’inclut pas de recommandations sur la préversion 

Security Center évalue continuellement vos ressources, vos abonnements et votre organisation en recherchant d’éventuels problèmes de sécurité. Il agrège ensuite toutes ses découvertes sous la forme d’un score qui vous permet de déterminer d’un coup d’œil votre niveau de sécurité actuel : plus le score est élevé, plus le niveau de risque identifié est faible.

À mesure que de nouvelles menaces sont découvertes, de nouveaux conseils en matière de sécurité sont mis à disposition dans Security Center via la création de recommandations. Pour éviter que des modifications ne soient apportées à votre degré de sécurisation et pour fournir une période de grâce pendant laquelle vous pouvez découvrir de nouvelles recommandations avant qu’elles n’aient un impact sur vos scores, les recommandations marquées **Preview (Préversion)** ne sont plus incluses dans les calculs du degré de sécurisation. Elles doivent tout de même être corrigées dans la mesure du possible, ainsi lorsque la période de préversion se termine, elles seront prises en compte dans le calcul.

En outre, les recommandations **Preview (Préversion)** de préversion n’affichent pas de ressources « non saines ».

Exemple de recommandation de préversion :

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recommandation portant l’indicateur Preview (Préversion)":::

[En savoir plus sur le degré de sécurisation](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Les recommandations incluent désormais un indicateur de gravité et l’intervalle d’actualisation

La page de détails des recommandations comprend désormais un indicateur d’intervalle d’actualisation (le cas échéant) et affiche clairement la gravité de la recommandation.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Page de recommandation affichant l’actualisation et la gravité":::



## <a name="august-2020"></a>Août 2020

Les mises à jour en août sont les suivantes :

- [Inventaire des ressources : nouvelle vue puissante de la position de sécurité de vos ressources](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Ajout de la prise en charge des paramètres de sécurité par défaut d’Azure Active Directory (pour l’authentification multifacteur)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Ajout d’une recommandation en faveur des principaux de service](#service-principals-recommendation-added)
- [Évaluation des vulnérabilités sur les machines virtuelles - recommandations et stratégies consolidées](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nouvelles stratégies de sécurité AKS ajoutées à ASC_default initiative : pour une utilisation par les clients de la préversion privée uniquement](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventaire des ressources : nouvelle vue puissante de la position de sécurité de vos ressources

L’inventaire des ressources de Security Center (actuellement en préversion) permet de visualiser la posture de sécurité des ressources que vous avez connectées à Security Center.

Security Center analyse périodiquement l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités de sécurité potentielles. Il fournit ensuite des recommandations sur la façon de corriger ces vulnérabilités. Lorsqu’une ressource contient des recommandations en suspens, celles-ci apparaissent dans l’inventaire.

Vous pouvez utiliser la vue et ses filtres pour explorer les données relatives à votre posture de sécurité et prendre d’autres mesures en fonction de vos conclusions.

En savoir plus sur l’[inventaire des ressources](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Ajout de la prise en charge des paramètres de sécurité par défaut d’Azure Active Directory (pour l’authentification multifacteur)

Security Center a ajouté la prise en charge complète des [paramètres de sécurité par défaut](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), les protections gratuites de Microsoft en matière de sécurité de l’identité.

Les paramètres de sécurité par défaut fournissent des paramètres de sécurité d’identité préconfigurés pour défendre votre organisation contre les attaques courantes liées aux identités. Les paramètres de sécurité par défaut protègent déjà plus de 5 millions de locataires ; 50 000 locataires sont également protégés par Security Center.

Security Center fournit désormais une recommandation de sécurité chaque fois qu’il identifie un abonnement Azure sans activation des paramètres de sécurité par défaut. Jusqu’à présent, Security Center recommandait d’activer l’authentification multifacteur à l’aide de l’accès conditionnel, qui fait partie de la licence Premium d’Azure Active Directory (AD). Pour les clients qui utilisent Azure AD gratuitement, nous vous recommandons maintenant d’activer les paramètres de sécurité par défaut. 

Notre objectif est d’encourager un plus grand nombre de clients à sécuriser leurs environnements cloud grâce à l’authentification multifacteur et d’atténuer l’un des risques les plus élevés, qui est également le plus important pour votre [score de sécurité](secure-score-security-controls.md).

En savoir plus sur les [paramètres de sécurité par défaut](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Ajout d’une recommandation en faveur des principaux de service

Une nouvelle recommandation a été ajoutée pour recommander aux clients de Security Center qui utilisent des certificats de gestion pour gérer leurs abonnements de basculer vers des principaux de service.

La recommandation **Des principaux de service doivent être utilisés pour protéger vos abonnements à la place des certificats de gestion** vous conseille d’utiliser des principaux de service ou Azure Resource Manager de gérer vos abonnements de manière plus sécurisée. 

En savoir plus sur [Objets du principal du service et de l’application dans Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Évaluation des vulnérabilités sur les machines virtuelles - recommandations et stratégies consolidées

Security Center inspecte vos machines virtuelles pour vérifier si elles exécutent une solution d’évaluation des vulnérabilités. Si aucune solution d’évaluation des vulnérabilités n’est trouvée, Security Center fournit une recommandation pour simplifier le déploiement.

Lorsque des vulnérabilités sont détectées, Security Center fournit une recommandation résumant les résultats à examiner et à corriger si nécessaire.

Pour garantir une expérience cohérente pour tous les utilisateurs, quel que soit le type d’analyse utilisé, nous avons unifié quatre recommandations dans les deux cas suivants :

|Recommandation unifiée|Description de la modification|
|----|:----|
|**Une solution d’évaluation des vulnérabilités doit être activée sur vos machines virtuelles**|Remplace les deux recommandations suivantes :<br> **•** Activer la solution intégrée d’évaluation des vulnérabilités sur les machines virtuelles (par Qualys) (désormais déconseillée) (incluse avec le niveau standard)<br> **•** La solution d’évaluation des vulnérabilités doit être installée sur vos machines virtuelles (désormais déconseillée) (niveaux standard et gratuits)|
|**Les vulnérabilités de vos machines virtuelles doivent être corrigées**|Remplace les deux recommandations suivantes :<br>**•** Corriger les vulnérabilités trouvées sur vos machines virtuelles (avec Qualys) (à présent déconseillée)<br>**•** Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités (à présent déconseillée)|
|||

Vous allez maintenant utiliser la même recommandation pour déployer l’extension d’évaluation de la vulnérabilité de Security Center ou une solution sous licence privée (« BYOL ») d’un partenaire tel que Qualys ou Rapid7.

De plus, lorsque des vulnérabilités sont détectées et signalées à Security Center, une recommandation unique vous avertit des conclusions, quelle que soit la solution d’évaluation des vulnérabilités qui les a identifiées.

#### <a name="updating-dependencies"></a>Mise à jour des dépendances

Si vous avez des scripts, des requêtes ou des automations qui font référence aux recommandations ou aux clés/noms de stratégie précédents, utilisez les tableaux ci-dessous pour mettre à jour les références :

##### <a name="before-august-2020"></a>Avant août 2020

|Recommandation|Étendue|
|----|:----|
|**Activer la solution intégrée d’évaluation des vulnérabilités sur les machines virtuelles (par Qualys)**<br>Clé : 550e890b-e652-4d22-8274-60b3bdb24c63|Intégré|
|**Corriger les vulnérabilités trouvées sur vos machines virtuelles (avec Qualys)**<br>Clé : 1195afff-c881-495e-9bc5-1486211ae03f|Intégré|
|**La solution d’évaluation des vulnérabilités doit être installée sur vos machines virtuelles**<br>Clé : 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités**<br>Clé : 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Policy|Étendue|
|----|:----|
|**L’évaluation des vulnérabilités doit être activée sur les machines virtuelles**<br>ID de stratégie : 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Intégré|
|**Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités**<br>ID de stratégie : 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>À partir d’août 2020

|Recommandation|Étendue|
|----|:----|
|**Une solution d’évaluation des vulnérabilités doit être activée sur vos machines virtuelles**<br>Clé : ffff0522-1e88-47fc-8382-2a80ba848f5d|Intégré + BYOL|
|**Les vulnérabilités de vos machines virtuelles doivent être corrigées**<br>Clé : 1195afff-c881-495e-9bc5-1486211ae03f|Intégré + BYOL|
||||

|Policy|Étendue|
|----|:----|
|[**L’évaluation des vulnérabilités doit être activée sur les machines virtuelles**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID de stratégie : 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Intégré + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nouvelles stratégies de sécurité AKS ajoutées à ASC_default initiative : pour une utilisation par les clients de la préversion privée uniquement

Pour vous assurer que les charges de travail Kubernetes sont sécurisées par défaut, Security Center ajoute des stratégies de niveau Kubernetes et des suggestions de renforcement, y compris des options de mise en œuvre avec le contrôle d’admission Kubernetes.

La première phase de ce projet comprend une préversion privée et l’ajout de nouvelles stratégies (désactivées par défaut) à l’initiative ASC_default.

Vous pouvez ignorer ces stratégies en toute sécurité et il n’y aura aucun impact sur votre environnement. Si vous souhaitez les activer, inscrivez-vous à la préversion sur https://aka.ms/SecurityPrP et sélectionnez à partir des options suivantes :

1. **Préversion unique** : pour rejoindre uniquement cette préversion privée. Mentionnez explicitement « Analyse continu ASC » comme préversion que vous souhaitez rejoindre.
1. **Programme en cours** : à ajouter à cette préversion privée et aux prochaines. Vous devrez remplir un profil et un accord de confidentialité.


## <a name="july-2020"></a>Juillet 2020

Les mises à jour du mois de juillet incluent :
- [L’évaluation des vulnérabilités des machines virtuelles est désormais disponible pour les images autres que celles du marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Protection contre les menaces pour Stockage Azure étendue pour inclure Azure Files et Azure Data Lake Storage Gen2 (préversion)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Huit nouvelles recommandations pour activer les fonctionnalités de protection contre les menaces](#eight-new-recommendations-to-enable-threat-protection-features)
- [Améliorations de la sécurité des conteneurs – Analyse du registre plus rapide et documentation actualisée](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Mise à jour des contrôles d’application adaptatifs avec une nouvelle recommandation et la prise en charge des caractères génériques dans les règles de chemin d’accès](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Dépréciation de six stratégies pour la sécurité avancée des données SQL](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>L’évaluation des vulnérabilités des machines virtuelles est désormais disponible pour les images non-Place de marché

Lors du déploiement d’une solution d’évaluation des vulnérabilités, Security Center effectuait précédemment un contrôle de validation préalable. Le contrôle visait à vérifier une référence (SKU) de la Place de marché sur la machine virtuelle de destination. 

À partir de cette mise à jour, le contrôle a été supprimé et vous pouvez désormais déployer les outils d’évaluation des vulnérabilités sur des machines Windows et Linux « personnalisées ». Les images personnalisées sont celles que vous avez modifiées à partir des images par défaut de la Place de marché.

Bien que vous puissiez désormais déployer l’extension d’évaluation des vulnérabilités intégrée (optimisée par Qualys) sur de nombreuses autres machines, le support n’est disponible que si vous utilisez un système d’exploitation répertorié dans [Déployer l’analyseur de vulnérabilité intégré sur des machines virtuelles de niveau Standard](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

Apprenez-en davantage sur l’[analyseur de vulnérabilité intégré pour machines virtuelles (Azure Defender requis)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Pour en savoir plus sur l’utilisation de votre propre solution d’évaluation des vulnérabilités sous licence privée de Qualys ou Rapid7, consultez [Déploiement d’une solution d’analyse des vulnérabilités des partenaires](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Protection contre les menaces pour Stockage Azure étendue pour inclure Azure Files et Azure Data Lake Storage Gen2 (préversion)

La protection contre les menaces pour Stockage Azure détecte les activités potentiellement dangereuses sur vos comptes Stockage Azure. Security Center affiche des alertes lorsqu’il détecte des tentatives d’accès ou d’exploitation de vos comptes de stockage. 

Vos données peuvent être protégées, qu’elles soient stockées en tant que conteneurs blob, de partages de fichiers ou de lacs de données.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Huit nouvelles recommandations pour activer les fonctionnalités de protection contre les menaces

Huit nouvelles recommandations ont été ajoutées pour fournir un moyen simple d’activer les fonctionnalités de protection contre les menaces d’Azure Security Center pour les types de ressources suivants : machines virtuelles, plans App Service, serveurs Azure SQL Database, serveurs SQL Server, comptes de Stockage Azure, clusters Azure Kubernetes Service, registres Azure Container Registry et coffres Azure Key Vault.

Les nouvelles recommandations sont les suivantes :

- **Advanced Data Security doit être activé sur les serveurs Azure SQL Database**
- **Advanced Data Security doit être activé sur les serveurs SQL sur les machines**
- **Advanced Threat Protection doit être activé sur les plans Azure App Service**
- **Advanced Threat Protection doit être activé sur les registres Azure Container Registry**
- **Advanced Threat Protection doit être activé sur les coffres Azure Key Vault**
- **Advanced Threat Protection doit être activé sur les clusters Azure Kubernetes Service**
- **Advanced Threat Protection doit être activé sur les comptes Stockage Azure**
- **Advanced Threat Protection doit être activé sur les machines virtuelles**

Ces nouvelles recommandations sont associées au contrôle de sécurité **Activer Azure Defender**.

Les recommandations incluent également la fonctionnalité de correction rapide. 

> [!IMPORTANT]
> L’application de n’importe laquelle de ces recommandations entraîne des frais pour la protection des ressources pertinentes. Ces frais s’appliquent immédiatement si vous avez des ressources associées dans l’abonnement actuel. Ou ils s’appliqueront à l’avenir si vous les ajoutez à une date ultérieure.
> 
> Par exemple, si vous n’avez pas de clusters Azure Kubernetes Service dans votre abonnement et que vous activez la protection contre les menaces, cela n’entraîne pas de frais. Si, à l’avenir, vous ajoutez un cluster sur le même abonnement, il sera automatiquement protégé et des frais seront facturés à ce moment-là.

Pour plus d’informations sur ces cas de figure, consultez la [page de référence sur les recommandations en matière de sécurité](recommendations-reference.md).

Apprenez-en davantage sur la [protection contre les menaces dans Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Améliorations de la sécurité des conteneurs – Analyse du registre plus rapide et documentation actualisée

Dans le cadre de nos investissements continus dans le domaine de la sécurité des conteneurs, nous avons le plaisir d’annoncer une amélioration significative des performances du Security Center en lien avec les analyses dynamiques d’images de conteneur stockées dans Azure Container Registry. Désormais, les analyses prennent généralement environ en deux minutes. Dans certains cas, elles peuvent prendre jusqu’à 15 minutes.

Afin d’améliorer la clarté et les recommandations concernant les fonctionnalités de sécurité des conteneurs d’Azure Security Center, nous avons également actualisé les pages de documentation sur la sécurité des conteneurs. 

Pour en savoir plus sur la sécurité des conteneurs qu’offre Security Center, consultez les articles suivants :

- [Vue d’ensemble des fonctionnalités de sécurité des conteneurs du Security Center](container-security.md)
- [Détails de l’intégration avec Azure Container Registry](defender-for-container-registries-introduction.md)
- [Détails de l’intégration avec Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [Comment analyser vos registres et renforcer vos hôtes Docker](container-security.md)
- [Alertes de sécurité des fonctionnalités de protection contre les menaces pour les clusters Azure Kubernetes Service](alerts-reference.md#alerts-akscluster)
- [Alertes de sécurité des fonctionnalités de protection contre les menaces pour les hôtes Azure Kubernetes Service](alerts-reference.md#alerts-containerhost)
- [Recommandations en matière de sécurité pour les conteneurs](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Mise à jour des contrôles d’application adaptatifs avec une nouvelle recommandation et la prise en charge des caractères génériques dans les règles de chemin d’accès

La fonctionnalité de contrôles d’application adaptatifs a fait l’objet de deux mises à jour importantes :

* Une nouvelle recommandation identifie les comportements potentiellement légitimes qui n’étaient pas autorisés auparavant. La nouvelle recommandation, intitulée **Les règles de liste verte dans votre stratégie de contrôle d’application adaptatif doivent être mises à jour**, vous invite à ajouter de nouvelles règles à la stratégie existante afin de réduire le nombre de faux positifs dans les alertes de violation des contrôles d’application adaptatifs.

* Les règles de chemin d’accès prennent désormais en charge les caractères génériques. À compter de cette mise à jour, vous pouvez configurer des règles de chemin d’accès autorisé avec des caractères génériques. Il existe deux scénarios pris en charge :

    * Utilisation d’un caractère générique à la fin d’un chemin d’accès pour autoriser tous les exécutables dans ce dossier et ses sous-dossiers

    * Utilisation d’un caractère générique au milieu d’un chemin d’accès pour activer un nom d’exécutable connu avec un nom de dossier variable (par exemple, des dossiers utilisateur personnels avec un exécutable connu, des noms de dossiers générés automatiquement, etc.).


[Apprenez-en davantage sur les contrôles d’application adaptatifs](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Dépréciation de six stratégies pour la sécurité avancée des données SQL

Six stratégies relatives à la sécurité avancée des données pour les machines SQL sont déconseillées :

- Les types de protection avancée contre les menaces doivent être définis sur « Tous » dans les paramètres avancés de sécurité des données de l’instance gérée SQL.
- Les types de protection avancée contre les menaces doivent être définis sur « Tous » dans les paramètres avancés de sécurité des données du serveur SQL.
- Les paramètres Advanced Data Security pour l’instance gérée SQL doivent inclure une adresse e-mail pour la réception des alertes de sécurité.
- Les paramètres Advanced Data Security pour le serveur SQL doivent inclure une adresse e-mail pour la réception des alertes de sécurité.
- Les notifications par e-mail aux administrateurs et propriétaires d’abonnements doivent être activées dans les paramètres Advanced Data Security de l’instance managée SQL
- Les notifications par e-mail aux administrateurs et propriétaires d’abonnements doivent être activées dans les paramètres Advanced Data Security SQL Server

En savoir plus sur les [stratégies intégrées](./policy-reference.md).





## <a name="june-2020"></a>Juin 2020

Les mises à jour du mois de juin incluent :
- [API Degré de sécurisation (préversion)](#secure-score-api-preview)
- [Sécurité avancée des données pour les machines SQL (Azure, autres clouds et en local) (préversion)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Deux nouvelles recommandations pour déployer l’agent Log Analytics sur des machines Azure Arc (préversion)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nouvelles stratégies pour créer des configurations d’exportation continue et d’automatisation de flux de travail à l’échelle](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nouvelle recommandation pour l’utilisation de NSG afin de protéger les machines virtuelles non accessibles sur Internet](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nouvelles stratégies pour activer la protection contre les menaces et la sécurité avancée des données](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>API Degré de sécurisation (préversion)

Vous pouvez maintenant accéder à votre degré de sécurisation par le biais de [l’API Degré de sécurisation](/rest/api/securitycenter/securescores/) (actuellement en préversion). Les méthodes de l’API offrent la flexibilité nécessaire pour interroger les données et créer votre propre mécanisme de création de rapports sur vos degrés de sécurisation au fil du temps. Par exemple, vous pouvez utiliser l’API **Degré de sécurisation** pour obtenir le degré de sécurisation d’un abonnement spécifique. En outre, vous pouvez utiliser l’API **Contrôles du degré de sécurisation** pour répertorier les contrôles de sécurité et le degré de sécurisation actuel de vos abonnements.

Pour obtenir des exemples d’outils externes accessibles avec l’API Degré de sécurisation, consultez [la zone consacrée au degré de sécurisation de notre communauté GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

En savoir plus sur [le degré de sécurisation et les contrôles de sécurité dans Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Sécurité avancée des données pour les machines SQL (Azure, autres clouds et en local) (préversion)

La sécurité des données avancée d’Azure Security Center pour les machines SQL protège désormais les serveurs SQL hébergés dans Azure, dans d’autres environnements cloud et même sur des machines locales. Cela étend les protections de vos serveurs SQL Azure natifs pour prendre entièrement en charge les environnements hybrides.

La sécurité avancée des données fournit une évaluation des vulnérabilités et une protection avancée contre les menaces pour vos machines SQL où qu’elles soient.

La configuration se fait en deux étapes :

1. Déploiement de l’agent Log Analytics sur l’ordinateur hôte de votre serveur SQL Server pour fournir la connexion au compte Azure.

1. Activation du bundle facultatif dans la page de tarification et des paramètres de Security Center.

En savoir plus sur la [sécurité avancée des données pour les machines SQL](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Deux nouvelles recommandations pour déployer l’agent Log Analytics sur des machines Azure Arc (préversion)

Deux nouvelles recommandations ont été ajoutées pour vous aider à déployer [l’agent Log Analytics](../azure-monitor/platform/log-analytics-agent.md) sur vos machines Azure Arc et vous assurer qu’elles sont protégées par Azure Security Center :

- **L’agent Log Analytics doit être installé sur vos machines Azure Arc basées sur Windows (préversion)**
- **L’agent Log Analytics doit être installé sur vos machines Azure Arc basées sur Linux (préversion)**

Ces nouvelles recommandations s’affichent dans les quatre mêmes contrôles de sécurité que la recommandation existante (associée), **L’agent d’analyse doit être installé sur vos machines** : corriger les configurations de sécurité, appliquer le contrôle d’application adaptatif, appliquer les mises à jour système et activer la protection de point de terminaison.

Les recommandations incluent également la fonctionnalité de correction rapide pour accélérer le processus de déploiement. 

Pour plus d’informations sur ces deux nouvelles recommandations, consultez le tableau [Recommandations relatives au calcul et aux applications](recommendations-reference.md#recs-computeapp).

En savoir plus sur la façon dont Azure Security Center utilise l’agent dans [Qu’est-ce que l’agent Log Analytics ?](faq-data-collection-agents.md#what-is-the-log-analytics-agent).

En savoir plus sur les [extensions pour les machines Azure Arc](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nouvelles stratégies pour créer des configurations d’exportation continue et d’automatisation de flux de travail à l’échelle

L’automatisation des processus d’analyse et de réponse aux incidents de votre organisation peut réduire de manière significative le temps requis pour examiner et atténuer les incidents de sécurité.

Pour déployer vos configurations d’automatisation à l’échelle de votre organisation, utilisez ces stratégies Azure « DeployIfdNotExist » intégrées pour créer et configurer les procédures [d’exportation continue](continuous-export.md) et [d’automatisation de flux de travail](workflow-automation.md) :

Les stratégies se trouvent dans Azure Policy :


|Objectif  |Policy  |ID de stratégie  |
|---------|---------|---------|
|Exportation continue vers Event Hub|[Déployer l’exportation vers Event Hub pour les alertes et les recommandations Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Exportation continue vers l’espace de travail Log Analytics|[Déployer l’exportation vers un espace de travail Log Analytics pour les alertes et les recommandations Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automatisation du flux de travail pour les alertes de sécurité|[Déployer l’automatisation de workflow pour les alertes Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatisation du flux de travail pour les recommandations de sécurité|[Déployer l’automatisation de workflow pour les recommandations Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Prise en main des [modèles d’automatisation de flux de travail](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Apprenez-en davantage sur l’utilisation des deux stratégies d’exportation dans [Configurer l’automatisation du workflow à grande échelle à l’aide des stratégies fournies](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) et [Configurer une exportation continue](continuous-export.md#set-up-a-continuous-export).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nouvelle recommandation pour l’utilisation de NSG afin de protéger les machines virtuelles non accessibles sur Internet

Le contrôle de sécurité « implémenter les meilleures pratiques de sécurité » comprend désormais la nouvelle recommandation suivante :

- **Les machines virtuelles non connectées à Internet doivent être protégées avec des groupes de sécurité réseau**

Une recommandation existante, **Les machines virtuelles accessibles à partir d’Internet doivent être protégées avec des groupes de sécurité réseau**, ne fait pas la distinction entre les machines virtuelles accessibles sur Internet et celles qui ne le sont pas. Pour les deux types de machines virtuelles, une recommandation à gravité élevée était générée si une machine virtuelle n’était pas affectée à un groupe de sécurité réseau. Cette nouvelle recommandation sépare les machines non accessibles à partir d’Internet pour réduire les faux positifs et éviter les alertes à gravité élevée inutiles.

En savoir plus dans le tableau [Recommandations pour le réseau](recommendations-reference.md#recs-network).




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nouvelles stratégies pour activer la protection contre les menaces et la sécurité avancée des données

Les nouvelles stratégies ci-dessous ont été ajoutées à l’initiative ASC par défaut et sont conçues pour aider à activer la protection contre les menaces ou la sécurité avancée des données pour les types de ressources appropriés.

Les stratégies se trouvent dans Azure Policy :


| Policy                                                                                                                                                                                                                                                                | ID de stratégie                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Advanced Data Security doit être activé sur les serveurs Azure SQL Database](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Advanced Data Security doit être activé sur les serveurs SQL sur les machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Advanced Threat Protection doit être activé sur les comptes Stockage Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Advanced Threat Protection doit être activé sur les coffres Azure Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Advanced Threat Protection doit être activé sur les plans Azure App Service](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Advanced Threat Protection doit être activé sur les registres Azure Container Registry](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Advanced Threat Protection doit être activé sur les clusters Azure Kubernetes Service](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Advanced Threat Protection doit être activé sur les machines virtuelles](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

En savoir plus sur la [protection contre les menaces dans Azure Security Center](azure-defender.md).
