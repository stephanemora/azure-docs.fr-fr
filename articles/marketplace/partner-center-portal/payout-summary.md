---
title: Récapitulatif des paiements de la place de marché commerciale | Place de marché Azure
description: La page Récapitulatif des paiements vous montre les détails de l’argent que vous avez gagné avec votre offre. Elle vous permet également de savoir quand vous recevrez des paiements et le montant que vous allez percevoir.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 0d05802c9d5d80f91913291d710b674369f0ff17
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979986"
---
# <a name="payout-reporting"></a>Rapports de paiement

La page **Récapitulatif des paiements** montre les détails de l’argent que vous avez gagné avec Microsoft. Elle vous permet également de savoir quand vous recevrez des paiements et le montant que vous allez percevoir.

Si vous vendez des offres sur la Place de marché Azure, **Récapitulatif des paiements** vous présente également des informations sur les paiements qui vous ont été versés. Pour plus d’informations sur le processus de paiement de la Place de marché Azure, consultez [Politiques concernant la participation à la Place de marché Microsoft Azure](https://go.microsoft.com/fwlink/p/?LinkId=722436) et [Contrat d’éditeur de la Place de marché Microsoft Azure](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Pour être éligible au paiement, votre produit doit atteindre le [seuil de paiement](payment-thresholds-methods-timeframes.md) de 50 $. Pour plus d’informations sur le seuil de paiement, consultez cette page pour prendre connaissance du [Contrat d’éditeur de la Place de marché Microsoft Azure](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Rapport de paiement : différence entre le portail Cloud Partner et l’Espace partenaires](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Types de clients](#customer-types)
- [Relation entre le paiement et l’utilisation](#corelation-between-payout-and-usage)
- [Téléchargement de l’historique des transactions](#transaction-history-download-export)
- [Questions et support sur la facturation](#billing-questions-and-support)

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Rapport de paiement : différence entre le portail Cloud Partner et l’Espace partenaires

| | Portail des partenaires cloud | Espace partenaire |
|---------|---------|---------|
| Liens | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory et https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| Navigation | Rapports de paiement fournis dans le paiement des insights | Rapports de paiement fournis dans l’Espace partenaires – Icône de paiement |
| Étendue | <ul> <li>La transaction par article est visible, quand la collecte en cours, quand elle est terminée et quand elle est payée </li> <li>Rapports : montre tous les articles une fois que le bon de commande est créé, notamment la collecte et la facturation en cours, ainsi que l’état de la collecte et les articles qui ne peuvent pas encore être payés. </li> </ul> | <ul> <li>Montre les articles une fois qu’ils sont considérés comme des revenus éligibles.</li> <li>Les clients paient d’abord Microsoft, puis les éditeurs de logiciels indépendants peuvent voir le rapport de paiement commencer.</li> <li>Le rapport de paiement ne montre pas la collecte ni la facturation en cours.  </li> </ul>  |
| Transaction non prête pour le paiement | Facturation en cours | Estimation du prochain paiement : Le paiement est dans l’état non traité.  |
| État de paiement |  | Non traité : <br> Le revenu est éligible au paiement. Il reste dans cet état pendant une période de réflexion telle que définie dans le guide du programme d’incentives. <br> <br> Ensuite : <br> Des vérifications internes en attente sont générées par l’ordre de paiement avant le traitement du paiement. <br> <br> Envoyé : <br> Le paiement a été envoyé à votre banque. |

## <a name="customer-types"></a>Types de clients 

### <a name="enterprise-agreement"></a>Contrat Entreprise

Les clients sans Contrat Entreprise sont facturés au mois pour les licences logicielles de la Place de marché. Les clients ayant un Contrat Entreprise sont facturés au mois, mais reçoivent une seule facture par trimestre.

### <a name="credit-cards-and-monthly-invoice"></a>Cartes de crédit et facture mensuelle

Les clients peuvent également payer à l’aide d’une carte de crédit et d’une facture mensuelle. Dans ce cas, vos frais de licence logicielle sont facturés tous les mois.

### <a name="csp-and-direct-pay-users"></a>CSP et utilisateurs à paiement direct

Exemple d’un client qui fait un achat avec une carte de crédit.

## <a name="corelation-between-payout-and-usage"></a>Relation entre le paiement et l’utilisation 

|Description    |    Date  | Commandes/utilisation  | Paiement |
|----------|----------|-----------|-------------|
|Période de commande   | 15 août 2019 au 30 août 2019 | **Commandes des attributs de corrélation** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Utilisation** <br> <ul> <li>CustomerId </li> <li>Nom du client</li> <li>(UsageReference)PurchaseRecordId/LineItemId</li> <li> Estimation des frais d’extension <br> Paiement estimé (DE) </li> </ul> |  |
|Échéance (mois)   | 30 août 2019 | | |
|Date de facturation | 1er septembre 2019 | | |
|Date de paiement du client | 1er septembre 2019 | | |
|Période de dépôt (cartes de crédit uniquement, 30 jours) | 1er septembre 2019 au 30 septembre 2019 | | **Commandes des attributs de corrélation :** <br> <ul><li>AssetId</li> <li>ID du client</li> <li> Nom du client</li> </ul> <br> **Utilisation** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nom du client</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **État de paiement :** Non traité |
|Début de la période de collecte | 1er septembre 2019 | | |
|Fin de la période de collecte (30 jours au maximum) | 30 septembre 2019 | | |
|Date de calcul du paiement (tous les mois le 15) | 1er octobre 2019 | | **Attributs de corrélation** <br> <ul><li>AssetId</li> <li>ID du client</li> <li>Nom du client</li> </ul> <br> **Utilisation** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nom du client</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **État de paiement :** UpComing |
|Date de paiement | 15 octobre 2019 | | **Attributs de corrélation** <br> <ul><li>AssetId</li> <li>ID du client</li> <li> Nom du client</li> </ul> <br> **Utilisation** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nom du client</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **État de paiement :** Paiement envoyé |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Contrat Entreprise (clients trimestriels/mensuels)

| Description |    Date  | Usage | Paiement |
|----------|----------|---------|-----------|
|Période de commande | 15 août 2019 au 30 août 2019 | **Commandes des attributs de corrélation** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Rapport d’utilisation** <br> <ul> <li>CustomerId </li> <li>Nom du client</li> <li>(UsageReference)PurchaseRecordId/LineItemId</li> <li> Estimation des frais d’extension <br> Paiement estimé (DE) </li> </ul> | |
|Échéance (trimestre) | 30 septembre 2019 | | |
|Date de facturation | 15 octobre 2019 | | |
|Période de dépôt (cartes de crédit uniquement, 30 jours) | n/a | | |
|Début de la période de collecte | 15 octobre 2019 | | |
|Cartes de crédit uniquement, 30 jours | 1er novembre 2019 au 30 novembre 2019 | | |
|Fin de la période de collecte (90 jours au maximum) | 15 janvier 2020 | | |
|Date de paiement du client | 30 décembre 2019 | | |
|Calcul du paiement | 15 janvier 2020 | | |
|Date de paiement | 15 février 2020 | | **Pour les clients basés sur le trimestre** <br> <br> **Rapport sur les commandes** <br> <ul><li>AssetId</li> <li>ID du client</li> <li> Nom du client</li> </ul> <br> **Utilisation** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nom du client</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **État de paiement :** envoyé |

<!---
## Billing

Depending on the transaction option used, the publisher’s software license fees can be presented as follows:

* Free: No charge for software licenses.
* **Bring the own license (BYOL)**: Any applicable charges for software licenses are managed directly between the publisher and customer. Microsoft only passes through Azure infrastructure usage fees. (Virtual Machines and Azure Applications only).
* **Pay-as-you-go**: Software license fees are presented as a per-hour, per-core (VCPU) pricing rate based on the Azure infrastructure used. This only applies to Virtual Machines and Azure Applications. 
* **Subscription pricing**: Software license fees are presented as a monthly or annual recurring fee billed as a flat rate or per-seat. This only applies to SaaS Apps and Azure Applications - Managed Apps.

### Pay as you go

If you enable the Pay-As-You-Go option, then you have the following cost structure. 

* If you enable the Pay-As-You-Go option, then you have the following cost structure.

|Your license cost  | $1.00 per hour  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$1.14 per hour*       |

* In this scenario, Microsoft bills $1.14 per hour for use of your published VM image.

|Microsoft bills  | $1.14 per hour  |
|---------|---------|
|Microsoft pays you 80% of your license cost|   $0.80 per hour     |
|Microsoft keeps 20% of your license cost  |  $0.20 per hour       |
|Microsoft keeps 100% of the Azure usage cost | $0.14 per hour |

### Bring Your Own License (BYOL)

* If you enable the BYOL option, then you have the following cost structure.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

### SaaS App subscription

This option must be configured to sell through Microsoft and can be priced at a flat rate or per user on a monthly or annual basis.
•   If you enable the Sell through Microsoft option for a SaaS offer, then you have the following cost structure.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.
* Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020. In this scenario, Microsoft bills $100.00 for your software license and pays out $90.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost <br> \* Microsoft pays you 90% of your license cost for any qualified SaaS apps   |   $80.00 per month <br> \* $90.00 per month    |
|Microsoft keeps 20% of your license cost <br> \* Microsoft keeps 10% of your license cost for any qualified SaaS apps.  |  $20.00 per month <br> \* $10.00     |

**Reduced Marketplace Service Fee:** For certain SaaS Products that you publish on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%.  In order for your Product to qualify, at least one of your products must be designated by Microsoft as either IP co-sell ready or IP co-sell prioritized. To receive this reduced Marketplace Service Fee for the month, eligibility must be met at least five (5) business days before the end of the previous calendar month. Reduced Marketplace Service fee will not apply to VMs, Managed Apps or any other products made available through our Commercial Marketplace.  This Reduced Marketplace Service Fee will be available to qualified offers, with license charges collected by Microsoft between May 1, 2019 and June 30, 2020.  After that time, the Marketplace Service Fee will return to its normal amount.

### Customer invoicing, payment, billing, and collections

**Invoicing and payment**

Publisher can use the customer's preferred invoicing method to deliver subscription or PAYGO software license fees.

**Enterprise agreement** 

If the customer's preferred invoicing method is the Microsoft Enterprise Agreement, your software license fees will be billed using this invoicing method as an itemized cost, separate from any Azure-specific usage costs.

**Credit cards and monthly invoice** 

Customers can also pay using a credit card and a monthly invoice. In this case, your software license fees will be billed just like the Enterprise Agreement scenario, as an itemized cost, separate from any Azure-specific usage costs.



**Billing and collections** 

Publisher software license billing is presented using the customer selected method of invoicing and follows the invoicing timeline. Customers without an Enterprise Agreement in place are billed monthly for marketplace software licenses. Customers with an Enterprise Agreement are billed monthly via an invoice that is presented quarterly.

When subscription or Pay-as-You-Go pricing models are selected, Microsoft acts as the agent of the publisher and is responsible for all aspects of billing, payment, and collection.

### Publisher payout and reporting

* Any software licensing fees collected by Microsoft as an agent are subject to a 20% transaction fee unless otherwise specified and are deducted at the time of publisher payout.

* Customers typically purchase using the Enterprise Agreement or a credit-card enabled pay-as-you-go agreement. The agreement type determines billing, invoicing, collection, and payout timing.
--->

## <a name="transaction-history-download-export"></a>Exportation du téléchargement de l’historique des transactions

Cette option permet de télécharger chaque article de revenu que vous voyez dans la page d’historique des transactions, le type de revenu, la date, le montant associé de la transaction, le client, le produit et d’autres détails transactionnels applicables au programme d’incentives. 

| Nom de la colonne     | Description    | 
|-------------|-------------------------------|
| earningId                      | Identificateur unique de chaque revenu                                                                                                       |
| participantId                  | Identité principale du revenu partenaire dans le cadre du programme                                                                            | 
| participantIdType              | Principalement, ID des programmes d’incentives et ID de vendeur pour les programmes du Store et la Place de marché Azure                                          | 
| participantName                | Nom du partenaire de revenu                                                                                                              | 
| partnerCountryCode             | Emplacement/pays du partenaire de revenu                                                                                                  |
| programName                    | Nom du programme d’incentives/du Store                                                                                                             | 
| transactionId                  | Identificateur unique de la transaction                                                                                                    | 
| transactionCurrency            | Devise dans laquelle la transaction cliente d’origine s’est produite (il ne s’agit pas de la devise de l’emplacement du partenaire)                                     | 
| transactionDate                | Date de la transaction Utile pour les programmes où de nombreuses transactions contribuent à un revenu                                           | 
| transactionExchangeRate        | Taux de change utilisé pour afficher le montant USD correspondant de la transaction                                                                 | 
| transactionAmount              | Montant de la transaction dans la devise de transaction d’origine en fonction du revenu généré                                              | 
| transactionAmountUSD           | Montant de la transaction en USD                                                                                                                | 
| lever                          | Indique une règle métier pour le revenu                                                                                                  | 
| earningRate                    | Taux d’incentives appliqué au montant de la transaction pour générer un revenu                                                                      | 
| quantité                       | Varie selon le programme. Indique la quantité facturée pour les programmes transactionnels                                                            | 
| quantityType                   | Indique le type de quantité, par exemple : Quantité facturée, utilisateurs actifs mensuels                                                                                     |
| earningType                    | Indique s’il s’agit de frais, de remise, de coop, de vente, etc.                                                                                          | 
| earningAmount                  | Montant du revenu dans la devise de la transaction d’origine                                                                                      |
| earningAmountUSD               | Montant du revenu en USD                                                                                                                    |
| earningDate                    | Date du revenu                                                                                                                      |
| calculationDate                | Date à laquelle le revenu a été calculé dans le système                                                                                            |
| earningExchangeRate            | Taux de change utilisé pour afficher le montant USD correspondant                                                                                  |
| exchangeRateDate               | Date du taux de change utilisée pour calculer le montant USD du revenu                                                                                   | 
| paymentAmountWOTax             | Montant du revenu (hors taxe) dans la devise de destination pour les paiements « envoyés » uniquement                                                                 |
| paymentCurrency                | Devise de destination choisie par le partenaire dans le profil de paiement. Affichée uniquement pour les paiements envoyés                                                   |
| paymentExchangeRate            | Taux de change utilisé pour calculer paymentAmountWOTax dans la devise de paiement avec ExchangeRateDate                                            |
| paymentId            | Identificateur unique du paiement. Ce numéro est visible dans votre relevé bancaire                                            |
| paymentStatus            | État du paiement                                            |
| paymentStatusDescription            | Description conviviale de l’état du paiement                                            |
| customerId                     | Sera toujours vide                                                                                                                     |
| customerName                   | Sera toujours vide                                                                                                                     |
| partNumber                     | Sera toujours vide                                                                                                                     |
| ProductName                    | Nom de produit lié à la transaction                                                                                                       |
| productId                      | Identificateur de produit unique                                                                                                                |
| parentProductId                | Identificateur de produit parent unique. Remarque : s’il n’existe aucun produit parent pour la transaction, l’ID du produit parent = ID du produit. |
| parentProductName              | Nom du produit parent. Remarque : s’il n’existe aucun produit parent pour la transaction, le nom du produit parent = Nom du produit.   |
| productType                    | Type de produit (par exemple application, module complémentaire, jeu, etc.)                                                                                        |
| invoiceNumber                  | Numéro de facture (applicable à EA uniquement)                                                                                                  |
| resellerId                     | Identificateur du revendeur                                                                                                                      |
| resellerName                   | Nom du revendeur                                                                                                                            |
| transactionType                | Type de transaction (achat, remboursement, contrepassation, rétrofacturation, etc.)                                                               |
| localProviderSeller            | Fournisseur local/vendeur d’enregistrement                                                                                                          |
| taxRemitted                    | Montant des taxes versées (ventes, consommation, ou TVA/GST).                                                                                   |
| taxRemitModel                  | Tiers responsable du versement des taxes (ventes, consommation, ou TVA/GST).                                                                    |
| storeFee                       | Montant retenu par Microsoft comme frais pour rendre l’application ou le module complémentaire disponible dans le Store.                                            |
| transactionPaymentMethod       | Moyen de paiement client utilisé pour la transaction (par exemple carte, facturation d’opérateur de téléphonie mobile, PayPal, etc.)                                |
| tpan                           | Indique le réseau publicitaire tiers                                                                                                     |
| customerCountry                | Pays du client                                                                                                                         |
| CustomerCity                   | Ville du client                                                                                                                            |
| customerState                  | État du client                                                                                                                           |
| customerZip                    | Code postal du client                                                                                                                 |
| ID de locataire                       |                                                                                                                                          |
| externalReferenceId            | Identificateur unique du programme                                                                                                        |
| externalReferenceIdLabel       | Étiquette de l’identificateur unique                                                                                                                  |
| transactionCountryCode       | Code du pays dans lequel la transaction s’est produite                                                                                                                  |
| taxCountry       | Pays du client de la vente                                                                                                                  |
| taxState       | État du client de la vente                                                                                                                  |
| taxCity       | Ville du client de la vente                                                                                                                  |
| taxZipCode       | Code postal du client de la vente                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Code du programme       | Chaîne à mapper avec le nom du programme                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Montant du revenu dans la dernière devise de paiement (ce champ sera vide si aucun paiement précédent n’a été effectué)                                                                                                                   |
| lastPaymentCurrency       | Dernière devise de paiement (ce champ sera vide si aucun paiement précédent n’a été effectué)                                                                                                                   |
| AssetId       | Identificateur unique des commandes du client pour votre service de la place de marché.  Il représente les articles achetés traités. Il peut y avoir plusieurs ressources.                                                                                                                   |
| OrderId       | Associé à la facture d’un client                                                                                                                   |
| LineItemId       | Ligne individuelle dans la facture d’un client                                                                                                                   |
| Pays du client       | Nom de pays fourni par le client.  Il peut être différent du pays associé à l’abonnement Azure d’un client.                                                                                                                   |
| EmailAddress de client       | Adresse e-mail fournie par le client final.  Elle peut être différente de l’adresse e-mail associée à l’abonnement Azure d’un client.                                                                                                                   |
| SkuId       | ID de la référence SKU, tel que défini lors de la publication. Une offre peut englober de nombreuses références SKU, mais une référence SKU ne peut être associée qu’à une seule offre.                                                                                                                   |

>[!Note]
>Tous les rapports et insights associés à l’option de publication Transaction sont disponibles dans la section Insights du portail Cloud Partner ou dans la section Analytics de l'Espace partenaires.

## <a name="billing-questions-and-support"></a>Questions et support sur la facturation

Pour obtenir de l'aide sur les questions de facturation, contactez le [support de l'éditeur de la place de marché commerciale](https://aka.ms/marketplacepublishersupport).
