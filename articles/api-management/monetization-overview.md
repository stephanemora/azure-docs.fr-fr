---
title: Monétisation avec Gestion des API Azure
description: Découvrez comment configurer votre stratégie de monétisation pour la Gestion des API Azure en six étapes simples.
author: v-hhunter
ms.author: v-hhunter
ms.date: 08/23/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 3e969ad51232007a04f3391090e122bf865aec20
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122825298"
---
# <a name="monetization-with-azure-api-management"></a>Monétisation avec Gestion des API Azure

Les API Web modernes étayent l’économie numérique. Ils fournissent à des tiers la propriété intellectuelle (IP) d’une entreprise et génèrent des revenus via :

- L’empaquetage de l’adresse IP sous forme de données, d’algorithmes ou de processus.
- L’autorisation à d’autres parties de découvrir et d’utiliser une adresse IP utile de manière cohérente et sans heurts.
- L’offre d’un mécanisme de paiement direct ou indirect pour cette utilisation.

Un thème commun aux récits de réussite d’API est un *modèle commercial sain*. La valeur est créée et échangée entre toutes les parties, de manière durable.

Les start-ups, les organisations établies et tout ce qui se trouve entre les deux cherchent généralement à transformer numériquement à partir du modèle d’entreprise. Les API permettent de réaliser le modèle commercial, ce qui permet de commercialiser, d’adopter, de consommer et de mettre à l’échelle l’adresse IP sous-jacente de manière plus simple et plus économique.

Les organisations qui publient leur première API font face à un ensemble complexe de décisions. Alors que la plateforme de Gestion des API Azure annule l’élévation des risques et accélère les éléments clés, les organisations doivent toujours configurer et créer leur API autour de leurs propres modèles techniques et commerciaux.

## <a name="developing-a-monetization-strategy"></a>Développement d’une stratégie de monétisation

La *Monétisation* est le processus de conversion d’un texte en monnaie, dans ce cas, la valeur de l’API. Les interactions d’API impliquent généralement trois parties distinctes dans la chaîne de valeur :

:::image type="content" source="media/monetization-overview/values-overview.png" alt-text="Diagramme de la chaîne de valeur de monétisation":::

Les catégories de stratégie de monétisation des API sont les suivantes :

| Stratégie de monétisation des API | Description |
| ----- | ----- |
| **Gratuit** | Une API facilite l’intégration interentreprises, telle que la rationalisation d’une chaîne d’approvisionnement. L’API n’est pas monétisée, mais offre une valeur significative en permettant aux processus commercial d’améliorer l’efficacité pour le fournisseur d’API et le consommateur d’API. |
| **Le consommateur paie** | Les consommateurs d’API paient en fonction du nombre d’interactions qu’ils ont avec l’API. Nous nous concentrons sur cette approche dans ce document. |
| **Le consommateur est payé** | Par exemple, un consommateur d’API utilise l’API pour incorporer la publicité dans son site Web et reçoit un partage des revenus générés. |
| **Monétisation indirecte** | La monétisation des API n’est pas pilotée par le nombre d’interactions avec l’API, mais par le biais d’autres sources de revenus facilitées par l’API. |

>[!NOTE]
>La stratégie de monétisation est définie par le fournisseur de l’API et doit être conçue pour répondre aux besoins du consommateur de l’API.

Dans la mesure où un large éventail de facteurs influencent la conception, la monétisation des API ne constitue pas une solution unique et adaptée. La stratégie de monétisation distingue votre API de vos concurrents et optimise vos revenus générés.

Les étapes suivantes expliquent comment implémenter une stratégie de monétisation pour votre API.

:::image type="content" source="media/monetization-overview/implementing-strategy.png" alt-text="Diagramme des étapes d’implémentation de votre stratégie de monétisation":::

### <a name="step-1---understand-your-customer"></a>Étape 1 : comprendre votre client

1. Mappez les étapes dans le parcours probable de vos consommateurs d’API, de la première découverte de votre API à la mise à l’échelle maximale.

    Par exemple, un ensemble d’étapes client peut être :

    | Étape client | Description |
    | ----- | ----- |
    | **Examen** | Autorisez le Consommateur de l’API à essayer votre API avec un coût et une friction nuls. |
    | **Implémentation** | Fournissez un accès suffisant à l’API pour prendre en charge le travail de développement et de test requis pour l’intégration. |
    | **Préversion** | Autorisez le client à lancer son offre et à comprendre la demande initiale. |
    | **Utilisation initiale de la production** | Prise en charge l’adoption précoce de l’API en production lorsque les niveaux d’utilisation ne sont pas entièrement compris et qu’une approche défavorable au risque peut être nécessaire. |
    | **Croissance initiale** | Activez l’API consommateur pour augmenter l’utilisation de l’API en réponse à l’augmentation de la demande des utilisateurs finaux. |
    | **Mise à l’échelle** | Encouragez le Consommateur de l’API à valider un plus grand volume d’achats une fois que l’API atteint régulièrement des niveaux d’utilisation élevés chaque mois. |
    | **Croissance mondiale** | Récompensez les utilisateurs de l’API qui utilisent l’API à l’échelle mondiale en offrant le prix de vente en gros optimal. |

1. Analysez la valeur que votre API va générer pour le client à chaque étape de son parcours. 
1. Envisagez d’appliquer une stratégie de tarification basée sur la valeur si la valeur directe de l’API au client est bien comprise.
1. Calculez les niveaux d’utilisation de la durée de vie anticipée de l’API pour un client et le nombre de clients attendus au cours de la durée de vie de l’API.

### <a name="step-2---quantify-the-costs"></a>Étape 2: quantifier les coûts

Calculez le coût total de possession de votre API.

| Coût | Description |
| ----- | ----- |
| **Coût de l’acquisition client (COCA)** | Le coût de la commercialisation, des ventes et de l’intégration. Les API les plus efficaces ont tendance à avoir un COCA à zéro à mesure que les niveaux d’adoption augmentent. Les API doivent être en grande partie en libre-service dans l’intégration. Les facteurs incluent la documentation et l’intégration sans friction avec les systèmes de paiement. |
| **Coûts d’ingénierie** | Les ressources humaines nécessaires à la création, au test, à l’exploitation et à la maintenance de l’API pendant sa durée de vie. Tendance à être le composant le plus important du coût. Dans la mesure du possible, exploitez les technologies PaaS et sans serveur Cloud à réduire. |
| **coûts de l’infrastructure** | Coûts pour les plates-formes, le calcul, le réseau et le stockage sous-jacents nécessaires à la prise en charge de l’API pendant sa durée de vie. Exploitez les plateformes Cloud pour obtenir un modèle de coût d’infrastructure qui évolue proportionnellement en fonction des niveaux d’utilisation des API. |

### <a name="step-3---conduct-market-research"></a>Étape 3 : effectuer des recherches sur le marché

1. Rechercher le marché pour identifier les concurrents. 
1. Analyser les stratégies de monétisation des concurrents. 
1. Comprendre les fonctionnalités spécifiques (fonctionnelles et non fonctionnelles) qu’elles proposent avec leur API.

### <a name="step-4---design-the-revenue-model"></a>Étape 4 : concevoir le modèle de revenu

Concevoir un modèle de revenu basé sur le résultat des étapes ci-dessus. Vous pouvez travailler sur deux dimensions :

| Dimension | Description |
| --------- | ----------- |
| **Qualité de service** | Placez des contraintes sur le niveau de service que vous offrez en définissant une limite d’utilisation des API. Définissez un quota pour les appels d’API qui peuvent être effectués sur une période donnée (par exemple, 50 000 appels par mois), puis bloquez les appels une fois ce quota atteint. <br> Vous pouvez également définir une limite de débit, en limitant le nombre d’appels qui peuvent être effectués sur une brève période (par exemple, 100 appels par seconde). <br> Les limites de seuils et de taux de transfert sont appliquées conjointement, ce qui empêche les utilisateurs de consommer leur quota mensuel d’appels d’API d’un seul coup et en peu de temps. |
| **Price** | Définissez le prix unitaire à payer pour chaque appel d’API. |

Optimisez la valeur de la durée de vie (LTV) que vous générez à partir de chaque client en concevant un modèle de chiffre d’affaires qui prend en charge votre client à chaque étape du parcours client.

1. Rendez la mise à l’échelle et la croissance les plus faciles possible pour vos clients :
    - Suggérez aux clients de passer au niveau suivant dans le modèle de revenu. 
    - Par exemple, récompensez les clients qui achètent un plus grand nombre d’appels d’API avec un prix unitaire inférieur.
1. Gardez le modèle de revenu aussi simple que possible.
    - Équilibrez le besoin d’offrir le choix avec le risque d’inonder les clients avec un ensemble d’options. 
    - Faites défiler le nombre de dimensions utilisées pour distinguer les différents niveaux de modèle de revenu.
1. Soyez transparent
    - Fournissez une documentation claire sur les différentes options.
    - Donnez à vos clients les outils nécessaires pour choisir le modèle de chiffre d’affaires qui répond le mieux à leurs besoins.

Identifiez la plage des modèles de tarification requis. Un *modèle de tarification* décrit un ensemble spécifique de règles pour que le fournisseur d’API convertisse la consommation par le consommateur de l’API en chiffre d’affaires.

Par exemple, pour la prise en charge des [étapes client ci-dessus](#step-1---understand-your-customer),  six types d’abonnement seraient nécessaires :

| Type d’abonnement | Description |
| ----- | ----- |
| `Free` | Permet au consommateur de l’API d’effectuer une évaluation de l’API dans une obligation et une solution gratuite, afin de déterminer si elle respecte un cas d’usage. Supprime toutes les barrières de l’entrée. |
| `Freemium` | Permet au consommateur de l’API d’utiliser l’API gratuitement, mais de passer à un service payant à mesure que la demande augmente. |
| `Metered` | Le consommateur de l’API peut effectuer autant d’appels qu’il le souhaite par mois et payer un montant fixe par appel. |
| `Tier` | Le consommateur de l’API paie pour un nombre défini d’appels par mois. S’il dépasse cette limite, il paie un montant de dépassement par appel supplémentaire. S’il encoure régulièrement un dépassement, il peut effectuer une mise à niveau vers le niveau suivant. |
| `Tier + Overage` | Le consommateur de l’API paie pour un nombre défini d’appels par mois. S’il dépasse cette limite, il paie un montant fixe par appel supplémentaire. |
| `Unit` | Le consommateur de l’API paie pour un nombre défini d’appels par mois. S’il dépasse cette limite, il doit payer pour une autre unité d’appels. |

Votre modèle de revenus définira l’ensemble des produits d’API. Chaque produit API implémente un modèle de tarification spécifique pour cibler une étape spécifique dans le cycle de vie des consommateurs de l’API.

Bien que les modèles de tarification ne soient généralement pas modifiés, vous devrez peut-être adapter la configuration et l’application des modèles de tarification pour votre modèle de revenus. Par exemple, vous souhaiterez peut-être ajuster vos prix pour qu’ils correspondent à un concurrent.

En s’appuyant sur les exemples ci-dessus, les modèles de tarification peuvent être appliqués pour créer un modèle de chiffre d’affaires global comme suit :

| Cycle de vie client : | Modèle de tarification | Configuration du modèle de tarification | Qualité de service |
| ------------------------ | ------------------------- | ------------------ | ----------------------------------------------------------------------------------------------------------- |
| Examen | Gratuit | Non implémenté. | Quota défini pour limiter le Consommateur à 100 appels/mois. |
| Implémentation | Freemium | Niveaux gradués : <ul> <li>Le montant fixe du premier niveau est $0.</li> <li>Les niveaux suivants par montant de frais unitaires sont définis pour facturer $0,20/100 appels.</li></ul> | Aucun quota défini. Le consommateur peut continuer à effectuer et payer les appels avec une limite de 100 appels/minute. |
| Préversion | Limité | Tarif fixé pour facturer le consommateur $0,15/100 appels. | Aucun quota défini. Le consommateur peut continuer à effectuer et payer les appels avec une limite de 200 appels/minute. |
| Utilisation initiale de la production | Niveau | Tarif fixé pour facturer les appels du consommateur $14,95/mois. | Quota défini pour limiter le consommateur à 50 000 appels/mois avec une limite de 100 appels/minute. |
| Croissance initiale | Niveau + dépassement | Niveaux gradués : <ul><li>Le montant fixe du premier niveau est de $89,95/mois pour les 100 000 premiers appels.</li><li>Les niveaux suivants par montant de frais unitaires sont définis pour facturer $0,10/100 appels .</li></ul> | Aucun quota défini. Le consommateur peut continuer à effectuer et payer les appels avec une limite de 100 appels/minute. |
| Scale | Niveau + dépassement | Niveaux gradués :<ul><li>Le montant fixe du premier niveau est de $449,95/mois pour les 500 000 premiers appels.</li><li>Les niveaux suivants par montant de frais unitaires sont définis pour facturer $0,06/100 appels .</li></ul> | Aucun quota défini. Le consommateur peut continuer à effectuer et payer les appels avec une limite de 1 200 appels/minute. |
| Croissance mondiale | Unité | Niveaux gradués, où chaque montant fixe de niveau est de $749,95/mois pour 1,5 million d’appels. | Aucun quota défini. Le consommateur peut continuer à effectuer et payer les appels avec une limite de 3 500 appels/minute. |

**Deux exemples montrant comment interpréter le modèle de revenu basé sur le tableau ci-dessus :**

* **Modèle de tarification de niveau**  
   Appliqué pour la prise en charge des consommateurs de l’API au cours de la **Phase de production initiale**  de la durée de vie. Avec la configuration du modèle de tarification de niveau, le consommateur :  
    * Paie $14,95/mois.  
    * Peut atteindre un maximum de 50 000 appels/mois. 
    * Est limité à 100 appels/minute.

* **Phase de mise à l’échelle de la durée de vie**  implémentée en appliquant le modèle de tarification **Niveau + Dépassement**, lorsque les consommateurs :
    * Paient $449,95/mois pour les 500 000 premiers appels. 
    * Sont facturés $0,06 supplémentaires/100 appels au-delà des 50 000 premiers. 
    * Est limité à 1 200 appels/minute.

### <a name="step-5---calibrate"></a>Étape 5 : étalonner

Étalonner la tarification dans le modèle de revenu à :

- Définir la tarification pour empêcher la surtarification ou la sous-tarification de votre API, en fonction de la recherche de marché à l’étape 3 ci-dessus.
- Éviter les points dans le modèle de revenu qui semblent déloyaux ou encourager les clients à contourner le modèle pour obtenir une tarification plus favorable.
- Veiller à ce que le modèle de revenu soit adapté pour générer une valeur de durée de vie totale (TLV) suffisante pour couvrir le coût total de possession et la marge.
- Vérifier que la qualité de vos offres de service dans chaque niveau de modèle de revenu peut être prise en charge par votre solution. 
    - Par exemple, si vous offrez une prise en charge de 3 500 appels par minute, assurez-vous que votre solution de bout en bout peut être mise à l’échelle pour prendre en charge ce niveau de débit.

### <a name="step-6---release-and-monitor"></a>Étape 6 : mise en production et surveillance

Choisir une solution appropriée pour collecter le paiement de l’utilisation de vos API.  Les fournisseurs ont tendance à former deux groupes :

- Les **Plateformes de paiement, telle que [Stripe](https://stripe.com/)**
    
    Calculer le paiement en fonction des métriques d’utilisation de l’API brute en appliquant le modèle de revenu spécifique que le client a choisi. Configurer la plateforme de paiement pour refléter votre stratégie de monétisation.
- Les **Fournisseurs de paiement, tel que [Adyen](https://www.adyen.com/)**

    Uniquement concerné par la simplification de la transaction de paiement. Vous devez appliquer votre stratégie de monétisation (par exemple, traduire les métriques d’utilisation de l’API en paiement) avant d’appeler ce service.

Utiliser la Gestion des API Azure pour accélérer et éliminer les risques liés à l’implémentation à l’aide des fonctionnalités intégrées fournies dans la Gestion des API. Pour plus d’informations sur les fonctionnalités spécifiques de la Gestion des API, consultez [Comment la Gestion des API prend-elle en charge la monétisation ?](monetization-support.md).

Implémenter une solution qui offre une flexibilité dans la façon dont vous codifier votre stratégie de monétisation dans les systèmes sous-jacents à l’aide de la même approche que l’exemple de projet. Avec un codage flexible, vous pouvez répondre de façon dynamique et réduire les risques et les coûts liés à l’apport de modifications.

Suivez la [documentation monetization GitHub repo](https://github.com/microsoft/azure-api-management-monetization) pour implémenter l’exemple de projet dans votre propre abonnement Azure.

Surveiller régulièrement la consommation de votre API pour vous permettre de prendre des décisions basées sur les preuves. Par exemple, si la preuve vous indique une activité clients, répétez les étapes 1 à 5 ci-dessus pour découvrir et traiter la source.

## <a name="ongoing-evolution"></a>Évolution continue

Passez régulièrement en revue votre stratégie de monétisation en réexaminant et en réévaluant toutes les étapes ci-dessus. Vous devrez peut-être faire évoluer votre stratégie de monétisation au fil du temps à mesure que vous en apprendrez davantage sur vos clients, sur les coûts de fourniture de l’API et sur la façon dont vous répondez aux évolutions de la concurrence sur le marché.

N’oubliez pas que la stratégie de monétisation n’est qu’une facette d’une implémentation d’API réussie. Les autres facettes sont les suivantes :
* L’expérience développeur
* La qualité de votre documentation
* Les mentions légales
* Votre capacité à mettre à l’échelle l’API pour respecter les niveaux de service validés.

## <a name="next-steps"></a>Étapes suivantes
* [Prise en charge de la monétisation par la Gestion des API](monetization-support.md).
* Déployez une Adyen de démonstration ou une intégration Stripe via le [Référentiel Git](https://github.com/microsoft/azure-api-management-monetization) associé.