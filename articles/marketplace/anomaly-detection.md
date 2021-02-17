---
title: Détection d’anomalie pour la facturation à l’usage | Place de marché Azure
description: Découvrez comment la détection d’anomalie automatique pour la facturation à l’usage vous aide à vérifier que vos clients sont facturés correctement pour l’utilisation facturée à l’usage de votre offre commerciale de la place de marché.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 1/09/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: d4fb88854359dcd6e383b47d2a8ce4e9c91f867a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989192"
---
# <a name="anomaly-detection-for-metered-billing"></a>Détection d’anomalie pour la facturation à l’usage

Cet article fournit des détails sur le service de mesure de la place de marché et la fonctionnalité de détection d’anomalie automatique associée afin de garantir que nous facturerons correctement les clients selon leur usage. L’option de facturation à l’usage est actuellement disponible pour les offres[SaaS (Software as a service)](plan-saas-offer.md) et les [applications Azure](plan-azure-application-offer.md#types-of-plans) avec un plan d’application managée. Cette option vous permet de créer des offres dans le cadre du programme commercial de la place de marché qui sont facturées en fonction d’unités non standard.

Les partenaires qui ont des compteurs personnalisés déployés pour des applications SaaS et managées peuvent observer l’écart entre le comportement d’utilisation attendu et les anomalies pour les _événements de dépassement_ sur des _compteurs personnalisés_ spécifiques dans l’Espace partenaires. Pour atténuer le risque, notre Espace partenaires utilise un service de détection d’anomalie qui applique des algorithmes de Machine Learning pour déterminer le comportement normal de la facturation à l’usage, analyser l’utilisation de la facturation à l’usage et détecter les anomalies avec une intervention minimale de l’utilisateur. À l’aide de _modèles de détection d’anomalie_ sur les jeux de données facturés à l’usage, l’Espace partenaires a pour but d’informer le serveur de publication lorsque l’utilisation signalée dépasse l’utilisation prévue.

## <a name="usability-experience"></a>Expérience de convivialité

Microsoft fait appel au partenaire pour signaler le dépassement d’utilisation par le client de ses offres SaaS ou d’applications managées par Azure avant que Microsoft ne facture le client. En cas de signalement d’utilisation incorrecte, le client peut recevoir une facture incorrecte, mettant à mal la crédibilité de Microsoft et du partenaire.

Pour limiter ce risque, une fonctionnalité de détection d’anomalie automatisée est fournie pour les applications SaaS et les plans d’application managée Azure. Cette fonctionnalité est un modèle Machine Learning qui surveille de manière proactive l’utilisation selon la facturation à l’usage et prédit la valeur attendue de l’utilisation dans la plage prévue. Si l’usage dépasse la plage attendue, cela est considéré comme une anomalie et une notification d’alerte est envoyée au partenaire sur la page Vue d’ensemble de l’offre dans le programme commercial de la place de marché de l’Espace partenaires.

Le modèle Machine Learning analyse le dépassement quotidien de l’utilisation. Le serveur de publication peut voir toutes les anomalies signalées par rapport au dépassement d’usage des clients pour chaque dimension de métrique personnalisée de l’offre.

### <a name="view-and-manage-metered-usage-anomalies"></a>Afficher et gérer les anomalies liées à l’utilisation facturée à l’usage

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
1. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Analyser**.
1. Sélectionnez l’onglet **Anomalies liées à l’utilisation facturée à l’usage**.

    [![Illustre l’onglet Anomalies liées à l’utilisation facturée à l’usage sur la page Utilisation.](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***Figure 1 : Onglet Anomalies liées à l’utilisation facturée à l’usage***

1. Pour toute anomalie d’utilisation détectée par rapport à la facturation à l’usage, en tant qu’éditeur, vous êtes invité à examiner et à confirmer s’il s’agit ou non d’une anomalie. Sélectionnez **Marquer comme anomalie** pour confirmer le diagnostic.

     [![Illustre la boîte de dialogue Marquer comme anomalie.](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***Figure 2 : Boîte de dialogue Marquer comme anomalie***

1. Si vous pensez que l’anomalie liée au dépassement de l’utilisation que nous avons détectée n’est pas avérée, vous pouvez le faire savoir en sélectionnant **Pas d’anomalie** au niveau de l’anomalie signalée par l’Espace partenaires pour le dépassement d’utilisation spécifique.

    [![Illustre la boîte de dialogue Pourquoi ne s’agit-il pas d’une anomalie ?](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***Figure 3 : Boîte de dialogue Pourquoi ne s’agit-il pas d’une anomalie ?***

1. Vous pouvez faire défiler la page pour afficher une liste des anomalies non confirmées. La liste dresse un inventaire des anomalies que vous n’avez pas confirmées. Vous pouvez choisir de marquer les anomalies signalées par l’Espace partenaires comme étant avérées ou non.

   [![Illustre la liste des anomalies signalées par l’Espace partenaires encore non confirmées sur la page Utilisation.](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***Figure 4 : Liste des anomalies signalées par l’Espace partenaires encore non confirmées***

1. Vous pouvez également consulter un journal d’actions liées aux anomalies qui reprend les actions que vous avez effectuées concernant les dépassements d’utilisation. Dans le journal des actions, vous pourrez voir quels événements de dépassement d’utilisation ont été marqués comme étant avérés ou non.

   [![Illustre le journal des actions liées aux anomalies sur la page Utilisation.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox)
   ***Figure 5 : Journal d’actions liées aux anomalies***

1. L’analyse de l’Espace partenaires ne prend pas en charge la reformulation des événements de dépassement d’utilisation dans les rapports d’exportation. L’Espace partenaires vous permet d’entrer la correction du dépassement d’utilisation pour une anomalie et les détails sont transmis aux équipes Microsoft pour investigation. En fonction de l’investigation, Microsoft émet des remboursements de crédit au client surfacturé, le cas échéant. Lorsque vous sélectionnez l’une des anomalies marquées, vous pouvez sélectionner **Marquer comme anomalie** pour marquer l’anomalie de dépassement d’utilisation comme avérée.

   [![Illustre la boîte de dialogue Marquer comme anomalie.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox)
   ***Figure : 6 : Boîte de dialogue Marquer comme anomalie***

La première fois qu’un dépassement d’utilisation est marqué comme anormal dans l’Espace partenaires, vous avez un délai de 30 jours à partir de cette instance pour marquer l’anomalie comme avérée ou fausse. Après la période de 30 jours, en tant qu’éditeur, vous ne pouvez plus réagir aux anomalies.

> [!IMPORTANT]
> Les unités de dépassement d’utilisation non signalées ne sont pas éligibles à la reformulation ni à l’ajustement financier.

Vous pouvez voir toutes les anomalies (reconnues ou non) pour la période de calcul sélectionnée. Les différentes périodes de calcul sont les 30 derniers jours, les 60 derniers jours et les 90 derniers jours.

> [!IMPORTANT]
> Vous êtes invité à réagir aux anomalies signalées dans les 30 jours qui suivent le premier signalement des anomalies dans l’Espace partenaires.

Le mode de filtre dans le widget vous permet de sélectionner des offres individuelles et des compteurs personnalisés individuels.

Après avoir marqué un dépassement d’utilisation comme une anomalie ou reconnu un modèle qui signalait une anomalie comme étant avérée, vous ne pouvez pas modifier la sélection en « Pas d’anomalie ».

> [!IMPORTANT]
> Vous pouvez soumettre à nouveau des dépassements d’utilisation en cas de surfacturation.

## <a name="see-also"></a>Voir également
- [Facturation à l’usage pour SaaS à l’aide du service de mesure de la consommation de la Place de marché commerciale](./partner-center-portal/saas-metered-billing.md)
- [Facturation des applications managées basée sur des mesures](./partner-center-portal/azure-app-metered-billing.md)
