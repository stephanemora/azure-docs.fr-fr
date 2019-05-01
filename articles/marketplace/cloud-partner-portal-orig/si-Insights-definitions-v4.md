---
title: Définitions des insights vendeur
description: Fournit des définitions pour un grand nombre des termes du contrat, que vous trouverez dans les informations du vendeur
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: e23297d8cd6cab5abeedc1a12fa1a37c93e94283
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943613"
---
<a name="seller-insights-definitions"></a>Définitions des insights vendeur
=======================

Le tableau suivant contient les définitions de la plupart des termes que vous rencontrerez dans les insights vendeur.

|  **Terme**                 |  **Définition**                                                                                                                              |
|  --------------------------------------------    |  ---------------------------------------------------------------------------------------------------------------------------------           |
| **Type de licence Azure**                               | Type de contrat de licence utilisé par les clients pour acheter Azure.  Également appelé canal.                                                  |
| **Type de licence Azure : Fournisseur de solutions de cloud**      | Le client final se procure Azure et votre offre de la Place de marché par le biais de son fournisseur de solutions cloud, qui agit en tant que revendeur.                 |
| **Type de licence Azure : Enterprise**                   | Le client final se procure Azure et votre offre de la Place de marché par le biais d'un contrat Entreprise, signé directement auprès de Microsoft.                  |
| **Type de licence Azure : Entreprise, via un revendeur**  | Le client final procure Azure et votre offre place de marché auprès d’un revendeur qui facilite leur contrat entreprise avec Microsoft.     |
| **Type de licence Azure : Paiement**                | Le client final procure Azure et votre offre place de marché via un contrat de « Paiement », connecté directement auprès de Microsoft.                |
| **Montant des frais (CC)**                              | Le montant facturé pour le client, dans le *devise client* (CC) pour la facturation.                                                                 |
| **Montant des frais (PC)**                               | Le montant facturé pour le client, dans le *devise de paiement* (PC).                                                                      |
| **Date de facturation**                                      | La date que des frais du client a été calculée (en général immédiatement après la période d’utilisation).                                             |
| **Nom de l’Instance cloud**                              | Instance de Microsoft Cloud sur laquelle des machines virtuelles ont été déployées.                                   |
| **Nom de l’Instance cloud : Azure Global**                | Le cloud public de Microsoft global.                           |
| **Nom de l’Instance cloud : Azure Government**        | Clouds de Microsoft Government spécifiques pour l’une des gouvernements suivantes : Chine, Allemagne ou États-Unis d’Amérique.                           |
| **Statut de la collection**  | Pour un coût spécifique, le dernier état du cycle de facturation et de la collection.  Par exemple, l’état peut être la collection en cours, collectées, ou de remboursement.                  |
| **Taille de base**  | Le nombre de cœurs virtuels présenté par la machine virtuelle que l’offre est en cours d’exécution.               |
| **Ville du client**  | Le nom de ville fourni par le client. Cela peut être différent de celui de la ville dans l’abonnement du client Azure.  |
| **Langue de Communication client**        | Langue dans laquelle le client préfère communiquer.                                  |
| **Nom de la société du client**                  | Le nom de société fourni par le client. Cela peut être différent de celui de la ville dans l’abonnement du client Azure.                                  |
| **Pays du client**                       | Le nom du pays fourni par le client.  Cela peut être différente de celle du pays dans l’abonnement du client Azure.                               |
| **Devise (CC) du client**                 | Devise privilégiée par le client pour la tarification et la facturation.                            |
| **E-mail client**                         | L’adresse de messagerie fournie par le client final.  Cela peut être différente de celle de l’adresse de messagerie dans l’abonnement du client Azure.                   |
| **Prénom du client**                              | Le prénom fourni par le client.  Cela peut être différent du nom fourni dans l’abonnement du client Azure.                    |
| **Nom de client**                               | Le nom fourni par le client.  Cela peut être différent du nom fourni dans l’abonnement du client Azure.                    |
| **ID du client**                               | Identificateur unique affecté à un client.  Un client peut avoir zéro ou plusieurs abonnements de la place de marché Azure.                    |
| **Type de paiement client**                            | Mode de paiement utilisé par le client.  Par exemple, cela peut être chèque électronique, une facture ou carte.                             |
| **Code Postal du client**                             | Le code postal fourni par le client.  Cela peut être différent de celui du code postal fourni dans l’abonnement du client Azure.                                                                 |
| **L’état du client**                                   | L’état (adresse) fourni par le client.  Cela peut être différent de celui le nom fourni dans l’abonnement du client Azure.                                                                 |
| **Date d’achat**                                    | La première date le client a acheté une offre publiée par vous.                 |
| **Date perdu**                                        | La dernière date le client a annulé la dernière de toutes les offres précédemment acheté.                                                         |
| **Estimation des frais d’étendue (CC)**                   | Estimation des frais d'extension relatifs à la quantité d’unités d’utilisation pour une référence SKU donnée (dans la devise du client). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.                            |
| **Estimation des frais d’étendue (PC)**                   | Estimé étendu de frais pour la quantité d’unités de l’utilisation pour une référence (SKU) donné, en fonction de conversion de change de devises sur l’utilisation de la date est calculé (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.                  |
| **Paiement estimé (PC)**                            | Le paiement estimé pour la quantité d’unités d’utilisation pour une référence (SKU) donné, en fonction de conversion de change à la date de que l’utilisation est calculée (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.                        |
| **Prix estimé (PC)**                             | Le prix estimé pour une unité de l’utilisation pour une référence (SKU) donné, en fonction de conversion de change de devises sur l’utilisation de la date est calculé (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.                                             |
| **Est le nouveau client**                                  | La valeur vous permettra de savoir si elle est un nouveau client consomme ou en achetant un ou plusieurs de vos place de marché offre pour la première fois (ou pas).  La valeur sera « Oui » si elle est le même mois pour « Acquérir Date ».  La valeur sera « Non » si le client a acheté un de vos offres avant le mois calendaire signalé.                                                       |
| **Est la version préliminaire de référence (SKU)**                                  | La valeur vous indiquera si vous avez étiqueté la référence (SKU) en tant que « preview ». La valeur sera être « Yes » si la référence (SKU) a été marquée en conséquence, les abonnements Azure uniquement autorisés par vous peuvent déployer et utiliser cette image.  La valeur sera « Non » si la référence (SKU) n’a pas été identifié comme « preview ».                                          |
| **Promotionnels pour refuser**                   | La valeur vous indiquera si le client proactive opté pour la réception des messages promotionnels provenant d’éditeurs. À ce stade, nous n'offrons pas cette possibilité aux clients. Par conséquent, nous avons indiqué « Non » sur le tableau. Une mise à jour interviendra lorsque cette fonctionnalité sera déployée.                                          |
| **Type de licence de place de marché**                         | Mode de facturation de l’offre de la Place de marché.                             |
| **Type de licence de place de marché : Facturée via Azure**   | Pour cette offre de la Place de marché, Microsoft est votre agent et facture les clients de votre part. (Carte de crédit avec paiement à l’utilisation ou facture d’entreprise)       |
| **Type de licence de place de marché : Apportez votre propre licence** | La machine virtuelle nécessite une clé de licence fournie par le client à déployer. Microsoft ne facture rien aux clients qui utilisent cette offre de la Place de marché. |
| **Type de licence de place de marché : Gratuit**                   | L’offre de la Place de marché est configurée pour permettre à tous les utilisateurs d'y accéder gratuitement. Microsoft ne facture rien aux clients qui utilisent cette offre de la Place de marché.    |
| **Type de licence de place de marché : Microsoft en tant que revendeur**  | Microsoft est votre revendeur pour cette offre de la Place de marché.                               |
| **Nom de la société d’abonnement de la place de marché**  | Le nom de société fourni par le client dans leur abonnement Azure.                           |
| **ID d’abonnement place de marché**            | L’identificateur unique associé à l’abonnement Azure du client utilisé pour acheter votre offre place de marché.  Il se trouvait auparavant le GUID d’abonnement Azure.  Dans certains cas, comme une commande SAAS, aucun abonnement n’est requis.           |
| **Frais de Microsoft (CC)**                               | Frais prélevés par Microsoft sur la transaction dans la devise du client.                              |
| **Nom de l’offre**                                | Le nom de l’offre de la place de marché.                                                             |
| **Type d’offre**                                | Le type d’offre de Microsoft Marketplace.  Par exemple, il peut être application managée, les licences de machine virtuelle ou conteneur.                           |
| **Date d’annulation de commande**                         | Date à laquelle la commande passée sur la Place de marché a été annulée.                                                       |
| **ID de commande**                                  | L’identificateur unique de la commande du client pour votre service de la place de marché.  Offres basées sur l’utilisation de machines virtuelles ne sont pas associés à une commande.                 |
| **Date de bon de commande**                       | Date de création de l’ordre de la place de marché.                                                        |
| **Quantité commandée**                       | La quantité de commandes pour la même référence SKU associée à un abonnement Azure.  Pour les commandes SAAS, il représente le nombre de sièges associé liés cette instance d’une commande.                                            |
| **État de la commande**                              | État d’une commande de la Place de marché au moment de la dernière actualisation des données.  Par exemple, l’ordre peut être active ou a été annulée.             |
| **État de la commande : Active**                           | Le client a acheté une commande et leur ordre n’a pas annulé.   |
| **État de la commande : Annulée**                        | Le client précédemment acheté un ordre et par la suite annulé leur ordre.        |
| **Quantité de paiement (PC)**                             | Le montant payé pour vous, dans votre devise de paiement préférée (PC).                                       |
| **Devise de paiement (PC)**                           | Devise utilisée pour vos paiements.                                                                |
| **Date de paiement**                                    | La date de la demande de paiement a été envoyée à partir de Microsoft vers votre institution financière choisie.                                                                         |
| **État de paiement**                                  | L’état de la paiement au moment de que la dernière actualisation de données.                                   |
| **État de paiement : En cours d’exécution**               | La transaction n’est pas prête pour le paiement. (Pour plus d’informations, consultez le statut de la Collection)                             |
| **État de paiement : Versé**                          | La transaction a été incluse dans le calcul d'un précédent paiement. Les valeurs positives sont payées, et les valeurs négatives sont déduites du montant total dû. |
| **État de paiement : Paiement à venir**                   | La transaction est prête pour le paiement et sera incluse dans le calcul du paiement disponible suivant.                      |
| **Prix (CC)**                                   | Prix d’une unité d’utilisation pour une référence SKU donnée (dans la devise du client).                                                                  |
| **E-mail de fournisseur**                                 | L’adresse de messagerie du fournisseur impliqué dans la relation entre Microsoft et le client final. Si le client est une entreprise via un revendeur, il sera le revendeur.  Si un fournisseur de solutions Cloud (CSP) est impliquée, il sera le CSP.                                                                 |
| **Nom du fournisseur**                                 | Le nom du fournisseur impliqué dans la relation entre le client de Microsoft et de fin. Si le client est une entreprise via un revendeur, il sera le revendeur.  Si un fournisseur de solutions Cloud (CSP) est impliquée, il sera le CSP.                                                                 |
| **URI de ressource**                                  | Le chemin d’accès où l’artefact de déploiement a été déployé.                                            |
| **Référence (SKU)**                                           | Nom de référence (SKU) tel que défini lors de la publication. Une offre peut avoir plusieurs références (SKU), mais une référence (SKU) peut uniquement être associé à une offre unique.              |
| **Type de facturation de référence (SKU)**                                 | La méthode de facturation de la référence SKU.                                                                                                             |
| **Type de facturation de référence (SKU) : Apportez votre propre licence**         | La machine virtuelle ou un service nécessite une clé de licence fournie par le client à déployer. Microsoft ne facture pas les clients pour leur utilisation de ces offres place de marché.   |
| **Type de facturation de référence (SKU) : Microsoft As Reseller**          | Microsoft est votre revendeur pour cette référence (SKU).   |
| **Type de facturation de référence (SKU) : Gratuit**                           | La référence (SKU) est configuré pour être libre de tous les utilisateurs. Microsoft ne facture pas les clients pour leur utilisation de cette référence SKU.                           |
| **Type de facturation de référence (SKU) : Paid**                           | Microsoft est votre agent pour cette référence SKU et facture aux clients de votre part. (Carte de crédit avec paiement à l’utilisation ou facture d’entreprise)                   |
| **Type de facturation de référence (SKU) : Version d’évaluation**                          | Le client bénéficie de la période d’essai gratuite. Sauf annulation ou suppression, l'offre d'essai sera convertie en offre payante.                                         |
| **Montant des taxes (CC)**                                  | Le montant des taxes appliqué à la facture du client dans la devise du client (CC).                                                               |
| **Date de transaction**                                 | Date de la transaction enregistrée dans votre rapport de paiement.                                                                           |
| **Type de transaction**                               | Type de la transaction.  Par exemple, cela peut être un coût, un remboursement ou un ajustement de paiement.                                                                                        |
| **Date de fin d’essai**                                 | La date de fin de la période d’essai de cette commande approche ou est dépassée.                                                                           |
| **Date d’utilisation**                                       | Date d'utilisation par le client.                                                                                                             |
| **Quantité d’utilisation**                                       | L’utilisation de signalés par les clients de la référence SKU. Pour les Images de machine virtuelle, les enregistrements d’utilisation représentent l’utilisation de la période signalée pour cette taille de machine virtuelle et de la référence (SKU).                                               |
| **Référence de l’utilisation**                                       | Identificateur correspondant à un ou plusieurs jours d’utilisation client pour une référence SKU associée à une entrée du rapport de paiement.                                               |
| **Type d'utilisation**                                       | Description de l’utilisation mesurée. (Utilisation normalisée ou l’utilisation brute)                                                              |
| **Type d’utilisation : Utilisation normalisée**                | Heures d’utilisation normalisées afin de prendre en compte le nombre de cœurs de machines virtuelles impliqués dans l’utilisation : [nombre de cœurs de machines virtuelles] x [heures d’utilisation brutes]. Les machines virtuelles désignées sous le nom de « SHAREDCORE » utilisent 1/6 (ou 0,1666) comme multiplicateur [nombre de cœurs de machines virtuelles].                                        |
| **Type d’utilisation : Utilisation brute**                        | Heures d’utilisation de votre offre de la Place de marché.  Il est présenté en termes d’heures de « calendar ».  Il est le nombre de machines virtuelles de temps d’exécution.                           |
| **L’utilisation des unités**                                   | Unité de mesure de l’utilisation mentionnée. Les machines virtuelles sont toujours mesurées à l'aide d’unités horaires.                               |
| **Taille de la machine virtuelle**                                       | Représente la taille du matériel de la machine virtuelle associée à l’offre Azure. Exemples : `Basic_A0`, `Standard_A11`, `Standard_D12`, et `Standard_G4`.   |
|  |  |


