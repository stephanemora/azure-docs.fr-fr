---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 12c28969ed00a1370b90e06e9d57cd666c50ea29
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522776"
---
1. Dans le menu du [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**. 

   ![Créer une ressource dans le portail Azure](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. Dans le champ **Rechercher dans le marketplace**, tapez « passerelle de réseau virtuel ». Recherchez **passerelle de réseau virtuel** dans la zone de recherche et cliquez sur l’entrée. Dans la page **Passerelle de réseau virtuel**, cliquez sur **Créer**. La page **Créer une passerelle de réseau virtuel** s’ouvre.

   ![Champs de la page Créer une passerelle de réseau virtuel](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw.png "Champs de la page Créer une passerelle de réseau virtuel")

   ![Champs de la page Créer une passerelle de réseau virtuel](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw2.png "Champs de la page Créer une passerelle de réseau virtuel")
3. Sur la page **Créer une passerelle de réseau virtuel**, renseignez les valeurs pour votre passerelle de réseau virtuel.

   **Détails du projet**

   - **Abonnement**: Sélectionnez l’abonnement à utiliser dans la liste déroulante.
   - **Groupe de ressources** : Ce paramètre est renseigné automatiquement quand vous sélectionnez votre réseau virtuel sur cette page.

   **Détails de l’instance**

   - **Nom** : Nommez votre passerelle. Le nommage de votre passerelle n’est pas identique au nommage d’un sous-réseau de passerelle. Il s’agit du nom de l’objet de passerelle que vous créez.
   - **Région** : Sélectionnez la région où vous voulez créer cette ressource. La région de la passerelle doit être la même que celle du réseau virtuel.
   - **Type de passerelle** : Sélectionnez **VPN**. Les passerelles VPN utilisent le type de passerelle de réseau virtuel **VPN**. 
   - **Type de VPN** : sélectionnez le type de VPN spécifié pour votre configuration. La plupart des configurations requièrent un type de VPN basé sur un itinéraire.
   - **SKU** : sélectionnez la référence de passerelle dans la liste déroulante. Les références répertoriées dans la liste déroulante dépendent du type de VPN que vous sélectionnez. Pour plus d’informations sur les références de passerelle, consultez [Références (SKU) de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      **Réseau virtuel** : Choisissez le réseau virtuel auquel vous souhaitez ajouter cette passerelle.

      **Plage d’adresses de sous-réseau de la passerelle** : Ce champ apparaît uniquement si votre réseau virtuel n’a pas de sous-réseau de passerelle. Dans la mesure du possible, utilisez la plage /27 ou au-delà (/26, /25, etc.). Nous vous déconseillons de créer une plage inférieure à /28. Si vous disposez déjà d’un sous-réseau de passerelle, vous pouvez en voir les détails en accédant à votre réseau virtuel. Cliquez sur **Sous-réseaux** pour afficher la plage. Si vous souhaitez modifier la plage, vous pouvez supprimer et recréer le sous-réseau de passerelle.

   **Adresse IP publique** : Ce paramètre spécifie l’objet d’adresse IP publique associé à la passerelle VPN. L’adresse IP publique est attribuée dynamiquement à cet objet pendant la création de la passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN.

     - **Adresse IP publique** : Laissez l’option **Créer** sélectionnée.
     - **Nom de l’adresse IP publique** : Dans la zone de texte, tapez un nom pour votre instance d’adresse IP publique.
     - **Attribution** : La passerelle VPN prend en charge seulement le mode dynamique.

   **Mode actif/actif** : Sélectionnez uniquement **Activer le mode actif / actif** si vous créez une configuration de passerelle active/active. Sinon, ne sélectionnez pas ce paramètre.

   Laissez l’option **Configurer un ASN BGP** désélectionnée, sauf si votre configuration exige spécifiquement ce paramètre. Si vous avez besoin de ce paramètre, la valeur par défaut ASN est 65515, bien que vous puissiez la modifier.

4. Cliquez sur **Vérifier + créer** pour exécuter la validation. Une fois la validation réussie, cliquez sur **Créer** pour déployer la passerelle VPN. La création et le déploiement complets d’une passerelle peuvent prendre jusqu’à 45 minutes. Vous pouvez voir l’état du déploiement dans la page Vue d’ensemble pour votre passerelle.

Une fois la passerelle créée, examinez le réseau virtuel dans le portail pour obtenir l’adresse IP affectée à la passerelle. Cette dernière apparaît sous la forme d’un appareil connecté.
