---
title: Archive des nouveautés d’Azure Security Center
description: Description des nouveautés et modifications apportées à Azure Security Center jusqu’à il y a six mois.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 06/14/2021
ms.author: memildin
ms.openlocfilehash: dd6a2deb36ba0a3aea2701721b8df09188358669
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112062331"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archive des nouveautés d’Azure Security Center

La page principale des notes de publication [Nouveautés d’Azure Security Center](release-notes.md) a trait aux mises à jour des six derniers mois, tandis que celle-ci a trait à des mises à jour plus anciennes.

Cette page fournit des informations sur les points suivants :

- Nouvelles fonctionnalités
- Résolution des bogues
- Fonctionnalités dépréciées

## <a name="december-2020"></a>Décembre 2020

Les mises à jour en décembre sont les suivantes :

- [Azure Defender pour les serveurs SQL sur les machines est en disponibilité générale](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [La prise en charge par Azure Defender pour SQL de pool SQL dédié Azure Synapse Analytics est en disponibilité générale](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Les administrateurs généraux peuvent désormais s’accorder des autorisations de niveau locataire](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Deux nouveaux plans Azure Defender : Azure Defender pour DNS et Azure Defender pour Resource Manager (en préversion)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Nouvelle page des alertes de sécurité dans le portail Azure (préversion)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Expérience Security Center relancée dans Azure SQL Database et SQL Managed Instance](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Outils et filtres d’inventaire des ressources mis à jour](#asset-inventory-tools-and-filters-updated)
- [Recommandation sur les applications web demandant des certificats SSL ne faisant plus partie du degré de sécurisation](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [La page Recommandations contient de nouveaux filtres pour l’environnement, la gravité et les réponses disponibles](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [L’exportation continue permet d’obtenir de nouveaux types de données et des stratégies deployifnotexist améliorées](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender pour les serveurs SQL sur les machines est en disponibilité générale

Azure Security Center propose deux plans Azure Defender pour les serveurs SQL :

- **Azure Defender pour serveurs de base de données Azure SQL** : défend vos serveurs Azure SQL natifs 
- **Azure Defender pour les serveurs SQL sur machines** : étend les mêmes protections à vos serveurs SQL dans les environnements hybrides, multicloud et locaux

Avec cette annonce, **Azure Defender pour SQL** protège désormais vos bases de données et leurs données où qu’elles se trouvent.

Azure Defender pour SQL comprend les fonctionnalités d’évaluation des vulnérabilités. L’outil d’évaluation des vulnérabilités comprend les fonctionnalités avancées suivantes :

- **Configuration de base de référence** (nouveau) pour affiner intelligemment les résultats des analyses de vulnérabilité à ceux susceptibles de représenter des problèmes de sécurité réels. Une fois que vous avez établi votre état de sécurité de base de référence, l’outil d’évaluation des vulnérabilités signale uniquement les écarts par rapport à cet état de base de référence. Les résultats correspondant à la base de référence sont considérés comme corrects lors des analyses ultérieures. Cela vous permet, à vous et à vos analystes, de concentrer votre attention sur ce qui est le plus important.
- **Informations de référence détaillées** pour vous aider à *comprendre* les résultats découverts et en quoi ils concernent vos ressources.
- **Scripts de correction** pour vous aider à atténuer les risques identifiés.

En savoir plus sur [Azure Defender pour SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>La prise en charge par Azure Defender pour SQL de pool SQL dédié Azure Synapse Analytics est en disponibilité générale

Azure Synapse Analytics (anciennement SQL DW) est un service d’analytique qui combine l’entreposage des données d’entreprise et l’analytique de Big Data. Les pools SQL dédiés sont les fonctionnalités d’entreposage de données d’entreprise d’Azure Synapse. Apprenez-en davantage dans [Qu’est-ce qu’Azure Synapse Analytics (anciennement SQL DW) ?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Azure Defender pour SQL protège vos pools SQL dédiés avec :

- **Protection avancée contre les menaces** pour détecter les menaces et les attaques 
- **Fonctionnalités d’évaluation des vulnérabilités** pour identifier et corriger les problèmes de configuration de la sécurité

La prise en charge par Azure Defender pour SQL des pools SQL dédiés Azure Synapse Analytics est ajoutée automatiquement au bundle de bases de données Azure SQL dans Azure Security Center. Vous trouverez un nouvel onglet « Azure Defender pour SQL » dans la page de votre espace de travail Synapse dans le portail Azure.

En savoir plus sur [Azure Defender pour SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Les administrateurs généraux peuvent désormais s’accorder des autorisations de niveau locataire

Si un utilisateur ayant le rôle Azure Active Directory d’**Administrateur général** peut avoir des responsabilités à l’échelle du locataire, il peut ne pas disposer des autorisations Azure lui permettant de consulter les informations à l’échelle de l’organisation dans Azure Security Center. 

Pour vous attribuer des autorisations de niveau locataire, suivez les instructions fournies dans [S’accorder des autorisations à l’échelle du locataire](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself).


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Deux nouveaux plans Azure Defender : Azure Defender pour DNS et Azure Defender pour Resource Manager (en préversion)

Nous avons ajouté deux nouvelles fonctionnalités de protection étendue natives cloud contre les menaces pour votre environnement Azure.

Ces nouvelles protections améliorent nettement votre résilience face aux attaques des acteurs des menaces et accroissent considérablement le nombre de ressources Azure protégées par Azure Defender.

- **Azure Defender pour Ressource Manager** – Supervise automatiquement toutes les opérations de gestion de ressources effectuées dans votre organisation. Pour plus d'informations, consultez les pages suivantes :
    - [Présentation d’Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md)
    - [Répondre aux alertes Azure Defender pour Resource Manager](defender-for-resource-manager-usage.md)
    - [Liste des alertes fournies par Azure Defender pour Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender pour DNS** – Supervise en continu toutes les requêtes DNS émanant de vos ressources Azure. Pour plus d'informations, consultez les pages suivantes :
    - [Présentation d’Azure Defender pour DNS](defender-for-dns-introduction.md)
    - [Répondre aux alertes Azure Defender pour DNS](defender-for-dns-usage.md)
    - [Liste des alertes fournies par Azure Defender pour DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Nouvelle page des alertes de sécurité dans le portail Azure (préversion)

La page des alertes de sécurité d’Azure Security Center a été repensée pour fournir les éléments suivants :

- **Expérience de triage améliorée des alertes** – favorise la réduction d’un trop grand nombre d’alertes et permet de se concentrer plus facilement sur les menaces les plus pertinentes. La liste comprend des filtres personnalisables et des options de regroupement.
- **Plus d’informations dans la liste des alertes** – telles que les tactiques MITRE ATT&ACK.
- **Bouton permettant de créer des exemples d’alertes** – pour évaluer les fonctionnalités Azure Defender et tester la configuration de vos alertes (pour l’intégration SIEM, les notifications par e-mail et les automatisations de workflow), vous pouvez créer des exemples d’alertes à partir de tous les plans Azure Defender.
- **Alignement avec l’expérience d’incident d’Azure Sentinel** – pour les clients qui utilisent les deux produits, le basculement entre eux est désormais une expérience plus simple et il est aisé d’apprendre l’un de l’autre.
- **Amélioration des performances** pour de grandes listes d’alertes.
- **Navigation au clavier** dans la liste des alertes.
- **Alertes à partir d’Azure Resource Graph** – vous pouvez effectuer des requêtes sur des alertes dans Azure Resource Graph, l’API de type Kusto pour toutes vos ressources. Cela est également utile si vous créez vos propres tableaux de bord d’alertes. [Apprenez-en davantage sur Azure Resource Graph](../governance/resource-graph/index.yml).

Pour accéder à la nouvelle expérience, utilisez le lien « Essayer maintenant » dans la bannière en haut de la page des alertes de sécurité.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Bannière avec un lien vers la nouvelle expérience d’alertes en préversion":::

Pour créer des exemples d’alertes à partir de la nouvelle expérience Alertes, consultez [Générer des exemples d’alertes Azure Defender](security-center-alert-validation.md#generate-sample-azure-defender-alerts).


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Expérience Security Center relancée dans Azure SQL Database et SQL Managed Instance 

L’expérience Security Center dans SQL permet d’accéder aux fonctionnalités Security Center et Azure Defender pour SQL suivantes :

- **Recommandations de sécurité** Security Center analyse régulièrement l’état de sécurité de toutes les ressources Azure connectées pour identifier les erreurs de configuration de sécurité potentielles. Il fournit ensuite des recommandations sur la façon de corriger ces vulnérabilités et d’améliorer l’état de la sécurité des entreprises.
- **Alertes de sécurité** : service de détection qui supervise en continu les activités Azure SQL pour repérer les menaces telles que l’injection de code SQL, les attaques par force brute et l’abus de privilèges. Ce service déclenche des alertes de sécurité détaillées et orientées action dans Security Center et fournit des options pour poursuivre des investigations avec Azure Sentinel, la solution SIEM native Azure de Microsoft.
- **Résultats** : service d’évaluation des vulnérabilités qui supervise en continu les configurations Azure SQL et aide à corriger les vulnérabilités. Les analyses d’évaluation fournissent une vue d’ensemble des états de sécurité Azure SQL ainsi que des résultats de sécurité détaillés.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Les fonctionnalités de sécurité d’Azure Security Center pour SQL sont disponibles à partir d’Azure SQL":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Outils et filtres d’inventaire des ressources mis à jour

La page d’inventaire dans Azure Security Center a été actualisée avec les modifications suivantes :

- **Guides et commentaires** ajoutés à la barre d’outils. Un volet contenant des liens vers des informations et des outils connexes s’ouvre. 
- **Filtre des abonnements** ajouté aux filtres par défaut disponibles pour vos ressources.
- Lien **Ouvrir une requête** pour ouvrir les options du filtre actuel en tant que requête Azure Resource Graph (anciennement « Afficher dans l’Explorateur Resource Graph »).
- **Options des opérateurs** pour chaque filtre. Vous pouvez désormais choisir un autre opérateur logique que « = ». Par exemple, vous souhaiterez peut-être rechercher toutes les ressources avec des recommandations actives dont les titres incluent la chaîne « chiffrer ». 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Contrôles de l’option d’opérateur dans les filtres de l’inventaire des ressources":::

Apprenez-en davantage sur l’inventaire dans [Explorer et gérer vos ressources avec l’inventaire des ressources](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Recommandation sur les applications web demandant des certificats SSL ne faisant plus partie du degré de sécurisation

La recommandation « Les applications web doivent exiger un certificat SSL pour toutes les demandes entrantes » a été déplacée du contrôle de sécurité **Gérer l’accès et les autorisations** (valeur de 4 points au maximum) dans **Implémenter les bonnes pratiques de sécurité** (qui ne vaut aucun point). 

La garantie qu’une application web demande un certificat renforce certainement sa sécurité. Toutefois, ce n’est pas pertinent pour les applications web publiques. si vous accédez à votre site sur HTTP et non HTTPS, vous ne recevez pas de certificat client. Ainsi, si votre application nécessite des certificats clients, vous ne devez pas autoriser les requêtes adressées à votre application via HTTP. Pour en savoir plus, consultez [Configurer l’authentification mutuelle TLS pour Azure App Service](../app-service/app-service-web-configure-tls-mutual-auth.md).

Avec ce changement, la recommandation est désormais une bonne pratique qui n’impacte pas votre degré de sécurisation. 

Découvrez les recommandations de chaque contrôle de sécurité dans [Contrôles de sécurité et leurs recommandations](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>La page Recommandations contient de nouveaux filtres pour l’environnement, la gravité et les réponses disponibles

Azure Security Center supervise toutes les ressources connectées et génère des recommandations de sécurité. Utilisez ces recommandations pour renforcer l’état de votre cloud hybride et effectuer le suivi de la conformité aux stratégies et normes pertinentes pour vos organisation, secteur d’activité et pays.

Comme Security Center continue d’étendre sa couverture et de développer ses fonctionnalités, la liste des recommandations de sécurité augmente chaque mois. Par exemple, consultez [Ajout de 29 recommandations (préversion) pour mieux détailler le benchmark de sécurité Azure](release-notes-archive.md#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Avec cette liste qui ne cesse de s’allonger, un filtrage des recommandations est nécessaire pour trouver celles qui présentent le plus grand intérêt. En novembre, nous avons ajouté des filtres à la page Recommandations (consultez [La liste des recommandations comprend désormais des filtres](release-notes-archive.md#recommendations-list-now-includes-filters)).

Les filtres ajoutés ce mois-ci fournissent des options pour affiner la liste des recommandations en fonction des éléments suivants :

- **Environnement** : affichez les recommandations pour vos ressources AWS, GCP ou Azure (ou n’importe quelle combinaison)
- **Gravité** : affichez les recommandations en fonction de la classification de gravité définie par Security Center
- **Actions de réponse** : Affichez les recommandations en fonction de la disponibilité des options de réponse Security Center : Corriger, Refuser et Appliquer

    > [!TIP]
    > Le filtre d’actions de réponse remplace le filtre **Correctif rapide disponible (Oui/Non)** . 
    > 
    > Apprenez-en davantage sur chacune de ces options de réponse :
    > - [Bouton Corriger](security-center-remediate-recommendations.md#fix-button)
    > - [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Recommandations regroupées par contrôle de sécurité" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>L’exportation continue permet d’obtenir de nouveaux types de données et des stratégies deployifnotexist améliorées

Les outils d’exportation continue d’Azure Security Center vous permettent d’exporter les recommandations et alertes de Security Center en vue de les utiliser avec d’autres outils de supervision dans votre environnement.

L’exportation continue vous permet de personnaliser entièrement ce qui sera exporté et où cela sera exporté. Pour plus d’informations, consultez [Exporter en continu des données Security Center](continuous-export.md).

Ces outils ont été améliorés et développés de différentes manières :

- **Amélioration des stratégies deployifnotexist de l’exportation continue**. À présent, les stratégies :

    - **Vérifient si la configuration est activée.** Si ce n’est pas le cas, la stratégie s’affiche comme non conforme et crée une ressource conforme. Découvrez les modèles Azure Policy fournis sous l’onglet « Déployer à grande échelle avec Azure Policy » dans [Configurer une exportation continue](continuous-export.md#set-up-a-continuous-export).

    - **Prennent en charge l’exportation des résultats de sécurité.** Quand vous utilisez les modèles Azure Policy, vous pouvez configurer votre exportation continue pour inclure les résultats. Cela s’applique lorsque vous exportez des recommandations avec des « sous-recommandations », telles que les résultats des analyseurs d’évaluation des vulnérabilités ou des mises à jour système spécifiques pour la recommandation « parent », « Des mises à jour système doivent être installées sur vos machines ».
    
    - **Prennent en charge l’exportation des données du degré de sécurisation.**

- **Ajout de données d’évaluation de conformité réglementaire (en préversion).** Vous pouvez désormais exporter en continu des mises à jour pour des évaluations de conformité réglementaire, notamment pour des initiatives personnalisées, vers un espace de travail Log Analytics ou un hub d’événements. Cette fonctionnalité n’est pas disponible sur les clouds nationaux/souverains.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="Options permettant d’inclure des informations d’évaluation de conformité réglementaire avec vos données d’exportation continue.":::

## <a name="november-2020"></a>Novembre 2020

Les mises à jour en novembre sont les suivantes :

- [Ajout de 29 recommandations (préversion) pour mieux détailler le benchmark de sécurité Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Ajout de NIST SP 800 171 R2 au tableau de bord de conformité réglementaire Security Center](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [La liste des recommandations comprend désormais des filtres](#recommendations-list-now-includes-filters)
- [Amélioration et développement de l’expérience de provisionnement automatique](#auto-provisioning-experience-improved-and-expanded)
- [Le score sécurisé est désormais disponible dans l’exportation continue (préversion)](#secure-score-is-now-available-in-continuous-export-preview)
- [La recommandation « Les mises à jour système doivent être installées sur vos machines » inclut désormais des sous-recommandations](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [La page Gestion des stratégies du portail Azure affiche maintenant l’état des affectations de stratégie par défaut](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Ajout de 29 recommandations (préversion) pour mieux détailler le benchmark de sécurité Azure

Le Benchmark de sécurité Azure constitue l’ensemble des directives propres à Azure et créées par Microsoft, qui contient les bonnes pratiques de sécurité et de conformité s’inscrivant dans les cadres de conformité courants. [En savoir plus sur le Benchmark de sécurité Azure](/security/benchmark/azure/introduction).

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

- [En savoir plus sur le Benchmark de sécurité Azure](/security/benchmark/azure/introduction)
- [En savoir plus sur les applications API Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [En savoir plus sur les applications de fonction Azure](../azure-functions/functions-overview.md)
- [En savoir plus sur les applications web Azure](../app-service/overview.md)
- [En savoir plus sur Azure Database for MariaDB](../mariadb/overview.md)
- [En savoir plus sur Azure Database pour MySQL](../mysql/overview.md)
- [En savoir plus sur Azure Database pour PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>Ajout de NIST SP 800 171 R2 au tableau de bord de conformité réglementaire Security Center

Le standard NIST SP 800-171 R2 est désormais disponible sous la forme d’une initiative intégrée à utiliser avec le tableau de bord de conformité réglementaire Azure Security Center. Les mappages des contrôles sont décrits dans [Détails de l’initiative intégrée de conformité réglementaire NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md). 

Pour appliquer le standard à vos abonnements et superviser votre état de conformité en permanence, suivez les instructions fournies dans [Personnaliser l’ensemble de normes du tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Standard NIST SP 800 171 R2 dans le tableau de bord de conformité réglementaire Security Center":::

Pour plus d’informations sur cette norme de conformité, consultez [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>La liste des recommandations comprend désormais des filtres

Vous pouvez maintenant filtrer la liste des recommandations de sécurité en fonction d’une plage de critères. Dans l’exemple suivant, la liste des recommandations a été filtrée pour afficher celles qui :

- sont **généralement disponibles** (c’est-à-dire, pas en préversion)
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


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>La recommandation « Les mises à jour système doivent être installées sur vos machines » inclut désormais des sous-recommandations

La recommandation **Les mises à jour système doivent être installées sur vos machines** a été améliorée. La nouvelle version inclut des sous-recommandations pour chaque mise à jour manquante et apporte les améliorations suivantes :

- Une expérience repensée dans les pages Azure Security Center du portail Azure. La page Détails de la recommandation pour **Les mises à jour système doivent être installées sur vos machines** comprend la liste des résultats, comme illustré ci-dessous. Lorsque vous sélectionnez une recherche unique, le volet d’informations s’ouvre avec un lien vers des informations de correction et une liste de ressources affectées.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Ouverture de l’une des sous-recommandations dans l’expérience du portail pour la recommandation mise à jour":::

- Des données enrichies pour la recommandation provenant d’Azure Resource Graph (ARG). ARG est un service Azure conçu pour fournir une exploration efficace des ressources. Vous pouvez utiliser ARG pour effectuer une requête à grande échelle sur un ensemble d’abonnements donné, et ainsi gérer de façon optimale votre environnement. 

    Pour Azure Security Center, vous pouvez utiliser ARG et le [langage de requête Kusto (KQL)](/azure/data-explorer/kusto/query/) pour interroger un large éventail de données relatives à la posture de sécurité.

    Avant, si vous interrogiez cette recommandation dans ARG, les seules informations disponibles étaient que la recommandation devait être corrigée sur une machine. La requête suivante de la version améliorée retournera toutes les mises à jour système manquantes, regroupées par machine.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>La page Gestion des stratégies du portail Azure affiche maintenant l’état des affectations de stratégie par défaut

Vous pouvez maintenant voir si la stratégie Security Center par défaut est affectée à vos abonnements, dans la page **Stratégie de sécurité** de Security Center du portail Azure.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Page Gestion des stratégies d’Azure Security Center présentant les affectations de stratégie par défaut":::



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

[Découvrez-en plus sur les serveurs avec Azure Arc](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Ajout d’une recommandation concernant le pare-feu Azure (préversion)

Une nouvelle recommandation a été ajoutée pour protéger tous vos réseaux virtuels à l’aide du pare-feu Azure.

La recommandation **Les réseaux virtuels doivent être protégés par le pare-feu Azure** vous conseille de limiter l’accès à vos réseaux virtuels et d’éviter les menaces potentielles à l’aide du pare-feu Azure.

Découvrez le [Pare-feu Azure](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Des plages d’adresses IP autorisées doivent être définies sur les services Kubernetes - Recommandation mise à jour avec correctif rapide

La recommandation **Des plages d’adresses IP autorisées doivent être définies sur les services Kubernetes** a maintenant une option de correctif rapide.

Pour plus d’informations sur cette recommandation et toutes les autres recommandations Security Center, consultez [Recommandations de sécurité – Guide de référence](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Des plages d’adresses IP autorisées doivent être définies sur les services Kubernetes - Recommandation avec l’option de correctif rapide":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Le tableau de bord de conformité réglementaire comprend désormais une option de suppression des normes

Le tableau de bord de conformité réglementaire fournit des insights sur votre posture de conformité d’après la façon dont vous répondez à des exigences et contrôles de conformité spécifiques.

Le tableau de bord comprend un ensemble de normes réglementaires par défaut. Si certaines des normes fournies ne sont pas pertinentes pour votre organisation, il est désormais facile de les supprimer de l’interface utilisateur d’un abonnement. Les normes ne peuvent être supprimées qu’au niveau de l’*abonnement*, et non à l’étendue du groupe d’administration.

Pour plus d’informations, consultez [Supprimer une norme de votre tableau de bord](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Suppression de la table Microsoft.Security/securityStatuses d’Azure Resource Graph (ARG)

Azure Resource Graph est un service d’Azure conçu pour fournir une exploration efficace des ressources avec la possibilité de lancer des requêtes à grande échelle sur un ensemble donné d’abonnements pour vous permettre d’optimiser la gestion de votre environnement. 

Pour Azure Security Center, vous pouvez utiliser ARG et le [langage de requête Kusto (KQL)](/azure/data-explorer/kusto/query/) pour interroger un large éventail de données relatives à la posture de sécurité. Par exemple :

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
- [Langage de requête Kusto (KQL)](/azure/data-explorer/kusto/query/)


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

Nous avons publié une interface utilisateur actualisée pour les pages du portail de Security Center. Les nouvelles pages incluent une page de vue d’ensemble inédite et des tableaux de bord pour le niveau de sécurité, l’inventaire des ressources et Azure Defender.

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

À partir du 1er octobre 2020, nous commencerons à facturer la protection des ressources présentes dans ces services.

Consultez [Azure Defender pour Stockage](defender-for-storage-introduction.md) pour en savoir plus.


### <a name="asset-inventory-tools-are-now-generally-available"></a>Les outils d’inventaire des ressources sont désormais mis à la disposition générale

La page d’inventaire des ressources d’Azure Security Center fournit une page unique pour visualiser la posture de sécurité des ressources que vous avez connectées à Security Center.

Security Center analyse périodiquement l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités de sécurité potentielles. Il fournit ensuite des recommandations sur la façon de corriger ces vulnérabilités.

Lorsqu’une ressource contient des recommandations en suspens, celles-ci apparaissent dans l’inventaire.

Pour en savoir plus, consultez [Explorer et gérer vos ressources avec l’inventaire des ressources](asset-inventory.md).



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

Utilisez l’exportation continue pour diffuser vos alertes et vos recommandations vers Azure Event Hubs, les espaces de travail Log Analytics ou Azure Monitor. À partir de là, vous pouvez intégrer ces données à des informations de sécurité et gestion d’événements (par exemple, Azure Sentinel, Power BI, Azure Data Explorer et bien plus.)

Les outils d’évaluation des vulnérabilités intégrés à Security Center renvoient des résultats sur vos ressources comme recommandations exploitables dans le cadre d’une recommandation « parent », telle que « les vulnérabilités de vos machines virtuelles doivent être corrigées ». 

Les résultats de sécurité sont désormais disponibles pour l’exportation via l’exportation continue lorsque vous sélectionnez recommandations et activez l’option **Inclure les résultats de sécurité**.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Intégrer les résultats de sécurité dans la configuration de l’exportation continue" :::

Pages connexes :

- [Solution intégrée d’évaluation des vulnérabilités de Security Center pour les machines virtuelles Azure](deploy-vulnerability-assessment-vm.md)
- [Solution d’évaluation des vulnérabilités intégrée à Security Center pour les images Azure Container Registry](defender-for-container-registries-usage.md)
- [Exportation continue](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Empêcher les erreurs de configurations de sécurité en appliquant des recommandations lors de la création de nouvelles ressources

Les erreurs de configuration de la sécurité sont à l’origine de la plupart des incidents. Security Center vous permet désormais de *prévenir* les erreurs de configuration des nouvelles ressources en ce qui concerne les recommandations spécifiques. 

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

La fonctionnalité « stratégie de sécurité des pods (en préversion) », sera bientôt dépréciée et ne sera plus disponible après le 15 octobre 2020 pour laisser place à Azure Policy pour AKS.

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
|**Une solution d’évaluation des vulnérabilités doit être activée sur vos machines virtuelles**|Remplace les deux recommandations suivantes :<br> ***** Activer la solution intégrée d’évaluation des vulnérabilités sur les machines virtuelles (par Qualys) (désormais déconseillée) (incluse avec le niveau standard)<br> ***** La solution d’évaluation des vulnérabilités doit être installée sur vos machines virtuelles (désormais déconseillée) (niveaux standard et gratuits)|
|**Les vulnérabilités de vos machines virtuelles doivent être corrigées**|Remplace les deux recommandations suivantes :<br>***** Corriger les vulnérabilités trouvées sur vos machines virtuelles (avec Qualys) (à présent déconseillée)<br>***** Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités (à présent déconseillée)|
|||

Vous allez maintenant utiliser la même recommandation pour déployer l’extension d’évaluation de la vulnérabilité de Security Center ou une solution sous licence privée (« BYOL ») d’un partenaire tel que Qualys ou Rapid7.

De plus, lorsque des vulnérabilités sont détectées et signalées à Security Center, une recommandation unique vous avertit des conclusions, quelle que soit la solution d’évaluation des vulnérabilités qui les a identifiées.

#### <a name="updating-dependencies"></a>Mise à jour des dépendances

Si vous avez des scripts, des requêtes ou des automations qui font référence aux recommandations ou aux clés/noms de stratégie précédents, utilisez les tableaux ci-dessous pour mettre à jour les références :

##### <a name="before-august-2020"></a>Avant août 2020

| Recommandation|Étendue|
|----|:----|
|**Activer la solution intégrée d’évaluation des vulnérabilités sur les machines virtuelles (par Qualys)**<br>Clé : 550e890b-e652-4d22-8274-60b3bdb24c63|Intégré|
|**Corriger les vulnérabilités trouvées sur vos machines virtuelles (avec Qualys)**<br>Clé : 1195afff-c881-495e-9bc5-1486211ae03f|Intégré|
|**La solution d’évaluation des vulnérabilités doit être installée sur vos machines virtuelles**<br>Clé : 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités**<br>Clé : 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
|||


|Policy|Étendue|
|----|:----|
|**L’évaluation des vulnérabilités doit être activée sur les machines virtuelles**<br>ID de stratégie : 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Intégré|
|**Les vulnérabilités doivent être corrigées avec une solution d’évaluation des vulnérabilités**<br>ID de stratégie : 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
|||


##### <a name="from-august-2020"></a>À partir d’août 2020

|Recommandation|Étendue|
|----|:----|
|**Une solution d’évaluation des vulnérabilités doit être activée sur vos machines virtuelles**<br>Clé : ffff0522-1e88-47fc-8382-2a80ba848f5d|Intégré + BYOL|
|**Les vulnérabilités de vos machines virtuelles doivent être corrigées**<br>Clé : 1195afff-c881-495e-9bc5-1486211ae03f|Intégré + BYOL|
|||

|Policy|Étendue|
|----|:----|
|[**L’évaluation des vulnérabilités doit être activée sur les machines virtuelles**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID de stratégie : 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Intégré + BYOL|
|||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nouvelles stratégies de sécurité AKS ajoutées à ASC_default initiative : pour une utilisation par les clients de la préversion privée uniquement

Pour vous assurer que les charges de travail Kubernetes sont sécurisées par défaut, Security Center ajoute des stratégies de niveau Kubernetes et des suggestions de renforcement, y compris des options de mise en œuvre avec le contrôle d’admission Kubernetes.

La première phase de ce projet comprend une préversion privée et l’ajout de nouvelles stratégies (désactivées par défaut) à l’initiative ASC_default.

Vous pouvez ignorer ces stratégies en toute sécurité et il n’y aura aucun impact sur votre environnement. Si vous souhaitez les activer, inscrivez-vous à la préversion sur https://aka.ms/SecurityPrP et sélectionnez à partir des options suivantes :

1. **Préversion unique** : pour rejoindre uniquement cette préversion privée. Mentionnez explicitement « Analyse continu ASC » comme préversion que vous souhaitez rejoindre.
1. **Programme en cours** : à ajouter à cette préversion privée et aux prochaines. Vous devrez remplir un profil et une déclaration de confidentialité.


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
- [Alertes de sécurité des fonctionnalités de protection contre les menaces pour les clusters Azure Kubernetes Service](alerts-reference.md#alerts-k8scluster)
- [Alertes de sécurité des fonctionnalités de protection contre les menaces pour les hôtes Azure Kubernetes Service](alerts-reference.md#alerts-containerhost)
- [Recommandations en matière de sécurité pour les conteneurs](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Mise à jour des contrôles d’application adaptatifs avec une nouvelle recommandation et la prise en charge des caractères génériques dans les règles de chemin d’accès

La fonctionnalité de contrôles d’application adaptatifs a fait l’objet de deux mises à jour importantes :

* Une nouvelle recommandation identifie les comportements potentiellement légitimes qui n’étaient pas autorisés auparavant. La nouvelle recommandation, intitulée **Les règles de liste verte dans votre stratégie de contrôle d’application adaptatif doivent être mises à jour**, vous invite à ajouter de nouvelles règles à la stratégie existante afin de réduire le nombre de faux positifs dans les alertes de violation des contrôles d’application adaptatifs.

* Les règles de chemin d’accès prennent désormais en charge les caractères génériques. À compter de cette mise à jour, vous pouvez configurer des règles de chemin d’accès autorisé avec des caractères génériques. Il existe deux scénarios pris en charge :

    * Utilisation d’un caractère générique à la fin d’un chemin d’accès pour autoriser tous les exécutables dans ce dossier et ses sous-dossiers

    * Utilisation d’un caractère générique au milieu d’un chemin pour activer un nom d’exécutable connu avec un nom de dossier variable (par exemple, des dossiers utilisateur personnels avec un exécutable connu, des noms de dossiers générés automatiquement, etc.).


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
- [Sécurité avancée des données pour les machines SQL (Azure, autres clouds et en local) (préversion)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Deux nouvelles recommandations pour déployer l’agent Log Analytics sur des machines Azure Arc (préversion)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nouvelles stratégies pour créer des configurations d’exportation continue et d’automatisation de flux de travail à l’échelle](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nouvelle recommandation pour l’utilisation de NSG afin de protéger les machines virtuelles non accessibles sur Internet](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nouvelles stratégies pour activer la protection contre les menaces et la sécurité avancée des données](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>API Degré de sécurisation (préversion)

Vous pouvez maintenant accéder à votre degré de sécurisation par le biais de [l’API Degré de sécurisation](/rest/api/securitycenter/securescores/) (actuellement en préversion). Les méthodes de l’API offrent la flexibilité nécessaire pour interroger les données et créer votre propre mécanisme de création de rapports sur vos degrés de sécurisation au fil du temps. Par exemple, vous pouvez utiliser l’API **Degré de sécurisation** pour obtenir le degré de sécurisation d’un abonnement spécifique. En outre, vous pouvez utiliser l’API **Contrôles du degré de sécurisation** pour répertorier les contrôles de sécurité et le degré de sécurisation actuel de vos abonnements.

Pour obtenir des exemples d’outils externes accessibles avec l’API Degré de sécurisation, consultez [la zone consacrée au degré de sécurisation de notre communauté GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

En savoir plus sur [le degré de sécurisation et les contrôles de sécurité dans Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Sécurité avancée des données pour les machines SQL (Azure, autres clouds et en local) (préversion)

La sécurité des données avancée d’Azure Security Center pour les machines SQL protège désormais les serveurs SQL hébergés dans Azure, dans d’autres environnements cloud et même sur des machines locales. Cela étend les protections de vos serveurs SQL Azure natifs pour prendre entièrement en charge les environnements hybrides.

La sécurité avancée des données fournit une évaluation des vulnérabilités et une protection avancée contre les menaces pour vos machines SQL où qu’elles soient.

La configuration se fait en deux étapes :

1. Déploiement de l’agent Log Analytics sur l’ordinateur hôte de votre serveur SQL Server pour fournir la connexion au compte Azure.

1. Activation du bundle facultatif dans la page de tarification et des paramètres de Security Center.

En savoir plus sur la [sécurité avancée des données pour les machines SQL](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Deux nouvelles recommandations pour déployer l’agent Log Analytics sur des machines Azure Arc (préversion)

Deux nouvelles recommandations ont été ajoutées pour vous aider à déployer [l’agent Log Analytics](../azure-monitor/agents/log-analytics-agent.md) sur vos machines Azure Arc et vous assurer qu’elles sont protégées par Azure Security Center :

- **L’agent Log Analytics doit être installé sur vos machines Azure Arc basées sur Windows (préversion)**
- **L’agent Log Analytics doit être installé sur vos machines Azure Arc basées sur Linux (préversion)**

Ces nouvelles recommandations s’affichent dans les quatre mêmes contrôles de sécurité que la recommandation existante (associée), **L’agent d’analyse doit être installé sur vos machines** : corriger les configurations de sécurité, appliquer le contrôle d’application adaptatif, appliquer les mises à jour système et activer la protection de point de terminaison.

Les recommandations incluent également la fonctionnalité de correction rapide pour accélérer le processus de déploiement. 

Pour plus d’informations sur ces deux nouvelles recommandations, consultez le tableau [Recommandations relatives au calcul et aux applications](recommendations-reference.md#recs-compute).

En savoir plus sur la façon dont Azure Security Center utilise l’agent dans [Qu’est-ce que l’agent Log Analytics ?](./faq-data-collection-agents.yml#what-is-the-log-analytics-agent-).

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

En savoir plus dans le tableau [Recommandations pour le réseau](recommendations-reference.md#recs-networking).




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


## <a name="may-2020"></a>Mai 2020

Les mises à jour du mois de mai incluent :
- [Règles de suppression d’alerte (préversion)](#alert-suppression-rules-preview)
- [Disponibilité de l’évaluation des vulnérabilités des machines virtuelles](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Modifications apportées à l’accès juste-à-temps (JAT) des machines virtuelles](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Déplacement des recommandations personnalisées vers un contrôle de sécurité distinct](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Ajout d’une option permettant d’afficher les recommandations dans les contrôles ou sous forme de liste plate](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Extension du contrôle de sécurité « Implémenter les bonnes pratiques de sécurité »](#expanded-security-control-implement-security-best-practices)
- [Disponibilité générale des stratégies personnalisées avec des métadonnées personnalisées](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Migration des fonctionnalités d’analyse du vidage sur incident vers la détection d’attaque sans fichier](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Règles de suppression d’alerte (préversion)

Cette nouvelle fonctionnalité (actuellement en préversion) permet de réduire la fréquence des alertes. Utilisez des règles pour masquer automatiquement les alertes connues pour être anodines ou liées à des activités normales au sein votre organisation. Cela vous permet de vous concentrer sur les menaces les plus pertinentes. 

Les alertes correspondant à vos règles de suppression activées sont toujours générées, mais leur état est défini sur ignoré. Vous pouvez voir leur état sur le portail Azure ou en accédant aux alertes de votre Security Center.

Les règles de suppression définissent les critères en vertu desquels les alertes doivent être automatiquement ignorées. En règle générale, vous utilisez une règle de suppression pour effectuer les opérations suivantes :

- supprimer des alertes identifiées comme faux positifs ;

- supprimer des alertes déclenchées trop souvent pour être utiles.

Apprenez-en davantage sur la [suppression des alertes à partir de la protection contre les menaces d’Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Disponibilité de l’évaluation des vulnérabilités des machines virtuelles

Le niveau standard de Security Center intègre désormais l’évaluation des vulnérabilités des machines virtuelles sans frais supplémentaires. Cette extension est fournie par Qualys mais renvoie ses résultats directement à Security Center. Vous n’avez pas besoin d’une licence Qualys ni même de compte Qualys : tout est traité de manière fluide dans Security Center.

La nouvelle solution peut analyser en continu vos machines virtuelles pour trouver des vulnérabilités et présenter les résultats au Security Center. 

Pour déployer la solution, suivez la nouvelle recommandation de sécurité :

« Activer la solution intégrée d’évaluation des vulnérabilités sur les machines virtuelles (optimisées par Qualys) (Préversion)

Apprenez-en davantage sur l’[évaluation des vulnérabilités des machines virtuelles intégrée dans le Security Center](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Modifications apportées à l’accès juste-à-temps (JAT) des machines virtuelles

Le Security Center intègre une fonctionnalité facultative destinée à protéger les ports de gestion de vos machines virtuelles. Celle-ci offre une protection contre la forme la plus courante d’attaques par force brute.

Cette mise à jour apporte à cette fonctionnalité les modifications suivantes :

- La recommandation suggérant d’activer l’accès JAT sur une machine virtuelle a été reformulée. L’ancien libellé, « Un contrôle d’accès réseau juste-à-temps doit être appliqué aux machines virtuelles », est remplacé par « Les ports de gestion des machines virtuelles doivent être protégés par un contrôle d’accès réseau juste-à-temps ».

- La recommandation n’est déclenchée que s’il existe des ports de gestion ouverts.

Apprenez-en davantage sur la [fonctionnalité accès JAT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Déplacement des recommandations personnalisées vers un contrôle de sécurité distinct

L’un des contrôles de sécurité introduits avec le degré de sécurisation amélioré était « Implémenter les meilleures pratiques de sécurité ». Toutes les recommandations personnalisées créées pour vos abonnements ont été placées automatiquement dans ce contrôle. 

Pour faciliter la recherche de vos recommandations personnalisées, nous les avons déplacées vers un contrôle de sécurité dédié nommé « Recommandations personnalisées ». Ce contrôle n’a aucun impact sur votre degré de sécurisation.

Pour en savoir plus sur les contrôles de sécurité, consultez [Version améliorée du degré de sécurisation (préversion) dans Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Ajout d’une option permettant d’afficher les recommandations dans les contrôles ou sous forme de liste plate

Les contrôles de sécurité sont des groupes logiques de recommandations de sécurité associées. Ceux-ci reflètent vos surfaces d’attaque vulnérables. Un contrôle est un ensemble de recommandations de sécurité, avec des instructions qui vous permettent de les implémenter.

Pour voir immédiatement dans quelle mesure votre organisation sécurise chacune des surfaces d’attaque, examinez le degré de chaque contrôle de sécurité.

Par défaut, vos recommandations s’affichent dans les contrôles de sécurité. À partir de cette mise à jour, vous pouvez également les afficher sous forme de liste. Pour les afficher sous la forme d’une liste simple triée en fonction de l’état d’intégrité des ressources affectées, utilisez la nouvelle option « Grouper par contrôles ». Cette option se trouve au-dessus de la liste dans le portail.

Les contrôles de sécurité, et cette option, font partie de la nouvelle expérience de degré de sécurisation. N’oubliez pas de nous envoyer vos commentaires à partir du portail.

Pour en savoir plus sur les contrôles de sécurité, consultez [Version améliorée du degré de sécurisation (préversion) dans Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Activer/désactiver « Regrouper par contrôles » pour les recommandations":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Extension du contrôle de sécurité « Implémenter les bonnes pratiques de sécurité » 

L’un des contrôles de sécurité introduits avec le degré de sécurisation amélioré est « Implémenter les meilleures pratiques de sécurité ». Quand ce contrôle contient une recommandation, celle-ci n’a aucun impact sur le degré de sécurisation. 

Avec cette mise à jour, trois recommandations ont été déplacées des contrôles dans lesquels elles étaient placées à l’origine vers ce contrôle des bonnes pratiques. Nous avons pris cette mesure parce que nous avons constaté que le risque que ces trois recommandations visaient à prévenir était moindre que le risque initialement prévu.

En outre, deux nouvelles recommandations ont été introduites et ajoutées à ce contrôle.

Les trois recommandations déplacées sont les suivantes :

- **L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de lecture de votre abonnement** (à l’origine, dans le contrôle « Activer la MFA »).
- **Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement** (à l’origine, dans le contrôle « Gérer l’accès et les autorisations »).
- **Trois propriétaires au plus doivent être désignés pour votre abonnement** (à l’origine, dans le contrôle « Gérer l’accès et les autorisations »).

Les deux nouvelles recommandations ajoutées au contrôle sont les suivantes :

- **L’extension de configuration d’invité doit être installée sur les machines virtuelles Windows (préversion)**  : la [Configuration d’invité Azure Policy](../governance/policy/concepts/guest-configuration.md) apporte une visibilité des machines virtuelles aux paramètres de serveur et d’application (Windows uniquement).

- **Windows Defender Exploit Guard doit être activé sur vos machines (préversion)**  : Windows Defender Exploit Guard tire parti de l’agent de configuration d’invité (Guest Configuration) Azure Policy. Windows Defender Exploit Guard compte quatre composants conçus pour verrouiller les appareils afin de les protéger contre un vaste éventail de vecteurs d’attaque et comportements de blocage couramment utilisés par les programmes malveillants, tout en permettant aux entreprises de trouver un juste équilibre entre sécurité et productivité (Windows uniquement).

Pour en savoir plus sur Windows Defender exploit Guard, consultez [Créer et déployer une stratégie Windows Defender Exploit Guard](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Pour en savoir plus sur les contrôles de sécurité, consultez [Version améliorée du degré de sécurisation (préversion)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Disponibilité générale des stratégies personnalisées avec des métadonnées personnalisées

Les stratégies personnalisées font désormais partie de l’expérience des Recommandations relatives à Azure Security Center, du degré de sécurisation et du tableau de bord des normes de conformité réglementaire. Cette fonctionnalité désormais généralement disponible vous permet d’étendre la couverture de l’évaluation de la sécurité de votre organisation dans Azure Security Center. 

Créez une initiative personnalisée dans Azure Policy, ajoutez-y des stratégies, intégrez-la à Azure Security Center et visualisez-la sous la forme de recommandations.

Nous avons également ajouté une option permettant de modifier les métadonnées de recommandation personnalisées. Les options des métadonnées incluent la gravité, des mesures de correction, des informations sur les menaces, etc.  

Pour en savoir plus, consultez [Amélioration de vos recommandations personnalisées avec des informations détaillées](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Migration des fonctionnalités d’analyse du vidage sur incident vers la détection d’attaque sans fichier 

Nous intégrons les fonctionnalités de détection de l’analyse du vidage sur incident Windows dans la [détection d’attaque sans fichier](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). L’analyse de la détection d’attaque sans fichier offre des versions améliorées des alertes de sécurité suivantes pour les ordinateurs Windows : Injection de code découverte, usurpation d’identité de module Windows détectée, Shellcode détecté et segment de code suspect détecté.

Voici quelques-uns des avantages de cette transition :

- **Détection proactive et en temps opportun des programmes malveillants** : l’approche de l’analyse du vidage sur incident impliquait l’attente de la survenance d’un incident, puis l’exécution d’une analyse pour trouver des artefacts malveillants. La détection d’attaque sans fichier introduit l’identification de manière proactive des menaces en mémoire pendant leur exécution. 

- **Alertes enrichies** : les alertes de sécurité liées à la détection d’attaque sans fichier apportent des enrichissements par rapport à une simple analyse du vidage sur incident, tels que les informations de connexions réseau actives. 

- **Agrégation d’alertes** : quand l’analyse du vidage sur incident détectait plusieurs modèles d’attaque au sein d’un même vidage sur incident, elle déclenchait plusieurs alertes de sécurité. La détection d’attaque sans fichier combine tous les modèles d’attaque identifiés participant d’un même processus dans une alerte unique, ce qui évite d’avoir à mettre en corrélation plusieurs alertes.

- **Exigences réduites concernant votre espace de travail Log Analytics** : les vidages sur incident contenant des données potentiellement sensibles ne sont plus chargés dans votre espace de travail Log Analytics.






## <a name="april-2020"></a>Avril 2020

Les mises à jour du mois d’avril incluent :
- [Disponibilité générale des packages de conformité dynamique](#dynamic-compliance-packages-are-now-generally-available)
- [Inclusion des recommandations relatives aux identités dans le niveau gratuit d’Azure Security Center](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Disponibilité générale des packages de conformité dynamique

Le tableau de bord de conformité réglementaire d’Azure Security Center inclut désormais des **packages de conformité dynamique** (en disponibilité générale) pour suivre des normes réglementaires et sectorielles supplémentaires.

Vous pouvez ajouter ces packages de conformité dynamique à votre abonnement ou à votre groupe d’administration à partir de la page Stratégie de sécurité d’Azure Security Center. Après l’intégration d’une norme ou un benchmark, ceux-ci apparaissent dans votre tableau de bord de conformité réglementaire avec toutes les données de conformité associées en tant qu’évaluations. Un rapport de synthèse pour toutes les normes intégrées sera disponible en téléchargement.

Désormais, vous pouvez ajouter des normes telles que les suivantes :

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official et UK NHS**
- **PBMM fédéral du Canada**
- **Azure CIS 1.1.0 (nouveau)** (représentation plus complète d’Azure CIS 1.1.0)

De plus, nous avons récemment ajouté le [Benchmark de sécurité Azure](/security/benchmark/azure/introduction), les directives spécifiques d’Azure créées par Microsoft pour les meilleures pratiques de sécurité et de conformité, basées sur des infrastructures de conformité courantes. Des normes supplémentaires seront prises en charge dans le tableau de bord dès qu’elles seront disponibles.  
 
Apprenez-en davantage sur la [personnalisation de l’ensemble de normes de votre tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Inclusion des recommandations relatives aux identités dans le niveau gratuit d’Azure Security Center

Les recommandations de sécurité relatives aux identités et aux accès sont désormais généralement disponibles dans le niveau gratuit d’Azure Security Center. Cela fait partie de l’effort visant à atteindre la gratuité des fonctionnalités de gestion de la posture de sécurité cloud. Jusqu’à présent, ces recommandations n’étaient disponibles qu’au niveau tarifaire standard.

Voici des exemples de recommandations relatives aux identités et aux accès :

- « L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement. »
- « Au maximum trois propriétaires doivent être désignés pour votre abonnement. »
- « Les comptes déconseillés doivent être supprimés de votre abonnement. »

Si vous avez des abonnements au niveau tarifaire gratuit, ce changement affectera leurs degrés de sécurisation, car ils n’ont jamais été évalués sur le plan de la sécurité des identités et des accès.

Apprenez-en davantage sur les [recommandations relatives aux identités et aux accès](recommendations-reference.md#recs-identityandaccess).

Apprenez-en davantage sur la [gestion de la mise en œuvre de l’authentification multifacteur (MFA) sur vos abonnements](security-center-identity-access.md).



## <a name="march-2020"></a>Mars 2020

Les mises à jour du mois de mars incluent :

- [Disponibilité générale de l’automatisation de flux de travail](#workflow-automation-is-now-generally-available)
- [Intégration d’Azure Security Center avec le Centre d’administration Windows](#integration-of-azure-security-center-with-windows-admin-center)
- [Protection pour Azure Kubernetes Service](#protection-for-azure-kubernetes-service)
- [Amélioration de l’expérience juste-à-temps](#improved-just-in-time-experience)
- [Deux recommandations de sécurité pour les applications web déconseillées](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Disponibilité générale de l’automatisation de flux de travail

La fonctionnalité d’automatisation de flux de travail d’Azure Security Center est désormais généralement disponible. Elle permet de déclencher automatiquement Logic Apps sur des alertes et recommandations de sécurité. En outre, des déclencheurs manuels sont disponibles pour les alertes et toutes les recommandations pour lesquelles l’option de correction rapide est disponible.

Chaque programme de sécurité comprend plusieurs workflows pour la réponse aux incidents. Ces processus peuvent inclure l’envoi de notifications aux parties prenantes concernées, le lancement d’un processus de gestion des changements et l’application d’étapes de correction spécifiques. Les experts en sécurité vous conseillent d’automatiser le plus possible les étapes de ces processus. L’automatisation contribue à réduire la surcharge et à renforcer votre sécurité en garantissant que les étapes du processus se déroulent rapidement, de manière cohérente et selon les exigences que vous avez prédéfinies.

Pour plus d’informations sur les fonctionnalités automatiques et manuelles d’Azure Security Center pour l’exécution de vos flux de travail, consultez [Automatisation des workflows](workflow-automation.md).

Apprenez-en davantage sur la [création de Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Intégration d’Azure Security Center avec le Centre d’administration Windows

Vous pouvez désormais déplacer vos serveurs Windows locaux du Centre d’administration Windows directement vers Azure Security Center. Azure Security Center devient alors votre unique fenêtre pour l’affichage des informations de sécurité de toutes vos ressources du Centre d’administration Windows, à savoir les serveurs locaux, les machines virtuelles et les charges de travail PaaS supplémentaires.

Après avoir déplacé un serveur du Centre d’administration Windows vers Azure Security Center, vous pourrez effectuer les opérations suivantes :

- Afficher les alertes et recommandations de sécurité dans l’extension Security Center du Centre d’administration Windows.
- Afficher la posture de sécurité et des informations détaillées supplémentaires sur vos serveurs gérés par le Centre d’administration Windows dans le Security Center à l’intérieur du portail Azure (ou via une API).

Apprenez-en davantage sur la [façon d’intégrer Azure Security Center avec le Centre d’administration Windows](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Protection pour Azure Kubernetes Service

Azure Security Center développe ses fonctionnalités de sécurité de conteneur pour protéger Azure Kubernetes Service (AKS).

La plateforme open source populaire Kubernetes a été adoptée si largement qu’elle fait désormais figure de norme sectorielle pour l’orchestration de conteneurs. En dépit de cette implémentation largement répandue, il subsiste un manque de compréhension de la manière de sécuriser un environnement Kubernetes. La défense des surfaces d’attaque d’une application en conteneur requiert de l’expertise pour s’assurer que l’infrastructure est configurée de façon totalement sécurisée et constamment surveillée pour détecter des menaces potentielles.

La défense orchestrée par Azure Security Center comprend les composantes suivantes :

- **Détection et visibilité** : détection continue des instances AKS gérées à l’intérieur des abonnements inscrits auprès d’Azure Security Center.
- **Recommandations de sécurité** : recommandations actionnables pour vous aider à vous conformer aux meilleures pratiques en matière de sécurité pour AKS. Ces recommandations sont incluses dans votre degré de sécurisation pour garantir leur visibilité en lien avec la posture de sécurité de votre organisation. Voici un exemple de recommandation relative à AKS : « Le contrôle d’accès en fonction du rôle doit être utilisé pour limiter l’accès à un cluster Kubernetes Service ».
- **Protection contre les menaces** : grâce à une analyse continue de votre déploiement AKS, Azure Security Center vous avertit des menaces et activités malveillantes détectées au niveau de l’hôte et du cluster AKS.

Pour en savoir plus, consultez [Intégration d’Azure Kubernetes Service avec Security Center](defender-for-kubernetes-introduction.md).

Apprenez-en davantage sur les [fonctionnalités de sécurité de conteneur d’Azure Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Amélioration de l’expérience juste-à-temps

Les fonctionnalités, le fonctionnement et l’interface utilisateur des outils juste-à-temps de l’Azure Security Center qui sécurisent vos ports de gestion ont été améliorés comme suit : 

- **Champ de justification** : lors de la demande d’accès à une machine virtuelle via la page Juste-à-temps du portail Azure, un nouveau champ facultatif est disponible pour entrer une justification de la demande. Le journal d’activité permet de suivre les informations entrées dans ce champ. 
- **Nettoyage automatique des règles JAT redondantes** : chaque fois que vous mettez à jour une stratégie JAT, un outil de nettoyage s’exécute automatiquement pour vérifier la validité de votre ensemble de règles. L’outil recherche les incompatibilités entre les règles de votre stratégie et les règles du groupe de sécurité réseau. Si l’outil de nettoyage détecte une incompatibilité, il en détermine la cause et, lorsque cela ne présente aucun risque, supprime les règles intégrées qui ne sont plus nécessaires. Le nettoyeur ne supprime jamais les règles que vous avez créées. 

Apprenez-en davantage sur la [fonctionnalité d’accès JAT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Deux recommandations de sécurité pour les applications web déconseillées

Deux recommandations de sécurité relatives aux applications web sont déconseillées : 

- Les règles relatives aux applications web sur des groupes de sécurité réseau IaaS doivent être renforcées.
    (Stratégie associée : Les règles de groupe de sécurité réseau pour les applications web IaaS doivent être renforcées)

- L’accès à App Services doit être limité.
    (Stratégie associée : L’accès à App Services doit être restreint [préversion])

Ces recommandations n’apparaissent plus dans la liste de recommandations d’Azure Security Center. Les stratégies associées ne seront plus incluses dans l’initiative nommée « Security Center par défaut ».

Apprenez-en davantage sur les [recommandations de sécurité](recommendations-reference.md).




## <a name="february-2020"></a>Février 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Détection d’attaque sans fichier pour Linux (préversion)

À mesure que les attaquants intensifient le recours à des méthodes de plus en plus furtives pour éviter d’être détectés, Azure Security Center étend la détection d’attaque sans fichier pour Linux, en plus de Windows. Les attaques sans fichier exploitent des vulnérabilités logicielles, injectent des charges utiles malveillantes dans des processus système inoffensifs et se cachent en mémoire. Ces techniques sont les suivantes :

- réduire ou éliminer les traces de logiciels malveillants sur disque ;
- réduire considérablement les risques de détection par des solutions d’analyse de programmes malveillants sur disque.

Pour contrer cette menace, Azure Security Center a publié une fonctionnalité de détection d’attaque sans fichier pour Windows en octobre 2018, et a maintenant étendu la détection d’attaque sans fichier également sur Linux. 



## <a name="january-2020"></a>Janvier 2020

### <a name="enhanced-secure-score-preview"></a>Amélioration du degré de sécurisation (préversion)

Une version améliorée de la fonctionnalité de degré de sécurisation d’Azure Security Center est désormais disponible en préversion. Dans cette version, plusieurs recommandations sont regroupées en contrôles de sécurité qui reflètent mieux vos surfaces d’attaque vulnérables (et, par exemple, restreignent l’accès aux ports de gestion).

Familiarisez-vous avec les changements apportés au degré de sécurisation au cours de la phase de préversion, et épinglez d’autres corrections qui vous aideront à sécuriser davantage votre environnement.

Pour en savoir plus, consultez [Version améliorée du degré de sécurisation (préversion)](secure-score-security-controls.md).



## <a name="november-2019"></a>Novembre 2019

Les mises à jour en novembre sont les suivantes :
 - [Protection contre les menaces pour Azure Key Vault dans les régions d’Amérique du Nord (préversion)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [La protection contre les menaces pour Stockage Azure inclut le filtrage de la réputation des programmes malveillants](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Automatisation des workflows avec Azure Logic Apps (préversion)](#workflow-automation-with-logic-apps-preview)
 - [Disponibilité générale d’un correctif rapide pour les ressources en bloc](#quick-fix-for-bulk-resources-generally-available)
 - [Analyser des images conteneur pour détecter les vulnérabilités (préversion)](#scan-container-images-for-vulnerabilities-preview)
 - [Autres normes de conformité réglementaire (préversion)](#additional-regulatory-compliance-standards-preview)
 - [Protection contre les menaces pour Azure Kubernetes Service (préversion)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Évaluation des vulnérabilités des machines virtuelles (préversion)](#virtual-machine-vulnerability-assessment-preview)
 - [Advanced Data Security pour les serveurs SQL sur Machines virtuelles Microsoft Azure (préversion)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Prise en charge de stratégies personnalisées (préversion)](#support-for-custom-policies-preview)
 - [Extension de la couverture d’Azure Security Center avec une plateforme pour la communauté et les partenaires](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Intégrations avancées avec exportation de recommandations et d’alertes (préversion)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Intégrer des serveurs locaux à Security Center à partir du Centre d’administration Windows (préversion)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Protection contre les menaces pour Azure Key Vault dans les régions d’Amérique du Nord (préversion)

Azure Key Vault est un service essentiel pour la protection des données et l’amélioration des performances des applications cloud, offrant la possibilité de gérer de manière centralisée les clés, les secrets, les clés de chiffrement et les stratégies dans le cloud. Étant donné qu’Azure Key Vault stocke des données sensibles et stratégiques, ses coffres de clés et les données qui y sont stockées exigent une sécurité maximale.

La prise en charge par Azure Security Center de la protection d’Azure Key Vault contre les menaces fournit une couche supplémentaire de veille de sécurité qui détecte les tentatives inhabituelles et potentiellement nuisibles d’accès aux coffres de clés ou d’exploitation de ceux-ci. Cette nouvelle couche de protection permet aux clients de traiter les menaces pesant sur leurs coffres de clés sans être experts en sécurité, ainsi que de gérer des systèmes de surveillance de la sécurité. Cette fonctionnalité est en préversion publique dans les régions d’Amérique du Nord.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>La protection contre les menaces pour Stockage Azure inclut le filtrage de la réputation des logiciels malveillants

La protection contre les menaces pour le service Stockage Azure offre de nouvelles fonctionnalités de détection optimisées par les renseignements sur les menaces Microsoft, qui permettent de détecter les chargements de programmes malveillants sur le service Stockage Azure à partir d’une de réputation de hachage et des accès suspects en provenance d’un nœud de sortie Tor actif (proxy d’anonymisation). Vous pouvez désormais afficher les programmes malveillants détectés parmi les comptes de stockage à l’aide d’Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automatisation des flux de travail avec Azure Logic Apps (préversion)

Les organisations dont la sécurité, l’informatique et les opérations sont gérées de manière centralisée implémentent des processus de flux de travail internes pour conduire les actions requises en leur sein en cas de détection d’incohérences dans leur environnement. Dans de nombreux cas, ces flux de travail étant des processus reproductibles, une automatisation peut considérablement simplifier leur exécution au sein de l’organisation.

Aujourd’hui, nous introduisons dans Security Center une nouvelle fonctionnalité qui permet aux clients de créer des configurations d’automatisation tirant parti d’Azure Logic Apps, et d’élaborer des stratégies qui les déclencheront automatiquement en fonction de résultats d’ASC spécifiques, tels que des recommandations ou des alertes. Il est possible de configurer une application logique pour effectuer toute action personnalisée prise en charge par le vaste éventail de connecteurs d’applications logiques, ou d’utiliser l’un des modèles fournis par Security Center, tels que l’envoi d’un e-mail ou l’ouverture d’un ticket ServiceNow&trade;.

Pour plus d’informations sur les fonctionnalités automatiques et manuelles d’Azure Security Center disponibles pour l’exécution de vos flux de travail, consultez [Automatisation des workflows](workflow-automation.md).

Pour en savoir plus sur la création d’applications logiques, consultez [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Disponibilité générale d’un correctif rapide pour les ressources en bloc

Compte tenu des nombreuses tâches confiées aux utilisateurs en lien avec le degré de sécurisation, il peut devenir difficile de résoudre efficacement les problèmes survenant au sein d’un part informatique de grande taille.

Pour simplifier la correction des configurations de sécurité incorrectes et appliquer rapidement des recommandations à un lot de ressources afin d’améliorer votre degré de sécurisation, utilisez une remédiation par correctif rapide.

Cette opération vous permet de sélectionner les ressources auxquelles à corriger et de lancer une action de correction qui configure le paramètre à votre place.

Un correctif rapide est dès aujourd’hui en disponibilité générale pour les clients dans la page Recommandations du Security Center.

Pour connaître les recommandations assorties d’un correctif rapide, consultez le [guide de référence relatif aux recommandations de sécurité](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Analyser des images de conteneur pour détecter des vulnérabilités (préversion)

Azure Security Center peut désormais analyser des images de conteneur dans Azure Container Registry pour rechercher des vulnérabilités.

Le balayage d’image fonctionne en analysant le fichier image du conteneur, puis en vérifiant s’il existe des vulnérabilités connues (optimisées par Qualys).

L’analyse proprement dite est déclenchée automatiquement lors de l’envoi (push) de nouvelles images de conteneur au Registre de conteneurs Azure (Azure Container Registry). Les vulnérabilités détectées sont présentées en tant que recommandations d’Azure Security Center, et incluses dans le degré de sécurisation Azure avec des informations sur la façon de les corriger afin de réduire la surface d’attaque autorisée.


### <a name="additional-regulatory-compliance-standards-preview"></a>Normes de conformité réglementaire supplémentaires (préversion)

Le tableau de bord Conformité réglementaire fournit des insights sur votre posture de conformité en fonction des évaluations du Security Center. Le tableau de bord indique la conformité de votre environnement avec des contrôles et exigences stipulés par des normes réglementaires et des benchmarks sectoriels spécifiques, et fournit des recommandations prescriptives sur la façon de s’y conformer.

À ce jour, ce tableau de bord prend en charge quatre normes intégrées :  Azure CIS 1.1.0, PCI-DSS, ISO 27001 et SOC-TSP. Nous annonçons à présent la pris en charge en préversion publique de normes supplémentaires, à savoir : NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM et UK Official avec UK NHS. Nous publions également une version mise à jour d’Azure CIS 1.1.0, qui couvre davantage de contrôles à partir de l’extensibilité standard et améliorée.

[Apprenez-en davantage sur la personnalisation de l’ensemble de normes de votre tableau de bord de conformité réglementaire](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Protection contre les menaces pour Azure Kubernetes Service (préversion)

Kubernetes devient rapidement la nouvelle norme pour le déploiement et la gestion de logiciels dans le cloud. Rares sont les utilisateurs qui ont une expérience approfondie de Kubernetes et nombreux sont ceux qui se concentrent uniquement sur l’ingénierie et l’administration générales en négligeant la sécurité. Il convient de configurer l’environnement Kubernetes avec soin pour le sécuriser en s’assurant qu’aucune porte laissée ouverte, donnant sur une surface d’attaque de conteneurs, n’est exposée pour des attaquants. Azure Security Center étend son support en lien avec les conteneurs à un service Azure qui connaît une croissance des plus rapides, à savoir Azure Kubernetes Service (AKS).

Les nouvelles fonctionnalités de cette préversion publique sont les suivantes :

- **Détection et visibilité** : détection continue des instances AKS gérées à l’intérieur des abonnements inscrits de Security Center.
- **Recommandations concernant le degré de sécurisation** : éléments actionnables pour aider les clients à se conformer aux meilleures pratiques de sécurité pour AKS, et renforcer leur degré de sécurisation. Ces recommandations incluent des éléments tels que « Le contrôle d’accès en fonction du rôle doit être utilisé pour limiter l’accès à un cluster de service Kubernetes ».
- **Détection des menaces** : analyses basées sur un hôte ou un cluster, par exemple, « un conteneur privilégié détecté ».


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Évaluation des vulnérabilités des machines virtuelles (préversion)

Les applications installées sur les machines virtuelles peuvent souvent présenter des vulnérabilités susceptibles d’entraîner une violation de ces machines. Nous annonçons que le niveau de service Standard de Security Center comprend une évaluation des vulnérabilités intégrée pour les machines virtuelles sans frais supplémentaires. L’évaluation des vulnérabilités, optimisée par Qualys dans la préversion publique, vous permet d’analyser en continu toutes les applications installées sur une machine virtuelle afin d’épingler celles qui sont vulnérables, et de présenter les résultats sur le portail Security Center. Security Center prend en charge toutes les opérations de déploiement afin que l’utilisateur n’ait aucun travail supplémentaire à accomplir. À l’avenir, nous envisageons de fournir des options d’évaluation des vulnérabilités pour satisfaire aux besoins uniques de nos clients.

[Apprenez-en davantage sur l’évaluation des vulnérabilités de vos machines virtuelles Azure](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Advanced Data Security pour SQL Server sur machines virtuelles Azure (préversion)

Le support d’Azure Security Center en lien avec la protection contre les menaces et l’évaluation des vulnérabilités pour les bases de données SQL s’exécutant sur des machines virtuelles IaaS est désormais en préversion.

La fonctionnalité [Évaluation des vulnérabilités](../azure-sql/database/sql-vulnerability-assessment.md) est un service simple à configurer, qui vous permet de découvrir, suivre et de corriger des vulnérabilités de base de données potentielles. Il offre une visibilité sur votre posture de sécurité en lien avec le degré de sécurisation Azure, et inclut des étapes pour résoudre des problèmes de sécurité et renforcer la protection de votre base de données.

La [protection avancée contre les menaces](../azure-sql/database/threat-detection-overview.md) détecte des activités anormales indiquant des tentatives inhabituelles et potentiellement dangereuses d’accès à votre serveur SQL ou d’exploitation de celui-ci. Elle supervise en permanence votre base de données pour détecter des activités suspectes, et envoie des alertes de sécurité orientées action en cas de modèles d’accès anormaux à la base de données. Ces alertes fournissent des détails sur les activités suspectes et des mesures recommandées pour examiner et atténuer la menace.


### <a name="support-for-custom-policies-preview"></a>Prise en charge de stratégies personnalisées (préversion)

Azure Security Center prend désormais en charge les stratégies personnalisées (en préversion).

Nos clients ont exprimé le souhait d’étendre la couverture de leurs évaluations de la sécurité actuelles dans Security Center en y ajoutant leurs propres évaluations de la sécurité, basées sur des stratégies qu’ils créent dans Azure Policy. Grâce à la prise en charge des stratégies personnalisées, c’est désormais possible.

Ces stratégies personnalisées font désormais partie des recommandations d’Azure Security Center, du degré de sécurisation et du tableau de bord des normes de conformité réglementaire. Avec la prise en charge des stratégies personnalisées, vous pouvez désormais créer une initiative personnalisée dans Azure Policy, puis l’ajouter en tant que stratégie dans Azure Security Center et la visualiser en tant que recommandation.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Extension de la couverture d’Azure Security Center avec une plateforme pour la communauté et les partenaires

Utilisez Security Center pour recevoir des recommandations, non seulement de Microsoft, mais aussi de solutions existantes de partenaires tels que Check Point, Tenable et CyberArk, avec de nombreuses intégrations supplémentaires à venir.  Le flux d’intégration simple de Security Center peut connecter vos solutions existantes à Security Center, ce qui vous permet d’afficher les recommandations relatives à votre posture de sécurité dans un emplacement unique, de générer des rapports unifiés et de tirer parti de toutes les fonctionnalités de Security Center en lien avec les recommandations intégrées et de partenaires. Vous pouvez également exporter les recommandations de Security Center vers des produits partenaires.

[Apprenez-en davantage sur l’Association de sécurité intelligente de Microsoft](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Intégrations avancées avec exportation de recommandations et d’alertes (préversion)

Afin d’activer des scénarios de niveau entreprise en plus de Security Center, il est désormais possible d’utiliser des alertes et recommandations du Security Center dans des emplacements supplémentaires, à l’exception du portail Azure ou de l’API. Vous pouvez les exporter directement vers un Event Hub et des espaces de travail Log Analytics. Voici quelques flux de travail que vous pouvez créer autour de ces nouvelles fonctionnalités :

- Avec une exportation vers un espace de travail Log Analytics, vous pouvez créer des tableaux de bord personnalisés avec Power BI.
- Avec une exportation vers Event Hub, vous pouvez exporter les alertes et recommandations d’Azure Security Center vers vos informations de sécurité et gestion d’événements (SIEM) tierces, vers une solution tierce ou vers Azure Data Explorer.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Intégrer des serveurs locaux à Security Center à partir du Centre d’administration Windows (préversion)

Le Centre d’administration Windows est un portail de gestion pour les serveurs Windows non déployés dans Azure, qui offre à ceux-ci plusieurs fonctionnalités de gestion Azure, telles que la sauvegarde et les mises à jour système. Nous avons récemment ajouté la possibilité d’intégrer ces serveurs non-Azure de façon à ce qu’ils soient protégés par ASC directement à partir du Centre d’administration Windows.

Avec cette nouvelle expérience, les utilisateurs devront intégrer un serveur WAC à Azure Security Center et activer l’affichage de des alertes et recommandations de sécurité de celui-ci directement dans le Centre d’administration Windows.


## <a name="september-2019"></a>Septembre 2019

Les mises à jour en septembre sont les suivantes :

 - [Gestion des règles avec des améliorations des contrôles d’application adaptatifs](#managing-rules-with-adaptive-application-controls-improvements)
 - [Contrôler la recommandation de sécurité de conteneur à l’aide d’Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Gestion des règles avec des améliorations des contrôles d’application adaptatifs

L’expérience de gestion des règles pour les machines virtuelles à l’aide de contrôles d’application adaptatifs a été améliorée. Les contrôles d’application adaptatifs d’Azure Security Center vous aident à contrôler les applications qui peuvent s’exécuter sur vos machines virtuelles. En plus d’une amélioration générale de la gestion des règles, un nouvel avantage vous permet de contrôler les types de fichiers qui seront protégés lorsque vous ajoutez une nouvelle règle.

[Apprenez-en davantage sur les contrôles d’application adaptatifs](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Contrôler la recommandation de sécurité de conteneur à l’aide d’Azure Policy

La recommandation d’Azure Security Center pour corriger des vulnérabilités dans la sécurité de conteneur peut désormais être activée ou désactivée via Azure Policy.

Pour afficher vos stratégies de sécurité activées, dans le Security Center, ouvrez la page Stratégie de sécurité.


## <a name="august-2019"></a>Août 2019

Les mises à jour en août sont les suivantes :

 - [Accès de machine virtuelle juste-à-temps (JAT) pour Pare-feu Azure](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Correction en un seul clic pour améliorer votre posture de sécurité (préversion)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Gestion multilocataire](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Accès de machine virtuelle juste-à-temps (JAT) pour le Pare-feu Azure 

L’accès de machine virtuelle juste-à-temps (JAT) pour le Pare-feu Azure est désormais généralement disponible. Utilisez-le pour sécuriser vos environnements protégés par un Pare-feu Azure en plus de vos environnements protégés par un groupe de sécurité réseau.

L’accès de machine virtuelle JAT réduit l’exposition aux attaques volumétriques de réseau en fournissant aux machines virtuelles un accès contrôlé uniquement si nécessaire, à l’aide de vos règles de groupe de sécurité réseau et de Pare-feu Azure.

Lorsque vous activez l’accès JAT pour vos machines virtuelles, vous créez une stratégie qui détermine les ports à protéger, la durée pendant laquelle les ports doivent rester ouverts et les adresses IP approuvées à partir desquelles ces ports sont accessibles. Cette stratégie vous permet contrôler ce que les utilisateurs peuvent faire quand ils demandent l’accès.

Les demandes étant consignées dans le journal d’activité Azure, vous pouvez surveiller et vérifier facilement l’accès. La page juste-à-temps vous aide également à identifier rapidement les machines virtuelles existantes pour lesquelles l’accès JAT est activé et celles pour lesquelles il est recommandé.

[Apprenez-en davantage sur le Pare-feu Azure](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Correction en un seul clic pour améliorer votre posture de sécurité (préversion)

Le degré de sécurisation est un outil qui vous aide à évaluer la sécurité de la charge de travail. Il examine vos recommandations de sécurité et les hiérarchise de façon à ce que vous sachiez celles que vous devez appliquer en premier. Cela vous aide à épingler les vulnérabilités de sécurité les plus graves pour hiérarchiser l’investigation.

Afin de simplifier la correction des configurations de sécurité incorrectes et de vous aider à améliorer rapidement votre degré de sécurisation, nous avons ajouté une nouvelle fonctionnalité qui vous permet d’appliquer une recommandation à un lot de ressources en un seul clic.

Cette opération vous permettra de sélectionner les ressources auxquelles vous souhaitez appliquer la correction, et de lancer une action de correction qui configurera le paramètre pour vous.

Pour connaître les recommandations assorties d’un correctif rapide, consultez le [guide de référence relatif aux recommandations de sécurité](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Gestion multilocataire

Azure Security Center prend désormais en charge les scénarios de gestion mutualisée dans Azure Lighthouse. Cela vous permet d’acquérir une visibilité et de gérer la posture de sécurité de plusieurs locataires dans Azure Security Center. 

[Apprenez-en davantage sur les expériences de gestion mutualisée](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Juillet 2019

### <a name="updates-to-network-recommendations"></a>Mises à jour des recommandations pour le réseau

Azure Security Center a publié de nouvelles recommandations pour la mise en réseau, et amélioré des recommandations existantes. Désormais, l’utilisation d’Azure Security Center garantit une meilleure protection réseau pour vos ressources. 

[Apprenez-en davantage sur les recommandations pour le réseau](recommendations-reference.md#recs-networking).


## <a name="june-2019"></a>Juin 2019

### <a name="adaptive-network-hardening---generally-available"></a>Disponibilité générale du renforcement du réseau adaptatif

L’une des principales surfaces d’attaque pour les charges de travail s’exécutant dans le cloud public est celle des interconnexions avec l’Internet public. Nos clients trouvent difficile de savoir quelles règles de groupe de sécurité mettre en place pour s’assurer que les charges de travail Azure ne sont disponibles que pour les plages sources requises. Cette fonctionnalité permet à Security Center d’apprendre le trafic réseau et les modèles de connectivité des charges de travail Azure, et fournit des recommandations de règle de groupe de sécurité réseau pour les machines virtuelles accessibles via Internet. Cela permet à nos clients de mieux configurer leurs stratégies d’accès réseau et de limiter leur exposition aux attaques. 

[Apprenez-en davantage sur le renforcement du réseau adaptative](security-center-adaptive-network-hardening.md).