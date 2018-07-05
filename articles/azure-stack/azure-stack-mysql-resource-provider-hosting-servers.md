---
title: Serveurs d’hébergement MySQL sur Azure Stack | Microsoft Docs
description: Comment ajouter des instances MySQL pour l’approvisionnement via le fournisseur de ressources de l’adaptateur MySQL
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 5522eb1b8b0398aeb6f1b0dd8578b906880b4e89
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939651"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Ajouter des serveurs d’hébergement pour le fournisseur de ressources MySQL

Vous pouvez héberger une instance MySQL sur une machine virtuelle dans [Azure Stack](azure-stack-poc.md), ou sur une machine virtuelle à l’extérieur de votre environnement Azure Stack, à condition que le fournisseur de ressources MySQL puisse se connecter à l’instance.

## <a name="connect-to-a-mysql-hosting-server"></a>Se connecter à un serveur d’hébergement MySQL

Assurez-vous de détenir les informations d’identification d’un compte disposant de privilèges d’administrateur système. Pour ajouter un serveur d’hébergement, procédez comme suit :

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur de service.
2. Sélectionnez **Plus de services**.
3. Sélectionnez **RESSOURCES ADMINISTRATIVES**  >  **Serveurs d’hébergement MySQL**  >  **+Ajouter**. Cette opération ouvre la boîte de dialogue **Ajouter un serveur d’hébergement MySQL**, illustrée dans la capture d’écran suivante.

   ![Configurer un serveur d’hébergement](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Spécifiez les détails de connexion de votre instance de MySQL Server.

   * Pour **Nom du serveur d’hébergement MySQL**, fournissez le nom de domaine complet (FQDN) ou une adresse IPv4 valide. N’utilisez pas le nom court de la machine virtuelle.
   * Une instance MySQL par défaut n’est pas fournie, donc vous devez spécifier la **taille du serveur d’hébergement en Go**. Entrez une taille qui est proche de la capacité du serveur de base de données.
   * Conservez la valeur par défaut pour **Abonnement**.
   * Pour **Groupe de ressources**, créez un groupe ou utilisez un groupe existant.

   > [!NOTE]
   > Si l’instance MySQL est accessible par le locataire et l’administrateur Azure Resource Manager, vous pouvez la placer sous le contrôle du fournisseur de ressources. Mais, l’instance MySQL **doit** être allouée exclusivement au fournisseur de ressources.

5. Sélectionnez **Références (SKU)** pour ouvrir la boîte de dialogue **Créer une référence (SKU)**.

   ![Créer une référence (SKU) MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   Le **nom** de la référence SKU doit refléter les propriétés de la référence SKU pour que les utilisateurs puissent déployer leurs bases de données sur la référence SKU appropriée.

   >[!IMPORTANT]
   >Les caractères spéciaux, y compris les espaces et les points, ne sont pas pris en charge dans le **Nom** ou le **Niveau** lors de la création d’une référence SKU pour le fournisseur de ressources MySQL.

6. Sélectionnez **OK** pour créer la référence SKU.
7. Sous **Ajouter un serveur d’hébergement MySQL**, sélectionnez **Créer**.

À mesure que vous ajoutez des serveurs, vous devez les affecter à une référence (SKU) nouvelle ou existante pour différencier les offres de service. Par exemple, vous pouvez avoir une instance d’entreprise MySQL qui fournit des sauvegardes de base de données et automatiques améliorées. Vous pouvez réserver ce serveur hautes performances pour les différents services de votre organisation.

## <a name="increase-backend-database-capacity"></a>Augmenter la capacité de base de données principale

Vous pouvez augmenter la capacité de base de données principale en déployant plus de serveurs MySQL dans le portail Azure Stack. Ajoutez ces serveurs à une référence (SKU) nouvelle ou existante. Si vous ajoutez un serveur à une référence (SKU) existante, assurez-vous que les caractéristiques du serveur sont les mêmes que pour les autres serveurs dans la référence (SKU).

## <a name="make-mysql-database-servers-available-to-your-users"></a>Mise à disposition des serveurs de base de données MySQL pour vos utilisateurs

Créez des plans et des offres pour mettre les serveurs de base de données MySQL à disposition des utilisateurs. Ajoutez le service Microsoft.MySqlAdapter au plan, puis ajoutez le Quota par défaut ou créez un nouveau Quota.

![Créer des plans et des offres pour les bases de données](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="next-steps"></a>Étapes suivantes

[Création d’une base de données MySQL](azure-stack-mysql-resource-provider-databases.md)