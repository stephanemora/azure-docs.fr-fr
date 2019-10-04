---
title: Surveiller et suivre l’utilisation du service gratuit Azure
description: Découvrez comment vérifier l’utilisation du service gratuit dans le portail Microsoft Azure et le fichier CSV d’utilisation.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ae8b26f0032c6659da18b822f4f8568995eff82b
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709766"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Vérifier l’utilisation du service gratuit inclus avec votre compte gratuit Azure

Vous n’êtes pas facturé pour les services compris gratuitement dans un compte gratuit Azure, sauf si vous dépassez les limites de ces services. Pour rester dans les limites, vous pouvez utiliser le portail Microsoft Azure ou votre fichier d’utilisation afin de surveiller et de suivre l’utilisation du service gratuit.

## <a name="check-usage-in-the-azure-portal"></a>Vérifier l’utilisation dans le portail Microsoft Azure

1.  Connectez-vous au [Portail Azure](https://portal.azure.com).

2.  Dans la zone de navigation de gauche, sélectionnez **Tous les services**.

3.  Sélectionnez **Abonnements**.

4.  Sélectionnez l’abonnement que vous avez créé lors de votre inscription pour un compte gratuit.

    ![Capture d’écran qui montre tous les abonnements](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  La section de vue d’ensemble affiche les informations essentielles de votre abonnement. Par exemple, l’ID d’abonnement, le type d’offre et le nom de l’abonnement. Vous y trouverez également des informations sur l’expiration du crédit de votre compte gratuit.

    ![Capture d’écran qui montre des informations essentielles sur l’abonnement](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Faites défiler vers le bas jusqu’à trouver des informations sur les coûts actuels et prévus. Le coût comprend l’utilisation du service non compris dans votre compte gratuit, et l’utilisation dépassant les limites des services gratuits.

    ![Capture d’écran qui montre des informations sur les coûts d’abonnement](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  La dernière partie de la section de vue d’ensemble comporte un tableau répertoriant l’utilisation du service gratuit.

    ![Capture d’écran qui montre l’utilisation des services gratuits](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Le tableau comporte les colonnes suivantes :

* **Nom du compteur :** Identifie l’unité de mesure du compteur consommé. Pour en savoir plus sur le mappage entre service et compteur, consultez [Présentation du mappage entre les services gratuits et les compteurs](billing-understand-free-service-meter-mapping.md).
* **Utilisation/Limite :** utilisation et limite du mois en cours pour le compteur. Ces informations figurent aussi dans la barre d’état.
* **État :** état d’utilisation du compteur. En fonction de votre modèle d’utilisation, vous pouvez avoir l’un des états suivants :
  * **Non utilisé :** vous n’avez pas utilisé le compteur, ou l’utilisation du compteur n’a pas atteint le système de facturation.
  * **Dépassement le \<Date> :** vous avez dépassé la limite du compteur le \<Date>.
  * **Dépassement peu probable :** il est peu probable que vous dépassiez la limite du compteur.
  * **Dépassement le \<Date> :** il est probable que vous dépasserez la limite du compteur le \<Date>.

## <a name="check-usage-with-the-usage-file"></a>Vérifier l’utilisation avec le fichier d’utilisation

Votre fichier d’utilisation fournit des informations précises sur votre abonnement Azure. Vous pouvez télécharger votre fichier d’utilisation mensuelle et quotidienne à partir du Centre des comptes Azure. Pour découvrir comment télécharger le fichier d’utilisation et comprendre l’accès requis, consultez [Télécharger ou afficher votre facture Azure et vos données d’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md). Pour en savoir plus sur les colonnes du fichier d’utilisation, consultez [Comprendre les termes figurant sur le fichier détaillant les frais d’utilisation Microsoft Azure](billing-understand-your-usage.md).

Le fichier d’utilisation contient des informations sur l’utilisation des services gratuits et payants. La mention **Gratuit** est ajoutée à la fin du nom du compteur pour les services gratuits. Pour rechercher les compteurs des services gratuits, ouvrez le fichier dans Excel et filtrez la colonne **Catégorie du compteur** pour afficher les cellules qui contiennent le texte **- Gratuit** (utilisez Filtres textuels &rarr; filtre Contient).


![Capture d’écran qui montre l’utilisation des services gratuits](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- [Mettre à niveau votre abonnement](billing-upgrade-azure-subscription.md)
