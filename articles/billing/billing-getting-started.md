---
title: Éviter les coûts imprévus et gérer la facturation dans Azure
description: Découvrez comment éviter des frais inattendus sur votre facture Azure. Utilisez les fonctionnalités de gestion et de suivi des coûts pour un abonnement Azure.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b64e84c3fff27675029ff35f27972a4aca014ec3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612159"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Éviter les charges inattendues avec la gestion de la facturation et des coûts dans Azure

Lorsque vous vous inscrivez à Azure, il y a plusieurs choses que vous pouvez faire pour avoir une meilleure idée de vos dépenses :

- La [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) peut fournir une estimation des coûts avant de créer une ressource Azure. 

- Le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) fournit la répartition des coûts et la prévision pour votre abonnement. 

- Si vous souhaitez regrouper et comprendre les coûts associés à différents projets ou équipes,reportez-vous au [balisage des ressources](../azure-resource-manager/resource-group-using-tags.md). Si votre organisation dispose d’un système de création de rapports que vous préférez utiliser, regardez du côté des [API de facturation](billing-usage-rate-card-overview.md).

- Si votre abonnement a été créé à partir d’un Accord Entreprise (EA), vous pouvez consulter vos coûts dans le Portail Azure. Si votre abonnement s’effectue par l’intermédiaire d’un fournisseur de solutions cloud (CSP) ou Azure Sponsorship, certaines des fonctionnalités suivantes peuvent ne pas vous concerner. Pour plus d’informations, consultez [Ressources supplémentaires pour les offres EA, CSP et Sponsorship](#other-offers).

- Si votre abonnement est une offre d’évaluation gratuite, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure dans Open (AIO) ou BizSpark, votre abonnement est automatiquement désactivé lorsque tous vos crédits sont utilisés. En savoir plus sur les [limites de dépenses](#spending-limit) pour éviter que votre abonnement ne soit désactivé de façon inattendue.

- Si vous avez souscrit à un [compte Azure gratuit](https://azure.microsoft.com/free/), [vous pouvez utiliser des services Azure populaires gratuitement pendant 12 mois](billing-create-free-services-included-free-account.md). Outre les recommandations répertoriées ci-dessous, consultez [Éviter les frais pour votre compte gratuit](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obtenir les coûts estimés avant d’ajouter des services Azure

Voici quelques informations supplémentaires sur l’estimation des coûts à l’aide des outils suivants :
- Calculatrice de prix Azure
- Portail Azure
- Limite de dépense

Les images des sections suivantes montrent des exemples de tarification en dollars américains.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimez le coût en ligne à l’aide de la calculatrice de prix

Examinez la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour obtenir une estimation mensuelle du coût de service qui vous intéresse. Vous pouvez ajouter tout premier tiers de ressources Azure pour obtenir une estimation de coût. Dans la calculatrice de prix, vous pouvez modifier le type de devise.

![Capture d’écran du menu de la calculatrice de prix](./media/billing-getting-started/pricing-calc.png)

Par exemple, dans la calculatrice de prix, le coût d’une machine virtuelle A1 Windows en heures de calcul est estimé à un certain montant par mois si vous la laissez s’exécuter en permanence :

![Capture d’écran de la calculatrice de prix montrant le coût estimé d’une machine virtuelle A1 Windows par mois](./media/billing-getting-started/pricing-calcvm.png)

Pour plus d’informations sur les prix, consultez [FAQ sur la tarification](https://azure.microsoft.com/pricing/faq/). Si vous souhaitez communiquer avec un commercial Azure, appelez le numéro de téléphone affiché en haut de la page du FAQ.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Vérifiez l’estimation du coût dans le portail Azure

En général, lorsque vous ajoutez un service dans le portail Azure, une vue présentant une estimation similaire du coût par mois vous est proposée. Par exemple, lorsque vous choisissez la taille de votre machine virtuelle Windows, vous pouvez voir l’estimation du coût mensuel pour les heures de calcul :

![Exemple : machine virtuelle A1 Windows montrant l’estimation de coût par mois](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a> Vérifiez si la limite de dépense est activée

Si votre abonnement utilise des crédits, la limite de dépense est activée pour vous par défaut. De cette manière, lorsque vous dépensez tous vos crédits, votre carte bancaire n’est pas facturée. Pour plus d’informations, consultez la [liste complète des offres Azure et les informations sur la disponibilité de la limite de dépense](https://azure.microsoft.com/support/legal/offer-details/).

Toutefois, lorsque vous atteignez votre limite de dépense, vos services sont désactivés. Cela signifie que vos machines virtuelles sont libérées. Pour éviter toute interruption de service, vous devez désactiver la limite de dépense. Tout dépassement est facturé sur la carte de crédit enregistrée.

Pour voir si une limite de dépense est activée, accédez à la [vue Abonnements du Centre des comptes](https://account.windowsazure.com/Subscriptions). Une bannière similaire à la suivante s’affiche si votre limite de dépense est activée :

![Capture d’écran montrant l’avertissement affiché dans le Centre des comptes lorsque la limite de dépense est activée](./media/billing-getting-started/spending-limit-banner.png)

Cliquez sur la bannière et suivez les invites pour supprimer la limite de dépense. Si vous n’avez pas entré d’informations de carte bancaire lors de votre inscription, vous devez les saisir pour supprimer la limite de dépense. Pour plus d’informations, consultez [Limite de dépense Azure : fonctionnement et activation ou désactivation](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Utiliser des budgets et des alertes de coût

Vous pouvez créer des [budgets](../cost-management/tutorial-acm-create-budgets.md) pour gérer les coûts et des [alertes](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût.

## <a name="monitor-costs-when-using-azure-services"></a>Surveiller les coûts lors de l’utilisation des services Azure
Vous pouvez surveiller les coûts à l’aide des outils suivants :

- Balises
- Répartition des coûts et taux d’avancement
- Analyse des coûts

### <a name="tags"></a> Ajoutez des balises aux ressources pour regrouper les données de facturation

Vous pouvez utiliser des balises pour regrouper les données de facturation associées aux services pris en charge. Par exemple, si vous exécutez plusieurs machines virtuelles pour différentes équipes, vous pouvez utiliser des balises pour classer les coûts par centre de coût (par exemple : RH, marketing, service financier, etc.) ou par environnement (par exemple : production, préproduction, test).

![Capture d’écran illustrant la configuration de balises dans le portail](./media/billing-getting-started/tags.png)

Les balises apparaissent dans les différentes vues des rapports de coûts. Par exemple, elles sont visibles dans votre [vue d’analyse des coûts](#costs) immédiatement et dans le fichier CSV d’utilisation détaillée après votre première période de facturation.

Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Surveiller la répartition des coûts et le taux d’avancement

Une fois vos services Azure en cours d’exécution, vérifiez régulièrement les frais. Vous pouvez consulter les dépenses et le taux d’avancement actuels dans le portail Azure.

1. Visitez le [panneau Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) et sélectionnez un abonnement.

2. Si cette option est disponible dans votre abonnement, vous voyez la répartition des coûts et le taux d’avancement.

    ![Capture d’écran du taux d’avancement et de la répartition des coûts dans le portail Azure](./media/billing-getting-started/burn-rate.PNG)

3. Cliquez sur [Analyse des coûts](../cost-management/quick-acm-cost-analysis.md) dans la liste figurant sur la gauche pour afficher la répartition des coûts par ressource. Après avoir ajouté un service, veuillez patienter 24 heures pour que les données s’affichent.

    ![Capture d’écran de la vue de l’analyse des coûts dans le portail Azure](./media/billing-getting-started/cost-analysis.png)

4. Vous pouvez filtrer les données en fonction de différentes propriétés : [balises](#tags), type de ressource, groupe de ressources et intervalle de temps. Cliquez sur **Appliquer** pour confirmer les filtres et sur **Télécharger** pour exporter la vue vers un fichier de valeurs séparées par des virgules (.csv).

5. En outre, vous pouvez cliquer sur une ressource pour afficher l’historique de vos dépenses quotidiennes et le coût des ressources chaque jour.

    ![Capture d’écran de la vue de l’historique des dépenses dans le portail Azure](./media/billing-getting-started/costhistory.png)

Comparez les coûts affichés avec les estimations que vous avez vues lors de la sélection des services. Si vous constatez une différence considérable, vérifiez le plan de tarification que vous avez sélectionné pour vos ressources.

## <a name="optimize-and-reduce-costs"></a>Optimiser et réduire les coûts
Si vous n’êtes pas familiarisé avec les principes de la gestion des coûts, découvrez [Guide pratique pour optimiser votre investissement dans le cloud avec Azure Cost Management](../cost-management/cost-mgt-best-practices.md).

Dans le Portail Azure, vous pouvez également optimiser et réduire les coûts Azure avec l’arrêt automatique pour les machines virtuelles et les recommandations d’Advisor.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Envisagez d’utiliser les fonctionnalités de réduction des coûts, telles que l’arrêt automatique pour les machines virtuelles

Selon votre scénario, vous pouvez configurer l’arrêt automatique de vos machines virtuelles dans le portail Azure. Pour en savoir plus, consultez le billet de blog [Auto-shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Arrêt automatique des machines virtuelles à l’aide d’Azure Resource Manager).

![Capture d’écran de l’option d’arrêt automatique dans le portail](./media/billing-getting-started/auto-shutdown.png)

L’arrêt automatique n’a pas le même effet que l’arrêt à partir des options d’alimentation d’une machine virtuelle. L’arrêt automatique met un terme au fonctionnement de vos machines virtuelles et les libère pour éviter des frais d’utilisation supplémentaires. Pour plus d’informations, consultez les informations concernant les états des machines virtuelles dans le FAQ sur la facturation pour les [machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) et les [machines Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Pour bénéficier d’autres fonctionnalités de réduction des coûts pour vos environnements de développement et de test, consultez [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Activer et découvrir les recommandations d’Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) vous permet de réduire les coûts en identifiant les ressources peu utilisées. Visitez Advisor dans le portail Azure :

![Capture d’écran du bouton Azure Advisor dans le portail Azure](./media/billing-getting-started/advisor-button.png)

Vous pouvez accéder à des recommandations exploitables à partir de l’onglet **Coût** du tableau de bord d’Advisor :

![Exemple de recommandation en matière de coûts d’Advisor](./media/billing-getting-started/advisor-action.png)

Consultez le didacticiel [Optimiser les coûts à partir de recommandations](../cost-management/tutorial-acm-opt-recommendations.md) pour obtenir un didacticiel guidé sur les recommandations d’Advisor relatives à la réalisation d’économies.

## <a name="review-costs-against-your-latest-invoice"></a>Comparer les coûts à ceux de votre dernière facture

À la fin du cycle de facturation, votre facture la plus récente est disponible. Vous pouvez également [télécharger des factures et des fichiers d’utilisation détaillés](billing-download-azure-invoice-daily-usage-date.md) pour vous assurer que vous avez été facturé correctement. Pour plus d’informations sur la relation entre votre facture et votre utilisation quotidienne, consultez [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>API de facturation

Utilisez nos API de facturation Azure pour obtenir les données d’utilisation par programmation. En associant les API RateCard et Resource Usage, vous pouvez connaître l’utilisation qui vous est facturée. Pour plus d’informations, consultez [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Ressources supplémentaires te cas spéciaux

### <a name="ea-csp-and-sponsorship-customers"></a>Clients EA, CSP et Sponsoring
Contactez votre responsable de compte ou votre partenaire Azure pour commencer.

| Offre | Ressources |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contrat Entreprise (EA) | [Portail EA](https://ea.azure.com/), [documents d’aide](https://ea.azure.com/helpdocs), et [Rapport Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Fournisseur de solutions cloud (CSP) | Contactez votre fournisseur |
| Azure Sponsorship | [Portail Sponsorship](https://www.microsoftazuresponsorships.com/) |

Si vous êtes responsable informatique d’une grande organisation, nous vous recommandons de lire l’article [Structure d’entreprise Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) et le document [entreprise livre blanc IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (téléchargement au format .pdf, disponible en anglais uniquement).

#### <a name="EA"></a> Vues des coûts de l’Accord Entreprise dans le portail Azure

Les vues des coûts d’entreprise sont actuellement en préversion publique. Éléments à noter :

- Les coûts d’abonnement sont basés sur l’utilisation et n’incluent pas les montants prépayés, dépassements, quantités incluses, ajustements et taxes. Les frais réels sont calculés au niveau de l’inscription.
- Les montants qui apparaissent dans le portail Azure peuvent être différents de ceux qui s’affichent dans le portail d’entreprise. Les mises à jour dans le portail d’entreprise peuvent prendre quelques minutes avant que les modifications n’apparaissent dans le portail Azure.
- Si vous ne voyez pas les coûts, l’une des raisons suivantes peut expliquer cette situation :
    - Vous ne disposez pas des autorisations au niveau de l’abonnement. Pour afficher les vues de coût d’entreprise, vous devez être un lecteur de facturation, un lecteur, un contributeur ou un propriétaire au niveau de l’abonnement.
    - Vous êtes propriétaire de compte et votre administrateur en charge de l’inscription a désactivé le paramètre « d’affichage des frais pour l’administrateur de compte ».  Contactez l’administrateur en charge de votre inscription pour obtenir l’accès aux coûts.
    - Vous êtes administrateur de service et l’administrateur de votre inscription a désactivé le paramètre **Affichage des frais pour l’administrateur de service**.  Contactez l’administrateur en charge de votre inscription pour obtenir l’accès.
    - Vous avez acheté Azure par le biais d’un partenaire et ce dernier n’a pas publié d’informations tarifaires.  
- Si vous mettez à jour des paramètres liés à l’accès aux coûts dans le portail d’entreprise, il existe un délai d’attente de quelques minutes avant que les modifications n’apparaissent dans le portail Azure.
- La limite de dépense et les conseils de facturation ne s’appliquent pas aux abonnements EA.

### <a name="check-your-subscription-and-access"></a>Vérifiez votre abonnement et votre accès

Pour voir les coûts, vous devez disposer d’un [accès de niveau abonnement aux informations de facturation](billing-manage-access.md). Seul l’administrateur de compte peut accéder au [Centre des comptes](https://account.azure.com/Subscriptions), modifier les informations de facturation et gérer les abonnements. L’administrateur de compte est la personne qui a effectué le processus d’inscription. Pour plus d’informations, consultez [Ajout ou modification de rôles d’administrateur Azure gérant l’abonnement ou les services](billing-add-change-azure-subscription-administrator.md).

Pour vérifier si vous êtes l’administrateur de compte, accédez au [panneau Abonnements dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Affichez la liste des abonnements et recherchez **Mon rôle**. Si le rôle est *Administrateur de compte*, vous disposez de privilèges complets. S’il est indiqué autre chose, par exemple *Propriétaire*, vous ne disposez pas de privilèges complets.

![Capture d’écran de votre rôle dans la vue Abonnements du portail Azure](./media/billing-getting-started/sub-blade-view.PNG)

Pour gérer les abonnements et modifier les informations de facturation, [identifiez l’administrateur de compte](billing-subscription-transfer.md#whoisaa). Demandez à l’administrateur de compte d’effectuer ces tâches ou de [vous transférer l’abonnement](billing-subscription-transfer.md).

Si votre administrateur de compte ne fait plus partie de votre organisation et que vous souhaitez gérer la facturation, [contactez-nous](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Demander un crédit Contrat de niveau de service pour un incident de service

Les contrats SLA décrivent les engagements de Microsoft en matière de temps d’activité et de connectivité. Un incident de service est signalé lorsque les services Azure rencontrent un problème qui a un impact sur la connectivité, ce qui est souvent appelé une *panne*. Si nous n’atteignons pas et ne maintenons pas les niveaux de service pour chaque service, comme décrit dans le contrat SLA, vous pouvez bénéficier d’un crédit pour une partie de vos frais de service mensuels.

Pour demander un crédit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/). Si vous avez plusieurs comptes, assurez-vous d’utiliser celui qui a été affecté par le temps d’arrêt d’Azure. 
2. Créez une nouvelle demande de support.
3. Sous **Type de problème**, sélectionnez **Facturation**.
4. Sous **Type de problème**, sélectionnez **Demande de remboursement**.
5. Ajoutez des détails pour spécifier que vous demandez un crédit SLA, mentionnez la date/l’heure/le fuseau horaire, ainsi que les services concernés (machines virtuelles, sites web, etc.)
6. Vérifiez vos coordonnées et sélectionnez **Créer** pour envoyer votre demande.

Les seuils de contrat SLA varient en fonction du service. Par exemple, le niveau SQL web a un contrat SLA de 99,9 %, les machines virtuelles ont un contrat SLA de 99,95 % et le niveau SQL standard a un contrat SLA de 99,99 %.

Pour certains services, il existe des conditions préalables à l’application du contrat SLA. Par exemple, les machines virtuelles doivent avoir au moins deux instances déployées dans le même groupe à haute disponibilité.

Pour plus d’informations, consultez [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/) et la documentation [Résumé des contrats SLA pour les services Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur l'utilisation des [limites de dépense](billing-spending-limit.md) pour éviter les dépassements.
- Commencez à [analyser vos coûts Azure](../cost-management/quick-acm-cost-analysis.md).
