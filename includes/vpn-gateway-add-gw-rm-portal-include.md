---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3c9dbca6a76bf0b10a83087fc31d9fa41c6bd03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78331247"
---
1. Dans le menu du [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**. 

   ![Créer une ressource dans le portail Azure](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. Dans le champ **Rechercher dans le marketplace**, tapez « passerelle de réseau virtuel ». Recherchez **passerelle de réseau virtuel** dans les résultats de la recherche et sélectionnez l’entrée. Dans la page **Passerelle de réseau virtuel**, sélectionnez **Créer**. La page **Créer une passerelle de réseau virtuel** s’ouvre.
3. Dans l’onglet **Informations de base**, renseignez les valeurs de la passerelle de réseau virtuel.

   ![Champs de la page Créer une passerelle de réseau virtuel](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Champs de la page Créer une passerelle de réseau virtuel")

   ![Champs de la page Créer une passerelle de réseau virtuel](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Champs de la page Créer une passerelle de réseau virtuel")

   **Détails du projet**

   - **Abonnement**: Sélectionnez l’abonnement à utiliser dans la liste déroulante.
   - **Groupe de ressources** : Ce paramètre est renseigné automatiquement quand vous sélectionnez votre réseau virtuel sur cette page.

   **Détails de l’instance**

   - **Name** : Nommez votre passerelle. Le nommage de votre passerelle n’est pas identique au nommage d’un sous-réseau de passerelle. Il s’agit du nom de l’objet de passerelle que vous créez.
   - **Région** : Sélectionnez la région où vous voulez créer cette ressource. La région de la passerelle doit être la même que celle du réseau virtuel.
   - **Type de passerelle** : Sélectionnez **VPN**. Les passerelles VPN utilisent le type de passerelle de réseau virtuel **VPN**.
   - **Type de VPN** : sélectionnez le type de VPN spécifié pour votre configuration. La plupart des configurations requièrent un type de VPN basé sur un itinéraire.
   - **SKU** : Sélectionnez la référence SKU de la passerelle dans la liste déroulante. Les références répertoriées dans la liste déroulante dépendent du type de VPN que vous sélectionnez. Pour plus d’informations sur les références de passerelle, consultez [Références (SKU) de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   - **Génération** : pour plus d’informations sur la génération de passerelles VPN, consultez [SKU de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   - **Réseau virtuel** : sélectionnez le réseau virtuel auquel vous souhaitez ajouter cette passerelle dans la liste déroulante.
   - **Plage d’adresses de sous-réseau de la passerelle** : Ce champ apparaît uniquement si votre réseau virtuel n’a pas de sous-réseau de passerelle. Dans la mesure du possible, utilisez la plage /27 ou au-delà (/26, /25, etc.). Nous vous déconseillons de créer une plage inférieure à /28. Si vous disposez déjà d’un sous-réseau de passerelle, vous pouvez en voir les détails en accédant à votre réseau virtuel. Cliquez sur **Sous-réseaux** pour afficher la plage. Si vous souhaitez modifier la plage, vous pouvez supprimer et recréer le sous-réseau de passerelle.

   **Adresse IP publique** : Ce paramètre spécifie l’objet d’adresse IP publique associé à la passerelle VPN. L’adresse IP publique est attribuée dynamiquement à cet objet pendant la création de la passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN.

     - **Adresse IP publique** : Laissez l’option **Créer** sélectionnée.
     - **Nom de l’adresse IP publique** : Dans la zone de texte, tapez un nom pour votre instance d’adresse IP publique.
     - **Attribution** : La passerelle VPN prend en charge seulement le mode dynamique.

   **Mode actif/actif** : Sélectionnez uniquement **Activer le mode actif / actif** si vous créez une configuration de passerelle active/active. Sinon, ne sélectionnez pas ce paramètre.

   Laissez l’option **Configurer un ASN BGP** désélectionnée, sauf si votre configuration exige spécifiquement ce paramètre. Si vous avez besoin de ce paramètre, la valeur par défaut ASN est 65515, bien que vous puissiez la modifier.
4. Sélectionnez **Vérifier + créer** pour exécuter la validation. Une fois la validation réussie, sélectionnez **Créer** pour déployer la passerelle VPN. La création et le déploiement complets d’une passerelle peuvent prendre jusqu’à 45 minutes. Vous pouvez voir l’état du déploiement dans la page Vue d’ensemble pour votre passerelle.

Une fois la passerelle créée, examinez le réseau virtuel dans le portail pour obtenir l’adresse IP affectée à la passerelle. Cette dernière apparaît sous la forme d’un appareil connecté.
