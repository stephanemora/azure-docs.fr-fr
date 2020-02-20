---
title: Éviter les coûts imprévus et gérer la facturation dans Azure
description: Découvrez comment éviter des frais inattendus sur votre facture Azure. Utilisez les fonctionnalités de gestion et de suivi des coûts pour un compte Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: e22663cf8cabd5d2abf487c96f2d69aeaaa90ab7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199702"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Éviter les charges inattendues avec la gestion de la facturation et des coûts dans Azure

Lorsque vous vous inscrivez à Azure, il y a plusieurs choses que vous pouvez faire pour avoir une meilleure idée de vos dépenses :

- Avant d’ajouter des services, vous pouvez obtenir des estimations de coûts à l’aide de la [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/), de la grille tarifaire Azure ou lors de l’ajout de services dans le portail Azure.
- Supervisez les coûts à l’aide de [budgets](../costs/tutorial-acm-create-budgets.md), d’[alertes](../costs/cost-mgt-alerts-monitor-usage-spending.md) et d’[analyses des coûts](../costs/quick-acm-cost-analysis.md).
- Passez en revue votre facture en la comparant aux [fichiers d’utilisation détaillée](download-azure-invoice-daily-usage-date.md).
- Intégrez les données relatives à la facturation et aux coûts à votre propre système de rapports à l’aide des API de [facturation](https://docs.microsoft.com/rest/api/billing/) et de [consommation](https://docs.microsoft.com/rest/api/consumption/).
- Utilisez des ressources et des outils supplémentaires pour les clients Contrat Entreprise (EA), Fournisseur de solutions cloud (CSP) et Azure Sponsorship.
- Avec un [compte gratuit Azure](https://azure.microsoft.com/free/), vous pouvez utiliser [gratuitement certains services Azure populaires pendant 12 mois](create-free-services.md). Outre les recommandations répertoriées ci-dessous, consultez [Éviter les frais pour votre compte gratuit](avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obtenir les coûts estimés avant d’ajouter des services Azure

Utilisez l’un des outils suivants pour estimer le coût de l’utilisation d’un service Azure :
- Calculatrice de prix Azure
- Grille tarifaire Azure
- Portail Azure

Les images des sections suivantes montrent des exemples de tarification en dollars américains.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimez le coût en ligne à l’aide de la calculatrice de prix

Utilisez la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) afin d’obtenir une estimation mensuelle de coût pour le service que vous souhaitez ajouter. Vous pouvez modifier la devise pour obtenir une estimation dans votre devise locale.

![Capture d’écran du menu de la calculatrice de prix](./media/getting-started/pricing-calc.png)

Vous pouvez afficher le coût estimé pour tout service Azure de premier tiers. Par exemple, dans la capture d’écran ci-dessous, le coût d’une machine virtuelle A1 Windows en heures de calcul est estimé à 66,96 USD par mois si vous la laissez s’exécuter en permanence :

![Capture d’écran de la calculatrice de prix montrant le coût estimé d’une machine virtuelle A1 Windows par mois](./media/getting-started/pricing-calcvm.png)

Pour plus d’informations sur les prix, consultez [FAQ sur la tarification](https://azure.microsoft.com/pricing/faq/). Si vous souhaitez communiquer avec un commercial Azure, appelez le numéro de téléphone affiché en haut de la page du FAQ.

### <a name="view-and-download-azure-price-sheet"></a>Afficher et télécharger la grille tarifaire Azure

Si vous avez accès à Azure par le biais d’un Contrat Entreprise (EA) ou d’un Contrat client Microsoft (MCA), vous pouvez afficher et télécharger la grille tarifaire pour votre compte Azure. La grille tarifaire est un fichier Excel qui contient les prix de tous les services Azure. Pour plus d’informations, consultez [Afficher et télécharger les tarifs Azure](ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Vérifiez l’estimation du coût dans le portail Azure

Vous pouvez afficher le coût estimé par mois lors de l’ajout d’un service dans le portail Azure. Par exemple, lorsque vous choisissez la taille de votre machine virtuelle Windows, vous pouvez voir l’estimation du coût mensuel pour les heures de calcul :

![Exemple : machine virtuelle A1 Windows montrant l’estimation de coût par mois](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Surveiller les coûts lors de l’utilisation des services Azure
Vous pouvez surveiller les coûts à l’aide des outils suivants :

- Budgets et alertes de coût
- Analyse des coûts

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Effectuer le suivi des coûts avec des budgets et des alertes de coût

Créez des [budgets](../costs/tutorial-acm-create-budgets.md) pour gérer les coûts et des [alertes](../costs/cost-mgt-alerts-monitor-usage-spending.md) permettant d’avertir automatiquement les parties prenantes ou vous-même en cas d’anomalies de dépenses et de risques de dépenses excessives.

### <a name="costs"></a> Explorer et analyser les coûts avec l’analyse de coûts

Une fois vos services Azure en cours d’exécution, vérifiez régulièrement les coûts afin de suivre vos dépenses Azure. Vous pouvez utiliser l’analyse des coûts pour comprendre les coûts liés à votre utilisation d’Azure.

1. Accédez à la [page Gestion des coûts + facturation dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Cliquez sur **Analyse du coût** sur le côté gauche de l’écran pour afficher les coûts actuels, décomposés par service, emplacement et abonnement. Après un achat ou l’ajout d’un service, les données correspondantes s’affichent au bout de 24 heures. Par défaut, l’analyse des coûts affiche le coût correspondant à l’étendue dans laquelle vous vous trouvez. Par exemple, dans la capture d’écran ci-dessous, le coût du compte de facturation Contoso s’affiche. Utilisez le paramètre Étendue pour passer à une autre étendue dans l’analyse des coûts. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](../costs/understand-work-scopes.md#scopes).

    ![Capture d’écran de la vue de l’analyse des coûts dans le portail Azure](./media/getting-started/cost-analysis.png)

4. Vous pouvez filtrer en fonction de différentes propriétés, telles que les étiquettes, le type de ressource et l’intervalle de temps. Cliquez sur **Ajouter un filtre** pour ajouter le filtre d’une propriété, puis sélectionnez les valeurs à filtrer. Sélectionnez **Exporter** pour exporter la vue vers un fichier de valeurs séparées par des virgules (.csv).

5. En outre, vous pouvez cliquer sur l’une des étiquettes du graphique pour afficher l’historique des dépenses quotidiennes correspondant. Par exemple, dans la capture d’écran ci-dessous, le fait de cliquer sur les machines virtuelles affiche le coût quotidien lié à l’exécution de celles-ci.

    ![Capture d’écran de la vue de l’historique des dépenses dans le portail Azure](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Optimiser et réduire les coûts
Si vous n’êtes pas familiarisé avec les principes de la gestion des coûts, découvrez [Guide pratique pour optimiser votre investissement dans le cloud avec Azure Cost Management](../costs/cost-mgt-best-practices.md).

Dans le Portail Azure, vous pouvez également optimiser et réduire les coûts Azure avec l’arrêt automatique pour les machines virtuelles et les recommandations d’Advisor.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Envisagez d’utiliser les fonctionnalités de réduction des coûts, telles que l’arrêt automatique pour les machines virtuelles

Selon votre scénario, vous pouvez configurer l’arrêt automatique de vos machines virtuelles dans le portail Azure. Pour en savoir plus, consultez le billet de blog [Auto-shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Arrêt automatique des machines virtuelles à l’aide d’Azure Resource Manager).

![Capture d’écran de l’option d’arrêt automatique dans le portail](./media/getting-started/auto-shutdown.png)

L’arrêt automatique n’a pas le même effet que l’arrêt à partir des options d’alimentation d’une machine virtuelle. L’arrêt automatique met un terme au fonctionnement de vos machines virtuelles et les libère pour éviter des frais d’utilisation supplémentaires. Pour plus d’informations, consultez les informations concernant les états des machines virtuelles dans le FAQ sur la facturation pour les [machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) et les [machines Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Pour bénéficier d’autres fonctionnalités de réduction des coûts pour vos environnements de développement et de test, consultez [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Activer et découvrir les recommandations d’Azure Advisor

[Azure Advisor](../../advisor/advisor-overview.md) vous permet de réduire les coûts en identifiant les ressources peu utilisées. Recherchez **Advisor** dans le portail Azure :

![Capture d’écran du bouton Azure Advisor dans le portail Azure](./media/getting-started/advisor-button.png)

Sélectionnez **Coût** dans la partie gauche. Des recommandations actionnables se trouvent sous l’onglet **Coût** :

![Exemple de recommandation en matière de coûts d’Advisor](./media/getting-started/advisor-action.png)

Consultez le didacticiel [Optimiser les coûts à partir de recommandations](../costs/tutorial-acm-opt-recommendations.md) pour obtenir un didacticiel guidé sur les recommandations d’Advisor relatives à la réalisation d’économies.

## <a name="review-charges-against-your-latest-invoice"></a>Comparer la facture actuelle à votre dernière facture

Votre facture est disponible à la fin du cycle de facturation. Vous pouvez [télécharger les factures et les fichiers d’utilisation détaillée](download-azure-invoice-daily-usage-date.md) pour vérifier que les frais qui vous sont facturés sont corrects. Pour plus d’informations sur la relation entre votre facture et votre utilisation quotidienne, consultez [Comprendre votre facture Microsoft Azure](../understand/review-individual-bill.md).

Si vous utilisez Azure via un Contrat client Microsoft (MCA), vous pouvez également [comparer votre facture aux transactions ](../understand/review-customer-agreement-bill.md#review-invoiced-transactions-in-the-azure-portal) afin de comprendre les frais qui figurent sur votre facture.

## <a name="integrate-with-billing-and-consumption-apis"></a>Intégrer les API de facturation et de consommation

Utilisez les API Azure de [facturation](https://docs.microsoft.com/rest/api/billing/) et de [consommation](https://docs.microsoft.com/rest/api/consumption/) pour accéder par programmation aux données de facturation et de coût. En associant les API RateCard et Resource Usage, vous pouvez connaître l’utilisation qui vous est facturée. Pour plus d’informations, consultez [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](usage-rate-card-overview.md).

## <a name="other-offers"></a> Ressources supplémentaires et cas spéciaux

### <a name="ea-csp-and-sponsorship-customers"></a>Clients EA, CSP et Sponsoring
Contactez votre responsable de compte ou votre partenaire Azure pour commencer.

| Offre | Ressources |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contrat Entreprise (EA) | [Portail EA](https://ea.azure.com/), [documents d’aide](https://ea.azure.com/helpdocs), et [Rapport Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Fournisseur de solutions cloud (CSP) | Contactez votre fournisseur |
| Azure Sponsorship | [Portail Sponsorship](https://www.microsoftazuresponsorships.com/) |

Si vous êtes responsable informatique d’une grande organisation, nous vous recommandons de lire l’article [Structure d’entreprise Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) et le document [entreprise livre blanc IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (téléchargement au format .pdf, disponible en anglais uniquement).

### <a name="EA"></a> Vues des coûts de l’Accord Entreprise dans le portail Azure

Les vues des coûts d’entreprise sont actuellement en préversion publique. Éléments à noter :

- Les coûts d’abonnement sont basés sur l’utilisation et n’incluent pas les montants prépayés, dépassements, quantités incluses, ajustements et taxes. Les frais réels sont calculés au niveau de l’inscription.
- Les montants qui apparaissent dans le portail Azure peuvent être différents de ceux qui s’affichent dans le portail d’entreprise. Les mises à jour dans le portail d’entreprise peuvent prendre quelques minutes avant que les modifications n’apparaissent dans le portail Azure.
- Si vous ne voyez pas les coûts, l’une des raisons suivantes peut expliquer cette situation :
    - Vous ne disposez pas des autorisations au niveau de l’abonnement. Pour afficher les vues de coût d’entreprise, vous devez être un lecteur de facturation, un lecteur, un contributeur ou un propriétaire au niveau de l’abonnement.
    - Vous êtes propriétaire de compte et votre administrateur en charge de l’inscription a désactivé le paramètre « d’affichage des frais pour l’administrateur de compte ».  Contactez l’administrateur en charge de votre inscription pour obtenir l’accès aux coûts.
    - Vous êtes administrateur de service et l’administrateur de votre inscription a désactivé le paramètre **Affichage des frais pour l’administrateur de service**.  Contactez l’administrateur en charge de votre inscription pour obtenir l’accès.
    - Vous avez acheté Azure par le biais d’un partenaire et ce dernier n’a pas publié d’informations tarifaires.  
- Si vous mettez à jour des paramètres liés à l’accès aux coûts dans le portail d’entreprise, vous devrez attendre quelques minutes avant que les modifications ne s’affichent dans le portail Azure.
- La limite de dépense et les conseils de facturation ne s’appliquent pas aux abonnements EA.

### <a name="check-your-subscription-and-access"></a>Vérifiez votre abonnement et votre accès

Pour afficher les coûts, vous avez besoin d’accéder aux informations de facturation et de coûts au niveau du compte ou de l’abonnement. L’accès varie selon le type de compte de facturation. Pour plus d’informations sur les comptes de facturation et savoir comment identifier le type de votre compte de facturation, consultez [Afficher les comptes de facturation dans le portail Azure](view-all-accounts.md).

Si vous avez accès à Azure par le biais d’un compte de facturation Microsoft Online Service Program (MOSP), consultez [Gérer l’accès aux informations de facturation pour Azure](manage-billing-access.md).

Si vous avez accès à Azure par le biais d’un compte de facturation Contrat Entreprise (EA), consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](understand-ea-roles.md).

Si vous avez accès à Azure par le biais d’un compte de facturation Contrat client Microsoft (MCA), consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](understand-mca-roles.md).

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
- En savoir plus sur l'utilisation des [limites de dépense](spending-limit.md) pour éviter les dépassements.
- Commencez à [analyser vos coûts Azure](../costs/quick-acm-cost-analysis.md).
