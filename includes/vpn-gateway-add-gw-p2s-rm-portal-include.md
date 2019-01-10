---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2d84a905cba503119f1b6e0f0a1a7cbbf91b3a1f
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444120"
---
1. Dans le portail, sur le côté gauche, cliquez sur **+Créer une ressource**, puis entrez « passerelle de réseau virtuel » dans la recherche. Recherchez **passerelle de réseau virtuel** dans la zone de recherche et cliquez sur l’entrée. Sur la page **Passerelle de réseau virtuel**, cliquez sur **Créer** en bas de la page. La page **Créer une passerelle de réseau virtuel** s’ouvre.
2. Sur la page **Créer une passerelle de réseau virtuel**, renseignez les valeurs pour votre passerelle de réseau virtuel.

   ![Champs de la page Créer une passerelle de réseau virtuel](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Champs de la page Créer une passerelle de réseau virtuel")
3. Dans la page **Créer une passerelle réseau virtuel**, renseignez les valeurs pour votre passerelle de réseau virtuel.

   - **Nom** : Nommez votre passerelle. Cela ne revient pas au même que de nommer un sous-réseau de passerelle. Il s’agit du nom de l’objet de passerelle que vous créez.
   - **Type de passerelle** : Sélectionnez **VPN**. Les passerelles VPN utilisent le type de passerelle de réseau virtuel **VPN**. 
   - **Type de VPN** : sélectionnez le type de VPN spécifié pour votre configuration. La plupart des configurations requièrent un type de VPN basé sur un itinéraire.
   - **SKU** : sélectionnez la référence de passerelle dans la liste déroulante. Les références répertoriées dans la liste déroulante dépendent du type de VPN que vous sélectionnez. Pour plus d’informations sur les références de passerelle, consultez [Références (SKU) de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

     Sélectionnez uniquement **Activer le mode actif / actif** si vous créez une configuration de passerelle active/active. Sinon, ne sélectionnez pas ce paramètre.
   - **Emplacement** : Vous devrez peut-être faire défiler l’affichage pour voir l’emplacement. Renseignez le champ **Emplacement** de manière à ce qu’il pointe vers l’emplacement où se trouve votre réseau virtuel. Par exemple, USA Ouest. Si l’emplacement ne pointe pas vers la région où se trouve votre réseau virtuel, quand vous sélectionnerez un réseau virtuel à l’étape suivante, il n’apparaîtra pas dans la liste déroulante.
   - **Réseau virtuel** : Choisissez le réseau virtuel auquel vous souhaitez ajouter cette passerelle. Cliquez sur **Réseau virtuel** pour ouvrir la page Choisir un réseau virtuel. Sélectionnez le réseau virtuel. Si vous ne voyez pas votre réseau virtuel, assurez-vous que le champ Emplacement pointe sur la région dans laquelle se trouve votre réseau virtuel.
   - **Plage d’adresses de sous-réseau de la passerelle** : Vous voyez ce paramètre uniquement si vous n’avez pas créé de sous-réseau de passerelle pour votre réseau virtuel. Si vous avez déjà créé un sous-réseau de passerelle valide, ce paramètre n’est pas affiché.
   - **Adresse IP publique** : Ce paramètre spécifie l’objet d’adresse IP publique associé à la passerelle VPN. L’adresse IP publique est attribuée dynamiquement à cet objet pendant la création de la passerelle VPN. Actuellement, la passerelle VPN prend uniquement en charge l’allocation d’adresses IP publiques *dynamiques*. Toutefois, cela ne signifie pas que l’adresse IP change après son affectation à votre passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN.

     - Laissez l’option **Créer** sélectionnée.
     - Dans la zone de texte, donnez un **nom** à votre adresse IP publique.

4. Laissez l’option **Configurer un ASN BGP** désélectionnée, sauf si votre configuration exige spécifiquement ce paramètre. Si vous avez besoin de ce paramètre, la valeur par défaut ASN est 65515, bien que vous puissiez la modifier.
5. Vérifiez les paramètres. Si vous souhaitez que votre passerelle apparaisse sur le tableau de bord, vous pouvez sélectionner **Épingler au tableau de bord** en bas de la page.
6. Cliquez sur **Créer** pour créer la passerelle VPN. Les paramètres sont validés et la vignette Déploiement d’une passerelle de réseau virtuel s’affiche sur le tableau de bord. La création d’une passerelle peut prendre jusqu’à 45 minutes Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.

Une fois la passerelle créée, examinez le réseau virtuel dans le portail pour obtenir l’adresse IP affectée à la passerelle. Cette dernière apparaît sous la forme d’un appareil connecté. Vous pouvez cliquer sur l’appareil connecté (votre passerelle de réseau virtuel) pour afficher davantage d’informations.