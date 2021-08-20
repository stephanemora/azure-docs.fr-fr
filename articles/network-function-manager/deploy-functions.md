---
title: 'Tutoriel : Déployer des fonctions réseau sur Azure Stack Edge'
titleSuffix: Azure Network Function Manager
description: Dans ce tutoriel, apprenez à déployer une fonction réseau en tant qu’application gérée.
author: cherylmc
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: cherylmc
ms.openlocfilehash: af82a3b51da76e181c2e5856458d553051b0c409
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113128999"
---
# <a name="tutorial-deploy-network-functions-on-azure-stack-edge-preview"></a>Tutoriel : Déployer des fonctions réseau sur Azure Stack Edge (préversion)

Dans ce tutoriel, vous allez apprendre à déployer une fonction réseau sur Azure Stack Edge à l’aide de Place de marché Azure. Network Function Manager active l’expérience Applications managées Azure pour simplifier le déploiement sur Azure Stack Edge.

> [!div class="checklist"]
> * Vérifier les [prérequis](overview.md#prereq)
> * Créer une fonction réseau
> * Vérifier les détails de la fonction réseau

## <a name="prerequisites"></a>Prérequis

* Vous avez créé une ressource d’appareil pour Network Function Manager. Si vous n’avez pas terminé ces étapes, consultez [Comment créer une ressource d’appareil](create-device.md).
* Sous l’onglet **Vue d’ensemble** de l’appareil, vérifiez que les valeurs suivantes sont présentes :
  * État d’approvisionnement = Réussi
  * État de l’appareil = Inscrit

## <a name="create-a-network-function"></a><a name="create"></a>Créer une fonction réseau

1. Connectez-vous au [portail Azure en préversion](https://aka.ms/AzureNetworkFunctionManager).
1. Accédez à la ressource d’**appareil** dans laquelle vous souhaitez déployer une fonction réseau, puis sélectionnez **+Créer une fonction réseau**.

   :::image type="content" source="./media/deploy-functions/create-network-function.png" alt-text="Capture d’écran de +Créer une fonction réseau." lightbox="./media/deploy-functions/create-network-function.png":::
1. Dans la liste déroulante, choisissez la **référence SKU du fournisseur** que vous souhaitez utiliser, puis cliquez sur **Créer**.

   :::image type="content" source="./media/deploy-functions/select.png" alt-text="Capture d’écran de la référence SKU du fournisseur." lightbox="./media/deploy-functions/select.png":::
1. En fonction de la référence SKU sélectionnée, vous êtes redirigé vers le portail Place de marché pour l’application gérée par fonction réseau.
 
   Chaque partenaire de fonction réseau aura des exigences différentes pour déployer sa fonction réseau sur Azure Stack Edge. En outre, certaines fonctions réseau, telles que le noyau de paquets mobiles et la périphérie SD-WAN, peuvent vous obliger à configurer des ports de gestion, de réseau local et de réseau étendu et à allouer des adresses IP sur ces ports avant de déployer les fonctions réseau. Vérifiez auprès de votre partenaire les propriétés requises et la configuration réseau de l’appareil Azure Stack Edge.
   
   > [!IMPORTANT]
   > Pour toutes les fonctions réseau qui prennent en charge l’adresse IP statique pour les interfaces réseau virtuelles de gestion, de réseau local ou de réseau étendu, veillez à ne pas utiliser les quatre premières adresses IP de la plage d’adresses IP attribuée pour le port spécifique. Ces adresses IP sont des adresses IP réservées pour le service Azure Stack Edge.
   >

1. Suivez les étapes du portail du marketplace pour déployer l’application gérée par le partenaire. Une fois l’application gérée correctement approvisionnée, vous pouvez accéder au groupe de ressources pour afficher l’application gérée. Pour vérifier si l’état de configuration du fournisseur de la ressource de fonction réseau est approvisionné, accédez au groupe de ressources géré. Cela confirme que le déploiement de la fonction réseau est réussi et que les configurations supplémentaires requises peuvent être approvisionnées via le portail de gestion du partenaire de fonction réseau. Vérifiez auprès du partenaire de fonction réseau l’expérience de gestion qui suit le déploiement initial à l’aide de Network Function Manager.

### <a name="example"></a>Exemple

1. Recherchez l’offre **Fusion Core – noyau de paquets 5G** dans Place de marché et cliquez sur **Créer** pour commencer à créer votre fonction réseau.

   :::image type="content" source="./media/deploy-functions/metaswitch.png" alt-text="Capture d’écran de la page de Metaswitch." lightbox="./media/deploy-functions/metaswitch.png":::
1. Configurez les paramètres Informations de base.

   :::image type="content" source="./media/deploy-functions/basics-blade.png" alt-text="Capture d’écran des paramètres Informations de base." lightbox="./media/deploy-functions/basics-blade.png":::
1. Appliquez une identité managée. Pour plus d’informations, consultez [Identité managée](overview.md#managed-identity).

   :::image type="content" source="./media/deploy-functions/managed-identity.png" alt-text="Capture d’écran d’Identité managée." lightbox="./media/deploy-functions/managed-identity.png":::
1. Entrez les informations d’adresse IP pour les interfaces de gestion, de réseau local et de réseau étendu de la machine virtuelle Fusion Core.

   :::image type="content" source="./media/deploy-functions/ip-settings.png" alt-text="Capture d’écran des interfaces de gestion, de réseau local et de réseau étendu de la machine virtuelle Fusion Core." lightbox="./media/deploy-functions/ip-settings.png":::
1. Entrez les paramètres facultatifs pour la configuration de la 5G et des UE de test.

   :::image type="content" source="./media/deploy-functions/5g-settings-blade.png" alt-text="Capture d’écran de 5G." lightbox="./media/deploy-functions/5g-settings-blade.png":::

   :::image type="content" source="./media/deploy-functions/test-blade.png" alt-text="Capture d’écran de la configuration des UE de test." lightbox="./media/deploy-functions/test-blade.png":::
1. Une fois la validation réussie, acceptez les conditions générales. Cliquez ensuite sur **Créer** pour commencer à créer l’application gérée Fusion Core dans le groupe de ressources Client et la ressource de fonction réseau dans le groupe de ressources géré. Vous devez cocher la case **Afficher les types masqués** dans l’affichage des groupes de ressources gérés pour afficher la ressource de fonction réseau.

   :::image type="content" source="./media/deploy-functions/managed-app-customer.png" alt-text="Capture d’écran de l’application gérée dans le groupe de ressources Client." lightbox="./media/deploy-functions/managed-app-customer.png":::

   :::image type="content" source="./media/deploy-functions/managed-resource-group.png" alt-text="Capture d’écran de la fonction réseau dans le groupe de ressources géré." lightbox="./media/deploy-functions/managed-resource-group.png":::

## <a name="next-steps"></a>Étapes suivantes

Accédez au portail du fournisseur pour terminer la configuration de la fonction réseau.
