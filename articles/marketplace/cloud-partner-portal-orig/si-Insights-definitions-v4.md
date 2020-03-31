---
title: Définitions des insights vendeur
description: Contient les définitions de la plupart des termes que vous rencontrerez dans Seller Insights.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 082e5f13ae44a58088b7728103415dbfa5a969f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280131"
---
<a name="seller-insights-definitions"></a>Définitions des insights vendeur
=======================

Le tableau suivant contient les définitions de la plupart des termes que vous rencontrerez dans les insights vendeur.

|  **Terme**                 |  **Définition**                                                                                                                              |
|  --------------------------------------------    |  ---------------------------------------------------------------------------------------------------------------------------------           |
| **Type de licence Azure**                               | Type de contrat de licence utilisé par les clients pour acheter Azure.  Également appelé canal.                                                  |
| **Type de licence Azure : fournisseur de solutions cloud**      | Le client final se procure Azure et votre offre de la Place de marché par le biais de son fournisseur de solutions cloud, qui agit en tant que revendeur.                 |
| **Type de licence Azure : entreprise**                   | Le client final se procure Azure et votre offre de la Place de marché par le biais d'un contrat Entreprise, signé directement auprès de Microsoft.                  |
| **Type de licence Azure : entreprise, via un revendeur**  | Le client final se procure Azure et votre offre de Place de marché par le biais d’un revendeur qui lui propose un contrat Entreprise avec Microsoft.     |
| **Type de licence Azure : paiement à l’utilisation**                | Le client final se procure Azure et votre offre de la Place de marché dans le cadre d’un contrat « Paiement à l’utilisation », signé directement avec Microsoft.                |
| **Montant des frais (DC)**                              | Montant facturé au client, dans la devise de facturation du client (*devise client*, DC).                                                                 |
| **Montant des frais (DE)**                               | Montant facturé au client, dans la *devise de paiement* (DE).                                                                      |
| **Date de facturation**                                      | Date à laquelle les frais imputables au client ont été calculés (généralement juste après la période d’utilisation).                                             |
| **Nom de l’instance cloud**                              | Instance de Microsoft Cloud sur laquelle des machines virtuelles ont été déployées.                                   |
| **Nom de l’instance cloud : Azure Global**                | Cloud public global de Microsoft.                           |
| **Nom de l’instance cloud : Azure Government**        | Clouds de Microsoft réservés aux gouvernements suivants : Chine, Allemagne ou États-Unis d’Amérique.                           |
| **État de collecte**  | Pour des frais spécifiques, dernier état du cycle de facturation et de collecte.  Par exemple : Collecte en cours, Collecté ou Remboursement.                  |
| **Taille de base**  | Nombre de cœurs virtuels présentés par la machine virtuelle associée à l’offre.               |
| **Ville du client**  | Nom de ville fourni par le client final. Il peut être différent du nom de la ville associée à l’abonnement Azure du client.  |
| **Langue de communication du client**        | Langue dans laquelle le client préfère communiquer.                                  |
| **Nom de la société du client**                  | Nom de société fourni par le client. Il peut être différent du nom de la ville associée à l’abonnement Azure du client.                                  |
| **Pays du client**                       | Nom de pays/région fourni par le client.  Il peut être différent du nom du pays ou de la région associé à l’abonnement Azure du client.                               |
| **Devise du client (DC)**                 | Devise privilégiée par le client pour la tarification et la facturation.                            |
| **Adresse e-mail du client**                         | Adresse e-mail fournie par le client final.  Elle peut être différente de l’adresse e-mail associée à l’abonnement Azure du client.                   |
| **Prénom du client**                              | Prénom fourni par le client.  Il peut être différent du prénom associé à l’abonnement Azure du client.                    |
| **Nom du client**                               | Nom fourni par le client.  Il peut être différent du prénom associé à l’abonnement Azure du client.                    |
| **ID du client**                               | Identificateur unique attribué à un client.  Un client peut avoir zéro ou plusieurs abonnements de la Place de marché Azure.                    |
| **Mode de paiement du client**                            | Mode de paiement utilisé par le client.  Par exemple, cela peut être un chèque électronique, une facture ou carte.                             |
| **Code postal du client**                             | Code postal fourni par le client.  Il peut être différent du code postal associé à l’abonnement Azure du client.                                                                 |
| **État du client**                                   | État (adresse) fourni par le client.  Il peut être différent de l’État associé à l’abonnement Azure du client.                                                                 |
| **Date d’acquisition**                                    | Date à laquelle le client a acheté pour la première fois une offre que vous avez publiée.                 |
| **Date d’annulation**                                        | Date à laquelle le client a annulé la dernière des offres achetées précédemment.                                                         |
| **Estimation des frais d’extension (DC)**                   | Estimation des frais d’extension relatifs à la quantité d’unités d’utilisation pour une référence SKU donnée (dans la devise du client). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.                            |
| **Estimation des frais d’extension (DE)**                   | Estimation des frais d’extension relatifs à la quantité d’unités d’utilisation pour une référence SKU donnée en fonction du taux de change à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.                  |
| **Paiement estimé (DE)**                            | Estimation du paiement relatif à la quantité d’unités d’utilisation pour une référence SKU donnée en fonction du taux de change à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.                        |
| **Prix estimé (DE)**                             | Estimation du prix d’une unité d’utilisation pour une référence SKU donnée en fonction du taux de change à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.                                             |
| **Nouveau client**                                  | La valeur vous permet de savoir s’il s’agit d’un nouveau client qui consomme ou achète une ou plusieurs de vos offres sur la Place de marché pour la première fois (ou pas).  La valeur est « Oui » si le mois civil est celui de la date d’acquisition.  La valeur est « Non » si le client a acheté une de vos offres avant le mois civil signalé.                                                       |
| **Référence SKU en préversion**                                  | La valeur indique si vous avez étiqueté la référence SKU en tant que « préversion ». La valeur est « Oui » si la référence SKU a été étiquetée ainsi, et seuls les abonnements Azure que vous avez autorisés peuvent déployer et utiliser cette image.  La valeur est « Non » si la référence SKU n’a pas été identifiée comme « préversion ».                                          |
| **Acceptation des messages promotionnels**                   | La valeur indique si le client a accepté les messages promotionnels provenant d’éditeurs de manière proactive. À ce stade, nous n'offrons pas cette possibilité aux clients. Par conséquent, nous avons indiqué « Non » sur le tableau. Une mise à jour interviendra lorsque cette fonctionnalité sera déployée.                                          |
| **Type de licence de la Place de marché**                         | Mode de facturation de l’offre de la Place de marché.                             |
| **Type de licence de la Place de marché : facturé via Azure**   | Pour cette offre de la Place de marché, Microsoft est votre agent et facture les clients de votre part. (Carte de crédit avec paiement à l’utilisation ou facture d’entreprise)       |
| **Type de licence de la Place de marché : BYOL (apportez votre propre licence)** | Le déploiement de la machine virtuelle requiert une clé de licence fournie par le client. Microsoft ne facture rien aux clients qui utilisent cette offre de la Place de marché. |
| **Type de licence de la Place de marché : gratuit**                   | L’offre de la Place de marché est configurée pour permettre à tous les utilisateurs d'y accéder gratuitement. Microsoft ne facture rien aux clients qui utilisent cette offre de la Place de marché.    |
| **Type de licence de la Place de marché : Microsoft en tant que revendeur**  | Microsoft est votre revendeur pour cette offre de la Place de marché.                               |
| **Nom de la société dans l’abonnement de la Place de marché**  | Nom de société fourni par le client dans son abonnement Azure.                           |
| **ID d’abonnement de la Place de marché**            | Identificateur unique associé à l’abonnement Azure que le client a utilisé pour acheter votre offre de la Place de marché.  Anciennement, GUID d’abonnement Azure.  Dans certains cas, comme une commande SAAS, aucun abonnement n’est requis.           |
| **Frais Microsoft (DC)**                               | Frais prélevés par Microsoft sur la transaction dans la devise du client.                              |
| **Nom de l’offre**                                | Nom de l’offre de la Place de marché.                                                             |
| **Type d’offre**                                | Type de l’offre de la Place de marché Microsoft.  Par exemple, il peut s’agir d’une application managée, de licences de machine virtuelle ou d’un conteneur.                           |
| **Date d’annulation de la commande**                         | Date à laquelle la commande passée sur la Place de marché a été annulée.                                                       |
| **ID de commande**                                  | Identificateur unique de la commande du client pour votre service de la Place de marché.  Les offres basées sur l’utilisation de machines virtuelles ne sont pas associées à une commande.                 |
| **Date de la commande**                       | Date à laquelle la commande de la Place de marché a été passée.                                                        |
| **Quantité commandée**                       | Quantité de commandes pour la même référence SKU associée à un abonnement Azure.  Pour les commandes SAAS, cette valeur représente le nombre de postes associés à cette instance d’une commande.                                            |
| **État de la commande**                              | État d’une commande de la Place de marché au moment de la dernière actualisation des données.  Par exemple, la commande peut être active ou annulée.             |
| **État de la commande : active**                           | Le client a passé commande et ne l’a pas annulée.   |
| **État de la commande : annulée**                        | Le client a passé commande, puis l’a annulée.        |
| **Montant du paiement (DE)**                             | Montant qui vous est versé, dans la devise de paiement (DE) de votre choix.                                       |
| **Devise de paiement (DE)**                           | Devise utilisée pour vos paiements.                                                                |
| **Date de paiement**                                    | Date à laquelle la demande de paiement a été envoyée à l’établissement bancaire de votre choix par Microsoft.                                                                         |
| **État de paiement**                                  | État du paiement au moment de la dernière actualisation des données.                                   |
| **État de paiement : en cours**               | La transaction n’est pas prête pour le paiement. (Pour plus d’informations, consultez le statut de collecte.)                             |
| **État de paiement : versé**                          | La transaction a été incluse dans le calcul d'un précédent paiement. Les valeurs positives sont payées, et les valeurs négatives sont déduites du montant total dû. |
| **État de paiement : paiement à venir**                   | La transaction est prête pour le paiement et sera incluse dans le calcul du paiement disponible suivant.                      |
| **Prix (DC)**                                   | Prix d’une unité d’utilisation pour une référence SKU donnée (dans la devise du client).                                                                  |
| **Adresse e-mail du fournisseur**                                 | Adresse e-mail du fournisseur impliqué dans la relation entre Microsoft et le client final. Si le client est une entreprise passant par un revendeur, ce sera le revendeur.  Si un fournisseur de solutions cloud (CSP) est impliqué, ce sera le CSP.                                                                 |
| **Nom du fournisseur**                                 | Nom du fournisseur impliqué dans la relation entre Microsoft et le client final. Si le client est une entreprise passant par un revendeur, ce sera le revendeur.  Si un fournisseur de solutions cloud (CSP) est impliqué, ce sera le CSP.                                                                 |
| **URI de ressource**                                  | Chemin d’accès où l’artefact de déploiement a été déployé.                                            |
| **Référence (SKU)**                                           | Nom de la référence SKU, tel que défini lors de la publication. Une offre peut englober de nombreuses références SKU, mais une référence SKU ne peut être associée qu’à une seule offre.              |
| **Type de facturation de la référence SKU**                                 | Mode de facturation de la référence SKU.                                                                                                             |
| **Type de facturation de la référence SKU : BYOL (apportez votre propre licence)**         | Le déploiement de la machine virtuelle ou du service requiert une clé de licence fournie par le client. Microsoft ne facture rien aux clients qui utilisent ces offres de la Place de marché.   |
| **Type de facturation de la référence SKU : Microsoft en tant que revendeur**          | Microsoft est votre revendeur pour cette référence SKU.   |
| **Type de facturation de la référence SKU : gratuit**                           | La référence SKU est configurée pour permettre à tous les utilisateurs d’y accéder gratuitement. Microsoft ne facture rien aux clients qui utilisent cette référence SKU.                           |
| **Type de facturation de la référence SKU : payant**                           | Pour cette référence SKU, Microsoft est votre agent et facture les clients de votre part. (Carte de crédit avec paiement à l’utilisation ou facture d’entreprise)                   |
| **Type de facturation de la référence SKU : Version d’évaluation**                          | Le client bénéficie de la période d’essai gratuite. Sauf annulation ou suppression, l'offre d'essai sera convertie en offre payante.                                         |
| **Montant des taxes (DC)**                                  | Montant des taxes appliquées à la facture du client dans la devise du client (DC).                                                               |
| **Date de la transaction**                                 | Date de la transaction enregistrée dans votre rapport de paiement.                                                                           |
| **Type de transaction**                               | Type de la transaction.  Par exemple, cela peut être un débit, un remboursement ou un ajustement de paiement.                                                                                        |
| **Date de fin d’essai**                                 | La date de fin de la période d’essai de cette commande approche ou est dépassée.                                                                           |
| **Date d’utilisation**                                       | Date d'utilisation par le client.                                                                                                             |
| **Quantité d’utilisation**                                       | Utilisation de la référence SKU par le client. Pour les images de machines virtuelles, les enregistrements d’utilisation représentent l’utilisation au cours de la période signalée pour cette taille de machine virtuelle et cette référence SKU.                                               |
| **Référence d’utilisation**                                       | Identificateur correspondant à un ou plusieurs jours d’utilisation client pour une référence SKU associée à une entrée du rapport de paiement.                                               |
| **Type d'utilisation**                                       | Description de l’utilisation mesurée. (Utilisation normalisée ou Utilisation brute)                                                              |
| **Type d’utilisation : utilisation normalisée**                | Heures d’utilisation normalisées afin de prendre en compte le nombre de cœurs de machines virtuelles impliqués dans l’utilisation : [nombre de cœurs de machines virtuelles] x [heures d’utilisation brutes]. Les machines virtuelles désignées sous le nom de « SHAREDCORE » utilisent 1/6 (ou 0,1666) comme multiplicateur [nombre de cœurs de machines virtuelles].                                        |
| **Type d’utilisation : utilisation brute**                        | Heures d’utilisation de votre offre de la Place de marché.  Cette valeur est présentée en heures « civiles ».  Il s’agit du nombre d’exécutions des machines virtuelles.                           |
| **Unités d’utilisation**                                   | Unité de mesure de l’utilisation mentionnée. Les machines virtuelles sont toujours mesurées à l'aide d’unités horaires.                               |
| **Taille de la machine virtuelle**                                       | Représente la taille du matériel de la machine virtuelle associée à l’offre Azure. Exemples : `Basic_A0`, `Standard_A11`, `Standard_D12`, et `Standard_G4`.   |
|  |  |


