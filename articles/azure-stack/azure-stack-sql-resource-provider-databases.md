---
title: Utilisation des bases de données fournies par le fournisseur de ressources SQL Adapter sur Azure Stack | Microsoft Docs
description: Comment créer et gérer des bases de données SQL approvisionnées avec le fournisseur de ressources de l’adaptateur SQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b9f92b4d85e17bc848d82be413df1d0dad7c8548
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294936"
---
# <a name="create-sql-databases"></a>Créer des bases de données SQL
Les bases de données libre-service sont fournies par le biais du portail de l’utilisateur. Un utilisateur Azure Stack a besoin d’un abonnement disposant d’une offre contenant le service de base de données SQL.

1. Connectez-vous au portail [Azure Stack](azure-stack-poc.md) (les administrateurs de service peuvent également utiliser le portail d’administration).

2. Cliquez sur **+ Nouveau** &gt; **Données + stockage** &gt; **Base de données SQL Server** &gt; **Ajouter**.

3. Remplissez le formulaire avec les détails sur la base de données, notamment le **Nom de la base de données** et une **Taille maximale**, puis changez les autres paramètres si nécessaire. Vous êtes invité à choisir une référence (SKU) pour votre base de données. Lors de leur ajout, les serveurs d’hébergement se voient affectés une référence SKU. Les bases de données sont créées dans ce pool de serveurs d’hébergement qui constituent la référence SKU.

  ![Nouvelle base de données](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > La taille de la base de données doit être d’au moins 64 Mo. Il est possible de l’augmenter à l’aide de paramètres.

4. Renseignez les paramètres de connexion : **Connexion à la base de données** et **Mot de passe**. Ces paramètres sont des informations d’identification d’authentification SQL qui sont créées pour votre accès à cette base de données uniquement. Le nom d’utilisateur de connexion doit être globalement unique. Créez un paramètre de connexion ou sélectionnez-en un qui existe déjà. Vous pouvez réutiliser les paramètres de connexion d’autres bases de données utilisant la même référence (SKU).

    ![Créer une connexion de base de données](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Envoyez le formulaire, puis attendez que le déploiement se termine.

    Dans le panneau qui s’affiche, notez le champ « Chaîne de connexion ». Vous pouvez utiliser cette chaîne dans toute application qui nécessite un accès à SQL Server (par exemple, une application web) dans Azure Stack.

    ![Récupérer la chaîne de connexion](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-alwayson-databases"></a>Supprimer des bases de données SQL AlwaysOn
Lorsqu’une base de données SQL AlwaysOn est supprimée du fournisseur de ressources, elle est bien supprimée du groupe de disponibilité principal et AlwaysOn. Toutefois, le groupe de disponibilité SQL est conçu pour placer la base de données en état de restauration dans chaque réplica sans supprimer la base de données, sauf sous l’impulsion d’un déclencheur. Si une base de données n’est pas supprimée, les réplicas secondaires passent à l’état Sans synchronisation. La fonction de réajout d’une nouvelle base de données au groupe de disponibilité avec le même via le fournisseur de ressources fonctionne toujours.

## <a name="verify-sql-alwayson-databases"></a>Vérifier les bases de données SQL AlwaysOn
Les bases de données AlwaysOn doivent s’afficher comme étant synchronisées et disponibles sur toutes les instances et dans le groupe de disponibilité. Après le basculement, la base de données doit se connecter en toute transparence. Vous pouvez utiliser SQL Server Management Studio pour vérifier qu’une base de données est en cours de synchronisation :

![Vérifier AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)


## <a name="next-steps"></a>Étapes suivantes

[Tenir à jour le fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-maintain.md)
