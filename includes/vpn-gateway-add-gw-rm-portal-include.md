---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72e61a36b58c0bc666f3e19b71fb1abe842208f5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176908"
---
1. Connectez-vous au portail Azure et sélectionnez **Créer une ressource**. La page **Nouveau** s’ouvre.

2. Dans le champ **Rechercher dans la Place de marché**, entrez *passerelle de réseau virtuel* et sélectionnez **Passerelle de réseau virtuel** dans la liste de recherche. 

3. Dans la page **Passerelle de réseau virtuel**, sélectionnez **Créer** pour ouvrir la page **Créer une passerelle de réseau virtuel**.

   ![Champs de la page Créer une passerelle de réseau virtuel](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Champs de la page Créer une passerelle de réseau virtuel")

4. Sur la page **Créer une passerelle de réseau virtuel**, renseignez les valeurs pour votre passerelle de réseau virtuel :

   - **Nom** : Entrez un nom pour l’objet passerelle que vous créez. Ce nom est différent de celui du sous-réseau de la passerelle. 

   - **Type de passerelle** : Sélectionnez **VPN** pour les passerelles VPN. 

   - **Type de VPN** : sélectionnez le type de VPN spécifié pour votre configuration. La plupart des configurations requièrent un type de **VPN basé sur un itinéraire**.

   - **Référence** : sélectionnez la référence de passerelle dans la liste déroulante. Les références répertoriées dans la liste déroulante dépendent du type de VPN que vous sélectionnez. Pour plus d’informations sur les références de passerelle, consultez [Références (SKU) de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      Sélectionnez uniquement **Activer le mode actif / actif** si vous créez une configuration de passerelle active/active. Sinon, ne sélectionnez pas ce paramètre.
  
   - **Emplacement** : Vous devrez peut-être faire défiler pour afficher l’**emplacement**. Indiquez dans le champ **Emplacement** l’emplacement où se trouve votre réseau virtuel. Par exemple, **USA Ouest**. Si l’emplacement ne pointe pas vers la région où se trouve votre réseau virtuel, il n’apparaîtra pas dans la liste déroulante quand vous sélectionnerez un réseau virtuel.

   - **Réseau virtuel** : Choisissez le réseau virtuel auquel vous souhaitez ajouter cette passerelle. Sélectionnez **Réseau virtuel** pour ouvrir la page **Choisir un réseau virtuel**, puis choisissez le réseau virtuel. Si vous ne voyez pas votre réseau virtuel, assurez-vous que le champ **Emplacement** désigne la région dans laquelle se trouve votre réseau virtuel.

   - **Plage d’adresses de sous-réseau de la passerelle** : ce paramètre n’apparaît que si vous n’avez pas encore créé de sous-réseau de passerelle pour votre réseau virtuel. Si vous avez déjà créé un sous-réseau de passerelle valide, ce paramètre n’est pas affiché.

   - **Adresse IP publique** : ce paramètre spécifie l’objet d’adresse IP publique qui est associé à la passerelle VPN. L’adresse IP publique est attribuée dynamiquement à cet objet pendant la création de la passerelle VPN. Actuellement, la passerelle VPN prend uniquement en charge l’allocation d’adresses IP publiques *dynamiques*. Toutefois, cette allocation dynamique ne signifie pas que l’adresse IP change après son affectation à votre passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN.
    
      - Laissez l’option **Créer** sélectionnée.

      - Dans la zone de texte, saisissez le nom de votre adresse IP publique.

   - **Configurer un ASN BGP** : Laissez cette option désélectionnée, sauf si votre configuration exige spécifiquement ce paramètre. Si vous avez besoin de ce paramètre, la valeur par défaut ASN est *65515*, et vous pouvez la modifier.
     
5. Vérifiez les paramètres et sélectionnez **Créer** pour commencer à créer la passerelle VPN. Les paramètres sont validés et la vignette **Déploiement d’une passerelle de réseau virtuel** s’affiche sur le tableau de bord. La création d’une passerelle peut prendre jusqu’à 45 minutes Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.

6. Une fois la passerelle créée, vérifiez l’adresse IP qui lui a été affectée en affichant le réseau virtuel dans le portail. Cette dernière apparaît sous la forme d’un appareil connecté. Vous pouvez sélectionner l’appareil connecté (votre passerelle de réseau virtuel) pour afficher davantage d’informations.