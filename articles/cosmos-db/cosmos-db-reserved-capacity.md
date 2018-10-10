---
title: Prépayer des ressources Azure Cosmos DB pour réaliser des économies | Microsoft Docs
description: Découvrez comment acheter une capacité réservée Azure Cosmos DB pour réduire vos coûts de calcul.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 1be2d67d8a1ee51c4883ae1f50b80ad3a9691c2d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981965"
---
# <a name="prepay-for-azure-cosmos-db-resources-with-reserved-capacity"></a>Prépayer des ressources Azure Cosmos DB avec une capacité réservée

La capacité réservée Azure Cosmos DB vous permet d’économiser de l’argent en prépayant les ressources Azure Cosmos DB pour une période d’un ou trois ans. La capacité réservée Azure Cosmos DB vous permet de bénéficier d’une remise sur le débit provisionné pour des ressources Cosmos DB telles que des bases de données et des conteneurs (tables/collections/graphes). La capacité réservée Azure Cosmos DB peut réduire sensiblement vos coûts Cosmos DB, jusqu’à 65 % sur les prix habituels avec un engagement d’un ou de trois ans. La capacité réservée permet de bénéficier d’une remise, et n’affecte pas l’état de runtime de vos ressources Cosmos DB.

La capacité réservée Azure Cosmos DB couvre le débit provisionné pour vos ressources, mais pas les frais de stockage et de mise en réseau. Dès que vous achetez une réservation, les frais de débit qui correspondent aux attributs de la réservation ne sont plus facturés au tarif du paiement à l’utilisation. Pour plus d’informations sur les réservations, consultez l’article [Réservations Azure](../billing/billing-save-compute-costs-reservations.md). 

Vous pouvez acheter de la capacité réservée Azure Cosmos DB sur le [portail Azure](https://portal.azure.com). Pour acheter une capacité réservée :

* Vous devez disposer du rôle de propriétaire sur au moins un abonnement Entreprise ou un abonnement Paiement à l’utilisation.  
* Concernant les abonnements Entreprise, les achats de réservation Azure doivent être activés sur le [portail EA](https://ea.azure.com/).  
* Pour ce qui est du programme des fournisseurs de solutions cloud, seuls les agents d’administration ou les agents commerciaux peuvent acheter une capacité réservée Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Déterminer le débit requis avant l’achat

La taille de la réservation doit être basée sur la quantité totale de débit utilisée par les ressources Azure Cosmos DB existantes ou sur le point d’être déployées (par exemple, les bases de données ou les conteneurs : collections, tables, graphes). Vous pouvez déterminer le débit requis de plusieurs manières :

* Accédez au [portail Azure](https://portal.azure.com), recherchez votre compte Azure Cosmos DB et ouvrez le panneau Métriques ; sous l’onglet **Débit**, vous trouverez les détails du débit moyen/s sur une période de 3 à 6 mois. Indiquez cette taille en guise d’unités de capacité réservée au moment de l’achat.

Si vous êtes sous Contrat Entreprise, vous pouvez télécharger votre fichier d’utilisation et consulter la valeur **Type de service** dans la section **Informations supplémentaires** pour obtenir les détails du débit Azure Cosmos DB.

Vous pouvez également calculer la somme du débit moyen de toutes les charges de travail sur vos comptes Azure Cosmos DB que vous prévoyez d’exécuter au cours de la prochaine année ou des trois prochaines années et utiliser cette somme pour la réservation.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Acheter une capacité réservée Azure Cosmos DB

1. Connectez-vous au [Portail Azure](https://portal.azure.com).  

2. Sélectionnez **Tous les services** > **Réservations** > **Ajouter**.  

3. Dans le volet **Sélectionner le type de produit**, choisissez **Azure Cosmos DB**, puis **Sélectionner** pour acheter une nouvelle réservation.  

4. Renseignez les champs requis, comme décrit dans le tableau ci-dessous :

   ![Remplir le formulaire de réservation de capacité](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |Champ  |Description  |
   |---------|---------|
   |NOM   |    Nom de la réservation. Ce champ est automatiquement renseigné avec le `CosmosDB_Reservation_<timeStamp>`. Vous pouvez fournir un autre nom au moment de la création de la réservation ou renommer la réservation après l’avoir créée.      |
   |Abonnement  |   Abonnement utilisé pour payer la capacité réservée Azure Cosmos DB. Les coûts initiaux sont facturés selon le mode de paiement défini sur l’abonnement sélectionné. Le type d’abonnement doit être un des types suivants : <br/><br/>  [Contrat Entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (numéro d’offre : MS-AZR-0017P) : pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. <br/><br/> [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) (numéro d’offre : MS-AZR-0003P) : pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.    |
   |Étendue   |   L’étendue de la réservation contrôle le nombre d’abonnements pouvant tirer parti de l’avantage de facturation associé à la réservation. Elle contrôle également la manière dont la réservation est appliquée à des abonnements spécifiques. L’étendue d’une réservation est « Abonnement unique » ou « Partagé ». Si vous sélectionnez :   <br/><br/>  **Abonnement unique** : la remise de réservation est appliquée aux instances Azure Cosmos DB incluses dans l’abonnement sélectionné. <br/><br/>  **Partagé** : la remise de réservation est appliquée aux instances Azure Cosmos DB en cours d’exécution dans tout abonnement de votre contexte de facturation. La façon dont vous vous êtes inscrit à Azure détermine le contexte de facturation. Pour les clients Entreprise, l’étendue partagée correspond à l’inscription et inclut tous les abonnements (à l’exception des abonnements de développement/test) au sein de l’inscription. Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.  <br/><br/> Vous pouvez changer l’étendue de la réservation après avoir acheté la capacité réservée.  |
   |Type de capacité réservée   |  Le type de capacité réservée est le débit provisionné en termes d’unités de requête.|
   |Unités de capacité réservée  |      Quantité de débit que vous souhaitez réserver. Vous pouvez calculer cette valeur en déterminant le débit nécessaire pour toutes vos ressources Cosmos DB (par exemple, les bases de données ou les conteneurs) par région, puis en multipliant le résultat obtenu par le nombre de régions à associer à votre base de données Cosmos DB.  <br/> Par exemple, si vous avez cinq régions avec chacune 1 million de RU/s, sélectionnez 5 millions de RU/s pour l’achat de capacité de réservation.    |
   |Terme  |   Une année ou trois ans.   |

5. Vérifiez la remise et le prix de la réservation dans la section **Coûts**. Ce prix de réservation s’applique aux ressources Azure Cosmos DB avec débit provisionné dans toutes les régions.  

6. Sélectionnez **Achat**. La capture d’écran suivante s’affiche une fois l’achat correctement effectué. 

   ![Remplir le formulaire de réservation de capacité](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

Une fois achetée, une réservation est immédiatement appliquée à toutes les ressources Azure Cosmos DB existantes qui satisfont à ses conditions. Si vous n’avez pas de ressources Azure Cosmos DB, la réservation s’applique quand vous déployez une nouvelle instance Cosmos DB qui satisfait aux conditions de la réservation. Dans les deux cas, la période de la réservation démarre une fois l’achat correctement effectué. 

Quand votre réservation expire, vos instances Azure Cosmos DB continuent à s’exécuter et sont facturées aux prix habituels du paiement à l’utilisation.

## <a name="next-steps"></a>Étapes suivantes

La remise de réservation est appliquée automatiquement aux ressources Azure Cosmos DB qui correspondent à l’étendue et aux attributs de la réservation. Vous pouvez mettre à jour l’étendue de la réservation par le biais du portail Azure, de PowerShell, de CLI ou de l’API.

*  Pour découvrir comment les remises sur capacité réservée sont appliquées à Azure Cosmos DB, consultez [Comprendre la remise sur réservation Azure](../billing/billing-understand-cosmosdb-reservation-charges.md)

* Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

   * [Qu’est-ce qu’une réservation Azure ?](../billing/billing-save-compute-costs-reservations.md)  
   * [Gérer les réservations Azure](../billing/billing-manage-reserved-vm-instance.md).  
   * [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](../billing/billing-understand-reserved-instance-usage.md)
   * [Réservations Azure dans le cadre du programme Fournisseur de solutions Microsoft Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

