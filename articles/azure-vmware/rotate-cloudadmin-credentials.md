---
title: Effectuer la rotation des informations d’identification cloudadmin pour Azure VMware Solution
description: Découvrez comment effectuer la rotation des informations d’identification de vCenter Server et de NSX-T Manager pour votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 305447f8eac40a08564b9b57d82709f223f6086d
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112021498"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>Effectuer la rotation des informations d’identification cloudadmin pour Azure VMware Solution

Dans cet article, vous allez effectuer la rotation des informations d’identification cloudadmin (informations d’identification vCenter et NSX-T) pour votre cloud privé Azure VMware Solution.  Bien que les mots de passe de ces comptes n’expirent pas, vous pouvez en générer de nouveaux. Après la génération de nouveaux mots de passe, vous devez mettre à jour le connecteur VMware HCX avec les dernières informations d’identification appliquées.

Vous pouvez également regarder une vidéo sur la façon de [réinitialiser le mot de passe d’administrateur vCenter CloudAdminet et NSX-T](https://youtu.be/cK1qY3knj88). 

## <a name="prerequisites"></a>Prérequis

Si vous utilisez vos informations d’identification cloudadmin pour des services connectés tels que HCX, vRealize Orchestrator, vRealize Operations Manager ou VMware Horizon, vos connexions cessent de fonctionner une fois que vous avez mis à jour votre mot de passe.  Arrêtez ces services avant d’effectuer la rotation du mot de passe. Sinon, vous constaterez des verrous temporaires sur vos comptes d’administrateur NSX-T et CloudAdmin vCenter, car ces services appellent en permanence à l’aide de vos anciennes informations d’identification.  Pour plus d’informations sur la configuration de comptes distincts pour les services connectés, consultez la page sur les [concepts d’accès et d’identité](./concepts-identity.md).

## <a name="reset-your-azure-vmware-solution-cloudadmin-credentials"></a>Réinitialiser les informations d’identification cloudadmin Azure VMware Solution

Dans cette étape, vous allez effectuer la rotation des informations d’identification cloudadmin pour vos composants Azure VMware Solution. 

>[!NOTE]
>N’oubliez pas de remplacer **{SubscriptionID}** , **{ResourceGroup}** et **{PrivateCloudName}** par les informations de votre cloud privé.

1. À partir du portail Azure, ouvrez une session Azure Cloud Shell.

2. Mettez à jour votre mot de passe vCenter CloudAdmin.  

   ```azurecli-interactive
   az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```
          
3. Mettez à jour votre mot de passe d’administrateur NSX-T. 

   ```azurecli-interactive
   az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```

## <a name="update-hcx-connector-with-the-latest-cloudadmin-credentials"></a>Mettre à jour le connecteur HCX avec les dernières informations d’identification cloudadmin

Dans cette étape, vous allez mettre à jour le connecteur HCX avec informations d’identification mises à jour.

1. Accédez au connecteur HCX local à l’adresse à l’adresse https://{ip of the HCX connector appliance}:443 et connectez-vous à l’aide des nouvelles informations d’identification.

   Veillez à utiliser le port 443. 

2. Dans le tableau de bord VMware HCX, sélectionnez **Site Pairing (Jumelage de sites)** .
    
   :::image type="content" source="media/rotate-cloudadmin-credentials/hcx-site-pairing.png" alt-text="Capture d’écran du tableau de bord VMware HCX avec l’option Site Pairing mise en surbrillance.":::
 
3. Sélectionnez la connexion à Azure VMware Solution qui convient, puis **Modifier la connexion**.
 
4. Fournissez les nouvelles informations d’identification utilisateur de CloudAdmin vCenter Server, puis sélectionnez **Modifier** pour les enregistrer. L’enregistrement doit s’afficher correctement.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment réinitialiser les informations d’identification de vCenter Server et de NSX-T Manager pour Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants :

- [Configuration des composants réseau NSX dans Azure VMware Solution](configure-nsx-network-components-azure-portal.md)
- [Intégration des services natifs Azure dans Azure VMware Solution](integrate-azure-native-services.md)
- [Déploiement d’une récupération d’urgence pour les charges de travail Azure VMware Solution à l’aide de VMware HCX](deploy-disaster-recovery-using-vmware-hcx.md)
