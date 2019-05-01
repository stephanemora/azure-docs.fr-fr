---
title: Gérer les coûts AWS et utilisation dans Azure Cost Management
description: Cet article vous aide à comprendre comment utiliser des budgets et analyse des coûts dans Cost Management pour gérer vos coûts AWS et votre utilisation.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 007b6c409dde248a4dde7a15fd16b543add234bc
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870311"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Gérer les coûts AWS et l’utilisation dans Azure

Une fois que vous avez configuré et configuré l’intégration de rapport de coût de AWS et d’utilisation pour Azure Cost Management, vous êtes prêt à commencer à gérer vos coûts AWS et votre utilisation. Cet article vous aide à comprendre comment utiliser des budgets et analyse des coûts dans Cost Management pour gérer vos coûts AWS et votre utilisation.

Si vous n’avez pas déjà configuré l’intégration, consultez [définir installer et configurer l’intégration de rapports d’utilisation de AWS](aws-integration-set-up-configure.md).

_Avant de commencer_: Si vous n’êtes pas familiarisé avec l’analyse des coûts, consultez le [Explorer et analyser les coûts avec analyse des coûts](quick-acm-cost-analysis.md) Guide de démarrage rapide. Et, si vous n’êtes pas familiarisé avec les budgets dans Azure, consultez le [créer et gérer des budgets Azure](tutorial-acm-create-budgets.md) didacticiel.

## <a name="view-aws-costs-in-cost-analysis"></a>Afficher les coûts AWS dans l’analyse des coûts

Aux coûts AWS sont disponibles dans l’analyse des coûts dans les étendues suivantes :

- Comptes AWS lié sous un groupe d’administration
- Coûts du compte AWS lié
- Coûts du compte AWS consolidés

Les sections suivantes décrivent comment utiliser les étendues que vous voyez des données d’utilisation et de coût pour chacun d'entre eux.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Afficher les comptes AWS lié sous un groupe d’administration

Affichage des coûts à l’aide de l’étendue de groupe d’administration est la seule façon d’afficher les coûts agrégées en provenance de différents abonnements et comptes liés. À l’aide d’un groupe d’administration fournit une vue du cloud.

Dans l’analyse des coûts, ouvrir le sélecteur de portée, puis sélectionnez le groupe d’administration qui contient vos comptes AWS lié. Voici un exemple d’image dans le portail Azure :

![Exemple de la vue étendue Select](./media/aws-integration-manage/select-scope01.png)



Voici un exemple indiquant le coût de groupe d’administration analyse du coût, regroupé par fournisseur (Azure et AWS).

![Exemple illustrant les coûts d’Azure et AWS pour l’analyse des coûts dans un trimestre](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Afficher les coûts de compte AWS lié

Pour afficher les coûts de compte AWS lien, ouvrir le sélecteur de portée et sélectionnez le AWS compte lié. Notez que les comptes liés sont associés à un groupe d’administration, tel que défini dans le connecteur AWS.

Voici un exemple que présente la sélection d’AWS lié étendue du compte.

![Exemple de la vue étendue Select](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Vue AWS consolidé les coûts de compte

Pour afficher les AWS compte consolidé compte consolidé les coûts, ouvrir le sélecteur de portée et sélectionnez le AWS. Voici un exemple qui présente la sélection d’AWS consolidés étendue du compte.

![Exemple de la vue étendue Select](./media/aws-integration-manage/select-scope03.png)



Cette étendue fournit qu'une vue agrégée de toutes les AWS lié comptes associés au compte AWS consolidés. Voici un exemple illustrant les coûts pour AWS consolidés compte, regroupée par nom de service.

![Exemple illustrant AWS consolidé les coûts dans l’analyse des coûts](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensions disponibles pour le filtrage et regroupement

Le tableau suivant décrit les dimensions permettent de regrouper et filtrer par dans l’analyse des coûts.

| Dimension | En-tête d’Amazon CUR | Étendues | Commentaires |
| --- | --- | --- | --- |
| Zone de disponibilité | lineitem/AvailabilityZone | Tous |   |
| Lieu | produit/région | Tous |   |
| Compteur |   | Tous |   |
| Catégorie du compteur | lineItem/ProductCode | Tous |   |
| Sous-catégorie du compteur | lineitem/UsageType | Tous |   |
| Opération | lineItem/Operation | Tous |   |
| Ressource | lineItem/ResourceId | Tous |   |
| Type de ressource | product/instanceType | Tous | Si le produit/instanceType est null, lineItem/UsageType est utilisé. |
| ResourceGuid | N/A | Tous | GUID de compteur Azure. |
| Nom du service | produit/ProductName | Tous | Si le produit/ProductName est null, lineItem/ProductCode est utilisé. |
| Niveau de service |   |   |   |
| Identifiant d’abonnement | lineItem/UsageAccountId | Groupe de compte et la gestion consolidé |   |
| Nom d’abonnement | N/A | Groupe de compte et la gestion consolidé | Noms de compte sont collectées à l’aide de l’API d’organisation de AWS. |
| Tag | resourceTags/\* | Tous | Le _utilisateur :_ préfixe est supprimé à partir de balises de défini par l’utilisateur pour autoriser les balises entre clouds. Le _aws :_ préfixe est laissé intact. |
| ID de compte de facturation | bill/PayerAccountId | Groupe d’administration |   |
| Nom du compte de facturation | N/A | Groupe d’administration | Noms de compte sont collectées à l’aide de l’API d’organisation de AWS. |
| Fournisseur | N/A | Groupe d’administration | AWS ou Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Ensemble des budgets sur les étendues d’AWS

Utiliser les budgets pour gérer de manière proactive les coûts et le lecteur de responsabilité de votre organisation. Budgets sont définies sur le compte AWS consolidés et AWS étendues de compte lié. Voici un exemple de budgets d’un compte AWS consolidés indiqué dans la gestion des coûts :

![Exemple illustrant les budgets pour AWS consolidés compte](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Processus de collecte de données AWS

Après avoir configuré le connecteur AWS, collecte de données et les processus de découverte démarrent. Il peut prendre plusieurs heures pour collecter toutes les données d’utilisation. La durée dépend :

- Le temps nécessaire pour traiter les fichiers CUR qui se trouvent dans le compartiment AWS S3.
- Le temps nécessaire pour créer le compte AWS consolidé et des étendues de compte AWS lié.
- L’heure et la fréquence de AWS écrivez les fichiers de rapport d’utilisation et de coût dans le compartiment S3

## <a name="aws-integration-pricing"></a>Tarification de l’intégration de AWS

Chaque connecteur AWS obtient 90 jours d’essai gratuits. La version préliminaire publique, il est gratuit.

Le tarif est de 1 % des coûts mensuels AWS. Chaque mois vous sont facturés en fonction de vos coûts facturés le mois précédent.

Accéder aux API de AWS peut entraîner des frais supplémentaires.

## <a name="aws-integration-limitations"></a>Limitations de l’intégration de AWS

- Cost Management ne prend pas en charge les rapports de coût qui contiennent plusieurs types de devise. Un message d’erreur s’affiche si vous sélectionnez une étendue qui a plusieurs devises.
- Connecteurs de cloud ne prennent pas en charge AWS GovCloud (US), AWS Gov ou en Chine AWS.
- Gestion des coûts montre AWS _les coûts d’utilisation_ uniquement. Taxe, prise en charge, les remboursements, RI, crédits ou autres types de frais ne sont pas encore pris en charge.

## <a name="troubleshooting-aws-integration"></a>Résolution des problèmes d’intégration de AWS

Utilisez les informations de dépannage suivantes pour résoudre les problèmes courants.

### <a name="no-permission-to-aws-linked-accounts"></a>Aucune autorisation de comptes AWS liés

Il existe deux façons d’obtenir les autorisations d’accès aux coûts de comptes AWS lié :

- Accédez au groupe d’administration qui contient les comptes AWS lié.
- Demandez à une personne de vous accorder l’autorisation au compte AWS lié.

Par défaut, le créateur du connecteur AWS est le propriétaire de tous les objets que le connecteur créé. Notamment, le AWS compte consolidé et l’AWS compte lié.

### <a name="collection-failed-with-assumerole"></a>Échoué avec AssumeRole de collection

Cette erreur signifie que la gestion des coûts est impossible d’appeler l’API de AssumeRole AWS. Ce problème peut se produire en raison d’un problème avec la définition de rôle. Vérifiez que les conditions suivantes sont remplies :

- L’ID externe est identique à celui de la définition de rôle et de la définition du connecteur.
- Le type de rôle est défini sur **AWS un autre compte appartenant à vous-même ou à des tiers 3e.**
- Le **MFA nécessitent** choix est désactivé.
- Le compte AWS approuvé dans le rôle AWS est _432263259397_.

### <a name="collection-failed-with-access-denied"></a>Collection a échoué avec l’accès refusé

Ce message d’erreur signifie que Cost Management ne peut pas accéder aux fichiers CUR stockés dans le compartiment Amazon S3. Assurez-vous que la stratégie AWS JSON attachée au rôle ressemble à l’exemple indiqué au bas de la [créer un rôle et une stratégie dans AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) section.

### <a name="connector-error-with-failedtofindreport"></a>Erreur de connecteur avec FailedToFindReport

Cette erreur indique que Cost Management ne peut pas trouver le rapport de coût et d’utilisation qui a été défini dans le connecteur. Assurez-vous qu’il n’est pas supprimé et que la stratégie AWS JSON attachée au rôle ressemble à l’exemple indiqué au bas de la [créer un rôle et une stratégie dans AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) section.

## <a name="next-steps"></a>Étapes suivantes

- Si vous n’avez pas déjà configuré votre environnement Azure avec des groupes d’administration, consultez [configuration des groupes d’administration initiale](../governance/management-groups/index.md#initial-setup-of-management-groups).
