---
title: Qu'est-ce qu’une version d'évaluation ? Marketplace commercial Microsoft
description: Description de la fonctionnalité Version d’évaluation du marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 06/19/2020
ms.openlocfilehash: abad72145b095b4da77ec499f936c6912fd970a6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229446"
---
# <a name="what-is-a-test-drive"></a>Qu'est-ce qu’une version d'évaluation ?

Une version d’évaluation est un bon moyen de présenter votre offre à vos clients potentiels en leur donnant la possibilité de *l’essayer avant de l’acheter*, ce qui se traduit par une hausse du taux de conversion et par la génération de prospects de qualité. Une version d’évaluation donne vie à votre produit dans un scénario d’implémentation réel, tout en générant des prospects de qualité.

Les versions d’évaluation sont des instances gérées qui déploient votre solution ou application à la demande pour les clients qui le demandent. Une fois attribuée, l’instance de la version d’évaluation est utilisable pendant un laps de temps défini, puis elle est supprimée afin de céder la place à un autre client.

En tant qu’éditeur, vous gérez et configurez les paramètres de la version d’évaluation dans Espace partenaires. Les détails de la configuration technique varient en fonction du type d’offre avec lequel vous travaillez. Pour obtenir des instructions détaillées, consultez le lien sous [Étape suivante](#next-step) à la fin de cette rubrique.

Les clients potentiels découvrent votre version d’évaluation sur le marketplace commercial. Ils fournissent leurs coordonnées et acceptent les conditions générales et la politique de confidentialité de votre offre, puis accèdent à votre environnement préconfiguré pour l’essayer pendant une période déterminée. Les clients bénéficient d’un essai pratique et autonome des principaux avantages et fonctionnalités de votre produit et vous recevez un prospect de valeur.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

En tant qu’éditeur, vous gérez et configurez les paramètres de la version d’évaluation au sein d’Espace partenaires. Après l’installation, elle devient une instance gérée qui sera déployée à la demande pour le client qui le demande. Une fois attribuée, l’instance de la version d’évaluation est utilisable pendant un laps de temps défini, puis elle est supprimée afin de céder la place à un autre client.

## <a name="types-of-test-drives"></a>Types de versions d’évaluation

Différentes versions d’évaluation sont disponibles sur le marketplace commercial pour certaines offres, selon le type de produit, de scénario et de marketplace sur lequel vous vous trouvez :

- Azure Resource Manager
- Version d’évaluation hébergée
    - Dynamics 365 pour Business Central
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Application logique
- Power BI

Pour plus d’informations sur la configuration de l’une de ces versions d’évaluation, suivez le lien sous [Étape suivante](#next-step) à la fin de cette rubrique.

### <a name="azure-resource-manager-test-drive"></a>Version d’évaluation d’Azure Resource Manager

Ce modèle de déploiement contient toutes les ressources Azure constituant votre solution. Les produits adaptés à ce scénario utilisent uniquement des ressources Azure. Il s’agit de la seule option de version d’évaluation pour les offres de machines virtuelles ou d’applications Azure.

### <a name="hosted-test-drive"></a>Version d’évaluation hébergée

Une version d’évaluation hébergée simplifie la configuration en permettant à Microsoft d’héberger et de gérer le service qui effectue l’approvisionnement, le déploiement et le déprovisionnement de l’utilisateur de la version d’évaluation. Si vous disposez d’une offre sur Microsoft AppSource, créez votre version d’évaluation pour vous connecter à une instance Dynamics AX/CRM ou à toute autre ressource au-delà d’Azure. Utilisez ce type pour les offres AppSource afin de vous connecter à ces offres Dynamics 365 :

- Utilisez [Dynamics 365 for Business Central](partner-center-portal/create-new-operations-offer.md) pour un système central de planification des ressources de l’entreprise tel que les finances, les opérations, la chaîne logistique et CRM.
- Utilisez [Dynamics 365 for Customer Engagement](partner-center-portal/create-new-customer-engagement-offer.md) pour un système d’engagement des clients tel que les ventes, le service, le service de projet et le service sur le terrain.
- Utilisez [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) pour un système de planification des ressources d’entreprise dans le domaine des finances et des opérations, comme les finances, les opérations, la fabrication et la chaîne logistique.

### <a name="logic-app-test-drive"></a>Version d’évaluation d’une application logique

Ce type de version d’évaluation n’est pas hébergé par Microsoft. Utilisez-le pour vous connecter à une offre Dynamics 365 ou à une autre ressource personnalisée.

### <a name="power-bi-test-drive"></a>Version d’évaluation de Power BI

Il s’agit simplement d’un lien incorporé vers un tableau de bord personnalisé. Tous les produits dotés uniquement d’un contrôle Power BI interactif doivent utiliser ce type de version d’évaluation.

## <a name="transforming-examples"></a>Exemples de transformations

Le processus de transformation d’une architecture de ressources en version d’évaluation peut être décourageant. Consultez ces exemples illustrant la meilleure façon de transformer les architectures actuelles.

[Transformer un modèle de site web en version d’évaluation](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Transformer un modèle d’ordinateur virtuel en version d’évaluation](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Transformer un modèle Resource Manager existant en version d’évaluation](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Générer des prospects à partir de votre version d’évaluation

Une version d’évaluation de marketplace commercial est un excellent outil pour les spécialistes marketing. Nous vous recommandons de l’intégrer dans vos efforts de mise sur le marché lorsque vous vous lancez afin de générer davantage de prospects pour votre entreprise. Pour obtenir des instructions détaillées, consultez [Prospects de votre offre de marketplace commercial](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Si vous concluez une affaire avec un prospect ayant utilisé une version d’évaluation, n’oubliez pas de la consigner sur [Microsoft Partner Center Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect). Nous aimerions savoir dans quels domaines une version d’évaluation a aidé le client à atteindre ses objectifs.

## <a name="other-resources"></a>Autres ressources

Ressources supplémentaires pour version d’évaluation :

- [Meilleures pratiques techniques](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Vue d’ensemble](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF ; veillez à désactiver votre bloqueur de fenêtres publicitaires)

## <a name="next-step"></a>Étape suivante

- [Configuration technique de la version d’évaluation](test-drive-technical-configuration.md)
