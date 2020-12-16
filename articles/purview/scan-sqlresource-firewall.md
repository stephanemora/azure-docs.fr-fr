---
title: Analyse d’Azure SQL Database derrière un pare-feu
description: Découvrez comment analyser des ressources qui se trouvent derrière un pare-feu à l’aide du portail Azure Purview.
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550135"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Analyse d’Azure SQL Database derrière un pare-feu dans Azure Purview

Dans cet article, vous allez découvrir comment utiliser Azure Purview pour analyser une ressource qui se trouve derrière un pare-feu.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Votre propre [locataire Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.

## <a name="set-up-sql-storage-behind-a-firewall"></a>Configuration du stockage SQL derrière un pare-feu

La première étape consiste à ajouter le fichier MSI du catalogue à une base de données Azure SQL Database suivant [Inscription et analyse d’une base de données Azure SQL Database](register-scan-azure-sql-database.md).

## <a name="scan-sql-db-from-purview"></a>Analyse de SQL DB sur Purview

1. Accédez à la page d’accueil Purview.

1. Sélectionnez **Centre de gestion** dans le volet de navigation gauche.

1. Sélectionnez **Sources de données** sous **Sources et analyse**.

1. Sélectionnez **+ Créer** pour ajouter la source de données.

1. Sélectionnez **Azure SQL Database**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="Capture d’écran de la sélection de SQL Server":::

1. Donnez un nom à la nouvelle source de données, sélectionnez **À partir d’un abonnement Azure**, puis sélectionnez votre abonnement et le nom de votre serveur dans la liste déroulante.

   Cliquez sur **Terminer** pour terminer l’inscription. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="Capture d’écran de la sélection de Terminer":::

1. Sélectionnez **Configurer l’analyse** pour le stockage situé derrière le pare-feu et testez la connexion. La connexion indique qu’elle a réussi. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="Capture d’écran de la sélection de Configurer l’analyse":::

1. Configurez la fréquence d’analyse et sélectionnez **Continuer**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="Capture d’écran de la sélection de Lancer l’analyse SQL":::

1.  L’analyse s’effectue, et l’état est mis à jour dans la liste des sources de données.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="Capture d’écran du message Analyse effectuée":::
   
## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant configurer un ensemble de règles d’analyse [Créer un ensemble de règles d’analyse](create-a-scan-rule-set.md) pour regrouper les analyses.
